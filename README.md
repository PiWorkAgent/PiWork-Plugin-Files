# PiWork-Plugin-Files

PiWork 插件：文件查看器（docx / pptx / xlsx / pdf / markdown / html / 文本 / 图片 / 音视频），内置 GenOffice 引擎。插件 id `com.pi.files`。

通过 [PiWork-Registry](https://github.com/PiWorkAgent/PiWork-Registry) 发布到 PiWork 插件中心。

## 开发

```bash
npm install          # 依赖较重（约 800 MB），首次安装要几分钟
npm run build        # 后端 dist/main.mjs + 面板 ui-dist/panel
npm run vendor:verify  # 校验 vendor/genoffice 未被改动
```

在 PiWork 仓库里实时调试：

```bash
PI_DEV_PLUGINS=/path/to/PiWork-Plugin-Files pnpm dev:desktop:plugins
```

`vendor/`（GenOffice，Apache-2.0）是源码依赖，已提交；`ui-dist/` 是构建产物，不提交。

## 发布

1. 改 `manifest.json` 的 `version`
2. 提交并推送 tag `v<version>`（或 Actions → Release → Run workflow）

Release 资产：`com.pi.files-<version>.zip`（约 27 MB，只含运行时负载）+ `index.json`。`vendor/`、`src/`、`node_modules/` 不进包。

## 目录

| 路径 | 说明 |
| --- | --- |
| `manifest.json` | 插件清单：id、版本、`apiVersion`、权限、工具、面板 |
| `src/` | 后端 / 面板源码 |
| `vendor/genoffice/` | 内置文档引擎（上游源码，勿改） |
| `dist/`、`ui-dist/` | 构建产物（`dist/` 已提交，`ui-dist/` 不提交） |
| `scripts/release.mjs` | 打包 + 生成单条目索引 |
