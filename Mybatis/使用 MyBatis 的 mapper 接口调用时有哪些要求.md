---
markmap:
  colorFreezeLevel: 2
---

# 使用 MyBatis 的 Mapper 接口调用要求

## 方法名与 id 一致

- 接口方法名必须与 XML 中 SQL 的 `id` 完全匹配

## namespace 正确

- namespace 必须是接口的全限定类名（含包名）

## 参数类型匹配

- 方法输入参数类型需与 `parameterType` 一致
- 多个参数可使用 `@Param` 注解处理

## 返回值类型匹配

- 方法返回类型需与 `resultType` 或 `resultMap` 对应的类型一致

## 面试速记

- **Q1: Mapper 方法名要求？**
  - 必须与 XML 中 SQL 的 `id` 一致
- **Q2: namespace 要求？**
  - 接口全限定类名（含包名）
- **Q3: 参数类型要求？**
  - 与 `parameterType` 对应，多参数可用 `@Param`
- **Q4: 返回值类型要求？**
  - 与 `resultType` 或 `resultMap` 对应
- **Q5: 常见错误？**
  - 方法名与 id 不一致 → 找不到 SQL
  - namespace 不匹配 → Mapper 加载失败
  - 参数类型不对 → 参数绑定异常
  - 返回值类型不匹配 → 类型转换异常
