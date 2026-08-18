# Lyra - CommonUI


## ***CommonUI 文档***

*https://dev.epicgames.com/documentation/zh-cn/unreal-engine/overview-of-advanced-multiplatform-user-interfaces-with-common-ui-for-unreal-engine*

## ***CommonUI的作用***

*Common UI 被设计来解决上述的难题。有了Common UI，你便可以：*

- *选择性的实现控件的可互动性，使创建丰富的多层UI变得更加轻松。*
- *主机和PC端的多平台支持。*
- *在主机端：*
  - *管理方向选择选项，包括选择轨迹和选择历史。*
  - *管理不同主机上的UI元素*

## ***CommonUI的使用场景***

*虽然CommonUI有许多有益功能，但它使用两个主要用例构建而成：*

- *复杂的多层界面。*
- *跨平台支持。*

*如果你预期你的项目不会使用其中任一用例，可能没有必要使用CommonUI。 除了这些用例之外，我们推荐你考虑你使用CommonUI是为了获得哪些好处，以及这些好处是否值得花时间精力学习UI创建和交互的新范例。*

*例如，设计仅限PC的实时策略（RTS）游戏时，CommonUI可能不适用，因为此类型的游戏通常使用不需要复杂基本浏览的单层UI，因此使用CommonUI的输入路由或绑定操作系统不大可能获益。*

*此外，不推荐将CommonUI用于使用WidgetComponents放置的控件。由于CommonUI依赖光标焦点浏览、激活顺序和绘制顺序/层ID，CommonUI仍可以处理你的2D游戏HUD，但不适用于游戏世界中放置的控件。*

*如果你有现有UI，可以考虑迁移到CommonUI。做出此决定时，考虑你的UI是否接近完成，以及未来是否有可以从使用CommonUI中获益的UI开发计划。*

*此外，考虑是否可以使用CommonUI创建新控件，同时与旧UI交互，或者是否需要刷新整个UI以迁移到CommonUI。*

## ***什么是 UCommonActivatableWidget***

*UCommonActivatableWidget 是 CommonUI 插件中最核心的基类。它继承自 UUserWidget，但扩展了极其强大的生命周期管理和输入响应机制。*

- ***原生 UMG 问题**：只有 Construct（创建）和 Destruct（销毁），难以处理“界面存在但被遮挡”或“界面存在但失去焦点”的情况。*
- ***CommonUI 方案**：引入了 **激活 (Activate)** 和 **反激活 (Deactivate)** 的概念，专门用于处理 UI 栈中的层级切换。*

## ***生命周期重要函数***

### *NativePreConstruct*

- *触发时机：在编辑器内预览或游戏运行 UI刚创建时。*
- *作用：设置一些在设计时就能看到的样式、颜色、默认文本。*

### *NativeConstruct*

- *触发时机：当 Widget 被添加到 Viewport 或者添加到某个 Stack 容器时触发。*
- *注意点：*
  - *在 CommonUI 中，Construct 触发并不代表玩家现在能操作它。*
  - *它可能只是被“加载”到了 UI 栈的底层，处于不可见或不可点击状态。*
  - *建议：只在这里做一次性的资源加载、事件绑定（Bind Event to...）。*

### *NativeOnActivated*

- ***触发时机**：当 bIsActivated 变为 true 时。*
- ***核心作用**：*
  1. ***捕获焦点**：系统会根据 GetDesiredFocusTarget 自动把手柄/键盘焦点移到此 UI。*
  2. ***配置输入模式**：系统会读取 Input Config，决定是否禁用玩家的移动、是否显示鼠标。*
  3. ***动画触发**：在这里播放“入场动画” (Fade In)。*
  4. ***注册返回键**：只有在激活后，该 UI 的返回逻辑（Back Action）才生效。*

### ***NativeOnDeactivated/OnDeactivated***

- ***触发时机**：当 bIsActivated 变为 false 时。*
- ***核心作用**：*
  1. ***释放焦点**：把焦点还给上一个 UI 或游戏世界。*
  2. ***清理状态**：停止循环音效、清空临时的输入缓存。*
  3. ***出场动画**：在这里播放“消失动画”。*
- ***关键理解**：反激活后的 UI 如果没有从父级移除，它依然渲染在屏幕上，只是玩家无法通过手柄/键盘与之交互。*

| *状态/行为*        | *Construct (构造)*  | *Activate (激活)*      | *Deactivate (反激活)* | *Destruct (销毁)* |
| ------------------ | ------------------- | ---------------------- | --------------------- | ----------------- |
| ***内存占用***     | *是*                | *是*                   | *是*                  | *否*              |
| ***视觉渲染***     | *取决于 Visibility* | *取决于 Visibility*    | *取决于 Visibility*   | *否*              |
| ***手柄焦点***     | *不一定获得*        | ***自动获得***         | ***自动丢失***        | *否*              |
| ***响应返回键***   | *否*                | ***是***               | *否*                  | *否*              |
| ***处理玩家输入*** | *否*                | ***是 (根据 Config)*** | *否*                  | *否*              |

## ***Lyra里的UI***

### ***第一切入点：CommonGame插件***

1. *UCommonLocalPlayer：该类继承于 ULocalPlayer，抛出了三个事件，同时提供了一个主要游戏布局查询，也就是当前游戏主要Widget*

   ```cpp
   	/** Called when the local player is assigned a player controller */
   	DECLARE_MULTICAST_DELEGATE_TwoParams(FPlayerControllerSetDelegate, UCommonLocalPlayer* LocalPlayer, APlayerController* PlayerController);
   	FPlayerControllerSetDelegate OnPlayerControllerSet;
   
   	/** Called when the local player is assigned a player state */
   	DECLARE_MULTICAST_DELEGATE_TwoParams(FPlayerStateSetDelegate, UCommonLocalPlayer* LocalPlayer, APlayerState* PlayerState);
   	FPlayerStateSetDelegate OnPlayerStateSet;
   
   	/** Called when the local player is assigned a player pawn */
   	DECLARE_MULTICAST_DELEGATE_TwoParams(FPlayerPawnSetDelegate, UCommonLocalPlayer* LocalPlayer, APawn* Pawn);
   	FPlayerPawnSetDelegate OnPlayerPawnSet;
   ```

2. *ACommonPlayerController：该类最终继承于 APlayerController*

   *他重写`virtual void ReceivedPlayer()`，这个函数的作用就是成功将 LocalPlayer 跟 PlayerController 成功匹配。也就是在这个函数他将触发 `OnPlayerControllerSet` 事件*

3. *UCommonGameInstance：该类继承于GameInstance*

   *它重写了`virtual int32 AddLocalPlayer(ULocalPlayer* NewPlayer, FPlatformUserId UserId) override;`，此函数是在添加本地玩家的时候调用。*

   *这个函数中Get到了Lyra自己写的一个`UGameUIManagerSubsystem`，并且调用了这个Subsystem的`NotifyPlayerAdded`函数*

4. *UGameUIManagerSubsystem：该类继承于 UGameInstanceSubsystem*

   *顾名思义，他是游戏UI管理子系统，其核心主要是管理和存放 UGameUIPolicy*

5. *UGameUIPolicy：该类就继承于一个 UObject，单纯的数据类。*

   *这个类就是游戏UI策略，比如多个本地玩家一块玩，就需要分屏，以及每一个LocalPlayer的UI信息 等等*

   *如果 OnPlayerControllerSet 事件触发。那么他就会给这个本地玩家去分配UI*

   *它里面就存放着一个 UPrimaryGameLayout，直接创建这个UI*

6. *UPrimaryGameLayout：游戏的主要游戏 UI 布局，这个小部件类代表如何布局、推送和显示所有图层单人游戏的用户界面。分屏游戏中的每个玩家都会收到自己的主要游戏布局。*

   *内部通过 TMap<FGameplayTag, UCommonActivatableWidgetContainerBase\*> 维护了多个图层。*
   
   * *HUD: 游戏内的玩家信息 比如血条 小地图 不需要跟玩家交互*
   * *GameMenu：跟游戏玩法相关的UI，比如 Shop*
   * *Menu：主菜单或者设置菜单之类的*
   * *Model：模态化UI，比如二次确认弹窗*
   
7. *UCommonUIExtensions：UI的BlueprintFunctionLibrary，提供一些给蓝图或者c++使用的接口，比如呼出 UI ， 查看用户当然输入设备是不是手柄*

### ***第二切入点：大厅 Frontend***

1. *大厅 Frontend 地图是 `/Script/Engine.World'/Game/System/FrontEnd/Maps/L_LyraFrontEnd.L_LyraFrontEnd'`，在这张地图的 World Setting中的 Default Gameplay Experience指定的就是大厅的游戏体验资产。*

   *打开此资产，随后会发现有UGameFeatureAction 是AddComponents，这个AddComponent会将LyraGameState中加入 B_LyraFrontendStateComponent。这个组件就包含着进入大厅的逻辑*

2. *ULyraFrontendStateComponent：该类继承了 UGameStateComponent，并且还继承了 ILoadingPRocessInterface 的接口，这说明了该组件有显示进度的功能*

   *`ULyraFrontendStateComponent::OnExperienceLoaded`：该函数会起FControlFlow（记得在项目中启动这个插件），创建了4个流程：*

   1. *`FlowStep_WaitForUserInitialization`： 清除用户的所有房间连接，并且判断用户是否是断线重连，如果是则重置用户状态。*
   2. *`FlowStep_TryShowPressStartScreen`：判断是否需要添加开始加载UI*
   3. *`FlowStep_TryJoinRequestedSession`：判断是否是因为好友邀请而进来的，是的话则不需要到主菜单大厅，直接加入游戏即可*
   4. *`FlowStep_TryShowMainScreen`：显示主菜单*

*就这样主菜单就可以显示出来了，主菜单和开始加载页面都是通过第一切入点写的UPrimaryGameLayout中的方法来直接加入显示的*

### ***第三切入点：ULyraActivatableWidget***

*该类继承于 `UCommonActivatableWidget`，其实就是做了一个InputMode的功能，再打开GameMenu之类的，我们就不用去手动的SetInputMode，在关闭的时候也不用手动的设置，这些就都可以自动的去帮我们做了*


