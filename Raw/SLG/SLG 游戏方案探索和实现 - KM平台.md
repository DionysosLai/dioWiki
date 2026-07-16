---
title: "SLG 游戏方案探索和实现 - KM平台"
source: "https://km.woa.com/knowledge/10437/node/9"
author:
published:
created: 2026-07-16
description:
tags:
  - "clippings"
---

**一、背景介绍**

《英雄杀二代》采用 “大世界 SLG + 大都会兴趣点” 的核心玩法设计，融合 SLG 经典策略元素与创新兴趣点交互，打造沉浸式策略体验。

**（一）主流 SLG 核心玩法**

当前市面上主流 SLG 游戏的核心内容集中于三大模块，也是《英雄杀二代》的核心设计参考：

1. **自由行军** ：玩家可操控军队自由移动至地图任意位置。  
	![20](https://km.woa.com/asset/0001000225110012338bee2d024ee701?height=399&width=865&imageMogr2/thumbnail/1540x%3E/ignore-error/1)
2. **无极缩放** ：支持玩家通过鼠标滚轮或手势任意调整视野高度，不同高度对应四层信息展示，满足 “微观操作” 与 “宏观战略” 双重需求：
- 战场层：展示最精细信息，包括军队模型（含骨骼动画）、城池细节模型、实时战斗特效等；  
	![](https://km.woa.com/asset/000100022511004bee8612300140d401?height=399&width=865&imageMogr2/thumbnail/1540x%3E/ignore-error/1)
- 战略层：聚焦联盟战术布局，显示联盟标记、军师指令图标、军队集结点等；  
	![](https://km.woa.com/asset/0001000225110091c9b5d0300d49ed01?height=399&width=865&imageMogr2/thumbnail/1540x%3E/ignore-error/1)
- 沙盘层：呈现宏观领土归属，以不同色块区分城池 / 资源矿所属势力，标注资源等级；  
	![](https://km.woa.com/asset/000100022511005ba113a264364f9301?height=399&width=865&imageMogr2/thumbnail/1540x%3E/ignore-error/1)
- 国家势力层：展示全图国家势力范围，用轮廓线划分势力边界，标注核心战略要地（如关隘、皇城）。  
	![](https://km.woa.com/asset/0001000225110058ed85fd1aa04ee201?height=399&width=865&imageMogr2/thumbnail/1540x%3E/ignore-error/1)
3. **地图进程机制** ：采用 “圈层式” 地图设计，引导玩家阶段性成长：
- 初始阶段：玩家出生于地图边缘 “出生州”，通过攻占低级城池、采集基础资源发育；
- 中期阶段：跟随地图进程 “缩圈”，进入内圈 “资源州”，争夺高级资源矿、稀有道具产出地；
- 终局阶段：全员进入地图中心区域，围绕 “皇城” 展开争夺，占领皇城者获得赛季核心奖励。

**（二）项目核心挑战**

基于上述玩法设计，项目开发面临两大核心场景挑战，直接衍生三大技术难点：

| **核心场景** | **场景特点** | **衍生技术难点** |
| --- | --- | --- |
| 超大地图与海量对象 | 地图尺寸达 8000\*8000 米，分布数十万游戏对象（军队、城池、资源矿等），需保障加载与渲染效率 | 数据同步、无极缩放 |
| 千人同屏皇城战 | 全服玩家集中于皇城区域，涉及 1000 + 军队操作、实时战斗计算，同时需支持海量玩家观战 | 数据同步、流量优化 |

**二、项目核心技术难点与解决方案**

**（一）数据同步：保障高效、可靠的跨端数据交互**

数据同步是 SLG 玩法落地的核心，需满足 “开发高效、同步高效、数据可靠、高扩展、易维护” 五大目标，具体方案如下：

**1\. 基础支撑：Gameplay 框架设计**

采用 **ECS 架构** 与 “逻辑 - 表现分离” 设计，为数据同步提供灵活、可扩展的底层支撑。

**（1）** **ECS 架构：组件化组合业务逻辑** ![](https://km.woa.com/asset/0001000225110059d62cc9d222417601?height=356&width=865&imageMogr2/thumbnail/1540x%3E/ignore-error/1)

- **核心模块职责** ：
- EntityManager：作为 Entity 管理中枢，维护entityMap（键为 EntityID，值为EntityBase），提供CreateEntity（创建实体）、DestroyEntity（销毁实体）等核心接口；
- EntityBase：逻辑层实体基类，本质是ComponentBase的容器，存储实体的id（唯一标识）、type（类型枚举）及compList（组件映射表），支持组件的增删查（AddComponent/RemoveComponent/GetComponent）；
- ComponentBase：组件基类，承载具体业务数据（如PositionComponent存储位置、HPComponent存储血量），提供OnAdded（组件添加事件）、OnRemoved（组件移除事件）、OnValueChange（属性变更事件）三大回调；
- EntityView：表现层实体基类，绑定 Unity 的GameObject，通过EntityBinder关联逻辑层EntityBase，实现 “数据变更→视觉反馈” 的映射；
- IView：视图接口，定义GetEntity方法，规范EntityView与EntityBase的关联逻辑。
- **优势** ：相比 OOP（面向对象），ECS 通过 “组件组合” 替代 “类继承”，可灵活组合业务需求，降低代码耦合度，提升扩展性。

**（2）逻辑与表现分离：解耦数据与视觉**

- **分层职责** ：
- 逻辑层：EntityBase作为数据载体，通过EntityChanel接收服务器同步数据，更新Component属性，并触发组件回调；
- 表现层：EntityView（如SLGArmyView、SLGMainCityView）绑定游戏对象，通过EntityBinder向EntityBase的Component注册属性变更回调，实现 “点对点” 数据响应。
- **回调粒度** ：支持 “组件级” 与 “属性级” 两层回调，示例代码如下：

o 向Army实体的多个Component注册属性变更回调 ![](https://km.woa.com/asset/000100022511003f7bd44b5df042d201?height=142&width=865) o 向LeagueInfoComponent注册属性级别回调 ![](https://km.woa.com/asset/000100022511007ff0ac94dc37451601?height=195&width=865)

**（3）数据更新时序问题解决**

- **问题 1：同数据帧组件更新乱序**

场景：如军队行军时，ArmyStateComp（状态）与ArmyTripComp（行军路径）在同一数据帧更新，业务读取时可能出现 “状态已变、路径未更” 的旧值问题。

解决方案：数据更新原子性约束 + 客户端事件队列

- 后端：同一Entity的多Component修改需满足 “原子性”，要么全部成功下发，要么全部失败回滚；
- 客户端：在OnUpdate函数中，先处理完当前帧所有同步包，再将组件增删改事件压入，最后按顺序抛送事件，确保数据一致性。  
	![](https://km.woa.com/asset/000100022511006b38aad124fb4a6801?height=293&width=865)
- **问题 2：多 Entity 依赖异步加载**

场景：Entity1的RelationshipComponent存储Entity2的 ID，但Entity2可能在跨服创建中，导致Entity1加载时Entity2尚未存在。

解决方案：客户端异步加载 + 业务无感知

a) 客户端检测到Entity软引用 ID 时，优先从EntityManager缓存中加载；若缓存无，则异步从后台请求加载；

b) 业务层通过await关键字等待加载完成，无需关注加载细节，降低开发门槛。

**2\. 核心实现：属性同步**

**（1）属性同步框架：服务器 - 客户端交互流程**

- **整体流程** ：  
	![](https://km.woa.com/asset/00010002251100919a87af0e734b7901?height=336&width=309)
1. 服务器：Entity属性修改后，标记 “脏数据”（属性标脏），定期收集所有脏数据（属性收集），按客户端连接下发PropSync包（属性下发）；
2. 客户端：通过DataBufferQueue缓存PropSync包，由NetSyncSystem解析包内CompData（组件数据），更新对应Entity的Component属性。

**（2）容器同步：List 与 Map 的通用设计**

针对 SLG 中常见的列表、字典数据，设计通用同步方案：

| **容器类型** | **同步方案** | **注意事项** |
| --- | --- | --- |
| List | 采用CustomList的 ProtoBuf 结构，内部存储二进制数据；   由CodeGenerator读取ECSDescription，解析二进制数据对应结构 | 规避容器嵌套；若无法规避，嵌套容器暂按全量同步 |
| Map | 同步 “增删改” 操作指令，而非全量数据，减少流量开销 | 容器内嵌套的结构数据，当前按全量同步，后续可优化为增量同步 |

**3\. 可靠性保障：Version 管理与增量同步**

**（1）Version 管理：快照 + Patch 包 + 兜底检查**

- **快照（Snapshot）** ：Entity定期生成全量数据快照，记录当前所有Component信息，作为数据恢复的基准；
- **Patch 包** ：属性变更时，仅生成增量Patch包，附带版本号（如ver\_1→ver\_2的patch1），由VersionComponent存储currentVersion（当前版本）与latestSnapshotVersion（最新快照版本）；  
	![](https://km.woa.com/asset/00010002251100c2d7e01a95e04bcf01?height=124&width=614)  
	如果Entity最后一个版本的patch包丢失，或者漏创建某些Entity，上面的Version机制是无法发现这些问题的，又该怎么办？
- **兜底检查** ：低频（如每 30 秒）发送视野内Entity的最新版本号，若发现客户端Entity版本落后，触发全量快照同步，解决 “最新Patch 包丢失”“Entity 漏创建” 问题。  
	![](https://km.woa.com/asset/00010002251100af692ff5b345470a01?height=122&width=865)

**（2）增量同步：仅同步变更数据**

- **核心逻辑** ：Entity首次初始化时，下发全量快照；后续仅同步变更的属性，大幅减少流量。
- **示例：SLGArmy 属性同步** ：

![](https://km.woa.com/asset/00010002251100e1c3a1042d92457501?height=596&width=865) a) 初始状态：SLGArmy的HPComponent（血量）为 10000，OddrPositionComponent（位置）为 (x:10, y:20)，首次同步下发全量快照；

b) 变更后：血量降至 3000，位置更新为 (x:30, y:20)，仅生成包含 “血量变更”“位置 x 变更” 的Patch包；

c) 元数据索引：Patch包携带(entity\_id, type\_enum, tag)三元组（元数据），entity\_id标识Entity，type\_enum（如 31 代表HPComponent）标识组件，tag（如 1 代表CurHP）标识属性；

d) 自动生成：元数据由ECSCodeGenerator自动生成，无需手动编写，Component的Merge接口也由工具生成，确保同步逻辑正确性。

**4\. 优化策略：按需同步（数据需求 + 数据 LOD）**

**（1）按数据需求同步：属性标签区分对象**

根据数据接收对象的不同，为Component属性打不同sync\_flag标签，实现精准同步：

- 示例：ArmyTripComponent的Path（路径）属性，sync\_flag=ALL\_CLIENT（同步给所有客户端）；军队私有属性（如军队背包），sync\_flag=OWNER\_CLIENT（仅同步给主机玩家）。  
	![](https://km.woa.com/asset/0001000225110028f5fc4c439d432f01?height=531&width=865)

**（2）按数据 LOD 同步：视角关联数据粒度**

结合 “无极缩放” 玩法，视角越近，数据粒度越细；视角越远，数据粒度越粗，减少无效同步：

| **数据LOD 等级** | **数据粒度** | **应用场景** |
| --- | --- | --- |
| LOD0（近视角） | 全量数据：如军队实时血量变化、精确位置 | 战场层操作（如单挑、攻城） |
| LOD1（中视角） | 精简数据：如军队大致位置、所属势力标记 | 战略层布局（如联盟集结） |
| LOD2（远视角） | 概览数据：如势力领土色块、核心城池标记 | 沙盘层 / 国家势力层（宏观观察） |

- **切换逻辑** ：当视角从 LOD1 切换至 LOD0 时，客户端自动请求 LOD0 级别的全量数据；从 LOD0 切换至 LOD1 时，仅保留 LOD1 所需精简数据，卸载冗余信息。

**（四）核心工具链：提升开发效率与问题排查能力**

为降低 ECS 架构、数据同步的开发门槛，设计三大核心工具，形成 “设计 - 生成 - 调试” 完整工作流：

![](https://km.woa.com/asset/0001000225110072a2392ebba9437601?height=530&width=865)

**1\. 工具 1：ECSEditor—— 可视化定义 Entity 与 Component**

- **核心功能** ：
- 可视化界面：支持拖拽创建Entity（如SLGArmy、SLGMainCity），添加 / 删除Component（如ArmyTripComponent、HPComponent）；

![](https://km.woa.com/asset/00010002251100a670706b250d4b2401?height=640&width=865)

- 生成ECSDescription：自动生成 XML 格式的实体 / 组件描述文件，包含Entity的组件列表、Component的属性与tag（如ArmyTrip.xml中Path属性的tag=1）。
- **示例：SLGArmy Description（XML 片段）** ：

![](https://km.woa.com/asset/000100022511008af0db90e132470a01?height=452&width=634)

- **示例：ArmyTripComponent Description（XML 片段）:**

![](https://km.woa.com/asset/0001000225110071eaa5a117db4adb01?height=174&width=631)

**2\. 工具 2：CodeGenerator—— 自动生成同步代码**

- **技术选型** ：采用 Scriban 模板引擎 + C#，实现代码自动化生成；
- **核心功能** ：
- 解析ECSDescription，生成Entity、Component的 C# 类（含属性 getter/setter）；
- 处理特殊类型映射：如 Unity基础结构Vector3Int与后端VectorInt3的自动转换；
- 生成属性同步代码：如Component的OnNetDataArrive方法，解析ComponentSyncData并更新属性。
- **示例：ArmyTripComponent CodeGen 代码片段** ：

![](https://km.woa.com/asset/000100022511003d709c219d7c4c1201?height=412&width=631)

**3\. 工具 3：Debug 工具 —— 问题定位与数据可视化**

针对 “数据同步问题难复现”“Entity 数据不直观” 痛点，开发两类调试工具：

| **工具名称** | **核心功能** | **应用场景** |
| --- | --- | --- |
| Replay 工具 | 1\. 记录属性同步包二进制日志；   2\. 支持逻辑层 / 表现层反复回放；   3\. 提供断点、逐帧、跳帧调试 | 定位 “偶发同步bug”“数据不一致” 问题 |
| RuntimeEntityDebugger | 在 Unity Inspector 面板中，实时显示Entity的所有Component当前数据（如HPComponent的CurHP） | 开发中快速查看Entity数据，验证同步是否正确 |

**4\. 工具链工作流：从设计到调试的闭环**

以 “新增SLGArmyEntity” 为例，完整工作流如下：

- 设计阶段：开发用ECSEditor创建SLGArmyEntity，添加ArmyInfoComponent（军队属性）、ArmyStateComponent（军队状态）等；
- 生成阶段：CodeGenerator自动生成SLGArmyEntity、各Component的 C# 类及同步代码；
- 测试阶段：运行游戏，通过RuntimeEntityDebugger查看SLGArmyEntity数据是否正确加载；
- 问题排查：若出现同步问题，用Replay工具回放同步日志，定位问题节点。

**5\. 总结 - 主要贡献** ：

1. 开发了ECS架构的Gameplay框架和自研属性同步框架搭建。
2. 开发配套ECS编辑器和ECS代码生成器辅助完成Entity和Compnent设计，属性同步代码生成做到了开发“零参与”，提升开发效率，降低开发门槛。
3. 开发离线Replay和RuntimeEntityDebugger调试工具，通过录像回放以及运行时Entity数据实时查看，大幅提升Debug效率，尤其是偶现的疑难杂症。

更新于：2025-11-26 17:03