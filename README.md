# skills-manage 维护手册

这份文档用于协助维护 `skills-manage` 项目中的 Agent Skills 集合。它适合用来管理从官方文档生成的 skills、从外部仓库同步的 skills，以及自己手写维护的 skills。

## 1. 项目定位

`skills-manage` 是一个 skills 管理与生成项目，不是单个 skill。

它的核心能力是：

- 从官方文档仓库生成 skill。
- 从其他项目同步已有 skill。
- 保留自己手写维护的 skill。
- 通过 `meta.ts` 统一配置整个 skills 集合。
- 用 `sources/`、`vendor/`、`skills/` 形成可更新、可追踪的 skills 组合包。

可以把它理解成：一个用配置和脚本维护 skills 的工程化工具箱。

## 2. 核心运行模型

这个项目的关键是：**`meta.ts` 是配置来源，`skills/` 是最终产物。**

基本关系：

```text
meta.ts
  -> sources/   官方文档来源
  -> vendor/    外部 skill 来源
  -> skills/    最终可使用的 skills
```

三类来源：

- Type 1：官方文档生成型，需要 agent 阅读文档后生成或更新。
- Type 2：外部 skill 同步型，主要由脚本复制同步。
- Type 3：手写维护型，由自己长期维护。

日常维护时，先判断 skill 属于哪一类，再选择对应流程。

## 3. 核心目录

```text
meta.ts              # skills 集合配置入口
sources/             # 官方文档来源，通常用于生成型 skills
vendor/              # 外部已有 skills 的来源仓库
skills/              # 最终输出的 skills
instructions/        # 可选，针对某个项目的生成说明
AGENTS.md            # 给 agent 的生成与更新规则
scripts/cli.ts       # init、sync、check、cleanup 等命令实现
```

重点：

- 修改集合范围时，优先改 `meta.ts`。
- 更新生成型 skill 时，参考 `AGENTS.md`。
- 最终要安装或使用的内容在 `skills/`。

## 4. 三类 skill 来源

### Type 1：官方文档生成型

来源目录：

```text
sources/{project}/
```

输出目录：

```text
skills/{project}/
```

适合场景：

- 官方文档较完整的项目。
- 需要从大量文档中提炼 agent 可用的开发规则、API 用法、最佳实践。

特点：

- 需要模型或 agent 参与生成。
- 脚本只负责拉取文档、管理 submodule。
- `SKILL.md` 和 `references/*.md` 需要由 agent 阅读文档后生成或更新。
- `GENERATION.md` 记录来源仓库、Git SHA 和生成时间。

### Type 2：外部 skill 同步型

来源目录：

```text
vendor/{project}/skills/{skill-name}/
```

输出目录：

```text
skills/{output-name}/
```

适合场景：

- 其他项目已经维护了自己的 skill。
- 你只需要同步它们，而不是重新生成。

特点：

- 主要是复制同步。
- 通常不应该在本仓库手动修改同步来的 skill。
- 如需修改，优先向上游仓库贡献。
- `SYNC.md` 记录来源路径、Git SHA 和同步时间。

### Type 3：手写维护型

来源：

```text
skills/{name}/
```

适合场景：

- 个人偏好。
- 团队规范。
- 项目经验。
- 固定工作流。
- 非官方文档能直接覆盖的领域知识。

特点：

- 由你自己维护。
- 需要在 `meta.ts` 的 `manual` 数组中声明，否则可能被 `cleanup` 当成多余 skill 删除。

## 5. 当前 skills 清单

本项目当前包含两类主要可复用 skills：官方文档生成型和外部同步型。维护时可以先根据下表判断某个 skill 的来源类型，再选择对应更新方式。

### 官方文档生成型

这类 skill 从官方文档仓库生成，更新时需要 agent 读取文档变化并判断是否影响 skill。

| Skill | 中文说明 | Source |
|-------|----------|--------|
| [vue](skills/vue) | Vue 核心开发能力，包括响应式、组件、Composition API 等。 | [vuejs/docs](https://github.com/vuejs/docs) |
| [nuxt](skills/nuxt) | Nuxt 框架能力，包括文件路由、服务端路由、模块机制等。 | [nuxt/nuxt](https://github.com/nuxt/nuxt) |
| [pinia](skills/pinia) | Vue 状态管理能力，关注类型安全、store 组织和组合式使用方式。 | [vuejs/pinia](https://github.com/vuejs/pinia) |
| [vite](skills/vite) | Vite 构建工具能力，包括配置、插件、SSR、库模式等。 | [vitejs/vite](https://github.com/vitejs/vite) |
| [vitepress](skills/vitepress) | VitePress 静态站点生成能力，适合文档站点和内容组织。 | [vuejs/vitepress](https://github.com/vuejs/vitepress) |
| [vitest](skills/vitest) | Vitest 测试能力，包括单元测试、测试配置和 Vite 生态测试实践。 | [vitest-dev/vitest](https://github.com/vitest-dev/vitest) |
| [unocss](skills/unocss) | UnoCSS 原子化 CSS 能力，包括 presets、transformers 和样式组织。 | [unocss/unocss](https://github.com/unocss/unocss) |
| [pnpm](skills/pnpm) | pnpm 包管理能力，包括依赖安装、workspace 和高效包管理实践。 | [pnpm/pnpm.io](https://github.com/pnpm/pnpm.io) |

### 外部同步型

这类 skill 从外部项目已有的 `skills/` 目录同步，更新时主要使用 `pnpm start sync`。

| Skill | 中文说明 | Source |
|-------|----------|--------|
| [slidev](skills/slidev) | Slidev 演示文稿开发能力，适合开发者幻灯片、主题和交互内容。 | [slidevjs/slidev](https://github.com/slidevjs/slidev) |
| [tsdown](skills/tsdown) | tsdown TypeScript 库构建能力，关注 Rolldown 驱动的打包流程。 | [rolldown/tsdown](https://github.com/rolldown/tsdown) |
| [turborepo](skills/turborepo) | Turborepo monorepo 构建能力，包括任务编排、缓存和工作区管理。 | [vercel/turborepo](https://github.com/vercel/turborepo) |
| [vueuse-functions](skills/vueuse-functions) | VueUse 函数使用能力，覆盖常用组合式工具函数和使用场景。 | [vueuse/skills](https://github.com/vueuse/skills) |
| [vue-best-practices](skills/vue-best-practices) | Vue 3 + TypeScript 最佳实践，适合组件、类型和项目结构规范。 | [vuejs-ai/skills](https://github.com/vuejs-ai/skills) |
| [vue-router-best-practices](skills/vue-router-best-practices) | Vue Router 最佳实践，适合路由组织、导航守卫和页面结构设计。 | [vuejs-ai/skills](https://github.com/vuejs-ai/skills) |
| [vue-testing-best-practices](skills/vue-testing-best-practices) | Vue 测试最佳实践，适合组件测试、交互测试和测试组织。 | [vuejs-ai/skills](https://github.com/vuejs-ai/skills) |
| [web-design-guidelines](skills/web-design-guidelines) | Web 界面设计指导，适合构建更美观、可用的前端界面。 | [vercel-labs/agent-skills](https://github.com/vercel-labs/agent-skills) |

## 6. meta.ts 配置方式

### 添加官方文档生成型 skill

在 `submodules` 中增加项目：

```ts
export const submodules = {
  'my-framework': 'https://github.com/org/my-framework-docs',
}
```

生成后通常对应：

```text
sources/my-framework/
skills/my-framework/
```

### 添加外部同步型 skill

在 `vendors` 中增加项目：

```ts
export const vendors = {
  'some-project': {
    source: 'https://github.com/org/some-project',
    skills: {
      'source-skill-name': 'output-skill-name',
    },
  },
}
```

含义：

```text
vendor/some-project/skills/source-skill-name/
同步到
skills/output-skill-name/
```

### 添加手写 skill

在 `manual` 中登记：

```ts
export const manual = [
  'my-team-rules',
  'my-project-manager-coach',
]
```

然后维护：

```text
skills/my-team-rules/
skills/my-project-manager-coach/
```

## 7. 常用命令

安装依赖：

```bash
pnpm install
```

初始化 submodules：

```bash
pnpm start init
```

自动确认初始化：

```bash
pnpm start init -y
```

检查来源是否有更新：

```bash
pnpm start check
```

更新 submodules 并同步 Type 2 skills：

```bash
pnpm start sync
```

清理不在 `meta.ts` 中的 submodules 和 skills：

```bash
pnpm start cleanup
```

自动确认清理：

```bash
pnpm start cleanup -y
```

谨慎使用 `cleanup -y`，它会直接删除所有不在 `meta.ts` 里的 submodules 和 skills。

## 8. 新增 skill 的标准流程

### 生成官方文档型 skill

1. 在 `meta.ts` 的 `submodules` 中添加项目。

2. 初始化来源仓库：

```bash
pnpm start init
```

3. 确认文档位置，例如：

```text
sources/my-framework/docs/
```

4. 让 agent 根据 `AGENTS.md` 生成 skill：

```text
请根据 AGENTS.md，为 my-framework 生成 skill。
读取 sources/my-framework/docs/，
输出到 skills/my-framework/，
创建 SKILL.md、references/*.md 和 GENERATION.md。
```

5. 检查生成结果：

```text
skills/my-framework/SKILL.md
skills/my-framework/references/
skills/my-framework/GENERATION.md
```

### 同步外部已有 skill

1. 在 `meta.ts` 的 `vendors` 中添加来源。

2. 初始化 vendor：

```bash
pnpm start init
```

3. 执行同步：

```bash
pnpm start sync
```

4. 检查输出：

```text
skills/{output-name}/
skills/{output-name}/SYNC.md
```

### 添加手写 skill

1. 在 `meta.ts` 的 `manual` 中添加 skill 名称。

2. 创建目录：

```text
skills/{name}/
```

3. 至少创建：

```text
skills/{name}/SKILL.md
```

4. 如有需要，再添加：

```text
skills/{name}/references/
skills/{name}/scripts/
skills/{name}/assets/
```

## 9. 检查与更新流程

`AGENTS.md` 是给 agent 看的项目操作说明。日常可以直接让 agent 先阅读它，再执行检查或更新。

### 9.1 只检查，不修改文件

适合先判断哪些 skills 可能需要更新：

```text
请在 D:\framework_warehouse\skills\skills-manage 项目中阅读 AGENTS.md，按照其中规则检查当前 skills 是否有需要更新的内容，只做检查和汇总，不要修改文件。
```

建议先用这个话术做安全检查。它只回答“哪些 skill 可能需要更新”，不直接改文件。

如果检查结果已经告诉你哪些 skills 需要更新，再用明确的名单让 agent 执行：

```text
根据刚才的检查结果，请只更新以下 skills：{skill-a}、{skill-b}。
请继续按照 AGENTS.md 操作：Type 2 外部同步型 skills 使用 pnpm start sync 同步；Type 1 官方文档生成型 skills 根据各自 GENERATION.md 的旧 SHA 对比 sources 中 docs/ 的变化，判断是否影响 skill，如影响则更新 SKILL.md、references 和 GENERATION.md。
不要执行 cleanup -y，不要修改未列出的 skills，最后汇总修改内容。
```

如果只想处理单个官方文档生成型 skill，可以更精确地说：

```text
请根据刚才的检查结果，只更新 skills/{project}。
读取 skills/{project}/GENERATION.md 中记录的旧 SHA，对比 sources/{project} 当前 HEAD 下 docs/ 的变化，判断是否影响该 skill。
如果影响，请更新 SKILL.md、相关 references/*.md 和 GENERATION.md；如果不影响，请说明原因，不要改文件。
不要修改其他 skills。
```

### 9.2 检查并执行更新

适合确认要让 agent 直接处理更新时使用：

```text
请在 D:\framework_warehouse\skills\skills-manage 项目中阅读 AGENTS.md，按照其中规则检查并更新 skills：先检查 git 状态和来源更新；Type 2 外部同步型 skills 用 pnpm start sync 同步；Type 1 官方文档生成型 skills 根据 GENERATION.md 的旧 SHA 对比 sources 中 docs/ 的变化，判断是否影响 skill，如影响则更新 SKILL.md、references 和 GENERATION.md。不要执行 cleanup -y，最后汇总修改内容。
```

建议：

- 不确定是否要改文件时，先使用“只检查”话术。
- 官方文档型 skill 文档量较大时，优先一次处理一个 skill。
- 执行更新时明确说“不要执行 cleanup -y”。

### 9.3 官方文档生成型 skill 更新

官方文档更新后，脚本不会自动重新生成 skill。需要 agent 参与判断和改写。

推荐流程：

1. 检查更新：

```bash
pnpm start check
```

2. 拉取 submodule 更新：

```bash
pnpm start sync
```

3. 查看当前 skill 记录的旧 SHA：

```text
skills/{project}/GENERATION.md
```

4. 只确认官方文档是否有变化，不必手动阅读完整 diff：

```bash
cd sources/{project}
git diff --name-only {old-sha}..HEAD -- docs/
```

如需查看变化规模，可以使用：

```bash
git diff --stat {old-sha}..HEAD -- docs/
```

这一步的目的只是判断 `GENERATION.md` 记录的旧 SHA 到当前 HEAD 之间，官方文档是否发生变化。具体差异是否影响 skill，交给 agent 分析。

5. 让 agent 根据差异判断并更新 skill：

```text
请根据 AGENTS.md 更新 skills/{project}。
旧 SHA 见 skills/{project}/GENERATION.md。
请对比 sources/{project} 中旧 SHA 到 HEAD 的 docs/ 变化，
判断这些变化是否影响 skill。
如果影响，请更新受影响的 SKILL.md 和 references/*.md，
最后更新 GENERATION.md 的 Git SHA；
如果不影响，请说明无需更新的原因，不要改文件。
```

重点：

- Type 1 更新是“理解文档变化后再加工”。
- 不能只靠脚本复制。
- 需要模型判断哪些内容应该写进 skill。

### 9.4 外部 skill 同步型更新

外部同步型 skill 更新后，处理更简单。

推荐流程：

```bash
pnpm start check
pnpm start sync
```

`sync` 会：

- 更新 submodules。
- 从 `vendor/{project}/skills/{skill-name}/` 复制到 `skills/{output-name}/`。
- 更新 `SYNC.md`。
- 复制许可证文件。

重点：

- Type 2 更新是“镜像同步”。
- 不建议本地手改同步来的 skill。
- 如果确实要改，优先改上游项目。

### 9.5 手写 skill 更新

手写 skill 没有外部自动来源，更新时只需要维护 `skills/{name}/` 中的内容。

注意：

- 手写 skill 必须登记在 `meta.ts` 的 `manual` 中。
- 修改后检查 `SKILL.md` 是否仍然简洁。
- 详细规则、案例、资料优先放入 `references/`。
- 如果 skill 已经变成固定流程，可以考虑补充 `scripts/`。

## 10. cleanup 的作用和风险

`cleanup` 会根据 `meta.ts` 清理多余内容。

它会删除两类东西：

- `.gitmodules` 中存在，但不在 `meta.ts` 中的 submodule。
- `skills/` 中存在，但不在 `meta.ts` 中的 skill 目录。

例如，如果你从 `meta.ts` 删除了某个项目，再执行：

```bash
pnpm start cleanup
```

它可能会提示删除：

```text
sources/{project}
skills/{project}
```

如果执行：

```bash
pnpm start cleanup -y
```

会跳过确认，直接删除。

安全建议：

- 第一次运行不要加 `-y`。
- 手写 skill 一定要加入 `manual`。
- 想保留某个 skill，就不要从 `meta.ts` 删除对应配置。
- 执行前先检查 git 状态。

```bash
git status
```

## 11. 推荐维护节奏

### 日常只检查

优先使用 agent 话术：

```text
请在 D:\framework_warehouse\skills\skills-manage 项目中阅读 AGENTS.md，按照其中规则检查当前 skills 是否有需要更新的内容，只做检查和汇总，不要修改文件。
```

或手动执行：

```bash
pnpm start check
```

### 同步外部 skills

```bash
pnpm start sync
```

完成后检查：

```text
skills/{output-name}/SYNC.md
```

### 更新官方文档生成型 skill

推荐一次处理一个 skill：

```text
check -> sync -> 查看 GENERATION.md -> name-only/stat 确认文档变化 -> agent 判断并更新 -> 更新 GENERATION.md
```

### 新增来源后

```bash
pnpm start init
pnpm start sync
```

然后根据 skill 类型执行生成、同步或手写维护。

### 清理废弃项

```bash
pnpm start cleanup
```

不要在不确认删除范围时使用：

```bash
pnpm start cleanup -y
```

## 12. 推荐使用原则

- 把 `meta.ts` 当成唯一配置来源。
- Type 1 适合官方文档生成，需要 agent。
- Type 2 适合已有 skills 同步，主要靠脚本。
- Type 3 适合个人或团队规范，记得加入 `manual`。
- `SKILL.md` 保持简洁，详细内容放 `references/`。
- 不要把所有文档原文塞进 skill，要提炼成 agent 能直接执行的规则、模式和示例。
- 每次更新都记录来源 SHA，方便下次 diff。
- 检查和更新可以交给 agent，但要明确“只检查”还是“允许修改”。
- 在大规模 cleanup、sync、重新生成前，先确认 git 状态。

## 13. 一句话记忆

这个项目的关键不是“写一个 skill”，而是用 `meta.ts + submodules + sync/generate 流程` 维护一整套可更新、可追踪、可组合的 skills 集合。
