---
name: matrix
description: |
  @fu/matrix is a JavaScript utility library for 同花顺/期货 applications.
  Use when: working with this library functions, questions about available APIs, or needs to implement environment detection, data formatting, browser operations, storage, performance optimization, or client bridging.
  Triggers: user asks to use Matrix functions, wants to know what functions are available, or asks about string/number formatting, platform detection, storage, debounce/throttle, login/auth, navigation.
---

# @fu/matrix

JavaScript 工具库，用于同花顺/期货应用。

**详细文档:** https://futures-test.10jqka.com.cn/matrix/docs/api/

> **重要提示:** 使用任何函数前，请先阅读上述详细文档确认具体用法、参数含义和返回值结构。

## 安装

```bash
npm install @fu/matrix

pnpm add @fu/matrix
```

或 CDN:

```html
<script src="https://futures-test.10jqka.com.cn/matrix/lib/matrix-latest.min.js"></script>
```

## Window 暴露内容

使用 CDN 引入时，window 上会暴露以下全局对象：

| 全局对象 | 说明 |
|---------|------|
| `window.fuMatrix` | 包含所有工具函数的全局对象（直接调用如 `window.fuMatrix.isIPhone()`） |
| `window.MatrixTools` | Matrix WebMCP Client 实例，支持 `listFunctions()`, `searchFunctions()`, `execute()`, `getFunctionInfo()` 等方法 |
| `window.executeMatrixTool` | 底层全局执行函数，用于 fallback 模式 |

## 使用

```ts
import { isIPhone, formatNumber, debounce } from '@fu/matrix';

// 浏览器全局
const { isIPhone, formatNumber } = window.fuMatrix;
```

---

## 函数签名

### 字符串转换

```ts
convertToCamelCase<T extends Record<string, any>>(obj: T): T
convertToUnderscore<T extends Record<string, any>>(obj: T): T
```

### 数字格式化

```ts
formatNumber(value: number | string, options?: {
  precision?: number;      // 默认 2
  thousandsSeparator?: string;  // 默认 ','
  decimalSeparator?: string;     // 默认 '.'
  prefix?: string;
  suffix?: string;
}): string

formatPercent(value: number | string, options?: {
  precision?: number;      // 默认 2
  thousandsSeparator?: string;
  decimalSeparator?: string;
}): string
```

### 类型判断

```ts
isEmpty(value: any): boolean
isInvalid(value: any): boolean
isNonEmptyArray(value: any): boolean
isPlainObject(value: any): boolean
```

### 平台检测

```ts
getPlatform(): 'PC' | 'iPhone' | 'GPhone' | 'Harmony' | 'iPad' | 'AndroidPad' | 'unknown'
isPC(): boolean
isIPhone(): boolean
isGPhone(): boolean
isHarmony(): boolean
```

### 系统检测

```ts
getOS(): 'Windows' | 'macOS' | 'Linux' | 'iOS' | 'Android' | 'unknown'
```

### 应用环境检测

```ts
isInThsApp(): boolean
isInFuturesApp(): boolean
isInIFindApp(): boolean
isInAInvestApp(): boolean
isInWeixin(): boolean
getApp(): 'ths' | 'futures' | 'ifind' | 'ainvest' | 'weixin' | 'thsPcTy' | 'thsPcYh' | 'futuresPc' | 'kamis' | null
isThsPcTyClient(): boolean
isThsPcYhClient(): boolean
isFuturesPcClient(): boolean
isInKamis(): boolean
```

### 主题

```ts
isDark(): boolean
initTheme(): void
```

### 浏览器操作

```ts
getCookie(name: string): string | null
getUrlParam(name: string, url?: string): string | null
```

### 用户信息

```ts
getUserId(): string | null
getUserAvatar(): string | null
getAccount(): string | null
```

### 存储

```ts
createLocalStorage(storageKey: string): StorageTool
createSessionStorage(storageKey: string): StorageTool

// StorageTool 实例方法:
class StorageTool {
  get<T>(key: string, isCustomer?: boolean): T | null
  set<T>(key: string, value: T, isCustomer?: boolean): void
  rm(key: string, isCustomer?: boolean): void
  getAll(): Record<string, any>
  setAll(data: Record<string, any>): void
  setMultiple(items: Record<string, any>, isCustomer?: boolean): void
  clean(isCustomer?: boolean): void
}
```

### 性能优化

```ts
debounce<T extends (...args: any[]) => any>(fn: T, delay: number): (...args: Parameters<T>) => void
throttle<T extends (...args: any[]) => any>(fn: T, interval: number): (...args: Parameters<T>) => void
```

### 生命周期 (App 桥接)

```ts
onShow(callback: () => void): void
onHide(callback: () => void): void
```

### 页面跳转 (App 桥接)

```ts
jumpWeb(url: string, title?: string): void
goBack(): void
jumpFenshi(code: string): void
```

### 登录认证 (App 桥接)

```ts
login(): void
isLogin(): boolean
checkLogin(cb?: () => void): boolean  // 返回是否已登录，cb 为登录成功回调
```

### 版本

```ts
getInnerVersion(): string | null
isHigherInnerVersion(version: string): boolean
```

---

## 示例

**格式化数字:**
```ts
formatNumber(1234.56)                           // "1,234.56"
formatNumber(1234.56, { precision: 0 })         // "1,235"
formatNumber(1234.56, { prefix: '¥' })           // "¥1,234.56"
formatPercent("50%")                             // "50.00%"
formatPercent(0.5, { precision: 0 })            // "50%"
```

**存储:**
```ts
const storage = createLocalStorage('myapp');
storage.set('theme', 'dark');
storage.get('theme');          // → 'dark'
storage.setMultiple({ a: 1, b: 2 });
storage.rm('a');
storage.clean();
```

**登录校验:**
```ts
if (!isLogin()) {
  login();
}

// 或使用回调
checkLogin(() => {
  console.log('已登录，执行后续逻辑');
});
```

**环境检测:**
```ts
if (isInFuturesApp()) {
  // 在期货App中
}

if (isDark()) {
  document.body.classList.add('dark');
}
```
