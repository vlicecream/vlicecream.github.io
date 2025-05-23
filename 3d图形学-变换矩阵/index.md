# 变换矩阵


# ***变换***

## ***变换矩阵***

### ***定义***

*先来研究一下数学函数 $ \tau(\vec{v}) = \tau(x, y, z) = (x', y', z') $。此函数的输入和输出都是 3D 向量。*

*我们称 τ 为 线性变换（linear transformation），当且仅当此函数具有下列性质：*

1. *$ \tau(\vec{u} + \vec{v}) = \tau{\vec{u}} + \tau{\vec{v}} $*
2. *$ \tau({k\vec{u}}) = k\tau({u}) $*

*其中，$ \vec{u} = (u_x, u_y, u_z)和 \vec{v} = (v_x, v_y, v_z) $是任意 3D 向量，k 为一个标量。*

### ***示例***

*定义函数 $ \tau(x, y, z) = (x^2, y^2, z^2) $, 例如 $ \tau(1, 2, 3) = (1, 4, 9) $ 这个函数是非线性函数，因为当 k=2 且 $ \vec{u} = (1, 2, 3) $时*

$$
\tau(k\vec{u}) = \tau(2，4，6) = (4, 16, 36)
$$
*但*
$$
k\tau(u) = 2(1,4,9) = (2,8,18)
$$
*因此，该函数不满足刚刚说的第二条性质*

*如果$ \tau $ 是线性函数，那么有*
$$
\begin{align*}
\tau(a\vec{u} + b\vec{v} + c\vec{w}) 
&= \tau(a\vec{u} + (b\vec{v} + c\vec{w}))
\\\ \\\
&= a\tau(\vec{u}) + \tau(b\vec{v} + c\vec{w})
\\\ \\\
&= a\tau(\vec{u}) + b\tau(\vec{v}) + c\tau(\vec{w})
\end{align*}
$$

### ***矩阵表示法***

*设 $ \vec{u} = (x, y, z) $，我们也可以将它写作*
$$
\vec{u} = (x, y, z) = x\vec{i} + y\vec{j} + z\vec{k} = x(1, 0, 0) + y(0,1,0) + z(0,0,1)
$$

*$ \vec{i} = (1, 0, 0)， \vec{j} = (0, 1, 0)， \vec{k} = (0, 0, 1) $ 分别表示位于当前坐标轴正方向上得3个单位向量，我们称之为 $ \mathbb{R}^3 $（表示所有的3D坐标向量的集合）的标准基向量*

*现假设 $ \tau $ 是一种线性变换，根据他的线性性质能够得到*
$$
\tau(u) = \tau(x\vec{i}, y\vec{j}, z\vec{k}) = x\tau\vec{i} + y\tau\vec{j} + z\tau\vec{k}
$$
*可以看出，这个公式其实就是线性组合，可以表示为向量与矩阵的乘积。*

*这个公式还可以进一步优化：*
$$
\begin{align}
\tau\vec{u} 
&= x\tau\vec{i} + y\tau\vec{j} + z\tau\vec{k}
\\\ \\\
&= \vec{u}A
\\\ \\\
&= [x, y, z]
\begin{bmatrix}
\leftarrow \tau(\vec{i}) \rightarrow
\\\ \\\
\leftarrow \tau(\vec{j}) \rightarrow
\\\ \\\
\leftarrow \tau(\vec{k}) \rightarrow
\end{bmatrix}
\\\ \\\
&= [x, y, z]
\begin{bmatrix}
A_{11} & A_{12} & A_{13}
\\\ \\\
A_{21} & A_{22} & A_{23}
\\\ \\\
A_{31} & A_{32} & A_{33}
\end{bmatrix}
\end{align}
$$
*其中 $ \tau\vec{i} = A\_{11}, A\_{12}, A\_{13}，\tau\vec{j} = A\_{21}, A\_{22}, A\_{23}，\tau\vec{k} = A\_{31}, A\_{32}, A\_{33} $。*

*我们称矩阵 A 是线性变换 τ 的矩阵表示法。*

### ***缩放***

*缩放（scaling，也有译作比例变换）是指改变物体的大小*

*我们把缩放定义为*
$$
S(x, y, z) = (s_x x, s_y y, s_z z)
$$
*此变换将相对于当前坐标系中的原点，令向量在 x, y, z 轴上分别以系数 $ s_x, s_y, s_z $ 进行缩放*

*下面证明 S 其实就是一种线性变换:*
$$
\begin{align}
S(\vec{u} + \vec{v}) 
&= (s_x(u_x + v_x), s_y(u_y, v_y), sz(u_z + v_z))
\\\ \\\
&= (s_x u_x + s_x v_x, s_y u_y + s_y v_y, s_z u_z + s_z v_z)
\\\ \\\
&= (s_x u_x, s_y u_y, s_z u_z) + (s_x v_x, s_y v_y, s_z v_z)
\\\ \\\
&= S(\vec{u}) + S(\vec{v})
\\\ \\\
S(k\vec{u}) &= (s_x k u_x, s_y k u_y, s_z k u_z)
\\\ \\\
&= k(s_x u_x, s_y u_y, s_z u_z)
\\\ \\\
&= kS(\vec{u})
\end{align}
$$
*因此，缩放变换 S 满足上述所有的性质。*

*这也就是说，S是线性变换并存在一种矩阵表示法。*

*为了求出S的矩阵表示，我们只需把每一个标准基向量一次代入 S，在将得到的向量作为矩阵的行向量*
$$
\begin{align*}
S(i) = (s_x \cdot 1, s_y \cdot 0, s_z \cdot 0) = (s_x, 0, 0)
\\\ \\\
s(j) = (s_x \cdot 0, s_y \cdot 1, s_z \cdot 0) = (0, s_y, 0)
\\\ \\\
s(k) = (s_x \cdot 0, s_y \cdot 0, s_z \cdot 1) = (0, 0, s_z)
\end{align*}
$$
*这样就得到了缩放变换 S 的矩阵表示*
$$
S=
\begin{bmatrix}
s_x & 0 & 0 
\\\
0 & s_y & 0
\\\
0 & 0 & s_z
\end{bmatrix}
$$
*我们称此矩阵为缩放矩阵（scaling matrix，亦有译为比例变换矩阵）。*

*而其对用的逆矩阵则为：*
$$
S^{-1}=
\begin{bmatrix}
\frac{1}{s_x} & 0 & 0 
\\\
0 & \frac{1}{s_y} & 0
\\\
0 & 0 & \frac{1}{s_z}
\end{bmatrix}
$$

#### ***例题***

*假设定义了一个最小点坐标为 (−4, −4, 0) 和最大点坐标为 (4, 4, 0) 的正方形。现欲将此正方形在 x 轴方 向上缩小 50%，在 y 轴方向上放大 2.0 倍，但在 z 轴方向上保持不变。其对应的缩放矩阵为：*
$$
S=
\begin{bmatrix}
0.5 & 0 & 0
\\\
0 & 2 & 0
\\\
0 & 0 & 1
\end{bmatrix}
$$
*此时，若要对正方形进行缩放，只需将最小点、最大点坐标分别与缩放矩阵相乘即可：*
$$
\begin{align}
[-4, -4, 0]
\begin{bmatrix}
0.5 & 0 & 0
\\\ \\\
0 & 2 & 0
\\\ \\\
0 & 0 & 1
\end{bmatrix}
&= [-2, -8, 0]
\\\ \\\
[4, 4, 0]
\begin{bmatrix}
0.5 & 0 & 0
\\\ \\\
0 & 2 & 0
\\\ \\\
0 & 0 & 1
\end{bmatrix}
&= [2, 8, 0]
\end{align}
$$


*变换的效果如图所示*

![缩放矩阵](https://raw.githubusercontent.com/vlicecream/cloudImage/main/image-20250305200041712.png)

### ***旋转***

*在本节中，我们将用数学的方式来描述令向量 v 绕轴 n 以角 θ 进行旋转*

*此过程如图 3.3 所示。注意，在沿 n 轴从上至下俯瞰时，我们按顺时针方向来测量角 θ ，并且假设 ||n|| = 1*

![image-20250303212116226](https://raw.githubusercontent.com/vlicecream/cloudImage/main/image-20250303212116226.png)

1. *将向量 v 分解为两部分：一部分平行于 n，另一部分正交于 n。*

   *平行于 n 的部分即为 $ proj_n(\vec{v}) $ ，正交于 n 的部分则是  $ \vec{v_\perp } = perp_n(\vec{v}) = \vec{v} = proj_n(\vec{v}) $（由于 n 是单位向量，我们就可以得到 $ proj_n(\vec{v}) = (\vec{n} \cdot \vec{v})\vec{n} $ ）*

   *观察示意图能够发现这样一个关键信息：平行于 n 的部分 $  proj_n(\vec{v}) $ 在旋转时是保持不变的。因此，我们只需考虑怎样旋转与 n 正交的部分。根据图 3.3 可知，旋转向量  $ R_n(\vec{v}) = proj_n(\vec{v}) + R_n(\vec{v_\perp}) $ 。*

2. *为了求出 $ R_n(\vec{v_\perp}) $，我们在旋转平面内建立一个2D坐标系，并将 $ \vec (\vec{v_\perp} $ 作为一个参考向量*

   *通过计算叉积 $ \vec{n} x \vec{v} $来获得既正交于 $ \vec (\vec{v_\perp} $ 又正交于 $ \vec{n}$ 的第二个参考向量（根据左手拇指法则）*

   *基于图3.3所示的三角关系可知：*
   $$
   ||\vec{n} \times \vec{v}|| = ||\vec{n}|| \cdot ||\vec{v}|| sin\alpha = ||\vec{v}||sin\alpha = ||v_\perp||
   $$

3. *其中，α 是 n 与 v 之间的夹角。由此可知：两个参考向量的长度相等，且都位于旋转的圆周之上。 根据三角学知识，我们就可以将这两个参考向量建立如下关系：*
   $$
   R_n(v_\perp) = cos\theta\vec{v}_\perp + sin\theta(\vec{n} \times \vec{v})
   $$

4. *这样就推导除了下列旋转公式：*
   $$
   \begin{align}
   R_n(\vec{v}) 
   &= proj_n(\vec{v}) + R_n(\vec{v}\_\perp)
   \\\ \\\
   &= (\vec{n} \cdot \vec{v})\vec{n} + cos\theta\vec{v}\_\perp + sin\theta(\vec{n} \times \vec{v})
   \\\ \\\
   &= (\vec{n} \cdot \vec{v})\vec{n} + cos\theta(\vec{v} - (\vec{n} \cdot \vec{v})\vec{n}) + sin\theta(\vec{n} \times \vec{v})
   \\\ \\\
   &= cos\theta\vec{v} + (1 - cos\theta)(\vec{n} \times \vec{v})\vec{n} + sin\theta(\vec{n} \times \vec{v})
   \end{align}
   $$

5. *若要得到旋转的变换矩阵表示，仅需将各个标准基向量代入到 Rn 中，再把得到的向量分别作为矩阵的行向量。 最终得到的结果为：*
   $$
   \begin{align*}
   R_n &= 
   \begin{bmatrix}
   c + (1 - c)x^2 & (1-c)xy + sz & (1-c)xz - sy
   \\\ \\\
   (1-c)xy - sz & c + (1 - c)y^2 & (1-c)yz + sx
   \\\ \\\
   (1-c)xz + sy & (1-c)yz - sx & c + (1-c)z^2
   \end{bmatrix}
   \\\ \\\
   &此处设 c = cos\theta 且 s = sin\theta
   \end{align*}
   $$

*旋转矩阵有个有趣的性质：每个行向量都为单位长度且两两正交（请分别证明）。*

*也就是说，这些行 向量都是规范正交的（orthonormal，即互相正交且具有单位长度）。*

*若一个矩阵的行向量都是规范正交 的，则称此矩阵为正交矩阵（orthogonal matrix）。*

*正交矩阵有个引人注目的性质，即它的逆矩阵与转置 矩阵是相等的：*
$$
\begin{align*}
R^{-1}_n = R^T_n  = 
\begin{bmatrix}
c + (1 - c)x^2 & (1-c)xy + sz & (1-c)xz - sy
\\\ \\\
(1-c)xy - sz & c + (1 - c)y^2 & (1-c)yz + sx
\\\ \\\
(1-c)xz + sy & (1-c)yz - sx & c + (1-c)z^2
\end{bmatrix}
\\\ \\\
此处设 c = cos\theta 且 s = sin\theta
\end{align*}
$$
***通常来说，由于正交矩阵的逆矩阵计算方便且高效，所以很受青睐。***

*特别地，如果选择绕 x 轴、y 轴或 z 轴进行旋转（即分别取 n = (1, 0, 0)、n = (0, 1, 0)和 n = (0, 0, 1)）， 便会获得以 x、y、z 为旋转轴的对应旋转矩阵：*
$$
R_x = 
\begin{bmatrix}
1 & 0 & 0
\\\ \\\
0 & cos\theta & sin\theta
\\\ \\
0 & -sin\theta & cos\theta
\\\ \\\
0 & 0 & 0
\end{bmatrix}
,R_y = 
\begin{bmatrix}
cos\theta & 0 & -sin\theta
\\\ \\\
0 & 1 & 0
\\\ \\
sin\theta & 0 & cos\theta
\\\ \\\
0 & 0 & 0
\end{bmatrix}
,R_z = 
\begin{bmatrix}
cos\theta & sin\theta & 0
\\\ \\\
-sin\theta & cos\theta & 0
\\\ \\
0 & 0 & 1
\\\ \\\
0 & 0 & 0
\end{bmatrix}
$$

#### ***例题***

*假设定义了一个最小点坐标为(−1, 0, −1)和最大点坐标为(1, 0, 1)的正方形。*

*现在，我们希望令它绕 y 轴顺时针旋转−30°（即按逆时针方向旋转 30°）。*

*根据问题所述可知， n =  (0, 1, 0) ，代入 $ R_n $ 并进行化简， 可得到 y 轴的旋转矩阵为*
$$
R_y = \begin{bmatrix} 
\cos\theta & 0 & -\sin\theta \\\
0 & 1 & 0 \\\
\sin\theta & 0 & \cos\theta 
\end{bmatrix} 
= \begin{bmatrix} 
\cos(-30^\circ) & 0 & -\sin(-30^\circ) \\\
0 & 1 & 0 \\\
\sin(-30^\circ) & 0 & \cos(-30^\circ) 
\end{bmatrix} 
= \begin{bmatrix} 
\frac{\sqrt{3}}{2} & 0 & \frac{1}{2} \\\
0 & 1 & 0 \\\
-\frac{1}{2} & 0 & \frac{\sqrt{3}}{2} 
\end{bmatrix}
$$
*为了旋转该正方形，还需将其最小点、最大点坐标分别乘以得到的旋转矩阵*
$$
\begin{align*}
[-1, 0, -1] \begin{bmatrix} 
\frac{\sqrt{3}}{2} & 0 & \frac{1}{2} \\\
0 & 1 & 0 \\\
-\frac{1}{2} & 0 & \frac{\sqrt{3}}{2} 
\end{bmatrix} 
&\approx [-0.36, 0, -1.36] 
\\\ \\\
[1, 0, 1] \begin{bmatrix} 
\frac{\sqrt{3}}{2} & 0 & \frac{1}{2} \\\
0 & 1 & 0 \\\
-\frac{1}{2} & 0 & \frac{\sqrt{3}}{2} 
\end{bmatrix} 
&\approx [0.36, 0, 1.36]
\end{align*}
$$

## ***仿射变换***

### ***齐次坐标***

*仿射变换 是一个线性变换和一个平移变换组合而成的。对于向量而言，平移操作是没有意义的，因为向量只描述方向和大小，却与位置无关。换句话说，平移向量不应该作用于向量。因此，平移变换只能应用于点(即位置向量)*

*齐次坐标（homogeneous coordinate）所提供的表示机制，使我们可以方便地对点和向量进行统一的处理。在采用齐次坐标表示法时，我们将坐标扩充为四元组，其中，第四个坐标 w 的取值将根据被描述对象是点还是向量而定。具体来讲：*

1. *(x, y, z, 0) 表示向量*
2. *(x, y, z, 1)表示点*

*在后面我们将会证明：设 w = 1 能使点被正确地平移，设 w = 0 则可以防止向量坐标受到平移操作的 影响（我们不希望对向量的坐标进行平移变换，因为这个计算过程会改变它的方向和大小——而平移操 作不应当修改向量的任何一种“属性”）。*

### ***仿射变换的定义及其矩阵表示***

*线性变换并不能表示出我们需要的所有变换，因此，现将其扩充为一种称作仿射变换的映射范围更广的函数类。*

*仿射变换为一个线性变换加上一个平移向量 b，即：*
$$
\alpha(\vec{u}) = \tau(\vec{u}) + \vec{b}
$$
*或者用矩阵表示法*
$$
\alpha(\vec{u})
= \vec{u}A + \vec{b} 
= [x, y, z]
\begin{bmatrix}
A_{11} & A_{12} & A_{13}
\\\ \\\
A_{21} & A_{22} & A_{23}
\\\ \\\
A_{31} & A_{32} & A_{33}
\end{bmatrix}
\+ [b_x, b_y, b_z]
= [x', y', z']
$$
*其中，A 是一个线性变换的矩阵表示。*

*如果用 w = 1 把坐标扩充为齐次坐标，那么就可以将上式更简洁地写作：*
$$
[x, y, z, 1]
\begin{bmatrix}
A_{11} & A_{12} & A_{13} & 0
\\\ \\\
A_{21} & A_{22} & A_{23} & 0
\\\ \\\
A_{31} & A_{32} & A_{33} & 0
\\\ \\\
b_x & b_y & b_z & 1
\end{bmatrix}
= [x', y', z', 1]
$$
*此 4 x 4 矩阵称为仿射变换的矩阵表示。*

*可以看出，加上向量 b 的这步运算，从本质上来说是一种平移操作（使目标对象的位置发生了改变）。但是，我们既不希望将此平移操作应用到向量上（因为向量的性质中并没有位置这个概念），又想令向量 受到仿射变换中线性部分的处理。此时，如果将向量的第四个分量设为 w = 0，它便不会受到向量 b 平移 操作的影响（可通过矩阵的乘法运算来验证这一点）。*

### ***平移***

*恒等变换（identity transformation）是一种直接返回其输入参数的线性变换，形如 I(u) = u。不难证 明，这种线性变换的矩阵表示即为单位矩阵*

*现将平移变换（translation transformation）定义为仿射变换，此时， 其中的线性变换就是一种恒等变换，即：*
$$
\tau(\vec{u}) = \vec{u}I + \vec{b} = \vec{u} + \vec{b}
$$
*如您所见，此线性变换简单地利用向量 b 对点 u 进行平移（或位移）。*

*$ \tau $ 的矩阵表示为*
$$
T = 
\begin{bmatrix}
1 & 0 & 0 & 0
\\\ \\\
0 & 1 & 0 & 0
\\\ \\\
0 & 0 & 1 & 0
\\\ \\\
b_x & b_y & b_z & 1
\end{bmatrix}
$$
*该矩阵称为平移矩阵（translation matrix）。*

*平移矩阵的逆矩阵则为：*
$$
T^{-1} = 
\begin{bmatrix}
1 & 0 & 0 & 0
\\\ \\\
0 & 1 & 0 & 0
\\\ \\\
0 & 0 & 1 & 0
\\\ \\\
-b_x & -b_y & -b_z & 1
\end{bmatrix}
$$

#### ***例子***

*假设定义了一个正方形，其最小点坐标为(−8, 2, 0)，最大点坐标为(−2, 8, 0)。我们希望将此正方形沿 x 轴正方向平移 12 个单位，沿 y 轴正方向平移 −10 个单位，在 z 轴方向保持不变*

*则其对应的平移矩阵为：*
$$
T = 
\begin{bmatrix}
1 & 0 & 0 & 0
\\\ \\\
0 & 1 & 0 & 0
\\\ \\\
0 & 0 & 1 & 0
\\\ \\\
12 & -10 & 0 & 1
\end{bmatrix}
$$
*现对此正方形进行平移（变换），将其最小点、最大点坐标分别乘以上述平移矩阵：*
$$
\begin{align*}
[-8, 2, 0, 1]
\begin{bmatrix}
1 & 0 & 0 & 0
\\\ \\\
0 & 1 & 0 & 0
\\\ \\\
0 & 0 & 1 & 0
\\\ \\\
12 & -10 & 0 & 1
\end{bmatrix}
&\=
[4, -8, 0 ,1]
\\\ \\\
[-2, -8, 0, 1]
\begin{bmatrix}
1 & 0 & 0 & 0
\\\ \\\
0 & 1 & 0 & 0
\\\ \\\
0 & 0 & 1 & 0
\\\ \\\
12 & -10 & 0 & 1
\end{bmatrix}
&\=
[10, -2, 0 ,1]
\end{align*}
$$
![变换矩阵-平移矩阵](https://raw.githubusercontent.com/vlicecream/cloudImage/main/变换矩阵-平移矩阵.png)

### ***缩放和旋转的仿射矩阵***

*通过观察可以发现，如果 b = 0，则仿射变换将退化为线性变换。这样一来，我们就能用 b = 0 的仿射变换来表示任意线性变换。更进一步说，也就意味着仅通过一个 4 × 4 的仿射矩阵表达出任意的线性变换。例如，缩放矩阵与旋转矩阵可写作下列的 4 × 4矩阵：*
$$
\begin{align*}
S &= 
\begin{bmatrix}
s_x & 0 & 0 & 0
\\\ \\\
0 & s_y & 0 & 0
\\\ \\\
0 & 0 & s_z & 0
\\\ \\\
0 & 0 & 0 & 1
\end{bmatrix}
\\\ \\\
R_n
&=
\begin{bmatrix}
c + (1 - c)x^2 & (1-c)xy + sz & (1-c)xz - sy
\\\ \\\
(1-c)xy - sz & c + (1 - c)y^2 & (1-c)yz + sx
\\\ \\\
(1-c)xz + sy & (1-c)yz - sx & c+ (1-c)z^2
\\\ \\\
0 & 0 & 0 & 1
\end{bmatrix}
\end{align*}
$$
*如此一来，就能用 4 × 4 矩阵统一地表示所有变换，并通过1 × 4 齐次行向量来表示点和向量。*

### ***仿射变换矩阵的几何意义***

*首先让我们来考察刚体变换（rigid body transformation），其本质是一种保形（shape preserving，即保持形状） 变换。*

*以下便是刚体变换在现实生活中的一个例子：从书桌上拿起书，再将它放到书架上。在移动书的这个过程中（平移），很可能会改变它的朝向（旋转）。*

*设 $ \tau $ 为描述物体旋转操作的旋转变换，而 b 为定义物体平移操作的平移向量。那么，刚体变换就可以用仿射变换来表示：*
$$
\alpha(x, y, z) = \tau(x, y, z) + \vec{b} = x\tau(\vec{i}) + y\tau(\vec{j}) + z\tau{\vec{k}} + \vec{b}
$$
*在矩阵表示法中，若采用齐次坐标（表示点时，w = 1；表示向量时，w = 0，如此一来，平移变换就不会作用于向量），上式将被改写为：*
$$
[x, y, z, w]
\begin{bmatrix}
\leftarrow \tau(\vec{i}) \rightarrow
\\\ \\\
\leftarrow \tau(\vec{j}) \rightarrow
\\\ \\\
\leftarrow \tau(\vec{k}) \rightarrow
\\\ \\\
\leftarrow \vec{b} \rightarrow
\end{bmatrix}
= [x', y', z', w]
$$
*至此，为了理解此方程的几何意义，我们还要将矩阵中的行向量依次绘制出来（见图 3.7）。由于 $ \tau $ 是一个 旋转变换，所以它具有保长性与保角性（详见章末习题 26）。*

*特别是我们能看到 $ \tau $ 仅将标准基向量 i、j 和 k，分 别旋转到对应的新方向 $ \tau(\vec{i}) 、\tau(\vec{j}) 和 \tau(\vec{k}) $ 。而向量b 则是一个位置向量，它表示物体相对于原点的位移。现在来看图3.7，它以几何学的角度展示了如何通过计算 $ \alpha(x, y, z) = x\tau(\vec{i}) + y\tau(\vec{j}) + z\tau{\vec{k}} + \vec{b} $ 来求取变换后的点。*

*这种思路同样可以运用在缩放或斜切（skew，也有译作倾斜、扭曲等）变换上。请考虑这样一种线性变换 $ \tau $，它将图 3.8 所示的正方形拉扯为一个平行四边形。斜切处理后的点即为斜切变换后的基向量的线性组合。*

![仿射变换矩阵的几何意义](https://raw.githubusercontent.com/vlicecream/cloudImage/main/image-20250304204433342.png)

## ***变换的复合***

*假设 S 是一个缩放矩阵，R 是一个旋转矩阵，T 是一个平移矩阵。*

*现给定一个由 8 个顶点 $ v_i $（其 中 i = 0, 1, …, 7）构成的立方体，并希望将这 3 种变换相继应用到此正方体的每个顶点之上。*

*我们以下列简明的方式来逐步对顶点进行变换：*
$$
\begin{align}
((\vec{v}, S)R)T &= (v'_i R)T = v''_i T = v'''_i
\\\ \\\
其中i&=0,1, ...,7
\end{align}
$$
*然后，由于矩阵乘法满足结合律，因而此式可以等价的改写为：*
$$
\begin{align}
v_i(SRT) &= v'''_i
\\\ \\\
其中 i &= 0, 1, ... , 7
\end{align}
$$
*还可将 C = SRT 视为一个矩阵，即提前将3种变换封装为一个净变换矩阵。换句话说，矩阵之间的乘法法则使我们将不同的变换连接在一起*

*这里十级还涉及性能问题，来看一个例子：*

1. *假设有一个由 20000 个点组成的 3D 物体，我们希望将 上述 3 种几何变换，逐个作用到这个物体上。*
2. *如果采用按部就班的计算方法，我们需要进行 20000 3 × 次 向量与矩阵的乘法运算。但通过上述组合矩阵的计算方法，只需要执行 20000 次的向量与矩阵乘法运算以及两次矩阵与矩阵的乘法运算即可。*
3. *显而易见的是，比起前者中近 3 倍的大量向量与矩阵乘法运算而 言，后者中两次额外的矩阵与矩阵乘法运算真可谓是九牛一毛。*

## ***坐标系变换***

*根据标架 A 与另一不同标架 B 的关系，我们就可以将相对于标架 A 表示某量的标量 k，转换为相对于标架 B 描述同一种量的新标量 k'*

*我们把不 同标架间的坐标的转换称之为坐标系变换*

*值得注意的是，在坐标变换的过程中，几何体本身并没有随之发生改变。坐标变换改变的仅是物体的参考系（又称参照系），因此改变的实为几何体的坐标表示。*

*相比之下，我们可以认为旋转、平移和缩放这些操作才使几何体发生了实质上的移动或形变。*

*3D 计算机图形学中，我们往往会用到许多不同的坐标系，所以需要了解在它们之间互相转换坐标 的方法。由于位置是点的属性，与向量无关，所以点和向量的坐标变换是不同的*

### ***向量的坐标变换***

![坐标系变换](https://raw.githubusercontent.com/vlicecream/cloudImage/main/坐标系变换.png)

*思考图 3.11，其中有一向量 p 分别位于标架 A 和标架 B 之中。*

*假设给定向量 p 在标架 A 中的坐标为 $ p_A = (x, y) $，现希望求得向量 p 在标架 B 中的对应坐标 $ p_B = (x', y') $。*

*说人话就是，向量p在另一个坐标系的坐标是什么*

*从图可以看出*
$$
\vec{p} = x\vec{u} + y\vec{v}
$$
*其中，u 和 v 分别是标架Ａ中 x 轴、y 轴正方向上的单位向量。用标架 B 中的坐标来表示以上公式中的单位向量，可得：*
$$
\vec{p}_B = x\vec{u}_B + y\vec{v}_B
$$
***所以，如果给定 $ p_A = (x, y) $，也已知向量 u 和向量 v 相对于标架 B 的坐标分别为 $ u_B = (u_x, u_y) $ 以及 $ v_B = (v_x, v_y) $， 那么就一定能求出 $ p_B = (x', y') $。***

*现将向量的坐标变换推广到3D空间，如果 $ \vec{p_A} = (x, y, z) $, 那么*
$$
\vec{p}_B = x\vec{u}_B + y\vec{v}_B + z\vec{w}_B
$$
*其中，u, v, w分别指向标价A中 x 轴，y 轴和 z 轴正方向上的单位向量*

*现将向量的坐标变换推广到 3D 空间，如果 $ p_A = (x, y, z) $，那么：*
$$
\vec{p}_B = x\vec{u}_B + y\vec{v}_B + z\vec{w}_B
$$
*其中，u、v 和 w 分别是指向标架Ａ中 x 轴、y 轴和 z 轴正方向上的单位向量。*

### ***点的坐标变换***

![点的坐标变换](https://raw.githubusercontent.com/vlicecream/cloudImage/main/点的坐标变换.png)

*点与向量的坐标变换稍有不同，这是由于位置是点的一个重要属性，因而不能将图 3.11 所示的向量 平移方法应用于点上。*

*图 3.12 展示了一个对点进行坐标变换的情景，通过观察，可以将点 p 表示为：*
$$
\vec{p} = x\vec{u} + y\vec{v} + Q
$$
*其中 u 和 v 是分别指向标架 A 中 x 轴和 y 轴正方向上的单位向量，且 Q 是标架 A 中的原点。用标架 B 中的坐标来表示上式中的每一个向量和点，可得：*
$$
\vec{p}_B = x\vec{u}_B + y\vec{v}_B + Q_B
$$
*如果给出 $ p_A = (x, y) $，同时也知道向量 u、v 以及原点 Q 相对于标架 B 的坐标分别为 $ u_B = (u_x, u_y)、v_B = (v_x, v_y) $ 以及 $ Q_B = (Q_x, Q_y) $，那么，我们总能求出 $ p_B = (x', y') $*

*现把点的坐标变换推广到 3D 空间，如果 $ p_A = (x, y, z) $，那么：*
$$
\vec{p}_B = x\vec{u}_B + y\vec{v}_B + z\vec{w}_B + Q_B
$$
*其中，向量 u、v 和 w 是分别指向标架 A 中 x 轴、y 轴和 z 轴的正方向上的单位向量，Q 则为标架 A 中的原点。*

### ***坐标变换的矩阵表示***

*到目前为止，我们已经分别探讨了向量和点的坐标变换：*
$$
\begin{align*}
(x', y', z') &= x\vec{u}_B + y\vec{v}_B + z\vec{w}_B
\\\ \\\
(x', y', z') &= x\vec{u}_B + y\vec{v}_B + z\vec{w}_B + Q_B
\end{align*}
$$
*如果使用齐次坐标，就可以用同一公式对点和向量进行处理：*
$$
(x', y', z', w) = x\vec{u}_B + y\vec{v}_B + z\vec{w}_B + Q_B
$$
*如果 w= 0, 此式化简为向量的坐标变换公式；如果 w = 1，则此时化简为点的坐标变换公式，此式子的优点在于：只要为其正确的 设置 w 分量值，他就能相应的处理点或向量的坐标变换。*

*这样，我们也就无需分别记住两个公式了，而且我们可以将此式子改写为矩阵形式：*
$$
\begin{align*}
[x', y', z', w] 
&= [x, y, z, w]
\begin{bmatrix}
\leftarrow \vec{u}_B \rightarrow
\\\ \\\
\leftarrow \vec{u}_B \rightarrow
\\\ \\\
\leftarrow \vec{u}_B \rightarrow
\\\ \\\
\leftarrow \vec{u}_B \rightarrow
\end{bmatrix}
\\\ \\\
&=
[x, y, z, w]
\begin{bmatrix}
u_x & u_y & u_z & 0
\\\ \\\
v_x & v_y & v_z & 0
\\\ \\\
w_x & w_y & w_z & 0
\\\ \\\
Q_x & Q_y & Q_z & 1
\end{bmatrix}
\\\ \\\
&=
x\vec{u}_B + y\vec{v}_B + z\vec{w}_B + wQ_B
\end{align*}
$$
*其中，$ Q_B = (Q_x, Q_y, Q_z, 1)，\vec{u}_B = (u_x, u_y, u_z, 0)，\vec{v}_B = (v_x, v_y, v_z, 0)与 \vec{w}_B = (w_x, w_y, w_z, 0) $分别表示标架 A 中的 原点和诸坐标轴相对于标架 B 的齐次坐标。我们把上述式子里能把标架 A 中的坐标转换（或映射， map）为标架 B 中坐标的 4 x 4矩阵，称为坐标变换矩阵（change of coordinate matrix）或标架变换矩阵 （change of frame matrix）。*

### ***坐标变换矩阵及其结合律***

*现假设有 3 个标架 F、G 和 H。A 为将坐标从 F 转换到 G 的标架变换矩阵，B 为把坐标由 G 转换至 H 的标架变换矩阵*

*在标架 F 中，有一向量的坐标为 $ p_F $，我们希望求出此向量相对于标架 H 的坐标 $ p_H $。 可按一般顺序来逐步计算：*
$$
\begin{align*}
(\vec{p}_FA)B &= \vec{p}_H
\\\ \\\
(\vec{p}_G)B &= \vec{p}_H
\end{align*}
$$
*由于矩阵的乘法运算满足结合律，我们可将 $ (\vec{p}\_FA)B = \vec{p}_H $ 写作：*
$$
(\vec{p}_F)AB = \vec{p}_H
$$
*这样一来，就能把矩阵乘积 C = AB 看作是将坐标从标架 F 直接变换至标架 H 的标架变换矩阵，它将变换矩阵 A 和 B 结合为一个净矩阵-------其思路就类似于函数的复合。*

*这种计算方法还会对性能产生影响。为了说明这一点，我们假设一个由 20000 个点构成的 3D 物体， 现要对它的每个点进行两次标架变换。若使用逐步计算的方法，我们需进行 20000 × 2 次向量与矩阵的乘法运算。但利用结合矩阵的方法，我们只要进行 20000 次向量与矩阵的乘法运算以及一次矩阵与矩阵的乘法运算（用于结合两个标架变换矩阵）即可。不难看出，仅借助一次开销极低的矩阵之间的乘法运算， 便可以节省多次向量与矩阵乘法所需的大量计算资源。*

### ***坐标变换矩阵及其逆矩阵***

*假设给定向量 p 相对于标架 B 的坐标 $p_B$，以及将坐标由标架 A 转换到标架 B 的变换矩阵 M，即有 $ p_B = p_AM $。现希望求得 $ p_A $。换句话说，我们这次是希望通过坐标变换矩阵，将标架 B 中的坐标映射到标架 A 中。 为了求出这个矩阵，我们假设矩阵 M 是可逆的（即存在 $ M^−1 $）。通过下列步骤，我们就能得到坐标 $ p_A $：*

1. *$ \vec{p}\_B = \vec{p}_AM $*
2. *$ \vec{p}\_B M^-1 = \vec{p}_AM M^-1 $  等式左右两次同时乘以矩阵 M^-1*
3. *$ \vec{p}\_B = \vec{p}_AI$ 由逆矩阵的定义可知 MM^-1 = I*
4. *$ \vec{p}\_B M^-1 = \vec{p}_A 由单位矩阵的定义可知\vec{p}\_AI = \vec{p}_A $*

*所以矩阵 $ M^-1 $ 即为将坐标由标架B转换到标架A的变换矩阵*

## ***变换矩阵与坐标变换矩阵***

*到目前为止，我们已经对“使几何体本身发生改变”的变换（缩放、旋转和平移）与坐标变换进行了区分*

*在本节中，我们将证明：从数学角度上看，两者在数学上其实是等价的。即，可将改变几何体的变换解释为坐标变换，反之亦然。*

![变换矩阵与坐标变换矩阵](https://raw.githubusercontent.com/vlicecream/cloudImage/main/变换矩阵与坐标变换矩阵.png)

*如果我们能认识到这一点，会发现其意义非凡。对于坐标变换来说，标架之间的差异仅为位置和朝向。因此，令坐标在标架之间转换的数学公式实质上描述的即为所需执行的旋转和平移操作，最终也会得到与几何变换（对物体进行缩放、旋转和平移）相同的数学形式，可谓殊途同归。几何变换也好，坐标变换也罢，计算出的结果都是相同的，差别只在于解释变换的角度。对于某些情况来说，保持物体不变，使之在多个坐标系之间转换是种更直观的办法。但是，若描述对象的参考系发生了变化，则物体的 坐标表示也会随之改变。有些时候，我们又希望在同一个坐标系中表示物体的变换，而不改变其参考系，此时即可采用几何变换法。*	

## ***题目***

1. *设 $ \tau = \mathbb{R}^3 → \mathbb{R}^3 $ 的定义为 $ \tau(x, y, z) = (x + y, x - 3, z) $ 。那么，$ \tau $ 是一种线性变换吗？如果是，求出它 的标准矩阵表示。*
2. *设 $ \tau = \mathbb{R}^3 → \mathbb{R}^3 $ 的定义为 $ \tau(x, y, z) = (3x + 4z, 2x - z, x + y + z) $ 。那么，$ \tau $ 是一种线性变换吗？如果是，求出它的标准矩阵表示。*
3. *设 $ \tau = \mathbb{R}^3 → \mathbb{R}^3 $ 是一种线性变换，而且 $ \tau(1, 0, 0) = (3, 1, 2)，\tau(0, 1, 0) = (2, -1, 3)，\tau(0, 0, 1) = (4, 0, 2) $, 求 $ \tau(1, 1, 1)$*
4. *构建一个缩放矩阵，使物体在 x 轴方向上放大 2 倍，在 y 轴方向上放大 − 3 倍，在 z 轴方向上保持不变。*
5. *构建一个旋转矩阵，使物体绕轴(1, 1, 1)旋转 30°*
6. *构建一个平移矩阵，使物体沿 x 轴正方向平移 4 个单位，在 y 轴方向保持不变，沿 z 轴正方向平移−9 个单位。*
7. *构建一个单独的变换矩阵，首先使物体在 x 轴方向上放大 2 倍，在 y 轴方向上放大−3 倍，在 z 轴上保持不变。接着将物体沿 x 轴正方向平移 4 个单位，在 y 轴上保持不变，沿 z 轴正方向平移 −9 个单位。*
8. *构建一个单独的变换矩阵，首先令物体绕 y 轴旋转 45° 。接着使之沿 x 轴正方向平移−2 个单位， 沿 y 轴正方向平移 5 个单位，最后沿 z 轴正方向平移 1 个单位。*
9. *重新计算例 3.2，但是这次使其中的正方形在 x 轴方向上放大 1.5 倍，在 y 轴方向上缩小至 0.75 倍，在 z 轴上保持不变。最后，绘制出变换前后的几何体，以确定所得到结果是否正确。*
10. *重新计算例 3.2，但是这次使其中的正方形在 x 轴方向上放大 1.5 倍，在 y 轴方向上缩小至 0.75 倍，在 z 轴上保持不变。最后，绘制出变换前后的几何体，以确定所得到结果是否正确。*
11. *重新计算例 3.4，此次将该正方形在 x 轴正方向平移−5 个单位，在 y 轴正方向平移−3.0 个单位， 沿 z 轴正方向平移 4.0 个单位。最后，画出变换前后的几何体，确认所得到结果的正确性。*
12. **

## ***第一题***

*设 $ \tau = \mathbb{R}^3 → \mathbb{R}^3 $ 的定义为 $ \tau(x, y, z) = (x + y, x - 3, z) $ 。那么，$ \tau $ 是一种线性变换吗？如果是，求出它 的标准矩阵表示。*
$$
\begin{align*}
\tau(\vec{u} + \vec{v}) 
&= \tau(u_x + v_x, u_y + v_y, u_z + v_z)
\\\ \\\
&= (u_x + v_x + u_y + v_y, u_x + v_x -3, u_z + v_z)
\\\ \\\
\tau{\vec{u}} + \tau{\vec{v}}
&= (u_x + u_y, u_x - 3, u_z) + (v_x + v_y, v_x - 3, v_z) 
\\\ \\\
&= (u_x + v_x + u_y + v_y, u_x + v_x - 6, u_z + v_z)
\\\ \\\
\tau(\vec{u} + \vec{v}) &\neq \tau{\vec{u}} + \tau{\vec{v}}
\end{align*}
$$

## ***第二题***

*设 $ \tau = \mathbb{R}^3 → \mathbb{R}^3 $ 的定义为 $ \tau(x, y, z) = (3x + 4z, 2x - z, x + y + z) $ 。那么，$ \tau $是一种线性变换吗？如果是，求出它的标准矩阵表示。*
$$
\begin{align*}
\tau(\vec{u} + \vec{v}) 
&= \tau(u_x + v_x, u_y + v_y, u_z + v_z)
\\\ \\\
&= (3u_x + 3v_x + 4u_z + 4v_z, 2u_x + 2v_x - u_z - v_z, u_x + v_x + u_y + v_y + u_z + v_z)
\\\ \\\
\tau{\vec{u}} + \tau{\vec{v}}
&= (3u_x + 4u_z, 2u_x - u_z, u_x + u_y + u_z) + (3v_x + 4v_z, 2v_x - v_z, v_x + v_y + v_z) 
\\\ \\\
&= (3u_x + 3v_x + 4u_z + 4v_z, 2u_x + 2v_x - u_z - v_z, u_x + v_x + u_y + v_y + u_z + v_z)
\\\ \\\
\tau({k\vec{u}}) &=
\tau(ku_x, ku_y, ku_z)
\\\ \\\
&= (3ku_x + 4ku_z, 2ku_x - ku_z, ku_x + ku_y + ku_z)
\\\ \\\
k\tau({u}) &= k(3u_x + 4u_z, 2u_x - u_z, u_x + u_y + u_z)
\\\ \\\
&= (3ku_x + 4ku_z, 2ku_x - ku_z, ku_x + ku_y + ku_z)
\\\ \\\
因为 &~ \tau(\vec{u} + \vec{v}) = \tau{\vec{u}} + \tau{\vec{v}} ~ 并且 ~ \tau({k\vec{u}}) = k\tau({u})
\\\ \\\
故 &~ \tau ~ 为线性变换
\\\ \\\
\tau(x, y, z) &= (3x + 4z, 2x - z, x + y + z) 
\\\ \\\
&= \begin{bmatrix} 3x + 0y + 4z \\\ \\\ 2x + 0y - z \\\ \\\ x + y + z \end{bmatrix}
\\\ \\\
& \begin{bmatrix} 3 & 0 & 4 \\\ \\\ 2 & 0 & -1 \\\ \\\ 1 & 1 & 1 \end{bmatrix} 
\begin{bmatrix} x \\\ \\\ y \\\ \\\ z \end{bmatrix} =
\begin{bmatrix} 3x + 0y + 4z \\\ \\\ 2x + 0y - z \\\ \\\ x + y + z \end{bmatrix}
\\\ \\\
& 所以 ~ \tau ~ 的标准矩阵为 ~~~ \begin{bmatrix} 3 & 0 & 4 \\\ \\\ 2 & 0 & -1 \\\ \\\ 1 & 1 & 1 \end{bmatrix} 
\end{align*} 
$$

## ***第三题***

*设 $ \tau = \mathbb{R}^3 → \mathbb{R}^3 $ 是一种线性变换，而且 $ \tau(1, 0, 0) = (3, 1, 2)，\tau(0, 1, 0) = (2, -1, 3)，\tau(0, 0, 1) = (4, 0, 2) $, 求 $ \tau(1, 1, 1)$*
$$
\begin{align*}
A &= \begin{bmatrix} 3 & 1 & 2 \\\ \\\ 2 & -1 & 3 \\\ \\\ 4 & 0 & 2 \end{bmatrix}
\\\ \\\
\tau(1, 1, 1) &= \begin{bmatrix} 1 & 1 & 1 \end{bmatrix} \begin{bmatrix} 3 & 1 & 2 \\\ \\\ 2 & -1 & 3 \\\ \\\ 4 & 0 & 2 \end{bmatrix}
\\\ \\\
&= \begin{bmatrix} 3 + 2 + 4 & 1 - 1 + 0 & 2 + 3 + 2 \end{bmatrix}
\\\ \\\
&= \begin{bmatrix} 9 & 0 & 7 \end{bmatrix}
\end{align*}
$$

## ***第四题***

*构建一个缩放矩阵，使物体在 x 轴方向上放大 2 倍，在 y 轴方向上放大 − 3 倍，在 z 轴方向上保持不变。*
$$
\begin{bmatrix} 2 & 0 & 0 \\\ \\\ 0 & -3 & 0 \\\ \\\ 0 & 0 & 1 \end{bmatrix}
$$

## ***第五题***

*构建一个旋转矩阵，使物体绕轴(1, 1, 1)旋转 30°*
$$
\begin{align*}
& 1. 首先将轴向量(1, 1, 1) 规范为单位向量
\\\ \\\
\vec{u} &= \frac{(1, 1, 1)}{\sqrt{1^2 + 1^2 + 1^2}}
\\\ \\\
&= (\frac{1}{\sqrt{3}}, \frac{1}{\sqrt{3}}, \frac{1}{\sqrt{3}})
\\\ \\\
& 2. 我们再构建其旋转矩阵
\\\ \\\
R_n &= 
\begin{bmatrix}
c + (1 - c)x^2 & (1-c)xy + sz & (1-c)xz - sy
\\\ \\\
(1-c)xy - sz & c + (1 - c)y^2 & (1-c)yz + sx
\\\ \\\
(1-c)xz + sy & (1-c)yz - sx & c + (1-c)z^2
\end{bmatrix}
\\\ \\\
&= 
\begin{bmatrix}
\frac{\sqrt{3}}{2} + \frac{1}{3}\left(1-\frac{\sqrt{3}}{2}\right) & \frac{1}{3}\left(1-\frac{\sqrt{3}}{2}\right) - \frac{1}{\sqrt{3}}\cdot\frac{1}{2} & \frac{1}{3}\left(1-\frac{\sqrt{3}}{2}\right) + \frac{1}{\sqrt{3}}\cdot\frac{1}{2} \\\ \\\
\frac{1}{3}\left(1-\frac{\sqrt{3}}{2}\right) + \frac{1}{\sqrt{3}}\cdot\frac{1}{2} & \frac{\sqrt{3}}{2} + \frac{1}{3}\left(1-\frac{\sqrt{3}}{2}\right) & \frac{1}{3}\left(1-\frac{\sqrt{3}}{2}\right) - \frac{1}{\sqrt{3}}\cdot\frac{1}{2} \\\ \\\
\frac{1}{3}\left(1-\frac{\sqrt{3}}{2}\right) - \frac{1}{\sqrt{3}}\cdot\frac{1}{2} & \frac{1}{3}\left(1-\frac{\sqrt{3}}{2}\right) + \frac{1}{\sqrt{3}}\cdot\frac{1}{2} & \frac{\sqrt{3}}{2} + \frac{1}{3}\left(1-\frac{\sqrt{3}}{2}\right)
\end{bmatrix} 
\\\ \\\
&= 
\begin{bmatrix}
\frac{2\sqrt{3} + 2}{6} & \frac{1}{3} & \frac{1 - \sqrt{3}}{3} \\\ \\\
\frac{1 - \sqrt{3}}{3} & \frac{2\sqrt{3} + 2}{6} & \frac{1}{3} \\\ \\\
\frac{1}{3} & \frac{1 - \sqrt{3}}{3} & \frac{2\sqrt{3} + 2}{6}
\end{bmatrix}
\end{align*}
$$

## ***第六题***

*构建一个平移矩阵，使物体沿 x 轴正方向平移 4 个单位，在 y 轴方向保持不变，沿 z 轴正方向平移−9 个单位。*
$$
\begin{bmatrix}
1 & 0 & 0 & 0
\\\ \\\
0 & 1 & 0 & 0
\\\ \\\
0 & 0 & 1 & 0
\\\ \\\
4 & 1 & -9 & 1
\end{bmatrix}
$$

## ***第七题***

*构建一个单独的变换矩阵，首先使物体在 x 轴方向上放大 2 倍，在 y 轴方向上放大−3 倍，在 z 轴上保持不变。接着将物体沿 x 轴正方向平移 4 个单位，在 y 轴上保持不变，沿 z 轴正方向平移 −9 个单位。*
$$
S = \begin{bmatrix}
2 & 0 & 0 & 0 \\\ \\\ 0 & -3 & 0 & 0 \\\ \\\ 0 & 0 & 1 & 0 \\\ \\\ 0 & 0 & 0 & 1
\end{bmatrix}
\~~~~~~~~~~~~~~
T = \begin{bmatrix}
1 & 0 & 0 & 0 \\\ \\\ 0 & 1 & 0 & 0 \\\ \\\ 0 & 0 & 1 & 0 \\\ \\\ 4 & 0 & -9 & 1
\end{bmatrix}
\\\ \\\ \\\ \\\
ST = \begin{bmatrix}
2 & 0 & 0 & 0 \\\ \\\ 0 & -3 & 0 & 0 \\\ \\\ 0 & 0 & 1 & 0 \\\ \\\ 4 & 0 & -9 & 1
\end{bmatrix}
$$

## ***第八题***

*构建一个单独的变换矩阵，首先令物体绕 y 轴旋转 45° 。接着使之沿 x 轴正方向平移−2 个单位， 沿 y 轴正方向平移 5 个单位，最后沿 z 轴正方向平移 1 个单位。*
$$
R = 
\begin{bmatrix}
\cos45^\circ & 0 & -\sin45^\circ & 0
\\\ \\\
0 & 1 & 0 & 0
\\\ \\
\sin45^\circ & 0 & \cos45^\circ & 0
\\\ \\\
0 & 0 & 0 & 0
\end{bmatrix}
= \begin{bmatrix}
\frac{\sqrt{2}}{2} & 0 & -\frac{\sqrt{2}}{2} & 0
\\\ \\\
0 & 1 & 0 & 0
\\\ \\
\frac{\sqrt{2}}{2} & 0 & \frac{\sqrt{2}}{2} & 0
\\\ \\\
0 & 0 & 0 & 0
\end{bmatrix}
\~~~~~~~~~
T = 
\begin{bmatrix}
1 & 0 & 0 & 0
\\\ \\\
0 & 1 & 0 & 0
\\\ \\\
0 & 0 & 1 & 0
\\\ \\\
-2 & 5 & 1 & 1
\end{bmatrix}
\\\ \\\
RT = 
\begin{bmatrix}
\frac{\sqrt{2}}{2} & 0 & -\frac{\sqrt{2}}{2} & -\sqrt{2} \\\ 
0 & 1 & 0 & 5 \\\ \\\
\frac{\sqrt{2}}{2} & 0 & \frac{\sqrt{2}}{2} & -\frac{\sqrt{2}}{2} \\\ \\\
-2 & 5 & 1 & 1
\end{bmatrix}
$$

