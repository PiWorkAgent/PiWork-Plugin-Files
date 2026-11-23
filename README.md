# PiWork-Plugin-Files

[English](README.en.md) | 简体中文

**PiWork 官方插件 · 文件查看器 · `com.pi.files`**

PiWork 的文件查看器，也是 Office / PDF 的编辑器和生成器：文件树里点开一个文件，它就在右侧栏渲染；同时给 agent 一整套文档工具——新建、读取、编辑 docx / pptx / xlsx，做 PDF 页操作、PDF → Office 转换和 PDF → PNG 导出。文档由插件内置的 GenOffice 引擎渲染和编辑，不需要 Python、LibreOffice 或外部 Office 软件。

## PiWork 中的文档处理

![PiWork 中的文档处理](./media/demo.gif)

## 能做什么

- **预览** —— docx / pptx / xlsx / pdf、markdown、html、纯文本（txt / json / log）、图片、音频和视频，都在右侧栏直接打开。
- **手动编辑** —— pptx 面板可双击改文字，调整字体、段落、填充、描边、位置、大小、旋转，增删元素与页面，插入表格 / 图表 / SmartArt / 图片 / 音视频 / 3D，管理母版、备注、动画、切换，撤销重做、查找替换、另存为和全屏放映；docx 也通过面板内的编辑器修改。
- **agent 生成与编辑** —— agent 用 `office_create` 新建文档，用 `office_read` 读取结构，再用 `office_edit` 按命名 op 修改。整批操作原子提交，失败不留半改状态，还支持 `dryRun` 和 mtime 冲突检测。
- **PDF 工具** —— 合并 / 拆分 / 插页 / 删页 / 裁剪 / 缩放 / N-up 拼版；PDF → docx / pptx / xlsx；PDF 页面 → PNG。全部本地完成。
- **查找文档** —— 用户只记得「上次那份季度汇报」时，`files_search` 按文件名、目录、标题和正文开头找到它，不用挨个打开。
- **完成卡片** —— 一轮 agent 运行结束后发一张完成卡，列出这一轮新建 / 修改过的文件，每行一个「打开」按钮。

## 覆盖范围

| 格式 | 面板预览 | agent 读取 / 编辑 | agent 新建 |
| --- | --- | --- | --- |
| `pptx` | ✅ Konva 渲染，可手动编辑 | ✅ 完整（命名 op） | ✅ `office_create` |
| `docx` `docm` | ✅ 分页渲染，可编辑 | ✅ 结构编辑（需先在查看器打开） | ✅ `office_create` |
| `xlsx` `xlsm` | ✅ 表格渲染（只读） | ✅ 单元格改值 / 清空 | ✅ `office_create` |
| `csv` | ✅ 表格渲染（只读） | ❌ 不做结构化编辑 | ✅ `office_create` |
| `pdf` | ✅ | 正文只读；页操作走 `pdf_edit` | ✅ `office_create` |
| `md` `html` `txt` `json` `log` | ✅ | — | 用普通文件写入工具 |
| 图片 / 音频 / 视频 | ✅ | — | — |
| `doc` `ppt`（97-2003） | ✅ 仅文字 | ❌ 需先另存为 docx / pptx | ❌ |

更细的格式支持和边界见 [PLUGIN.md](./PLUGIN.md)。

## 在 PiWork 里安装

1. 打开 PiWork 桌面端。
2. 右侧栏 →「插件中心」→「文件查看器」→ 安装。
3. 首次安装会显示权限清单；确认后下载并校验（sha256）。

Web 端不加载插件；文档预览与编辑需要桌面端。

从源码开发（依赖较重，约 800 MB）：

```bash
git clone https://github.com/PiWorkAgent/PiWork-Plugin-Files
cd PiWork-Plugin-Files
npm install
npm run build          # 后端 dist/main.mjs + 面板 ui-dist/panel
npm run vendor:verify  # 校验 vendor/genoffice 未被改动
```

在 PiWork 仓库里实时调试：

```bash
PI_DEV_PLUGINS=/path/to/PiWork-Plugin-Files pnpm dev:desktop:plugins
```

`vendor/`（GenOffice，Apache-2.0）是源码依赖，已提交；`ui-dist/` 是构建产物，不提交。

## 对话里的用法

| 你说 | 会发生什么 |
| --- | --- |
| 「打开这份合同看看」 | 文件树或 `files_open` 打开，右栏渲染 |
| 「做一份季度汇报 PPT」 | agent 调 `office_create` 新建 pptx 并写入内容 |
| 「把这个表格里华东区的数字改成 4321」 | `office_read` 定位单元格 → `office_edit` 改值 |
| 「把这三份 PDF 合并，再导出每一页图片」 | `pdf_edit` 合并 → `pdf_render` 导出 PNG |
| 「把这个 PDF 转成 Word」 | `pdf_convert` 本地转换，文字保持可编辑 |
| 「上次那份提过华东预算的文件在哪」 | `files_search` 按标题和正文找到 |

## 工具

| 工具 | 作用 |
| --- | --- |
| `office_guide` | 查询 op 词汇表、字段表、示例和常见错误；写 ops 前先调 |
| `office_read` | 读取文档结构，返回 slide / block / sheet / cell，元素 id 就是编辑目标 |
| `office_edit` | 对文档应用一批 op；整批原子，支持 `dryRun` 和 mtime 冲突检测 |
| `office_create` | 新建 pptx / docx / xlsx / pdf / csv，可顺带写入第一批内容 |
| `pdf_edit` | PDF 页操作：合并、拆分、插页、删页、裁剪、缩放、N-up、文字 |
| `pdf_convert` | PDF → docx / pptx / xlsx，完全本地 |
| `pdf_render` | PDF 页面 → PNG |
| `files_search` | 按文件名、目录、标题和正文开头搜索 Office 文档与 PDF |
| `files_open` | 在查看器里打开文件（docx 结构编辑前必须先打开） |

## 权限

| 权限 | 用途 |
| --- | --- |
| `filesystem` | 读写用户在工作区里打开或生成的文档 |
| `messages` | 一轮运行结束后发完成卡片（列出本轮新建 / 修改的文件） |

插件后端拥有与用户账户相同的 Node 进程权限；权限声明约束的是插件通过宿主获取能力的范围。安装时的权限确认是用户唯一的实时防线。

## 发布

1. 改 `manifest.json` 的 `version`
2. 提交并推送 tag `v<version>`（或 Actions → Release → Run workflow）

Release 资产：`com.pi.files-<version>.zip`（约 27 MB，只含运行时负载）+ `index.json`。`vendor/`、`src/`、`node_modules/` 不进包。

## 目录

| 路径 | 说明 |
| --- | --- |
| `manifest.json` | 插件清单：id、版本、`apiVersion`、权限、工具、面板 |
| `src/` | 后端 / 面板源码 |
| `vendor/genoffice/` | 内置文档引擎（上游源码，勿改，Apache-2.0） |
| `dist/`、`ui-dist/` | 构建产物（`dist/` 已提交，`ui-dist/` 不提交） |
| `assets/` | 运行时图标 |
| `media/` | README 演示素材（不进入发布包） |
| `scripts/release.mjs` | 打包 + 生成单条目索引 |

## 相关链接

- [PiWork](https://github.com/PiWorkAgent/PiWork) — 宿主应用
- [PiWork-Registry](https://github.com/PiWorkAgent/PiWork-Registry) — 插件市场索引
- [插件开发指南](https://github.com/PiWorkAgent/PiWork-Plugin-Docs/blob/main/docs/plugin-dev-guide.md)
- [插件 SDK `@weiqun789/plugin-sdk`](https://www.npmjs.com/package/@weiqun789/plugin-sdk)
- [PLUGIN.md](./PLUGIN.md) — 写给 agent 的使用说明
