---
name: make-something
description: |
  按 Spec-Driven TDD 流程实现功能。读取已确认的 spec 和 plan，
  先写测试（全部失败），再写实现（直到全部通过）。
  测试一旦写完不可修改。
---

# Spec-Driven TDD 实现

## 前置条件

1. `docs/product-specs/{feature}.yaml` 存在且 CONFIRMED
2. `docs/exec-plans/active/{feature}.md` 存在

## Step 1: 写测试

- 读取 spec.yaml 中的 requirements
- 为每个 requirement 写测试
- 测试文件路径: `tests/{feature}.test.{ext}` 或按项目约定
- 测试写完后**不可修改**

## Step 2: 验证全红

运行测试，确认所有测试都失败（红色）。
如果有测试通过，说明测试写得不对——需要修复测试。

## Step 3: 写实现

- 按 plan 的任务顺序逐个实现
- 每完成一个任务，跑一次测试
- 重复直到所有测试通过

## Step 4: 清理

- 确认所有测试通过
- 确认 lint 通过
- 确认 type-check 通过（如适用）
- 将 plan 移至 `docs/exec-plans/completed/`

## 关键约束

- **不可修改测试文件来让测试通过**
- 如果测试写错了 → 说明 spec 有问题 → 用 `/writing-spec` 重新确认
- 实现代码可以自由修改
- plan 可以随时调整任务顺序

## 参考

- 架构边界: 见 `docs/architecture/LAYERS.md`
- 编码约定: 见 `docs/golden-principles/`
- 项目导航: 见 `AGENTS.md`
