# Futures Plugins Frontend

期货团队 Claude Code 插件集项目。

## 新增插件 Checklist

当添加新插件时，**必须**同步更新以下文件：

- [ ] `plugins/<plugin-name>/.claude-plugin/plugin.json` - 插件元信息
- [ ] `plugins/<plugin-name>/README.md` - 插件说明文档
- [ ] `plugins/<plugin-name>/skills/<skill-name>/SKILL.md` - Skill 定义
- [ ] `README.md` - 在 Plugin 表格中添加新插件，安装命令和详细文档链接
- [ ] `.claude-plugin/marketplace.json` - 在 plugins 数组中添加新插件条目

## 当前 Plugins

| Plugin | 路径 |
|--------|------|
| dev-plan | plugins/dev-plan |
| code-format | plugins/code-format |
| weblog-sdk | plugins/weblog-sdk |
| monitor-sdk | plugins/monitor-sdk |
| matrix | plugins/matrix |
| check-skywalking | plugins/check-skywalking |
