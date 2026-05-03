<!-- omit in toc -->
# MarketSense

基于多 Agent 协作的自主式市场信息抓取分析智能体系统。

> 从"人找信息"到"**信息带着判断来找人**"。

<p align="center">
  <img src="https://img.shields.io/badge/Architecture-Multi--Agent-blueviolet" alt="Multi-Agent">
  <img src="https://img.shields.io/badge/Orchestration-LangGraph-00cec9" alt="LangGraph">
  <img src="https://img.shields.io/badge/Deployment-Private_LLM-ff6b6b" alt="Private LLM">
  <img src="https://img.shields.io/badge/Throughput-120K_items/day-6c5ce7" alt="120K/day">
</p>

---

## 1. 解决的问题

在企业战略决策中，市场情报团队长期面临三大痛点：

| 痛点 | 描述 |
|---|---|
| **信息碎片化** | 新闻、社交媒体、财报、研报分散于数十个独立平台，人工搜集效率极低，关键信息频繁遗漏 |
| **分析浅层化** | 缺乏跨源关联与长链推理能力，单条信息易被孤立解读，弱信号淹没在噪音中 |
| **预警滞后** | 传统日报/周报以天为单位，等报告到达决策层时，市场窗口早已关闭 |

> **核心数据**：分析师 70% 以上的时间消耗在机械性搜集与整理上，而非真正的洞察。

---

## 2. 设计理念

系统围绕 **"感知 — 理解 — 推理 — 行动"** 闭环设计：

```
异构数据源  →  Kafka 实时管道  →  清洗 & 对齐  →  分析引擎  →  预警 & 推送
  (新闻/社交/财报/研报)                                          (置信度评分 + 行动建议)
```

采用**分层多 Agent 架构**，每个 Agent 聚焦单一职责，通过 LangGraph 编排复杂决策流。各 Agent 之间松耦合、高内聚，可独立扩展或替换。

---

## 3. 核心 Agent

| Agent | 职责 | 关键技术 |
|---|---|---|
| 🕷 **抓取 Agent** | 自主解析目标页面，应对反爬，注入 Kafka | Playwright, 亮数据代理池 |
| 🧹 **清洗 Agent** | 非结构化 → 统一 Schema，去重，实体对齐 | 结构化抽取模型 |
| 🧠 **分析 Agent** | 舆情情感、竞争态势、供应链风险深度解析 | 行业知识图谱, RAG, 思维链推理 |
| 🚨 **预警 Agent** | 多维发现整合为报告，触发阈值自动推送 | 置信度评分, 企业微信 Bot |

### 分析 Agent 详解（系统核心）

分析 Agent 被设计为具备**长链推理能力**的思维链引擎：

1. **背景补充** — 调用行业知识图谱与 RAG 检索引擎，获取上下文
2. **多轮推理** — 通过"假设 → 验证 → 修正"循环进行演绎分析
3. **弱信号识别** — 跨源关联分析，例如：某原材料论坛抱怨激增 → 可能预示供应商产能问题

---

## 4. 多 Agent 辩论机制

为解决单一模型幻觉带来的误判，引入对抗式协作：

```
  🔵 乐观视角 Agent                       🔴 悲观视角 Agent
  (最佳情境推演)                          (最坏情境推演)
         │                                       │
         └────────────┬─────────────────────────┘
                      ▼
              🟡 仲裁 Agent
         (综合两方论证，排除单一偏见，生成最终评估)
```

**效果**：两份独立简报 + 一份综合评估 = 三眼审视，显著降低幻觉风险。

---

## 5. 技术栈

| 层级 | 技术选型 |
|---|---|
| Agent 编排 | LangGraph |
| 动态抓取 | Playwright + 亮数据代理池 |
| 数据管道 | Apache Kafka |
| 知识增强 | RAG 检索 + 行业知识图谱 |
| 推理模型 | 私有化部署大语言模型 |
| 消息推送 | 企业微信 Bot API |
| 部署环境 | 全链路内网部署 |

---

## 6. 运行指标

| 指标 | 数值 |
|---|---|
| 日均处理信息量 | **12 万条**（多语种） |
| 日均 Token 消耗 | **180 万** |
| 市场周报产出时间 | 3 天 → **4 小时** |
| 关键风险识别速度 | 提升 **90%** |
| 数据安全 | 全链路私有化，零外部传输 |

---

## 7. 项目结构

```
.
├── agents/
│   ├── crawler/       # 抓取 Agent
│   ├── cleaner/       # 清洗 Agent
│   ├── analyzer/      # 分析 Agent（含辩论子模块）
│   └── alerter/       # 预警 Agent
├── graph/
│   └── workflow.py    # LangGraph 编排逻辑
├── knowledge/
│   ├── graph/         # 行业知识图谱
│   └── retriever/     # RAG 检索引擎
├── pipeline/
│   └── kafka/         # Kafka 生产/消费配置
├── docs/
│   └── index.html     # 项目展示页
└── config/
    └── settings.yaml  # 全局配置
```

---

## 8. License

MIT © 2025
