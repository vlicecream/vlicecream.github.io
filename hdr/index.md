# SDR与HDR


## ***HDR的介绍***

1. *更宽的色域*

2. *更高的峰值亮度*

### *HDR10*

*HDR10 是现在最常用的 HDR 的标准，他是采用PQ曲线，Rec 2020 色彩空间，强制要求使用 **10-bit** 色深，峰值亮度为 1000nits 甚至更高*

*他同时还有一些元数据，这些数据有的显示器使用，有的没有*

1. *MaxFall*

   *最大帧平均亮度*

2. *MaxCall*

   *最大内容亮度*

## ***SDR的介绍***

1. *色域有限*
2. *较低的峰值亮度*

*SDR的 Nit 值为100以内，色彩还原比HDR要更低*

## ***OETF & EOTF***

*OETF 指的是光电转换函数。 摄像机利用光电转换器将光信号转换为电信号*

*EOTF 与 OETF 相反，它指的是“电光传输函数”。 电视显示器通过 EOTF 来计算输出光的功率*

## ***宽色域 (Wide Color Gamut)***

*色域范围比传统的 sRGB 更宽的叫做宽色域*

### ***sRGB***

![sRGB](https://raw.githubusercontent.com/vlicecream/cloudImage/main/image-20251013233217806.png)

*sRGB的色域范围很窄*

### ***Adobe RGB***

*这是 Adobe RGB 色彩空间。*

*最流行的宽色域。这种色域仅在绿色方面比 sRGB 更宽。但有很多设备都支持这种色域。所以这种色域将是很好的第一步。*

![Adobe RGB](https://raw.githubusercontent.com/vlicecream/cloudImage/main/image-20251013233341045.png)

### ***BT.2020 (Rec.2020)***

*这是 BT.2020 色域。*

*一系列标准的高动态范围电视。 它有不同的名称，比如国际电信联盟无线电通信部门的推荐标准 BT.2020，或者简称为 Rec.2020，但其实意思是一样的。*

*所有原色都在色谱线上。 这意味着这种效果在实际的显示器上很难实现。 所以到了 2018 年，还没有任何电视显示器能够实现 100% 的这种色域覆盖范围。*

![BT.2020 (Rec.2020)](https://raw.githubusercontent.com/vlicecream/cloudImage/main/image-20251013233523175.png)

### ***DCI-P3***

*广泛用于电影分级，iphone一直在用的色域，现在有些显示器也在用*

![DCI-P3](https://raw.githubusercontent.com/vlicecream/cloudImage/main/image-20251013233739812.png)

### ***ACEScg***

*ACES是学院色彩编码系统，用于渲染与合成，所以游戏很多会用*

*ACEScg 又称 ACES AP1*

![ACEScg](https://raw.githubusercontent.com/vlicecream/cloudImage/main/image-20251013233815162.png)

### ***ACES2065-1***

*该色域设计与存储图像 覆盖了整个光谱，所以会存在色彩精度丢失情况*

*ACES2065-1又称ACES AP0*

![ACES2065-1](https://raw.githubusercontent.com/vlicecream/cloudImage/main/image-20251013233934646.png)

## ***Tone Mapping Variations***

### ***ACES RRT + ODT***

*他是一个基于富士胶片的胶片特性*

*RRT 就是 参考渲染变换，他将屏幕线性颜色转换成胶片颜色*

*ODT 就是 输出设备变换，他将屏幕颜色转换成设备信号*

*优点*

1. *专业设计*

*缺点*

1. *ODT只支持 1000/2000/4000尼特显示设备*

## ***CIE XYZ***

***CIE**（国际照明委员会）*

***CIE XYZ 是一个“设备无关”的“主”色彩空间，它用数学方式定义了普通人眼能看见的所有颜色。***

*它是所有色彩空间转换的**中间人**。当你想把一张P3色域的图片转换到sRGB色域时，标准流程是：**P3 -> CIE XYZ -> sRGB***

