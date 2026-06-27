---
name: Refactor Knowledge
description: Scan /snippets and /patterns for duplicates or similarities. Consolidate them into global files and update links. Run this when the user types /refactor.
---

# Instruction

The user wants to deduplicate their knowledge base. As the user ingests multiple projects, there might be overlapping snippets or architectural patterns (e.g., multiple projects using the same JWT authentication logic or similar State Management hooks).

This skill performs a "Knowledge Consolidation".

## Workflow

1. **Semantic Scan**:
   - Scan the `/snippets` and `/patterns` directories.
   - Look for files that share similar concepts, tags, or titles. 

2. **Propose Merge**:
   - If you find files that describe highly similar or overlapping concepts (e.g. `projectA-auth.md` and `projectB-auth.md`), propose to merge them into a single, generic "Global" file (e.g., `global-jwt-auth.md`).
   - Stop and ask the user for approval. Show them exactly which files you plan to merge and what the new filename will be.

3. **Consolidate (Upon Approval)**:
   - Read the contents of the redundant files.
   - Extract the core concepts, code, and explanations, and combine them logically into the new global file. 
   - ALWAYS use `view_file` to read the appropriate template from `/templates` (e.g. `pattern-template.md` or `snippet-template.md`) to format the new consolidated file.
   - Ensure the new file has the standard YAML frontmatter, setting `project: "global"`.

4. **Update Links**:
   - Search through the `/wiki` directory to find any markdown files that linked to the old, redundant files (e.g., `[[projectA-auth]]`).
   - Safely update those links to point to the new unified file (e.g., `[[global-jwt-auth]]`).

5. **Cleanup**:
   - Delete the old redundant files from `/snippets` and `/patterns` using the terminal `rm` or `Remove-Item` command (since there's no native file deletion tool).
   - Inform the user that the knowledge base is now cleaner and more consolidated!
