# stl-体系结构


# 体系结构

# 标准库与STL

1. **标准库与STL的差别**
   - *标准库 > STL / 标准库里面百分之70 80 都是STL*
2. **STL六大部件**
   - *容器 / 分配器 / 算法 / 迭代器 / 仿函数 / 适配器*

```
// 标准库以header files 形式呈现// C++标准库的head files 不带.h 例如:    #include <vector>// 新式 C header files 不带.h 例如    #include <cstdio>// 旧式 C header files 带.h 仍然可用 例如    #include <stdio.h>
// 新式headers内的组件封装于 命名空间namespace "std"using namespace std;  // 代表直接打开这个namespace 里面的随便用  推荐用这个using std::cout // 就可以直接用cout来表示  // example:  #include <string>  #include <iostream>  #include <vector>  using namespace std;
```

**这里推荐直接用新式的习惯**

## 标准库重要网页

```
// 标准库网页    CPlusPlus.com    CppReference.com    gcc.gnu.org// 书籍介绍    THE C++ STANDARD LIBRARY    STL源码剖析
```

## STL六大部件

![image-20220522221751209](https://vlicecream.github.io/images/ring.svg)

```
/*    容器: 存放data的地方    分配器: 分配内存的作用    迭代器: 可以理解为一个指针    算法: 比如 sort 之类的    仿函数: 作用跟函数类似    适配器: 相当于转换xxx的功能*/
#include <vector>#include <algorithm>#include <functional>#include <iostream>using namespace std;int main() {  int ia[6] = { 27, 210, 12, 47, 109, 83 };  vector<int, allocator<int>> vi(ia, ia+6);  cout << count_if(vi.begin(), vi.end(), not1(bind2nd(less<int>(), 40)));  return 0;}/*代码解析:    vector 其实就是一个容器    allocator: 分配器 可以不写 源代码会有默认的分配器用来分配内存    count_if: 一种算法 能够帮我们在某种条件下计算元素的个数    vi.begin(): 迭代器 传元素    less: 仿函数 比大小 跟a, b比大小 取小    bind2nd: 适配器  绑定第二个参数 此处就是可以跟40来比    not1: 否定 原本是小于40  现在是大于等于40*/
```

## 前闭后开 区间

```
#include <vector>#include <algorithm>#include <functional>#include <iostream>using namespace std;int main() {  int ia[6] = { 27, 210, 12, 47, 109, 83 };  vector<int, allocator<int>> vi(ia, ia+6);  cout << count_if(vi.begin(), vi.end(), not1(bind2nd(less<int>(), 40)));  return 0;}/*    vi.begin() 指向ia数组的第一个元素的位置    vi.end() 指向ia数组最后一个元素的下一个位置    这种的我们就叫做前闭后开   [ )    所以利用这个我们可以对这个容器用伪代码遍历*/Container<T> c;  // 创建一个容器...  // 往容器塞值container<T>::iterator ite = c.begin()  // 给一个迭代器for (; ite != c.end(); ++ite) { // 如果这个迭代器不到c.end() 就执行代码  ...}// 现在有一个可以优化上面的伪代码的方法std::vector<double> vec;...for ( auto elem : vec) {  // auto的作用就是代替 container<T>::iterator  std::cout << elem << std::end1;}// 对auto也可以优化for ( auto& elem : vec) {  elem *= 3}// 对auto再一次解释list<string> c;...list<string>::iterator ite;ite = ::find(c.begin(), c.end(), target);// 上述代码利用auto可以写成list<string> c;...auto ite = ::find(c.begin(), c.end(), target);
```

![image-20220810213753831](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data/imagesimage-20220810213753831.png)

