# Unreal - Gameplay框架


## ***Gameplay定义***

### ***广义***

*广义的Gameplay：需要改动引擎源码叫做引擎开发。而基于引擎开发游戏则成为Gameplay*

### ***狭义***

*其实Gameplay这个概念还是来自于Unreal，以Unreal提供的Gameplay框架作为参考，其实就是游戏的规则和状态，用户界面。(UE5 Gamelay框架概念更为宽阔了)*

*但是更具体来讲就是Unreal Gameplay框架的类*

## ***`UWorld`***

*它是所有物体（Actors）的容器。当你加载一个关卡（Level），你实际上是在加载一个 World。所有的物理模拟、渲染、声音都在这里发生*

## ***`UGameInstance`***

*唯一跨关卡存在的对象。也就是全局数据实例*

*当你从“主菜单”切换到“第一关”时，World 被销毁了，GameMode 重置了，但 GameInstance 依然存在。通常用来保存全局数据（如设置、存档数据、网络Session管理）。*

*他是存在于客户端 和 服务端，主导权肯定在服务端，然后同步到客户端，防止玩家作弊*

## ***`AGameMode`***

*制定游戏规则。比如：怎么算赢？玩家在哪里出生？能不能暂停？*

*GameMode 只存在于服务器端。客户端不知道规则，只能听服务器指挥。这是为了防止作弊。*

## ***`AGameState`***

*记录整个游戏当前的状态。比如：比赛剩余时间、红蓝队总比分、当前是第几波怪。*

*它由服务器更新，并同步给所有客户端。每个玩家都能看到 GameState。*

## ***`APlayerController`***

*它是玩家意志的体现。它接收键盘鼠标输入，并告诉游戏里的角色该做什么。它也是处理 UI（HUD）的地方。*

- *服务器有所有玩家的 Controller（为了验证操作）。*
- *客户端只有**自己**的 Controller（你不能控制别人的灵魂）。*

## ***`APlayerState`***

*记录属于个人但在全局重要的数据。比如：玩家名字、个人击杀数、Ping值、当前等级。*

*即便玩家的角色（Pawn）死掉了，Controller 还没重生，PlayerState 依然存在，数据不会丢。它会同步给所有客户端（这样你按Tab键才能看到队友的分数）。*

- *服务器有所有玩家的 APlayerState。*
- *客户端只有**自己**的 APlayerState。*

## ***`APawn`***

*可以被 Controller “附身”的 Actor。是最小可控制单位，比如载具，说白了就是Character的行走逻辑不合适的都用这个*

## ***`ACharacter`***

*Pawn 的特化子类。它自带了 CharacterMovementComponent（角色移动组件），帮你处理好了极其复杂的双足行走逻辑（走路、跳跃、游泳、网络平滑插值）*

## ***HUD / UI***

*AHUD类是 UI管理者*

## ***总结***

| *类名 (Class)*         | *存在位置*         | *作用总结*                                                   |
| ---------------------- | ------------------ | ------------------------------------------------------------ |
| ***GameInstance***     | *Client & Server*  | *跨关卡全局管理，如设置、存档。*                             |
| ***GameMode***         | *Server Only*      | *权威规则制定者，客户端不存在。*                             |
| ***GameState***        | *Server & Clients* | *比赛的当前状态（比分、时间），大家都能看。*                 |
| ***PlayerController*** | *Server & Client*  | *玩家的输入处理、UI逻辑。我在服务器有备份，但我看不到别人的。* |
| ***PlayerState***      | *Server & Clients* | *玩家的个人数据（KDA、名字），大家都能看。*                  |
| ***Pawn/Character***   | *Server & Clients* | *玩家在世界里的实体模型。*                                   |
| ***HUD / UI***         | *Client Only*      | *纯本地的UI显示。*                                           |

