---
name: writing-plan
description: 基于已确认的 spec 生成执行计划，包含任务分解、实现顺序和验收标准。
agent: agent
tools: ['search', 'read_file', 'create_file', 'replace_string_in_file']
---

# 编写执行计划

## 前置条件

`docs/product-specs/{feature}.yaml` 已存在且 status 为 CONFIRMED。

## 流程

1. 读取对应的 spec.yaml
2. 分解为实现步骤（每个任务可独立完成）
3. 生成 `docs/exec-plans/active/{feature}.md`

## 输出格式

```markdown
# Execution Plan: {feature-name}

Spec: docs/product-specs/{feature}.yaml

## Tasks

### Task 1: {名称}
- [ ] 描述
- Verifies: REQ-001
- Files: src/...

### Task 2: {名称}
- [ ] 描述
- Verifies: REQ-002
- Files: src/...

## Order

Task 1 → Task 2 → ...

## Decision Log

| Date | Decision | Reason |
|------|----------|--------|
```

## 规则

- 每个 task 必须映射到 spec 中的 requirement
- plan 可以在实现过程中调整（不像 spec 不可变）
- 完成后将 plan 移至 `docs/exec-plans/completed/`
