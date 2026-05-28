# B站 - 虚幻 c++ 基础 作业


## ***前言***

*此博客是 https://www.bilibili.com/video/BV1ZSS3BHE3E/?spm_id_from=333.1387.collection.video_card.click 作业*

***作业都以 unreal cpp 为主***

*标题的 "x - x"(eg: 2 - 3) 的意思是 第2堂课 - 第3堂课看完后的作业汇总*

## ***2 - 3 判断循环逻辑语句***

### *第一题：血量预警系统 (if-else 基础)*
***题目描述：***
*假设你在写一个游戏角色的血量系统。定义一个整数变量 `HP`（血量）。*

*   *如果 `HP` 大于 100，输出："血量异常：角色超神了！"*
*   *如果 `HP` 在 20 到 100 之间（含），输出："状态良好。"*
*   *如果 `HP` 在 1 到 19 之间（含），输出："警告：血量过低！"*
*   *如果 `HP` 等于 0，输出："角色已阵亡。"*
*   *其他情况（负数），输出："错误：血量不能为负。"*

---

### *第二题：寻找“幸运金币” (for 循环基础)*
***题目描述：***
*编写一个程序，使用 `for` 循环打印从 1 到 50 的所有数字。*
*但是有一个特殊规则：每当遇到 **7 的倍数**（如 7, 14, 21...）或者**个位数是 7** 的数字（如 17, 27...）时，不打印数字，而是打印字符串：`"Lucky Coin!"`。*

---

### *第三题：技能冷却（CD）计时器 (循环 + 累加)*
***题目描述：***
*一个技能的冷却时间是 10 秒。请使用循环模拟倒计时，从 10 打印到 1。*
*在循环结束后，计算并输出这 10 秒内总共消耗了多少“魔法值”。*
*规则： 假设倒计时的奇数秒（9, 7, 5...）不消耗魔法，偶数秒（10, 8, 6...）每秒消耗 5 点魔法。最后打印出总消耗值。*

---

### *第四题：绘制简易关卡地图 (嵌套循环)*
***题目描述：***
*老师要求你生成一个 5 x 5 的方形网格地图。*
*请使用嵌套的 `for` 循环（一个循环里面再套一个循环）来打印如下图形：*

```text
* * * * *
* * * * *
* * * * *
* * * * *
* * * * *
```
***进阶挑战（可选）：** 只让地图的边界是 `*`，中间全部变为空格（模拟房间）。*

---

### *第五题：Boss 战逻辑模拟 (综合应用)*
***题目描述：***
*Boss 有 100 点血量。玩家每次攻击造成 7 点伤害。*
*请使用 `for` 循环（或 `while`）模拟攻击过程，每攻击一次打印一条信息："Boss 剩余血量：XX"。*
***要求：***

1. *当 Boss 血量低于 50% 时，触发“狂暴状态”，此时玩家的攻击力减半（每次只造成 3 点伤害）。*
2. *当血量降为 0 或以下时，停止循环并输出："战斗结束，Boss 已被击败！"*

## ***4 - 数组与字典 (TArray & TMap)***

### *第一题：背包物品清单 (TArray 基础)*

***题目描述：***
*假设你正在为玩家创建一个简单的背包系统。请定义一个字符串数组 `Inventory`。*

*   *向数组中添加三个初始物品："长剑"、"木盾"、"恢复药剂"内容。*
*   *使用循环遍历数组，并使用 `UE_LOG` 打印出每一个物品的名字。*
*   *最后，打印出背包中总共拥有多少件物品。*

---

### *第二题：最高伤害统计 (TArray 遍历)*

***题目描述：***
*在一场战斗中，你记录了一组伤害数据 `DamageList`（整数数组）。*
*数据内容为：`{15, 42, 8, 91, 23, 67}`。*

*   *请编写逻辑找出这组数据中的最大值（最高伤害）。*
*   *打印输出该最高伤害的值。*

---

### *第三题：怪物属性查询 (TMap 基础)*

***题目描述：***
*请创建一个字典（Map），用于存储不同怪物的名字及其对应的攻击力。*

*   *存储以下数据：`"史莱姆": 5`，`"哥布林": 15`，`"巨魔": 50`。*
*   *编写代码查询 "哥布林" 的攻击力，如果存在，打印其攻击力数值；如果不存在，输出 "未找到该怪物信息"。*

---

### *第四题：装备商店调价 (TMap 遍历与修改)*

***题目描述：***
*假设商店里有几件装备及其价格（Map）：`{"头盔": 100, "铠甲": 300, "靴子": 150}`。*

*   *由于商店搞促销，所有装备的价格都需要打 8 折（乘以 0.8）。*
*   *请遍历字典并更新所有装备的价格（注意价格应保持为整数）。*
*   *最后打印出更新后的所有装备名称及对应价格。*

---

### *第五题：动态库存管理 (综合应用)*

***题目描述：***
*模拟一个掉落物拾取系统。使用字典 `ItemStorage` 记录物品名称及其对应的数量。*

*   *逻辑要求：拾取一个物品时，如果该物品已在字典中，则数量加 1；如果不在，则将其加入字典并将数量设为 1。*
*   *请模拟依次拾取以下物品："金币"、"木材"、"金币"、"金币"、"铁矿"。*
*   *最后打印出字典中所有物品及其最终数量。*

---

## ***5 - 8 通过值指针引用来丰富前面所学扩展***

### *第一题：值传递的“陷阱” (Pass by Value)*

***题目描述：***
*理解值传递（Pass by Value）实际上是创建了一个变量的副本。*

*   *编写一个函数 `TryHealCharacter`，接收一个整数参数 `int32 CurrentHP`。*
*   *函数逻辑：在函数内部给 `CurrentHP` 增加 50 点，并打印函数内部修改后的值。*
*   *在 `BeginPlay` 中定义一个变量 `MyHP = 10`，调用该函数后，分别打印函数运行后的 `MyHP` 值。*
*   *思考：为什么函数执行完后，外部的 `MyHP` 没有变化？*

---

### *第二题：引用传递的“直达” (Pass by Reference)*

***题目描述：***
*引用传递（Reference）相当于给变量起了一个别名，函数内部的操作会直接影响外部变量。*

*   *编写一个函数 `GainExp`，接收一个引用参数 `int32& OutLevel`。*
*   *函数逻辑：将 `OutLevel` 的值加 1。*
*   *在外部定义变量 `PlayerLevel = 5`，调用函数后查看其值。*
*   *要求：对比第一题，解释为什么这次外部变量发生了改变。*

---

### *第三题：指针的“安全检查” (Pointer & Nullptr)*

***题目描述：***
*指针存储的是内存地址。在虚幻引擎中，访问指针前必须检查是否为空（nullptr），否则会导致编辑器崩溃。*

*   *编写一个函数 `DisplayActorName`，接收一个指向 Actor 的指针参数 `AActor* TargetActor`。*
*   *函数逻辑：*
    1.  *首先判断指针是否有效（不为 `nullptr`）。*
    2.  *如果有效，使用 `TargetActor->GetName()` 获取名称并打印。*
    3.  *如果无效，打印 "错误：目标指针为空！"*
*   *测试：分别传入一个真实的 Actor 指针和 `nullptr` 进行测试。*

---

### *第四题：常量引用与性能优化 (Const Reference)*

***题目描述：***
*对于复杂对象（如 `FString`、`TArray`），使用值传递会造成不必要的内存拷贝。通常使用 `const &` 来保证既能高效传递，又不被意外修改。*

*   *编写一个函数 `LogSecureMessage`。*
*   *参数：要求使用常量引用传递字符串 `const FString& Message`。*
*   *逻辑：打印该消息。*
*   *要求：尝试在函数内部修改 `Message` 的内容（如 `Message = "Hacked"`），观察编译器是否报错，并解释原因。*

---

### *第五题：综合应用：战斗系统模拟 (Mixed Passing)*

***题目描述：***
*设计一个综合函数 `ExecuteAttack`，模拟一次复杂的战斗结算。要求同时用到三种传递方式：*

1.  *参数一（值传递）：`int32 BaseDamage`（基础伤害）。*
2.  *参数二（引用传递）：`int32& OutCriticalHits`（暴击次数统计）。*
3.  *参数三（指针传递）：`AActor* Enemy`（敌人对象）。*

***函数要求：***
*   *如果 `Enemy` 指针为空，直接返回。*
*   *如果伤害 `BaseDamage` > 50，则判定为一次暴击，将外部的 `OutCriticalHits` 计数加 1。*
*   *打印出攻击了哪个敌人以及当前的伤害值。*

---

## ***10 - 12 类与对象 (Classes & Objects)***

### *第一题：生命周期追踪 (Constructor & Destructor)*

***题目描述：***
*理解对象何时被创建，何时被销毁。*

*   *请创建一个继承自 `UObject` 的类 `UMinimalistObject`。*
*   *在构造函数中，使用 `UE_LOG` 打印："对象已诞生：[对象地址]"。*
*   *在析构函数（或者虚幻的 `BeginDestroy`）中，打印："对象已被销毁"。*
*   *要求：在主程序中创建一个该类的实例，观察日志输出。*

---

### *第二题：金库访问权限 (Public & Private)*

***题目描述：***
*练习封装思想。私有成员不可直接访问，必须通过公有接口。*

*   *创建一个类 `UPlayerWallet`。*
*   *定义一个私有（`private`）变量 `int32 Money`（初始值为 0）。*
*   *定义两个公有（`public`）函数：*
    1.  *`AddMoney(int32 Amount)`：增加金币。*
    2.  *`GetBalance()`：返回当前余额。*
*   *测试：尝试在类外部直接修改 `Money` 变量，观察编译器报错；然后通过函数正确操作余额。*

---

### *第三题：属性继承与保护 (Protected)*

***题目描述：***
*理解 `protected` 的作用：外部不可见，但子类可以访问。*

*   *定义一个父类 `AMyBaseCharacter`（继承自 Actor）。*
*   *在 `protected` 区域定义一个变量 `FString CharacterName`。*
*   *定义一个子类 `AMyWarrior` 继承自 `AMyBaseCharacter`。*
*   *在子类的 `BeginPlay` 中尝试给 `CharacterName` 赋值，并打印。*
*   *要求：解释为什么在子类中可以修改这个变量，而在外部（如 GameMode）却不行。*

---

### *第四题：技能重写 (Virtual Functions & Inheritance)*

***题目描述：***
*通过继承实现多态。父类定义规范，子类实现细节。*

*   *定义一个父类 `USkillBase`。*
*   *声明一个虚函数（`virtual`） `ExecuteSkill()`，默认打印 "释放基础技能"。*
*   *定义一个子类 `UFireballSkill` 继承自父类，并重写（`override`）该函数。*
*   *在重写的函数中，先调用 `Super::ExecuteSkill()`，然后再打印 "释放火球术：造成爆炸伤害！"。*

---

### *第五题：综合应用：UI 系统基类设计 (Comprehensive)*

***题目描述：***
*模拟你的 MinimalistUI 框架设计。*

*   *设计一个基类 `UMinimalistWidget`：*
    *   *`private`：变量 `bool bIsActive`。*
    *   *`protected`：函数 `OnStateChanged()`。*
    *   *`public`：函数 `ActivateWidget()`（在此函数内将 `bIsActive` 设为 `true` 并调用 `OnStateChanged`）。*
*   *设计一个子类 `UMainMenuWidget`：*
    *   *重写 `OnStateChanged()`，在其中打印 "主菜单状态已更新，播放渐入动画"。*

---

## ***13 - 14 接口与多态 (Interfaces & Polymorphism)***

### *第一题：定义交互接口 (Interface Definition)*

***题目描述：***
*在虚幻引擎中，接口（Interface）用于定义一种“行为协议”。一个类只要“实现”了该接口，就必须具备某种能力。*

*   *请定义一个 C++ 接口 `IMyInteractableInterface`。*
*   *在接口中声明一个纯虚函数：`virtual void ExecuteInteract() = 0;`。*
*   *要求：简述在虚幻 C++ 中，`U` 开头的类和 `I` 开头的类分别起什么作用。*

---

### *第二题：多态的基础——虚函数 (Virtual & Override)*

***题目描述：***
*多态允许我们通过父类指针调用子类重写后的函数。*

*   *定义父类 `ASkillBase`（技能基类），包含虚函数 `virtual void CastSkill()`，默认打印 "释放了基础技能"。*
*   *定义两个子类 `AFireballSkill`（火球术）和 `AHealSkill`（治疗术），分别重写（Override）该函数。*
*   *火球术打印："发射了一枚大火球！"*
*   *治疗术打印："恢复了 50 点生命值！"*

---

### *第三题：指针的多态行为 (Polymorphism via Pointers)*

***题目描述：***
*这是多态的核心应用：使用父类指针指向子类对象。*

*   *在 `BeginPlay` 中，创建一个 `ASkillBase*` 类型的指针。*
*   *先后让该指针指向一个 `AFireballSkill` 对象和 `AHealSkill` 对象。*
*   *调用指针的 `CastSkill()` 函数，观察并打印结果。*
*   *要求：解释为什么同一个指针变量调用同一个函数名，却产生了不同的结果。*

---

### *第四题：接口的动态检测 (Interface Casting)*

***题目描述：***
*当我们拿到一个通用的 `AActor*` 指针时，并不确定它是否支持某种交互，这时需要用到接口转换。*

*   *假设你有一个类 `AChest`（宝箱）实现了交互接口。*
*   *编写一段逻辑：接收一个 `AActor* Target`。*
*   *使用 `Cast<IMyInteractableInterface>(Target)` 尝试将其转换为接口指针。*
*   *如果转换成功（不为空），调用其 `ExecuteInteract()` 函数。*
*   *如果失败，打印 "目标不可交互"。*

---

### *第五题：综合应用：技能管理器 (Array & Polymorphism)*

***题目描述：***
*利用多态管理不同类型的对象。*

*   *创建一个数组 `TArray<ASkillBase*>`，用于存放技能列表。*
*   *向数组中添加一个火球术对象和一个治疗术对象。*
*   *使用 `for` 循环遍历数组，并统一调用 `CastSkill()` 函数。*
*   *要求：体现出“统一接口，多种实现”的思想。*

---

## ***15 - 16 结构体与枚举 (Structs & Enums)***

### *第一题：玩家状态管理 (Enum 基础)*

***题目描述：***
*在游戏中，角色通常处于不同的状态（如：闲置、战斗中、已阵亡）。使用枚举（Enum）可以增加代码的可读性。*

*   *请定义一个 `UENUM` 枚举 `EPlayerState`。*
*   *包含三个状态：`Idle`（闲置）、`Battle`（战斗）、`Dead`（死亡）。*
*   *编写一个函数 `HandleStateChange`，接收 `EPlayerState` 作为参数。*
*   *使用 `switch` 语句判断当前状态，并分别打印对应的日志信息。*

---

### *第二题：角色属性包 (Struct 基础)*

***题目描述：***
*结构体（Struct）用于将相关的变量组合成一个数据包。*

*   *请定义一个 `USTRUCT` 结构体 `FCharacterStats`。*
*   *包含成员变量：`FString Name`（名字）、`int32 Level`（等级）、`float Health`（生命值）。*
*   *在 `BeginPlay` 中创建一个该结构体的实例，并为其成员赋值。*
*   *最后，打印出该角色的所有属性信息。*

---

### *第三题：战斗结果判定 (Enum + Switch 逻辑)*

***题目描述：***
*利用枚举作为函数的返回值来描述复杂的逻辑结果。*

*   *定义一个枚举 `EDamageResult`，包含：`Normal`（普通伤害）、`Critical`（暴击）、`Miss`（闪避）。*
*   *编写函数 `CalculateHitResult`，根据传入的随机数返回不同的枚举值。*
*   *在调用处根据返回的枚举类型执行不同的逻辑（如：暴击时打印红色警告日志）。*

---

### *第四题：物品信息表 (Struct 与数组结合)*

***题目描述：***
*结构体非常适合存储配置数据。*

*   *定义结构体 `FItemData`，包含 `FString ItemName` 和 `int32 Price`。*
*   *创建一个 `TArray<FItemData>` 类型的数组。*
*   *向数组中添加三个物品的数据："药剂"（50金）、"盾牌"（200金）、"宝剑"（500金）。*
*   *使用循环计算并打印出购买这三件物品的总花费。*

---

### *第五题：综合应用：武器系统配置 (Comprehensive)*

***题目描述：***
*将枚举嵌套在结构体中，模拟复杂的武器配置。*

*   *定义枚举 `EWeaponType`：`Sword`（剑）、`Bow`（弓）、`Staff`（法杖）。*
*   *定义结构体 `FWeaponInfo`：*
    *   *`EWeaponType Type`（武器类型）*
    *   *`int32 AttackPower`（攻击力）*
*   *编写一个函数 `LogWeaponDetails`，接收 `FWeaponInfo` 结构体。*
*   *逻辑：先通过 `switch` 判断武器类型名称，再配合打印其攻击力。*

---

## ***17 - 20 虚幻反射系统 (UCLASS, UPROPERTY, UFUNCTION)***

### *第一题：属性的可视化与访问 (UPROPERTY Specifiers)*

***题目描述：***
*在虚幻引擎中，我们通过 `UPROPERTY` 宏告诉编辑器如何处理变量。*

*   *请在一个 Actor 类中定义两个整数变量：*
    1.  *`BaseAttack`：要求在编辑器细节面板中可以随意修改，但在蓝图逻辑中只能读取不能修改。*
    2.  *`ComboMultiplier`：要求在编辑器中不可见，但在蓝图逻辑中既可以读取也可以修改。*
*   *要求：为这两个变量设置分类（Category）为 "CombatStats"。*

---

### *第二题：蓝图调用 C++ 函数 (UFUNCTION BlueprintCallable)*

***题目描述：***
*通过 `UFUNCTION` 宏，我们可以让蓝图调用 C++ 编写的逻辑。*

*   *定义一个函数 `ApplyHealing`，接收一个浮点数 `Amount`。*
*   *要求：*
    1.  *该函数必须能在蓝图中被显式调用（带有执行线）。*
    2.  *该函数在编辑器中的显示名字（DisplayName）为 "Heal Player"。*
*   *编写函数逻辑：打印一条日志显示治疗了多少数值。*

---

### *第三题：纯净函数与性能 (UFUNCTION BlueprintPure)*

***题目描述：***
*有些函数只是为了获取数据，不需要执行线。*

*   *编写一个函数 `GetHealthPercentage`。*
*   *参数：传入当前血量和总血量（浮点数）。*
*   *返回值：返回百分比（0.0 到 1.0）。*
*   *要求：在蓝图中该节点没有执行线（即 `BlueprintPure`）。*

---

### *第四题：C++ 定义接口，蓝图实现逻辑 (BlueprintImplementableEvent)*

***题目描述：***
*有时 C++ 只负责触发时机，而具体的表现（如特效、音效）交给蓝图处理。*

*   *在 C++ 中定义一个函数 `OnPlayerDeath`。*
*   *要求：*
    1.  *该函数在 C++ 中不需要写 `.cpp` 实现。*
    2.  *当玩家血量掉至 0 时，在 C++ 逻辑中调用此函数，并让蓝图能够重写此事件。*

---

### *第五题：反射系统的综合结构 (USTRUCT & UENUM & UPROPERTY)*

***题目描述：***
*将自定义数据类型完全暴露给虚幻系统，使其在编辑器中可配置。*

*   *定义一个枚举 `EItemRarity`（物品稀有度）：`Common`, `Rare`, `Legendary`。*
*   *定义一个结构体 `FItemProfile`：*
    *   *包含变量：`FString ItemName`、`EItemRarity Rarity`、`int32 GoldValue`。*
*   *在一个 Actor 类中定义该结构体变量 `MyItemInfo`。*
*   *要求：让该变量在编辑器中不仅可以修改，还要支持“显示预览”（即在蓝图中通过 Break 节点拆分）。*

---

刚才我们已经完成了**第 11 章：委托基础（定义与蓝图交互）**。既然你再次提到了委托，这说明委托在虚幻引擎 C++ 开发（特别是 UI 框架开发）中极其重要。

这一章我们将深入到**委托的高级进阶：绑定方式与 Lambda 表达式**。这在 C++ 层面编写 UI 逻辑（如：响应点击、延迟回调）时非常高频。

---

## ***21 - 委托高级进阶 (Advanced Delegate Binding)***

### *第一题：匿名函数绑定 (BindLambda)*

***题目描述：***
*在 C++ 中，有时我们不想为了一个小功能专门写一个函数，这时可以使用 Lambda（匿名函数）。*

*   *假设有一个单播委托 `FOnActionTriggered`。*
*   *请展示如何使用 `BindLambda` 绑定一段逻辑：打印一条 "Action Executed via Lambda!" 的日志。*
*   *要求：展示完整的绑定代码，并解释为什么 Lambda 绑定在处理临时逻辑时非常方便。*

---

### *第二题：带负载数据的绑定 (Payload Data)*

***题目描述：***
*委托在绑定时可以“偷偷”带上一些额外的参数，这些参数在定义委托时并不存在。*

*   *定义一个不带参数的单播委托 `FSimpleDelegate`。*
*   *在绑定时，通过负载参数传递一个 `int32 ID = 99`。*
*   *要求：被绑定的函数必须有一个 `int32` 参数来接收这个负载数据。*

---

### *第三题：定时器与委托回调 (Timer & FTimerDelegate)*

***题目描述：***
*在虚幻中，定时器（Timer）经常配合委托使用来实现延迟效果。*

*   *编写一个函数 `StartDelayedTask`。*
*   *要求：使用 `FTimerDelegate` 绑定一个带参数的函数 `OnTimerFinished(FString TaskName)`。*
*   *逻辑：设置定时器在 2 秒后触发该委托。*

---

### *第四题：弱引用绑定防止崩溃 (BindUObject)*

***题目描述：***
*指针可能失效，如果委托调用了一个已经被销毁的对象函数，游戏会崩溃。*

*   *简述 `BindRaw`（原始指针绑定）和 `BindUObject`（UObject 绑定）的区别。*
*   *要求：展示如何使用 `BindUObject` 安全地绑定一个 Actor 的成员函数，并解释为什么这样写更安全。*

---

### *第五题：综合应用：UI 列表动态绑定 (Comprehensive)*

***题目描述：***
*模拟一个动态生成的任务列表，每个按钮点击时都要知道自己代表哪个任务。*

*   *定义一个多播委托 `FOnTaskSelected(int32 TaskID)`。*
*   *在一个循环中创建 3 个对象，并分别为它们的点击事件绑定 Lambda。*
*   *要求：在 Lambda 内部捕获当前循环的索引 `i`（作为 TaskID），点击时打印 "Selected Task ID: X"。*

---

抱歉，刚才确实说得**不够严谨**。你说得对：虚幻的智能指针系统确实分为两套：**一套专门给普通 C++ 类用，另一套专门给 UObject 用。**

如果把 `TSharedPtr` 用在 `UObject` 上，会导致内存管理冲突甚至崩溃。为了纠正这个错误，我们重新梳理一下针对 **UObject** 的智能指针（管理机制）。

---

## ***22 - UObject 专属智能指针***

### *第一题：UE5 新标准 (TObjectPtr)*

***题目描述：***
*在虚幻引擎 5 中，官方推荐在类成员变量中使用 `TObjectPtr<T>` 代替传统的原始指针 `T*`。*

*   *请展示如何在类中定义一个 `TObjectPtr<UStaticMeshComponent>`。*
*   *要求：说明在开发过程中（Editor）它比原始指针多出了哪些功能（如动态访问追踪）。*

---

### *第二题：防止 UObject 内存泄漏 (TWeakObjectPtr)*

***题目描述：***
*`UPROPERTY` 指针是强引用，会阻止 GC 回收对象。如果你只想观察一个对象而不影响它的生命周期，必须使用弱引用。*

*   *假设你有一个指向 `APlayerState` 的指针。*
*   *请展示如何使用 `TWeakObjectPtr` 存储它。*
*   *编写逻辑：在访问该玩家状态前，如何判断该对象是否已经被 GC 回收。*

---

### *第三题：延迟加载与资源优化 (TSoftObjectPtr)*

***题目描述：***
*如果直接在类里使用 `UPROPERTY` 引用一个大贴图或模型，加载该类时会导致所有资源被同步加载。`TSoftObjectPtr` 存储的是路径，只有在需要时才加载。*

*   *定义一个 `TSoftObjectPtr<UTexture2D>` 变量。*
*   *编写代码：演示如何判断资源是否已加载，如果没有加载，如何同步加载它（`ToSoftObjectPath`）。*

### *第四题：类型安全的类引用 (TSubclassOf)*

***题目描述：***
*在开发 UI 框架时，我们经常需要指定“要创建哪一个蓝图类”。如果直接用 `UClass*`，编辑器会允许你选择任何类（甚至选个石头），这非常危险。*

*   *请定义一个 `TSubclassOf<UUserWidget>` 类型的变量 `DefaultWidgetClass`。*
*   *编写逻辑：判断该变量是否有效，如果有效，使用 `CreateWidget` 创建它。*
*   *要求：解释为什么 `TSubclassOf` 比 `UClass*` 更安全。*

---

### *第五题：延迟类加载与内存优化 (TSoftClassPtr)*

***题目描述：***
*在大型 UI 系统中，如果你在主菜单里通过 `TSubclassOf` 引用了 100 个复杂的二级菜单蓝图，加载主菜单时这 100 个蓝图会全部被加载进内存（即使玩家根本不打开它们）。*

*   *请定义一个 `TSoftClassPtr<UUserWidget>` 类型的变量 `SoftWidgetClass`。*
*   *编写代码逻辑：*
    1.  *检查该类是否已经加载。*
    2.  *如果未加载，使用 `LoadSynchronous()` 将其加载并转换为 `TSubclassOf`。*
*   *要求：说明在 UI 框架设计中，什么情况下该用 `TSoftClassPtr` 而不是 `TSubclassOf`。*

## ***25 - const 修饰符 (指针与返回值)***

### *第一题：常量指针 (const int*)*

***题目描述：***
*在 C++ 中，`const int*`（或 `int const*`）被称为“指向常量的指针”。这种写法常用于函数参数，以确保函数内部不会修改原始数据。*

*   *定义一个函数 `void AnalyzeHealth(const int32* HealthPtr)`。*
*   *编写逻辑：在函数内部尝试修改 `*HealthPtr = 100;`，并说明编译器会报什么错。*
*   *要求：解释为什么 `HealthPtr = &OtherHealth;`（修改指针指向的地址）却是合法的。*

---

### *第二题：指针常量 (int* const)*

***题目描述：***
*`int* const` 被称为“指针常量”。它锁定的是指针本身存储的地址，而不是地址里存放的数据。*

*   *在类中定义一个成员变量 `int32* const FixedBufferPtr;` 并假设它已在构造函数初始化列表中绑定了一个地址。*
*   *编写代码：演示尝试执行 `FixedBufferPtr = NewAddress;`。*
*   *要求：解释该写法在内存管理（如固定大小的缓冲区）中的作用。*

---

### *第三题：函数返回常量值 (const T)*

***题目描述：***
*在虚幻引擎的 C++ 规范中，我们经常看到函数返回 `const` 类型，特别是在返回引用时，以防止“外部篡改”。*

*   *编写一个类成员函数 `const FString& GetSecretKey() const;`，返回类中的私有成员 `FString SecretKey`。*
*   *题目：如果在调用处尝试执行 `GetSecretKey() = TEXT("Hacked");`，编译器会如何处理？*
*   *要求：说明返回 `const` 引用相比直接返回变量副本在性能和安全性上的优势。*

---

### *第四题：返回指向常量的指针 (const T*)*

***题目描述：***
*当我们需要向外部暴露一个私有对象的访问权限，但又不允许外部通过该指针对其进行修改时，会返回 `const T*`。*

*   *定义一个函数 `const AActor* GetTargetActor() const;`。*
*   *要求：如果外部调用者拿到了这个指针，它能否调用该 Actor 的非 const 函数（如 `SetActorLocation`）？为什么？*

---

### *第五题：双重锁定 (const int* const)*

***题目描述：***
*有时候为了极致的安全，我们需要同时锁定“指针的指向”和“指向的数据”。*

*   *请定义一个变量 `const int32* const FinalDataPtr` 并将其指向一个已有的整数变量。*
*   *要求：列举出对该指针进行哪些操作会导致编译失败（至少列出两种），并解释这种写法在底层系统开发（如配置文件读取）中的意义。*

---

## ***答案 2 - 3 判断循环逻辑语句***

这里是为您准备的 5 道作业题的标准答案（采用标准 C++ 编写）。您可以直接发给学生参考，或者作为评分标准。

---

### *第一题：血量预警系统*
```cpp
void AMyCharacter::CheckHealthStatus(int32 HP)
{
    if (HP > 100)
    {
        UE_LOG(LogTemp, Warning, TEXT("血量异常：角色超神了！"));
    }
    else if (HP >= 20 && HP <= 100)
    {
        UE_LOG(LogTemp, Log, TEXT("状态良好。"));
    }
    else if (HP >= 1 && HP <= 19)
    {
        UE_LOG(LogTemp, Error, TEXT("警告：血量过低！"));
    }
    else if (HP == 0)
    {
        UE_LOG(LogTemp, Fatal, TEXT("角色已阵亡。"));
    }
    else
    {
        UE_LOG(LogTemp, Display, TEXT("错误：血量不能为负。"));
    }
}
```

---

### *第二题：寻找“幸运金币”*
**考察点：** `%` 取余运算符、逻辑或 `||` 、以及循环控制。

```cpp
void AMyGameMode::FindLuckyCoins()
{
    for (int32 i = 1; i <= 50; ++i)
    {
        // 判断是否是 7 的倍数，或者个位数是 7
        if (i % 7 == 0 || i % 10 == 7)
        {
            UE_LOG(LogTemp, Warning, TEXT("Lucky Coin!"));
        }
        else
        {
            // 在 UE 中打印变量需要使用 %d 等格式化占位符
            UE_LOG(LogTemp, Log, TEXT("Number: %d"), i);
        }
    }
}
```

---

### *第三题：技能冷却（CD）计时器*

*考察点：倒着走的 for 循环和复合赋值运算。*

```cpp
void ASkillActor::StartCooldown()
{
    int32 TotalManaConsumed = 0;

    // 倒计时从 10 开始到 1 结束
    for (int32 i = 10; i >= 1; --i)
    {
        UE_LOG(LogTemp, Log, TEXT("技能倒计时: %d 秒"), i);

        // 偶数秒消耗 5 点魔法
        if (i % 2 == 0)
        {
            TotalManaConsumed += 5;
        }
    }

    UE_LOG(LogTemp, Warning, TEXT("倒计时结束！总计消耗魔法: %d"), TotalManaConsumed);
}
```

---

### *第四题：绘制简易关卡地图*
*考察点：嵌套循环、格式控制。*

```cpp
void ALevelGenerator::GenerateGrid()
{
    // 5x5 的空心房间
    for (int32 i = 0; i < 5; ++i)
    {
        FString RowString = ""; // 用来存储一行的字符串

        for (int32 j = 0; j < 5; ++j)
        {
            // 判断是否是边界
            if (i == 0 || i == 4 || j == 0 || j == 4)
            {
                RowString += TEXT("* ");
            }
            else
            {
                RowString += TEXT("  ");
            }
        }
        // 打印出这一行
        UE_LOG(LogTemp, Log, TEXT("%s"), *RowString);
    }
}
```

---

### *第五题：Boss 战逻辑模拟*
***考察点：** 循环中断 (`break`)、变量动态修改、综合逻辑应用。*

```cpp
void ABossFight::SimulateBossBattle()
{
    int32 BossHP = 100;
    int32 AttackCount = 0;

    while (BossHP > 0)
    {
        AttackCount++;
        int32 Damage = 0;

        if (BossHP >= 50)
        {
            Damage = 7;
        }
        else
        {
            // 狂暴状态伤害降低
            Damage = 3;
            UE_LOG(LogTemp, Warning, TEXT("--- Boss 触发狂暴状态 ---"));
        }

        BossHP -= Damage;
        if (BossHP < 0) BossHP = 0;

        // 使用 FString::Printf 构造更复杂的日志
        FString BattleInfo = FString::Printf(TEXT("第 %d 次攻击！造成 %d 伤害，Boss 剩余血量: %d"), 
            AttackCount, Damage, BossHP);
        
        UE_LOG(LogTemp, Log, TEXT("%s"), *BattleInfo);
    }

    UE_LOG(LogTemp, Error, TEXT("战斗结束：Boss 已被击败！"));
}
```

## ***答案 - 4 - 数组与字典 (TArray & TMap)***

### *第一题：背包物品清单*

***考察点：** `TArray` 的基本定义、`Add` 函数、`Num` 函数以及 `for-each` 遍历。*

```cpp
void UMyLibrary::ManageInventory()
{
    TArray<FString> Inventory;
    
    // 添加物品
    Inventory.Add(TEXT("长剑"));
    Inventory.Add(TEXT("木盾"));
    Inventory.Add(TEXT("恢复药剂"));

    // 遍历打印
    for (const FString& Item : Inventory)
    {
        UE_LOG(LogTemp, Log, TEXT("背包物品: %s"), *Item);
    }

    // 打印数量
    UE_LOG(LogTemp, Warning, TEXT("背包总计物品数: %d"), Inventory.Num());
}
```

---

### *第二题：最高伤害统计*

***考察点：** 数组遍历、最大值比较逻辑。*

```cpp
void UMyLibrary::CalculateMaxDamage()
{
    TArray<int32> DamageList = {15, 42, 8, 91, 23, 67};
    
    if (DamageList.Num() == 0) return;

    int32 MaxDamage = DamageList[0]; // 假设第一个是最大的

    for (int32 i = 1; i < DamageList.Num(); ++i)
    {
        if (DamageList[i] > MaxDamage)
        {
            MaxDamage = DamageList[i];
        }
    }

    UE_LOG(LogTemp, Warning, TEXT("本次战斗最高伤害为: %d"), MaxDamage);
}
```

---

### *第三题：怪物属性查询*

***考察点：** `TMap` 的定义、`Find` 函数的使用（注意 Find 返回的是指针）。*

```cpp
void UMyLibrary::QueryMonsterPower()
{
    TMap<FString, int32> MonsterMap;
    MonsterMap.Add(TEXT("史莱姆"), 5);
    MonsterMap.Add(TEXT("哥布林"), 15);
    MonsterMap.Add(TEXT("巨魔"), 50);

    FString QueryTarget = TEXT("哥布林");
    
    // Find 返回指向 Value 的指针，找不到则返回 nullptr
    int32* FoundPower = MonsterMap.Find(QueryTarget);

    if (FoundPower)
    {
        UE_LOG(LogTemp, Log, TEXT("%s 的攻击力为: %d"), *QueryTarget, *FoundPower);
    }
    else
    {
        UE_LOG(LogTemp, Error, TEXT("未找到该怪物信息"));
    }
}
```

---

### *第四题：装备商店调价*

***考察点：** `TMap` 的引用遍历、数值计算与转换。*

```cpp
void UMyLibrary::UpdateShopPrices()
{
    TMap<FString, int32> EquipmentShop;
    EquipmentShop.Add(TEXT("头盔"), 100);
    EquipmentShop.Add(TEXT("铠甲"), 300);
    EquipmentShop.Add(TEXT("靴子"), 150);

    // 使用迭代器或引用遍历并修改
    for (auto& Elem : EquipmentShop)
    {
        // 打 8 折
        Elem.Value = FMath::FloorToInt(Elem.Value * 0.8f);
        
        UE_LOG(LogTemp, Log, TEXT("装备: %s, 促销价: %d"), *Elem.Key, Elem.Value);
    }
}
```

---

### *第五题：动态库存管理*

***考察点：** `TMap` 的 `Contains` 判断或通过 `FindOrAdd` 实现更新逻辑。*

```cpp
void UMyLibrary::PickupItems()
{
    TMap<FString, int32> ItemStorage;
    TArray<FString> DroppedItems = { TEXT("金币"), TEXT("木材"), TEXT("金币"), TEXT("金币"), TEXT("铁矿") };

    for (const FString& ItemName : DroppedItems)
    {
        // 逻辑：如果存在则增加，不存在则设为 1
        if (ItemStorage.Contains(ItemName))
        {
            ItemStorage[ItemName]++;
        }
        else
        {
            ItemStorage.Add(ItemName, 1);
        }
        
        UE_LOG(LogTemp, Display, TEXT("拾取了物品: %s"), *ItemName);
    }

    // 打印最终结算
    UE_LOG(LogTemp, Warning, TEXT("--- 最终库存清单 ---"));
    for (const auto& Pair : ItemStorage)
    {
        UE_LOG(LogTemp, Log, TEXT("物品: %s, 数量: %d"), *Pair.Key, Pair.Value);
    }
}
```

## ***答案 5 - 8 通过值指针引用来丰富前面所学扩展***

### *第一题：值传递的“陷阱”*

***考察点：** 理解值传递会产生副本，函数内部的操作与外部变量无关。*

```cpp
void AMyCharacter::TryHealCharacter(int32 CurrentHP)
{
    CurrentHP += 50;
    UE_LOG(LogTemp, Log, TEXT("函数内部 HP: %d"), CurrentHP); // 显示 60
}

// 调用示例：
// int32 MyHP = 10;
// TryHealCharacter(MyHP);
// UE_LOG(LogTemp, Warning, TEXT("外部变量 MyHP: %d"), MyHP); // 依然显示 10
```

---

### *第二题：引用传递的“直达”*

***考察点：** 使用 `&` 符号进行引用传递，直接修改原始数据。*

```cpp
void AMyCharacter::GainExp(int32& OutLevel)
{
    OutLevel += 1;
}

// 调用示例：
// int32 PlayerLevel = 5;
// GainExp(PlayerLevel);
// UE_LOG(LogTemp, Warning, TEXT("等级更新为: %d"), PlayerLevel); // 显示 6
```

---

### *第三题：指针的“安全检查”*

***考察点：** 指针解引用（`->`）及空指针防御性编程。*

```cpp
void AMyCharacter::DisplayActorName(AActor* TargetActor)
{
    // 在 UE 中访问指针前，务必判空
    if (TargetActor != nullptr)
    {
        UE_LOG(LogTemp, Log, TEXT("目标名称: %s"), *(TargetActor->GetName()));
    }
    else
    {
        UE_LOG(LogTemp, Error, TEXT("错误：目标指针为空！"));
    }
}
```

---

### *第四题：常量引用与性能优化*

***考察点：** `const &` 既避免了内存拷贝（高效），又防止了函数内部篡改数据（安全）。*

```cpp
void AMyCharacter::LogSecureMessage(const FString& Message)
{
    // Message = TEXT("Hacked"); // 这行会报错，因为是 const
    UE_LOG(LogTemp, Log, TEXT("系统消息: %s"), *Message);
}
```

---

### *第五题：综合应用：战斗系统模拟*

***考察点：** 综合区分三种传递方式的应用场景。*

```cpp
void AMyCharacter::ExecuteAttack(int32 BaseDamage, int32& OutCriticalHits, AActor* Enemy)
{
    // 1. 指针检查
    if (Enemy == nullptr)
    {
        UE_LOG(LogTemp, Warning, TEXT("攻击失败：没有目标！"));
        return;
    }

    // 2. 值传递的使用
    int32 FinalDamage = BaseDamage;

    // 3. 引用传递修改外部统计
    if (BaseDamage > 50)
    {
        OutCriticalHits++;
        UE_LOG(LogTemp, Warning, TEXT("触发暴击！"));
    }

    UE_LOG(LogTemp, Log, TEXT("玩家攻击了 [%s]，造成了 %d 点伤害。"), *(Enemy->GetName()), FinalDamage);
}

// 使用场景：
// int32 TotalCrits = 0;
// AActor* Target = GetNearestEnemy(); // 假设获取一个敌人指针
// ExecuteAttack(60, TotalCrits, Target);
```

## ***答案 10 -12 类与对象 (Classes & Objects)***

### *第一题：生命周期追踪*

***考察点：** 构造函数的定义及对象实例化的基本语法。*

```cpp
// .h
UCLASS()
class UMinimalistObject : public UObject
{
    GENERATED_BODY()
public:
    UMinimalistObject();
    virtual void BeginDestroy() override; // UE中常用BeginDestroy代替析构处理逻辑
};

// .cpp
UMinimalistObject::UMinimalistObject()
{
    UE_LOG(LogTemp, Log, TEXT("对象已诞生，地址为: %p"), this);
}

void UMinimalistObject::BeginDestroy()
{
    Super::BeginDestroy();
    UE_LOG(LogTemp, Warning, TEXT("对象开始销毁过程"));
}
```

---

### *第二题：金库访问权限*

***考察点：** 封装（Encapsulation），理解数据保护的意义。*

```cpp
UCLASS()
class UPlayerWallet : public UObject
{
    GENERATED_BODY()

private:
    int32 Money = 0; // 外部无法直接修改

public:
    void AddMoney(int32 Amount) 
    { 
        if(Amount > 0) Money += Amount; 
    }
    
    int32 GetBalance() const { return Money; }
};

// 调用测试：
// Wallet->Money = 100; // 报错：Money is private
// Wallet->AddMoney(100); // 正确
```

---

### *第三题：属性继承与保护*

***考察点：** `protected` 访问修饰符在继承链中的作用。*

```cpp
// 父类
UCLASS()
class AMyBaseCharacter : public AActor
{
    GENERATED_BODY()
protected:
    FString CharacterName; // 子类可见，外部不可见
};

// 子类
UCLASS()
class AMyWarrior : public AMyBaseCharacter
{
    GENERATED_BODY()
protected:
    virtual void BeginPlay() override
    {
        Super::BeginPlay();
        CharacterName = TEXT("战士阿尔法"); // 允许访问父类的 protected 变量
        UE_LOG(LogTemp, Log, TEXT("角色名: %s"), *CharacterName);
    }
};
```

---

### *第四题：技能重写*

***考察点：** `virtual` 虚函数、`override` 关键字及 `Super` 调用父类逻辑。*

```cpp
// 父类
UCLASS()
class USkillBase : public UObject
{
    GENERATED_BODY()
public:
    virtual void ExecuteSkill() 
    {
        UE_LOG(LogTemp, Log, TEXT("释放基础技能"));
    }
};

// 子类
UCLASS()
class UFireballSkill : public USkillBase
{
    GENERATED_BODY()
public:
    virtual void ExecuteSkill() override
    {
        Super::ExecuteSkill(); // 先执行父类逻辑
        UE_LOG(LogTemp, Warning, TEXT("释放火球术：造成爆炸伤害！"));
    }
};
```

---

### *第五题：综合应用：UI 系统基类设计*

***考察点：** 综合运用权限管理与继承重写实现框架逻辑。*

```cpp
// 基类
UCLASS()
class UMinimalistWidget : public UObject
{
    GENERATED_BODY()
private:
    bool bIsActive = false;

protected:
    virtual void OnStateChanged() { } // 留给子类实现

public:
    void ActivateWidget()
    {
        bIsActive = true;
        OnStateChanged(); // 触发内部通知
    }
};

// 子类
UCLASS()
class UMainMenuWidget : public UMinimalistWidget
{
    GENERATED_BODY()
protected:
    virtual void OnStateChanged() override
    {
        UE_LOG(LogTemp, Log, TEXT("主菜单状态已更新，播放渐入动画"));
    }
};
```

## ***答案  13 - 14 接口与多态 (Interfaces & Polymorphism)***

### *第一题：定义交互接口*

***考察点：** 虚幻接口的特殊结构（U/I 类的配合）。*

```cpp
// .h 文件中
UINTERFACE(MinimalAPI)
class UMyInteractableInterface : public UInterface
{
	GENERATED_BODY()
};

class IMyInteractableInterface
{
	GENERATED_BODY()

public:
    // 定义行为协议：交互
	virtual void ExecuteInteract() = 0; 
};

/* 
   U 类：用于反射系统，让虚幻知道这个接口的存在。
   I 类：包含实际的函数声明，供开发者在子类中重写。
*/
```

---

### *第二题：多态的基础——虚函数*

***考察点：** `virtual` 和 `override` 关键字的正确使用。*

```cpp
// 父类
class ASkillBase : public AActor
{
public:
    virtual void CastSkill() { UE_LOG(LogTemp, Log, TEXT("释放了基础技能")); }
};

// 子类火球
class AFireballSkill : public ASkillBase
{
public:
    virtual void CastSkill() override { UE_LOG(LogTemp, Warning, TEXT("发射了一枚大火球！")); }
};

// 子类治疗
class AHealSkill : public ASkillBase
{
public:
    virtual void CastSkill() override { UE_LOG(LogTemp, Display, TEXT("恢复了 50 点生命值！")); }
};
```

---

### *第三题：指针的多态行为*

***考察点：** 父类指针指向子类对象的地址，以及动态绑定的理解。*

```cpp
void AMyGameMode::TestPolymorphism()
{
    ASkillBase* SkillPtr = nullptr;

    // 指向火球术
    SkillPtr = NewObject<AFireballSkill>();
    SkillPtr->CastSkill(); // 打印：发射了一枚大火球！

    // 指向治疗术
    SkillPtr = NewObject<AHealSkill>();
    SkillPtr->CastSkill(); // 打印：恢复了 50 点生命值！
}
/* 
   解释：因为函数被声明为 virtual，程序在运行时会检查指针指向的
   具体对象的类型（动态绑定），并调用该类型对应的函数版本。
*/
```

---

### *第四题：接口的动态检测*

***考察点：** 接口转换（Casting）及其安全性检查。*

```cpp
void AMyCharacter::TryInteract(AActor* Target)
{
    if (Target == nullptr) return;

    // 尝试将 Actor 指针转换为接口指针
    IMyInteractableInterface* Interactable = Cast<IMyInteractableInterface>(Target);

    if (Interactable)
    {
        // 如果转换成功，说明该 Actor 实现了接口
        Interactable->ExecuteInteract();
    }
    else
    {
        UE_LOG(LogTemp, Error, TEXT("目标不可交互"));
    }
}
```

---

### *第五题：综合应用：技能管理器*

***考察点：** 异质容器（存储不同子类）与多态调用。*

```cpp
void AMySkillSystem::ProcessSkillBar()
{
    TArray<ASkillBase*> SkillBar;

    // 填充不同类型的技能
    SkillBar.Add(NewObject<AFireballSkill>());
    SkillBar.Add(NewObject<AHealSkill>());
    SkillBar.Add(NewObject<ASkillBase>()); // 甚至可以放基类

    // 统一处理逻辑
    for (ASkillBase* CurrentSkill : SkillBar)
    {
        if (CurrentSkill)
        {
            // 通过多态，自动根据对象类型执行正确的 CastSkill
            CurrentSkill->CastSkill();
        }
    }
}
```

## ***答案 15 - 16 结构体与枚举 (Structs & Enums)***

### *第一题：玩家状态管理*

***考察点：** `UENUM` 的声明、`enum class` 的作用域、`switch` 分支逻辑。*

```cpp
UENUM(BlueprintType)
enum class EPlayerState : uint8
{
    Idle    UMETA(DisplayName = "闲置"),
    Battle  UMETA(DisplayName = "战斗中"),
    Dead    UMETA(DisplayName = "已阵亡")
};

void AMyGameMode::HandleStateChange(EPlayerState NewState)
{
    switch (NewState)
    {
    case EPlayerState::Idle:
        UE_LOG(LogTemp, Log, TEXT("玩家现在很闲。"));
        break;
    case EPlayerState::Battle:
        UE_LOG(LogTemp, Warning, TEXT("战斗音乐起！"));
        break;
    case EPlayerState::Dead:
        UE_LOG(LogTemp, Error, TEXT("屏幕变灰了..."));
        break;
    }
}
```

---

### *第二题：角色属性包*

***考察点：** `USTRUCT` 的定义规范（必须以 F 开头）、初始化方式。*

```cpp
USTRUCT(BlueprintType)
struct FCharacterStats
{
    GENERATED_BODY()

    UPROPERTY(EditAnywhere, BlueprintReadWrite)
    FString Name;

    UPROPERTY(EditAnywhere, BlueprintReadWrite)
    int32 Level = 1;

    UPROPERTY(EditAnywhere, BlueprintReadWrite)
    float Health = 100.0f;
};

// 使用示例：
FCharacterStats MyHero;
MyHero.Name = TEXT("战士安东尼");
MyHero.Level = 10;
UE_LOG(LogTemp, Log, TEXT("角色: %s, 等级: %d"), *MyHero.Name, MyHero.Level);
```

---

### *第三题：战斗结果判定*

***考察点：** 枚举作为函数返回值，增强代码的可读性和逻辑严密性。*

```cpp
EDamageResult AMyCharacter::CalculateHitResult(float Chance)
{
    if (Chance > 0.8f) return EDamageResult::Critical;
    if (Chance < 0.2f) return EDamageResult::Miss;
    return EDamageResult::Normal;
}

// 调用示例：
EDamageResult Result = CalculateHitResult(0.9f);
if (Result == EDamageResult::Critical)
{
    UE_LOG(LogTemp, Warning, TEXT("大暴击！造成双倍伤害！"));
}
```

---

### *第四题：物品信息表*

***考察点：** 结构体在容器（TArray）中的应用。*

```cpp
void AMyStore::CalculateTotalCost()
{
    TArray<FItemData> ShoppingList;

    // 添加数据
    ShoppingList.Add(FItemData{ TEXT("药剂"), 50 });
    ShoppingList.Add(FItemData{ TEXT("盾牌"), 200 });
    ShoppingList.Add(FItemData{ TEXT("宝剑"), 500 });

    int32 Total = 0;
    for (const FItemData& Item : ShoppingList)
    {
        Total += Item.Price;
    }

    UE_LOG(LogTemp, Log, TEXT("总共需要支付: %d 金币"), Total);
}
```

---

### *第五题：综合应用：武器系统配置*

***考察点：** 嵌套数据结构、结构体作为参数传递。*

```cpp
void AMyGame::LogWeaponDetails(FWeaponInfo Info)
{
    FString TypeName;
    switch (Info.Type)
    {
        case EWeaponType::Sword: TypeName = TEXT("短剑"); break;
        case EWeaponType::Bow:   TypeName = TEXT("长弓"); break;
        case EWeaponType::Staff: TypeName = TEXT("法杖"); break;
    }

    UE_LOG(LogTemp, Log, TEXT("武器类型: %s, 基础攻击力: %d"), *TypeName, Info.AttackPower);
}

// 调用示例：
// FWeaponInfo MySword = { EWeaponType::Sword, 25 };
// LogWeaponDetails(MySword);
```

## ***答案 17 - 20 虚幻反射系统 (Reflection System)***

### *第一题：属性的可视化与访问*

***考察点：** `EditAnywhere`, `BlueprintReadOnly`, `BlueprintReadWrite` 等特性的组合使用。*

```cpp
UCLASS()
class AMyCharacter : public AActor
{
    GENERATED_BODY()

public:
    // 在面板可改，蓝图只读
    UPROPERTY(EditAnywhere, BlueprintReadOnly, Category = "CombatStats")
    int32 BaseAttack;

    // 面板不可见，蓝图可读写
    UPROPERTY(VisibleNone, BlueprintReadWrite, Category = "CombatStats")
    int32 ComboMultiplier;
};
```

---

### *第二题：蓝图调用 C++ 函数*

***考察点：** `BlueprintCallable` 以及 `meta` 元数据的使用。*

```cpp
// .h
UFUNCTION(BlueprintCallable, Category = "Gameplay", meta = (DisplayName = "Heal Player"))
void ApplyHealing(float Amount);

// .cpp
void AMyCharacter::ApplyHealing(float Amount)
{
    UE_LOG(LogTemp, Log, TEXT("治疗量: %f"), Amount);
}
```

---

### *第三题：纯净函数与性能*

***考察点：** `BlueprintPure` 适用于无状态修改、仅返回值的计算函数。*

```cpp
// .h
UFUNCTION(BlueprintPure, Category = "Stats")
float GetHealthPercentage(float CurrentHealth, float MaxHealth);

// .cpp
float AMyCharacter::GetHealthPercentage(float CurrentHealth, float MaxHealth)
{
    if (MaxHealth <= 0.0f) return 0.0f;
    return CurrentHealth / MaxHealth;
}
```

---

### *第四题：C++ 定义接口，蓝图实现逻辑*

***考察点：** `BlueprintImplementableEvent` 的机制（C++ 无需实现，蓝图直接重写）。*

```cpp
// .h
UFUNCTION(BlueprintImplementableEvent, Category = "Events")
void OnPlayerDeath();

// 触发逻辑 (示例)
void AMyCharacter::TakeDamage(float Damage)
{
    CurrentHP -= Damage;
    if (CurrentHP <= 0)
    {
        // 直接调用，蓝图中的对应的事件节点会被触发
        OnPlayerDeath();
    }
}
```

---

### *第五题：反射系统的综合结构*

***考察点：** 结构体和枚举必须加上 `BlueprintType` 才能在蓝图中作为变量使用。*

```cpp
// 枚举
UENUM(BlueprintType)
enum class EItemRarity : uint8
{
    Common,
    Rare,
    Legendary
};

// 结构体
USTRUCT(BlueprintType)
struct FItemProfile
{
    GENERATED_BODY()

    UPROPERTY(EditAnywhere, BlueprintReadWrite)
    FString ItemName;

    UPROPERTY(EditAnywhere, BlueprintReadWrite)
    EItemRarity Rarity;

    UPROPERTY(EditAnywhere, BlueprintReadWrite)
    int32 GoldValue;
};

// Actor 类中
UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = "Inventory")
FItemProfile MyItemInfo;
```

## ***答案 21 委托高级进阶 (Advanced Delegate Binding)***

### *第一题：匿名函数绑定*

***考察点：** Lambda 表达式在委托绑定中的应用。*

```cpp
// 定义
DECLARE_DELEGATE(FOnActionTriggered);

// 绑定
MyDelegate.BindLambda([]() {
    UE_LOG(LogTemp, Log, TEXT("Action Executed via Lambda!"));
});

// 执行
MyDelegate.ExecuteIfBound();
```

---

### *第二题：带负载数据的绑定*

***考察点：** 理解负载数据（Payload）如何填补定义与实现之间的参数差异。*

```cpp
DECLARE_DELEGATE(FSimpleDelegate);

// 被绑定的函数（带一个参数）
void AMyActor::MyFunction(int32 ID)
{
    UE_LOG(LogTemp, Log, TEXT("接收到负载 ID: %d"), ID);
}

// 绑定过程：虽然委托没参数，但绑定时强行塞入一个 99
MyDelegate.BindUObject(this, &AMyActor::MyFunction, 99);
```

---

### *第三题：定时器与委托回调*

***考察点：** `FTimerDelegate` 与 `SetTimer` 的配合。*

```cpp
void AMyActor::StartDelayedTask()
{
    FTimerDelegate TimerDel;
    
    // 绑定带参数的函数
    TimerDel.BindUObject(this, &AMyActor::OnTimerFinished, TEXT("LevelUpTask"));

    // 设置 2 秒延迟
    GetWorldTimerManager().SetTimer(TimerHandle, TimerDel, 2.0f, false);
}

void AMyActor::OnTimerFinished(FString TaskName)
{
    UE_LOG(LogTemp, Log, TEXT("任务 [%s] 已延迟完成！"), *TaskName);
}
```

---

### *第四题：弱引用绑定防止崩溃*

***考察点：** 安全意识。`BindUObject` 会在执行前自动检查对象是否还“活着”（PendingKill 检查）。*

```cpp
// 强烈建议在 UE 中对 UObject 使用 BindUObject
MyDelegate.BindUObject(this, &AMyActor::SomeFunction);

/* 
   区别：
   1. BindRaw: 绑定原始 C++ 指针，对象删了它照样执行，必崩。
   2. BindUObject: 内部持有弱引用。如果对象被销毁，执行时会自动跳过，不崩溃。
*/
```

---

### *第五题：综合应用：UI 列表动态绑定*

***考察点：** Lambda 变量捕获（Capture）在动态 UI 生成中的实战应用。*

```cpp
void UMyUI::InitializeTaskList()
{
    for (int32 i = 0; i < 3; ++i)
    {
        // 假设我们有 3 个按钮
        UButton* NewButton = CreateButton(); 

        // 关键点：使用 [i] 捕获当前循环的值
        // 如果使用 [&i] 引用捕获，则点击时 i 可能已经变成 3 了，所以必须用值捕获 [i]
        NewButton->OnClickedNative.AddLambda([i]() {
            UE_LOG(LogTemp, Warning, TEXT("Selected Task ID: %d"), i);
        });
    }
}
```

## ***答案 22 UObject 专属智能指针 (UObject Specific)***

### *第一题：UE5 新标准 (TObjectPtr)*

***考察点：** 掌握 UE5 成员变量定义的最新规范。*

```cpp
UCLASS()
class AMyActor : public AActor
{
    GENERATED_BODY()

public:
    // UE5 推荐写法，代替传统的 UStaticMeshComponent* Mesh;
    UPROPERTY(VisibleAnywhere)
    TObjectPtr<UStaticMeshComponent> MeshPtr;
};
/* 
   注意：TObjectPtr 只在类成员变量（Header）中使用，
   函数内部的局部变量依然推荐使用原始指针 T*。
*/
```

---

### *第二题：防止 UObject 内存泄漏 (TWeakObjectPtr)*

***考察点：** 使用弱引用（WeakPtr）配合 GC 检查，避免“假死”引用。*

```cpp
void AMyHUD::CheckPlayerStatus()
{
    // 定义弱引用变量
    TWeakObjectPtr<APlayerState> WeakPlayerState = MyPlayerState;

    // 判断对象是否还活着（且没被标记为 PendingKill）
    if (WeakPlayerState.IsValid())
    {
        UE_LOG(LogTemp, Log, TEXT("玩家分数: %f"), WeakPlayerState->GetScore());
    }
    else
    {
        UE_LOG(LogTemp, Warning, TEXT("玩家状态对象已销毁"));
    }
}
```

---

### *第三题：延迟加载与资源优化 (TSoftObjectPtr)*

***考察点：** 理解资源路径引用与手动加载。*

```cpp
UPROPERTY(EditAnywhere)
TSoftObjectPtr<UTexture2D> LazyTexture;

void AMyActor::LoadIcon()
{
    // 1. 检查是否已经加载到内存
    if (LazyTexture.IsPending())
    {
        // 2. 同步加载（实际项目中常用异步加载 LoadAsync）
        UTexture2D* LoadedTexture = LazyTexture.LoadSynchronous();
    }
}
```

---

*第四题：类型安全的类引用 (TSubclassOf)*

***考察点：** 限制类的选择范围，确保 `CreateWidget` 或 `SpawnActor` 的目标类型合法。*

```cpp
// .h
UPROPERTY(EditAnywhere, Category = "UI")
TSubclassOf<UUserWidget> DefaultWidgetClass; // 只能在编辑器里选 UserWidget 及其子类

// .cpp
void AMyHUD::ShowInitialWidget()
{
    // 1. 检查是否在编辑器里设置了该类
    if (DefaultWidgetClass)
    {
        // 2. 创建 Widget 实例
        UUserWidget* NewWidget = CreateWidget<UUserWidget>(GetWorld(), DefaultWidgetClass);
        if (NewWidget)
        {
            NewWidget->AddToViewport();
        }
    }
}
/* 
   解答：TSubclassOf 提供了模板过滤功能。
   如果你写 TSubclassOf<UUserWidget>，编辑器在下拉列表中只会显示 Widget 蓝图，
   不会显示 Actor 蓝图或其他无关类，从而在编译阶段和配置阶段防止错误。
*/
```

---

### *第五题：延迟类加载与内存优化 (TSoftClassPtr)*

***考察点：** 软引用（Soft Reference）的概念，避免启动时产生巨大的“加载链”。*

```cpp
// .h
UPROPERTY(EditAnywhere, Category = "UI")
TSoftClassPtr<UUserWidget> SoftWidgetClass; // 存储的是路径，不会直接加载

// .cpp
void UMinimalistUISystem::OpenExpensiveWidget()
{
    // 1. 获取类（如果还没加载，这会返回空）
    UClass* WidgetClass = SoftWidgetClass.Get();

    if (!WidgetClass)
    {
        UE_LOG(LogTemp, Log, TEXT("正在同步加载 UI 类..."));
        
        // 2. 同步加载类资源（也可以使用异步加载异步器 FStreamableManager）
        WidgetClass = SoftWidgetClass.LoadSynchronous();
    }

    if (WidgetClass)
    {
        // 3. 此时 WidgetClass 已经加载完毕，可以正常创建
        CreateWidget<UUserWidget>(GetWorld(), WidgetClass)->AddToViewport();
    }
}
/* 
   解答：
   - TSubclassOf 是“硬引用”：加载持有它的 A 对象，就会立刻加载 B 类。
   - TSoftClassPtr 是“软引用”：加载 A 对象时，只存了 B 类的名字/路径，B 不进内存。
   在 UI 框架中，不常用的设置页面、复杂的商店页面应使用 TSoftClassPtr，
   只有当玩家真正点击时才去加载，从而减少游戏的初始加载时间和内存占用。
*/
```

## ***答案 25 const 修饰符***

### *第一题答案*

* **代码**：

  ```cpp
  void AnalyzeHealth(const int32* HealthPtr) {
      // *HealthPtr = 100; // 报错：表达式必须是可修改的左值
      int32 OtherHealth = 50;
      HealthPtr = &OtherHealth; // 合法
  }
  ```

* **原理**：`const` 在 `*` 左边，修饰的是“指向的内容”。数据不可变，但指针可以自由指向别的地址。

### *第二题答案*

*   **操作**：`FixedBufferPtr = &SomeOtherInt;` // 编译错误：无法给常量赋值。
*   **作用**：确保指针始终指向初始化时的固定内存位置，防止程序运行中途意外改变指针指向（如防止缓冲区地址丢失），但允许修改缓冲区内的数据。

### *第三题答案*

*   **结果**：报错，无法对常量引用的返回值进行赋值。
*   **优势**：
    1.  **性能**：避免了 FString 字符串拷贝产生的开销。
    2.  **安全**：保证了类内部封装的数据不会被外部调用者在无意间修改。

### *第四题答案*

*   **解析**：**不能**。编译器会强制执行常量检查。因为返回的是 `const AActor*`，调用者只能通过它访问该 Actor 中声明为 `const` 的成员函数。尝试调用任何会修改对象状态的函数都会导致编译失败。

### *第五题答案*

*   **非法操作**：
    1.  `*FinalDataPtr = 99;`（修改数据）。
    2.  `FinalDataPtr = &AnotherInt;`（修改指针指向）。
*   **意义**：提供最高级别的只读保护。在多线程或底层配置读取场景下，确保一个指针在生命周期内既不会偏离目标地址，其指向的内容也绝对不会被篡改。


