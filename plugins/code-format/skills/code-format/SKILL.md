---
name: code-format
description: 前端代码格式化规范。当需要配置代码自动格式化、pre-commit hook 时触发。支持 Prettier 和 Biome 两种方案。
---

# 代码格式化规范

团队统一的代码格式化配置，提交时自动格式化。支持两种方案：

- **Biome**（推荐）- 新一代工具，更快更全
- **Prettier** - 传统方案，生态成熟

## 快速使用

根据用户选择从对应文件获取配置：

- `references/biome.md` - Biome 完整配置（推荐）
- `references/pre-commit.md` - Prettier 完整配置

## 核心规则（Biome 默认值）

| 规则 | 值 |
|------|-----|
| indentStyle | 空格 |
| indentWidth | 2 |
| lineWidth | 110 |
| quoteStyle | 单引号 |
| trailingCommas | ES5 |

## 触发场景

- 用户说"配置自动格式化"
- 用户说"配置 pre-commit"
- 用户说"配置 husky"
- 用户说"配置 Biome"
- 用户说"配置 Prettier"
- 新项目初始化时

## 选择流程

1. 如果用户没有指定方案，优先推荐 Biome
2. 如果用户明确要求 Prettier，使用 Prettier 方案
3. 如果项目已有 Prettier 生态，尊重现有选择
