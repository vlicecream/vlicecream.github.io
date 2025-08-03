# Lyra 库存与装备系统


## ***Equipment***

*Equipment 是 Lyra 的装备系统，也就是执行装备这个过程逻辑的一个系统*

*它通过 `ULyraEquipmentDefinition` 这个类，来获得装备的Actor真正的实例，以及装备上会获得什么技能，还有装备到哪里，比如枪装备到人的一个左手骨骼点上*

### ***ULyraEquipmentDefinition***

*此类就是装备的一个数据结构，你需要提供一个Instance，还有一个装备上会有什么技能，比如装备一个剑，装备上了就会有一把剑的剑光技能，以及还有装备到人身上那个部位，比如剑装备到骨骼体上面*

### ***ULyraEquipmentInstance***

*此类就是装备的一个实例，比如武器就有枪和剑两种，枪是有根据距离，散射程度来进行伤害衰减的，所以就可以继承此基类，去实现，Lyra也就是这么做的，装备以后就可以从各种各样的枪上面拿到伤害数据*

### ***ULyraEquipmentManagerComponent***

*此Component就是所有的装备到身上的一个管理者了，也是向外部提供各种CURD的接口*

*Lyra里会在Gamefeature里将这个绑定在**Character**上*

#### ***ULyraQuickBarComponent***

*此Component就是游戏中的装备栏，可以通过此Component的方法快捷装备到人物身上*

*Lyra里会在Gamefeature里将这个绑定在**PlayerState**上*

## ***Inventory***

*Inventory 是Lyra的库存系统，也可以理解为背包系统，然后管理着一个Actor身上所有的物品*

*它通过 `ULyraInventoryItemFragment` 的各种子类，来获得相对应的数据，比如 ULyraEquipmentDefinition*

### ***ULyraInventoryItemDefinition***

*这个就是库存系统中库存的数据了，他的成员就是一个显示名称，以及还有基类，比如UInventoryFragment_EquippableItem就是继承于这个基类，随后UInventoryFragment_EquippableItem只需要绑定一个装备系统的数据结构体-ULyraEquipmentDefinition, 这样就可以将库存系统与装备系统串起来了*

- ***核心职责**:*
  - *定义物品的显示信息，如名称、图标。*
  - *通过一个**Fragment（碎片）列表**来组合物品的功能。一个物品可以由多个Fragment构成，例如：*
    - *UInventoryFragment_EquippableItem: 表明这是一个**可装备**的物品。这个Fragment内部持有一个对 ULyraEquipmentDefinition 的引用，**这正是将库存系统与装备系统串联起来的关键桥梁***
    - *UInventoryFragment_SetStats: 表明这个物品会提供一些基础属性（比如作为合成材料）。*
- ***与ULyraEquipmentDefinition的区别**: ULyraInventoryItemDefinition 描述的是“背包里的物品是什么”，而 ULyraEquipmentDefinition 描述的是“这个物品装备后会发生什么”。*

### ***ULyraInventoryItemInstance***

*这个类就是库存的实例，他的主要职责就是存储这个实例相对应的数据-ULyraInventoryItemDefinition，以及还有一个自定义的 GameplayTagStackContainer，这个Tag的作用就是，Unreal原生提供的只能表示一个Tag，自定义的多了一个等级，比如这个库存的1级和2级效果不一致，这个时候加入一个等级概念就很好区分*

### ***ULyraInventoryManagerComponent***

*这个Component就是库存的管理者了，它的作用就是管理全部的库存，并且对外提供各种CURD的方法*

*Lyra里会在Gamefeature里将这个绑定在**PlayerState**上*

