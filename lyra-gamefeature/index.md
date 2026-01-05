# Lyra - Game Feature


## ***什么是GameFeature***

*Game Feature（游戏特性）是虚幻引擎中一个强大的插件系统，旨在实现高度模块化和可扩展的游戏开发。*

*想象一下，你可以像搭乐高积木一样构建你的游戏：角色的能力、新的游戏模式、季节性活动，甚至UI界面，都可以作为一个独立的“特性”插件来开发。当需要时，只需动态地激活这个插件，所有相关的功能、内容和逻辑就会自动应用到游戏中；不需要时，则可以随时停用，干净利落地移除，而不会影响游戏的核心代码。*

*这种方式极大地降低了团队协作的耦合度，让大型项目管理变得前所未有的清晰和高效。*

*简单理解就可以理解为游戏Mod把，加载了就有这块功能，卸载了就没有这块功能*

## ***创建一个GameFeature插件***

1. *打开GameFeature插件*

   ![image-20250715233759648](https://raw.githubusercontent.com/vlicecream/cloudImage/main/image-20250715233759648.png)

2. *在插件页面 点击 Add(添加) 根据是否需要c++ 创建GameFeature*

   ![image-20250715233911414](https://raw.githubusercontent.com/vlicecream/cloudImage/main/image-20250715233911414.png)

3. *他会帮你默认创建一个 GameFeature的 PrimaryDataAsset，写好你需要的即可*

4. *我们一定要打开项目设置查看下，这个是否加入，没有得补上去*

   ![image-20250715234304112](https://raw.githubusercontent.com/vlicecream/cloudImage/main/image-20250715234304112.png)

5. *这样的话我们就创建好了，记得在插件设置里面启动你刚刚创建好的插件*

## ***UGameFeatureData***

*在你创建好一个Feature后就会有该DataAsset，在里面填入这个GameFeature的数据*

### ***FeatureState***

* *Initial State: 显示当前插件状态，如果你在运行游戏的时候激活了，然后打开此DataAsset就会发现他变成了Active。后面的EditPlugin是可以让你修改此插件信息，比如插件信息，以及还有此插件依赖的其他插件*
* *Current State: 插件当前状态，可以手动设置默认状态*
* *GameplayTagConfigPath: 该插件的指定加载或存放 Gameplay Tags 的特定配置文件（.ini）路径。*

### ***GameFeature***

* *Actions: `UGameFeatureAction`类，专门用于在加载GameFeature的时候做的额外操作，比如 Add Component, 在加载的时候给类加上Component*
* *AssetManager：`FPrimaryAssetTypeInfo`结构体， 定义了规则、配置和源数据结构，用于告诉引擎如何识别、扫描、分配以及打包游戏中的各类资源*

## ***UGameFeatureAction***

*`UGameFeatureAction` 是你在加载 Gamefeature的时候可以顺便干的事，比如你可以给主角或者其他类 Add Componment，也可以自定义给主角添加特定能力(Ability)*

### ***重要接口***

1. `virtual void OnGameFeatureRegistering() override;`

   *当虚幻引擎启动并发现这个 Game Feature 插件时，会立即调用。*

2. `virtual void OnGameFeatureUnregistering() override;`

   *引擎关闭的时候，会立刻调用*

3. `virtual void OnGameFeatureActivating(FGameFeatureActivatingContext& Context) override; `

   *当游戏逻辑通过调用 UGameFeaturesSubsystem::Get().ActivateGameFeaturePlugin() 来**主动激活**这个插件时触发。这通常是游戏流程的一部分*

4. `virtual void OnGameFeatureDeactivating(FGameFeatureDeactivatingContext& Context) override; `

   *当游戏逻辑通过调用 UGameFeaturesSubsystem::Get().DeactivateGameFeaturePlugin() 来**主动停用**这个插件时触发*

## ***FPrimaryAssetTypeInfo***

*它不包含复杂的逻辑，而是定义了规则、配置和源数据结构，用于告诉引擎如何识别、扫描、分配以及打包游戏中的各类资源。*

### ***FPrimaryAssetTypeInfo***

*用于在项目设置中定义资源类型的一种扫描规则*

* *PrimaryAssetType：名称*
* *AssetBaseClass：选定这个资源类型的基类*
* *HasBlueprintClasses：如果是`true`，则也扫描该类的蓝图。反之，则只扫描 c++ 的实例资源*
* *IsEditorOnly：如果为`true`，则该资产永远不会被烘焙*
* *Directorires：资源所在的文件夹*
* *SpecificAssets：直接指定某些特定的资源路径，而不通过文件夹扫描*
* *Rules：`FPrimaryAssetRules`结构体，定义资源管理的核心规则*

### ***FPrimaryAssetRules***

*这是定义资源管理的核心规则*

* *Priority：当一个次要资源(如贴图)被多个主资源引用时，优先级绝对谁拥有它。高优先级会覆盖低优先级。如果优先级一样，共同管理*
* *ChunkId：用于分包下载或补丁管理。如果设置为0以上的，该资源及其依赖项会被放入指定的 `.pak`块中*
* *bApplyRecursively：如果为`true`，该规则不仅影响主资源本身，还会自动应用到它所引用的所有次要资源（除非那些资源被更高优先级的规则管理）*
* *CookRule：EPrimaryAssetCookRule 枚举，这个枚举定义了一个主资源在打包过程中的行为。它决定了资源是在开发环境还是生成环境中被包含。*

### ***EPrimaryAssetCookRule***

*`FPrimaryAssetRules`：这个枚举定义了一个主资源在打包过程中的行为。它决定了资源是在开发环境还是生成环境中被包含*

* *Unknown: 默认值。如果没有特殊规定，只要被其他资源引用，它就会被烘焙。*
* *NeverCook：绝对不烘焙，如果有引用该资源，直接报错*
* *ProductionNeverCook：如果有其他东西依赖于资产，资产将在开发中被烘焙，但永远不会在生产构建中被烘焙。*
* *DevelopmentAlwaysProductionNeverCook: 资产永远在开发的时候烘焙，即使没被引用。永远不会在生产构件中被烘焙*
* *DevelopmentAlwaysProductionUnknownCook：资产永远在开发中被烘焙；关于它是否应该在生产中烘焙尚不清楚。如果有其他东西依赖于它，它会在生产中煮熟。*
* *AlwaysCook：资产无论在什么环境都会被烘焙*

## ***注意***

1. *可以主项目引用 GameFeature 里的资源 但是编译会报 Error Log，所以不推荐这么做，因为你用 GameFeature 的资源的时候，这个GameFeature可能未被加载。故只能GameFeature引用主项目这块*

