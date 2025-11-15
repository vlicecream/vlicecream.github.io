# 复数与四元数


# ***复数***

## ***复数的定义***

*任意复数 $z \in \mathbb{C}$都可以表示为 $z = a + bi$ 的形式，其中 $a, b \in \mathbb{R}$ 而且 $i^2 = 1$。我们将 a 称之为这个复数的实部，b 称之为这个复数的虚部*

*因为 $z = a + bi$ 其实就是对于 { 1, i } 这个基的线性组合，我们也可以用向量来表示一个复数：*
$$
z = \begin{bmatrix}
a \\\ \\\ b
\end{bmatrix}
$$
*因为这个向量有两个元素，我们可以使用复平面上的一个点来表示一个复数。复平面的横坐标 Re 代表他的实部，纵坐标 Im 代表他的虚部*

![复数坐标轴](https://raw.githubusercontent.com/vlicecream/cloudImage/main/%E5%A4%8D%E6%95%B0%E5%9D%90%E6%A0%87%E8%BD%B420251114153431840.png)

## ***复数的性质***

### ***复数加减法***

*如果我们有两个复数 $z_1 = a + bi，z_2 = c + di$，他们的和就是分量相加的结果*
$$
z_1 + z_2 = (a + c) + (b + d)i
$$
*同理，如果要对他们相减，直接将分量相减就可以了*
$$
z_1 - z_2 = (a - c) + (b - d)i
$$

### ***复数乘法***

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

### ***复数的模长***

*如果 $z = a + bi$，那么他的模长为：*
$$
||z|| = \sqrt{a^2 + b^2}
$$

### ***复数的共轭***

*如果 $z = a + bi$，那么他的共轭为：*
$$
\bar{z} = a - bi;
$$

### ***模长与共轭之关系***

*一个复数的模长又可以通过乘积的方式进行计算：*
$$
||z|| = \sqrt{z \bar{z}}
$$

## ***复数相乘与2D旋转***

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

### ***复数的极坐标型***

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

## ***总结：三种 2D旋转公式***

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

## ***旋转的复合***

*当我们对两个 2D 旋转进行复合时，所得到的变换 $z_{net}$ 仍是一个旋转，而且与施加的次序无关*

*这个等效变换的旋转角是 $z_1$ 与 $z_2$旋转角之和*

# ***三维空间的旋转***

## ***三维空间旋转 - 引言***

*表示三维空间中旋转的方法有很多种，但我们这里关注的是轴角式 (Axisangle) 的旋转。欧拉角的旋转很常用，但是有万向节死锁问题。*

*使用的坐标系是右手坐标系*

*在轴角的表示方法中，一个旋转的定义需要使用到四个变量：旋转轴 u 的 x，y，z 坐标，以及一个旋转角 $\theta$，这咋比欧拉角还多出来一个呢？实际上，任何三维中的旋转只需要三个自由度就可以定义了*

*在三维空间中定义一个方向只需要用到两个量就可以了（与任意两个坐标轴之间的夹角）。最简单的例子就是地球经纬度，我们可以靠经纬度两个变量就可以定义地球任何一个方位。但是如果我们要表达方位上特定的一个点，就需要加上海拔这个第三个变量*

*我们通常都在说旋转是绕着一个向量在旋转，其实就是绕着向量的方向在旋转，所以他的大小（长度）我们是不关心的。所以为了消除这个长度变量，我们可以将长度转化成一个单位向量。故我们从4个自由度就变成 3个自由度了*

## ***三维空间旋转的分解***

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

## ***$v_{\parallel}$ 的旋转***

*首先，我们来看一下 $v_{parallel}$ 的旋转。这种情况其实非常简单，从之前的图示中就可以看到，$v_{\parallel}$其实根本就没有旋转，仍然与旋转轴重合，所以：*

*3D旋转公式（向量型，平行情况）*
$$
v' = v'_{\parallel}
$$

## ***$v_{\perp}$ 的旋转***

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

## ***v的旋转***

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

## ***总结：轴角式的 3D旋转公式***

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
   

# ***四元数***

