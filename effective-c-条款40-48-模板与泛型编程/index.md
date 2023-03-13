# 条款40~48 模板与泛型编程


## ***条款41 隐式接口和编译器多态***

1. *什么是显式接口和运行期多态*

   *在面向对象编程世界里总是以显式接口和运行期多态解决问题，举个例子*

   ```cpp
   // 无意义的类
   class Widget{
   public:
       Widget();
       virtual Widget();
       virtual std::size_t size() const;
       virtual void normalize();
       void swap(Widget& other);
   }
   
   // 无意义的函数
   void doProcessing(Widget& w){
       if(w.size()>10 && w!= somNastyWidget){
           Widget temp(w);
           temp.normalize();
           temp.swap(w);
       }
   }
   ```

   *在上述代码中，可知*

   - *由于w的类型被声明为Widget，所以w必须支持Widget接口。我们可以在源码中找出这个接口，看看它是什么样子，所以我们称此为一个显式接口，也就是他在源码中明确可见*

   - *由于Widget的某些成员是 virtual，w对那些函数的调用将表现出运行时多态，也就是说将于运行期根据w的动态类型（[条款37](https://vlicecream.github.io/effective-c-%E6%9D%A1%E6%AC%BE32-40-%E7%BB%A7%E6%89%BF%E4%B8%8E%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1%E8%AE%BE%E8%AE%A1/)）决定究竟调用哪一个函数*

2. *Template及泛型编程*

   *Template及泛型编程与面向对象有根本不同。`implicit interface` 和 `compile-time polymorphism`移到前头。*

   *还是用上一个例子，一探究竟，我们将`doProcessing`函数变成`function template`时看看发生咩事*

   ```cpp
   template<typename T>
   void doProcessing<T& w>
   {
     if (w.size() > 10 && w != someNastyWidget) {
       T temp(w);
       temp.normalize();
       temp.swap(w);
     }
   }
   ```

   *这里的`doProcessing`内的的w是如何操作的呢*

   - *w必须支持的哪一种接口，由`template`中执行于`w`身上的操作来决定*
   - *本例w的类型T必须支持size，normalize和swap成员函数，copy构造函数、不等比较!=，并非完全正确。这一组表达式便是T必须支持的一组隐式接口*

   *凡设计w的任何函数调用，例如`operator>`和`operator!=`，有可能造成`template`具现化，是这些调用得以成功。这样的具现行为发生在编译器。"以不同的`template`参数具现化会导致调用不同的函数，这便是所谓的编译器多态"*

   *现在我们再来细看下述代码*

   ```cpp
   template<typename T>
   void doProcessing(T& w)
   {
     if (w.size() > 10 && w != someNastyWidget) {
       ...
     }
   }
   ```

   *`T(w的type)`的隐式接口看来好像有这些约束*

   - *必须提供一个名为`size`的成员函数，该函数返回一个整体值*
   - *必须支持一个`operator!=`函数，用来比较两个`T`对象*

   *真要感谢操作符重载带来的可能性，这两个约束都不需要满足*

   *是的，T必须支持size成员函数，然而这个函数也可能从`base class`继承而得。这个成员函数不需要返回一个整数值，甚至不需要返回一个数值类型。就此而言，他甚至不需要返回一个定义有`operator>`的类型，他唯一需要做的就是返回一个类型为x的对象，而x对象加上一个int(10的类型)必须能够调用一个`operator>`。这个`operator>`不需要非得取得一个类型为x的参数不可，因为他可以取得类型Y的参数，只要存在一个隐式转换能够将类型x转换为类型y的对象*

   *加诸于`template`参数身上的隐式接口，就像加诸于class对象身上的显式接口一样真实，而且两者都在编译期完成检查。就像你无法以一种"与class提供之显式接口接口矛盾"的方式来使用对象（代码无法通过编译），你也无法在template中使用"不支持template所要求之隐式接口"的对象（代码一样无法通过编译）*

### ***Summary***

1. *classes 和 template都支持接口和多态*
2. *对于classes而言接口是显式的，以函数签名为中心，多态则是通过virtual function发生于运行期*
3. *对template参数而言，接口是隐式的，基于有效表达式，多态则是通过template具现化和函数重载解析发生于编译期*

## ***条款42 了解typename***

*我们看以下代码*

```cpp
template<typename C>
void print2nd(const C& container) {
  if (container.size() >= 2) {
    C::const_iterator iter(container.begin());
    ...
  }
}
```

*现在应该很清楚为什么这不是有效的c++代码了吧。iter声明式只有在`C::const_iterator`是个类型时才合理，但我们并没有告诉c++说他是，于是c++假设他不是*

*若要矫正这个，我们必须告诉c++说`C::const_iterator`是个类型。只要紧临它之前放置关键字typename即可*

```cpp
template<typename C>
void print2nd(const C& container) {
  if (container.size() >= 2) {
    typename C::const_iterator iter(container.begin());
    ...
  }
}
```

*再次强调一下，任何时候当你想要在template中指一个嵌套从属类型名称，就必须在紧临它的前一个位置放上关键字`typename`*

*`typename`知识用来言明嵌套从属类型名称，其他名称不该有他的存在。例如下面这个function template，接收一个容器和一个指向该容器的迭代器*

```cpp
template<typename C> // 允许使用 "typename" (或 class)
void f(const C& container, // 不允许使用 "typename"
      typename C::iterator iter); // 一定要使用 typename
```

*上述的c并不是嵌套从属类型名称（他并非嵌套于任何 取决于template参数 的东西内），所以声明container时并不需要以typename为前导，但C::iterator是个嵌套从属类型名称，所以必须以typename为辅导*

*typename必须作为嵌套从属类型名称的前缀词，这一规则的例外是，typename不可以出现在 base classed list内的嵌套从属类型名称之前，也不可在 member initialization list（成员初始列）中作为 base class 修饰符。例如*

```cpp
template<typename T>
class Derived : public Base<T>::Nested {  // base class list中不允许 typename
public:
  explicit Dervied(int x) : Base<T>::Nested(x) { // mem.init.list中 不允许 typename
    typename Base<T>::Nested temp;  // 不再上述的两个特殊情况内，作为一个 base class 修饰符 需加上 typename
  }
}
```

### ***Summary***

1. *声明 template 参数时，前缀关键字 class 和 typename 可互换*
2. *请使用关键字 typename 标识嵌套从属类型名称，但不得在 基类列 或者 成员初始列 内以他作为 base class 修饰符*

## ***条款43 调用基类模板成员***

1. *模板中，派生类不可调用模板基类的成员函数*

   *在模板类中，如果一个派生类在其方法中调用了基类的方法，那么这段代码可能无法编译通过*

   *备注（重点）：*

   ​	*这一现象是与编译器有关的，Effective C++的作者编译的时候出错，本人使用VS编译的时候没有出错，因此这个问题是与编译器有关*

   - *假设现在有这样一个类体系：*

     *我们有若干公司类，其包含两个成员函数可以用来将信息发送到公司（一个为发送加密信息，一个为发送不加密信息）*

     *有一个MsgSender类，其中有两个成员函数，函数中可以定义若干公司，然后调用公司的成员方法向公司发送信息*

     *有一个MsgInfo类，用来封装消息（这个类不重要）*

     ```cpp
     //公司类
     class CompanyA {
     public:
         void sendCleartext(const std::string& msg); //向公司发送未加密信息
         void sendEncrypted(const std::string& msg); //向公司发送加密信息
     };
     
     class MsgInfo {}; //封装信息的类
     
     //发送信息类
     template<typename Company>
     class MsgSender {
     public:
         //在其中定义公司A，并调用公司A的sendCleartext()函数向公司A发送信息
         void sendClear(const MsgInfo& info)
         {
             std::string msg;
             CompanyA a;
             a.sendCleartext(msg);
         }
     
         //同上，只是发送加密信息
         void sendSecret(const MsgInfo& info)
         {
             std::string msg;
             CompanyA a;
             a.sendEncrypted(msg);
         }
     };
     ```

     *现在我们为MsgSender类添加了一个派生类，我们希望每次在发送信息的时候记录一下日志。因此定义如下：*

     ```cpp
     template<typename Company>
     class LoggingMsgSender:public MsgSender<Company>{
     public:
         void sendClearMsg(const MsgInfo& info)
         {
             //记录一下日志
             sendClear(info); //调用基类的方法发送消息，这段代码可能无法编译通过
             //记录一下日志
         }
     
         void sendSecretMsg(const MsgInfo& info)
         {    
             //记录一下日志
             sendSecret(msg); //调用基类的方法发送消息，这段代码可能无法编译通过
             //记录一下日志
         }
     };
     ```

     *上面代码对于某些编译器会出错的原因在于：*

     - *编译期出错：当类遇到LoggingMsgSender类模板定义式时，其并不知道LoggingMsgSender继承的class属于什么类型，因为还没有具体被实例化*

     因此在编译到LoggingMsgSender的成员函数时，其并不知道其基类是否有一个`sendClear()`函数*

2. *解决上面错误的3种方法*

   - *使用this指针：使用this指针调用这些函数，实现先告诉编译器这些函数是属于自身类的（在编译之后它们会从基类中继承而来）*

     ```cpp
     template<typename Company>
     class LoggingMsgSender:public MsgSender<Company>{
     public:
         void sendClearMsg(const MsgInfo& info)
         {
             //记录一下日志
             this->sendClear(info);
             //记录一下日志
         }
         void sendSecretMsg(const MsgInfo& info)
         {    
             //记录一下日志
             this->sendSecret(info);
             //记录一下日志
         }
     };
     ```

   - *使用using声明式*

     *注意这种using声明式与非模板类的不同*

     - *在非模板类中，使用using是为了防止派生类隐藏继承的方法，而使基类中的方法在派生类中可见*
     - *在模板类中，使用using是为了让编译器去基类中查找这个函数*

     ```cpp
     template<typename Company>
     class LoggingMsgSender:public MsgSender<Company>{
     public:
         using MsgSender<Company>::sendClearMsg;
         using MsgSender<Company>::sendSecretMsg;
     
         void sendClearMsg(const MsgInfo& info)
         {
             //记录一下日志
             sendClear(info);
             //记录一下日志
         }
         void sendSecretMsg(const MsgInfo& info)
         {    
             //记录一下日志
             sendSecret(info);
             //记录一下日志
         }
     };
     ```

   - *明确指出被调用的函数位于base class中*

     *这种方法不太建议，因为：被调用的函数可能是virtual函数，这种修饰符会关闭“virtual绑定行为”*

     ```cpp
     template<typename Company>
     class LoggingMsgSender:public MsgSender<Company>{
     public:
         void sendClearMsg(const MsgInfo& info)
         {
             //记录一下日志
             sgSender<Company>::sendClear(info);
             //记录一下日志
         }
         void sendSecretMsg(const MsgInfo& info)
         {    
             //记录一下日志
             sgSender<Company>::sendSecret(info);
             //记录一下日志
         }
     };
     ```

### ***Summary***

1. *当一个类的基类包含模板参数时，需要通过this->的方式调用基类内的函数，例如 `class F: public S<C>`，在F中的成员函数中调用S中的成员函数`this->test()`，而直接写`test()`无法通过编译，原因是因为C是个模板没有办法确定类S的具体长相，或者说无法确定S中一定有test函数，即使你写的所有C都包含test函数，但是在编译器看来它是不确定这个问题的，因此无法通过编译*
2. *解决办法是：*
   - *使用this->test，这样做告诉编译器假设这个test已经被继承了*
   - *使用using声明式：`using S<C>::test`告诉编译期这个test位于S内。相当于必须手动通知编译器这个函数是存在的*
   - *直接指明 但是不推荐 因为丧失多态特性*

## ***条款44 将与参数无关的代码抽离template***

*Templates能够节省时间和避免代码反复。对于相似的classes或functions。能够写一个class template或function template，让编译器来做剩余的事。这样做，有时候会导致代码膨胀（code bloat）：其二进制码带着反复（或差点儿反复）的代码、数据。或者两者。*

*但这时候源代码看起来可能非常整齐。*

*先来学习一个名词：共性与变性分析（commonality and variability analysis）。*

*比較easy理解。比如，你在编写几个函数，会用到同样作用的代码。这时候你往往将同样代码搬到一个新函数中。给其它几个函数调用。同理，假设编写某个class。当中某些部分和另外几个class同样，这时候你不会反复编写这些同样部分，仅仅需把共同部分搬到新class中去就可以，去使用继承或复合（**条款**32,38,39），让原先的classes取用这些共同特性，原classes的互异部分（变异部分）仍然留在原位置不动。*

*编写templates时，也要做同样分析，避免反复。non-template代码中反复十分明白：你能够看到两个函数或classes之间有所反复。可是在template代码中，反复是隐晦的。由于仅仅有一份template源代码。*

*比如。你打算在为尺寸固定的正方矩阵编写一个template，该矩阵有个支持逆矩阵运算的函数*

```cpp
template<typename T, std::size_t n>//T为数据类型。n为矩阵大小
class SquareMatrix{
  public:
  ……
    void invert();//求逆运算
};
SquareMatrix<double,5> sm1;
sm1.invert();//调用SquareMatrix<double,5>::invert
SquareMatrix<double,10> sm2;
sm2.invert();//调用SquareMatrix<double,10>::invert
```

*上面会详细化两份invert。*

*这两份函数差点儿全然同样（除了一个操作5*5矩阵。一个操作10*10）。这就是代码膨胀的一个典型样例。*

*上面两个函数除了操作矩阵大小不同外。其它同样。这时能够为其建立一个带数值的函数，而不是反复代码。于是有了对SquareMatrix的第一份改动*

```cpp
template<typename T>
class SquareMatrixBase{
  protected:
  void invert(std::size_t matrixSize);
  ……
};
template<typename T, std::size_t n>
class SquareMatrix : private SquareMatrixBase<T>{
private:
  using SquareMatrixBase<T>::invert();//编码遮掩base中的invert，**条款**33
public:
  ……
  void invert()//求逆运算
  {
    this->invsert(n);//稍后解释为什么用this
  }
};
```

*SquareMatrixBase::invert仅仅是企图避免derived classes代码反复，所以它以protected替换public。这个函数使用this->，由于模板化基类内的函数名称会被derived classes掩盖（条款43）*

*注意，SquareMatrixBase和SquareMatrix之间继承关系是private。这说明base class是为了帮助derived classes实现，两者不是**is-a关系。*

*如今另一个问题，SquareMatrixBase::invert操作的数据在哪？它在參数中直到矩阵大小，可是矩阵数据derived class才知道。derived class和base class怎样联络？一个做法是能够为SquareMatrixBase::invert加入一个參数（比如一个指针）。*

*这个行得通，可是考虑到其它因素（比如，SquareMatrixBase内还有其它函数。也要操作这些数据），能够把这个指针加入到SquareMatrixBase类中*

```cpp
template<typename T>
class SquareMatrixBase{
protected:
  SquareMatirxBase(std::size_t n,T* pMem)
    :size(n), pData(pMem){}
  void setDataPtr(T* ptr) {pData=ptr;}
  ……
private:
  std::size_t size;
  T* pData;
};
template<typename T, std::size_t n>
class SquareMatrix:private SquareMatrixBase<T>{
public:
  SquareMatrix()
    :SquareMatrixBase<T>(n, data){}
  ……
private:
  T data[n*n];
};
```

*这样的类型的对象不须要动态分配内存。可是对象自身可能非常大。另一个做法是把矩阵数据放到heap*

```cpp
template<typename T, std::size_t n>
class SquareMatrix:private SquareMatrixBase<T>{
public:
  SquareMatrix()
    :SquareMatrixBase<T>(n, 0),
  pData(new T[n*n])
  {this->setDataPtr(pData.get());}
  ……
private:
  boost::scoped_array<T> pData;
};
```

*这样以来。类型同样的derived classes会共享base class。比如。SquareMatrix*

### ***Summary***

1. *原因是模板会根据具体类型具象化不同的代码，如果将与模板无关的代码也放入模板函数或者类中，那么就会生成重复的代码，就会导致代码膨胀的问题，函数模板中与参数无关的代码可以包装成单独的函数。类模板中与参数无关的模板可以放到父类中*

## ***条款45 ***

