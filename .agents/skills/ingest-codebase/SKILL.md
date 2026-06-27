---
name: Ingest Codebase
description: Extract knowledge from a project folder and create LLM-friendly, Obsidian-optimized wiki entries to save context window.
---

# Instruction

The user wants to ingest an entire codebase or project folder into the `/wiki` system to act as an "LLM Wiki". This allows future agents to read the summarized markdown files instead of scanning the entire codebase, saving context.

1. **Target**: Ask the user for the absolute or relative path to the directory they want to ingest. Do NOT expect the codebase to be inside this workspace.
2. **Analyze**: Read the source code, architecture, and configuration of the specified project. (Automatically ignore `node_modules`, `dist`, `.git`, and build artifacts).
3. **Summarize into Wiki (Deep & Comprehensive)**: Create or update `.md` files in the `/wiki` directory. You MUST be extremely thorough, especially for full-stack projects. Extract database schemas, API route documentation, authentication flows, and frontend state management. Structure the knowledge logically (e.g., `<project>-architecture.md`, `<project>-api.md`, `<project>-db-schema.md`, `<project>-components.md`).
4. **Extract Snippets Aggressively**: Do not hold back on extracting highly reusable code. If you find custom hooks, complex configurations (e.g., Dockerfile, CI/CD), utility functions, DB models, or middleware, extract them and save them as markdown files in the `/snippets` directory (e.g., `/snippets/<project>-auth-middleware.md`).
5. **Document Patterns**: Deeply analyze and identify distinct architectural patterns, bug fixes, or system-wide design decisions. Document them in the `/patterns` directory (e.g., `/patterns/<project>-state-management.md`, `/patterns/<project>-repository-pattern.md`).
6. **Format for Obsidian & Multi-Agent LLMs**: ALWAYS format the output for ALL generated markdown files using the templates provided in the `/templates` directory (e.g., `wiki-template.md`, `snippet-template.md`, `pattern-template.md`).
   - Use `view_file` to read the appropriate template before writing new files.
   - Assign `target_agents` using formal developer roles (e.g., `architect`, `frontend`, `backend`, `devops`, `qa`, `security`, `data`, `all`) so specific subagents know if this file is for them.
   - Include `createdAt` and `modifiedAt`.
7. **No moving**: Do NOT move the source code files. Leave the original project files exactly where they are.
