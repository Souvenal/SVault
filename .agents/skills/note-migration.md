# Note Migration - 笔记迁移到 Wiki

将现有笔记迁移到 LLM-Wiki 知识库，自动识别 topic、拆分多主题内容、建立关联引用。

---

## 触发条件

用户明确请求迁移笔记到 wiki，例如：
- "把这个笔记迁移到 wiki"
- "迁移 buffer/Obsidian/Archive/Render 目录下的笔记"
- "把 PBR.md 迁移到 wiki"

---

## 前置检查

### 1. 确认源路径

用户必须明确指定笔记位置。如果未指定，询问：

```
请指定要迁移的笔记路径：
- 单个文件：`buffer/Obsidian/Archive/Render/PBR.md`
- 目录：`buffer/Obsidian/Archive/Render/`
```

### 2. 读取 wiki 现状

迁移前必须读取：
- `wiki/_master_index.md` — 了解现有 topic 列表
- 相关 topic 的 `_index.md` — 了解现有页面

---

## 迁移流程

### Step 1: 读取源笔记

```
Read the specified note file(s)
```

### Step 2: 分析笔记结构

识别笔记中的：

1. **主主题** — 笔记核心主题
2. **子主题** — 可独立成篇的部分
3. **关联概念** — 与其他 wiki 条目的潜在关联
4. **现有 wikilinks** — 笔记中已有的 `[[链接]]`

### Step 3: 决定拆分策略

**单主题笔记** → 一个 wiki 页面
- 例如：`TLS.md` → `wiki/Network/TLS.md`

**多主题笔记** → 拆分为多个 wiki 页面
- 例如：`PBR.md` 包含 BRDF、Fresnel、微表面模型 → 拆分为多个条目

**拆分判断标准：**
- 每个 `##` 二级标题是否可独立理解？
- 各部分是否有独立的知识价值？
- 是否有明确的关联可以建立 wikilink？

### Step 4: 确定目标 Topic

对照 `wiki/_master_index.md` 判断：

1. **Topic 已存在** → 直接添加到该 topic
2. **Topic 不存在** → 询问用户是否创建新 topic

```
检测到新主题 [TOPIC_NAME]，当前 wiki 中不存在该 topic。

选项：
1. 创建新 topic `[TOPIC_NAME]`
2. 添加到现有 topic `[EXISTING_TOPIC]`
3. 其他建议？

请选择：
```

### Step 5: 创建/更新 Wiki 页面

**必须遵循 wiki-voice skill 的写作风格。**

每个 wiki 页面：

```markdown
# [页面标题]

[一句话概括]

---

## [主体内容]

[按 wiki-voice 风格编写]
```

**页面命名规则：**
- 使用中文标题
- 技术术语可保留英文
- 避免过长标题

### Step 6: 建立关联

**内部 wikilinks：**
- 页面之间用 `[[页面名]]` 互相引用
- 相关概念建立双向链接

**源文件引用：**
- 在页面底部添加来源标注：

```markdown
---
source: buffer/Obsidian/Archive/Render/PBR.md
```

### Step 7: 更新索引文件

**Topic `_index.md`：**
```markdown
- [[新页面名]] — 一句话描述
```

**Topic `_log.md`：**
```markdown
## [YYYY-MM-DD] MIGRATE | <Source Filename> | <Number of pages created>
```

**Root `_log.md`：**
```markdown
## [YYYY-MM-DD] MIGRATE | <Source Path> | <Number of pages> pages migrated

**Created**:
- `wiki/<Topic>/<Page>.md` — 描述

**Topic Log**: [[<Topic>/_log]]
```

**`_master_index.md`** (如果有新 topic):
```markdown
- [[<Topic>/_index|<Topic>]] — 一句话描述
```

---

## 迁移报告

完成后输出：

```markdown
## 迁移完成

**源文件**: buffer/Obsidian/Archive/Render/PBR.md

**创建页面**:
- wiki/PBR/BRDF.md — BRDF 能量守恒与微表面模型
- wiki/PBR/Fresnel.md — Fresnel 方程与 Schlick 近似

**更新索引**:
- wiki/PBR/_index.md (+2 entries)
- wiki/PBR/_log.md (new entry)
- wiki/_log.md (new entry)
- wiki/_master_index.md (new topic: PBR)

**建立关联**:
- BRDF.md ↔ Fresnel.md (双向 wikilink)

**待用户确认**:
- 源文件是否移入 raw/？（用户手动操作）
```

---

## 特殊情况处理

### 笔记包含代码片段

- 代码块保持原格式
- 添加必要注释
- 代码说明用 Q&A 风格

### 笔记包含外部链接

保留外部引用格式：
```markdown
ref: https://example.com/article

[这篇文章](https://example.com/article)描述了...
```

### 笔记包含图片/资源

- 图片保留原路径引用（buffer 中的 assets）
- 提示用户：图片需要在移入 raw/ 时一并处理

### 笔记内容与现有 wiki 冲突

1. 标记冲突点
2. 不自动覆盖，保留两者
3. 在报告中提示用户确认

---

## 质量检查清单

迁移完成后检查：

- [ ] 每个页面有明确主题
- [ ] 遵循 wiki-voice 写作风格
- [ ] 所有相关页面已建立 wikilink
- [ ] `_index.md` 已更新
- [ ] `_log.md` 已更新
- [ ] 源文件引用已标注

---

## 与 wiki-voice Skill 协作

**关键点：所有 wiki 内容编写必须调用 wiki-voice skill。**

```
skill(name="wiki-voice")
```

wiki-voice 确保：
- 简洁直接，无废话
- 技术术语中英混用
- Q&A 风格解释概念
- 代码与解释紧密结合
- 无 AI 味道词汇

---

## 示例：迁移 PBR.md

**输入**:
```
迁移 buffer/Obsidian/Archive/Render/PBR.md
```

**处理流程**:

1. 读取 PBR.md，发现包含：
   - BRDF 能量守恒
   - 微表面模型
   - Fresnel 与 Schlick 近似

2. 判断为多主题笔记，拆分为 3 个页面

3. 检测到 "Render" topic 不存在，询问用户

4. 用户确认创建 "PBR" topic

5. 创建页面：
   - `wiki/PBR/BRDF.md`
   - `wiki/PBR/Fresnel.md`

6. 更新所有索引文件

7. 输出迁移报告

---

## 注意事项

1. **不修改源文件** — buffer/ 中的笔记保持不变，用户决定是否移入 raw/
2. **不覆盖现有内容** — 如有冲突，保留两者并提示
3. **保持 wikilink 语义** — 笔记中的 `[[链接]]` 尽量保留或转换为正确的 wiki 引用
4. **中文优先** — wiki 页面内容使用中文，技术术语除外
