---
name: writing-spec
description: 交互式编写功能规格 spec.yaml。与用户确认需求后生成不可变规格文件。
agent: agent
tools: ['search', 'read_file', 'create_file', 'replace_string_in_file']
---

# 编写功能规格

## 流程

1. 询问用户功能名称和描述
2. 确认核心需求（每条必须可验证）
3. 确认接口定义（如有）
4. 确认约束条件
5. 生成 `docs/product-specs/{feature-name}.yaml`
6. 让用户确认，确认后 status 变为 CONFIRMED

## 输出格式

```yaml
name: feature-name
description: 一句话描述这个功能做什么
status: CONFIRMED  # DRAFT → CONFIRMED 后不可改

requirements:
  - id: REQ-001
    description: 需求描述
    verifiable: true

api:
  - method: POST
    path: /api/example
    request: { field: type }
    response: { field: type }

constraints:
  - 约束条件
```

## 规则

- spec.yaml 一旦 CONFIRMED 就**不可修改**
- 每条 requirement 必须是可验证的（能写成测试）
- 如果 spec 需要修改，创建新版本（v2）而不是改原文件
