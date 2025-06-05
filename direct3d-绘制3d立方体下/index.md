# 绘制3D立方体(下)


## ***帧资源***

*在上篇，我们的演示程序在绘制每一帧时 都会将 CPU 和 GPU 进行一次同步，这样做的原因当然是确保数据安全。这种解决方案虽然奏效却效率低下*

*解决此问题的一种方案是：以 CPU 每帧都需更新的资源作为基本元素，创建一个环形数组。我们称这些资源为帧资源，而这种循环数组通常是由 3 个帧资源元素所构成的。该方案的思路是：在处理第 n 帧的时候，CPU 将周而复始地从帧资源数组中获取下一个可用的（即没被 GPU 使用中的）帧资源。趁着 GPU 还在处理此前帧之时，CPU 将为第 n 帧更新 资源，并构建和提交对应的命令列表。随后，CPU 会继续针对第 n+1 帧执行同样的工作流程，并不断重复下去。如果帧资源数组共有 3 个元素，则令 CPU 比 GPU 提前处理两帧，以确保 GPU 可持续工作。*

*不难看出，这种解决方案还是无法完全避免等待情况的发生。**但是如果CPU执行的比GPU快，那么就可以保证GPU不会浪费任何性能，并且CPU等GPU的时候可以去执行不依赖GPU，比如AI以及游戏业务逻辑一些的任务***

## ***渲染项***

*绘制一个物体需要设置多种参数，例如绑定顶点缓冲区和索引缓冲区、绑定与物体有关的常量数据、 设定图元类型以及指定 DrawIndexedInstanced 方法的参数。随着场景中所绘物体的逐渐增多，如果我们能创建一个轻量级结构来存储绘制物体所需的数据，那真是极好的；由于每个物体的特征不同，绘制过程中所需的数据也会有所变化，因此该结构中的数据也会因具体程序而异。我们把单次绘制调用过程中，需要向渲染流水线提交的数据集称为渲染项。*

## ***渲染过程中所用到的常量数据***

*随着演示代码复杂度的不断增加，该缓冲区中存储的数据内容（例如观察位置、观察矩阵与投影矩阵以 及与屏幕（渲染目标）分辨率等相关的信息）会根据特定的渲染过程（rendering pass）而确定下来。其中也包含了与游戏计时有关的信息，它们是着色器程序中要访问的极有用的数据。*

*就目前的情况而言，为了绘制物体，与之唯一相关的常量就是它的世界矩阵：*

*我们做出上述调整的思路为：基于资源的更新频率对常量数据进行分组。在每次渲染过程中，只需将本次所用的常量（cbPass）更新一次；而每当某个物体的世界矩阵发生改变时，只需更新该物体的相关常量（cbPerObject）即可。如果场景中有一个静态物体，比如一棵树，则只需对它的物体常量缓冲区设置一次（树的）世界矩阵，而后就再也不必对它进行更新了。*

*现在，着色器所期望的输入资源已发生了改变，因此我们需要相应地调整根签名来使之获取所需的 两个描述符表（此时，我们的着色器程序需要获取两个描述符表，因为这两个 CBV（常量缓冲区视图） 有着不同的更新频率——渲染过程 CBV 仅需在每个渲染过程中设置一次，而物体 CBV 则要针对每一个 渲染项进行配置）*

## ***不同形状的几何体***

*我们将展示如何创建不同形状的几何体，如椭球体、球体、柱体（通过调整圆柱体的上下两底即可创建出圆台和圆锥体）。这些几何体对于绘制天空穹顶（sky dome，即描述游戏中玩家头顶的天空部分，也有译作天空穹、天穹等）、图形程序调试、碰撞检测的可视化以及延迟渲染（deferred rendering） 是有极大裨益的。比如说，我们可以先将正在制作中的游戏角色简化渲染成球体，以供调试检测。*

### ***生成柱形网格***

*在定义一个柱体时，需要指定其顶、底面半径，高度，切片数量（slice count，即将截面分割的块数）， 以及堆叠层数（stack count，即横向切割的层数）*

*程序中的柱体呈圆台形状，因而就此展开讨论。我们将圆台的构成分为侧面几何体，顶面几何体以及底面几何体 3 个部分。*

![image-20250427220548111](https://raw.githubusercontent.com/vlicecream/cloudImage/main/image-20250427220548111.png)

#### ***柱体的侧面几何体***

*圆台的所有顶点都列于其各层侧面的“环”上，共有 stackCount + 1 环，而每个环上的顶点数 量都为 sliceCount。相邻环的半径差为 Δ =r (topRadius-bottomRadius)/stackCount 。*

*如果从底面上的环开始用索引来 0 表示，那么第 i 环的半径就是 $ r_i = + bottomRadius + iΔr $，且第 i 环的高度值为 $ h_i = -\frac{h}{2} + iΔr $ 其中的 Δh 是每层的高度，h 为圆台的高度。*

*由此可知， 生成圆台的基本思路是遍历每个环，并生成列于环上的各个顶点  下面给出此算法的实现：*

```cpp
GeometryGenerator::MeshData GeometryGenerator::CreateCylinder (
    float bottomRadius, float topRadius,
    float height, uint32 sliceCount, uint32 stackCount
) 
{
    MeshData meshData; 
    // 构建堆叠层
    float stackHeight = height / stackCount;
    // 计算从下至上遍历每个相邻分层时所需的半径增量
 	float radiusStep = (topRadius - bottomRadius) / stackCount; 
    uint32 ringCount = stackCount+1;
    // 从底面开始，由下至上计算每个堆叠层环上的顶点坐标
    for(uint32 i = 0; i < ringCount; ++i)
    {
        float y = -0.5f*height + i*stackHeight;
        float r = bottomRadius + i*radiusStep;
        // 环上的各个顶点
        float dTheta = 2.0f*XM_PI/sliceCount;
        for(uint32 j = 0; j <= sliceCount; ++j)
        { 
            Vertex vertex;
            t c = cosf(j*dTheta);
            float s = sinf(j*dTheta); 
            vertex.Position = XMFLOAT3(r*c, y, r*s);
            vertex.TexC.x = (float)j/sliceCount;
            vertex.TexC.y = 1.0f - (float)i/stackCount;
            
            /*
            可以像下面那样以参数化（parameterized）的方式来计算圆台顶点，
            我们引入与纹理坐标 v 方向相同的参数 v，从而使副切线与纹理坐标 v  的方向相同
            设 r0 为底面半径，r1 为顶面半径
            y(v) = h - hv 其中 v 位于区间[0,1] 
            r(v) = r1 + (r0-r1)v 
            x(t, v) = r(v)*cos(t) 
            y(t, v) = h - hv 
            z(t, v) = r(v)*sin(t)
            dx/dt = -r(v)*sin(t) 
            dy/dt = 0
            dz/dt = +r(v)*cos(t) 
            dx/dv = (r0-r1)*cos(t)
            dy/dv = -h 
            dz/dv = (r0-r1)*sin(t) 
            */
            
            // 此为单位长度
            vertex.TangentU = XMFLOAT3(-s, 0.0f, c); 
            float dr = bottomRadius-topRadius;
            XMFLOAT3 bitangent(dr*c, -height, dr*s);
            XMVECTOR T = XMLoadFloat3(&vertex.TangentU);
            XMVECTOR B = XMLoadFloat3(&bitangent);
            XMVECTOR N = XMVector3Normalize(XMVector3Cross(T, B));
            XMStoreFloat3(&vertex.Normal, N);
            meshData.Vertices.push_back(vertex); 
        }
    }
}
```

*观察图 7.2 可知，由每个分层以及切片分割出的侧面块都是一个四边形（由两个三角形构成）。而以第 i 层与第 j 块切片所确定下来的侧面块中的两个三角形的索引分别为：*

1. *ΔABC = (i · n + j, (i + 1) · n + j, (i + 1) ·  n + j + 1)*
2. *ΔABC = (i · n + j, (i + 1) · n + j + 1,  i · n + j + 1)*

*其中，n 是每个环上的顶点数量。因此，求取圆台侧面块上所有三角形索引的主要思路是：遍历每个堆叠层和每个切片，并运用上述公式进行计算。*

```cpp
GeometryGenerator::MeshData GeometryGenerator::CreateCylinder (
    float bottomRadius, float topRadius,
    float height, uint32 sliceCount, uint32 stackCount
) 
{
    MeshData meshData; 
    // 构建堆叠层
    float stackHeight = height / stackCount;
    // 计算从下至上遍历每个相邻分层时所需的半径增量
 	float radiusStep = (topRadius - bottomRadius) / stackCount; 
    uint32 ringCount = stackCount+1;
    // 从底面开始，由下至上计算每个堆叠层环上的顶点坐标
    for(uint32 i = 0; i < ringCount; ++i)
    {
        float y = -0.5f*height + i*stackHeight;
        float r = bottomRadius + i*radiusStep;
        // 环上的各个顶点
        float dTheta = 2.0f*XM_PI/sliceCount;
        for(uint32 j = 0; j <= sliceCount; ++j)
        { 
            Vertex vertex;
            t c = cosf(j*dTheta);
            float s = sinf(j*dTheta); 
            vertex.Position = XMFLOAT3(r*c, y, r*s);
            vertex.TexC.x = (float)j/sliceCount;
            vertex.TexC.y = 1.0f - (float)i/stackCount;
            
            /*
            可以像下面那样以参数化（parameterized）的方式来计算圆台顶点，
            我们引入与纹理坐标 v 方向相同的参数 v，从而使副切线与纹理坐标 v  的方向相同
            设 r0 为底面半径，r1 为顶面半径
            y(v) = h - hv 其中 v 位于区间[0,1] 
            r(v) = r1 + (r0-r1)v 
            x(t, v) = r(v)*cos(t) 
            y(t, v) = h - hv 
            z(t, v) = r(v)*sin(t)
            dx/dt = -r(v)*sin(t) 
            dy/dt = 0
            dz/dt = +r(v)*cos(t) 
            dx/dv = (r0-r1)*cos(t)
            dy/dv = -h 
            dz/dv = (r0-r1)*sin(t) 
            */
            
            // 此为单位长度
            vertex.TangentU = XMFLOAT3(-s, 0.0f, c); 
            float dr = bottomRadius-topRadius;
            XMFLOAT3 bitangent(dr*c, -height, dr*s);
            XMVECTOR T = XMLoadFloat3(&vertex.TangentU);
            XMVECTOR B = XMLoadFloat3(&bitangent);
            XMVECTOR N = XMVector3Normalize(XMVector3Cross(T, B));
            XMStoreFloat3(&vertex.Normal, N);
            meshData.Vertices.push_back(vertex); 
        }
    }
    
    // +1 是希望让每环的第一个顶点和最后一个顶点重合，这是因为它们的纹理坐标并不相同
    uint32 ringVertexCount = sliceCount+1; 
    // 计算每个侧面块中三角形的索引
    for(uint32 i = 0; i < stackCount; ++i) 
    {
        for(uint32 j = 0; j < sliceCount; ++j) 
        {
            meshData.Indices32.push_back(i*ringVertexCount + j);
            meshData.Indices32.push_back((i+1)*ringVertexCount + j);
            meshData.Indices32.push_back((i+1)*ringVertexCount + j+1);
            meshData.Indices32.push_back(i*ringVertexCount + j);
            meshData.Indices32.push_back((i+1)*ringVertexCount + j+1);
            meshData.Indices32.push_back(i*ringVertexCount + j+1); 
        }
    }
    
    BuildCylinderTopCap(bottomRadius, topRadius, height,
                        sliceCount, stackCount, meshData);
    BuildCylinderBottomCap(bottomRadius, topRadius, height,
                           sliceCount, stackCount, meshData);
    return meshData; 
}
```

![image-20250427223534916](https://raw.githubusercontent.com/vlicecream/cloudImage/main/image-20250427223534916.png)

#### ***柱体的端面几何体***

*生成圆台端面的几何体，相当于在其顶面和底面的截面上切割出多个三角形，使之逼近一个圆形：*

```cpp
void GeometryGenerator::BuildCylinderTopCap (
    float bottomRadius, 
    float topRadius, 
    float height, 
    uint32 sliceCount, 
    uint32 stackCount,
    MeshData& meshData
) 
{
    uint32 baseIndex = (uint32)meshData.Vertices.size(); 
    float y = 0.5f*height;
    float dTheta = 2.0f*XM_PI/sliceCount; 
    // 使圆台端面环上的首尾顶点重合，因为这两个顶点的纹理坐标和法线是不同的
    for(uint32 i = 0; i <= sliceCount; ++i)
    {
        float x = topRadius*cosf(i*dTheta);
        float z = topRadius*sinf(i*dTheta);
        // 根据圆台的高度使顶面纹理坐标的范围按比例缩小
        float u = x/height + 0.5f;
        float v = z/height + 0.5f;
        meshData.Vertices.push_back(
            Vertex(x, y, z, 0.0f, 1.0f, 0.0f, 1.0f, 0.0f, 0.0f, u, v) );
    }
    // 顶面的中心顶点
    meshData.Vertices.push_back(
        Vertex(0.0f, y, 0.0f, 0.0f, 1.0f, 0.0f, 1.0f, 0.0f, 0.0f, 0.5f, 0.5f) );
    // 中心顶点的索引值
    uint32 centerIndex = (uint32)meshData.Vertices.size()-1;
    for(uint32 i = 0; i < sliceCount; ++i)
    {
        meshData.Indices32.push_back(centerIndex);
        meshData.Indices32.push_back(baseIndex + i+1);
        meshData.Indices32.push_back(baseIndex + i);
    }
} 
```

*生成圆台底面的代码与之相似。*

### ***生成球体网格***

*欲定义一个球体，就要指定其半径、切片数量及其堆叠层数，如图 7.3 所示。除了每个环上的半径是依三角函数非线性变化，生成球体的算法与生成圆台的算法非常相近。我们将把 GeometryGenerator::CreateSphere 方法的代码留给读者自行研究。最后，值得一提的是，若采用不等比缩放世界变换，即可将球体转换为椭球体。*

![image-20250508211016286](https://raw.githubusercontent.com/vlicecream/cloudImage/main/image-20250508211016286.png)

### ***生成几何球体网格***

*观察图 7.3 可知，构成球体的三角形面积并不相同，这在某些情景中并非我们所愿。相对而言，几何球体（geosphere）利用面积相同且边长相等的三角形来逼近球体，如图 7.4 所示。*

![image-20250508211104589](https://raw.githubusercontent.com/vlicecream/cloudImage/main/image-20250508211104589.png)

*为了生成几何球体，我们以一个正二十面体作为基础，细分其上的三角形，再根据给定的半径向球面投影新生成的顶点。反复重复这个过程，便可以提高该几何球体的曲面细分程度。*

*图 7.5 展示了如何将一个三角形细分为 4 个大小相等的小三角形。不难发现，新生成的顶点都位于原始三角形边上的中点。先将顶点投影到单位球面上，再利用 r 进行标量乘法： $ v^' = r \frac{v}{||v||} $，即可把新顶点都投影到半径为 r 的球体之上。*

![image-20250508211225548](https://raw.githubusercontent.com/vlicecream/cloudImage/main/image-20250508211225548.png)

## ***细探根签名***

*根签名定义了：在绘制调用之前，需要绑定到渲染流水线上的资源，以及这些资源应如何映射到着色器的输入寄存器中。选择绑定到流水线上的资源要根据着色器程序的具体需求。从 PSO 被创建之时起，根签名和着色器程序的组合就开始生效。*

### ***根参数***

