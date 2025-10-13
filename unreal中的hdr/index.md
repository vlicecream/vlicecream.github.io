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

7. `r.HDR.UI.CompositeMode`

   *控制UI是否启用HDR合成模式，尝试获得与SDR相同的UI视觉效果。*

8. `r.HDR.UI.Level`

   *控制 UI 合成的高光值*

*除了 `r.HDR.EnableHDROutput` ，其他的参数都是控制 UE 渲染逻辑的 Shader 参数*

## ***HDR 后处理***

*关于 HDR 的后处理可以分为两个流程，实际里面也有对 SDR 的处理*

1. *cLUTs*
2. *Tonemap*

### ***cLUTs***

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

```c
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
```

#### ***（2）白平衡***




