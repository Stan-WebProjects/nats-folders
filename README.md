# iFolder

一个在浏览器里自定义 macOS 文件夹图标的小工具。单文件 HTML，打开即用。

## 功能

- 自定义文件夹颜色与「含文件」时的内页颜色
- 内置两套图形库：
  - **符号**：Tabler Icons，数百个通用图标，按分类搜索
  - **品牌**：精选 350+ 个 Simple Icons 品牌图标，覆盖开发 / 设计 / 社交 / 媒体娱乐 / 云与 AI / 生产力 / 购物生活 / 浏览器系统 八大分类
- 一键「使用品牌色」：选中品牌后自动套用官方色（如 GitHub 黑、Figma 红、Claude 棕橙），也可随时切回自定义颜色
- 支持取色滴管、色卡、预设调色板
- 导出 PNG / SVG，直接用于 macOS 「显示简介 → 拖到左上角图标」

## 使用

下载本仓库，双击 `index.html` 即可在浏览器中打开。无需构建、无需安装依赖。

如果需要本地预览服务，也可以任选一种：

```bash
# Python
python3 -m http.server 8099

# Node
npx serve .
```

然后访问 `http://localhost:8099`。

## 浏览器兼容

推荐 Chrome / Safari / Edge 最新版。移动端已适配。

## 技术栈

- 纯 HTML / CSS / 原生 JavaScript，单文件
- [iro.js](https://iro.js.org/) — 颜色选择器
- [Tabler Icons](https://tabler.io/icons) — 符号图标库（MIT）
- [Simple Icons](https://simpleicons.org/) — 品牌图标库（CC0-1.0），图标数据已内联进 `index.html`，无需联网

## 许可

MIT

品牌名称与 Logo 归各自所有者所有；iFolder 仅作为图形化展示工具使用，不代表任何品牌的认可或附属关系。
