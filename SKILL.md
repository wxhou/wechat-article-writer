---
name: wechat-article-writer
description: 公众号文章创作与发布。完整流程：自动场景识别 → 获取内容 → 质量检查 → 写作/翻译 → AI配图 → 封面图 → 格式优化 → 发布。使用场景：(1) X/网页链接 (2) 英文文章翻译 (3) 标题创作 (4) 直接发布
---

# 公众号文章创作 Skill

自动化公众号文章创作与发布全流程。

## 完整流程

```
自动识别 → 获取内容 → 质量检查 → 写作 → AI配图 + 封面图 → 格式优化 → 发布
```

**并行处理**: 配图生成与封面图生成可并行执行

---

## 触发条件

| 输入类型 | 识别特征 | 处理方式 |
|---------|---------|---------|
| X/网页链接 | x.com、twitter.com、http | 获取内容 → 写作 → 配图 → 封面 → 发布 |
| 英文文章 | 纯英文、English | 翻译 → 写作 → 配图 → 封面 → 发布 |
| 仅有标题 | 纯中文无链接 | article-writer 调研 → 写作 → 配图 → 封面 → 发布 |
| 直接发布 | "直接发布"、"走完整流程" | 跳过草稿，直接完整流程 |

---

## Step 1: 获取内容

**X/网页链接**:
```bash
npx -y bun ~/.claude/skills/baoyu-danger-x-to-markdown/scripts/main.ts <url>
# 或
npx -y bun ~/.claude/skills/baoyu-url-to-markdown/scripts/main.ts <url>
```

**仅有标题**:
```bash
cd "/Users/wxhou/Documents/obsidian-note/微信公众号"
content init 文章标题 --workspace wechat
# /specify → /topic → /research → /write
```

---

## Step 2: 质量检查 (自动化)

使用 validator 模式自动检查：

### 2.1 错别字检测
```bash
# 检查常见错别字
grep -E "份饭|成份|其它|其它|一部份|帐号|帐户" article.md
```

### 2.2 可读性检查
- 单句长度不超过 40 字
- 段落不超过 3 行
- 使用中文标点

### 2.3 敏感词检测
```bash
# 敏感词列表检查
grep -iE "敏感词1|敏感词2|敏感词3" article.md
```

### 2.4 标题检查
- 标题长度 20-30 字
- 有数字/疑问/对比更佳

**质量检查循环**: 检查 → 修复 → 重新检查，直到通过

---

## Step 3: AI 配图

使用 `baoyu-article-illustrator` 或 `baoyu-image-gen`:

```bash
# 分析文章，自动识别配图位置和风格
npx -y bun ~/.claude/skills/baoyu-article-illustrator/scripts/main.ts article.md
```

**备用方案** (API 失败时):
```bash
python3 ~/.claude/skills/baoyu-image-gen/scripts/modelscope_fallback.py "prompt" output.png
```

---

## Step 4: 封面图 (新增)

使用 `baoyu-cover-image` 生成封面:

```bash
npx -y bun ~/.claude/skills/baoyu-cover-image/scripts/main.ts \
  --prompt "文章标题" \
  --type infographic \
  --palette tech \
  --mood professional \
  --image cover.png
```

---

## Step 5: 格式优化

```bash
# Markdown 转 HTML
npx -y bun ~/.claude/skills/baoyu-markdown-to-html/scripts/main.md \
  article.md \
  --theme grace \
  --output article.html
```

**手动优化项**:
- 二级标题: 彩色背景 (#0F4C81)
- 引用块: 深蓝色强调
- 分隔线: `---` 章节区分

---

## Step 6: 发布

```bash
npx -y bun ~/.claude/skills/baoyu-post-to-wechat/scripts/wechat-api.ts \
  article.html \
  --cover cover.png \
  --author "博主"
```

---

## 并行处理

配图生成与封面图生成可并行执行:

```bash
# 后台并行执行
npx -y bun ~/.claude/skills/baoyu-article-illustrator/... &
npx -y bun ~/.claude/skills/baoyu-cover-image/... &
wait
```

---

## 错误处理

| 问题 | 解决方案 |
|-----|---------|
| 链接获取失败 | 使用浏览器方式 baoyu-url-to-markdown |
| 图片生成失败 | 使用 modelscope_fallback.py 备用 |
| API 白名单 | 添加 IP 到微信后台 |
| 格式异常 | 使用 --theme default |

**关闭浏览器**:
```bash
mcp__chrome-devtools__close_page --pageId <ID>
mcp__chrome-devtools__list_pages
```

---

## 项目目录

- 文章: `/Users/wxhou/Documents/obsidian-note/微信公众号/`
- 附件: `attachments/`

## 依赖 Skills

- baoyu-post-to-wechat
- baoyu-markdown-to-html
- baoyu-image-gen
- baoyu-article-illustrator
- baoyu-cover-image
- baoyu-danger-x-to-markdown
- baoyu-url-to-markdown
