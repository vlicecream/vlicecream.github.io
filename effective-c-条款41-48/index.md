# 条款41~48 模板与泛型编程


# 条款41~48 模板与泛型编程

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
   
   ​    *这一现象是与编译器有关的，Effective C++的作者编译的时候出错，本人使用VS编译的时候没有出错，因此这个问题是与编译器有关*
   
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

*还是比较容易理解的。比如，你在编写几个函数，会用到同样作用的代码。这时候你往往将同样代码搬到一个新函数中。给其它几个函数调用。同理，假设编写某个class。当中某些部分和另外几个class同样，这时候你不会反复编写这些同样部分，仅仅需把共同部分搬到新class中去就可以，去使用继承或复合（[Item32 Item38 Item39](https://vlicecream.github.io/effective-c-%E6%9D%A1%E6%AC%BE32-40-%E7%BB%A7%E6%89%BF%E4%B8%8E%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1%E8%AE%BE%E8%AE%A1/)），让原先的classes取用这些共同特性，原classes的互异部分（变异部分）仍然留在原位置不动。*

*编写templates时，也要做同样分析，避免反复。non-template代码中反复十分明白：你能够看到两个函数或classes之间有所反复。可是在template代码中，反复是隐晦的。由于仅仅有一份template源代码。*

*比如。你打算在为尺寸固定的正方矩阵编写一个template，该矩阵有个支持逆矩阵运算的函数*

```cpp
template<typename T, std::size_t n>  // T为数据类型。n为矩阵大小
class SquareMatrix{
public:
  ……
  void invert();// 求逆运算
};

SquareMatrix<double,5> sm1;
sm1.invert();  // 调用SquareMatrix<double,5>::invert
SquareMatrix<double,10> sm2;
sm2.invert();  // 调用SquareMatrix<double,10>::invert
```

*上面会详细化两份invert。*

*这两份函数差点儿全然同样（除了一个操作55矩阵。一个操作1010）。这就是代码膨胀的一个典型样例。*

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
  using SquareMatrixBase<T>::invert();  // 编码遮掩base中的invert，条款33有说到
public:
  ……
  void invert() {  // 求逆运算
    this->invsert(n);  // 稍后解释为什么用this
  }
};
```

*`SquareMatrixBase::invert`仅仅是企图避免derived classes代码反复，所以它以protected替换public。这个函数使用`this->`，由于模板化基类内的函数名称会被derived classes掩盖（条款43）*

*注意，SquareMatrixBase和SquareMatrix之间继承关系是private。这说明base class是为了帮助derived classes实现，两者不是is-a关系*

*如今另一个问题，SquareMatrixBase::invert操作的数据在哪？它在參数中直到矩阵大小，可是矩阵数据derived class才知道。derived class和base class怎样联络？一个做法是能够为SquareMatrixBase::invert加入一个參数（比如一个指针）。*

*这个行得通，可是考虑到其它因素（比如，SquareMatrixBase内还有其它函数。也要操作这些数据），能够把这个指针加入到SquareMatrixBase类中*

```cpp
template<typename T>
class SquareMatrixBase{
protected:
  SquareMatirxBase(std::size_t n, T* pMem) : size(n), pData(pMem) { }
  void setDataPtr(T* ptr) { pData=ptr; }
  ……
private:
  std::size_t size;
  T* pData;
};
template<typename T, std::size_t n>
class SquareMatrix : private SquareMatrixBase<T>{
public:
  SquareMatrix() : SquareMatrixBase<T>(n, data) { }
  ……
private:
  T data[n*n];
};
```

*这样的类型的对象不须要动态分配内存。可是对象自身可能非常大。另一个做法是把矩阵数据放到heap*

```cpp
template<typename T, std::size_t n>
class SquareMatrix : private SquareMatrixBase<T>{
public:
  SquareMatrix() : SquareMatrixBase<T> (n, 0), pData(new T[n*n]) { this->setDataPtr(pData.get()); }
  ……
private:
  boost::scoped_array<T> pData;
};
```

*这样以来。类型同样的derived classes会共享base class。比如。SquareMatrix*

### ***Summary***

1. *原因是模板会根据具体类型具象化不同的代码，如果将与模板无关的代码也放入模板函数或者类中，那么就会生成重复的代码，就会导致代码膨胀的问题，函数模板中与参数无关的代码可以包装成单独的函数。类模板中与参数无关的模板可以放到父类中*

## ***条款45 运用成员函数模板接受全部兼容类型***

1. *隐式转化（前言）*
   
   *该条款作者阐述自己的观点是通过智能指针的样例*
   
   *所以我们复习一下隐式转化的问题，例如以下代码*
   
   ```cpp
   #include<iostream>
   using namespace std;
   class A {
   public:
       explicit A(int i) : a(i) {};
       A(const A& obj) : a(obj.a) {}
   private:
       int a;
   };
   ```

   int main()
   {
       int value =0;
       A a = value;  // 编译不通过。由于构造函数中有explicit限定符
       return 0;
   }

```
*我们知道由于explicit限定符的存在编译不通过*

2. *问题抛出*

*既然复习完了，我们来看看书上另一段代码*

```cpp
template<typename T>
class SmartPtr {
public:
  explicit SmartPtr(T* realPtr);
  ...
};

SmartPtr<Top> pt1 = SmartPtr<Middle>(new Middle);
SmartPrt<Top> pt2 = SmartPrt<Bottom>(new Bottom);
SmartPrt<const Top> pt2=pt1;
```

   *我们能够知道，由于`SmartPtr<Top>`类型和`SmartPtr<Middle>`类型不同，再加上`explicit SmartPtr<Middle>`中的explicit限定符，`SmartPtr<Top> pt1=SmartPtr<Middle>(new Middle);`这句代码编译不通过。*

   *并且编译器并不觉得`SmartPtr<Top>`类型和`SmartPtr<Middle>`类型存在继承关系*

   *为了能够实现相互转化。能够加入本节的主旨技术去解决上面出现的问题*

3. *解决方法*
   
   ```cpp
   template<typaname T>
   class SmartPtr{
   public:
     template<typename U>
     SmartPrt(const SmartPrt<U>& other) : heldPrt(other.get()) { };
     T* get() const{ return heldPrt; }
     ……
   private:
     T* heldPrt;
   };
   
   SmartPtr<Top> pt1 = SmartPtr<Middle>(new Middle);
   SmartPrt<Top> pt2 = SmartPrt<Bottom>(new Bottom);
   SmartPrt<const Top> pct2 = pt1;
   ```
   
   *我们加入了一个member function template函数。由于typename T和typename U 是两种类型，并且构造函数中没有explicit关键字，不会阻止`heldPrt(other.get())的隐式转换`*
   
   *所以，以上代码能够通过编译*

4. *TR1规范中关于`tr1::shared_ptr`的一份摘录*
   
   ```cpp
   template<class T>
   class shared_ptr{
   public:
     template<class Y>
       explicit shared_ptr(Y* p);
     template<class Y> 
       shared_ptr(shared_ptr<Y> const& r);
     template<class Y>
       explicit shared_ptr(weak_ptr<Y> const& r);
     template<class Y>
       explicit shared_ptr(auto_ptr<Y> const& r);
     template<class Y>
       shared_ptr& operator=(shared_ptr<Y> const& r);
     template<class Y>
       shared_ptr& operator=(auto_ptr<Y> const& r);
      ……
   };
   ```
   
   *我们能够发现上面仅仅有泛化copy构造函数不是explicit，表示shared_ptr 的隐式转化被同意，而其它的智能指针转化不被同意*
   
   *这里另一个须要注意的地方，在class类声明泛化copy构造函数（member template）。并不会阻止编译器生成它们自己的copy构造函数（non-template），换言之。假设程序中仅仅写了泛化的copy构造函数，那么编译器还是会自己主动生成一个非泛化的版本号出来，假设不想要这个缺省版本号，那一定不能偷懒。要两个版本号的copy构造函数都要写*
   
   ```cpp
   template<typaname T>
   class SmartPtr{
   public:
     template<typename U>
     SmartPrt(const SmartPrt<U>& other) : heldPrt(other.get()) {};
     SmartPtr() {}  // 假设不写自己的非泛化构造函数，编译器会自己主动生成自己的默认非泛化构造函数。
     T* get() const{ return heldPrt; }
     ……
   private:
     T* heldPrt;
   };
   ```

### ***Summary***

1. *请使用member function templates（成员函数模板）生成“可接受全部兼容类型”的函数*
2. *假设你声明member templates用于“泛化copy构造”或“泛化assignment操作”，你还是须要声明正常的copy构造函数和copy assignment操作符*

## ***条款46 需要类型转换时请为模板定义非成员函数***

*这个条款是在《[Item24](https://vlicecream.github.io/effective-c-%E6%9D%A1%E6%AC%BE18-25-%E8%AE%BE%E8%AE%A1%E4%B8%8E%E7%94%9F%E5%91%BD/)》的基础上，讲述有关非成员函数在模板类中的作用，所以忘了的要去复习下*

*想要在template实现Item24的功能，还得考虑其他问题*

```cpp
template<typename T>
class Rational{
public:
  Rational(const T& numerator=0,const T& denominator=1);
  const T numerator() const;
  const T denominator() const;
  ……
};
template<typename T>
const Rational<T> operator*(const Rational<T>& lhs,const Rational<T>& rhs) { …… };
Rational<int> oneHalf(1,2);
Rational<int> result = oneHalf*2;  // 错误 无法通过编译
```

*大家思考一下为什么`oneHalf*2`这句话不能通过编译*

*事实上，operator模板函数中參数有两个，所以它会分别对这两个参数进行匹配来确定函数模板类型，试想一下，函数模板在没有实例化之前是不存在的，不存在的函数怎么会实现參数的隐式转换？我们来判断一般模板函数的运行过程。首先，模板函数通过自身參数实例化，实例化之后才会被调用运行。然而。对于本例来说，两个參数的类型一个是`Rational<int>`，还有一个是`2`，在编译期间前者能够被判断出来类型是int的rational，后者却判断不出来。由于在template实參推导过程中从不将隐式类型转换考虑在内*

*为了能让编译通过，我们能够进行例如以下改变*

```cpp
template<typename T>
class Rational
{
    public:
        ……
        friend const Rational operator*(const Rational& lhs,const Rational& rhs);
        {
            return Rational（lhs.numerator()*rhs.numerator(),lhs.denominator()*rhs.denominator());
        }

};
```

*将operator变成Rational类的友元函数。这样在定义一个`Rational<int>`对象的时候，operator模板函数事实上已经被实例化了，这时候再调用`oneHalf*2`这句话的时候，就是直接调用已经实例化的operator*函数了，所以，此时，它支持隐式转换。将2转换为`Rational<int>`对象*

*值得一提的是以上代码也可写成例如以下形式*

```cpp
template<typename T>
class Rational
{
    public:
        ……
        friend const Rational<T> operator*(const Rational<T>& lhs,const Rational<T>& rhs);
        {
            return Rational<T>（lhs.numerator()*rhs.numerator(),lhs.denominator()*rhs.denominator());
        }

    };
```

*也就是说`Rational<T>`和`Rational`的形式是一个意思，为了简化，我们能够用`Rational`的形式*

*由于这样将友元函数定义在Rational类中，也就默认是内联函数inline了，为了避免复杂的friend函数影响代码体积，我们利用另外的一种形式实现**

**例如以下代码*

```cpp
 template<typename T> class Rational;//forward decelarion
    template<typename T>
    const Rational<T> doMultiply(const Rational<T>& lhs,const Rational<T>& rhs);
    template<typename T>
    class Rational{
    public:
        ……
        friend const Rational operator*(const Rational& lhs,const Rational& rhs);//声明+定义
        {
            return doMultiply(lhs,rhs);
        }

    };

template<typename T>
    const Rational<T> doMultiply(const Rational<T>& lhs,const Rational<T>& rhs)
    {
        return Rational<T>（lhs.numerator()*rhs.numerator(),lhs.denominator()*rhs.denominator());
    }
```

*我们又又一次定义了一个非类成员函数non-member，将此函数的声明和定义都放在类的外部，这样就能避免代码膨胀问题*

### ***Summary***

1. *当编写一个class template时，它所提供之“与此template相关的”函数支持“全部參数之隐式类型转换”时，请将那些函数定义为class template内部的friend函数*

## ***条款47 请使用traits classes表现类型信息***

1. *STL迭代器分类*
   
   - *input迭代器*
     
     - *只能一次一步向前移动,客户只可读取(不能涂写)且只能读取一次它们所指的东西,模仿指向输入文件的阅读指针.例如istream_iterators*
   
   - *output迭代器*
     
     - *与input迭代器类似,但"一切只为输出",只能一次一步向前移动,客户只可涂写(不能读取)且只能涂写一次它们所指向的东西,模仿指向输出文件的涂写指针.例如ostream_iterators*
   
   - *forward迭代器*
     
     - *具有input迭代器和output迭代器的所有功能:只能一次一步向前移动,可以读或写其所指物一次以上.STL并未提供单向linked list,但某些程序库有(通常名为slist),这种容器的迭代器就是forward迭代器*
   
   - *bidirectional迭代器*
     
     - *它除了可以向前移动,还可以向后移动,一步只能一次,并可以读或写所指物一次以上.STL的list,set,multiset,map和multimap的迭代器就属于这一类*
   
   - *random迭代器*
     
     - *除了bidirectional迭代器的所有功能以外,还可以执行"迭代器算数",即在常量时间内向前或向后移动任意距离.例如vector,deque和string的迭代器*
   
   - *针对这五种分类,C++标准库分别提供专属的 struct 加以区分*
     
     ```cpp
     struct input_iterator_tag {};
     struct output_iterator_tag {};
     struct forward_iterator_tag : public input_iterator_tag {};
     struct bidirectional_iterator_tag : public forward_iterator_tag {};
     struct random_access_iterator_tag : public bidirectional_iterator_tag {};
     ```

2. *traits*
   
   - *STL主要由"用以表现容器,迭代器和算法"的templates组成,单也覆盖若干工具性templates,例如用于将迭代器移动到某个给定距离的advance函数模板.正如1所言,不同迭代器具有不同接口,因而advance需要根据不同迭代器所能进行的操作确定不同实现,例如对于random迭代器,advance可以直接进行+=操作,而对于其他迭代器,则可能反复使用++或--,因此advance的实现可能像这样*
     
     ```cpp
     template<typename IterT,typename DisT>
     void advance(Iter& iter,Dist d){
         if(iter is a random access iterator) //伪代码
             iter+=d;
         else
             if(d>=0)
                 while(d--)
                     ++iter;
             else
                 while(d++)
                     --iter;
     }
     ```
     
     *这种做法必须首先判断iter是否为random迭代器,因此需要取得类型的有关信息,traits技术就是用来使STL的某些泛型算法能够在编译期取得某些类型信息.*
   
   - *"Traits并不是C++关键字或一个预先定义好的构件;它们是一种技术,也是一个C++程序员共同遵守的协议.""这个技术的要求之一是,它对内置类型和用户自定义类型的表现必须一样好"*
     
     *"Traits能够施行于内置类型"意味着通过在类型内嵌套信息实现类型判断不可行,因此类型的traits信息必须位于类型自身之外.标准技术是把它置入一个template及一个或多个特化版本中."这样的templates在标准程序库中有若干个,其中针对迭代器的被被命名为iterator_traits"*
     
     ```cpp
     template<typename IterT>
     struct iterator_traits;
     ```
     
     *iterator_traits的运作方式是:针对每一个类型IterT,在struct iterator_traits<IterT>内一定声明某个typedef名为iterator_catagory,这个typedef用于确定Iter的迭代器分类:*
     
     *对于自定义类型,它要求每一个用户"自定义的iterator类型"必须嵌套一个typedef,名为iterator_catagory(实际上,要使自定义的iterator支持更多的STL泛型算法,还需要其他typedef,见第3条),这个typedef用来确认Iter的分类 ,因此针对一个的确迭代器设计的class可能回想这样*
     
     ```cpp
     template<...>
     class deque{
     public:
         class iterator{
         public:
             typedef random_access_iterator_tag iterator_category;
             ...
         };
         ...
     }
     ```
     
      *list的迭代器可能像这样:*
     
     ```cpp
     template<...>
     class list{
     public:
         class Iterator{
         public:
              typedef bidirectional_iterator_tag iterator_category;
             ...
         };
         ...
     };
     ```
     
     *至于iterator_traits,只是用来表现iterator class的嵌套式typedef*
     
     ```cpp
     template<typename IterT>
     struct iterator_traits{
         typedef typename IterT::iterator_category iterator_category;
         ...
     };  
     ```
     
     *对于指针迭代器,由于指针不可能嵌套typedef,iterator_traits特别针对指针类型提供一个偏特化版本:*
     
     ```cpp
     template<typename Iter>
     struct iterator_traits<IterT*>{
         typedef random_access_iterator_tag iterator_category;
         ...
     };
     ```
     
     *由以上实例,trait classes的设计与实现过程大体如下:*
     
     ​    *1). 确认若干想取得的类型相关信息.(对于迭代器,包括其category)*
     
     ​    *2). 为该信息选择一个名称(对于迭代器的category,名称为iterator_category)*
     
     ​    *3). 提供一个template和一组特化版本(例如iterator_traits),内含希望支持的类型信息.*
     
     *因此,最开始的伪代码可以实行*
     
     ```cpp
     template<typename IterT,typename DisT>
     void advance(Iter& iter,Dist d){
         if(typeid(typename::std::iterator_traits<IterT>::iterator_category
            ==typeid(typename::std::random_access_iterator_tag))
         ...
     }
     ```
     
     *此时还未结束,因为以上代码会存在编译问题:假设对advance作以下调用*
     
     ```cpp
     std::list<int>::iterator iter;
     advance(iter,10);
     ```
     
     *那么advance将被特化为以下形式:*
     
     ```cpp
     void advance(std::list<int>::iterator iter,int d){
     if(typeid(typename::std::iterator_traits<std::list<int>::iterator>:iterator_category==typeid(typename::std::random_access_iterator_tag))
             iter+=d;  //错误,编译时不通过!
         else
             if(d>=0)
                 while(d--)
                     ++iter;
             else
                 while(d++)
                     --iter;
     }
     ```
     
     尽管测试typeid的那一行总会因为list<int>::iterator而失败,因而iter+=d永远不会执行,但在此之前编译器必须确保所有的源码有效,纵使是不会执行的代码!
     
       此外,由于iterator_traits<IterT>::category在编译期即可确定,但if语句的判断却要在运行期核定,这不仅浪费时间,也会造成可执行文件膨胀.
     
       实际上,C++提供了完成在编译期进行核定的方法:函数重载.合成两种重载函数,但接受不同的iterator_category对象,由它们完成advance的实际功能,因此advance的最终实现版本如下:
     
     ```cpp
     template<typename IterT,typename DistT>
     void doAdvance(IterT& iter,Dist d,std::random_access_iterator_tag){
         iter+=d;
     }
     template<typename IterT,typename DistT>
     void doAdvance(IterT& iter,Dist d,std::bidirectional_iterator_tag){
         if(d>=0)
             while(d--)
                 ++iter;
         else
             while(d++)
                 --iter;
     }
     template<typename IterT,typename DistT>
     void doAdvance(IterT& iter,Dist d,std::input_iterator_tag){
         if(d<0)
             throw out_of_range("Negative distance");
         while(d--)
             ++iter;
     }
     template<typename IterT,typename DistT>
     void doAdvance(Iter& iter,Dist d){
         doAdvance(iter,d,typename std::iterator_traits<IterT>::iterator_category());
     }
     ```
     
     其中,由于之前iterator卷标结构的继承关系,doAdvance的input_iterator版本也可以被forward iterator调用.
     
       由以上实例,trait classes的使用过程如下:
     
       1). 建立一组重载函数(身份像劳工)或函数模板(例如doAdvance),彼此之间的差异仅在于各自的traits参数.令每个函数实现码与其接受之traits相应和.
     
       2). 建立一个控制函数(身份像工头)或函数模板(例如advance),它调用上述"劳工函数并传递traits classes所提供的信息".

3. traits 广泛应用于标准库,包括上述iterator_traits,除了iterator_category,iterator_traits还供应四分迭代器相关信息(value_type指明迭代器所指对象类型,difference_type指明迭代器距离类型,pointer指明对象的原生指针类型,reference指明对象的引用类型,参照http://www.cnblogs.com/tracylee/archive/2012/10/26/2741907.html)此外还有char_traits用于保存字符类型的相关信息,numeric_limits用于保存数值类型相关信息等等.
   
     TR1导入许多新的traits classes用以提供类型信息,包括is_fundamental<T>(判断T是否为内置类型),is_array<T>(判断T是否为数组类型),is_base_of<T1,T2>(判断T1,T2是否相同,抑或T1是T2的base classes).总计TR1一共为C++添加了50个以上的trait classes.

## ***条款48 模板元编程***

### ***Summary***

1. *什么是模板元编程*
   
   - *模板元编程(template mataprogramming,TMP)是编写C++程序并执行于编译期的过程,"所谓template mataprogram(模板元程序),是以C++写成,执行于C++编译器内的程序.一旦TMP程序结束执行,其输出,也就是从templates具现出来的若干C++源码,便会一如往常地被编译*

2. *模板元编程的优点*
   
   - *它让某些事情变得更容易*
   
   - *template program执行于C++编译期的特性使得工作可以从编译期转移至执行期,这使得错误可以被提前检测,并产出具有较小可执行文件,较短运行期,较少内存需求的文件,代价就是编译时间变长了.*
   
   - *Item47中的traits解法就是TMP，因为traits引发编译期发生于类型身上的if...else运算：用编译期的重载模板函数参数匹配行为代替执行期发生的if...else运算功能*
   
   - *TMP已经被证明是"图灵完全"的,使用TMP可以声明变量,执行循环,编写及调用函数......针对TMP设计的程序库(例如Boost's MPL)提供更高层次的语法."但这般构件相对于正常的C++对应物看起来很是不同,例如Item47展示的TMP if-else条件句是藉由templates及其特化体表现出来",另外一个例子是循环,TMP是的循环是藉由递归完成*
     
     *用TMP实现一个计算阶乘的函如下*
     
     ```cpp
     template<unsigned n>
     struct Factorial{
         enum{value=n*Factiroal<n-1>::value;};
     }
     template<>
     struct Factorial<0>{
         enum{ value=1};
     }
     ```

3. *TMP的重要应用*
   
   - *确保量度单位正确*
   - *优化矩阵运算*
   - *生成客户定制之设计模式实现品(如Strategy，Observer，Visitor等)*

4. *TMP的缺点*
   
   - 语法不直观
   - 编译时间长

