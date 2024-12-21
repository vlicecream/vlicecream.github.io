# 桥模式


# 桥模式

## 单一职责模式

1. ***什么是单一职责***
   - *在软件组件的设计中，如果责任划分的不清晰，使用继承得到的结果往往是随着需求的变化，子类急剧膨胀，同时充满着重复代码*
   - *这时候最关键是划清责任，每个类引起他变化的原因只有一个*
2. ***典型模式***
   - *Decorator*
   - *Bridge*

## 使用桥模式动机

1. *由于某些类型的固有的实现逻辑 使得它们具有两个变化的维度 乃至多个维度的变化*
2. *如何应对这种"多维度的变化" 如何利用面向对象技术来使得类型可以轻松的沿着两个乃至多个方向变化 而不引入额外的复杂度*

## 初始代码

1. ***代码演示***
   
   - ```cpp
     class Messager{
     public:
         virtual void Login(string username, string password)=0;
         virtual void SendMessage(string message)=0;
         virtual void SendPicture(Image image)=0;
     
         virtual void PlaySound()=0;
         virtual void DrawShape()=0;
         virtual void WriteText()=0;
         virtual void Connect()=0;
     
         virtual ~Messager(){}
     };
     ```

     //平台实现
    
     class PCMessagerBase : public Messager{
     public:
         virtual void PlaySound(){
             //**********
         }
         virtual void DrawShape(){
             //**********
         }
         virtual void WriteText(){
             //**********
         }
         virtual void Connect(){
             //**********
         }
     };
    
     class MobileMessagerBase : public Messager{
     public:
         virtual void PlaySound(){
             //==========
         }
         virtual void DrawShape(){
             //==========
         }
         virtual void WriteText(){
             //==========
         }
         virtual void Connect(){
             //==========
         }
     };
    
     //业务抽象
     class PCMessagerLite : public PCMessagerBase {
     public:
    
         virtual void Login(string username, string password){
    
             PCMessagerBase::Connect();
             //........
         }
         virtual void SendMessage(string message){
    
             PCMessagerBase::WriteText();
             //........
         }
         virtual void SendPicture(Image image){
    
             PCMessagerBase::DrawShape();
             //........
         }
     };
    
    
    
     class PCMessagerPerfect : public PCMessagerBase {
     public:
    
         virtual void Login(string username, string password){
    
             PCMessagerBase::PlaySound();
             //********
             PCMessagerBase::Connect();
             //........
         }
         virtual void SendMessage(string message){
    
             PCMessagerBase::PlaySound();
             //********
             PCMessagerBase::WriteText();
             //........
         }
         virtual void SendPicture(Image image){
    
             PCMessagerBase::PlaySound();
             //********
             PCMessagerBase::DrawShape();
             //........
         }
     };
    
    
     class MobileMessagerLite : public MobileMessagerBase {
     public:
    
         virtual void Login(string username, string password){
    
             MobileMessagerBase::Connect();
             //........
         }
         virtual void SendMessage(string message){
    
             MobileMessagerBase::WriteText();
             //........
         }
         virtual void SendPicture(Image image){
    
             MobileMessagerBase::DrawShape();
             //........
         }
     };
    
    
     class MobileMessagerPerfect : public MobileMessagerBase {
     public:
    
         virtual void Login(string username, string password){
    
             MobileMessagerBase::PlaySound();
             //********
             MobileMessagerBase::Connect();
             //........
         }
         virtual void SendMessage(string message){
    
             MobileMessagerBase::PlaySound();
             //********
             MobileMessagerBase::WriteText();
             //........
         }
         virtual void SendPicture(Image image){
    
             MobileMessagerBase::PlaySound();
             //********
             MobileMessagerBase::DrawShape();
             //........
         }
     };
    
    
     void Process(){
             //编译时装配
             Messager *m =
                 new MobileMessagerPerfect();
     }
    
    
    
    
     ```

## 利用 组合 重构代码

1. ***代码示例***
   
   - ```cpp
     class Messager{
     protected:
          MessagerImp* messagerImp;//...
     public:
         virtual void Login(string username, string password)=0;
         virtual void SendMessage(string message)=0;
         virtual void SendPicture(Image image)=0;
     
         virtual ~Messager(){}
     };
     
     class MessagerImp{
     public:
         virtual void PlaySound()=0;
         virtual void DrawShape()=0;
         virtual void WriteText()=0;
         virtual void Connect()=0;
     
         virtual MessagerImp(){}
     };
     ```

     //平台实现 n
     class PCMessagerImp : public MessagerImp{
     public:
    
         virtual void PlaySound(){
             //**********
         }
         virtual void DrawShape(){
             //**********
         }
         virtual void WriteText(){
             //**********
         }
         virtual void Connect(){
             //**********
         }
     };
    
     class MobileMessagerImp : public MessagerImp{
     public:
    
         virtual void PlaySound(){
             //==========
         }
         virtual void DrawShape(){
             //==========
         }
         virtual void WriteText(){
             //==========
         }
         virtual void Connect(){
             //==========
         }
     };
    
    
    
     //业务抽象 m
    
     //类的数目：1+n+m
    
     class MessagerLite :public Messager {
    
    
     public:
    
         virtual void Login(string username, string password){
    
             messagerImp->Connect();
             //........
         }
         virtual void SendMessage(string message){
    
             messagerImp->WriteText();
             //........
         }
         virtual void SendPicture(Image image){
    
             messagerImp->DrawShape();
             //........
         }
     };
    
    
    
     class MessagerPerfect  :public Messager {
     public:
         virtual void Login(string username, string password){
    
             messagerImp->PlaySound();
             //********
             messagerImp->Connect();
             //........
         }
         virtual void SendMessage(string message){
    
             messagerImp->PlaySound();
             //********
             messagerImp->WriteText();
             //........
         }
         virtual void SendPicture(Image image){
    
             messagerImp->PlaySound();
             //********
             messagerImp->DrawShape();
             //........
         }
     };
    
     void Process(){
         //运行时装配
         MessagerImp* mImp=new PCMessagerImp();
         Messager *m =new Messager(mImp);
     }
     ```

## 模式定义

1. *将抽象部分(业务功能)与实现部分(平台实现)分离，使他们都可以独立的变化*

## 类图

![桥模式类图](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data/imagesimage-20220529155705292.png)

## 要点总结

1. *Bridge模式使用“对象间的组合关系”解耦了抽象和实现之间固有的绑定关系，使得抽象和实现可以沿着各自的维度来变化。所谓抽象和实现沿着各自纬度的变化，即“子类化”它们*
2. *Bridge模式有时候类似于多继承方案，但是多继承方案往往违背单一职责原则（即一个类只有一个变化的原因），复用性比较差。Bridge模式是比多继承方案更好的解决方法。*
3. *Bridge模式的应用一般在“两个非常强的变化维度”，有时一个类也有多于两个的变化维度，这时可以使用Bridge的扩展模式。*

