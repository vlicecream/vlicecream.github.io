# Lyra - 输入系统


## ***前言***

*需要你先了解：*

1. *Lyra 架构解析：Experience DataAsset 的动态加载机制*

## ***Lyra 基础 Input 流程***

1. `void ULyraHeroComponent::HandleChangeInitState(UGameFrameworkComponentManager* Manager, FGameplayTag CurrentState, FGameplayTag DesiredState)`

   *经历过加载游戏体验的DataAsset后，就来到了这个函数*

2. `void ULyraHeroComponent::InitializePlayerInput(UInputComponent* PlayerInputComponent)`

   *这边就是初始化Input的一个重要函数，他会去拿到你的游戏体验中配置的InputConfig的信息，从而拿到 FInputMappingContextAndPriority 具体的信息*

   ***要注意，你需要建立一个 GameFeature 然后利用LyraDataAsset 将InputMappingContext 提前加载进内存，否则判断不会进去***

   *你还要注意将项目设置里面的 UEnhancedInputComponent 给替换成自己实现的类，随后就是绑定输入了*

   *蓝图中创建 InputAction 和 InputMappingContext 自己配置好其对应的游戏体验DataAsset 即可*
   
   ```cpp
   LyraIC->BindAbilityActions(InputConfig, this, &ThisClass::Input_AbilityInputTagPressed, &ThisClass::Input_AbilityInputTagReleased, /*out*/ BindHandles);
   
   /*
   	此函数是技能函数，比如鼠标左键枪开火
   	在 ULyraInputConfig 里配置 AbilityInputActions
   */
   ```
   
   ```cpp
   /*
   	此函数则是配置一些角色基本移动输入
   	在 ULyraInputConfig 里配置 NativeInputActions
   */
   LyraIC->BindNativeAction(InputConfig, LyraGameplayTags::InputTag_Move, ETriggerEvent::Triggered, this, &ThisClass::Input_Move, /*bLogIfNotFound=*/ false);
   LyraIC->BindNativeAction(InputConfig, LyraGameplayTags::InputTag_Look_Mouse, ETriggerEvent::Triggered, this, &ThisClass::Input_LookMouse, /*bLogIfNotFound=*/ false);
   LyraIC->BindNativeAction(InputConfig, LyraGameplayTags::InputTag_Look_Stick, ETriggerEvent::Triggered, this, &ThisClass::Input_LookStick, /*bLogIfNotFound=*/ false);
   LyraIC->BindNativeAction(InputConfig, LyraGameplayTags::InputTag_Crouch, ETriggerEvent::Triggered, this, &ThisClass::Input_Crouch, /*bLogIfNotFound=*/ false);
   LyraIC->BindNativeAction(InputConfig, LyraGameplayTags::InputTag_AutoRun, ETriggerEvent::Triggered, this, &ThisClass::Input_AutoRun, /*bLogIfNotFound=*/ false);
   ```

## ***要记住的点***

1. *需要在项目设置手动指定增强输入的setting类*

2. *Mouse上下移动的方向*

   ```ini
   [/Script/LyraGame.LyraPlayerController]
   InputYawScale=1.0
   InputPitchScale=1.0
   InputRollScale=1.0
   ForceFeedbackScale=1.0
   ```

   *Lyra 是靠这个 来使得我们鼠标向上滑就是人往天看，InputPitchScale默认值是-2.5，如果是默认值，效果则反之*

## ***相关类与结构体***

### ***ULyraInputConfig***

#### ***作用***

1. *如何定义 `GameplayTag` 与 `InputAction` 的映射*

#### ***接口与方法***

##### ***FLyraInputAction***

1. *该结构体用于将输入操作映射到游戏玩法的输入标签上。*

##### ***FindNativeInputActionForTag***

1. *根据 GameplayTag来搜索 NativeInputAction*

##### ***FindAbilityInputActionForTag***

1. *根据 GameplayTag来搜索 AbilityInputAction*

#### ***成员变量说明***

* *NativeInputActions：就是WASD这些有关基本操作的*
* *AbilityInputActions: 跟技能相关的，只要跟GAS有关联，都可放这里*

### ***ULyraInputComponent***

#### ***作用***

1. *用于通过输入配置数据资产来管理输入映射和绑定的组件。说白了就是处理ULyraInputConfig数据CURD的*

#### ***接口与方法***

1. *不多说，基本每个方法直接看就能看出来 都是CURD*

### ***UGameFeatureAction_AddInputContextMapping***

#### ***作用***

1. *给本地玩家添加输入映射: 自动添加 `InputMappingContext` 到 `EnhancedInputSubsystem` 中*
2. *和 GameInstance / LocalPlayer 生命周期关联：玩家加入时会自动添加；玩家离开时自动移除*
3. *支持优先级和是否注册到 Settings 的配置：每个 IMC 可以设置优先级、是否保存到设置中*

#### ***核心结构体：FInputMappingContextAndPriority***

* *`InputMapping`: 要添加的输入映射资源（IMC）*
* *`Priority`: 优先级（高的优先）*
* *`bRegisterWithSettings`: 是否写入用户设置中（用户可以修改的那种）*

#### ***生命周期函数***

1. *virtual void OnGameFeatureRegistering() override;*
2. *virtual void OnGameFeatureActivating(FGameFeatureActivatingContext& Context) override;*
3. *virtual void OnGameFeatureDeactivating(FGameFeatureDeactivatingContext& Context) override;*
4. *virtual void OnGameFeatureUnregistering() override;*

#### ***GameFeature***

*`GameFeature` 是 UE5 引入的一个 **模块化插件系统**，主要目标是：*

| *Feature 插件*     | *功能*                           |
| ------------------ | -------------------------------- |
| *InputMapping*     | *添加新的输入操作映射*           |
| *Weapon_AR*        | *加入一个步枪的逻辑、资源、蓝图* |
| *UI_HUD*           | *添加一个特定的 HUD 或菜单界面*  |
| *Ability_Fireball* | *加入一个技能火球术*             |

*每个 `GameFeature` 插件都能控制：*

- *加载哪些资源（IMC、AbilitySet、蓝图、DataAsset…）*
- *在哪个状态执行（只在服务器？只在客户端？）*
- *在哪张地图中生效*

