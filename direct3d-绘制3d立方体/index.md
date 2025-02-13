# 绘制3D立方体


# ***绘制3D立方体***

## ***顶点与输入布局***

*除了空间位置，Direct3D 中的顶点还可以存储其他属性数据*

*为了构建自定义的 顶点格式，我们首先要创建一个结构体来容纳选定的顶点数据。例如，下面列出了两种不同类型的顶点 格式：一种由位置和颜色信息组成，另一种则由位置、法向量以及两组 2D 纹理坐标构成。*

```cpp
#include <DirectXMath.h>
using namespace DirectX;

struct Vertex1
{
	XMFLOAT3 Pos;
	XMFLOAT4 Color;
};

struct Vertex2
{
	XMFLOAT3 Pos;
	XMFLOAT3 Normal;
	XMFLOAT2 Tex0;
	XMFLOAT2 Tex1;
};

```

*定义了顶点结构体之后，我们还需要向 Direct3D 提供该顶点结构体的描述，使它了解应怎样来处理 结构体中的每个成员。用户提供给 Direct3D 的这种描述被称为**输入布局描述***

### ***D3D12_INPUT_LAYOUT_DESC***

***用结构体 D3D12_INPUT_LAYOUT_DESC 来表示***

```cpp
typedef struct D3D12_INPUT_LAYOUT_DESC
{
	const D3D12_INPUT_ELEMENT_DESC *pInputElementDescs;
	UINT NumElements;
} D3D12_INPUT_LAYOUT_DESC;
// 输入布局描述实由两部分组成：即一个以 D3D12_INPUT_ELEMENT_DESC 元素构成的数组，以及一个表示该数组中的元素数量的整数
```

*D3D12_INPUT_ELEMENT_DESC 数组中的元素依次描述了顶点结构体中所对应的成员。这就是说， 如果某顶点结构体中有两个成员，那么与之对应的 D3D12_INPUT_ELEMENT_DESC 数组也将存有两个 元素。D3D12_INPUT_ELEMENT_DESC 结构体的定义如下：*

```cpp
typedef struct D3D12_INPUT_ELEMENT_DESC
{
 LPCSTR SemanticName;
 UINT SemanticIndex;
 DXGI_FORMAT Format;
 UINT InputSlot;  // 指定传递元素所用的输入槽（input slot index）索引
 UINT AlignedByteOffset;  // 在特定输入槽中，从 C++顶点结构体的首地址到其中某点元素起始地址的偏移量（用字节表示）
 D3D12_INPUT_CLASSIFICATION InputSlotClass;  // 我们暂且把此参数指定为 D3D12_INPUT_CLASSIFICATION_PER_VERTEX_DATA
 UINT InstanceDataStepRate;  // 目前仅将此值指定为 0。若要采用实例化这种高级技术，则将此参数设为 1
} D3D12_INPUT_ELEMENT_DESC; 
```

1. *SemanticName: 一个与元素相关联的特定字符串，我们称之为语义（semantic），它传达了元素的预期用途 该参数可以是任意合法的语义名*

2. *SemanticIndex: 附加到语义上的索引 例如，顶点结构体中的纹理坐标可能不止一组，而仅在语义名尾部添加一个索引，即可在不引入新语义名的情况下区分出这两组不同的纹理坐标*

3. *Format: 在 Direct3D 中，要通过枚举类型 DXGI_FORMAT 中的成员来指定顶点元素的格式（即数据类型）*

   ```cpp
   下面是一些常用的格式。
        DXGI_FORMAT_R32_FLOAT // 1D 32 位浮点标量
        DXGI_FORMAT_R32G32_FLOAT // 2D 32 位浮点向量
        DXGI_FORMAT_R32G32B32_FLOAT // 3D 32 位浮点向量
        DXGI_FORMAT_R32G32B32A32_FLOAT // 4D 32 位浮点向量
        DXGI_FORMAT_R8_UINT // 1D 8 位无符号整型标量
        DXGI_FORMAT_R16G16_SINT // 2D 16 位有符号整型向量
        DXGI_FORMAT_R32G32B32_UINT // 3D 32 位无符号整型向量
        DXGI_FORMAT_R8G8B8A8_SINT // 4D 8 位有符号整型向量
        DXGI_FORMAT_R8G8B8A8_UINT // 4D 8 位无符号整型向量
   ```

4. *InputSlot: 指定传递元素所用的输入槽（input slot index）索引。Direct3D 共支持 16 个输入槽 （索引值为 0～15），可以通过它们来向输入装配阶段传递顶点数据。目前我们只会用到输入槽 0（即 所有的顶点元素都来自同一个输入槽），但在本博客的习题 2中将会涉及多输入槽的编程实践。*

5. *AlignedByteOffset: 在特定输入槽中，从 C++顶点结构体的首地址到其中某点元素起始地 址的偏移量（用字节表示）。*

   *例如，在下列顶点结构体中，元素 Pos 的偏移量为 0 字节，因为它 的起始地址与顶点结构体的首地址一致；元素 Normal 的偏移量为 12 字节，因为跳过 Pos 所 占用的字节数才能找到 Normal 的起始地址；元素 Tex0 的偏移量为 24 字节，因为跨过 Pos 和 Normal 的总字节数才可获取 Tex0 的起始地址。同理，元素 Tex1 的偏移量为 32 字节， 只有跳过 Pos、Normal 和 Tex0 这 3 个元素的总字节数方可寻得 Tex1 的起始地址。*

   ```cpp
   struct Vertex2
   {
    XMFLOAT3 Pos; // 偏移量为 0 字节
    XMFLOAT3 Normal; // 偏移量为 12 字节
    XMFLOAT2 Tex0; // 偏移量为 24 字节
    XMFLOAT2 Tex1; // 偏移量为 32 字节
   }; 
   ```

6. *InputSlotClass: 我们暂且把此参数指定为 D3D12_INPUT_CLASSIFICATION_PER_ VERTEX_DATA。而另一选项（D3D12_INPUT_CLASSIFICATION_PER_INSTANCE_DATA ） 则用于实现实例化（instancing）这种高级技术*

7. *InstanceDataStepRate：目前仅将此值指定为 0。若要采用实例化这种高级技术，则将此参数设为 1*

*就前面 Vertex1 和 Vertex2 这两个顶点结构体的例子来说，其相应的输入布局描述为：*

```cpp
D3D12_INPUT_ELEMENT_DESC desc1[] = 
{
 {"POSITION", 0, DXGI_FORMAT_R32G32B32_FLOAT, 0, 0,
 D3D12_INPUT_CLASSIFICATION_PER_VERTEX_DATA, 0},
 {"COLOR", 0, DXGI_FORMAT_R32G32B32A32_FLOAT, 0, 12,
 D3D12_INPUT_CLASSIFICATION_PER_VERTEX_DATA, 0}
}; 

D3D12_INPUT_ELEMENT_DESC desc2[] =
{
 {"POSITION", 0, DXGI_FORMAT_R32G32B32_FLOAT, 0, 0,
 D3D12_INPUT_CLASSIFICATION_PER_VERTEX_DATA, 0},
 {"NORMAL", 0, DXGI_FORMAT_R32G32B32_FLOAT, 0, 12,
 D3D12_INPUT_CLASSIFICATION_PER_VERTEX_DATA, 0},
 {"TEXCOORD", 0, DXGI_FORMAT_R32G32_FLOAT, 0, 24,
 D3D12_INPUT_CLASSIFICATION_PER_VERTEX_DATA, 0}
 {"TEXCOORD", 1, DXGI_FORMAT_R32G32_FLOAT, 0, 32,
 D3D12_INPUT_CLASSIFICATION_PER_VERTEX_DATA, 0}
}; 
```

## ***顶点缓冲区***

*为了使 GPU 可以访问顶点数组，就需要把它们放置在称为缓冲区（buffer）的 GPU 资源 （ID3D12Resource）里。我们把存储顶点的缓冲区叫作顶点缓冲区（vertex buffer）。缓冲区的结构比纹 理更为简单：既非多维资源，也不支持 mipmap、过滤器以及多重采样等技术。当需要向 GPU 提供如顶 点这类数据元素所构成的数组时，我们便会使用缓冲区*

### ***D3D12_RESOURCE_DESC***

*我们先通过填写 D3D12_RESOURCE_DESC 结构体来描述缓冲区资源*

*接着再调用 ID3D12Device::CreateCommittedResource 方法去创建 ID3D12Resource 对象。*

*至于 D3D12_RESOURCE_DESC 结构体中所有成员的介绍，可参考 4.3.8 节。*

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
	D3D12_RESOURCE_MTSC_FLAG Misc Flags;
} D3D12_RESOURCE_DESC; 
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

2. *Width：以纹素为单位来表示的纹理宽度。对于缓冲区资源来说，此项是缓冲区占用的字节数。*

3. *Height：以纹素为单位来表示的纹理高度。*

4. *DepthOrArraySize：以纹素为单位来表示的纹理深度，或者（对于 1D 纹理和 2D 纹理来说） 是纹理数组的大小。注意，Direct3D 中并不存在 3D 纹理数组的概念*

5. *MipLevels：mipmap 层级的数量。我们会在讲纹理时介绍 mipmap。对于深度/模板缓冲区而言，只能有一个 mipmap 级别*

6. *Format：DXGI_FORMAT 枚举类型中的成员之一，用于指定纹素的格式。对于深度/模板缓冲区 来说，需要从x下述介绍的格式中选择*

   ```cpp
   1. DXGI_FORMAT_D32_FLOAT_S8X24_UINT：该格式共占用 64 位，取其中的 32 位指定一个浮点型深度缓冲区，另有 8 位（无符号整数）分配给模板缓冲区（stencil buffer），并将该元素映射到[0, 255]区间，剩下的 24 位仅用于填充对齐（padding）不作他用。
       
   2. DXGI_FORMAT_D32_FLOAT：指定一个 32 位浮点型深度缓冲区。
       
   3. DXGI_FORMAT_D24_UNORM_S8_UINT：指定一个无符号 24 位深度缓冲区，并将该元素映射到[0, 1]区间。另有 8 位（无符号整型）分配给模板缓冲区，将此元素映射到[0, 255]区间。
       
   4. DXGI_FORMAT_D16_UNORM：指定一个无符号 16 位深度缓冲区，把该元素映射到[0, 1]区间。
   ```

7. *SampleDesc：多重采样的质量级别以及对每个像素的采样次数. 先来回顾一下 4X MSAA 技术：为了存储每个子像素的颜色和深度/模板信息，所用后台 缓冲区和深度缓冲区的大小要 4 倍于屏幕的分辨率。因此，深度/模板缓冲区与渲染目标的多 重采样设置一定要相匹配*

8. *Layout：D3D12_TEXTURE_LAYOUT 枚举类型的成员之一，用于指定纹理的布局。我们暂 时还不用考虑这个问题，在此将它指定为 D3D12_TEXTURE_LAYOUT_UNKNOWN 即可。*

9. *Flags：与资源有关的杂项标志。对于一个深度/模板缓冲区资源来说，要将此项指定为 D3D12_RESOURCE_FLAG_ALLOW_DEPTH_STENCIL*

### ***CD3DX12_RESOURCE_DESC***

*Direct3D 12 提供了 一个 C++包装类 CD3DX12_RESOURCE_DESC，它派生自 D3D12_RESOURCE_DESC 结构体，并附有 多种便于使用的构造函数以及方法。特别是它提供的下列方法，一种简化缓冲区描述过程的 D3D12_ RESOURCE_DESC 的构造函数：*

```cpp
static inline CD3DX12_RESOURCE_DESC Buffer(UINT64 width, D3D12_RESOURCE_FLAGS flags = D3D12_RESOURCE_FLAG_NONE, UINT64 alignment = 0)
{
	return CD3DX12_RESOURCE_DESC( D3D12_RESOURCE_DIMENSION_BUFFER,
	alignment, width, 1, 1, 1,
	DXGI_FORMAT_UNKNOWN, 1, 0,
	D3D12_TEXTURE_LAYOUT_ROW_MAJOR, flags );
} 
```

*对于缓冲区而言，函数代码中的 width 即表示缓冲区中所占的字节数。例如，若缓冲区存储了 64 个 float 类型的数据，那么 width 的值即为 64*sizeof(float)。*

### ***D3D12_HEAP_TYPE_DEFAULT***

*对于静态几何体（static geometry，即每一帧都不会发生改变的几何体）而言，我们会将其顶点缓冲区置于默认堆（D3D12_HEAP_TYPE_DEFAULT）中来优化性能。*

*一般说来，游戏中的大多数几何体（如 树木、建筑物、地形和动画角色）都是如此处理。在这种情况下，顶点缓冲区初始化完毕后，只有 GPU 需要从其中读取数据来绘制几何体，所以使用默认堆是很明智的做法*

*然而，如果 CPU 不能向默认堆中 的顶点缓冲区写入数据，那么我们该如何初始化此顶点缓冲区呢？*

### ***D3D12_HEAP_TYPE_UPLOAD***

*因此，除了创建顶点缓冲区资源本身之外，我们还需用 D3D12_HEAP_TYPE_UPLOAD 这种堆类型 来创建一个处于中介位置的上传缓冲区（upload buffer）资源*

*由于我们需要利用作为中介的上传缓冲区来初始化默认缓冲区（即用堆类型 D3D12_HEAP_TYPE_ DEFAULT 创建的缓冲区）中的数据，因此，我们就在 d3dUtil.h/.cpp 文件中构建了下列工具函数，以避 免在每次使用默认缓冲区时再做这些重复的工作*

### ***D3D12_SUBRESOURCE_DATA***

*D3D12_SUBRESOURCE_DATA 结构体的定义为：*

```cpp
typedef struct D3D12_SUBRESOURCE_DATA
{
	const void *pData;
	LONG_PTR RowPitch;
	LONG_PTR SlicePitch;
} D3D12_SUBRESOURCE_DATA;
```

1. *pData：指向某个系统内存块的指针，其中有初始化缓冲区所用的数据。如果欲初始化的缓冲 区能够存储 n 个顶点数据，则该系统内存块必定可容纳至少 n 个顶点数据，以此来初始化整个 缓冲区。*
2. *RowPitch：对于缓冲区而言，此参数为欲复制数据的字节数。*
3. *SlicePitch：对于缓冲区而言，此参数亦为欲复制数据的字节数。*

### ***创建存有立方体 8 个顶点的默认缓冲区***

*下面的代码演示了此类将如何创建存有立方体 8 个顶点的默认缓冲区，并为其中的每个顶点都分别 赋予了不同的颜色*

```cpp
struct Vertex
{
	XMFLOAT3 Pos;
	XMFLOAT4 Color;
}; 
Vertex vertices[] =
{
	{ XMFLOAT3(-1.0f, -1.0f, -1.0f), XMFLOAT4(Colors::White) },
	{ XMFLOAT3(-1.0f, +1.0f, -1.0f), XMFLOAT4(Colors::Black) },
	{ XMFLOAT3(+1.0f, +1.0f, -1.0f), XMFLOAT4(Colors::Red) },
	{ XMFLOAT3(+1.0f, -1.0f, -1.0f), XMFLOAT4(Colors::Green) },
	{ XMFLOAT3(-1.0f, -1.0f, +1.0f), XMFLOAT4(Colors::Blue) },
	{ XMFLOAT3(-1.0f, +1.0f, +1.0f), XMFLOAT4(Colors::Yellow) },
	{ XMFLOAT3(+1.0f, +1.0f, +1.0f), XMFLOAT4(Colors::Cyan) },
	{ XMFLOAT3(+1.0f, -1.0f, +1.0f), XMFLOAT4(Colors::Magenta) }
};
const UINT64 vbByteSize = 8 * sizeof(Vertex);
ComPtr<ID3D12Resource> VertexBufferGPU = nullptr;
ComPtr<ID3D12Resource> VertexBufferUploader = nullptr;
VertexBufferGPU = d3dUtil::CreateDefaultBuffer(md3dDevice.Get(), mCommandList.Get(), vertices, vbByteSize, VertexBufferUploader);  
```

### ***D3D12_VERTEX_BUFFER_VIEW***

*为了将顶点缓冲区绑定到渲染流水线上，我们需要给这种资源创建一个顶点缓冲区视图（vertex buffer view）。*

*与 RTV（render target view，渲染目标视图）不同的是，我们无须为顶点缓冲区视图创建描 述符堆。*

*而且，顶点缓冲区视图是由 D3D12_VERTEX_BUFFER_VIEW结构体来表示。*

```cpp
typedef struct D3D12_VERTEX_BUFFER_VIEW
{
	D3D12_GPU_VIRTUAL_ADDRESS BufferLocation; 
	UINT SizeInBytes;
	UINT StrideInBytes;
} D3D12_VERTEX_BUFFER_VIEW;
```

1. *BufferLocation：待创建视图的顶点缓冲区资源虚拟地址。我们可以通过 ID3D12Resource:: GetGPUVirtualAddress 方法来获得此地址*
2. *SizeInBytes：待创建视图的顶点缓冲区大小（用字节表示）。*
3. *StrideInBytes：每个顶点元素所占用的字节数。*

*在顶点缓冲区及其对应视图创建完成后，便可以将它与渲染流水线上的一个输入槽（input slot）相绑定。 这样一来，我们就能向流水线中的输入装配器阶段传递顶点数据了。此操作可以通过下列方法来实现*

### ***ID3D12GraphicsCommandList::IASetVertexBuffers***

```cpp
void ID3D12GraphicsCommandList::IASetVertexBuffers
(
     UINT StartSlot, 
     UINT NumView,
     const D3D12_VERTEX_BUFFER_VIEW *pViews
);
```

1. *StartSlot：在绑定多个顶点缓冲区时，所用的起始输入槽（若仅有一个顶点缓冲区，则将其 绑定至此槽）。输入槽共有 16 个，索引为 0～15*
2. *NumViews：将要与输入槽绑定的顶点缓冲区数量（即视图数组 pViews 中视图的数量）。如果 起始输入槽 StartSlot 的索引值为 k，且我们要绑定 n 个顶点缓冲区，那么这些缓冲区将依次 与输入槽 $ I_k, I_{k+1},…,I_{k+n-1} $相绑定*
3. *pViews：指向顶点缓冲区视图数组中第一个元素的指针。*

*下面是该函数的一个调用示例。*

```cpp
D3D12_VERTEX_BUFFER_VIEW vbv;
vbv.BufferLocation = VertexBufferGPU->GetGPUVirtualAddress();
vbv.StrideInBytes = sizeof(Vertex);
vbv.SizeInBytes = 8 * sizeof(Vertex);
D3D12_VERTEX_BUFFER_VIEW vertexBuffers[1] = { vbv };
mCommandList->IASetVertexBuffers(0, 1, vertexBuffers); 
```

*由于 IASetVertexBuffers 方法会将顶点缓冲区数组中的元素设置到不同的输入槽上去，所以使 它看起来似乎有些复杂。但是，在我们的示例中实际只会使用一个输入槽。而在章末的一道习题中，我 们将体验到运用两个输入槽进行绘制的过程*

### ***使用多个顶点缓冲区***

*我们若不对顶点缓冲区进行任何修改，它就将一直被绑定于所在的输入槽上。所以，如果使用多个 顶点缓冲区，那么就可以按以下流程来构建代码。*

```cpp
ID3D12Resource* mVB1; // 存储 Vertex1 类型的顶点
ID3D12Resource* mVB2; // 存储 Vertex2 类型的顶点
D3D12_VERTEX_BUFFER_VIEW mVBView1; // mVB1 的视图
D3D12_VERTEX_BUFFER_VIEW mVBView2; // mVB2 的视图
/*……创建顶点缓冲区及其视图……*/
mCommandList->IASetVertexBuffers(0, 1, &mVBView1);
/* ……使用顶点缓冲区 1 来绘制物体…… */ 
mCommandList->IASetVertexBuffers(0, 1, &mVBView2);
/* ……使用顶点缓冲区 2 来绘制物体…… */ 
```

***ID3D12GraphicsCommandList::DrawInstanced***

*将顶点缓冲区设置到输入槽上并不会对其执行实际的绘制操作，而是仅为顶点数据送至渲染流 水线做好准备而已。这最后一步才是通过 ID3D12GraphicsCommandList::DrawInstanced 方法真正地绘制顶点*

```cpp
void ID3D12GraphicsCommandList::DrawInstanced (
	UINT VertexCountPerInstance,
	UINT InstanceCount,
	UINT StartVertexLocation,
	UINT StartInstanceLocation
); 
```

1. *VertexCountPerInstance：每个实例要绘制的顶点数量。*
2. *InstanceCount：用于实现一种被称作实例化（instancing）的高级技术。就目前来说，我们 只绘制一个实例，因而将此参数设置为 1。*
3. *StartVertexLocation：指定顶点缓冲区内第一个被绘制顶点的索引（该索引值以 0 为基准）。*
4. *StartInstanceLocation：用于实现一种被称作实例化的高级技术，暂时只需将其设置为 0。*

*VertexCountPerInstance和StartVertexLocation两个参数定义了顶点缓冲区中将要被绘 制的一组连续顶点*

### ***IASetPrimitiveTopology***

*既然 DrawInstanced 方法没有指定顶点被定义为何种图元，那么，它们应该被绘制为点、线 列表还是三角形列表呢？回顾 图元拓扑 可知，图元拓扑状态实由 ID3D12GraphicsCommandList:: IASetPrimitiveTopology 方法来设置。下面给出一个相关的调用示例：*

```cpp
cmdList->IASetPrimitiveTopology(D3D_PRIMITIVE_TOPOLOGY_TRIANGLELIST);
```

## ***索引和索引缓冲区***

*与顶点相似，为了使 GPU 可以访问索引数组，就需要将它们放置于 GPU 的缓冲区资源（ID3D12Resource）内。*

*我们称存储索引的缓冲区为索引缓冲区（index buffer）。*

*如果采用 d3dUtil::CreateDefaultBuffer 函数，他是通过 void*类型作为参数引入泛型数据，这就意味着我们 也可以用此函数来创建索引缓冲区（或任意类型的默认缓冲区）。*

### ***D3D12_INDEX_BUFFER_VIEW***

*为了使索引缓冲区与渲染流水线绑定，我们需要给索引缓冲区资源创建一个索引缓冲区视图（index buffer view）。如同顶点缓冲区视图一样，我们也无须为索引缓冲区视图创建描述符堆。但索引缓冲区视 图要由结构体 D3D12_INDEX_BUFFER_VIEW 来表示*

```cpp
typedef struct D3D12_INDEX_BUFFER_VIEW
{
	D3D12_GPU_VIRTUAL_ADDRESS BufferLocation;
	UINT SizeInBytes;
	DXGI_FORMAT Format;
} D3D12_INDEX_BUFFER_VIEW; 
```

1. *BufferLocation：待创建视图的索引缓冲区资源虚拟地址。我们可以通过调用ID3D12Resource:: GetGPUVirtualAddress 方法来获取此地址。*
2. *SizeInBytes：待创建视图的索引缓冲区大小（以字节表示）。*
3. *Format：索引的格式必须为表示 16 位索引的 DXGI_FORMAT_R16_UINT 类型，或表示 32 位 索引的 DXGI_FORMAT_R32_UINT 类型。16 位的索引可以减少内存和带宽的占用，但如果索引 值范围超过了 16 位数据的表达范围，则也只能采用 32 位索引了。*

### ***ID3D12GraphicsCommandList::IASetIndexBuffer***

*与顶点缓冲区相似（也包括其他的 Direct3D 资源在内），在使用之前，我们需要先将它们绑定到渲染流水线上。通过 ID3D12GraphicsCommandList::IASetIndexBuffer 方法即可将索引缓冲区绑 定到输入装配器阶段*

### ***创建一个索引缓冲区来定义构成立方体的三角形***

```cpp
std::uint16_t indices[] = {
	// 立方体前表面
	0, 1, 2,
	0, 2, 3,
	// 立方体后表面
	4, 6, 5,
	4, 7, 6,
	// 立方体左表面
	4, 5, 1,
	4, 1, 0,
	// 立方体右表面
	3, 2, 6,
	3, 6, 7,
	// 立方体上表面
	1, 5, 6,
	1, 6, 2,
	// 立方体下表面
	4, 0, 3,
	4, 3, 7
}; 
const UINT ibByteSize = 36 * sizeof(std::uint16_t);
ComPtr<ID3D12Resource> IndexBufferGPU = nullptr;
ComPtr<ID3D12Resource> IndexBufferUploader = nullptr;
IndexBufferGPU = d3dUtil::CreateDefaultBuffer(md3dDevice.Get(), mCommandList.Get(), indices, ibByteSize, IndexBufferUploader);
D3D12_INDEX_BUFFER_VIEW ibv;
ibv.BufferLocation = IndexBufferGPU->GetGPUVirtualAddress();
ibv.Format = DXGI_FORMAT_R16_UINT;
ibv.SizeInBytes = ibByteSize;
mCommandList->IASetIndexBuffer(&ibv);
```

### ***ID3D12GraphicsCommandList:: DrawIndexedInstanced***

*最后需要注意的是，在使用索引的时候，我们一定要用 ID3D12GraphicsCommandList:: DrawIndexedInstanced 方法代替 DrawInstanced 方法进行绘制。*

```cpp
void ID3D12GraphicsCommandList::DrawIndexedInstanced(
	UINT IndexCountPerInstance,
	UINT InstanceCount,
	UINT StartIndexLocation,
	INT BaseVertexLocation,
	UINT StartInstanceLocation
); 
```

1. *IndexCountPerInstance：每个实例将要绘制的索引数量。*
2. *InstanceCount：用于实现一种被称作实例化的高级技术。就目前而言，我们只绘制一个实例，因而将此值设置为 1。*
3. *StartIndexLocation：指向索引缓冲区中的某个元素，将其标记为欲读取的起始索引。*
4. *BaseVertexLocation：在本次绘制调用读取顶点之前，要为每个索引都加上此整数值。*
5. *StartInstanceLocation：用于实现一种被称为实例化的高级技术，暂将其设置为 0。*

*为了理解这些参数，让我们来思考这样一个情景:*

* *假设有 3 个欲绘制的物体，一个球体、一个立方 体以及一个圆柱体。*

* *首先，每个物体都有自己的顶点缓冲区以及索引缓冲区。而每个局部索引缓冲区中 的索引，又都引用的是各自的局部顶点缓冲区。*

* *现在，我们把这 3 个物体的顶点和索引分别连接为全局 顶点缓冲区和全局索引缓冲区，如图 6.3 所示。（在顶点缓冲区和索引缓冲区合并的过程中，调用 API 时 可能会产生一些开销，但这基本上不会成为程序的瓶颈。出于性能的原因，当应用中有一些可以轻易合 并的零散顶点缓冲区和索引缓冲区时，便值得照这样试一试）*

* *待合并完成后，索引缓冲区里的元素就不 能正确地引用对应的顶点数据了，这是因为它们存储的索引值是相对于物体各自的局部顶点缓冲区而言， 而非全局的顶点缓冲区。*

* *所以，还需要对索引值进行重新计算，使之可以正确地引用全局顶点缓冲区中 的顶点数据。假设原始的立方体索引是根据下述立方体顶点编号来计算的：*

  ```cpp
  0, 1, ..., numBoxVertices-1 
  ```

* *但是在顶点缓冲区与索引缓冲区一一合并之后，该立方体的索引编号将依次变为：*

  ```cpp
  firstBoxVertexPos,
  firstBoxVertexPos+1,
  ...,
  firstBoxVertexPos+numBoxVertices-1
  ```

* *因此，为了更新索引，我们需要为每个立方体的原索引加上 firstBoxVertexPos（缓冲区合并后 立方体第一个顶点的索引值）。类似地，我们也需要给每个圆柱体的原索引加上 firstCylVertexPos （缓冲区合并后圆柱体第一个顶点的索引值）。注意，球体的索引是不需要改变的（因为球体第一个顶点 的位置始终为 0，在合并后的全局索引缓冲区中也未曾改变）。我们将每个物体的第一个顶点相对于 全局顶点缓冲区的位置叫作它的基准顶点地址（base vertex location）。*

*通常来讲，一个物体的新索引 是通过原始索引加上它的基准顶点地址来获取的。事实上，我们不必亲自计算新的索引值：通过向 DrawIndexedInstanced 函数的第 4 个参数传递基准顶点地址，即可让 Direct3D 去执行相关计算工作*

*接下来，我们再通过下列 3 次绘制调用来依次绘制球体、立方体和圆柱体：*

```cpp
mCmdList->DrawIndexedInstanced(numSphereIndices, 1, 0, 0, 0);
mCmdList->DrawIndexedInstanced(numBoxIndices, 1, firstBoxIndex, firstBoxVertexPos, 0);
mCmdList->DrawIndexedInstanced(numCylIndices, 1, firstCylIndex, firstCylVertexPos, 0); 
```

## ***顶点着色器示例***

*以下代码实现的是一个简单的顶点着色器*

```cpp
cbuffer cbPerObject : register(b0)
{
	float4x4 gWorldViewProj;
};

void VS(float3 iPosL : POSITION,
 float4 iColor : COLOR,
 out float4 oPosH : SV_POSITION,
 out float4 oColor : COLOR)
{
	// 把顶点变换到齐次裁剪空间
	oPosH = mul(float4(iPosL, 1.0f), gWorldViewProj);

	// 直接将顶点的颜色信息传至像素着色器
	oColor = iColor;
}
```

*在 Direct3D 中，编写着色器的语言为高级着色语言（High Level Shading Language，HLSL），其语 法与 C++十分相似，这使得它较易于学习*

*一般情况下，着色器通常在以.hlsl 为扩展名的文本文件中编写。*

*详细语法介绍，请参考https://cutecocoa.github.io/unreal-hlsl/*

*顶点着色器就是上例中名为 VS 的函数，值得注意的是，我们可以给顶点着色器起任意合法的函数 名。*

*上述顶点着色器共有 4 个参数，前两个为输入参数，后两个为输出参数（通过关键字 out 来表示）。*

*HLSL 没有引用（reference）和指针（pointer）的概念，所以需要借助结构体或多个输出参数才能够从函 数中返回多个数值。*

*而且，在 HLSL 中，所有的函数都是内联（inline）函数。*

*前两个输入参数分别对应于绘制立方体所自定义顶点结构体中的两个数据成员，也构成了顶点着色 器的输入签名（input signature）。*

*参数语义“ :POSITION”和“ :COLOR”用于将顶点结构体中的元素映射到顶点着色器的相应输入参数*

![顶点着色器参数语义](https://raw.githubusercontent.com/CuteCocoa/MyImage/main/顶点着色器参数语义.png)

*输出参数也附有各自的语义（“:SV_POSITION”和“:COLOR”），并以此作为纽带，将顶点着色器 的输出参数映射到下个处理阶段（几何着色器或像素着色器）中所对应的输入参数*

*注意，SV_POSITION 语义比较特殊（SV 代表系统值，即 system value），它所修饰的顶点着色器输出元素存有齐次裁剪空间中的顶点位置信息*

*因此，我们必须为输出位置信息的参数附上 SV_POSITION 语义，使 GPU 可以在进 行例如裁剪、深度测试和光栅化等处理之时，借此实现其他属性所无法介入的有关运算*

*值得注意的是对于任何不具有系统值的输出参数而言，我们都可以根据需求以合法的语义名修饰它。*

*上述顶点着色器函数的第一行代码是通过将顶点与 4 x 4 矩阵 gWorldViewProj 相乘，使其坐标由 局部空间变换到齐次裁剪空间*

```cpp
// 将顶点坐标变换到齐次裁剪空间
oPosH = mul(float4(iPosL, 1.0f), gWorldViewProj); 
```

1. *借助构造语法 float4(iPosL, 1.0f)即可构建一个等价于 float4(iPosL.x, iPosL.y, iPosL.z, 1.0f)的 4D 向量*

2. *我们知道，顶点的位置是一个点而非向量，所以将向量的第 4 个分量设 置为 1（w = 1）。并以 float2 与 float3 类型分别表示 2D 和 3D 向量。*

3. *矩阵变量 gWorldViewProj 存于常量缓冲区（constant buffer）内*

4. *内置函数（built-in function, 也译作内建函数、内部函数等）mul 则用于计算向量与矩阵之间的乘法*

*顺便提一下，mul 函数可以根据 不同规模的矩阵乘法而重载。例如，我们可以用 mul 函数进行两个 4 × 4 矩阵的乘法、两个3 × 3矩阵的乘法或者一个1 × 3向量与 3 x 3矩阵的乘法等。*

*着色器函数的最后一行代码把输入的颜色直接复制给输出参数，继而将该颜色传递到渲染流水线的下个阶段:*

```cpp
oColor = iColor;
```

*我们可以把函数的返回类型和输入签名替换为结构体（从而取代过长的参数列表），即将以上顶点着 色器改写为另一种等价实现：*

```cpp
cbuffer cbPerObject : register(b0)
{
	float4x4 gWorldViewProj;
};
struct VertexIn
{
	float3 PosL : POSITION;
	float4 Color : COLOR;
};
struct VertexOut
{
	float4 PosH : SV_POSITION;
	float4 Color : COLOR;
};
VertexOut VS(VertexIn vin)
{
	VertexOut vout;

	// 将顶点变换到齐次裁剪空间
	vout.PosH = mul(float4(vin.PosL, 1.0f), gWorldViewProj);

	// 直接将顶点颜色传至像素着色器
	vout.Color = vin.Color;

	return vout;
} 
```

### ***连接输入布局描述符与输入签名***

*根据图 6.4 来看，输送到渲染流水线的顶点属性与输入布局描述的定义相关联。如果我们传入的顶 点数据与顶点着色器所期望的输入不相符，便会导致错误*

*例如，下列顶点着色器的输入签名与顶点数 据就是不匹配的：*

```cpp
//--------------
// C++应用程序代码
//--------------
struct Vertex
{
	XMFLOAT3 Pos;
	XMFLOAT4 Color;
};
D3D12_INPUT_ELEMENT_DESC desc[] =
{
	{"POSITION", 0, DXGI_FORMAT_R32G32B32_FLOAT, 0, 0,
	D3D12_INPUT_CLASSIFICATION_PER_VERTEX_DATA, 0},
	{"COLOR", 0, DXGI_FORMAT_R32G32B32A32_FLOAT, 0, 12,
	D3D12_INPUT_CLASSIFICATION_PER_VERTEX_DATA, 0}
};
//--------------
// 顶点着色器
//--------------
struct VertexIn
{
	float3 PosL : POSITION;
	float4 Color : COLOR;
	float3 Normal : NORMAL;
};
struct VertexOut
{
	float4 PosH : SV_POSITION;
	float4 Color : COLOR;
};
VertexOut VS(VertexIn vin) { ... } 
```

*在创建 ID3D12PipelineState 对象的时候，必须指定输入 布局描述和顶点着色器，而 Direct3D 则会验证两者是否匹配*

*事实上，顶点数据与输入签名不需要完全匹配，前提是我们一定要向顶点着色器提供其输入签 名所定义的顶点数据。这就是说，顶点数据中也可以附带一些顶点着色器根本用不到的额外数据。 下面的代码就描述了这样一种匹配的情况：*

```cpp
//--------------
// C++应用程序代码
//--------------
struct Vertex
{
	XMFLOAT3 Pos;
	XMFLOAT4 Color;
	XMFLOAT3 Normal;
};
D3D12_INPUT_ELEMENT_DESC desc[] =
{
	{"POSITION", 0, DXGI_FORMAT_R32G32B32_FLOAT, 0, 0, D3D12_INPUT_CLASSIFICATION_PER_VERTEX_DATA, 0}, 
	{"COLOR", 0, DXGI_FORMAT_R32G32B32A32_FLOAT, 0, 12, D3D12_INPUT_CLASSIFICATION_PER_VERTEX_DATA, 0},
	{"NORMAL", 0, DXGI_FORMAT_R32G32B32_FLOAT, 0, 28, D3D12_INPUT_CLASSIFICATION_PER_VERTEX_DATA, 0 }
};
//--------------
// 顶点着色器
//--------------
struct VertexIn
{
	float3 PosL : POSITION;
	float4 Color : COLOR;
};
struct VertexOut
{
	float4 PosH : SV_POSITION;
	float4 Color : COLOR;
};
VertexOut VS(VertexIn vin) { ... } 
```

*现在，让我们再来思考这样一种情况：当顶点结构体和输入签名有着匹配的顶点元素，唯独两者的 颜色属性类型却不相同，此时会发生什么呢？*

```cpp
//--------------
// C++应用程序代码
//--------------
struct Vertex
{
	XMFLOAT3 Pos;
	XMFLOAT4 Color;
};
D3D12_INPUT_ELEMENT_DESC desc[] =
{
	{"POSITION", 0, DXGI_FORMAT_R32G32B32_FLOAT, 0, 0, D3D12_INPUT_CLASSIFICATION_PER_VERTEX_DATA, 0},
	{"COLOR", 0, DXGI_FORMAT_R32G32B32A32_FLOAT, 0, 12, D3D12_INPUT_CLASSIFICATION_PER_VERTEX_DATA, 0}
};
//--------------
// 顶点着色器
//--------------
struct VertexIn
{
	float3 PosL : POSITION;
	int4 Color : COLOR;
};
struct VertexOut
{ 
	float4 PosH : SV_POSITION;
	float4 Color : COLOR;
};
VertexOut VS(VertexIn vin) { ... } 
```

*这其实也是合法的，因为 Direct3D 允许用户对输入寄存器（input register，全称为顶点着色器输入 寄存器）中数据的类型重新加以解释。然而，VC++调试输出窗口还是会给出下面的警告*

```cpp
D3D12 WARNING: ID3D12 Device::CreateInputLayout: The provided input
signature expects to read an element with SemanticName/Index: 'COLOR'/0 and
component(s) of the type 'int32'. However, the matching entry in the Input Layout
declaration, element[1], specifies mismatched format: 'R32G32B32A32_FLOAT'.
This is not an error, since behavior is well defined: The element format
determines what data conversion algorithm gets applied before it shows up in
a shader register. Independently, the shader input signature defines how the
shader will interpret the data that has been placed in its input registers, with
no change in the bits stored. It is valid for the application to reinterpret
data as a different type once it is in the vertex shader, so this warning is
issued just in case reinterpretation was not intended by the author.
（D3D12 警告：ID3D12Device::CreateInputLayout: 根据代码中提供的输入签名推断，其中
的 SemanticName/Index: 'COLOR'/0 项希望读取'int32'类型的数据分量。但是，此类型却与输
入布局声明中的对应匹配项 element[1]所指定的格式'R32G32B32A32_FLOAT'不匹配。这并不是一个
错误，因为 Direct3D 对该行为有着明确的定义：输入布局中的元素格式确定的是，在数据未进入着色器
寄存器之前，应运用何种数据转换算法来确定各元素的具体格式。而着色器输入签名则定义的是，在不
修改输入寄存器中所存数据的情况下，顶点着色器将如何来解释这些数据的类型。所以，在应用程序里
将顶点着色器中的数据重新解释为与之不同的类型，亦是合法的。因此，该警告只用于提醒在无意间对
数据类型作出不同解释的程序员①。）
```

## ***像素着色器示例***

*为了计算出三角形中每个像素的属性，我们会在光栅化处理期间对顶点着色器（或几何着色器）输出的顶点属性进行插值。随后，再将这些插值数据传至像素着色器中作为它的输入*

*现假设我们的程序未使用几何着色器，图 6.5 展示的即为当前顶点数 据所流经的路径*

*像素着色器与顶点着色器有些相似：前者是针对每一个像素片段（pixel fragment）而运行的函数， 后者是针对每一个顶点而运行的函数（即在每次执行时处理单个像素（顶点））。*

*只要为像素着色器指 定了输入数据，它就会为像素片段计算出一个对应的颜色*

*值得我们注意的是，这些输入像素着色器 的像素片段有可能最终不会传入或留存在后台缓冲区中*

*例如，像素片段可能会在像素着色器中被裁 剪掉（HLSL 中内置了一个裁剪函数 clip，可以使指定的像素片段在后续的处理流程中被忽略掉）、 被另一个具有较小深度值的像素片段所遮挡或者在类似于模板缓冲区测试的后续渲染流水线测试中被丢弃。*

*因此，在确定后台缓冲区某一像素的过程中，可能会存在多个候选的像素片段。这就是“像素 片段”和“像素”意义的差别，尽管有时这两个术语可以互用，但是在一些语境下它们的意义也将变得更加分明*

![像素着色器图](C:\Users\peng.xu\Desktop\像素着色器图6.5.png)

*下面是一段简单的像素着色器代码，它与顶点着色器相呼应。考虑到代码的完整性， 此处把顶点着色器部分也一并再次给出*

```cpp
cbuffer cbPerObject : register(b0)
{
	float4x4 gWorldViewProj;
};

void VS(float3 iPos : POSITION, 
 float4 iColor : COLOR,
 out float4 oPosH : SV_POSITION,
 out float4 oColor : COLOR)
{
	// 将顶点变换到齐次裁剪空间
	oPosH = mul(float4(iPos, 1.0f), gWorldViewProj);

	// 直接把顶点颜色传递到像素着色器
	oColor = iColor;
}
float4 PS(float4 posH : SV_POSITION, float4 color : COLOR) : SV_Target
{
	return color;
} 
```

*在这个示例中，像素着色器只简单地返回了插值颜色数据。*

*可以发现，像素着色器的输入与顶点着 色器的输出可以准确匹配，这也是必须满足的一点*

*像素着色器返回一个 4D 颜色值，而位于此函数参 数列表后的 SV_TARGET 语义则表示该返回值的类型应当与渲染目标格式（render target format）相匹配 （该输出值会被存于渲染目标之中）。*

*我们可以利用输入/输出结构体重写上述顶点着色器和像素着色器的等价实现。这与之前的表示方法 有所不同，我们要将语义附加给输入/输出结构体中的成员，并通过一条用于输出结构体的返回语句代替 之前的多个输出参数。*

```cpp
cbuffer cbPerObject : register(b0)
{
	float4x4 gWorldViewProj;
};
struct VertexIn 
{
	float3 Pos : POSITION;
	float4 Color : COLOR;
};
struct VertexOut
{
	float4 PosH : SV_POSITION;
	float4 Color : COLOR;
};
VertexOut VS(VertexIn vin)
{
	VertexOut vout;

	// 把顶点变换到齐次裁剪空间
	vout.PosH = mul(float4(vin.Pos, 1.0f), gWorldViewProj);

	// 直接将顶点颜色传至像素着色器
	vout.Color = vin.Color;

	return vout;
}
float4 PS(VertexOut pin) : SV_Target
{
	return pin.Color;
} 
```

## ***常量缓冲区***

### ***创建常量缓冲区***

*常量缓冲区（constant buffer）也是一种 GPU 资源（ID3D12Resource），其数据内容可供着色器程序所引用。就像我们将会学到的纹理等其他类型的缓冲区资源一样，它们都可以被着色器程序所引用*

*顶点着色器中有如下代码*

```hlsl
cbuffer cbPerObject : register(b0)
{
	float4x4 gWorldViewProj;
}; 
```

1. *在这段代码中，cbuffer 对象（常量缓冲区）的名称为 cbPerObject，其中存储的是一个 4 x 4 矩阵 gWorldViewProj，表示把一个点从局部空间变换到齐次裁剪空间所用到的由世界、视图和投影 3 种变换组合而成的矩阵。*
2. *在 HLSL 中，可将一个 4 × 4  矩阵声明为内置的 float4x4 类型。相应地，要声 明 3 × 4 矩阵或 2 × 4 矩阵，即可分别使用 float3x4 和 float2x4 这两种类型*

*与顶点缓冲区和索引缓冲区不同的是，**常量缓冲区通常由 CPU 每帧更新一次。***

*举个例子，如果摄像机每帧都在不停地移动，那么常量缓冲区也需要在每一帧都随之以新的视图矩阵而更新。所以，我们会把常量缓冲区创建到一个上传堆而非默认堆中，这样做能使我们从 CPU 端更新常量*

***常量缓冲区对硬件也有特别的要求，即常量缓冲区的大小必为硬件最小分配空间（256B）的整数倍。***

*我们经常需要用到多个相同类型的常量缓冲区。例如，假设常量缓冲区 cbPerObject 内存储的是 随不同物体而异的常量数据，因此，如果我们要绘制 n 个物体，则需要 n 个该类型的常量缓冲区*

*下列 代码展示了我们是如何创建一个缓冲区资源，并利用它来存储 NumElements 个常量缓冲区。*

```cpp
struct ObjectConstants
{
	DirectX::XMFLOAT4X4 WorldViewProj = MathHelper::Identity4x4();
};
UINT mElementByteSize = d3dUtil::CalcConstantBufferByteSize(sizeof(ObjectConstants));
ComPtr<ID3D12Resource> mUploadCBuffer;
device->CreateCommittedResource
(
	&CD3DX12_HEAP_PROPERTIES(D3D12_HEAP_TYPE_UPLOAD),
	D3D12_HEAP_FLAG_NONE,
	&CD3DX12_RESOURCE_DESC::Buffer(mElementByteSize * NumElements),
	D3D12_RESOURCE_STATE_GENERIC_READ,
	nullptr,
	IID_PPV_ARGS(&mUploadCBuffer)
); 
```

*我们可以认为 mUploadCBuffer 中存储了一个 ObjectConstants 类型的常量缓冲区数组（同时 按 256 字节的整数倍来为之填充数据）。待到绘制物体的时候，只要将常量缓冲区视图（Constant Buffer View，CBV）绑定到存有物体相应常量数据的缓冲区子区域即可。由于 mUploadCBuffer 缓冲区存储 的是一个常量缓冲区数组，因此，我们把它称之为常量缓冲区。*

#### ***CalcConstantBufferByteSize***

*工具函数 d3dUtil::CalcConstantBufferByteSize 会做适当的运算，使缓冲区的大小凑整为 硬件最小分配空间（256B）的整数倍。*

```cpp
UINT d3dUtil::CalcConstantBufferByteSize(UINT byteSize)
{
	// 常量缓冲区的大小必须是硬件最小分配空间（通常是 256B）的整数倍
	// 为此，要将其凑整为满足需求的最小的 256 的整数倍。我们现在通过为输入值 byteSize 加上 255，
	// 再屏蔽求和结果的低 2 字节（即计算结果中小于 256 的数据部分）来实现这一点
	// 例如：假设 byteSize = 300
	// (300 + 255) & ~255
	// 555 & ~255
	// 0x022B & ~0x00ff
	// 0x022B & 0xff00
	// 0x0200
	// 512
	return (byteSize + 255) & ~255;
} 
```

*随 Direct3D 12 一同推出的是着色器模型（shader model，SM）5.1。其中新引进了一条可用于定义 常量缓冲区的 HLSL 语法，它的使用方法如*

```cpp
struct ObjectConstants
{
	float4x4 gWorldViewProj;
	uint matIndex;
};
ConstantBuffer<ObjectConstants> gObjConstants : register(b0);  
```

*在此段代码中，常量缓冲区的数据元素被定义在一个单独的结构体中，随后再用此结构体来创建一 个常量缓冲区。这样一来，我们就可以利用下列获取数据成员的语法，在着色器里访问常量缓冲区中的 各个字段：*

```cpp
uint index = gObjConstants.matIndex; 
```

### ***更新常量缓冲区***

*由于常量缓冲区是用 D3D12_HEAP_TYPE_UPLOAD 这种堆类型来创建的，所以我们就能通过 CPU 为常量缓冲区资源更新数据。为此，我们首先要获得指向欲更新资源数据的指针，可用 Map 方法来做到这一点：*

```cpp
ComPtr<ID3D12Resource> mUploadBuffer;
BYTE* mMappedData = nullptr;
mUploadBuffer->Map(0, nullptr, reinterpret_cast<void**>(&mMappedData)); 
```

1. *第一个参数是子资源（subresource）的索引①，指定了欲映射的子资源。对于缓冲区来说，它自身就 是唯一的子资源，所以我们将此参数设置为 0*

2. *第二个参数是一个可选项，是个指向 D3D12_RANGE 结构体的指针，此结构体描述了内存的映射范围，若将该参数指定为空指针，则对整个资源进行映射*

3. *第三个参数则借助双重指针，返回待映射资源数据的目标内存块。我们利用 memcpy 函数将数据从系统内存（system memory，也就是 CPU 端控制的内存）复制到常量缓冲区*

   ```cpp
   memcpy(mMappedData, &data, dataSizeInBytes);
   
   ```

*当常量缓冲区更新完成后，我们应在释放映射内存之前对其进行 Unmap（取消映射）操作*

```cpp
if(mUploadBuffer != nullptr) mUploadBuffer->Unmap(0, nullptr);
mMappedData = nullptr; 
```

*Unmap 的第一个参数是子资源索引，指定了将被取消映射的子资源。若取消映射的是缓冲区，则将其置为 0。第二个参数是个可选项，是一个指向 D3D12_RANGE 结构体的指针，用于描述取消映射的内存范围，若将它指定为空指针，则取消整个资源的映射*

### ***上传缓冲区辅助函数***

*将上传缓冲区的相关操作简单地封装一下，使用起来会更加方便。*

*我们在 UploadBuffer.h 文件中定义了下面这个类，令上传缓冲区的相关处理工作更加轻松。*

*它替我们实现了上传缓冲区资源的构造与析构函数、处理资源的映射和取消映射操作，还提供了 CopyData 方法来更新缓冲区内的特定元素。*

*在需要通过 CPU 修改上传缓冲区中数据的时候（例如，当观察矩阵有了变化），便可以使用 CopyData*

*注 意，此类可用于各种类型的上传缓冲区，而并非只针对常量缓冲区。当用此类管理常量缓冲区时，我们就需要通过构造函数参数 isConstantBuffer 来对此加以描述。另外，如果此类中存储的是常量缓冲 区，那么其中的构造函数将自动填充内存，使每个常量缓冲区的大小都成为 256B 的整数倍。*

```cpp
template<typename T>
class UploadBuffer
{
public:
	UploadBuffer(ID3D12Device* device, UINT elementCount, bool isConstantBuffer) :
	mIsConstantBuffer(isConstantBuffer)
	{
		mElementByteSize = sizeof(T);
		 // 常量缓冲区的大小为 256B 的整数倍。这是因为硬件只能按 m*256B 的偏移量和 n*256B 的数据
		 // 长度这两种规格来查看常量数据
		 // typedef struct D3D12_CONSTANT_BUFFER_VIEW_DESC {
		 // D3D12_GPU_VIRTUAL_ADDRESS BufferLocation; // 256 的整数倍
		 // UINT SizeInBytes; // 256 的整数倍
		 // } D3D12_CONSTANT_BUFFER_VIEW_DESC;
		 if(isConstantBuffer) mElementByteSize = d3dUtil::CalcConstantBufferByteSize(sizeof(T));
		ThrowIfFailed(
			device->CreateCommittedResource(
				&CD3DX12_HEAP_PROPERTIES(D3D12_HEAP_TYPE_UPLOAD), D3D12_HEAP_FLAG_NONE,
				&CD3DX12_RESOURCE_DESC::Buffer(mElementByteSize*elementCount),
				D3D12_RESOURCE_STATE_GENERIC_READ,
				nullptr,
				IID_PPV_ARGS(&mUploadBuffer)));
				ThrowIfFailed(mUploadBuffer->Map(0, nullptr, reinterpret_cast<void**>(&mMappedData))
			);
		 // 只要还会修改当前的资源，我们就无须取消映射
		 // 但是，在资源被 GPU 使用期间，我们千万不可向该资源进行写操作（所以必须借助于同步技术）
	 } 
	    
	 UploadBuffer(const UploadBuffer& rhs) = delete;
	 UploadBuffer& operator=(const UploadBuffer& rhs) = delete;
	 ~UploadBuffer()
	 {
		 if(mUploadBuffer != nullptr) mUploadBuffer->Unmap(0, nullptr);
		 mMappedData = nullptr;
	 }
	 ID3D12Resource* Resource()const
	 {
		return mUploadBuffer.Get();
	 }
	 void CopyData(int elementIndex, const T& data)
	 { 
	      memcpy(&mMappedData[elementIndex*mElementByteSize], &data, sizeof(T));
	 }
private:
	Microsoft::WRL::ComPtr<ID3D12Resource> mUploadBuffer;
	BYTE* mMappedData = nullptr;
	UINT mElementByteSize = 0;
	bool mIsConstantBuffer = false;
}; 
```

*一般来讲，物体的世界矩阵将随其移动 / 旋转 / 缩放而改变，观察矩阵随虚拟摄像机的移动/旋转而改 变，投影矩阵随窗口大小的调整而改变。*

*如果用户可以通过鼠标来旋转和移动摄像机， 变换观察角度。我们就要在每一帧都要用 Update 函数，以新的观察矩阵来更新“世界—观察—投影” 3 种矩阵组合而成的复合矩阵*

```cpp
void BoxApp::OnMouseMove(WPARAM btnState, int x, int y)
{
	if((btnState & MK_LBUTTON) != 0)
	{
		// 根据鼠标的移动距离计算旋转角度，令每个像素按此角度的 1/4 进行旋转
		float dx = XMConvertToRadians(0.25f*static_cast<float>(x - mLastMousePos.x));
		float dy = XMConvertToRadians(0.25f*static_cast<float>(y - mLastMousePos.y));
		// 根据鼠标的输入来更新摄像机绕立方体旋转的角度
		mTheta += dx;
		mPhi += dy;
		// 限制角度 mPhi 的范围
		mPhi = MathHelper::Clamp(mPhi, 0.1f, MathHelper::Pi - 0.1f);
	}
	else if((btnState & MK_RBUTTON) != 0)
	{
		// 使场景中的每个像素按鼠标移动距离的 0.005 倍进行缩放
		float dx = 0.005f*static_cast<float>(x - mLastMousePos.x);
		float dy = 0.005f*static_cast<float>(y - mLastMousePos.y);
		// 根据鼠标的输入更新摄像机的可视范围半径
		mRadius += dx - dy;
		// 限制可视半径的范围
		mRadius = MathHelper::Clamp(mRadius, 3.0f, 15.0f);
	}
	mLastMousePos.x = x;
	mLastMousePos.y = y;
} 

void BoxApp::Update(const GameTimer& gt)
{
	// 由球坐标（也有译作球面坐标）转换为笛卡儿坐标
	float x = mRadius*sinf(mPhi)*cosf(mTheta);
	float z = mRadius*sinf(mPhi)*sinf(mTheta);
	float y = mRadius*cosf(mPhi);
	// 构建观察矩阵
	XMVECTOR pos = XMVectorSet(x, y, z, 1.0f);
	XMVECTOR target = XMVectorZero();
	XMVECTOR up = XMVectorSet(0.0f, 1.0f, 0.0f, 0.0f);
	XMMATRIX view = XMMatrixLookAtLH(pos, target, up);
	XMStoreFloat4x4(&mView, view);
	XMMATRIX world = XMLoadFloat4x4(&mWorld);
	XMMATRIX proj = XMLoadFloat4x4(&mProj);
	XMMATRIX worldViewProj = world*view*proj;
	// 用最新的 worldViewProj 矩阵来更新常量缓冲区
	ObjectConstants objConstants;
	XMStoreFloat4x4(&objConstants.WorldViewProj,
	XMMatrixTranspose(worldViewProj));
	mObjectCB->CopyData(0, objConstants);
} 
```

### ***常量缓冲区描述符***

*我们首次通过描述符对象将资源绑定到渲染流水线上。*

*到目前为止，已经依次介绍 了渲染目标、深度 / 模板缓冲区、顶点缓冲区以及索引缓冲区这几种资源描述符（或称视图）的使用方法， 现在还需利用描述符将常量缓冲区绑定至渲染流水线上。而且常量缓冲区描述符都要存放在以 D3D12_DESCRIPTOR_HEAP_TYPE_CBV_SRV_UAV 类型所建的描述符堆里。这种堆内可以混合存储常 量缓冲区描述符、着色器资源描述符和无序访问（unordered access）描述符*

*为了存放这些新类型的描 述符，我们需要为之创建以下类型的新式描述符堆：*

```cpp
D3D12_DESCRIPTOR_HEAP_DESC cbvHeapDesc;
cbvHeapDesc.NumDescriptors = 1;
cbvHeapDesc.Type = D3D12_DESCRIPTOR_HEAP_TYPE_CBV_SRV_UAV;
cbvHeapDesc.Flags = D3D12_DESCRIPTOR_HEAP_FLAG_SHADER_VISIBLE;
cbvHeapDesc.NodeMask = 0;
ComPtr<ID3D12DescriptorHeap> mCbvHeap
md3dDevice->CreateDescriptorHeap(&cbvHeapDesc, IID_PPV_ARGS(&mCbvHeap)); 
```



### ***根签名和描述符表***

## ***编译着色器***

*在 Direct3D 中，着色器程序必须先被编译为一种可移植的字节码。接下来，图形驱动程序将获取这 些字节码，并将其重新编译为针对当前系统 GPU 所优化的本地指令[ATI1]。我们可以在运行期间用下列 函数对着色器进行编译。*

```cpp
HRESULT D3DCompileFromFile(
	LPCWSTR pFileName,
	const D3D_SHADER_MACRO *pDefines,
	ID3DInclude *pInclude,
	LPCSTR pEntrypoint,
	LPCSTR pTarget,
	UINT Flags1,
	UINT Flags2,
	ID3DBlob **ppCode,
	ID3DBlob **ppErrorMsgs
 ); 
```

1. *pFileName：我们希望编译的以.hlsl 作为扩展名的 HLSL 源代码文件。*

2. *pDefines：在本书中，我们并不使用这个高级选项，因此总是将它指定为空指针。关于此参数的详细信息可参见 SDK 文档。*

3. *pInclude：在本书中，我们并不使用这个高级选项，因而总是将它指定为空指针。关于此参 数的详细信息可详见 SDK 文档。*

4. *pEntrypoint：着色器的入口点函数名。一个.hlsl 文件可能存有多个着色器程序（例如，一个 顶点着色器和一个像素着色器），所以我们需要为待编译的着色器指定入口点。*

5. *pTarget：指定所用着色器类型和版本的字符串。在本博客中，我们采用的着色器模型版本是 5.0 和 5.1。*

   * *vs_5_0 与 vs_5_1：表示版本分别为 5.0 和 5.1 的顶点着色器（vertex shader）* 
   * *hs_5_0 与 hs_5_1：表示版本分别为 5.0 和 5.1 的外壳着色器（hull shader）*
   * *ds_5_0 与 ds_5_1：表示版本分别为 5.0 和 5.1 的域着色器（domain shader）*
   * *gs_5_0 与 gs_5_1：表示版本分别为 5.0 和 5.1 的几何着色器（geometry shader）*
   * *ps_5_0 与 ps_5_1：表示版本分别为 5.0 和 5.1 的像素着色器（pixel shader）*
   * *cs_5_0 与 cs_5_1：表示版本分别为 5.0 和 5.1 的计算着色器（compute shader)*

6. *Flags1：指示对着色器代码应当如何编译的标志。在 SDK 文档里，这些标志列出得不少，但 是此书中我们仅用两种*

   * *D3DCOMPILE_DEBUG：用调试模式来编译着色器*
   * *D3DCOMPILE_SKIP_OPTIMIZATION：指示编译器跳过优化阶段（对调试很有用处）*

7. *Flags2：我们不会用到处理效果文件的高级编译选项，关于它的信息请参见 SDK 文档*

8. *ppCode：返回一个指向 ID3DBlob 数据结构的指针，它存储着编译好的着色器对象字节码。*

9. *ppErrorMsgs：返回一个指向 ID3DBlob 数据结构的指针。如果在编译过程中发生了错误， 它便会储存报错的字符串*

   *ID3DBlob 类型描述的其实就是一段普通的内存块，这是该接口的两个方法*

   * *LPVOID GetBufferPointer：返回指向 ID3DBlob 对象中数据的 void*类型的指针。由此 可见，在使用此数据之前务必先要将它转换为适当的类型*
   * *SIZE_T GetBufferSize：返回缓冲区的字节大小（即该对象中的数据大小）*

*为了能够输出错误信息，我们在 d3dUtil.h/.cpp 文件中实现了下列辅助函数在运行时编译着色器：*

```cpp
ComPtr<ID3DBlob> d3dUtil::CompileShader(
	const std::wstring& filename,
	const D3D_SHADER_MACRO* defines,
	const std::string& entrypoint,
	const std::string& target)
{
	// 若处于调试模式,则使用调试标志
	UINT compileFlags = 0;
#if defined(DEBUG) || defined(_DEBUG)
	compileFlags = D3DCOMPILE_DEBUG | D3DCOMPILE_SKIP_OPTIMIZATION;
#endif
	HRESULT hr = S_OK;
	ComPtr<ID3DBlob> byteCode = nullptr;
	ComPtr<ID3DBlob> errors;
	hr = D3DCompileFromFile(filename.c_str(), defines, D3D_COMPILE_STANDARD_FILE_INCLUDE,
	entrypoint.c_str(), target.c_str(), compileFlags, 0, &byteCode, &errors);
	// 将错误信息输出到调试窗口
	if(errors != nullptr) OutputDebugStringA((char*)errors->GetBufferPointer());
	ThrowIfFailed(hr);
	return byteCode;
} 

ComPtr<ID3DBlob> mvsByteCode = nullptr;
ComPtr<ID3DBlob> mpsByteCode = nullptr;
mvsByteCode = d3dUtil::CompileShader(L"Shaders\\color.hlsl",nullptr, "VS", "vs_5_0");
mpsByteCode = d3dUtil::CompileShader(L"Shaders\\color.hlsl",nullptr, "PS", "ps_5_0"); 
```

*HLSL 的错误和警告消息将通过 ppErrorMsgs 参数返回。比方说，如果不小心把 mul 函数拼写错 误，那么我们便会从调试窗口得到类似于下列的错误输出：*

```cpp
Shaders\color.hlsl(29,14-55): error X3004: undeclared identifier 'mu'
（Shaders\color.hlsl(29,14-55): 错误 X3004: 未声明的标识符 'mu'）
```

*仅对着色器进行编译并不会使它与渲染流水线相绑定以供其使用*

### ***离线编译***

*我们不仅可以在运行期间编译着色器，还能够以单独的步骤（例如，将其作为构建整个工程过程中 的一个独立环节，或是将其视为资源内容流水线（asset content pipeline）流程的一部分）离线地（offline） 编译着色器*

*这样做有原因若干：*

1. *对于复杂的着色器来说，其编译过程可能耗时较长。因此，借助离线编译即可缩短应用程序的 加载时间*
2. *以便在早于运行时的构建处理期间提前发现编译错误。*
3. *对于 Windows 8 应用商店中的应用而言，必须采用离线编译这种方式*

*我们通常用.cso (即 compiled shader object，已编译的着色器对象)作为已编译着色器的扩展名。*

### ***生成着色器汇编代码***

*FXC 程序根据可选参数/Fc 来生成可移植的着色器汇编代码。通过查阅着色器的汇编代码，既可核对着色 器的指令数量，也能了解生成的代码细节——这是为了验证编译器所生成的代码与我们预想的是否一致。*

## ***光栅器状态***

### ***D3D12_RASTERIZER_DESC***

*当今渲染流水线中的大多阶段都是可编程的，但是有些特定环节却只能接受配置。

例如，用于配置渲 染流水线中光栅化阶段的光栅器状态（rasterizer state）组由结构体 D3D12_RASTERIZER_DESC 来表示: *

```cpp
typedef struct D3D12_RASTERIZER_DESC {
	D3D12_FILL_MODE FillMode; // 默认值为： D3D12_FILL_MODE_SOLID
	D3D12_CULL_MODE CullMode; // 默认值为： D3D12_CULL_MODE_BACK
	BOOL FrontCounterClockwise; // 默认值为： false
	INT DepthBias; // 默认值为： 0
	FLOAT DepthBiasClamp; // 默认值为： 0.0f
	FLOAT SlopeScaledDepthBias; // 默认值为： 0.0f
	BOOL DepthClipEnable; // 默认值为： true
	BOOL MultisampleEnable; // 默认值为： false
	BOOL AntialiasedLineEnable; // 默认值为： false
	UINT ForcedSampleCount; // 默认值为： 0
	// 默认值为： D3D12_CONSERVATIVE_RASTERIZATION_MODE_OFF
	D3D12_CONSERVATIVE_RASTERIZATION_MODE ConservativeRaster;
} D3D12_RASTERIZER_DESC;
```

1. *FillMode：将此参数设置为D3D12_FILL_MODE_WIREFRAME 是采用线框模式进行渲染，而设置 为 D3D12_FILL_MODE_SOLID 则是使用实体模式进行渲染。默认设置为实体渲染模式。*
2. *CullMode：指定 D3D12_CULL_MODE_NONE 是禁用剔除操作，D3D12_CULL_MODE_BACK 是 剔除背面朝向的三角形，而 D3D12_CULL_MODE_FRONT 是剔除正面朝向的三角形。默认配置 为剔除背面朝向的三角形*
3. *FrontCounterClockwise：如果指定为 false，则根据摄像机的观察视角，将顶点顺序为 顺时针方向的三角形看作正面朝向，而把逆时针绕序的三角形当作背面朝向。相反，如果指定 为 true，则根据摄像机的观察视角，将顶点顺序为逆时针方向的三角形看作正面朝向，而把顺 时针绕序的三角形当作背面朝向。此参数默认值为 false*

*下列代码展示了如何创建一个开启线框模式，且禁用背面剔除的光栅化状态：*

```cpp
CD3DX12_RASTERIZER_DESC rsDesc(D3D12_DEFAULT);
rsDesc.FillMode = D3D12_FILL_MODE_WIREFRAME;
rsDesc.CullMode = D3D12_CULL_MODE_NONE; 
```

*CD3DX12_RASTERIZER_DESC 是在扩展自 D3D12_RASTERIZER_DESC 结构体的基础上，又添加 了一些辅助构造函数的工具类。*

*其中有一个以接收 CD3DX12_DEFAULT 作为参数来创建光栅化状态对象 的构造函数，其实 CD3DX12_DEFAULT 只是一个哑类型（dummy），而此函数的作用是将光栅化状态中 需要被初始化的成员重载为默认值。CD3DX12_DEFAULT① 和 D3D12_DEFAULT 的定义如下：*

```cpp
struct CD3DX12_DEFAULT {};
extern const DECLSPEC_SELECTANY CD3DX12_DEFAULT D3D12_DEFAULT; 
```

*另外，D3D12_DEFAULT（CD3DX12_DEFAULT）还被广泛地运用于 Direct3D 的其他几种工具类中。*

## ***流水线状态对象***

### ***D3D12_GRAPHICS_PIPELINE_STATE_DESC***

*到目前为止，我们已经展示过编写输入布局描述、创建顶点着色器和像素着色器，以及配置光栅器状 态组这 3 个步骤，还未曾讲解如何将这些对象绑定到图形流水线上，用以实际绘制图形。大多数控制图形 流水线状态的对象被统称为流水线状态对象（Pipeline State Object，PSO），用 ID3D12PipelineState 接 口来表示。要创建 PSO，我们首先要填写一份描述其细节的 D3D12_GRAPHICS_PIPELINE_STATE_DESC 结构体实例。*

```cpp
typedef struct D3D12_GRAPHICS_PIPELINE_STATE_DESC
{
	ID3D12RootSignature *pRootSignature;
	D3D12_SHADER_BYTECODE VS;
	D3D12_SHADER_BYTECODE PS;
	D3D12_SHADER_BYTECODE DS;
	D3D12_SHADER_BYTECODE HS;
	D3D12_SHADER_BYTECODE GS;
	D3D12_STREAM_OUTPUT_DESC StreamOutput;
	D3D12_BLEND_DESC BlendState;
	UINT SampleMask;
	D3D12_RASTERIZER_DESC RasterizerState;
	D3D12_DEPTH_STENCIL_DESC DepthStencilState;
	D3D12_INPUT_LAYOUT_DESC InputLayout;
	D3D12_PRIMITIVE_TOPOLOGY_TYPE PrimitiveTopologyType;
	UINT NumRenderTargets;
	DXGI_FORMAT RTVFormats[8];
	DXGI_FORMAT DSVFormat;
	DXGI_SAMPLE_DESC SampleDesc;
} D3D12_GRAPHICS_PIPELINE_STATE_DESC;
```

1. *pRootSignature：指向一个与此 PSO 相绑定的根签名的指针。该根签名一定要与此 PSO 指 定的着色器相兼容。*

2. *VS：待绑定的顶点着色器。此成员由结构体 D3D12_SHADER_BYTECODE 表示，这个结构体存 有指向已编译好的字节码数据的指针，以及该字节码数据所占的字节大小。*

   ```cpp
   typedef struct D3D12_SHADER_BYTECODE {
   	const void *pShaderBytecode; 
   	SIZE_T BytecodeLength;
   } D3D12_SHADER_BYTECODE; 
   ```

3. *PS：待绑定的像素着色器。*

4. *DS：待绑定的域着色器（我们将在后续章节中讲解此类型的着色器）。*

5. *HS：待绑定的外壳着色器（我们将在后续章节中讲解此类型的着色器）。*

6. *GS：待绑定的几何着色器（我们将在后续章节中讲解此类型的着色器）。*

7. *StreamOutput：用于实现一种称作流输出（stream-out）的高级技术。目前我们仅将此字段清零*

8. *BlendState：指定混合（blending）操作所用的混合状态。我们将在后续章节中讨论此状态组， 目前仅将此成员指定为默认的 CD3DX12_BLEND_DESC(D3D12_DEFAULT)。*

9. *SampleMask：多重采样最多可采集 32 个样本。借此参数的 32 位整数值，即可设置每个采样 点的采集情况（采集或禁止采集）。例如，若禁用了第 5 位（将第 5 位设置为 0），则将不会对 第 5 个样本进行采样。当然，要禁止采集第 5 个样本的前提是，所用的多重采样至少要有 5 个样本。假如一个应用程序仅使用了单采样（single sampling），那么只能针对该参数的第 1 位 进行配置。一般来说，使用的都是默认值 0xffffffff，即表示对所有的采样点都进行采样。*

10. *RasterizerState：指定用来配置光栅器的光栅化状态。*

11. *DepthStencilState：指定用于配置深度/模板测试的深度/模板状态。我们将在后续章节中对 此状态进行讨论，目前只把它设为默认的 CD3DX12_DEPTH_STENCIL_DESC(D3D12_DEFAULT)。*

12. *InputLayout：输入布局描述，此结构体中有两个成员：一个由 D3D12_INPUT_ELEMENT_DESC 元素构成的数组，以及一个表示此数组中元素数量的无符号整数。*

    ```cpp
    typedef struct D3D12_INPUT_LAYOUT_DESC
    {
    	const D3D12_INPUT_ELEMENT_DESC *pInputElementDescs;
    	UINT NumElements;
    } D3D12_INPUT_LAYOUT_DESC; 
    ```

13. *PrimitiveTopologyType：指定图元的拓扑类型。*

    ```cpp
    typedef enum D3D12_PRIMITIVE_TOPOLOGY_TYPE {
    	D3D12_PRIMITIVE_TOPOLOGY_TYPE_UNDEFINED = 0,
    	D3D12_PRIMITIVE_TOPOLOGY_TYPE_POINT = 1,
    	D3D12_PRIMITIVE_TOPOLOGY_TYPE_LINE = 2,
    	D3D12_PRIMITIVE_TOPOLOGY_TYPE_TRIANGLE = 3,
    	D3D12_PRIMITIVE_TOPOLOGY_TYPE_PATCH = 4
    } D3D12_PRIMITIVE_TOPOLOGY_TYPE;
    ```

14. *NumRenderTargets：同时所用的渲染目标数量（即 RTVFormats 数组中渲染目标格式的数量）。*

15. *RTVFormats：渲染目标的格式。利用该数组实现向多渲染目标同时进行写操作。使用此 PSO 的渲染目标的格式设定应当与此参数相匹配。*

16. *DSVFormat：深度/模板缓冲区的格式。使用此 PSO 的深度/模板缓冲区的格式设定应当与 此参数相匹配。*

17. *SampleDesc：描述多重采样对每个像素采样的数量及其质量级别。此参数应与渲染目标的对 应设置相匹配。*

### ***ID3D12Device:: CreateGraphicsPipelineState***

*在 D3D12_GRAPHICS_PIPELINE_STATE_DESC 实例填写完毕后，我们即可用 ID3D12Device:: CreateGraphicsPipelineState 方法来创建 ID3D12PipelineState 对象。*

```cpp
ComPtr<ID3D12RootSignature> mRootSignature;
std::vector<D3D12_INPUT_ELEMENT_DESC> mInputLayout;
ComPtr<ID3DBlob> mvsByteCode;
ComPtr<ID3DBlob> mpsByteCode;
...
D3D12_GRAPHICS_PIPELINE_STATE_DESC psoDesc;
ZeroMemory(&psoDesc, sizeof(D3D12_GRAPHICS_PIPELINE_STATE_DESC));
psoDesc.InputLayout = { mInputLayout.data(), (UINT)mInputLayout.size() };
psoDesc.pRootSignature = mRootSignature.Get();
psoDesc.VS =
{
	reinterpret_cast<BYTE*>(mvsByteCode->GetBufferPointer()),
	mvsByteCode->GetBufferSize()
};
psoDesc.PS =
{
	reinterpret_cast<BYTE*>(mpsByteCode->GetBufferPointer()),
	mpsByteCode->GetBufferSize()
};
psoDesc.RasterizerState = CD3DX12_RASTERIZER_DESC(D3D12_DEFAULT);
psoDesc.BlendState = CD3DX12_BLEND_DESC(D3D12_DEFAULT);
psoDesc.DepthStencilState = CD3DX12_DEPTH_STENCIL_DESC(D3D12_DEFAULT);
psoDesc.SampleMask = UINT_MAX;
psoDesc.PrimitiveTopologyType = D3D12_PRIMITIVE_TOPOLOGY_TYPE_TRIANGLE;
psoDesc.NumRenderTargets = 1;
psoDesc.RTVFormats[0] = mBackBufferFormat;
psoDesc.SampleDesc.Count = m4xMsaaState ? 4 : 1;
psoDesc.SampleDesc.Quality = m4xMsaaState ? (m4xMsaaQuality - 1) : 0;
psoDesc.DSVFormat = mDepthStencilFormat;
ComPtr<ID3D12PipelineState> mPSO;
md3dDevice->CreateGraphicsPipelineState(&psoDesc, IID_PPV_ARGS(&mPSO)));
```

*ID3D12PipelineState 对象集合了大量的流水线状态信息。为了保证性能，我们将所有这些对 象都集总在一起，一并送至渲染流水线。*

*通过这样的一个集合，Direct3D 便可以确定所有的状态是否彼 此兼容，而驱动程序则能够据此而提前生成硬件本地指令及其状态。*

*并非所有的渲染状态都封装于 PSO 内，如视口（viewport）和裁剪矩形（scissor rectangle）等 属性就独立于 PSO。由于将这些状态的设置与其他的流水线状态分隔开来会更有效，所以把它们 强行集中在 PSO 内也并不会为之增添任何优势。*

*Direct3D 实质上就是一种状态机（state machine），里面的事物会保持它们各自的状态，直到我们将 其改变。如果我们以不同的 PSO 去绘制不同物体，则需要像下面那样来组织代码: *

```cpp
// 重置命令列表并指定初始 PSO
mCommandList->Reset(mDirectCmdListAlloc.Get(), mPSO1.Get());
/* ……使用 PSO 1 绘制物体…… */
// 改变 PSO
mCommandList->SetPipelineState(mPSO2.Get());
/* ……使用 PSO 2 绘制物体…… */
// 改变 PSO
mCommandList->SetPipelineState(mPSO3.Get());
/* ……使用 PSO 3 绘制物体…… */ 
```

*换句话说，如果把一个 PSO 与命令列表相绑定，那么，在我们设置另一个 PSO 或重置命令列表之 前，会一直沿用当前的 PSO 绘制物体。*

## ***几何图形辅助结构体***

*我们通过创建一个同时存有顶点缓冲区和索引缓冲区的结构体来方便地定义多个几何体*

*另外，借此结构体即可将顶点和索引数据置于系统内存之中，以供 CPU 读取*

*例如，执行拾取（picking）和碰撞检测（collision detection）这样的工作就需要 CPU 来访问几何体数据。*

*再者，该结构体还缓存了顶点缓冲 区和索引缓冲区的一些重要属性（例如格式和每个顶点项所占用的字节数），并提供了返回缓冲区视图的方法。*

*当需要定义多个几何体时，我们就使用下面的 MeshGeometry结构体*

```cpp
// 利用 SubmeshGeometry 来定义 MeshGeometry 中存储的单个几何体
// 此结构体适用于将多个几何体数据存于一个顶点缓冲区和一个索引缓冲区的情况
// 它提供了对存于顶点缓冲区和索引缓冲区中的单个几何体进行绘制所需的数据和偏移量，我们可以据此来
// 实现图 6.3 中所描绘的技术
struct SubmeshGeometry
{
	UINT IndexCount = 0;
	UINT StartIndexLocation = 0;
	INT BaseVertexLocation = 0;
	// 通过此子网格来定义当前 SubmeshGeometry 结构体中所存几何体的包围盒（bounding box）。我们
	// 将在本书的后续章节中使用此数据
	DirectX::BoundingBox Bounds;
};
struct MeshGeometry
{
	// 指定此几何体网格集合的名称，这样我们就能根据此名找到它
	std::string Name;
	// 系统内存中的副本。由于顶点/索引可以是泛型格式（具体格式依用户而定），所以用 Blob 类型来表示
	// 待用户在使用时再将其转换为适当的类型
	Microsoft::WRL::ComPtr<ID3DBlob> VertexBufferCPU = nullptr;
	Microsoft::WRL::ComPtr<ID3DBlob> IndexBufferCPU = nullptr;
	Microsoft::WRL::ComPtr<ID3D12Resource> VertexBufferGPU = nullptr;
	Microsoft::WRL::ComPtr<ID3D12Resource> IndexBufferGPU = nullptr;
	Microsoft::WRL::ComPtr<ID3D12Resource> VertexBufferUploader = nullptr;
	Microsoft::WRL::ComPtr<ID3D12Resource> IndexBufferUploader = nullptr;
	// 与缓冲区相关的数据
	UINT VertexByteStride = 0;
	UINT VertexBufferByteSize = 0;
	DXGI_FORMAT IndexFormat = DXGI_FORMAT_R16_UINT;
	UINT IndexBufferByteSize = 0;
	// 一个 MeshGeometry 结构体能够存储一组顶点/索引缓冲区中的多个几何体
	// 若利用下列容器来定义子网格几何体，我们就能单独地绘制出其中的子网格（单个几何体）
	std::unordered_map<std::string, SubmeshGeometry> DrawArgs;
	D3D12_VERTEX_BUFFER_VIEW VertexBufferView()const
	{
		D3D12_VERTEX_BUFFER_VIEW vbv;
		vbv.BufferLocation = VertexBufferGPU->GetGPUVirtualAddress();
		vbv.StrideInBytes = VertexByteStride;
		vbv.SizeInBytes = VertexBufferByteSize;
		return vbv;
	}
	D3D12_INDEX_BUFFER_VIEW IndexBufferView()const
	{
		D3D12_INDEX_BUFFER_VIEW ibv;
		ibv.BufferLocation = IndexBufferGPU->GetGPUVirtualAddress();
		ibv.Format = IndexFormat;
		ibv.SizeInBytes = IndexBufferByteSize;
		return ibv;
	}
	// 待数据上传至 GPU 后，我们就能释放这些内存了
	void DisposeUploaders()
	{
		VertexBufferUploader = nullptr;
		IndexBufferUploader = nullptr;
	}
};
```


