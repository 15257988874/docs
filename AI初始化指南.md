# AI 初始化指南

> AGENTS.md 生成 × AI 文档分层 × 工具配置
> 更新日期：2026-11-27　｜　标注 **✅ 实测** 的命令已在本机跑通。
> 脚手架与验证链的初始化见配套文档：[项目初始化指南](项目初始化指南.md)

---

## 0. 一页结论

| 目标 | 推荐路径 |
| --- | --- |
| 已有代码，最快生成 AGENTS.md | Codex 里执行 `/init` |
| 同时用 Claude Code | `/init` 生成后改名 + 软链接 |
| 不装任何工具 | 用 AI 对话 + 固定 Prompt |
| 全新空项目 | 直接套模板手写 |
| 规格驱动开发 | `npx @fission-ai/openspec@latest init` |
| 团队强规范 | Spec Kit（需能访问 GitHub） |
| 全流程角色化 | `npx bmad-method install` |

**核心原则**：AGENTS.md 的价值不在文笔，而在**可验证**——命令能直接跑、目录真实存在、禁区明确。生成只是起点，校验才是关键。

---

## 1. 快速生成 AGENTS.md

### 1.1 方式一：Codex `/init`（最直接）

Codex CLI 是少数**原生生成 `AGENTS.md`** 的工具，无需改名。

```bash
cd my-project
codex
# 进入交互界面后输入：
/init
```

执行后会扫描仓库结构、构建配置与已有约定，输出类似：

```text
• Explored repository structure
• Read package.json, tsconfig.json, .oxlintrc.json
• Read README.md
• Wrote AGENTS.md
```

生成物通常包含：项目概览、构建/测试命令、代码风格、目录说明。大型仓库还会在子目录放局部 `AGENTS.md`（就近生效）。

**验证是否成功**：

```bash
ls -l AGENTS.md
head -20 AGENTS.md
```

### 1.2 方式二：Claude Code `/init` + 改名

Claude Code 的 `/init` 只生成 `CLAUDE.md`，多一步即可共用：

```bash
cd my-project
claude
# 输入 /init，等待生成后退出

mv CLAUDE.md AGENTS.md
ln -s AGENTS.md CLAUDE.md          # macOS / Linux
# Windows：copy AGENTS.md CLAUDE.md，或 mklink /H CLAUDE.md AGENTS.md
```

效果：两个工具读同一份内容，后续只需维护 `AGENTS.md`。

### 1.3 方式三：AI 对话生成（不装工具）

把下面这段 Prompt 连同仓库一起交给任意编码模型：

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

### 1.4 方式四：手写模板（新项目）

还没有代码时，任何生成器都只能产出空话，直接套模板最快：

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

### 1.5 四种方式对比

| 方式 | 依赖 | 原生 AGENTS.md | 适合 |
| --- | --- | --- | --- |
| Codex `/init` | Codex CLI | ✅ 是 | 已有代码，最快 |
| Claude Code `/init` | Claude Code | ❌ 需改名 | 同时用 Claude Code |
| AI 对话 + Prompt | 任意模型 | ✅ 是 | 不想装工具 |
| 手写模板 | 无 | ✅ 是 | 全新空项目 |

### 1.6 完整示例：React + Vite 项目

以下是一份实际可用的输出样例（对应 `react-app` 项目）：

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
- 测试：暂无，补测前先不要声称"已验证"

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

要点：这份文件**每一行都能验证**——命令来自 `package.json`，目录真实存在，约定来自实际配置。

### 1.7 生成后必做校验

| 检查项 | 方法 | 不合格表现 |
| --- | --- | --- |
| 命令真实 | 逐条复制执行 | `missing script` / 命令不存在 |
| 目录真实 | `ls` 对照 | 写了不存在的目录 |
| 约定可执行 | 对照 lint / tsconfig | 只有"保持代码整洁"这类空话 |
| 体量合理 | `wc -l AGENTS.md` | 超过 200 行 |
| 三问可答 | 新会话只读该文件提问 | 答不出启动方式或禁区 |

三问测试：

1. 怎么启动开发服务？
2. 新增一个接口该放哪、要遵守什么约定？
3. 哪些文件不能碰？

三问全对才算合格；答不出来说明缺的是**命令**和**禁区**，不是文笔。

---

## 2. 五层模型

AI 层的本质是给编码代理提供**入口、知识、规格、能力、配置**五个层次，不是写一篇 README 就结束。

| 层 | 内容 | 位置示例 | 读取时机 | 体量控制 |
| --- | --- | --- | --- | --- |
| **L1 入口** | 项目定位、命令、约定、禁区 | `AGENTS.md` / `CLAUDE.md` | 每次会话必读 | < 200 行 |
| **L2 知识** | 架构、领域术语、ADR | `docs/ai/` | 按需检索 | 单篇 < 300 行 |
| **L3 规格** | 需求、变更提案、任务拆解 | `openspec/` / `.specify/` | 开发前 | 按变更组织 |
| **L4 能力** | 命令、技能、子代理、钩子 | `.claude/commands/`、`.claude/skills/` | 主动调用 | 一事一技能 |
| **L5 配置** | MCP、权限、忽略规则 | `.mcp.json`、`settings.json`、`.cursorignore` | 工具启动时 | 最小权限 |

> 判断标准：**L1 越短越好，L2 越准越好，L3 越可追溯越好，L4 越原子越好，L5 越收紧越好。**

---

## 3. 入口文件全景

| 文件 | 读取方 | 官方文档 |
| --- | --- | --- |
| `AGENTS.md` | Codex / Cursor / Claude Code / 多数代理 | [agents.md](https://agents.md) |
| `CLAUDE.md` | Claude Code | [docs.claude.com](https://docs.claude.com/en/docs/claude-code/memory) |
| `.cursor/rules/*.mdc` | Cursor | [cursor.com/docs](https://cursor.com/docs/context/rules) |
| `.github/copilot-instructions.md` | GitHub Copilot | [docs.github.com/copilot](https://docs.github.com/en/copilot) |
| `.windsurf/rules/`（旧版 `.windsurfrules`） | Windsurf | [windsurf.com/docs](https://windsurf.com/docs) |
| `.clinerules` | Cline | [docs.cline.bot](https://docs.cline.bot) |
| `.roorules` / `.roo/rules/` | Roo Code | [docs.roocode.com](https://docs.roocode.com) |
| `CONVENTIONS.md` + `.aider.conf.yml` | Aider | [aider.chat/docs](https://aider.chat/docs) |
| `GEMINI.md` | Gemini CLI | [gemini-cli](https://github.com/google-gemini/gemini-cli) |

**多工具共存的推荐做法**：只维护一份 `AGENTS.md`，其余入口用软链接：

```bash
touch AGENTS.md
ln -s AGENTS.md CLAUDE.md          # Claude Code
ln -s AGENTS.md GEMINI.md          # Gemini CLI
# Windows 无开发者模式时改用复制，或用 mklink /H
```

> 例外：Cursor 的 `.mdc` 支持 globs、alwaysApply 等元数据，需要按目录分规则时，不适合软链接，单独维护。

---

## 4. AI 初始化工具对比

| 工具 | 类型 | 命令 | 产出 | 交互性 | 适用 |
| --- | --- | --- | --- | --- | --- |
| **OpenSpec** 2.2.0 ✅ | 规格驱动 | `npx @fission-ai/openspec@latest init --tools claude,cursor,codex --scope project --no-animation` | `openspec/`、skills、各工具指令 | 可全非交互 | 新项目、变更可追溯 |
| **Spec Kit** | 规格驱动 + 宪法 | `uvx --from git+https://github.com/github/spec-kit.git specify init my-app --ai claude` | `.specify/`、项目宪法 | 半交互 | 团队强规范 |
| **BMAD-METHOD** 6.12.4 | 角色化工作流 | `npx bmad-method install` | `_bmad/` 角色与工作流 | 交互式 | 多角色协作 |
| **Aider** | 结对编程 | `aider`（读 `CONVENTIONS.md`） | 无脚手架 | 交互 | 终端内改代码 |
| **Continue** | IDE 插件 | 安装插件 + `.continue/config.yaml` | 自定义规则/模型 | 交互 | VS Code / JetBrains |
| **Cline / Roo Code** | IDE Agent | 安装插件 + `.clinerules` / `.roorules` | 规则文件 | 交互 | VS Code 内自主执行 |
| **GitHub Copilot** | IDE 补全 + Agent | 手写 `.github/copilot-instructions.md` | 指令与提示文件 | 手动 | GitHub 生态 |
| **Gemini CLI** | 终端 Agent | `gemini`（读 `GEMINI.md`） | 无脚手架 | 交互 | Google 生态 |

**选型建议**

| 场景 | 选择 |
| --- | --- |
| 个人 / 小项目 | Claude Code `/init` |
| 新项目、要"先想清楚再写" | OpenSpec |
| 团队要统一原则与评审门禁 | Spec Kit |
| 需要产品 / 架构 / 开发角色链 | BMAD |
| 已有项目、只想补规则 | 手写 `AGENTS.md` + 各工具入口 |

---

## 5. OpenSpec 详解（已实测）

```bash
cd my-app
npx --yes @fission-ai/openspec@latest init \
  --tools claude,cursor,codex \
  --scope project \
  --no-animation
```

| 参数 | 取值 | 作用 |
| --- | --- | --- |
| `--tools` | `all` / `none` / `claude,cursor,codex,...` | 生成哪些工具的接入文件，支持 80+ 个 ID |
| `--scope` | `global` / `project` | 技能装到用户级还是项目级（团队协作选 `project`） |
| `--language` | 如 `zh-CN` | 生成物语言 |
| `--profile` | `core` / `custom` | 工作流数量（core 精简，custom 全量） |
| `--no-animation` | — | 关闭欢迎动画，适配脚本 / CI |

生成后的工作流：`openspec-propose` → `openspec-apply-change` → `openspec-archive-change`，另有 explore / sync-specs / update-change / verify / onboard。

> 实测补充：OpenSpec **不会**生成 `AGENTS.md` 或 `CLAUDE.md`，只写 skills 与 `openspec/` 配置。需要入口文档时，按第 1 节的方法单独生成。

---

## 6. 各工具专属配置

**Claude Code**

| 路径 | 作用 |
| --- | --- |
| `CLAUDE.md` | 入口（可与 AGENTS.md 软链） |
| `.claude/commands/*.md` | 自定义斜杠命令，如 `/verify` |
| `.claude/skills/<name>/SKILL.md` | 技能，按需加载的完整流程 |
| `.claude/agents/*.md` | 子代理，隔离上下文执行专项任务 |
| `.claude/settings.json` | 权限、钩子（hooks）、环境变量 |
| `.mcp.json` | 项目级 MCP 服务配置 |

**Cursor**

| 路径 | 作用 |
| --- | --- |
| `.cursor/rules/*.mdc` | 规则，支持 globs 与 alwaysApply |
| `.cursor/mcp.json` | MCP 配置 |
| `.cursorignore` | 索引忽略（等同 .gitignore 语义） |

**GitHub Copilot**

| 路径 | 作用 |
| --- | --- |
| `.github/copilot-instructions.md` | 全局指令 |
| `.github/instructions/*.instructions.md` | 按文件类型/目录的局部指令 |
| `.github/prompts/*.prompt.md` | 可复用提示模板 |

---

## 7. MCP 与安全边界

**常用 MCP 服务**

| 服务 | 用途 | 典型配置方式 |
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
| Cursor | `.cursorignore` | 阻止 AI 索引敏感文件 |
| Aider | `.aiderignore` | 不进入上下文 |
| Copilot | 仓库内容排除设置 | 在 GitHub 后台配置 |
| Claude Code | `settings.json` 的 `permissions.deny` | 拒绝读取/执行指定路径与命令 |

底线：**`.env`、密钥、生产配置永不进入仓库，也不进入 AI 上下文**；能只读的 MCP 不要给写权限。

---

## 8. 初始化时序

```
① 官方脚手架 → ② 跑通 lint / build / test → ③ 生成 AGENTS.md（/init）
      → ④ 初始化规格体系（openspec init）→ ⑤ 配命令与技能（.claude/）
      → ⑥ 配 MCP 与忽略规则 → ⑦ 三问验收
```

顺序不能反：**先有能跑的验证，再让 AI 学规则**，否则 AI 会照着错误的状态生成文档。

---

## 9. 组合配方

### 配方 A：极简（约 3 分钟）

```bash
# 前提：项目已有可运行的验证链
claude        # 进入后执行 /init
mv CLAUDE.md AGENTS.md && ln -s AGENTS.md CLAUDE.md
```

### 配方 B：Codex 原生

```bash
codex
/init         # 直接产出 AGENTS.md
```

### 配方 C：前端正式项目

```bash
npm create vite@latest my-app -- --template react-ts
cd my-app && npm install
npm run lint && npm run build

npx @fission-ai/openspec@latest init --tools claude,cursor,codex --scope project --no-animation
codex        # 再执行 /init 补 AGENTS.md
```

### 配方 D：团队强规范

```bash
uvx --from git+https://github.com/github/spec-kit.git specify init my-app --ai claude
```

### 配方 E：全流程

```bash
npx bmad-method install
```

---

## 10. 验收清单

- [ ] `AGENTS.md` 存在，且不超过 200 行
- [ ] 文件中每条命令都能直接跑通
- [ ] 目录地图与仓库实际结构一致
- [ ] 编码约定能从 lint / tsconfig 配置中找到依据
- [ ] 禁区明确列出（产物、密钥、lockfile）
- [ ] AI 工具能实际读到规则（Cursor 需重启，Claude Code 需在仓库根启动）
- [ ] 三问测试通过（启动方式 / 新代码位置 / 禁止操作）
- [ ] 多工具入口指向同一份内容（软链接或复制已同步）

---

## 11. 常见坑

| 坑 | 现象 | 处理 |
| --- | --- | --- |
| 生成器编造命令 | 文件里的 script 不存在 | 逐条执行校验，删掉不存在的 |
| 空话过多 | "请写高质量代码" | 换成可验证的约束 |
| 入口文件越写越长 | 超过 200 行后 AI 开始忽略细节 | 拆分到 `docs/ai/`，入口只留索引 |
| 多工具规则各写一份 | 内容漂移、互相矛盾 | 只维护 `AGENTS.md`，其余软链接 |
| OpenSpec 没生成 AGENTS.md | Codex 读不到项目说明 | 按第 1 节单独生成 |
| `ln -s` 失败 | Windows 无开发者模式 | 复制文件，或用 `mklink /H` |
| 规则不生效 | AI 仍然乱写 | 确认从仓库根启动，Cursor 重启 IDE |
| MCP 权限过大 | AI 能删库、能推主干 | 只读优先，写操作加确认 |
| 文档长期不更新 | 三个月后完全失真 | 改命令/目录时同 PR 更新 |

---

## 12. 官网速查

| 分类 | 地址 |
| --- | --- |
| AGENTS.md 标准 | [agents.md](https://agents.md) |
| Claude Code 文档 | [docs.claude.com](https://docs.claude.com/en/docs/claude-code) |
| Cursor 文档 | [cursor.com/docs](https://cursor.com/docs) |
| GitHub Copilot 文档 | [docs.github.com/copilot](https://docs.github.com/en/copilot) |
| OpenSpec | [github.com/Fission-AI/OpenSpec](https://github.com/Fission-AI/OpenSpec) |
| Spec Kit | [github.com/github/spec-kit](https://github.com/github/spec-kit) |
| BMAD-METHOD | [github.com/bmad-code-org/BMAD-METHOD](https://github.com/bmad-code-org/BMAD-METHOD) |
| Aider | [aider.chat](https://aider.chat) |
| Continue | [continue.dev](https://continue.dev) |
| Roo Code | [roocode.com](https://roocode.com) |
| Gemini CLI | [gemini-cli](https://github.com/google-gemini/gemini-cli) |

> 注：`cline.bot`、`windsurf.com` 在本次网络环境下未能连通，链接按官方文档记录，实际以官方为准。

---

## 13. 一分钟速查

```bash
# 生成 AGENTS.md
codex            → /init                # 原生生成
claude           → /init && mv CLAUDE.md AGENTS.md   # 改名共用

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
