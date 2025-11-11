# Unreal中的HDR


***源码版本 - UE5.5***

## ***HDR 相关命令***

1. `r.HDR.EnableHDROutput`

   *控制是否启用 HDR 输出。*

   *它主要控制 DXGI 设置，以更改后台缓冲区格式，并允许将 HDR 格式的后台缓冲区发送到 HDR 显示设备。*

2. `r.HDR.Display.OutputDevice`

   *控制使用哪个输出设备的传输函数*

   ```cpp
   TAutoConsoleVariable<int32> CVarDisplayOutputDevice(
   	TEXT("r.HDR.Display.OutputDevice"),
   	0,
   	TEXT("Device format of the output display:\n")
   	TEXT("0: sRGB (LDR)\n")
   	TEXT("1: Rec709 (LDR)\n")
   	TEXT("2: Explicit gamma mapping (LDR)\n")
   	TEXT("3: ACES 1000 nit ST-2084 (Dolby PQ) (HDR)\n")
   	TEXT("4: ACES 2000 nit ST-2084 (Dolby PQ) (HDR)\n")
   	TEXT("5: ACES 1000 nit ScRGB (HDR)\n")
   	TEXT("6: ACES 2000 nit ScRGB (HDR)\n")
   	TEXT("7: Linear EXR (HDR)\n")
   	TEXT("8: Linear final color, no tone curve (HDR)\n")
   	TEXT("9: Linear final color with tone curve\n"),
   	ECVF_Scalability | ECVF_RenderThreadSafe
   );
   ```

3. `r.HDR.Display.ColorGamut`

   *控制用作输出设备的色域范围*

   ```cpp
   TAutoConsoleVariable<int32> CVarDisplayColorGamut(
   	TEXT("r.HDR.Display.ColorGamut"),
   	0,
   	TEXT("Color gamut of the output display:\n")
   	TEXT("0: Rec709 / sRGB, D65 (default)\n")
   	TEXT("1: DCI-P3, D65\n")
   	TEXT("2: Rec2020 / BT2020, D65\n")
   	TEXT("3: ACES, D60\n")
   	TEXT("4: ACEScg, D60\n"),
   	ECVF_Scalability | ECVF_RenderThreadSafe
   );
   ```

4. `r.HDR.Display.MinLuminanceLog10`

   *所设定的最低显示输出的 Nit 值（以对数形式表示的值）*

   *默认值 -4*

5. `r.HDR.Display.MidLuminance`

   *18% 灰度的配置显示输出的亮度水平*

   *默认值 15*

6. `r.HDR.Display.MaxLuminance`

   *所配置的显示输出的亮度水平（假设已启用 HDR 输出）。*

   *默认值 0 但是其实代码判断为0 就会显示1000，如果启用 HDR 输出*

   *跟 `r.HDR.Display.MidLuminance` 配合使用，要不然这个调的太低，场景会灰*

7. `r.HDR.UI.CompositeMode`

   *控制UI是否启用HDR合成模式，尝试获得与SDR相同的UI视觉效果。*

8. `r.HDR.UI.Level`

   *将用户界面元素合成到高动态范围帧缓冲区时的亮度级别*
   
9. `r.HDR.UI.Luminance`

   *控制 UI HDR 亮度值*

   *在将用户界面元素合成到 HDR 帧缓冲区时，其基亮度值（以尼特为单位）乘以 r.HDR.UI.Level 的结果*

*除了 `r.HDR.EnableHDROutput` ，其他的参数都是控制 UE 渲染逻辑的 Shader 参数*

## ***HDR 后处理***

*关于 HDR 的后处理可以分为两个流程，实际里面也有对 SDR 的处理*

1. *cLUTs*
2. *Tonemap*

### ***生成 cLUTs***

*首先，生成当前帧的 CLUT。*

*此逻辑由 `PostProcessCombineLUTs.usf` 处理。有两个版本可供选择：PS 和 CS，具体取决于当前平台。*

*此 CLUT 包含以下颜色操作：*

1. *依靠UV计算出 2D/3D 的 中性LUTs*
2. *白平衡*
3. *HDR 调色*
4. *输出变换 （RRT + ODT）*

#### ***（1）中性LUTs***

*`USE_VOLUME_LUT` 这个宏内走的会根据你的 LUTSize（默认值为32）生成 32 x 32 x32 的 3D LUTs 表*

*如果不走这个宏的话，他就会按照你的 LUTSize（默认值为32） 生成像素长为 32 x 32 =  1024，像素宽为 32 的 2D LUTs*

*再然后如果发现你的 `OutputDevice >= 3` 则把 ST 2084/PQ 传输函数将 0-1 范围重新映射到 0-100 尼特的转换*

*SDR输出设备分支采用对数编码，利用对数函数将0到1的范围重新映射到从0到场景中最大像素值（约50）的转换。*

```hlsl
float4 CombineLUTsCommon(float2 InUV, uint InLayerIndex)
{
#if USE_VOLUME_LUT == 1
	// construct the neutral color from a 3d position volume texture	
	float4 Neutral;
	{
		float2 UV = InUV - float2(0.5f / LUTSize, 0.5f / LUTSize);

		Neutral = float4(UV * LUTSize / (LUTSize - 1), InLayerIndex / (LUTSize - 1), 0);
	}
#else
	// construct the neutral color from a 2d position in 256x16
	float4 Neutral;
	{ 
		float2 UV = InUV;

		// 0.49999f instead of 0.5f to avoid getting into negative values
		UV -= float2(0.49999f / (LUTSize * LUTSize), 0.49999f / LUTSize);

		float Scale = LUTSize / (LUTSize - 1);

		float3 RGB;
		
		RGB.r = frac(UV.x * LUTSize);
		RGB.b = UV.x - RGB.r / LUTSize;
		RGB.g = UV.y;

		Neutral = float4(RGB * Scale, 0);
	}
#endif
    
    ......
        
    float3 LUTEncodedColor = Neutral.rgb;
	float3 LinearColor;
	// Decode texture values as ST-2084 (Dolby PQ)
	BRANCH
	if (GetOutputDevice() >= TONEMAPPER_OUTPUT_ACES1000nitST2084)
	{
		// Since ST2084 returns linear values in nits, divide by a scale factor to convert
		// the reference nit result to be 1.0 in linear.
		// (for efficiency multiply by precomputed inverse)
		LinearColor = ST2084ToLinear(LUTEncodedColor) * LinearToNitsScaleInverse;
	}
	// Decode log values
	else
	{
		LinearColor = LogToLin(LUTEncodedColor) - LogToLin(0);
	}
}

float3 LogToLin( float3 LogColor )
{
	const float LinearRange = 14;
	const float LinearGrey = 0.18;
	const float ExposureGrey = 444;

	// Using stripped down, 'pure log', formula. Parameterized by grey points and dynamic range covered.
	float3 LinearColor = exp2( ( LogColor - ExposureGrey / 1023.0 ) * LinearRange ) * LinearGrey;
	//float3 LinearColor = 2 * ( pow(10.0, ((LogColor - 0.616596 - 0.03) / 0.432699)) - 0.037584 );	// SLog
	//float3 LinearColor = ( pow( 10, ( 1023 * LogColor - 685 ) / 300) - .0108 ) / (1 - .0108);	// Cineon
	//LinearColor = max( 0, LinearColor );

	return LinearColor;
}
```

#### ***（2）白平衡***

*使用上一个步骤计算出来的 `LinearColor` 计算白平衡*

```hlsl
float4 CombineLUTsCommon(float2 InUV, uint InLayerIndex)
{
    ......
	float3 BalancedColor = WhiteBalance(LinearColor, WhiteTemp, WhiteTint, bIsTemperatureWhiteBalance, (float3x3)WorkingColorSpace.ToXYZ, (float3x3)WorkingColorSpace.FromXYZ);
    ......
}

float3 WhiteBalance(float3 LinearColor, float WhiteTemp, float WhiteTint, bool bIsTemperatureWhiteBalance, const float3x3 WCS_2_XYZ, const float3x3 XYZ_2_WCS)
{
	float2 SrcWhiteDaylight = D_IlluminantChromaticity(WhiteTemp);
	float2 SrcWhitePlankian = PlanckianLocusChromaticity(WhiteTemp);

	float2 SrcWhite = WhiteTemp < 4000 ? SrcWhitePlankian : SrcWhiteDaylight;
	float2 D65White = float2(0.31270, 0.32900);

	{
		// Offset along isotherm
		float2 Isothermal = PlanckianIsothermal(WhiteTemp, WhiteTint) - SrcWhitePlankian;
		SrcWhite += Isothermal;
	}

	if (!bIsTemperatureWhiteBalance)
	{
		float2 Temp = SrcWhite;
		SrcWhite = D65White;
		D65White = Temp;
	}

	float3x3 WhiteBalanceMat = ChromaticAdaptation(SrcWhite, D65White);
	WhiteBalanceMat = mul( XYZ_2_WCS, mul( WhiteBalanceMat, WCS_2_XYZ ) );

	return mul(WhiteBalanceMat, LinearColor);
}
```

*白平衡是利用是**色温值**来选择是普朗克轨迹还是日光模型来算出白点的色度坐标*

*如果是 `色温值 < 4000`则光源更接近于黑体辐射，所以使用普朗克轨迹模型，更通俗点就是人造光源*

*反之，则光源更接近于日光这种，所以使用标准日光模型*

#### ***（3）从sRGB到AP1色彩空间的转换***

*计算白平衡后，色彩空间从sRGBLinear转换为ACESAP1线性色彩空间。*

```hlsl
float4 CombineLUTsCommon(float2 InUV, uint InLayerIndex)
{
    ......
	float3 ColorAP1 = mul( (float3x3)WorkingColorSpace.ToAP1, BalancedColor );
    ......
}
```

*我们将色域从 sRGB 空间转换到了广色域 AP1，但之前的场景渲染是在 sRGB 线性色彩空间中进行的，因此得到的色域值始终在 sRGB 色域内。*

*现在 UE 出了一个小技巧：场景看起来像是在广色域空间中进行计算的，其原色介于 P3 和 AP1 之间，它使用 Wide_2_AP1 进行颜色转换，最后将参数与原始的 sRGB_2_AP1 转换结果进行插值。*

```hlsl
float4 CombineLUTsCommon(float2 InUV, uint InLayerIndex)
{
    ......
		// Expand bright saturated colors outside the sRGB gamut to fake wide gamut rendering.
 	float  LumaAP1 = dot( ColorAP1, AP1_RGB2Y );
	float3 ChromaAP1 = ColorAP1 / LumaAP1;

	float ChromaDistSqr = dot( ChromaAP1 - 1, ChromaAP1 - 1 );
	float ExpandAmount = ( 1 - exp2( -4 * ChromaDistSqr ) ) * ( 1 - exp2( -4 * ExpandGamut * LumaAP1*LumaAP1 ) );

	// Bizarre matrix but this expands sRGB to between P3 and AP1
	// CIE 1931 chromaticities:	x		y
	//				Red:		0.6965	0.3065
	//				Green:		0.245	0.718
	//				Blue:		0.1302	0.0456
	//				White:		0.3127	0.329
	const float3x3 Wide_2_XYZ_MAT = 
	{
		0.5441691,  0.2395926,  0.1666943,
		0.2394656,  0.7021530,  0.0583814,
		-0.0023439,  0.0361834,  1.0552183,
	};

	const float3x3 Wide_2_AP1 = mul( XYZ_2_AP1_MAT, Wide_2_XYZ_MAT );
	const float3x3 ExpandMat = mul( Wide_2_AP1, AP1_2_sRGB );

	float3 ColorExpand = mul( ExpandMat, ColorAP1 );
	ColorAP1 = lerp( ColorAP1, ColorExpand, ExpandAmount );
    ColorAP1 = ColorCorrectAll( ColorAP1 );
    
    // Store for Linear HDR output without tone curve
	float3 GradedColor = mul( (float3x3)WorkingColorSpace.FromAP1, ColorAP1 );
    
    // Apply Fade track to linear outputs also
	GradedColor = lerp(GradedColor * ColorScale, OverlayColor.rgb, OverlayColor.a);
    ......
}
```

#### ***（4）SDR中的输出变换***

*这里先随口带过一下：*

*SDR 中的 Output Transform 使用了 ACES 的 LMT + RRT + ODT 变换。首先是 LMT 部分。*

*UE 使用 ACES LMT 中的 BlueLightArtifactFix 部分来修复高亮度蓝色值导致的过饱和问题。*

*然后就又中和掉刚才修复蓝色值的一些操作*

*如果你目前是 SDR Device 输出，则就用这个最终输出颜色*

#### ***（6）HDR中的输出变换***

*UE的HDR输出管线在计算完 `GradedColor` 之后，直接将色彩空间从AP1重新转换为sRGB。*

*最后，为了将编码的 HDR 信号传递到 HDR 显示设备，使用 PQ 的 OETF 传输函数对其进行编码。*

```hlsl

	// ACES 1000nit transform with PQ/2084 encoding, user specified gamut 
	else if( GetOutputDevice() == TONEMAPPER_OUTPUT_ACES1000nitST2084 || GetOutputDevice() == TONEMAPPER_OUTPUT_ACES1000nitScRGB)
	{		
		// 1000 nit ODT
		FACESTonemapParams AcesParams = ComputeACESTonemapParams(ACESMinMaxData, ACESMidData, ACESCoefsLow_0, ACESCoefsHigh_0, ACESCoefsLow_4, ACESCoefsHigh_4, ACESSceneColorMultiplier, ACESGamutCompression);
		float3 ODTColor = ACESOutputTransforms1000( GradedColor, (float3x3)WorkingColorSpace.ToAP0, AcesParams);

		// Convert from AP1 to specified output gamut except for ScRGB as it might contain negative values. 
		// In this case, the AP1->output conversion happens through ST2084ToScRGB in the tonemap function
		if( GetOutputDevice() != TONEMAPPER_OUTPUT_ACES1000nitScRGB )
		{
			ODTColor = mul(AP1_2_Output, ODTColor);
		}

		// Apply conversion to ST-2084 (Dolby PQ)
		OutDeviceColor = LinearToST2084( ODTColor );
	}

	// ACES 2000nit transform with PQ/2084 encoding, user specified gamut 
	else if( GetOutputDevice() == TONEMAPPER_OUTPUT_ACES2000nitST2084 || GetOutputDevice() == TONEMAPPER_OUTPUT_ACES2000nitScRGB)
	{		
		// 2000 nit ODT
		FACESTonemapParams AcesParams = ComputeACESTonemapParams(ACESMinMaxData, ACESMidData, ACESCoefsLow_0, ACESCoefsHigh_0, ACESCoefsLow_4, ACESCoefsHigh_4, ACESSceneColorMultiplier, ACESGamutCompression);
		float3 ODTColor = ACESOutputTransforms2000( GradedColor, (float3x3)WorkingColorSpace.ToAP0, AcesParams);

		// Convert from AP1 to specified output gamut except for ScRGB as it might contain negative values. 
		// In this case, the AP1->output conversion happens through ST2084ToScRGB in the tonemap function
		if ( GetOutputDevice() != TONEMAPPER_OUTPUT_ACES2000nitScRGB)
		{
			ODTColor = mul(AP1_2_Output, ODTColor);
		}

		// Apply conversion to ST-2084 (Dolby PQ)
		OutDeviceColor = LinearToST2084( ODTColor );
	}	
	
	else if( GetOutputDevice() == TONEMAPPER_OUTPUT_LinearEXR)
	{
			float3 OutputGamutColor = mul( AP1_2_Output, mul( (float3x3)WorkingColorSpace.ToAP1, GradedColor ) );
			OutDeviceColor = LinearToST2084( OutputGamutColor );
	}
	// Linear HDR, including all color correction, but no tone curve
	else if( GetOutputDevice() == TONEMAPPER_OUTPUT_NoToneCurve)
	{
			OutDeviceColor = GradedColor;
	}
```

### ***使用 cLUTs (Tonemap)***

*使用CLUT的逻辑由PostProcessTonemap.usf处理，其主要功能逻辑在TonemapCommonPS函数中*

*该函数首先计算任何目前尚未完成的后处理，例如Grain、Color Fringe、Sharpen、Bloom、Exposure和Vignette，然后将上述后处理计算得到的最终结果转换为3D LUT的采样坐标来查找颜色：*

```hlsl
half3 ColorLookupTable( half3 LinearColor )
{
	float3 LUTEncodedColor;
	// Encode as ST-2084 (Dolby PQ) values
	BRANCH
	if(GetOutputDevice() >= TONEMAPPER_OUTPUT_ACES1000nitST2084)
	{
		// ST2084 expects to receive linear values 0-10000 in nits.
		// So the linear value must be multiplied by a scale factor to convert to nits.
		// We don't send negative values to LinearToST2084 as it will result in NaN because of pow.
		LUTEncodedColor = LinearToST2084(max(0, LinearColor) * LinearToNitsScale);
	}
	else
	{
		LUTEncodedColor = LinToLog( LinearColor + LogToLin( 0 ) );
	}

	float3 UVW = LUTEncodedColor * LUTScale + LUTOffset;

#if USE_VOLUME_LUT == 1
	half3 OutDeviceColor = Texture3DSample( ColorGradingLUT, ColorGradingLUTSampler, UVW ).rgb;
#else
	half3 OutDeviceColor = UnwrappedTexture3DSample( ColorGradingLUT, ColorGradingLUTSampler, UVW, LUTSize, InvLUTSize ).rgb;
#endif
	
	return OutDeviceColor * 1.05;
}

float4 TonemapCommonPS(
	float3 UV,
	float2 Vignette,
	float4 GrainUV,
	float2 ScreenPos, // [-1, 1]x[-1, 1]
	float2 FullViewUV,
	float4 SvPosition,
	out float OutLuminance
	)
{
    ......
	half3 OutDeviceColor = ColorLookupTable(FinalLinearColor);
    ......
}
```

*因为我们 cLUTs 是非线性的 HDR 空间的值，所以我们经历 Bloom 等一些计算出的颜色也要 To ST2084 后才能去 cLUTs 里面寻找*

*最后，根据 OutputDevice，如果你在编辑器开启了HDR，则会将 ST2084 转成 ScRGB，输出到下一阶段，其他的看分支即可*

```hlsl
float4 TonemapCommonPS(
	float3 UV,
	float2 Vignette,
	float4 GrainUV,
	float2 ScreenPos, // [-1, 1]x[-1, 1]
	float2 FullViewUV,
	float4 SvPosition,
	out float OutLuminance
	)
{
    ......
    if(GetOutputDevice() == TONEMAPPER_OUTPUT_ACES1000nitScRGB || GetOutputDevice() == TONEMAPPER_OUTPUT_ACES2000nitScRGB)
    {
            OutColor.xyz = ST2084ToScRGB(OutColor.xyz, GetOutputDevice(), OutputMaxLuminance);
    }
    else if(GetOutputDevice() == TONEMAPPER_OUTPUT_LinearEXR)
    {
        OutColor.xyz = ST2084ToLinear(OutColor.xyz);
    }
    else
    {
        OutColor.xyz = OutDeviceColor;
    }
    ......
}
```

## ***UI 中的 HDR***

*UI中使用 HDR 主要是在 `CompositeUIPixelShader.usf` 中处理的*

### ***（1）计算 UI 颜色***

*在这里会从一个包含UI元素的纹理中采样颜色。随后进行颜色变换，从 sRGB 空间转换到 REC.2020的线性空间，所以这里 Unreal 会默认一切UI颜色都是为 sRGB*

*在这个步骤，其实就会跟 `r.HDR.UI.Luminance`计算*

```hlsl
void Main(
	FScreenVertexOutput Input,
	out float4 OutColor : SV_Target0
	)
{
    ...
	float4 UIColor = ComputeHDRUIColor(Input.UV);
	...
}

```

### ***（2）计算场景颜色***

*这里先从场景纹理采样，随后因为我们在之前 Tonemap 中最终输出的是ST2080，所以在这里如果没开启SCRGB的宏，会将ST2080 转成 Rec.2020 线性空间.*

*如果开启ScRGB的宏，最终其实也是转为了 Rec.2020 的颜色空间*

```hlsl
void Main(
	FScreenVertexOutput Input,
	out float4 OutColor : SV_Target0
	)
{
	...
	float3 SceneColor = Texture2DSample(SceneTexture, SceneSampler, Input.UV).xyz;
	SceneColor = ComputeHDRSceneColor(SceneColor);
	...
}

```

### ***（3）混合 UI 和 场景 颜色***

*最终会在这个步骤将场景和UI混合到一张 RT 上，这里会如果 UI 为半透明会进行特殊处理，如果为 a通道为0或者不透明，则会乘上 `r.HDR.UI.Level` 的值，做运算*

```hlsl
float3 ComposeUIAndScene(float3 SceneColor, float4 InUIColor, float InUILevel)
{
	BRANCH
	if (InUIColor.w > 0.f && InUIColor.w < 1.f)
	{
		// Clamp gamut to sRGB as extended gamut colors bleeding into the UI can look funny
		SceneColor = max(SceneColor, 0.f);

		// Tonemap HDR under transparent UI with a simple Reinhard to the max luminance of the UI
		// This prevents HDR bleed through destroying UI legibility
		// Rec2020 coefficients to compute luminance
		float KR = 0.2627, KG = 0.678, KB = 0.0593;
		float Luminance = dot(SceneColor, half3(KR, KG, KB)) / InUILevel;
		float OutL = 1.f / (Luminance + 1.f);

		// Ease out remapping to avoid hard transitions where UI is near zero opacity
		SceneColor *= lerp(1.f, OutL * InUILevel, InUIColor.w);
	}

	// Composite, assuming pre-multiplied alpha
	return SceneColor * (1.f - InUIColor.w) + InUIColor.xyz * InUILevel;
}

void Main(
	FScreenVertexOutput Input,
	out float4 OutColor : SV_Target0
	)
{
	...
	OutColor.xyz = ComposeUIAndScene(SceneColor.xyz, UIColor, UILevel);
	...
}
```

### ***（4）色盲模式***

*Unreal 其实对色盲是有过处理的，将合成后的颜色转换成模拟某种色盲（如红绿色盲、蓝黄色盲）所看到的颜色。*

```hlsl
void Main(
	FScreenVertexOutput Input,
	out float4 OutColor : SV_Target0
	)
{
	OutColor.rgb = ApplyColorDeficiency(OutColor.rgb);
}
```

### ***（5）输出到显示器***

*我们到这个流程的颜色空间为 Rec.2020 线性空间，既然要输出到显示器，肯定必须在转换为 ST 2084（PQ），如果使用 scRGB 编码则就转换为 sRGB色域*

```hlsl
void Main(
	FScreenVertexOutput Input,
	out float4 OutColor : SV_Target0
	)
{
#if !SCRGB_ENCODING
	// Linear -> PQ
	OutColor.xyz = LinearToST2084(OutColor.xyz);
#else
	const float3x3 Rec2020_2_sRGB = mul(XYZ_2_sRGB_MAT, Rec2020_2_XYZ_MAT);
	OutColor.xyz = mul(Rec2020_2_sRGB, OutColor.xyz / ScRGBScaleFactor);
#endif
}
```


