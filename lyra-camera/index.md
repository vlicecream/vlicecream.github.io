# Lyra - Camera


## ***Lyra的相机系统 简介***

*Lyra其实没有用最基本的UCameraComponent，也没有用 USpringComponent*

*他是自己实现了继承于 UCameraComponent的类，他的功能为：*

1. *它挂载在玩家角色上，并重写了引擎的 GetCameraView 函数。在每一帧，它负责启动整个更新和混合流程，并将最终计算出的视图结果提交给渲染器。*

*它拥有一个 CameraModeStack，这是一个“后进先出”的栈结构，用于存放所有当前激活的相机模式。它负责：*

1. ***管理模式**：处理相机模式的推入（Push）和移除*
2. ***更新模式**：在每帧调用每个模式自身的更新逻辑*
3. ***混合视图**：将栈中所有模式的视图根据权重混合成一个最终视图。*

*当我们需要一种特定的相机行为的时候，就可以自定义一个CameraMode，比如：*

1. *CameraMode_ThirdPerson：定义了基础的第三人称跟随视角。*
2. *CameraMode_Aiming：定义了瞄准时，相机移动到角色肩膀的贴近视角。*
3. *其他可能的模式：冲刺模式（增加FOV和抖动）、蹲伏模式（降低相机高度）等。*

*我们可以把相机一些简单的数据存放到一个结构体 - **FLyraCameraModeView (相机视图数据)**，用于存放相机的位置（Location）、旋转（Rotation）、视场角（FieldOfView）等关键信息*

## ***工作流程***

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

