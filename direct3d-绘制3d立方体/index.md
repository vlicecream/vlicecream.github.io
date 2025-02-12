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

