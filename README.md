# harness-pro

基于 [charlesanim/harness-engineering](https://github.com/charlesanim/harness-engineering) 增强的 Agent Skill 套件。在 charlesanim 的基础设施层（架构边界测试、CI、GC）之上，增加了 Agent Hooks、Spec-Driven TDD 工作流和 Copilot 原生定制文件。

## 组成

| 文件 | 类型 | 用途 |
|------|------|------|
| `SKILL.md` | Agent Skill | 一键搭建 harness engineering 环境（Phase 0-8） |
| `skills/make-something/SKILL.md` | Agent Skill | Spec-Driven TDD 开发流程 |
| `prompts/writing-spec.prompt.md` | Prompt File | 编写功能规格 |
| `prompts/writing-plan.prompt.md` | Prompt File | 编写执行计划 |
| `instructions/spec-guard.instructions.md` | Instructions | 保护已确认的 spec 文件不被修改 |

## 基于 charlesanim/harness-engineering (MIT)

Phase 0-7 的核心设计来自 charlesanim，本项目新增：

- **Phase 2 扩展**：docs/ 增加 exec-plans、product-specs、references、security 分类
- **Phase 2 增强**：Golden Principles 同步生成 `.instructions.md` 文件
- **Phase 8 (新增)**：Agent Hooks — Stop 事件测试门控
- **Spec-TDD 工作流**：writing-spec → writing-plan → make-something
- **Copilot 原生文件**：.prompt.md / .instructions.md / SKILL.md 标准格式

## 兼容性

| Agent | 支持 |
|-------|------|
| VS Code Copilot | ✅ 完整支持（hooks、skills、prompts、instructions） |
| Claude Code | ✅ 完整支持 |
| GitHub Codex | ✅ Skill 标准 |
| Cursor | ✅ 基础支持 |
