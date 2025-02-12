---
title: "BootCamp Tutorial 2: Vue.js Fundamentals: Setting Up Authentication & Routing"  
date: "2025-02-02 00:00:00"
categories: [bootcamp]
tags: [bootcamp, vue, flask]
---

<!-- # **🔥 Vue.js Fundamentals: Setting Up Authentication & Routing** -->

## **📌 Learning Objectives**
Now that you’ve learned the basics of Vue.js authentication with **Login & Signup**, in this tutorial, we will review:
✅ **Vue Project Structure**  
✅ **Configuring Vue Router for Navigation**  
✅ **Handling Authentication (Login & Signup) Using API**  
✅ **Storing JWT Token Locally**  

By the end of this tutorial, you'll have a working **authentication system** with **routing** in Vue.js.

---

# **1️⃣ Setting Up Vue Project Structure**
A typical Vue.js project contains the following key files:

```
📂 vue-auth-project/
 ├── 📂 src/
 │   ├── 📂 components/         # Reusable components (optional)
 │   ├── 📂 views/              # Main pages (Login, Signup, Home)
 │   ├── 📂 router/             # Vue Router Configuration
 │   │   ├── index.js
 │   ├── 📂 assets/             # Static assets (images, CSS)
 │   ├── App.vue                # Root Vue component
 │   ├── main.js                # Main entry file
 ├── index.html                 # Base HTML file
 ├── vite.config.js             # Vite configuration (for Vue)
 ├── package.json               # Dependencies & scripts
```

---

# **2️⃣ Configuring Vue.js with Routing**
### **📌 `main.js` - Registering Vue App & Router**
📂 `src/main.js`
```javascript
import { createApp } from 'vue';
import App from './App.vue';
import router from './router';

const app = createApp(App);
app.use(router);
app.mount('#app');
```
✅ **This initializes the Vue application** and registers the **router**.

---

### **📌 `App.vue` - Root Component**
📂 `src/App.vue`
```vue
<template>
  <RouterView />
</template>
```
✅ **`<RouterView />` is a placeholder for dynamically loaded pages.**  
- It loads different pages based on the **URL path**.

---

### **📌 `index.html` - The Main Entry Point**
📂 `public/index.html`
```html
<!doctype html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <link rel="icon" type="image/svg+xml" href="/vite.svg" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Vite + Vue</title>
</head>
<body>
  <div id="app"></div>
  <script type="module" src="/src/main.js"></script>
</body>
</html>
```
✅ **Vue renders the app inside `<div id="app"></div>`.**

---

# **3️⃣ Setting Up Vue Router**
### **📌 `router/index.js` - Defining Routes**
📂 `src/router/index.js`
```javascript
import { createRouter, createWebHistory } from 'vue-router';
import Home from '../views/HomeView.vue';
import Login from '../views/LoginView.vue';
import Signup from '../views/SignupView.vue';

const router = createRouter({
    history: createWebHistory(import.meta.env.BASE_URL),
    routes: [
        { path: '/', name: 'home', component: Home },
        { path: '/login', name: 'login', component: Login },
        { path: '/signup', name: 'signup', component: Signup }
    ]
});

export default router;
```
✅ **Key Features:**
- Uses **`createWebHistory()`** for clean URLs (`/login` instead of `#/login`).
- Defines **routes** (`/`, `/login`, `/signup`).
- Each route loads a **view component** dynamically.

---

# **4️⃣ Creating Authentication Pages**
## **🔥 Login Page**
📂 `src/views/LoginView.vue`
```vue
<template>
  <h2>Login</h2>
  <form @submit.prevent="loginUser">
    <div>
      <label for="email">Email</label>
      <input type="email" id="email" v-model="email" required />
    </div>
    <div>
      <label for="password">Password</label>
      <input type="password" id="password" v-model="password" required />
    </div>
    <button type="submit">Login</button>
  </form>

  <div>
    <RouterLink to="/">Home</RouterLink> | 
    <RouterLink to="/signup">Signup</RouterLink>
  </div>
</template>

<script>
export default {
  data() {
    return {
      email: "",
      password: "",
    };
  },
  methods: {
    async loginUser() {
      if (!this.email || !this.password) {
        alert("All fields are required!");
        return;
      }

      try {
        const response = await fetch("http://localhost:5000/login", {
          method: "POST",
          headers: { "Content-Type": "application/json" },
          body: JSON.stringify({ email: this.email, password: this.password })
        });

        const res = await response.json();
        if (res.token) {
          localStorage.setItem("token", res.token);
          alert("Login successful!");
          this.$router.push("/"); // Redirect to Home
        } else {
          alert("Invalid login credentials.");
        }
      } catch (error) {
        alert("Login failed! Please try again.");
      }
    }
  }
};
</script>
```
✅ **Key Features:**
- **Form submission** using `@submit.prevent`
- **Stores JWT token** in `localStorage`
- **Redirects user** after login (`this.$router.push("/")`)

---

## **🔥 Signup Page**
📂 `src/views/SignupView.vue`
```vue
<template>
  <h2>Signup</h2>
  <form @submit.prevent="signupUser">
    <div>
      <label for="username">Username</label>
      <input type="text" id="username" v-model="username" required />
    </div>
    <div>
      <label for="email">Email</label>
      <input type="email" id="email" v-model="email" required />
    </div>
    <div>
      <label for="password">Password</label>
      <input type="password" id="password" v-model="password" required />
    </div>
    <button type="submit">Sign Up</button>
  </form>

  <div>
    <RouterLink to="/">Home</RouterLink> | 
    <RouterLink to="/login">Login</RouterLink>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      username: "",
      email: "",
      password: "",
    };
  },
  methods: {
    async signupUser() {
      if (!this.username || !this.email || !this.password) {
        alert("All fields are required!");
        return;
      }

      try {
        const response = await axios.post("http://localhost:5000/signup", {
          username: this.username,
          email: this.email,
          password: this.password
        });

        alert(response.data.message || "Signup successful");
        this.$router.push("/login"); // Redirect to Login
      } catch (error) {
        alert(error.response?.data.message || "Signup failed!");
      }
    }
  }
};
</script>
```
✅ **Key Features:**
- **Uses `axios` for API requests**
- **Handles signup validation**
- **Redirects user to login page on success**

---

# **5️⃣ Adding Navigation**
📂 `src/components/Navbar.vue`
```vue
<template>
  <nav>
    <RouterLink to="/">Home</RouterLink> | 
    <RouterLink v-if="!isAuthenticated" to="/login">Login</RouterLink> | 
    <RouterLink v-if="!isAuthenticated" to="/signup">Signup</RouterLink> |
    <button v-if="isAuthenticated" @click="logout">Logout</button>
  </nav>
</template>

<script>
export default {
  computed: {
    isAuthenticated() {
      return !!localStorage.getItem("token");
    }
  },
  methods: {
    logout() {
      localStorage.removeItem("token");
      this.$router.push("/login");
    }
  }
};
</script>
```

✅ **This creates a dynamic navbar that:**
- Shows **Login/Signup** if the user **is not logged in**
- Shows **Logout** if the user **is logged in**
- Clears token & redirects on logout

---

<!-- # **🎯 Final Thoughts**
🎉 Now you have:
- **Complete Authentication System (Login, Signup)**
- **JWT Token Storage & Logout Handling**
- **Vue Router Navigation Between Pages**
- **User Authentication & Role Management Ready!** -->

🚀 Next: 


[Mastering Decorators & Database Communication in Flask API]({{ site.baseurl }}/posts/App-Dev-II-bootcamp-t3/)

[Vue.js Advanced API Integration with Role-Based Access]({{ site.baseurl }}/posts/App-Dev-II-bootcamp-t4/)