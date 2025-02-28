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

*设 $ \vec{u} = (x, y, z)，我们也可以将它写作 $*
$$
\vec{u} = (x, y, z) = x\vec{i} + y\vec{j} + z\vec{k} = x(1, 0, 0) + y(0,1,0) + z(0,0,1)
$$

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
S(i) = (s_x \cdot 1, s_y \cdot 0, s_z \cdot 0) = (s_x, 0, 0)
\\\ \\\
s(j) = (s_x \cdot 0, s_y \cdot 1, s_z \cdot 0) = (0, s_y, 0)
\\\ \\\
s(k) = (s_x \cdot 0, s_y \cdot 0, s_z \cdot 1) = (0, 0, s_z)
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

![image-20250227222204332](C:\Users\peng.xu\AppData\Roaming\Typora\typora-user-images\image-20250227222204332.png)

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
   $$

*旋转矩阵有个有趣的性质：每个行向量都为单位长度且两两正交（请分别证明）。*

*也就是说，这些行 向量都是规范正交的（orthonormal，即互相正交且具有单位长度）。*

*若一个矩阵的行向量都是规范正交 的，则称此矩阵为正交矩阵（orthogonal matrix）。*

*正交矩阵有个引人注目的性质，即它的逆矩阵与转置 矩阵是相等的：*
$$
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

*根据问题所述可知， n =  (0, 1, 0) ，代入 $ R_n $ 并进行化简， 可得到 y 轴的旋转矩阵为：*
$$
R_y = 
\begin{bmatrix}
cos\theta & 0 & -sin\theta
\\\ \\\
0 & 1 & 0
\\\ \\\
sin\theta & 0 & cos\theta
\end{bmatrix}
\=
\begin{bmatrix}
cos(-30\textdegree) & 0 & -sin(-30\textdegree)
\\\ \\\
0 & 1 & 0
\\\ \\\
sin(-30\textdegree) & 0 & cos(-30\textdegree)
\end{bmatrix}
\= 
\begin{bmatrix}
\frac{\sqrt{3}}{2} & 0 & \frac{1}{2}
\\\ \\\
0 & 1 & 0
\\\ \\\
-\frac{1}{2} & 0 & \frac{\sqrt{3}}{2}
\end{bmatrix}
$$
*为了旋转该正方形，还需将其最小点、最大点坐标分别乘以得到的旋转矩阵：*
$$
[-1, 0, -1]
\begin{bmatrix}
\frac{\sqrt{3}}{2} & 0 & \frac{1}{2}
\\\ \\\
0 & 1 & 0
\\\ \\\
-\frac{1}{2} & 0 & \frac{\sqrt{3}}{2}
\end{bmatrix}
\thickapprox
[-0.36,0,-1.36]
\\\ \\\
[1, 0, 1]
\begin{bmatrix}
\frac{\sqrt{3}}{2} & 0 & \frac{1}{2}
\\\ \\\
0 & 1 & 0
\\\ \\\
-\frac{1}{2} & 0 & \frac{\sqrt{3}}{2}
\end{bmatrix}
\thickapprox
[0.36,0,1.36]
$$

