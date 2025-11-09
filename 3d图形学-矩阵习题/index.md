# 矩阵-习题


## ***题目大纲***

1. *求解下列矩阵方程中的矩阵 X*

   *$ 3(\begin{bmatrix}-2 & 0\\\ \\\ 1 & 3\end{bmatrix}- 2X) = 2\begin{bmatrix}-2 & 0\\\ \\\ 1 & 3\end{bmatrix} $*

2. *计算下列矩阵的乘积：*

   *（a）$ \begin{bmatrix}-2 & 0 & 3 \\\ \\\ 4 & 1 & -1 \end{bmatrix} \begin{bmatrix} 2 & -1 \\\ \\\ 0 & 6 \\\ \\\ 2 & -3 \end{bmatrix} $*

   *（b）$ \begin{bmatrix}1 & 2 \\\ \\\ 3 & 4 \end{bmatrix} \begin{bmatrix} -2 & 0 \\\ \\\ 1 & 1 \end{bmatrix} $*

   *（c）$ \begin{bmatrix} 2 & 0 & 2 \\\ \\\ 0 & -1 & -3 \\\ \\\ 0 & 0 & 1 \end{bmatrix} \begin{bmatrix} 1 \\\ \\\ 2 \\\ \\\ 1 \end{bmatrix} $*

3. *计算下列矩阵的转置矩阵*

   *（a）$ \begin{bmatrix} 1 & 2 & 3 \end{bmatrix} $*

   *（b）$ \begin{bmatrix} x & y \\\ \\\ z & w \end{bmatrix} $*

   *（c）$ \begin{bmatrix} 1 & 2 \\\ \\\ 3 & 4 \\\ \\\ 5 & 6 \\\ \\\ 7 & 8 \end{bmatrix} $*

4. *将下列线性组合写作向量与矩阵乘积的形式：*

   *（a）v = 2(1, 2, 3) − 4(−5, 0, −1) + 3(2, −2, 3)*

   *（b）v = 3(2, −4) + 2(1, 4) − 1(−2, −3) + 5(1, 1)*
   
5. *证明：$ AB = \begin{bmatrix} A_{11} & A_{12} & A_{13} \\\ A_{21} & A_{22} & A_{23} \\\ A_{31} & A_{32} & A_{33}\end{bmatrix}\begin{bmatrix} B_{11} & B_{12} & B_{13} \\\ B_{21} & B_{22} & B_{23} \\\ B_{31} & B_{32} & B_{33} \end{bmatrix} =\begin{bmatrix} \leftarrow A_{1,\*}B \rightarrow \\\ \\\ \leftarrow A_{2,\*}B \rightarrow\\\ \\\ \leftarrow A_{3,\*}B \rightarrow \end{bmatrix} $*

6. *证明：$ A\vec{u} = \begin{bmatrix} A_{11} & A_{12} & A_{13} \\\ A_{21} & A_{22} & A_{23} \\\ A_{31} & A_{32} & A_{33} \end{bmatrix} \begin{bmatrix} x \\\ y \\\ z \end{bmatrix} = xA\_{\*,1} + y A\_{\*,2} + zA\_{\*,3} $*

7. *证明向量的叉积可以用矩阵的乘积来表示：$ \vec{u} \times \vec{v} = \begin{bmatrix} v_x & v_y & v_z \end{bmatrix} \begin{bmatrix} 0 & u_z & -u_y \\\ \\\ -u_z & 0 & u_x \\\ \\\ u_y & -u_x & 0 \end{bmatrix} $*

8. *设矩阵A = $ \begin{bmatrix} 2 & 0 & 1 \\\ 0 & -1 & -3 \\\ 0 & 0 & 1 \end{bmatrix} $, 那么请问矩阵B = $ \begin{bmatrix} \frac{1}{2} & 0 & - \frac{1}{2} \\\ 0 & -1 & -3 \\\ 0 & 0 & 1 \end{bmatrix} $ 是 A 的逆矩阵吗*

9. *设矩阵A = $ \begin{bmatrix} 1 & 2 \\\ 3 & 4 \end{bmatrix} $, 那么请问矩阵B = $ \begin{bmatrix} -2 & 1 \\\ \frac{3}{2} & \frac{1}{2} \end{bmatrix} $ 是A的逆矩阵吗*

10. *求下列矩阵的行列式：$ \begin{bmatrix} 21 & -4 \\\ 10 & 7 \end{bmatrix} 与 \begin{bmatrix} 2 & 0 & 0 \\\ 0 & 3 & 0 \\\ 0 & 0 & 7 \end{bmatrix} $*

11. *求下列矩阵的逆矩阵：$ \begin{bmatrix} 21 & -4 \\\ 10 & 7 \end{bmatrix} 与 \begin{bmatrix} 2 & 0 & 0 \\\ 0 & 3 & 0 \\\ 0 & 0 & 7 \end{bmatrix} $*

12. *下列矩阵是可逆矩阵吗？\begin{bmatrix} 1 & 2 & 3 \\\ \\\ 0 & 4 & 5 \\\ \\\ 0 & 0 & 0 \end{bmatrix}*

13. *假设矩阵A是可逆矩阵，证明 $ (A^{-1})^T = (A^T)^{-1} $*

14. *所有的线性代数书籍都会证明 $ det(AB) = detA \cdot detB $这一性质。设A和B皆为 n x n 矩阵，并假设A是可逆的，试根据  $ det I = 1 $与上述性质来证明 $ detA^{-1} = \frac{1}{detA}$*

15. *证明2D矩阵 $ \begin{bmatrix} u_x & u_y \\\ v_x & v_y \end{bmatrix} $的行列式得到的是：由 $ \vec{u} = (u_x, u_y) $与向量 $ \vec{v} = (v_x, v_y) $ 张成的平行四边形的有向面积。如果向量 u 以逆时针方向旋转角θ ∈(0, π) 能与向量 v 重合，则结果为正，否则为负。*

    ![矩阵第十五题](https://raw.githubusercontent.com/vlicecream/cloudImage/main/矩阵第十五题.png)

16. *求由下列向量张成的平行四边形面积：*

    1. *u = (3, 0)与 v = (1, 1)*

    2. *u = (−1, −1)与 v = (0, 1)*

17. *设 A = $ \begin{bmatrix} A_{11} & A_{12} \\\ A_{21} & A_{22} \end{bmatrix} $. B = $ \begin{bmatrix} B_{11} & B_{12} \\\ B_{21} & B_{22} \end{bmatrix} $, C = $ \begin{bmatrix} C_{11} & C_{12} \\\ C_{21} & C_{22} \end{bmatrix} $。证明 A(BC) = (AB)C。这个结论说明了 2 × 2矩阵之间的乘法运算满足结合律。（事实上，只要矩阵的乘法有意义，任意规模的矩阵乘法都满足结合律。）*

## ***第一题***

*求解下列矩阵方程中的矩阵 X $ 3(\begin{bmatrix}-2 & 0\\\ \\\ 1 & 3\end{bmatrix}- 2X) = 2\begin{bmatrix}-2 & 0\\\ \\\ 1 & 3\end{bmatrix} $*
$$
\begin{gather*}
3(\begin{bmatrix}-2 & 0\\\ \\\ 1 & 3\end{bmatrix}- 2X) = 2\begin{bmatrix}-2 & 0\\\ \\\ 1 & 3\end{bmatrix}
\\\ \\\
\begin{bmatrix} -6 & 0 \\\ \\\ 3 & 9 \end{bmatrix} - 6X = \begin{bmatrix} -4 & 0 \\\ \\\ 2 & 6 \end{bmatrix}
\\\ \\\
-6X = \begin{bmatrix} -4 & 0 \\\ \\\ 2 & 6 \end{bmatrix} - \begin{bmatrix} -6 & 0 \\\ \\\ 3 & 9 \end{bmatrix}
\\\ \\\
-6X = \begin{bmatrix} 2 & 0 \\\ \\\ -1 & -3 \end{bmatrix}
\\\ \\\
X = \begin{bmatrix} -\frac{1}{3} & 0 \\\ \\\ \frac{1}{6} & \frac{1}{2} \end{bmatrix}
\end{gather*} 
$$

## ***第二题***

*计算下列矩阵的乘积：*

*（a）$ \begin{bmatrix}-2 & 0 & 3 \\\ \\\ 4 & 1 & -1 \end{bmatrix} \begin{bmatrix} 2 & -1 \\\ \\\ 0 & 6 \\\ \\\ 2 & -3 \end{bmatrix} $*
$$
\begin{bmatrix}-2 & 0 & 3 \\\ \\\ 4 & 1 & -1 \end{bmatrix} \begin{bmatrix} 2 & -1 \\\ \\\ 0 & 6 \\\ \\\ 2 & -3 \end{bmatrix} = \begin{bmatrix} 2 & -7 \\\ \\\ 6 & 5 \end{bmatrix}
$$
*（b）$ \begin{bmatrix}1 & 2 \\\ \\\ 3 & 4 \end{bmatrix} \begin{bmatrix} -2 & 0 \\\ \\\ 1 & 1 \end{bmatrix} $*
$$
\begin{bmatrix}1 & 2 \\\ \\\ 3 & 4 \end{bmatrix} \begin{bmatrix} -2 & 0 \\\ \\\ 1 & 1 \end{bmatrix}
= \begin{bmatrix} 0 & 2 \\\ \\\ -2 & 4 \end{bmatrix}
$$
*（c）$ \begin{bmatrix} 2 & 0 & 2 \\\ \\\ 0 & -1 & -3 \\\ \\\ 0 & 0 & 1 \end{bmatrix} \begin{bmatrix} 1 \\\ \\\ 2 \\\ \\\ 1 \end{bmatrix} $*
$$
\begin{bmatrix} 2 & 0 & 2 \\\ \\\ 0 & -1 & -3 \\\ \\\ 0 & 0 & 1 \end{bmatrix} \begin{bmatrix} 1 \\\ \\\ 2 \\\ \\\ 1 \end{bmatrix} = \begin{bmatrix} 4 \\\ \\\ -5 \\\ \\\ 1 \end{bmatrix}
$$

## ***第三题***

*计算下列矩阵的转置矩阵*

*（a）$ \begin{bmatrix} 1 & 2 & 3 \end{bmatrix} $*
$$
\begin{bmatrix} 1 & 2 & 3 \end{bmatrix}^T = \begin{bmatrix} 1 \\\ 2 \\\ 3 \end{bmatrix}
$$
*（b）$ \begin{bmatrix} x & y \\\ \\\ z & w \end{bmatrix} $*
$$
\begin{bmatrix} x & y \\\ \\\ z & w \end{bmatrix} ^ T = \begin{bmatrix} x & z \\\ \\\ y & w \end{bmatrix}
$$
*（c）$ \begin{bmatrix} 1 & 2 \\\ \\\ 3 & 4 \\\ \\\ 5 & 6 \\\ \\\ 7 & 8 \end{bmatrix} $*
$$
\begin{bmatrix} 1 & 2 \\\ \\\ 3 & 4 \\\ \\\ 5 & 6 \\\ \\\ 7 & 8 \end{bmatrix} ^ T = 
\begin{bmatrix} 1 & 3 & 5 & 7 \\\ \\\ 2 & 4 & 6 & 8 \end{bmatrix}
$$

## ***第四题***

*将下列线性组合写作向量与矩阵乘积的形式：*

*（a）v = 2(1, 2, 3) − 4(−5, 0, −1) + 3(2, −2, 3)*
$$
\begin{bmatrix} 1 & -5 & 2 \\\ \\\ 2 & 0 & -2 \\\ \\\ 3 & -1 & 3 \end{bmatrix}
\begin{bmatrix} 2 \\\ \\\ -4 \\\ \\\ 3 \end{bmatrix}
$$


*（b）v = 3(2, −4) + 2(1, 4) − 1(−2, −3) + 5(1, 1)*
$$
\begin{bmatrix} 2 & 1 & -2 & 1 \\\ \\\ -4 & 4 & -3 & 1 \end{bmatrix}
\begin{bmatrix} 3 \\\ \\\ 2 \\\ \\\ -1 \\\ \\\ 5 \end{bmatrix}
$$

## ***第五题***

*证明：$ AB = \begin{bmatrix} A_{11} & A_{12} & A_{13} \\\ A_{21} & A_{22} & A_{23} \\\ A_{31} & A_{32} & A_{33}\end{bmatrix}\begin{bmatrix} B_{11} & B_{12} & B_{13} \\\ B_{21} & B_{22} & B_{23} \\\ B_{31} & B_{32} & B_{33} \end{bmatrix} =\begin{bmatrix} \leftarrow A_{1,\*}B \rightarrow \\\ \\\ \leftarrow A_{2,\*}B \rightarrow\\\ \\\ \leftarrow A_{3,\*}B \rightarrow \end{bmatrix} $*
$$
AB = 
\begin{bmatrix} A_{11} & A_{12} & A_{13} \\\ A_{21} & A_{22} & A_{23} \\\ A_{31} & A_{32} & A_{33} \end{bmatrix}
\begin{bmatrix} B_{11} & B_{12} & B_{13} \\\ B_{21} & B_{22} & B_{23} \\\ B_{31} & B_{32} & B_{33} \end{bmatrix} =
\begin{bmatrix} 
\leftarrow A_{1,\*}B \rightarrow 
\\\ \\\ 
\leftarrow A_{2,\*}B \rightarrow
\\\ \\\
\leftarrow A_{3,\*}B \rightarrow
\end{bmatrix}
$$

## ***第六题***

*证明：$ A\vec{u} = \begin{bmatrix} A_{11} & A_{12} & A_{13} \\\ A_{21} & A_{22} & A_{23} \\\ A_{31} & A_{32} & A_{33} \end{bmatrix} \begin{bmatrix} x \\\ y \\\ z \end{bmatrix} = xA\_{\*,1} + y A\_{\*,2} + zA\_{\*,3} $*
$$
A\vec{u} = 
\begin{bmatrix} A_{11} & A_{12} & A_{13} \\\ A_{21} & A_{22} & A_{23} \\\ A_{31} & A_{32} & A_{33} \end{bmatrix}
\begin{bmatrix} x \\\ y \\\ z \end{bmatrix}
= xA_{\*,1} + y A_{\*,2} + zA_{\*,3}
$$

## ***第七题***

*证明向量的叉积可以用矩阵的乘积来表示：$ \vec{u} \times \vec{v} = \begin{bmatrix} v_x & v_y & v_z \end{bmatrix} \begin{bmatrix} 0 & u_z & -u_y \\\ \\\ -u_z & 0 & u_x \\\ \\\ u_y & -u_x & 0 \end{bmatrix} $*
$$
\vec{u} \times \vec{v} =
\begin{bmatrix}
v_x & v_y & v_z
\end{bmatrix}
\begin{bmatrix}
0 & u_z & -u_y
\\\ \\\
-u_z & 0 & u_x
\\\ \\\
u_y & -u_x & 0
\end{bmatrix}
$$

## ***第八题***

*设矩阵A = $ \begin{bmatrix} 2 & 0 & 1 \\\ 0 & -1 & -3 \\\ 0 & 0 & 1 \end{bmatrix} $, 那么请问矩阵B = $ \begin{bmatrix} \frac{1}{2} & 0 & - \frac{1}{2} \\\ 0 & -1 & -3 \\\ 0 & 0 & 1 \end{bmatrix} $ 是 A 的逆矩阵吗*
$$
\begin{bmatrix} 2 & 0 & 1 \\\ 0 & -1 & -3 \\\ 0 & 0 & 1 \end{bmatrix}
\begin{bmatrix} \frac{1}{2} & 0 & - \frac{1}{2} \\\ 0 & -1 & -3 \\\ 0 & 0 & 1 \end{bmatrix}
$$

## ***第九题***

*设矩阵A = $ \begin{bmatrix} 1 & 2 \\\ 3 & 4 \end{bmatrix} $, 那么请问矩阵B = $ \begin{bmatrix} -2 & 1 \\\ \frac{3}{2} & \frac{1}{2} \end{bmatrix} $ 是A的逆矩阵吗*
$$
\begin{bmatrix} 1 & 2 \\\ 3 & 4 \end{bmatrix}
\begin{bmatrix} -2 & 1 \\\ \frac{3}{2} & \frac{1}{2} \end{bmatrix}
$$

## ***第十题***

*求下列矩阵的行列式：$ \begin{bmatrix} 21 & -4 \\\ 10 & 7 \end{bmatrix} 与 \begin{bmatrix} 2 & 0 & 0 \\\ 0 & 3 & 0 \\\ 0 & 0 & 7 \end{bmatrix} $*
$$
\begin{bmatrix} 21 & -4 \\\ 10 & 7 \end{bmatrix}
与
\begin{bmatrix} 2 & 0 & 0 \\\ 0 & 3 & 0 \\\ 0 & 0 & 7 \end{bmatrix}
$$

## ***第十一题***

*求下列矩阵的逆矩阵：$ \begin{bmatrix} 21 & -4 \\\ 10 & 7 \end{bmatrix} 与 \begin{bmatrix} 2 & 0 & 0 \\\ 0 & 3 & 0 \\\ 0 & 0 & 7 \end{bmatrix} $*
$$
\begin{bmatrix} 21 & -4 \\\ 10 & 7 \end{bmatrix}
与
\begin{bmatrix} 2 & 0 & 0 \\\ 0 & 3 & 0 \\\ 0 & 0 & 7 \end{bmatrix}
$$

## ***第十二题***

*下列矩阵是可逆矩阵吗？$ \begin{bmatrix} 1 & 2 & 3 \\\ \\\ 0 & 4 & 5 \\\ \\\ 0 & 0 & 0 \end{bmatrix} $*
$$
\begin{bmatrix}
1 & 2 & 3 \\\ \\\ 0 & 4 & 5 \\\ \\\ 0 & 0 & 0
\end{bmatrix}
$$

## ***第十三题***

*假设矩阵A是可逆矩阵，证明 $ (A^{-1})^T = (A^T)^{-1} $*

## ***第十四题***

*所有的线性代数书籍都会证明 $ det(AB) = detA \cdot detB $这一性质。设A和B皆为 n x n 矩阵，并假设A是可逆的，试根据  $ det I = 1 $与上述性质来证明 $ detA^{-1} = \frac{1}{detA}$*

## ***第十五题***

*证明2D矩阵 $ \begin{bmatrix} u_x & u_y \\\ v_x & v_y \end{bmatrix} $的行列式得到的是：由 $ \vec{u} = (u_x, u_y) $与向量 $ \vec{v} = (v_x, v_y) $ 张成的平行四边形的有向面积。如果向量 u 以逆时针方向旋转角θ ∈(0, π) 能与向量 v 重合，则结果为正，否则为负。*

![矩阵第十五题](https://raw.githubusercontent.com/vlicecream/cloudImage/main/矩阵第十五题.png)

## ***第十六题***

*求由下列向量张成的平行四边形面积：*

1. *u = (3, 0)与 v = (1, 1)*

2. *u = (−1, −1)与 v = (0, 1)*

## ***第十七题***

*设 A = $ \begin{bmatrix} A_{11} & A_{12} \\\ A_{21} & A_{22} \end{bmatrix} $. B = $ \begin{bmatrix} B_{11} & B_{12} \\\ B_{21} & B_{22} \end{bmatrix} $, C = $ \begin{bmatrix} C_{11} & C_{12} \\\ C_{21} & C_{22} \end{bmatrix} $。证明 A(BC) = (AB)C。这个结论说明了 2 × 2矩阵之间的乘法运算满足结合律。（事实上，只要矩阵的乘法有意义，任意规模的矩阵乘法都满足结合律。）*

