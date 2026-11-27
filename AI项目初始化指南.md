# AI 项目初始化指南

> 脚手架选型 × 验证链 × AI 文档初始化
> 更新日期：2026-11-27　｜　标注 **✅ 实测** 的命令已在本机跑通，其余为官方标准命令，版本迭代较快，执行前建议先看 `--help`。

---

## 0. 一页结论


| 目标        | 推荐路径                                                                     |
| --------- | ------------------------------------------------------------------------ |
| 只要快       | 任意官方脚手架 → `claude` 里执行 `/init`                                           |
| 要规格驱动（推荐） | 脚手架 → `npx @fission-ai/openspec@latest init --tools claude,cursor,codex` |
| 团队强规范     | 脚手架 → Spec Kit（需能访问 GitHub）                                              |
| 全流程角色化    | 脚手架 → `npx bmad-method install`                                          |
| 验证链最完整    | NestJS / Go / Rust / Spring Boot（开箱自带 lint + 测试）                         |


**没有单一脚手架同时自带「验证 + AI 文档」**，必须两段式：先官方脚手架解决验证，再 AI 工具解决文档。

---

## 1. 官方脚手架速查

### 1.1 前端


| 技术栈         | 初始化命令                                                    | 自带验证                                        | 缺口           |
| ----------- | -------------------------------------------------------- | ------------------------------------------- | ------------ |
| React（Vite） | `npm create vite@latest my-app -- --template react-ts` ✅ | **oxlint** + `tsc -b`（build 时执行）            | 无测试框架        |
| Next.js     | `npx create-next-app@latest my-app`                      | ESLint + TypeScript                         | 无测试框架        |
| Vue         | `npm create vue@latest my-app`                           | 可勾选 ESLint / Prettier / **Vitest** / E2E    | 需手动勾选        |
| Nuxt        | `npm create nuxt@latest my-app`                          | ESLint / Vitest 按提示勾选                       | 默认较薄         |
| Svelte      | `npx sv create my-app`                                   | 可勾选 ESLint / Prettier / Vitest / Playwright | 需手动勾选        |
| Astro       | `npm create astro@latest`                                | TypeScript 严格模式                             | lint / 测试需另配 |
| **NestJS**  | `npx @nestjs/cli@latest new my-app`                      | **Jest + ESLint + Prettier 全自带**            | 无            |


> ⚠️ 实测发现：Vite 7 的 React 模板用的是 **oxlint**，不是 ESLint；`package.json` 里只有 `lint / build / dev / preview` 四个脚本，**没有 test**。

### 1.2 后端


| 技术栈             | 初始化命令                             | 自带验证                                    | 缺口                                 |
| --------------- | --------------------------------- | --------------------------------------- | ---------------------------------- |
| Node + TS（裸）    | `npm init -y`                     | 无                                       | lint / 类型 / 测试全需自配                 |
| Hono            | `npm create hono@latest my-app`   | TypeScript                              | 无测试                                |
| Python          | `uv init my-app`                  | 无                                       | 建议 `uv add --dev ruff mypy pytest` |
| Django          | `django-admin startproject myapp` | 自带测试运行器                                 | 无 lint / 类型                        |
| FastAPI         | 无官方脚手架（`uv init` + 手动装依赖）         | 无                                       | 全需自配                               |
| **Go**          | `go mod init example.com/myapp`   | **`go vet` + `go test` 内置**             | lint 建议加 `golangci-lint`           |
| **Rust**        | `cargo new my-app`                | **`cargo fmt` / `clippy` / `test` 全内置** | 无                                  |
| **Spring Boot** | start.spring.io 生成（或 IDE 向导）      | **JUnit + Checkstyle 可选**               | 无                                  |
| .NET            | `dotnet new webapi`               | 编译检查                                    | 测试需 `dotnet new xunit`             |


### 1.3 验证能力排序

```
Rust / Go / Spring Boot / NestJS   ← 开箱即用
        ↑
Vue / Svelte / Next.js             ← 勾选后可用
        ↑
Vite React / Nuxt / Astro          ← 只有 lint + 类型
        ↑
裸 Node / uv init / FastAPI        ← 什么都要自己装
```

---

## 2. AI 初始化层

AI 层的本质是给编码代理提供**入口文件 + 规格 + 技能**，三者不是一回事。

### 2.1 入口文件标准


| 文件                                | 读者                                  | 说明                   |
| --------------------------------- | ----------------------------------- | -------------------- |
| `AGENTS.md`                       | Codex / Cursor / Claude Code / 多数代理 | 跨工具事实标准，优先维护这一份      |
| `CLAUDE.md`                       | Claude Code                         | 可与 `AGENTS.md` 软链接共用 |
| `.cursor/rules/*.mdc`             | Cursor                              | 按目录/场景分规则时可拆多份       |
| `.github/copilot-instructions.md` | GitHub Copilot                      | Copilot 专属           |


开启软链接共用的做法（可选）：

```bash
touch AGENTS.md
ln -s AGENTS.md CLAUDE.md      # Windows 需开发者模式或用 mklink
```

### 2.2 AI 初始化工具对比


| 工具                      | 命令                                                                                                | 产出                                                                                   | 交互性       | 备注                              |
| ----------------------- | ------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------ | --------- | ------------------------------- |
| **Claude Code `/init`** | 在 Claude Code 中执行 `/init`                                                                         | `CLAUDE.md`                                                                          | 非交互       | 需**已有代码**才反向生成，适合补入口文档          |
| **OpenSpec** 2.2.0 ✅    | `npx @fission-ai/openspec@latest init --tools claude,cursor,codex --scope project --no-animation` | `openspec/config.yaml`、`specs/`、`changes/`、`.agents/skills/`、`.claude/skills/`、各工具指令 | **可全非交互** | 轻量、规格驱动，适合新项目                   |
| **Spec Kit**（GitHub 官方） | `uvx --from git+https://github.com/github/spec-kit.git specify init my-app --ai claude`           | `.specify/`、项目宪法、各 Agent 指令                                                          | 半交互       | **需能访问 github.com**，本次本机网络受限未跑通 |
| **BMAD-METHOD** 6.12.4  | `npx bmad-method install`                                                                         | `_bmad/` 全套代理、工作流、PRD/架构模板                                                           | 交互式       | 最重，等于把敏捷流程装进仓库                  |


**选型建议**

- 个人 / 小项目：`/init` 足够。
- 新项目要"先想清楚再写"：OpenSpec。
- 团队要统一原则与评审：Spec Kit。
- 需要产品经理 + 架构师 + 开发角色链：BMAD。

### 2.3 OpenSpec 详解（已实测）

```bash
cd my-app
npx --yes @fission-ai/openspec@latest init \
  --tools claude,cursor,codex \
  --scope project \
  --no-animation
```

关键参数：


| 参数               | 取值                                         | 作用                            |
| ---------------- | ------------------------------------------ | ----------------------------- |
| `--tools`        | `all` / `none` / `claude,cursor,codex,...` | 生成哪些工具的接入文件，支持 80+ 个 ID       |
| `--scope`        | `global` / `project`                       | 技能装到用户级还是项目级（团队协作选 `project`） |
| `--language`     | 如 `zh-CN`                                  | 生成物语言                         |
| `--no-animation` | —                                          | 关闭欢迎动画，适配脚本/CI                |


生成后可用工作流：`openspec-propose` → `openspec-apply-change` → `openspec-archive-change`，另有 explore / sync-specs / update-change。

> 实测补充：OpenSpec **不会**生成 `AGENTS.md` 或 `CLAUDE.md`，它只写 skills 与 `openspec/` 配置。需要入口文档时，再执行一次 Claude Code 的 `/init`。

---

## 3. 组合配方

### 配方 A：极简（约 3 分钟）

```bash
npm create vite@latest my-app -- --template react-ts
cd my-app && npm install
npm run lint && npm run build        # 确认验证链可用
claude                                # 进入后执行 /init 生成 CLAUDE.md
```

适用：个人练手、原型、临时项目。

### 配方 B：规格驱动（推荐）

```bash
npm create vite@latest my-app -- --template react-ts
cd my-app && npm install

# AI 层
npx @fission-ai/openspec@latest init --tools claude,cursor,codex --scope project --no-animation

# 可选：补测试链
npm i -D vitest @testing-library/react @testing-library/jest-dom jsdom
```

适用：正式项目、需要可追溯的变更记录。

### 配方 C：团队强规范

```bash
# 需能访问 GitHub
uvx --from git+https://github.com/github/spec-kit.git specify init my-app --ai claude
```

适用：多人协作、需要宪法级约束与技术评审门禁。

### 配方 D：全流程

```bash
npx bmad-method install
```

适用：产品/架构/开发多角色协作，愿意承担流程重量。

### 配方 E：后端

```bash
# Node
npx @nestjs/cli@latest new my-api        # 自带 Jest + ESLint

# Python
uv init my-api && cd my-api
uv add fastapi uvicorn
uv add --dev ruff mypy pytest

# Go
go mod init example.com/my-api           # vet + test 内置

# Rust
cargo new my-api                         # fmt + clippy + test 内置
```

---

## 4. 验收清单

初始化完成后，逐项打勾：

**验证链**

- [ ] `npm run lint`（或等价命令）能跑通，且故意写一行错代码时**会报错**
- [ ] 类型检查 / 编译通过
- [ ] `test` 脚本存在且至少有一个能跑的用例
- [ ] CI 配置文件存在，或明确决定暂不接 CI

**AI 层**

- [ ] 入口文件（`AGENTS.md` / `CLAUDE.md`）包含：技术栈、常用命令、目录地图、编码约定、禁区、完成定义
- [ ] AI 工具能实际读到规则（Cursor 需重启，Claude Code 需在仓库根启动）
- [ ] 新开一个 AI 会话，只读入口文件，能正确回答三个问题：
    1. 怎么启动开发服务？
    2. 新增一个接口该放哪、要遵守什么约定？
    3. 哪些文件不能碰？

三个都答对才算合格；答不出来说明缺的是**命令**和**禁区**，不是文笔。

---

## 5. 常见坑


| 坑                      | 现象                                | 处理                                 |
| ---------------------- | --------------------------------- | ---------------------------------- |
| 误以为模板自带 ESLint         | Vite 7 React 用 oxlint             | 看 `.oxlintrc.json`，不要找 `.eslintrc` |
| 误以为模板自带测试              | `npm test` 报 missing script       | 按配方 B 补 Vitest                     |
| OpenSpec 没生成 AGENTS.md | Codex 读不到项目说明                     | 再跑 `/init` 补入口文件                   |
| `ln -s` 失败             | Windows 无开发者模式                    | 改为复制文件，或 `mklink /H`               |
| Spec Kit 安装失败          | `Failed to connect to github.com` | 需代理/镜像，或改用 OpenSpec                |
| 规则不生效                  | AI 仍然乱写                           | 确认从仓库根启动，Cursor 重启 IDE             |
| 入口文件越写越长               | 超过 200 行后 AI 开始忽略细节               | 拆分到 `docs/`，入口只留索引                 |


---

## 6. 附录：本次环境实测记录


| 项                      | 版本 / 结果                                    |
| ---------------------- | ------------------------------------------ |
| 操作系统                   | macOS (darwin 25.2.0, Apple Silicon)       |
| Node / npm             | v22.20.0 / 10.9.3                          |
| Python / uv            | 3.9.6 / 0.12.11                            |
| Go                     | 1.17.6                                     |
| create-vite（React 模板）  | 跑通，产物 lint ✅ / build ✅                     |
| `@fission-ai/openspec` | 2.2.0，`init --tools claude,cursor,codex` ✅ |
| `bmad-method`          | 6.12.4（仅查版本）                               |
| `create-next-app`      | 16.4.6（仅查版本）                               |
| `nuxt`                 | 4.7.0（仅查版本）                                |
| `@nestjs/cli`          | 12.0.15（仅查版本）                              |
| `create-astro`         | 5.3.2（仅查版本）                                |
| `create-svelte`        | 7.0.1（仅查版本）                                |
| Spec Kit               | ❌ 本机无法访问 github.com，未验证                    |


实测产物参考：`react-app/`

---

## 7. 一分钟速查

```bash
# 前端
npm create vite@latest my-app -- --template react-ts     # React
npx create-next-app@latest my-app                        # Next.js
npm create vue@latest my-app                             # Vue
npx @nestjs/cli@latest new my-app                        # NestJS（验证最全）

# AI 层
npx @fission-ai/openspec@latest init --tools claude,cursor,codex --scope project
npx bmad-method install
claude   →   /init

# 验证
npm run lint && npm run build && npm test
```

