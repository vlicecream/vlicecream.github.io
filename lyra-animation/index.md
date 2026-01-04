# Lyra - Anim Layers


## ***参考资料***

*https://dev.epicgames.com/documentation/zh-cn/unreal-engine/animation-in-lyra-sample-game-in-unreal-engine?application_version=5.3*

*https://dev.epicgames.com/documentation/zh-cn/unreal-engine/using-animation-blueprint-linking-in-unreal-engine?application_version=5.3*

## ***简介***

*Lyra 的 动画蓝图，首先是采用 蓝图线程安全更新动画，此开启方法在文档中就写了。再次提一句，这实现了使用属性访问（Property Access）来访问其所有数据，并避免了重新计算链接的图层可能使用的值，如 **加速度（Acceleration）** 或 **速度（Velocity）** 。*

*Lyra 还采用了 LinkLayersInterface，此动画层接口的作用是：比如你的角色可以拿枪和拿剑，但是他们的动作不一样，你就可以很好的利用这个来加载不同的动画*

## ***LyraLayersInterface***

*此动画层接口的使用方法可以看文档，我在这只解释比较绕的逻辑*

*你创建一个 LyraLayersInterface，创建好图层（接口方法），在创建一个Animation Blueprint，实现了具体的图层（接口方法），在lyra里这其实是创建了一个ABP基类，随后将各种动画创建变量，比如 Idle 变量，然后再创建具体的子类，去设置动画变量。这样再不同的场景就可以用各自的ABP子类*

*然后在我们的Character是不是必须要设置一个ABP，这个ABP不需要设置为 刚才说的那几个ABP，你可以继续创建一个新的，然后再这新的interface设置为 LyraLayersInterface，随后再具体的使用idle动画，咱们可以使用 这个动画层接口图层，此时记得再character上使用 **Set Linked Anim Class**，这个方法的参数，再传我们刚刚说的那个基类或者子类，这样就可以用你创建好的图层的ABP的方法了*

## ***坑***

1. *线程安全函数需要在detail里标记线程安全*
2. *如果想创建自定义线程安全函数函数，则一定要勾上纯（Pure）函数和线程安全选项*

