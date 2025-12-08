# Unreal的委托系统


## ***Unreal的委托系统***

*UE 的委托系统是标准 c++ 函数指针的超级增强版。*

*他是类型安全的、支持反射的、支持多播的*

## ***单播委托***

*特点1：一对一，只能绑定一个函数。如果绑定新的，旧的会被覆盖*

*特点2：可以有返回值*

*适用场景：请求某个结果，比如背包已满...*

*定义宏：`DECLARE_DELEGATE`*

```cpp
// 声明：带一个 int 参数
DECLARE_DELEGATE_OneParam(FOnItemUsed, int32 /*ItemID*/);

// 声明：带返回值 bool，带一个 int 参数
DECLARE_DELEGATE_RetVal_OneParam(bool, FCheckCanEquip, int32 /*ItemID*/);

// 调用：
MyDelegate.ExecuteIfBound(101); // 安全调用
bool bResult = MyRetValDelegate.Execute(101); // 获取返回值
```

## ***多播委托***

*特点1：一对多。可以绑定无数个函数。调用时，所有绑定的函数都会执行（执行顺序不保证）*

*特点2：不能有返回值，因为那么多人回答，不知道听谁的*

*适用场景：广播事件（如：主角死亡后，UI要知道，音乐要转变）*

*定义宏：`DECLARE_MULTICAST_DELEGATE...`*

```cpp
// 声明
DECLARE_MULTICAST_DELEGATE_OneParam(FOnPlayerDied, float /*TimeAlive*/);

// 绑定：
MyDelegate.AddUObject(this, &MyClass::FuncA);
MyDelegate.AddUObject(OtherObj, &OtherClass::FuncB);

// 广播：所有绑定的函数都会跑
MyDelegate.Broadcast(120.5f);
```

## ***动态委托***

*特点1：蓝图可见。他们支持序列化*

*特点2：因为要支持反射 序列化等让蓝图可见，所以性能比上面两种要慢。*

*绑定的函数必须加上 UFUNCTION()*

*定义宏：`DECLARE_DYNAMIC_DELEGATE...`（单播）/ `DECLARE_DYNAMIC_MULTICAST_DELEGATE...` 多播*

```cpp
// 声明：必须加 DYNAMIC，且参数必须有名字
DECLARE_DYNAMIC_MULTICAST_DELEGATE_OneParam(FOnButtonClocked, int32, ButtonIndex);

// 在 UCLASS 里：
UPROPERTY(BlueprintAssignable) // 蓝图可以拖出来 Assign
FOnButtonClocked OnClicked;
```

## ***绑定方法***

| *绑定方式*          | 函数名         | 适用对象                          | 备注                                                         |
| ------------------- | -------------- | --------------------------------- | ------------------------------------------------------------ |
| ***BindUObject***   | *AddUObject*   | *UObject 子类*(Actor, Widget...)* | *最常用。安全，如果对象被 GC 销毁了，委托不会执行，不会崩。* |
| ***BindSP***        | *AddSP*        | *TSharedPtr (普通 C++ 类)*        | *用于由智能指针管理的非 UObject 类。*                        |
| ***BindRaw***       | *AddRaw*       | *普通 C++ 指针*                   | *危险。不安全，如果对象被删了，委托还在，调用会崩。需手动解绑。* |
| ***BindLambda***    | *AddLambda*    | *匿名函数*                        | *非常方便写短逻辑。注意捕获 this 的生命周期。*               |
| ***BindStatic***    | *AddStatic*    | *全局静态函数*                    | *绑定 static void Func()。*                                  |
| ***BindUFunction*** | *AddUFunction* | *按名字绑定*                      | *用于动态委托，或者反射调用。*                               |

## ***小技巧***

```cpp
// 1. 委托定义：只接受一个 bool
DECLARE_DELEGATE_OneParam(FMyDelegate, bool);

// 2. 你的函数：却有两个参数
void MyFunc(bool bSuccess, int32 UserID) 
{
    // ...
}

// 3. 绑定时：把 10086 硬塞进去
// 当委托执行 Execute(true) 时，UE 会自动补上 10086
MyDelegate.BindUObject(this, &MyClass::MyFunc, 10086);
```

## ***Event事件***

*你会看到 DECLARE_EVENT 宏。*
*它本质上就是多播委托 (Multicast Delegate)，唯一的区别是权限控制。*

- *Multicast Delegate：任何拿到这个委托变量的人，都可以调用 .Broadcast()。*
- *Event：只有定义这个 Event 的类，才能调用 .Broadcast()。外部类只能 .Add() (绑定监听)。*

*场景：如果你写一个底层的 Manager，不想让外面的 UI 随便乱发广播，就用 Event。*

## ***`FSimpleDelegate`***

*Unreal 帮你实现的最简单的 无参数 无返回值的单播委托*

## ***`FSimpleMulticastDelegate`***

*Unreal 帮你实现的最简单的 无参数 无返回值的多播委托*

## ***`FTSSimpleMulticastDelegate`***

*Unreal 帮你实现的最简单的 无参数 无返回值的多播委托，但是他是线程安全的*

