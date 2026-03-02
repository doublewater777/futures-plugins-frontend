---
name: code-format
description: 前端代码格式化规范。当需要配置代码自动格式化、pre-commit hook 时触发。包含 Prettier 团队配置和 husky + lint-staged 自动格式化集成指南。
---

# 代码格式化规范

团队统一的 Prettier 配置，提交时自动格式化。

## 快速使用

从 `references/pre-commit.md` 获取完整配置：
- Prettier 团队配置
- husky + lint-staged 集成
- 安装和验证步骤

## 核心规则

- 单引号 `''`
- 分号 `;`
- 2 空格缩进
- 110 字符行宽

## 触发场景

- 用户说"配置自动格式化"
- 用户说"配置 pre-commit"
- 用户说"配置 husky"
- 新项目初始化时
