---
title: "Angular Optimistic UI Updates"
type: wiki-entry
project: "clinical-triage"
createdAt: 2026-06-28T02:50:00
modifiedAt: 2026-06-28T02:50:00
target_agents:
  - frontend
related_tools:
  - angular
tags:
  - architecture-pattern
  - ux
  - performance
---

# Angular Optimistic UI Updates

> **🤖 Agent Routing:** Frontend agents should read this to understand how to dramatically improve perceived performance (UX) by mutating local state before the API responds.

> **🧠 LLM Context:** This pattern is extracted from `app.ts` in the Clinical Triage project. It demonstrates how to achieve a "Zero-Latency" feel when updating or deleting patient records on a Kanban board.

## 📌 The Problem
Traditional flow: User clicks "Delete" -> Show loading spinner -> Wait 1-2 seconds for API -> API returns success -> Re-fetch data or update UI. 
This makes the application feel sluggish and unresponsive, especially on slow network connections.

## 🛡️ The Solution (Optimistic UI)
"Optimistic UI" means we *assume* the API request will succeed. We update the UI instantly, and only roll back or show an error if the API actually fails.

### Example 1: Updating Status (Drag & Drop / Buttons)

```typescript
  updatePatientStatus(id: number | undefined, newStatus: string) {
    if (!id) return;

    // 1. OPTIMISTIC UPDATE: Find the item in the local array
    const index = this.patientsList.findIndex((p) => p.id === id);
    if (index !== -1) {
      // 2. Mutate the state immediately
      this.patientsList[index].status = newStatus as any; 
      
      // 3. Force Angular to redraw the screen instantly (No API wait!)
      this.cdr.detectChanges(); 
    }

    // 4. Send the actual command to Spring Boot in the background
    this.patientService.updateStatus(id, newStatus).subscribe({
      next: () => {
        this.displayToast(`Moved to ${newStatus.replace('_', ' ')}.`);
        // If it fails, we would theoretically catch the error and revert the state here.
      },
      error: (err) => console.error('Error updating status:', err),
    });
  }
```

### Example 2: Deleting a Record

```typescript
  deleteCase(id: number | undefined) {
    if (!id) return;

    if (confirm('Are you sure you want to delete this case?')) {
      // 1. OPTIMISTIC UPDATE: Kick the item out of the array immediately
      this.patientsList = this.patientsList.filter((p) => p.id !== id);
      
      // 2. Force screen redraw
      this.cdr.detectChanges();

      // 3. Send delete command to the server in the background
      this.patientService.deletePatient(id).subscribe({
        next: () => {
          this.displayToast('Case deleted successfully.');
        },
        error: (err) => {
           console.error('Error deleting patient:', err);
           // Rollback logic would go here
        },
      });
    }
  }
```

## ✨ Key Takeaways
- Use `ChangeDetectorRef.detectChanges()` to force Angular to paint the new state instantly.
- Always perform the array mutation *before* you call the `.subscribe()` on the HTTP service.
- For enterprise-grade apps, ensure you have rollback logic in the `error` callback of the subscription just in case the server fails.

## 🔗 Related Context
- [[clinical-triage-architecture]]
