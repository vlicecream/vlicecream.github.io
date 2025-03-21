# 函数运行时在内存中是什么样子


# 函数运行时在内存中是什么样子?

## ***引言***

*在进入到本章的内容前，我们来思考思考几个问题*

1. ```cpp
   void f(int a) 
   {
     if (a > 10000000) return;
     int arr[100] = { 0 };
     f(a+1);
   }
   ```
   
   *你能看出这段代码有啥问题吗*

2. *你知道协程的本质吗？有的同学可能会说是用户态线程，那么用户态线程是怎么实现的？*

3. *函数运行起来后在内存中是什么样子？*

***这几个问题看起来没什么关联，但是背后都指向一个东西，这就是所谓函数运行栈（run time stack）***

## ***从进程、线程到函数调用***

*举个小例子，汽车在高速上行驶时有很多信息，比如速度、位置，通过这些信息我们可以直观的感受汽车的运行时状态*

*同样的，程序在运行时也有很多信息，像有哪些程序正在运行、这些程序执行到了哪里等等，通过这些信息我们可以直观的感受系统中程序运行的状态*

*其中，我们创造了进程、线程这样的概念来记录有哪些程序正在运行*

***进程和线程的运行体现在函数执行上***，*函数的执行除了函数内部执行的顺序执行还有子函数调用的控制转移以及子函数执行完毕的返回。其中函数内部的顺序执行乏善可陈，重点是函数的调用*

*因此接下来我们的视角将从宏观的进程和线程拉近到微观下的函数调用，重点来讨论一下函数调用是怎样实现的*

## ***函数执行的活动轨迹：栈***

*也是一样，举个小例子，玩过游戏的同学应该知道，有时你为了完成一项主线任务不得不去打一些支线的任务，支线任务中可能还有支线任务，当一个支线任务完成后退回到前一个支线任务*

1. *主线任务A - 西天取经*
   - *支线任务B - 收服🐒*
     - *支线任务D - 拿到紧箍咒*
   - *支线任务C - 收服🐷*

*也就是说，我们必须支线任务B和支线任务C完成后，才能继续完成主线任务A*

*想要完成支线任务B 就必须完成支线任务D-拿到紧箍咒*

*整个任务的依赖关系如图所示：*

- ![游戏任务脉络图解](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data/202303051738468.png)

*然后，我们来模拟模拟任务完成过程*

1. *首先，我们来到任务A，执行主线任务*
   - ![A](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data/202303051806443.png)
2. *执行任务A的过程中我们发现任务A依赖任务B，这时我们暂停任务A去执行任务B*
   - ![A-B](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data/202303051804059.png)
3. *执行任务B的时候，我们又发现依赖任务D*
   - ![A-B-D](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data/202303051804272.png)
4. *执行任务D的时候我们发现该任务不再依赖任何其它任务，因此C完成后我们可以会退到前一个任务，也就是B*
   - ![A-B](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data/202303051804059.png)
5. *任务B除了依赖任务C外不再依赖其它任务，这样任务B完成后就可以回到任务A*
   - ![A](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data/202303051806443.png)
6. *现在我们回到了主线任务A，依赖的任务B执行完成，接下来是任务C*
   - ![A-C](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data/202303051807851.png)
7. *和任务D一样，C不依赖任何其它其它任务，任务C完成后就可以再次回到任务A，再之后任务A执行完毕，整个任务执行完成*

*让我们来看一下整个任务的活动轨迹*

- ![任务的活动轨迹](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data/202303051808577.jpg)

*仔细观察，实际上你会发现这是一个 **"First In Last Out"** 的顺序，天然适用于栈这种数据结构来处理。*

*再仔细看一下栈顶的轨迹，也就是A、B、D、B、A、C、A，**实际上你会发现这里的轨迹就是任务依赖树的遍历过程**，是不是很神奇，这也是为什么树这种数据结构的遍历除了**可以用递归也可以用栈来实现**的原因。*

## ***A Box***

*函数调用也是同样的道理，你把上面的ABCD换成函数ABCD，本质不变*

*因此，我们知道了，使用栈这种结构就可以用来保存函数调用信息*

*和游戏中的每个任务一样，当函数在运行时每个函数也要有自己的一个"小盒子"，**这个小盒子中保存了函数运行时的各种信息**，这写小盒子通过栈这种结构组织起来，这个小盒子就被称为"栈帧（stack frames）"，也有的称之为 call stack*

*不管用什么命名，总之，就是这里所说的小盒子，这个小盒子就是函数运行起来后占用的内存，这些小盒子构成了我们通常所说的栈区*

*那么问题也就来了，函数调用时都有哪些信息呢*

## ***控制转移***

*我们知道当函数A调用函数B的时候，控制从A转移到了B，所谓控制其实就是指CPU执行属于哪个函数的指令，CPU从开始执行属于函数A的指令切换到执行属于函数B的指令，我们就说控制从函数A转移到了函数B*

*控制从函数A转移到了函数B，我们需要有这两个信息*

- *我从哪里来（返回）*
- *要到哪里去（跳转）*

*好比你出去旅游，你需要知道去哪里，还需要记住回家的路（写到这里我想起来 哲学的三大问题 我是谁，我从哪里来，要到哪里去。笑死，其实函数也一样要问自己这三个问题，人的智慧其实就是会仿生，无论什么东西都是充斥的模仿的艺术，好比tree数据结构，不是吗?）*

*好，回归正题，函数调用也是一样的道理，当函数A调用函数B，我们只要知道*

- *函数A对于的机器指令执行到了哪里（我从哪里来）*
- *函数B第一条机器指令所在的地址（要到哪里去）*

*那么这些信息是怎么获取并保持的呢，现在我们就可以打开这个小盒子，来看看怎么玩的*

*假设函数A调用函数B，如图所示*

- ![函数A call 函数B](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data/202303051842547.jpg)

*当前，CPU执行函数A的机器指令，该指令的地址为0x400564，接下来CPU将执行下一条机器指令也就是 `call 0x400540`*

*这条机器指令对应的就是我们在代码中所写的函数调用，注意call后有一条机器指令地址，注意观察上图你会看到，**该地址就是函数B的第一条机器指令**，从这条机器指令后CPU将跳转到函数B*

*现在我们已经解决了控制跳转的“要到哪里去”问题，当函数B执行完毕后怎么跳转回来呢？*

*原来，call指令除了给出跳转地址之外还有这样一个作用，也就是**把call指令的下一条指令的地址，也就是0x40056a push到函数A的栈帧中**，如图所示*

- ![](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data/202303051841097.jpg)

*现在，函数A的小盒子变大了一些，因为装入了返回地址*

*现在CPU开始执行函数B对应的机器指令，注意观察，函数B也有一个属于自己的小盒子(栈帧)，可以往里面扔一些必要的信息*

- ![](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data/202303051848881.jpg)

*如果函数B中又调用了其它函数呢？道理和函数A调用函数B是一样的*

*让我们来看一下函数B最后一条机器指令ret，这条机器指令的作用是告诉CPU跳转到函数A保存在栈帧上的返回地址，这样当函数B执行完毕后就可以跳转到函数A继续执行了*

*至此，我们解决了控制转移中“我从哪里来”的问题*

## ***传递参数与获取返回值***

*函数调用与返回使得我们可以编写函数，进行函数调用。但调用函数除了提供函数名称之外还需要传递参数以及获取返回值，那么这又是怎样实现的呢？*

*在x86-64中，多数情况下参数的传递与获取返回值是通过**寄存器**来实现的*

*假设函数A调用了函数B，函数A将一些参数写入相应的寄存器，当CPU执行函数B时就可以从这些寄存器中获取参数了*

*同样的，函数B也可以将返回值写入寄存器，当函数B之行结束后函数A从该寄存器中就可以读取到返回值了*

*但是，我们都知道寄存器的数量是有限的，当传递的参数多于寄存器的数量该怎么办*

*这时候，小盒子也就是栈帧，又是他发挥作用的时候到了*

- *原来，当参数个数多于寄存器数量时剩下的参数直接放到栈帧中，这样被调函数就可以**从前一个函数的栈帧中获取到参数了***

*现在栈帧的样子又可以进一步丰富了，如图所示：*

- ![加了参数的栈帧](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data/202303051858033.png)

*从图中我们可以看到，调用函数B时有部分参数放到了函数A的栈帧中，同时函数A栈帧的顶部依然保存的是返回地址*

## ***局部变量***

*我们知道在函数内部定义的变量被称为局部变量，这些变量在函数运行时被放在了哪里呢*

*原来，这些变量同样可以放在寄存器中，但是当局部变量的数量超过寄存器的时候这些变量就必须放到栈帧中了*

*因此，我们的栈帧内容又一步丰富了*

- ![加了局部变量的栈帧](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data/202303051858483.png)

*细心的同学可能会有这样的疑问，我们知道寄存器是共享资源可以被所有函数使用，既然可以将函数A的局部变量写入寄存器，那么当函数A调用函数B时，函数B的局部变量也可以写到寄存器，这样的话当函数B执行完毕回到函数A时寄存器的值已经被函数B修改过了，这样会有问题吧*

*这样的确会有问题，因此我们在向寄存器中写入局部变量之前，**一定要先将寄存器中开始的值保存起来**，当寄存器使用完毕后再恢复原值就可以了*

*那么我们要将寄存器中的原始值保存在哪里呢？ 没错 还是栈帧*

- ![加了寄存器初始值的栈帧](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data/202303051857350.png)

*最终，我们的小盒子就变成了如图所示的样子，当寄存器使用完毕后根据栈帧中保存的初始值恢复其内容就可以了*

*现在你应该知道函数在运行时到底是什么样子了吧，以上就是问题3的答案*

## ***Big Picture***

***需要再次强调的一点就是，上述讨论的栈帧就位于我们常说的栈区***

*栈区，属于进程地址空间的一部分，如图所示，我们将栈区放大就是图左边的样子*

- ![进程地址空间](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data/202303051903759.png)

*关于栈区详细的讲解你可以参考《深入理解操作系统：程序员应如何理解内存（未完待续）》这篇。*

## ***问题1***

*最后，让我们回到文章开始的这段简单代码*

- ```cpp
  void func(int a) {
      if (a > 100000000) return;
  
      int arr[100] = {0};
      func(a + 1);
  }
  
  void main(){
      func(0);
  }
  ```

*想一想这段代码会有什么问题？*

*原来，**栈区是有大小限制的**，当超过限制后就会出现著名的**栈溢出**问题，显然上述代码会导致这一问题的出现*

*因此我们要注意*

- *不要创建过大的局部变量*
- *函数栈帧，也就是调用层次不能太多*

## ***总结***

1. *本章我们从几个看似没什么关联的问题出发，详细讲解了函数运行时栈是怎么一回事，为什么我们不能创建过多的局部变量*
2. *细心的同学会发现第2个问题我们没有解答，这个问题的讲解可以在《[如何理解协程](https://vlicecream.github.io/%E7%BC%98%E8%B5%B7-%E5%A6%82%E4%BD%95%E7%90%86%E8%A7%A3%E5%8D%8F%E7%A8%8B/)》中理解*

