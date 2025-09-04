# 微积分 - 导数


## ***作为函数的导数***

### ***导数的定义***

*函数 $ f(x) $ 关于变量 x 的导数是 $f'$，他在 x 处的值为：*
$$
f'(x) = \lim_{h \to 0} \frac{f(x + h) - f(x)}{h} \quad 如果该极限存在
$$

#### ***例题（运用定义）***

*求 $ y = \sqrt{x}，x > 0 $ 的导数*
$$
\begin{gather*}
f(x) = \sqrt{x} \quad 而 f(x + h) = \sqrt{x + h}
\\\ \\\
\frac{f(x + h) - f(x)}{h} = \frac{\sqrt{x + h} - \sqrt{x}}{h} = \frac{(x + h) - x}{h(\sqrt{x + h} - \sqrt{x})} = \frac{1}{h(\sqrt{x + h} - \sqrt{x})}
\\\ \\\
f'(x) = \lim_{h \to 0} \frac{1}{h(\sqrt{x + h} - \sqrt{x})} 
\end{gather*}
$$

