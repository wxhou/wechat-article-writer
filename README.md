# 公众号文章创作 Skill

自动化公众号文章创作与发布全流程。

## 功能特性

- **自动场景识别** - 根据输入自动判断处理方式
- **质量检查** - 错别字检测、可读性评分、敏感词检测
- **AI配图建议** - 自动分析内容推荐配图位置和风格
- **格式优化** - 一键美化公众号排版
- **一键发布** - 支持发布到公众号草稿箱

## 使用场景

| 输入类型 | 处理方式 |
|---------|---------|
| X/网页链接 | 获取内容 → 写作 → 配图 → 格式优化 → 发布 |
| 英文文章 | 翻译 → 写作 → 配图 → 格式优化 → 发布 |
| 仅有标题 | article-writer 多轮调研 → 写作 → 配图 → 格式优化 → 发布 |
| 直接要求发布 | 跳过草稿，直接走完整流程 |

## 快速开始

```bash
# 使用 skill
/wechat-article-writer <X链接/标题/英文文章>
```

## 完整流程

```
自动识别 → 获取内容 → 质量检查 → 写作/翻译 → AI配图建议 → 格式优化 → 发布
```

### 1. 获取内容

- **X 链接**: 使用 `baoyu-url-to-markdown` 或 `baoyu-danger-x-to-markdown`
- **英文文章**: 读取原文，进行信达雅的翻译
- **仅有标题**: 使用 `article-writer` 进行多轮信息检索

### 2. 质量检查

- 错别字检测
- 可读性评分
- 敏感词检测
- 标题检查

### 3. 写作

- 公众号: 深度技术文章，2000-4000字，配图精美
- 使用中文标题和中文标点
- 段落适中，避免长篇大块文字
- 关键信息使用引用块强调

### 4. AI 配图

使用 `baoyu-article-illustrator` 或 `baoyu-image-gen`

### 5. 格式优化

使用 `baoyu-markdown-to-html` 转换后执行美化：

- 标题样式: 二级标题使用彩色背景（#0F4C81）
- 引用块: 深蓝色文字强调核心观点
- 分隔线: `---` 区分章节
- 字体大小: 正文 16px，行高 1.75

### 6. 发布

```bash
npx -y bun ~/.claude/skills/baoyu-post-to-wechat/scripts/wechat-api.ts article.html --cover cover.png --author "博主"
```

## 依赖 Skills

- `baoyu-post-to-wechat` - 发布到公众号
- `baoyu-markdown-to-html` - Markdown 转 HTML
- `baoyu-image-gen` - AI 图片生成
- `baoyu-article-illustrator` - 文章配图
- `baoyu-danger-x-to-markdown` - X 转 Markdown
- `baoyu-url-to-markdown` - 网页转 Markdown
- `article-writer` - AI 文章写作

## 项目目录

- 文章项目: `/Users/wxhou/Documents/obsidian-note/微信公众号/`
- 附件图片: `attachments/`

## License

MIT
