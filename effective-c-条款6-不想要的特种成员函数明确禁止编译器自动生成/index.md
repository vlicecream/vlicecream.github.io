# 条款5-不想要的特种成员函数，明确禁止编译器自动生成


## ***前言***

1. *该条款解决方法 分为两种，一个为"c++11"以后的解决方法，一个为"c++98"*

## ***c++11***

- *直接在不想要的特种成员函数 后面 加上 `= delete` 即可*

  ```cpp
  class HomeForSale {
  public:
    HomeForSale(const HomeForSale&) = delete;
    HomeForSale& operator=(const HomeForSale&) = delete;
  }
  ```

## c++98

- *可以通过私有化 并且 只声明，而不去定义，具体如下述代码*

  ```cpp
  class Uncopyable {
  protected:
    Uncopyable() { };
    ~Uncopyable() { };
  private:
    Uncopyable(const Uncopyable&);
    Uncopyable& operator=(const Uncopyable&);
  }
  
  class HomeForSale : private Uncopyable {
    
  }
  ```

  

