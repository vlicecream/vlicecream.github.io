# 向量


# ***向量***

## ***向量的定义***

*向量（vector）是一种兼具大小（也称为模，magnitude）和方向的量。具有这两种属性的量皆 称为向量值物理量（vector-valued quantity）*

*与向量值物理量相关的例子有作用力（在特定方向上施 加的力—力的大小即为向量的模）、位移（质点沿净方向移动的距离）和速度（速率和方向）。这 样一来，向量就能用于表示力、位移和速度*

*另外，有时也用向量单指方向，例如玩家在 3D 游戏 里的视角方向、一个多边形的朝向、一束光线的传播方向以及它照射在某表面后的反射方向等*

### ***几何意义***

*通过图像中的一条有向线段即可表示一个向量， 其中，线段长度代表向量的模，箭头的指向代表向量的方向。我们可以注意到：向量的绘制位置之于其 自身是无足轻重的，因为改变某向量的位置并不会对其大小或方向这两个属性造成任何影响。因此，我 们说：两个向量相等，当且仅当它们的长度相等且方向相同。所以，图中的向量 u 和向量 v 相等， 因为它们的长度相等且方向相同*

*事实上，由于位置对于向量是无关紧要的，所以我们总是能在平移一 个向量的同时又完全不改变它的几何意义（因为平移操作既不影响它的长度，也不改变它的方向）。显而 易见，我们可以将向量 u 完全平移到向量 v 处（反之亦可），使两者完全重合，分毫不差—由此即可证明它们是相等的*

![向量的定义](https://raw.githubusercontent.com/CuteCocoa/MyImage/main/向量的定义.png)

## ***向量与坐标系***

### ***左 /右手坐标系***

*Direct3D 采用的是左手坐标系（left-handed coordinate system）。*

![左右手坐标系](https://raw.githubusercontent.com/CuteCocoa/MyImage/main/左右手坐标系.png)

***同一个向量在不同的坐标系中有着不同的坐标表示***

*对于向量来说，它的方向和模都表现 在对应的有向线段上，不会更改；只有在改变 描述它的参考系时，其坐标才会相应地改变。这一点是很重要的，因为这意味着：**每当我们根据坐标来 确定一个向量时，其对应的坐标总是相对于某一参考系而言的**。在 3D 计算机图形学中，我们通常会用 到较多的参考系。因此，我们需要记录向量在每一种坐标系中的对应坐标。另外，我们也需要知道如何 将向量坐标在不同的标架之间进行转换。*

## ***向量的基本运算***

$$
\vec{u} = (u_x, u_y, u_z) ~ 和 ~ \vec{v} = (v_x, v_y, v_z)
$$

### ***向量相等***

*两个向量相等，当且仅当他们的对应分量相等。即 $ \vec{u} = \vec{v} $ , 当且仅当 $ u_x = v_x，u_y = v_y，u_z = v_z $*

### ***向量相加***

*向量的加法即令两个向量的对应分量分别相加： $ \vec{u} = \vec{v} = ( u_x + v_x, u_y + v_y, u_z + v_z)$*

*注意，只有同维 的向量之间才可以进行加法运算*

### ***向量相减***

*向量减法可以通过向量加法和标量乘法表示：*
$$
\vec{u} - \vec{v} = \vec{u} + (-1 \cdot \vec{v}) = \vec{u} + (-\vec{v}) = (u_x - v_x, u_y - v_y, u_z - v_z)
$$

### ***标量 x 向量***

*向量可以与标量（即实数）相乘，所得到的结果仍是一个向量。*

*例如，设 k 为一个标量，则 $ k\vec{u} = (ku_x, ku_y, ku_z) $。这种运算叫作标量乘法*

## ***零向量***

*有个叫作零向量（zero-vector）的特殊向量，它的所有分量都为 0， 可直接将它简记作 0*

## ***向量的几何意义***

1. *见图 1.6a由此可知，把一个向量的系数变为其相反数，就相当于在几何学中“翻 转”此向量的方向，而且对向量进行标量乘法即为对其长度进行缩放。*

2. *图 1.6b 展示了向量加法运算的几何意义：把 向量 u 进行平移，使 u 的尾部与 v 的头部重合。此时，向量 u 与向量 v 的和即：以 v 的尾部为 起点、以平移后 u 的头部为终点所作的向量（如果令向量 u 的位置保持不变，平移向量 v，使 v 的尾部与 u 的头部重合也能得到同样的结果。在这种情况下，u + v 的和就可以表示为以 u 的尾 部为起点、以平移后 v 的头部为终点所作的向量）。*

   *可以看出，向量的加法运算与物理学中不同 作用力合成合力的规则是一致的。如果有两个力（两个向量）作用在同一方向上，则将在这个 方向上产生更大的合力（更长的向量）；如果有两个力（两个向量）作用于彼此相反的方向上， 那么便会产生更小的合力（更短的向量），如图 1.7 所示*

3. *图 1.6c 展示了向量减法运算的几何意义。从本质 上讲，v − u 的差值仍是一个向量，该向量自 u 的头部始至 v 的头部终。如果我们将 u 和 v 看作两个 点，那么 v − u 得到的是一个从点 u 指向点 v 的向量；*

   *这种解释方式的重点在于使我们找出向量的方 向。同时，不难看出，在把 u 与 v 看作点的时候，v − u 的长度也就是“点 u 到点 v 的距离”*

![向量的几何意义](https://raw.githubusercontent.com/CuteCocoa/MyImage/main/向量的几何意义.png)

## ***长度和单位向量***

### ***长度（模  ）***

*向量大小（亦称为模）的几何意义是对应有向线段的长度，用双竖线表示（例如||u||代表向量 u 的 模）*

*现给出向量 u = (x, y, z)，我们希望用代数的方法计算它的 模。3D 向量的模可通过运用两次勾股定理得出*

![向量的模](https://raw.githubusercontent.com/CuteCocoa/MyImage/main/向量的模.png)
$$
\Vert u \Vert = \sqrt{y^2 + a^2 } = \sqrt{y^2 + (\sqrt x^2 + z^2)^2} = \sqrt{x^2 + y^2 + z^2}
$$

### ***单位向量***

*当向量的模为1的时候他就是单位向量*

### ***向量的规范化***

*在某些情况下，我们并不关心向量的长度，仅用它来表示方向。对此，我们希望使该向量的长度为 1。把一个向量的长度变为单位长度称为向量的规范化（normalizing）处理。具体实现方法是，将向量 的每个分量分别除以该向量的模*
$$
\begin{gather*}
\hat{u} = \frac{\vec{u}}{\Vert \vec{u} \Vert} = (\frac{x}{\Vert \vec{u} \Vert}, \frac{y}{\Vert \vec{u} \Vert}, \frac{z}{\Vert \vec{u} \Vert})
\\\ \\\
为了验证公式的正确性，下面计算 \hat{u} 的长度，也就是模:
\\\ \\\
\Vert \hat{u} \Vert 
= \sqrt{(\frac{x}{\Vert u \Vert})^2 (\frac{x}{\Vert u \Vert})^2 (\frac{z}{\Vert u \Vert})^2}
= \frac{\sqrt{x^2 + y^2 + z^2}}{\sqrt{\Vert u \Vert ^ 2}}
= \frac{\Vert u \Vert}{\Vert u \Vert}
= 1
\\\ \\\
由此可见， \hat{u} 确实是一个单位向量（unit vector）
\end{gather*}
$$

## ***点积***

*点积（dot product，亦称数量积或内积）是一种计算结果为标量值的向量乘法运算，因此有时也称 为标量积（scalar product）。*
$$
\begin{gather*}
设向量 \vec{u} = ( u_x, u_y, u_z),~ \vec{v} = (v_x, v_y, v_z)
\\\ \\\
则点积的定义为:
\vec{u} \cdot \vec{v} = u_x v_x + u_y v_y + u_z v_z
\\\ \\\
可见，点积就是向量间对应分量的乘积之和。
\end{gather*}
$$
*点积的定义并没有明显地体现出其几何意义。但是我们却能根据余弦定理（law of cosines)，找到二向量点积的几何关系*
$$
\vec{u} \cdot \vec{v} = \Vert u \Vert \Vert v \Vert \cos\theta
$$
*其中，$ \theta $ 是向量 $ \vec{u} $与向量 $ \vec{v} $ 之间的夹角，$ 0  \leq \theta \leq \pi $*

*如果向量 u 和向量 v 都是单位向量，那么 u ⋅ v 就等 于两向量夹角的余弦值，即 u⋅ v= cosθ*

### ***点积几何性质***

1. ***如果 u⋅ v = 0 ，那么 u ⊥ v （即两个向量正交）***
2. ***如果 u⋅ v > 0 ，那么两向量之间的夹角 θ 小于 90°（即两向量间的夹角为一锐角）***
3. ***如果 u⋅ v < 0 ，那么两向量之间的夹角 θ 大于 90°（即两向量间的夹角为一钝角）***

### ***正交投影***

*考虑图 1.10。给出向量 v 和单位向量 n，请借助点积公式求出用 v 和 n 表示向量 p 的公式。*

![正交投影](https://raw.githubusercontent.com/CuteCocoa/MyImage/main/正交投影.png)
$$
\begin{align*}
\vec{p} &= k\vec{n} ~ (观察图示存在标量k，使得 \vec{p} = k\vec{n}。 只要都在同一方向 就可以用这个公式)
\\\ \\\
&= (\Vert \vec{v} \Vert \cos\theta)\vec{n} ~ (这是利用三角函数, \cos \theta = \frac{k}{\vec{v}})
\\\ \\\
&= (\Vert \vec{v} \Vert \cdot 1 \cos\theta)\vec{n} ~ (这个 1 其实就是单位向量n)
\\\ \\\
&= (\Vert \vec{v} \Vert \Vert \vec{n} \Vert \cos\theta)\vec{n}
\\\ \\\
&= (\vec{v} \cdot \vec{n})\vec{n}
\end{align*}
$$
*特别是这里证明了：当 n 是单位向量时， k = v ⋅ n*

*顺带也解释了在这种情况下 v ⋅ n的几何意义。我们 称 p 为向量 v 落在向量 n 上的正交投影（orthogonal projection），通常将它表示为：*
$$
\vec{p} = proj_n(\vec{v})
$$
*如果 n 不具有单位长度，就先对它进行规范化处理，使之成为单位向量。通过把向量 n 替换为单位向量 $ \frac{n}{\Vert n \Vert} $，即可得到更具一般性的投影公式：*
$$
\vec{p} 
= proj_n(\vec{v}) 
= (\vec{v} \cdot \frac{\vec{n}}{\Vert \vec{n} \Vert})\frac{\vec{n}}{\Vert \vec{n} \Vert} 
= (\frac{(\vec{v} \cdot \vec{n})}{\Vert \vec{n} \Vert ^ 2}) \vec{n}
$$

## ***正交化***

### ***规范正交***

*如果向量集 $ {v_0, …, v_{n-1}} $中的每个向量都是互相正交（集合内的任一向量都与集合中的其他所有向量 相互正交）且皆具单位长度，那么我们就称此集合是规范正交（orthonormal）的*

*有时我们会接到一个 近乎（但并不完全）规范正交的集合。这时，一个常见的工作就是通过正交化手段，使之成为规范正交集。*

*例如，我们有时会在 3D 计算机图形学中用到规范正交集，但是由于处理过程中数值精度的问题， 它会随之逐步变为非规范正交集。这时就要用到正交化这一手段了*

![正交化](https://raw.githubusercontent.com/CuteCocoa/MyImage/main/正交化.png)

### ***2个向量的正交化***

*先来考察相对简单的 2D 情况吧(也就是说，集合内只有 2 个向量的情况)。假设我们有向量集合$ {v_0, v_1} $，现欲将它正交化为图 1.11 中所示的 正交集${w_0, w_1}$*

首先设 $ w_0 = v_0 $，通过使 $v_1$减去它在 $w_0$上的分量（投影）来令它正交于 $w_0$
$$
\vec{w}_1 = \vec{v}_1 - proj\_{\vec{w}_0}(\vec{v}_1)
$$
*此时，我们便得到了一个元素互相正交的向量集合${w_0, w_1}$；最后一步是构建一个规范正交集，将向量 $w_0$和 $w_1$规范化为单位向量即可。*

### ***3个向量的正交化***

*假设有向量集${v_0, v_1, v_2}$，现希望将它正交化为 正交集${w_0, w_1, w_2}$，过程如图 1.12 所示。*

*首先使 $w_0 = v_0$，通过令 $v_1$减去它在 $w_0$方向上的分量，让它正交 于 $w_0$：*
$$
\vec{w}_1 = \vec{v}\_1 - proj\_{\vec{w}_0}(\vec{v}_1)
$$
*接下来，通过令 $v_2$依次减去它在 $w_0$方向与 $w_1$方向上的分量（投影），使之同时正交于 $w_0、w_1$：*
$$
\vec{w}_2 = \vec{v}\_2 - proj\_{\vec{w}_0}(\vec{v}_2) - proj\_{\vec{w}_1}(\vec{v}_2)
$$
*现在我们就得到了所有元素都彼此正交的向量集${w_0, w_1, w_2}$；最后一步是通过将 $w_0、w_1、 w_2$规范化为单位向量来构建一个规范正交集*

### ***n个向量的正交化(格拉姆—施密特正交化)***

*基本步骤：设$ \vec{w}\_0 = \vec{v}\_0 $*

对于 $ 1  \leq i \leq n-1 $ , 令 $ \vec{w}\_i = \vec{v}\_i -  \sum\_{j=0}^{i-1} \text{proj}\_{\mathbf{w}\_j} (\mathbf{v}\_i) $

## ***叉积***

*向量乘法的第二种形式是叉积（cross product，亦称向量积、外积）。与计算结果为标量的点积不同， 叉积的计算结果亦为向量。此外，只有 3D 向量的叉积有定义（不存在 2D 向量叉积）。*

*假设 3D 向量 u 和 v 的叉积得到的是另一个向量 w，则 w 与向量 u、v 彼此正交。也就是说，向量 w 既正交于 u，也正交于 v*
$$
\begin{gather*}
\vec{u} = (u_x, u_y, u_z),~ \vec{v} = (v_x, v_y, v_z)
\\\ \\\
那么叉积的计算方法是
\\\ \\\
\vec{w} = \vec{u} \times \vec{v} = (u_y v_z - u_z v_y, u_z v_x - u_x v_z, u_x v_y - u_y v_x)
\end{gather*}
$$
*根据计算结果可以明确地得出一项结论：一般来说 $ \vec{u} \times \vec{v} \neq \vec{v} \times \vec{u} $ ，即向量的叉积不满足交换律*

*事实上， 我们同时也能够证明 $ \vec{u} \times \vec{v} = -\vec{v} \times \vec{u} $  ，这正是叉积的反交换律。*

### ***叉积的模长公式***

$$
\Vert \vec{u} \times \vec{v} \Vert = \Vert \vec{u} \Vert \Vert \vec{v} \Vert \sin\theta
$$



### ***2D 向量的伪叉积***

*我们刚刚证明了：通过叉积可以求出与两个指定 3D 向量 正交的向量。在 2D 空间中虽然不存在这种情况，但是若给定 一个 2D 向量 $u = (u_x, u_y)$，我们还是能通过与 3D 向量叉积相似 的方法，求出与 向量u 正交的向量 v*
$$
\vec{u} \cdot \vec{v} = (u_x, u_y) \cdot (-v_y, v_x)
$$

## ***通过叉积来进行正交化处理***

*我们也可以通过叉积来进行正交化处理,但是收到数值精度误差累计的影响，也许会导致成为非规范正交集*

## ***点***

*在一个坐标系中，通 过一个处于标准位置的向量就能表示出 3D 空间中的特定位置，我们称这种向量为位置向 量（position vector）*

*在这种情况下，向量箭头的位置才是值得关注的主要特征，而方向和大小都是无足 轻重的。“位置向量”和“点”这两个术语可以互相替代，这是因为一个位置向量足以确定一个点。*

*然而，用向量表示点也有副作用，在代码中则更为明显，因为部分向量运算对点来说是没有意义的。 例如，两点之和的意义何在？但从另一方面来讲，一些运算却可以在点上得到推广*

*可以将两个点 的差 q−p 定义为由点 p 指向点 q 的向量。*

*同样，也可以定义点 p 与向量 v 相加，其意义为：令点 p 沿向量 v 位移而得到点 q。*

## ***小结***

1. *向量可以用来模拟同时具有大小和方向的物理量。*

   *在几何学上，我们用有向线段表示向量。当 向量平移至尾部与所在坐标系原点恰好重合的位置时，向量位于标准位置。一旦向量处于标准 位置，我们便可以用向量头部相对于坐标系的坐标来作为它的数学描述*

2. *假设有向量 $\vec{u} = (u_x, u_y, u_z)$ 和向量 $ \vec{v} = (v_x, v_y, v_z) $，那么就能对他们进行下列向量计算*

   *（a）加法运算：*
   $$
   \begin{gather*}
   \vec{u} + \vec{v} = (u_x + v_x, u_y + v_y, u_z + v_z) ~~~~~ 公式
   \\\ \\\
   \vec{u} + \vec{v} = \vec{v} + \vec{u} ~~~~~ 满足交换律
   \\\ \\\
   (\vec{u} + \vec{v}) + \vec{w} = \vec{u} + (\vec{v} + \vec{w}) ~~~~~ 满足结合律
   \\\ \\\
   (\vec{u} + \vec{v})k = k\vec{u} + k\vec{v} ~~~~~ 满足与标量相乘的分配律
   \end{gather*}
   $$

   *（b） 减法运算：*
   $$
   \begin{gather*}
   \vec{u} - \vec{v} = (u_x - v_x, u_y - v_y, u_z - v_z) ~~~~~ 公式
   \\\ \\\
   \vec{u} - \vec{v} \neq \vec{v} - \vec{u} ~~~~~ 不满足交换律 ~ 除非 \vec{u} = \vec{v}
   \\\ \\\
   (\vec{u} - \vec{v}) - \vec{w} \neq \vec{u} - (\vec{v} - \vec{w}) ~~~~~ 不满足结合律 ~ 除非\vec{w} 是零向量
   \\\ \\\
   (\vec{u} - \vec{v})k = k\vec{u} - k\vec{v} ~~~~~ 满足与标量相乘的分配律
   \end{gather*}
   $$
   *（c）标量乘法运算：*
   $$
   k\vec{u} = (ku_x, ku_y, ku_z)
   $$
   *（d） 向量长度（模）:* 
   $$
   \Vert \vec{u} \Vert = \sqrt{x^2 + y^2 + z^2}
   $$
   *（e） 规范化：*
   $$
   \hat{u} = \frac{\vec{u}}{\Vert \vec{u} \Vert} = (\frac{x}{\Vert \vec{u} \Vert}, \frac{y}{\Vert \vec{u} \Vert}, \frac{z}{\Vert \vec{u} \Vert})
   $$
   *（f） 点积：*

   *点积的一个重要几何应用是计算两个向量之间的夹角*

   *点积可以利用正交投影公式来一个向量在另一个向量方向上的投影*

   *点积可以判断两条直线或两个平面是否垂直*
   $$
   \begin{gather*}
   \vec{u} \cdot \vec{v} = \Vert u \Vert \Vert v \Vert \cos\theta = u_x v_x + u_u v_y + u_z v_z ~~~~~ 公式
   \\\ \\\
   \vec{u} \cdot \vec{v} = \vec{v} \cdot \vec{u} ~~~~~ 满足交换律
   \\\ \\\
   (\vec{u} \cdot \vec{v}) \cdot \vec{w} = \vec{u} \cdot (\vec{v} \cdot \vec{w}) ~~~~~ 满足分配律
   \\\ \\\
   (k \vec{u}) \cdot \vec{v} = \vec{u} \cdot (k\vec{v}) ~~~~~ 满足标量的结合律
   \\\ \\\
   (\vec{u} \cdot \vec{v}) \cdot \vec{w} \neq \vec{u} \cdot (\vec{v} \cdot \vec{w}) 
   ~~~~~
   不满足 点积与点积的结合律 这是因为点积的结果是一个标量，而标量与向量的点积没有定义。
   \end{gather*}
   $$
   *（g） 叉积：* 

   *叉积的结果向量垂直于 向量u 和 向量v 所在的平面，因此常用于计算平面的法向量。*

   *叉积的模长等于以 向量u 和 向量v 为邻边的平行四边形的面积  || u x v ||*

   *叉积的方向由右手定则确定，常用于判断向量的旋转方向。*

   *在物理学中，叉积常用于计算力矩、角动量等与旋转相关的物理量。*
   $$
   \begin{gather*}
   \vec{w} = \vec{u} \times \vec{v} = (u_y v_z - u_z v_y, u_z v_x - u_x v_z, u_x v_y - u_y v_x) ~~~~~ 公式
   \\\ \\\
   \Vert \vec{u} \times \vec{v} \Vert = \Vert \vec{u} \Vert \Vert \vec{v} \Vert \sin\theta 
   ~~~~~~ 
   叉积的模长公式
   \\\ \\\
   \vec{u} \times \vec{v} = \vec{v} \times \vec{u} ~~~~~ 满足交换律
   \\\ \\\
   \vec{u} \times \vec{v} = -\vec{v} \times \vec{u} ~~~~~ 反交换律
   \\\ \\\
   \vec{u} \times (\vec{v} + \vec{w}) = \vec{u} \times \vec{c} + \vec{u} \times \vec{v} ~~~~~ 满足分配律
   \\\ \\\
   k\vec{u} \times \vec{v} = \vec{u} \times k\vec{v} ~~~~~~ 满足标量的结合律
   \\\ \\\
   (\vec{u} \times \vec{v}) \times\vec{w} \neq \vec{u} \times (\vec{v} \times\vec{w}) ~~~~~ 不满足叉积与叉积的结合律
   \end{gather*}
   $$

3. *[向量的几何意义](#向量的几何意义)*

4. *当向量的模为1的时候他就是单位向量*

5. *[点积几何性质](#点积几何性质)*

6. *正交投影*
   $$
   \vec{p} 
   = proj_n(\vec{v}) 
   = (\vec{v} \cdot \frac{\vec{n}}{\Vert \vec{n} \Vert})\frac{\vec{n}}{\Vert \vec{n} \Vert} 
   = (\frac{(\vec{v} \cdot \vec{n})}{\Vert \vec{n} \Vert ^ 2}) \vec{n}
   $$

7. *向量的正交化其实就是一个递归，在将给定集合内的向量 $ \vec{v\_i} $添加到规范正交集中时，我们需要令 $ \vec{v\_i} $减去它 在现有规范正交集中其他向量$ {w_0, w_1, …, w_i−1} $方向上的分量（投影）*

   *基本步骤：设$ \vec{w}\_0 = \vec{v}\_0 $*

   对于 $ 1  \leq i \leq n-1 $ , 令 $ \vec{w}\_i = \vec{v}\_i -  \sum\_{j=0}^{i-1} \text{proj}\_{\mathbf{w}\_j} (\mathbf{v}\_i) $

## ***作业***

### ***题目大纲***

1. *设向量 u = (1, 2)和向量 v = (3, –4)。写出下列各式的演算过程，并在 2D 坐标系内画出相应的向量*

2. *设向量 u = (−1, 3, 2)和向量 v = (3, −4, 1)。写出下列问题的解答过程*

3. *证明式子*

   *(a) $ \vec{u} + \vec{v} =  \vec{v} + \vec{u} $ （加法交换律）*

   *(b) $ \vec{u} + (\vec{v} + \vec{w}) = (\vec{u} + \vec{v}) + \vec{w} $ （加法结合律）*

   *(c) $ (ck)\vec{u} = c(k\vec{u}) $ （标量乘法的结合律）*

   *(d) $ k(\vec{u} + \vec{v}) = k\vec{u} + k\vec{v} $ （分配律 1）*

   *(e) $ \vec{u}(k + c) = k\vec{u} + c\vec{u} $ （分配律 2）*

4. *根据等式  "2[(1, 2, 3) − x] − (−2, 0, 4) = −2(1, 2, 3)"，求其中的向量 x。*

5. *设向量 u = (−1, 3, 2)和向量 v = (3, −4, 1)。对 u 和 v 进行规范化处理。*

6. *设 k 为标量，$向量 u = (u_x, u_y, u_z)。求证||ku|| = ||k|| ||u||$*

7. *下列各组向量中，u 与 v 之间的夹角是直角、锐角还是钝角？*

   *（a）u = (1, 1, 1)，v = (2, 3, 4)*

   *（b）u = (1, 1, 0)，v = (−2, 2, 0)*

   *（c）u = (−1, −1, −1)，v = (3, 1, 0)*

8. *设向量 u = (−1, 3, 2)和向量 v = (3, −4, 1)。计算 u 和 v 之间的夹角 θ*

9. *设向量 $ u = (u_x, u_y, u_z)、v = (v_x, v_y, v_z)和 w = (w_x, w_y, w_z)，且 c 和 k 为标量。证明下列点积性质。*

   *（a）$ \vec{u} \cdot \vec{v} = \vec{v} \cdot \vec{u} $*

   *（b）$ \vec{u} \cdot (\vec{v} + \vec{w}) = \vec{u} \cdot \vec{v} + \vec{u} \cdot \vec{w} $*

   *（c）$ k(\vec{u} \cdot \vec{v}) = (k\vec{u}) \cdot \vec{v} = \vec{u} \cdot (k\vec{v}) $*

   *（d）$ \vec{v} \cdot \vec{v} = ||v||^2 $*

   *（e）$ 0 \cdot \vec{v} = 0 $*

10. *利用余弦定理 （ $  c^2 = a^2 + b^2 - 2ab \cos\theta $ ，其中 a、b、c 分别是三角形 3 条边的边长， θ 为 a 与 b 之间的夹角）来证明：$ u_x v_x + u_y v_y + u_z v_z = ||u|| ||v|| \cos\theta $*

11. *设向量 n = (−2, 1)。将向量 g = (0, −9.8) 分解为两个相互正交的向量之和，使它们一个平行于 n、 一个正交于 n。最后，在同一 2D 坐标系中画出这些向量。*

12. *设向量 u = (−2, 1, 4)和向量 v = (3, −4, 1)。求向量 w  = u × v ，再证明 w⋅ u= 0 及 w ⋅ v= 0 。*

13. *设 A = (0, 0, 0)，B = (0, 1, 3)和 C = (5, 1, 0)三点在某坐标系中定义了一个三角形。求出一正交于此三角形的向量。*

14. *证明 $ \Vert \vec{u} \times \vec{v} \Vert = \Vert \vec{u} \Vert \Vert \vec{v} \Vert \sin\theta $*

15. *证明：由向量 u 和向量 v 张成的平行四边形面积为|| u × v || ，如图 1.21 所示。*

    ![向量第十五题](https://raw.githubusercontent.com/vlicecream/cloudImage/main/向量第十五题.png)

16. *举例证明：存在 3D 向量 u、v 和 w，满足 u x (v x w) ≠ (u x v) x w 。这说明叉积一般不满足结合律。*

17. *证明两个非零且相互平行向量的叉积为零向量，即 $ \vec{u} \times k\vec{u} = 0 $。*

18. *利用格拉姆—施密特正交化方法，令向量集 {(1, 0, 0), (1, 5, 0), (2, 1, −4)} 规范正交化。*

### ***第一题***

*设向量 u = (1, 2)和向量 v = (3, –4)。写出下列各式的演算过程，并在 2D 坐标系内画出相应的向量*
$$
\begin{align*}
\vec{u} + \vec{v} 
&= (u_x + v_x, u_y + v_y) 
\\\ \\\
&= (1 + 3, 2 + (-4))
\\\ \\\
&= (4, -2)
\end{align*}
$$

$$
\begin{align*}
\vec{u} - \vec{v} 
&= (u_x - v_x, u_y - v_y) 
\\\ \\\
&= (1 - 3, 2 - (-4))
\\\ \\\
&= (-2, 6)
\end{align*}
$$

$$
\begin{align*}
2\vec{u} + \frac{1}{2} \vec{v} 
&= (2u_x, 2u_y) + (\frac{1}{2} v_x, \frac{1}{2} v_y)
\\\ \\\
&= (2u_x + \frac{1}{2} v_x, 2u_y + \frac{1}{2} v_y)
\\\ \\\
&= (2 + 1.5, 4 + (-2))
\\\ \\\
&= (3.5, 2)
\end{align*}
$$

$$
\begin{align*}
-2\vec{u} + \vec{v} 
&= (-2u_x, -2u_y) + (v_x,v_y)
\\\ \\\
&= (-2u_x + v_x, -2u_y, v_y)
\\\ \\\
&= (-2 + 3, -4 + -4)
\\\ \\\
&= (1, -8)
\end{align*}
$$



### ***第二题***

*设向量 u = (−1, 3, 2)和向量 v = (3, −4, 1)。写出下列问题的解答过程*
$$
\begin{align*}
\vec{u} + \vec{v} 
&= (u_x + v_x, u_y + v_y, u_z + v_z) 
\\\ \\\
&= (-1 + 3, 3 + (-4), 2 + 1)
\\\ \\\
&= (2, -1, 3)
\end{align*}
$$

$$
\begin{align*}
\vec{u} - \vec{v} 
&= (u_x - v_x, u_y - v_y, u_z - v_z)
\\\ \\\
&= (-1 - 3, 3 - (-4), 2 - 1)
\\\ \\\
&= (-4, 7, 1)
\end{align*}
$$

$$
\begin{align*}
3\vec{u} + 2\vec{v}
&= (3u_x, 3u_y, 3u_z) + (2v_x, 2v_y, 2v_z)
\\\ \\\
&= (-3, 9, 6) + (6, -8, 2)
\\\ \\\
&= (-3 + 6, 9 + (-8), 6 + 2)
\\\ \\\
&= (3, 1, 8)
\end{align*}
$$

$$
\begin{align*}
-2\vec{u} + \vec{v}
&= (-2u_x, -2u_y, -2u_z) + (v_x, v_y, v_z)
\\\ \\\
&= (2, -6, -4) + (3, -4, 1)
\\\ \\\
&= (2 + 3, -6 + (-4), -4 + 1)
\\\ \\\
&= (5, -10, -3)
\end{align*}
$$



### ***第三题***

*(a) $ \vec{u} + \vec{v} =  \vec{v} + \vec{u} $ （加法交换律）*
$$
\vec{u} + \vec{v} 
= (u_x + v_x, u_y + v_y, u_z + v_z) 
= (v_x + u_x, v_y + u_y, v_z + u_z) 
= \vec{v} + \vec{u}
$$
*(b) $ \vec{u} + (\vec{v} + \vec{w}) = (\vec{u} + \vec{v}) + \vec{w} $ （加法结合律）*
$$
\begin{align*}
\vec{u} + (\vec{v} + \vec{w}) 
&= (u_x, u_y, u_z) + (v_x + w_x, v_y + w_y, v_z + w_z) 
\\\ \\\
&= (u_x + (v_x + w_x), u_y + (v_y + w_y), u_z + (v_z + w_z))
\\\ \\\
&= ((u_x + v_x) + w_x, (u_y + v_y) + w_y, (u_z + v_z) + w_z)
\\\ \\\
&= (u_x + v_x, u_y + v_y, u_z + v_z) + (w_x, w_y, w_z) 
\\\ \\\
&= (\vec{u} + \vec{v}) + \vec{w}
\end{align*}
$$
*(c) $ (ck)\vec{u} = c(k\vec{u}) $ （标量乘法的结合律）*
$$
\begin{align*}
(ck)\vec{u}
&= (ck)(u_x, u_y, u_z)
\\\ \\\
&= ((ck)u_x, (ck)u_y, (ck)u_z)
\\\ \\\
&= (c(ku_x), c(ku_y), c(ku_z))
\\\ \\\
&= c(k\vec{u})
\end{align*}
$$
*(d) $ k(\vec{u} + \vec{v}) = k\vec{u} + k\vec{v} $ （分配律 1）*
$$
\begin{align*}
k(\vec{u} + \vec{v}) 
&= k(u_x + v_x, u_y + v_y, u_z + v_z)
\\\ \\\
&= (ku_x + kv_x, ku_y + kv_y, ku_z + kv_z)
\\\ \\\
&= k\vec{u} + k\vec{v}
\end{align*}
$$
*(e) $ \vec{u}(k + c) = k\vec{u} + c\vec{u} $ （分配律 2）*
$$
\begin{align*}
\vec{u}(k + c)
&= (u_x(k+c), u_y(k+c), u_z(k+c))
\\\ \\\
&= (ku_x + cu_x, ku_y + cu_y, ku_z + cu_z)
\\\ \\\
&= k\vec{u} + c\vec{u}
\end{align*}
$$

### ***第四题***

*根据等式  "2[(1, 2, 3) − x] − (−2, 0, 4) = −2(1, 2, 3)"，求其中的向量 x。*
$$
\begin{align*}
2[(1, 2, 3) − \vec{x}] − (−2, 0, 4) &= -2(1, 2, 3)
\\\ \\\
[(2, 4, 6) - 2(\vec{x})] - (-2, 0, 4) &= (-2, -4, -6)
\\\ \\\
[(2, 4, 6) - 2(\vec{x})] &= (-2, -4, -6) + (-2, 0, 4)
\\\ \\\
[(2, 4, 6) - 2(\vec{x})] &= (-4, -4, -2)
\\\ \\\
\- 2(\vec{x}) &= (-4, -4, -2) - (2, 4, 6)
\\\ \\\
2(\vec{x}) &= - (-6, -8, -8)
\\\ \\\
\vec{x} &= (3, 4, 4)
\end{align*}
$$

### ***第五题***

*设向量 u = (−1, 3, 2)和向量 v = (3, −4, 1)。对 u 和 v 进行规范化处理。*
$$
\begin{align*}
\vec{u} 
= \frac{\vec{u}}{\Vert \vec{u} \Vert}
= \frac{(u_x, u_y, u_z)}{\sqrt{u_x^2 + u_y^2 + u_z^2}}
= \frac{(-1, 3, 2)}{\sqrt{1 + 9 + 4}}
= \frac{(-1, 3, 2)}{\sqrt{14}}
= (\frac{-1}{\sqrt{14}}, \frac{3}{\sqrt{14}}, \frac{2}{\sqrt{14}})
\\\ \\\
\vec{v} 
= \frac{\vec{v}}{\Vert \vec{v} \Vert}
= \frac{(v_x, v_y, v_z)}{\sqrt{v_x^2 + v_y^2 + v_z^2}}
= \frac{(3, -4, 1)}{\sqrt{9 + 16 + 1}}
= \frac{(3, -4, 1)}{\sqrt{26}}
= (\frac{3}{\sqrt{26}}, \frac{-4}{\sqrt{26}}, \frac{1}{\sqrt{26}})
\end{align*}
$$

### ***第六题***

*设 k 为标量，$向量 u = (u_x, u_y, u_z)。求证||ku|| = ||k|| ||u||$*
$$
\begin{align*}
||ku||
&= ||ku_x, ku_y, ku_z||
\\\ \\\
&= \sqrt{(ku_x)^2 + (ku_y)^2 + (ku_z)^2}
\\\ \\\
&= \sqrt{k^2u_x^2 + k^2u_y^2 + k^2u_z^2}
\\\ \\\
&= \sqrt{k^2 + (u_x^2, u_y^2, u_z^2)}
\\\ \\\
&= \sqrt{k^2} + \sqrt{\vec{u}^2}
\\\ \\
&= ||k|| + ||\vec{u}||
\end{align*}
$$

### ***第七题***

*下列各组向量中，u 与 v 之间的夹角是直角、锐角还是钝角？*

*（a）u = (1, 1, 1)，v = (2, 3, 4)*
$$
\begin{gather*}
\vec{u} \cdot \vec{v} = ||u|| ||v|| \cos\theta
\\\ \\\
\cos\theta = \frac{\vec{u} \cdot \vec{v}}{||u|| ||v||}
\\\ \\\
\cos\theta = \frac{u_x v_x + u_y v_y + u_z v_z}{\sqrt{u_x^2 + u_y^2 + u_z^2} \cdot \sqrt{v_x^2 + v_y^2 + v_z^2}}
\\\ \\\
\cos\theta = \frac{2 + 3 + 4}{\sqrt{3} \cdot \sqrt{29}}
\\\ \\\
\cos\theta = \frac{9}{\sqrt{87}}
\\\ \\\
因\cos\theta > 0, 故为锐角
\\\ \\\
如果想求出角度 则可以 θ=arccos(\frac{9}{\sqrt{87}})≈15.8^\circ
\end{gather*}
$$

*（b）u = (1, 1, 0)，v = (−2, 2, 0)*
$$
\begin{gather*}
\vec{u} \cdot \vec{v} = ||u|| ||v|| \cos\theta
\\\ \\\
\cos\theta = \frac{\vec{u} \cdot \vec{v}}{||u|| ||v||}
\\\ \\\
\cos\theta = \frac{u_x v_x + u_y v_y + u_z v_z}{\sqrt{u_x^2 + u_y^2 + u_z^2} \cdot \sqrt{v_x^2 + v_y^2 + v_z^2}}
\\\ \\\
\cos\theta = \frac{-2 + 2 + 0}{\sqrt{2} \cdot \sqrt{8}}
\\\ \\\
\cos\theta = \frac{0}{\sqrt{16}} = 0
\\\ \\\
因\cos\theta = 0, 故为直角
\end{gather*}
$$
*（c）u = (−1, −1, −1)，v = (3, 1, 0)*
$$
\begin{gather*}
\vec{u} \cdot \vec{v} = ||u|| ||v|| \cos\theta
\\\ \\\
\cos\theta = \frac{\vec{u} \cdot \vec{v}}{||u|| ||v||}
\\\ \\\
\cos\theta = \frac{u_x v_x + u_y v_y + u_z v_z}{\sqrt{u_x^2 + u_y^2 + u_z^2} \cdot \sqrt{v_x^2 + v_y^2 + v_z^2}}
\\\ \\\
\cos\theta = \frac{(-3) + (-1) + 0}{\sqrt{3} \cdot \sqrt{10}}
\\\ \\\
\cos\theta = \frac{-4}{\sqrt{30}}
\\\ \\\
因\cos\theta < 0, 故为钝角
\end{gather*}
$$

### ***第八题***

*设向量 u = (−1, 3, 2)和向量 v = (3, −4, 1)。计算 u 和 v 之间的夹角 θ*
$$
\begin{gather*}
\vec{u} \cdot \vec{v} = ||u|| ||v|| \cos\theta
\\\ \\\
\cos\theta = \frac{\vec{u} \cdot \vec{v}}{||u|| ||v||}
\\\ \\\
\cos\theta = \frac{u_x v_x + u_y v_y + u_z v_z}{\sqrt{u_x^2 + u_y^2 + u_z^2} \cdot \sqrt{v_x^2 + v_y^2 + v_z^2}}
\\\ \\\
\cos\theta = \frac{(-3) + (-12) + 2}{\sqrt{14} \cdot \sqrt{26}}
\\\ \\\
\cos\theta = \frac{-13}{\sqrt{2\sqrt{91}}}
\\\ \\\
θ=arccos(\frac{-13}{2\sqrt{91}})≈123.7^\circ
\end{gather*}
$$

### ***第九题***

*设向量 $ u = (u_x, u_y, u_z)、v = (v_x, v_y, v_z)和 w = (w_x, w_y, w_z)，且 c 和 k 为标量。证明下列点积性质。*

*（a）$ \vec{u} \cdot \vec{v} = \vec{v} \cdot \vec{u} $*
$$
\begin{align*}
\vec{u} \cdot \vec{v}
&= (u_x, u_y, u_z) + (v_x, v_y, v_z)
\\\ \\\
&= (u_x v_x + u_y v_y + u_z v_z)
\\\ \\\
&= (v_x, v_y, v_z) + (u_x, u_y, u_z)
\\\ \\\
&= \vec{v} \cdot \vec{u}
\end{align*}
$$
*（b）$ \vec{u} \cdot (\vec{v} + \vec{w}) = \vec{u} \cdot \vec{v} + \vec{u} \cdot \vec{w} $*
$$
\begin{align*}
\vec{u} \cdot (\vec{v} + \vec{w}) 
&= (u_x, u_y, u_z) \cdot ((v_x + w_x, v_y + w_y, v_z + w_z))
\\\ \\\
&= u_x(v_x + w_x) + u_y(v_y + w_y) + u_z(v_z + w_z)
\\\ \\\
&= (u_x v_x + u_x w_x) + (u_y v_y + u_y w_y) + (u_z v_z + u_z w_z)
\\\ \\\
&= (u_x v_x + u_y v_y + u_z v_z) + (u_x w_x + u_y w_y + u_z w_z)
\\\ \\\
&= \vec{u} \cdot \vec{v} + \vec{u} \cdot \vec{w}
\end{align*}
$$
*（c）$ k(\vec{u} \cdot \vec{v}) = (k\vec{u}) \cdot \vec{v} = \vec{u} \cdot (k\vec{v}) $*
$$
\begin{align*}
k(\vec{u} \cdot \vec{v})
&= k(u_x v_y + u_y v_y + u_z v_z)
\\\ \\\
&= (ku_x)v_x + (ku_y)v_y + (ku_z)v_z
\\\ \\\
&= (k\vec{u}) \cdot \vec{v} 
\\\ \\\
&= u_x(kv_x) + u_y(kv_y) + u_z(kv_z)
\\\ \\\
&= \vec{u} \cdot (k\vec{v})
\end{align*}
$$
*（d）$ \vec{v} \cdot \vec{v} = ||v||^2 $*
$$
\begin{align*}
\vec{v} \cdot \vec{v}
&= v_x v_x + v_y v_y + v_z v_z
\\\ \\\
&= v_x^2 + v_y^2 + v_z^2
\\\ \\\
&= \sqrt{v_x^2 + v_y^2 + v_z^2}^2
\\\ \\\
&= ||v||^2
\end{align*}
$$
*（e）$ 0 \cdot \vec{v} = 0 $*
$$
\begin{align*}
0 \cdot \vec{v} 
&= 0v_x + 0v_y + 0v_z
\\\ \\\
&= 0 + 0 + 0
\\\ \\\
&= 0
\end{align*}
$$

### ***第十题***

*利用余弦定理 （ $  c^2 = a^2 + b^2 - 2ab \cos\theta $ ，其中 a、b、c 分别是三角形 3 条边的边长， θ 为 a 与 b 之间的夹角）来证明：$ u_x v_x + u_y v_y + u_z v_z = ||u|| ||v|| \cos\theta $*
$$
\begin{gather*}
c^2 = a^2 + b^2 - 2ab \cos\theta
\\\ \\\
||w||^2 = ||u||^2 + ||v||^2 - 2||u|| ||v|| \cos\theta ~~~~~~ (因为 c a b都是各边对应的模)
\\\ \\\
||w||^2 = (\vec{u} - \vec{v})^2 = ||u||^2 + ||v||^2 - 2(\vec{u} \cdot \vec{v}) ~~~~~ (点积分配律)
\\\ \\\
||u||^2 + ||v||^2 - 2||u|| ||v|| \cos\theta = ||u||^2 + ||v||^2 - 2(\vec{u} \cdot \vec{v}) ~~~~~ (||w||^2 = ||w||^2)
\\\ \\\
\- 2||u|| ||v|| \cos\theta = - 2(\vec{u} \cdot \vec{v})
\\\ \\\
||u|| ||v|| \cos\theta = \vec{u} \cdot \vec{v}
\end{gather*}
$$

### ***第十一题***

*设向量 n = (−2, 1)。将向量 g = (0, −9.8) 分解为两个相互正交的向量之和，使它们一个平行于 n、 一个正交于 n。最后，在同一 2D 坐标系中画出这些向量。*
$$
\begin{align*}
\vec{g_{||}} 
&= proj_n(\vec{g}) 
\\\ \\\
&= (\vec{g} \cdot \frac{\vec{n}}{\Vert \vec{n} \Vert})\frac{\vec{n}}{\Vert \vec{n} \Vert} 
\\\ \\\
&= (\frac{(\vec{g} \cdot \vec{n})}{\Vert \vec{n} \Vert ^ 2}) \vec{n}
\\\ \\\
&= (\frac{(g_x n_x + g_y n_y)}{\sqrt{n_x^2 + n_y^2}}) (-2, 1)
\\\ \\\
&= (\frac{(0 + (-9.8))}{\sqrt{5}})(-2, 1)
\\\ \\\
&= −1.96(−2,1)
\\\ \\\
&= (-3.92, −1.96)
\\\ \\\
\vec{g}_\perp &= \vec{g} - \vec{g_{||}}
\\\ \\\
&=  (0, −9.8) − (3.92, −1.96) 
\\\ \\\
&= (−3.92, −7.84) 
\end{align*}
$$
![向量第十一题](https://raw.githubusercontent.com/vlicecream/cloudImage/main/向量第十一题.png)

### ***第十二题***

*设向量 u = (−2, 1, 4)和向量 v = (3, −4, 1)。求向量 w  = u × v ，再证明 w⋅ u= 0 及 w ⋅ v= 0 。*
$$
\begin{align*}
\vec{w} = \vec{u} \times \vec{v} &= (u_y v_z - u_z v_y, u_z v_x - u_x v_z, u_x v_y - u_y v_x)
\\\ \\\
&= (1 - (-16), 12 - (-2), 8 - 3)
\\\ \\\
&= (17, 14, 5)
\\\ \\\
\vec{w} \cdot \vec{u} &= (w_x u_x + w_y u_y + w_z u_z)
\\\ \\\
&= (-34 + (14) + (20))
\\\ \\\
&= 0
\\\ \\\
\vec{w} \cdot \vec{v} &= (w_x v_x + w_y v_y + w_z v_z)
\\\ \\\
&= (51 + (-56) + 5)
\\\ \\\
&= 0
\end{align*}
$$

### ***第十三题***

*设 A = (0, 0, 0)，B = (0, 1, 3)和 C = (5, 1, 0)三点在某坐标系中定义了一个三角形。求出一正交于此三角形的向量。*
$$
\begin{align*}
\vec{AB} &= B - A = (0 - 0, 1 - 0, 3 - 0) = (0, 1, 3)
\\\ \\\
\vec{AC} &= C - A = (5 - 0, 1 - 0, 0 - 0) = (5, 1, 0)
\\\ \\\
\vec{ABC_\perp} &= \vec{AB} \times \vec{AC}
\\\ \\\
&= (AB_y AC_z - AB_z AC_y, AB_z AC_x - AB_x AC_z, AB_x AC_y - AB_y AC_x)
\\\ \\\
&= (0 - 3, 15 - 0, 0 - 5)
\\\ \\\
&= (-3, 15, -5)
\end{align*}
$$

### ***第十四题***

*证明 $ \Vert \vec{u} \times \vec{v} \Vert = \Vert \vec{u} \Vert \Vert \vec{v} \Vert \sin\theta $*
$$
\begin{gather*}
\Vert \vec{u} \times \vec{v} \Vert = \Vert \vec{u} \Vert \Vert \vec{v} \Vert \sin\theta
\\\ \\\
\Vert \vec{u} \times \vec{v} \Vert = (u_y v_z - u_z v_y, u_z v_x - u_x v_z, u_x v_y - u_y v_z)
\\\ \\\
\Vert \vec{u} \times \vec{v} \Vert^2 = (u_y v_z - u_z v_y)^2 + (u_z v_x - u_x v_z)^2 + (u_x v_y - u_y v_z)^2
\\\ \\\
\Vert \vec{u} \times \vec{v} \Vert^2 
= (u_y^2 v_z^2 - 2u_yv_zu_zv_y + u_z^2 v_y^2) 
+ (u_z^2 v_x^2 - 2u_zv_xu_xv_z + u_x^2 v_z^2) 
+ (u_x^2 v_y^2 - 2u_xv_yu_yv_x + u_y^2 v_x^2)
\\\ \\\
\Vert \vec{u} \Vert^2 \Vert \vec{v} \Vert^2 - (\vec{u} \cdot\vec{v})^2
= (u_y^2 v_z^2 + u_z^2 v_y^2) 
+ (u_z^2 v_x^2 + u_x^2 v_z^2) 
+ (u_x^2 v_y^2 + u_y^2 v_x^2)
\- 2(u_yv_zu_zv_y + u_zv_xu_xv_z + u_xv_yu_yv_x)
\\\ \\\
\Vert \vec{u} \Vert^2 \Vert \vec{v} \Vert^2 - (\vec{u} \cdot\vec{v})
\end{gather*}
$$

### ***第十五题***

*证明：由向量 u 和向量 v 张成的平行四边形面积为 || u x v || ，如图 1.21 所示。*

![向量第十五题](https://raw.githubusercontent.com/vlicecream/cloudImage/main/向量第十五题.png)

$$
A = \Vert \vec{v} \Vert h
\\\ \\\
h = \Vert \vec{u} \Vert \sin\theta
\\\ \\\
A = \Vert \vec{u} \Vert \Vert \vec{v} \Vert \sin\theta
\\\ \\\
A = \Vert \vec{u} \times \vec{v} \Vert
$$

### ***第十六题***

*举例证明：存在 3D 向量 u、v 和 w，满足 u x (v x w) ≠ (u x v) x w 。这说明叉积一般不满足结合律。*
$$
\begin{gather*}
设 \vec{u} = (1, 0, 0), \vec{v} = (1, 1, 0), \vec{w} = (0, 1, 1)
\\\ \\\
\vec{u} \times (\vec{v} \times \vec{w}) = \vec{u} \times (1, -1, 1) = (0, -1, -1)
\\\ \\\
(\vec{u} \times \vec{v}) \times \vec{w} = (0, 0, 1) \times \vec{w} = (-1, 0, 0)
\\\ \\\
故 \vec{u} \times (\vec{v} \times \vec{w}) \neq (\vec{u} \times \vec{v}) \times \vec{w}
\end{gather*}
$$

### ***第十七题***

*证明两个非零且相互平行向量的叉积为零向量，即 $ \vec{u} \times k\vec{u} = 0 $。*
$$
\begin{align*}
\vec{u} \times k\vec{u} 
&= (u_y ku_z - u_z ku_y, u_z ku_x - u_x ku_z, u_x ku_y - u_y ku_x ) 
\\\ \\\
&= k(u_y u_z - u_z u_y, u_z u_x - u_x u_z, u_x u_y - u_y u_x)
\\\ \\\
&= k(0, 0, 0)
\\\ \\\
&= (0, 0, 0)
\end{align*}
$$


### ***第十八题***

*利用格拉姆—施密特正交化方法，令向量集 {(1, 0, 0), (1, 5, 0), (2, 1, −4)} 规范正交化。*

