# GTL 增强核心 · 玩家手册

GTL Enhanced 的单页静态站点：查询增强多方块机器、在浏览器里旋转/缩放/剖切查看结构模型，并查看 AE 自动化工具与园区实景。

- 仓库：<https://github.com/skyernovie/GTLEnhanced>
- 在线预览：<https://skyernovie.github.io/GTLEnhanced/>

## 目录结构

```text
.
├── index.html                 # 全部页面结构与样式、脚本（单文件）
├── assets
│   ├── data
│   │   ├── guide-index.json   # 机器清单、贴图集信息、园区图片索引
│   │   └── guide-photos.json  # 园区实景照片（base64 内嵌）
│   └── images
│       ├── hero-photo.webp    # 首屏 / 石英工厂实景
│       ├── world-woodland.webp
│       └── world-custom.webp
└── models                     # 多方块结构模型，一台机器一个 JSON
```

## 本地预览

页面通过 `fetch` 读取 JSON，请用静态服务器打开，不要直接双击 `index.html`：

```powershell
# 任选其一
python -m http.server 8080
npx serve .
```

然后访问 <http://localhost:8080>。

## 数据格式

### assets/data/guide-index.json

| 字段 | 说明 |
| --- | --- |
| `assets` | 页面图标与小图，`data:` URL 或相对路径（如 `assets/images/hero-photo.webp`） |
| `models` | 贴图集信息：`atlas`、`tileSize`、`atlasColumns`、`tiles` |
| `campuses` | 园区条目 |
| `machines` | 机器数组，见下表 |

`machines[]` 字段：`id`、`name`、`origin`、`category`、`stage`、`model`、`summary`、`changes[]`、`notes`。

`model` 为模型键（如 `gtl/advanced_vacuum_drying_furnace`），站点按以下规则映射到 `models/` 下的文件：把除 `A-Z a-z 0-9 _ -` 以外的字符替换为 `_`，再补 `.json`。因此上面的键对应 `models/gtl_advanced_vacuum_drying_furnace.json`。`model` 为空表示该机器暂无自定义结构模型，页面会退化为纯文字条目。

### models/*.json

结构模型由渲染器直接消费：`size`（网格尺寸）、`quads`（每个面的顶点、法线、贴图坐标与方块 id）、`quadCount`。

### assets/data/guide-photos.json

`assets`（图标）与 `campuses`（园区实景，按 `quartz` / `woodland` 等键存放 base64 图片）。站点会在首屏预取失败或缺失时回退到 `guide-index.json` 中的 `assets` 路径。

## 新增或修改一台机器

1. 在 `assets/data/guide-index.json` 的 `machines` 中追加条目，`model` 填模型键（留空则只显示文字）。
2. 把结构模型放到 `models/`，文件名按上面的替换规则命名。
3. 本地起静态服务器确认列表、搜索与三维预览正常。

## 部署

纯静态站点，无构建步骤，资源全部使用相对路径，可直接部署在仓库根路径或任意子路径。

GitHub Pages：仓库 **Settings → Pages → Source: Deploy from a branch → main / (root)**。

## 技术说明

- 原生 HTML / CSS / JavaScript，无框架、无构建、无外部依赖。
- 三维结构使用 WebGL 渲染，支持旋转、缩放与剖切查看。
- 建议使用支持 WebGL 的现代浏览器（Chrome / Edge / Firefox / Safari 最新版）。
