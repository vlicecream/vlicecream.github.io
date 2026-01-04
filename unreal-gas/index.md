# GAS


## ***AbilitySystemComponent***

*GAS 的心脏。*

*处理所有 Ability 的赋予、激活，管理 Attribute 的修改，以及处理 GameplayEffect 的应用与移除。*

## ***IAbilitySystemInterface***

*一个契约。确保引擎和 GAS 内部（如 GameplayCue）无论在哪个 Actor 身上，都能通过一个统一的接口 GetAbilitySystemComponent() 找到它的心脏。*

## ***UAbilitySystemGlobals***

*全局单例配置类*

## ***GameplayAbility(GA)***

### ***GameplayAbility***

*定义技能是什么。*

*它包含技能的释放逻辑、CD、消耗、标签要求。*

### ***UAbilityTask***

*异步处理机。技能是瞬时启动的，Task 允许技能“等待”某事发生（如：等待动画结束、等待一个碰撞、等待一段时间）*

### ***FGameplayAbilityTargetData_SingleTargetHit***

*数据传输协议。用于将客户端选中的目标（位置、Actor 指针等）打包发给服务器。*

## ***效果系统***

### ***GameplayEffect***

*数据容器。它不包含逻辑代码，只包含“修改什么属性、持续多久、有什么标签”。*

### ***UGameplayEffectExecutionCalculation***

*重型计算器。用于处理最复杂的伤害公式（涉及多属性对比，如：攻击者的破甲 vs 目标的护甲）。*

### ***FGameplayEffectContext***

*溯源信息。记录了“谁释放了 GE”、“从哪里释放”、“命中了哪个点”。*

### ***UGameplayEffectContextPayloadBase***

*它是存储在 FGameplayEffectContext 中的自定义动态数据载体。*

*你可以写一个 UHeadshotPayload 继承自这个 Base，里面存一个 `float HeadshotMultiplier`。在应用 GE 前，把这个 Payload 塞进 Context，后续的伤害计算类（ExecCalc）就能精准地把它取出来。*

### ***UGameplayEffectComponent***

*UE5.3 引入的模块化设计。将以前杂乱的 GE 设置（如持续时间、几率等）解耦成组件，提高性能。*

### ***UGameplayModMagnitudeCalculation***

*轻量级动态参数。用于为 GE 里的某一个 Modifier 提供动态数值（如：伤害 = 等级 x 2.0）*

## ***表现层***

### ***GameplayCue***

*处理非数值的表现（特效、音效、震动）。*

*通过 GameplayTag 触发。它的优点是“火后即焚”，不参与服务器的回滚，只在客户端执行。*

### ***UGameplayCueManager***

*GC 的分发中心。它负责扫描项目中的 GC 路径，并将 Tag 路由到对应的 Notify 或 Actor 上。*

## ***UAttributeSet***

*属性仓库。存储 Health, Mana, Attack 等浮点数。*

