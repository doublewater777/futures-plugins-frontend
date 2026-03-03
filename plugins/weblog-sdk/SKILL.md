---
name: weblog-sdk
description: 埋点上报 SDK 使用指南。当用户需要上报埋点时触发，包括：如何使用 @thsf2e/weblog 进行埋点上报、setConfig 配置、report 上报方法、SSR 环境支持、CDN 引用、组件自动拼接等功能。
---

# Weblog SDK 使用指南

## 快速开始

### npm 引用

```javascript
import { setConfig, report } from '@thsf2e/weblog';

// 国内上报
setConfig({ appKey: 'xxxx' })

report({
  id: 'ths_xxx_page_element_action',
  action: 'click',
  logmap: { /* 自定义字段 */ }
})

// 海外上报
setConfig({
  appKey: 'xxxx',
  domain: 'stat.ainvest.com'
})
```

### SSR 环境

```javascript
import { setConfig, report, reportLeft } from '@thsf2e/weblog';

setConfig({
  appKey: 'xxxx',
  userAgent: context.userAgent,  // 必传
  deviceId: 'xxxx',              // 必传
  userId: 'xxxx',                // 必传
  domain: 'stat.ainvest.com'    // 海外必传
})

// 页面跳转前调用
reportLeft()
```

### CDN 引用

```html
<script src="//s.thsi.cn/cb?cd/weblog/0.0.5/weblog.js"></script>

<script>
window.weblog.setConfig({ appKey: 'xxxx' })
window.weblog.report({ id: 'xxx', action: 'click' })
</script>
```

### 组件自动拼接

```javascript
// 页面设置前缀
setConfig({
  appKey: 'xxxx',
  logPrefix: 'ths_tgpt_askdetail'  // 自动拼接为三段式
})

// 组件内只需二段式
report({ id: 'pageBot_checkAgree', action: 'click' })
// 实际发送: ths_tgpt_askdetail_pageBot_checkAgree
```

### external 配置

```javascript
// vue.config.js
configureWebpack: {
  externals: { '@thsf2e/weblog': 'weblog' }
}

// vite.config.js
build: {
  rollupOptions: {
    external: ['@thsf2e/weblog'],
    plugins: [externalGlobals({ '@thsf2e/weblog': 'weblog' })]
  }
}
```

## 详细 API

从 `references/api.md` 获取完整 API 文档：
- 常用 appKey 列表
- setConfig 完整参数
- report 完整参数
- reportLeft 使用场景
- action 类型说明

## 触发场景

- 用户说"添加埋点"
- 用户说"怎么上报"
- 用户说"weblog 配置"
- 用户提到埋点 ID、action、logmap
