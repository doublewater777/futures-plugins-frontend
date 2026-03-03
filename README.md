# 期货团队开发技能包

Claude Code 技能集，用于前端开发场景。

## 包含技能

| 技能 | 用途 | 触发场景 |
|------|------|----------|
| weblog-sdk | 埋点上报 | 用户需要添加埋点时 |
| monitor-sdk | 前端监控 | 用户需要配置异常上报时 |
| code-format | 代码格式化 | 用户需要配置自动格式化时 |
| dev-plan | 开发方案 | 用户需要写开发方案时 |

## 使用方式

```bash
# 添加 plugin
claude plugin add https://github.com/doublewater777/futures-dev
```

当需要使用某个技能时，Claude Code 会根据 description 自动触发相应技能。
