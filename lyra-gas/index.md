# Lyra-GAS


## ***ULyraHeroComponent***

### ***作用***

1. *让 Pawn 有能力接收输入、处理视角、连接 GAS 输入。*
2. *为玩家控制的 Pawn 提供输入初始化功能。*
3. *管理 camera mode（视角模式），支持 Gameplay Ability 控制相机。*
4. *与 GAS 输入系统结合，负责把输入（比如按下 Q）映射到能力（如施放火球）。*
5. *依赖 `ULyraPawnExtensionComponent` 的初始化状态。*

### ***接口与方法***

#### ***InitializePlayerInput(UInputComponent* PlayerInputComponent)***

* *真正把 Enhanced Input 系统的按键绑定到函数或 GAS 的入口。*

#### ***Input_AbilityInputTagPressed(FGameplayTag InputTag)***

* *当玩家按下某个技能键（比如“技能1”）时被调用。*
* *会调用 AbilitySystemComponent 的对应函数，比如：`AbilitySystem->AbilityLocalInputPressed(InputTag)`。实际作用是：激活绑定了该 InputTag 的 Ability。*

#### ***AddAdditionalInputConfig/RemoveAdditionalInputConfig***

* *允许动态添加/移除输入配置（如切换战斗/驾驶模式时加载不同操作逻辑）。*

#### ***SetAbilityCameraMode / ClearAbilityCameraMode***

* *GAS 中的 Ability 可以覆盖默认相机模式，比如瞄准模式/特殊视角。*
* *设置 `AbilityCameraMode` 和记录触发这个模式的 `AbilitySpecHandle`。*

#### ***IsReadyToBindInputs()***

* *检查当前 HeroComponent 是否已经准备好进行输入绑定（比如 PlayerController 已就绪）。*
* *`bReadyToBindInputs` 是这个状态的缓存。*

#### ***初始化接口 `IGameFrameworkInitStateInterface`***

* *Lyra 使用了一套通用初始化流程，组件通过实现这个接口来参与初始化状态机（如 Pawn 的各个初始化阶段）*

### ***成员变量说明***

* *DefaultInputMappings：默认的输入映射表（使用 Enhanced Input）*
* *AbilityCameraMode：当前由 Ability 设置的相机类*
* *AbilityCameraModeOwningSpecHandle：设置相机的 Ability 的句柄*
* *bReadyToBindInput：标记是否已完成输入绑定（通常在角色初始化完成后设为 true）*

## ***ULyraPawnExtensionComponent***

### ***作用***

1. *`ULyraPawnExtensionComponent` 继承自 `UPawnComponent`（Pawn 的组件基类），并实现了`IGameFrameworkInitStateInterface`，说明它参与游戏框架的初始化状态管理流程。*
2. *它为 Pawn 提供统一的扩展支持，主要包含：*
   - *Ability System Component (ASC) 的初始化与解绑*
   - *角色数据（PawnData）的管理与同步*
   - *输入绑定相关的辅助*
   - *监听并响应 Pawn 相关的状态变化（控制器变化、玩家状态同步等）*

*它是挂载在 Pawn（角色、载具等）上的一个扩展组件，负责协调角色的能力系统（GAS）及其他初始化状态的管理。整体职责是让所有 Pawn 类统一支持 Lyra 项目的功能扩展，尤其是与 **Ability System Component (ASC)** 相关的管理和初始化。*

### ***关键成员变量***

1. *`TObjectPtr<const ULyraPawnData> PawnData`：角色数据，通常是配置角色属性、模型、能力等的 DataAsset，带网络同步（`ReplicatedUsing`）*
2. *`TObjectPtr<ULyraAbilitySystemComponent>` ：AbilitySystemComponent 缓存指向角色的 ASC，方便快速访问（非持久化，Transient）*
3. *`FSimpleMulticastDelegate OnAbilitySystemInitialized`：角色成为 ASC Avatar 时触发的事件广播*
4. *`FSimpleMulticastDelegate OnAbilitySystemUninitialized`：角色解绑 ASC Avatar 时触发的事件广播*

### ***接口与方法***

#### ***接口实现：`IGameFrameworkInitStateInterface`***

- *`GetFeatureName()`：返回组件功能名称，用于框架的功能管理。*
- *`CanChangeInitState()`：判断是否允许状态从当前转变到目标状态。*
- *`HandleChangeInitState()`：执行状态切换时的逻辑。*
- *`OnActorInitStateChanged()`：当 Pawn 初始化状态改变时的响应。*
- *`CheckDefaultInitialization()`：检查默认初始化状态。*

#### ***Ability System 相关方法***

- `InitializeAbilitySystem(ULyraAbilitySystemComponent* InASC, AActor* InOwnerActor)`
  ***初始化能力系统，绑定 ASC 和 Owner 角色，建立能力系统与 Pawn 的关联。***
- `UninitializeAbilitySystem()`
   ***解绑 ASC，清理关联，通常在角色销毁或失去控制时调用。***
- `GetLyraAbilitySystemComponent()`
  *** 返回当前绑定的 ASC。***

#### ***PawnData 相关方法***

- `GetPawnData()`

  *模板方法，获取具体类型的 PawnData，方便访问角色配置。*

- `SetPawnData(const ULyraPawnData* InPawnData)`
   *设置 Pawn 的数据 Asset，通常在角色生成或更换数据时调用。*

- `OnRep_PawnData()`
   *网络同步回调，PawnData 变化时触发。*

***角色状态变化回调***

- `HandleControllerChanged()`

  *Pawn 的控制器变更时调用，可能需要重新绑定输入或权限。*

- `HandlePlayerStateReplicated()`
  *PlayerState 网络同步完成后调用，通常用于初始化角色状态。*

- `SetupPlayerInputComponent()`
   *绑定玩家输入，映射按键与 Ability 系统交互。*

#### ***事件注册***

- `OnAbilitySystemInitialized_RegisterAndCall()`
   *注册并立即调用 AbilitySystem 初始化事件委托。*
- `OnAbilitySystemUninitialized_Register()`
   *注册 AbilitySystem 解绑事件委托*。

###  ***静态辅助***

- `FindPawnExtensionComponent(const AActor* Actor)`
   *静态方法，方便查找某个 Actor 上的 `ULyraPawnExtensionComponent`。*

### ***IGameFrameworkInitStateInterface***

*此Component继承了这个类，`IGameFrameworkInitStateInterface` 是一个 **初始化状态管理接口**，主要用于 **控制游戏框架（Game Framework）中各个子系统或模块的初始化顺序和状态依赖***

#### ***`GetFeatureName()`***

1. *作用：*
   * ***`virtual FName GetFeatureName() const`**： 返回当前模块或组件的 **唯一标识名称**（用于区分不同的初始化模块）。* 

#### ***`CanChangeInitState()`***

1. *作用：*

   * *检查当前模块 **是否允许从 `CurrentState` 切换到 `DesiredState`**（状态变更的条件验证）。*

1. *参数：*

   * Manager` 管理初始化状态的 `UGameFrameworkComponentManager`*

   * *`CurrentState`：当前状态（如 `"PreInit"`）*

   * *`DesiredState`：目标状态（如 `"Initialized"`）*

2. *返回值*

   - `true`：允许切换。

   - `false`：阻止切换（需满足依赖条件后再尝试）。

3. *示例：*

   ```cpp
   virtual bool CanChangeInitState(UGameFrameworkComponentManager* Manager, FGameplayTag CurrentState, FGameplayTag DesiredState) const override {
       // 只有渲染系统初始化完成后，音频系统才允许初始化
       if (DesiredState == TAG_InitState_Initialized) {
           return Manager->HasFeatureReachedInitState(TEXT("RenderSystem"), TAG_InitState_Initialized);
       }
       return true;
   }
   ```

4. *调用时机：*
   * *在状态变更前由 `UGameFrameworkComponentManager` 调用*

#### ***`HandleChangeInitState()`***

1. *作用：*
   * *执行状态变更的 **实际逻辑**（如加载资源、注册回调等）。*

2. *参数*
   * *同 `CanChangeInitState`。*

3. *示例：*

   ```cpp
   virtual void HandleChangeInitState(UGameFrameworkComponentManager* Manager, FGameplayTag CurrentState, FGameplayTag DesiredState) override {
       if (DesiredState == TAG_InitState_Initialized) {
           LoadSoundBanks(); // 初始化音频资源
       } else if (DesiredState == TAG_InitState_Uninitialized) {
           ReleaseSoundBanks(); // 清理资源
       }
   }
   ```

4. *调用时机*
   * *当 `CanChangeInitState` 返回 `true` 后，由管理器触发。*

#### ***`OnActorInitStateChanged()`***

1. *作用：*
   * *监听 **Actor 的初始化状态变化**，用于处理与 Actor 相关的依赖逻辑。*

2. *参数：*
   - *`Params`：包含变动的 Actor 和状态信息（`FActorInitStateChangedParams`）。*

3. *示例：*

   ```cpp
   virtual void OnActorInitStateChanged(const FActorInitStateChangedParams& Params) override {
       if (Params.FeatureName == TEXT("PlayerController") && Params.NewState == TAG_InitState_Initialized) {
           BindInputEvents(Params.Actor); // 当玩家控制器初始化后绑定输入
       }
   }
   ```

4. *调用时机*
   - *当某个 Actor 的初始化状态变化时（如从 `Spawned` 变为 `Possessed`）。*

#### ***`CheckDefaultInitialization()`***

1. *作用*
   * ***强制检查**当前模块的默认初始化状态，通常用于修复未正确初始化的模块。*

2. *示例*

   ```cpp
   virtual void CheckDefaultInitialization() override {
       if (GetInitState() == TAG_InitState_None) {
           Manager->SetInitState(GetFeatureName(), TAG_InitState_PreInit); // 重新触发初始化
       }
   }
   ```

3. *调用时机*
   - *引擎启动时或手动调用 `UGameFrameworkComponentManager::CheckDefaultInitialization`。*

#### ***`static const FName NAME_ActorFeatureName`***

1. *作用*
   * *静态常量，定义模块的 **默认名称**（避免硬编码字符串）。*

### *总结*

*`ULyraPawnExtensionComponent` 是 Lyra 框架里连接 Pawn 和 Ability System 的桥梁组件，承担以下关键职责：*

- ***管理 Ability System Component 的绑定与解绑**，确保 Pawn 能正常作为 GAS 的 Avatar。*
- ***维护 Pawn 数据（PawnData）**，提供角色配置数据的存取与网络同步。*
- ***支持游戏框架的初始化状态管理**，与游戏框架的生命周期钩子相结合。*
- ***对控制器变更、玩家状态同步、输入绑定等事件提供接口**，保证角色状态与能力系统正确同步。*
- ***提供事件广播机制**，通知外部 Ability System 初始化/解绑的状态变化。*

*这个组件让 Lyra 里的角色能够在不同模块间灵活协作，特别是在 GAS 能力系统的动态加载、初始化和解绑中起到核心作用。*

