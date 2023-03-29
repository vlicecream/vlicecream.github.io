# 虚幻c++基础类及相关api


## ***前言***

*Unreal 4.22.3*

*有很多功能比如StaticMesh，代码和蓝图都可以实现，但是在本篇博客是c++系列，所以蓝图并不会介绍太多，主要则是c++代码知识*

# ***虚幻基础概念***

## ***虚幻c++类的继承结构***

![虚幻c++类的继承结构](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data/202303261407447.jpg)

## ***反射 / 宏 / 垃圾回收系统***

1. *反射是程序在运行时的自检。c++没有内建的自检系统，但是是虚幻拥有自己的自检系统*

   - *这一系统的功能是融合C++数据，提供虚幻的蓝图使用*

     - *如果创建一个继承自对象的对象，那么默认是不能通过蓝图来创建一个继承它的类的。需要做的是在`UCLASS(blueprintable)`中传以蓝图可打印参数 即可*
     - *创造的变量需要使用`UPROPERTY()`来标记，并且还需要传递`BlueprintReadWrit`e参数，注意变量需要使用`Public:`来修改作用域*
     - *创造的函数需要用到UFUNCTION()，并且要传BlueprintCallable参数*

     - *举例: 垃圾回收*

       - *为了对象能被这一系统识别，需要使用专用的宏`SPECIAL_MACRO()`*

       - *任何想被虚幻引导垃圾回收系统管理的对象，都需要被使用*

         ```cpp
         SPECIAL_MACRO()
         class AMyActor : public AActor
         {
           
         }
         ```

         ```cpp
         UCLASS()  // 垃圾回收系统使用来标记对象的
         class AMyActor : public AActor
         {
          UPROPERTY()  // 标明某一个变量
          float MyFloat;
          
          UFUNCTION()  // 标明某一个函数
          void MyFunction();
         }
         ```

2. *Unreal Header Tool UHT*

   - *用来在编译时收集这个信息的程序。当遇到这个宏时，额外的代码会添加到这个对象，提供垃圾回收系统获得*
   - *这个 UHT 要求`#include "<MyActor>.generated.h"`，这个引用必须是最后一个，`MyActor`是你创建的类的名称*

## ***加快Unreal的编译速度***

*在4.22.3版本之前，如果我们没有修改任何代码的时候，重新编译，他会重新生成所有的反射代码，这样就很蠢*

*在这里教一下玄学的方法，本方法仅仅针对 win64 平台*

1. *打开此电脑，找到Unreal的安装路径*
2. *Engine -> Binaries -> win64 -> UnrealHeaderTool.target*
3. *打开 UnrealHeaderTool.target，在第二行结尾加一个空格*
4. *回到Unreal 不要动任何代码，再次编译，看看有没有效，第一次还是会有问题，第二次就会生效*

*后续版本 Unreal可能会更新，反正大家可以去验证下，毕竟很好验证，只需要随便创建一个Object类，然后转换蓝图类，就别动代码就好了*

## ***虚幻命名规范***

1. *派生自 Actor 的类带有 A 前缀，如 AController*
2. *派生自 Object 的类有 U 前缀，如UComponent*
3. *Enums 的前缀是E，如EFortificationType*
4. *Interface 的前缀是 I，如IAbilitySystemInterface*
5. *Template 的前缀是T，如TArray*
6. *派生自 SWidget的类(Slate UI) 带有前缀S， 如SButton*
7. *其他类的前缀为字母F，如FVector*
8. *bool 的前缀是 b 开头*

## ***c++与blueprint的关系***

*在Unreal里，其实不需要写代码，光使用蓝图也是可以做出来一个游戏的，那同理，光使用代码，不使用蓝图，也是可以做出一款游戏的*

*但是明眼人都知道，这是两个极端，肯定不可取，所以我们要利用好蓝图和c++的优点配合使用*

*我们一般都是用c++代码来完成底层逻辑，然后在蓝图上实现一些简单拓展*

## ***蓝图类***

1. *Blueprintable*
   - *如果想要让一个类被蓝图继承，成为蓝图类，那么就可以`UCLASS(Blurprintable)`*
   - *在Unreal编译后，这时候就可以在Unreal里把这个类转换成蓝图类*
2. *流程*
   - *在Content下创建文件夹"Blueprints"，专门放置蓝图类*
   - *我们在建立好的c++类中，右键 创建基于MyObject的蓝图类，名字取为"BP_MyObject"，存放到Blueprints文件夹中*
   - *随后在这个蓝图类中 右键查询自己的成员属性，成员方法，就可以使用啦*

## ***如何删除自定义的c++类***

1. *去找到相对应的文件夹删除就好了，在UE，至少在4里面是不行的*
   - *将你想删除的c++类的派生类和有关的蓝图这些的全部删掉*

   - *在项目里找到 sources，然后选中项目名，删除自己想删除的类文件就好了，但是注意有些东西是不能删的哦*
   - *工程目录找到文件夹Binaries 直接删除这个文件夹*

# ***虚幻基础知识***

## ***类的默认生成代码***

### ***默认生成的UObject代码***

*我们在c++类中生成了Myobject类后，vs就会帮我们生成 .h .cpp 两个文件，默认是没有任何方法*

*因为默认代码中的宏没有传参数，也就是没有告诉他这个类要承担什么反射角色，所以默认的UObject类是不能转换成蓝图类的*

### ***默认生成的Actor代码***

1. *MyActor.h*

   ```cpp
   #pragma once
   
   #include "CoreMinimal.h"
   #include "GameFramework/Actor.h"
   #include "MyActor.generated.h"
   
   UCLASS()
   class BASICTRAINING_API AMyActor : public AActor
   {
   	GENERATED_BODY()
   	
   public:	
   	// Sets default values for this actor's properties
   	AMyActor();
   
   protected:
   	// 在游戏开始或生成时调用
   	virtual void BeginPlay() override;
   
   public:	
   	// 每一帧都会调用
   	virtual void Tick(float DeltaTime) override;
   
   };
   ```

2. *MyActor.cpp*

   ```cpp
   #include "MyActor.h"
   
   // Sets default values
   AMyActor::AMyActor()
   {
    	// 将此 actor 设置为每帧调用 Tick()。如果不需要，可以关闭它以提高性能
   	PrimaryActorTick.bCanEverTick = true;
   }
   
   // 在游戏开始或生成时调用
   void AMyActor::BeginPlay()
   {
   	Super::BeginPlay();
   	
   }
   
   // 每一帧都会调用
   void AMyActor::Tick(float DeltaTime)
   {
   	Super::Tick(DeltaTime);
   
   }
   ```

### ***默认生成的Pawn代码***

1. *MyPawn.h*

   ```cpp
   #pragma once
   
   #include "CoreMinimal.h"
   #include "GameFramework/Pawn.h"
   #include "MyPawn.generated.h"
   
   UCLASS()
   class BASICTRAINING_API AMyPawn : public APawn
   {
   	GENERATED_BODY()
   
   public:
   	// Sets default values for this pawn's properties
   	AMyPawn();
   
   protected:
   	// 在游戏开始或生成时调用
   	virtual void BeginPlay() override;
   
   public:	
   	// 每一帧调用
   	virtual void Tick(float DeltaTime) override;
   
   	// 调用以将功能绑定到输入
   	virtual void SetupPlayerInputComponent(class UInputComponent* PlayerInputComponent) override;
   };
   ```

2. *MyPawn.cpp*

   ```cpp
   #include "MyPawn.h"
   
   // Sets default values
   AMyPawn::AMyPawn()
   {
    	// 将此 pawn 设置为每帧调用 Tick()。如果不需要，可以关闭它以提高性能
   	PrimaryActorTick.bCanEverTick = true;
   }
   
   // 在游戏开始或生成时调用
   void AMyPawn::BeginPlay()
   {
   	Super::BeginPlay();
   }
   
   // 每一帧调用
   void AMyPawn::Tick(float DeltaTime)
   {
   	Super::Tick(DeltaTime);
   }
   
   // 调用以将功能绑定到输入
   void AMyPawn::SetupPlayerInputComponent(UInputComponent* PlayerInputComponent)
   {
   	Super::SetupPlayerInputComponent(PlayerInputComponent);
   }
   ```

   

## ***蓝图类***

### ***UObject代码***

- *MyObject.h*

  ```cpp
  UCLASS(Blueprintable)
  class BASICTRAINING_API UMyObject : public UObject
  {
  	GENERATED_BODY()
  public:
  	UMyObject();
      
  	UPROPERTY(BlueprintReadWrite)
  	float MyFloat;
      
  	UFUNCTION(BlueprintCallable)
  	void MyFunction();
  };
  ```
  
- *MyObject.cpp*

  ```cpp
  #include "MyObject.h"
  UMyObject::UMyObject() { }
  void UMyObject::MyFunction() { }
  ```

### ***在蓝图中实例化继承于Object的类***

1. *问题*
   - *Object是不能放在场景中的哦~ 那么怎么才能将继承于Object的类放置在场景中，那就是关卡蓝图，一般用来做于测试，摄像机，或者属于这个关卡的独特配置*
2. *流程*
   - *Blueprints -> Open Level Blueprint*
   - ![image-20230326164453845](C:\Users\Xu\Desktop\Unreal Note\image-20230326164453845.png)

### ***Actor / Pawn*** 

*这些都是可以直接转换蓝图类的，因为他们的父类以及在宏中进行了反射*

## ***打印日志***

### ***UE_LOG( )***

- *第一个变量 - 输出日志类型，可以写LogTemp，也可以自己自定义*
- *第二个变量 - 输出级别*
  - *Log*
  - *Warning*
  - *Error*
- *打印的内容*
  - *不是简单的字符串，需要加"TEXT()"*
  - *如`TEXT("Hello Unreal")`*

- *基于以上 MyObject 的代码改动*
  - *MyObject,h*

  ```cpp
  UCLASS(Blueprintable)
  class BASICTRAINING_API UMyObject : public UObject
  {
  	GENERATED_BODY()
  	
  public:
  	UMyObject();
  
  	UPROPERTY(BlueprintReadOnly, Category = "My Variables")
  	float MyFloat;
  
  	UFUNCTION(BlueprintCallable, Category = "My Functions")
  	void MyFunction();
  };
  ```

  - *MyObject.cpp*

  ```cpp
  #include "MyObject.h"
  
  UMyObject::UMyObject() 
  {
  	MyFloat = 0.0f;
  }
  
  void UMyObject::MyFunction()
  {
      UE_LOG(LogTemp, Log, TEXT("This is Log"));
      UE_LOG(LogTemp, Warning, TEXT("This is Warning"));
      UE_LOG(LogTemp, Error, TEXT("This is Error"));
  }
  ```

## ***组件***

### ***DefaultSceneRoot***

- *在c++类转换为蓝图类的时候，默认增加的一个组件*
- *他的作用就是给蓝图标明位置，和显示为一个球体*

### ***静态网格组件***

1. *作用*

   - *最简单的一个组件，他就是为你在世界中渲染出来一个静态网格*

2. *调用其方法要导入的头文件*

   - *`#include "Components/StaticMeshComponent.h"`*

3. *代码示例*

   - *MyActor.h*

     ```cpp
     UCLASS()
     class BASICTRAINING_API AMyActor : public AActor
     {
     	GENERATED_BODY()
     	
     public:	
     	...
     	UPROPERTY(VisibleAnywhere, Category = "My Actor Component")
     	UStaticMeshComponent* MyStaticMesh;
     	...
     };
     ```

   - *MyActor.cpp*

     ```cpp
     #include "MyActor.h"
     
     // Sets default values
     AMyActor::AMyActor()
     {
     	...
     	// 注意传参是一个标识，在一个类中不能重复，不会再蓝图显示出来，一般再报错上显示
     	MyStaticMesh = CreateDefaultSubobject<UStaticMeshComponent>(TEXT("MyStaticMesh"));
     }
     ```

### ***根组件***

1. *`RootComponent`并不需要声明，他是 Unreal 帮你声明好的一个变量*

   ```cpp
   RootComponent = CreateDefaultSubobject<USceneComponent>(TEXT("RootComponent"));
   ```

2. *将组件附加到根组件*

   - *不同的组件里面会持有`SetupAttachment();`方法的，但是要引入相对应的头文件*

     *`GetRootComponent()`方法的作用就是获得根组件*

     ```cpp
     SetupAttachment(GetRootComponent());
     ```

   - *eg：将静态网格组件附加到根组件*
     - *h 文件*

       ```cpp
       UCLASS()
       class BASICTRAINING_API AMyPawn : public APawn
       {
       	GENERATED_BODY()
       
       public:
       ...
       	// 声明静态网格
       	UPROPERTY(VisibleAnywhere, Category = "My Actor Component")
       	UStaticMeshComponent* MyStaticMesh;
       ...
       };
       
       ```

     - *MyPawn.cpp*

       ```cpp
       #include "MyPawn.h"
       #include "Components/StaticMeshComponent.h"
       
       // Sets default values
       AMyPawn::AMyPawn()
       {
       ...
       	// 初始化场景组件 - 根组件
       	RootComponent = CreateDefaultSubobject<USceneComponent>(TEXT("RootComponent"));
       	// 初始化静态网格组件
       	MyStaticMesh = CreateDefaultSubobject<UStaticMeshComponent>(TEXT("MyStaticMesh"));
       	// 将静态网格附加到根组件
       	MyStaticMesh->SetupAttachment(GetRootComponent());
       ...
       }
       ```

### ***相机组件***

1. *作用*

   - *对相机进行操作，例如设置初始位置*

2. *调用其方法要导入的头文件*

   - `#include "Camera/CameraComponent.h"`

3. *示例代码*

   - *MyPawn.h*

     ```cpp
     UCLASS()
     class BASICTRAINING_API AMyPawn : public APawn
     {
     	GENERATED_BODY()
     
     public:
     ...
     	// 声明相机组件
     	class UCameraComponent* MyCamera;
     ...
     };
     ```

   - *MyPawn.cpp*

     ```cpp
     #include "MyPawn.h"
     #include "Camera/CameraComponent.h"
     
     // Sets default values
     AMyPawn::AMyPawn()
     {
     ...
     	// 初始化场景组件 - 根组件
     	RootComponent = CreateDefaultSubobject<USceneComponent>(TEXT("RootComponent"));
     
     	// 初始化相机组件，并附加到根组件
     	MyCamera = CreateDefaultSubobject<UCameraComponent>(TEXT("MyCamera"));
     	MyCamera->SetupAttachment(GetRootComponent());
         
         // 设置相机组件和附组件的相对位置
         MyCamera->SetRelativeLocation(FVector(-300.0f, 0.0f, 300.0f));
     	MyCamera->SetRelativeRotation(FRotator(-45.0f, 0.0f, 0.0f));
     }
     ```

### ***SpringArm组件***

1. *作用*

   - *类似于拍电影的摄像头悬臂的作用，比如你穿墙，然后就会调整视角，不会被墙挡住*

2. *调用其方法要导入的头文件*

   - `#include "GameFramework/SpringArmComponent.h"`

3. *代码示例*

   - *MyPawn.h*

     ```cpp
     UCLASS()
     class BASICTRAINING_API AMyPawn : public APawn
     {
     	GENERATED_BODY()
     
     public:
     ...
     	// 声明悬臂组件
     	class USpringArmComponent* MySpringArm;
     ...
     };
     ```

   - *MyPawn.cpp*

     ```cpp
     #include "MyPawn.h"
     #include "Components/StaticMeshComponent.h"
     #include "Camera/CameraComponent.h"
     #include "Components/InputComponent.h"
     #include "GameFramework/SpringArmComponent.h"
     
     // Sets default values
     AMyPawn::AMyPawn()
     {
     ...
     	// 初始化静态网格组件并附加到根组件
     	MyStaticMesh = CreateDefaultSubobject<UStaticMeshComponent>(TEXT("MyStaticMesh"));
     	MyStaticMesh->SetupAttachment(GetRootComponent());
     
     	// 初始化SpringArm组件
     	MySpringArm = CreateDefaultSubobject<USpringArmComponent>(TEXT("MySpringArm"));
     	MySpringArm->SetupAttachment(MyStaticMesh);
     	MySpringArm->RelativeRotation = FRotator(-45.0f, 0.0f, 0.0f);
     	MySpringArm->TargetArmLength = 400.0f;
     	MySpringArm->bEnableCameraLag = true;
     	MySpringArm->CameraLagSpeed = 3.0f;
     ...
     }
     ```

## ***关于位置的基本api***

### ***SetActorLocation***

- *只有第一个参数必传，其他都有默认值，第一个参数传 FVector 类型即可*
  1. *"FVector" Type*

- *`SetActorLocation(FVector(0.0f));`*

### ***GetActorLocation***

- *获取当前Actor的位置坐标*
- `PlacedLocation = GetActorLocation();`

### ***AddActorLocalOffset***

- *Actor移动偏移量*
- *参数介绍*
  - *第一个参数 - 移动的偏移坐标系 （必传）*
  - *第二个参数 - 如果设为true并且没有开启物理模拟，碰到障碍物则就是一个遮挡的感觉 （default = false）*
  - *第三个参数 - FHitResult reference，碰撞信息，比如碰撞到物体时的坐标点（default = nullptr）*
    - *`HitResult.Location.x`，`HitResult.Location.z`，`HitResult.Location.z`*
- *`AddActorLocalOffset(FVector(0.0f), true, HitResult);`*

## ***移动***

### ***轴按键映射***

- *Settings -> Project Settings -> Input -> Axis Mappings*
- *加了轴按键映射后，还需要在代码中去绑定轴按键*

### ***轴按键绑定***

- *需要在pawn类中加入对按键也就是移动的处理方法，然后在Pawn的`SetupPlayerInputComponent()`函数中利用`BindAxis()`去处理*

  ```cpp
  // Called to bind functionality to input
  void AMyPawn::SetupPlayerInputComponent(UInputComponent* PlayerInputComponent)
  {
  	Super::SetupPlayerInputComponent(PlayerInputComponent);
  	PlayerInputComponent->BindAxis(TEXT("MoveForward"), this, &AMyPawn::MoveForward);
  	PlayerInputComponent->BindAxis(TEXT("MoveRight"), this, &AMyPawn::MoveRight);
  }
  
  
  void AMyPawn::MoveForward(float Value)
  {
  }
  
  void AMyPawn::MoveRight(float Value)
  {
  }
  ```

### ***利用 Tick 去实现移动的处理方法***

1. *MyPawn.h*

   ```cpp
   UCLASS()
   class BASICTRAINING_API AMyPawn : public APawn
   {
   	GENERATED_BODY()
   
   public:
   ...
   	// 最大速度值
   	UPROPERTY(EditAnywhere, Category = "My Pawn Movement")
   	float MaxSpeed;
   ...
   
   public:	
   	// Called every frame
   	virtual void Tick(float DeltaTime) override;
   
   	// Called to bind functionality to input
   	virtual void SetupPlayerInputComponent(class UInputComponent* PlayerInputComponent) override;
   
   private:
   	void MoveForward(float Value);
   	void MoveRight(float Value);
   	FVector Velocity;
   };
   ```

2. *MyPawn.cpp*

   ```cpp
   // Called every frame
   void AMyPawn::Tick(float DeltaTime)
   {
   	Super::Tick(DeltaTime);
   
   	AddActorLocalOffset(Velocity * DeltaTime, true);
   }
   
   // Called to bind functionality to input
   void AMyPawn::SetupPlayerInputComponent(UInputComponent* PlayerInputComponent)
   {
   	Super::SetupPlayerInputComponent(PlayerInputComponent);
   	PlayerInputComponent->BindAxis(TEXT("MoveForward"), this, &AMyPawn::MoveForward);
   	PlayerInputComponent->BindAxis(TEXT("MoveRight"), this, &AMyPawn::MoveRight);
   }
   
   void AMyPawn::MoveForward(float Value)
   {
   	// Clamp 是限制这个值的大小
   	Velocity.X = FMath::Clamp(Value, -1.0f, 1.0f) * MaxSpeed;
   }
   
   void AMyPawn::MoveRight(float Value)
   {
   	// Clamp 是限制这个值的大小
   	Velocity.Y = FMath::Clamp(Value, -1.0f, 1.0f) * MaxSpeed;
   }
   
   ```

### ***DeltaTime***

*DeltaTime能够帮助我们进行优化，对于那些性能较高的电脑和性能较低的电脑保持一样的情况*

![image-20230327142109824](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data%5Cimage-20230327142109824.png)

## ***旋转控制视野***

## ***通过代码增加力与力矩***

*需要导入头文件哦，而且这是 UStaticMeshComponent 的成员方法*

*`#include "Components/StaticMeshComponent.h"`*

1. *AddForce*

- *传参*
  - *第一个参数 - 施加多大的力 （必传）*
  - *第二个参数 - 施加在什么骨骼（default = "Name_None"）*
  - *第三个参数 - 是否无视质量，直接给加速度 （default = false）*

- *`MyStaticMesh->AddForce(FVector(0.0f), "Name_None", true);`*

2. *AddTorque*

   - *第一个参数 - 施加多大的力 （必传）*
   - *第二个参数 - 施加在什么骨骼（default = "Name_None"）*
   - *第三个参数 - 是否无视质量，直接给加速度 （default = false）*

- *`MyStaticMesh->AddTorque(FVector(0.0f), "Name_None", true)`*

## ***player 0 自动持有该pawn***

`AutoPossessPlayer = EAutoReceiveInput::Player0;`

## ***设置模型与材质的默认值***

*我们可以利用`#include "UObject/ConstructorHelpers.h"`文件来设置，它是一个静态文件，所以必须要用static*

- *MyPawn.h*

  ```cpp
  UCLASS()
  class BASICTRAINING_API AMyPawn : public APawn
  {
  	GENERATED_BODY()
  
  public:
  ...
  	// 声明静态网格
  	UPROPERTY(VisibleAnywhere, Category = "My Actor Component")
  	UStaticMeshComponent* MyStaticMesh;
  ...
  };
  ```

- *MyPawn.cpp*

  ```cpp
  #include "MyPawn.h"
  #include "Components/StaticMeshComponent.h"
  #include "Camera/CameraComponent.h"
  #include "Components/InputComponent.h"
  #include "GameFramework/SpringArmComponent.h"
  #include "UObject/ConstructorHelpers.h"
  
  // Sets default values
  AMyPawn::AMyPawn()
  {
  ...
  
  	// 初始化场景组件 - 根组件
  	RootComponent = CreateDefaultSubobject<USceneComponent>(TEXT("RootComponent"));
  
  	// 初始化静态网格组件并附加到根组件
  	MyStaticMesh = CreateDefaultSubobject<UStaticMeshComponent>(TEXT("MyStaticMesh"));
  	MyStaticMesh->SetupAttachment(GetRootComponent());
  
  	// 给静态网格组件增加默认的静态网格和默认的材质
  	static ConstructorHelpers::FObjectFinder<UStaticMesh> StaticMeshAsset(TEXT("xxx"));
  	static ConstructorHelpers::FObjectFinder<UMaterialInterface> MaterialAsset(TEXT("xxx"));
  	if (StaticMeshAsset.Succeeded() && MaterialAsset.Succeeded()) {
  		MyStaticMesh->SetStaticMesh(StaticMeshAsset.Object);
  		MyStaticMesh->SetMaterial(0, MaterialAsset.Object);
  		MyStaticMesh->SetWorldScale3D(FVector(0.5f));
  	}
  ...
  }
  
  ```

## 

# ***基础宏参数***

## ***基础宏参数介绍***

1. *BlueprintReadWrite*
- *在蓝图中可以进行get and set*
  
2. *BlueprintReadOnly*

   - *只读，在蓝图中只有get，没有set*

3. *BlueprintCallable*

   - *在蓝图中可以被调用*

4. *Category*

   - *`Category = "My Functions"` 这个是分类到"My Functions"的意思，可以在蓝图调用的时候显示出来，就是分类本身的作用*
   - *如果还想弄子分类那就加 "|" 如：`Category = "My Actor Properties | Vector"`*

5. *VisibleAnywhere*

   - *在组件用的比较多，作用就是在任何地方都可以看见他*

6. *EditInstanceOnly*

   - *只允许在实例上修改，（也就是在场景实例化之后的编辑栏去编辑，不能再未实例化的地方编辑）*

7. *VisibleInstanceOnly*

   - *只允许在实例上查看，（也就是在场景实例化之后的编辑栏去编辑，不能再未实例化的地方编辑）*

8. *EditDefaultsOnly*

   - *只允许在默认值上修改，而且还是统一修改（也就是直接打开蓝图类的编辑版，非实例化场景）*

9. *VisibleDefaultsOnly*

   - *只允许在默认值上查看（也就是直接打开蓝图类的编辑版，非实例化场景）*

10. *EditAnywhere*

    - *无论在哪都可以修改*
    - *千万不要将组件指针设为EditAnywhere*

11. *meta*

    - *Clamp系列可以限制输入值的范围 / UI系列可以限制输入框鼠标拖动值的范围*

    - `meta = (ClampMin = -5.0f, ClampMax = 5.0f, UIMin = -5.0f, UIMax = 5.0f)`
