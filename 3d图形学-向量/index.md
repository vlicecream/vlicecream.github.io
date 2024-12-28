# 向量


# ***向量***

## ***标量与向量***

### ***标量***

*标量有大小 可以是正负，但是他没有方向*

### ***向量***

#### ***介绍***

*向量最基本的定义就是一个方向。或者更正式的说，向量有一个方向(Direction)和大小(Magnitude，也叫做强度或长度)*

*我们通常只使用 2至 4维。如果一个向量有2个维度，它表示一个平面的方向(想象一下2D的图像)，当它有3个维度的时候它可以表达一个3D世界的方向*

#### ***数学公式***

![sss](https://raw.githubusercontent.com/CuteCocoa/MyImage/main/2024/12/24-10-14-55-2024-12-24-10-07-20-image.png)

#### ***图示***

*你可以把这些2D向量当做z坐标为0的3D向量。由于向量表示的是方向，起始于何处并不会改变它的值。下图我们可以看到向量 $\vec{v}$ 和 $\vec{w}$ 是相等的，尽管他们的起始点不同*

![](https://raw.githubusercontent.com/CuteCocoa/MyImage/main/2024/12/24-09-55-57-2024-12-24-09-55-52-image.png)

#### ***代码***

1. ***2D向量代码***
   
   ```cpp
   struct f_vector_2d
   {
       f_vector_2d(const float in_x, const float in_y);
   
   private:
       float x;
       float y;
   };
   ```

2. ***3D向量代码***
   
   ```cpp
   struct f_vector_3d
   {
       f_vector_3d(float in_x, float in_y, float in_z);
   private:
       float x;
       float y;
       float z;
   };
   ```

3. ***4D向量代码***

## ***向量与标量运算***

### ***介绍***

*标量(Scalar)只是一个数字（或者说是仅有一个分量的向量）。当把一个向量加/减/乘/除一个标量，我们可以简单的把向量的每个分量分别进行该运算*

***注意，数学上是没有向量与标量相加这个运算的，但是很多线性代数的库都对它有支持（比如说我们用的GLM）。如果你使用过numpy的话，可以把它理解为[Broadcasting](https://numpy.org/doc/1.18/user/basics.broadcasting.html)。***

### ***数学公式***

*其中的+可以是+，-，·或÷，其中·是乘号。注意－和÷运算时不能颠倒（标量-/÷向量），因为颠倒的运算是没有定义的。*

![](https://raw.githubusercontent.com/CuteCocoa/MyImage/main/2024/12/24-10-14-52-2024-12-24-10-01-53-image.png)

### ***代码***

1. ***2D向量代码***
   
   ```cpp
   struct f_vector_2d
   {
       f_vector_2d(const float in_x, const float in_y);
   
       // 向量与标量 运算
       f_vector_2d operator+ (const float f) const
       {
           return f_vector_2d(x + f, y + f);
       }
   
       f_vector_2d operator- (const float f) const
       {
           return f_vector_2d(x - f, y - f);
       }
   
       f_vector_2d operator* (const float f) const
       {
           return f_vector_2d(x * f, y * f);
       }
   
       f_vector_2d operator/ (const float f) const
       {
           return f_vector_2d(x / f, y / f);
       }
   
   private:
       float x;
       float y;
   }; f, y / f);
       }
   ```

2. ***3D向量代码***

3. ***4D向量代码***

## ***向量取反***

### ***简介***

*对一个向量取反(Negate)会将其方向逆转。一个指向东北的向量取反后就指向西南方向了。我们在一个向量的每个分量前加负号就可以实现取反了（或者说用-1数乘该向量）*

### ***数学公式***

![](https://raw.githubusercontent.com/CuteCocoa/MyImage/main/2024/12/24-10-14-49-2024-12-24-10-14-46-image.png)

### ***代码***

1. ***2D向量代码***
   
   ```cpp
   f_vector_2d operator~ (const f_vector_2d& v) const
   {
       return f_vector_2d(v.x * -1, v.y * -1);
   }
   ```

2. ***3D向量代码***

3. ***4D向量代码***

## ***向量的加法***

### ***介绍***

*向量的加法可以被定义为是分量的(Component-wise)相加，即将一个向量中的每一个分量加上另一个向量的对应分量*

### ***数学公式***

![](https://raw.githubusercontent.com/CuteCocoa/MyImage/main/2024/12/24-10-22-25-2024-12-24-10-22-22-image.png)

### ***图示***

*向量`v = (4, 2)`和`k = (1, 2)`可以直观地表示*

![](https://raw.githubusercontent.com/CuteCocoa/MyImage/main/2024/12/24-10-23-08-2024-12-24-10-23-06-image.png)

### ***代码***

1. ***2D向量代码***
   
   ```cpp
   f_vector_2d operator+ (const f_vector_2d& v) const
   {
       return f_vector_2d(x + v.x, x + v.y);
   }
   ```

2. ***3D向量代码***

3. ***4D向量代码***

## ***向量的减法***

### ***介绍***

*就像普通数字的加减一样，向量的减法等于加上第二个向量的相反向量*

### ***数学公式***

![](https://raw.githubusercontent.com/CuteCocoa/MyImage/main/2024/12/24-10-25-38-2024-12-24-10-25-34-image.png)

### ***图示***

*两个向量的相减会得到这两个向量指向位置的差。这在我们想要获取两点的差会非常有用*

![](https://raw.githubusercontent.com/CuteCocoa/MyImage/main/2024/12/24-10-26-11-2024-12-24-10-26-09-image.png)

### ***代码***

1. ***2D向量代码***
   
   ```cpp
   f_vector_2d operator- (const f_vector_2d& v) const
   {
       return f_vector_2d(x - v.x, y - v.y);
   }
   ```

2. ***3D向量代码***

3. ***4D向量代码***

## ***向量的长度（模）***

### ***介绍***

*我们使用勾股定理(Pythagoras Theorem)来获取向量的长度(Length)/大小(Magnitude)。如果你把向量的 x与y分量画出来，该向量会和x与y分量为边形成一个三角形*

### ***数学公式***

*因为两条边（ x和y ）是已知的，如果希望知道斜边$\vec{v}$的长度，我们可以直接通过勾股定理来计算*

![](https://raw.githubusercontent.com/CuteCocoa/MyImage/main/2024/12/24-10-44-13-2024-12-24-10-44-11-image.png)

*||$\vec{v}$||表示向量 $\vec{v}$的长度，我们也可以加上 z<sup>2</sup> 把这个公式拓展到三维空间。例子中向量( 4, 2 )的长度等于*

![](https://raw.githubusercontent.com/CuteCocoa/MyImage/main/2024/12/24-10-45-24-2024-12-24-10-45-19-image.png)

### ***图示***

![](https://raw.githubusercontent.com/CuteCocoa/MyImage/main/2024/12/24-10-43-44-2024-12-24-10-43-41-image.png)

### ***代码***

1. ***2D向量***
   
   ```cpp
   #include <cmath> 
   float size() const
   {
       return std::sqrt(x * x + y * y);
   }
   ```

## ***单位向量***

### ***介绍***

*有一个特殊类型的向量叫做单位向量(Unit Vector)。单位向量有一个特别的性质——它的长度是1。我们可以用任意向量的每个分量除以向量的长度得到它的单位向量n^*

### ***数学公式***

*我们把这种方法叫做一个向量的标准化(Normalizing)。单位向量头上有一个^样子的记号。通常单位向量会变得很有用，特别是在我们只关心方向不关心长度的时候（如果改变向量的长度，它的方向并不会改变）。*

![](https://raw.githubusercontent.com/CuteCocoa/MyImage/main/2024/12/24-10-47-40-2024-12-24-10-47-38-image.png)

## ***向量的相乘***

### ***介绍***

*两个向量相乘是一种很奇怪的情况。普通的乘法在向量上是没有定义的，因为它在视觉上是没有意义的。但是在相乘的时候我们有两种特定情况可以选择：一个是点乘(Dot Product)，记作  $\vec{v} ⋅ \vec{k}$，另一个是叉乘(Cross Product)，记作  $\vec{v} x \vec{k}$。*

### ***点乘***

#### ***介绍***

*两个向量的点乘等于它们的数乘结果乘以两个向量之间夹角的余弦值。可能听起来有点费解，我们来看一下公式*

![](https://raw.githubusercontent.com/CuteCocoa/MyImage/main/2024/12/24-10-56-24-2024-12-24-10-56-22-image.png)

*它们之间的夹角记作 θ。为什么这很有用？想象如果  $\vec{v}$ 和  $\vec{k}$ 都是单位向量，它们的长度会等于1。这样公式会有效简化成*

![](https://raw.githubusercontent.com/CuteCocoa/MyImage/main/2024/12/24-10-57-05-2024-12-24-10-57-03-image.png)

***现在点积只定义了两个向量的夹角。你也许记得 90度的余弦值是0，0度的余弦值是1。使用点乘可以很容易测试两个向量是否正交(Orthogonal)或平行（正交意味着两个向量互为直角）***

![](https://raw.githubusercontent.com/CuteCocoa/MyImage/main/2024/12/24-10-58-11-2024-12-24-10-58-08-image.png)

#### ***数学公式***

*所以，我们该如何计算点乘呢？点乘是通过将对应分量逐个相乘，然后再把所得积相加来计算的。两个单位向量的（你可以验证它们的长度都为1）点乘会像是这样*

![](https://raw.githubusercontent.com/CuteCocoa/MyImage/main/2024/12/24-10-59-26-2024-12-24-10-59-24-image.png)

*要计算两个单位向量间的夹角，我们可以使用反余弦函数cos−1 ，可得结果是143.1度。现在我们很快就计算出了这两个向量的夹角。点乘会在计算光照的时候非常有用*

### ***叉乘***

#### ***介绍***

*叉乘只在3D空间中有定义，它需要两个不平行向量作为输入，生成一个正交于两个输入向量的第三个向量。如果输入的两个向量也是正交的，那么叉乘之后将会产生3个互相正交的向量。下面的图片展示了3D空间中叉乘的样子*

![](https://raw.githubusercontent.com/CuteCocoa/MyImage/main/2024/12/24-11-01-18-2024-12-24-11-00-29-image.png)

#### ***数学公式***

![](https://raw.githubusercontent.com/CuteCocoa/MyImage/main/2024/12/24-11-01-22-2024-12-24-11-01-14-image.png)

## ***正交投影***

### ***推导***

*参考下图。给出向量 $\vec{v}$ 和单位向量 $\vec{n}$ , 请借助点乘公式求出用  $\vec{v}$ 和 $\vec{n}$ 表示 向量 $\vec{p}$ 得公式*

![](https://raw.githubusercontent.com/CuteCocoa/MyImage/main/2024/12/28-17-26-36-2024-12-28-17-26-31-1735377979278.png)



1. *首先观察图示可以得知存在一标量 **k** , 使得*
   
   *$\vec{p} = k\vec{n}$ ;*

2. *P得模 又可以用这个公式算出来* 
   
   *$||p|| = |k| * ||n||$ ;*

3. *因为 我们假设 $\vec{n}$ 是单位向量*
   
   *$||n|| = 1$*

4. *然后推导出了* 
   
   *$||p|| = |k| $*

5. *注意这个 k 可能是负数，当且仅当 $\vec{p}$ 与 $\vec{n}$ 方向相反*

6. *利用三角函数*
   
   *$ cosθ = \frac{k}{||v||} $*
   
   *$k = ||v||cosθ$*
   
   *$\vec{p} = k\vec{n} = (||v||cosθ)\vec{n}$*

7. *由于 $\vec{n}$ 是单位向量，所以*
   
   *$\vec{p} = k\vec{n} = (||v|| \cdot cosθ)\vec{n} = (||v|| \cdot 1cosθ)n = (||v|| \cdot ||n|| \cdot cosθ)n$*

8. *由于 $||v|| \cdot ||n|| cosθ = (\vec{v} \cdot \vec{n})$*
   
   *$\vec{p} = (||v|| \cdot ||n|| \cdot cosθ)\vec{n} = (\vec{v} \cdot \vec{n})\vec{n}$*

9. *这里证明了，当 $\vec{n}$ 是单位向量时，$k = \vec{v} \cdot \vec{n}$，顺带也解释了这种情况下 $\vec{v} \cdot \vec{n}$ 得几何意义 我们称 $\vec{p}$ 为向量 v 落在向量 n 上得**正交投影** 通常将他表示为：*
   
   $\vec{p} = proj_n(\vec{v})$

10. *如果将 $\vec{v}$看作是一个力，便可认为 $\vec{p}$ 是力 $\vec{v}$ 在方向 $\vec{n}$ 上得分力*

11. *同理，$\vec{w} = prep_n(\vec{v}) = \vec{v} - \vec{p}$ 是作用力 $\vec{v}$ 在 $\vec{n}$ 正交方向上得分力（$prep_n(\vec{v})$ 表示垂直）*

12. *所以我们观察到 $\vec{v} = \vec{p} + \vec{w} = proj_n(\vec{v}) + perp_n(\vec{b})$ ，这说明了可以将 $\vec{v}$ 分解成两个互相正交得 $\vec{p}$ 与 $\vec{w}$ 之和*
    
    1. *如果 $\vec{n}$ 不具有单位长度，就先对他进行规范化处理，让它成为单位向量。通过把 $\vec{n}$ 替换为单位向量 $\frac{\vec{n}}{||n||}$ ，就可以得到更具一般性的投影公式*
       
       *$\vec{p} = proj_n(\vec{v}) = (\vec{v} \cdot \frac{\vec{n}}{||n||}) \frac{\vec{n}}{||n||} = \frac{(\vec{v}) \cdot \vec{n}}{||n||^2} \vec{n} $*

## ***缩放***

*对一个向量进行缩放(Scaling)就是对向量的长度进行缩放，而保持它的方向不变。由于我们进行的是2维或3维操作，我们可以分别定义一个有2或3个缩放变量的向量，每个变量缩放一个轴(x、y或 z)。*

*我们先来尝试缩放向量 $\vec{v}$ =(3,2)。我们可以把向量沿着x轴缩放0.5，使它的宽度缩小为原来的二分之一；我们将沿着y轴把向量的高度缩放为原来的两倍。我们看看把向量缩放(0.5, 2)倍所获得的 $\vec{s}$ 是什么样的*

![](https://raw.githubusercontent.com/CuteCocoa/MyImage/main/2024/12/24-13-38-06-2024-12-24-13-38-04-image.png)

*对于 2D的情况我们可以把z轴缩放 1倍，这样 z轴的值就不变了。我们刚刚的缩放操作是不均匀(Non-uniform)缩放，因为每个轴的缩放因子(Scaling Factor)都不一样。如果每个轴的缩放因子都一样那么就叫均匀缩放(Uniform Scale)*

## ***位移***

*位移(Translation)是在原始向量的基础上加上另一个向量从而获得一个在不同位置的新向量的过程，从而在位移向量基础上**移动**了原始向量。我们已经讨论了向量加法，所以这应该不会太陌生*

## ***齐次坐标***

*向量的w分量也叫齐次坐标。想要从齐次向量得到 3D向量，我们可以把x、y和 z坐标分别除以w坐标。我们通常不会注意这个问题，因为w分量通常是1.0。使用齐次坐标有几点好处：它允许我们在3D向量上进行位移（如果没有w分量我们是不能位移向量的）*

***如果一个向量的齐次坐标是0，这个坐标就是方向向量(Direction Vector)，因为w坐标是0，这个向量就不能位移***

## ***旋转***

*2D或3D空间中的旋转用角(Angle)来表示。角可以是角度制或弧度制的，周角是360角度或2 [PI](https://en.wikipedia.org/wiki/Pi)弧度。我个人更喜欢用角度，因为它们看起来更直观*

*大多数旋转函数需要用弧度制的角，但幸运的是角度制的角也可以很容易地转化为弧度制的：*

- *弧度转角度：`角度 = 弧度 * (180.0f / PI)`*
- *角度转弧度：`弧度 = 角度 * (PI / 180.0f)`*

*`PI`约等于3.14159265359。*

*在3D空间中旋转需要定义一个角**和**一个旋转轴(Rotation Axis)。物体会沿着给定的旋转轴旋转特定角度。如果你想要更形象化的感受，可以试试向下看着一个特定的旋转轴，同时将你的头部旋转一定角度。当2D向量在3D空间中旋转时，我们把旋转轴设为z轴（尝试想象这种情况）。*

*使用三角学，给定一个角度，可以把一个向量变换为一个经过旋转的新向量。这通常是使用一系列正弦和余弦函数（一般简称 sin和cos）各种巧妙的组合得到的*

