---
name: Catch Up (Update Context)
description: Run a quick conversational interview to find out what the user is currently focusing on, then update CONTEXT.md automatically.
---

# Instruction

The `CONTEXT.md` file is critical for Multi-Agent routing and understanding the user's high-level goals. However, it can become outdated quickly as the user finishes projects or shifts focus.

When the user triggers this skill (e.g., by saying "Catch up" or "Update Context"):

1. **Interview Mode (Grill Me)**:
   - Do NOT update the file immediately.
   - Ask the user 1-2 friendly, conversational questions to find out what has changed in their life/work recently. 
   - Example: *"ช่วงนี้กำลังโฟกัสโปรเจกต์ไหนอยู่ครับ? มีโปรเจกต์ไหนที่ทำเสร็จไปแล้วบ้างไหม?"*
   - Wait for their response.

2. **Analysis & Update**:
   - Once the user answers, analyze their new situation.
   - Use your file editing tools to update `CONTEXT.md`.
   - **Crucial**: Remove outdated goals (e.g., projects that are now finished). Add the new goals, and adjust the "Notes for AI" to match their new focus.

3. **Confirmation**:
   - Show the user a brief summary of what you updated in the `CONTEXT.md` file.
