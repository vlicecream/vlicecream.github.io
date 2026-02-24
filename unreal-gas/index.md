# Unreal - GAS


## ***什么是GAS***

*GAS 全称 Gameplay Ability System，是虚幻引擎提供的一套高度模块化、且原生支持网络同步的玩法逻辑框架。它将“技能逻辑”、“数值计算”和“表现效果”彻底解耦，是构建大型 RPG 或动作游戏的工业级标准。*

*GAS 的核心组成部分：*

*1. Ability System Component (ASC) —— 心脏与中枢*

*它是 GAS 的核心组件，所有需要使用 GAS 的对象都必须挂载它。它负责持有和管理所有的能力、效果和标签，并且是网络同步的权威中心。*

*2. Gameplay Abilities (GA) —— 技能逻辑*

*它定义了技能“具体要做什么”。包含技能的释放顺序、激活条件、消耗以及冷却。它是一段逻辑代码块，比如“闪避”、“火球术”或“打开宝箱”。*

*3. Attribute Set —— 属性仓库*

*它是专门存放数值的地方。比如 Health、Mana、Strength。它不仅存储浮点数，还负责处理属性变动时的限制逻辑，比如血量不能超过最大上限。*

*4. Gameplay Effects (GE) —— 技能效果*

*它是改变属性的载体。它不包含代码逻辑，只包含数据。比如“每秒扣 10 点血”、“永久增加 5 点力量”或者“给目标附加一个眩晕状态”。*

*5. Gameplay Tags ——  标签*

*它是层级化的字符串标签。它是 GAS 系统判断逻辑的灵魂，比如通过检查角色是否有“State.Dead”标签来决定是否允许释放技能。*

*6. Gameplay Cues (GC) —— 表现层*

*它专门处理非数值的表现效果，比如粒子特效、音效、震动。它将视觉反馈从复杂的逻辑计算中剥离出来，从而大幅提升性能和网络带宽利用率。*

*7. Ability Tasks —— 异步处理机*

*因为技能通常是持续的过程，它允许技能在运行中“等待”特定事件。比如“等待动画播放到某个节点”、“等待玩家再次输入”或“等待 2 秒钟”。*

*总结：*

*你可以把 GAS 想象成一个自动化的流程：一般来说玩家按下按键触发“能力(GA)”，能力检查“标签(Tags)”确认状态，通过“任务(Tasks)”执行异步过程，产生“效果(GE)”去修改“属性集(Attribute Set)”中的数值，最后触发“提示(GC)”播放华丽的特效。*

## ***AbilitySystemComponent***

*GAS 的心脏。用于与玩法能力系统 (GameplayAbilities System) 对接的核心 Actor 组件。*

*更加通俗的理解就是：它是连接你的角色（Actor）与技能系统（GAS）的桥梁/核心入口*

*它负责所有 Gameplay Ability 的赋予与激活，管理 Attribute 的聚合修改，处理 Gameplay Effect 的生命周期，并作为 Gameplay Tags 的宿主来驱动状态逻辑，同时在底层处理所有相关数据的网络同步与动作预测。*

### ***FPredictionKey***

***FPredictionKey** 是 GameplayAbility 系统中支持“客户端预测”的一种通用方式。它本质上是一个 **ID**，用于标识客户端所执行的预测动作及其产生的“副作用”（Side Effects）。UAbilitySystemComponent（ASC）负责在客户端和服务器之间同步这个预测键及其关联的副作用。*

*本质上，任何行为都可以与一个预测键关联，例如“激活技能”。*

1. ***流程开始**：客户端生成一个全新的预测键，并在调用 ServerTryActivateAbility 时将其发送给服务器。*
2. ***服务器响应**：服务器会确认或拒绝该请求（触发 ClientActivateAbilitySucceed 或 Failed）。*
3. ***本地预测阶段**：在客户端预测技能运行期间，它会产生各种**副作用**（如应用 GameplayEffects、触发事件、播放动画等）。每产生一个副作用，客户端都会将其与启动时生成的那个预测键关联起来。*
4. ***结果处理**：*
   - ***如果激活被拒绝**：客户端可以立即回滚（撤销）这些关联的副作用*。
   - ***如果激活被接受**：客户端必须等待服务器同步真实的副作用数据。*
     - *（ClientActivatbleAbilitySucceed 远程调用会立即发送，但属性同步可能晚几帧到达）。*
     - *一旦服务器版本的副作用同步完成，客户端就会撤销本地预测的临时副作用（并以服务器同步过来的权威数据为准）。*

*一般并不会将伤害数值客户端会预测，只会预测技能特效这些表现效果*

***FPredictionKey 本身主要提供以下功能：***

- ***唯一 ID 系统**：支持“当前”和“基础”整数构成的依赖链系统。*
- ***特殊的网络序列化（NetSerialize）实现**：**该预测键只会序列化给发起预测的那个客户端**。*
  - *这一点至关重要：它允许我们将预测键存在同步状态中，但只有给服务器发送过该键的客户端才能真正看到它（其他客户端不会收到多余的预测数据）。*

## ***IAbilitySystemInterface***

*一个契约。确保引擎和 GAS 内部（如 GameplayCue）无论在哪个 Actor 身上，都能通过一个统一的接口 GetAbilitySystemComponent() 找到它的心脏。*

## ***UAbilitySystemGlobals***

*存储能力系统（GAS）的全局数据。*

*可以通过“项目设置”（Project Settings）中的“游戏玩法能力设置”（Game -> Gameplay Abilities Settings）进行配置。*

## ***元数据与逻辑***

### ***FGameplayTag / FGameplayTagContainer***

*GAS 的 “神经递质”。*

*GAS 几乎所有的判断（能不能放技能、能不能吃 Buff、触发什么特效）全部依赖 Tag。没有标签，GAS 只是普通的属性库；有了标签，它才真正拥有了“逻辑”。*

### ***UAbilitySystemBlueprintLibrary***

*GAS 的“万能工具箱”。*

*提供了一系列静态函数，让你在蓝图中能轻松地获取 ASC、应用 GE、判断 Tag。它是 C++ 底层与蓝图表现层之间的快速通道。*

## ***GAS调试***

```cmd
showdebug abilitysystem
```

*在控制台输入 showdebug abilitysystem。这个命令显示的界面，本质上就是把上面提到的所有 **Spec**、**ActiveGE**、**Tags** 的实时状态可视化给你看。*

## ***能力系统***

### ***UGameplayAbility(GA)***

*能力 (Abilities) 定义了自定义的玩法逻辑，这些逻辑可以由玩家或外部游戏逻辑激活。*

*它包含技能的释放逻辑、CD、消耗、标签要求。*

### ***GA蓝图属性***

*Tags 标签属性组*

​    *Ability Tags 用于标识技能本身的标签 能够让其他逻辑或系统识别出此技能的类别*

​    *Cancel Abilities With Tag 当此技能激活时 会自动寻找并取消掉当前角色身上正在运行且带有这些标签的其他技能*

​    *Block Abilities With Tag 当此技能处于激活状态时 会阻止任何带有这些标签的新技能被触发激活*

​    *Activation Owned Tags 技能在激活期间 技能持有者（ASC）会自动获得这些标签 技能结束时标签随之移除*

​    *Activation Required Tags 激活的前提条件 只有当技能持有者（ASC）已经具备了这些标签时 技能才允许被开启*

​    *Activation Blocked Tags 激活的排除条件 如果技能持有者（ASC）身上带有这些标签 那么该技能将被锁定无法激活*

​    *Source Required Tags 检查技能的发起者 只有当发起者（Source Object）具备这些标签时 技能才能成功触发*

​    *Source Blocked Tags 检查技能的发起者 如果发起者（Source Object）具备这些标签 技能将无法被成功激活*

​    *Target Required Tags 检查技能的目标 只有当目标对象具备这些标签时 技能才会对该目标产生效果*

​    *Target Blocked Tags 检查技能的目标 如果目标对象具备这些标签 技能将无法对该目标产生任何效果*

*Input 输入属性组*

​    *Replicate Input Directly 勾选后会将玩家的原始按键输入直接同步给服务器 而不是通过常规的技能指令流*

*Advanced 高级属性组*

​    *Replication Policy 决定了技能的状态和变量是否在网络上进行同步的策略枚举*

​        *Do Not Replicate 技能不进行网络同步 仅在各自触发的那一端运行*

​        *Replicate 技能的内部状态和变量会从服务器同步给所有相关的客户端*

​    *Instancing Policy 决定了技能在内存中如何创建实例对象的策略枚举*

​        *Non Instanced 技能不产生实例 所有角色共享一个类定义 内存效率最高但不能存储个体状态*

​        *Instanced Per Actor 每个角色在首次使用该技能时创建一个持久实例 适合存储角色的连招段数等*

​        *Instanced Per Execution 每次按下技能都会创建一个全新的独立实例 适合处理复杂的独立逻辑*

​    *Server Respects Remote Ability Cancellation 决定了当客户端尝试主动取消技能时 服务器是否听从该请求*

​    *Retrigger Instanced Ability 对于实例化技能 如果在运行中再次触发 是否停止当前运行并从头重新开始*

​    *Net Execution Policy 决定了技能在客户端和服务器之间执行顺序和同步关系的策略枚举*

​        *Local Predicted 本地预测模式 客户端立即执行并在服务器端进行验证 这种模式下的操作感最流畅*

​        *Local Only 仅本地模式 技能只在本地客户端运行 不会与服务器进行任何通信*

​        *Server Only 仅服务器模式 技能只在服务器运行 客户端不运行任何逻辑 适合安全性高的数值逻辑*

​        *Server Initiated 服务器启动模式 必须由服务器决定何时启动技能并通知客户端同步执行*

​    *Net Security Policy 决定了技能激活权限等级以防止客户端作弊的策略枚举*

​        *Client Or Server 客户端或服务器模式 允许客户端请求激活 灵活但安全性一般*

​        *Server Only Execution 即使客户端请求 也要等服务器确认指令传回后才允许开始执行*

​        *Server Only Termination 客户端无权主动停止技能 必须由服务器判定结束后发送指令*

​        *Server Only 技能的启动和停止完全由服务器掌控 具有最高的防作弊安全性*

*Costs 消耗属性组*

​    *Cost Gameplay Effect Class 指定一个 Gameplay Effect 类 用于定义释放技能需要消耗的属性如法力或体力*

*Triggers 触发属性组*

​    *Ability Triggers 定义了触发技能的各种外部条件数组 包含触发源类型和对应的触发标签*

​        *Trigger Source 决定了技能响应何种外部逻辑变化来尝试自动激活的策略枚举*

​            *Gameplay Event 监听并响应特定的游戏事件标签 通常由其他逻辑发送事件及其数据载荷来精准触发*

​            *Owned Tag Added 当技能持有者（ASC）获得并新增了匹配的标签时 立即触发技能尝试自动激活*

​            *Owned Tag Present 状态检查触发 只要技能持有者（ASC）当前正持有匹配的标签 技能便满足自动激活前提*

*Cooldowns 冷却属性组*

​    *Cooldown Gameplay Effect Class 指定一个 Gameplay Effect 类 用于通过标签和持续时间定义技能的冷却逻辑*

### ***UAbilityTask***

*异步处理机。技能是瞬时启动的，Task 允许技能“等待”某事发生（如：等待动画结束、等待一个碰撞、等待一段时间）*

### ***AGameplayAbilityTargetActor***

*3D 世界的“选择器”。*

*用于在场景中生成视觉辅助（如圆形范围、直线射线）。它负责捕捉玩家选中的目标，并将其打包成 TargetData 发送给服务器。*

*警告：*

* *这些角色在每次能力激活时都会生成一次，并且在默认形式下效率不高*

* *对于大多数游戏，您需要子类化并大量修改此 actor，或者您需要在特定于游戏的 actor 或蓝图中实现类似的功能，以避免 actor 生成成本*

* *这个类没有经过内部游戏的充分测试，但它是一个有用的类，可以用来了解目标复制是如何发生的*

### ***UGameplayAbilityWorldReticle***

*目标的“反馈准心”。*

*专门用于显示目标确认的反馈效果，比如选中的敌人脚下的红圈，或者技能预判的虚影。*

### ***FGameplayAbilitySpec***

*技能的“说明书实例”。*

*当你调用 GiveAbility 时，系统会把 GA 类封装成一个 Spec。它存储了技能的等级、输入绑定 ID、以及谁是这个技能的原始所有者。它是技能在内存中真实存在的“户口本”。*

### ***FGameplayAbilitySpecHandle***

*技能在内存中的“唯一身份证”。*

- ***定义**：一个全局唯一的整数标识符，指向 ASC 中的 FGameplayAbilitySpec。*
- ***作用**：在 C++ 中，你**不应该**保存 UGameplayAbility 的指针，而是应该保存这个 Handle。*
- ***应用场景**：当你需要手动结束技能（EndAbility）或者取消特定技能（CancelAbilityHandle）时，系统只认这个 ID。*

### ***FGameplayAbilityTargetData***

*这是一个用于处理目标数据的通用结构体。目标是让通用的函数能够生成这些数据，并由其他通用函数来消费/处理这些数据。*

*该结构能够同时持有特定的 Actor/对象引用，以及通用的位置（Location）/ 方向（Direction）/ 源点（Origin）信息。*

*生成者（Producers）示例：*

- ***碰撞事件**：重叠（Overlap）或命中（Hit）碰撞事件生成关于近战攻击中“谁被击中”的目标数据。*
- ***准星射线检测**：鼠标输入触发射线检测（Hit Trace），将准星前方的 Actor 转换为目标数据。*
- ***视角信息**：鼠标输入导致目标数据直接从所有者的准星视角源点或方向生成。*
- ***AOE/光环**：范围伤害或光环发生脉冲，将施法者周围半径内的所有 Actor 添加到目标数据中。*
- ***“涂抹”锁定模式**：类似于《铁甲飞龙》（Panzer Dragoon）风格的滑动涂抹锁定模式。*
- ***MMORPG 地面 AOE**：典型的地面范围技能瞄准方式（可能同时包含一个地面坐标位置和在该区域内的 Actor 列表）。*

*消费者（Consumers）示例：*

- ***应用效果**：向目标数据中的所有 Actor 应用一个 GameplayEffect。*
- ***寻找最近目标**：从目标数据包含的所有对象中找到距离最近的 Actor。*
- ***逻辑调用**：对目标数据中的所有 Actor 调用某个特定的函数。*
- ***数据操作**：过滤（Filter）或合并（Merge）多个目标数据。*
- ***生成对象**：在目标数据指定的位置生成（Spawn）一个新的 Actor。*

### ***FGameplayAbilityTargetDataHandle***

*用来包装 FGameplayAbilityTargetData 的句柄*

*它的存在主要有以下两个核心目的：*

- ***性能优化**：避免我们在蓝图中不得不频繁地拷贝整个完整的目标数据结构体。*
- ***支持多态性**：允许我们在目标数据结构中使用多态特性（即一个句柄可以指向不同类型的具体子类数据）。*
- ***网络同步支持**：允许我们实现 NetSerialize（网络序列化），从而在客户端和服务器之间实现“按值同步（Replication by value）”。*

### ***FGameplayAbilityActivationInfo***

*技能的“网络状态快照”。*

- ***定义**：存储在技能实例中的结构体，记录了当前技能是如何启动的。*
- ***作用**：它是处理网络同步（Replication）和预测（Prediction）时的重要依据。*
- ***核心状态**：*
  - *ActivationMode：标识当前是“本地预测（Predicting）”、“服务器权威（Authority）”还是“远程非权威（Non-Authoritative）”。*
- ***价值**：在技能内部逻辑中，你可以通过它判断：“我现在是在客户端先行播放特效吗？”或者是“我现在是在服务器做最后的数值结算吗？”*

### ***FGameplayAbilityActorInfo***

*技能的“环境快照”。*

*记录了技能相关的 Owner、Avatar（化身）、SkeletalMesh、甚至 MovementComponent。它能让技能内部快速获取这些组件，避免频繁调用 GetComponent 造成的性能损耗。*

## ***效果系统***

### ***UGameplayEffect***

*数据容器。它不包含逻辑代码，只包含“修改什么属性、持续多久、有什么标签”。*

*GE 表达了 GAS 架构中一个极其重要的原则：数据与逻辑的彻底分离。*

1. *“驱动一切”的数据资产：*

   *在 GAS 中，UGameplayEffect 本质上是一个“复杂的结构体”。它就像一张处方单，上面写着：加多少血、持续多久、带什么标签。所有的属性改变（Attributes）和状态切换（Tags）都必须通过它来驱动。*

2. *为什么不让写蓝图逻辑（No Graphs）？*

   - *性能开销：GE 在游戏中会被高频触发、叠加和同步。如果每个 GE 内部都有复杂的蓝图逻辑，性能会迅速崩溃。*
   - *可预测性：GE 的作用应该是确定的。逻辑应该写在 Ability (GA) 里，或者写在 Execution Calculation（重型计算器）里，而不是写在 GE 这个“数据包”里。*
   - *网络优化：纯数据的同步比同步一段逻辑要高效得多。*

3. *所谓的“模板（Templating）”：*

   *你在编辑器里右键创建 Gameplay Effect 蓝图时，你其实只是在利用蓝图的界面来填表（设置数值）。这被称为“模板化”。你只是在给这个资产设置初始值，而不是在给它写程序。*

### ***FAttributeBasedFloat***

*1. BackingAttribute（支撑属性捕获定义）*
*数据类型：FGameplayEffectAttributeCaptureDefinition*
*介绍：这是整个计算的“传感器”。它决定了我们要去抓取谁的属性。*
*它的数据包含：*

- *AttributeToCapture：具体的属性指针（比如攻击力、蓝量）。*
- *Source / Target：是从施法者身上抓，还是从受击者身上抓。*
- *bSnapshot：是否快照。如果设为 true，则记录放技能那一刻的属性；如果为 false，则在效果结算那一刻读取实时属性。*

*2. Coefficient（系数）*
*数据类型：FScalableFloat*
*介绍：公式里的乘数。注意，源码里显示它是一个 FScalableFloat。*
*深度含义：这意味着你的“系数”本身也可以随着 GE 的等级（Level）而变化。*
*游戏场景：比如“法力加成”。1 级 GE 时，伤害是蓝量的 1.0 倍；到了 5 级 GE，伤害可能变成蓝量的 1.5 倍。这就是靠这个系数的 FScalableFloat 曲线来实现的。*

*3. PreMultiplyAdditiveValue（预加值）*
*数据类型：FScalableFloat*
*介绍：在乘法执行前，先给捕获到的属性值加上的底数。*
*逻辑顺序：计算器会先执行 (属性值 + 预加值)。*
*游戏场景：比如“保底计算”。即便你的蓝量（属性）是 0，你可以设预加值为 10，保证后面乘以系数时不会算出 0。*

*4. PostMultiplyAdditiveValue（后加值）*
*数据类型：FScalableFloat*
*介绍：在乘法执行完毕后，最后加上的一个固定偏移量。*
*逻辑顺序：(系数 \* 前面的结果) + 这个值。*
*游戏场景：比如“基于蓝量的额外固伤”。你的一招基础伤害是 100（后加值），然后再额外加上蓝量的 20%。*

*5. AttributeCurve（属性曲线表）*
*数据类型：FCurveTableRowHandle*
*介绍：这是源码里最精彩的部分。如果这里填了数据，系统就不直接使用属性数值了，而是把属性数值当做 X 轴坐标，去这张表里查对应的 Y 轴结果。*
*游戏场景：比如“非线性属性收益”。你的力量达到 100 之后，每点力量带来的伤害提升会越来越少（边际递减），这种复杂的数学曲线不需要写代码，直接把这张表填上就行。*

*6. AttributeCalculationType（属性计算策略）*
*数据类型：EAttributeBasedFloatCalculationType*
*介绍：它决定了计算器怎么看待抓到的那个属性。*
*常用选项：*

- *AttributeMagnitude：直接拿属性的当前值（算完 Buff 的结果）。*
- *AttributeBaseValue：只看属性的基础值（不看任何临时 Buff）。*
- *AttributeEvaluatedUpToChannel：这是一个很深的功能，允许你计算属性时只计算到特定的“层级/通道”，比如无视某些高级别的 Buff。*

*7. SourceTagFilter / TargetTagFilter（标签过滤器）*
*数据类型：FGameplayTagContainer*
*介绍：这是给计算过程加的“前置滤网”。*
*作用：只有当来源（Source）或目标（Target）拥有这些特定的标签时，这个属性修改才会生效。如果标签不匹配，这一整套公式可能会直接返回 0。*

### ***FGameplayEffectSpec***

*这个结构体他告诉了我们*

- *使用了哪个 UGameplayEffect（引用的是不可变的常量数据资产）。*
- *等级是多少（Level）。*
- *谁发起的（Instigator）。*

### ***FGameplayEffectSpecHandle***

*允许蓝图仅生成一次 GameplayEffectSpec，随后通过句柄对其进行引用，从而实现将其多次应用或应用给多个不同的目标。*

### ***FActiveGameplayEffect***

*正在运行的“效果快照”。*

*存储在 ASC 内部的结构体。它代表了一个正在生效的持续性（Duration）或永久性（Infinite）的 GE。你想知道自己身上有多少层 Buff，看的就是它。*

### ***FActiveGameplayEffectHandle***

*效果的“遥控器”。*

*一个唯一的 ID。当你应用一个 Buff 后，系统返回这个句柄。你想手动移除某个 Buff（比如驱散效果），必须通过这个句柄来操作。*

### ***FGameplayEffectContext***

*这是一个用于存储 “发起者”及相关数据（例如位置和目标）的数据结构。*

*开发者可以派生（子类化）该结构，以添加特定于游戏的自定义信息。*

*由于该结构贯穿于整个效果执行的全过程，因此它是追踪单次执行过程中“瞬态信息（Transient Information）”的绝佳位置。*

### ***FGameplayEffectContextHandle***

*包装 FGameplayEffectContext 或子类的句柄，以允许其具有多态性并正确复制*

### ***UGameplayModMagnitudeCalculation***

*该类用于通过蓝图或原生代码（C++）执行自定义的玩法效果（Gameplay Effect）修饰符计算。*

*MMC 的核心定位是：为 Gameplay Effect (GE) 里的某一个属性修改提供动态数值。*

- *单一属性修改：如果你只需要算出一个数值（比如“伤害值”或“加血量”），并填入 GE 的某一个 Modifier 里。*
- *需要客户端预测 (Prediction)：这是最关键的一点。MMC 在客户端和服务器都会运行。如果你希望玩家按下按键，UI 上的数值（如蓝条、体力）立即顺滑减少，必须用 MMC*。
- *公式相对独立：公式只涉及基础的数学运算。例如：*
  - 加血量 = 等级 * 20 + 基础值
  - 技能消耗 = 基础消耗 * (1 - 冷却缩减属性)
- *逻辑简单：它只能返回一个 float，不能直接修改标签（Tags）或执行复杂的逻辑分支。*

### ***UGameplayEffectExecutionCalculation***

*重型计算器。用于处理最复杂的伤害公式（涉及多属性对比，如：攻击者的破甲 vs 目标的护甲）。*

*ExecCalc 的核心定位是：处理涉及多个属性交互、复杂逻辑的最终结算。*

- *多属性交互（经典伤害公式）：如果你的计算需要同时读取来源（Source）和目标（Target）的大量属性。例如：*
  - 最终伤害 = (攻击者攻击力 * 暴击倍率 - 目标防御力) * (1 - 目标的免伤率) * 属性克制系数。
- *修改多个属性：ExecCalc 一次执行可以同时修改多个属性。例如：*
  - *一个“吸血”效果：同时减少目标的 Health，增加来源的 Health。*
  - *一个“破甲攻击”：同时扣除目标的 Shield 和 Health。*
- *复杂逻辑判断：内部可以写 if-else。例如：*
  - *“如果目标生命值低于 20%，则触发斩杀，伤害翻倍”。*
  - *“如果目标有‘护盾’标签，则伤害先扣除护盾”。*
- *不需要/不建议预测：ExecCalc 通常只在服务器运行。因为伤害结算涉及跨对象的数据交换，客户端预测极易产生“血条回跳”现象。*

### ***UGameplayEffectContextPayloadBase***

*它是存储在 FGameplayEffectContext 中的自定义动态数据载体。*

*你可以写一个 UHeadshotPayload 继承自这个 Base，里面存一个 `float HeadshotMultiplier`。在应用 GE 前，把这个 Payload 塞进 Context，后续的伤害计算类（ExecCalc）就能精准地把它取出来。*

### ***UGameplayEffectComponent***

*UE5.3 引入的模块化设计。将以前杂乱的 GE 设置（如持续时间、几率等）解耦成组件，提高性能。*

## ***表现层***

### ***UGameplayCue***

*处理非数值的表现（特效、音效、震动）。*

*通过 GameplayTag 触发。它的优点是“火后即焚”，不参与服务器的回滚，只在客户端执行。*

### ***UGameplayCueManager***

*GC 的分发中心。它负责扫描项目中的 GC 路径，并将 Tag 路由到对应的 Notify 或 Actor 上。*

### ***AGameplayCueNotify_Static***

*“火后即焚”的表现。*

*专门处理瞬间触发的特效（如爆炸、打击感）。它性能极高，因为它不需要在场景中生成长久存在的 Actor。*

### ***AGameplayCueNotify_Actor***

*“持续存在”的表现。*

*用于处理持续性的效果表现（如身上一直冒火、护盾光圈）。它会创建一个真实的 Actor 挂在目标身上，支持复杂的开启和关闭逻辑。*

### ***FGameplayCueParameters***

*表现层的“情报包”。*

*当一个 Cue 触发时（比如一次大爆炸），这个结构体负责传递所有的细节。它就像是一个公文包，里面装载了：*

- *Location / Normal：爆炸发生的精确坐标和法线方向。*
- *RawMagnitude：一个原始的浮点数值（比如用来缩放爆炸特效的大小）。*
- *EffectContext：溯源信息，让你知道是哪个技能、哪把武器触发了这个特效。*
  *没有它，你的特效只能呆板地播放，有了它，特效才能实现“哪里被打哪喷血”的精准反馈。*

## ***UAttributeSet***

*属性仓库。存储 Health, Mana, Attack 等浮点数。*

### ***FGameplayAttribute***

*属性的“门牌号”。*

*它不仅仅是一个名字，而是一个结构体，包装了对 UAttributeSet 中特定字段的反射引用。你在代码中指定“修改生命值”时，传的就是这个。*

### ***FGameplayAttributeData***

*属性的“保险箱”。*

*这是你在 AttributeSet 中定义的每一个属性的真实数据类型。它不像普通的 float，它内部包含两个核心数值：*

- *BaseValue（基础值）：你的永久属性。*

- *CurrentValue（当前值）：算上所有临时 Buff/Debuff 后的最终数值。*

  *理解这两者的区别，是解决“为什么我 Buff 消失后血量回不到上限”等问题的关键。*

### ***FAttributeDefaultsWidget***

*属性的“初始化模板”。*

*用于在 DataAsset 中方便地批量配置属性初始值，通常用于 NPC 或不同等级职业的属性初始化。*

