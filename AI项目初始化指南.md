# AI 项目初始化指南

> 脚手架选型 × 验证链 × AI 文档初始化
> 更新日期：2026-11-27　｜　标注 **✅ 实测** 的命令已在本机跑通，其余为官方标准命令；版本迭代较快，执行前建议先看 `--help`。

---

## 0. 一页结论

| 目标 | 推荐路径 |
| --- | --- |
| 只要快 | 任意官方脚手架 → `claude` 里执行 `/init` |
| 要规格驱动（推荐） | 脚手架 → `npx @fission-ai/openspec@latest init --tools claude,cursor,codex` |
| 团队强规范 | 脚手架 → Spec Kit（需能访问 GitHub） |
| 全流程角色化 | 脚手架 → `npx bmad-method install` |
| 验证链最完整 | NestJS / AdonisJS / Go / Rust / Spring Boot（开箱自带 lint + 测试） |

**没有单一脚手架同时自带「验证 + AI 文档」**，必须两段式：先官方脚手架解决验证，再 AI 工具解决文档。

---

## 1. 技术栈初始化

### 1.1 前端 Web

| 框架 | 初始化命令 | 自带验证 | 官网 |
| --- | --- | --- | --- |
| React（Vite） | `npm create vite@latest my-app -- --template react-ts` ✅ | **oxlint** + `tsc -b`（build 时执行） | https://vite.dev |
| Next.js | `npx create-next-app@latest my-app` | ESLint + TypeScript | https://nextjs.org |
| React Router 7 | `npx create-react-router@latest my-app` | TypeScript + 内置测试模板 | https://reactrouter.com |
| Remix | `npx create-remix@latest my-app` | ESLint + TypeScript | https://remix.run |
| TanStack Start | `npm create @tanstack/start@latest` | TypeScript | https://tanstack.com/start |
| Vue | `npm create vue@latest my-app` | 可勾选 ESLint / Prettier / **Vitest** / E2E | https://vuejs.org |
| Nuxt | `npm create nuxt@latest my-app` | ESLint / Vitest 按提示勾选 | https://nuxt.com |
| Angular | `npx @angular/cli@latest new my-app` | **ESLint + Karma/Jasmine 全套** | https://angular.dev |
| Svelte / SvelteKit | `npx sv create my-app` | 可勾选 ESLint / Prettier / Vitest / Playwright | https://svelte.dev |
| Solid / SolidStart | `npm create solid@latest my-app` | TypeScript | https://www.solidjs.com |
| Astro | `npm create astro@latest` | TypeScript 严格模式 | https://astro.build |
| Qwik | `npm create qwik@latest` | TypeScript + ESLint 可选 | https://qwik.dev |
| Ember | `npx ember-cli new my-app` | **自带 QUnit 测试** | https://emberjs.com |

> ⚠️ 实测：Vite 7 的 React 模板用 **oxlint**，不是 ESLint；`package.json` 只有 `lint / build / dev / preview`，**没有 test**。

---

### 1.2 后端（按语言分类）

#### 1.2.1 Node.js

Node 生态没有统一的"官方框架"，按定位分四类：极简路由、全栈 MVC、企业级、运行时专用。

| 框架 | 定位 | 初始化命令 | 自带验证 | 官网 |
| --- | --- | --- | --- | --- |
| **Express** | 极简、生态最大 | `npx express-generator my-app` | 无（需自配 eslint + vitest） | https://expressjs.com |
| **Koa** | 轻量洋葱模型 | `npx koa-generator my-app` | 无 | https://koajs.com |
| **Fastify** | 高性能、Schema 校验 | `npm create fastify@latest my-app` | 按选项勾选测试 | https://fastify.dev |
| **Hono** | 边缘运行时、超轻量 | `npm create hono@latest my-app` | TypeScript | https://hono.dev |
| **NestJS** | 企业级、装饰器、模块化 | `npx @nestjs/cli@latest new my-app` | **Jest + ESLint + Prettier 全套** | https://nestjs.com |
| **AdonisJS** | 全栈 MVC、电池内置 | `npm init adonisjs@latest my-app` | **Japa 测试 + ESLint** | https://adonisjs.com |
| **Elysia** | Bun 原生、端到端类型安全 | `bun create elysia my-app` | TypeScript | https://elysiajs.com |
| **Feathers** | 实时 API / 微服务 | `npx @feathersjs/cli generate app` | Mocha 可选 | https://feathersjs.com |
| **LoopBack 4** | API 优先、OpenAPI 驱动 | `npx lb4 app` | Mocha | https://loopback.io |
| **Sails** | MVC + 实时、约定优先 | `npm i -g sails && sails new my-app` | Mocha | https://sailsjs.com |
| **hapi** | 配置式、安全优先 | `npm init -y && npm i @hapi/hapi` | lab（需自配） | https://hapi.dev |
| **Restify** | 纯 REST API 专用 | `npm init -y && npm i restify` | 无 | https://restify.com |
| **tRPC** | 类型安全 RPC（非独立服务） | 依附 Next / Express 安装 | 随宿主框架 | https://trpc.io |

**Node 选型建议**

| 场景 | 选择 | 理由 |
| --- | --- | --- |
| 快速原型 / 小服务 | Hono 或 Fastify | 启动快、依赖少、TS 友好 |
| 企业级 / 多人协作 | **NestJS** | 架构约束强、测试链完整、招人容易 |
| 想要 Laravel 式体验 | **AdonisJS** | 全栈一体化，自带认证/ORM/测试 |
| Bun 运行时 | Elysia | 与 Bun 深度集成，类型推导最强 |
| 存量维护 | Express / Koa | 生态与中间件最广 |

> Node 后端最大的坑：Express / Koa 本身**不带任何验证**，`npm init` 之后 lint、类型、测试全要自己装。只有 NestJS、AdonisJS 是开箱即用的。

#### 1.2.2 Python

| 框架 | 定位 | 初始化命令 | 自带验证 | 官网 |
| --- | --- | --- | --- | --- |
| **FastAPI** | 异步、OpenAPI 自动生成 | `uv init my-app && uv add fastapi "uvicorn[standard]"` | 无 | https://fastapi.tiangolo.com |
| **Django** | 全家桶、Admin/ORM | `django-admin startproject myapp` | **自带测试运行器** | https://www.djangoproject.com |
| **Flask** | 微框架、灵活 | `uv init my-app && uv add flask` | 无 | https://flask.palletsprojects.com |
| **Litestar** | 异步、高性能、DI | `uv init my-app && uv add litestar` | 无 | https://litestar.dev |
| **Django REST Framework** | DRF，Django 的 API 层 | `uv add djangorestframework` | 继承 Django | https://www.django-rest-framework.org |

Python 通用验证链（几乎所有项目都该装）：

```bash
uv add --dev ruff mypy pytest
```

#### 1.2.3 JVM（Java / Kotlin）

| 框架 | 语言 | 定位 | 初始化方式 | 自带验证 | 官网 |
| --- | --- | --- | --- | --- | --- |
| **Spring Boot** | Java/Kotlin | 企业标准 | https://start.spring.io 生成 | **JUnit + Checkstyle 可选** | https://spring.io |
| **Quarkus** | Java/Kotlin | 云原生、快启动 | `quarkus create app` 或 https://code.quarkus.io | JUnit | https://quarkus.io |
| **Micronaut** | Java/Kotlin | 编译期 DI、低内存 | `mn create-app` 或 https://start.micronaut.io | JUnit | https://micronaut.io |
| **Ktor** | Kotlin | 协程原生、轻量 | https://start.ktor.io 生成 | kotlin.test | https://ktor.io |

#### 1.2.4 Go

| 框架 | 定位 | 初始化命令 | 自带验证 | 官网 |
| --- | --- | --- | --- | --- |
| **标准库 net/http** | 零依赖 | `go mod init example.com/myapp` | **`go vet` + `go test` 内置** | https://go.dev |
| **Gin** | 最流行的 Web 框架 | `go mod init myapp && go get github.com/gin-gonic/gin` | 继承 go test | https://gin-gonic.com |
| **Echo** | 中量级、中间件丰富 | `go mod init myapp && go get github.com/labstack/echo/v4` | 继承 go test | https://echo.labstack.com |
| **Fiber** | Express 风格、基于 fasthttp | `go mod init myapp && go get github.com/gofiber/fiber/v2` | 继承 go test | https://gofiber.io |
| **Chi** | 轻量路由、标准库兼容 | `go mod init myapp && go get github.com/go-chi/chi/v5` | 继承 go test | https://go-chi.io |

Go 的验证是语言级内置，无论用哪个框架，`go vet ./... && go test ./...` 都能直接跑；只建议补一个 `golangci-lint`。

#### 1.2.5 Rust

| 框架 | 定位 | 初始化命令 | 自带验证 | 官网 |
| --- | --- | --- | --- | --- |
| **Axum** | Tokio 官方系、模块化 | `cargo new my-app && cd my-app && cargo add axum tokio` | **fmt + clippy + test 内置** | https://docs.rs/axum |
| **Actix Web** | 极致性能 | `cargo new my-app && cargo add actix-web` | 同上 | https://actix.rs |
| **Rocket** | 易用、宏驱动 | `cargo new my-app && cargo add rocket` | 同上 | https://rocket.rs |

#### 1.2.6 PHP

| 框架 | 定位 | 初始化命令 | 自带验证 | 官网 |
| --- | --- | --- | --- | --- |
| **Laravel** | 全栈、生态最大 | `composer create-project laravel/laravel my-app` | **PHPUnit + Pint** | https://laravel.com |
| **Symfony** | 组件化、企业级 | `composer create-project symfony/skeleton my-app` | PHPUnit 可选 | https://symfony.com |
| **Slim** | 微框架 | `composer create-project slim/slim-skeleton my-app` | PHPUnit | https://www.slimframework.com |

#### 1.2.7 Ruby

| 框架 | 定位 | 初始化命令 | 自带验证 | 官网 |
| --- | --- | --- | --- | --- |
| **Rails** | 全栈、约定优于配置 | `rails new my-app` | **Minitest + RuboCop 可选** | https://rubyonrails.org |
| **Sinatra** | 微框架 | `gem install sinatra` | 无 | https://sinatrarb.com |

#### 1.2.8 C# / .NET

| 模板 | 定位 | 初始化命令 | 自带验证 | 官网 |
| --- | --- | --- | --- | --- |
| **ASP.NET Core Web API** | 主流后端 | `dotnet new webapi -o MyApp` | 编译检查 | https://dotnet.microsoft.com |
| **Minimal API** | 轻量端点 | `dotnet new web -o MyApp` | 编译检查 | 同上 |
| **Blazor** | 全栈 C# | `dotnet new blazor -o MyApp` | 编译检查 | 同上 |

测试需单独建项目：`dotnet new xunit -o MyApp.Tests`。

#### 1.2.9 其他语言

| 语言 | 框架 | 初始化命令 | 自带验证 | 官网 |
| --- | --- | --- | --- | --- |
| Elixir | **Phoenix** | `mix phx.new my_app` | **ExUnit + mix format** | https://www.phoenixframework.org |
| Swift | **Vapor** | `vapor new my-app` | XCTest | https://vapor.codes |
| Dart | **Serverpod** | `dart pub global activate serverpod_cli && serverpod create my_app` | 自带测试模板 | https://serverpod.dev |

---

### 1.3 移动端 / 桌面端

| 目标 | 命令 | 自带验证 | 官网 |
| --- | --- | --- | --- |
| React Native（Expo） | `npx create-expo-app@latest` | TypeScript + Jest 可选 | https://expo.dev |
| React Native（CLI） | `npx @react-native-community/cli@latest init MyApp` | **Jest 自带** | https://reactnative.dev |
| Flutter | `flutter create my_app` | **flutter analyze + test 内置** | https://flutter.dev |
| Tauri（Rust + Web） | `npm create tauri-app@latest` | 前端验证 + Rust clippy | https://tauri.app |
| Electron | `npm create @quick-start/electron@latest` | 需自配 | https://www.electronjs.org |

---

### 1.4 验证能力矩阵（按语言）

| 语言 | 开箱即有的验证 | 通常还需补 |
| --- | --- | --- |
| **Node.js** | 取决于框架：Nest / Adonis 全套；Express / Koa 为零 | eslint + prettier + vitest（或 jest） |
| **Python** | Django 自带测试运行器，其余为零 | ruff + mypy + pytest |
| **Java / Kotlin** | JUnit + 构建期编译检查 | Checkstyle / SpotBugs |
| **Go** | `go vet` + `go test` + `gofmt` | golangci-lint |
| **Rust** | `cargo fmt` + `clippy` + `test` | 无 |
| **PHP** | Laravel / Symfony 自带 PHPUnit | Pint / PHPStan |
| **Ruby** | Rails 自带 Minitest | RuboCop |
| **C#** | 编译检查 | xunit 测试项目 |
| **Elixir** | ExUnit + mix format | Credo |
| **Dart / Flutter** | analyze + test | 无 |

---

## 2. AI 初始化层

AI 层的本质是给编码代理提供**入口、知识、规格、能力、配置**五个层次，不是单纯写一篇 README。

### 2.1 五层模型

| 层 | 内容 | 位置示例 | 读取时机 | 体量控制 |
| --- | --- | --- | --- | --- |
| **L1 入口** | 项目定位、命令、约定、禁区 | `AGENTS.md` / `CLAUDE.md` | 每次会话必读 | < 200 行 |
| **L2 知识** | 架构、领域术语、ADR | `docs/ai/` | 按需检索 | 单篇 < 300 行 |
| **L3 规格** | 需求、变更提案、任务拆解 | `openspec/` / `.specify/` | 开发前 | 按变更组织 |
| **L4 能力** | 命令、技能、子代理、钩子 | `.claude/commands/`、`.claude/skills/` | 主动调用 | 一事一技能 |
| **L5 配置** | MCP、权限、忽略规则 | `.mcp.json`、`settings.json`、`.cursorignore` | 工具启动时 | 最小权限 |

> 判断标准：**L1 越短越好，L2 越准越好，L3 越可追溯越好，L4 越原子越好，L5 越收紧越好。**

### 2.2 入口文件全景

| 文件 | 读取方 | 官方文档 |
| --- | --- | --- |
| `AGENTS.md` | Codex / Cursor / Claude Code / 多数代理 | https://agents.md |
| `CLAUDE.md` | Claude Code | https://docs.claude.com/en/docs/claude-code/memory |
| `.cursor/rules/*.mdc` | Cursor | https://cursor.com/docs/context/rules |
| `.github/copilot-instructions.md` | GitHub Copilot | https://docs.github.com/en/copilot |
| `.windsurf/rules/`（旧版 `.windsurfrules`） | Windsurf | https://windsurf.com/docs |
| `.clinerules` | Cline | https://docs.cline.bot |
| `.roorules` / `.roo/rules/` | Roo Code | https://docs.roocode.com |
| `CONVENTIONS.md` + `.aider.conf.yml` | Aider | https://aider.chat/docs |
| `GEMINI.md` | Gemini CLI | https://github.com/google-gemini/gemini-cli |
| `AGENTS.md`（同标准） | Codex CLI / IDE | https://developers.openai.com/codex |

**多工具共存的推荐做法**：只维护一份 `AGENTS.md`，其余入口用软链接或一行动用：

```bash
touch AGENTS.md
ln -s AGENTS.md CLAUDE.md          # Claude Code
ln -s AGENTS.md GEMINI.md          # Gemini CLI
# Windows 无开发者模式时改用复制，或用 mklink /H
```

> 例外：Cursor 的 `.mdc` 支持 globs、alwaysApply 等元数据，需要按目录分规则时，不适合软链接，单独维护。

### 2.3 AI 初始化工具对比

| 工具 | 类型 | 命令 | 产出 | 交互性 | 适用 |
| --- | --- | --- | --- | --- | --- |
| **Claude Code `/init`** | 反向生成 | Claude Code 内执行 `/init` | `CLAUDE.md` | 非交互 | 已有代码，补入口文档 |
| **OpenSpec** 2.2.0 ✅ | 规格驱动 | `npx @fission-ai/openspec@latest init --tools claude,cursor,codex --scope project --no-animation` | `openspec/config.yaml`、`specs/`、`changes/`、`.agents/skills/`、`.claude/skills/` | 可全非交互 | 新项目、变更可追溯 |
| **Spec Kit** | 规格驱动 + 宪法 | `uvx --from git+https://github.com/github/spec-kit.git specify init my-app --ai claude` | `.specify/`、项目宪法、各 Agent 指令 | 半交互 | 团队强规范 |
| **BMAD-METHOD** 6.12.4 | 角色化工作流 | `npx bmad-method install` | `_bmad/` 角色、工作流、PRD/架构模板 | 交互式 | 多角色协作 |
| **Aider** | 结对编程 | `aider`（读 `CONVENTIONS.md`） | 无脚手架，靠约定文件 | 交互 | 终端内改代码 |
| **Continue** | IDE 插件 | 安装插件，配置 `.continue/config.yaml` | 自定义规则/模型 | 交互 | VS Code / JetBrains |
| **Cline / Roo Code** | IDE Agent | 安装插件，编写 `.clinerules` / `.roorules` | 规则文件 | 交互 | VS Code 内自主执行 |
| **GitHub Copilot** | IDE 补全 + Agent | 手写 `.github/copilot-instructions.md`、`.github/prompts/*.prompt.md` | 指令与提示文件 | 手动 | GitHub 生态 |
| **Gemini CLI** | 终端 Agent | `gemini`（读 `GEMINI.md`） | 无脚手架 | 交互 | Google 生态 |

**选型建议**

| 场景 | 选择 |
| --- | --- |
| 个人 / 小项目 | Claude Code `/init` |
| 新项目、要"先想清楚再写" | OpenSpec |
| 团队要统一原则与评审门禁 | Spec Kit |
| 需要产品 / 架构 / 开发角色链 | BMAD |
| 已有项目、只想补规则 | 手写 `AGENTS.md` + 各工具入口 |

### 2.4 OpenSpec 详解（已实测）

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

> 实测补充：OpenSpec **不会**生成 `AGENTS.md` 或 `CLAUDE.md`，只写 skills 与 `openspec/` 配置。需要入口文档时，再执行一次 Claude Code 的 `/init`。

### 2.5 AGENTS.md 模板（完整版）

```markdown
# 项目名 —— 一句话定位

> AI 编码代理入口。命令 / 目录 / 约定变化时同步更新；细节超过一屏请拆到 docs/ai/。

## 技术栈
- 运行时与版本（Node 22 / Python 3.12 / Go 1.22）
- 框架与关键库
- 包管理器（pnpm / uv / cargo，写明，别让 AI 猜）

## 常用命令
- 安装：pnpm install
- 开发：pnpm dev
- 测试：pnpm test -- <path>
- 静态检查：pnpm lint && pnpm typecheck
- 完整验证：./scripts/verify.sh

## 目录地图
- src/views/       页面
- src/api/         接口封装（统一走 request.ts，禁止裸 fetch）
- src/components/  通用组件
- docs/ai/         架构与约定

## 编码约定
- 组件用 <script setup lang="ts">，禁 Options API
- 样式只写原子类，不新增 scss 文件
- 新接口必须补类型，禁止 any

## 禁区
- 不改 generated/ 下自动生成文件
- 不升级主版本依赖
- 不提交 .env 与本地数据

## 完成定义
一次改动算完成 = 验证脚本全绿 + 受影响的文档已同步

## 参考资料
- 架构：docs/ai/architecture.md
- 约定：docs/ai/conventions.md
```

### 2.6 各工具专属配置

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

### 2.7 MCP 与安全边界

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

### 2.8 AI 初始化时序

```
① 官方脚手架 → ② 跑通 lint / build / test → ③ 生成入口文档（/init）
      → ④ 初始化规格体系（openspec init）→ ⑤ 配命令与技能（.claude/）
      → ⑥ 配 MCP 与忽略规则 → ⑦ 三项验收
```

顺序不能反：**先有能跑的验证，再让 AI 学规则**，否则 AI 会照着错误的状态生成文档。

---

## 3. 组合配方

### 配方 A：极简（约 3 分钟）

```bash
npm create vite@latest my-app -- --template react-ts
cd my-app && npm install
npm run lint && npm run build
claude        # 进入后执行 /init
```

### 配方 B：前端正式项目（推荐）

```bash
npm create vite@latest my-app -- --template react-ts
cd my-app && npm install
npx @fission-ai/openspec@latest init --tools claude,cursor,codex --scope project --no-animation
npm i -D vitest @testing-library/react @testing-library/jest-dom jsdom
```

### 配方 C：Node 后端

```bash
# 企业级
npx @nestjs/cli@latest new my-api
cd my-api && npx @fission-ai/openspec@latest init --tools claude,cursor,codex --scope project

# 轻量
npm create fastify@latest my-api
cd my-api && npx @fission-ai/openspec@latest init --tools claude,cursor
```

### 配方 D：Python

```bash
uv init my-api && cd my-api
uv add fastapi "uvicorn[standard]"
uv add --dev ruff mypy pytest

# Django 路线
# uv add django && uv run django-admin startproject myapp

npx @fission-ai/openspec@latest init --tools claude,cursor
```

### 配方 E：Go / Rust（验证已内置）

```bash
# Go
go mod init example.com/my-api
go vet ./... && go test ./...

# Rust
cargo new my-api && cd my-api
cargo fmt --check && cargo clippy -- -D warnings && cargo test

# AI 层
npx @fission-ai/openspec@latest init --tools claude,cursor
```

### 配方 F：Java

```bash
# 从 start.spring.io 下载解压后
./mvnw test
npx @fission-ai/openspec@latest init --tools claude,cursor
```

### 配方 G：团队强规范

```bash
uvx --from git+https://github.com/github/spec-kit.git specify init my-app --ai claude
```

---

## 4. 验收清单

**验证链**

- [ ] lint 能跑通，且故意写一行错代码时**确实会报错**
- [ ] 类型检查 / 编译通过
- [ ] `test` 脚本存在且至少有一个能跑的用例
- [ ] CI 配置文件存在，或明确决定暂不接 CI

**AI 层**

- [ ] 入口文件包含：技术栈、常用命令、目录地图、编码约定、禁区、完成定义
- [ ] AI 工具能实际读到规则（Cursor 需重启，Claude Code 需在仓库根启动）
- [ ] 新开会话，只读入口文件，能正确回答三问：
  1. 怎么启动开发服务？
  2. 新增一个接口该放哪、要遵守什么约定？
  3. 哪些文件不能碰？

三问全对才算合格；答不出来说明缺的是**命令**和**禁区**，不是文笔。

---

## 5. 常见坑

| 坑 | 现象 | 处理 |
| --- | --- | --- |
| 误以为模板自带 ESLint | Vite 7 React 用 oxlint | 看 `.oxlintrc.json`，不要找 `.eslintrc` |
| 误以为模板自带测试 | `npm test` 报 missing script | 补 Vitest，或选 Nest / Adonis |
| Express / Koa 零验证 | 装完什么检查都没有 | 按配方自己装 eslint + vitest |
| OpenSpec 没生成 AGENTS.md | Codex 读不到项目说明 | 再跑 `/init` 补入口文件 |
| `ln -s` 失败 | Windows 无开发者模式 | 复制文件，或用 `mklink /H` |
| Spec Kit 安装失败 | `Failed to connect to github.com` | 配代理，或改用 OpenSpec |
| 规则不生效 | AI 仍然乱写 | 确认从仓库根启动，Cursor 重启 IDE |
| 入口文件越写越长 | 超过 200 行后 AI 开始忽略细节 | 拆分到 `docs/ai/`，入口只留索引 |
| 多工具规则各写一份 | 内容漂移、互相矛盾 | 只维护 `AGENTS.md`，其余软链接 |
| MCP 权限过大 | AI 能删库、能推主干 | 只读优先，写操作加确认 |

---

## 6. 官网速查

| 分类 | 地址 |
| --- | --- |
| AGENTS.md 标准 | https://agents.md |
| Claude Code 文档 | https://docs.claude.com/en/docs/claude-code |
| Cursor 文档 | https://cursor.com/docs |
| GitHub Copilot 文档 | https://docs.github.com/en/copilot |
| OpenSpec | https://github.com/Fission-AI/OpenSpec |
| Spec Kit | https://github.com/github/spec-kit |
| BMAD-METHOD | https://github.com/bmad-code-org/BMAD-METHOD |
| Aider | https://aider.chat |
| Continue | https://continue.dev |
| Roo Code | https://roocode.com |
| Gemini CLI | https://github.com/google-gemini/gemini-cli |
| Vite | https://vite.dev |
| Node.js | https://nodejs.org |
| Go | https://go.dev |
| Rust | https://www.rust-lang.org |

> 注：`cline.bot`、`windsurf.com` 在本次网络环境下未能连通，链接按官方文档记录，实际以官方为准。

---

## 7. 一分钟速查

```bash
# 前端
npm create vite@latest my-app -- --template react-ts     # React
npx create-next-app@latest my-app                        # Next.js
npm create vue@latest my-app                             # Vue
npx @angular/cli@latest new my-app                       # Angular（验证最全）

# Node 后端
npx @nestjs/cli@latest new my-app                        # 企业级，Jest 全套
npm create fastify@latest my-app                         # 高性能轻量
npm create hono@latest my-app                            # 边缘运行时

# Python 后端
uv init my-app && uv add fastapi "uvicorn[standard]"
django-admin startproject myapp

# 其他语言
go mod init example.com/myapp                            # vet + test 内置
cargo new my-app                                         # fmt + clippy + test 内置
dotnet new webapi -o MyApp
composer create-project laravel/laravel my-app
rails new my-app

# AI 层
npx @fission-ai/openspec@latest init --tools claude,cursor,codex --scope project
npx bmad-method install
claude   →   /init

# 验证
npm run lint && npm run build && npm test
```
