# Quant Agent CN - A股量化 AI Agent 系统

## 项目概述

基于 [TradingAgents-CN](https://github.com/hsliuping/TradingAgents-CN) 的 A股/期货量化分析系统。
使用多 Agent LLM 协作（LangGraph 编排）进行基本面分析、政策解读、风险评估。

**定位**：研究工具优先（政策解读+基本面+风险预警），选股信号为实验性功能。

## A股交易规则硬性约束

所有涉及交易逻辑、回测、信号生成的代码，**必须遵守以下规则**：

1. **T+1 结算**：当日买入的股票次日才能卖出，Agent 决策必须考虑持仓锁定
2. **涨跌停**：主板 ±10%，创业板/科创板 ±20%，触及涨跌停视为无法成交
3. **最小交易单位**：100 股（1手），仓位计算向下取整到整手
4. **印花税**：卖出时 0.05%（单边），买入不收
5. **佣金**：约万分之 2.5，双边收取
6. **停牌处理**：停牌股票不可交易，持仓计为流动性风险
7. **交易时间**：9:30-11:30, 13:00-15:00（北京时间），节假日休市

## 期货规则（Phase 2）

1. **T+0**：当日可买卖，与 A 股规则完全不同
2. **双向交易**：可做多也可做空
3. **保证金制度**：杠杆交易，需监控保证金占用率
4. **夜盘**：部分品种 21:00-次日 02:30
5. **合约展期**：主力合约换月时需处理展期

## 技术架构

```
本项目 (quant-agent-cn)
├── tradingagents/          # 核心框架（来自 TradingAgents-CN）
│   ├── agents/             # Agent 定义（analysts, risk_mgmt, trader）
│   ├── dataflows/          # 数据层（AKShare, Tushare, BaoStock）
│   ├── graph/              # LangGraph 编排
│   ├── llm_adapters/       # 多 LLM 支持
│   └── tools/              # 分析工具
├── web/                    # Streamlit Web UI
├── app/                    # FastAPI 后端
├── frontend/               # Vue.js 前端
├── tests/                  # 测试
└── data/                   # 数据和分析结果
```

## 开发规范

- 数据源：优先使用 AKShare（免费），Tushare 作为备选
- LLM：不锁定特定提供商，通过 llm_adapters 抽象层切换
- Prompt：逐步提取到独立文件，便于版本管理和 A/B 测试
- 回测：必须包含 T+1、涨跌停、印花税等 A 股真实约束

## upstream 同步

- `origin`: yanchuanjiu/quant-agent-cn（我们的 repo）
- `upstream`: hsliuping/TradingAgents-CN（上游）
- 定期 `git fetch upstream` 同步上游更新
