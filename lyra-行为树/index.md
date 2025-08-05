# Lyra - 行为树


## ***Lyra行为树***

*Lyra是在生成Bot的时候会传入数据 生成AIController，随后在AIController里面绑定行为树*

## ***注意事项***

1. *我们使用 EQS 中的 **Generator: Perceived Actors**的时候，必须再AIController或者AI的Pawn中加入 AIPerceptionComponent，还有对感知对方物体中加入 AIPerceptionStimuliSource，并且进行这两个感知组件的 Senses Config*

   *还有一个，这两个组件之间必须阵营不一样, 它依赖于一个接口：**IGenericTeamAgentInterface**。你需要让AI和玩家都实现这个接口，并告诉系统“我们是不同队伍的”, 或者直接将 Detection by Affiliation: **把 Detect Enemies, Detect Neutrals, Detect Friendlies 全部勾选上！** 这一步是为了暂时绕开复杂的阵营系统，看看AI到底能不能“感知”到任何东西。*

2. *还有 AI 自动导航，比如 MoveTo 需要 **NavMeshBoundsVolume**网格铺满地形，在这网格内AI才会自动导航*

