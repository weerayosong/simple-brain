---
title: "React Context API (Global State)"
type: wiki-entry
project: "balance-tracker"
createdAt: 2026-06-28T03:09:00
modifiedAt: 2026-06-28T03:09:00
target_agents:
  - frontend
related_tools:
  - react
tags:
  - snippet
  - state-management
  - pattern
---

# React Context API (Global State)

> **🤖 Agent Routing:** Frontend agents should read this to understand how to manage global state (e.g., User Authentication, App Data) natively in React without relying on heavy external libraries like Redux.

> **🧠 LLM Context:** This snippet is extracted from `TaskContext.jsx` in the Balance Tracker frontend. It demonstrates how to wrap the application in a `Provider` to make API data available to any deeply nested child component via `useContext`.

## Code Snippet

```javascript
import { createContext, useState, useEffect } from "react";
import { taskService } from "../services/taskService";

// 1. Create the Context
export const TaskContext = createContext();

// 2. Create the Provider Component
export const TaskProvider = ({ children }) => {
    // 3. Define the Global State
    const [tasks, setTasks] = useState([]);
    const [isLoading, setIsLoading] = useState(true);

    // 4. Data Fetching Logic (Runs once when app mounts)
    useEffect(() => {
        const fetchTasks = async () => {
            try {
                setIsLoading(true);
                const data = await taskService.getAllTasks();
                if (data.success) setTasks(data.data);
            } catch (error) {
                console.error("Error fetching tasks:", error);
            } finally {
                setIsLoading(false);
            }
        };
        fetchTasks();
    }, []);

    // 5. Global Actions (Mutations)
    const addTask = async (newTaskData) => {
        try {
            const data = await taskService.createTask(newTaskData);
            if (data.success) {
                // Update Global State
                setTasks((prev) => [...prev, data.data]); 
            }
        } catch (error) {
            console.error("Error adding task:", error);
        }
    };

    // 6. Provide the State and Actions down the component tree
    return (
        <TaskContext.Provider
            value={{
                tasks,
                isLoading,
                addTask,
            }}
        >
            {children}
        </TaskContext.Provider>
    );
};
```

## ✨ Implementation Details

### Setup (`main.jsx`)
To use this, you must wrap your top-level `<App />` component in the Provider:
```javascript
import { TaskProvider } from './contexts/TaskContext';

ReactDOM.createRoot(document.getElementById('root')).render(
  <TaskProvider>
    <App />
  </TaskProvider>
);
```

### Consumption (`AnyComponent.jsx`)
Any child component can now access the data instantly without "Prop Drilling":
```javascript
import { useContext } from 'react';
import { TaskContext } from '../contexts/TaskContext';

export default function Dashboard() {
    const { tasks, isLoading, addTask } = useContext(TaskContext);
    
    if (isLoading) return <p>Loading...</p>;
    
    return <div>You have {tasks.length} tasks!</div>;
}
```

## 🔗 Related Context
- [[balance-tracker-architecture]]
