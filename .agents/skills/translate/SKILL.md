---
name: Translate Raw
description: Translate raw files into clean wiki entries. Use this skill when the user asks to process, translate, or organize the /raw folder into the wiki.
---

# Instruction

Read everything in `/raw`. Update or create matching markdown files in `/wiki` so the wiki reflects what's in `/raw`. Then move each processed file from `/raw` to `/archive`.

Rules:

- One topic per file in `/wiki`, kebab-case filenames.
- ALWAYS use `view_file` to read `/templates/wiki-template.md` and use it to format the newly generated files in `/wiki`.
- Merge into existing entries, never overwrite blindly.
- Link related entries with relative markdown links.
- Move processed files from `/raw` to `/archive` with the same filename. Never delete.
- If a raw note relates to a folder in `/projects`, also surface it in that project's notes.
- When uncertain, note it inside the entry rather than guessing.
