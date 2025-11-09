# 向量-习题


## ***题目大纲***

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

## ***第一题***

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



## ***第二题***

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



## ***第三题***

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

## ***第四题***

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

## ***第五题***

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

## ***第六题***

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

## ***第七题***

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

## ***第八题***

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

## ***第九题***

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

## ***第十题***

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

## ***第十一题***

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
\vec{g}\_\perp &= \vec{g} - \vec{g_{||}}
\\\ \\\
&=  (0, −9.8) − (3.92, −1.96) 
\\\ \\\
&= (−3.92, −7.84) 
\end{align*}
$$
![向量第十一题](https://raw.githubusercontent.com/vlicecream/cloudImage/main/向量第十一题.png)

## ***第十二题***

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

## ***第十三题***

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

## ***第十四题***

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
\+ (u_z^2 v_x^2 - 2u_zv_xu_xv_z + u_x^2 v_z^2) + (u_x^2 v_y^2 - 2u_xv_yu_yv_x + u_y^2 v_x^2)
\\\ \\\
\Vert \vec{u} \Vert^2 \Vert \vec{v} \Vert^2 - (\vec{u} \cdot\vec{v})^2
= (u_y^2 v_z^2 + u_z^2 v_y^2) 
\+ (u_z^2 v_x^2 + u_x^2 v_z^2) + (u_x^2 v_y^2 + u_y^2 v_x^2)
\- 2(u_yv_zu_zv_y + u_zv_xu_xv_z + u_xv_yu_yv_x)
\\\ \\\
\Vert \vec{u} \Vert^2 \Vert \vec{v} \Vert^2 - (\vec{u} \cdot\vec{v})
\end{gather*}
$$

## ***第十五题***

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

## ***第十六题***

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

## ***第十七题***

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


## ***第十八题***

*利用格拉姆—施密特正交化方法，令向量集 {(1, 0, 0), (1, 5, 0), (2, 1, −4)} 规范正交化。*

