# Lyra-GAS


## ***GAS 与 Input 调用***

*经过游戏体验DataAsset和一系列的组件初始化加载后，在 LyraHeroComponent 绑定按键*

```cpp
/*
	此函数是技能函数，比如鼠标左键枪开火
	在 ULyraInputConfig 里配置 AbilityInputActions
*/
LyraIC->BindAbilityActions(InputConfig, this, &ThisClass::Input_AbilityInputTagPressed, &ThisClass::Input_AbilityInputTagReleased, /*out*/ BindHandles);
```

*在绑定了按键后，会在ASC的 `void ULyraAbilitySystemComponent::AbilityInputTagPressed(const FGameplayTag& InputTag)` 留下记录*

```cpp
void ULyraAbilitySystemComponent::AbilityInputTagPressed(const FGameplayTag& InputTag)
{
	if (InputTag.IsValid())
	{
		for (const FGameplayAbilitySpec& AbilitySpec : ActivatableAbilities.Items)
		{
			if (AbilitySpec.Ability && (AbilitySpec.GetDynamicSpecSourceTags().HasTagExact(InputTag)))
			{
				InputPressedSpecHandles.AddUnique(AbilitySpec.Handle);
				InputHeldSpecHandles.AddUnique(AbilitySpec.Handle);
			}
		}
	}
}
```

*自然松开的时候，会调用ASC的`void ULyraAbilitySystemComponent::AbilityInputTagReleased(const FGameplayTag& InputTag)` 并留下相对应的记录*

```cpp
void ULyraAbilitySystemComponent::AbilityInputTagReleased(const FGameplayTag& InputTag)
{
	if (InputTag.IsValid())
	{
		for (const FGameplayAbilitySpec& AbilitySpec : ActivatableAbilities.Items)
		{
			if (AbilitySpec.Ability && (AbilitySpec.GetDynamicSpecSourceTags().HasTagExact(InputTag)))
			{
				InputReleasedSpecHandles.AddUnique(AbilitySpec.Handle);
				InputHeldSpecHandles.Remove(AbilitySpec.Handle);
			}
		}
	}
}
```

*在然后，ALyraPlayerController中会Tick调用`void ALyraPlayerController::PostProcessInput(const float DeltaTime, const bool bGamePaused)`*

*随后检索一些TArray记录下来的东西，通过 `TryActivateAbility(AbilitySpecHandle);` 调用到实际技能*

## ***从武器到技能***

*举个例子， 比如是捡到枪，或者默认装备枪，随后鼠标左键开火，这其实就是一个开枪技能*

### ***装备技能***

*捡到枪并且装备是通过 Lyra 的 Equipment系统和Inventory系统来实现的，在这里不详细讲，但是此时有一个ULyraEquipmentDefinition 中会记录一个 ULyraAbilitySet，里面有见到这个枪就会装备什么GameAbility技能，以及GameEffect效果，还有AttributeSet属性数值，这时候就会将GameAbility技能给GiveAbility装备上去*

### ***技能的实现***

*创建一个GA类，不管是蓝图还是c++，GameplayAbility有以下比较重要的接口*

1. `virtual bool CanActivateAbility(const FGameplayAbilitySpecHandle Handle, const FGameplayAbilityActorInfo* ActorInfo, const FGameplayTagContainer* SourceTags = nullptr, const FGameplayTagContainer* TargetTags = nullptr, OUT FGameplayTagContainer* OptionalRelevantTags = nullptr) const override;`

   *判断是否可以激活此技能，在 `TryActivateAbility` 后，其实会进行一系列的检查，此接口就在其中*

2. `virtual void ActivateAbility(const FGameplayAbilitySpecHandle Handle, const FGameplayAbilityActorInfo* ActorInfo, const FGameplayAbilityActivationInfo ActivationInfo, const FGameplayEventData* TriggerEventData) override;`

   *在经过一系列技能检查后就会触发，也就是此技能的核心逻辑，比如播放蒙太奇，然后蒙太奇播放完调用 `EndAbility`*

3. `virtual void EndAbility(const FGameplayAbilitySpecHandle Handle, const FGameplayAbilityActorInfo* ActorInfo, const FGameplayAbilityActivationInfo ActivationInfo, bool bReplicateEndAbility, bool bWasCancelled) override;`

   *技能结束的核心逻辑写在这个函数，但是记住这个函数不代表跟技能相关的实例就会销毁*

4. *virtual void InputPressed(const FGameplayAbilitySpecHandle Handle, const FGameplayAbilityActorInfo* ActorInfo, const FGameplayAbilityActivationInfo ActivationInfo) override;*
   *此函数的作用就在于，如果是通过按键去触发技能，那么在按下键的时候就会触发*

5. `virtual void InputReleased(const FGameplayAbilitySpecHandle Handle, const FGameplayAbilityActorInfo* ActorInfo, const FGameplayAbilityActivationInfo ActivationInfo) override;`

   *抬起键的时候触发*

*随后不管c++还是蓝图，将其 InstancingPolicy 修改为：InstancedPerActor，此参数有以下几种：*

1. *NonInstanced：此能力从未实例化。任何执行此能力的操作都是在 CDO 上进行的。*
2. *InstancedPerActor：当这个 Ability 被授予给一个 Actor 时，系统会为这个 Actor 创建一个全新的 UGameplayAbility 对象实例。如果 10 个不同的 Actor 都被授予了这个 Ability，内存中就会有 10 个独立的 Ability 对象。*
3. *InstancedPerExecution：当这个 Ability 被授予时，系统不会立即创建实例。只有在每次这个 Ability 被激活 (ActivateAbility) 时，系统才会临时创建一个新的 Ability 对象实例。当这个 Ability 执行完毕并结束 (EndAbility) 时，这个临时的实例会被立即销毁（垃圾回收）。*

*随后还得将一个变量Ability Triggers修改，将你的按键绑定的Tag输入进去，这样就可以将按键跟技能上面关联上啦*

![image-20250805230152296](https://raw.githubusercontent.com/vlicecream/cloudImage/main/image-20250805230152296.png)

### ***检测到伤害目标***

*这里不详细讲如何检测到伤害目标（可能后续会更新？），比如枪发射子弹，就发出射线，进行扫描。如果是近战武器，则利用动画通知去生成碰撞盒子，来进行伤害目标*

*如果要在这拿到GA实例，只要能拿到ASC，则可以利用 `ASC->GetActivatableAbilities()`，拿到所有激活的技能Spec，在通过 `AbilitySpec.GetPrimaryInstance()` Cast 成你的GA实例就好了*

### ***应用GE***

*这时候我们就会有目标Actor了，我们获取到自己的ASC和Target的ASC，可以利用如下代码，创建GameplayEffectSpecHandle，并且应用*

```cpp
FGameplayEffectSpecHandle DamageSpecHandle = MakeOutgoingGameplayEffectSpec(DamageEffect, GetAbilityLevel());

if (DamageSpecHandle.IsValid())
{
    SourceASC->ApplyGameplayEffectSpecToTarget(*DamageSpecHandle.Data.Get(), TargetASC);
}
```

*好，这时候注意力来到 我们创建的GE，GE也有着很多的属性，我们关注点先放在Gameplay Effect*

1. *Modifiers：这个可以应用比较简单的Attributes。比如直接将Health -> Add -> -10，这样每次攻击，都会对Target扣10点血。但是这样缺点就是不灵活，因为这个伤害值计算确实很复杂，比如lyra的枪，他有距离衰减，物理散射，包括打头伤害更高之类的，所以就需要 Executions*
2. *Executions：我们可以创建一个 UGameplayEffectExecutionCalculation 的子类，这个类就可以帮助我们去算伤害的具体值，这个类的执行就是在应用AttricuteSet之前，所以我们可以直接操作他的伤害值*

### ***GameplayEffectContext***

*再聊到具体的 Executions 之前还得聊一聊这个，这个其实就是 GameplayEffect 的上下文，也就是储存着 GE 通知 AttricuteSet的各种信息，我们继承于这个Struct FGameplayEffectContext，这样就可以塞我们自己自定义的任何信息，比如伤害接口的伤害倍数，在这个struct有如下接口需要注意下，有可能用到：*

```cpp
// ~Begin FGameplayEffectContext Interface
virtual FGameplayEffectContext* Duplicate() const override;  // 进行拷贝结构体的
virtual UScriptStruct* GetScriptStruct() const override;  // 获取当前 GameplayEffectContext 实例的实际 UStruct 类型。
virtual bool NetSerialize(FArchive& Ar, class UPackageMap* Map, bool& bOutSuccess) override;  // 进行网络复制
// ~End FGameplayEffectContext Interface
```

***我们如果想要我们自定义的生效，必须重写AbilitySystemGlobals方法，并且在Project Setting里 设置我们默认的 AbilitySystemGlobals 类***

***并且要在 我们的GA中 重写 `virtual FGameplayEffectContextHandle MakeEffectContext(const FGameplayAbilitySpecHandle Handle, const FGameplayAbilityActorInfo* ActorInfo) const override;` 在这个函数里面去初始化我们的GEContext哦***

### ***AbilitySystemGlobals***

```cpp
virtual FGameplayEffectContext* AllocGameplayEffectContext() const override;

FGameplayEffectContext* USAbilitySystemGlobals::AllocGameplayEffectContext() const
{
	return new FSGameplayEffectContext();
}
```

### ***Executions***

*好了，终于回到了我们的Executions，我们重写 `virtual void Execute_Implementation(const FGameplayEffectCustomExecutionParameters& ExecutionParams, FGameplayEffectCustomExecutionOutput& OutExecutionOutput) const override;`方法*

1. *ExecutionParams：这个参数就是带有各种数据，比如SourceASC，TargetASC*
2. *OutExecutionOutput：这个参数就是我们要给AttricuteSets的数据，所以我们弄好了伤害具体数值后就可以通过这个直接扔过去*

*我们要注意 比如我们拿一个 BaseDamage，这个基础伤害，我们要通过FGameplayEffectAttributeCaptureDefinition 这个结构体去拿*

```cpp
struct FDamageStatics
{
	FGameplayEffectAttributeCaptureDefinition BaseDamageDef;

	FDamageStatics()
	{
		BaseDamageDef = FGameplayEffectAttributeCaptureDefinition(USCombatSet::GetBaseDamageAttribute(), EGameplayEffectAttributeCaptureSource::Source, true);
	}
};

static FDamageStatics DamageStatics()
{
	static FDamageStatics Statics;
	return Statics;
}

USDamageExecution::USDamageExecution()
{
	RelevantAttributesToCapture.Add(DamageStatics().BaseDamageDef);
}
```

*如上方代码所示，我们初始化的三个参数的意思分别为：*

1. *USCombatSet::GetBaseDamageAttribute()：到我们的这个AttricuteSet去拿一个 Attricute*
2. *EGameplayEffectAttributeCaptureSource::Source：是从Source拿 不是从Target拿*
3. *True: 是否拍快照，这个基本都是true，要不然你的数据可能会被污染*

*然后我们放在RelevantAttributesToCapture里，通过一下方式去拿：*

```cpp
FAggregatorEvaluateParameters EvaluateParameters;
EvaluateParameters.SourceTags = SourceTags;
EvaluateParameters.TargetTags = TargetTags;

float BaseDamage = 0.0f;
ExecutionParams.AttemptCalculateCapturedAttributeMagnitude(DamageStatics().BaseDamageDef, EvaluateParameters, BaseDamage);
```

*我们最后通过拿到准确数据后就塞到数据里 返回回去：*

```cpp
OutExecutionOutput.AddOutputModifier(FGameplayModifierEvaluatedData(USHealthSet::GetDamageAttribute(), EGameplayModOp::Additive, DamageDone));
```

*Lyra是通过WeaponInstance计算好具体的倍数，然后继承了一个接口，送到这里来的*

*最后别忘记要在GE把这个Executions设置进去哦*

### ***应用AttributeSet***

*好了 这下数据都来AttributeSet了，我们就要修改角色身上的Attribute了，这Set也有几个很关键的接口*

```cpp
virtual bool PreGameplayEffectExecute(FGameplayEffectModCallbackData& Data) override;
virtual void PostGameplayEffectExecute(const FGameplayEffectModCallbackData& Data) override;

virtual void PreAttributeBaseChange(const FGameplayAttribute& Attribute, float& NewValue) const override;
virtual void PreAttributeChange(const FGameplayAttribute& Attribute, float& NewValue) override;
virtual void PostAttributeChange(const FGameplayAttribute& Attribute, float OldValue, float NewValue) override;
```


