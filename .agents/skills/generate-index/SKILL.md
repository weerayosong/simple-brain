---
name: Generate Index
description: Scans all markdown files in the workspace and generates a centralized INDEX.md map of content.
---

# Generate Index Skill

## Prerequisites
- This skill requires traversing `/wiki`, `/snippets`, and `/patterns` directories.
- You must read the YAML frontmatter of the files to organize the index.

## Instructions
When the user asks to generate or update the index, follow these steps:

1. Use the `list_dir` tool on `/wiki`, `/snippets`, and `/patterns`.
2. For each file found, you may read its YAML frontmatter (if you don't already have it in context) to extract the `title`, `project`, and `tags`.
3. Create or overwrite a file named `INDEX.md` at the root of the workspace (`c:/Users/weera/jsd12/simple-brain/INDEX.md`).
4. Format the `INDEX.md` clearly. Group the files by **Project** first, then by **Category** (Wiki, Snippets, Patterns).
5. Use bullet points and provide standard Obsidian links (`[[filename-without-extension]]`) or markdown file links.
6. Present the result to the user.

## Example Format for INDEX.md
```markdown
# 🗺️ Simple Brain Index (MOC)

## 🚀 Project: typesith
### Wiki (Deep Context)
- [[typesith-architecture]]: Typesith Full-Stack Architecture
- [[typesith-data-model]]: Typesith Data Model

### Snippets
- [[typesith-useForceCodex]]: useForceCodex Custom Hook

## 🚀 Project: nextzy-gamification
...
```
