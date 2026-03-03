# Futures Plugins Frontend

期货团队 Claude Code 技能集。

## 包含 Plugins

| Plugin | 用途 |
|--------|------|
| dev-plan | 前端开发方案模板 |
| code-format | 代码格式化规范 |
| weblog-sdk | 埋点上报 SDK |
| monitor-sdk | 前端监控 SDK |

## 使用方式

### 1. 添加自定义插件市场

```bash
claude plugin marketplace add https://github.com/doublewater777/futures-plugins-frontend
```

### 2. 安装插件

```bash
# 安装单个插件
claude plugin install code-format@futures-plugins-frontend

# 安装所有插件
claude plugin install dev-plan@futures-plugins-frontend
claude plugin install code-format@futures-plugins-frontend
claude plugin install weblog-sdk@futures-plugins-frontend
claude plugin install monitor-sdk@futures-plugins-frontend
```

Claude Code 会根据 description 自动触发相应技能。

## 详细文档

- [dev-plan](./plugins/dev-plan/README.md)
- [code-format](./plugins/code-format/README.md)
- [weblog-sdk](./plugins/weblog-sdk/README.md)
- [monitor-sdk](./plugins/monitor-sdk/README.md)
