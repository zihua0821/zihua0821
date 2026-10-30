## 关于我

UE C++ 开发者，游戏系统与架构方向。

主要有以下项目：

- **[ProjectB](https://github.com/zihua0821/ProjectB)** —— Mass Entity 驱动的球形星球农场模拟
- **[GASAttributeDebugger](https://github.com/zihua0821/GASAttributeDebugger)** —— 编辑器内实时调试 GAS 属性的插件

## 技术栈

C++ / 蓝图 · Unreal Engine 5（源码构建）· Mass Entity · GAS · ISM 实例化渲染 · Unreal Insights · Rider / Visual Studio

---

## [ProjectB](https://github.com/zihua0821/ProjectB) —— 球形星球农场模拟
> UE 5.5.4 · C++ · 球面网格农场 × 无 Actor 卫星 AI

星球表面按经纬网格划分为 **5.4 万单元格**，一队 Mass Entity 驱动的卫星沿轨道巡航，自主认领地块、规划轨道、执行农事作业；玩家可随时抢占 AI 正在作业的格子。

**系统构成：**

- **农场玩法**：七种编辑模式（耕地 / 灌溉 / 施肥 / 杂草 / 虫害 / 种植 / 收获）、地块状态位掩码、作物五阶段生长与品质评分、昼夜循环与存档
- **卫星 AI**：格子和卫星都是无 Actor 的 Mass 实体；Mass StateTree 六状态机编排作业流程，轨道全局规划负责槽位分配与漂移均匀化
- **作业权仲裁**：5.4 万格子注册智能对象席位，玩家的优先级高于卫星，可抢占它正在作业的格子
- **渲染底座**：自建 `ISMManager` 负责球面空间分桶（每桶 ≤ 2048 格）与增量更新；选中高亮用顶点偏移在 GPU 端弯曲成贴合球面的面片
- **轨道编辑**：运行时 Slate 面板，暂停仿真后编辑轨道参数，落定按轨重建

**工程与验证**：`docs/` 下 60+ 份文档覆盖需求到审阅全过程；性能用挡位表 + A/B 对照做量化（7 万实例压力场景下，分桶与剔除生效时 60+ FPS，剔除失效对照 10–20 FPS），后续补了一轮 17 档分级测试，把 GT / RT 拆成可复算的耗时模型。

> 项目 README 有更详细的架构说明与文档导航。

---

## [GASAttributeDebugger](https://github.com/zihua0821/GASAttributeDebugger) —— GAS 属性调试面板
> UE 5.5+ · 编辑器插件 · C++ + Editor Utility Widget

在编辑器里实时查看和修改任意 Actor 的 GAS 属性，不用重启 PIE。

**功能：**

- 世界 → Actor → AttributeSet → 属性逐级选择，改完立即生效，行上的数值实时回读
- 多开 PIE 时列出所有世界并标明 NetMode，不会改错端
- 运行时临时造 Instant GE 施加修正，走真实 GAS 管线，复制 / Cue / 属性变化委托都会正常触发
- 标出属性是否 Replicated（运行时无 API 可查，走反射查 `CPF_Net`）

**分工**：C++ 只做蓝图做不了的三件事——枚举属性、造 GE、判复制；交互编排全在 Editor Utility Widget 里，改下拉选项不用重编译。
