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
&= \begin{bmatrix} (-1,5,-4) ~ \cdot ~ (2, 0, -1) & (-1,~5,~-4) \cdot (1,-2,2) & (-1,5,-4) \cdot (0,1,3) \\\
(3,2,1) \cdot (2,0.-1) & (3,2,1) \cdot (1,-2,2) & (3,2,1) \cdot (0,1,3) \end{bmatrix} 
\\\ \\\
&= \begin{bmatrix} 2 & -19 & -7 \\\ 5 & 1 & 5 \end{bmatrix}
\end{align}
$$

### ***向量与矩阵的乘法***

$$
\vec{u}A = \begin{bmatrix} x,y,z \end{bmatrix} \begin{bmatrix} A_{11} & A_{12} & A_{13} \\\ A_{21} & A_{22} & A_{23} \\\ A_{31} & A_{32} & A_{33} \end{bmatrix} 
= \begin{bmatrix} x,y,z \end{bmatrix} \begin{bmatrix} \uparrow & \uparrow & \uparrow \\\ A_{*,1} & A_{*,2} & A_{*,3} \\\ \downarrow & \downarrow & \downarrow \end{bmatrix}
$$

*可以观察到：该例中，uA 的计算结果是一个规模为 1 × 3 的行向量*
$$
\begin{align}
uA = \left[ \vec{u} \cdot A_{*,1}, \vec{u} \cdot A_{*,2}, \vec{u} \cdot A_{*,3} \right] 
\\\ \\\
= \left[ xA_{11} + yA_{21} + zA_{31}, xA_{12} + yA_{22} + zA_{32}, xA_{13} + yA_{23} + zA_{33} \right] 
\\\ \\\
= \left[ xA_{11}, xA_{12}, xA_{13} \right] + \left[ yA_{21}, yA_{22}, yA_{23} \right] + \left[ zA_{31}, zA_{32}, zA_{33} \right] 
\\\ \\\
= x\left[ A_{11}, A_{12}, A_{13} \right] + y\left[ A_{21}, A_{22}, A_{23} \right] + z\left[ A_{31}, A_{32}, A_{33} \right] 
\\\ \\\
&= xA_{1,*} + yA_{2,*} + zA_{3,*}
\end{align}
$$
*这个结果实为一种线性组合（linear combination），这意味着向量与矩阵的乘积 uA 就相当于：向量*

*u 给定的标量系数 x、y、z 与矩阵 A 中各行向量的线性组合。注意，尽管我们只展示了1 × 3  行向量与 3 × 3*
*矩阵的乘法，但是这个结论却具有一般性。也就是说，对于一个 1 × n 行向量 u 与一个 n × m 矩阵 A，我们总可得到 u 所给出的标量系数与 A 中诸行向量的线性组合 uA*

## ***转置矩阵***

*转置矩阵（transpose matrix）指的是将原矩阵的行与列进行互换所得到的新矩阵。所以，根据一个 m × n 矩阵可得到一个规模为 n × m的转置矩阵。我们将矩阵 M 的转置矩阵记作 M<sup>T</sup>*

### ***例子***

$$
A = \begin{bmatrix} 2 & -1 & 8 \\\ 3 & 6 & -4 \end{bmatrix} ~~~~~~~~
B = \begin{bmatrix} a & b & c \\\ d & e & f \\\ g & h & i \end{bmatrix} ~~~~~~~~
C = \begin{bmatrix} 1 \\\ 2 \\\ 3 \\\ 4 \end{bmatrix}
\\\
\\\
A^T = \begin{bmatrix} 2 & 3 \\\ -1 & 6 \\\ 8 & -4 \end{bmatrix} ~~~~~~~~
B^T = \begin{bmatrix} a & d & g \\\ b & e & h \\\ c & f & i \end{bmatrix} ~~~~~~~~
C^T = \begin{bmatrix} 1 & 2 & 3 & 4 \end{bmatrix}
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
设 ~ M= \begin{bmatrix} 1 & 2 \\\ 0 & 4 \end{bmatrix} ~ 以及 ~ I = \begin{bmatrix} 1 & 0 \\\ 0 & 1 \end{bmatrix}
\\\ \\\
MI = \begin{bmatrix} 1 & 2 \\\ 0 & 4 \end{bmatrix} \begin{bmatrix} 1 & 0 \\\ 0 & 1 \end{bmatrix}
= \begin{bmatrix} (1, 2) \cdot (1,0) & (1,2) \cdot (0,1) \\\ (0,4) \cdot (1,0) & (0,4) \cdot (0,1) \end{bmatrix}
= \begin{bmatrix} 1 & 2 \\\ 0 & 4 \end{bmatrix}
\\\ \\\
IM = \begin{bmatrix} 1 & 0 \\\ 0 & 1 \end{bmatrix} \begin{bmatrix} 1 & 2 \\\ 0 & 4 \end{bmatrix}
= \begin{bmatrix} (1, 0) \cdot (1,0) & (1,0) \cdot (2,4) \\\ (0,1) \cdot (1,0) & (0,1) \cdot (2,4) \end{bmatrix}
= \begin{bmatrix} 1 & 2 \\\ 0 & 4 \end{bmatrix}
\\\ \\\
所以 ~ MI = IM = M ~ 是真情古鳄的
$$

### ***证明 AI = A***

$$
设 ~ \vec{u} = \begin{bmatrix} -1, & 2 \end{bmatrix} 且 ~ I = \begin{bmatrix} 1 & 0 \end{bmatrix} ~ 验证 ~ \vec{u}I = \vec{u}
\\\ \\\
\vec{u}I = \begin{bmatrix} -1 & 2 \end{bmatrix} \begin{bmatrix} 1 & 0 \end{bmatrix}
= \begin{bmatrix} (-1,2) \cdot (1,0), & (-1,2) \cdot (0,1) \end{bmatrix} 
= \begin{bmatrix} -1, & 2 \end{bmatrix}
\\\ \\\
所以 ~ \vec{u}I = \vec{u} ~ 另外我们也可以看出，我们无法计算乘积 ~ I\vec{u}, 因为此矩阵乘法是无定义的
$$

## ***矩阵的行列式***

## ***基本运算定律***

*由于在矩阵的加法和标量乘法的运算过程中，是以元素为单位展开计算的，所以它们实际上也分别从实数运算中继承了下列性质*

1. *A + B = B + A 加法交换律*
2. *(A + B) + C = A + (B + C) 加法结合律*
3. *r (A + B) = rA + rB 标量乘法对矩阵加法的分配律*
4. *(r + s) A = rA + sA 矩阵乘法对标量加法的分配律*

