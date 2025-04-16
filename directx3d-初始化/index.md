# DirectX3D的初始化


# ***Direct3D的初始化***

## ***预备知识***

*通过 Direct3D 这种底层图形应用程序编程接口（Application Programming Interface，API），即可在 在应用程序中对图形处理器（Graphics Processing Unit，GPU）进行控制和编程。我们能够借此以硬件加 速的方式渲染出虚拟的 3D 场景*

### ***组件对象模型***

*组件对象模型（Component Object Model，COM）是一种令 DirectX 不受编程语言束缚，并且使之向后兼 容的技术。*

*我们通常将 COM 对象视为一种接口，但考虑当前编程的目的，遂将它当作一个 C++类来使用。*

*用 C++语言编写 DirectX 程序时，COM 帮我们隐藏了大量底层细节。*

*我们只需知道：要获取指向某 COM 接 口的指针，需借助特定函数或另一COM 接口的方法——而不是用C++语言中的关键字new 去创建一个COM 接口。*

*另外，COM 对象会统计其引用次数；因此，在使用完某接口时，我们便应调用它的 Release 方法（COM 接口的所有功能都是从 IUnknown 这个 COM 接口继承而来的，包括 Release 方法在内），而不是用 delete 来删除——当 COM 对象的引用计数为 0 时，它将自行释放自己所占用的内存。*

### ***纹理格式***

*2D 纹理（2D texture）是一种由数据元素构成的矩阵（可将此“矩阵”看作 2D 数组）。*

*它的用途之 一是存储 2D 图像数据，在这种情况下，纹理中每个元素存储的都是一个像素的颜色。然而，纹理的 用处并非仅此而已。*

*例如，有种称作法线贴图（normal mapping）的高级技术，其纹理内的每个元素存 储的就是一个 3D 向量而不是颜色信息。*

*因此，尽管纹理给人的第一印象通常是用来存储图像数据，但 其实际用途却十分广泛。*

*简单来讲，1D、2D、3D 纹理就相当于特定数据元素所构成 1D、2D、3D 数 组。但随着后续章节中对纹理讨论的逐渐深入，我们便会知道，纹理其实还不只是像“数据数组”那 样简单。它们可能还具有多种 mipmap 层级，而 GPU 则会据此对它们进行特殊的处理，例如运用过 滤器（filter）和进行多重采样（multisample）。另外，并不是任意类型的数据元素都能用于组成纹理， 它只能存储 DXGI_FORMAT 枚举类型中描述的特定格式的数据元素*

### ***交换链和页面翻转***

*为了避免动画中出现画面闪烁的现象，最好将动画帧完整地绘制在一种称为后台缓冲区的离屏 （off-screen，即不可直接呈现在显示设备上之意）纹理内。*

*只要将指定动画帧的整个场景绘到后台缓冲区 中，它就会以一个完整的帧画面展现在屏幕上；*

*依照此法，观者便不会察觉出帧的绘制过程——而只会 观赏到完整的动画帧。*

*为此，需要利用由硬件管理的两种纹理缓冲区：即所谓的前台缓冲区（front buffer） 和后台缓冲区（back buffer）。*

*前台缓冲区存储的是当前显示在屏幕上的图像数据，而动画的下一帧则被 绘制在后台缓冲区里。*

*当后台缓冲区中的动画帧绘制完成之后，两种缓冲区的角色互换：后台缓冲区变 为前台缓冲区呈现新一帧的画面，而前台缓冲区则为了展示动画的下一帧转为后台缓冲区，等待填充数 据。前后台缓冲的这种互换操作称为呈现（presenting，亦有译作提交、显示等）。*

*呈现是一种高效的操 作，只需交换指向当前前台缓冲区和后台缓冲区的两个指针即可实现。*

### ***深度缓冲***

*深度缓冲区（depth buffer）这种纹理资源存储的并非图像数据，而是特定像素的深度信息。*

*深度值 的范围为 0.0～1.0。0.0 代表观察者在视锥体（view frustum， 亦有译作视域体、视景体、视截体或视体等，意即观察者 能看到的空间范围，形如从四棱锥中截取的四棱台，常称 该形为平截头体（frustum，见图 4.3，后文亦有详述））中 能看到离自己最近的物体，1.0 则代表观察者在视锥体中能 看到离自己最远的物体。*

*深度缓冲区中的元素与后台缓冲 区内的像素呈一 一对应关系（即后台缓冲区中第 i 行第 j 列 的元素对应于深度缓冲区内第 i 行第 j 列的元素）。所以， 如果后台缓冲区的分辨率为1280 × 1024  ，那么深度缓冲区 中就应当有1280 × 1024 个深度元素*

### ***资源与描述符***

*在渲染处理的过程中，GPU 可能会对资源进行读（例如，从描述物体表面样貌的纹理或者存有 3D 场景中几何体位置信息的缓冲区中读取数据）和写（例如，向后台缓冲区或深度/模板缓冲区写入数据） 两种操作。*

*在发出绘制命令之前，我们需要将与本次绘制调用（draw call）相关的资源绑定（bind 或称 链接，link）到渲染流水线上。*

*部分资源可能在每次绘制调用时都会有所变化，所以我们也就要每次按 需更新绑定。*

*但是，GPU 资源并非直接与渲染流水线相绑定，而是要通过一种名为描述符（descriptor） 的对象来对它间接引用，我们可以把描述符视为一种对送往 GPU 的资源进行描述的轻量级结构。*

*从本质上来讲，它实际上即为一个中间层；若指定了资源描述符，GPU 将既能获得实际的资源数据，也能了解到 资源的必要信息。*

*因此，我们将把绘制调用需要引用的资源，通过指定描述符的方式绑定到渲染流水线。*

### ***多重采样技术的原理***

*由于屏幕中显示的像素不可能是无穷小的，所以并不是任意一条直线都能在显示器上“平滑”而 完美地呈现出来。*

*图 4.4 所示的，即为以像素矩阵（matrix of pixels，可以理解为“像素 2D 数组”）逼近 直线的方法所产生的“阶梯”（aliasing，锯齿状走样）效果。类似地，显示器中呈现的三角形之边也存 在着不同程度的锯齿效应。 *

*通过提高显示器的分辨率就能够缩小像素的大小， 继而使上述问题得到显著地改善，使阶梯效应在很大 程度上不易被用户所察觉。 *

*在不能提升显示器分辨率，或在显示器分辨率受 限的情况下，我们就可以运用各种反走样（antialiasing， 也有译作抗锯齿、反锯齿、反失真等）技术。*

*有一种 名为超级采样（supersampling，可简记作 SSAA，即 Super Sample Anti-Aliasing）的反走样技术，它使用 4 倍于屏幕分辨率大小的后台缓冲区和深度缓冲区。3D 场景将以这种更大的分辨率渲染到后台缓冲区中。当 数据要从后台缓冲区调往屏幕显示的时候，会将后台 缓冲区按 4 个像素一组进行解析（resolve，或称降采 样，downsample。把放大的采样点数降低回原采样点数）：每组用求平均值的方法得到一种相对平滑 的像素颜色。因此，超级采样实际上是通过软件的方式提升了画面的分辨率。*

*超级采样是一种开销高昂的操作，因为它将像素的处理数量和占用的内存大小都增加到之前的 4 倍。 对此，Direct3D 还支持一种在性能与效果等方面都较为折中的反走样技术，叫作多重采样（multisampling，可简记作 MSAA，即 MultiSample Anti-Aliasing）。*

*这种技术通过跨子像素①共享一些计算信息，从而使它比 超级采样的开销更低。现假设采用 4X 多重采样（即每个像素中都有 4 个子像素），并同样使用 4 倍于屏幕 分辨率的后台缓冲区和深度缓冲区。*

*值得注意的是，这种技术并不需要对每一个子像素都进行计算，而是仅 计算一次像素中心处的颜色，再基于可视性（每个子像素经深度/模板测试的结果）和覆盖性（子像素的中心 在多边形的里面还是外面？）将得到的颜色信息分享给其子像素*

### ***利用 Direct3D 进行多重采样***

### ***功能级别***

### ***功能支持的检测***

### ***资源驻留***

## ***初始化Direct3D***

*我们对 Direct3D 进行初始化的过程可以分为以下几个步骤*

1. *用 D3D12CreateDevice 函数创建 ID3D12Device 接口实例。*
2. *创建一个 ID3D12Fence 对象，并查询描述符的大小。*
3. *检测用户设备对 4X MSAA 质量级别的支持情况。*
4. *依次创建命令队列、命令列表分配器和主命令列表。*
5. *描述并创建交换链*
6. *创建应用程序所需的描述符堆*
7. *调整后台缓冲区的大小，并为他创建渲染目标视图*
8. *创建深度 / 模板缓冲区及与之关联的深度 / 模板视图*
9. *设置视口（viewport）和裁剪矩形（scissor rectangle）*

### ***创建设备（D3D12CreateDevice）***

*要初始化 Direct3D，必须先创建 Direct3D 12 设备（ID3D12Device）通过下面的函数就可以创建 Direct3D 12 设备：*

```cpp
HRESULT WINAPI D3D12CreateDevice(
    _In_opt_ IUnknown* pAdapter,
    D3D_FEATURE_LEVEL MinimumFeatureLevel,
    _In_ REFIID riid, // Expected: ID3D12Device
    _COM_Outptr_opt_ void** ppDevice )
```

1. *pAdapter：指定在创建设备时所用的显示适配器。若将此参数设定为空指针，则使用主显示适配器。我们在本书的示例中总是采用主适配器*
2. *MinimumFeatureLevel: 应用程序需要硬件所支持的最低功能级别。如果适配器不支持此功能级别，则设备创建失败。支持 Direct3D 11 的特性的话 指定的是 D3D_FEATURE_LEVEL_11_0*
3. *riid：所建 ID3D12Device 接口的 COM ID*
4. *ppDevice：返回所创建的 Direct3D 12 设备*

*以下是此函数的调用示例*

```cpp
#if defined(DEBUG) || defined(_DEBUG) 
	// 启用 D3D12 的调试层
{
	ComPtr<ID3D12Debug> debugController;
	ThrowIfFailed(D3D12GetDebugInterface(IID_PPV_ARGS(&debugController)));
	debugController->EnableDebugLayer();
}
#endif

ThrowIfFailed(CreateDXGIFactory1(IID_PPV_ARGS(&mdxgiFactory)));

// 尝试创建硬件设备
HRESULT hardwareResult = D3D12CreateDevice(
    nullptr,             // default adapter
    D3D_FEATURE_LEVEL_11_0,
    IID_PPV_ARGS(&md3dDevice)
);

// 回退至 WARP 设备
if(FAILED(hardwareResult))
{
    ComPtr<IDXGIAdapter> pWarpAdapter;
    ThrowIfFailed(mdxgiFactory->EnumWarpAdapter(IID_PPV_ARGS(&pWarpAdapter)));

    ThrowIfFailed(D3D12CreateDevice(
        pWarpAdapter.Get(),
        D3D_FEATURE_LEVEL_11_0,
        IID_PPV_ARGS(&md3dDevice)));
}
```

1. *可以看到，为了进入调试模式，我们首先开启了调试层（debug layer）。*

2. *随后，Direct3D 便会开启额 外的调试功能，并在错误发生时向 VC++的输出窗口发送类似于下面的调试信息*

   ```cpp
   D3D12 ERROR: ID3D12CommandList::Reset: Reset fails because the command list was not closed.
   （D3D12 ERROR: ID3D12CommandList::Reset: 由于没有关闭命令列表因此重置失败。）
   ```

3. *还可以发现，当调用 D3D12CreateDevice 失败后，程序将回退到一种软件适配器：WARP 设备*

   *WARP意为 Windows Advanced Rasterization Platform（Windows 高级光栅化平台）。
   在 Windows 7 及以下版本的操作系统中，WARP 设备支持的最高功能级别是 10.1；在 Windows 8 系统中，WARP 设备支持的最高功能级别是11.1。
   为了创建 WARP 适配器，需要先创建一个 IDXGIFactory4 对象，并通过它来枚举 WARP 适配器：*

   ```cpp
   ThrowIfFailed(mdxgiFactory->CreateSwapChain(
       mCommandQueue.Get(),
       &sd, 
       mSwapChain.GetAddressOf())
   );
   ```

   *作为 DXGI 的一部分，mdxgiFactory 对象也可用于创建交换链。*

### ***创建围栏并获取描述符的大小（CreateFence）***

*一旦创建好设备，便可以为 CPU/GPU 的同步而创建围栏了。*

*另外，若用描述符进行工作，还需要了解它们的大小，但描述符在不同的CPU平台上大小各异*

*随后我们会把描述符的大小缓存起来，需要时即可直接引用*

```cpp
ThrowIfFailed(md3dDevice->CreateFence(0, D3D12_FENCE_FLAG_NONE, IID_PPV_ARGS(&mFence)));

mRtvDescriptorSize = md3dDevice->GetDescriptorHandleIncrementSize(D3D12_DESCRIPTOR_HEAP_TYPE_RTV);
mDsvDescriptorSize = md3dDevice->GetDescriptorHandleIncrementSize(D3D12_DESCRIPTOR_HEAP_TYPE_DSV);
mCbvSrvUavDescriptorSize = md3dDevice->GetDescriptorHandleIncrementSize(D3D12_DESCRIPTOR_HEAP_TYPE_CBV_SRV_UAV);
```

### ***检测对 4X MSAA 质量级别的支持（D3D12_FEATURE_DATA_MULTISAMPLE_QUALITY_LEVELS）***

*这里选择 4X，是因为借此采样数量就可以获 得开销不高却性能不凡的效果。而且，在一切支持 Direct3D 11 的设备上，所有的渲染目标格式就皆已支 持 4X MSAA 了。因此，凡是支持 Direct3D 11 的硬件，都会保证此项功能的正常开启，我们也就无须再 对此进行检验了。但是，对质量级别的检测还是不可或缺，为此，可采取下列方法加以实现：*

```cpp
D3D12_FEATURE_DATA_MULTISAMPLE_QUALITY_LEVELS msQualityLevels;
msQualityLevels.Format = mBackBufferFormat;
msQualityLevels.SampleCount = 4;
msQualityLevels.Flags = D3D12_MULTISAMPLE_QUALITY_LEVELS_FLAG_NONE;
msQualityLevels.NumQualityLevels = 0;
ThrowIfFailed(md3dDevice->CheckFeatureSupport(
    D3D12_FEATURE_MULTISAMPLE_QUALITY_LEVELS,
    &msQualityLevels,
    sizeof(msQualityLevels))
);

m4xMsaaQuality = msQualityLevels.NumQualityLevels;
assert(m4xMsaaQuality > 0 && "Unexpected MSAA quality level.");
```

*由于我们所用的平台必能支持 4X MSAA 这一功能，其返回值应该也总是大于 0，所以对此而做出上述断言*

### ***创建命令队列和命令列表***

***ID3D12CommandQueue 接口表示命令队列***

***ID3D12CommandAllocator 接 口代表命令分配器***

***ID3D12GraphicsCommandList 接口表示命令列表***

```cpp
D3D12_COMMAND_QUEUE_DESC queueDesc = {};
queueDesc.Type = D3D12_COMMAND_LIST_TYPE_DIRECT;
queueDesc.Flags = D3D12_COMMAND_QUEUE_FLAG_NONE;
ThrowIfFailed(md3dDevice->CreateCommandQueue(&queueDesc, IID_PPV_ARGS(&mCommandQueue)));

ThrowIfFailed(md3dDevice->CreateCommandAllocator(
    D3D12_COMMAND_LIST_TYPE_DIRECT,
    IID_PPV_ARGS(mDirectCmdListAlloc.GetAddressOf())));

ThrowIfFailed(md3dDevice->CreateCommandList(
    0,
    D3D12_COMMAND_LIST_TYPE_DIRECT,
    mDirectCmdListAlloc.Get(),  // 关联命令分配器
    nullptr,                   // 初始化流水线状态对象
    IID_PPV_ARGS(mCommandList.GetAddressOf())));

// 首先要将命令列表置于关闭状态。这是因为在第一次引用命令列表时，我们要对它进行重置，而在调用
// 重置方法之前又需先将其关闭
mCommandList->Close();
```

### ***描述并创建交换链（DXGI_SWAP_CHAIN_DESC）***

*初始化流程的下一步是创建交换链*

*首先，要填写一份 DXGI_SWAP_CHAIN_DESC 结构体实例，用 它来描述欲创建交换链的特性。此结构体的定义如下：*

```cpp
typedef struct DXGI_SWAP_CHAIN_DESC
{
    DXGI_MODE_DESC BufferDesc;
    DXGI_SAMPLE_DESC SampleDesc;
    DXGI_USAGE BufferUsage;
    UINT BufferCount;
    HWND OutputWindow;
    BOOL Windowed;
    DXGI_SWAP_EFFECT SwapEffect;
    UINT Flags;
} 	DXGI_SWAP_CHAIN_DESC;
```

1. *BufferDesc：这个结构体描述了待创建后台缓冲区的属性。在这里我们仅关注它的宽度、高 度和像素格式属性*

   *其中的 DXGI_MODE_DESC 类型则是另一种结构体，它的定义为：*

   ```cpp
   typedef struct DXGI_MODE_DESC
   {
       UINT Width;
       UINT Height;
       DXGI_RATIONAL RefreshRate;
       DXGI_FORMAT Format;
       DXGI_MODE_SCANLINE_ORDER ScanlineOrdering;
       DXGI_MODE_SCALING Scaling;
   } DXGI_MODE_DESC;
   ```

2. *SampleDesc：多重采样的质量级别以及对每个像素的采样次数。对于单次采 样来说，我们要将采样数量指定为 1，质量级别指定为 0*

3. *BufferUsage：由于我们要将数据渲染至后台缓冲区（即用它作为渲染目标），因此将此参数 指定为 DXGI_USAGE_RENDER_TARGET_OUTPUT*

4. *BufferCount：交换链中所用的缓冲区数量。我们将它指定为 2，即采用双缓冲*

5. *OutputWindow：渲染窗口的句柄。*

6. *Windowed：若指定为 true，程序将在窗口模式下运行；如果指定为 false，则采用全屏模式。*

7. *SwapEffect：指定为 DXGI_SWAP_EFFECT_FLIP_DISCARD。*

8. *Flags：可选标志。如果将其指定为 DXGI_SWAP_CHAIN_FLAG_ALLOW_MODE_SWITCH，那 么，当程序切换为全屏模式时，它将选择最适于当前应用程序窗口尺寸的显示模式。如果没有 指定该标志，当程序切换为全屏模式时，将采用当前桌面的显示模式*

*描述完交换链之后，我们用 IDXGIFactory::CreateSwapChain 方法来创建它：*

```cpp
HRESULT STDMETHODCALLTYPE CreateSwapChain( 
    /* [annotation][in] */ 
    IUnknown *pDevice,  // 指向 ID3D12CommandQueue 接口的指针
    /* [annotation][in] */ 
    DXGI_SWAP_CHAIN_DESC *pDesc,  // 指向描述交换链的结构体的指针
    /* [annotation][out] */ 
    IDXGISwapChain **ppSwapChain  // 返回所创建的交换链接口
)
```

*研究此函数的代码就会发现，我 们是按照可以对它进行多次调用来设计的。*

*即，在创建新的交换链之前，先要销毁旧的交换链。这样一来，我们就可以用不同的设置来重新创建交换链，借此在运行时修改多重采样的配置。*

```cpp
DXGI_FORMAT mBackBufferFormat = DXGI_FORMAT_R8G8B8A8_UNORM;

void D3DApp::CreateSwapChain()
{
    // Release the previous swapchain we will be recreating.
    mSwapChain.Reset();

    DXGI_SWAP_CHAIN_DESC sd;
    sd.BufferDesc.Width = mClientWidth;
    sd.BufferDesc.Height = mClientHeight;
    sd.BufferDesc.RefreshRate.Numerator = 60;
    sd.BufferDesc.RefreshRate.Denominator = 1;
    sd.BufferDesc.Format = mBackBufferFormat;
    sd.BufferDesc.ScanlineOrdering = DXGI_MODE_SCANLINE_ORDER_UNSPECIFIED;
    sd.BufferDesc.Scaling = DXGI_MODE_SCALING_UNSPECIFIED;
    sd.SampleDesc.Count = m4xMsaaState ? 4 : 1;
    sd.SampleDesc.Quality = m4xMsaaState ? (m4xMsaaQuality - 1) : 0;
    sd.BufferUsage = DXGI_USAGE_RENDER_TARGET_OUTPUT;
    sd.BufferCount = SwapChainBufferCount;
    sd.OutputWindow = mhMainWnd;
    sd.Windowed = true;
	sd.SwapEffect = DXGI_SWAP_EFFECT_FLIP_DISCARD;
    sd.Flags = DXGI_SWAP_CHAIN_FLAG_ALLOW_MODE_SWITCH;

	// Note: Swap chain uses queue to perform flush.
    ThrowIfFailed(mdxgiFactory->CreateSwapChain(
		mCommandQueue.Get(),
		&sd, 
		mSwapChain.GetAddressOf())
	);
}
```

### ***创建描述符堆（ID3D12DescriptorHeap）***

*我们需要通过创建描述符堆来存储程序中要用到的描述符/视图*

*Direct3D 12 以 ID3D12DescriptorHeap 接口表示描述符堆，并用 ID3D12Device::CreateDescriptorHeap 方法来 创建它*

*我们将为交换链中 SwapChainBufferCount 个用于渲染数据的缓冲区 资源创建对应的渲染目标视图（Render Target View，RTV），并为用于深度测试（depth test）的深度/模 板缓冲区资源创建一个深度/模板视图（Depth/Stencil View，DSV）。*

*所以，我们此时需要创建两个描述符 堆，其一用来存储 SwapChainBufferCount 个 RTV，而那另一个描述堆则用来存储那 1 个 DSV*

*现通过下述代码来创建这两个描述符堆：*

```cpp
Microsoft::WRL::ComPtr<ID3D12DescriptorHeap> mRtvHeap;
Microsoft::WRL::ComPtr<ID3D12DescriptorHeap> mDsvHeap;

void D3DApp::CreateRtvAndDsvDescriptorHeaps()
{
    D3D12_DESCRIPTOR_HEAP_DESC rtvHeapDesc;
    rtvHeapDesc.NumDescriptors = SwapChainBufferCount;
    rtvHeapDesc.Type = D3D12_DESCRIPTOR_HEAP_TYPE_RTV;
    rtvHeapDesc.Flags = D3D12_DESCRIPTOR_HEAP_FLAG_NONE;
	rtvHeapDesc.NodeMask = 0;
    ThrowIfFailed(md3dDevice->CreateDescriptorHeap(&rtvHeapDesc, IID_PPV_ARGS(mRtvHeap.GetAddressOf())));

    D3D12_DESCRIPTOR_HEAP_DESC dsvHeapDesc;
    dsvHeapDesc.NumDescriptors = 1;
    dsvHeapDesc.Type = D3D12_DESCRIPTOR_HEAP_TYPE_DSV;
    dsvHeapDesc.Flags = D3D12_DESCRIPTOR_HEAP_FLAG_NONE;
	dsvHeapDesc.NodeMask = 0;
    ThrowIfFailed(md3dDevice->CreateDescriptorHeap(
        &dsvHeapDesc, IID_PPV_ARGS(mDsvHeap.GetAddressOf()))
    );
}
```

*创建描述符堆之后，还要能访问其中所存的描述符。*

#### ***ID3D12DescriptorHeap::GetCPUDescriptorHandleForHeapStart***

*在程序中，我们是通过句柄来引用描述符的， 并以 ID3D12DescriptorHeap::GetCPUDescriptorHandleForHeapStart 方法来获得描述符堆中第一个描述符的句柄*

*借助下列函数即可获取当前后台缓冲区的 RTV 与 DSV：*

```cpp
D3D12_CPU_DESCRIPTOR_HANDLE D3DApp::CurrentBackBufferView()const
{
	return CD3DX12_CPU_DESCRIPTOR_HANDLE(
		mRtvHeap->GetCPUDescriptorHandleForHeapStart(),
		mCurrBackBuffer,
		mRtvDescriptorSize
	);
}

D3D12_CPU_DESCRIPTOR_HANDLE D3DApp::DepthStencilView()const
{
	return mDsvHeap->GetCPUDescriptorHandleForHeapStart();
}
```

*通过这段示例代码，我们就能够看出描述符大小的用途了。为了用偏移量找到当前后台缓冲区的 RTV 描述符①，我们就必须知道 RTV 描述符的大小*

### ***创建渲染目标视图***

*资源不能与渲染流水线中的阶段直接绑定，所以我们必须先为资源创建视图（描述符）并将其绑定到流水线阶段*

*为了将后台缓冲区绑定到流水线的输出合并阶段（output merger stage，这样 Direct3D 才能向其渲染），便需要为该后台缓冲区创建一个渲染目标视图。而这第一个步骤就是要获得存 于交换链中的缓冲区资源*

```cpp
HRESULT STDMETHODCALLTYPE GetBuffer( 
    /* [in] */ 
    UINT Buffer,
    /* [annotation][in] */   
    _In_  REFIID riid,    
    /* [annotation][out][in] */ 
    _COM_Outptr_  void **ppSurface
)
```

1. *Buffer：希望获得的特定后台缓冲区的索引（有时后台缓冲区并不只一个，所以需要用索引来 指明）*
2. *riid：希望获得的 ID3D12Resource 接口①的 COM ID。*
3. *ppSurface：返回一个指向 ID3D12Resource 接口的指针，这便是希望获得的后台缓冲区。*

*调用 IDXGISwapChain::GetBuffer 方法会增加相关后台缓冲区的 COM 引用计数，所以在每次使用后一定要将其释放。通过 ComPtr 便可以自动做到这一点。*

#### ***ID3D12Device::CreateRenderTargetView***

*接下来，使用 ID3D12Device::CreateRenderTargetView 方法来为获取的后台缓冲区创建渲染目标视图。*

```cpp
void STDMETHODCALLTYPE CreateRenderTargetView( 
    ID3D12Resource *pResource,
    const D3D12_RENDER_TARGET_VIEW_DESC *pDesc,
    D3D12_CPU_DESCRIPTOR_HANDLE DestDescriptor
)
```

1. *pResource：指定用作渲染目标的资源。在上面的例子中是后台缓冲区（即为后台缓冲区创建 了一个渲染目标视图*
2. *pDesc：指向 D3D12_RENDER_TARGET_VIEW_DESC 数据结构实例的指针。该结构体描述了资源中元素的数据类型（格式）。如果该资源在创建时已指定了具体格式（即此资源不是无类型 格式，not typeless），那么就可以把这个参数设为空指针，表示采用该资源创建时的格式，为它的第一个 mipmap 层级（后台缓冲区只有一种 mipmap 层级）创建一个视图。由于已经指定了后台缓冲区的格式，因此就将这个参数设置为空指针。*
3. *DestDescriptor：引用所创建渲染目标视图的描述符句柄*

*下面的示例通过调用这两种方法为交换链中的每一个缓冲区都创建了一个 RTV：*

```cpp
Microsoft::WRL::ComPtr<ID3D12Resource> mSwapChainBuffer[SwapChainBufferCount];

CD3DX12_CPU_DESCRIPTOR_HANDLE rtvHeapHandle(mRtvHeap->GetCPUDescriptorHandleForHeapStart());
for (UINT i = 0; i < SwapChainBufferCount; i++)
{
    ThrowIfFailed(mSwapChain->GetBuffer(i, IID_PPV_ARGS(&mSwapChainBuffer[i])));
    md3dDevice->CreateRenderTargetView(mSwapChainBuffer[i].Get(), nullptr, rtvHeapHandle);
    rtvHeapHandle.Offset(1, mRtvDescriptorSize);
}
```

### ***创建深度 / 模板缓冲区及其视图***

*深度缓冲区其实就是一种 2D 纹理， 它存储着离观察者最近的可视对象的深度信息（如果使用了模板，还会附有模板信息）*

#### ***D3D12_RESOURCE_DESC***

*纹理是一种 GPU 资源，因此我们要通过填写 D3D12_RESOURCE_DESC 结构体来描述纹理资源，再用 ID3D12Device:: CreateCommittedResource 方法来创建它*

```cpp
typedef struct D3D12_RESOURCE_DESC
{
    D3D12_RESOURCE_DIMENSION Dimension;
    UINT64 Alignment;
    UINT64 Width;
    UINT Height;
    UINT16 DepthOrArraySize;
    UINT16 MipLevels;
    DXGI_FORMAT Format;
    DXGI_SAMPLE_DESC SampleDesc;
    D3D12_TEXTURE_LAYOUT Layout;
    D3D12_RESOURCE_FLAGS Flags;
} 	D3D12_RESOURCE_DESC;
```

1. *Dimension：资源的维度，即为下列枚举类型中的成员之一。*

   ```cpp
   enum D3D12_RESOURCE_DIMENSION
   {
       D3D12_RESOURCE_DIMENSION_UNKNOWN = 0,
       D3D12_RESOURCE_DIMENSION_BUFFER = 1,
       D3D12_RESOURCE_DIMENSION_TEXTURE1D = 2,
       D3D12_RESOURCE_DIMENSION_TEXTURE2D = 3,
       D3D12_RESOURCE_DIMENSION_TEXTURE3D = 4
   } D3D12_RESOURCE_DIMENSION;
   ```

2. *Width：以纹素为单位来表示的纹理宽度。对于缓冲区资源来说，此项是缓冲区占用的字节数*

3. *Height：以纹素为单位来表示的纹理高度。*

4. *DepthOrArraySize：以纹素为单位来表示的纹理深度，或者（对于 1D 纹理和 2D 纹理来说） 是纹理数组的大小。注意，Direct3D 中并不存在 3D 纹理数组的概念*

5. *MipLevels：mipmap 层级的数量。。对于深度 / 模板缓冲区而言，只能有一个 mipmap 级别*

6. *Format：DXGI_FORMAT 枚举类型中的成员之一，用于指定纹素的格式*

7. *SampleDesc：多重采样的质量级别以及对每个像素的采样次数*

8. *Layout：D3D12_TEXTURE_LAYOUT 枚举类型的成员之一，用于指定纹理的布局。我们暂时还不用考虑这个问题，在此将它指定为D3D12_TEXTURE_LAYOUT_UNKNOWN 即可*

9. *Flags：与资源有关的杂项标志。对于一个深度/模板缓冲区资源来说，要将此项指定为 D3D12_RESOURCE_FLAG_ALLOW_DEPTH_STENCIL*

#### ***ID3D12Device::CreateCommittedResource***

*GPU 资源都存于堆（heap）中，其本质是具有特定属性的 GPU 显存块*

*ID3D12Device:: CreateCommittedResource 方法将根据我们所提供的属性创建一个资源与一个堆，并把该资源提交 到这个堆中。*

```cpp
HRESULT ID3D12Device::CreateCommittedResource(
	const D3D12_HEAP_PROPERTIES *pHeapProperties,
	D3D12_HEAP_FLAGS HeapFlags,
	const D3D12_RESOURCE_DESC *pDesc,
	D3D12_RESOURCE_STATES InitialResourceState,
	const D3D12_CLEAR_VALUE *pOptimizedClearValue,
	REFIID riidResource,
	void **ppvResource
); 
```

1. *pHeapProperties：（资源欲提交至的）堆所具有的属性,有一些属性是针对高级用法而设。*

   ```cpp
   typedef struct D3D12_HEAP_PROPERTIES {
       D3D12_HEAP_TYPE Type;
       D3D12_CPU_PAGE_PROPERTY CPUPageProperty;
       D3D12_MEMORY_POOL MemoryPoolPreference;
       UINT CreationNodeMask;
       UINT VisibleNodeMask;
   } D3D12_HEAP_PROPERTIES;
   ```

   *目前只需关心 D3D12_HEAP_PROPERTIES 中的 D3D12_HEAP_TYPE 枚举类型这一主要属性， 其中的成员列举如下*

   * *D3D12_HEAP_TYPE_DEFAULT：默认堆（default heap）。向这堆里提交的资源，唯独 GPU 可以访问。举一个有关深度/模板缓冲区的例子：GPU 会读写深度 / 模板缓冲区，而 CPU 从不需要访问它，所以深度/模板缓冲区应被放入默认堆中*
   * *D3D12_HEAP_TYPE_UPLOAD：上传堆（upload heap）。向此堆里提交的都是需要经 CPU 上传至 GPU 的资源*
   * *D3D12_HEAP_TYPE_READBACK：回读堆（read-back heap）。向这种堆里提交的都是需要由 CPU 读取的资源*
   * *D3D12_HEAP_TYPE_CUSTOM：此成员应用于高级场景*

2. *HeapFlags：与（资源欲提交至的）堆有关的额外选项标志。通常将它设为 D3D12_HEAP_ FLAG_NONE*

3. *pDesc：指向一个 D3D12_RESOURCE_DESC 实例的指针，用它描述待建的资源*

4. *InitialResourceState：不管何时，每个资源都会处于一种特定的使用状态。在资源创建时，需要用此参数来设置它的初始状态。对于深度/模板缓冲 区来说，通常将其初始状态设置为 D3D12_RESOURCE_STATE_COMMON ，再利用 ResourceBarrier 方法辅以 D3D12_RESOURCE_ STATE_DEPTH_WRITE 状态，将其转换 为可以绑定在渲染流水线上的深度/模板缓冲区*

5. *pOptimizedClearValue：指向一个 D3D12_CLEAR_VALUE 对象的指针，它描述了一个用于 清除资源的优化值。选择适当的优化清除值，可提高清除操作的执行速度。若不希望指定优化 清除值，可把此参数设为 nullptr*

   ```cpp
   struct D3D12_CLEAR_VALUE
   {
       DXGI_FORMAT Format;
       union
       {
           FLOAT Color[ 4 ];
           D3D12_DEPTH_STENCIL_VALUE DepthStencil;
       };
   } D3D12_CLEAR_VALUE; 
   ```

6. *riidResource：我们希望获得的 ID3D12Resource 接口的 COM ID*

7. *ppvResource：返回一个指向 ID3D12Resource 的指针，即新建的资源*

*另外，在使用深度/模板缓冲区之前，一定要创建相关的深度/模板视图，并将它绑定到渲染流水线 上。这个流程类似于创建渲染目标视图*

#### ***创建深度/模板纹理及相应的深度/模板视图***

*下面的代码演示了该如何创建深度/模板纹理及相应的深度/模 板视图：*

```cpp
// 创建深度/模板缓冲区及其视图
D3D12_RESOURCE_DESC depthStencilDesc;
depthStencilDesc.Dimension = D3D12_RESOURCE_DIMENSION_TEXTURE2D;
depthStencilDesc.Alignment = 0;
depthStencilDesc.Width = mClientWidth;
depthStencilDesc.Height = mClientHeight;
depthStencilDesc.DepthOrArraySize = 1;
depthStencilDesc.MipLevels = 1;
depthStencilDesc.Format = mDepthStencilFormat;
depthStencilDesc.SampleDesc.Count = m4xMsaaState ? 4 : 1;
depthStencilDesc.SampleDesc.Quality = m4xMsaaState ? (m4xMsaaQuality - 1) : 0;
depthStencilDesc.Layout = D3D12_TEXTURE_LAYOUT_UNKNOWN;
depthStencilDesc.Flags = D3D12_RESOURCE_FLAG_ALLOW_DEPTH_STENCIL;

D3D12_CLEAR_VALUE optClear;
optClear.Format = mDepthStencilFormat;
optClear.DepthStencil.Depth = 1.0f;
optClear.DepthStencil.Stencil = 0;
ThrowIfFailed(md3dDevice->CreateCommittedResource(
    &CD3DX12_HEAP_PROPERTIES(D3D12_HEAP_TYPE_DEFAULT),
    D3D12_HEAP_FLAG_NONE,
    &depthStencilDesc,
    D3D12_RESOURCE_STATE_COMMON,
    &optClear,
    IID_PPV_ARGS(mDepthStencilBuffer.GetAddressOf()))
);

// 利用此资源的格式，为整个资源的第 0 mip 层创建描述符
md3dDevice->CreateDepthStencilView(mDepthStencilBuffer.Get(), nullptr, DepthStencilView());

// 将资源从初始状态转换为深度缓冲区
mCommandList->ResourceBarrier(1, &CD3DX12_RESOURCE_BARRIER::Transition(mDepthStencilBuffer.Get(),D3D12_RESOURCE_STATE_COMMON, D3D12_RESOURCE_STATE_DEPTH_WRITE));
```

*注意，刚刚采用了 CD3DX12_HEAP_PROPERTIES 辅助构造函数来创建堆的属性结构体，它的具体实现如下*

```cpp
explicit CD3DX12_HEAP_PROPERTIES(D3D12_HEAP_TYPE type, UINT creationNodeMask = 1, UINT nodeMask = 1 )
{
    Type = type;
    CPUPageProperty = D3D12_CPU_PAGE_PROPERTY_UNKNOWN;
    MemoryPoolPreference = D3D12_MEMORY_POOL_UNKNOWN;
    CreationNodeMask = creationNodeMask;
    VisibleNodeMask = nodeMask;
}
```

*CreateDepthStencilView 方法的第二个参数是指向 D3D12_DEPTH_STENCIL_VIEW_DESC 结构体的指针*

*这个结构体描述了资源中元素的数据类型（格式）。如果资源在创建时已指定了具体格式（即 此资源不是无类型格式），那么就可以把该参数设为空指针，表示以该资源创建时的格式为它的第一个 mipmap 层级创建一个视图（在创建深度/模板缓冲区时就只有一个 mipmap 层级）。由于我们已经为深度/模板缓冲区设置了具体格式，所以向此参数传入空指针*

### ***设置视口***

*我们通常会将 3D 场景绘制到与整个屏幕（在全屏模式下）或整个窗口工作区大小相当的后台缓冲 区中。但是，有时只是希望把 3D 场景绘制到后台缓冲区的某个矩形子区域当中*

#### ***D3D12_VIEWPORT***

*我们把后台缓冲区中的这种矩形子区域叫作视口（viewport），并通过下列结构体来描述它：*

```cpp
typedef struct D3D12_VIEWPORT
{
    FLOAT TopLeftX;
    FLOAT TopLeftY;
    FLOAT Width;
    FLOAT Height;
    FLOAT MinDepth;
    FLOAT MaxDepth;
} 	D3D12_VIEWPORT;
```

1. *结构体中的前 4 个数据成员定义了视口矩形（viewport rectangle）相对于后台缓冲区的绘制范围（由于数据成员是用 float 类型表示的，所以我们能够以小数精度来指定像素坐标）*

2. *在 Direct3D 中，存储在深度缓冲区中的数据都是范围在 0～1 的归一化深度值。*

   *MinDepth 和 MaxDepth 这两个成员负责 将深度值从区间[0, 1]转换到区间[MinDepth, MaxDepth]*

   *通过对深度范围进行转换即可实现某些特效， 例如，我们可以依次设置 MinDepth=0 和 MaxDepth=0，用此视口绘制的物体其深度值都为 0，它们将比场景中其他物体的位置都更靠前。*

   *然而，在大多数情况下通常会把 MinDepth 与 MaxDepth 分别设置为 0 与 1，也就是令深度值保持不变*

#### ***ID3D12GraphicsCommandList::RSSetViewports***

*只要填写好D3D12_VIEWPORT 结构体，便可以用ID3D12GraphicsCommandList::RSSetViewports 方法来设置 Direct3D 中的视口了。*

*下面的示例是通过创建并设置一个视口，将场景绘至整个后台缓冲区：*

```cpp
D3D12_VIEWPORT vp; 

vp.TopLeftX = 0;
vp.TopLeftY = 0;
vp.Width    = static_cast<float>(mClientWidth);
vp.Height   = static_cast<float>(mClientHeight);
vp.MinDepth = 0.0f;
vp.MaxDepth = 1.0f;

mCommandList->RSSetViewports(1, &vp);
```

1. *第一个参数是要绑定的视口数量（有些高级效果需要使用多个视口）*
2. *第二个参数是一个指向视口数组的指针。*

*事实上，还可以用视口技术来实现双人游戏的分屏（split screen）模式。首先创建两个视口，一个占屏幕左半部，另一个占右半部。接下来，在左视口中以玩家 1 的视角来绘制 3D 场景，再在右视口中以 玩家 2 的视角来绘制 3D 场景即可。*

### ***设置裁剪矩形***

*我们可以相对于后台缓冲区定义一个裁剪矩形（scissor rectangle），在此矩形外的像素都将被剔除（即 这些图像部分将不会被光栅化（rasterize）至后台缓冲区）。*

*这个方法能用于优化程序的性能。例如，假设已知有一个矩形的 UI（user interface，用户界面）元素覆于屏幕中某块区域的最上层，那么我们也就无须对 3D 空间中那些被它遮挡的像素进行处理了*

#### ***D3D12_RECT***

*裁剪矩形由类型为 RECT 的 D3D12_RECT 结构体（typedef RECT D3D12_RECT;）定义而成*

```cpp
typedef struct tagRECT
{
    LONG left;
    LONG top;
    LONG right;
    LONG bottom;
} RECT; 
```

#### ***ID3D12GraphicsCommandList::RSSetScissorRects***

*在 Direct3D 中，要用 ID3D12GraphicsCommandList::RSSetScissorRects 方法来设置裁剪矩形。下面的示例将创建并设置一个覆盖后台缓冲区左上角 1/4 区域的裁剪矩形：*

```cpp
mScissorRect = { 0, 0, mClientWidth/2, mClientHeight/2 };
mCommandList->RSSetScissorRects(1, &mScissorRect); 
```

1. *类似于 RSSetViewports 方法，RSSetScissorRects 方法的第一个参数是要绑定的裁剪矩形数量（为了实现一些高级效果有时会采用多个裁剪矩形）*
2. *第二个参数是指向一个裁剪矩形数组的指针。*

## ***小结***

1. *可以把 Direct3D 看作是一种介于程序员和图形硬件之间的“桥梁”。借此，程序员便可以通过调 用 Direct3D 函数来实现把资源视图绑定到硬件渲染流水线、配置渲染流水线的输出以及绘制 3D 几何体等操作。*
2. *组件对象模型（COM）是一种可以令 DirectX 不依赖于特定语言且向后兼容的技术。Direct3D 程序员不需知道 COM 的具体实现细节，也无需了解其工作原理，只需知晓如何获取和释放 COM 接口即可。*
3. *1D、2D、3D 纹理分别类似于由数据元素所构成的 1D、2D、3D 数组。纹理元素的格式必定为 DXGI_FORMAT 枚举类型中的成员之一。除了常见的图像数据，纹理也能存储像深度信息等其 他类型的数据（如深度缓冲区就是一种存储深度值的纹理）。GPU 可以对纹理进行特殊的操作， 比如运用过滤器和进行多重采样。*
4. *为了避免动画中发生闪烁的问题，最好将动画帧完全绘制到一种称为后台缓冲区的离屏纹理中。 只要依此行事，显示在屏幕上的就会是一个完整的动画帧，观者也就不会察觉到帧的绘制过程。 当动画帧被绘制在后台缓冲区后，前台缓冲区与后台缓冲区的角色也就该互换了：为了显示下一帧动画，此前的后台缓冲区将变为前台缓冲区，而此前的前台缓冲区亦会变成后台缓冲区。 后台和前台缓冲区交换角色的行为称为呈现（present）。前台和后台缓冲区构成了交换链，在代 码中通过 IDXGISwapChain 接口来表示。使用两个缓冲区（前台和后台）的情况称作双缓冲。*
5. *假设场景中有一些不透明的物体，那么离摄像机最近的物体上的点便会遮挡住它后面一切物体上的对应点。深度缓冲就是一种用于确定在场景中离摄像机最近点的技术。通过这种技术，我 们就不必再担心场景中物体的绘制顺序了。*
6. *在 Direct3D 中，资源不能直接与渲染流水线相绑定。为此，我们需要为绘制调用时所引用的资 源指定描述符。我们可将描述符对象看作是 GPU 识别以及描述资源的一种轻量级结构体。而且， 我们还可以为同一种资源创建不同的描述符。如此一来，一种资源就可以具有多种用途。例如， 我们可以借此将同一种资源绑定到渲染流水线的不同阶段，或者用不同的 DXGI_FORMAT 成员 将它描述为不同的格式。应用程序可通过创建描述符堆来为描述符分配所需的内存。*
7. *ID3D12Device 是 Direct3D 中最重要的接口，我们可以把它看作是图形硬件设备的软件控制 器。我们能够通过它来创建 GPU 资源以及其他用于控制图形硬件的特定接口。*
8. *每个 GPU 中都至少有一个命令队列。CPU 可通过 Direct3D API 用命令列表向该队列提交命令， 而这些命令则指挥 GPU 执行某些操作。在命令没有到达队列首部以前，用户所提交的命令是无 法被执行的。如果命令队列内为空，则 GPU 会因为没有任务要去处理而处于空闲状态；但若命 令队列被装得太满，则 CPU 将在某个时刻因提交命令的速度追上 GPU 执行命令的速度而进入 空闲状态。值得一提的是，这两种情景其实都没有充分地利用系统资源。*
9. *GPU 是系统中与 CPU 一起并行工作的第二种处理器。有时，我们需要对 CPU 与 GPU 进行同步。 例如，若 GPU 命令队列中有一条引用某资源的命令，那么在 GPU 完成此命令的处理之前，CPU 就不能修改或销毁这一资源。任何同步方法都会导致其中的一种处理器处于一段等待和空闲的 状态，这意味着两种处理器并没有被充分利用，因此，我们应尽量减少同步的次数，并缩短同 步的时间。*
10. *性能计数器是一种高精度的计时器，它是测量微小时间差的一种有效工具。例如，我们 可以用它来测量两帧之间的间隔时间。性能计时器使用的时间单位称为计数（count）。 QueryPerformanceFrequency 函数输出的是性能计时器每秒的计数，可用它将计数单位转 换为秒。性能计时器的当前时间值（以计数为单位测量）可用 QueryPerformanceCounter 函数获得。*
11. *通过统计时间段 Δt 内处理的帧数即可计算出每秒的平均帧数（FPS）。设 n 为时间 Δt 内处理的帧 数，那么该时间段内每秒的平均帧数为 $ FPS\_{avg}= n  / Δt $ 。采用帧率进行考量可能会对性能造成一些 误判，相对而言，“处理一帧所花费时间”这个统计信息可能更加精准、直观。以秒为单位表示 的每帧平均处理时间可以用帧率的倒数来计算，即 $ 1 / FPS\_{avg} $*

