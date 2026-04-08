# 检查 SkyWalking 主动上报封装函数

该 skill 用于检查项目中是否存在对 `window.skywalkingTool.report` 的**统一封装函数**，功能应与 `src/utils/error.js` 中的 `errorReport` 类似，用于主动上报 ELK 日志。

参考实现（当前项目中的示例）：

- 文件：`src/utils/error.js`
- 函数：`errorReport(message, options)`
- 关键特征：
  - 接收 `message` 与扩展配置 `options`
  - 组装上报参数（如合并 `options` 并写入 `err` 字段、`timeout: 1` 表示立即上报）
  - 在 `window.skywalkingTool` 存在时调用 `window.skywalkingTool.report(params)`
  - 未发现 `skywalkingTool` 时输出降级日志（`console.warn`）

## 检查目标

1. 项目中是否有**集中封装** `window.skywalkingTool.report` 的函数（例如 `errorReport`）。
2. 是否存在**散落的直接调用** `window.skywalkingTool.report(...)`，而未通过统一封装。
3. 若当前项目已有 `src/utils/error.js` 的实现，是否被其他业务代码正确复用。
4. 业务侧调用封装函数（如 `errorReport`）时，是否遵守以下参数规范：
   - 第一个参数必须为 `new Error('上报信息')`，且**上报信息需与当前上下文运行环境语义匹配**（例如「查询报名状态异常」「敏感词接口异常」等）。
   - 第二个参数为**可选**，但一旦传入，必须为形如 `{ content: err }` 的对象，其中 `err` 通常为实际捕获到的异常对象或与异常紧密相关的数据。

## 检查步骤

1. **定位封装函数候选**

   - 在 `src` 目录下搜索 `skywalkingTool.report`。
   - 关注以下特征：
     - 是否出现在 `utils`、`services`、`monitor`、`logger` 等公共模块中。
     - 是否被多个业务文件引用。
   - 若匹配到 `src/utils/error.js` 中的：
     - `export const errorReport = (message, options) => { ... }`
     - 内部包含：
       - `const params = { ...options, err: message || options.err, timeout: 1 }`
       - `if (window.skywalkingTool) { window.skywalkingTool.report(params); }`
       - `else { console.warn(..., JSON.stringify(params)); }`
     - 则将该函数视为**标准封装实现**。

2. **统计直接调用点**

   - 在 `src` 下继续搜索 `skywalkingTool.report(`：
     - 记录所有**不在封装函数内部**的调用位置（文件路径 + 行号）。
     - 判断这些调用是否**可以改为复用封装函数**（尤其是业务页面、组件、接口调用处）。

3. **检查封装函数的复用情况与参数规范**

   - 搜索 `errorReport(`（或其他识别到的封装函数名）：
     - 统计调用数量与调用分布（哪些模块/页面在使用）。
     - 若仅在定义文件附近被使用，说明封装复用度不高。
   - 对每一处业务调用，重点检查：
     - **第一个参数**是否为 `new Error('...')`：
       - 若仅传入字符串或其他类型（如 `errorReport('xxx异常', {...})`），需在报告中标记为**不符合规范**。
       - 检查 `Error` 文案是否能准确描述当前上下文的业务场景和异常原因，避免过于模糊或与实际行为不符。
     - **第二个参数（如存在）**是否为 `{ content: err }`：
       - 对象键名应为 `content`，值通常命名为 `err`、`error` 或直接使用捕获到的异常对象。
       - 若结构不为 `{ content: xxx }` 或传入了与异常无关的数据，应在报告中单独列出。

4. **判断是否「存在封装」与「需要优化」**

   - 存在封装且封装函数满足以下条件之一，即视为「封装完整」：
     - 对 `window.skywalkingTool` 进行存在性判断。
     - 统一处理上报参数（至少组装 `err` 字段，支持传入扩展配置）。
     - 提供降级行为（如 `console.warn` 打印无法上报的异常）。
   - 若存在大量直接调用 `skywalkingTool.report(...)` 的业务代码，而未通过封装函数，则视为「需要优化」。

## 输出格式

检查完成后，按以下模板输出报告：

```markdown
## SkyWalking 主动上报封装函数检查报告

### 封装函数概况

- 是否存在封装函数（如 `errorReport`）: 是/否
- 封装函数定义位置:
  - `src/utils/error.js:Lx-Ly`（若存在）
  - [其他位置，如有则列出]
- 封装函数关键行为是否满足要求:
  - 检查 `window.skywalkingTool` 是否存在: 是/否
  - 统一组装上报参数（含 `err` 字段）: 是/否
  - 立即上报配置（如 `timeout: 1`）: 是/否/不适用
  - 未找到 skywalkingTool 时有降级处理（`console.warn` 等）: 是/否

### 直接调用情况

- 直接调用 `skywalkingTool.report(...)` 的位置（不在封装函数内部）:
  - `src/xxx/yyy.vue:Lx`
  - `src/zzz/service.js:La`
  - ...

### 封装函数复用情况

- 封装函数名称: `errorReport` / [其他名称]
- 被调用位置:
  - `src/aaa/bbb.vue:Lm`
  - `src/ccc/ddd.js:Ln`
  - ...
- 参数规范检查结果（节选）:
  - 调用是否使用 `new Error('...')` 作为第一个参数: 是/否
  - `Error` 文案是否与当前上下文业务语义匹配: 是/否/需人工确认
  - 第二个参数（如有）是否为 `{ content: err }` 结构，且 `err` 为异常对象或与异常强相关的数据: 是/否

### 结论与建议

- 综合结论: [已存在统一封装且复用良好，参数规范一致 / 已存在封装但参数使用不规范 / 已存在封装但复用不足 / 未发现封装，仅有零散 direct 调用]
- 建议:
  - 若不存在封装: 建议在 `src/utils/error.js` 或类似公共模块中新增统一封装函数，并替换业务中直接调用。
  - 若复用不足: 建议将统计到的直接调用改为使用封装函数，避免散落逻辑。
  - 若参数使用不规范:
    - 将非 `new Error('...')` 的首参替换为 `new Error('符合当前上下文的异常文案')`。
    - 统一第二个参数为 `{ content: err }` 形式，并确保 `err` 是实际捕获到的异常对象（例如 `catch (err) { errorReport(new Error('xxx异常'), { content: err }); }`）。
```

## 新增/修复封装函数建议

当检查发现：

- 项目**没有统一封装**，或
- 仅有零散直接调用 `skywalkingTool.report(...)`，

可以参考当前项目中 `src/utils/error.js` 的实现，在公共工具模块中新增或强化封装函数，例如：

```javascript
// 建议放在 src/utils/error.js 或类似公共工具文件中
/**
 * @description 特殊异常处理，上报 elk 日志（统一封装）
 * @param {string} message 上报的信息
 * @param {ErrorReportOptions} options 异常上报拓展配置
 */
export const errorReport = (message, options = {}) => {
  const params = {
    ...options,
    err: message || options.err,
    // 立即上报
    timeout: 1,
  };
  if (window.skywalkingTool && typeof window.skywalkingTool.report === 'function') {
    // 主动上报
    window.skywalkingTool.report(params);
  } else {
    console.warn('存在需要上报 elk 的异常，但未找到 skywalkingTool 依赖。异常日志：', JSON.stringify(params));
  }
};
```

> 注意：实际实现时以项目现有的 `src/utils/error.js` 为准，上述代码仅作为模板。若项目已有同名函数，应优先复用并在其基础上补齐逻辑，而不是随意新建重复实现。

### 参数与调用方式修复步骤

当检测报告中发现**参数使用不规范**或**直接调用未通过封装**时，按以下步骤进行修复：

1. **修复首参不是 `new Error('...')` 的情况**

   - 典型不规范示例：
     - `errorReport('敏感词接口异常', { content: error });`
     - `errorReport(msg, { content: err });`
   - 修复方式：
     - 将首参改为 `new Error('符合当前上下文的异常文案')`，例如：
       - `errorReport(new Error('敏感词接口异常'), { content: error });`
       - 在循环或复用场景下，可根据实际上下文动态拼接文案（如携带 ID、类型等关键信息）。

2. **修复第二个参数结构不为 `{ content: err }` 的情况**

   - 若发现如下调用：
     - `errorReport(new Error('xxx异常'), err);`
     - `errorReport(new Error('xxx异常'), { error: err });`
   - 修复方式：
     - 统一改为：
       - `errorReport(new Error('xxx异常'), { content: err });`
     - 其中 `err` 推荐直接使用 `catch (err)` 中的异常对象，或与异常强相关的数据结构。

3. **为缺失第二个参数但有可用异常对象的调用补充 `{ content: err }`**

   - 若调用环境中已捕获异常对象，但只传入了 `new Error('...')`：

     ```javascript
     try {
       // ...
     } catch (err) {
       errorReport(new Error('xxx异常'));
     }
     ```

   - 建议修复（可选强化，而非强制）：

     ```javascript
     try {
       // ...
     } catch (err) {
       errorReport(new Error('xxx异常'), { content: err });
     }
     ```

4. **将直接调用 `skywalkingTool.report(...)` 替换为封装函数**

   - 若在业务代码中发现如下调用：

     `window.skywalkingTool.report(params);`

   - 修复方式：

     - 将该逻辑迁移到统一封装内（如 `errorReport`），业务处仅调用封装：

       ```javascript
       // 业务代码中
       errorReport(new Error('xxx异常'), { content: err });
       ```

     - 在封装内部负责组装 `params` 并调用 `window.skywalkingTool.report(params)`。

5. **修复时的通用建议**

   - 为每一个 `Error` 文案选择**与当前函数/方法责任强相关的描述**，例如：
     - 「查询报名状态异常」「查询表现数据异常」「敏感词接口异常」等。
   - 避免过于宽泛的文案（如「接口异常」），优先包含：
     - 所属业务模块（如「个人中心」「奖励页」）
     - 具体操作（如「查询列表」「修改密码」「校验敏感词」）
   - 修复完成后，重新运行本 skill 的检查流程，确认所有调用点均已满足：
     - 首参为 `new Error('上下文相关文案')`
     - 二参为可选 `{ content: err }`，且 `err` 来源合理。

## 使用建议

- 当用户提到：
  - 「检查 skywalkingTool.report 封装」
  - 「主动上报封装函数」
  - 「elk 异常日志统一上报」
  - 「类似 src/utils/error.js 里的 errorReport」
    时，应优先使用本 skill。
- 可与 `check-skywalking-weblog` skill 搭配使用：
  - 先用 `check-skywalking-weblog` 确认 SkyWalking / SkyWalking-tool / Weblog 引入与初始化是否正确。
  - 再用本 skill 确认业务侧是否通过统一封装函数进行主动上报，避免散落调用和重复逻辑。
