# 虚幻 蓝图基础


## ***编辑器介绍***

- *关卡编辑器：构建世界的工具。可以构建关卡、放置和修改Actor、测试关卡*
- *模式面板：包含了可供选择的编辑器的各种工具模式*
- *内容浏览器：在虚幻编辑器中创建、导入、组织、查看和修改内容资源的主区域*
- *世界大纲视图：显示了场景中所有的Actors*
- *详细信息面板：包含特定于当前选择的信息、工具和函数*
- *工具栏：提供了对常用工具和操作的快速访问*

![image-20230329212240321](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data%5Cimage-20230329212240321.png)

## ***蓝图***

### ***蓝图***

- *蓝图（可视化脚本系统）：是一个完整的游戏脚本系统，其理念是，在虚幻编辑器中，使用基于节点的界面创建游戏可玩性元素*
- *和其他一些常见的脚本语言一样，蓝图的用法也是通过定义在引擎中的面向对象的类或者对象*

### ***蓝图类型***

- *关卡蓝图、蓝图类、蓝图宏、蓝图接口、蓝图接口*
- *其中比较常用的是关卡蓝图（Level Blueprints）和蓝图类（Class Blueprints）*

1. *关卡蓝图*
   - *每个关卡具有其自己的关卡蓝图，是作用于整个关卡的全局事件图表*
2. *蓝图类*
   - *适合用来制作交互式的资源*

### ***蓝图编辑器 - 关卡蓝图***

![image-20230329212703702](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data%5Cimage-20230329212703702.png)

### ***蓝图编辑器 - 蓝图类***

- *组件：是一种特殊类型的对象。用作为Actor中的一个子对象*
- *我的蓝图：显示了蓝图中的图表、脚本、函数、宏等的树形列表*
- *图表编辑器：是蓝图系统的核心，可以在这里创建节点和连接节点*
- *详细信息：可以在蓝图编辑器中编辑选中的属性*

![image-20230329212928882](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data%5Cimage-20230329212928882.png)

## ***组件***

### ***概念***

- *一种特殊类型的对象。用作为Actor中的一个子对象*

### ***类型***

- *AI组件、音频组件、摄像机组件、灯光组件、移动组件、寻路组件、Paper 2D组件、物理组件、渲染组件、形状组件、骨架网格模型组件、静态网格模型组件、实用工具组件、控件组件*
- *可以在这网址去查询 ：http://api.unrealengine.com/CHN/Engine/Components/index.html*

## ***图表***

1. *Construction Script(构建脚本)：类蓝图中的一种图表类型。当在编辑器中放置或更新Actor时会执行他，但是在游戏过程中不会执行它*
2. *Event Graph（事件图表）：包含一个使用事件和函数调用用来执行动作的节点图表。用于对和蓝图相关的蓝图事件作出反应。在游戏过程中执行*

## ***节点***

### ***节点*** 

1. *概念*

   *是制作蓝图图表时的基本构建块，如下图所示，每一个构建块都是一个节点*

   *每个节点具有一些可以设置的属性及出入和输出引脚。这些引脚通过执行引脚连线及数据引脚连线连接到一起。来编写您期望的蓝图功能*

   ![什么是节点](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data%5Cimage-20230327220152641.png)

2. *节点类型*

   - *事件(Events)、自定义事件、函数调用、事件调度器、流程控制、数组节点、时间轴、数学表达式节点、随机流、注释*

### ***增加注释***

- *针对单个节点 - 选中节点，随后右键 -> Node Comment，输入注释*
- *针对多个节点 - 选中多个节点，随后右键 -> Node Comment from Selection*

### ***组合节点***

- *选中要组合的节点，随后右键 -> Collapse Nodes*

### ***事件***

### ***概念***

- *从游戏性代码中调用的节点。在事件图表（EventGraph）中开始执行个体网格*
- *他们是蓝图执行一系列操作，对游戏中发生的特定事件（如游戏开始，关卡重置、受到伤害等）进行回应*
- *任意数量的Events均可在单一 EventGraph 中使用，但是每种类型只能使用一个*

### ***常用的事件***

1. *`Event Actor Begin Overlap`：两个Actor的碰撞开始重叠时执行，需要满足两个条件*
   - *Actor之间的碰撞响应必须允许重叠*
   - *执行事件的两个Actor的 Generate Overlap Events 均设为 true*
2. *`Event Actor End Overlap`：两个Actor的碰撞停止重叠时执行*
3. *`Keyboard Events`：接受键盘输入时执行，需满足一个条件*
   - *可以接受输入（Enable Input）*
4. *`Custon Events`：自定义事件，可以在蓝图序列的任何地方调用这些事件*
   - *右键 -> Add Custom Event*![image-20230328200613122](C:\Users\Xu\AppData\Roaming\Typora\typora-user-images\image-20230328200613122.png)

### ***函数调用***

1. *Function Calls(函数调用)*
   - *可以在蓝图中形成的操作。同属于目标Actor或对象的函数相对应*
2. *Other Function Calls(其他函数调用)*
   - *属于除了该蓝图之外的其他对象或Actor的函数*
3. *Pure Function Calls(纯函数调用)*
   - *可以执行的特殊动作。他不会直接影响世界或者世界中的对象*
   - *比如输出一个属性值、数学运算操作*
4. *示例图*
   - <img src="https://raw.githubusercontent.com/vlicecream/cloudImage/main/data%5Cimage-20230328164628347.png" alt="image-20230328164628347"  />

## ***流程控制***

- *Flow Control(流程控制)：使得能在蓝图中清楚的控制执行的流程*
- *此种控制可以以多种形式进行。默认的流程控制操作包括了分支（if）、循环（for and while）、门、序列*

### ***标准流程控制节点***

#### ***Switch开关节点***

1. *概念*
   - *开关节点读取数据输入，并会基于该输入值来从匹配的（或可选的默认）执行输出中发送执行流程*
2. *可用的开关有以下几种类型*
   - *Int、String、Name、Enum*
3. *流程*
   - *右键 -> 搜索 switch，就会出来一堆switch的选项，会根据不同类型值走不同的分支，比如`Switch On Int`的Switch节点就是根据不同的数字去走到不同的分支*

#### ***Branch分支节点***

1. *通过判断输入的Bool变量的 True/False 来进行输出*

#### ***Do N节点***

1. *某条件内，可以执行 n 次。在达到限制后，将停止，知道被重置*

#### ***Do Once节点***

1. *某条件内，仅执行一次。可重置*

#### ***DoOnce MultiInput节点***

1. *执行多个输入中的某一个输入并且响应一次输出。可重置。*

#### ***FlipFlop节点***

1. *在两个执行输出间切换*

#### ***ForLoop***

1. *for循环，将在开始和结束之间的每个索引触发执行*

#### ***ForLoopWithBreak***

1. *包含了能中断循环的输入引脚的for循环*
2. *break 是一个事件, Event Break*

***Gate门节点***

1. *用来开启和关闭执行流*

***MultiGate节点***

1. *单个输入，并执行任意数量的输出。可随机顺序输出，也可以循环输出。可重置*

***Sequence节点***

1. *按顺序触发一系列的事件*

## ***变量***

### ***概念***

*存放一个值或引用世界中的一个Object或Actor的属性*

### ***变量类型***

![image-20230328180627875](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data%5Cimage-20230328180627875.png)

![image-20230328180752143](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data%5Cimage-20230328180752143.png)

## ***数组节点***

1. *操作：Add(添加项目)、Clear(清除项目)、Contains(包含项目)、Find Item(过滤项目)、Get(获取)、Insert Item(插入项目)、Length(长度)等*

## ***随机流***

1. *概念*
   - *允许在蓝图、关卡蓝图及针对动画的动画蓝图中重复地生成及应用随机数*
2. *随机流函数*
   - ![image-20230329215940604](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data%5Cimage-20230329215940604.png)

## ***函数***

1. *概念*
   - *属于蓝图中的节点图表。可以被执行，或在蓝图中的其他图表中被调用*
2. *访问修饰符*
   - *public / protected / private*

## ***宏***

1. *概念*
   - *从本质上讲和合并的节点图表一样*
   - *它们具有由通道节点指定的一个入口点和一个出口点 。每个通道可以具有多个执行引脚或数据引脚*
   - *和函数不同 ， 宏可以具有多个输出执行引脚*

## ***TimeLine***

### ***概念***

- *他是时间轴节点，蓝图中的特殊节点，可以快速地设计基于时间的简单动画，并基于游戏中的事件进行播放*

### ***应用场景***

- *这些节点是专门用来处理简单的、非过场动画式的任务，比如开门、改变光源或对场景中的Actor执行其它基于时间的操作*

### ***节点***

1. *Get Play Rate(获得播放速率)*
2. *Get Playback Position(获得播放位置)*
3. *Get Timeline Length(获得时间轴长度)*
4. *Is Looping(是否循环)*
5. *Is Playing(是否正在播放)*
6. *Is Reversing(是否正在反向播放)*
7. *Play(播放)*
8. *Play from Start(从开始处播放)*
9. *Reverse(反向播放)*
10. *Reverse from End(从结尾处开始反向播放)*
11. *Set Looping(设置循环)*
12. *Set New Time(设置新时间)*
13. *Set Play Rate(设置播放速率)*
14. *Set Playback Position(设置播放位置)*
15. *Set Timeline Length(设置时间轴长度)*
16. *Set Timeline Length Mode(设置时间轴长度模式)*
17. *Stop(停止)*

## ***Transformation***

### ***概念***

- *与物体的移动、旋转、缩放相关的节点*

### ***节点***

1. *GetActorLocation(获取Actor的位置信息)*
2. *GetActorRotation(获取Actor的旋转信息)*
3. *GetActorScale3D(获取Actor的缩放信息)*
4. *GetActorTransform(获取Actor的Transform信息)*
5. *SetActorLocation(设置Actor的位置信息)*
6. *SetActorRotation(设置Actor的旋转信息)*
7. *SetActorScale3D(设置Actor的缩放信息)*
8. *SetActorTransform(设置Actor的Transform信息)*
9. *AddActorLocalOffset(增加偏移量，局部坐标系中)*
10. *AddActorLocalRotation(增加旋转量，局部坐标系中)*
11. *AddActorLocalTransform(增加变换量，局部坐标系中)*
12. *AddActorWorldOffset(增加偏移量，世界坐标系中)*
13. *AddActorWorldRotation(增加旋转量，世界坐标系中)*
14. *AddActorWorldTransform(增加变换量，世界坐标系中)*
15. *GetActorForwardVector(获得Actor的前向量)*
16. *GetActorRightVector(获得Actor的右向量)*
17. *GetActorUpVector(获得Actor的上向量)*
18. *GetDistanceTo(获得两个Actor之间的距离)*

## ***蓝图通信***

### ***蓝图通信***

1. *概念*
   - *使用蓝图时，如果需要在蓝图之间传递或共享信息，则需要使用蓝图通信*
   - *可根据需求使用数种不同类型的通信*
2. *实现方式*
   - *直接蓝图通信、使用事件调度器、使用蓝图接口*

#### ***使用时机***

1. *直接蓝图通信*
   - *最普通的通信方式。适用于在关卡中存在两个Actors，且他们之间需要进行传递或共享信息*
2. *事件调度器*
   - *适用于告知其他”正在倾听的“蓝图已发生事件。事件发生时，正在倾听的蓝图便会作出反应，并相互独立的执行预期的操作*
3. *蓝图接口*
   - *适用于多个蓝图中存在一些相似的功能，但在调用后会执行不同的效果*
