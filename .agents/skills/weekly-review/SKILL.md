---
name: Weekly Review
description: Summarize achievements over the past 7 days by analyzing recent wiki changes or git history, and align them with current goals.
---

# Instruction

The user wants to review their progress over the past week to ensure they are moving towards their goals defined in `CONTEXT.md`.

When the user triggers this skill (e.g., by saying "Weekly review" or "สรุปงานสัปดาห์นี้ให้หน่อย"):

1. **Analyze Activity**:
   - Check what files have been created or modified recently in the `/wiki`, `/snippets`, and `/patterns` directories.
   - You can also look at the `git log` (using your terminal tools) for the past 7 days to see commit messages.
2. **Align with Goals**:
   - Read the `CONTEXT.md` file to recall the user's current high-level goals.
3. **Generate Report**:
   - Create a markdown report for the user outlining:
     - 🏆 **Achievements**: What new knowledge was ingested or what patterns were created.
     - 🎯 **Goal Alignment**: How these achievements match the "Current Focus" in `CONTEXT.md`.
     - 💡 **Next Steps**: Suggestions for what the user should focus on next week based on their goals.
   - Output this report directly in the chat as a well-formatted markdown response (do not save it to the wiki unless the user explicitly asks you to).
