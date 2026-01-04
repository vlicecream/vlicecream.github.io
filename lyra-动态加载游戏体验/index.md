# Lyra - Experience DataAsset 的动态加载机制


***举的例子为 大厅***

## ***什么是 Experience DataAsset 的动态加载机制***

*Lyra中是分很多游戏玩法的，他在进入一个新的玩法的时候，就会找到玩法对应的 ULyraExperienceDefinition 的数据，系统根据 ULyraExperienceDefinition 里的内容去激活**Game Features**、**Abilities** 和 **UI** 等。*

*这样的好处是啥，举个例子：*

* ***传统方式（硬编码/硬引用）：** 想象一下你走进一家自助餐厅。为了能让你随时吃到任何菜，厨房必须**提前把所有菜品都做好并摆出来**。这会导致：*
  1. ***启动慢：** 厨房开工时间长。*
  2. ***资源浪费：** 很多菜你根本不吃，但它们依然占着空间和保温的能源（内存）。*
* ***Lyra 的动态加载方式：** 现在想象你走进一家点餐的餐厅。你拿到一本**菜单 (Experience DataAsset)**。菜单上写着各种套餐的描述（比如“西部对决”套餐包含：牛仔Pawn、左轮手枪、沙漠地图UI）。*
  1. ***启动快：** 餐厅开门很快，因为厨房什么都还没做，只是准备好了菜单。*
  2. ***按需加载：** 当你点单时（选择了一个 Experience），服务员（ExperienceManagerComponent）才把订单（PrimaryAssetId）交给厨房（AssetManager）。厨房只根据你点的菜去准备，然后上菜。*
  3. ***高效节约：** 你没点的菜，就完全不占用厨房的任何资源。*

## ***动态加载代码流程***

1. `void ALyraGameMode::InitGame(const FString& MapName, const FString& Options, FString& ErrorMessage)`

2. `void ALyraGameMode::HandleMatchAssignmentIfNotExpectingOne()`

   *通过一系列的渠道，获取 ULyraExperienceDefinition， 此 ULyraExperienceDefinition 后面会讲解*

3. `void ALyraGameMode::OnMatchAssignmentGiven(FPrimaryAssetId ExperienceId, const FString& ExperienceIdSource)`

   *获取到 GameState 里的 ULyraExperienceManagerComponent，此Component后面会讲解*

4. `void ULyraExperienceManagerComponent::SetCurrentExperience(FPrimaryAssetId ExperienceId)`

   *通过 FPrimaryAssetId 进行加载 获取到里面的各种数据，并且保存*

5. `void ULyraExperienceManagerComponent::StartExperienceLoad()`

   *将 ULyraExperienceDefinition 其里面的 DataAsset 包括自己本身 都异步加载出来，加载成功了就调用回调事件 OnExperienceLoadComplete*

6. `void ULyraExperienceManagerComponent::OnExperienceLoadComplete()`

   *加载需要 ULyraExperienceDefinition 里面 GameFeature 的插件，如果加载完成则调用 OnExperienceFullLoadCompleted*

7. `void ULyraExperienceManagerComponent::OnExperienceFullLoadCompleted()`

   *需要的数据都已经加载完成，获得所有的GameFeatureAction, 调用其生命周期函数，开始进入GameFeatureAction的表演*

   *并发出 OnExperienceLoaded_HighPriority 等 3个优先级事件，通知接收事件的对象*

8. `void ALyraGameMode::OnExperienceLoaded(const ULyraExperienceDefinition* CurrentExperience)`

   *将世界的所有 Playcontroller 遍历一遍，如果满足 Restart条件，则 RestartPlayer(PC)*

9. `APawn* ALyraGameMode::SpawnDefaultPawnAtTransform_Implementation(AController* NewPlayer, const FTransform& SpawnTransform)`

   *如果这时候经过 上述函数都是正常的，`RestartPlayer(PC)` 之后会到这个函数，随后根据我们 ULyraExperienceDefinition 里面的Pawn类生成我们的玩家*

## ***加载流程牵扯到的类***

### ***ALyraGameMode***

#### ***设计理念***

* *标准AGameModeBase：*

  1. *通常是游戏规则的中心。*

  2. *硬编码了许多核心逻辑，比如玩家的 Pawn 类型 (DefaultPawnClass)、玩家控制器类型 (PlayerControllerClass)、游戏状态 (GameStateClass) 等。*

  3. *如果想换一个游戏模式（比如从“死亡竞赛”换到“夺旗”），你通常需要创建一个全新的 GameMode 子类，并在里面写新的逻辑。*

* *ALyraGameMode：*
  1. *它本身不包含具体游戏模式的逻辑。它不知道什么是“死亡竞赛”或“据点控制”。*
  2. *它的设计目标是保持通用性，能够适应任何通过 Experience 定义的游戏玩法。*
  3. *它将具体的游戏规则和玩家配置“外包”给了 LyraExperienceDefinition。*

#### ***核心职责***

*ALyraGameMode 依然承担着 AGameMode 的传统职责，即管理玩家的连接、生成和重生。*

*他只会读取 ULyraExperienceDefinition 并且通过GameState的 ULyraExperienceManagerComponent 去加载他，随后加载完了，再去生成玩家Pawn。*

***所以他的核心职责就是推迟玩家Pawn的生成，读取 ULyraExperienceDefinition***

### ***ALyraGameState***

#### ***设计理念***

* *标准 GameStateBase*

  1. *主要职责是同步那些需要被所有客户端知晓的游戏状态信息，例如比赛时间、分数、队伍信息等。*
  2. *它的数据通常是被动的，由 GameMode 在服务器上修改，然后自动复制（Replicate）到所有客户端。*
  3. *它本身很少主动执行复杂的逻辑。*

* *ALyraGameState*

  1. *继承了所有标准职责：它依然负责同步分数、比赛时间等传统状态。*

  2. *新增了核心职责：它成为了体验（Experience）和游戏功能插件（Game Features）的管理中心。这是通过它所持有的组件实现的。*

  3. *它的行为是主动的。它主动管理体验的加载、激活和状态切换，并向全场“广播”当前的状态。*

#### ***核心职责***

*将 ExperienceManagerComponent 放在GameState身上，当游戏需要切换到一个新的体验时，负责接收 Experience 的 PrimaryAssetId，通过 AssetManager 异步加载它，并在加载完成后激活它。并激活后，发出事件通知*

### ***ULyraExperienceDefinition***

#### ***结构成员***

1. `TArray<FString> GameFeaturesToEnable;`

   *定义了这个体验依赖哪些游戏功能插件 (Game Feature Plugins)。*

2. `TObjectPtr<const ULyraPawnData> DefaultPawnData;`

   *指定了在这个体验中，玩家默认应该被生成为什么样子的Pawn*

3. `TArray<TObjectPtr<UGameFeatureAction>> Actions;`

   *定义了一系列直接嵌入到这个Experience中的Game Feature Actions。*

4. `TArray<TObjectPtr<ULyraExperienceActionSet>> ActionSets;`

   *允许你引用一个或多个可复用的功能集合。*

### ***ULyraExperienceManagerComponent***

#### ***核心职责***

*ULyraExperienceDefinition 结构体 的 管理者*

## ***一些坑***

### ***GameModeBase /GameState混用***

*GameModeBase 与 GameState 不能混用，相反也是一样的道理*

*这是因为：*

1. ***网络多人游戏设计**：`AGameModeBase`是游戏模式的基类，它只在服务器上实例化，不会存在于客户端。这意味着`AGameModeBase`负责定义游戏的核心逻辑和规则，但它并不需要在客户端被复制或同步。*
2. ***GameState的复制**：`AGameState`（或`AGameStateBase`）用于存储和同步游戏中的全局状态信息，比如玩家的得分和游戏的当前状态。在网络多人游戏中，`AGameState`需要被复制到所有客户端以确保所有玩家都有相同的游戏信息。*
3. ***兼容性问题**：当你尝试将`AGameState`与`AGameModeBase`一起使用时，会出现兼容性问题，因为`AGameModeBase`不设计为与需要在客户端复制的`AGameState`一起工作。这种混合使用会导致运行时错误，因为`AGameModeBase`不会在客户端存在，而`AGameState`需要在客户端存在以同步状态。*


