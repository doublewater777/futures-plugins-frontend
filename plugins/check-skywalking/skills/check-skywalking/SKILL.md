---
name: check-skywalking
description: |
  SkyWalking 相关资源检查工具集。包含两个检查 Skill：
  1. check-skywalking-weblog: 检查 SkyWalking、SkyWalking-tool、Weblog 引入和初始化
  2. check-skywalking-report-wrapper: 检查 window.skywalkingTool.report 统一封装函数
  Use when: checking skywalking/weblog/skywalking-tool resources, auditing report wrapper, or fixing related configuration.
  Triggers: user asks to check skywalking引入, weblog初始化, errorReport封装, 主动上报检查.
---

# Check SkyWalking 工具集

## 概述

本工具集包含两个相关检查 Skill，用于审核项目中 SkyWalking 监控相关的资源引入和代码规范。

## Skill 1: check-skywalking-weblog

检查项目中是否正确引入和初始化 SkyWalking、SkyWalking-tool、Weblog 三类资源。

**详细文档：** `references/weblog.md`

### 快速检查步骤

1. 在 HTML 中搜索 `skywalking`、`skywalking-tool`、`weblog` 关键词
2. 在 package.json 中查找相关依赖
3. 检查 Weblog 是否调用 `weblog.setConfig({ appKey: '9099f8be61' })`
4. 检查 SkyWalking-tool 是否调用 `skywalkingTool.register({ group: 'futures', ... })`

### 版本要求

| 资源 | 版本要求 |
|------|----------|
| SkyWalking | 1.1.25 及以上 |
| SkyWalking-tool | 必须为 `index.1.0.0.min.js` |
| Weblog | 必须为 `/0.0.5/weblog.js` |

### CDN 地址

| 资源 | CDN 地址 |
|------|----------|
| SkyWalking | `//s.thsi.cn/hxapp/m/base/js/skywalking.1.1.25.min.js` |
| SkyWalking-tool | `//s.thsi.cn/cd/futures-frontend-fu-cdn/common/skywalking-tool/index.1.0.0.min.js` |
| Weblog | `//s.thsi.cn/cd/weblog/0.0.5/weblog.js` |

---

## Skill 2: check-skywalking-report-wrapper

检查项目中是否存在对 `window.skywalkingTool.report` 的统一封装函数。

**详细文档：** `references/wrapper.md`

### 封装函数特征

标准封装应包含：
- 对 `window.skywalkingTool` 的存在性判断
- 统一组装上报参数（含 `err` 字段）
- 立即上报配置（如 `timeout: 1`）
- 降级处理（如 `console.warn`）

### 参数规范

业务调用封装函数时需遵守：
- 第一个参数必须为 `new Error('上下文相关文案')`
- 第二个参数（可选）为 `{ content: err }` 形式

---

## 配合使用建议

1. 先用 `check-skywalking-weblog` 确认资源引入与初始化正确
2. 再用 `check-skywalking-report-wrapper` 确认主动上报规范

## 参考文件

- 详细 Weblog 检查流程：`references/weblog.md`
- 详细封装函数检查流程：`references/wrapper.md`
