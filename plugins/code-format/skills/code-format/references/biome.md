# Biome 配置

Biome 是新一代前端工具链，格式化 + lint 一站式解决方案。

## 规则说明

| 规则 | 值 | 说明 |
|------|-----|------|
| indentStyle | space | 缩进方式：空格 |
| indentWidth | 2 | 缩进：2 空格 |
| lineWidth | 110 | 行宽：110 字符 |
| quoteStyle | single | 引号：单引号 |
| trailingCommits | es5 | 末尾逗号：ES5 有效位置 |
| semicolons | always | 分号：始终添加 |

---

# Biome 自动格式化配置

## 安装依赖

```bash
npm install -D @biomejs/biome
```

## 配置 biome.json

```json
{
  "$schema": "https://biomejs.dev/schemas/1.9.0/schema.json",
  "organizeImports": {
    "enabled": true
  },
  "linter": {
    "enabled": true,
    "rules": {
      "recommended": true
    }
  },
  "formatter": {
    "enabled": true,
    "indentStyle": "space",
    "indentWidth": 2,
    "lineWidth": 110,
    "quoteStyle": "single",
    "trailingCommas": "es5",
    "semicolons": "always"
  }
}
```

## 配置 package.json

```json
{
  "scripts": {
    "format": "biome format --write .",
    "lint": "biome lint .",
    "check": "biome check --write ."
  }
}
```

## 配置 lint-staged

```json
{
  "lint-staged": {
    "*.{js,ts,jsx,tsx,vue}": ["biome check --write --no-errors-on-unmatched"]
  }
}
```

## 初始化 husky

```bash
# 初始化 husky
npx husky install

# 添加 pre-commit hook
npx husky add .husky/pre-commit "npx lint-staged"
```

## .husky/pre-commit

```bash
#!/usr/bin/env sh
. "$(dirname -- "$0")/_/husky.sh"

npx lint-staged
```

## 验证

```bash
# 本地测试
npx lint-staged

# 格式化检查
npm run format

# 代码检查（格式化 + lint）
npm run check

# 提交测试
git commit -m "test"
```

## 常用命令

| 命令 | 说明 |
|------|------|
| `biome format --write .` | 格式化代码 |
| `biome lint .` | 检查 lint 问题 |
| `biome check --write .` | 格式化 + lint 修复 |
| `biome check .` | 仅检查不修改 |
