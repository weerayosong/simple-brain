---
title: "React Generic useCrud Hook"
type: wiki-entry
project: "jsd12-full-stack-app"
createdAt: 2026-06-28T03:00:00
modifiedAt: 2026-06-28T03:00:00
target_agents:
  - frontend
related_tools:
  - react
tags:
  - snippet
  - custom-hook
  - api
---

# React Generic useCrud Hook

> **🤖 Agent Routing:** Frontend agents should read this to understand how to encapsulate complex API interactions (GET, POST, PUT, DELETE) and form state into a single, reusable custom React hook.

> **🧠 LLM Context:** This snippet extracts `useCrud.jsx` from the JSD12 Full Stack App (Lab I). It demonstrates Separation of Concerns (SoC) by removing all `fetch` logic and state mutation from the UI components.

## Code Snippet

```javascript
import { useState, useEffect } from "react";

/**
 * A master hook that manages the entire lifecycle of a CRUD resource.
 * @param {string} apiUrl - The base endpoint for the resource (e.g., /api/users)
 * @param {object} initialFormState - The blank state for the creation form
 * @param {string} idKey - The primary key of the resource (default: "id", or "_id" for MongoDB)
 */
export function useCrud(apiUrl, initialFormState, idKey = "id") {
    // 1. Core State
    const [data, setData] = useState([]);
    const [form, setForm] = useState(initialFormState);
    const [editId, setEditId] = useState(null);

    // 2. Initial Data Fetch (GET)
    useEffect(() => {
        const fetchData = async () => {
            try {
                const response = await fetch(apiUrl);
                const result = await response.json();
                if (result.success) setData(result.data);
            } catch (error) {
                console.error(`Fetch Error (${apiUrl}):`, error);
            }
        };
        fetchData();
    }, [apiUrl]);

    // 3. Form Input Handlers
    const handleChange = (e) => {
        const value = e.target.type === "checkbox" ? e.target.checked : e.target.value;
        setForm({ ...form, [e.target.name]: value });
    };

    const handleEditClick = (item) => {
        setEditId(item[idKey]);
        const formCopy = { ...item };
        // Security logic: Don't populate password fields when editing
        if (formCopy.password !== undefined) formCopy.password = "";
        setForm(formCopy);
    };

    const cancelEdit = () => {
        setEditId(null);
        setForm(initialFormState);
    };

    // 4. Create & Update Handler (POST & PUT)
    const handleSubmit = async (e) => {
        e.preventDefault();
        const isEditing = !!editId;
        const url = isEditing ? `${apiUrl}/${editId}` : apiUrl;
        const payload = { ...form };

        // Ensure empty passwords aren't submitted during edits
        if (isEditing && payload.password === "") delete payload.password;

        try {
            const response = await fetch(url, {
                method: isEditing ? "PUT" : "POST",
                headers: { "Content-Type": "application/json" },
                body: JSON.stringify(payload),
            });
            const result = await response.json();

            if (result.success && result.data) {
                if (isEditing) {
                    setData(data.map((item) => item[idKey] === editId ? result.data : item));
                } else {
                    setData([...data, result.data]);
                }
                cancelEdit();
            } else {
                alert(`Error: ${result.error?.message || result.error}`);
            }
        } catch (error) {
            console.error("Submit Error:", error);
        }
    };

    // 5. Delete Handler (DELETE)
    const handleDelete = async (id) => {
        if (!window.confirm("Are you sure you want to delete this?")) return;
        try {
            const response = await fetch(`${apiUrl}/${id}`, { method: "DELETE" });
            const result = await response.json();
            if (result.success) {
                setData(data.filter((item) => item[idKey] !== id));
            }
        } catch (error) {
            console.error("Delete Error:", error);
        }
    };

    // Return all state and handlers to the UI Component
    return {
        data,
        form,
        editId,
        handleChange,
        handleEditClick,
        cancelEdit,
        handleSubmit,
        handleDelete,
    };
}
```

## ✨ Usage Example
Because the logic is abstracted, creating a fully functional CRUD panel becomes trivial:

```javascript
import { useCrud } from "../hooks/useCrud";

export default function UsersPanel() {
    const { data, form, handleChange, handleSubmit, handleDelete } = useCrud(
        "http://api.com/users", 
        { username: "", email: "" }, 
        "_id" // Mongo primary key
    );

    return (
        <div>
            <form onSubmit={handleSubmit}>
                <input name="username" value={form.username} onChange={handleChange} />
                <button type="submit">Save</button>
            </form>
            <ul>
                {data.map(user => (
                    <li key={user._id}>
                        {user.username} 
                        <button onClick={() => handleDelete(user._id)}>Delete</button>
                    </li>
                ))}
            </ul>
        </div>
    );
}
```

## 🔗 Related Context
- [[jsd12-frontend-architecture]]
