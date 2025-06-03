+++
date = '2025-06-03T15:56:41+08:00'
draft = false
title = 'Learn_git'
categories = ["工具"]
tags = ["git"]
+++


# 原则

## 好的commit

好的commit包含类型、作用域、主题、正文

```git
fix（Login）：修复登录页面加载失败的问题
因为返回的数据结构变了，导致解析失败，这里调整了解析逻辑。
```

类型是fix，说明这次的commit是在修bug
作用域是login，说明改动了login模块
主题说明这次commit是为了修复登陆页面
正文说明具体的修改细节

| 类型      | 说明                                 |
|-----------|--------------------------------------|
| feat      | 新功能                               |
| fix       | 修 bug                               |
| refactor  | 重构，不新增功能，也不修 bug          |
| docs      | 改文档，比如 README                  |
| style     | 改代码风格，不影响功能               |
| test      | 加测试、改测试                       |
| chore     | 杂项，比如改 .gitignore、构建脚本     |
| perf      | 性能优化                             |
| ci        | CI/CD 相关改动                       |
| build     | 改构建系统或依赖                     |
| revert    | 回滚某个提交                         |

test


行内数学公式：$a^2 + b^2 = c^2$。

块公式，

$$
a^2 + b^2 = c^2
$$

<div>
$$
\boldsymbol{x}_{i+1}+\boldsymbol{x}_{i+2}=\boldsymbol{x}_{i+3}
$$
</div>
