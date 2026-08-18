# Lyra - Camera


## ***什么是 Lyra 摄像机系统***

*Lyra 摄像机系统是一个高度模块化、数据驱动的“模式切换”架构。它没有把第三人称、开镜、特殊玩法视角全部硬编码在 Character 或 CameraComponent 里，而是把每种视角抽象成独立的 **ULyraCameraMode**，再通过 **ULyraCameraModeStack** 负责模式之间的平滑混合。*

- ***传统 UE 做法***：在 Character 类里放置 `CameraComponent` / `SpringArmComponent`，通过大量 `if-else`、硬编码 `RelativeLocation` 或手写插值逻辑切换瞄准、冲刺、蹲伏等状态。
- ***Lyra 方案***：`PawnData`、`HeroComponent`、`GameplayAbility` 只负责表达“当前想要哪个 CameraMode”；真正的位置、旋转、FOV 计算由 CameraMode 子类完成，最终通过 `ULyraCameraComponent::GetCameraView` 交给 Unreal 渲染流程。

---

## ***核心组件职责划分***

### *ALyraPlayerCameraManager (最终视角管理器)*

- ***地位***：每个 `PlayerController` 专属的最终视角管理器，是 Unreal 玩家相机流程中的关键汇总层。
- ***核心作用***：
  1. ***ViewTarget 管理***：决定当前玩家是在看自己控制的 Pawn，还是在看观战目标、回放目标或其他 ViewTarget。
  2. ***UI 相机扩展点***：Lyra 在这里挂了 `ULyraUICameraManagerComponent`，用于将来让 UI 场景覆盖普通 Gameplay 视角；当前源码里默认 `NeedsToUpdateViewTarget()` 返回 `false`，所以它更像预留入口。
  3. ***调试信息***：`DisplayDebug` 会继续向下调用 `ULyraCameraComponent::DrawDebug`，把 CameraModeStack 和当前模式信息画出来。

### *ULyraCameraComponent (Pawn 身上的相机出口)*

- ***地位***：长在玩家 Pawn 身上的 Lyra 相机组件，是 Gameplay Camera 结果进入 Unreal 渲染流程的出口。
- ***核心作用***：
  1. ***桥接 Unreal***：重写 `GetCameraView`，每帧把 Lyra 计算出的 `Location / Rotation / FOV / PostProcess` 填进 `FMinimalViewInfo`。
  2. ***发起询问***：它自己不决定“用哪个 CameraMode”，而是通过 `DetermineCameraModeDelegate` 询问外部，默认由 `ULyraHeroComponent::DetermineCameraMode` 回答。
  3. ***同步状态***：将最终 `ControlRotation` 同步回 `PlayerController`，并把最终位置、旋转、FOV 同步回组件本身。
  4. ***一次性 FOV 偏移***：`AddFieldOfViewOffset` 可以给当前帧加一个临时 FOV 偏移，应用后会立刻清零。

### *ULyraCameraModeStack (模式混合器)*

- ***核心逻辑***：**“栈式模式混合”**。
- ***作用***：
  1. ***栈式管理***：新模式会被压到栈顶，例如默认第三人称模式上方压入一个开镜 ADS 模式。
  2. ***实例复用***：CameraMode 实例会缓存在 `CameraModeInstances` 里；旧模式从当前栈中移除后并不是立刻销毁，而是可以后续复用。
  3. ***自动移除旧层***：当某个上层模式权重达到 1.0 时，它下面已经没有贡献的旧模式会被移出当前栈。
  4. ***平滑插值***：根据 `BlendTime`、`BlendFunction`、`BlendExponent`，让镜头在不同模式间平滑过渡，而不是瞬移。

### *ULyraCameraMode (具体视角算法)*

- ***地位***：具体相机模式的基类。通常新增相机不是重写 `GetCameraView`，而是新建一个 `ULyraCameraMode` 子类并重写 `UpdateView`。
- ***核心属性***：
  1. ***FieldOfView***：定义视野大小。
  2. ***ViewPitchMin / ViewPitchMax***：限制玩家抬头或低头角度。
  3. ***CameraTypeTag***：给 Gameplay 系统识别当前相机类型，例如瞄准类相机可以影响武器扩散。
  4. ***Blend Logic***：定义自己被压入栈时需要多久、用什么曲线完成过渡。
- ***子类扩展***：
  - `ULyraCameraMode_ThirdPerson` 使用 `TargetOffsetCurve` 或运行时曲线，根据 Pitch 计算第三人称偏移，并处理蹲伏偏移和防穿墙。
  - `ULyraCameraMode_TopDownArenaCamera` 根据场地范围、默认俯视角度和距离曲线生成固定俯视相机。

---

## ***GameplayAbility 是怎么接入相机的***

*Lyra 的 Ability 不直接移动 CameraComponent，也不直接改相机 Transform。Ability 只表达一个 Gameplay 意图：我激活期间希望使用某个 CameraMode。*

### *1. Ability 暴露相机接口*

`ULyraGameplayAbility` 提供两个蓝图可调用函数：

```cpp
void SetCameraMode(TSubclassOf<ULyraCameraMode> CameraMode);
void ClearCameraMode();
```

- ***SetCameraMode***：Ability 激活时调用，用来声明“我现在需要这个相机模式”。
- ***ClearCameraMode***：Ability 结束或取消时清理相机模式；`EndAbility` 里会自动调用，避免相机状态残留。

### *2. Ability 把 CameraMode 写给 HeroComponent*

`SetCameraMode` 内部不会直接访问 `ULyraCameraComponent`，而是先找到 `ULyraHeroComponent`：

```cpp
HeroComponent->SetAbilityCameraMode(CameraMode, CurrentSpecHandle);
ActiveCameraMode = CameraMode;
```

- `AbilityCameraMode` 保存当前 Ability 要求的相机模式。
- `AbilityCameraModeOwningSpecHandle` 保存是哪个 Ability 设置了这个模式。
- 清理时会检查 `OwningSpecHandle`，防止一个 Ability 错误清掉另一个 Ability 设置的相机。

### *3. CameraComponent 每帧反向询问 HeroComponent*

`ULyraCameraComponent::UpdateCameraModes` 每帧执行：

```cpp
DetermineCameraModeDelegate.Execute()
CameraModeStack->PushCameraMode(CameraMode)
```

这个 Delegate 在 HeroComponent 初始化时绑定到：

```cpp
ULyraHeroComponent::DetermineCameraMode()
```

它的优先级非常清楚：

```text
AbilityCameraMode
  -> PawnData->DefaultCameraMode
  -> nullptr
```

也就是说，只要 Ability 设置了 CameraMode，它就会临时压过 PawnData 上配置的默认相机。

### *4. 开镜 Ability 的完整例子*

```text
默认状态：
DetermineCameraMode 返回 PawnData->DefaultCameraMode
玩家看到默认第三人称相机

按下瞄准：
Aim Ability 激活
Ability 调 SetCameraMode(ADS_CameraMode)
HeroComponent 保存 AbilityCameraMode

下一帧：
CameraComponent 通过 Delegate 问 HeroComponent 当前相机
HeroComponent 返回 ADS_CameraMode
CameraModeStack Push ADS_CameraMode
相机从第三人称平滑 Blend 到 ADS

松开瞄准：
Aim Ability 结束
EndAbility 自动 ClearCameraMode
HeroComponent 清掉 AbilityCameraMode
CameraComponent 再次拿到默认 CameraMode
相机平滑 Blend 回默认第三人称
```

### *5. Ability 相机还能反向影响 Gameplay*

CameraMode 可以配置 `CameraTypeTag`。武器系统会读取：

```cpp
CameraComponent->GetBlendInfo(TopCameraWeight, TopCameraTag);
```

如果当前相机 Tag 是 `Lyra.Weapon.SteadyAimingCamera`，武器扩散会根据相机混合权重逐步降低。也就是说，相机不只是视觉效果，还可以作为 Gameplay 数值判断的一部分。

> 源码阅读提醒：`GetBlendInfo` 的注释写的是读取 top layer，但当前实现使用 `CameraModeStack.Last()`。结合 `PushCameraMode` 使用 `Insert(CameraMode, 0)` 的逻辑来看，`Last()` 更像栈底。这个点适合在源码课上单独讨论和验证。

---

## ***相机画面的“诞生之旅” (每帧调用链)***

1. ***视角更新入口***：`PlayerCameraManager` 更新当前玩家的 ViewTarget。
2. ***组件响应***：如果 ViewTarget 上有 `ULyraCameraComponent`，Unreal 会调用它的 `GetCameraView`。
3. ***查询意图***：`CameraComponent` 通过 Delegate 问 `HeroComponent`：“当前 Gameplay 状态要求什么 CameraMode？”
4. ***模式选择***：`HeroComponent` 优先返回 Ability 设置的 `AbilityCameraMode`，否则返回 `PawnData->DefaultCameraMode`。
5. ***栈计算***：`CameraModeStack` 把最新模式压入栈顶，并根据 `DeltaTime` 更新所有活跃模式的 View 和 BlendWeight。
6. ***数学混合***：系统从栈底向栈顶遍历，将位置、旋转、ControlRotation、FOV 按权重混合。
7. ***最终输出***：`CameraComponent` 把结果填充进 `FMinimalViewInfo`，正式交给 Unreal 渲染流程。

---

## ***主要消费场景***

### *1. 远程武器与交互系统 (Trace & Interaction)*

- ***消费函数***：`PC->GetPlayerViewPoint`。
- ***核心作用***：
  1. ***确定瞄准方向***：Lyra 的远程武器 Ability 会使用玩家当前视角计算射线方向，保证准星指向和命中检测一致。
  2. ***寻找目标***：交互系统通过相机视角进行射线扫描，判断玩家当前正在注视哪个开关、箱子或可交互对象。

### *2. 武器扩散与数值修正 (Weapon Spread)*

- ***消费数据***：`CameraComponent->GetBlendInfo`。
- ***核心作用***：
  - ***读取标签 (Tag)***：武器系统会读取当前相机模式的 `CameraTypeTag`，例如 `Lyra.Weapon.SteadyAimingCamera`。
  - ***混合权重控制***：当玩家正在开镜过渡时，武器扩散会根据相机混合百分比动态缩放。相机越接近瞄准模式，射击越稳定。

### *3. 动态准星与 UI 投影 (UI Reticle)*

- ***消费数据***：`PlayerCameraManager->GetCameraViewPoint`、FOV、`ProjectWorldLocationToScreen`。
- ***核心作用***：
  1. ***准星扩散半径***：UI 准星根据相机位置、旋转和 FOV，把武器扩散圆投影到屏幕上，计算它应该占多少像素。
  2. ***屏幕空间表现***：准星显示不是随便放一个圆，而是把真实 3D spread cone 映射到玩家当前屏幕视角。

### *4. 辅助瞄准系统 (Aim Assist)*

- ***消费数据***：玩家当前 ViewTransform 和 `PlayerCameraManager->GetFOVAngle`。
- ***核心作用***：
  - ***FOV 缩放***：当摄像机处于开镜模式、FOV 变小时，Aim Assist 会根据当前 FOV 缩放搜索范围、投影尺寸或输入响应，保持手柄瞄准手感一致。

### *5. 防穿墙与组件隐藏 (Penetration Handling)*

- ***消费行为***：`ILyraCameraAssistInterface::OnCameraPenetratingTarget`。
- ***核心逻辑***：
  - 第三人称相机被墙体挤压、距离角色过近时，CameraMode 会通知实现了 `ILyraCameraAssistInterface` 的对象。
  - `ALyraPlayerController` 收到通知后，会在下一帧隐藏 ViewTarget Pawn 的组件，避免玩家看到角色模型内部。

### *6. Debug / Cheat / Replay / SceneCapture*

- ***Debug***：`PlayerCameraManager::DisplayDebug` 会继续调用 `CameraComponent->DrawDebug`，展示当前相机模式和混合权重。
- ***Cheat***：`LyraCheatManager` 可以切换 debug camera 或 fixed camera，用于开发调试。
- ***Replay***：回放控制器可以 `SetViewTarget` 到被跟随玩家的 Pawn。
- ***SceneCapture***：`PocketWorlds` 插件会读取普通 `UCameraComponent::GetCameraView`，把相机结果复制给 `SceneCaptureComponent2D` 做捕获。

---

## ***方案对比：原生 UE vs Lyra 架构***

| *特性/维度*      | *原生 UE 组件模式*       | *Lyra 摄像机模式栈*                    |
| ---------------- | ------------------------ | -------------------------------------- |
| ***逻辑耦合度*** | *高，常堆在 Pawn 或 PCM* | ***低，模式、选择、输出职责分离***     |
| ***平滑切换***   | *通常需要手写插值*       | ***由 ModeStack 统一处理混合***        |
| ***多状态切换*** | *容易出现 if-else 冲突*  | ***通过模式优先级和栈混合处理***       |
| ***技能关联***   | *手动修改组件参数*       | ***Ability 声明 CameraMode 即可***     |
| ***数据驱动***   | *常依赖代码硬编码*       | ***PawnData / CameraMode 蓝图可配置*** |
| ***扩展性***     | *改一处牵动多处逻辑*     | ***新建 CameraMode 子类即可扩展***     |

---

## ***简述工作流程***

1. `void ULyraCameraComponent::GetCameraView(float DeltaTime, FMinimalViewInfo& DesiredView)`

   *每帧都会调用一次，或者其他情况，在此函数会进行照相机的一系列数值计算*

2. `void ULyraCameraComponent::UpdateCameraModes()`

   *发出委托，获得ULyraPawnData 里面的配置的 CameraMode*

3. `void ULyraCameraModeStack::PushCameraMode(TSubclassOf<ULyraCameraMode> CameraModeClass)`

   *它的作用是将一个新的 摄像机模式（Camera Mode）压入堆栈，并处理 混合（Blending） 和 权重（Blend Weight）逻辑。*

4. `void ULyraCameraModeStack::UpdateStack(float DeltaTime)`

   *Lyra 摄像机模式堆栈（Camera Mode Stack）的更新逻辑，负责每帧更新堆栈中的摄像机模式，并清理不再需要的模式*

5. `void ULyraCameraModeStack::BlendStack(FLyraCameraModeView& OutCameraModeView) const`

   *负责将堆栈中的所有摄像机模式按权重混合，生成最终的摄像机视图*

## ***一句话总结***

*Lyra Camera 的核心不是在 `CameraComponent` 里写一堆条件判断，而是用 `HeroComponent / Ability / PawnData` 决定当前要哪个 `CameraMode`，用 `CameraModeStack` 做平滑切换，用 `CameraMode` 子类负责具体视角算法，最后由 `ULyraCameraComponent::GetCameraView` 输出给 Unreal。*

