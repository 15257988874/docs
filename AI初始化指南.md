# AI 初始化指南

> AGENTS.md 生成、AI 文档分层与工具配置
> 更新：2026-11-27　·　配套文档：[项目初始化指南](项目初始化指南.md)

**本文内容**

- 四种方式快速生成 AGENTS.md（含完整示例）
- AI 文档的五层组织模型
- 主流工具的入口文件与配置方式

---

## 1. 选型概览

| 目标 | 推荐路径 |
| --- | --- |
| 已有代码，最快生成 | Codex 里执行 `/init` |
| 同时使用 Claude Code | `/init` 后改名 + 软链接 |
| 不想安装工具 | AI 对话 + 固定 Prompt |
| 全新空项目 | 直接套模板手写 |
| 规格驱动开发 | `npx @fission-ai/openspec@latest init` |
| 团队强规范 | Spec Kit（需能访问 GitHub） |
| 全流程角色化 | `npx bmad-method install` |

> **核心原则**：AGENTS.md 的价值不在文笔，而在**可验证**——命令能直接跑、目录真实存在、禁区明确。生成只是起点，校验才是关键。

---

## 2. 快速生成 AGENTS.md

### 2.1 方式一：Codex `/init`

Codex CLI 是少数**原生生成 `AGENTS.md`** 的工具，无需改名。

```bash
cd my-project
codex
```

进入交互界面后执行：

```text
/init
```

生成过程通常如下：

```text
• Explored repository structure
• Read package.json, tsconfig.json, .oxlintrc.json
• Read README.md
• Wrote AGENTS.md
```

产出包含项目概览、构建与测试命令、代码风格、目录说明。大型仓库还会在子目录放置局部 `AGENTS.md`，就近生效。

**确认结果**

```bash
ls -l AGENTS.md
head -20 AGENTS.md
```

### 2.2 方式二：Claude Code `/init`

Claude Code 只生成 `CLAUDE.md`，多一步即可与 `AGENTS.md` 共用。

```bash
cd my-project
claude
```

进入后执行 `/init`，等待生成完成并退出，然后：

```bash
mv CLAUDE.md AGENTS.md
ln -s AGENTS.md CLAUDE.md
```

> Windows 无开发者模式时，改为复制文件：`copy AGENTS.md CLAUDE.md`，或使用 `mklink /H`。

此后只需维护 `AGENTS.md`，Claude Code 通过软链接读取。

### 2.3 方式三：AI 对话生成

不安装任何工具时，把下面这段 Prompt 连仓库一起交给任意编码模型：

```text
请阅读当前仓库，生成一份 AGENTS.md，只写六块：

1. 技术栈：运行时版本、框架、包管理器——从 package.json /
   pyproject.toml / go.mod 等配置读取，不要猜。
2. 常用命令：安装 / 开发 / 构建 / 测试 / 检查，必须来自真实配置，
   script 不存在就写"暂无"。
3. 目录地图：只列真实存在且有源码的目录，每个目录一行职责。
4. 编码约定：从 lint、tsconfig、格式化配置反推，禁止写
   "写高质量代码"这类空话。
5. 禁区：生成产物、密钥、lockfile 主版本、不要改的目录。
6. 完成定义：一次改动怎样才算完成。

约束：
- 全文不超过 150 行
- 每条都可被验证
- 不确定的地方标注"待确认"，不要编造
```

### 2.4 方式四：手写模板

还没有代码时，任何生成器都只能产出空话，直接套用模板：

```markdown
# 项目名 —— 一句话定位

## 技术栈
- Node 22 / pnpm / React 19

## 常用命令
- 安装：pnpm install
- 开发：pnpm dev
- 验证：pnpm lint && pnpm build && pnpm test

## 目录地图
- src/pages/    页面
- src/api/      接口封装（统一走 request.ts）

## 编码约定
- 只用函数组件 + Hooks
- 新接口必须补类型，禁止 any

## 禁区
- 不改 dist/、generated/
- 不升级主版本依赖
- 不提交 .env

## 完成定义
验证全绿 + 相关文档同步
```

### 2.5 四种方式对比

| 方式 | 依赖 | 原生 AGENTS.md | 适合 |
| --- | --- | --- | --- |
| Codex `/init` | Codex CLI | 是 | 已有代码，最快 |
| Claude Code `/init` | Claude Code | 否，需改名 | 同时使用 Claude Code |
| AI 对话 + Prompt | 任意模型 | 是 | 不想安装工具 |
| 手写模板 | 无 | 是 | 全新空项目 |

### 2.6 完整示例：React + Vite 项目

以下是一份可直接使用的输出样例：

```markdown
# react-app —— 基于 Vite + React 的前端应用

## 技术栈
- 运行时：Node.js 22（要求 >= 20）
- 框架：React 19 + TypeScript 5
- 构建：Vite 7
- 静态检查：oxlint（注意：不是 ESLint）
- 包管理器：npm（以 package-lock.json 为准）

## 常用命令
- 安装依赖：npm install
- 启动开发：npm run dev
- 类型检查 + 构建：npm run build（内部执行 tsc -b && vite build）
- 静态检查：npm run lint
- 预览产物：npm run preview
- 测试：暂无，补测前不要声称"已验证"

## 目录地图
- src/components/  通用组件
- src/hooks/       自定义 Hooks
- src/assets/      静态资源
- src/main.tsx     应用入口
- src/App.tsx      根组件
- public/          不参与构建的静态文件

## 编码约定
- 只用函数组件 + Hooks，禁止 class 组件
- 组件文件用 PascalCase.tsx 命名
- Props 必须用 interface 显式声明，禁止 any
- 样式优先 CSS Modules，不写内联 style

## 禁区
- 不修改 dist/、node_modules/
- 不手动改 package-lock.json 的间接依赖版本
- 不提交 .env / .env.local
- 不改 src/assets 下的设计稿原图

## 完成定义
npm run lint && npm run build 全绿；涉及 UI 的改动需附说明或截图
```

> 💡 这份文件的每一行都能验证：命令来自 `package.json`，目录真实存在，约定来自实际配置。

### 2.7 生成后校验

| 检查项 | 方法 | 不合格表现 |
| --- | --- | --- |
| 命令真实 | 逐条复制执行 | `missing script` 或命令不存在 |
| 目录真实 | 用 `ls` 对照 | 写了不存在的目录 |
| 约定可执行 | 对照 lint / tsconfig | 只有"保持代码整洁"类空话 |
| 体量合理 | `wc -l AGENTS.md` | 超过 200 行 |
| 三问可答 | 新会话只读该文件提问 | 答不出启动方式或禁区 |

**三问测试**

1. 怎么启动开发服务？
2. 新增一个接口该放哪、要遵守什么约定？
3. 哪些文件不能碰？

三问全对才算合格；答不出来说明缺的是**命令**和**禁区**，不是文笔。

---

## 3. AI 文档五层模型

AI 层的本质是给编码代理提供**入口、知识、规格、能力、配置**五个层次，不是写一篇 README 就结束。

| 层 | 内容 | 位置示例 | 读取时机 | 体量控制 |
| --- | --- | --- | --- | --- |
| **L1 入口** | 定位、命令、约定、禁区 | `AGENTS.md` / `CLAUDE.md` | 每次会话 | 少于 200 行 |
| **L2 知识** | 架构、术语、ADR | `docs/ai/` | 按需检索 | 单篇少于 300 行 |
| **L3 规格** | 需求、变更、任务 | `openspec/` / `.specify/` | 开发前 | 按变更组织 |
| **L4 能力** | 命令、技能、子代理 | `.claude/commands/`、`.claude/skills/` | 主动调用 | 一事一技能 |
| **L5 配置** | MCP、权限、忽略 | `.mcp.json`、`settings.json` | 工具启动 | 最小权限 |

> 判断标准：**L1 越短越好，L2 越准越好，L3 越可追溯越好，L4 越原子越好，L5 越收紧越好。**

---

## 4. 入口文件全景

| 文件 | 读取方 | 官方文档 |
| --- | --- | --- |
| `AGENTS.md` | Codex / Cursor / Claude Code / 多数代理 | [agents.md](https://agents.md) |
| `CLAUDE.md` | Claude Code | [docs.claude.com](https://docs.claude.com/en/docs/claude-code/memory) |
| `.cursor/rules/*.mdc` | Cursor | [cursor.com/docs](https://cursor.com/docs/context/rules) |
| `.github/copilot-instructions.md` | GitHub Copilot | [docs.github.com](https://docs.github.com/en/copilot) |
| `.windsurf/rules/` | Windsurf | [windsurf.com](https://windsurf.com/docs) |
| `.clinerules` | Cline | [docs.cline.bot](https://docs.cline.bot) |
| `.roorules` / `.roo/rules/` | Roo Code | [docs.roocode.com](https://docs.roocode.com) |
| `CONVENTIONS.md` | Aider | [aider.chat](https://aider.chat/docs) |
| `GEMINI.md` | Gemini CLI | [gemini-cli](https://github.com/google-gemini/gemini-cli) |

**多工具共存**：只维护一份 `AGENTS.md`，其余入口软链接过去。

```bash
touch AGENTS.md
ln -s AGENTS.md CLAUDE.md          # Claude Code
ln -s AGENTS.md GEMINI.md          # Gemini CLI
```

> ⚠️ Cursor 的 `.mdc` 支持 globs、alwaysApply 等元数据，需要按目录分规则时不适合软链接，应单独维护。

---

## 5. 工具对比

| 工具 | 类型 | 命令 | 产出 | 交互性 |
| --- | --- | --- | --- | --- |
| **OpenSpec** 2.2.0 ✅ | 规格驱动 | `npx @fission-ai/openspec@latest init` | `openspec/`、skills、工具指令 | 可全非交互 |
| **Spec Kit** | 规格 + 宪法 | `uvx --from git+https://github.com/github/spec-kit.git specify init` | `.specify/`、项目宪法 | 半交互 |
| **BMAD-METHOD** 6.12.4 | 角色化工作流 | `npx bmad-method install` | `_bmad/` 角色与工作流 | 交互式 |
| **Aider** | 结对编程 | `aider` | 约定文件 | 交互 |
| **Continue** | IDE 插件 | 安装插件 + `config.yaml` | 自定义规则 | 交互 |
| **Cline / Roo Code** | IDE Agent | 安装插件 + 规则文件 | `.clinerules` / `.roorules` | 交互 |
| **GitHub Copilot** | IDE + Agent | 手写指令文件 | `copilot-instructions.md` | 手动 |
| **Gemini CLI** | 终端 Agent | `gemini` | `GEMINI.md` | 交互 |

**选型建议**

| 场景 | 选择 |
| --- | --- |
| 个人 / 小项目 | Claude Code `/init` |
| 新项目、先想清楚再写 | OpenSpec |
| 团队统一原则与评审 | Spec Kit |
| 产品 / 架构 / 开发角色链 | BMAD |
| 已有项目，只补规则 | 手写 `AGENTS.md` |

---

## 6. OpenSpec 详解

```bash
cd my-app
npx --yes @fission-ai/openspec@latest init \
  --tools claude,cursor,codex \
  --scope project \
  --no-animation
```

| 参数 | 取值 | 作用 |
| --- | --- | --- |
| `--tools` | `all` / `none` / 逗号分隔 ID | 生成哪些工具的接入文件 |
| `--scope` | `global` / `project` | 技能装到用户级还是项目级 |
| `--language` | 如 `zh-CN` | 生成物语言 |
| `--profile` | `core` / `custom` | 工作流数量 |
| `--no-animation` | — | 关闭动画，适配脚本与 CI |

生成后的工作流：

```text
openspec-propose → openspec-apply-change → openspec-archive-change
```

另有 explore / sync-specs / update-change / verify / onboard。

> ⚠️ OpenSpec **不会**生成 `AGENTS.md` 或 `CLAUDE.md`，只写 skills 与 `openspec/` 配置。入口文档需按第 2 节单独生成。

---

## 7. 工具专属配置

### 7.1 Claude Code

| 路径 | 作用 |
| --- | --- |
| `CLAUDE.md` | 入口，可与 AGENTS.md 软链 |
| `.claude/commands/*.md` | 自定义斜杠命令 |
| `.claude/skills/<name>/SKILL.md` | 技能，按需加载 |
| `.claude/agents/*.md` | 子代理，隔离上下文 |
| `.claude/settings.json` | 权限、钩子、环境变量 |
| `.mcp.json` | 项目级 MCP 配置 |

### 7.2 Cursor

| 路径 | 作用 |
| --- | --- |
| `.cursor/rules/*.mdc` | 规则，支持 globs 与 alwaysApply |
| `.cursor/mcp.json` | MCP 配置 |
| `.cursorignore` | 索引忽略 |

### 7.3 GitHub Copilot

| 路径 | 作用 |
| --- | --- |
| `.github/copilot-instructions.md` | 全局指令 |
| `.github/instructions/*.instructions.md` | 按目录或文件类型的局部指令 |
| `.github/prompts/*.prompt.md` | 可复用提示模板 |

---

## 8. MCP 与安全边界

**常用 MCP 服务**

| 服务 | 用途 | 配置方式 |
| --- | --- | --- |
| filesystem | 受控目录读写 | `npx -y @modelcontextprotocol/server-filesystem <path>` |
| github | 仓库、Issue、PR | `gh` 认证或 Token |
| playwright | 浏览器自动化 | `npx -y @playwright/mcp` |
| postgres / sqlite | 只读查库 | 连接串放环境变量 |

Claude Code 添加示例：

```bash
claude mcp add github -- npx -y @modelcontextprotocol/server-github
```

**忽略与安全**

| 工具 | 忽略文件 | 说明 |
| --- | --- | --- |
| Cursor | `.cursorignore` | 阻止索引敏感文件 |
| Aider | `.aiderignore` | 不进入上下文 |
| Copilot | 仓库内容排除设置 | 在 GitHub 后台配置 |
| Claude Code | `permissions.deny` | 拒绝指定路径与命令 |

> **底线**：`.env`、密钥、生产配置永不进入仓库，也不进入 AI 上下文；能只读的 MCP 不要给写权限。

---

## 9. 初始化时序

```text
① 官方脚手架
② 跑通 lint / build / test
③ 生成 AGENTS.md（/init）
④ 初始化规格体系（openspec init）
⑤ 配置命令与技能（.claude/）
⑥ 配置 MCP 与忽略规则
⑦ 三问验收
```

顺序不能反：**先有能跑的验证，再让 AI 学规则**，否则 AI 会照着错误的状态生成文档。

---

## 10. 组合配方

### 10.1 极简

```bash
# 前提：项目已有可运行的验证链
claude        # 执行 /init
mv CLAUDE.md AGENTS.md && ln -s AGENTS.md CLAUDE.md
```

### 10.2 Codex 原生

```bash
codex
/init         # 直接产出 AGENTS.md
```

### 10.3 前端正式项目

```bash
npm create vite@latest my-app -- --template react-ts
cd my-app && npm install
npm run lint && npm run build

npx @fission-ai/openspec@latest init --tools claude,cursor,codex --scope project --no-animation
codex        # 再执行 /init 补 AGENTS.md
```

### 10.4 团队强规范

```bash
uvx --from git+https://github.com/github/spec-kit.git specify init my-app --ai claude
```

### 10.5 全流程

```bash
npx bmad-method install
```

---

## 11. 验收清单

- [ ] `AGENTS.md` 存在，且不超过 200 行
- [ ] 文件中每条命令都能直接跑通
- [ ] 目录地图与仓库实际结构一致
- [ ] 编码约定能从 lint / tsconfig 中找到依据
- [ ] 禁区明确列出（产物、密钥、lockfile）
- [ ] AI 工具能实际读到规则（从仓库根启动）
- [ ] 三问测试通过
- [ ] 多工具入口指向同一份内容

---

## 12. 常见坑

| 坑 | 现象 | 处理 |
| --- | --- | --- |
| 生成器编造命令 | 文件里的 script 不存在 | 逐条执行校验，删掉不存在的 |
| 空话过多 | "请写高质量代码" | 换成可验证的约束 |
| 入口文件过长 | 超过 200 行后 AI 忽略细节 | 拆分到 `docs/ai/`，入口只留索引 |
| 多工具规则各写一份 | 内容漂移、相互矛盾 | 只维护 `AGENTS.md`，其余软链接 |
| OpenSpec 没生成 AGENTS.md | Codex 读不到项目说明 | 按第 2 节单独生成 |
| 软链接失败 | Windows 无开发者模式 | 复制文件，或用 `mklink /H` |
| 规则不生效 | AI 仍然乱写 | 从仓库根启动，重启 IDE |
| MCP 权限过大 | AI 能删库、能推主干 | 只读优先，写操作加确认 |
| 文档长期不更新 | 三个月后完全失真 | 改命令或目录时同步更新 |

---

## 13. 速查

### 13.1 官网

| 分类 | 地址 |
| --- | --- |
| AGENTS.md 标准 | [agents.md](https://agents.md) |
| Claude Code | [docs.claude.com](https://docs.claude.com/en/docs/claude-code) |
| Cursor | [cursor.com/docs](https://cursor.com/docs) |
| GitHub Copilot | [docs.github.com/copilot](https://docs.github.com/en/copilot) |
| OpenSpec | [github.com/Fission-AI/OpenSpec](https://github.com/Fission-AI/OpenSpec) |
| Spec Kit | [github.com/github/spec-kit](https://github.com/github/spec-kit) |
| BMAD-METHOD | [github.com/bmad-code-org/BMAD-METHOD](https://github.com/bmad-code-org/BMAD-METHOD) |
| Aider | [aider.chat](https://aider.chat) |
| Continue | [continue.dev](https://continue.dev) |
| Roo Code | [roocode.com](https://roocode.com) |
| Gemini CLI | [gemini-cli](https://github.com/google-gemini/gemini-cli) |

> 注：`cline.bot`、`windsurf.com` 在编写时未能连通，链接按官方文档记录。

### 13.2 常用命令

```bash
# 生成 AGENTS.md
codex            → /init
claude           → /init && mv CLAUDE.md AGENTS.md

# 规格体系
npx @fission-ai/openspec@latest init --tools claude,cursor,codex --scope project
npx bmad-method install

# 多工具共用
ln -s AGENTS.md CLAUDE.md
ln -s AGENTS.md GEMINI.md

# 校验
wc -l AGENTS.md
# 逐条复制 AGENTS.md 中的命令执行一遍
```
