# Repository Guidelines

## 项目结构与模块组织
- 核心配置：根目录 `_config.yml` 为 Hexo 全局配置，主题配置位于 `themes/next/_config.yml`，模板位于 `scaffolds/`。
- 内容来源：`source/_posts` 存放 Markdown 文章，建议命名为 `YYYY-MM-DD-title.md` 并包含 `title/date/tags/categories/description` 的 Front Matter；`source/_data` 存放菜单与站点元数据；`source/images` 存放静态资源；`source/categories`、`source/tags`、`source/html` 负责分类、标签与自定义页面。
- 部署与平台：`package.json` 管理依赖；根目录与 `source/vercel.json` 保存 Vercel 相关配置，`public/` 为生成输出目录（无需入库）。

## 构建、测试与本地开发
- `npm install`：首次或依赖变更时安装依赖。
- `npx hexo clean`：清理缓存与旧的静态文件。
- `npx hexo g`：生成站点到 `public/`，部署前必跑。
- `npx hexo s -o`：本地预览，默认 http://localhost:4000。
- `npx hexo d`：按 `_config.yml` 中的 deploy 设置推送部署，需要先配置凭据。

## 代码风格与命名规范
- Markdown 文章使用 UTF-8 与空行分段；标题用 `#`，代码块注明语言；图片统一放入 `source/images`，引用相对路径。
- Front Matter 键名统一小写；标签与分类使用短横线分词，避免全角符号。
- 主题定制遵循 Next 约定：样式在 `themes/next/source/css`（Stylus），布局在 `themes/next/layout`，修改时保持缩进 2 空格并避免内联脚本。

## 测试与验证
- 本项目无自动化测试，提交前请执行 `npx hexo clean && npx hexo g && npx hexo s` 自查页面渲染、链接与图片是否正常。
- 新增页面或配置时，检查不同设备宽度下的排版，并确认生成的 `public/` 未被提交。

## 提交与合并要求
- 提交信息建议使用简洁祈使句，参考历史例子（如“禁用VIM的鼠标模式”、“通过 HTTPS 端口使用 SSH”），先动词后对象。
- Pull Request 需包含：变更摘要、影响范围（页面/配置/主题）、本地预览步骤、相关 issue 或截图（若有 UI 变更），确保可复现。
- 在合并前确认依赖未新增未使用包，部署配置未暴露敏感令牌。
