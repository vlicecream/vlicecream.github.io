# 微积分 - 极限和连续 - 部分习题及答案


## ***平均变化率 - 习题***

***在题 1 - 4中，求给定区间上函数的平均变化率***

1. *$ f(x) = x^3 + 1 $*

   * *[2, 3]*
     $$
     \frac{\Delta y}{\Delta x} = \frac{f(x_2) - f(x_1)}{x_2 - x_1} = \frac{(3^3 + 1) - (2^3 + 1)}{3 - 2} = 3
     $$

   * *[-1, 1]*
     $$
     \frac{\Delta y}{\Delta x} = \frac{f(x_2) - f(x_1)}{x_2 - x_1} = \frac{(1^3 + 1) - ((-1)^3 + 1)}{1 - (-1)} = 1
     $$
   
2. *$ R(\theta) = \sqrt{4 \theta + 1} $*

   * *[0, 2]*
     $$
     \frac{\Delta y}{\Delta x} = \frac{f(x_2) - f(x_1)}{x_2 - x_1} = \frac{(\sqrt{4 \cdot 2 + 1}) - (\sqrt{4 \cdot 0 + 1})}{2 - 0} = 1
     $$

3. $ h(t) = \cot t $

   * *$ [ \frac{\pi}{4}, \frac{3 \pi}{4} ] $*
     $$
     \frac{\Delta y}{\Delta x} 
     = \frac{f(x_2) - f(x_1)}{x_2 - x_1} 
     = \frac{(\cot \frac{3 \pi}{4}) - (\cot \frac{\pi}{4})}{\frac{3 \pi}{4} - \frac{\pi}{4}}
     = \frac{-2}{\frac{\pi}{2}}
     = -\frac{4}{\pi}
     $$

   * *$ [\frac{\pi}{6}, \frac{\pi}{2}] $*
     $$
     \frac{\Delta y}{\Delta x} 
     = \frac{f(x_2) - f(x_1)}{x_2 - x_1} 
     = \frac{(\cot \frac{\pi}{2}) - (\cot \frac{\pi}{6})}{\frac{\pi}{2} - \frac{\pi}{6}}
     = \frac{0 - \sqrt{3}}{\frac{2\pi}{6}}
     = \frac{-\sqrt{3}}{\frac{\pi}{3}}
     = -\sqrt{3} \cdot \frac{3}{\pi}
     = -\frac{3\sqrt{3}}{\pi}
     $$

4. *$ g(t) = 2 + \cos t $*

   * *$ [0, \pi] $*
     $$
     \frac{\Delta y}{\Delta x} 
     = \frac{f(x_2) - f(x_1)}{x_2 - x_1}
     = \frac{(2 + \cos \pi) - (2 + \cos 0)}{\pi - 0}
     = \frac{-1 + 3}{\pi}
     = -\frac{2}{\pi}
     $$
   
   * *$ [-\pi, \pi] $*
     $$
     \frac{\Delta y}{\Delta x} 
     = \frac{f(x_2) - f(x_1)}{x_2 - x_1}
     = \frac{(2 + \cos \pi) - (2 - \cos \pi)}{\pi - (-\pi)}
     = \frac{1 - 1}{2\pi}
     = \frac{0}{2\pi}
     = 0
     $$

***福特马特眼镜蛇的汽车的速度***

5. *下图展示了1994 福特汽车从静止开始加速后的时间对距离的图形*

   ![福特马特眼镜蛇的汽车的速度](https://raw.githubusercontent.com/vlicecream/cloudImage/main/image-20250820082102461.png)
   
   * *估算割线 $ PQ_1, PQ_2, PQ_3 $ 和 $ PQ_4 $ 的斜率*
     $$
     \text{割线的斜率其实就是平均变化率，我在这只计算一个} PQ_1
     \\\ \\\
     \frac{\Delta y}{\Delta x} 
     = \frac{f(x_2) - f(x_1)}{x_2 - x_1}
     = \frac{650 - 220}{20 - 10}
     = \frac{430}{10}
     = 43
     $$
     
   * *估算该型号汽车在 $ t = 20 $秒的速度*
   
     *我们可以通过观察割线斜率的趋势来估算这个值。当点 Q 越来越接近点 P 时，割线 PQ 的斜率会越来越接近点 P 处切线的斜率。*
   
     *从图像上可以观察到，大约从 t=12 秒之后，图像变成了一条**直线**。这意味着汽车进入了**匀速行驶**阶段。对于一条直线，其上任意点的切线斜率都等于直线本身的斜率。*
   
     *因此，我们可以确认，在 $ t = 20 $秒时，汽车的速度就是这段匀速阶段的速度。*
   
     ***结论：该型号汽车在 $ t = 20 $秒时的速度估算为 45 米/秒。***

6. *下落板钳的速度*

   ![下落板钳的速度](https://raw.githubusercontent.com/vlicecream/cloudImage/main/image-20250826073307219.png)

   * *估算割线 $ PQ_1, PQ_2, PQ_3 $ 和 $ PQ_4 $ 的斜率*
     $$
     \text{割线的斜率其实就是平均变化率，我在这只计算一个} PQ_1
     \\\ \\\
     \frac{\Delta y}{\Delta x} 
     = \frac{f(x_2) - f(x_1)}{x_2 - x_1}
     = \frac{80 - 20}{10 - 5}
     = \frac{60}{5}
     = 12
     $$

   * *当板钳在站顶时，他的下落速度有多快？*

     *“在站顶时”指的是下落刚刚开始的时刻，即 t = 0 秒。*

     *因此，当板钳在站顶时，他的下落速度是 0 米/秒。*

7. *球的速度*
   *附表的数据给出了从斜面上滚下的球的距离，试着通过找速度的上，下界并求其平均来估算 $ t = 1 $ 时的瞬时速度。即：求 $ a \leq v(1) \leq b $ 然后估算 $ v(1) $ 为 $ \frac{(a+b)}{2} $*

   ![球的速度](https://raw.githubusercontent.com/vlicecream/cloudImage/main/image-20250826074614233.png)
   $$
   \begin{gather*}
   \frac{\Delta y}{\Delta x} 
   = \frac{f(x_2) - f(x_1)}{x_2 - x_1}
   = \frac{13.10 - 8.39}{1.0 - 0.8}
   = \frac{4.71}{0.2}
   = 23.55
   \\\ \\\
   \frac{\Delta y}{\Delta x} 
   = \frac{f(x_2) - f(x_1)}{x_2 - x_1}
   = \frac{18.87 - 13.10}{1.2 - 1.0}
   = \frac{5.77}{0.2}
   = 28.85
   \\\ \\\
   v(1) = \frac{(a+b)}{2} = \frac{52.4}{2} = 26.2
   \end{gather*}
   $$

8. *火车行进的距离*

   *一辆火车从静止加速到的最大缓慢巡行速度，然后以某个常速度行经一个城镇，经过该城镇后又加速到他缓慢巡行速度。最后，火车平稳的减速直到到达目的地时停下来，试着画一个火车的行进距离作为时间的函数的可能的图形*

   - *A：从静止加速到“缓慢巡行速度”（曲线向上、斜率逐渐变大）*
   - *B：穿越城镇的匀速慢行（直线、斜率较小）*
   - *C：驶出城镇后再次加速到更快的巡航（曲线向上、斜率继续变大）*
   - *D：更高巡航速度的匀速段（直线、斜率更大）*
   - *E：平稳减速至终点停下（曲线向上但逐渐变平，最后斜率为 0）*

   *图中“斜率 = 速度”。加速段斜率随时间变大（凹向上），减速段斜率变小（凹向下），匀速段是直线。整条曲线单调递增、连续，在各阶段交界处可以有斜率的突变（理想化时也可光滑过渡）。*

   ![火车行进的距离](https://raw.githubusercontent.com/vlicecream/cloudImage/main/image-20250826080206062.png)

## ***对图形求极限 - 习题***

![第九题图/第十题图](https://raw.githubusercontent.com/vlicecream/cloudImage/main/image-20250826080435309.png)

9. *对左下图的函数 $g(x)$，求下列极限，或者解释说为什么没有极限*

   * *$ \displaystyle \lim_{x \to 1} g(x) $*
     - ***分析：***
       - *当 x 从左侧无限接近 1 时 $ (x→1^⁻) $，函数 $ g(x) $ 的值从图像上看趋近于 1。所以左极限是 1。*
       - *当 x 从右侧无限接近 1 时 $ (x→1^⁺) $，函数 $ g(x) $ 的值也趋近于 1。所以右极限是 1。*
     - ***结论：*** *因为左极限和右极限相等，所以该点的极限存在。*
     - ***答案：*** *$ \displaystyle \lim_{x \to 1} g(x) = 1 $*
   * *$ \displaystyle \lim_{x \to 2} g(x) $*
     - ***分析：***
       - *当 x 从左侧无限接近 2 时 $ (x→2^⁻) $，函数 $ g(x) $ 的值趋近于 1。所以左极限是 1*
       - *当 x 从右侧无限接近 2 时 $ (x→2^⁺) $，函数 $ g(x) $ 的值也趋近于 1。所以右极限是 1。*
     - ***结论：*** *因为左极限和右极限相等，所以该点的极限存在。*
     - ***答案：*** *$ \displaystyle \lim_{x \to 2} g(x) = 1 $*
   * *$ \displaystyle \lim_{x \to 3} g(x) $*
     - ***分析：***
       - *当 x 从左侧无限接近 3 时 $ (x→3^⁻) $，函数 $ g(x) $ 的值趋近于 0。所以左极限是 0。*
       - *当 x 从右侧无限接近 3 时 $ (x→3^⁺) $，函数 $ g(x) $ 的值也趋近于 0。所以右极限是 0。*
     - ***结论：*** *因为左极限和右极限相等，所以该点的极限存在。*
     - ***答案：*** *$ \displaystyle \lim_{x \to 3} g(x) = 0 $*
   
   10. *对于图片的函数 $ f(x) $，求下列极限，或结束为什么没有极限*
       * *$ \displaystyle \lim_{t \to -2} f(t) $*
         - ***分析：***
           - *当 t 从左侧无限接近 -2 时 $ (t→-2^⁻) $，函数 $ f(t) $ 的值趋近于 0。所以左极限是 0。*
           - *当 t 从右侧无限接近 -2 时 $ (t→-2^⁺) $，函数 $ f(t) $ 的值也趋近于 0。所以右极限是 0。*
         - ***结论：*** *因为左极限和右极限相等，所以该点的极限存在。*
         - ***答案：*** *$ \displaystyle \lim_{t \to -2} f(t) = 0 $*
       * *$  \displaystyle \lim_{t \to -1} f(t) $*
         - ***分析：***
           - *当 t 从左侧无限接近 -1 时 $ (t→-1^⁻) $，函数 $ f(t) $ 的值趋近于 -1。所以左极限是 -1。*
           - *当 t 从右侧无限接近 -1 时 $ (t→-1^⁺) $，函数 $ f(t) $ 的值也趋近于 -1。所以右极限是 -1。*
         - ***结论：*** *因为左极限和右极限相等，所以该点的极限存在。*
         - ***答案：*** *$ \displaystyle \lim_{t \to -1} f(t) = -1 $*
       * *$ \displaystyle \lim_{t \to 0} f(t) $*
         - ***分析：***
           - *当 t 从左侧无限接近 0 时 $ (t→0^⁻) $，函数 $ f(t) $ 的值趋近于 -1。所以左极限是 -1。*
           - *当 t 从右侧无限接近 0 时 $ (t→0^⁺) $，函数 $ f(t) $ 的值趋近于 1。所以右极限是 1。*
         - ***结论：*** *因为左极限 (-1) 不等于右极限 (1)，所以该点的极限不存在。*
         - ***答案：*** *极限不存在*

## ***从图形估计极限 - 习题***

***在题 1 - 6 中，利用图形来估算函数的极限，或解释为什么极限不存在***

![题1 - 题2](https://raw.githubusercontent.com/vlicecream/cloudImage/main/image-20250828080146776.png)

1. * *$ \displaystyle \lim_{x \to 3^-} f(x) $*
     - ***分析：***
       - *当 x 从左侧无限接近 3 时 $ (x \to 3^⁻) $，函数 $ f(x) $ 的值趋近于 3。所以左极限是 3。*
     - ***答案：*** *$ \displaystyle \lim_{x \to 3^-} f(x) = 3 $*
   * *$ \displaystyle \lim_{x \to 3^+} f(x) $*
     - ***分析：***
       - *当 x 从右侧无限接近 3 时 $ (x \to 3^+) $，函数 $ f(x) $ 的值趋近于 -2。所以右极限是 -2。*
     - ***答案：*** *$ \displaystyle \lim_{x \to 3^+} f(x) = -2 $*
   * *$ \displaystyle \lim_{x \to 3} f(x) $*
     - ***分析：***
       - *当 x 从左侧无限接近 3 时 $ (x \to 3^⁻) $，函数 $ f(x) $ 的值趋近于 3。所以左极限是 3。*
       - *当 x 从右侧无限接近 3 时 $ (x \to 3^+) $，函数 $ f(x) $ 的值趋近于 -2。所以右极限是 -2。*
     - ***答案：*** *因为左极限跟右极限不一样，所以极限不存在*
   * *$ f(3) $*
     - ***分析：***
       - *我们观察图像，在 x=3 的位置，有一个实心点，这个点对应的 y 值是 1（注意：极限值与函数在该点是否有定义或取值是多少没有必然联系）*
     - ***答案：*** *$ f(3) = 1 $*
2. * *$ \displaystyle \lim_{ t \to (-4)^-} g(t) $*
     - ***分析：***
       - *当 t 从左侧无限接近 -4 时 $ (t \to -4^⁻) $，函数 $ g(t) $ 的值趋近于 5。所以左极限是 5。*
     - ***答案：*** *$ \displaystyle \lim_{ t \to (-4)^-} g(t) = 5 $*
   * *$ \displaystyle \lim_{t \to (-4)^+} g(t) $*
     - ***分析：***
       - *当 t 从右侧无限接近 -4 时 $ (t \to -4^+) $，函数 $ g(t) $ 的值趋近于 5。所以右极限是 5。*
     - ***答案：*** *$ \displaystyle \lim_{ t \to (-4)^+} g(t) = 5 $*
   * *$ \displaystyle \lim_{t \to -4} g(t) $*
     - ***分析：***
       - *当 t 从左侧无限接近 -4 时 $ (t \to -4^⁻) $，函数 $ g(t) $ 的值趋近于 5。所以左极限是 5。*
       - *当 t 从右侧无限接近 -4 时 $ (t \to -4^+) $，函数 $ g(t) $ 的值趋近于 5。所以右极限是 5。*
     - ***答案：*** *$ \displaystyle \lim_{t \to -4} g(t) = 5 $*
   * *$ g(-4) $*
     - ***分析：***
       - *我们观察图像，在 t=-4 的位置，有一个实心点，这个点对应的 y 值是 2（注意：极限值与函数在该点是否有定义或取值是多少没有必然联系）*
     - ***答案：*** *$ g(-4) = 2 $*

## ***运用极限法则 - 习题***

7. *假设 $ \displaystyle \lim_{x \to 0} f(x) = 1$ 和  $ \displaystyle \lim_{x \to 0} g(x) = 5$，写出下面计算中步骤（a）（b）（c）中用得到的定理1中法则的名称*
   $$
   \displaystyle \lim_{x \to 0} f(x) = 1 和  \displaystyle \lim_{x \to 0} g(x) = 5
   $$
   *（a）商法则*

   *（b）幂法则*

   *（c）积法则*

8. *设 $ \displaystyle \lim_{x \to 1}h(x) = 5, \lim_{x \to 1}p(x) = 1,\lim_{x \to 1}r(x) = -2 $ 写出下面计算中步骤（a）（b）（c）中用到的定理1中法则的名称*
   $$
   \displaystyle \lim_{x \to 1}h(x) = 5, \lim_{x \to 1}p(x) = 1,\lim_{x \to 1}r(x) = -2 
   $$
   *（a）商法则*

   *（b）幂法则*

   *（c）积法则*

9. *假设 $ \displaystyle \lim_{x \to c} f(x) = 5 $ 以及 $ \displaystyle \lim_{x \to c} g(x) = -2 $求：*

   * *$ \displaystyle \lim_{x \to c} f(x) g(x) $*
     $$
     \begin{gather*}
     \displaystyle \lim_{x \to c} f(x) g(x) = 5 (-2) = -10
     \end{gather*}
     $$

   * *$ \displaystyle \lim_{x \to c} 2 f(x) g(x) $*
     $$
     \begin{gather*}
     \displaystyle \lim_{x \to c} 2 f(x) g(x) = (2 \cdot 5) (-2) = 10 \cdot (-2) = -20
     \end{gather*}
     $$

   * *$ \displaystyle \lim_{x \to c} (f(x) + 3g(x)) $*
     $$
     \begin{gather*}
     \displaystyle \lim_{x \to c} (f(x) + 3g(x)) = (5 + 3(-2)) = 5 - 6 = -1
     \end{gather*}
     $$

   * *$ \displaystyle \lim_{x \to c} \frac{f(x)}{f(x) - g(x)} $*
     $$
     \displaystyle \lim_{x \to c} \frac{f(x)}{f(x) - g(x)} = \frac{5}{5 - (-2)} = \frac{5}{7}
     $$

10. *假设 $ \displaystyle \lim_{x \to c} f(x) = 0 $ 以及 $ \displaystyle \lim_{x \to c} g(x) = -3 $求：*

    * *$ \displaystyle \lim_{x \to 4} (g(x) + 3) $*
    * *$ \displaystyle \lim_{x \to 4} f(x) $*
    * *$ \displaystyle \lim_{x \to 4} (g(x))^2 $*
    * *$ \displaystyle \lim_{x \to 4} \frac{g(x)}{f(x) - 1} $*

## ***极限计算 - 习题***

***求 题 11 - 14 中的极限***

11. * *$ \displaystyle \lim_{x \to -7} (2x + 5) $*
    * *$ \displaystyle \lim_{x \to 6} 8(t - 5)(t - 7) $*
    * *$ \displaystyle \lim_{x \to 2} \frac{y + 2}{y ^2 + 5y + 6} $*
    * *$ \displaystyle \lim_{h \to 0} \frac{3}{\sqrt{3h + 1} + 1} $*
    
12. * *$ \displaystyle  \lim_{r \to -2}(r^3 - 2r^2 + 4r + 8) $*
    
    * *$ \displaystyle \lim_{x \to 2} \frac{x + 3}{x + 6} $*
    
    * *$ \displaystyle \lim_{t \to -3} (5 - y)^{\frac{4}{3}} $*
    
    * *$ \displaystyle \lim_{\theta - 5} \frac{\theta - 5}{\theta^2 - 25} $*
    
      

