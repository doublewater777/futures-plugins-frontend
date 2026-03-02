# Prettier 配置

```json
{
  "trailingComma": "es5",
  "semi": true,
  "singleQuote": true,
  "printWidth": 110,
  "tabWidth": 2,
  "useTabs": false,
  "bracketSpacing": true,
  "arrowParens": "avoid"
}
```

## 规则说明

| 规则 | 值 | 说明 |
|------|-----|------|
| trailingComma | es5 | 末尾逗号：ES5 有效位置 |
| semi | true | 分号：始终添加分号 |
| singleQuote | true | 引号：使用单引号 |
| printWidth | 110 | 行宽：最大 110 字符 |
| tabWidth | 2 | 缩进：2 空格 |
| useTabs | false | 制表符：不使用 Tab |
| bracketSpacing | true | 对象空格：对象括号内加空格 |
| arrowParens | avoid | 箭头函数括号：能省略则省略 |

---

# Pre-commit 自动格式化配置

## 安装依赖

```bash
npm install -D husky lint-staged prettier
```

## 配置 package.json

```json
{
  "prettier": {
    "trailingComma": "es5",
    "semi": true,
    "singleQuote": true,
    "printWidth": 110,
    "tabWidth": 2,
    "useTabs": false,
    "bracketSpacing": true,
    "arrowParens": "avoid"
  },
  "lint-staged": {
    "*.{js,vue,ts,tsx,jsx}": ["prettier --write"]
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

## .git/hooks/pre-commit（自动生成）

```bash
#!/usr/bin/env sh
. "$(dirname -- "$0")/_/husky.sh"

npx lint-staged
```

## 验证

```bash
# 本地测试
npx lint-staged

# 提交测试
git commit -m "test"
```
