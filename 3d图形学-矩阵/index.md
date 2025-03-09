# 矩阵


# ***矩阵***

## ***矩阵的定义***

*一个规模为 m × n  的矩阵（matrix）M，是由 m 行 n 列实数所构成的矩形阵列。行数和列数的乘积表示了矩阵的维度。*

*矩阵中的数字则称作元素（element）或元（entry）。通过双下标表示法 M<sub>ij</sub>指定元素
的行和列就可以确定出对应的矩阵元素，M<sub>ij</sub>表示的是矩阵中第 i 行、第 j 列的元素。*

### ***例子***

$$
A = \quad \begin{bmatrix} 3.5 & 0 & 0 & 0 \\\ 0 & 1 & 0 & 0 \\\ 0 & 0 & 0.5 & 0 \\\ 2 & -5 & \sqrt{2} & 1 \end{bmatrix} ~~~~~~~~ 
B = \quad \begin{bmatrix} B_{11} & B_{12} \\\ B_{21} & B_{22} \\\ B_{31} & B_{32}  \end{bmatrix} ~~~~~~~~
C = \quad \left[u_1, u_2 \right] ~~~~~~~~
D = \quad \begin{bmatrix} 1 \\\ 2 \\\ \sqrt{3} \\\ \pi \end{bmatrix}
$$

1. *A是一个 4 x 4 矩阵，B是一个 3 x 2 矩阵，u 是一个特殊的 1 x 3矩阵，v是一个 4 x 1 矩阵*

2. *我们通过双下标表示法  A<sub>42</sub> =−5 将矩阵 A 中第 4 行第 2 列的元素指定为−5，并以  B<sub>21</sub>表示矩阵 B中第 2 行第 1 列的这一元素。*

3. *u & v 是特殊的矩阵，其实他们就是向量，分别只用一行或者一列来表示矩阵，有时候也被称为行向量或者列向量，我们只需要使用单下标表示就好了*

4. *在某些情况下，我们倾向于把矩阵的每一行都看作一个向量*
   
   比如 A<sub>1,\*</sub> 第一个索引 1 表示特定的行，第二个索引"\*" 表示该行的整个向量&#42
   
   自然而然，A<sub>*,1</sub> 第一个索引"*" 表示该列的整个向量，第二个表示特定的列

## ***算数运算***

$$
A = \begin{bmatrix} 1 & 5 \\\ -2 & 3 \end{bmatrix} ~~~~~~~
B = \begin{bmatrix} 6 & 2 \\\ 5 & -8 \end{bmatrix} ~~~~~~~
C = \begin{bmatrix} 1 & 5 \\\ -2 & 3 \end{bmatrix} ~~~~~~~
D = \begin{bmatrix} 2 & 1 & -3 \\\ -6 & 3 & 0 \end{bmatrix} ~~~~~~~
$$

### ***相等***

*A = C*

### ***加减***

$$
A + B = \begin{bmatrix} 1 & 5 \\\ -2 & 3 \end{bmatrix} + \begin{bmatrix} 6 & 2 \\\ 5 & -8 \end{bmatrix}
= \begin{bmatrix} 1+6 & 5+2 \\\ -2+5 & 3+(-8) \end{bmatrix}
= \begin{bmatrix} 7 & 7 \\\ 3 & -5 \end{bmatrix}
$$

### ***标量相乘***

$$
3D = 3\begin{bmatrix} 2 & 1 & -3 \\\ -6 & 3 & 0 \end{bmatrix}
= \begin{bmatrix} 3(2) & 3(1) & 3(-3) \\\ 3(-6) & 3(3) & 3(0) \end{bmatrix}
= \begin{bmatrix} 6 & 3 & -9 \\\ -18 & 9 & 0 \end{bmatrix}
$$

## ***矩阵乘法***

### ***定义***

*如果 A 是一个 m × n  矩阵，B 是一个 n × p  矩阵，那么，两者乘积 AB 的结果是一个规模为 m × p  的矩阵 C。矩阵 C 中第 i 行、第 j 列的元素，由矩阵 A 的第 i 个行向量与矩阵 B 的第 j 个列向量的点积求得*

***为了使矩阵乘积 AB 有意义，矩阵 A 中的列数与矩阵 B 中的行数必须相同 !***

#### ***不能运算的例子***

$$
A = \begin{bmatrix} 1 & 5 \\\ -2 & 3 \end{bmatrix}
\quad 和 \quad
B = \begin{bmatrix} 2 & -6 \\\ 1 & 3 \\\ -3 & 0 \end{bmatrix}
$$

*因为矩阵 A 的行向量维数为 2，矩阵 B 的列向量维数为 3，所以乘积 AB 无定义。*

*不妨这么想，由于 2D向量不能与 3D 向量进行点积计算，因此，矩阵 A 中的第一个行向量与矩阵 B 中的第一个列向量也就无法开展点积运算*

#### ***能运算的例子***

$$
A = \begin{bmatrix} -1 & 5 & -4 \\\ 3 & 2 & 1 \end{bmatrix}
\quad 和 \quad
B = \begin{bmatrix} 2 & 1 & 0 \\\ 0 & -2 & 1 \\\ -1 & 2 & 3 \end{bmatrix}
$$

*由于矩阵 A 的列数与矩阵 B 的行数相同，可首先指出乘积 AB 是有意义的*

*我们还可以发现乘积 BA 却没有意义，因为矩阵 B 的列数不等于矩阵 A 的行数。这表明，**矩阵的乘法一般不满足交换律，即 AB ≠ BA***
$$
\begin{align} 
AB &= \begin{bmatrix} -1 & 5 & -4 \\\ 3 & 2 & 1 \end{bmatrix}
\begin{bmatrix} 2 & 1 & 0 \\\ 0 & -2 & 1 \\\ -1 & 2 & 3 \end{bmatrix} 
\\\ \\\
&= \begin{bmatrix} (-1,5,-4) ~ \cdot ~ (2, 0, -1) & (-1,5,-4) \cdot (1,-2,2) & (-1,5,-4) \cdot (0,1,3) \\\
(3,2,1) \cdot (2,0.-1) & (3,2,1) \cdot (1,-2,2) & (3,2,1) \cdot (0,1,3) \end{bmatrix} 
\\\ \\\
&= \begin{bmatrix} 2 & -19 & -7 \\\ 5 & 1 & 5 \end{bmatrix}
\end{align}
$$

### ***向量与矩阵的乘法***

$$
\vec{u}A = \begin{bmatrix} x,y,z \end{bmatrix} \begin{bmatrix} A_{11} & A_{12} & A_{13} \\\ A_{21} & A_{22} & A_{23} \\\ A_{31} & A_{32} & A_{33} \end{bmatrix} 
= \begin{bmatrix} x,y,z \end{bmatrix} \begin{bmatrix} \uparrow & \uparrow & \uparrow \\\ A_{\*,1} & A_{\*,2} & A_{\*,3} \\\ \downarrow & \downarrow & \downarrow \end{bmatrix}
$$

*可以观察到：该例中，uA 的计算结果是一个规模为 1 × 3 的行向量*


$$
\begin{align}
uA &= \left[ \vec{u} \cdot A_{\*,1}, \vec{u} \cdot A_{\*,2}, \vec{u} \cdot A_{\*,3} \right] 
\\\ \\\
&= \left[ xA_{11} + yA_{21} + zA_{31}, xA_{12} + yA_{22} + zA_{32}, xA_{13} + yA_{23} + zA_{33} \right] 
\\\ \\\
&= \left[ xA_{11}, xA_{12}, xA_{13} \right] + \left[ yA_{21}, yA_{22}, yA_{23} \right] + \left[ zA_{31}, zA_{32}, zA_{33} \right] 
\\\ \\\
&= x\left[ A_{11}, A_{12}, A_{13} \right] + y\left[ A_{21}, A_{22}, A_{23} \right] + z\left[ A_{31}, A_{32}, A_{33} \right] 
\\\ \\\
&= xA_{1,\*} + yA_{2,\*} + zA_{3,\*}
\end{align}
$$
*这个结果实为一种线性组合（linear combination），这意味着向量与矩阵的乘积 uA 就相当于：向量*

*u 给定的标量系数 x、y、z 与矩阵 A 中各行向量的线性组合。注意，尽管我们只展示了1 × 3  行向量与 3 × 3*
*矩阵的乘法，但是这个结论却具有一般性。也就是说，对于一个 1 × n 行向量 u 与一个 n × m 矩阵 A，我们总可得到 u 所给出的标量系数与 A 中诸行向量的线性组合 uA*

## ***转置矩阵***

*转置矩阵（transpose matrix）指的是将原矩阵的行与列进行互换所得到的新矩阵。所以，根据一个 m × n 矩阵可得到一个规模为 n × m的转置矩阵。我们将矩阵 M 的转置矩阵记作 M<sup>T</sup>*

### ***例子***

$$
\begin{gather}
A = \begin{bmatrix} 2 & -1 & 8 \\\ 3 & 6 & -4 \end{bmatrix} ~~~~~~~~
B = \begin{bmatrix} a & b & c \\\ d & e & f \\\ g & h & i \end{bmatrix} ~~~~~~~~
C = \begin{bmatrix} 1 \\\ 2 \\\ 3 \\\ 4 \end{bmatrix}
\\\ \\\
A^T = \begin{bmatrix} 2 & 3 \\\ -1 & 6 \\\ 8 & -4 \end{bmatrix} ~~~~~~~~
B^T = \begin{bmatrix} a & d & g \\\ b & e & h \\\ c & f & i \end{bmatrix} ~~~~~~~~
C^T = \begin{bmatrix} 1 & 2 & 3 & 4 \end{bmatrix}
\end{gather}
$$

### ***实用性质***

1. *( A + B )<sup>T</sup> = A<sup>T</sup> + B<sup>T</sup>*

2. *( cA )<sup>T</sup> = cA<sup>T</sup>*

3. *( AB )<sup>T</sup> = B<sup>T</sup>A<sup>T</sup>*

4. *( A<sup>T</sup> )<sup>T</sup>  = A*

5. *( A<sup>-1</sup> )<sup>T</sup> = ( A<sup>T</sup> )<sup>-1</sup>*

## ***单位矩阵***

*单位矩阵（identity matrix）比较特殊，是一种主对角线上的元素均为 1，其他元素都为 0 的方阵。例如，下列依次是规模为 2 × 2， 3 × 3  和 4 × 4 的单位矩阵*
$$
\begin{bmatrix} 1 & 0 \\\ 0 & 1 \end{bmatrix}
\begin{bmatrix} 1 & 0 & 0 \\\ 0 & 1 & 0 \\\ 0 & 0 & 1 \end{bmatrix}
\begin{bmatrix} 1 & 0 & 0 & 0 \\\ 0 & 1 & 0 & 0 \\\ 0 & 0 & 1 & 0 \\\ 0 & 0 & 0 & 1 \end{bmatrix}
$$
*单位矩阵是矩阵的乘法单位元（multiplicative identity）。即如果 A 为 m n × 矩阵，B 为 n × p  矩阵，而 I 为 n × n 的单位矩阵，那么 **AI = A 且 IB = B**;*

*换句话说，**任何矩阵与单位矩阵相乘，得到的依然是原矩阵**。我们可以将单位矩阵看作是矩阵中的“数字 1”。特别地，如果 M 是一个方阵，那么它与单位矩阵的乘法满足交换律：**MI = IM = M***

### ***证明  MI = IM = M***

$$
\begin{gather}
设 ~ M 
= \begin{bmatrix} 1 & 2 \\\ 0 & 4 \end{bmatrix} ~ 以及 ~ I = \begin{bmatrix} 1 & 0 \\\ 0 & 1 \end{bmatrix}
\\\ \\\
MI 
= \begin{bmatrix} 1 & 2 \\\ 0 & 4 \end{bmatrix} \begin{bmatrix} 1 & 0 \\\ 0 & 1 \end{bmatrix}
= \begin{bmatrix} (1, 2) \cdot (1,0) & (1,2) \cdot (0,1) \\\ (0,4) \cdot (1,0) & (0,4) \cdot (0,1) \end{bmatrix}
= \begin{bmatrix} 1 & 2 \\\ 0 & 4 \end{bmatrix}
\\\ \\\
IM 
= \begin{bmatrix} 1 & 0 \\\ 0 & 1 \end{bmatrix} \begin{bmatrix} 1 & 2 \\\ 0 & 4 \end{bmatrix}
= \begin{bmatrix} (1, 0) \cdot (1,0) & (1,0) \cdot (2,4) \\\ (0,1) \cdot (1,0) & (0,1) \cdot (2,4) \end{bmatrix}
= \begin{bmatrix} 1 & 2 \\\ 0 & 4 \end{bmatrix}
\\\ \\\
所以 ~ MI = IM = M ~ 是成立的
\end{gather}
$$

### ***证明 AI = A***

$$
\begin{gather}
设 ~ \vec{u} = \begin{bmatrix} -1, & 2 \end{bmatrix} 且 ~ I = \begin{bmatrix} 1 & 0 \end{bmatrix} ~ 验证 ~ \vec{u}I = \vec{u}
\\\ \\\
\vec{u}I = \begin{bmatrix} -1 & 2 \end{bmatrix} \begin{bmatrix} 1 & 0 \end{bmatrix}
= \begin{bmatrix} (-1,2) \cdot (1,0), & (-1,2) \cdot (0,1) \end{bmatrix} 
= \begin{bmatrix} -1, & 2 \end{bmatrix}
\\\ \\\
所以 ~ \vec{u}I = \vec{u} ~ 另外我们也可以看出，我们无法计算乘积 ~ I\vec{u}, 因为此矩阵乘法是无定义的
\end{gather}
$$

## ***矩阵的行列式***

*行列式是一个特殊的函数，他以一个方阵作为输出，并输出一个实数。方阵A的行列式通常表示为 $ \det(A) $*

*我们可以从几何的角度来解释行列式。行列式反映了在线性变换下，（n 维多面体）体积变化的相关信息。另外，行列式也应用于解线性方程组的克莱姆法则（Cramer’s Rule，亦称克莱默法则）。*

*然而，我们在此学习行列式的主要目的是：**利用它推导出求逆矩阵的公式***

*此外， 行列式还可以用于证明：**方阵 A 是可逆的，当且仅当 det A ≠ 0 ** 。这个结论很实用，因为它为我们确认 矩阵的可逆性提供了一种行之有效的计算工具。不过在定义行列式之前，我们先要介绍一下余子阵的 概念*

### ***余子阵***

*指定一个 n × n 的矩阵 A，余子阵（minor matrix）$ \overline{A}_{ij} $ 即为从 A 中去除第 i 行和第 j 列的  (n-1)x(n-1) 矩阵。*

#### ***例子***

$$
\begin{gather}
求出下列矩阵的余子阵 \overline{A}\_{11} ~ \overline{A}\_{22} ~ \overline{A}\_{13}
\\\ \\\
A = \begin{bmatrix} A\_{11} & A\_{12} & A\_{13} \\\ A\_{21} & A\_{22} & A\_{23} \\\ A\_{31} & A\_{32} & A\_{33} \end{bmatrix}
\\\ \\\
去除矩阵A的第一行和第一列，得到 \overline{A}\_{11} 为:
A = \begin{bmatrix} A\_{22} & A\_{23} \\\ A\_{32} & A\_{33} \end{bmatrix}
\\\ \\\
去除矩阵A的第二行和第二列，得到 \overline{A}\_{22} 为:
A = \begin{bmatrix} A\_{11} & A\_{13} \\\ A\_{31} & A\_{33} \end{bmatrix}
\\\ \\\
去除矩阵A的第一行和第三列，得到 \overline{A}\_{13} 为:
A = \begin{bmatrix} A\_{21} & A\_{22} \\\ A\_{31} & A\_{32} \end{bmatrix}
\end{gather}
$$



### ***行列式的定义***

*矩阵的行列式有一种递归定义*

*例如，一个 4 × 4 矩阵的行列式要根据 3 × 3 矩阵的行列式来定义，而 3 × 3 矩阵的行列式要靠 2 × 2 矩阵的行列式来定义，最后， 2 × 2 矩阵的行列式则依赖于1 × 1矩阵的行列式来定义*
$$
1 \times 1 矩阵A = [A_{11}] 的行列式被简单的定义为\det{[A_{11}]} = A_{11}
$$

#### ***例子***

$$
\begin{gather}
设 A 为一个 n \times n 的矩阵. 那么 n > 1时， 我们定义:
\\\ \\\
\det{A} = \sum_{j=1}^{n} A_{1j}(-1)^{1+j} \det{\overline{A}\_{ij}}
\\\ \\\
对照余子阵\overline{A}\_{ij}的定义可知，对于 2 \times 2 矩阵来说，其相应的行列式公式为:
\\\ \\\
\det{\begin{bmatrix} A_{11} & A_{12} \\\ A_{21} & A_{22} \end{bmatrix}}
= A_{11} \det[A_{22}] - A_{12} \det{[A_{21}]} = A_{11}A_{22} - A_{12}A_{21}
\\\ \\\
对于3 \times 3矩阵来说，其行列式计算公式为：
\\\ \\\
\det{\begin{bmatrix} A_{11} & A_{12} & A_{13} \\\ A_{21} & A_{22} & A_{23} \\\ A_{31} & A_{32} & A_{33} \end{bmatrix}} 
= A_{11} \det{\begin{bmatrix} A_{22} & A_{23} \\\ A_{32} & A_{33} \end{bmatrix}} 
\- A_{12} \det{\begin{bmatrix} A_{21} & A_{23} \\\ A_{31} & A_{33} \end{bmatrix}}
\+ A_{13} \det{\begin{bmatrix} A_{21} & A_{22} \\\ A_{31} & A_{32} \end{bmatrix}}
\\\ \\\
对于 4 \times 4 矩阵, 其行列式计算公式为：
\\\ \\\
\det{\begin{bmatrix} A_{11} & A_{12} & A_{13} & A_{14} \\\ A_{21} & A_{22} & A_{23} & A_{24} \\\ A_{31} & A_{32} & A_{33} & A_{34} \\\ A_{41} & A_{42} & A_{43} & A_{44} \end{bmatrix}}
= A_{11} \det{\begin{bmatrix} A_{22} & A_{23} & A_{24} \\\ A_{} \end{bmatrix}}
\end{gather}
$$

## ***伴随矩阵***

*设A为一个 n x n 矩阵。*

*乘积 $ C_{ij} = (-1)^{i+j}\det{\overline{A}\_ij} $称为元素 $ A_{ij} $的代数余子式*

*如果为矩阵A中的每个元素分别计算出 $ C_{ij} $，并将他置于 $ C_A $中的第i行，第j列的相应位置, 那么将获得矩阵A的代数余子式;*

*若取矩阵 $ C_A $ 的转置矩阵, 将得到矩阵A的伴随矩阵, 记作: $ A^* = C^T_A $*

## ***逆矩阵***

*矩阵代数不存在除法运算的概念，但是却另外定义了一种矩阵乘法的逆运算。下面总结了与矩阵逆 运算有关的关键信息。*

1. *只有方阵才具有逆矩阵。因此，当提到逆矩阵时，我们便假设要处理的是一个方阵。*
2. *n x n 矩阵 M 的逆也是一个 n x n矩阵，并表示为 $ M^-1 $。*
3. *不是每个方阵都有逆矩阵。存在逆矩阵的方阵称为可逆矩阵（invertible matrix），不存在逆矩阵 的方阵称作奇异矩阵（singular matrix）。*
4. *可逆矩阵的逆矩阵是唯一的。*
5. *矩阵与其逆矩阵相乘将得到单位方阵：$ MM^-1 = M^-1M = I $。可以发现，矩阵与其逆矩阵的乘法运 算满足交换律。*

## ***基本运算定律***

*由于在矩阵的加法和标量乘法的运算过程中，是以元素为单位展开计算的，所以它们实际上也分别从实数运算中继承了下列性质*

1. *A + B = B + A 加法交换律*
2. *(A + B) + C = A + (B + C) 加法结合律*
3. *r (A + B) = rA + rB 标量乘法对矩阵加法的分配律*
4. *(r + s) A = rA + sA 矩阵乘法对标量加法的分配律*

### 

