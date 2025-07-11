# 解密Lyra初始化流程：IGameFrameworkInitStateInterface的核心回调


## ***作用***

*IGameFrameworkInitStateInterface 的作用，就是将一个组件原本**混乱、单体**的初始化过程（主要挤在BeginPlay里），拆分成一个**清晰、有序、分阶段的“生命周期”**。*

*它允许不同组件之间优雅地声明和等待依赖关系，从而构建出**可预测、可扩展、无惧异步加载**的复杂系统。*

## ***核心接口***

1. `virtual FName GetFeatureName() const override { return NAME_ActorFeatureName; }`

   *返回这个组件在 GameFrameworkComponentManager 系统中的**唯一标识名称（身份证号）***

   *其他组件如果想查询或依赖这个组件的初始化状态，就必须使用这个函数返回的 FName*

2. `virtual bool CanChangeInitState(UGameFrameworkComponentManager* Manager, FGameplayTag CurrentState, FGameplayTag DesiredState) const override;`

   *这是一个条件检查函数。当 Manager 尝试将组件的状态从 CurrentState 推进到 DesiredState 时，会调用这个函数来询问：“现在可以进行这个状态转换吗？”*

3. `virtual void HandleChangeInitState(UGameFrameworkComponentManager* Manager, FGameplayTag CurrentState, FGameplayTag DesiredState) override;`

   *这是一个状态改变回调函数。在 CanChangeInitState 返回 true 并且 Manager 成功地将组件的状态更新后，这个函数会被立即调用。*

4. `virtual void OnActorInitStateChanged(const FActorInitStateChangedParams& Params) override;`

   *这是一个事件监听器。前提是你必须在 BeginPlay 中调用 BindOnActorInitStateChanged() 来注册这个监听。*

   *注册后，当同一个Actor上其他被Manager管理的组件的初始化状态发生变化时，这个函数就会被调用。*

5. `virtual void CheckDefaultInitialization() override;`

   *在这个函数内部，你会调用 UGameFrameworkComponentManager 提供的一些工具函数（如 ContinueInitStateChain），来触发一次从当前状态到最终状态的连续转换尝试*

## ***正常工作流程***

*假设我们有4个 FGAmeplayTag： `LyraGameplayTags::InitState_Spawned, LyraGameplayTags::InitState_DataAvailable, LyraGameplayTags::InitState_DataInitialized, LyraGameplayTags::InitState_GameplayReady`*

*我们还有两个Component：*

1. *`class LYRAGAME_API ULyraHeroComponent : public UPawnComponent, public IGameFrameworkInitStateInterface`*
2. *`class LYRAGAME_API ULyraPawnExtensionComponent : public UPawnComponent, public IGameFrameworkInitStateInterface`*

*所以流程为：*

1. *先把这四个注册进去，比如在GameInstance注册：*

   ```cpp
   void ULyraGameInstance::Init()
   {
   	Super::Init();
   
   	UGameFrameworkComponentManager* ComponentManager = GetSubsystem<UGameFrameworkComponentManager>(this);
   
   	if (ensure(ComponentManager))
   	{
   		ComponentManager->RegisterInitState(LyraGameplayTags::InitState_Spawned, false, FGameplayTag());
   		ComponentManager->RegisterInitState(LyraGameplayTags::InitState_DataAvailable, false, LyraGameplayTags::InitState_Spawned);
   		ComponentManager->RegisterInitState(LyraGameplayTags::InitState_DataInitialized, false, LyraGameplayTags::InitState_DataAvailable);
   		ComponentManager->RegisterInitState(LyraGameplayTags::InitState_GameplayReady, false, LyraGameplayTags::InitState_DataInitialized);
   	}
   }
   ```

2. *随后在两个Component初始化，并记得销毁状态（两个Component都一样）*

   ```cpp
   void ULyraPawnExtensionComponent::OnRegister()
   {
   	Super::OnRegister();
   
   	// Register with the init state system early, this will only work if this is a game world
   	RegisterInitStateFeature();
   }
   
   void ULyraPawnExtensionComponent::BeginPlay()
   {
   	Super::BeginPlay();
   
   	// Listen for changes to all features
   	BindOnActorInitStateChanged(NAME_None, FGameplayTag(), false);
   	
   	// Notifies state manager that we have spawned, then try rest of default initialization
   	ensure(TryToChangeInitState(LyraGameplayTags::InitState_Spawned));
   	CheckDefaultInitialization();
   }
   
   void ULyraPawnExtensionComponent::EndPlay(const EEndPlayReason::Type EndPlayReason)
   {
   	UnregisterInitStateFeature();
   
   	Super::EndPlay(EndPlayReason);
   }
   ```

3. *只要BeginPlay触发了，`CheckDefaultInitialization()` 这个就会执行，当然你在其他地方也可以执行，只要满足你的需要*

   ```cpp
   void ULyraPawnExtensionComponent::CheckDefaultInitialization()
   {
   	// Before checking our progress, try progressing any other features we might depend on
   	CheckDefaultInitializationForImplementers();
   
   	static const TArray<FGameplayTag> StateChain = { LyraGameplayTags::InitState_Spawned, LyraGameplayTags::InitState_DataAvailable, LyraGameplayTags::InitState_DataInitialized, LyraGameplayTags::InitState_GameplayReady };
   
   	// This will try to progress from spawned (which is only set in BeginPlay) through the data initialization stages until it gets to gameplay ready
   	ContinueInitStateChain(StateChain);
   }
   ```

4. *随后就会执行 CanChangeInitState，每当当前状态通过，返回 true 后，就会调用一次 HandleChangeInitState*

   ```cpp
   bool ULyraPawnExtensionComponent::CanChangeInitState(UGameFrameworkComponentManager* Manager, FGameplayTag CurrentState, FGameplayTag DesiredState) const
   {
   	check(Manager);
   
   	APawn* Pawn = GetPawn<APawn>();
   	if (!CurrentState.IsValid() && DesiredState == LyraGameplayTags::InitState_Spawned)
   	{
   		return true;
   	}
   	if (CurrentState == LyraGameplayTags::InitState_Spawned && DesiredState == LyraGameplayTags::InitState_DataAvailable)
   	{
   		if (!PawnData)
   		{
   			return false;
   		}
   
   		return true;
   	}
   	else if (CurrentState == LyraGameplayTags::InitState_DataAvailable && DesiredState == LyraGameplayTags::InitState_DataInitialized)
   	{
   		return Manager->HaveAllFeaturesReachedInitState(Pawn, LyraGameplayTags::InitState_DataAvailable);
   	}
   	else if (CurrentState == LyraGameplayTags::InitState_DataInitialized && DesiredState == LyraGameplayTags::InitState_GameplayReady)
   	{
   		return true;
   	}
   
   	return false;
   }
   ```

   ```cpp
   void ULyraPawnExtensionComponent::HandleChangeInitState(UGameFrameworkComponentManager* Manager, FGameplayTag CurrentState, FGameplayTag DesiredState)
   {
   	if (DesiredState == LyraGameplayTags::InitState_DataInitialized)
   	{
   		// This is currently all handled by other components listening to this state change
   	}
   }
   ```

5. *当同一个Actor上其他被Manager管理的组件的初始化状态发生变化时，就会调用：*

   ```cpp
   void ULyraPawnExtensionComponent::OnActorInitStateChanged(const FActorInitStateChangedParams& Params)
   {
   	// If another feature is now in DataAvailable, see if we should transition to DataInitialized
   	if (Params.FeatureName != NAME_ActorFeatureName)
   	{
   		if (Params.FeatureState == LyraGameplayTags::InitState_DataAvailable)
   		{
   			CheckDefaultInitialization();
   		}
   	}
   }
   ```

## ***协作方法***

1. `bool UGameFrameworkComponentManager::HaveAllFeaturesReachedInitState(AActor* Actor, FGameplayTag RequiredState, FName ExcludingFeature) const`

   *询问 “对于指定的这个 Actor（Pawn），所有在 Manager 中注册过的、并且与这个 Actor 相关的功能（Features），是否都已经达到或超过了给定的状态（InitState_DataAvailable）？”*

2. `bool UGameFrameworkComponentManager::HasFeatureReachedInitState(AActor* Actor, FName FeatureName, FGameplayTag FeatureState) const`

   *它在问 GameFrameworkComponentManager：“嘿，我的同事 PawnExtensionComponent，它是不是已经到达过 DataInitialized 状态？”*

   *注意是：到达过 xxx 状态，也就是说如果目标已经经过这个状态也会返回 true*

