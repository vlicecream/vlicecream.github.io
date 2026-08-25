# Lyra - 网络同步


*Lyra 的网络同步不是简单地给变量加 `Replicated`。它的核心设计是：先把数据放到正确的对象上，然后再决定用属性复制、RPC、GAS、FastArray、SubObject 或 RepGraph 去同步。*

*所以学习 Lyra 网络同步时，不要一开始就陷入各种宏，而是先问四个问题：*

- ***这个数据谁有权修改？***
- ***这个数据应该放在 PlayerState、Pawn、Controller、GameState，还是某个 Component 上？***
- ***哪些客户端需要看到这个数据？***
- ***客户端收到以后，是要保存状态，还是触发表现？***

*Lyra 的整体同步关系可以先理解成这样：*

```text
Client 输入 / 预测表现
        ↓
Server 权威校验 / 修改真实状态
        ↓
Replication / RPC / GAS / FastArray
        ↓
Client OnRep / GameplayMessage / GameplayCue / UI 表现
```

---

## ***Lyra 同步对象怎么分层***

*Lyra 没有把所有状态都塞到 Character 上，而是按照生命周期拆开了。*

```text
GameState
  - 当前 Experience
  - 全局 AbilitySystemComponent
  - 全局消息

PlayerState
  - 玩家长期状态
  - AbilitySystemComponent
  - PawnData
  - Team
  - StatTags

Pawn / Character
  - 当前这条命
  - 移动
  - 动画表现
  - 当前 Avatar

Controller
  - 玩家输入来源
  - QuickBar
  - WeaponState
  - 本地玩家相关组件

Inventory / Equipment / CharacterParts
  - 动态列表
  - FastArray
  - Replicated SubObject
```

*这里最重要的一句话是：*

> ***PlayerState 代表玩家本人，Pawn 代表玩家当前这条命。***

*所以 Lyra 把 `AbilitySystemComponent` 放在 `ALyraPlayerState` 上，而不是放在 `ALyraCharacter` 上。这样角色死亡、销毁、重生时，能力系统依然跟着玩家走。*

---

## ***第一条主线：Experience 的同步***

*Lyra 的玩法内容由 Experience 决定。服务器选择 Experience 后，需要让客户端也加载同一套玩法内容。*

*核心类是：*

```text
ALyraGameState
  -> ULyraExperienceManagerComponent
      -> CurrentExperience
```

*`ULyraExperienceManagerComponent` 本身是可复制组件：*

```cpp
ULyraExperienceManagerComponent::ULyraExperienceManagerComponent(...)
{
    SetIsReplicatedByDefault(true);
}
```

*它里面的 `CurrentExperience` 是 RepNotify：*

```cpp
UPROPERTY(ReplicatedUsing=OnRep_CurrentExperience)
TObjectPtr<const ULyraExperienceDefinition> CurrentExperience;
```

*流程是：*

```text
Server 选择 Experience
  -> SetCurrentExperience()
  -> CurrentExperience 复制到 Client
  -> Client 触发 OnRep_CurrentExperience()
  -> Client 执行 StartExperienceLoad()
  -> Client 加载同一套 GameFeature / ActionSet / DataAsset
```

*也就是说，Lyra 不是把所有玩法类硬编码进 GameMode，而是先同步一个 Experience，再让所有端根据这个 Experience 去加载玩法资源。*

---

## ***第二条主线：PlayerState 承载长期玩家状态***

*`ALyraPlayerState` 是 Lyra 网络同步里最核心的类之一。*

*构造函数里创建 ASC：*

```cpp
AbilitySystemComponent = ObjectInitializer.CreateDefaultSubobject<ULyraAbilitySystemComponent>(this, TEXT("AbilitySystemComponent"));
AbilitySystemComponent->SetIsReplicated(true);
AbilitySystemComponent->SetReplicationMode(EGameplayEffectReplicationMode::Mixed);
```

*这里有两个关键点：*

- ***ASC 是复制的***：技能、效果、属性相关状态可以通过 GAS 自己的复制系统同步。
- ***ReplicationMode 是 Mixed***：Owner 获得更完整的 GameplayEffect 信息，其他客户端拿到必要信息，减少带宽。

*PlayerState 还复制这些重要字段：*

```cpp
DOREPLIFETIME_WITH_PARAMS_FAST(ThisClass, PawnData, SharedParams);
DOREPLIFETIME_WITH_PARAMS_FAST(ThisClass, MyPlayerConnectionType, SharedParams);
DOREPLIFETIME_WITH_PARAMS_FAST(ThisClass, MyTeamID, SharedParams);
DOREPLIFETIME_WITH_PARAMS_FAST(ThisClass, MySquadID, SharedParams);
DOREPLIFETIME(ThisClass, StatTags);
```

*其中 `PawnData` 非常关键。服务器设置 PawnData 后，会给 ASC 授予对应的 AbilitySet：*

```cpp
for (const ULyraAbilitySet* AbilitySet : PawnData->AbilitySets)
{
    if (AbilitySet)
    {
        AbilitySet->GiveToAbilitySystem(AbilitySystemComponent, nullptr);
    }
}
```

*所以可以这样理解：*

```text
Experience 决定这局是什么玩法
PawnData 决定这个玩家用什么角色配置
AbilitySet 决定这个玩家拥有什么能力、效果、属性
```

---

## ***第三条主线：PawnExtension 解决初始化顺序问题***

*网络游戏里，客户端收到对象的顺序是不稳定的。Pawn 可能先到，PlayerState 可能后到；Controller 可能已经复制了，但 PawnData 还没到。*

*Lyra 用 `ULyraPawnExtensionComponent` 管这个问题。*

*它复制 PawnData：*

```cpp
DOREPLIFETIME(ULyraPawnExtensionComponent, PawnData);
```

*客户端收到 PawnData 后：*

```cpp
void ULyraPawnExtensionComponent::OnRep_PawnData()
{
    CheckDefaultInitialization();
}
```

*`CheckDefaultInitialization()` 会推进 InitState：*

```text
Spawned
  -> DataAvailable
  -> DataInitialized
  -> GameplayReady
```

*只有当 PawnData、Controller、PlayerState 等条件都满足后，Lyra 才初始化 ASC：*

```cpp
AbilitySystemComponent = InASC;
AbilitySystemComponent->InitAbilityActorInfo(InOwnerActor, Pawn);
```

*这里的 OwnerActor 通常是 PlayerState，AvatarActor 是当前 Pawn。*

```text
ASC Owner  = PlayerState
ASC Avatar = Pawn / Character
```

*这就是为什么 Lyra 的角色重生不会重新创建一整套能力系统，而是把 PlayerState 上的 ASC 重新绑定到新的 Pawn。*

---

## ***第四条主线：Character 的移动同步***

*Lyra 的移动同步主要还是依赖 Unreal 原生的 `CharacterMovementComponent`。*

*也就是说：*

```text
客户端输入移动
  -> 本地预测
  -> 发送移动数据给服务器
  -> 服务器校验
  -> 必要时校正客户端
  -> 其他客户端看到 Simulated Proxy 的移动
```

*Lyra 在这个基础上额外复制了一个压缩后的加速度：*

```cpp
UPROPERTY(Transient, ReplicatedUsing = OnRep_ReplicatedAcceleration)
FLyraReplicatedAcceleration ReplicatedAcceleration;
```

*它只复制给模拟代理：*

```cpp
DOREPLIFETIME_CONDITION(ThisClass, ReplicatedAcceleration, COND_SimulatedOnly);
```

*原因是本地控制者自己有输入，不需要服务器再告诉自己加速度；真正需要这个数据的是其他客户端上的远端角色，用它来改善动画表现。*

*Lyra 还定义了 `FastSharedReplication`：*

```cpp
UFUNCTION(NetMulticast, unreliable)
void FastSharedReplication(const FSharedRepMovement& SharedRepMovement);
```

*这是配合 ReplicationGraph 的快速共享移动路径，用来在默认属性复制被跳过的帧里，仍然可以用 unreliable multicast 发一份轻量移动数据。*

*不过当前工程配置里：*

```ini
[/Script/LyraGame.LyraReplicationGraphSettings]
bDisableReplicationGraph=True
```

*所以默认情况下 RepGraph 是关闭的。代码保留了这条优化路径，但普通运行不一定会启用。*

---

## ***第五条主线：GAS 的网络同步***

*Lyra 的技能、Buff、伤害、血量都主要走 GAS。*

*默认 Lyra GameplayAbility 是本地预测的：*

```cpp
NetExecutionPolicy = EGameplayAbilityNetExecutionPolicy::LocalPredicted;
NetSecurityPolicy = EGameplayAbilityNetSecurityPolicy::ClientOrServer;
```

*简单理解就是：*

```text
客户端先预测执行
  -> 服务端再权威确认
  -> 如果预测不一致，GAS 负责回滚 / 修正
```

*输入链路大概是：*

```text
LyraHeroComponent 绑定 EnhancedInput
  -> InputTag Pressed / Released
  -> ULyraAbilitySystemComponent 记录 InputPressedSpecHandles
  -> PlayerController::PostProcessInput()
  -> ASC::ProcessAbilityInput()
  -> TryActivateAbility()
```

*技能激活、技能结束、InputPressed、InputReleased 这些，很多都不是 Lyra 自己手写 RPC，而是 GAS 内部已经处理了预测键、能力句柄和复制事件。*

---

## ***第六条主线：Attribute 怎么同步***

*Lyra 的生命值在 `ULyraHealthSet` 里。*

*`Health` 和 `MaxHealth` 都是 RepNotify：*

```cpp
UPROPERTY(BlueprintReadOnly, ReplicatedUsing = OnRep_Health)
FGameplayAttributeData Health;

UPROPERTY(BlueprintReadOnly, ReplicatedUsing = OnRep_MaxHealth)
FGameplayAttributeData MaxHealth;
```

*复制规则是：*

```cpp
DOREPLIFETIME_CONDITION_NOTIFY(ULyraHealthSet, Health, COND_None, REPNOTIFY_Always);
DOREPLIFETIME_CONDITION_NOTIFY(ULyraHealthSet, MaxHealth, COND_None, REPNOTIFY_Always);
```

*客户端收到后会执行：*

```cpp
GAMEPLAYATTRIBUTE_REPNOTIFY(ULyraHealthSet, Health, OldValue);
```

*然后 Lyra 会广播本地事件，比如：*

```text
OnHealthChanged
OnMaxHealthChanged
OnOutOfHealth
```

*这些事件再被 UI、死亡逻辑、表现层监听。*

*需要注意的是，伤害不是直接复制 `Damage` 这个临时属性。Lyra 的流程是：*

```text
GameplayEffect / Execution 算出 Damage
  -> PostGameplayEffectExecute()
  -> Health = Health - Damage
  -> Damage 清零
  -> Health 复制给客户端
```

---

## ***第七条主线：开火同步***

*Lyra 的远程武器同步很适合拿来理解“客户端手感”和“服务器权威”的分工。*

*大流程是：*

```text
客户端按下开火
  -> 激活 RangedWeapon Ability
  -> 本地 PerformLocalTargeting() 做射线检测
  -> 本地立刻播放开火表现 / hit marker 预测
  -> 构造 GameplayAbilityTargetData
  -> CallServerSetReplicatedTargetData() 发给服务器
  -> 服务器消费 TargetData
  -> 服务器校验并应用 GameplayEffect
  -> HealthSet 改血
  -> Health 复制给所有需要的客户端
  -> 服务器 ClientConfirmTargetData() 回给开火客户端
```

*这里的核心函数是：*

```text
ULyraGameplayAbility_RangedWeapon::StartRangedWeaponTargeting()
ULyraGameplayAbility_RangedWeapon::PerformLocalTargeting()
ULyraGameplayAbility_RangedWeapon::OnTargetDataReadyCallback()
ULyraWeaponStateComponent::ClientConfirmTargetData()
```

*客户端本地先 trace，是为了手感。服务器最后确认，是为了权威。*

*所以这条链路可以总结成：*

```text
客户端负责“我感觉我打中了”
服务器负责“这次到底算不算打中”
```

---

## ***第八条主线：Inventory 的 FastArray 同步***

*Lyra 的背包不是普通数组复制，而是 `FFastArraySerializer`。*

*核心结构是：*

```text
ULyraInventoryManagerComponent
  -> FLyraInventoryList : FFastArraySerializer
      -> TArray<FLyraInventoryEntry>
          -> ULyraInventoryItemInstance
```

*`FLyraInventoryList` 使用 FastArray delta 序列化：*

```cpp
return FFastArraySerializer::FastArrayDeltaSerialize<FLyraInventoryEntry, FLyraInventoryList>(Entries, DeltaParms, *this);
```

*它的好处是：*

- ***不是整个数组每次重发***。
- ***只同步新增、删除、变化的元素***。
- ***客户端可以在 PostReplicatedAdd / PreReplicatedRemove 里处理表现***。

*每个物品实例 `ULyraInventoryItemInstance` 自己也复制：*

```cpp
DOREPLIFETIME(ThisClass, StatTags);
DOREPLIFETIME(ThisClass, ItemDef);
```

*因为 ItemInstance 是 UObject，不是 Actor，所以 Lyra 还需要把它作为 replicated subobject 注册：*

```cpp
AddReplicatedSubObject(Instance);
```

*所以 Inventory 实际上是两层同步：*

```text
FastArray 同步“列表里有哪些物品”
SubObject 同步“每个物品自己的数据”
```

---

## ***第九条主线：Equipment 的 FastArray 同步***

*装备系统和背包系统很像。*

```text
ULyraEquipmentManagerComponent
  -> FLyraEquipmentList : FFastArraySerializer
      -> TArray<FLyraAppliedEquipmentEntry>
          -> ULyraEquipmentInstance
```

*装备列表复制：*

```cpp
DOREPLIFETIME(ThisClass, EquipmentList);
```

*装备实例作为 replicated subobject：*

```cpp
AddReplicatedSubObject(Result);
```

*客户端收到装备增加时：*

```cpp
PostReplicatedAdd()
  -> Entry.Instance->OnEquipped()
```

*客户端收到装备移除时：*

```cpp
PreReplicatedRemove()
  -> Entry.Instance->OnUnequipped()
```

*这就是为什么你看到装备同步后，客户端会自动生成/销毁对应装备表现。*

---

## ***第十条主线：QuickBar 怎么同步***

*QuickBar 是装备栏，逻辑比 Inventory / Equipment 更直接。*

*切换槽位是 Server RPC：*

```cpp
UFUNCTION(Server, Reliable, BlueprintCallable, Category="Lyra")
void SetActiveSlotIndex(int32 NewIndex);
```

*槽位数组和当前激活槽位是 RepNotify：*

```cpp
UPROPERTY(ReplicatedUsing=OnRep_Slots)
TArray<TObjectPtr<ULyraInventoryItemInstance>> Slots;

UPROPERTY(ReplicatedUsing=OnRep_ActiveSlotIndex)
int32 ActiveSlotIndex = -1;
```

*流程是：*

```text
客户端请求切换槽位
  -> Server RPC: SetActiveSlotIndex()
  -> 服务器修改 ActiveSlotIndex
  -> ActiveSlotIndex 复制到客户端
  -> OnRep_ActiveSlotIndex()
  -> GameplayMessage 通知 UI
```

*这里可以看到 RPC 和 RepNotify 的分工：*

```text
RPC 负责“我要切换”
Replicated Property 负责“现在切到哪里了”
OnRep 负责“收到以后刷新表现”
```

---

## ***第十一条主线：CharacterParts 外观同步***

*角色外观部件也是 FastArray。*

```text
ULyraPawnComponent_CharacterParts
  -> FLyraCharacterPartList : FFastArraySerializer
      -> FLyraAppliedCharacterPartEntry
```

*它同步的不是简单 Mesh，而是“应用了哪些 CharacterPart”。客户端收到增删改以后，会生成或销毁对应的外观 Actor。*

*这类数据适合 FastArray，因为它是动态列表，并且经常是增删改，而不是每帧变化。*

---

## ***第十二条主线：Team 同步***

*队伍信息主要在 PlayerState 上。*

```cpp
UPROPERTY(ReplicatedUsing=OnRep_MyTeamID)
FGenericTeamId MyTeamID;
```

*服务器修改队伍：*

```cpp
SetGenericTeamId()
  -> MyTeamID = NewTeamID
  -> ConditionalBroadcastTeamChanged()
```

*客户端收到队伍变化：*

```cpp
OnRep_MyTeamID()
  -> ConditionalBroadcastTeamChanged()
```

*Character 上也有一份 `MyTeamID`，它跟随 Controller / PlayerState 的队伍，用来让 Pawn 本身也能作为 TeamAgent 被查询。*

*所以队伍系统大概是：*

```text
PlayerState 保存玩家队伍
Character 缓存当前 Pawn 队伍
TeamSubsystem 负责查询、比较、伤害判断
AsyncAction_ObserveTeam 负责蓝图/UI 监听变化
```

---

## ***第十三条主线：消息不是都靠网络复制***

*Lyra 很多表现层通知使用 `GameplayMessageSubsystem`。*

*需要注意：GameplayMessage 本身更像本地消息总线，不等于网络复制系统。*

*比如 GameState 提供了两个 multicast：*

```cpp
UFUNCTION(NetMulticast, Unreliable)
void MulticastMessageToClients(const FLyraVerbMessage Message);

UFUNCTION(NetMulticast, Reliable)
void MulticastReliableMessageToClients(const FLyraVerbMessage Message);
```

*客户端收到 multicast 后，再在本地广播 GameplayMessage：*

```cpp
UGameplayMessageSubsystem::Get(this).BroadcastMessage(Message.Verb, Message);
```

*也就是说：*

```text
网络层：NetMulticast 把消息送到客户端
本地层：GameplayMessageSubsystem 通知 UI / 逻辑模块
```

*这种设计让 UI 不需要直接依赖 GameState、ASC 或 Weapon 代码。*

---

## ***第十四条主线：ReplicationGraph 在 Lyra 里的定位***

*ReplicationGraph 是 Unreal 用来优化大量 Actor 复制的系统。普通复制会对每个连接判断很多 Actor 的相关性，而 RepGraph 会提前把 Actor 放进不同节点。*

*Lyra 自己实现了：*

```text
ULyraReplicationGraph
ULyraReplicationGraphNode_AlwaysRelevant_ForConnection
ULyraReplicationGraphNode_PlayerStateFrequencyLimiter
```

*主要思路是：*

- ***空间化节点***：距离相关 Actor 放到 GridSpatialization2D。
- ***全局 AlwaysRelevant***：所有客户端都需要的 Actor。
- ***连接级 AlwaysRelevant***：只对某个连接重要的 Actor，比如自己的 Controller、ViewTarget、PlayerState。
- ***PlayerState 限频节点***：不是每帧复制所有 PlayerState，而是分批复制。
- ***FastSharedReplication***：配合角色移动做更轻的共享更新。

*但是当前默认配置关闭了：*

```ini
bDisableReplicationGraph=True
```

*所以学习 Lyra 时，可以先理解它的设计，再决定是否在自己的项目中打开。*

---

## ***Lyra 用到的 Unreal 网络机制汇总***

*Lyra 主要用到了这些 Unreal 网络同步机制：*

| *机制* | *Lyra 中的用途* |
| --- | --- |
| `Replicated` / `OnRep` | *同步状态，比如 Health、PawnData、Team、QuickBar* |
| `Server RPC` | *客户端请求服务器做事，比如切换 QuickBar* |
| `Client RPC` | *服务器通知某个客户端，比如命中确认* |
| `NetMulticast` | *服务器广播消息或表现* |
| `CharacterMovement` | *移动预测、服务端校正、模拟代理移动* |
| `GAS Replication` | *Ability、GameplayEffect、Attribute、Prediction、TargetData* |
| `FastArraySerializer` | *Inventory、Equipment、CharacterParts 动态列表* |
| `Replicated SubObject` | *ItemInstance、EquipmentInstance 这种 UObject 复制* |
| `Replication Condition` | *OwnerOnly、SkipOwner、SimulatedOnly 等条件复制* |
| `Push Model` | *属性变化时主动标脏，比如 PawnData、TeamID* |
| `GameplayMessage` | *客户端本地解耦通知 UI / 逻辑* |
| `GameplayCue` | *GAS 驱动表现，如特效、音效* |
| `ReplicationGraph` | *大规模 Actor 复制优化，Lyra 默认关闭* |

---

## ***从源码应该怎么读***

*如果想自己从代码里追 Lyra 网络同步，我建议按下面顺序读：*

### ***1. Experience 同步***

```text
Source/LyraGame/GameModes/LyraGameState.h
Source/LyraGame/GameModes/LyraGameState.cpp
Source/LyraGame/GameModes/LyraExperienceManagerComponent.h
Source/LyraGame/GameModes/LyraExperienceManagerComponent.cpp
```

*先看 `CurrentExperience` 怎么从服务器复制到客户端。*

### ***2. PlayerState 和 ASC***

```text
Source/LyraGame/Player/LyraPlayerState.h
Source/LyraGame/Player/LyraPlayerState.cpp
Source/LyraGame/AbilitySystem/LyraAbilitySystemComponent.h
Source/LyraGame/AbilitySystem/LyraAbilitySystemComponent.cpp
```

*重点看 ASC 为什么在 PlayerState 上，以及 PawnData 如何授予 AbilitySet。*

### ***3. Pawn 初始化***

```text
Source/LyraGame/Character/LyraPawnExtensionComponent.h
Source/LyraGame/Character/LyraPawnExtensionComponent.cpp
Source/LyraGame/Character/LyraCharacter.h
Source/LyraGame/Character/LyraCharacter.cpp
```

*重点看 PawnData、Controller、PlayerState 到齐后如何进入 GameplayReady。*

### ***4. GAS 属性与伤害***

```text
Source/LyraGame/AbilitySystem/Attributes/LyraHealthSet.h
Source/LyraGame/AbilitySystem/Attributes/LyraHealthSet.cpp
Source/LyraGame/AbilitySystem/Attributes/LyraCombatSet.h
Source/LyraGame/AbilitySystem/Attributes/LyraCombatSet.cpp
```

*重点看 Health 如何复制，以及 Damage 如何变成 Health 改变。*

### ***5. 武器开火***

```text
Source/LyraGame/Weapons/LyraGameplayAbility_RangedWeapon.h
Source/LyraGame/Weapons/LyraGameplayAbility_RangedWeapon.cpp
Source/LyraGame/Weapons/LyraWeaponStateComponent.h
Source/LyraGame/Weapons/LyraWeaponStateComponent.cpp
```

*重点看 TargetData 如何从客户端发到服务器，以及服务器如何确认命中。*

### ***6. 背包和装备***

```text
Source/LyraGame/Inventory/LyraInventoryManagerComponent.h
Source/LyraGame/Inventory/LyraInventoryManagerComponent.cpp
Source/LyraGame/Inventory/LyraInventoryItemInstance.h
Source/LyraGame/Inventory/LyraInventoryItemInstance.cpp
Source/LyraGame/Equipment/LyraEquipmentManagerComponent.h
Source/LyraGame/Equipment/LyraEquipmentManagerComponent.cpp
Source/LyraGame/Equipment/LyraEquipmentInstance.h
Source/LyraGame/Equipment/LyraEquipmentInstance.cpp
Source/LyraGame/Equipment/LyraQuickBarComponent.h
Source/LyraGame/Equipment/LyraQuickBarComponent.cpp
```

*重点看 FastArray 和 replicated subobject。*

### ***7. ReplicationGraph***

```text
Source/LyraGame/System/LyraReplicationGraph.h
Source/LyraGame/System/LyraReplicationGraph.cpp
Config/DefaultGame.ini
```

*重点看它如何路由 Actor，以及为什么当前默认关闭。*

---

## ***几个容易误解的点***

### ***1. GameplayMessage 不是网络复制***

*GameplayMessage 是本地消息分发。它可以被网络 RPC 触发，但它自己不是网络层。*

### ***2. OnRep 不应该做权威逻辑***

*OnRep 是客户端收到状态后的响应，适合刷新 UI、播放表现、触发本地事件。真正权威修改应该在服务器。*

### ***3. Client 预测不等于 Client 权威***

*Lyra 开火时客户端先 trace，是为了手感；最终是否命中、是否扣血，仍然由服务器决定。*

### ***4. UObject 可以复制，但需要挂在可复制 Actor / Component 下***

*ItemInstance、EquipmentInstance 不是 Actor，但可以通过 replicated subobject 复制。前提是它们挂在 InventoryManager / EquipmentManager 这类可复制组件下面。*

### ***5. FastArray 不是万能数组***

*FastArray 适合动态列表增删改，比如背包、装备、外观。每帧变化的数据，比如移动，就不适合用 FastArray。*

---

## ***最后总结***

*如果用一句话总结 Lyra 的网络同步：*

> ***Lyra 用 PlayerState 保存玩家长期权威状态，用 Pawn 表达当前身体和移动，用 GAS 管能力和属性，用 FastArray 管动态列表，用 RPC 处理请求和确认，用 GameplayMessage 解耦本地表现，用 RepGraph 预留大规模复制优化。***

*再压缩一下，就是：*

```text
PlayerState：玩家长期状态
Pawn：当前表现和移动
ASC/GAS：能力、效果、属性、预测
FastArray：背包、装备、外观列表
RPC：请求、确认、广播
OnRep：客户端收到状态后的表现刷新
RepGraph：大规模复制优化
```

*只要能顺着这几条线去读源码，Lyra 的网络同步就不会是一堆散乱的宏，而是一套比较清楚的状态归属和数据流设计。*

