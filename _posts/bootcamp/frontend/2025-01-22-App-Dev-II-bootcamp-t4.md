---
title: "BootCamp Tutorial 4: Vue.js Advanced API Integration with Role-Based Access"  
date: "2025-02-12 00:00:00"
categories: [bootcamp]
tags: [bootcamp, vue, flask]
---

<!-- # **🔥 Vue.js Advanced API Integration with Role-Based Access** -->
<!-- --- -->

## **📌 Learning Objectives**
In this tutorial, we will integrate API endpoints with Vue.js while maintaining **separation of concerns** by keeping API calls modular. You will learn:
✅ **Fetching and displaying tasks**  
✅ **Managing users and approving/rejecting them**  
✅ **Updating and deleting tasks**  
✅ **Assigning tasks to users**  
✅ **Displaying statistics for Admin & Manager**  

By the end, you will have a complete Vue.js dashboard to **manage tasks & users efficiently**.

---

# **📌 Setting Up API Handling in Vue**
Before creating pages, let's set up a file to handle API interactions in a structured way.

📂 `src/services/api.js`
```javascript
import axios from "axios";

const API_URL = "http://127.0.0.1:5000";
const token = localStorage.getItem("token");

const api = axios.create({
  baseURL: API_URL,
  headers: {
    "Content-Type": "application/json",
    Authorization: `Bearer ${token}`,
  },
});

export const fetchTasks = () => api.get("/tasks");
export const fetchTaskById = (taskId) => api.get(`/task/${taskId}`);
export const updateTask = (taskId, data) => api.put(`/task/${taskId}`, data);
export const deleteTask = (taskId) => api.delete(`/task/${taskId}`);
export const fetchPendingUsers = () => api.get("/users/pending");
export const approveUser = (userId) => api.put(`/users/${userId}/approve`);
export const rejectUser = (userId) => api.delete(`/users/${userId}/reject`);
export const assignTask = (taskId, userId) => api.put(`/task/${taskId}/assign`, { user_id: userId });
export const fetchStats = () => api.get("/stats");

export default api;
```

---

# **1️⃣ Fetching & Displaying Tasks**
📂 `src/views/TaskList.vue`
{% raw %}
```vue
<template>
  <div>
    <h2>Task Management</h2>
    <button @click="fetchTaskList">Refresh</button>
    <table>
      <thead>
        <tr>
          <th>ID</th>
          <th>Title</th>
          <th>Description</th>
          <th>Status</th>
          <th>Deadline</th>
          <th>Actions</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="task in tasks" :key="task.id">
          <td>{{ task.id }}</td>
          <td>{{ task.title }}</td>
          <td>{{ task.description || 'N/A' }}</td>
          <td>{{ task.status }}</td>
          <td>{{ task.deadline || 'No deadline' }}</td>
          <td>
            <button @click="editTask(task)">Edit</button>
            <button @click="deleteTaskById(task.id)">Delete</button>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
import { fetchTasks, deleteTask } from "@/services/api.js";

export default {
  data() {
    return {
      tasks: [],
    };
  },
  methods: {
    async fetchTaskList() {
      const response = await fetchTasks();
      this.tasks = response.data;
    },
    async deleteTaskById(taskId) {
      if (confirm("Are you sure you want to delete this task?")) {
        await deleteTask(taskId);
        this.fetchTaskList();
      }
    },
    editTask(task) {
      this.$router.push({ name: "EditTask", params: { taskId: task.id } });
    },
  },
  mounted() {
    this.fetchTaskList();
  },
};
</script>
```
{% endraw %}

---

# **2️⃣ Editing Tasks**
📂 `src/views/EditTask.vue`
```vue
<template>
  <div>
    <h2>Edit Task</h2>
    <form @submit.prevent="updateTaskData">
      <label>Title:</label>
      <input v-model="task.title" required />
      <label>Description:</label>
      <input v-model="task.description" />
      <label>Status:</label>
      <select v-model="task.status">
        <option>pending</option>
        <option>in-progress</option>
        <option>completed</option>
      </select>
      <button type="submit">Update Task</button>
    </form>
  </div>
</template>

<script>
import { fetchTaskById, updateTask } from "@/services/api.js";

export default {
  data() {
    return {
      task: {
        title: "",
        description: "",
        status: "pending",
      },
    };
  },
  async mounted() {
    const taskId = this.$route.params.taskId;
    const response = await fetchTaskById(taskId);
    this.task = response.data;
  },
  methods: {
    async updateTaskData() {
      await updateTask(this.$route.params.taskId, this.task);
      this.$router.push("/tasks");
    },
  },
};
</script>
```

---

# **3️⃣ User Approvals**
📂 `src/views/UserApproval.vue`
{% raw %}
```vue
<template>
  <div>
    <h2>Pending User Approvals</h2>
    <table>
      <thead>
        <tr>
          <th>ID</th>
          <th>Username</th>
          <th>Email</th>
          <th>Actions</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="user in pendingUsers" :key="user.id">
          <td>{{ user.id }}</td>
          <td>{{ user.username }}</td>
          <td>{{ user.email }}</td>
          <td>
            <button @click="approveUserById(user.id)">Approve</button>
            <button @click="rejectUserById(user.id)">Reject</button>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
import { fetchPendingUsers, approveUser, rejectUser } from "@/services/api.js";

export default {
  data() {
    return {
      pendingUsers: [],
    };
  },
  async mounted() {
    const response = await fetchPendingUsers();
    this.pendingUsers = response.data;
  },
  methods: {
    async approveUserById(userId) {
      await approveUser(userId);
      this.fetchPendingUsers();
    },
    async rejectUserById(userId) {
      await rejectUser(userId);
      this.fetchPendingUsers();
    },
  },
};
</script>
```
{% endraw %}

---

# **4️⃣ Assigning Tasks**
📂 `src/views/AssignTask.vue`

{% raw %}
```vue
<template>
  <div>
    <h2>Assign Task</h2>
    <label>Select User:</label>
    <select v-model="selectedUser">
      <option v-for="user in users" :key="user.id" :value="user.id">
        {{ user.username }}
      </option>
    </select>
    <button @click="assignTaskToUser">Assign</button>
  </div>
</template>

<script>
import { assignTask, fetchPendingUsers } from "@/services/api.js";

export default {
  data() {
    return {
      users: [],
      selectedUser: null,
    };
  },
  async mounted() {
    const response = await fetchPendingUsers();
    this.users = response.data;
  },
  methods: {
    async assignTaskToUser() {
      await assignTask(this.$route.params.taskId, this.selectedUser);
      alert("Task Assigned!");
    },
  },
};
</script>
```
{% endraw %}

---

# **5️⃣ Fetching System Statistics**
📂 `src/views/Stats.vue`
{% raw %}
```vue
<template>
  <div>
    <h2>System Statistics</h2>
    <p>Total Users: {{ stats.total_users }}</p>
    <p>Total Tasks: {{ stats.total_tasks }}</p>
    <p>Completed Tasks: {{ stats.completed_tasks }}</p>
  </div>
</template>

<script>
import { fetchStats } from "@/services/api.js";

export default {
  data() {
    return {
      stats: {},
    };
  },
  async mounted() {
    const response = await fetchStats();
    this.stats = response.data;
  },
};
</script>
```
{% endraw %}

---

# **🎯 Summary**
- 🔥 **Task Management (List, Edit, Delete, Assign)**
- ✅ **User Approvals (Approve/Reject)**
- 📊 **Fetching Statistics**
- 🛠️ **Role-Based API Handling**

This completes our **Vue.js API Integration with Role-Based Access**! 🚀