# 条款2-尽量不使用#define


## ***`#define`定义常量***

1. ***`#define`定义常量的缺点***

   - *`#define ASPECT_RATIO 1.653`，`#define`是不会添加到符号表的，所以当跟ASPECT_RATIO有关报错的时候，不会提示ASPECT_RATIO出了问题，不利于报错排查*

2. ***替换手段***

   - *可以拿`const double AspectRatio = 1.653;`来进行替换`#define`*

   - *在类中也可以拿static来定义"常量"*

     ```cpp
     class GamePlayer {
     private:
     	static const int NumTurns = 5;
       int scores[NumTurns];
     }
     ```

     *但是要注意 在编译的时候这个是通过的，但是如果你在后面对他有取地址之类的操作，链接就会报错，因为这里只是声明，并没有给他分配内存空间，所以还需要在cpp处理一下即可*

     ```cpp
     const int GamePlayer::NumTurns;
     ```

## ***`#define`定义宏***

1. ***`#define`定义宏的缺点***

   - *其实在inline专题有提到，`#define`定义宏，太麻烦了，只因他是直接替换的效果，最经典就是计算平方问题*

     ```cpp
     #define CALL_WITH_MAX(a, b) f((a) > (b) ? (a) : (b))
     ```

2. ***替换手段***

   - *可以拿`inline`函数去进行替换*

     ```cpp
     template<typename T>
     inline void callWithMax(const T& a, const T& b)
     {
       f(a > b ? a : b);
     }
     ```

     

