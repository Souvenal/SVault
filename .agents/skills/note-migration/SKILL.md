---
name: note-migration
description: >
  Migrate existing notes to LLM-Wiki knowledge base. Auto-detects topics, splits multi-subject 
  content, establishes cross-references. Triggers on: "migrate to wiki", "迁移到 wiki",
  "/note-migration", or when user specifies a buffer path for migration.
---

Migrate notes from buffer/ to wiki/ with proper structure and wikilinks.

## Pre-flight Checks

1. **Confirm source path** - User must specify note location. If not specified, ask:
   ```
   Specify note path:
   - Single file: `buffer/Obsidian/Archive/Render/PBR.md`
   - Directory: `buffer/Obsidian/Archive/Render/`
   ```

2. **Read wiki state** - `wiki/_master_index.md` and relevant `_index.md`

## Migration Flow

### Step 1: Read source notes

### Step 2: Analyze structure

Identify:
- Main topic
- Sub-topics (can stand alone?)
- Related concepts
- Existing wikilinks

### Step 3: Decide split strategy

- Single topic → one wiki page
- Multi-topic → split into multiple pages

Split criteria:
- Each `##` heading can be understood independently?
- Each part has standalone knowledge value?
- Clear associations for wikilinks?

### Step 4: Determine target topic

Check `wiki/_master_index.md`:
- Topic exists → add to it
- Topic not exists → ask user:
   ```
   Detected new topic [TOPIC_NAME], not in wiki.
   
   Options:
   1. Create new topic `[TOPIC_NAME]`
   2. Add to existing topic `[EXISTING_TOPIC]`
   3. Other suggestions?
   ```

### Step 5: Create/update wiki pages

**Must follow wiki-voice style.**

Page structure:
```markdown
# [Page Title]

[One-sentence summary]

---

## [Content]

[Follow wiki-voice style]
```

Page naming:
- Chinese titles preferred
- Technical terms in English
- Avoid long titles

### Step 6: Establish links

- Internal: `[[PageName]]` between pages
- Source reference (optional, user preference):
  ```markdown
  ---
  source: buffer/path/to/file.md
  ```

### Step 7: Update index files

**Topic `_index.md`:**
```markdown
- [[NewPage]] — one-line description
```

**Topic `_log.md`:**
```markdown
## [YYYY-MM-DD] MIGRATE | <Source Filename> | <Number of pages created>
```

**Root `_log.md`:**
```markdown
## [YYYY-MM-DD] MIGRATE | <Source Path> | <Number of pages> pages migrated

**Created**:
- `wiki/Topic/Page.md` — description

**Topic Log**: [[Topic/_log]]
```

**`_master_index.md`** (if new topic):
```markdown
- [[Topic/_index|Topic]] — one-line description
```

## Migration Report

Output summary:
```markdown
## Migration Complete

**Source**: buffer/path/to/notes/

**Created pages**:
- wiki/Topic/Page.md — description

**Updated indexes**:
- wiki/Topic/_index.md (+N entries)
- wiki/Topic/_log.md (new entry)
- wiki/_log.md (new entry)
- wiki/_master_index.md (new topic: Topic)

**Established links**:
- PageA.md ↔ PageB.md (bidirectional wikilink)

**Pending user action**:
- Move source file to raw/? (user action)
```

## Special Cases

- **Code snippets** - preserve format, add inline comments
- **External links** - keep reference format:
  ```markdown
  ref: https://example.com/article
  
  [这篇文章](https://example.com/article)描述了...
  ```
- **Images/assets** - preserve paths, note for user
- **Conflicts** - mark but don't overwrite, prompt user

## Quality Checklist

- [ ] Each page has clear topic
- [ ] Follows wiki-voice style
- [ ] Wikilinks established
- [ ] `_index.md` updated
- [ ] `_log.md` updated
- [ ] Source reference added

## Notes

1. Never modify source files in buffer/
2. Never overwrite existing wiki content
3. Preserve wikilink semantics
4. Chinese content preferred, technical terms excepted
