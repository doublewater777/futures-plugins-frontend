# 前端监控 SDK API 文档

---

## register 参数

| 参数 | 类型 | 说明 |
|------|------|------|
| rate | number | 采集率 0.1-1 |
| service | string | 业务标识，如 `futures-frontend-thshome-pod` |
| pagePath | string | 页面路径 |
| enableSPA | boolean | 是否开启 SPA 处理 |
| group | string | 部门标识，默认 `futures` |
| subGroup | string | 子部门，如 `webpage`、`components` |
| hooks.reportHook | function | 上报拦截器，返回 true 允许上报，false 禁止 |
| hooks.filterHook | function | 日志过滤器，返回 true 允许采集，false 过滤 |

---

## report 参数

| 参数 | 类型 | 说明 |
|------|------|------|
| err | Error/string/object | 异常信息 |
| category | string | 上报类型：js/ajax/resource |
| grade | string | 异常级别：Error/Warning/Info |
| timeout | number | 上报间隔，默认 10s，设为 1 可立即上报 |
| content | string/object | 拓展上下文 |

---

## category 说明

| 类型 | 适用场景 |
|------|----------|
| js | JavaScript 错误、Promise 拒绝 |
| ajax | 接口请求错误 |
| resource | 静态资源加载失败 |

---

## grade 说明

| 级别 | 适用场景 |
|------|----------|
| Error | 实际错误，需要关注 |
| Warning | 警告，不影响功能 |
| Info | 信息日志 |

---

## perform 参数

| 参数 | 类型 | 说明 |
|------|------|------|
| pagePath | string | 新路由路径 |

---

## 完整示例

```javascript
const initFemonitor = () => {
    if (isDev) return;

    if (window.skywalkingTool) {
        window.skywalkingTool.register({
            rate: 1,
            service: 'futures-frontend-xxx-pod',
            pagePath: window.location.pathname,
            group: 'futures',
            subGroup: 'webpage',
            enableSPA: true,
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

// 手动上报
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

// 路由切换
window.skywalkingTool.perform({
    pagePath: location.hash.replace('#', '')
})
```
