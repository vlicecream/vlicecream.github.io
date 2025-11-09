# 桥模式


## ***单一职责模式***

1. ***什么是单一职责***
   - *在软件组件的设计中，如果责任划分的不清晰，使用继承得到的结果往往是随着需求的变化，子类急剧膨胀，同时充满着重复代码*
   - *这时候最关键是划清责任，每个类引起他变化的原因只有一个*
2. ***典型模式***
   - *Decorator*
   - *Bridge*

## ***使用桥模式动机***

1. *由于某些类型的固有的实现逻辑 使得它们具有两个变化的维度 乃至多个维度的变化*
2. *如何应对这种"多维度的变化" 如何利用面向对象技术来使得类型可以轻松的沿着两个乃至多个方向变化 而不引入额外的复杂度*

## ***初始代码***

1. ***代码演示***
   
   ```cpp
   #include <iostream>
   #include <string>
   
   // 为了让代码能编译，定义一个空的 Image 类
   class Image {};
   
   // =================================================================
   // 1. 统一的抽象基类
   //    这个基类混合了“业务逻辑”和“平台实现”的接口。
   // =================================================================
   class Messager {
   public:
       // 业务逻辑接口
       virtual void Login(std::string username, std::string password) = 0;
       virtual void SendMessage(std::string message) = 0;
       virtual void SendPicture(Image image) = 0;
   
       // 平台实现接口
       virtual void PlaySound() = 0;
       virtual void DrawShape() = 0;
       virtual void WriteText() = 0;
       virtual void Connect() = 0;
   
       virtual ~Messager() {}
   };
   
   
   // =================================================================
   // 2. 平台实现层 (n个平台)
   //    每个平台都从基类继承，并实现平台相关的部分。
   //    但它们必须将业务逻辑接口保留为纯虚函数，留给下一层实现。
   // =================================================================
   
   // 平台实现 A: PC 平台
   class PCMessagerBase : public Messager {
   public:
       // 实现平台相关功能
       void PlaySound() override {
           std::cout << "[PC] Playing sound..." << std::endl;
       }
       void DrawShape() override {
           std::cout << "[PC] Drawing shape..." << std::endl;
       }
       void WriteText() override {
           std::cout << "[PC] Writing text..." << std::endl;
       }
       void Connect() override {
           std::cout << "[PC] Connecting..." << std::endl;
       }
   };
   
   // 平台实现 B: 移动端平台
   class MobileMessagerBase : public Messager {
   public:
       // 实现平台相关功能
       void PlaySound() override {
           std::cout << "[Mobile] Playing sound..." << std::endl;
       }
       void DrawShape() override {
           std::cout << "[Mobile] Drawing shape..." << std::endl;
       }
       void WriteText() override {
           std::cout << "[Mobile] Writing text..." << std::endl;
       }
       void Connect() override {
           std::cout << "[Mobile] Connecting..." << std::endl;
       }
   };
   
   
   // =================================================================
   // 3. 业务逻辑层 (m * n 个具体类)
   //    这一层将业务逻辑和平台实现“固化”在一起。
   //    每增加一个业务需求，就需要为每个平台都创建一个新类。
   //    每增加一个新平台，就需要为每个业务需求都创建一个新类。
   // =================================================================
   
   // --- PC 平台的业务实现 ---
   
   class PCMessagerLite : public PCMessagerBase {
   public:
       void Login(std::string username, std::string password) override {
           std::cout << "--- PC Lite Login ---" << std::endl;
           PCMessagerBase::Connect(); // 直接调用基类的实现
       }
       void SendMessage(std::string message) override {
           std::cout << "--- PC Lite SendMessage ---" << std::endl;
           PCMessagerBase::WriteText();
       }
       void SendPicture(Image image) override {
           std::cout << "--- PC Lite SendPicture ---" << std::endl;
           PCMessagerBase::DrawShape();
       }
   };
   
   class PCMessagerPerfect : public PCMessagerBase {
   public:
       void Login(std::string username, std::string password) override {
           std::cout << "--- PC Perfect Login ---" << std::endl;
           PCMessagerBase::PlaySound();
           PCMessagerBase::Connect();
       }
       void SendMessage(std::string message) override {
           std::cout << "--- PC Perfect SendMessage ---" << std::endl;
           PCMessagerBase::PlaySound();
           PCMessagerBase::WriteText();
       }
       void SendPicture(Image image) override {
           std::cout << "--- PC Perfect SendPicture ---" << std::endl;
           PCMessagerBase::PlaySound();
           PCMessagerBase::DrawShape();
       }
   };
   
   // --- 移动端平台的业务实现 ---
   
   class MobileMessagerLite : public MobileMessagerBase {
   public:
       void Login(std::string username, std::string password) override {
           std::cout << "--- Mobile Lite Login ---" << std::endl;
           MobileMessagerBase::Connect();
       }
       void SendMessage(std::string message) override {
           std::cout << "--- Mobile Lite SendMessage ---" << std::endl;
           MobileMessagerBase::WriteText();
       }
       void SendPicture(Image image) override {
           std::cout << "--- Mobile Lite SendPicture ---" << std::endl;
           MobileMessagerBase::DrawShape();
       }
   };
   
   class MobileMessagerPerfect : public MobileMessagerBase {
   public:
       void Login(std::string username, std::string password) override {
           std::cout << "--- Mobile Perfect Login ---" << std::endl;
           MobileMessagerBase::PlaySound();
           MobileMessagerBase::Connect();
       }
       void SendMessage(std::string message) override {
           std::cout << "--- Mobile Perfect SendMessage ---" << std::endl;
           MobileMessagerBase::PlaySound();
           MobileMessagerBase::WriteText();
       }
       void SendPicture(Image image) override {
           std::cout << "--- Mobile Perfect SendPicture ---" << std::endl;
           MobileMessagerBase::PlaySound();
           MobileMessagerBase::DrawShape();
       }
   };
   
   
   // =================================================================
   // 客户端代码 (Client)
   // =================================================================
   
   void Process() {
       // 编译时装配：选择哪个版本是在写代码时就定死的。
       // 无法在运行时动态切换平台或业务版本（除非使用工厂模式等）。
       std::cout << ">>> Using Mobile Perfect version <<<" << std::endl;
       Messager* m = new MobileMessagerPerfect();
       m->Login("user", "pass");
       m->SendMessage("Hello from Mobile!");
   
       // 清理内存
       delete m;
   }
   
   int main() {
       Process();
       return 0;
   }
   ```

## ***利用 组合 重构代码***

1. ***代码示例***
   
   ```cpp
   // 为了让代码能编译，定义一个空的 Image 类
   class Image {};
   
   // =================================================================
   // 1. 实现部分 (Implementor) - 定义了实现类的接口
   //    这部分关注的是“平台”或“底层”的功能。
   // =================================================================
   
   class MessagerImp {
   public:
       virtual void PlaySound() = 0;
       virtual void DrawShape() = 0;
       virtual void WriteText() = 0;
       virtual void Connect() = 0;
   
       virtual ~MessagerImp() {}
   };
   
   // =================================================================
   // 2. 具体实现 (Concrete Implementors) - n个平台实现
   //    这里是平台相关的具体代码。
   // =================================================================
   
   // 平台实现 A: PC 平台
   class PCMessagerImp : public MessagerImp {
   public:
       void PlaySound() override {
           std::cout << "[PC] Playing sound..." << std::endl;
       }
       void DrawShape() override {
           std::cout << "[PC] Drawing shape..." << std::endl;
       }
       void WriteText() override {
           std::cout << "[PC] Writing text..." << std::endl;
       }
       void Connect() override {
           std::cout << "[PC] Connecting..." << std::endl;
       }
   };
   
   // 平台实现 B: 移动端平台
   class MobileMessagerImp : public MessagerImp {
   public:
       void PlaySound() override {
           std::cout << "[Mobile] Playing sound..." << std::endl;
       }
       void DrawShape() override {
           std::cout << "[Mobile] Drawing shape..." << std::endl;
       }
       void WriteText() override {
           std::cout << "[Mobile] Writing text..." << std::endl;
       }
       void Connect() override {
           std::cout << "[Mobile] Connecting..." << std::endl;
       }
   };
   
   
   // =================================================================
   // 3. 抽象部分 (Abstraction) - 定义了业务逻辑的高层接口
   //    它包含一个指向“实现部分”对象的指针。
   // =================================================================
   
   class Messager {
   protected:
       MessagerImp* messagerImp; // 持有一个实现部分的引用
   
   public:
       // 构造函数，用于“桥接”实现
       Messager(MessagerImp* imp) : messagerImp(imp) {}
   
       virtual void Login(std::string username, std::string password) = 0;
       virtual void SendMessage(std::string message) = 0;
       virtual void SendPicture(Image image) = 0;
   
       virtual ~Messager() {}
   };
   
   // =================================================================
   // 4. 扩展抽象 (Refined Abstractions) - m个业务逻辑实现
   //    这里是业务需求相关的具体代码。
   // =================================================================
   
   // 业务抽象 A: 简洁版 Messager
   class MessagerLite : public Messager {
   public:
       // 继承构造函数
       MessagerLite(MessagerImp* imp) : Messager(imp) {}
   
       void Login(std::string username, std::string password) override {
           std::cout << "--- MessagerLite Login ---" << std::endl;
           messagerImp->Connect();
           // ... 其他登录逻辑 ...
       }
       void SendMessage(std::string message) override {
           std::cout << "--- MessagerLite SendMessage ---" << std::endl;
           messagerImp->WriteText();
           // ... 其他发送消息逻辑 ...
       }
       void SendPicture(Image image) override {
           std::cout << "--- MessagerLite SendPicture ---" << std::endl;
           messagerImp->DrawShape();
           // ... 其他发送图片逻辑 ...
       }
   };
   
   // 业务抽象 B: 完美版 Messager
   class MessagerPerfect : public Messager {
   public:
       // 继承构造函数
       MessagerPerfect(MessagerImp* imp) : Messager(imp) {}
   
       void Login(std::string username, std::string password) override {
           std::cout << "--- MessagerPerfect Login ---" << std::endl;
           messagerImp->PlaySound();
           messagerImp->Connect();
           // ... 其他登录逻辑 ...
       }
       void SendMessage(std::string message) override {
           std::cout << "--- MessagerPerfect SendMessage ---" << std::endl;
           messagerImp->PlaySound();
           messagerImp->WriteText();
           // ... 其他发送消息逻辑 ...
       }
       void SendPicture(Image image) override {
           std::cout << "--- MessagerPerfect SendPicture ---" << std::endl;
           messagerImp->PlaySound();
           messagerImp->DrawShape();
           // ... 其他发送图片逻辑 ...
       }
   };
   
   // =================================================================
   // 客户端代码 (Client)
   // =================================================================
   
   void Process() {
       // 运行时装配：可以自由组合“业务”和“平台”
       
       // 组合1：在PC上运行简洁版
       std::cout << ">>> Running Lite version on PC <<<" << std::endl;
       MessagerImp* pcImp = new PCMessagerImp();
       Messager* m1 = new MessagerLite(pcImp); // 使用 MessagerLite 进行实例化
       m1->Login("user", "pass");
       m1->SendMessage("Hello!");
       std::cout << std::endl;
   
   
       // 组合2：在移动端上运行完美版
       std::cout << ">>> Running Perfect version on Mobile <<<" << std::endl;
       MessagerImp* mobileImp = new MobileMessagerImp();
       Messager* m2 = new MessagerPerfect(mobileImp); // 使用 MessagerPerfect 进行实例化
       m2->Login("user", "pass");
       m2->SendMessage("Hi there!");
       std::cout << std::endl;
   
       // 清理内存
       delete m1;
       delete pcImp;
       delete m2;
       delete mobileImp;
   }
   
   int main() {
       Process();
       return 0;
   }
   ```

## ***模式定义***

1. *将抽象部分(业务功能)与实现部分(平台实现)分离，使他们都可以独立的变化*

## ***类图***

![桥模式类图](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data/imagesimage-20220529155705292.png)

## ***要点总结***

1. *Bridge模式使用“对象间的组合关系”解耦了抽象和实现之间固有的绑定关系，使得抽象和实现可以沿着各自的维度来变化。所谓抽象和实现沿着各自纬度的变化，即“子类化”它们*
2. *Bridge模式有时候类似于多继承方案，但是多继承方案往往违背单一职责原则（即一个类只有一个变化的原因），复用性比较差。Bridge模式是比多继承方案更好的解决方法。*
3. *Bridge模式的应用一般在“两个非常强的变化维度”，有时一个类也有多于两个的变化维度，这时可以使用Bridge的扩展模式。*

