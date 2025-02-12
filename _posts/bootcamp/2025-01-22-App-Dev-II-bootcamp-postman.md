---
title: "Bonus Tutorial: API Testing with Postman"  
date: "2025-02-12 00:00:00"
categories: [bootcamp]
tags: [bootcamp, vue, flask]
---


# **Bonus Tutorial: API Testing with Postman – Why & How?** 🚀
---

## **📌 Why Should You Test APIs Before Frontend Integration?**
Before integrating APIs into the frontend, it’s **critical** to test them independently. Here's why:

✅ **Early Bug Detection** – Find API issues before they affect the UI.  
✅ **Faster Debugging** – Identify if an issue is in the backend or frontend.  
✅ **Smoother Integration** – Ensure API responses match frontend expectations.  
✅ **Security Testing** – Test authentication, roles, and protected routes.  
✅ **Automation Ready** – Save test cases for future automated testing.  

Instead of constantly **switching between frontend & backend**, Postman lets you **test APIs efficiently** before connecting them.

---

## **🔧 Getting Started with Postman**
### **1️⃣ Install Postman**
1. Download & install [Postman](https://www.postman.com/downloads/).
2. Open the Postman app and create a free account (optional but useful for saving requests).

---

## **🚀 Testing Flask API with Postman**
We will **test** the following API endpoints:
- User **Signup**
- User **Login**
- Protected API **(JWT Authentication)**
- CRUD Operations: **Tasks Management**
- Role-Based Access Control **(Admin & Manager Actions)**

---

## **1️⃣ Testing User Signup API**
📌 **Endpoint:** `POST http://127.0.0.1:5000/signup`

### **🔹 Steps in Postman:**
1. Open **Postman** and select `POST` request.
2. Enter the API URL:  
   ```
   http://127.0.0.1:5000/signup
   ```
3. Go to the **Body** tab → Select `raw` → Choose `JSON`.
4. Enter this JSON data:
   ```json
   {
     "username": "testuser",
     "email": "testuser@example.com",
     "password": "testpassword"
   }
   ```
5. Click **Send** and check the response.

✅ **Expected Response:**
```json
{
  "username": "testuser",
  "email": "testuser@example.com"
}
```

---

## **2️⃣ Testing User Login & Generating JWT Token**
📌 **Endpoint:** `POST http://127.0.0.1:5000/login`

### **🔹 Steps in Postman:**
1. Select `POST` request.
2. URL:  
   ```
   http://127.0.0.1:5000/login
   ```
3. **Body** → `raw` → Select `JSON` and enter:
   ```json
   {
     "email": "testuser@example.com",
     "password": "testpassword"
   }
   ```
4. Click **Send**.

✅ **Expected Response (with JWT Token):**
```json
{
  "msg": "successfully logged in",
  "token": "eyJhbGciOiJIUzI1..."
}
```
⚡ **Copy the JWT token** – You’ll need it for testing protected APIs.

---

## **3️⃣ Testing Protected API with JWT Authentication**
📌 **Endpoint:** `GET http://127.0.0.1:5000/hello`

### **🔹 Steps in Postman:**
1. Select `GET` request.
2. Enter URL:  
   ```
   http://127.0.0.1:5000/hello
   ```
3. Go to **Headers** tab.
4. Add a new header:  
   - **Key:** `Authorization`
   - **Value:** `Bearer <JWT-TOKEN>`  
     _(Replace `<JWT-TOKEN>` with the token copied earlier.)_
5. Click **Send**.

✅ **Expected Response:**
```json
{
  "msg": "hello world! from flask restful",
  "user_name": "testuser"
}
```
🚀 If you don't send a valid JWT, it returns **401 Unauthorized**.

---

## **4️⃣ Creating a Task (Admin/Manager Only)**
📌 **Endpoint:** `POST http://127.0.0.1:5000/tasks`

### **🔹 Steps in Postman:**
1. Select `POST` request.
2. URL:  
   ```
   http://127.0.0.1:5000/tasks
   ```
3. **Headers** → Add Authorization Header:
   ```
   Authorization: Bearer <JWT-TOKEN>
   ```
4. **Body** → `raw` → `JSON`:
   ```json
   {
     "title": "Fix Backend Bug",
     "description": "Resolve API timeout issue",
     "deadline": "2025-02-20"
   }
   ```
5. Click **Send**.

✅ **Expected Response:**
```json
{
  "message": "Task created successfully"
}
```
📌 If the user **isn’t** an admin/manager, it returns:
```json
{
  "message": "Unauthorized access"
}
```

---

## **5️⃣ Fetching All Tasks (Admin/Manager Only)**
📌 **Endpoint:** `GET http://127.0.0.1:5000/tasks`

### **🔹 Steps in Postman:**
1. Select `GET` request.
2. URL:  
   ```
   http://127.0.0.1:5000/tasks
   ```
3. **Headers** → Add Authorization Header:
   ```
   Authorization: Bearer <JWT-TOKEN>
   ```
4. Click **Send**.

✅ **Expected Response:**
```json
[
  {
    "id": 1,
    "title": "Fix Backend Bug",
    "description": "Resolve API timeout issue",
    "status": "pending",
    "deadline": "2025-02-20"
  }
]
```

---

## **6️⃣ Assigning a Task to a User (Manager Only)**
📌 **Endpoint:** `PUT http://127.0.0.1:5000/task/1/assign`

### **🔹 Steps in Postman:**
1. Select `PUT` request.
2. URL:  
   ```
   http://127.0.0.1:5000/task/1/assign
   ```
3. **Headers** → Add Authorization Header:
   ```
   Authorization: Bearer <JWT-TOKEN>
   ```
4. **Body** → `raw` → `JSON`:
   ```json
   {
     "user_id": 2
   }
   ```
5. Click **Send**.

✅ **Expected Response:**
```json
{
  "message": "Task assigned successfully"
}
```

---

## **7️⃣ Viewing API Stats (Admin/Manager Only)**
📌 **Endpoint:** `GET http://127.0.0.1:5000/stats`

### **🔹 Steps in Postman:**
1. Select `GET` request.
2. URL:  
   ```
   http://127.0.0.1:5000/stats
   ```
3. **Headers** → Add Authorization Header:
   ```
   Authorization: Bearer <JWT-TOKEN>
   ```
4. Click **Send**.

✅ **Expected Response:**
```json
{
  "total_users": 5,
  "total_tasks": 10,
  "completed_tasks": 3
}
```

---

<table>
  <thead>
    <tr>
      <th>Feature</th>
      <th>Postman API Testing</th>
      <th>Frontend Integration</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Faster Debugging</td>
      <td>✅ Directly test backend</td>
      <td>❌ Requires UI interactions</td>
    </tr>
    <tr>
      <td>Error Identification</td>
      <td>✅ Isolates API errors</td>
      <td>❌ Hard to debug API issues</td>
    </tr>
    <tr>
      <td>Role-Based Testing</td>
      <td>✅ Easily switch tokens</td>
      <td>❌ Requires logging in/out</td>
    </tr>
    <tr>
      <td>Security Testing</td>
      <td>✅ Test unauthorized access</td>
      <td>❌ Harder to simulate</td>
    </tr>
    <tr>
      <td>Automated Testing</td>
      <td>✅ Save requests & automate</td>
      <td>❌ Requires frontend setup</td>
    </tr>
    <tr>
      <td>No UI Dependency</td>
      <td>✅ Works standalone</td>
      <td>❌ Needs UI components</td>
    </tr>
  </tbody>
</table>

🔹 **Conclusion:** **Testing APIs with Postman** before frontend integration makes development smoother, debugging faster, and reduces errors before they impact users! 🚀🔥

---

## **📌 Next Steps**
- 🔥 Automate Postman tests using **Pre-Request Scripts**
- 📊 Learn API monitoring with **Postman Collections**
- 🛠️ Integrate APIs with **React/Angular Frontend**

Happy Testing! 🚀🎯