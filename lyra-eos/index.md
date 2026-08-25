# Lyra - EOS


## ***什么是 Lyra 的 EOS 实现***

*Lyra 并没有把 EOS SDK 直接写进 Gameplay 代码里。它的在线系统是一个“配置驱动 + CommonUser / CommonSession 抽象”的结构：玩法层只关心登录、开房、搜房、加入、跳转，真正底层是 Null、Steam、EOS 还是 SteamEOS，由 Target 和 ini 配置决定。*

- ***玩法层***：`LyraGame`、前端流程、GameMode 只调用 `CommonUserSubsystem` 和 `CommonSessionSubsystem`。
- ***抽象层***：`CommonUser` 负责用户登录、权限检查、平台用户和本地玩家绑定；`CommonSession` 负责 Host / Find / Join / Travel。
- ***在线服务层***：当前工程默认走 OSSv1，也就是 `OnlineSubsystem`、`IOnlineIdentity`、`IOnlineSession` 这套接口。
- ***EOS Provider***：当使用 `LyraGameEOS` 或 `LyraServerEOS` Target 时，默认 OnlineSubsystem 会变成 `OnlineSubsystemEOS`。

所以它的整体关系更像这样：

```text
Lyra Gameplay / UI / GameMode
        ↓
CommonUser / CommonSession
        ↓
OnlineSubsystem OSSv1 接口
        ↓
OnlineSubsystemEOS 插件
        ↓
EOS SDK / Epic Online Services
```

---

## ***它到底用的是 OSS 还是 Online Services***

### *1. 当前 CommonUser 默认走 OSSv1*

`Plugins/CommonUser/Source/CommonUser/CommonUser.Build.cs` 里有一个关键开关：

```csharp
bool bUseOnlineSubsystemV1 = true;
```

这个值为 `true` 时，CommonUser 会：

- ***依赖***：`OnlineSubsystem`。
- ***定义宏***：`COMMONUSER_OSSV1=1`。
- ***使用接口***：`IOnlineSubsystem`、`IOnlineIdentity`、`IOnlineSession`、`IOnlineExternalUI`、`IOnlinePresence`。

也就是说，虽然 EOS Target 会启用 `OnlineServicesEOS`，但这份 Lyra 代码里的 CommonUser / CommonSession 主路径还是 OSSv1。

### *2. OSSv2 路径存在，但不是当前主路*

代码里能看到 `#if COMMONUSER_OSSV1 / #else` 两套分支：

```cpp
#if COMMONUSER_OSSV1
    Online::GetSubsystem(GetWorld());
    OnlineSub->GetIdentityInterface();
    OnlineSub->GetSessionInterface();
#else
    GetServices(GetWorld(), EOnlineServices::Default);
    OnlineServices->GetAuthInterface();
    OnlineServices->GetLobbiesInterface();
#endif
```

- ***OSSv1***：老 OnlineSubsystem 接口，Lyra 当前实际使用。
- ***OSSv2***：`OnlineServicesInterface` / `OnlineServicesEOS` 新接口，代码有保留，但不少地方仍有 TODO。

所以学习这份工程时，先按 OSSv1 理解最准确。

---

## ***EOS 是怎么被启用的***

### *1. 默认工程不是 EOS*

普通 `Config/DefaultEngine.ini` 里默认是：

```ini
[OnlineServices]
DefaultServices=Null
```

这说明普通 Lyra 默认是本地 Null Online，不是 EOS。

### *2. EOS Target 会指定 CustomConfig*

`Source/LyraGameEOS.Target.cs` 的核心是：

```csharp
CustomConfig = "EOS";

EnablePlugins.AddRange(
    new string[]
    {
        "OnlineServicesEOS",
        "OnlineSubsystemEOS"
    }
);

OptionalPlugins.Add("EOSReservedHooks");
```

这代表构建 `LyraGameEOS` 时，会额外读取：

```text
Config/Custom/EOS/DefaultEngine.ini
```

Dedicated Server 也有对应版本：

```text
LyraServerEOS.Target.cs
LyraServerSteamEOS.Target.cs
```

### *3. Custom EOS 配置切换底层 OnlineSubsystem*

`Config/Custom/EOS/DefaultEngine.ini` 里最关键的是：

```ini
[OnlineSubsystem]
DefaultPlatformService=EOS

[OnlineSubsystemEOS]
bEnabled=true

[OnlineServices]
DefaultServices=Epic
```

这三段的意思是：

- ***DefaultPlatformService=EOS***：OSSv1 的默认 OnlineSubsystem 变成 EOS。
- ***bEnabled=true***：启用 `OnlineSubsystemEOS`。
- ***DefaultServices=Epic***：OSSv2 的默认服务设为 Epic；虽然当前 CommonUser 主路是 OSSv1，但配置也给 OSSv2 留了入口。

---

## ***EOSSettings 负责哪些能力***

EOS 自己的设置在：

```ini
[/Script/OnlineSubsystemEOS.EOSSettings]
bEnableOverlay=True
bEnableSocialOverlay=True
bEnableEditorOverlay=True
bUseEAS=True
bUseEOSConnect=True
bMirrorStatsToEOS=True
bMirrorAchievementsToEOS=True
bUseEOSSessions=True
bMirrorPresenceToEAS=True
```

这些开关可以这样理解：

- ***bUseEAS***：使用 Epic Account Services，用于 Epic 账号体系登录。
- ***bUseEOSConnect***：使用 EOS Connect，用于跨平台产品用户身份。
- ***bUseEOSSessions***：Session 使用 EOS 后端能力。
- ***bMirrorPresenceToEAS***：把 Presence 同步到 Epic Account 侧。
- ***bEnableOverlay / bEnableSocialOverlay***：启用 EOS Overlay 和社交 Overlay。
- ***bMirrorStatsToEOS / bMirrorAchievementsToEOS***：把统计和成就同步到 EOS。

实际项目上线时，还要填自己的 Artifact 信息：

```ini
DefaultArtifactName=ARTIFACTNAME
+Artifacts=(ArtifactName="ARTIFACTNAME",ClientId="CLIENTID",ClientSecret="CLIENTSECRET",ProductId="PRODUCTID",SandboxId="SANDBOXID",DeploymentId="DEPLOYTMENTID",EncryptionKey="ENCRYPTIONKEY")
```

Lyra 示例里这些值默认是注释状态，不能直接拿来上线。

---

## ***EOS NetDriver 是怎么接进来的***

EOS 配置还替换了网络驱动：

```ini
[/Script/Engine.Engine]
!NetDriverDefinitions=ClearArray
+NetDriverDefinitions=(DefName="GameNetDriver",DriverClassName="/Script/SocketSubsystemEOS.NetDriverEOS",DriverClassNameFallback="/Script/OnlineSubsystemUtils.IpNetDriver")
+NetDriverDefinitions=(DefName="BeaconNetDriver",DriverClassName="/Script/SocketSubsystemEOS.NetDriverEOS",DriverClassNameFallback="/Script/OnlineSubsystemUtils.IpNetDriver")
+NetDriverDefinitions=(DefName="DemoNetDriver",DriverClassName="/Script/Engine.DemoNetDriver",DriverClassNameFallback="/Script/Engine.DemoNetDriver")
```

- ***GameNetDriver***：正常玩家联机走 EOS Socket NetDriver。
- ***BeaconNetDriver***：Party / Reservation Beacon 也走 EOS Socket。
- ***Fallback***：如果 EOS NetDriver 不可用，会退回 `IpNetDriver`。

这就是为什么 Lyra 的 `ClientTravel` 最后拿到的连接字符串可以交给 EOS 网络层处理。

---

## ***CommonUser 登录流程***

### *1. 前端普通玩家登录*

前端流程在 `ULyraFrontendStateComponent` 里排了几个步骤：

```cpp
QueueStep("Wait For User Initialization")
QueueStep("Try Show Press Start Screen")
QueueStep("Try Join Requested Session")
QueueStep("Try Show Main Screen")
```

如果平台不需要 Press Start，Lyra 会直接：

```cpp
UserSubsystem->TryToInitializeForLocalPlay(0, FInputDeviceId(), false);
```

这个函数内部请求的是：

```cpp
Params.RequestedPrivilege = ECommonUserPrivilege::CanPlay;
```

所以普通前端进入游戏时，默认只要求“能玩本地游戏”，不一定强制在线。

### *2. Dedicated Server 登录*

Dedicated Server 启动时，`ALyraGameMode::TryDedicatedServerLogin()` 会检查自己是不是在默认地图上运行。如果是，它会调用：

```cpp
UserSubsystem->TryToLoginForOnlinePlay(0);
```

这个函数内部请求的是：

```cpp
Params.RequestedPrivilege = ECommonUserPrivilege::CanPlayOnline;
```

成功或失败后都会继续：

```cpp
HostDedicatedServerMatch(ECommonSessionOnlineMode::Online);
```

这说明 Lyra 的 Dedicated Server 登录失败不一定阻止开服。注释里也写得很清楚：一些 OnlineSubsystem 可能期望服务器登录，但 Dedicated Server 本身不强制依赖用户登录。

### *3. CommonUser 的实际登录步骤*

`LoginLocalUser()` 会创建 `FUserLoginRequest`，然后进入 `ProcessLoginRequest()`。

它的大致顺序是：

```text
检查当前是否已经 LoggedIn
    ↓
尝试 TransferPlatformAuth
    ↓
尝试 AutoLogin
    ↓
尝试 ShowLoginUI
    ↓
检查 CanPlay / CanPlayOnline 权限
    ↓
绑定 LocalPlayer / PlayerState 的 UniqueNetId
```

OSSv1 下 `TransferPlatformAuth` 直接返回 `false`，所以当前主路更像：

```text
已有登录
    ↓
AutoLogin
    ↓
External Login UI
    ↓
Privilege Check
```

相关接口都是 OSSv1：

```cpp
IdentityInterface->AutoLogin(...)
ExternalUI->ShowLoginUI(...)
IdentityInterface->GetUserPrivilege(...)
```

---

## ***Online Context 是怎么理解的***

`ECommonUserOnlineContext` 里有几个容易混的概念：

```cpp
Game
Default
Service
ServiceOrDefault
Platform
PlatformOrDefault
```

可以这样记：

- ***Default***：当前默认 OnlineSubsystem。EOS Target 下通常就是 EOS。
- ***Platform***：当前原生平台服务。例如 SteamEOS 下可能是 Steam。
- ***Service***：外部在线服务。Lyra 这里预留给更复杂的服务拆分。
- ***Game***：玩法代码使用的高层语义。它会先处理平台上下文，再处理最终服务上下文。

`ProcessLoginRequest()` 里有一段关键逻辑：

```cpp
if (Request->DesiredContext == ECommonUserOnlineContext::Game)
{
    Request->CurrentContext = ResolveOnlineContext(ECommonUserOnlineContext::PlatformOrDefault);
}
else
{
    Request->CurrentContext = ResolveOnlineContext(Request->DesiredContext);
}
```

这就是 SteamEOS 场景下“先平台、后 EOS”的基础。

---

## ***SteamEOS 和纯 EOS 的差异***

### *纯 EOS*

`Config/Custom/EOS/DefaultEngine.ini`：

```ini
[OnlineSubsystem]
DefaultPlatformService=EOS

[OnlineServices]
DefaultServices=Epic
```

纯 EOS 下，Default OnlineSubsystem 就是 EOS。

### *SteamEOS*

`Config/Custom/SteamEOS/DefaultEngine.ini`：

```ini
[OnlineSubsystem]
DefaultPlatformService=EOS
NativePlatformService=Steam

[/Script/OnlineSubsystemEOS.EOSSettings]
SteamTokenType=WebApi:epiconlineservices

[OnlineSubsystem]
bLoadNativeOSSBeforeDefault=true
```

这个配置表达的是：

- ***DefaultPlatformService=EOS***：游戏主要在线服务还是 EOS。
- ***NativePlatformService=Steam***：原生平台是 Steam。
- ***SteamTokenType***：EOS 通过 Steam WebApi token 做平台集成。
- ***bLoadNativeOSSBeforeDefault***：先加载 Steam，再加载 EOS，保证 EOS 初始化时 Steam DLL 和平台环境已经准备好。

所以 SteamEOS 不是“只用 Steam”，而是：

```text
Steam 负责原生平台身份 / 发布平台集成
EOS 负责跨平台在线服务 / Session / Presence 等
```

---

## ***CommonSession 开房流程***

### *1. UserFacingExperience 生成 HostRequest*

Lyra 的每个可见玩法体验由 `ULyraUserFacingExperienceDefinition` 描述。它会创建：

```cpp
UCommonSession_HostSessionRequest* Result = Subsystem->CreateOnlineHostSessionRequest();
```

然后填入：

```cpp
Result->MapID = MapID;
Result->ModeNameForAdvertisement = UserFacingExperienceName;
Result->ExtraArgs.Add("Experience", ExperienceName);
Result->MaxPlayerCount = MaxPlayerCount;
```

也就是说，Lyra 开房不是直接写死地图和模式，而是从 UserFacingExperience 数据资产生成 HostRequest。

### *2. HostSession 进入在线 Session 创建*

`UCommonSessionSubsystem::HostSession()` 会判断：

```text
Offline
    -> 直接 ServerTravel

Online / LAN
    -> CreateOnlineSessionInternal
```

当前 OSSv1 主路最后会进入：

```cpp
CreateOnlineSessionInternalOSSv1(...)
```

### *3. OSSv1 创建 EOS Session*

创建 Session 时，Lyra 会构造 `FCommonSession_OnlineSessionSettings`：

```cpp
FCommonSession_OnlineSessionSettings HostSettings(
    Request->OnlineMode == ECommonSessionOnlineMode::LAN,
    Request->bUsePresence,
    MaxPlayers
);
```

然后写入几个可搜索字段：

```cpp
HostSettings.bUseLobbiesIfAvailable = Request->bUseLobbies;
HostSettings.bUseLobbiesVoiceChatIfAvailable = Request->bUseLobbiesVoiceChat;
HostSettings.Set(SETTING_GAMEMODE, Request->ModeNameForAdvertisement, ViaOnlineService);
HostSettings.Set(SETTING_MAPNAME, Request->GetMapName(), ViaOnlineService);
HostSettings.Set(SETTING_MATCHING_TIMEOUT, 120.0f, ViaOnlineService);
HostSettings.Set(SETTING_SESSION_TEMPLATE_NAME, "GameSession", ViaOnlineService);
HostSettings.Set(SETTING_ONLINESUBSYSTEM_VERSION, true, ViaOnlineService);
```

最后调用：

```cpp
Sessions->CreateSession(*UserId, NAME_GameSession, HostSettings);
```

如果当前默认 OnlineSubsystem 是 EOS，这个 `CreateSession` 实际就是交给 `OnlineSubsystemEOS`。

---

## ***搜房和加入流程***

### *1. 搜房 FindSessions*

`FindSessions()` 会创建 `FCommonOnlineSearchSettingsOSSv1`：

```cpp
QuerySettings.Set(SETTING_ONLINESUBSYSTEM_VERSION, true, Equals);

if (InSearchRequest->bUseLobbies)
{
    QuerySettings.Set(SEARCH_LOBBIES, true, Equals);
}
```

真正查找时又加了：

```cpp
SearchSettings->QuerySettings.Set(SETTING_SESSION_TEMPLATE_NAME, "GameSession", Equals);
Sessions->FindSessions(*LocalPlayer->GetPreferredUniqueNetId().GetUniqueNetId(), SearchSettings);
```

所以 Lyra 默认只找同一套模板创建出来的游戏 Session，避免搜到不兼容的房间。

### *2. 加入 JoinSession*

加入时主路是：

```cpp
Request->Result.Session.SessionSettings.bUsesPresence = true;
Request->Result.Session.SessionSettings.bUseLobbiesIfAvailable = bUseLobbiesDefault;
Sessions->JoinSession(*LocalPlayer->GetPreferredUniqueNetId().GetUniqueNetId(), NAME_GameSession, Request->Result);
```

成功后：

```cpp
FinishJoinSession(Result)
```

如果没有启用 Reservation Beacon，就会直接：

```cpp
InternalTravelToSession(NAME_GameSession);
```

### *3. Travel 到服务器*

`InternalTravelToSession()` 里会通过 OnlineSubsystem 解析连接字符串：

```cpp
Sessions->GetResolvedConnectString(SessionName, URL);
PlayerController->ClientTravel(URL, TRAVEL_Absolute);
```

这一步非常关键：客户端不需要自己拼 IP 或 EOS P2P 地址，而是让当前 OnlineSubsystem 返回正确的连接字符串。

---

## ***Lobby Schema 是干什么的***

EOS 配置里还有：

```ini
[OnlineServices.Lobbies]
+SchemaDescriptors=(Id="GameLobby", ParentId="LobbyBase")
+SchemaAttributeDescriptors=(Id="GAMEMODE", Type="String", Flags=("Public"), MaxSize=64)
+SchemaAttributeDescriptors=(Id="MAPNAME", Type="String", Flags=("Public"), MaxSize=64)
+SchemaAttributeDescriptors=(Id="MATCHTIMEOUT", Type="Double", Flags=("Public"))
+SchemaAttributeDescriptors=(Id="SESSIONTEMPLATENAME", Type="String", Flags=("Public"), MaxSize=64)
```

这些主要服务于 OSSv2 / OnlineServices Lobbies 路径。虽然当前 CommonSession 主路是 OSSv1，但 Lyra 已经把 OSSv2 Lobby 需要的 Schema 也写在配置里。

可以理解成：

- ***OSSv1 Session Settings***：用 `SETTING_GAMEMODE`、`SETTING_MAPNAME` 等键值广告房间。
- ***OSSv2 Lobby Schema***：提前声明 Lobby 有哪些字段、字段类型、是否可搜索。

---

## ***Presence 是怎么更新的***

`UCommonUserBasicPresence` 会监听：

```cpp
CommonSession->OnSessionInformationChangedEvent
```

当 Session 状态变化时，它会把状态同步到在线服务：

```cpp
Status_InGame
Status_MainMenu
Status_Matchmaking
```

并写入：

```cpp
game_mode
map_name
```

EOS 配置里对应：

```ini
[/Script/CommonUser.CommonUserBasicPresence]
bEnableSessionsBasedPresence=true
PresenceStatusInGame=Status_InGame
PresenceStatusMainMenu=Status_MainMenu
PresenceStatusMatchmaking=Status_Matchmaking
PresenceKeyGameMode=game_mode
PresenceKeyMapName=map_name
```

这说明 Lyra 的 Presence 不是到处手动更新，而是跟 Session 状态绑定。

---

## ***QuickPlay 是怎么工作的***

`QuickPlaySession()` 的逻辑很简单：

```text
进入 Matchmaking 状态
    ↓
FindSessions
    ↓
如果找到结果，Join 第一个
    ↓
如果没找到，HostSession 自己开房
```

代码里甚至有 TODO，说明它现在没有复杂排序：

```cpp
// We should probably look at ping?
```

所以 Lyra 的 QuickPlay 更像教学用的基础实现，不是成熟商业匹配系统。

如果正式做项目，通常还要扩展：

- ***匹配规则***：地图、模式、地区、段位、队伍人数。
- ***结果排序***：Ping、人数、服务器负载。
- ***失败重试***：搜不到房、Join 失败、Session 满员后的回退。
- ***Dedicated Server 分配***：不要只靠玩家 Host。

---

## ***学习时应该先看哪些文件***

### *配置入口*

```text
Config/DefaultEngine.ini
Config/Custom/EOS/DefaultEngine.ini
Config/Custom/SteamEOS/DefaultEngine.ini
```

重点看：

```ini
DefaultPlatformService
DefaultServices
OnlineSubsystemEOS.EOSSettings
NetDriverDefinitions
OnlineServices.Lobbies
CommonUserBasicPresence
```

### *Target 入口*

```text
Source/LyraGameEOS.Target.cs
Source/LyraServerEOS.Target.cs
Source/LyraGameSteamEOS.Target.cs
Source/LyraServerSteamEOS.Target.cs
```

重点看：

```csharp
CustomConfig = "EOS";
EnablePlugins.AddRange(...)
OptionalPlugins.Add("EOSReservedHooks");
```

### *登录入口*

```text
Plugins/CommonUser/Source/CommonUser/Private/CommonUserSubsystem.cpp
Plugins/CommonUser/Source/CommonUser/Public/CommonUserTypes.h
Plugins/CommonUser/Source/CommonUser/Private/AsyncAction_CommonUserInitialize.cpp
```

重点看：

```cpp
CreateOnlineContexts()
TryToInitializeForLocalPlay()
TryToLoginForOnlinePlay()
LoginLocalUser()
ProcessLoginRequest()
HandleUserLoginCompleted()
HandleCheckPrivilegesComplete()
```

### *Session 入口*

```text
Plugins/CommonUser/Source/CommonUser/Private/CommonSessionSubsystem.cpp
Plugins/CommonUser/Source/CommonUser/Public/CommonSessionSubsystem.h
```

重点看：

```cpp
CreateOnlineHostSessionRequest()
HostSession()
CreateOnlineSessionInternalOSSv1()
FindSessionsInternalOSSv1()
JoinSessionInternalOSSv1()
InternalTravelToSession()
CleanUpSessions()
```

### *Lyra 接入点*

```text
Source/LyraGame/UI/Frontend/LyraFrontendStateComponent.cpp
Source/LyraGame/GameModes/LyraGameMode.cpp
Source/LyraGame/GameModes/LyraUserFacingExperienceDefinition.cpp
Source/LyraGame/System/LyraGameInstance.cpp
```

这些文件说明 Lyra 是如何把前端、登录、体验数据、开服和 travel 串起来的。

---

## ***最容易踩的坑***

- ***只改 DefaultEngine.ini 不够***：EOS 主要在 `Config/Custom/EOS` 里，必须用对应 Target 或 CustomConfig 才会加载。
- ***插件启用不等于登录成功***：还要填正确的 `ArtifactName`、`ClientId`、`ClientSecret`、`ProductId`、`SandboxId`、`DeploymentId`。
- ***当前主路是 OSSv1***：不要看到 `OnlineServicesEOS` 就以为 CommonUser 已经切到 OSSv2。
- ***Session 和 Lobby 概念会混***：OSSv1 下 Lyra 仍然用 `IOnlineSession`，但可以通过 `bUseLobbiesIfAvailable` 让底层使用可用的 Lobby 能力。
- ***SteamEOS 不是 Steam 联机***：它是 Steam 原生平台 + EOS 跨平台服务。
- ***QuickPlay 很基础***：Lyra 的 QuickPlay 更像示例，不是完整 matchmaking。
- ***Dedicated Server 登录失败仍可能开服***：代码注释说明服务器不强制依赖用户登录，但某些 OnlineSubsystem 可能需要。

---

## ***简述工作流程***

```text
构建 LyraGameEOS / LyraServerEOS
        ↓
加载 Config/Custom/EOS/DefaultEngine.ini
        ↓
Default OnlineSubsystem 变成 EOS
        ↓
CommonUser 初始化 OnlineSubsystem + Identity
        ↓
前端或服务器触发用户登录
        ↓
CommonSession 创建 / 搜索 / 加入 Session
        ↓
OnlineSubsystemEOS 把请求转给 EOS
        ↓
成功后 ServerTravel / ClientTravel
```

---

## ***一句话总结***

*Lyra 的 EOS 不是“玩法代码直接调用 EOS SDK”，而是通过 Target 选择配置、通过 `OnlineSubsystemEOS` 提供底层能力、通过 `CommonUser` 和 `CommonSession` 把登录与联机流程包装成游戏可用的通用接口。*

