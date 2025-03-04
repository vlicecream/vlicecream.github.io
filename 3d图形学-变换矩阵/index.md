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

*现假设 $ \tau 是一种线性变换，根据他的线性性质能够得到$*
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
*其中$ \tau\vec{i} = A_{11} & A_{12} & A_{13}，\tau\vec{j} = A_{21} & A_{22} & A_{23}，\tau\vec{k} = A_{31} & A_{32} & A_{33} $。*

*我们称矩阵 A 是线性变换 τ 的矩 阵表示法。*

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
变换的效果如图所示

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
   R_n = 
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
+ [b_x, b_y, b_z]
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
\=
[4, -8, 0 ,1]

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
=
[10, -2, 0 ,1]
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


