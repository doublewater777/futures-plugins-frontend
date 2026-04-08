# Check SkyWalking Plugin

SkyWalking 相关资源检查工具集，包含两个检查 Skill：

## Skills

### check-skywalking-weblog
检查 SkyWalking、SkyWalking-tool、Weblog 的引入和初始化是否规范。

**触发场景：**
- 询问检查/审核/修复 skywalking、skywalking-tool、weblog 资源
- HTML 中的 script/link 引入检查
- 初始化代码检查

### check-skywalking-report-wrapper
检查项目中是否存在对 `window.skywalkingTool.report` 的统一封装函数。

**触发场景：**
- 审核 skywalkingTool 使用情况
- 主动上报封装函数
- ELK 异常日志统一上报

## 使用方式

在 Claude Code 中触发 skill：
- 询问检查 SkyWalking 引入情况
- 询问检查主动上报封装
- 需要修复相关配置时

## 相关资源

- Skill 文档：`skills/check-skywalking/SKILL.md`
- 详细文档：
  - `skills/check-skywalking/references/weblog.md` - Weblog 检查详解
  - `skills/check-skywalking/references/wrapper.md` - 封装函数检查详解
