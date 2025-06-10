# 光照


## ***纹理简介***

*2D 纹理是一种由特定数据元素所构成的矩阵，它的用处之一即是存储 2D 图像数据，纹理中的每个元素都存储着对应像素的颜色。但是，这并不是它唯一的用途，比如说，在一种称为法线贴图的高级技术中，每个纹理元素都存储的是一个 3D 向量而非颜色数据。*

*一个纹理可以绑定到渲染流水线的不同阶段，一个常见的例子是既可将一纹理用作渲染目标（即 Direct3D 中的渲染到纹理技术），又能把它作为着色器资源（即在着色器中对该纹理进行采样）。一个纹理可以当作渲染目标，也可以充当着色器资源，但是不能同时“身兼数职”。将数据渲染到一个纹理后，再用它作为着色器资源，这种方法称为渲染到纹理（render-to-texture）*

## ***纹理坐标***

*Direct3D 所采用的纹理坐标系，是由指向图像水平正方向的 u 轴与指向图像垂直正方向的 v 轴所组成的。*

### ***纹素***

*取值范围为 0 ≤ u, v ≤ 1 的坐标(u, v)标定的是一 种称为纹素（texel）的纹理元素*

*无论纹理实际的大小是 256 × 256 像素、512 × 1024 像素还是 2048 × 2048 像素，纹理坐标(0.5, 0.5)总是表示 纹理正中间的纹素；坐标(0.25, 0.75)总是表示在纹理中， 位于水平方向总宽度 1/4、垂直方向总高度3/4处的纹素。这就是纹理坐标采用的归一化坐标区间[0, 1]，通过这种方式便可令 Direct3D 的工作摆脱具体纹理尺寸的干扰*

### ***纹理图集***

*我们可以只向几何体映射部分纹理。事实上，我们也能够将几个并无关联的图像合为一个大的纹 理图（这被称为纹理图集），再将它应用于若干不同的物体。此时，纹理坐标将用于确定纹理的哪一部分将被映射到目标三角形上。*

## ***纹理数据源***

### ***DDS 图像文件格式***

*贴图师通常会借助 Photoshop 或一些其他的图像编辑器为游戏制作纹理，最后再将它们保存为某种格式的图像文件，如 BMP、DDS、TGA 或 PNG 等。随后，游戏应用程序会在加载期间将图像文件载入 ID3D12Resource 对象。*

*对于实时图形应用程序来说，DDS 图像文件格式（DirectDraw 图面格式， DirectDraw Surface format，DDS）是一种尚佳的选择：除了支持 GPU 可原生处理的各种图像格式，它还 支持一些 GPU 自身就可解压的压缩图像格式。*

### ***DDS 格式概述***

*DDS 对于 3D 图形来说是一种理想的格式，因为它支持一些专用于 3D 图形的特殊格式以及纹理类型。*

*从本质上来讲，它实为一种针对 GPU 而专门设计的图像格式。例如，DDS 纹理满足用于3D 图形开发的以下特征：*

1. *mipmap*
2. *GPU 能自行解压的压缩格式。*
3. *纹理数组。*
4. *立方体图（cube map，也有译作立方体贴图）*
5. *体纹理（volume texture，也有译作体积纹理、立体纹理等）*

*DDS 格式能够支援不同的像素格式。像素格式由枚举类型 DXGI_FORMAT 中的成员来表示，但是并非所有的格式都适用于 DDS 纹理。*

*随着虚拟场景中纹理数量的大幅增长，对 GPU 端显存的需求也在迅速增加（还记得吗，我们需要将所有的纹理都置于显存当中，以便在程序中快速地运用这些资源）。为了缓解这些内存的需求压力， Direct3D 支持下列几种压缩纹理格式*

1. *BC1（DXGI_FORMAT_BC1_UNORM）：如果我们需要将图片压缩为支持 3 个颜色通道和仅有 1 位（开/关）alpha 分量的格式，则使用此格式。*
2. *BC2（DXGI_FORMAT_BC2_UNORM）：如果我们需要将图片压缩为支持 3 个颜色通道和仅有 4 位 alpha 分量的格式，则应用此格式。*
3. *BC3（DXGI_FORMAT_BC3_UNORM）：如果我们需要将图片压缩为支持 3 个颜色通道和 8 位 alpha 分量的格式，则采用此格式。*
4. *BC4（DXGI_FORMAT_BC4_UNORM）：如果我们需要将图片压缩为仅含有 1 个颜色通道的格式 （如灰度图像），则运用此格式。*
5. *BC5（DXGI_FORMAT_BC5_UNORM）：如果我们需要将图片压缩为只支持 2 个颜色通道的格式， 则使用此格式。*
6. *BC6（DXGI_FORMAT_BC6H_UF16）：如果我们需要将图片压缩为 HDR（高动态范围）图像数 据，则应用此格式。*
7. *BC7（DXGI_FORMAT_BC7_UNORM）：此格式用于对 RGBA 数据进行高质量的压缩。特别是， 此格式可极大地减少因压缩法线图而造成的误差。*

*再次重申，这些格式的优点是可以使图像以压缩的形式存于显存之中，而在需要时，GPU 便能动态 地对它们进行解压。将纹理压缩为 DDS 文件还有另一个好处，即更节省硬盘空间*

### ***创建 DDS 文件***

*下面介绍两种可以将传统图像格式转换为 DDS 格式的方法*

1. *NVIDIA 公司为 Adobe Photoshop 提供了一款可以将图像导出为 DDS 格式的插件。该插件现存 于 https://developer.nvidia.com/nvidia-texture-tools-adobe-photoshop。此插件还有一些其他选项， 可供用户指定 DDS 文件的 DXGI_FORMAT 格式，或生成 mipmap 等。*
2. *微软公司提供了一个名为 texconv 的命令行工具，该工具能将传统的图像格式转换为 DDS 文件。 另外，texconv 程序还有更多的其他功能，如调整图像大小、改变像素格式、生成 mipmap 等。可 以在网站https://directxtex.codeplex.com/wikipage?title=Texconv&referringTitle=ocumentation 找到它 的文档与下载链接。*

*下面的示例展示了向 texconv 程序输入一个 BMP 文件 bricks.bmp，并通过它来输出格式为 BC3_UNORM 且具有一个 mipmap 链（链中共有 10 个 mipmap）的 DDS 文件 bricks.dds。*

```shell
texconv -m 10 -f BC3_UNORM bricks.bmp 
```



## ***创建以及启用纹理***

### ***加载 DDS 文件***

*微软公司提供了一组用来加载 DDS 文件的轻量级源代码（ps: 此代码仅支持 DirectX 11）*

```cpp
HRESULT DirectX::CreateDDSTextureFromFile12(
    _In_ ID3D12Device* device,
    _In_ ID3D12GraphicsCommandList* cmdList,
    _In_z_ const wchar_t* szFileName,
    _Out_ Microsoft::WRL::ComPtr<ID3D12Resource>& texture,
    _Out_ Microsoft::WRL::ComPtr<ID3D12Resource>& textureUploadHeap
);
```

1. *device：指向用于创建纹理资源的 D3D 设备的指针*
2. *cmdList：提交 GPU 命令（例如，将纹理数据从上传堆复制到默认堆的命令）的命令列表*
3. *szFileName：欲加载的图像文件名。*
4. *texture：返回载有图像数据的纹理资源。*
5. *textureUploadHeap：返回的纹理资源，在此，将它当作一个上传堆，用于将图像数据复制到默认堆中的纹理资源。在 GPU 完成其上述复制命令之前，不能销毁该资源。*

### ***着色器资源视图堆***

*创建了纹理资源后，我们还需要为它再创建一个 SRV（着色器资源视图）描述符，并将其设置到一 个根签名参数槽（root signature parameter slot），以供着色器程序使用。*

*为此，首先要用 ID3D12Device:: CreateDescriptorHeap 函数来创建描述符堆，借此存储 SRV 描述符*

*下面的代码构建了一个可容纳 3 个类型为 CBV、SRV 或 UAV 描述符的描述符堆，并使之在着色器中可见（即可供着色器使用）：*

```cpp
D3D12_DESCRIPTOR_HEAP_DESC srvHeapDesc = {};
srvHeapDesc.NumDescriptors = 3;
srvHeapDesc.Type = D3D12_DESCRIPTOR_HEAP_TYPE_CBV_SRV_UAV;
srvHeapDesc.Flags = D3D12_DESCRIPTOR_HEAP_FLAG_SHADER_VISIBLE;
ThrowIfFailed(md3dDevice->CreateDescriptorHeap(&srvHeapDesc, IID_PPV_ARGS(&mSrvDescriptorHeap))); 
```

### ***创建着色器资源视图描述符***

*一旦创建了 SRV 堆，便可创建真正的描述符。我们通过填写 D3D12_SHADER_RESOURCE_VIEW_DESC 对象来描述 SRV 描述符，该结构体详述了资源的类型以及其他的信息，如格式、维数、mipmap 数量等。*

```cpp
typedef struct D3D12_SHADER_RESOURCE_VIEW_DESC
{
    DXGI_FORMAT Format;
    D3D12_SRV_DIMENSION ViewDimension;
    UINT Shader4ComponentMapping; 
    union
    {
        D3D12_BUFFER_SRV Buffer;
        D3D12_TEX1D_SRV Texture1D;
        D3D12_TEX1D_ARRAY_SRV Texture1DArray;
        D3D12_TEX2D_SRV Texture2D;
        D3D12_TEX2D_ARRAY_SRV Texture2DArray;
        D3D12_TEX2DMS_SRV Texture2DMS;
        D3D12_TEX2DMS_ARRAY_SRV Texture2DMSArray;
        D3D12_TEX3D_SRV Texture3D;
        D3D12_TEXCUBE_SRV TextureCube;
        D3D12_TEXCUBE_ARRAY_SRV TextureCubeArray;
    };
} D3D12_SHADER_RESOURCE_VIEW_DESC;

typedef struct D3D12_TEX2D_SRV
{
    UINT MostDetailedMip;
    UINT MipLevels;
    UINT PlaneSlice;
    FLOAT ResourceMinLODClamp;
} D3D12_TEX2D_SRV; 
```

*对于 2D 纹理来说，我们只关心联合体中的 D3D12_TEX2D_SRV 部分。*

1. *Format：视图的格式。如果待创建视图的资源有具体的格式，即并非以无类型（typeless）的格式创建而成），就用此资源的 DXGI_FORMAT 格式来填写此参数。如果是通过无类型的 DXGI_FORMAT 来创建该资源的，则一定要在此为视图填写具体的类型，只有这样 GPU 才能知 道怎样解释并处理这一数据。*

2. *ViewDimension：资源的维数。目前我们只使用 2D 纹理，所以将此参数指定为 D3D12_SRV_ DIMENSION_TEXTURE2D*

   *以下是几种常见的纹理维数：*

   * *D3D12_SRV_DIMENSION_TEXTURE1D：资源为 1D 纹理。*

   * *D3D12_SRV_DIMENSION_TEXTURE3D：资源为 3D 纹理。*

   * *D3D12_SRV_DIMENSION_TEXTURECUBE：资源为立方体纹理（cube texture）。*

3. *Shader4ComponentMapping：在着色器中对纹理进行采样时，它将返回特定纹理坐标处的纹理数据向量。这个字段提供了一种方法，可以将采样时所返回的纹理向量中的分量进行重新排序。例如，可以用此字段将红色分量与绿色分量互换。该方法常用于一些特殊的场合，但目前并不涉及这些情景。因此，只要将它指定为 D3D12_DEFAULT_SHADER_4_COMPONENT_MAPPING 即可。这样一来，向量分量的顺序将不会改变，它会以纹理资源中默认的数据顺序直接返回。*

4. *MostDetailedMip：指定此视图中图像细节最详尽的 mipmap 层级的索引。此参数的取值范围在 0 与 MipLevels-1 之间。*

5. *MipLevels：自 MostDetailedMip 算起，待创建视图的 mipmap 层级数量。通过将这个字段与 MostDetailedMip 配合起来，我们就能够指定此视图 mipmap 层级的连续子范围。可以将此字段设置为-1，用来表示自 MostDetailedMip 始至最后一个 mipmap 层级之间的所有 mipmap 级别。*

6. *PlaneSlice：平面切片的索引。*

7. *ResourceMinLODClamp：指定可以访问的最小 mipmap 层级。设置为 0.0 表示可以访问所有 的 mipmap 层级。将此参数指定为 3.0，则表示可以访问从 3.0 到 MipCount-1 的 mipmap 层级。*

### ***将纹理绑定到流水线***

*至此，我们在每次绘制调用时所指定的材质，都是由材质常量缓冲区来进行更新的。这就意味着在 绘制调用的过程中，所有的几何体都将使用同一组材质数据。这对程序而言是一种极大的限制，因为我们 将不能动态地指定每个像素的材质，继而导致场景细节的缺失。*

*纹理映射技术的想法是用纹理图（texture map）来取代材质常量缓冲区以获取材质数据。这将使每个像素的数据都是灵活可变化的，从而为场景增添更丰富的细节与几分真实感。*

## ***过滤器***

### ***纹理放大***

*我们可以将纹理图中的元素看作是从连续图像中采集的离散颜色样本，但并不应认为它们是有着特定面积大小的矩形。所以，当前的疑问是：如果在纹理坐标(u, v)处没有与之对应的纹素点究竟会发生什么？*

*假设玩家慢慢靠近了场景中的一堵墙壁，则墙壁将逐渐放大并占据完整的镜头。为了便于说明问题，这里假设显示器的分辨率为1024 × 1024 ，而且墙壁纹理的分辨率 为 256 × 256 。由此就产生了纹理放大（magnification）的概念——我们试图用少量纹素来覆盖大量的像 素。*

*还有一点需要注意的是，在以虚拟视角可以自由移动和探索的交互式 3D 程序中，纹理放大是个无法回避的问题。在与目标保持特定距离时，纹理可能看上去还不错，但是随着观察点逐渐接近目标，其效果就开始惨不忍睹了。一些游戏对虚拟视角过于接近物体表面的行为进行了限制，以避免过度的放大处理。通过使用更高分辨率的纹理也可以使此难题得到一定程度的改善。*

*图形硬件往往会支持常数插值（constant interpolation，也有译作常量插值）与 线性插值（linear interpolation）两种插值方法。在实践中，线性插值的使用更为普遍。*

#### ***点过滤***

*1D 情况下的使用过程：假设我们有一个内含 256 个样本的 1D 纹理， 并且某个插值纹理坐标为 u = 0.126484375，所以此归一化纹理坐标就对应于 0.126484375 × 256  = 32.38 处的纹素。显而易见，此值实际位于两个纹素样本之间，所以我们必须通过插值这一手段来求取它的近似值。*

#### ***线性过滤***

*2D 线性插值又称为双线性插值（bilinear interpolation），其处理流程：给出四个纹素之间的一个纹理坐标，先在水平方向 u 上进行两次 1D 线性插值（求出 cT与 cB），后在垂直方向 v 上再进行 一次 1D 内插（求取 c）。*

### ***纹理缩小***

*纹理缩小（minification）是纹理放大的逆运算。在缩小的过程中，大量纹素将被映射到少数纹理之上。*

*例如，考虑下列情景：假设有一堵被 256 × 256 纹理所映射的墙壁，玩家的观察视角正紧盯着它，并逐渐向后退却。在此过程中，这堵墙会看上去越来越小，直至它在屏幕上只覆盖大小为 64 × 64 像素的区 域。此时，我们就应当将 256 × 256 纹素映射到 64 × 64 屏幕像素。*

### ***各向异性过滤***

*还有一种名为各向异性过滤（anisotropic filtering）的过滤器类型。该过滤器有助于缓解当多边形法向量与摄像机观察向量之间夹角过大（比如当多边形正交于观察窗口时）所导致的失真现象。*

*这种过滤器的开销最大，但是其校正失真的效果的确对得起它所消耗的资源。*

## ***寻址模式***

*可将经过常数插值或线性插值的纹理定义为一个返回向量值的函数 T(u, v) = (r, g, b, a)，即给定纹理坐标 $ (u, v) ∈ [0,1]^2 $  ，则上述纹理函数 T 将返回颜色(r, g, b, a)。*

*Direct3D 允许我们采用下列 4 种不同方式（即寻址模式）来扩充此函数的定义域（解决输入值超出定义域这一问 题）*

1. *重复寻址模式通过在坐标的每个整数点处重复绘制图像来拓充纹理函数*
2. *边框颜色寻址模式通过将每个不在范围 $ [0,1]^2 $ 内的坐标(u, v)都映射为程序员指定的颜色而拓充纹理函数*
3. *钳位寻址模式通过将范围 $ [0,1]^2 $外的每个坐标(u, v)都映射为颜色 T(u0, v0)来扩充纹理函数，其中， $ (u_0, v_0) $ 为范围 $ [0,1]^2 $ 内距离(u, v)最近的点*
4. *镜像寻址模式通过在坐标的每个整数点处绘制图像的镜像来扩充纹理函数*

*重复寻址也许是最常使用的一种模式，它允许我们将一种纹理反复平铺到某一表面上。也就是说，这种寻址模式使我们在不提供额外数据的情况下，亦可提升纹理的分辨率，虽然很多都是重复的*

*在执行平铺的时候，纹理是否能无缝衔接往往也是一个重点。例如，若板条箱纹理原本并不是无缝连接的，那么我们就能够明显地看出它是在反复贴同一张图。*

## ***采样器对象***

*从前面两个小节中可以看出，在运用纹理的过程中，除了纹理数据本身之外，还有另外两个相关的 重要概念，即纹理过滤以及寻址模式。采集纹理资源时所用的过滤器和寻址模式都是由采样器对象来定义的。一个应用程序通常需要采用若干个采样器对象以不同的方式来采集纹理。*

### ***创建采样器***

*为了将采样器绑定到着色器上供其使 用，我们就需要为采样器对象绑定描述符。*、

*下面的代码展示了这样的一个根签名示例，它的第二个槽位 获取了一个描述符表，此表中存有 1 个与采样器寄存器槽 0 相绑定的采样器描述符。*

```cpp
CD3DX12_DESCRIPTOR_RANGE descRange[3];
descRange[0].Init(D3D12_DESCRIPTOR_RANGE_TYPE_SRV, 1, 0); 
descRange[1].Init(D3D12_DESCRIPTOR_RANGE_TYPE_SAMPLER, 1, 0);
descRange[2].Init(D3D12_DESCRIPTOR_RANGE_TYPE_CBV, 1, 0);
CD3DX12_ROOT_PARAMETER rootParameters[3];
rootParameters[0].InitAsDescriptorTable(1, &descRange[0], D3D12_SHADER_VISIBILITY_PIXEL);
rootParameters[1].InitAsDescriptorTable(1, &descRange[1], D3D12_SHADER_VISIBILITY_PIXEL);
rootParameters[2].InitAsDescriptorTable(1, &descRange[2], D3D12_SHADER_VISIBILITY_ALL);
CD3DX12_ROOT_SIGNATURE_DESC descRootSignature;
descRootSignature.Init(3, rootParameters, 0, nullptr,D3D12_ROOT_SIGNATURE_FLAG_ALLOW_INPUT_ASSEMBLER_INPUT_LAYOUT); 
```

### ***静态采样器***

*事实证明，图形应用程序通常不会使用过多的采样器。为此，Direct3D 专门提供了一种特殊的方式来定义采样器数组，使用户可以在不创建采样器堆的情况下也能对它们进行配置。*

## ***在着色器中对纹理进行采样***

*通过下列 HLSL 语法来定义纹理对象，并将其分配给特定的纹理寄存器：*

```hlsl
Texture2D gDiffuseMap : register(t0); 
```

*注意，纹理寄存器由 tn 来标定，其中，整数 n 表示的是纹理寄存器的槽号。此根签名的定义指出了由槽位参数到着色器寄存器的映射关系，这便是应用程序代码能将 SRV 绑定到着色器中特定 Texture2D 对象的原因。*

## ***小结***

1. *纹理坐标用于定义将要映射到 3D 三角形上的纹理三角形*

2. *对于游戏而言，创建纹理的常用方法是，请贴图师在 Photoshop 或一些其他的图像编辑器中进行创作，并将结果存为某种图像文件，如 BMP、DDS、TGA 或 PNG。接着，游戏应用程序会在加载资源期间将图像数据载入 ID3D12Resource 对象。*

   *对于实时图形应用程序来说，DDS （DirectDraw 图面格式）图像文件是极好的选择，因为它支持各种 GPU 本身即可处理的图像格 式，以及 GPU 可原生解压的压缩图像格式。*

3. *有两种将传统图像格式转换为 DDS 格式的常用方法：使用图像编辑器或借助一种微软公司提供的名为 texconv 的命令行工具导出 DDS 格式。*

4. *我们能够通过 CreateDDSTextureFromFile12 函数以存于磁盘中的图像文件来创建纹理*

5. *当放大物体表面并试图以少数纹素来覆盖大量屏幕像素时，便会涉及纹理放大的问题。而当缩小物体表面并尝试令大量纹素覆盖少数屏幕像素时，就会进行纹理缩小的相关操作。*

   *mipmap 与纹理过滤器是处理纹理放大与缩小这两种操作的关键技术。GPU 原生支持 3 种纹理过滤器（根据质量由低到高、开销由廉至贵的顺序来排列），即点过滤器、线性过滤器以及各向异性过滤器。*

6. *纹理的寻址模式定义了 Direct3D 将如何处理超出范围[0, 1]外的纹理坐标。例如，对于那些超出范围的纹理，究竟是应当采取平铺、镜像，还是钳位或其他处理方式呢？*

7. *我们可以像变换普通点那样，利用纹理坐标对纹理进行缩放、旋转以及平移。通过在每一帧中小幅度渐进地变换纹理坐标，便可以实现纹理的动画效果。*

