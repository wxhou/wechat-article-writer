---
name: wechat-article-writer
description: 公众号文章创作与发布。完整流程：自动场景识别 → 获取内容 → 质量检查 → 写作/翻译 → AI配图 → 封面图 → 格式优化 → 发布。使用场景：(1) X/网页链接 (2) 英文文章翻译 (3) 标题创作 (4) 直接发布
---

# 公众号文章创作 Skill

自动化公众号文章创作与发布全流程。

## 核心原则：你的写作风格

### 开头写法（3选1）
1. **反问引入** - "你们有没有想过...？"
2. **场景切入** - 从一个具体问题/故事开始
3. **痛点共鸣** - "我之前一直用XXX，直到..."

### 中间写法
- **个人经历**：融入自己的踩坑故事（"我第一次装XXX，光是解决XXX就花了半天"）
- **口语化表达**：说白了、懂的都懂、省心、舒服
- **对比分析**：优缺点、方案对比
- **代码演示**：技术文章必须有可运行代码
- **细节丰富**：避免泛泛而谈

### 结尾写法
- **互动感**：引发思考和讨论
- **个人感悟**：分享真实想法
- **开放结尾**：不把话说死

### 禁止的句式（AI 味重灾区）
```
❌ 首先...其次...最后...
❌ 需要注意的是...
❌ 综上所述...
❌ 相信...
❌ 由此可见...
❌ 值得注意...
❌ 总体来说...
```

### 推荐表达
```
✅ 说白了就是...
✅ 懂的都懂...
✅ 你们有没有遇到过...
✅ 我之前...
✅ 举个例子...
✅ 试试就知道了...
✅ 反正是...不亏
```

---

## 完整流程

```
自动识别 → 获取内容 → 质量检查 → 写作 → AI配图 + 封面图 → 格式优化 → 发布
```

---

## 触发条件

| 输入类型 | 处理方式 |
|---------|---------|
| X/网页链接 | 获取内容 → 写作 → 配图 → 封面 → 发布 |
| 英文文章 | 翻译 → 写作 → 配图 → 封面 → 发布 |
| 仅有标题 | 调研 → 写作 → 配图 → 封面 → 发布 |
| 直接发布 | 跳过草稿，直接完整流程 |

---

## Step 1: 获取内容

**X/网页链接**:
```bash
npx -y bun ~/.claude/skills/baoyu-danger-x-to-markdown/scripts/main.ts <url>
```

**仅有标题**:
```bash
cd "/Users/wxhou/Documents/obsidian-note/微信公众号"
content init 文章标题 --workspace wechat
```

然后在 Claude Code 中使用九步写作流程：

```
1. /specify    - 定义创作需求
2. /topic      - 选题讨论（提供3-4个方向）
3. /research   - 信息搜索与调研 ⭐
4. /collect    - 搜索个人素材库
5. /write      - 创作初稿
6. /review     - 三遍审校（降低AI味）
7. /images     - 配图建议
8. /check      - 发布前检查
9. /publish    - 发布指南
```

**推荐流程**：`specify → topic → research → write → review → images → check`

---

## Step 2: 质量检查

### 错别字检测
```bash
grep -E "份饭|成份|其它|一部份|帐号|帐户" article.md
```

### AI 味检测（必须检查）
```bash
# 检查地雷句式
grep -E "首先|其次|最后|综上所述|值得注意|总体来说|需要注意的是|由此可见|相信" article.md
```

发现后全部改写为口语化表达。

### 可读性检查
- 单句 ≤ 40 字
- 段落 ≤ 3 行
- 使用中文标点

---

## Step 3: 写作（重点）

**每个段落都要有"人味"**：
- 避免干巴巴的说明文
- 用故事带出知识点
- 让读者觉得你在和他聊天

**技术文章必备**：
- 可运行的代码示例
- 实际踩坑经历
- 和其他工具的对比

---

## Step 4: AI 配图

使用 slash 命令调用：
```bash
/baoyu-article-illustrator article.md
```

**备用方案** (baoyu-image-gen):
```bash
python3 ~/.claude/skills/baoyu-image-gen/scripts/modelscope_fallback.py "prompt" output.png
```

---

## Step 5: 封面图

使用 slash 命令调用：
```bash
/baoyu-cover-image article.md --style blueprint
```

---

## Step 6: 格式优化

```bash
npx -y bun ~/.claude/skills/baoyu-markdown-to-html/scripts/main.ts article.md --theme grace
```

---

## Step 7: 发布

```bash
npx -y bun ~/.claude/skills/baoyu-post-to-wechat/scripts/wechat-api.ts \
  article.html \
  --cover cover.png \
  --author "博主"
```

---

## 并行处理

这两个 skill 用 slash 命令，无法并行。串行执行即可。

---

## 错误处理

| 问题 | 解决 |
|-----|------|
| 链接失败 | 用 baoyu-url-to-markdown |
| 图片失败 | modelscope_fallback.py |
| API 白名单 | 加 IP 到微信后台 |

**关闭浏览器**:
```bash
mcp__chrome-devtools__close_page --pageId <ID>
```

---

## 项目目录

- 文章: `/Users/wxhou/Documents/obsidian-note/微信公众号/`
- 附件: `attachments/`

## 依赖 Skills

**可用 slash 命令**：
- `/baoyu-article-illustrator` - 文章配图
- `/baoyu-cover-image` - 封面图生成
- `/baoyu-url-to-markdown` - 网页转 Markdown
- `/baoyu-danger-x-to-markdown` - X 转 Markdown

**需要用 npx bun**：
- `baoyu-markdown-to-html` - Markdown 转 HTML
- `baoyu-post-to-wechat` - 发布到公众号
