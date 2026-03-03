---
name: wechat-article-writer
description: 公众号文章创作与发布。完整流程：自动场景识别 → 获取内容 → 质量检查 → 写作/翻译 → AI配图 → 封面图 → 格式优化 → 发布。使用场景：(1) X/网页链接 (2) 英文文章翻译 (3) 标题创作 (4) 直接发布
---

# 公众号文章创作 Skill

自动化公众号文章创作与发布全流程。

## 核心原则

**减少 AI 味的写作技巧**：
1. 避免套路句式：首先/其次/最后、 需要注意的是
2. 增加具体细节：案例、代码、亲身经历
3. 自然过渡：少用列表，用流畅段落
4. 口语化表达：适当使用反问、感叹
5. 真实观点：有自己的判断和思考

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
# 或
npx -y bun ~/.claude/skills/baoyu-url-to-markdown/scripts/main.ts <url>
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

### 可读性检查
- 单句 ≤ 40 字
- 段落 ≤ 3 行
- 使用中文标点

### AI 味检测（新增）
检查并修改以下模式：
- "首先/其次/最后" → 改为自然过渡
- "需要注意的是" → 删除或改写
- "总的来说" → 用口语化表达
- 连续 3 个以上列表 → 改为段落叙述

---

## Step 3: 写作技巧

### 开头写法
- 场景切入：从一个具体问题/故事开始
- 痛点共鸣：让读者觉得"说的就是我"
- 数据吸引：用惊人数据引发好奇

### 中间写法
- 案例驱动：每个观点配一个真实案例
- 代码演示：技术文章必须有可运行代码
- 对比分析：优缺点、方案对比
- 细节丰富：避免泛泛而谈

### 结尾写法
- 行动呼吁：给读者一个具体行动
- 开放问题：引发思考和讨论
- 个人感悟：分享真实想法

### 避免的句式
```
❌ 首先...其次...最后...
❌ 需要注意的是...
❌ 综上所述...
❌ 相信...
❌ 由此可见...
```

### 推荐句式
```
✅ 说到这个...
✅ 我之前...
✅ 你们有没有遇到过...
✅ 说白了就是...
✅ 举个例子...
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

## 并行处理

配图和封面图可并行生成：
```bash
npx -y bun .../baoyu-article-illustrator/... &
npx -y bun .../baoyu-cover-image/... &
wait
```

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

- baoyu-post-to-wechat
- baoyu-cover-image
- baoyu-article-illustrator
- baoyu-markdown-to-html
- baoyu-image-gen
- baoyu-danger-x-to-markdown
