# Lyra 库存与装备系统


## ***Equipment***

*Equipment 是 Lyra 的装备系统，也就是执行装备这个过程逻辑的一个系统*

*它通过 `ULyraEquipmentDefinition` 这个类，来获得装备的Actor真正的实例，以及装备上会获得什么技能，还有装备到哪里，比如枪装备到人的一个左手骨骼点上*

## ***Inventory***

*Inventory 是Lyra的库存系统，也可以理解为背包系统，然后管理着一个Actor身上所有的物品*

*它通过 `ULyraInventoryItemFragment` 的各种子类，来获得相对应的数据，比如 ULyraEquipmentDefinition*

