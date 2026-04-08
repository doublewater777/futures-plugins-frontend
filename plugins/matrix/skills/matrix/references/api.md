# Matrix API 文档

@fu/matrix 工具库完整 API 参考。

---

## 字符串转换

```ts
convertToCamelCase<T extends Record<string, any>>(obj: T): T
convertToUnderscore<T extends Record<string, any>>(obj: T): T
```

将对象属性名在驼峰和下划线格式之间转换。

---

## 数字格式化

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

**示例:**
```ts
formatNumber(1234.56)                           // "1,234.56"
formatNumber(1234.56, { precision: 0 })         // "1,235"
formatNumber(1234.56, { prefix: '¥' })           // "¥1,234.56"
formatPercent("50%")                             // "50.00%"
formatPercent(0.5, { precision: 0 })            // "50%"
```

---

## 类型判断

```ts
isEmpty(value: any): boolean
isInvalid(value: any): boolean
isNonEmptyArray(value: any): boolean
isPlainObject(value: any): boolean
```

---

## 平台检测

```ts
getPlatform(): 'PC' | 'iPhone' | 'GPhone' | 'Harmony' | 'iPad' | 'AndroidPad' | 'unknown'
isPC(): boolean
isIPhone(): boolean
isGPhone(): boolean
isHarmony(): boolean
```

---

## 系统检测

```ts
getOS(): 'Windows' | 'macOS' | 'Linux' | 'iOS' | 'Android' | 'unknown'
```

---

## 应用环境检测

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

---

## 主题

```ts
isDark(): boolean
initTheme(): void
```

---

## 浏览器操作

```ts
getCookie(name: string): string | null
getUrlParam(name: string, url?: string): string | null
```

---

## 用户信息

```ts
getUserId(): string | null
getUserAvatar(): string | null
getAccount(): string | null
```

---

## 存储

```ts
createLocalStorage(storageKey: string): StorageTool
createSessionStorage(storageKey: string): StorageTool
```

**StorageTool 实例方法:**

| 方法 | 说明 |
|------|------|
| `get<T>(key, isCustomer?)` | 获取值 |
| `set<T>(key, value, isCustomer?)` | 设置值 |
| `rm(key, isCustomer?)` | 删除指定 key |
| `getAll()` | 获取所有数据 |
| `setAll(data)` | 批量设置 |
| `setMultiple(items, isCustomer?)` | 批量设置多个 |
| `clean(isCustomer?)` | 清空所有 |

**示例:**
```ts
const storage = createLocalStorage('myapp');
storage.set('theme', 'dark');
storage.get('theme');          // → 'dark'
storage.setMultiple({ a: 1, b: 2 });
storage.rm('a');
storage.clean();
```

---

## 性能优化

```ts
debounce<T extends (...args: any[]) => any>(fn: T, delay: number): (...args: Parameters<T>) => void
throttle<T extends (...args: any[]) => any>(fn: T, interval: number): (...args: Parameters<T>) => void
```

---

## 生命周期 (App 桥接)

```ts
onShow(callback: () => void): void
onHide(callback: () => void): void
```

---

## 页面跳转 (App 桥接)

```ts
jumpWeb(url: string, title?: string): void
goBack(): void
jumpFenshi(code: string): void
```

---

## 登录认证 (App 桥接)

```ts
login(): void
isLogin(): boolean
checkLogin(cb?: () => void): boolean  // 返回是否已登录，cb 为登录成功回调
```

**示例:**
```ts
if (!isLogin()) {
  login();
}

checkLogin(() => {
  console.log('已登录，执行后续逻辑');
});
```

---

## 版本

```ts
getInnerVersion(): string | null
isHigherInnerVersion(version: string): boolean
```
