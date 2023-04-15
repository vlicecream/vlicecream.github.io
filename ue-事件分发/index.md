# 事件分发


## ***观察者模式***

*如果想了解事件分发，则就先必须先去了解[设计模式 - 观察者模式](https://vlicecream.github.io/%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F-%E8%A7%82%E5%AF%9F%E8%80%85%E6%A8%A1%E5%BC%8F/)*

## ***事件分发机制***

### ***概念***

1. *通过观察者模式的 **订阅 - 发布** 机制，使用事件传递数据与驱动行为的方法*

### ***作用***

1. *数据传递*
   - *UI / HUD数据更新*
   - *角色状态更新*
2. *行为驱动*
   - *网络协议处理*
   - *多系统行为联动*

### ***事件分发的优势***

1. *不使用事件分发的劣势*
   - *最主要的也就是不使用设计模式的劣势 - **无法抵御变化**，因为事件分发的核心思想就是观察者模式*
   - *高耦合、不优雅、随着功能增加，可读性降低，可维护性降低*
2. *使用事件分发的优势*
   - *解耦合、优雅、可读性强、可维护性强、修改需求沟通成本低*
   - *其实就是设计模式的优势*

## ***Unreal内置的事件分发机制***

### ***Blueprints***

1. ***纯蓝图之广播事件通知***

   ![image-20230413222357618](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data%5Cimage-20230413222357618.png)

2. ***纯蓝图之订阅事件通知***

   ![image-20230413225149309](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data%5Cimage-20230413225149309.png)

3. ***纯蓝图之解绑事件通知与安全性测试***

   *如果在游戏运行时，将一个订阅者删除，是不会有任何的问题的，比如崩溃*

   - *解绑事件通知：*

     ![image-20230413225739882](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data%5Cimage-20230413225739882.png)

## ***自定义事件分发机制***

### ***Unreal推荐的游戏开发逻辑开发流程***

![image-20230415152031583](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data%5Cimage-20230415152031583.png)

### ***整体规划***

![image-20230415152121347](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data%5Cimage-20230415152121347.png)

### ***结构与类图***

![image-20230415152150030](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data%5Cimage-20230415152150030.png)

## ***代码示例***

***阿里云盘：https://www.aliyundrive.com/s/oySUgjtkknf***

***提取码：74zj***
