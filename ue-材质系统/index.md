# 材质基础


## ***什么是材质***

*简单地说就是物体看起来是什么质地，材质可以看成是材料和质感的结合*

*在Ai渲染程式中，他是表面各可视属性的结合。这些可视属性是指表面的色彩、纹理、光滑度、透明度、反射率、折射率、发光度等* 

## ***模型制作渲染流程***

*Maya Max3d 的流程基本都是 制作模型 -> 拆分UV -> 添加灯光 -> 制作材质 -> 渲染 -> 合成*

## ***UE4中的材质***

1. *UE4中的材质是什么作用*
   - *可以应用到网格物体上的资源，用它可控制场景的可视外观。当穿过场景的光照接触到表面后，材质被用来 **计算该光照如何与该表面进行互动**。这些计算是通过对材质的输入数据来完成的，而这些输入数据来自于一系列图像(贴图)以及数学表达式，以及材质本身所继承的不同属性设置*
2. *UE4中的材质是什么*
   - *UE4中的材质是基于物理的材质(PVR)：Unreal4使用了基于物理的着色器模型。这意味着不再使用数学模型中的某个参数属性（比如漫反射颜色和高光次幂）定义一个材质，而是使用和现实世界更加相关的属性定义材质。这些属性包括底色、金属色、高光及粗糙度*
3. *UE4中的材质跟普通的材质有什么区别*
   - *普通的材质是基于数学逻辑 / UE4中的材质是基于物理逻辑*
   - *UE4这样会更加接近现实世界的质感*

## ***材质编辑页面UI功能***

- *菜单栏：列出当前材质的菜单选项*
- *工具栏：含材质使用工具*
- *视口面板：预览材质在网格体上的效果*
- *细节面板：列出材质、所选材质表现或函数节点的属性*
- *图标面板：显示创建材质着色器指令的材质表现和函数节点*
- *控制板（调色板面板）：列出所有材质表现和函数节点*
- *更加详细的可以参考官方文档：http://api.unrealengine.com/CHN/Engine/Rendering/Materials/Editor/Interface/index.html#%E5%B7%A5%E5%85%B7%E6%A0%8F*

![image-20230331201340469](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data%5Cimage-20230331201340469.png)

## ***快捷键属性***

![image-20230402210038896](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data/202304022100386.png)

## ***材质类型控制属性***

![image-20230331224025089](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data%5Cimage-20230331224025089.png)

![image-20230402210647088](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data/202304022106478.png)

![image-20230402210657985](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data/202304022106854.png)

![image-20230402210713277](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data/202304022107360.png)

*更多内容请参考官方文档：http://api.unrealengine.com/CHN/Engine/Rendering/Materials/MaterialInputs/index.html*

## ***材质输入属性***

### ***基础材质节点图表概述***

![image-20230331205231530](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data%5Cimage-20230331205231530.png)

### ***Base Color 基础颜色***

1. *作用*
   - *没有任何光照的颜色*
2. *使用*
   - *1 代表白色 / 0 代表黑色 / 中间值代表比例灰色*
   - *也可以直接使用 RGB 或者 依靠 TextureSample节点贴图*

### ***Metallic 金属色***

1. *作用*
   - *让材质更接近金属感觉*
2. *使用*
   - *1 代表金属质感 / 0 代表无金属质感 / 中间值代表比例过渡*

### ***Roughness 粗糙度***

1. *作用*
   - *决定材质的粗糙度*
2. *使用*
   - *1 代表粗糙质感 - 完全没有光泽或者漫射 / 0 代表平滑质感 - 镜面反射 / 中间值代表比例过渡*

### ***Specular 高光***

1. *作用*
   - *高光，用于调整非金属的表面上的镜面反射量，在金属上是没有效果的*
2. *使用*
   - *取值范围0 ~ 1，默认值 0.5*

### ***Emissive Color 自发光颜色***

1. *作用*
   - *控制材质的哪些部分将发光*
2. *使用*
   - *通过RGB 还有 Multiply节点*
   - *注意配合 Multiply 的时候，只适用于这个 Emissive Color*

***Normal 法线***

1. *作用*
   - *通过发现图将每个单独像素的法线或面向方向打乱，为表面提供重要的物理细节*

### ***World Position Offset 世界位置偏移***

1. *作用*
   - *使得网格物体的顶点可以由材质在世界空间内进行控制*
   - *比如可以使我们目标可以移动，或者改变形状，旋转，甚至还可以写环境动画*
2. *使用*
   - *可以配合Time、VertexNormalWS节点来使用*

### ***Ambient Occlusion 环境遮挡***

1. *作用*
   - *用来模拟在表面缝隙中发生的自我遮蔽*
2. *使用*
   - *一般和环境遮挡贴图（OCC）配合使用*

### ***Pixel  Depth Offset 像素深度偏移***

1. *作用*
   - *可以将像素的深度沿着摄像机到该像素的方向推一个值*
2. *使用*
   - *给一个值即可*

### ***Opacity Mask 不透明遮罩***

1. *作用*
   - *表现材质效果，要么完全可见，要么完全不可见*
   - *比如铁丝网，围栏等*
   - ***只有当混合模式为Masked才可用***

### ***Opacity 不透明度***

1. *作用*
   - *用来表现半透明度*
2. *使用*
   - *0 代表完全透明 / 1 代表不透明 / 中间值代表半透明*

### ***World Displacement & Tessellation Multipiller***

1. *World Displacement 世界场景位置*
   - *和世界位置偏移类似，但是使用曲面细分顶点而不是网格的基本顶点，使曲面细分顶点可以由材质在世界空间内进行控制*
2. *Tessellation Multiplier 曲面细分乘数*
   - *控制表面的曲面细分量，与允许在需要的地方添加更多的细节*

### ***Subsurface Color 子表面颜色***

1. *作用*
   - *可以用来模拟光照通过表面时的颜色的变化*
   - *只有当 shading model 设置为subsurface时才可以使用*

### ***Clear Coat & Clear Coat Roughness***

1. *Clear Coat 透明图层*
   - *可以用来模拟多层材料，这些材料的表面有一层薄薄的半透明薄膜*
   - *只有在 shading model 设置为 clear coat才可以使用*
   - *0 默认光照模式 / 1 完全透明图层模式*
2. *Clear Coat Roughness 透明图层粗糙度*
   - *一般取比较小的值*
   - *可以用来模拟车漆等*

### ***Refraction 折射***

1. *作用*
   - *用来模拟表明的折射率*
   - *可以应用于水、玻璃*

## ***常用重要节点***

### ***TextureSample节点***

1. *作用*
   - *纹理取样*
   - *输出纹理中的颜色值*
2. *引脚介绍*
   - *UVs*
   - *Tex：通过 Texture Object节点配合使用来控制贴图，优先级最高，在细节面板也是可以设置贴图，但是相比优先级较低*

### ***Add 节点***

1. *作用*

   - *加法，具体作用请看图示*

2. *图示*

   ![image-20230402210540727](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data/202304022105763.png)

   ![image-20230402210600129](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data/202304022106486.png)

### ***Multiply 节点***

1. *作用*
   
   - *乘法，RGB每个通道分别相乘*
   
1. *图示*
   
   ![image-20230402210319779](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data/202304022103933.png)
   
   ![image-20230402210339765](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data/202304022103173.png)

### ***Time 节点***

1. *作用*
   - *时间节点，用来像材质或相关操作添加经历时间*

### ***VertexNormalWS 节点***

1. *作用*
   - *顶点法线方向节点，输出世界场景空间顶点法线*

### ***Lerp 节点***

1. *作用*
   
   - *线性插值*
   - *（A(1-alpha) + B(1+alpha)）*
   
1. *图示*
   
   ![image-20230402210226711](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data/202304022102184.png)

## ***材质参数化***

*场景：如果多个材质只有粗糙度不一样，其他一致，我们就要创建多个材质，并且进行连线吗?*

*解决：可以将粗糙度的值提升为变量参数，这样就可以实例化他的子类，然后输入我们想要修改的粗糙度即可*

## ***材质函数***

1. *什么是材质函数*
   - *材质图的一些小片段，他们可以保存在包中，并在多个材质之间复用*
