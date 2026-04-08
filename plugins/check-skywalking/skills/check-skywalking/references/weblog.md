# 检查 SkyWalking / SkyWalking-tool / Weblog 引入

检查项目中是否引入了以下三类资源：
- **SkyWalking**：APM 前端监控脚本（如 `skywalking.*.min.js`）
- **SkyWalking-tool**：SkyWalking 工具脚本（路径含 `skywalking-tool`）
- **Weblog**：前端日志/上报脚本（路径含 `weblog`）

## 版本要求

检查时需同时判断版本是否符合要求：

| 资源 | 版本要求 | 判断方式 |
|------|----------|----------|
| **SkyWalking** | 1.1.25 及以上 | 从脚本 URL/文件名解析版本号（如 `skywalking.1.1.25.min.js` → 1.1.25），满足 ≥ 1.1.25 即合规 |
| **SkyWalking-tool** | 必须为 `index.1.0.0.min.js` | 路径中须包含 `skywalking-tool` 且脚本文件名为 `index.1.0.0.min.js` |
| **Weblog** | 必须为 `/0.0.5/weblog.js` | 路径中须包含 `/0.0.5/weblog.js`（版本 0.0.5） |

- **SkyWalking 版本解析**：匹配 `skywalking.(\d+)\.(\d+)\.(\d+)` 等形式，按主.次.修订比较，低于 1.1.25 则判定为不符合。
- **SkyWalking-tool / Weblog**：仅当路径与上述要求完全一致时判定为符合。

## Weblog 初始化判断

Weblog 视为已完成初始化当且仅当在代码中调用了：

`window.weblog.setConfig({ appKey: '9099f8be61' });`

- 在入口或初始化逻辑（如 `main.js`、`init.js`、`app.js` 等）中搜索 `weblog.setConfig`。
- 若存在对 `weblog.setConfig` 的调用且传入的配置中包含 `appKey: '9099f8be61'`，则判定为**已完成初始化**；仅引入 weblog 脚本但未调用 `setConfig` 或 appKey 不一致则判定为**未完成初始化**。

## SkyWalking-tool 初始化判断

SkyWalking-tool 视为已完成初始化当且仅当在代码中调用了 `window.skywalkingTool.register(...)`（参考 `init.js` 中 `initFemonitor` 的实现），且 **`group` 必须为 `'futures'`**：

- 在 `window.skywalkingTool` 存在时，调用 `skywalkingTool.register` 并传入配置对象，至少包含：`rate`、`service`、**`group: 'futures'`**、`pagePath`、以及可选的 `hooks`（如 `reportHook`、`filterHook`）等。
- 在入口或初始化逻辑中搜索 `skywalkingTool.register`。若存在对该方法的调用、传入有效配置，且配置中 **`group` 为 `'futures'`**，则判定为**已完成初始化**；未调用 `register`、或 `group` 非 `'futures'`，则判定为**未完成初始化**。

## 检查步骤

1. **HTML 中的 script/link**
   - 在 `*.html`、`public/**/*.html`、`index.html` 中搜索：
     - `skywalking`（匹配 SkyWalking 主脚本）
     - `skywalking-tool`（匹配 SkyWalking 工具）
     - `weblog`（匹配 Weblog 脚本）
   - 使用 grep 在 `src`、`href` 或整行内容中匹配上述关键词。

2. **依赖声明**
   - 在 `package.json` 的 `dependencies`、`devDependencies` 中查找包名是否包含：
     - `skywalking`、`skywalking-tool`、`weblog`。

3. **构建/配置注入**
   - 若项目通过配置注入脚本（如 `vue.config.js`、`vite.config.*`、`index.html` 模板），在对应文件中搜索上述关键词。

4. **Weblog 初始化**
   - 在 `src` 下搜索 `weblog.setConfig`，确认是否调用了 `window.weblog.setConfig({ appKey: '9099f8be61' });` 或等效代码（含 appKey `'9099f8be61'`）。

5. **SkyWalking-tool 初始化**
   - 在 `src` 下搜索 `skywalkingTool.register`，确认是否调用了 `window.skywalkingTool.register(...)` 且配置中 **`group` 为 `'futures'`**（如 `group: 'futures'`）。

## 输出格式

检查完成后，按以下模板输出报告：

```markdown
## SkyWalking / SkyWalking-tool / Weblog 引入检查报告

| 资源 | 是否引入 | 版本是否符合 | 位置/说明 |
|------|----------|--------------|------------|
| SkyWalking | 是/否 | 是/否/— | （脚本 URL 或文件:行号，若引入则写出版本号） |
| SkyWalking-tool | 是/否 | 是/否/— | （须为 index.1.0.0.min.js） |
| Weblog | 是/否 | 是/否/— | （须为 /0.0.5/weblog.js） |
| **Weblog 初始化** | — | 是/否 | （若已引入 weblog：是否调用了 weblog.setConfig({ appKey: '9099f8be61' })，并注明文件:行号） |
| **SkyWalking-tool 初始化** | — | 是/否 | （若已引入 skywalking-tool：是否调用了 skywalkingTool.register(...)，并注明文件:行号） |
```

- **是否引入**：在 HTML 的 script/link、package.json 或构建配置中任一处出现即填「是」。
- **版本是否符合**：按「版本要求」判断；未引入时填「—」。Weblog/SkyWalking-tool 初始化行仅在已引入对应资源时填写「是/否」，未引入时填「—」。
- **位置/说明**：列出具体文件路径、行号或脚本 URL；若引入则注明实际版本或路径以便核对。初始化行需注明调用 `weblog.setConfig` 或 `skywalkingTool.register` 的文件与行号。

## 搜索建议

- HTML：优先检查 `public/index.html`、`index.html` 及模板中 `<script src="...">`、`<link href="...">`。
- 关键词区分：`skywalking-tool` 与 `skywalking` 分别搜索，避免漏掉仅引入其一的情况。
- 初始化：用 `weblog.setConfig`、`skywalkingTool.register` 在 `src` 下搜索，常见于 `init.js`、`main.js` 等入口或监控初始化函数（如 `initFemonitor`）。
- 若未找到任何匹配，在报告中注明「未发现」并列出已检查的文件/目录。

---

## 修复功能

当检查发现资源未引入、版本不符或初始化缺失时，可进行修复。

### CDN 地址

| 资源 | CDN 地址 |
|------|----------|
| **SkyWalking** | `//s.thsi.cn/hxapp/m/base/js/skywalking.1.1.25.min.js` |
| **SkyWalking-tool** | `//s.thsi.cn/cd/futures-frontend-fu-cdn/common/skywalking-tool/index.1.0.0.min.js` |
| **Weblog** | `//s.thsi.cn/cd/weblog/0.0.5/weblog.js` |

### 修复步骤

#### 1. 修复 HTML 中的脚本引入

若 `public/index.html`（或主 HTML 模板）中**未引入**或**版本不符**，在 `<head>` 中插入/替换以下脚本（按顺序）：

```html
<script src="//s.thsi.cn/hxapp/m/base/js/skywalking.1.1.25.min.js"></script>
<script type="text/javascript" src="//s.thsi.cn/cd/futures-frontend-fu-cdn/common/skywalking-tool/index.1.0.0.min.js" crossorigin="anonymous" defer></script>
<script charset="utf-8" src="//s.thsi.cn/cd/weblog/0.0.5/weblog.js" crossorigin="anonymous" defer></script>
```

- 若旧版本存在，替换为上述 CDN 地址。
- 建议放在 `<head>` 的靠前位置（如 viewport meta 之后）。

#### 2. 修复 Weblog 初始化

若 Weblog 已引入但**未完成初始化**（未调用 `weblog.setConfig` 或 appKey 不正确），在入口初始化逻辑（如 `src/utils/init.js` 的 `initFemonitor` 函数）中添加：

```javascript
if (window.weblog) {
  window.weblog.setConfig({ appKey: '9099f8be61' });
}
```

#### 3. 修复 SkyWalking-tool 初始化

若 SkyWalking-tool 已引入但**未完成初始化**（未调用 `skywalkingTool.register` 或 `group` 非 `'futures'`），在入口初始化逻辑中添加以下代码（参考 `init.js` 中 `initFemonitor` 实现）：

```javascript
const NUM_M_1 = -1;
const FILTER_MESSAGES = [
  'Script error.',
  '接口响应正常',
  'TypeError: undefined is not an object (evaluating \'callbacks.length\')',
];

if (window.skywalkingTool) {
  window.skywalkingTool.register({
    rate: 1,
    service: '<项目名称>',           // 替换为实际项目名，如 'futures-frontend-firm-traderace'
    group: 'futures',
    pagePath: `${window.location.pathname}${window.location.search}`,
    hooks: {
      // 上报拦截器：过滤 FILTER_MESSAGES 中的日志
      reportHook: ({ data }) => {
        let index;
        if (Array.isArray(data)) {
          index = data.findIndex(({ message }) =>
            FILTER_MESSAGES.includes(message)
          );
        }
        if ('undefined' !== typeof index && index > NUM_M_1) {
          return false;
        }
        return true;
      },
      // 日志过滤器
      filterHook: data => !FILTER_MESSAGES.includes(data.message),
    },
  });
}
```

- **`service`**：替换为当前项目名称（如 `'futures-frontend-firm-traderace'`）。
- **`group`**：必须为 `'futures'`。
- **`FILTER_MESSAGES`**：需过滤的日志消息列表，可按需增减。
- **`reportHook`**：上报拦截器，若 `data` 数组中存在需过滤的消息则返回 `false` 阻止上报。
- **`filterHook`**：日志过滤器，过滤单条日志。

### 修复报告

修复完成后，在原检查报告基础上追加「修复结果」列或单独输出修复说明：

```markdown
## 修复结果

| 资源 | 修复操作 | 修复位置 |
|------|----------|----------|
| SkyWalking | 已插入/替换脚本 | public/index.html:XX |
| SkyWalking-tool | 已插入/替换脚本 | public/index.html:XX |
| Weblog | 已插入/替换脚本 | public/index.html:XX |
| Weblog 初始化 | 已添加 setConfig 调用 | src/utils/init.js:XX |
| SkyWalking-tool 初始化 | 已添加 register 调用 | src/utils/init.js:XX |
```
