# Lyra - Plugins


## ***ModularGameplayActors***

### ***作用***

* *Lyra 里的插件 **`ModularGameplayActors`** 是整个架构的一个核心部分，用来实现 **角色、控制器、HUD 等 Gameplay 类的模块化（Modular）构建机制**。它是 Lyra “模块化组件系统” 的基础，支持你以“插件化方式”给 Actor 添加功能，而不是通过硬继承和写死逻辑。*

### ***模块化系统的核心目标***

*传统方式的问题：*

* *你通常会在 `MyCharacter`、`MyPlayerController`、`MyHUD` 里硬编码各种行为，比如：初始化输入/初始化技能系统组件/初始化 UI 控制逻辑*
* *但这样：很难复用/不利于热更、插件式内容扩展/玩家控制器和角色之间的耦合度太高*

*`ModularGameplayActors `将这些逻辑**解耦**成一个个“模块化组件”，通过注册自动注入到对应 Actor（如 Pawn, Controller, HUD）中。*

### ***此插件的主要功能***

1. *模块化 Actor 生命周期管理：支持在特定时机（如 Controller 初始化、Pawn Possess 等）注册模块组件*
2. *动态挂载组件：可以在运行时给 Pawn/Controller 添加组件（如 `ULyraHeroComponent`）*
3. *支持 GameFeature 插件动态注册组件：GameFeature 激活时可以给现有 Actor 动态添加模块逻辑*
4. *按类型分组扩展点：拥有像 `ExtensionEvent-BindInputsNow`、`OnPawnExtensionAdded` 等事件点*

### ***核心类说明***

1. *UGameFrameworkComponentManager*

   * *它负责监听 `Pawn`、`Controller`、`HUD` 等的创建事件，并调用注册的扩展。*

   * *这个语句会触发所有注册到该事件的组件（如 `ULyraHeroComponent`）执行绑定。*

     ```cpp
     UGameFrameworkComponentManager::SendGameFrameworkComponentExtensionEvent(Pawn, NAME_BindInputsNow);
     ```

2. *`UModularPawn` / `UModularPlayerController` 等*

   * *这些是 Lyra 扩展出来的基类，继承自引擎默认的 `APawn` / `APlayerController` 等。*

   * *它们的特点：*

     - *自动调用 `UGameFrameworkComponentManager` 的初始化逻辑*
     - *支持组件注册、事件派发等*

     

