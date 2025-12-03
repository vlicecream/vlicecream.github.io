# 复数与四元数


## ***复数***

### ***复数的定义***

*任意复数 $z \in \mathbb{C}$都可以表示为 $z = a + bi$ 的形式，其中 $a, b \in \mathbb{R}$ 而且 $i^2 = 1$。我们将 a 称之为这个复数的实部，b 称之为这个复数的虚部*

*因为 $z = a + bi$ 其实就是对于 { 1, i } 这个基的线性组合，我们也可以用向量来表示一个复数：*
$$
z = \begin{bmatrix}
a \\\ \\\ b
\end{bmatrix}
$$
*因为这个向量有两个元素，我们可以使用复平面上的一个点来表示一个复数。复平面的横坐标 Re 代表他的实部，纵坐标 Im 代表他的虚部*

![复数坐标轴](https://raw.githubusercontent.com/vlicecream/cloudImage/main/%E5%A4%8D%E6%95%B0%E5%9D%90%E6%A0%87%E8%BD%B420251114153431840.png)

### ***复数的性质***

#### ***复数加减法***

*如果我们有两个复数 $z_1 = a + bi，z_2 = c + di$，他们的和就是分量相加的结果*
$$
z_1 + z_2 = (a + c) + (b + d)i
$$
*同理，如果要对他们相减，直接将分量相减就可以了*
$$
z_1 - z_2 = (a - c) + (b - d)i
$$

#### ***复数乘法***

*如果我们有两个复数 $z_1 = a + bi，z_2 = c + di$，我们可以用分配律来计算他们的乘积*
$$
\begin{align*}
z_1 z_2 &= (a + bi)(c + di)
\\\ \\\
&= ac + adi + bci + bdi^2
\end{align*}
$$
*因为 i^2 = -1，这可以进一步简化为：*
$$
\begin{align*}
z_1 z_2 &= ac - bd + adi + bci
\\\ \\\
&= ac - bd + (bc + ad)i
\end{align*}
$$
*如果仔细观察你就能法线，复数相乘的结果其实也是一个矩阵与向量相乘的结果，也就是说：*
$$
\begin{align*}
z_1 z_2 &= ac - bd + (bc + ad)i
\\\ \\\
&= \begin{bmatrix} a & -b \\\ \\\ b & a \end{bmatrix}
\begin{bmatrix} c \\\ \\\ d \end{bmatrix}
\end{align*}
$$
*右侧的 $\begin{bmatrix} c \\\ \\\ d \end{bmatrix}$ 是用向量的实行来表示的 $z_2$，而左侧的$ \begin{bmatrix} a & -b \\\ \\\ b & a \end{bmatrix} $ 则是 $z_1$ 的矩阵形式。*

*那么，在矩阵形式下，复数与复数的相乘也可以表示为矩阵的相乘，如果我们有两个复数 $z_1 = a + bi，z_2 = c + di$，那么与 $z_1 z_2$所代表的变换则可以表示为：*
$$
\begin{align*}
z_1 z_2 &= \begin{bmatrix} a & -b \\\ \\\ b & a \end{bmatrix} \begin{bmatrix} c & -d \\\ \\\ d & c \end{bmatrix}
\\\ \\\
&= \begin{bmatrix} ac - bd & -(bc + ad) \\\ \\\ bc + ad & ac - bd \end{bmatrix}
\end{align*}
$$
*注意，复数的相乘是满足交换律的，如果你自己尝试一下，就会发现 $z_1 z_2$与$z_2 z_1$是等价的：*
$$
\begin{align*}
z_2 z_1 &= \begin{bmatrix} c & -d \\\ \\\ d & c \end{bmatrix} \begin{bmatrix} a & -b \\\ \\\ b & a \end{bmatrix}
\\\ \\\
&= \begin{bmatrix} ac - bd & -(bc + ad) \\\ \\\ bc + ad & ac - bd \end{bmatrix} = z_1 z_2
\end{align*}
$$
*除此之外，我们来看一下一些特殊复数的矩阵形式：*
$$
1 = \begin{bmatrix} 1 & 0 \\\ \\\ 0 & 1 \end{bmatrix} = I \quad \quad \quad \quad \quad \quad (a = 1, b = 0)
\\\ \\\ \\\ \\\
i = \begin{bmatrix} 0 & -1 \\\ \\\ 1 & 0 \end{bmatrix} \quad \quad \quad \quad \quad \quad (a = 0, b = 1)
$$
*可以看到，实数单位 1 与单位矩阵是等价的。而虚数单位 i 则等价与 $\begin{bmatrix} 0 & -1 \\\ \\\ 1 & 0 \end{bmatrix}$*

*如果我们尝试对他进行平方，可以发现：*
$$
i^2 = i \cdot i = \begin{bmatrix} 0 & -1 \\\ \\\ 1 & 0 \end{bmatrix} \begin{bmatrix} 0 & -1 \\\ \\\ 1 & 0 \end{bmatrix} = \begin{bmatrix} -1 & 0 \\\ \\\ 0 & -1 \end{bmatrix} = -I = -1;
$$

#### ***复数的模长***

*如果 $z = a + bi$，那么他的模长为：*
$$
||z|| = \sqrt{a^2 + b^2}
$$

#### ***复数的共轭***

*如果 $z = a + bi$，那么他的共轭为：*
$$
\bar{z} = a - bi;
$$

#### ***模长与共轭之关系***

*一个复数的模长又可以通过乘积的方式进行计算：*
$$
||z|| = \sqrt{z \bar{z}}
$$

### ***复数相乘与2D旋转***

*既然与复数的相乘代表着 $\begin{bmatrix} a & -b \\\ \\\ b & a \end{bmatrix}$ 矩阵所做出的变换，那这种变换代表着什么呢？*

***复数的相乘其实是旋转与缩放变换的复合***

*如果有一个复数 $z = a + bi$，那么 z 与任意一个复数 c 相乘都会将 c 逆时针旋转 $\theta = atan2(b, a)$度，并将其缩放 $||z|| = \sqrt{a^2 + b^2}$ 倍*

*如果我们想让 2D 空间中任意一个向量 v 旋转 $\theta$ 度，那么我们就可以使用这个矩阵对 v 进行变换：*
$$
v' = \begin{bmatrix} \cos(\theta) & -\sin(\theta) \\\ \\\ \sin(\theta) & \cos(\theta) \end{bmatrix} v
$$
*注意，其实 $\begin{bmatrix} \cos(\theta) & -\sin(\theta) \\\ \\\ \sin(\theta) & \cos(\theta) \end{bmatrix}$，这个旋转矩阵如果写成复数形式的话就是 $\cos(\theta) + i \sin(\theta)$*

*如果我们将向量 $v = \begin{bmatrix} x \\\ \\\ y \end{bmatrix}$看作是一个复数 $v = x + yi$，其中实部为 x，虚部为 y。那么，我们可以构造一个复数 $z = \cos(\theta) + i \sin(\theta)$，并将它与 $v$ 相乘来进行旋转。旋转 $\theta$度之后的向量 $v'$ 可以用等价的复数乘法来表示：*
$$
v' = zv = (\cos(\theta) + i\sin(\theta))v
$$

#### ***复数的极坐标型***

*根据欧拉公式：*
$$
\cos(\theta) + i \sin(\theta) = e^{i\theta}
$$
*有了这个等式，我们能将复数表示为：*
$$
\begin{align*}
z &= ||z|| \begin{bmatrix} \cos(\theta) & -\sin(\theta) \\\ \\\ \sin(\theta) & \cos(\theta) \end{bmatrix}
\\\ \\\
&= ||z|| (\cos(\theta) + i \sin(\theta))
\\\ \\\
&= ||z|| e^{i \theta}
\end{align*}
$$
*如果我们定义 $r = ||z||$，我们就得到了复数的极坐标形式：*
$$
z = r e^{i \theta}
$$
*现在复数的定义就与实部与虚部的两个分量 a，b 无关了，我们可以使用一个缩放因子 r 和旋转角度 $\theta$的形式来定义任意一个复数，而且她旋转与缩放的性质仍然存在。*

*如果我们想对 2D 空间中向量 $ v = \begin{bmatrix} x \\\ \\\ y \end{bmatrix}$进行旋转并缩放，我们可以类似地将这个向量看作是一个复数 $v = x + yi$，那么，经过旋转 $\theta$ 度，缩放 r 倍之后的向量 v' 就可以这样计算：*
$$
v' = re^{i\theta}v
$$
*如果仅旋转 $\theta$ 度的话，可以令缩放因子 r = 1，那么变换后的结果就是：*
$$
v' = e^{i \theta}v
$$
*这三种 2D 旋转公式其实都是等价的，根据不同的需求我们可以使用旋转的不同形态*

### ***总结：三种 2D旋转公式***

1. *2D 旋转公式（矩阵型）*
   $$
   v' = \begin{bmatrix} \cos(\theta) & -\sin(\theta) \\\ \\\ \sin(\theta) & \cos(\theta) \end{bmatrix} v
   $$

2. *2D 旋转公式（复数积型）*
   $$
   v' = zv = (\cos(\theta) + i\sin(\theta))v
   $$

3. *2D 旋转公式（指数型）*
   $$
   v' = e^{i\theta}v
   $$

### ***旋转的复合***

*当我们对两个 2D 旋转进行复合时，所得到的变换 $z_{net}$ 仍是一个旋转，而且与施加的次序无关*

*这个等效变换的旋转角是 $z_1$ 与 $z_2$旋转角之和*

## ***三维空间的旋转***

### ***引言***

*表示三维空间中旋转的方法有很多种，但我们这里关注的是轴角式 (Axisangle) 的旋转。欧拉角的旋转很常用，但是有万向节死锁问题。*

*使用的坐标系是右手坐标系*

*在轴角的表示方法中，一个旋转的定义需要使用到四个变量：旋转轴 u 的 x，y，z 坐标，以及一个旋转角 $\theta$，这咋比欧拉角还多出来一个呢？实际上，任何三维中的旋转只需要三个自由度就可以定义了*

*在三维空间中定义一个方向只需要用到两个量就可以了（与任意两个坐标轴之间的夹角）。最简单的例子就是地球经纬度，我们可以靠经纬度两个变量就可以定义地球任何一个方位。但是如果我们要表达方位上特定的一个点，就需要加上海拔这个第三个变量*

*我们通常都在说旋转是绕着一个向量在旋转，其实就是绕着向量的方向在旋转，所以他的大小（长度）我们是不关心的。所以为了消除这个长度变量，我们可以将长度转化成一个单位向量。故我们从4个自由度就变成 3个自由度了*

### ***旋转的分解***

*首先，我们可以将 v 分解为平行于旋转轴 u 以及正交（垂直）于 u 的两个分量。$v\_{\parallel}$ 和 $v\_{\perp}$，即：*
$$
v = v_{\parallel} + v_{\perp}
$$
*我们可以分别旋转这两个分向量，再将他们旋转的结果相加获得旋转后的向量*
$$
v' = v'\_{\parallel} + v'\_{\perp}
$$
![分解示意图](https://raw.githubusercontent.com/vlicecream/cloudImage/main/image-20251115103841859.png)

*可以看到，$v_{\parallel}$ 其实就是 v 在 u 上的正交投影 ，根据正交投影的公式，我们可以得出：*
$$
\begin{align*}
v_{\parallel} &= proj_u(v)
\\\ \\\
&= \frac{u \cdot v}{u \cdot u} u
\\\ \\\
&= \frac{u \cdot v}{||u||^2} u \quad \quad \quad \quad \quad \quad (||u||^2 = u \cdot u)
\\\ \\\
&= (u \cdot v)u \quad \quad \quad \quad \quad \quad (||u|| = 1)
\end{align*}
$$
*因为 $v = v_{\parallel} + v_{perp}$，我们可以得到：*
$$
v_{\perp} = v - v_{\parallel} = v - (u \cdot v) u
$$

#### ***$v_{\parallel}$ 的旋转***

*首先，我们来看一下 $v_{parallel}$ 的旋转。这种情况其实非常简单，从之前的图示中就可以看到，$v_{\parallel}$其实根本就没有旋转，仍然与旋转轴重合，所以：*

*3D旋转公式（向量型，平行情况）*
$$
v' = v'_{\parallel}
$$

#### ***$v_{\perp}$ 的旋转***

*接下来我们需要处理正交于 u 的 $v_{perp}$。因为这两个向量是正交的，这个旋转可以看做是平面内的一个旋转，因为旋转不改变 $v_{perp}$ 的长度，所以路径是一个圆。下面是这个旋转的示意图，右侧的为俯视图*

![$v_{\perp}$ 的旋转](https://raw.githubusercontent.com/vlicecream/cloudImage/main/v的旋转-20251115130221553.png)

*在2D的旋转上我们只有一个向量 $v_{perp}$，用他来表示一个旋转是不够的，所以我们造了一个同时正交于 u 和 $v_{\perp}$ 的向量 w，这个可以通过叉乘来获得：*
$$
w = u \times v_{\perp}
$$
*注意叉乘的顺序，因为叉乘不支持交换律。*

*因为 $||u|| = 1$（之前说了 向量 u 转化为单位向量），我们可以发现：*
$$
\begin{align*}
\Vert v \Vert &= \Vert u \times v_{\perp} \Vert
\\\ \\\
&= \Vert u \Vert \cdot \Vert v_{\perp} \Vert \cdot \sin(\pi / 2) \quad \quad \quad \quad \quad \quad (\pi / 2 是u与v_{\perp}的夹角)
\\\ \\\
&= \Vert v_{\perp} \Vert
\end{align*}
$$
*也就是说，w 和 $v_{\perp}$ 的模长是相同的，所以他们在同一个圆上。我们现在可以把 $v'\_{\perp}$ 投影到 w 和 $v_{\perp}$，将其分解为 $v'\_v$ 和 $v'\_w$。使用一点三角学的知识我们就能得到：*
$$
\begin{align*}
v'\_{\perp} &= v'\_v + v'\_w
\\\ \\\
&= \cos(\theta)v_{\perp} + \sin(\theta)w
\\\ \\\
&= \cos(\theta)v_{\perp} + \sin(\theta)(u \times v_{\perp})
\end{align*}
$$
*这也是完成了旋转第二步，我们可以得到这样一个定理：*

*当 $v_{\perp}$ 正交于旋转轴 u 时，旋转 $\theta$角度之后的 $v'\_{\perp}$ 为：*
$$
v'\_{\perp} = \cos(\theta)v_{\perp} + \sin(\theta)(u \times v_{\perp})
$$

#### ***v的旋转***

*将上面两个结果组合就可以获得：*
$$
\begin{align*}
v' &= v'\_{\parallel} + v'\_{\perp}
\\\ \\\
&= v_{\parallel} + \cos(\theta)v_{\perp} + \sin(\theta)(u \times v_{\perp})
\end{align*}
$$
*因为叉乘遵守分配律：*
$$
\begin{align*}
u \times v_{\perp} &= u \times (v - v_{\parallel})
\\\ \\\
&= u \times v - u \times v_{\parallel}
\\\ \\\
&= u \times v \quad \quad \quad \quad \quad \quad (u 平行于 v_{\perp}，所以 u \times v_{\perp} = 0)
\end{align*}
$$
*最后，将 $v_{\parallel} = (u \cdot v)u$ 与 $v_{\perp} = v - (u \cdot v)u$ 代入：*
$$
\begin{align*}
v' &= (u \cdot v)u + \cos(\theta)(v - (u \cdot v)u) + \sin(\theta)(u \times v)
\\\ \\\
&= \cos(\theta)v + (1 - \cos(\theta))(u \cdot v) u +\sin(\theta)(u \times v)
\end{align*}
$$
*这样我们就得到了一般形式的旋转公式：*
$$
v' = \cos(\theta)v + (1 - \cos(\theta))(u \cdot v) u +\sin(\theta)(u \times v)
$$
*马上就能看到四元数跟上面公式的联系了*

### ***总结：轴角式的 3D旋转公式***

1. *3D 旋转公式（向量型，正交情况）*

   *当 $v_{\parallel}$ 平行于旋转轴 u 时，旋转 $\theta$ 角度之后的 $v'\_{\parallel}$ 为：*
   $$
   v'\_{\parallel} = v_{\parallel}
   $$

2. *3D 旋转公式（向量型，正交情况）*

   *当 $v_{\perp}$正交于旋转轴 u 时，旋转 $\theta$ 角度之后的 $v'\_{\parallel}$为：*
   $$
   v'\_{\perp} = \cos(\theta)v_{\perp} + \sin(\theta)(u \times v_{\perp})
   $$

3. *3D 旋转公式（向量型，一般情况，也叫做「Rodrigues’ Rotation Formula」*

   *3D空间中任意一个 v 沿着单位向量 u 旋转 $\theta$ 角度之后的 v' 为：*
   $$
   v' = \cos(\theta)v + (1 - \cos(\theta))(u \cdot v) u +\sin(\theta)(u \times v)
   $$
   

## ***四元数***

### ***什么是四元数***

*四元数的定义和复数非常相似，唯一的区别就是复数只有一个虚数，而四元数有3个虚数。所有的四元数$q \in \mathbb{H}$都可以写成下面这种形式：*
$$
q = a + bi + cj + dk \quad \quad \quad \quad \quad \quad (a, b, c, d \in \mathbb{R})
$$
*其中：*
$$
i^2 = j^2 = k^2 = ijk = -1
$$
*与复数类似，因为四元数其实就是对于基{1, i, j, k}的线性组合，四元数也可以写成向量的形式：*
$$
q = \begin{bmatrix} a \\\ \\\ b \\\ \\\ c \\\ \\\ d \end{bmatrix}
$$
*除此之外，我们经常将四元数的实部与虚部分开，并用一个三维的向量来表示虚部，将它表示为标量和向量的有序对形式*
$$
q = [s, v] \quad \quad \quad \quad \quad \quad (v = \begin{bmatrix} x \\\ \\\ y \\\ \\\ z \end{bmatrix}, s, x,y, z \in \mathbb{R})
$$

### ***定义与性质***

#### ***模长（范数）***

*仿照复数的定义，我们可以暂时将一个四元数 q = a + bi + cj + dk 的模长（或者说范数）定义为：*
$$
\Vert q \Vert = \sqrt{a^2 + b^2 + c^2 + d^2}
$$
*如果用标量向量有序对的形式进行表示的话，$q = [s, v]$的模长为：*
$$
\Vert q \Vert = \sqrt{s^2 + \Vert v \Vert ^2} = \sqrt{s^2 + v \cdot v}
$$
*显然，四元数的模长很难用几何的方法来进行理解，因为它代表的是一个四维的长度．但是，和高维向量的模长一样，这只是类比复数模长进行衍生定义的结果，你只需要将它理解为一个定义就可以了*

#### ***四元数加减法***

*与复数类似，四元数的加法只需要将分量相加就可以了。*

*如果我们有两个四元数 $q_1 = a + bi + cj + dk，q_2 = e + fi + gi +hk$，那么他们的和为：*
$$
\begin{align*}
q_1 + q_2 &= a + bi + cj + dk + e + fi + gi + hk 
\\\ \\\
&= (a+e) + (b+f)i + (c+g)j + (d + h)k
\end{align*}
$$
*减法也是同理，只要将加号改为减号就可以了*
$$
q_1 - q_2 = (a - e) + (b - f)i + (c - g)j + (d - h)k
$$
*如果四元数是以标量向量有序对形式定义的，比如说 $q_1 = [s, \mathbf{v}]，q_2 = [t, \mathbf{u}]$，那么：*
$$
q_1 \pm q_2 = [s \pm t, \mathbf{v} \pm \mathbf{u}]
$$

#### ***标量乘法***

*如果我们有一个四元数 q = a + bi + cj + dk 和一个标量 s，那么他们的乘积为：*
$$
\begin{align*}
sq &= s(a + bi + cj + dk)
\\\ \\\
&= sa + sbi + scj + sdk
\end{align*}
$$
*四元数与标量相乘是遵守交换律的，也就是说 sq = qs*

#### ***四元数乘法***

*四元数之间的乘法比较特殊，他们是不遵守交换律的，也就是说一般情况下 $q_1q_2 \neq q_2q_1$。这也就有了左乘右乘的区别。结合律和分配律在四元数中都是成立的*

*如果有两个四元数 $q_1 = a + bi + cj + dk$ 和 $q_2 = e + fi + gj + hk$，那么他们的乘积为：*
$$
\begin{align*}
q_1 q_2 = &(a + bi + cj + dk)(e + fi + gj + hk)
\\\ \\\
= & ae + afi + agj + ahk  +
\\\ \\\
& bei + bfi^2 + bgij + bhik +
\\\ \\\
& cej + cfji + cgj^2 + chjk +
\\\ \\\
& dek + dfki + dgkj + dhk^2
\end{align*}
$$
*这样乘法最终的结果显然非常凌乱，但是我们可以根据 $i^2 = j^2 = k^2 = ijk = -1$这个定义来化简：*
$$
\begin{align*}
ijk &= -1
\\\ \\\
iijk &= -i \quad \quad \quad \quad \quad \quad (等式两边同时左乘以i)
\\\ \\\
-jk &= -i \quad \quad \quad \quad \quad \quad (ii = i^2 = -1)
\\\ \\\
jk &= i
\end{align*}
$$
*同理：*
$$
\begin{align*}
ijk &= -1
\\\ \\\
ijkk &= -k \quad \quad \quad \quad \quad \quad (等式两边同时右乘以k)
\\\ \\\
-ij &= -k
\\\ \\\
ij &= k
\end{align*}
$$
*利用 ij = k 这个公式，我们可以继续推导：*
$$
\begin{align*}
ij &= k
\\\ \\\
ijj &= kj \quad \quad \quad \quad \quad \quad (等式两边同时右乘以j)
\\\ \\\
-i &= kj
\\\ \\\
kj &= -i
\end{align*}
$$


<table style="border-collapse: collapse; text-align: center; color: #EAEAEA;">
    <thead>
        <tr style="background-color: #495057;">
            <th style="border: 1px solid #6c757d; padding: 8px;">&times;</th>
            <th style="border: 1px solid #6c757d; padding: 8px;">1</th>
            <th style="border: 1px solid #6c757d; padding: 8px;"><em>i</em></th>
            <th style="border: 1px solid #6c757d; padding: 8px;"><em>j</em></th>
            <th style="border: 1px solid #6c757d; padding: 8px;"><em>k</em></th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <th style="border: 1px solid #6c757d; padding: 8px; background-color: #495057;">1</th>
            <td style="border: 1px solid #6c757d; padding: 8px;">1</td>
            <td style="border: 1px solid #6c757d; padding: 8px;"><em>i</em></td>
            <td style="border: 1px solid #6c757d; padding: 8px;"><em>j</em></td>
            <td style="border: 1px solid #6c757d; padding: 8px;"><em>k</em></td>
        </tr>
        <tr>
            <th style="border: 1px solid #6c757d; padding: 8px; background-color: #495057;"><em>i</em></th>
            <td style="border: 1px solid #6c757d; padding: 8px;"><em>i</em></td>
            <td style="border: 1px solid #6c757d; padding: 8px;">-1</td>
            <td style="border: 1px solid #6c757d; padding: 8px; background-color: #8B3A3A;"><em>k</em></td>
            <td style="border: 1px solid #6c757d; padding: 8px; background-color: #3A5F8B;">-<em>j</em></td>
        </tr>
        <tr>
            <th style="border: 1px solid #6c757d; padding: 8px; background-color: #495057;"><em>j</em></th>
            <td style="border: 1px solid #6c757d; padding: 8px;"><em>j</em></td>
            <td style="border: 1px solid #6c757d; padding: 8px; background-color: #8B3A3A;">-<em>k</em></td>
            <td style="border: 1px solid #6c757d; padding: 8px;">-1</td>
            <td style="border: 1px solid #6c757d; padding: 8px; background-color: #8B7500;"><em>i</em></td>
        </tr>
        <tr>
            <th style="border: 1px solid #6c757d; padding: 8px; background-color: #495057;"><em>k</em></th>
            <td style="border: 1px solid #6c757d; padding: 8px;"><em>k</em></td>
            <td style="border: 1px solid #6c757d; padding: 8px; background-color: #3A5F8B;"><em>j</em></td>
            <td style="border: 1px solid #6c757d; padding: 8px; background-color: #8B7500;">-<em>i</em></td>
            <td style="border: 1px solid #6c757d; padding: 8px;">-1</td>
        </tr>
    </tbody>
</table>

*表格最左列中一个元素右乘以顶行中一个元素的结果就位于这两个元素行列 的交叉处．比如说 𝑗𝑖 = −𝑘．用颜色标记的格子代表着乘法交换律不成立．*

*利用这个表格，我们进一步化简四元数乘积的结果：*
$$
\begin{align*}
q_1 q_2 = &(a + bi + cj + dk)(e + fi + gj + hk)
\\\ \\\
= & ae + afi + agj + ahk  +
\\\ \\\
& bei + bfi^2 + bgij + bhik +
\\\ \\\
& cej + cfji + cgj^2 + chjk +
\\\ \\\
& dek + dfki + dgkj + dhk^2
\\\ \\\
= & (ae - bf - cg - dh) +
\\\ \\\
& (be + af - dg + ch)i +
\\\ \\\
& (ce + df + ag +bh)j +
\\\ \\\
& (de - cf + bg + ah)k
\end{align*}
$$

#### ***矩阵形式***

*可以看到，四元数的相乘其实也是一个线性组合，我们同样可以将它写成矩阵的形式*
$$
q_1q_2 = \begin{bmatrix} a & -b & -c & -d \\\ \\\ b & a & -d & c \\\ \\\ c & d & a & -b \\\ \\\ d & -c & b & a \end{bmatrix}
\begin{bmatrix} e \\\ \\\ f \\\ \\\ g \\\ \\\ h \end{bmatrix}
$$
*因为四元数不符合交换律，所以在下面给出右乘 $q_1$ 的变换矩阵：*
$$
q_2q_1 = \begin{bmatrix} a & -b & -c & -d \\\ \\\ b & a & d & -c \\\ \\\ c & -d & a & b \\\ \\\ d & c & -b & a \end{bmatrix}
\begin{bmatrix} e \\\ \\\ f \\\ \\\ g \\\ \\\ h \end{bmatrix}
$$

#### ***Graßmann 积***

*对任意四元数 $q_1 = [s, \mathbf{v}]$，$q_2 = [t, \mathbf{u}]$，$q_1 q_2$的结果是：*
$$
q_1 q_2 = [st - \mathbf{v} \cdot \mathbf{u}, s\mathbf{u} + t\mathbf{v} + \mathbf{v} \times \mathbf{u}]
$$
*如果你还记得之前推导 3D 旋转公式时的结果，你应该就能注意到上面这个定理会成为将四元数与旋转联系起来的关键．*

#### ***纯四元数***

*如果一个四元数能写成这种形式：$v = [0, \mathbf{v}]$，那我们则称 v 为一个纯四元数，即仅有虚部的四元数。*

*因为纯四元数仅由虚部的3D向量决定，我们可以将任意的3D向量转为纯四元数。*

*纯四元数有一个很重要的特性：如果有两个纯四元数 $v = [0, \mathbf{v}]，u = [0, \mathbf{u}]$，那么：*
$$
\begin{align*}
vu &= [0 - \mathbf{v} \cdot \mathbf{u}, 0 + \mathbf{v} \times \mathbf{u}]
\\\ \\\
& = [-\mathbf{v} \cdot \mathbf{u}, \mathbf{v} \times \mathbf{u}]
\end{align*}
$$

#### ***逆和共轭***

*因为四元数是不遵守交换律的，我们通常不会将两个四元数相除写为 $\frac{p}{q}$ 的 形式．取而代之的是将乘法的逆运算定义为 $𝑝𝑞^{−1}$ 或者 $𝑞^{−1}𝑝$，注意它们的结果一般是不同的．*

*其中，$q^{-1}$是q的逆，我们规定：*
$$
q q^{-1} = q^{-1}q = 1 \quad \quad \quad \quad \quad \quad (q \neq 0)
$$
*这也就是说：*
$$
(pq)q^{-1} = p(qq^{-1}) = p \cdot 1 = p
\\\ \\\
q^{-1}(qp) = (q^{-1}q)p = 1 \cdot p = p
$$
*所以，右乘 q 的逆运算为右乘 $q^{-1}$，左乘 q的逆运算为左乘$q^{-1}$，这个与矩阵的性质非常相似。*

*我们定义，一个四元数 $q = a + bi + cj + dk$的共轭为 $q^\* = a - bi - cj - dk$。如果用标量向量有序对的形式来定义的话，$q = [s，\mathbf{v}]$的共轭为 $q^\* = [s, -\mathbf{v}]$*

*共轭四元数一个非常有用的性质就是：*
$$
\begin{align*}
qq^* &= [s, \mathbf{v}] \cdot [s, -\mathbf{v}]
\\\ \\\
&= [s^2 - \mathbf{v} \cdot (-\mathbf{v}), s(-\mathbf{v}) + s\mathbf{v} + \mathbf{v} \times (-\mathbf{v})]
\\\ \\\
&= [s^2 + \mathbf{v} \cdot \mathbf{v}, 0] \quad \quad \quad \quad \quad \quad (\mathbf{v} 平行于 -\mathbf{v}, 所以 \mathbf{v} \times (-\mathbf{v}) = 0)
\\\ \\\
&= s^2 + x^2 + y^2 + z^2
\\\ \\\
&= \Vert q \Vert^2
\end{align*}
$$
*因为 $(q^\*)\* = [s, -(-\mathbf{v})] = [s, \mathbf{v}] = q$*
$$
\begin{align*}
q^\*q &= (q^\*)(q^\*)*
\\\ \\\
&= \Vert q^* \Vert^2
\\\ \\\
&= s^2 + x^2 + y^2 + z^2
\\\ \\\
&= \Vert q \Vert^2
\\\ \\\
&= qq^*
\end{align*}
$$


*所以我们得到，$q^\*q = qq^\*$．这个特殊的乘法是遵守交换律的．*

*如果还记得之前四元数逆的定义：*
$$
\begin{align*}
qq^{-1} &= 1
\\\ \\\
q^\*qq^{-1} &= q^\* \quad \quad \quad \quad \quad \quad (等式两边同时左乘以q^\*)
\\\ \\\
(q^\*q)q^{-1} &= q^\*
\\\ \\\
\Vert q \Vert^2 \cdot q^{-1} &= q^\* \quad \quad \quad \quad \quad \quad (q^\*q = \Vert q \Vert^2)
\\\ \\\
q^{-1} = \frac{q^\*}{\Vert q \Vert^2}
\end{align*}
$$
*用这种办法寻找一个四元数的逆会非常高效，我们只需要将一个四元数的虚部改变符号，除以它模长的平方就能获得这个四元数的逆了．*

*如果 $\Vert q \Vert = 1$， 也就是说 𝑞 是一个单位四元数 (Unit Quaternion)，那么：*
$$
q^{-1} = \frac{q^\*}{1^2} = q^\*
$$

### ***四元数的几个定理***

#### ***定理1***

*如果 $q = [\cos(\theta), \sin(\theta)\mathbf{u}]$，而且$\mathbf{u}$ 为单位向量，那么 $q^2 = qq = [\cos(2\theta), \sin(2\theta)\mathbf{u}]$*

*这个定理的几何意义就是，如果绕着同一个轴 $\mathbf{u}$ 连续旋转 $\theta$ 度两次，那么所作出的变换等同于直接绕着 $\mathbf{u}$ 旋转 20度*

#### ***定理2***

*假设 $v\_{\parallel} = [0, v\_{\parallel}]$是一个纯四元数，而 $q = [\alpha, \beta\mathbf{u}]$ ，其中 $\mathbf{u}$ 是一个单位向量，$\alpha, \beta \in \mathbb{R}$。在这种条件下，如果 $v_{\parallel}$平行于 $\mathbf{u}$，那么 $qv_{\parallel} = v_{\parallel}q$*

#### ***定理3***

*假设$p_{\perp} = [0, \mathbf{v_{\perp}]}$是一个纯四元数，而 $q = [\alpha, \beta\mathbf{u}]$，其中 $\mathbf{u}$ 是一个单位向量，$\alpha, \beta \in \mathbb{R}$。在这种条件下，如果$v_{\perp}$正交于 $\mathbf{u}$，那么 $qv_{\perp} = v_{\perp}q\*$*

#### ***定理4***

*对任意四元数 $q_1 = [s, \mathbf{v}], q_2 = [t, \mathbf{u}]$：*
$$
q_1^* q_2^* = (q_2q_1^*)
$$

### ***四元数与 3D 旋转***

#### ***前言***

*我们需要将一个向量$\mathbf{v}$沿着一个用单位向量所定义的旋转轴$\mathbf{u}$旋转$\theta$度，那么我们可以将这个向量$\mathbf{v}$拆分为正交于旋转轴的$v\_{\perp}$以及平行于旋转轴的$v\_{\parallel}$。我们可以对这两个分向量分别进行旋转，获得$v'\_{\perp}$和$v'\_{\parallel}$。将他们相加就是 $\mathbf{v}$ 旋转之后的结果 $v' = v'\_{\parallel} + v'_{\perp}$*

*我们可以将这些向量定义为纯四元数：*
$$
\begin{gather*}
v = [0, \mathbf{v}] \quad \quad \quad \quad \quad \quad v' = [0, \mathbf{v'}]
\\\ \\\
v_{\perp} = [0, \mathbf{v_{\perp}}] \quad \quad \quad \quad \quad \quad v'\_{\perp} = [0, \mathbf{v'\_{\perp}}]
\\\ \\\
v_{\parallel} = [0, \mathbf{v_{\parallel}}] \quad \quad \quad \quad \quad \quad v'\_{\parallel} = [0, \mathbf{v'\_{\parallel}}]
\\\ \\\
u = [0, \mathbf{u}]
\end{gather*}
$$
*那么我们就能得到：*
$$
v = v_{\parallel} + v_{\perp} \quad \quad \quad \quad \quad \quad v' = v'\_{\parallel} + v'\_{\perp}
$$

#### ***$v_{\perp}$的旋转***

##### ***推导***

*我们首先讨论正交于旋转轴的 $v_{\perp}$。我们之前推导过，如果一个向量 $v\_{\perp}$ 正交于旋转轴 $\mathbf{u}$，那么：*
$$
v'\_{\perp} = \cos(\theta)v_{\perp} + \sin(\theta)(u \times v_{\perp})
$$
*我们可以很容易地将前面的 $\mathbf{v'\_{\perp}}$ 和 $\mathbf{v\_{\perp}}$ 替换为 $v'\_{\perp}$ 和 $v\_{\perp}$，但是我们仍遗留下来 $\mathbf{u} \times \mathbf{v_{\perp}}$。*


*幸运的是，利用四元数的性质，我们可以将它写成四元数积的形式：*

*如果有两个纯四元数 $v = [0, \mathbf{v}]$，$u = [0, \mathbf{u}]$，那么 $vu = [-\mathbf{v} \cdot \mathbf{u}，\mathbf{v} \times \mathbf{u}]$。类似地：*
$$
\begin{align*}
vu_{\perp} &= [-\mathbf{u} \cdot \mathbf{v_{\perp}}, \mathbf{u} \times \mathbf{v_{\perp}}]
\\\ \\\
&= [0, \mathbf{u} \times \mathbf{v_{\perp}}] 
\quad \quad \quad \quad \quad \quad 
因为 \mathbf{v_{\perp}}正交于\mathbf{u}，所以 \mathbf{u} \cdot \mathbf{v_{\perp}}
\\\ \\\
&= \mathbf{u} \times \mathbf{v_{\perp}}
\end{align*}
$$
*注意，$uv_{\perp}$ 同样是一个纯四元数。将这个等式以及之前定义的纯四元数代入，我们就能获得：*
$$
\begin{align*}
v'\_{\perp} &= \cos(\theta)v_{\perp} + \sin(\theta)(uv_{\perp}) \quad \quad \quad \quad \quad \quad 通过轴角式v_{\perp}的旋转公式，再把 vu_{\perp}代入
\\\ \\\
&= (cos(\theta) + \sin(\theta)u)v_{\perp} \quad \quad \quad \quad \quad \quad 乘法分配律
\end{align*}
$$
*你应该可以注意到，如果我们将$(cos(\theta) + \sin(\theta)u)$ 看作是一个四元数，我们就能将旋转写成四元数的乘积了。如果令 $q = cos(\theta) + \sin(\theta)u$，我们能得到：*
$$
v'\_{\perp} = qv_{\perp}
$$
*如果能构造一个q，那么我们就能完成这个旋转了，我们可以对 q 继续进行变形：*
$$
\begin{align*}
q &= \cos(\theta) + \sin(\theta)u
\\\ \\\
&= [\cos(\theta), 0] + [0, \sin(\theta)u]
\\\ \\\
&= [\cos(\theta), \sin(\theta)u]
\\\ \\\
&= \cos(\theta) + \sin(\theta)u_xi + sin(\theta)u_yj + \sin(\theta)u_zk
\end{align*}
$$
*这样我们就完成了对 $v_{\perp}$的旋转，我们可以得到下面定理*

##### ***总结：定理***

*3D 旋转公式（四元数型，正交情况）*

*当 $v_{\perp}$ 正交于旋转轴 $\mathbf{u}$ 时，旋转 $\theta$ 角度后的 $v'\_{\perp}$ 可以使用四元数乘法来获得获得。*

*令 $v_{\perp} = [0, \mathbf{v_{\perp}}], q = [\cos(\theta), \sin(\theta)\mathbf{u}]$，那么：*
$$
v'\_{\perp} = qv_{\perp}
$$

##### ***q 单位四元数***

*这个我们构造出来的 q其实还是一个单位四元数。因为 ||q|| = 1，他所代表的变换并不会对原项链进行缩放，是一个纯旋转*
$$
\begin{align*}
\Vert q \Vert &= \sqrt{\cos^2(\theta) + (\sin(\theta)\mathbf{u} \cdot \sin(\theta)\mathbf{u})}
\\\ \\\
&= \sqrt{\cos^2(\theta) + \sin^2(\theta)(\mathbf{u} \cdot \mathbf{u})}
\\\ \\\
&= \sqrt{\cos^2(\theta) + \sin^2(\theta)(\Vert \mathbf{u} \Vert^2)} \quad \quad \quad \quad \quad \quad (\mathbf{u} \cdot \mathbf{u} = \Vert u \Vert ^2)
\\\ \\\
&= \sqrt{\cos^2(\theta) + \sin^2{\theta}} \quad \quad \quad \quad \quad \quad (\Vert \mathbf{u} \Vert = 1)
\\\ \\\
&= 1 \quad \quad \quad \quad \quad \quad (三角恒等式)
\end{align*}
$$

#### ***$v_{\paraller}$的旋转***

*我们之前讨论过，如果一个向量 $\mathbf{v_{\parallel}}$ 平行于 $\mathbf{u}$，那么旋转不会对他作出任何变换，也就是说：*

*3D 旋转公式（四元数型，平行情况）*

*当 $v_{\parallel}$ 平行于旋转轴 $\mathbf{u}$ 时，旋转 $\theta$角度之后的 $v'\_{\parallel}$用四元数可以写为：*
$$
v'\_{\parallel} = v\_{\parallel}
$$

#### ***v的旋转***

*有了这些知识，我们能够获得一般情况下 𝑣 ′ 的结果了*
$$
\begin{align*}
v' &= v'\_{\parallel} + v'\_{\perp}
\\\ \\\
&= v_{\parallel} + q v_{\perp} \quad \quad \quad \quad \quad \quad (其中 q = [\cos(\theta), \sin(\theta)u])
\\\ \\\
&= 1 \cdot v_{\parallel} + qv_{\perp} \quad \quad \quad \quad \quad \quad (qq^{-1} = 1 ~ 和 ~ 定理1)
\\\ \\\
&= pp^{-1}v_{\parallel} + ppv_{\perp} \quad \quad \quad \quad \quad \quad (令 q = p^2)
\\\ \\\
&= pp^\*v_{\parallel} + ppv_{\perp} \quad \quad \quad \quad \quad \quad (p^{-1} = p^\*)
\\\ \\\
&= pv_{\parallel}p^* + pv_{\perp}p^*
\\\ \\\
&= p(v_{\parallel} + v_{\perp})p^*
\\\ \\\
&= pvp^*
\end{align*}
$$
*3D 旋转公式（四元数型，一般情况）*

*任意向量 $\mathbf{v}$ 沿着以单位向量定义的旋转轴 $\mathbf{u}$ 旋转 $\theta$度之后的 $\mathbf{v'}$ 可以使用四元数乘法来获得。*

*令 $v = [0, \mathbf{v}], q = [\cos(\frac{1}{2}\theta), \sin(\frac{1}{2}\theta)\mathbf{u}]$，那么：*
$$
v' = qvq^* = qvq^{-1}
$$
*换句话说，如果我们有 $q = [\cos(\theta)，\sin(\theta)\mathbf{u}]$，那么 $v' = qvq^\*$ 可以将 $\mathbf{v}$沿着 $\mathbf{u}$旋转20度*

#### ***总结 3D旋转***

1. *3D 旋转公式（四元数型，正交情况）*

   *当 $v_{\perp}$ 正交于旋转轴 $\mathbf{u}$ 时，旋转 $\theta$ 角度后的 $v'\_{\perp}$ 可以使用四元数乘法来获得获得。*

   *令 $v_{\perp} = [0, \mathbf{v_{\perp}}], q = [\cos(\theta), \sin(\theta)\mathbf{u}]$，那么：*
   $$
   v'\_{\perp} = qv_{\perp}
   $$

2. *3D 旋转公式（四元数型，平行情况）*

   *当 $v_{\parallel}$ 平行于旋转轴 $\mathbf{u}$ 时，旋转 $\theta$角度之后的 $v'\_{\parallel}$用四元数可以写为：*
   $$
   v'\_{\parallel} = v\_{\parallel}
   $$

3. *3D 旋转公式（四元数型，一般情况）*

   *任意向量 $\mathbf{v}$ 沿着以单位向量定义的旋转轴 $\mathbf{u}$ 旋转 $\theta$度之后的 $\mathbf{v'}$ 可以使用四元数乘法来获得。*

   *令 $v = [0, \mathbf{v}], q = [\cos(\frac{1}{2}\theta), \sin(\frac{1}{2}\theta)\mathbf{u}]$，那么：*
   $$
   v' = qvq^* = qvq^{-1}
   $$

### ***3D旋转的矩阵形式***

*3D 旋转公式（矩阵型）*

*任意向量 $\mathbf{v}$ 沿着以单位向量定义的旋转轴 $\mathbf{u}$ 旋转 $\theta$ 角度之后的 $v'$ 可以使用矩阵乘法来获得。*

*令 $a = \cos(\frac{1}{2}\theta), b = \sin(\frac{1}{2}\theta)u_x, c = \sin(\frac{1}{2}\theta)u_y, d = \sin(\frac{1}{2}\theta)u_z$，那么：*
$$
v' = 
\begin{bmatrix}
1 - 2c^2 - 2d^2 & 2bc - 2ad & 2ac + 2bd
\\\ \\\
2bc + 2ad & 1 - 2b^2 - 2d^2 & 2cd - 2ab
\\\ \\\
2bd - 2ac & 2ab + 2cd & 1 - 2b^2 - 2c^2
\end{bmatrix}
$$


*虽然 3D 旋转的矩阵形式可能不如四元数形式简单，而且占用更多的空间， 但是对于大批量的变换，使用预计算好的矩阵是比四元数乘法更有效率的．*

### ***旋转的复合***

*假设有两个表示沿着不同轴，不同角度旋转的四元数 $q_1$, $q_2$，我们先对 $v$ 进行 $q_1$ 的变换，再进行 q_2 的变换，变换的结果是什么呢？*

*我们分步进行。首先，我们实施 $q_1$的变换，变换之后的 $v'$为：*
$$
v' = q_1 v q_1^*
$$
*接下来，对 $v'$ 进行 $q_2$的变换，得到 $v''$*
$$
v'' = q_2v'q_2^\* = q_2q_1vq_1^\*q_2^\*
$$
*我们对这两个变换进行复合，写为一个等价交换的形式：*
$$
v'' = q_{net}vq_{net}^*
$$
*在运用定理4：*
$$
\begin{align*}
v'' &= q_2q_1vq_1^\*q_2^\*
\\\ \\\
&= (q_2q_1)v(q_2q_1)
\end{align*}
$$
*注意四元数乘法的顺序，我们先进行的是 𝑞1 的变 换，再进行 𝑞2 的变换．*

*比如说我们还需要进行第三个旋转 𝑞3，那么：*
$$
v''' = q_3(q_2q_1)v(q_2q_1)^\*q_3^\*
$$

### ***双倍覆盖***

*单位四元数与 3D 旋转有一个「2 对 1 满射同态」(2-1 Surjective Homomorphism) 关系，或者说单位四元数双倍覆盖 (Double Cover) 了 3D 旋转．*

*通俗来说就是同一个 3D 旋转可以使用两个不同的四元数来表示。*

*从旋转公式中也能推导出相同的结果：*
$$
(-q)v(-q)^* = (-1)^2qvq^* = qvq^*
$$

### ***指数形式***

*任意向量 $\mathbf{v}$ 沿着以单位向量定义的旋转轴 $\mathbf{u}$ 旋转 $\theta$ 角度之后的 $\mathbf{v'}$ 可以使用四元数的指数表示：*

*令 $v = [0, \mathbf{v}]$，$u = [0, \mathbf{u}]$，那么：*
$$
v' = e^{u\frac{\theta}{2}}ve^{-u\frac{\theta}{2}}
$$

