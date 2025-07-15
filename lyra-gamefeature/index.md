# Lyra架构：Game Feature 篇


## ***作用***

***Game Feature**（游戏特性）是虚幻引擎中一个强大的插件系统，旨在实现**高度模块化和可扩展的游戏开发**。*

*想象一下，你可以像搭乐高积木一样构建你的游戏：角色的能力、新的游戏模式、季节性活动，甚至UI界面，都可以作为一个独立的“特性”插件来开发。当需要时，只需**动态地激活**这个插件，所有相关的功能、内容和逻辑就会自动应用到游戏中；不需要时，则可以随时**停用**，干净利落地移除，而不会影响游戏的核心代码。*

*这种方式极大地**降低了团队协作的耦合度**，让大型项目管理变得前所未有的清晰和高效。*

*可以理解为游戏Mod把*

## ***创建一个GameFeature插件***

1. *打开GameFeature插件*

   ![image-20250715233759648](https://raw.githubusercontent.com/vlicecream/cloudImage/main/image-20250715233759648.png)

2. *在插件页面 点击 Add(添加) 根据是否需要c++ 创建GameFeature*

   ![image-20250715233911414](https://raw.githubusercontent.com/vlicecream/cloudImage/main/image-20250715233911414.png)

3. *他会帮你默认创建一个 GameFeature的 PrimaryDataAsset，写好你需要的即可*

4. *我们一定要打开项目设置查看下，这个是否加入，没有得补上去*

   ![image-20250715234304112](https://raw.githubusercontent.com/vlicecream/cloudImage/main/image-20250715234304112.png)

5. *这样的话我们就创建好了*

## ***GameFeature接口***

1. `virtual void OnGameFeatureRegistering() override;`

   *当虚幻引擎启动并发现这个 Game Feature 插件时，会立即调用。*

2. `virtual void OnGameFeatureUnregistering() override;`

   *引擎关闭的时候，会立刻调用*

3. `virtual void OnGameFeatureActivating(FGameFeatureActivatingContext& Context) override; `

   *当游戏逻辑通过调用 UGameFeaturesSubsystem::Get().ActivateGameFeaturePlugin() 来**主动激活**这个插件时触发。这通常是游戏流程的一部分*

4. `virtual void OnGameFeatureDeactivating(FGameFeatureDeactivatingContext& Context) override; `

   *当游戏逻辑通过调用 UGameFeaturesSubsystem::Get().DeactivateGameFeaturePlugin() 来**主动停用**这个插件时触发*

