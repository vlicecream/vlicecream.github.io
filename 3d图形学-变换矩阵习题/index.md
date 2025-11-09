# 变换矩阵-习题


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

