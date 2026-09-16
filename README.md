# Task0 学习记录 — Chapter 1 · AI Agent 入门

《深入理解 AI Agent：设计原理与工程实践》第 1 章学习与实验 1-1（上下文消融）记录。

## 1. 环境配置

| 项 | 说明 |
|---|---|
| 系统 | Linux (Ubuntu) |
| Python | conda 环境 `myenv`，Python 3.12（仓库要求 3.11–3.13） |
| 依赖 | `pip install -e ".[ch1]"`（uv 未安装，走 pip；另补装 `pytest`） |
| 实验仓库 | [bojieli/ai-agent-book](https://github.com/bojieli/ai-agent-book) → `chapter1/context` |
| LLM Provider | DeepSeek 官方 API（`--provider deepseek`，默认模型 `deepseek-v4-flash`） |

> 凭据通过 `.env` / 环境变量注入，**未包含在本仓库中**。

复现命令：

```bash
cd chapter1/context
python tests/manual/check_deepseek_quick.py                    # 连通性自检
python main.py --provider deepseek --mode single --context-mode full --task "..."
python main.py --provider deepseek --mode interactive          # 输入 samples → sample 1
python main.py --provider deepseek --mode ablation --cases 3 --output my_ablation.json
```

## 2. 实验 1-1 结果摘要（DeepSeek V4 Flash，3 个任务 × 5 种上下文模式）

| 上下文模式 | 结果 | 迭代/工具调用 | 现象 |
|---|---|---|---|
| `full`（完整基线） | ✓ 正常 | 3it / 4–6tc | 正确调用工具并给出最终答案 |
| `no_history`（去掉历史） | ✗ 无最终答案 | 10it / 30–45tc | 重复操作、丢失任务进度，耗尽迭代预算 |
| `no_reasoning`（去掉思考过程） | ✓ 正常 | 3it / 4–6tc | 与基线几乎无差别——"为什么"可从工具结果重建 |
| `no_tool_calls`（去掉工具定义） | ⚠ 编造数字 | 1it / 0tc | 无法调用工具，直接给出无观测支撑的汇率与结果 |
| `no_tool_results`（去掉工具结果） | ⚠ 编造数字 | 7–9it / 11–16tc | 反复重试后仍给出无观测支撑的答案 |

结论：工具定义与工具结果决定"行动"与"闭环"，缺失后失败形式不是报错，而是**看起来毫无破绽的编造答案**；历史记录防止重复操作；思考过程在可重建时并非不可替代。

## 3. 目录结构

```
.
├── README.md
├── notes/Task0笔记.md          # 个人学习笔记
├── screenshots/                 # 实验截图（终端实测）
│   ├── 01_env_check.png         # ① 环境/API 连通性检查
│   ├── 02_react_toolcalls.png   # ② ReAct 轨迹：工具调用
│   ├── 03_final_answer.png      # ③ 最终答案
│   └── 04_ablation_matrix.png   # ④ 消融实验对比矩阵
├── results/my_ablation.json     # 消融实验原始结果（15 条）
└── logs/
    ├── 01_check_deepseek.log    # 环境/API 连通性
    ├── 02_single_full.log       # 单任务完整 ReAct 轨迹
    ├── 03_ablation.log          # 消融实验对比表
    └── 04_interactive_sample1.log  # 交互模式 sample 1
```

## 4. 结论与心得

见 [notes/Task0笔记.md](notes/Task0笔记.md)。
