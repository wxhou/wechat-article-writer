---
name: wechat-article-writer
description: 公众号文章创作与发布。完整流程：自动场景识别 → 获取内容 → 质量检查 → 写作/翻译 → AI配图 → 封面图 → 格式优化 → 发布。使用场景：(1) X/网页链接 (2) 英文文章翻译 (3) 标题创作 (4) 直接发布
---

# 公众号文章创作 Skill

自动化公众号文章创作与发布全流程。

---

## 核心原则

### 你的写作风格（从博客分析）

| 特点 | 示例 |
|-----|------|
| **第一人称** | "我", "我的" |
| **口语化** | "好了", "可以看到", "是不是非常简单呢" |
| **互动邀请** | "欢迎评论区交流", "有问题欢迎反馈" |
| **个人标签** | 融入个人经历/背景 |
| **代码驱动** | 每个知识点配有完整可运行代码 |
| **结构清晰** | 分层标题 + 表格整理信息 |

### 减少 AI 味

- 避免："首先/其次/最后", "需要注意的是", "总的来说"
- 改为：口语化过渡、场景代入、个人感悟
- 每段不超过 3 行，每句不超过 40 字

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
| 仅有标题 | article-writer 调研 → 写作 → 配图 → 封面 → 发布 |
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

---

## Step 2: 质量检查

### 错别字检测
```bash
grep -E "份饭|成份|其它|一部份|帐号|帐户" article.md
```

### AI 味检测
检查并修改：
- "首先/其次/最后" → 自然过渡
- "需要注意的是" → 删除或改写
- 连续列表 → 改为段落叙述

---

## Step 3: 写作技巧（你的风格）

### 开头写法
- 场景切入：从具体问题/故事开始
- 个人经历：融入 "我" 的经历
- 痛点共鸣：让读者觉得"说的就是我"

### 中间写法
- **代码驱动**：每个知识点配完整代码
- **表格整理**：复杂信息用表格
- **步骤清晰**：分步说明，每步可执行

### 结尾写法
- **互动邀请**："有问题欢迎评论区交流"
- **反馈邀请**："觉得有帮助，点个赞呗"
- **个人感悟**：分享真实想法

### 你的口头禅
```
✅ "好了"
✅ "可以看到"
✅ "是不是非常简单呢"
✅ "欢迎评论区交流"
✅ "有问题欢迎反馈"
```

---

## Step 4: AI 配图

```bash
npx -y bun ~/.claude/skills/baoyu-article-illustrator/scripts/main.ts article.md
```

**备用方案**:
```bash
python3 ~/.claude/skills/baoyu-image-gen/scripts/modelscope_fallback.py "prompt" output.png
```

---

## Step 5: 封面图

```bash
npx -y bun ~/.claude/skills/baoyu-cover-image/scripts/main.ts article.md --style blueprint
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

## 错误处理

| 问题 | 解决 |
|-----|------|
| 链接失败 | 用 baoyu-url-to-markdown |
| 图片失败 | modelscope_fallback.py |
| API 白名单 | 加 IP 到微信后台 |

---

## 项目目录

- 文章: `/Users/wxhou/Documents/obsidian-note/微信公众号/`

## 依赖 Skills

- baoyu-post-to-wechat
- baoyu-cover-image
- baoyu-article-illustrator
- baoyu-markdown-to-html
- baoyu-image-gen
- baoyu-danger-x-to-markdown
