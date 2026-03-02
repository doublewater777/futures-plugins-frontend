# Weblog SDK API 文档

## 常见 appKey

| 应用 | appKey |
|------|--------|
| 期货通 | `9099f8be61` |
| 期货通PC | `3ecadbf9f2` |
| 手炒免费版 | `ce19ea099b` |
| 手炒至尊版 | `2385235c20` |
| iFinDapp | `87cdda1fcd` |

---

## setConfig 参数

| 参数 | 类型 | 必传 | 说明 |
|------|------|------|------|
| appKey | string | 是 | 业务方申请的 appKey |
| debug | boolean | 否 | 开启后控制台打印埋点 |
| logPrefix | string | 否 | 埋点前缀，自动拼接三段式 |
| domain | string | 否 | 海外上报域名，默认 cbasspider.10jqka.com.cn |
| maxQueueLimit | number | 否 | localStorage 缓存条数，默认 100 |
| userAgent | string | SSR 必传 | 用户 UA |
| deviceId | string | SSR 必传 | 设备指纹 |
| userId | string | SSR 必传 | 用户 ID |

---

## report 参数

| 参数 | 类型 | 必传 | 说明 |
|------|------|------|------|
| id | string | 是 | 埋点 ID（四段式） |
| action | string | 是 | 动作类型：click/slide/show/hover/stay/dis/pull/dclick/start/press |
| logmap | object | 否 | 自定义扩展字段 |

---

## reportLeft

页面跳转前调用，强制上报剩余埋点：

```javascript
app.get('/example', (req, res) => {
  reportLeft()
  res.redirect(301, '/new-page')
})
```

---

## action 说明

| action | 适用场景 |
|--------|----------|
| click | 点击事件 |
| slide | 滑动事件 |
| show | 曝光展示 |
| hover | 鼠标悬停 |
| stay | 停留时长 |
| dis | 距离滚动 |
| pull | 下拉刷新 |
| dclick | 双击 |
| start | 开始事件 |
| press | 按压事件 |
