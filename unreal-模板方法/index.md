# Unreal - 模板方法


## ***什么是模板***

*模板就是 代码的模具*

*你写一段代码，把类型挖空，用一个占位符 T 代替。编译时，编译器会根据你传入的真实类型，自动生辰对应的代码。*

*例如：*

```cpp
// 这里的 typename T 就是占位符
template <typename T>
T* FindActor(UWorld* World)
{
    // T::StaticClass() 要求传入的 T 必须是 UObject 体系的
    // 这就是泛型编程的“隐式约束”
    return Cast<T>(UGameplayStatics::GetActorOfClass(World, T::StaticClass()));
}

// 使用：
AMyCharacter* Char = FindActor<AMyCharacter>(GetWorld());
```

*`UCLASS` & `USTRUCT` & `UFUNCTION` 不能是模板*

## ***泛型编程***

*泛型编程的核心思想就是：我不关心你是什么类型，我只关心你能不能做这件事*

*例如我们要写一个函数，打印任何对象的名称，我们不关心他是 `AActor` 还是 `UComponent`，只要他有`GetName()` 方法就行*

***类型转换***

*一个 B 继承于 A，我们想把 A 转换成 B，只需要 `Cast<B>(A)`。*

*这 `Cast<T>(Obj)` 就很好用，他也是模板，泛型编程。他不关心T是什么，只关心 T 和 Obj 有继承关系 就够了*

## ***模板偏特化***

*Unreal 其实是有偏特化的，因为他就是c++魔改*

*偏特化只能适用于 class or struct，不能用于 function*

*不能暴露给蓝图  UCLASS / USTRUCT / UFUNCTION*

*所以Unreal 的模板偏特化是用于编写底层工具库，序列化工具的核心技术*

## ***可变长参数模板***

*这个模板允许你传入任意数量、任意类型的参数*

```cpp
// 1. 递归终止函数（Base Case）
// 当参数被剥离完了，就调用这个空的
void MySuperLog() {}

// 2. 递归展开函数
// FirstArg: 当前处理的第一个参数
// RestArgs: 剩下的一堆参数包
template <typename T, typename... Args>
void MySuperLog(const T& FirstArg, const Args&... RestArgs)
{
    // 打印当前的第一个参数
    // 这里利用了 UE 的 FString::FromInt/FromFloat 等转换，或者简单的 << 重载
    // 为了演示简单，我们假设是 String
    UE_LOG(LogTemp, Warning, TEXT("Param: %s"), *LexToString(FirstArg));

    // 递归调用：把剩下的参数包传下去
    MySuperLog(RestArgs...);
}

// --- 使用 ---
void GameStart()
{
    int32 Health = 100;
    float Speed = 50.5f;
    FString Name = "PlayerOne";

    // 一次性打印不同类型，数量不限
    MySuperLog(Name, Health, Speed); 
    // 输出:
    // Param: PlayerOne
    // Param: 100
    // Param: 50.500000
}
```


