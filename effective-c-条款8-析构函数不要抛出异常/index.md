# 条款8-析构函数不要抛出异常


## ***析构函数不要抛出异常***

1. *析构函数抛出异常就代表这个析构函数并没有执行完，可能会造成内存泄漏*

## ***避免异常从析构函数抛出的方法***

*场景描述：*

```cpp
class DBConnection {
public:
  static DBConnection FactoryCreate(); // 工厂函数
  void close(); // 抛出异常
};

class DBConn {
public:
  ~DBConn() 
  {
    db.close(); // 这样的话 其实close方法是可能会抛出异常的
  }
private:
  DBConnection db;
}
```

1. *发生异常直接终止程序（程序：你清高）*

   ```cpp
   DBConn::~DBConn()
   {
     try { db.Close(); }
     catch (...) {
       // 打印日志
       std::abort(); //终止程序
     }
   }
   ```

2. *或者就是不终止程序了，直接把错误吞掉，打印好日志*

   ```cpp
   DBConn::~DBConn()
   {
     try { db.Close(); }
     catch (...) {
       // 打印日志
     }
   }
   ```

3. *前两者都无法对"抛出异常"做出什么反应,另一个方法是避免异常函数在析构函数内执行,由客户来调用func函数,为避免客户忘记执行,需设立flag标记客户是否调用,如果客户没有调用,在析构函数内调用该函数*

   ```cpp
   class DBConn {
   public:
     void close() {
       db.close();
       closed = true; // 如果成功close 就设一个标识符
     }
     ~DBConn() {
       if (!closed) {
         try { db.close(); }
         catch (...) {
           // make log
         }
       }
     }
   private:
     DBConnection db;
     
   }
   ```

   

