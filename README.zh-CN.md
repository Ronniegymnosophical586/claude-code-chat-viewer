<p align="center">
  <a href="README.md">English</a> ·
  <a href="README.ru.md">Русский</a> ·
  <a href="README.es.md">Español</a> ·
  <a href="README.fr.md">Français</a> ·
  <b>中文</b> ·
  <a href="README.ar.md">العربية</a>
</p>

# Claude Code Chat Viewer

<p align="center">
  <img src="screenshot.png" alt="截图" width="900" />
</p>

用于浏览 [Claude Code](https://claude.com/claude-code) 会话记录(JSONL 格式)的 HTML 查看器。在浏览器中直接打开 — 无需服务器,无需构建,只有一个本地打包的依赖。开箱即用,完全离线工作。

## 为什么

Claude Code 把每次会话写入 `~/.claude/projects/<project>/<session-uuid>.jsonl` — 每行一条记录(用户消息、模型回复、thinking、tool_use、tool_result、attachment 等)。原始文件几乎无法阅读;内置命令如 `/resume` 能显示对话,但不能导出或事后分析。

这个查看器把这样的 `.jsonl` 变成可读的时间流,按角色着色,服务块(thinking / tools / results)可折叠,附带过滤器。

## 显示的内容

- **user**(蓝色)— 真实的用户消息
- **assistant**(绿色)— Claude 的文字回复
- **thinking**(紫色)— extended thinking,默认折叠
- **tool_use**(琥珀色)— 工具调用,附带参数预览
- **tool_result**(青色 / 错误为红色)— 工具响应
- **meta / task-note**(黄色)— 系统注入和子代理的 `<task-notification>`
- **system / attachment / ui-state** — 服务记录(默认隐藏)

每个块都是独立的一行,左侧有彩色条。没有聊天气泡:这是一份日志,不是聊天界面。

## 如何打开

1. 克隆仓库或下载 ZIP。需要 `index.html` 和 `lib/` 目录。
2. 双击 `index.html` — 在任意现代浏览器中打开。
3. 使用文件选择器选择 `.jsonl` 记录。

Claude Code 的记录位于:

```
~/.claude/projects/<project-slug>/<session-uuid>.jsonl
```

其中 `<project-slug>` 是你的工作目录,`/` 替换成 `-`。例如:`/home/user/myproj` → `-home-user-myproj`。

## 功能

- **亮色 / 暗色主题** — 标题栏的切换按钮,偏好保存在 `localStorage` 中。
- **六种界面语言** — English、Русский、Español、Français、中文、العربية。阿拉伯语自动切换为 RTL。标题栏中的下拉选择器,偏好会保存。
- **流式解析** — `.jsonl` 通过 `file.stream()` + `TextDecoderStream` 读取,不会整体加载为单个字符串。
- **原生虚拟化** — 每条记录使用 `content-visibility: auto`:浏览器跳过屏幕外条目的布局和绘制。可扩展到成千上万条记录。
- **分块渲染** — 每块 500 条,剩余部分通过「加载更多」按钮加载。
- **过滤器** — 五个复选框(thinking / tools / results / system / ui-state),通过容器上的单个 CSS 类切换类别(无 DOM reflow)。
- **XSS 安全渲染** — 每个文本块在传入 markdown 解析器*之前*会先进行 HTML 转义。记录中的原始 HTML 永远不会进入 DOM,因此不需要运行时消毒器。
- **大小限制** — prose 块截断至 20 KB,服务块截断至 5 KB(它们通常包含 10-50 KB 没人会读的杂项)。序列化也有界限 — 大的工具输入不会在截断前完整物化。
- **`.json` 回退** — 如果文件不是 JSONL 而是普通的 JSON 数组/对象,会作为记录列表解析。

## 浏览器要求

- Chrome / Edge 85+
- Safari 18+
- Firefox 125+

都是为了 `content-visibility: auto`。在旧版浏览器上查看器仍可打开,但大文件的滚动会明显变慢。

## 依赖

只有一个,在本地 `lib/` 中打包:

- [marked](https://github.com/markedjs/marked) — markdown → HTML(~35 KB)

没有 CDN,没有网络,没有 Subresource Integrity。克隆即可运行。

## 隐私

一切都在你的浏览器本地运行。查看器**不**发起任何网络请求 — 没有 CDN,没有分析,没有远程字体。你的记录留在你的机器上。

## 已知限制

- 超过约 100 MB 的文件需要基于 line-offset 的索引化加载与窗口化渲染(尚未实现)。
- 没有导出到 Markdown/HTML 的功能(目标是浏览,不是转换)。
- 代码块中没有语法高亮(有意为之 — 保持依赖最小)。

## 开发

所有代码都在一个 HTML 文件里。直接编辑即可 — 样式在 `<style>`,逻辑在 `<script>`,翻译在脚本开头的 `I18N` 对象中。

不用浏览器检查 JS 语法:

```bash
sed -n '/^<script>$/,/^<\/script>$/p' index.html | sed '1d;$d' | node --check /dev/stdin
```

## 许可证

[Unlicense](LICENSE) — 公有领域。随意使用,无需署名。
