# Lyra-GAS


## ***Lyra GAS的流程***

### ***GAS 与 Input 调用***

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

### ***技能的装备（GiveAbility）***

*举个例子， 比如是捡到枪，随后鼠标左键开火*

*捡到枪并且装备是通过 Lyra 的 Equipment系统和Inventory系统来实现的，此时有一个ULyraEquipmentDefinition 中会记录一个 ULyraAbilitySet，里面有见到这个枪就会装备什么GameAbility技能，以及GameEffect效果，还有AttributeSet属性数值，这时候就会将GameAbility技能给GiveAbility装备上去*

