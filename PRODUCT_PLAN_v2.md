# ai-lib 产品规划方案 v2.0

> **版本**：v2.0  
> **日期**：2026-04-25  
> **作者**：Spider 🕷️  
> **性质**：正式内部规划文档，取代《ai-lib开源分界产品规划和商业模式》原对话体文档  
> **前置依赖**：Prism/Vela 命名已决（2026-04-20），D1-D5 决策已确认  
> **状态**：等待先生审阅后进入 constitution rules + plans tasks

---

## 目录

1. [执行摘要](#1-执行摘要)
2. [产品矩阵总览](#2-产品矩阵总览)
3. [逐象限分析](#3-逐象限分析)
   - 3.1 象限①：Prism Enterprise
   - 3.2 象限②：Prism
   - 3.3 象限③：Vela Pro
   - 3.4 象限④：Vela
4. [三阶段路线图](#4-三阶段路线图)
   - 4.1 Phase 1（3 周）
   - 4.2 Phase 2（6-8 周）
   - 4.3 Phase 3（3-6 月）
5. [热点产品/工具分析](#5-热点产品与工具分析)
   - 5.1 Phase 1 热点
   - 5.2 Phase 2 热点
   - 5.3 Phase 3 热点
6. [三带模型对齐](#6-三带模型对齐)
7. [商业模式汇总](#7-商业模式汇总)
8. [竞品对标定位](#8-竞品对标定位)
9. [与原始文档的修正对照](#9-与原始文档的修正对照)
10. [附录：已确认决策清单](#10-附录已确认决策清单)

---

## 1. 执行摘要

ai-lib 生态的产品战略围绕 **Prism（平台侧·P 层）** 和 **Vela（用户端·A 层）** 双品牌展开，以 **双维度矩阵**（架构层 × 市场层）组织四个产品象限，覆盖企业级和消费级场景。

**核心差异化**：

| 差异点 | 具体内容 | 竞品是否具备 |
|--------|---------|------------|
| 协议运行时（E 层） | `ai-lib-core` 作为标准化协议执行引擎，所有 Provider 差异在 E 层归一化 | ❌ — Poe/OpenRouter 仅做 API 转发 |
| 托管式 Key 池 | 平台替用户管理多 Key 轮换、冷却、熔断、自动恢复 | ❌ — 竞品不提供 |
| 架构级隐私 | P 层设计上不接触对话内容（只消费 ExecutionMetadata） | ❌ — 靠政策/配置，非架构 |
| 中文市场原生 | DeepSeek/Qwen 主力支持、本土支付、数据本地化 | ❌ — Poe/OpenRouter 面向英语市场 |
| 学术理论支撑 | S=(A,P,E) 三层架构形式化定义 | ❌ — 行业内唯一 |

**产品矩阵**：

| | To B（企业/开发者） | To C（个人用户） |
|---|---|---|
| **Prism（P 层·平台侧）** | ① Prism Enterprise | ② Prism |
| **Vela（A 层·用户侧）** | ③ Vela Pro | ④ Vela |

**执行节奏**：Phase 1（3 周）→ 闭环验证 → Phase 2（6-8 周）→ 初步商业化 → Phase 3（3-6 月）→ 规模化。

---

## 2. 产品矩阵总览

### 2.1 矩阵定义

四个象限不是四个独立产品，而是**同一技术栈在不同市场的不同交付形态**。Prism 共享 P 层（路由/Key 池/计费），Vela 共享 A 层（UI/本地历史/WASM 客户端决策）。Prism 和 Vela 的能力几乎不重叠——这是 E/P 分层架构的自然结果。

### 2.2 跨象限能力归属矩阵

| 能力 | ① Enterprise | ② Prism | ③ Vela Pro | ④ Vela | 归属层 |
|------|:---:|:---:|:---:|:---:|:------:|
| E 层协议执行 | ✅ | ✅ | ✅ | ✅ | A 带（开源） |
| Key 池轮换+熔断 | ✅ | ✅ | — | — | Prism 闭源独占 |
| 基础路由（round-robin+fallback） | ✅ | ✅ | — | — | Prism 闭源独占 |
| 智能路由（成本/延迟/可用性） | ✅ | ✅（Phase 2） | — | — | Prism 闭源独占 |
| WASM 客户端路由决策 | — | — | ✅ | ✅（Phase 2） | Vela 客户端 |
| 用量记录+计费 | ✅ | ✅ | — | — | Prism 闭源独占 |
| 审计报表 | ✅ | — | — | — | Enterprise 独占 |
| SSO/SAML | ✅ | — | — | — | Enterprise 独占 |
| 策略推送 | ✅（发） | — | ✅（收） | — | 跨层协作 |
| BYOK | ✅ | ✅（Phase 2） | — | — | Prism 侧 |
| 本地对话历史 | — | — | ✅ | ✅ | Vela 侧 |
| E2E 加密同步 | — | ✅（密文存储） | ✅ | ✅（付费） | 跨层协作 |
| 合规模板 | ✅ | — | ✅ | — | To B 独占（C 带） |
| 聊天 UI | — | — | — | ✅ | Vela 侧 |

### 2.3 关键洞察

1. **Prism 和 Vela 能力互补不重叠**——E/P 分离架构的自然结果，也是产品矩阵的内在逻辑
2. **唯一跨层协作点**：E2E 加密同步——Prism 存密文，Vela 管密钥，既是 A/P 合同边界，也是 To B/C 收费分界
3. **合规模板是 To B 独占价值**——验证了矩阵设计的合理性

---

## 3. 逐象限分析

### 3.1 象限① — Prism Enterprise（Prism × To B）

#### 目标客户

- 中大型企业 IT/SRE 团队、合规部门
- 需要多 Provider 统一接入的 SaaS 公司
- 对数据隐私和安全合规有刚性需求的组织（金融/医疗/法律）
- 在中国运营并有数据本地化需求的外资企业

#### 核心价值

**不用自己建 AI 网关。** 多 Provider 整合、Key 管理、路由、合规、审计全托管。

具体来说：
- **省运维**：Key 池托管免去轮换/冷却脚本的运营成本
- **保合规**：架构级隐私（对话内容不经平台存储），审计日志自动生成
- **防中断**：Provider 级 + Key 级双重降级，单 Provider 故障不影响服务
- **降成本**：智能路由自动选择最优 Provider（成本/延迟/可用性），无需手动比对

#### 功能清单

**Phase 1（基础设施期）**：
- [ ] 多 Provider 统一 API（OpenAI 兼容）
- [ ] 批量 Key 池托管（多用户共享池或独立池）
- [ ] Provider 级降级路由
- [ ] 基础用量记录

**Phase 2（企业版 MVP）**：
- [ ] 多租户隔离（`--features enterprise`）
- [ ] SSO/SAML 集成
- [ ] 审计报表（用量/成本/Provider 健康度）
- [ ] 管理员策略推送（定义路由规则，推至全公司客户端）
- [ ] 私有部署（Docker 镜像 via on-premise）
- [ ] BYOK（自带 Provider 合同和费率）
- [ ] Vela Pro 捆绑套餐
- [ ] Admin API 完善（用户/Key/用量/策略 CRUD）

**Phase 3（企业成熟期）**：
- [ ] SLA 99.9%（含赔偿条款）
- [ ] 行业合规模板（医疗 HIPAA/金融 PCI/法律 GDPR）
- [ ] 专属客户支持 + CVE 响应
- [ ] 源码授权（NDA 大客户）
- [ ] 高级审计（完整审计钩子 + SIEM 集成）

#### 定价模型

| 项目 | 定价方式 | 启动 Phase |
|------|---------|-----------|
| 基础用量 | 按量 margin（Provider 成本价 + 平台费） | Phase 2 |
| 企业年费 | 按团队规模（头50/100/500 用户分档） | Phase 2 |
| 私有部署许可 | Docker 镜像 + 在线激活，年费制 | Phase 2 |
| 合规模板 | 按行业包购买 | Phase 3 |
| 专属支持 | 按 SLA 等级加价 | Phase 3 |

**客单价预估**：中大型企业年费 ¥50,000–¥500,000（含用量配额）

#### 关键决策

**如何与 Prism ② 共享代码库？**
→ **Decision D2**：同一仓库 + `--features enterprise` feature flag。Rust 的 feature flag 天然支持，避免"免费版修了 Bug 企业版忘了"的场景。

---

### 3.2 象限② — Prism（Prism × To C）

#### 目标客户

- 个人开发者、独立开发者
- 开源项目、小团队
- 技术爱好者、AI 应用玩家
- 中国开发者（需要一个原生支持 DeepSeek/Qwen 的入口）

#### 核心价值

**一个 API Key 访问所有 Provider。** Prism 帮你管理 Key、路由、降级、计费，开发者只需要专注于产品。

用户侧看到的：就是一个 OpenAI 兼容的 API，后端可以自动切换任一 Provider。

#### 功能清单

**Phase 1**：
- [x] OpenAI 兼容 API（`POST /v1/chat/completions`，含流式 SSE）
- [x] `GET /v1/models` 模型列表
- [x] Key 池托管（轮换 + 冷却 + 熔断 + 自动恢复）
- [x] 基础路由（round-robin + fallback 降级）
- [x] 5 个 P0 Provider：OpenAI / Anthropic / Gemini / DeepSeek / Qwen
- [x] API Key 认证（Bearer Token）
- [x] 基础用量记录（SQLite）
- [x] Docker Compose 部署 + Caddy TLS

**Phase 2**：
- [ ] 智能路由（auto 模式：基于成本/延迟/可用性自动选择）
- [ ] BYOK（自带 Provider 合同 + 费率）
- [ ] 用户 Provider 偏好设置（白名单/黑名单）
- [ ] 按量计费（Pricer 引擎实时计算）
- [ ] 订阅套餐（月费含配额，超出按量）
- [ ] 用户面板（用量/成本/历史查询）
- [ ] 管理 API 完善

**Phase 3**：
- [ ] 多模态扩展（图像生成 Flux/DALL-E / Stable Diffusion）
- [ ] 本土支付接入（支付宝/微信支付）
- [ ] 免费配额 + 超出付费模式（零门槛+自然转化）
- [ ] 增值服务（embedding / TTS / STT / rerank）
- [ ] Provider 社区贡献机制

#### 定价模型

| Phase | 模式 | 说明 |
|-------|------|------|
| 1 | **免费** | 闭环验证阶段，不限量/不计费（但用户需自带 Key） |
| 2 | **按量 + margin** | Provider 成本价 + 平台费率。margin ≈ 5-15%，比竞品透明 |
| 2 | **订阅套餐** | 月费 ¥29/¥99/¥299 含配额，超出按量加价 |
| 3 | **转换为免费配额 + 超出付费** | 月免费额度吸引新用户，自然转化付费 |

**定价原理**：Prism 的 margin 空间不止是 5% 加价，而是**总拥有成本更低**——智能路由自动选择最经济的 Provider 组合，Key 池托管省去运维人力，中文 Provider 本土化定价带来直接成本优势。

#### 差异化传播路径（To C）

To C 用户不买"架构优势"，他们买**结果优势**：

| 架构事实 | → 用户语言 |
|---------|-----------|
| 协议运行时 E 层 | "换 Provider 不用改代码"——**省心** |
| Key 池托管 | "不用担心 Key 被封"——**稳定** |
| 架构级不存对话 | "我们存不了你的对话"——**信任** |
| 中文 Provider 直连 | "DeepSeek/Qwen 直连不绕路"——**快 + 便宜** |

---

### 3.3 象限③ — Vela Pro（Vela × To B）

#### 目标客户

- 需要在产品中嵌入 AI 能力的开发者 / SaaS 公司
- 希望为团队提供统一 AI 入口的技术管理者
- 场景 Privacy-first 的组织（客户端处理路由决策）

#### 核心价值

**一行代码接入 Prism 的路由能力。** Vela Pro 是 Prism 生态的客户端扩展——在用户侧完成协议执行和路由决策，Prism 侧只做集中管理和数据同步。

#### 功能清单

**Phase 1**：
- [ ] 开源 Prism SDK（`@ailib/prism-sdk`）— 一行代码接入 Prism API
- [ ] SDK 文档 + 快速入门示例

**Phase 2**：
- [ ] WASM 路由嵌入模块（浏览器端协议执行 + 客户端路由决策）
- [ ] 团队历史 E2E 加密同步（Prism 存密文，Vela 管密钥）
- [ ] 合规模板（轻量）— 医疗/金融/法律预置路由策略 YAML
- [ ] CLI 工具（`vela` 命令行配置路由）

**Phase 3**：
- [ ] 高级 WASM 路由（离线预测 + 本地方案评估）
- [ ] 合规模板（重）— 完整行业 SDK（路由 + 审计钩子 + 脱敏适配）
- [ ] 自托管部署选项

#### 定价模型

| 产品形态 | 定价 | 带 |
|---------|------|:--:|
| Prism SDK | **免费开源** | A |
| WASM 路由基础模块 | **开源免费** | A |
| 高级 WASM 路由 | 付费（年费 ¥1,000–¥5,000） | C |
| 团队 E2E 同步 | 按人头/月（¥30/人/月） | C |
| 合规模板（轻量） | 按行业包购买（¥3,000–¥10,000/包） | C |
| 合规模板（完整 SDK） | 按年签约 + 实施费 | C |

#### 关键决策

**Vela Pro 与 Prism Enterprise 的关系？**
→ **Decision D5**：Phase 2 卖 **Prism + Vela Pro 捆绑套餐**（网关 + SDK + 审计 + 支持）。Vela Pro 不捆绑 Prism 则功能不完整。Phase 3 再视市场反馈决定是否需要独立拆卖。

**WASM 路由在 Phase 1 的优先级？**
→ **Decision D4**：Phase 1 不以 WASM 路由为卖点。多数开发者的 AI 调用在服务器端，客户端 WASM 路由的需求有待验证。Phase 2 将其作为"隐私+离线"增强功能加入。

---

### 3.4 象限④ — Vela（Vela × To C）

#### 目标客户

- 日常使用 AI 的个人用户
- 隐私敏感型用户（不希望对话数据被平台存储）
- 需要多模型对比/选择的消费者
- 中国用户（需要一个本土化的 AI 聊天入口）

#### 核心价值

**帮用户选最好的模型，数据留在本地。**

Poe 是"给你所有模型你自己试"，Vela 是"告诉你要做什么，我帮你选最适合的"，同时保证对话数据平台存不了。

#### 功能清单

**Phase 1**（最小 UI 验证）：
- [ ] 聊天 UI + 多模型切换（固定在页面上选择 Provider 和模型）
- [ ] 本地对话历史存储（IndexedDB）
- [ ] 一键导出 Markdown/PDF
- [ ] 对基础聊天结果提供 Provider/模型标签以增加感知度

**Phase 2**（体验升级）：
- [ ] 智能推荐："这个问题用 DeepSeek 最划算"
- [ ] 模型对比并排显示（最多 4 模型同时输出）
- [ ] E2E 加密多设备同步（付费增值）
- [ ] 用户偏好学习（从历史中学习你的模型偏好）

**Phase 3**（付费转化）：
- [ ] 多设备无缝漫游
- [ ] 高级分析（使用习惯洞察、成本报告）
- [ ] 高级路由（auto mode）付费解锁
- [ ] 模型对比无限扩容

#### 定价模型

| 功能 | 免费 | 付费（¥19/月） |
|------|:---:|:---:|
| 基础聊天 + 本地历史 | ✅ | ✅ |
| 多模型切换 | ✅ | ✅ |
| 手动对比（2 模型） | ✅ | ✅ |
| 无限对比 | — | ✅ |
| 高级路由（auto） | — | ✅ |
| E2E 加密多设备同步 | — | ✅ |
| 云端历史（E2E 加密） | — | ✅（¥39/月） |

#### 关键决策

**Phase 1 的投入深度？**
→ **Decision D1**：Phase 1 Vela 做到"可演示的最小聊天界面"即可（验证 Prism API 可用性），不做消费者级体验。目标用户是验证闭环的**技术验证者**，而不是大众消费者。Phase 2 再升级到消费者级体验。

**Vela vs Poe 的生存空间？**
Poe 多模态体验极好。Vela 差异化在**隐私**和**透明**——Poe 存对话（消费者聊天产品基因），Vela 的架构决定了对话留在本地。短期受众：隐私敏感人群 + 成本透明用户 + 中国开发者 = 小众但高忠诚度。Phase 1 不与 Poe 竞争消费者市场份额，Phase 2 以隐私 + 透明切入细分。

---

## 4. 三阶段路线图

### 4.1 Phase 1（3 周）—— 验证闭环

**目标**：跑通"一个请求从 Vela → Prism → Provider → 返回用户"的完整链路，做到外部可演示、可访问、可验证。

#### 各象限交付物

| 象限 | 交付物 | 投入比例 | 说明 |
|------|--------|:-------:|------|
| ② Prism | 核心 API + Key 池 + 基础路由 + 5 Provider | **80%** | 主力输出 |
| ④ Vela | 最小聊天 UI（验证 Prism API） | 15% | 只做验证层 |
| ③ Vela Pro | Prism SDK 开源客户端库 | 5% | 随 Prism 产出 |
| ① Enterprise | — | 0% | 不做 |

#### 里程碑

| 里程碑 | 时间 | 验收标准 |
|--------|------|---------|
| M1: Proxy Works | Week 1 末 | 请求链路跑通，OpenAI + Anthropic 2 个 Provider 可用 |
| M2: Production Ready | Week 2 末 | Key 池 + 计费 + 降级完整，SQLite 用量记录 |
| M3: Live | Week 3 末 | `api.prism.ailib.info` 外部可用，5 Provider 全通 |

#### 任务包

**Prism Phase 1 任务包**：

| ID | 任务 | 依赖 | 人天 |
|----|------|------|:---:|
| P1-01 | Prism 项目骨架（Axum + config + health） | ai-lib-core v0.9.4 | 1-2 |
| P1-02 | 核心代理（`/v1/chat/completions` 同步+流式） | P1-01 | 2-3 |
| P1-03 | Key 池调度（轮换 + 冷却 + 熔断） | P1-02 | 1-2 |
| P1-04 | 用量记录（SQLite + Pricer） | P1-02 | 1-2 |
| P1-05 | 降级路由（主 Provider 失败 → 备用切换） | P1-02, P1-03 | 1 |
| P1-06 | Docker 部署 + Caddy TLS + 域名配置 | P1-05 | 1-2 |
| P1-07 | 管理 API（`/admin/*`） | P1-03, P1-04 | 1-2 |
| P1-08 | 5 个 P0 Provider 联调验证 | P1-06 | 1-2 |

**Vela Phase 1 最小任务包**：

| ID | 任务 | 依赖 | 人天 |
|----|------|------|:---:|
| V1-01 | Vela Web 骨架（React/Vanilla + Prism SDK 调用） | Prism 可用 | 2-3 |
| V1-02 | 本地对话历史（IndexedDB + 导出） | V1-01 | 1 |
| V1-03 | Provider/模型导航 UI | V1-01 | 1-2 |

**wasm-test 工程化任务包**（与 Prism Phase 1 并行）：

| ID | 任务 | 说明 |
|----|------|------|
| W-01 | 可复现构建（run 脚本 / justfile + `.gitignore` 产物路径） | 独立 |
| W-02 | 静态资源路径清理（取消机器相关绝对路径） | 独立 |
| W-03 | 容器化（多阶段 Dockerfile + env 约定） | 独立 |
| W-04 | 双 WASM 线说明（浏览器 vs WASI 适用场景）写入 README | 独立 |
| W-05 | v0.2.0 tag + changelog | 独立 |

#### 闸门关系

- **Prism Phase 1 不阻塞 PT-073**：使用已发布的 `ai-lib-core v0.9.4`，不依赖 v1.0 RC
- **Vela Phase 1 依赖 Prism 可用**：Vela 开发可先以 mock API 做前端，后端对接依赖 Prism 上线
- **wasm-test 工程化完全独立**：可与 Prism 并行，无交叉依赖

---

### 4.2 Phase 2（6-8 周）—— 规模化 + 初步商业化

**目标**：Prism 完成从免费验证到付费产品的跃迁，Enterprise 首版可交付，Vela 达到消费者可用水平，WASM 路由能力可展示。

#### 各象限交付物

| 象限 | 交付物 | 说明 |
|------|--------|------|
| ② Prism | 智能路由 + BYOK + 用户偏好 + 按量计费 | 核心产品升级，Pricer 引擎上线 |
| ① Enterprise | 多租户 + SSO + 审计 + 私有部署（Docker） | To B 首版 MVP |
| ③ Vela Pro | WASM 路由 + 团队同步 + 合规模板（轻量） | To B 嵌入能力 |
| ④ Vela | 智能推荐 + 模型对比 + E2E 同步（付费） | To C 体验升级 |

#### 里程碑

| 里程碑 | 验收标准 | 周 |
|--------|---------|:--:|
| M4: Smart Routing | 智能路由（auto 模式）可基于成本/延迟/可用性决策 | W5 |
| M5: First Paid User | Prism 按量计费在线，用户可付费调用 | W6 |
| M6: Enterprise MVP | Enterprise 私有部署 Demo 可交付（SSO + 审计） | W7 |
| M7: Vela Consumer Ready | Vela 聊天 UI + 智能推荐 + 模型对比上线 | W8 |

#### 任务包概览

| 模块 | 重点任务 | 预估 |
|------|---------|:----:|
| Prism | 智能路由引擎 + BYOK 支持 + 用户面板 | 15-20 天 |
| Enterprise | 多租户 feature flag + SSO 连接器 + 审计管道 | 10-15 天 |
| Vela Pro | WASM 路由模块 + SDK 升级 + 团队同步 | 10-12 天 |
| Vela | 智能推荐 + 模型对比 + 付费服务层 | 8-10 天 |
| 基础设施 | PostgreSQL 迁移（可选）+ 负载均衡 | 5-7 天 |

#### 闸门关系

- **Prism Phase 2 智能路由：依赖 PT-073 后的 Contact API 稳定度**
- **Enterprise 首版：新增 SSO/审计基础设施，与 PT-073 松耦合**

---

### 4.3 Phase 3（3-6 月）—— 商业化 + 生态

**目标**：收入模式成熟化，Enterprise 高客单价产品形成稳定收入，合规模板进入垂直行业，Vela 订阅制用户规模增长。

#### 各象限交付物

| 象限 | 交付物 | 说明 |
|------|--------|------|
| ② Prism | 多模态 + 本土支付 + 订阅制 | 收入模式成熟，覆盖开发者全场景 |
| ① Enterprise | SLA 99.9% + 合规模板（重）+ 专属支持 | 高客单价企业产品定型 |
| ③ Vela Pro | 合规模板（审计钩子）+ 自托管 | 垂直行业渗透 |
| ④ Vela | 多设备漫游 + 高级分析 | To C 付费转化 |

#### 里程碑

| 里程碑 | 验收标准 |
|--------|---------|
| M8: Multi-modal | Prism 支持图像生成（Flux/DALL-E），Vela 聊天嵌入视觉 |
| M9: China Payments | 支付宝/微信支付上线 |
| M10: SLA 99.9% | Enterprise SLA 生效，含赔偿条款 |
| M11: Industry Templates | 医疗 HIPAA / 金融 PCI / 法律 GDPR 模板上线各至少 1 个 |
| M12: Vela Subscription | Vela 月活付费用户 > 100 |

---

## 5. 热点产品与工具分析

每个 Phase 单独列出可能形成口碑传播 / HN 热门 / 开发者社区讨论的**独立亮点**。

### 5.1 Phase 1 热点

#### 🔥 热点 1：Prism — 5-Provider 免费开放代理

- **一句话描述**：5 个顶级 AI Provider 通过一个单点免费接入（Phase 1 免费）
- **为什么可能火**：OpenRouter 的竞争替代品，且 Phase 1 完全免费的 offer 极吸引早期开发者。在 HN/Reddit r/MachineLearning 上"Free access to all major LLMs through one API"话题热度极高。
- **目标受众**：独立开发者、微创业团队、AI 玩家
- **MVP 形态**：`api.prism.ailib.info` 上线 + README 写一句"Your first 10K requests free, no credit card required"。不需要注册，发请求即用。

#### 🔥 热点 2：WASM 协议运行时 Demo（ailib-wasm-test）

- **一句话描述**：浏览器里跑 AI 协议执行引擎，说明 AI 协议可以完全在客户端本地执行
- **为什么可能火**："Protocol runtime runs in browser"在技术社区有很强的展示效应。WASM 引擎 1.3MB 的 binary size 也是亮点。这不是普通 Demo，而是**协议在浏览器端完整执行的证明**，直接验证了"去中心化"叙事。
- **目标受众**：WebAssembly 爱好者、PL 社区、AI 基础设施开发者
- **MVP 形态**：GitHub Pages 上线 `ailib-wasm-test` 浏览器 Demo，README 说明协议编译为 WASM 的全过程。配合博文"Compiling an AI Protocol Runtime to WASM"发出。

#### 🔥 热点 3：`@ailib/prism-sdk` 开源客户端库

- **一句话描述**：一行代码连接 Prism，npm 包已发布
- **为什么可能火**：低门槛接入是关键。如果 SDK 真的做到 `import { Prism } from '@ailib/prism-sdk'; const ai = new Prism();` 就能用，对比 OpenAI 的 OpenAI SDK 需要配置 key/org，Prism SDK 更轻、更友好。
- **目标受众**：前端开发者、全栈开发者
- **MVP 形态**：npm 包发布 + README 含 3 行代码示例 + 集成到 Vela 聊天 UI

---

### 5.2 Phase 2 热点

#### 🔥 热点 4：Prism BYOK 模式（自带 Key）

- **一句话描述**：已有 OpenAI Key？到 Prism 上也能用，同时解锁智能路由
- **为什么可能火**：把竞品变成了上游。"Bring your own keys to get smart routing"——用户不损失已有合同优惠，还能享受智能路由。OpenRouter 也有 BYOK，但 Prism 的 BYOK + 托管 Key 池组合是独特卖点。
- **目标受众**：已有 Provider 合同的中小型团队
- **MVP 形态**：配置文件中可填写用户自己的 Provider Key，Prism 统一管理"自有池 + 平台池"的双池调度。

#### 🔥 热点 5：Vela 模型对比并排显示

- **一句话描述**：发同一个问题，4 个模型同时回答，并排对比
- **为什么可能火**：在 Poe 和 ChatGPT 上没有这个功能。对开发者而言，"Same prompt, compare outputs"是刚需。对比工具+路由推荐=价值落地。
- **目标受众**：技术型 AI 用户、Prompt 工程师、模型评估者
- **MVP 形态**：Vela 聊天界面输入 prompt，选择模型组合，以 2×2 网格展示输出结果，附成本/延迟/质量评分标签。

#### 🔥 热点 6：Prism 智能路由（auto mode）

- **一句话描述**：不需要选择和匹配，Prism 会优先使用成本、可用性或最优场景的 Provider
- **为什么可能火**：在社区讨论中"LLM routing"本身是小众热点。"Auto"是最强的宣传词。加上按 Provider 的最新负载自动优化，是工程角度的又一亮点。
- **目标受众**：追求"能跑就行"的务实开发者
- **MVP 形态**：请求 `model: "auto"` → Prism 评估成本+延迟+可用性 → 自动选择最优 Provider + 返回实际 provider 标签。

---

### 5.3 Phase 3 热点

#### 🔥 热点 7：Prism 多模态（Flux/DALL-E/Stable Diffusion 聚合 API）

- **一句话描述**：文本+图像+视频的聚合多模态 API
- **为什么可能火**：文本聚合市场竞争激烈，但**多模态聚合**做好的很少。Poe 是消费者侧多模态，Prism 是开发者 API 侧多模态，不同定位。
- **目标受众**：需要多模态能力的应用开发者、AI Startups
- **MVP 形态**：`POST /v1/images/generations` 兼容 OpenAI Image API 格式，后端路由到 Flux/DALL-E/SD。对用户不透明。

#### 🔥 热点 8：合规模板包（医疗/金融/法律）

- **一句话描述**：AI 需要医疗数据还去了 DeepSeek？合规包帮你设计路由和脱敏策略
- **为什么可能火**：合规是最好的 To B 卖点之一。垂直行业的合规包打"企业级"牌，"Banking/Healthcare model routing"是企业 CTO 的痛点。
- **目标受众**：受监管行业的企业客户
- **MVP 形态**：YAML 配置文件 + 审计日志 + 策略生效证明。打包为 `.tar.gz`，售价 ¥5,000/行业包。

---

## 6. 三带模型对齐

### 6.1 A 带（永久开源）

**原则**：标准与"能互操作的最小实现"在 A 带。这是生态的共同语言。

| 内容 | 所属 | License |
|------|------|---------|
| `ai-protocol` 全量 schema、示例 manifest、合规测试夹具 | 协议层 | Apache-2.0 |
| `ai-lib-core`（各语言 E 层：driver/transport/pipeline） | E 层 | Apache-2.0 |
| `ai-lib-wasm`（WASI 侧协议运行时） | E 层 | Apache-2.0 |
| `ailib-wasm-test`（浏览器 Demo） | A 层参考 | Apache-2.0 |
| `@ailib/prism-sdk`（Prism 客户端 SDK） | Prism 入口 | Apache-2.0 |
| Vela 聊天 UI 核心 | Vela | Apache-2.0 |
| Vela 本地历史（IndexedDB） | Vela | Apache-2.0 |
| Vela WASM 路由基础模块 | Vela | Apache-2.0 |
| `ai-lib-benchmark` 方法与基线 | 基准 | Apache-2.0 |

### 6.2 B 带（开源但明确"非 SLA / 自承载"）

**原则**：B 带降低采用门槛，但**不透支企业承诺**。开源声明 README 中标注"非企业 SLA"、"不保证与某云厂商最新限额一致"。

| 内容 | 所属 | 说明 |
|------|------|------|
| Vela E2E 加密同步客户端实现 | Vela/A | 开源但非 SLA，需自建同步服务 |
| Contact（P）参考实现 + 示例策略 + 示例路由 | P 层 | 教育市场用，非生产级 |
| spiderswitch（执行面+MCP） | 路由 | 保持执行面职责，不承诺"多租户路由大脑"全功能 |

### 6.3 C 带（可闭源 / 双授权 / 仅二进制商业模块）

**原则**：C 带卖的是**信任、责任范围与集成深度**，不是"没有 schema 就不可用"的勒索式闭源。

| 内容 | 所属 | 理由 |
|------|------|------|
| Prism 路由引擎（智能路由/降级/熔断） | Prism 核心 | 商业护城河 |
| Key 池调度算法 + 计费引擎 | Prism 核心 | 实现细节是竞争壁垒 |
| Prism Admin API + 管理面板 | Prism 核心 | 运营与安全责任 |
| Prism Pack 签名分发服务 | Prism 扩展 | 运营资产 |
| Enterprise 多租户/SSO/审计 | Enterprise | 集成与支持成本 |
| 合规模板（行业包内容） | Enterprise | 内容与服务 |
| Vela 云端 E2E 加密同步服务 | Vela | 服务 infra |
| Vela 高级 WASM 路由 | Vela | 增值功能 |

### 6.4 三带 × 矩阵对应表

| | A 带（永久开源） | B 带（开源非 SLA） | C 带（可闭源） |
|---|---|---|---|
| **① Enterprise** | E 层 + Prism SDK | Vela Pro E2E 同步客户端 | 路由引擎 + 审计 + SSO + 合规模板 |
| **② Prism** | E 层 + Prism SDK | — | Key 池 + 计费 + Admin API |
| **③ Vela Pro** | Vela 客户端核心 + WASM 基础 | 团队同步客户端 | 高级 WASM 路由 + 合规模板 |
| **④ Vela** | Vela 聊天 UI + 本地历史 | — | E2E 同步服务 + 高级路由 |

**洞察**：每个象限都有 A 带基础（开源引流）和 C 带增值（商业收费）。B 带只在特定象限出现（Enterprise 和 Vela Pro 的"可自托管"组件）。

---

## 7. 商业模式汇总

### 7.1 收入来源按象限

| 象限 | 主要收入 | 客单价 | 启动 Phase |
|------|---------|--------|-----------|
| ① Enterprise | 年费 + 按量 margin + 合规模板 | 高（¥50K–¥500K/年） | 2 |
| ② Prism | 按量 margin / 订阅套餐 | 低-中（按用量 ¥29–¥299/月） | 2 |
| ③ Vela Pro | 合规模板 / 团队同步 | 中（按人头 ¥30/人/月） | 2 |
| ④ Vela | 增值订阅（高级路由 + 同步） | 低（¥19–¥39/月） | 3 |

### 7.2 Phase 1 → 3 收入演进

```
Phase 1（3周）    Phase 2（6-8周）        Phase 3（3-6月）
────┬──────      ────┬──────────        ────┬──────────
    │ 免费           │ 首个付费用户          │ 稳定收入
    │ 闭环验证        │ Prism 按量计费        │ Enterprise 年费
    │ 零收入          │ BYOK 平台费           │ Vela 订阅
    │                │ Enterprise 试点       │ 合规模板销售
    │                │ 收入：初期            │ 收入：成长
```

### 7.3 定价策略核心原则

1. **Phase 1 全免费** — 降低采用门槛，收集真实用量数据，验证闭环稳定性
2. **Phase 2 先按量后订阅** — Decison D3 确认：先按量加价（最简单结算模型），Phase 3 迁移到"免费配额 + 超出付费"（零门槛+自然转化）
3. **Enterprise 捆绑销售** — Decision D5 确认：Phase 2 Prism + Vela Pro 捆绑，Phase 3 再拆卖
4. **架构级定价优势** — margin 不止来自加价，更来自 Key 池省运维、智能路由省成本、中文 Provider 更低的基础定价

### 7.4 三带模型的商业闭环

```
A 带（开源）→ 开发者采用 ai-lib-core/SDK → 协议网络效应
                      ↓
              需要路由/Key池/计费
                      ↓
          自然流向 Prism（C 带闭源）
                      ↓
               B 带（开源非 SLA）
           教育市场 + 降低顾虑
                      ↓
          C 带付费（引擎/同步/合规模板）
               实际收入来源
```

**Prism 是 C 带从"概念"变成"有名字的产品、有架构、有 Phase 1 计划"的关键锚点。**

---

## 8. 竞品对标定位

### 8.1 竞品矩阵

| | **To B（企业/开发者）** | **To C（个人用户）** |
|---|---|---|
| **平台侧（Prism）** | Prism Enterprise vs **OpenRouter** | Prism vs **OpenRouter（免费层）** |
| **客户端（Vela）** | Vela Pro vs **LangChain / LlamaIndex** | Vela vs **Poe / ChatGPT** |

**关键发现**：
- **OpenRouter 只占一个象限**（平台侧 To B/C），没有客户端产品
- **Poe 只占一个象限**（客户端 To C），没有平台侧独立产品
- **Prism + Vela 覆盖全部四个象限**——这是 E/P 分层架构的市场优势
- **LangChain / LlamaIndex 在 Vela Pro 象限**，但它们是编排框架，不是协议运行时——不同生态位

### 8.2 核心差异定位

| 维度 | Poe | OpenRouter | Prism/Vela |
|------|-----|-----------|------------|
| **本质** | 消费者聊天+模型超市 | 开发者路由市场 | 协议执行引擎+策略商业层 |
| **路由能力** | ❌ 无 | ✅ 声明式（用户控制） | ✅ 策略引擎驱动（平台自动） |
| **Key 管理** | 共享/无 | 自带 Key | **托管池（核心差异）** |
| **隐私** | 不明确 | 可配置 ZDR | **架构级（存不了）** |
| **协议深度** | API 转发 | API 转发+路由 | **协议运行时（核心差异）** |
| **中文市场** | ❌ | ❌ | **✅（核心差异）** |
| **学术支撑** | ❌ | ❌ | **✅（核心差异）** |
| **多模态** | ✅ 最强 | ⚠️ 有限 | ⚠️ Phase 2+ |
| **模型数量** | 100+ | 300+ | 10（Phase 1） |
| **BYOK** | ❌ | ✅ | ⚠️ Phase 2 |

### 8.3 一句话差异化

> **Poe = 消费者的 AI 游乐场（多模态，手动选）**
> **OpenRouter = 开发者的模型路由器（声明式控制，自带 Key）**
> **Prism = 企业级 AI 协议引擎（托管 Key 池，架构级隐私，协议标准）**

### 8.4 Go-to-Market 优先路径

1. **论文发表** → 学术话语权 → 行业标准认知
2. **E 层开源** → 开发者社区 → driver 贡献 + SDK 采用
3. **中文市场首发** → 本土 Provider + 本土支付 → 蓝海切入
4. **隐私叙事** → 合规敏感客户 → 架构级隐私 = 无需信任

---

## 9. 与原始文档的修正对照

### 9.1 产品名映射

原始文档（《ai-lib开源分界产品规划和商业模式》）中散落的 7 个构想，全部收敛为 **2 条产品线 4 个象限**：

| 原始文档构想 | 实际对应 | 归属 | 说明 |
|-------------|---------|------|------|
| AI-Capability Router | **Prism** | P 层（C 带） | Phase 1 闭源网关 |
| Capability Pack | **Prism Pack** 服务 | P 层 | Phase 2+ manifest 分发+签名 |
| Enterprise Console | **Prism Admin** | Enterprise | Phase 2+ 管理 API + Web UI |
| Distributed AI Mesh | — | 愿景 | 不在 12 个月路线图内 |
| Benchmark Pro | **ai-lib-benchmark SaaS** | 独立 | Phase 2+，松耦合 |
| 托管透明协议 Proxy | **Prism** | P 层（C 带） | Phase 1，即 Prism 本身 |
| Zero-Config Hybrid Router | **Vela + WASM** | A 层（A/B 带） | Phase 2+ 客户端路由 |

### 9.2 关键修正点

| 原文档主张 | 现规划立场 | 修正原因 |
|-----------|-----------|---------|
| "manifest 解析器必须闭源" | schema 全公开，解析器继续开源；企业增强（签名链/策略生效证明）可闭源 | 与多运行时生态一致，避免社区信任崩盘 |
| "spiderswitch 作为统一路由内核" | spiderswitch 保持执行面+MCP 职责；Prism 是独立 P 层产品，不绑定 spiderswitch | 遵守已决 E/P 分离架构 |
| "P2P Mesh 作为 6 月目标" | 标为愿景/研究项，不在 12 月路线图内 | 无现成 P2P 基座，小团队不可承受 |
| "一年变成基础设施公司"时间表 | 三阶段实线：3 周→6-8 周→3-6 月，从验证闭环到规模化 | 与 PT-073、E/P 治理、小团队现实对齐 |
| "AI-Capability Router 作为独立商业单品" | 收敛为 **Prism**（4 象限矩阵的唯一平台侧产品） | Prism/Vela 命名已决 |

### 9.3 保留的一致性项

| 原文档主张 | 与现规划一致 | 说明 |
|-----------|:----------:|------|
| 协议进 WASM、薄代理 | ✅ | `ailib-wasm-test` 已验证，Vela WASM 路由 Phase 2 |
| "路由与分发能力"是差异化 | ✅ | Prism P 层策略引擎 + Key 池 = 竞品没有的组合 |
| Open Core：core/SDK 开源 | ✅ | A 带定义与三带模型一致 |
| ailib-wasm-test 先做到"可演示" | ✅ | 与 Phase 1 wasm-test 工程化任务包一致 |
| 上下文存储纯客户端策略 | ✅ | Phase 1 纯客户端，Phase 2 E2E 加密 |

---

## 10. 附录：已确认决策清单

以下决策经先生确认，已写入本规划方案：

| 编号 | 决策 | 结论 | 影响 |
|:----:|------|------|------|
| D1 | Phase 1 Vela To C 的深度 | 最小演示 UI，不做消费者级体验 | Prism 80% / Vela 15% 投入分配 |
| D2 | Enterprise 与 Prism 共享代码库 | 同一仓库 + `--features enterprise` | 工程架构：feature flag |
| D3 | Phase 2 收费模式 | 先按量加价，Phase 3 迁移到免费配额+超出付费 | 商业节奏：两阶段定价 |
| D4 | WASM 路由在 Vela Pro 优先级 | Phase 2 增强功能，非 Phase 1 核心 | 技术投入顺序：服务器端路由优先 |
| D5 | Enterprise + Vela Pro 捆绑 vs 拆卖 | Phase 2 捆绑，Phase 3 拆卖 | 产品组合策略 |

---

### 版本历史

| 版本 | 日期 | 变更说明 |
|------|------|---------|
| v2.0 | 2026-04-25 | 初始正式版。合成 6 份审阅报告 + Prism/Vela 补充 + 矩阵分析，建立矩阵骨架、四象限分析、三阶段路线图、三带对齐、热点分析、竞品定位和修正对照 |

---

*文档完*

Spider 🕷️ | 2026-04-25
