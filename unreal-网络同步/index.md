# Unreal - 网络同步


## ***网络同步基础***

*在理解 Unreal 网络同步之前，先要明确一个核心问题：网络同步并不是“给变量加一个 Replicated 就完事了”。真正重要的是要先判断数据的归属、修改权限、同步对象和客户端收到后的表现逻辑。*

*网络同步最核心的问题是：*

* *谁拥有权威？*
* *谁发起请求？*
* *谁需要收到结果？*
* *收到以后是保存状态，还是播放表现？*

*只要这几个问题想清楚，大部分同步逻辑就不会乱。*

---

## ***Server Authority***

*Unreal 默认是服务器权威模型，也就是：真正可信的游戏结果由 Server 决定。*

*举个例子，玩家开枪时：*

```text
Client：我按下鼠标左键，我感觉我打中了
Server：我来判断你到底有没有打中
Client：收到结果后刷新 UI / 播放反馈
```

*所以 Client 可以做预测，可以先播放表现，但是最终的真实状态应该由 Server 修改。*

*常见例子：*

| *数据* | *应该谁决定* |
| --- | --- |
| *是否造成伤害* | *Server* |
| *目标是否死亡* | *Server* |
| *背包是否增加物品* | *Server* |
| *当前装备是哪把武器* | *Server* |
| *本地准星扩散表现* | *Client 可以预测* |
| *开火音效 / 镜头抖动* | *Client 可以先播* |

*所以写网络逻辑时，第一反应应该是：这个东西是不是会影响游戏结果？如果会，就应该让服务器决定。*

---

## ***Role 和 Authority***

*Unreal 里经常会看到这些判断：*

```cpp
HasAuthority()
GetLocalRole()
IsLocallyControlled()
```

*它们用来判断当前这份对象实例，在当前机器上是什么身份。*

### ***Authority***

*`HasAuthority()` 表示这个 Actor 当前是否由服务器权威控制。*

```cpp
if (HasAuthority())
{
    // 只有服务器执行
}
```

*大多数修改真实游戏状态的逻辑，都应该放在服务器执行。*

### ***Locally Controlled***

*`IsLocallyControlled()` 常用于 Pawn / Character，表示这个 Pawn 是否由本机玩家控制。*

```cpp
if (IsLocallyControlled())
{
    // 本地玩家自己的输入、摄像机、UI、预测表现
}
```

*比如第一人称相机、准星、输入处理，通常只应该在本地控制的 Pawn 上执行。*

### ***Simulated Proxy***

*如果一个角色是其他玩家的角色，那么在你客户端上它通常是 Simulated Proxy。*

```text
你的客户端：
  你的角色 = Autonomous Proxy / Locally Controlled
  别人的角色 = Simulated Proxy
```

*Simulated Proxy 不负责输入，只接收服务器同步过来的移动和状态，然后播放表现。*

---

## ***Ownership（所有权）***

*Ownership 决定一个 Actor 属于哪个连接。这个概念非常重要，因为它会影响 RPC 能不能发、属性复制给谁。*

*比如 PlayerController 是某个客户端拥有的，PlayerState 通常也跟这个玩家连接相关。*

*常见规则：*

```text
Client 想调用 Server RPC
  -> 这个 RPC 所在 Actor / Component 通常需要被该 Client 拥有

Server 想调用 Client RPC
  -> 目标 Actor 通常也需要有明确 Owner
```

*所以如果你在一个不属于该客户端的 Actor 上调用 Server RPC，很可能根本发不到服务器。*

***如何设置和检查 Ownership？***

- *检查拥有权：*

  *在代码中，你可以调用 `GetOwner()` 或者 `IsLocallyControlled()`*

- *设置拥有权：*

  *在服务器上调用 `Actor->SetOwner(NewOwner)`。*

  *注意： 只有服务器有权更改所有权。*

***常见的坑***

*情景*

* *你写了一个“宝箱” Actor。玩家走过去按 E，宝箱里调用了一个 ServerOpenChest() 的 Server RPC。*

*结果：*

* *没反应，服务器日志显示丢弃了 RPC。*

*原因：*

* *因为宝箱不属于玩家。宝箱是服务器放在关卡里的，它的 Owner 通常是空（即服务器）。玩家没有所有权，就不能直接在宝箱上发 Server RPC。*

*解决方法：*

* *玩家必须在自己的 **PlayerController** 或者 **Pawn**（这两个是你肯定拥有的）里面发起 Server RPC，告诉服务器：*“我想打开那个宝箱，它的 ID 是 XXX。”* 然后由服务器去执行打开宝箱的逻辑。*

*这也是为什么很多输入请求、装备栏请求、玩家操作，会放在 Controller、PlayerState、Pawn 或它们的组件上。因为这些对象跟玩家连接关系明确。*

---

## ***属性复制 Replicated***

*属性复制适合同步“状态”。*

*比如：*

```cpp
UPROPERTY(Replicated)
int32 Ammo;
```

*或者：*

```cpp
UPROPERTY(ReplicatedUsing = OnRep_Health)
float Health;
```

*服务器修改属性后，Unreal 会在合适的网络帧把变化复制到客户端。*

```text
Server 修改 Health
  -> 网络复制
  -> Client 收到新的 Health
  -> 如果有 OnRep，触发 OnRep_Health()
```

*注意：属性复制一般是 Server 到 Client。Client 本地改一个 Replicated 变量，不会自动同步到 Server。*

*所以不要以为：*

```cpp
Health -= 10;
```

*只要变量有 `Replicated`，Client 改了就能通知服务器。不是这样的。真实状态应该由服务器改。*

***一个非常重要的“坑”（Server 端的区别）***

*如果你希望服务器（作为监听服务器/玩家）也执行这段逻辑，你必须手动调用它：*

```cpp
if (GetLocalRole() == ROLE_Authority)
{
    OnRep_Health(100.0f); // 手动调用，因为服务器不会自动触发 RepNotify
}
```

---

## ***OnRep 是什么***

*`OnRep` 是客户端收到复制属性变化后调用的函数。*

```cpp
UPROPERTY(ReplicatedUsing = OnRep_Health)
float Health;

UFUNCTION()
void OnRep_Health();
```

*它适合做客户端表现刷新，比如：*

```text
刷新血条
播放受击表现
更新 UI
触发本地事件
```

*但 OnRep 不适合做服务器权威逻辑。*

*可以这样理解：*

```text
Server 修改状态
Replication 发送状态
Client OnRep 响应状态变化
```

*例如血量同步：*

```text
Server:
  Health = 50

Client:
  收到 Health = 50
  OnRep_Health()
  刷新血条
```

---

## ***RPC 是什么***

*RPC 适合同步“行为”或者“请求”。*

*Unreal 常见 RPC 有三种：*

```cpp
UFUNCTION(Server, Reliable)
void ServerDoSomething();

UFUNCTION(Client, Reliable)
void ClientDoSomething();

UFUNCTION(NetMulticast, Unreliable)
void MulticastDoSomething();
```

### ***Server RPC***

*Client 请求 Server 做事。*

```cpp
UFUNCTION(Server, Reliable)
void SetActiveSlotIndex(int32 NewIndex);
```

*比如玩家按键切换武器：*

```text
Client 按下切枪键
  -> Server RPC 请求切换
  -> Server 检查是否合法
  -> Server 修改 ActiveSlotIndex
  -> ActiveSlotIndex 复制给客户端
```

*这里 RPC 只是“请求”，真正状态还是服务器修改，再通过属性复制同步结果。*

### ***Client RPC***

*Server 通知某个客户端。*

```cpp
UFUNCTION(Client, Reliable)
void ClientConfirmTargetData(...);
```

*比如服务器只需要告诉某一个玩家：这次操作是否成功。这个时候用 Client RPC 很合适。*

### ***NetMulticast RPC***

*Server 广播给多个客户端。*

```cpp
UFUNCTION(NetMulticast, Unreliable)
void MulticastPlayEffect();
```

*适合一次性表现，比如爆炸、全局提示、某些特效。*

*不过 Multicast 不适合滥用。能用属性复制、局部预测或者表现系统解决的，就不一定要全场广播 RPC。*

---

## ***Reliable 和 Unreliable***

*RPC 可以分为 Reliable 和 Unreliable。*

### ***Reliable***

*Reliable 表示必须送到。*

*适合：*

```text
购买成功
切换装备
任务完成
关键 UI 通知
```

*但是 Reliable 不能滥用，因为如果大量 Reliable 堵住，会影响后续网络包。*

### ***Unreliable***

*Unreliable 表示可以丢。*

*适合：*

```text
枪口火焰
脚步声
普通命中特效
频繁移动表现
不重要的临时反馈
```

*简单判断：如果这个事件丢了一次，游戏状态不会错，就可以考虑 Unreliable。*

---

## ***属性复制和 RPC 怎么选***

*可以用这个规则判断：*

| *需求* | *更适合* |
| --- | --- |
| *同步一个持续存在的状态* | *Replicated Property* |
| *状态变化后客户端要刷新表现* | *ReplicatedUsing / OnRep* |
| *Client 请求 Server 做事* | *Server RPC* |
| *Server 单独通知某个 Client* | *Client RPC* |
| *Server 广播一次表现事件* | *NetMulticast* |
| *大量列表增删改* | *FastArray* |
| *技能、Buff、属性、预测* | *GAS* |
| *大量 Actor 相关性优化* | *ReplicationGraph* |

*举几个例子：*

```text
血量是多少？
  -> Replicated Attribute / OnRep

我要换枪
  -> Server RPC 请求
  -> Server 修改当前装备
  -> 属性复制结果

爆炸特效
  -> 表现系统或 NetMulticast

背包新增一个物品
  -> Server 修改 Inventory
  -> FastArray 同步差异

玩家开火
  -> 本地预测表现
  -> TargetData 发给 Server
  -> Server 校验并应用伤害
```

---

## ***Replication Condition***

*不是所有属性都要发给所有客户端。Unreal 提供了复制条件。*

*常见条件有：*

```cpp
COND_None
COND_OwnerOnly
COND_SkipOwner
COND_SimulatedOnly
COND_AutonomousOnly
COND_InitialOnly
COND_ReplayOnly
```

*含义大概是：*

| *条件* | *含义* |
| --- | --- |
| `COND_None` | *发给所有相关客户端* |
| `COND_OwnerOnly` | *只发给 Owner* |
| `COND_SkipOwner` | *发给除了 Owner 以外的人* |
| `COND_SimulatedOnly` | *只发给模拟代理* |
| `COND_AutonomousOnly` | *只发给自主代理* |
| `COND_InitialOnly` | *只在初始复制时发一次* |
| `COND_ReplayOnly` | *只给 Replay* |

*比如本地玩家自己的视角旋转，自己本来就知道，不需要服务器再发回来，就可以 `SkipOwner`。*

*远端角色动画需要某些额外数据，本地玩家自己不需要，就可以 `SimulatedOnly`。*

---

## ***Relevancy 相关性***

*服务器不是把所有 Actor 都发给所有客户端。它会先判断某个 Actor 对某个连接是否相关。*

*比如一个很远的道具，客户端根本看不到，就没必要同步。*

*常见相关性参数：*

```cpp
bAlwaysRelevant
bOnlyRelevantToOwner
bNetUseOwnerRelevancy
NetCullDistanceSquared
IsNetRelevantFor()
```

*简单理解：*

```text
相关 -> 可以考虑复制
不相关 -> 不发
```

*所以如果你发现某个 Actor 在客户端不存在，除了看 `Replicated`，还要看它对这个客户端是否 relevant。*

---

## ***NetUpdateFrequency 和 ForceNetUpdate***

*Actor 不是每一帧都复制。`NetUpdateFrequency` 控制这个 Actor 每秒最多被考虑复制多少次。*

```cpp
SetNetUpdateFrequency(100.0f);
```

*频率越高，更新越及时，但带宽压力越大。*

*如果某个关键状态刚刚变化，希望尽快发出去，可以调用：*

```cpp
ForceNetUpdate();
```

*比如服务器刚刚修改了一个关键状态，就可以强制尽快进行一次网络更新。*

---

## ***Dormancy 休眠***

*有些 Actor 很少变化，比如门、箱子、机关、拾取物。它们没必要一直参与复制，可以进入 Dormancy。*

*常见状态：*

```cpp
DORM_Awake
DORM_DormantAll
```

*如果休眠后又发生变化，需要唤醒：*

```cpp
FlushNetDormancy();
ForceNetUpdate();
```

*简单理解：*

```text
平时不变 -> 休眠省带宽
发生变化 -> 唤醒并同步
```

---

## ***NetSerialize 和量化***

*网络同步不是所有数据都原样发送。很多数据会被压缩或量化。*

*比如位置、旋转、速度，如果全部用完整 float 发送，带宽会很大。Unreal 里常见这些类型：*

```cpp
FVector_NetQuantize
FVector_NetQuantize10
FVector_NetQuantize100
FRotator
FRepMovement
```

*这些类型会用更适合网络传输的方式序列化数据。*

*如果你自己定义结构体，也可以实现：*

```cpp
bool NetSerialize(FArchive& Ar, UPackageMap* Map, bool& bOutSuccess);
/*
    核心逻辑：全量同步。
    工作方式：每当这个结构体需要同步时，它会把所有的属性一股脑地写进二进制流发给客户端。
    特点：
        手动挡：你需要自己写 Ar << MyVar 这样的代码。
        无记忆：它不关心上次发了什么，每次都发当前完整的数据。
    适用场景：小型结构体（例如只有位置、旋转），或者需要极其精细的位压缩时。
*/
```

*这样可以控制这个结构体在网络中怎么被压缩、写入和读取。*

---

## ***移动同步和预测***

*角色移动是网络游戏里最特殊的一类同步。*

*如果所有移动都等服务器返回，玩家会感觉非常卡。所以 Unreal 的 CharacterMovement 内置了客户端预测和服务器校正。*

*大概流程是：*

```text
Client 输入移动
  -> 本地立刻移动，保证手感
  -> 把移动请求发给 Server
  -> Server 校验移动是否合法
  -> 如果 Client 预测错了，Server 发校正
  -> 其他 Client 接收这个角色的模拟移动
```

*所以移动同步不是简单的 `Replicated FVector Location`。不要自己手写一套位置复制去替代 CharacterMovement，除非你非常清楚自己在做什么。*

---

## ***FastArray***

*普通数组如果频繁增删改，直接复制会比较重。Unreal 提供了 `FFastArraySerializer`，适合动态列表同步。*

*常见结构是：*

```cpp
struct FMyEntry : public FFastArraySerializerItem
{
    ...
};

struct FMyList : public FFastArraySerializer
{
    TArray<FMyEntry> Entries;
};
```

*FastArray 的优势是：*

```text
只同步新增项
只同步删除项
只同步变化项
客户端能知道哪些项被 Add / Remove / Change
```

*适合：*

```text
背包物品
装备列表
角色外观部件
Buff 列表
任务列表
```

*不适合：*

```text
每帧移动
每帧旋转
高频临时表现
```

---

## ***SubObject Replication***

*Unreal 不只 Actor 能复制，ActorComponent 和 UObject 子对象也可以复制。*

*比如背包里的一个物品实例可能是 UObject。它不是 Actor，但只要挂在一个可复制 Actor / Component 下，并注册成 replicated subobject，也可以参与同步。*

*大概关系是：*

```text
PlayerState / Pawn
  -> Replicated Component
      -> Replicated SubObject
```

*这种方式适合把复杂数据拆成独立对象，而不是全部塞进一个 Actor 的属性里。*

---

## ***GAS 网络同步***

*GAS 自己已经内置了大量网络同步能力，包括：*

```text
Ability 激活
GameplayEffect 复制
GameplayTag 复制
Attribute 复制
PredictionKey
TargetData
GameplayCue
```

*所以如果项目使用 GAS，不要随便自己写 RPC 去同步技能状态和 Buff 状态。优先使用 GAS 自己的机制。*

*比如伤害流程通常应该是：*

```text
Server 应用 GameplayEffect
  -> Attribute 改变
  -> Attribute 复制
  -> Client OnRep / Attribute Change Delegate
  -> UI 和表现刷新
```

*而不是：*

```text
Client RPC: 你扣 10 点血
```

*后者很容易让状态分裂，后期也很难维护。*

---

## ***GameplayCue***

*这两个东西容易混淆。*

### ***GameplayCue***

*GameplayCue 属于 GAS，常用来表现 GameplayEffect 相关的特效、音效、镜头反馈。*

```text
GameplayEffect 触发
  -> GameplayCue 播放表现
```

*比如燃烧、治疗、命中特效，都可以走 GameplayCue。*

## ***ReplicationGraph***

*普通复制在玩家和 Actor 很多时，服务器需要频繁判断每个 Actor 对每个连接是否相关，这会比较重。*

*ReplicationGraph 的思路是提前把 Actor 分组：*

```text
空间格子节点
AlwaysRelevant 节点
OwnerOnly 节点
Team 节点
连接专属节点
```

*这样每个客户端只从相关节点里拿 Actor，减少遍历成本。*

*不过 ReplicationGraph 属于优化层。学习网络同步时，应该先理解属性复制、RPC、Ownership、GAS、FastArray，再看 ReplicationGraph。*

---

## ***基础总结***

*Unreal 网络同步可以先记住这张表：*

| *问题* | *常用机制* |
| --- | --- |
| *这个状态要同步给客户端* | `Replicated` |
| *客户端收到状态后要刷新表现* | `OnRep` |
| *客户端请求服务器操作* | `Server RPC` |
| *服务器通知单个客户端* | `Client RPC` |
| *服务器广播一次事件* | `NetMulticast` |
| *只想发给某些客户端* | `Replication Condition` |
| *Actor 太远不想发* | `Relevancy` |
| *Actor 很少变化* | `Dormancy` |
| *动态数组增删改* | `FastArray` |
| *UObject 也要同步* | `SubObject Replication` |
| *技能、Buff、属性、预测* | `GAS` |
| *大量 Actor 优化* | `ReplicationGraph` |

*最后用一句话总结：*

> ***状态用 Replicated，同步后的表现用 OnRep，请求用 Server RPC，确认用 Client RPC，一次性广播用 Multicast，大列表用 FastArray，技能属性用 GAS，大规模优化用 ReplicationGraph。***

*理解了这些基础，再去看具体项目的网络代码，就会发现它不是在乱用网络宏，而是在不同场景下选择不同的同步工具。*

