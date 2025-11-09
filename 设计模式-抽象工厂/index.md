# 抽象工厂


## ***对象创建模式***

1. ***什么是对象创建***
   - *通过对象创建模式，绕开new，来避免对象创建(new)过程中所导致的紧耦合(依赖具体类)从而支持对象创建*
   - ***他是接口抽象之后的第一步工作***
2. ***典型模式***
   - *Factory Method*
   - *Abstract Factory*
   - *Prototype*
   - *Builder*

## ***抽象工厂动机***

1. *在软件系统中 经常面临着 "一系列相互依赖的对象"的创建工作，同时, 由于需求的变化, 往往存在更多系列对象的创建工作*
2. *如果应对这种变化? 如何绕过常规的对象创建方法(new), 提供一种"封装机制"来避免客户程序和这种"多系列具体对象创建工作"的紧耦合?*

## ***初始代码***

```cpp
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

## ***运用了工厂模式代码***

```cpp
#include <iostream>
#include <string>
#include <vector>
#include <memory>

// 为了让代码能编译，定义空的 EmployeeDO 类
class EmployeeDO {};

// =================================================================
// 1. 抽象产品 (Abstract Products)
//    定义了产品族中每个对象的接口。
// =================================================================

class IDataReader; // 前向声明

class IDBCommand {
public:
    virtual void SetConnection(IDBConnection* connection) = 0;
    virtual void CommandText(const std::string& text) = 0;
    virtual std::unique_ptr<IDataReader> ExecuteReader() = 0;
    virtual ~IDBCommand() {}
};

class IDBConnection {
public:
    virtual void ConnectionString(const std::string& connStr) = 0;
    virtual ~IDBConnection() {}
};

class IDataReader {
public:
    virtual bool Read() = 0;
    virtual ~IDataReader() {}
};

// =================================================================
// 2. 具体产品 (Concrete Products)
//    实现了具体数据库（产品族）的各个组件。
// =================================================================

// --- SQL Server 产品族 ---

class SqlDataReader; // 前向声明

class SqlCommand : public IDBCommand {
public:
    void SetConnection(IDBConnection* connection) override { std::cout << "SqlCommand: Setting SQL connection." << std::endl; }
    void CommandText(const std::string& text) override { std::cout << "SqlCommand: Setting command text." << std::endl; }
    std::unique_ptr<IDataReader> ExecuteReader() override; // 实现放在后面
};

class SqlConnection : public IDBConnection {
public:
    void ConnectionString(const std::string& connStr) override { std::cout << "SqlConnection: Setting connection string." << std::endl; }
};

class SqlDataReader : public IDataReader {
public:
    bool Read() override { 
        std::cout << "SqlDataReader: Reading data..." << std::endl;
        return false; // 简化示例
    }
};

// SqlCommand 的实现需要 SqlDataReader 的完整定义
std::unique_ptr<IDataReader> SqlCommand::ExecuteReader() {
    std::cout << "SqlCommand: Executing reader for SQL." << std::endl;
    return std::make_unique<SqlDataReader>();
}


// --- Oracle 产品族 ---
// (为简洁起见，只写出类名，实现与 SQL Server 类似)
class OracleDataReader;

class OracleCommand : public IDBCommand {
public:
    void SetConnection(IDBConnection* connection) override { std::cout << "OracleCommand: Setting Oracle connection." << std::endl; }
    void CommandText(const std::string& text) override { std::cout << "OracleCommand: Setting command text." << std::endl; }
    std::unique_ptr<IDataReader> ExecuteReader() override;
};

class OracleConnection : public IDBConnection {
public:
    void ConnectionString(const std::string& connStr) override { std::cout << "OracleConnection: Setting connection string." << std::endl; }
};

class OracleDataReader : public IDataReader {
public:
    bool Read() override {
        std::cout << "OracleDataReader: Reading data..." << std::endl;
        return false;
    }
};

std::unique_ptr<IDataReader> OracleCommand::ExecuteReader() {
    std::cout << "OracleCommand: Executing reader for Oracle." << std::endl;
    return std::make_unique<OracleDataReader>();
}


// =================================================================
// 3. 抽象工厂 (Abstract Factory)
//    提供一个接口，用于创建产品族中的所有产品。
// =================================================================
class IDatabaseFactory {
public:
    virtual std::unique_ptr<IDBConnection> CreateDBConnection() = 0;
    virtual std::unique_ptr<IDBCommand> CreateDBCommand() = 0;
    // DataReader 通常由 Command 创建，所以工厂里可以不提供
    virtual ~IDatabaseFactory() {}
};

// =================================================================
// 4. 具体工厂 (Concrete Factories)
//    每个具体工厂实现一个特定的产品族。
// =================================================================
class SqlServerFactory : public IDatabaseFactory {
public:
    std::unique_ptr<IDBConnection> CreateDBConnection() override {
        return std::make_unique<SqlConnection>();
    }
    std::unique_ptr<IDBCommand> CreateDBCommand() override {
        return std::make_unique<SqlCommand>();
    }
};

class OracleFactory : public IDatabaseFactory {
public:
    std::unique_ptr<IDBConnection> CreateDBConnection() override {
        return std::make_unique<OracleConnection>();
    }
    std::unique_ptr<IDBCommand> CreateDBCommand() override {
        return std::make_unique<OracleCommand>();
    }
};

// =================================================================
// 5. 客户端 (Client)
//    客户端仅依赖于抽象接口。
// =================================================================
class EmployeeDAO {
private:
    std::unique_ptr<IDatabaseFactory> factory; // 只需持有一个工厂

public:
    // 通过构造函数注入具体的工厂
    EmployeeDAO(std::unique_ptr<IDatabaseFactory> fact) : factory(std::move(fact)) {}

    std::vector<EmployeeDO> GetEmployees() {
        if (!factory) {
            std::cout << "Error: Factory is not set!" << std::endl;
            return {};
        }

        // 使用智能指针自动管理内存
        std::unique_ptr<IDBConnection> connection = factory->CreateDBConnection();
        connection->ConnectionString("...");

        std::unique_ptr<IDBCommand> command = factory->CreateDBCommand();
        command->CommandText("select * from employees");
        command->SetConnection(connection.get()); // 关联性

        std::unique_ptr<IDataReader> reader = command->ExecuteReader(); // 关联性
        while (reader->Read()) {
            // ... process data ...
        }
        
        std::cout << "Employee data retrieval process finished." << std::endl;
        return {}; // 返回空vector作为示例
    }
};

// =================================================================
// 示例用法
// =================================================================
int main() {
    // 场景1：应用程序配置为使用 SQL Server
    std::cout << "--- Configuring DAO for SQL Server ---" << std::endl;
    auto sqlFactory = std::make_unique<SqlServerFactory>();
    EmployeeDAO sqlDao(std::move(sqlFactory));
    sqlDao.GetEmployees();

    std::cout << "\n-------------------------------------\n" << std::endl;

    // 场景2：应用程序配置为使用 Oracle
    std::cout << "--- Reconfiguring DAO for Oracle ---" << std::endl;
    auto oracleFactory = std::make_unique<OracleFactory>();
    EmployeeDAO oracleDao(std::move(oracleFactory));
    oracleDao.GetEmployees();

    return 0;
}
```

## ***运用了抽象工厂的代码***

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

## ***模式定义***

1. *提供一个接口, 让该接口负责创建一系列"**相关或者互相依赖的对象**", 无需确定他们具体的类*

## ***类图***

![抽象工厂类图](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data/imagesimage-20220529155754118.png)

## ***要点总结***

1. *如果没有应对"多系列对象构建"的需求变化, 则没有必要使用 Abstract Factory模式, 这时候使用简单的工厂完全可以*
2. *"系列对象"指的是在某一个特定系列下的对象之间有互相依赖\或作用的关系.不同系列的对象自建不能相互依赖*
3. *Abstract Factory模式主要在于应对"新系列"的需求变动. 其缺点在于难以应对"新对象"的需求改动*

