# UE DS 架构：客户端和服务器是不是同一份代码？

> **创建时间**：2026-05-12
> **场景**：学习 UE DS（Dedicated Server）架构时的常见疑问
> **核心问题**：`UFUNCTION(Server, Reliable)` 这种"服务器调用"，服务器代码是不是要单独一个工程？

---

## 一、直接结论

**不需要单独工程。客户端和服务器用同一份代码、同一个工程、同一个 `.uproject`**。

区别只是**启动方式不同**：
- 同一份代码跑在不同启动模式下
- 代码里通过 `HasAuthority()` 判断"我现在是服务器还是客户端"
- 不同分支执行不同逻辑

---

## 二、UE 的"同代码双端"模型

这是 UE 跟传统 C/S 架构（比如端游的 Java/C++ 服务器 + 另写一份客户端）**最大的区别**。

### 2.1 传统 C/S 架构（MMORPG 自研服务器）

```
ClientProject（C++/Unity/UE）
  └── Login.cpp / Battle.cpp / ...

ServerProject（Java/Go/C++）← 完全独立的工程
  └── LoginServer.java / BattleServer.java / ...

客户端和服务器：
  - 不同语言
  - 不同代码
  - 通过自定义协议（Protobuf / JSON）通信
  - 各写各的逻辑
```

### 2.2 UE 的架构（DS + 状态同步）

```
YourGame.uproject  ← 只有一个工程
  └── Source/YourGame/
        ├── MonsterCharacter.cpp   ← 同一个文件
        ├── PlayerCharacter.cpp    ← 同一个文件
        └── ...

编译时产生两个可执行文件（Target）：
  ├── YourGameServer.exe      ← 服务器
  └── YourGameClient.exe      ← 客户端
  （底层其实是同一份代码，只是 Build 参数不同）

运行时：
  YourGameServer.exe -server   ← 启动为服务器（无渲染）
  YourGameClient.exe           ← 启动为客户端（连到服务器）
```

---

## 三、"同一份代码"是怎么做到双端不同行为的

**关键就靠两个机制**。

### 3.1 机制 1：`HasAuthority()` 判断

```cpp
void AMonsterCharacter::TakeDamage(float Amount)
{
    if (HasAuthority())
    {
        // 这段代码只在服务器执行
        CurrentHealth -= Amount;   // 改数值（权威）
        if (CurrentHealth <= 0)
            Die();
    }
    else
    {
        // 这段代码只在客户端执行（一般不会改权威数据）
        PlayHitReactionAnimation();  // 播个本地反馈
    }
}
```

**`HasAuthority()`**：
- 服务器进程执行时返回 `true`
- 客户端进程执行时返回 `false`
- 同一行代码，两边跑出来结果不同

### 3.2 机制 2：RPC 标记（你问的 `UFUNCTION(Server)`）

```cpp
// 这三个 RPC 写在同一个类里！
class AMyPlayer : public ACharacter
{
    // 玩家按键 → 客户端调用 → 实际在服务器执行
    UFUNCTION(Server, Reliable)
    void ServerFire(FVector Direction);

    // 服务器广播 → 所有客户端执行（播特效）
    UFUNCTION(NetMulticast, Unreliable)
    void MulticastPlayFireVFX();

    // 服务器单独通知某个客户端（比如通知被击中的玩家）
    UFUNCTION(Client, Reliable)
    void ClientShowHitMarker();
};

// 实现也写在同一个 cpp 里
void AMyPlayer::ServerFire_Implementation(FVector Direction)
{
    // UBT/UHT 自动生成的外壳会保证这段代码只在服务器真实执行
    // 如果客户端调用，UE 引擎会自动把它打包成 RPC 发给服务器
    DoServerFireLogic(Direction);
}
```

**魔法在哪里**：
- 你写一份 `ServerFire_Implementation`
- UE 的代码生成器（UHT）会自动生成一个"路由层"
  - 客户端调 `ServerFire(...)` 时，不会真的在本地执行 `_Implementation`，而是把参数**打包发给服务器**
  - 服务器收到包后解包，调自己本地的 `_Implementation`
- 你作为开发者**不用关心网络序列化、不用写 socket、不用写协议**，就像调本地函数一样

---

## 四、Build Target：怎么编出两个 exe

UE 项目的 `Source/` 目录下通常有这几个 `*.Target.cs` 文件：

```
Source/
├── YourGame.Target.cs          ← 打包客户端用（也用于开发模式的 Editor）
├── YourGameEditor.Target.cs    ← 编辑器
└── YourGameServer.Target.cs    ← 打包服务器用（DS）
```

### 4.1 客户端 Target

`YourGame.Target.cs`：

```csharp
public class YourGameTarget : TargetRules
{
    public YourGameTarget(TargetInfo Target) : base(Target)
    {
        Type = TargetType.Game;   // ← 客户端类型
        ExtraModuleNames.Add("YourGame");
    }
}
```

### 4.2 服务器 Target

`YourGameServer.Target.cs`：

```csharp
public class YourGameServerTarget : TargetRules
{
    public YourGameServerTarget(TargetInfo Target) : base(Target)
    {
        Type = TargetType.Server;   // ← 服务器类型（关键！）
        ExtraModuleNames.Add("YourGame");
    }
}
```

### 4.3 打包结果

| Target | 输出 | 特点 |
| --- | --- | --- |
| `YourGame` | `YourGameClient.exe` | 带渲染、UI、音频 |
| `YourGameServer` | `YourGameServer.exe` | **剥离渲染/材质/音频，纯逻辑**，体积小、CPU/内存占用低 |

**但是**——源代码是同一份，引用的是同一个 `YourGame` 模块。UE 用**编译宏**决定编什么不编什么：

```cpp
#if WITH_SERVER_CODE
    // 服务器专用代码（Client 打包时会被剔除）
#endif

#if !UE_SERVER
    // 客户端专用代码（比如渲染、UI）
#endif
```

---

## 五、开发阶段怎么测试"双端"

你**不需要**真的打两个 exe 来回切，UE 编辑器里有强大的 **PIE 多人模式**。

### 5.1 启用 PIE 多人模式

```
Editor Preferences → Play → Multiplayer Options:
  - Number of Players: 2（或更多）
  - Net Mode: Play As Listen Server / Play As Client
  - ☑ Launch Separate Server（关键！勾上这个就是真 DS 模式）
```

### 5.2 PIE 自动做了什么

按 PIE（Play In Editor）后 UE 会自动：

1. 启动一个**隐藏的服务器进程**（或者让主窗口当 Listen Server）
2. 启动 N 个客户端窗口
3. 自动连接到服务器

### 5.3 你能看到什么

- 服务器侧的 Log（`Log.Server`）
- 每个客户端的 Log（`Log.Client1` / `Log.Client2`）
- 同一份代码在不同端跑出来的不同结果

调试 `HasAuthority()` 分支就是在这个模式下完成。

---

## 六、几个常见误区（先打预防针）

### 误区 1："服务器要单独一台机器才能跑"

**错**。
- 开发阶段：PIE 里就有服务器进程
- 打包测试：可以在自己电脑上跑 `YourGameServer.exe -log` 本地联调
- 上线时：才部署到云服务器

### 误区 2："我要写服务器代码就要切到另一个工程"

**错**。
- 你永远在**同一个工程**里写代码
- 只是用 `if (HasAuthority())` 和 RPC 标记来区分"哪段代码在哪边跑"

### 误区 3："服务器和客户端通信要自己写 socket / 定义协议"

**错**。UE 的 Replication + RPC 全部自动化：

| 你声明 | UE 自动做的 |
| --- | --- |
| `UPROPERTY(Replicated)` | 自动同步（增量、压缩、可靠/不可靠） |
| `UFUNCTION(Server, Reliable)` | 自动 RPC，参数序列化 |
| 底层 socket / UDP / 序列化 / 加密 | UE 全部代劳 |

### 误区 4：直接调 `_Implementation` 函数

**错**。

```cpp
// 你写：
UFUNCTION(Server, Reliable)
void ServerFire(FVector Direction);

void AMyPlayer::ServerFire_Implementation(FVector Direction) { ... }

// UHT 自动生成的外壳大致相当于：
void AMyPlayer::ServerFire(FVector Direction)
{
    // 检查 Authority，如果不是服务器就打包发给服务器
    // 如果是服务器直接调 _Implementation
}
```

**调用规则**：
- ✅ 永远写 `ServerFire(Direction)`
- ❌ 不要直接写 `ServerFire_Implementation(Direction)`，否则会**绕过网络层**，本地直接执行

---

## 七、对比一张表

| 维度 | 传统 C/S（端游 MMO） | UE DS 架构 |
| --- | --- | --- |
| **工程数量** | 2 个（客户端一个，服务器一个） | **1 个** |
| **编程语言** | 客户端 C++/C#，服务器 Java/Go/C++ | **同一种 C++** |
| **代码复用** | 几乎无（协议层共享） | **90% 代码双端共享** |
| **通信方式** | 自定义协议（Protobuf/JSON over TCP/WebSocket） | **UE 内置 RPC + Replication** |
| **双端差异实现** | 写在不同工程里，天然隔离 | **`HasAuthority()` 分支 + 编译宏** |
| **上手门槛** | 高（要懂两套技术栈） | 低（一套 UE 搞定） |
| **性能上限** | 高（可以极致优化服务器） | 中（UE Replication 有固定开销） |
| **适用范围** | MMORPG、大世界长驻服务器 | **房间制**：FPS / MOBA / 搜打撤 / 大逃杀 |

---

## 八、回到搜打撤项目

我们要做的搜打撤怪物模块**完全走 UE DS 架构**：

```
YourGame.uproject （一个工程）
├── Source/YourGame/
│   ├── Characters/
│   │   ├── MonsterCharacter.cpp    ← 服务器算 AI 逻辑，客户端显示
│   │   └── PlayerCharacter.cpp     ← 服务器算权威状态，客户端预测
│   ├── AI/
│   │   ├── MonsterAIController.cpp  ← 只在服务器真实执行
│   │   └── BTTasks/                 ← 同上
│   ├── Weapons/
│   │   └── WeaponBase.cpp           ← Fire RPC 在服务器执行
│   └── ...

编译时：
  YourGameServer.exe  ← 跑怪物 AI、伤害判定、物品结算
  YourGame.exe        ← 玩家客户端，接收同步数据显示
```

**AI 逻辑（BehaviorTree、Perception、战斗决策）100% 只在服务器跑**，客户端只看结果。

所以前面提到的"AI 只在服务器跑，可以大胆用 UE 原生 BT/GAS，不用担心确定性"，本质就是因为**根本不会在客户端跑 AI 代码**。

---

## 九、速记结论

哥们，记住这 5 条就够了：

1. **UE 客户端 + 服务器 = 同一个工程 + 同一份代码**
2. **通过 `HasAuthority()` 和 RPC 标记区分"谁执行这段代码"**
3. **打包时用不同 Target.cs 产出不同 exe**，但源码共享
4. **开发阶段用 PIE 多人模式就能测试双端**，不需要真的部署服务器
5. **搜打撤怪物模块的 AI 代码 100% 只在服务器执行**，客户端只显示

---

## 十、扩展阅读

后面遇到下面这些场景时可以回来翻这份文档：

| 场景 | 对应章节 |
| --- | --- |
| 写 `UFUNCTION(Server)` 时不知道为啥要分 `_Implementation` | 第三章机制 2 + 第六章误区 4 |
| PIE 多人测试看不到服务器 Log | 第五章 |
| 想知道"打包后的服务器 exe 跟客户端 exe 区别" | 第四章 |
| 同事问"我们项目要不要单独再开个服务器工程" | 第一章 + 第七章对比表 |
| 写代码不确定某段是不是只该服务器跑 | 第三章机制 1 |

---

## 附：常用代码片段速查

### 判断当前在哪一端

```cpp
// 服务器侧（Authority）
if (HasAuthority())
{
    // 这里改的数据会自动同步给所有客户端
}

// 仅本地玩家（自己控制的角色）
if (IsLocallyControlled())
{
    // 显示 UI / 播本地音效
}

// 仅模拟侧（其他玩家在你这台机器上的副本）
if (GetLocalRole() == ROLE_SimulatedProxy)
{
    // 这是别人的角色在你这边的副本
}
```

### 三种 RPC 模板

```cpp
// 客户端 → 服务器
UFUNCTION(Server, Reliable, WithValidation)
void ServerXXX(参数);

void AYourClass::ServerXXX_Implementation(参数) { /* 在服务器执行 */ }
bool AYourClass::ServerXXX_Validate(参数) { return true; /* 反外挂校验 */ }


// 服务器 → 所有客户端
UFUNCTION(NetMulticast, Unreliable)
void MulticastXXX(参数);

void AYourClass::MulticastXXX_Implementation(参数) { /* 在所有客户端执行 */ }


// 服务器 → 拥有此 Pawn 的特定客户端
UFUNCTION(Client, Reliable)
void ClientXXX(参数);

void AYourClass::ClientXXX_Implementation(参数) { /* 在该客户端执行 */ }
```

### Replicate 属性模板

```cpp
// 头文件
UPROPERTY(ReplicatedUsing = OnRep_Health)
float CurrentHealth;

UFUNCTION()
void OnRep_Health(float OldHealth);

// cpp 文件
void AYourClass::GetLifetimeReplicatedProps(TArray<FLifetimeProperty>& OutLifetimeProps) const
{
    Super::GetLifetimeReplicatedProps(OutLifetimeProps);
    DOREPLIFETIME(AYourClass, CurrentHealth);
}

void AYourClass::OnRep_Health(float OldHealth)
{
    // 客户端收到新值时触发（比如更新血条）
    UpdateHealthBar();
    if (CurrentHealth < OldHealth)
        PlayHurtAnimation();
}
```

---

**End of Document**
