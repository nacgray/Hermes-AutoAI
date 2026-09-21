# Hermes-AutoAI

> 基于 [hermes-agent](https://github.com/NousResearch/hermes-agent) 引擎的**多智能体协同 AI 开发工作台** —— 一家"可视化的 AI 软件外包公司"。

Hermes Agent 作为主 Agent，负责任务规划与划分；角色化子 Agent（需求分析师/架构师/评审专家/开发工程师/集成工程师/测试工程师/DevOps）沿**软件工程 SOP 流水线**（需求分析 → 模块设计 → 分析验证 → 模块开发 → 协同集成 → 测试 → 部署落地）交付稳定可靠的软件产品。全过程**前端可视化、可干预（含中途 steer/queue 临时指令）、可追溯**（需求→设计→代码→测试全链路）。

- 架构设计：[docs/architecture.md](docs/architecture.md)（v0.3）
- P0.5 引擎验证报告：[docs/p05-engine-verification-report.md](docs/p05-engine-verification-report.md)

## 仓库结构

```
Hermes-AutoAI/
├── docs/                     # 架构文档、引擎验证报告
├── apps/web/                 # 工作台前端（Vite + React + TS）
├── services/server/          # 治理层后端（FastAPI + SQLAlchemy + Alembic）
│   ├── autoai/
│   │   ├── core/             # pipeline engine（P1）
│   │   ├── engine/           # HermesAdapter 防腐层（接口草案）
│   │   ├── artifacts/        # 制品库（版本化 + Schema 校验）
│   │   ├── events/           # 事件总线（append-only）
│   │   ├── storage/          # ORM 模型
│   │   └── api/              # REST + WebSocket
│   ├── alembic/              # DB 迁移
│   └── tests/
├── pipelines/                # SOP 流水线模板（YAML，数据不是代码）
├── profiles/                 # 引擎角色 Profile（persona/工具/模型路由）
├── goals/                    # 自包含 goal 模板（jinja2）
├── spike/hermes/             # P0.5 引擎验证套件（8 项，可复跑）
├── workspaces/               # 每 Mission 工作区（运行时数据，gitignore）
└── .github/workflows/        # CI（backend: ruff+pytest；frontend: build）
```

## 快速开始（P0）

### 后端（治理层）

```powershell
cd services/server
uv venv .venv
.venv\Scripts\activate          # Linux/macOS: source .venv/bin/activate
pip install -e ".[dev]"
alembic upgrade head            # 初始化 SQLite（dev.db）
uvicorn autoai.api.main:app --reload --port 8000
# 健康检查: http://127.0.0.1:8000/api/health
```

### 前端（工作台）

```powershell
cd apps/web
npm install
npm run dev                     # http://127.0.0.1:5173 （/api、/ws 已代理到 8000）
```

### P0 DoD 自检（API 写入读回事件）

```powershell
Invoke-RestMethod -Method Post http://127.0.0.1:8000/api/missions -ContentType "application/json" -Body '{"title":"demo","brief":"P0 DoD"}'
Invoke-RestMethod -Method Post http://127.0.0.1:8000/api/missions/1/events -ContentType "application/json" -Body '{"type":"mission.demo","data":{"hello":"autoai"}}'
Invoke-RestMethod http://127.0.0.1:8000/api/missions/1/events
```

或直接在前端界面创建任务 → 发送测试事件 → 观察实时事件流（含 WebSocket 推送与断线补拉）。

### 引擎验证 Spike（P0.5）

见 [spike/hermes/README.md](spike/hermes/README.md)。

## 当前状态

| 里程碑 | 状态 |
|---|---|
| P0 地基（Monorepo/CI/DB/事件制品基座/Adapter 草案） | ✅ |
| P0.5 引擎验证 Spike（8 项） | 见验证报告 |
| P1 治理内核（S1–S3 文档闭环） | 未开始 |
| P2 工作台 v1 | 未开始 |
# Hermes-AutoAI
