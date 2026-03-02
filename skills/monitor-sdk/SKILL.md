---
name: monitor-sdk
description: 前端监控上报 SDK 使用指南。当用户需要接入前端监控、异常上报、skywalking 监控时触发，包括：如何引入监控工具、注册监听器、主动上报异常、路由切换上报等。
---

# 前端监控 SDK 使用指南

## 引入工具包

### CDN 引用

```html
<!-- 放在 <head> 首位 -->
<script src="//s.thsi.cn/cd/futures-frontend-fu-cdn/common/skywalking-tool/index.1.0.0.min.js" crossorigin="anonymous"></script>
```

### 静态资源加 crossorigin

```html
<link href="xxx.css" crossorigin="anonymous">
<script src="xxx.js" crossorigin="anonymous"></script>
```

### Vue 项目配置

```javascript
// vue.config.js
module.exports = {
    crossorigin: 'anonymous',
    // ...
}
```

## 注册监听器 (register)

在项目入口调用，建议在 main.js 中：

```javascript
const initFemonitor = () => {
    if (isDev) return; // 开发环境不监听

    if (window.skywalkingTool) {
        window.skywalkingTool.register({
            rate: 1,                          // 采集率 0.1-1
            service: 'futures-frontend-xxx-pod',  // 业务标识
            pagePath: window.location.pathname,
            group: 'futures',                 // 部门标识
            subGroup: 'webpage',             // 子部门
            enableSPA: true,                 // SPA 模式
            hooks: {
                reportHook: ({ data }) => {
                    return true;
                },
                filterHook: (data) => {
                    return true;
                }
            }
        })
    }
}
initFemonitor();
```

## 主动上报 (report)

手动捕获的异常需要主动上报：

```javascript
export const errorReport = (err, options = {}) => {
    if (window.skywalkingTool) {
        window.skywalkingTool.report({
            err: err,
            category: 'js',
            grade: 'Error',
            timeout: 1,
            ...options
        });
    }
}
```

## 路由切换上报 (perform)

SPA 项目路由切换时调用：

```javascript
window.skywalkingTool.perform({
    pagePath: location.hash.replace('#', '')
})
```

## 详细 API

从 `references/api.md` 获取完整 API 文档：
- register 完整参数
- report 完整参数
- perform 完整参数
- category 和 grade 说明
- 完整代码示例

## 触发场景

- 用户说"添加监控"
- 用户说"异常上报"
- 用户说"skywalking 配置"
- 用户提到 errorReport、perform
