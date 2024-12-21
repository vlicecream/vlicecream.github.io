# 抽象工厂


# 抽象工厂

## 对象创建模式

1. ***什么是对象创建***
   - *通过对象创建模式，绕开new，来避免对象创建(new)过程中所导致的紧耦合(依赖具体类)从而支持对象创建*
   - ***他是接口抽象之后的第一步工作***
2. ***典型模式***
   - *Factory Method*
   - *Abstract Factory*
   - *Prototype*
   - *Builder*

## 抽象工厂动机

1. *在软件系统中 经常面临着 "一系列相互依赖的对象"的创建工作，同时, 由于需求的变化, 往往存在更多系列对象的创建工作*
2. *如果应对这种变化? 如何绕过常规的对象创建方法(new), 提供一种"封装机制"来避免客户程序和这种"多系列具体对象创建工作"的紧耦合?*

## 初始代码

1. ```cpp
   class EmployeeDAO{
   
   public:
       vector<EmployeeDO> GetEmployees(){
           SqlConnection* connection =
               new SqlConnection();
           connection->ConnectionString = "...";
   
           SqlCommand* command =
               new SqlCommand();
           command->CommandText="...";
           command->SetConnection(connection);
   
           SqlDataReader* reader = command->ExecuteReader();
           while (reader->Read()){
   
           }
   
       }
   };
   ```

## 运用了工厂模式代码

1. ```cpp
   //数据库访问有关的基类
   class IDBConnection{
   
   };
   class IDBConnectionFactory{
   public:
       virtual IDBConnection* CreateDBConnection()=0;
   };
   ```
   
   class IDBCommand{
   
   };
   class IDBCommandFactory{
   public:
   
       virtual IDBCommand* CreateDBCommand()=0;
   
   };
   
   class IDataReader{
   
   };
   class IDataReaderFactory{
   public:
   
       virtual IDataReader* CreateDataReader()=0;
   
   };
   
   //支持SQL Server
   class SqlConnection: public IDBConnection{
   
   };
   class SqlConnectionFactory:public IDBConnectionFactory{
   
   };
   
   class SqlCommand: public IDBCommand{
   
   };
   class SqlCommandFactory:public IDBCommandFactory{
   
   };
   
   class SqlDataReader: public IDataReader{
   
   };
   class SqlDataReaderFactory:public IDataReaderFactory{
   
   };
   
   //支持Oracle
   class OracleConnection: public IDBConnection{
   
   };
   
   class OracleCommand: public IDBCommand{
   
   };
   
   class OracleDataReader: public IDataReader{
   
   };
   
   class EmployeeDAO{
   
       IDBConnectionFactory* dbConnectionFactory;
       IDBCommandFactory* dbCommandFactory;
       IDataReaderFactory* dataReaderFactory;
   
   public:
   
       vector<EmployeeDO> GetEmployees(){
           IDBConnection* connection =
               dbConnectionFactory->CreateDBConnection();
           connection->ConnectionString("...");
       
           IDBCommand* command =
               dbCommandFactory->CreateDBCommand();
           command->CommandText("...");
           command->SetConnection(connection); //关联性
       
           IDBDataReader* reader = command->ExecuteReader(); //关联性
           while (reader->Read()){
       
           }
       
       }
   
   };

```
## 运用了抽象工厂的代码

```cpp
//数据库访问有关的基类
class IDBConnection{

};

class IDBCommand{

};

class IDataReader{

};


class IDBFactory{
public:
 virtual IDBConnection* CreateDBConnection()=0;
 virtual IDBCommand* CreateDBCommand()=0;
 virtual IDataReader* CreateDataReader()=0;

};


//支持SQL Server
class SqlConnection: public IDBConnection{

};
class SqlCommand: public IDBCommand{

};
class SqlDataReader: public IDataReader{

};


class SqlDBFactory:public IDBFactory{
public:
 virtual IDBConnection* CreateDBConnection()=0;
 virtual IDBCommand* CreateDBCommand()=0;
 virtual IDataReader* CreateDataReader()=0;

};

//支持Oracle
class OracleConnection: public IDBConnection{

};

class OracleCommand: public IDBCommand{

};

class OracleDataReader: public IDataReader{

};



class EmployeeDAO{
 IDBFactory* dbFactory;

public:
 vector<EmployeeDO> GetEmployees(){
     IDBConnection* connection =
         dbFactory->CreateDBConnection();
     connection->ConnectionString("...");

     IDBCommand* command =
         dbFactory->CreateDBCommand();
     command->CommandText("...");
     command->SetConnection(connection); //关联性

     IDBDataReader* reader = command->ExecuteReader(); //关联性
     while (reader->Read()){

     }

 }
};
```

## 模式定义

1. *提供一个接口, 让该接口负责创建一系列"**相关或者互相依赖的对象**", 无需确定他们具体的类*

## 类图

![抽象工厂类图](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data/imagesimage-20220529155754118.png)

## 要点总结

1. *如果没有应对"多系列对象构建"的需求变化, 则没有必要使用 Abstract Factory模式, 这时候使用简单的工厂完全可以*
2. *"系列对象"指的是在某一个特定系列下的对象之间有互相依赖\或作用的关系.不同系列的对象自建不能相互依赖*
3. *Abstract Factory模式主要在于应对"新系列"的需求变动. 其缺点在于难以应对"新对象"的需求改动*

