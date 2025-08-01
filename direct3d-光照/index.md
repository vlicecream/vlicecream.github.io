# 光照


## ***光照与材质的交互***

*在开启光照的同时，我们不再直接指出顶点的颜色，而是指定材质与光照，再运用光照方程（lighting equation）基于两者的交互来计算顶点颜色。这样做会使物体的颜色更趋于真实*

### ***局部光照模型***

*若使用这种局部模型，则每个物体的光照皆独立于其他物体，我们也就可以在处理光照的过 程中仅考虑光源直接发出的光线（即在处理当前的 物体光照时，忽略来自场景中其他物体所反弹来的*

***说通俗点也就是即使物体被遮挡，但是也会受到光照，每个物体的光照皆独立于其他物体***

### ***全局光照模型***

*谈及全局光照模型， 除了要考虑由光源直接发出的光，还要顾及场景中其他物体所反弹来的间接光照。*

*之所以称为全局光 照模型，是因为在对一个物体进行照明时，还要考虑全局场景中的所有事物。*

*全局光照模型的开销通常是实时游戏所负担不起的（但是这种模型会生成十分接近于照片级真实感的场景）。*

*接近于全局光照的实时方 法尚处于研究阶段。例如，立体像素全局光照技术。 另一种流行的方法是预计算静态物体（如墙壁、塑像）的间接光照，再用得到的结果来近似地模拟动态 物体（如可运动游戏角色）的间接光照。*

## ***法向量***

### ***平面法线***

*平面法线是一种描述多边形朝向（即正交于多边形上所有点）的单位向量*

### ***曲面法线***

*曲面法线（surface normal）是 一种垂直于曲面上一点处切平面（有文献强调还要满足曲面法线经过该点这一条件）的单位向量*

### ***顶点法线***

*为了求出曲面法线，我们仅先指定位于网格顶点处的曲面法线（所以也将之称作顶点法线）*

## ***计算法向量***

*为了找到三角形 $p_0,p_1,p_2$ 的平面法线，我们首先计算位于三角形边上的两个向量：*
$$
\vec{u} = p_1 - p_0
\vec{v} = p_2 - p_0
$$
*那么，此三角形的平面法线即为：*
$$
\vec{n} =  \frac{\vec{u} \times \vec{v}}{|| \vec{u} \times \vec{v} ||}
$$
下列函数将根据三角形的 3 个顶点来计算该三角形正面的平面法线：

```cpp
MVECTOR ComputeNormal(FXMVECTOR p0, FXMVECTOR p1, FXMVECTOR p2)
{
    XMVECTOR u = p1 - p0;
    XMVECTOR v = p2 - p0;
    return XMVector3Normalize(XMVector3Cross(u,v));
} 
```

### ***求顶点法线平均值***

*对于可微的光滑曲面而言，我们可以利用微积分方面的知识来求出曲面点处的法线。但问题在于，三角形网格运用一种被称为求顶点法线平均值（vertex normal averaging）的计算方法。*

*此方法通过对网格中共享顶点 v 的多边形的平面法线求取平均值，从而获得网格中任意顶点 v 处的顶点法线 n。*

*例如网格中的四个多边形共用顶点 v，因此，v 处的顶点法线求法如下：*
$$
\vec{n}_{avg} = 
\frac{\vec{n}_0 + \vec{n}_1 + \vec{n}_2 + \vec{n}_3}{|| \vec{n}_0 + \vec{n}_1 + \vec{n}_2 + \vec{n}_3 ||}
$$
*在上面这个例子中，由于我们对求和的结果已进行了规范化处 理，因此便无需像往常求算术平均值那样再除以 4。*

*注意，为了得到更为精准的结果，我们还可以采用更加复杂的求平均值方法，比如说，根据多边形的面积来确定权重（如面积大的多边形的权重要大于面积小的多边形），以求取加权平均值。*

*下列伪代码展示了若给定三角形网格的顶点列表和索引列表，该如何来求取相应的法线平均值。*

```cpp
// 输入
// 1. 一个顶点数组（mVertices）。每个顶点都有一个位置分量（pos）和一个法线分量（normal）
// 2. 一个索引数组（mIndices）
// 对于网格中的每个三角形来说
for(UINT i = 0; i < mNumTriangles; ++i)
{
    // 第 i 个三角形的索引
    UINT i0 = mIndices[i*3+0];
    UINT i1 = mIndices[i*3+1];
    UINT i2 = mIndices[i*3+2];
    // 第 i 个三角形的顶点
    Vertex v0 = mVertices[i0];
    Vertex v1 = mVertices[i1];
    Vertex v2 = mVertices[i2];
    // 计算平面法线
    Vector3 e0 = v1.pos - v0.pos;
    Vector3 e1 = v2.pos - v0.pos;
    Vector3 faceNormal = Cross(e0, e1);
    // 该三角形共享了下面 3 个顶点，所以将此平面法线与这些顶点法线相加以求平均值
    mVertices[i0].normal += faceNormal;
    mVertices[i1].normal += faceNormal;
    mVertices[i2].normal += faceNormal;
}
// 对于每个顶点 v 来说，由于我们已经对所有共享顶点 v 的三角形的平面法线进行求和，所以现在仅需进行规范化处理即可
for (UINT i = 0; i < mNumVertices; ++i)
{
    mVertices[i].normal = Normalize(&mVertices[i].normal)); 
}

```

## ***变换法向量***

![image-20250605211124775](https://raw.githubusercontent.com/vlicecream/cloudImage/main/image-20250605211124775.png)

*思考图 8.7a。图中，切向量 u = v1 – v0 正交于法向量 n。如果对此应用一个非等比缩放变换 A，则可从图 8.7b 看到，变换后的切向量 uA = v1A – v0A 没能与变换后的法向量 nA 继续保持正交性。*

*若给定一个用于变换点与向量（非法线）的变换矩阵 A，如何能够求出这样一个变换矩阵 B：通过它来变换法向量，使经矩阵 A 变换后的切向量与法向量重归正交的关 系（即 uA ⋅  nB = 0 ）。*

*我们首先从已知的信息着手，如果法向量 n 正交于切向量 u，则有：*

1. *切向量正交于法向量*
   $$
   \vec{u} \cdot \vec{n} = 0
   $$

2. *点积改写为矩阵乘法*
   $$
   \vec{u}\vec{n}^T = 0
   $$

3. *插入单位矩阵 $ I = AA^{−1} $*
   $$
   \vec{u}(AA^{-1})\vec{n}^T
   $$

4. *根据矩阵乘法运算的结合律*
   $$
   (\vec{u}A)(A^{-1}\vec{n}^T) = 0
   $$

5. *根据转置矩阵的性质 $ (A^T)^T = A $*
   $$
   (\vec{u}A)((A^{-1}\vec{n}^T)^T)^T = 0
   $$

6. *根据转置矩阵的性质 $ (AB)^T = B^T A^T $*
   $$
   (\vec{u}A)(\vec{n}(A^{-1})^T)^T = 0
   $$

7. *将矩阵乘法改写为点积的形式*
   $$
   \vec{u}A \cdot \vec{n}(A^{-1})^T = 0
   $$

8. *变换后的切向量正交于变换后的法向量*
   $$
   \vec{u}A \cdot \vec{n}B = 0
   $$

*因此，通过 $ B = (A^{−1})^T $（矩阵 A 的逆转置矩阵）对法向量进行变换后，即可使它垂直于经矩阵 A 变换后的切向量 uA。*

*我们可以为计算逆转置矩阵实现一个辅助函数*

```cpp
static XMMATRIX InverseTranspose(CXMMATRIX M)
{
    XMMATRIX A = M;
    A.r[3] = XMVectorSet(0.0f, 0.0f, 0.0f, 1.0f);
    XMVECTOR det = XMMatrixDeterminant(A);
    return XMMatrixTranspose(XMMatrixInverse(&det, A));
} 
```

## ***参与光照计算的一些关键向量***

![image-20250607090543683](https://raw.githubusercontent.com/vlicecream/cloudImage/main/image-20250607090543683.png)

***图8.8***

1. *E 是观察者的观察位置*
2. *这是在观察点 p 处沿着单位向量 v 所定义的视线来进行观察的过程*
3. *位于表面的点 p 处有法线 n，光线由入射方向 I 照射到点 p。*

### ***光向量***

*光向量（light vector）L 为单位向量，其所指方向与照射到表面 上点 p 处入射光线 I 的方向刚好相反。尽管在工作中使用光的入射方向 I 可能更为直观，但是为了进行光照计算，我们还是采用光向量 L*

*向量L 用于计算 $ \vec{L} \cdot \vec{n} = \cos\theta_i $. 其中的 $\cos\theta_i$ 是光向量L与法向量n之间的夹角*

### ***反射向量***

*反射向量 r 是入射光向量 L 关于表面法线 n 的镜像。*

*反射向量的定义为 $ \vec{r} = \vec{I} - 2(\vec{n} \cdot \vec{i} ) \vec{n} $ ，如图 8.9 所示（这里假设 n 为单位向量）。然而，我们在着色器中实际上是利用内置函数 reflect 来计算 r 的。*

### ***观察向量***

*观察向量 $\vec{v} = normalize(E − p) $ 是从表面上的点 p 到观察点 E 方向上的 单位向量，它定义了由观察点向表面点观察的视线。我们有时还要用到向量−v，它是我们所要计算的由观察点到表面点这条光线路径上的单位向量。*

## ***朗伯余弦定律***

### ***辐射通量与辐(射)照度***

*我们可以将光看作是光子的集合，在空间中按特定的方向传播。每个光子都载有（光）能量。光源每秒发出的（光）能量称为辐射通量（radiant flux）。而单位面积上的辐射通量密度（irradiance，称为辐（射） 照度）是一种很重要的概念，因为我们将用它来确定表面某8区域所接收到的光量（即眼睛感受到的明亮度）。*

*一般来讲，我们可以认为辐照度是照射到表面某区域的光量，或者是通过空间中某假想区域的光量。*

### ***公式推导***

![image-20250607094017654](https://raw.githubusercontent.com/vlicecream/cloudImage/main/image-20250607094017654.png)

*如图 8.10a，光线垂直照射到表面（即光向量 L 与法向量 n 相等时）的强度要大于以某个角度照射到表面的强度。*

*如图 8.10a，一小束辐射通量为 P 且横截面面积为 A1的光束，将此光束垂直打向表面，则光束照射到表面上的面积为 $ A_1 $，而  $ A_1 $内的辐照度为 $ E_1 = P/A_1 $*

*现假设转动光源，使光束以某个入射角度照射到表面上（如图 8.10b），则光束将覆于表面上的更大面积 $ A_2 $。此时，该面积的辐照度为 $ E_2 = P/A_2 $。*

*根据三角学可知，$A_1,A_2$ 关系为:*
$$
\begin{align} 
\cos\theta = \frac{A_1}{A_2} \Rightarrow
A_2 \cos\theta = A_1 \Rightarrow
\frac{\cos\theta}{A_1} = \frac{1}{A_2} \Rightarrow
\frac{1}{A_2} = \frac{\cos\theta}{A_1}
\end{align}
$$
*所以:*
$$
E_2 = \frac{p}{A_2} = \frac{p}{A_1}\cos\theta = E_1 \cos\theta = E_1(\vec{n} \cdot \vec{L})
$$
*换句话说，面积A2内的辐照度就相当于将受垂直方向光照的面积A1内的辐照度按比例 $ \vec{n} \cdot \vec{L} = \cos\theta $ 进行缩放，这就是朗伯余弦定律*

*考虑到光线照射到表面另一侧的情况（此时。点积结果为负值），我们用 max 函数来钳制“缩放因子”的取值范围：*
$$
f(\theta) = \max(\cos\theta, 0) = \max(\vec{L} \cdot \vec{n}, 0)
$$
*图 8.11 所示的是函数 f(θ) 的图像。观察可知，随着变量θ 的变化，函数的值域范围为 0.0～1.0（即光照强度的变化范围为 0%～100%）。*

![image-20250607095701919](https://raw.githubusercontent.com/vlicecream/cloudImage/main/image-20250607095701919.png)

## ***漫反射光照***

*当光线照射到表面上的某一点时，一部分光会进入物体的内部，并与表面附近的物质相互作用。这些光会在物体内部四处反弹，其中一部分会被吸收，而余下部分则会向各个方向散射并返回表面，这即是所谓的漫反射（diffuse reflection）。*

### ***漫反射光照的计算***

*我们将漫反射光照的计算分为两个部分。在第一部分中，我们要指定光照颜色以及漫反射反照率（diffuse albedo）颜色。漫反射反照率表示的是根据表面的漫反射率（diffuse reflectance，也译作漫反射比）而被反射的入射光量（根据能量守恒定律，入射光不是被反射回表面就是被材 质吸收了）。*

*要对它们进行处理就需用分量式颜色乘法（因为光是具有颜色的）。例如，假设表面上一点会 反射 50%的入射红光、100%的绿光以及 75%的蓝光，如果这时有一束强度为 80%的白光袭来，那么此入射光的量值就可以表示为 $ B_L = (0.8, 0.8, 0.8) $，又因为漫反射反照率为 $ m_d = (0,5, 1.0, 0.75) $，所以此点的反射光量为：*
$$
c_d = B_L \otimes m_d  = (0.8, 0.8, 0.8) \otimes (0.5,1.0, 0.75) = (0.4, 0.8, 0.6)
$$
*可以发现，漫反射反照率分量的取值范围必定在 0.0～1.0 之间，因此我们用小数来表示反射光。*

*然而，上述公式并非十分准确，我们还需将朗伯余弦定律（根据表面法线与光向量之间的夹角来控 制表面接收原始光照的量）考虑在内。*

*设  $ B_L $表示入射光量，$ m_d $为漫反射反照率，L 为光向量，而 n 为表面法线，则位于表面上某点处的漫反射光量为：*
$$
c_d = \max(\vec{L} \cdot \vec{n}, 0) \cdot B_L \otimes m_d
$$

## ***环境光照***

*假设我们正坐在一间设有一个光源的屋子里，房内的桌上还放有一个茶壶。虽然茶壶只有一侧被光源直接照射，但它的另一侧却不可能完全被笼罩在漆黑的阴影当中。这是因为一部分光经墙壁或室内的其他物体反射，而最终照射到茶壶的背面。*

*为了处理这种间接光照，我们给光照方程引进了一个环境光（ambient light）项：*
$$
c_a = A_L \otimes m_d
$$

1. *颜色 $ A_L $ 指定了表面收到的间接（环境）光量，它可能与光源发出的光量不同，因为光源发射的光在其他表面反射的时候会被吸收一部分。*
2. *漫反射反照率 $ m_d $ 指示了根据表面漫反射率而被表面反射的入射光量。*

## ***镜面光照***

*我们此前用漫反射光照来模拟漫反射的过程：光进入介质，发生反射，部分光被吸收，而剩下的光 则向介质外的各个方向散射。*

*第二种反射的发生是根据一种名为菲涅耳效应（也译作菲涅尔效应）的物理现象。*

*当光线到达两种不同折射率介质之间的界面时，一部分光将被反射，而剩下的光则发生折射。*

*折射率是一种介质的物理性质，即光在真空中传播的速度与光在给定介质内的传播速度之比。我们将第二种光的反射过程称为镜面反射，把被反射的光称为镜面（反射）光*

### ***菲尼尔效应***

*我们来考虑一个具有法线 n 的平滑界面，它将两种不同折射率的介质分隔开来。由于在界面处具有折射率不连续性（因不同介质的折射率差异所导致），当光线照射到界面时，一部分会被界面反射，另一部分则折射进界面。*

*菲涅耳方程（Fresnel equations）以数学方法描述了入射光线被反射的百分比， 即 $ 0 ≤ R_F ≤  1 $。根据能量守恒定律，如果 $ R_F $是反射光量，则 $ (1 − R_F) $为折射光量。$ R_F $的值是一个 RGB 向量， 因为光的颜色反映了反射光量。*

*反射的光量既依赖于介质（某些材质的反射率相对更大），也与法向量 n 与光向量 L 之间的夹角 $ θi $ 有 关。由于光照过程的复杂性，我们一般不会将完整的菲涅耳方程用于实时渲染，而是采用石里克近似法来加以代替：*
$$
R_F(\theta_i) = R_F(0\textdegree) + (1 - R_F(0^\textdegree))(1-\cos\theta_i)^5
$$
*$ R_F(0\textdegree) $ 是介质的一种属性，下面所列的即是一些常见材质的对应属性数值*

![image-20250609223410638](https://raw.githubusercontent.com/vlicecream/cloudImage/main/image-20250609223410638.png)

*我们来看一个现实世界中的例子，现假设我们正置身 于一个水质相对清澈、深达数英尺的小池塘里。若向下俯视，我们基本可以清楚地看到其底部沉积的沙 。这是由于有从周围环境中照射到池水的光以接近于 0.0° 的小角度 $ θ_i $ 反射进我们的眼睛而造成的；如此一来，反射到我们眼中的光量相对较低，又根据能量守恒定律可知，此时的折射光量却很高。现在来换个观察角度，如果我们向稍远处望去，将会看到池水极强的反射光。这是因为有从周围环境中射向水的光以接近 90.0° 的角度 $ θ_i $反射进我们的眼中，从而增加了反射光量。这种现象通常称为菲涅耳效应 。*

***可以将菲涅耳效应简洁地概括为：反射光量取决于材质 $ (R_F(0^\textgress)) $ 以及法线与光向量之间的夹角。***

### ***表面粗糙度***

#### ***粗糙度***

*真实世界中的反射物体往往不是理想镜面（perfect mirror）。尽管一个物体的表面看起来似乎十分平滑，但从微观水平上看，它还是具有一定的粗糙度。*

#### ***镜面瓣***

*随着粗糙度的增加，微观表面法线的方向开始纷纷偏离宏观表面法线， 由此反射光逐渐扩展为一个镜面瓣*

#### ***微平面模型***

*为了用数学方法对粗糙度进行建模，我们采用了微平面（microfacet，也作微表面）模型。在此模型中，我们将微观表面模拟为由多个既微小又平滑的微平面所构成的集合；而微观表面法线正是这些微平面上的法线*

*算上菲尼尔效应和表面粗糙度，获得公式:*
$$
c_s = \max(\vec{L} \cdot \vec{n}, 0) \cdot \vec{B}_L \otimes \vec{R}_F(\alpha_h)\frac{m + 8}{8}(\vec{n} \cdot \vec{h})^m
$$
*可以发现，如果 $ \vec{L} \cdot \vec{n} \leq 0 $ ，那么所计算的结果是射向表面另一侧的光，因而此时的正表面并不会接收到任何光照*

## ***光照模型的概述***

*现在将之前所述的所有光照内容都结合起来，即表面反射的光量相当于环境反射光、漫反射光以及镜面反射光的光量总和。*

1. *环境光 $ c_a $：模拟经表面反射的间接光量。*

2. *漫反射光 $ c_d $：对进入介质内部，又经过表面下吸收而最终散射出表面的光进行模拟。由于对表面下的散射光建模比较困难，我们便假设在表面下与介质相互作用后的光从进入表面处返回， 并向各个方向均匀散射。*
3. *镜面光 $ c_s $：模拟经菲涅耳效应与表面粗糙度共同作用的表面反射光。*

*所以，结合这三个的着色方程出来了:*
$$
\begin{align}
LitColor &= c_a + c_d + c_s \\\ \\
&= A_L \otimes m_d + \max(\vec{L} \cdot \vec{n}, 0) \cdot B_L \otimes \
\end{align}
$$

