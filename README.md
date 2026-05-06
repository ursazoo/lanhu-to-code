# fshows-frontend-skills

> 蓝湖设计稿转前端代码的 Claude Code Skills 工具集。给一个蓝湖链接，自动生成符合项目规范的完整框架代码。

## 它能做什么

给 Claude Code 一个蓝湖页面链接，它会：

1. **自动读取设计稿**（通过蓝湖 MCP）——获取 HTML 结构、样式数据、切图
2. **映射到项目现有组件**——查阅 `docs/components.md`，优先复用，不重复造轮子
3. **生成完整框架代码**——Vue / React / 其他，以项目 README 为准
4. **标出所有待确认项**——API 路径、枚举、不确定的逻辑，全部标 `// TODO:`
5. **可选 code review**——生成后问你要不要跑 code-review-expert

**不会做：** 猜测 API 路径、硬编码设计 token 值（颜色/间距）、省略代码

---

## Skills 说明

| Skill | 触发方式 | 说明 |
|-------|---------|------|
| **design-to-code** | `/design-to-code` | 入口 skill，三阶段编排（获取 → 生成 → 审查） |
| **code-gen** | 由 design-to-code 自动调用 | 从蓝湖 MCP 获取设计稿 HTML/CSS/切图，以隔离 subagent 运行（`context: fork`） |
| **code-format** | 由 design-to-code 自动调用 | 将蓝湖产物转为项目框架代码，支持新建和改造两种模式 |
| **component-doc-gen** | `/component-doc-gen` 或 CI 定时触发 | 扫描 `src/` 自动生成组件文档 `docs/components.md` |

---

## 架构

```
/design-to-code（入口 skill）
    │
    ├── 前置检查：check-prerequisites.sh
    │   ├── README.md / CLAUDE.md / package.json → 确定技术栈
    │   ├── docs/tech-spec.md → 判断新建 vs 改造
    │   ├── docs/components.md → 组件复用依据
    │   └── docs/dev-spec.md → 编码规范
    │
    ├── 阶段 1：code-gen（context: fork 隔离）
    │   ├── 蓝湖 MCP → 获取 HTML+CSS+切图
    │   ├── 保存到 .claude/lanhu-output/<页面名>/
    │   └── 返回路径 + 元素摘要（不污染主会话上下文）
    │
    ├── 阶段 2：code-format
    │   ├── 读取项目文档（components.md / tech-spec.md / dev-spec.md）
    │   ├── CSS 值对照表（强制检查点，需用户确认）
    │   ├── 组件映射（优先复用现有组件）
    │   └── 生成完整框架代码
    │
    └── 阶段 3：code-review（用户确认后）
        └── /code-review-expert
```

### 为什么 code-gen 要隔离？

蓝湖原始 HTML 数据量很大。如果直接在主会话读取，大量无关内容会稀释后续读取业务文档（components.md、tech-spec.md）的效果。code-gen 通过 `context: fork` 以隔离 subagent 运行，只把"路径 + 元素摘要"返回给主流程。

---

## 快速开始

### 前提

- 已安装 [Claude Code](https://claude.ai/code)（`claude` 命令可用）
- 已安装 Python 3.11+（用于蓝湖 MCP）
- 有蓝湖账号，能登录 lanhuapp.com

### 1. 安装蓝湖 MCP

```bash
git clone https://github.com/dsphber/lanhu-mcp ~/.claude/mcp-servers/lanhu-mcp
cd ~/.claude/mcp-servers/lanhu-mcp
bash easy-install.sh
```

脚本会自动完成：创建 Python 环境 → 安装依赖 → 引导你获取并填入蓝湖 Cookie。

```bash
claude mcp add lanhu --transport http http://localhost:8000/mcp -s user
```

验证：`curl http://localhost:8000/mcp` 返回 JSON 则成功。

> 每次重启电脑后需要重新启动 MCP 服务：`bash ~/.claude/mcp-servers/lanhu-mcp/start.sh &`

### 2. 安装 Skills

```bash
git clone https://github.com/ursazoo/fshows-frontend-skills ~/fshows-frontend-skills
bash ~/fshows-frontend-skills/install.sh
```

验证：`ls ~/.claude/skills/` 应能看到 `design-to-code`、`code-gen`、`code-format`、`component-doc-gen`。

### 3. 配置项目

在你的前端项目根目录执行：

```bash
# 复制 CLAUDE.md 模板，按项目实际情况填写技术栈、UI 库、CSS 方案
cp ~/.claude/skills/design-to-code/templates/project-CLAUDE.md ./CLAUDE.md

# 复制 PostToolUse Hook（可选，写入代码后自动运行 ESLint）
mkdir -p .claude
cp ~/.claude/skills/design-to-code/templates/project-dot-claude-settings.json .claude/settings.json
```

### 4. 准备 docs/ 目录

文档越齐全，生成质量越高：

| 文档 | 作用 | 缺失时的影响 |
|------|------|-------------|
| `docs/components.md` | 可复用组件目录（props/用法） | 会生成重复的组件 |
| `docs/tech-spec.md` | API 接口、枚举、交互逻辑 | 只能生成静态视图，数据逻辑全标 TODO |
| `docs/dev-spec.md` | 命名规范、文件结构、CSS Token 映射 | 可能不符合团队编码风格 |

其中 `docs/components.md` 可以由 component-doc-gen 自动生成：

```bash
claude -p "运行 component-doc-gen skill"
```

`docs/tech-spec.md` 和 `docs/dev-spec.md` 需要团队手动维护。参考格式见 [QUICKSTART.md](./QUICKSTART.md)。

### 5. 使用

```bash
cd your-project
claude
/design-to-code
```

粘贴蓝湖页面 URL，三阶段流程自动执行。也支持只传项目级 URL，会展示设计图列表让你选择。

---

## 输出示例

```
[前置检查] 通过
[意图判断] 新建页面
阶段 1/3：正在从蓝湖获取设计稿数据...
✓ 阶段 1 完成 → 阶段 2/3：正在生成框架代码...
✓ 阶段 2 完成 → 是否进行 code review？(y/n)

✓ 生成文件：src/views/order/index.vue
✓ 复用组件：SearchInput, StatusTag, OrderCard
⚠ 新增组件：DateRangePicker（需 review）
⚠ TODO 项：
  - GET /api/orders 接口参数（需确认分页方式）
  - 订单状态枚举（docs/tech-spec.md 未定义）
```

---

## 最常见失败原因

**硬编码设计 token 值**（最高频）——蓝湖给出颜色 `#7c3aed`，直接写进代码，但项目实际有 CSS 变量 `var(--color-primary)`。

防范：在 `docs/dev-spec.md` 中维护 [Token 映射表](./QUICKSTART.md)，`docs/components.md` 保持最新。

---

## 常见问题

**Q: 触发 `/design-to-code` 没有反应？**

确认 skill 文件在正确位置：`~/.claude/skills/design-to-code/SKILL.md`

**Q: 提示"蓝湖 MCP 未配置"？**

确认 MCP 服务在运行（`curl http://localhost:8000/mcp` 有返回），且已注册：`claude mcp list` 应看到 `lanhu`。

**Q: 生成的代码没有复用现有组件？**

检查 `docs/components.md` 是否存在且包含对应组件。运行 component-doc-gen 重新生成。

**Q: 颜色/间距与设计稿不一致？**

在 `docs/dev-spec.md` 中补充 CSS token 映射表，Claude Code 下次生成时会参考。

---

## 仓库结构

```
fshows-frontend-skills/
├── README.md                          # 本文件
├── QUICKSTART.md                      # 详细安装指南（含文档示例）
├── install.sh                         # 一键安装脚本
└── skills/
    ├── design-to-code/                # 入口 skill
    │   ├── SKILL.md                   # 主流程编排
    │   ├── workflow-guide.md          # 完整流程图和失败模式
    │   ├── scripts/
    │   │   └── check-prerequisites.sh # 前置条件检查
    │   └── templates/
    │       ├── project-CLAUDE.md      # 项目 CLAUDE.md 模板
    │       └── project-dot-claude-settings.json  # ESLint Hook 模板
    ├── code-gen/                      # 蓝湖数据获取（subagent 隔离）
    │   └── SKILL.md
    ├── code-format/                   # 框架代码生成
    │   ├── SKILL.md
    │   ├── failure-patterns.md        # 常见失败案例
    │   └── fallback-guide.md          # 文档缺失降级策略
    └── component-doc-gen/             # 组件文档自动生成
        ├── SKILL.md
        ├── output-format.md           # 输出格式规范
        ├── ci-template.yml            # CI Pipeline 模板
        └── scripts/
            └── detect-components.sh   # 组件扫描脚本
```

---

> 文档和 skill 有问题？在 [issues](https://github.com/ursazoo/fshows-frontend-skills/issues) 中反馈。
