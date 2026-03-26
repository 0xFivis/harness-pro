---
name: Spec Guard
description: 禁止修改已确认的 spec 和由 spec 生成的测试文件
applyTo: 'docs/product-specs/**'
---

# Spec Guard

以下文件一旦创建/确认，**禁止修改**：

1. `docs/product-specs/*.yaml`（status 为 CONFIRMED 后）
2. 由 `/make-something` 生成的测试文件

## 原因

spec 是不可变契约——定义"做什么"。
测试是验收标准——定义"怎么算完成"。

## 如需修改

1. 创建新版本 spec（如 `feature-v2.yaml`）
2. 与用户重新确认
3. 重新生成测试
