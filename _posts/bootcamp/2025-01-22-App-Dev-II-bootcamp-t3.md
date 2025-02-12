---
title: "Bootcamp Tutorial 3: Mastering Decorators & Database Communication in Flask API"  
date: "2025-02-11 00:00:00"
categories: [bootcamp]
tags: [bootcamp, vue, flask]
---



## **Introduction**
In this tutorial, we'll dive deeper into **decorators**, **advanced API communication**, and **database operations**. By the end of this guide, you’ll be able to:
- Implement **custom decorators** for role-based access control
- Enhance **database communication** with SQLAlchemy
- Perform CRUD operations on users and tasks with **Flask-RESTful**
- Securely assign tasks and manage user approvals

---

# **1. Understanding Python Decorators**
### **What is a Decorator?**
A **decorator** in Python is a function that modifies another function’s behavior without changing its structure. It allows us to reuse functionality, making our code cleaner and more efficient.

#### **Example of a Basic Decorator**
```python
def my_decorator(func):
    def wrapper():
        print("Something before the function runs")
        func()
        print("Something after the function runs")
    return wrapper

@my_decorator
def say_hello():
    print("Hello, World!")

say_hello()
```
**Output:**
```
Something before the function runs
Hello, World!
Something after the function runs
```

Now, let’s implement a decorator for **role-based access control** in our Flask API.

---

# **2. Implementing Role-Based Access Control (RBAC)**
## **Helper Function: Get Current User**
Before enforcing access control, we need a helper function to fetch the currently logged-in user.
```python
def get_current_user():
    username = get_jwt_identity()
    return User.query.filter_by(username=username).first()
```
This function retrieves the user from the **JWT token identity**.

## **Decorator: Restrict Access by Role**
We create a **`role_required`** decorator that restricts API access based on user roles.
```python
def role_required(required_roles):
    def decorator(func):
        def wrapper(*args, **kwargs):
            user = get_current_user()
            if user is None or user.role not in required_roles:
                return jsonify({"message": "Unauthorized access"}), 403
            return func(*args, **kwargs)
        return wrapper
    return decorator
```
### **How It Works**
- It checks if the current user has the required role.
- If unauthorized, it returns a **403 Forbidden** response.

---

# **3. Advanced Database Communication with API**
## **Managing User Approvals (Admin Only)**
In a **multi-role system**, we want **admins** to approve or reject users before they can access tasks.

### **Fetching Pending Approvals**
```python
class UserApprovalResource(Resource):
    @jwt_required()
    @role_required(["admin"])
    def get(self):
        users = User.query.filter_by(is_approved=False).all()
        return jsonify([user.serialize() for user in users])
```
🔹 **Only admins** can retrieve the list of users pending approval.

### **Approving Users**
```python
    @jwt_required()
    @role_required(["admin"])
    def put(self, user_id):
        user = User.query.get(user_id)
        if not user:
            return jsonify({"message": "User not found"})

        user.is_approved = True
        db.session.commit()
        return jsonify({"message": "User approved successfully"})
```
🔹 Admins can approve users by setting `is_approved = True`.

### **Rejecting Users**
```python
    @jwt_required()
    @role_required(["admin"])
    def delete(self, user_id):
        user = User.query.get(user_id)
        if not user:
            return jsonify({"message": "User not found"})

        db.session.delete(user)
        db.session.commit()
        return jsonify({"message": "User rejected and removed"})
```
🔹 Unapproved users can be **deleted** from the database.

---

# **4. Task Management APIs**
## **CRUD Operations on Tasks**
### **Fetch All Tasks or a Single Task**
```python
class TaskResource(Resource):
    @jwt_required()
    @role_required(["admin", "manager"])
    def get(self, task_id=None):
        if task_id:
            task = Task.query.get(task_id)
            return jsonify(task.serialize() if task else {"message": "Task not found"})
        tasks = Task.query.all()
        return jsonify([task.serialize() for task in tasks])
```
🔹 **Admins & Managers** can fetch tasks.

### **Create a New Task**
```python
    @jwt_required()
    @role_required(["manager", "admin"])
    def post(self):
        data = request.get_json()
        new_task = Task(
            title=data['title'],
            description=data.get('description'),
            deadline=datetime.strptime(data['deadline'], '%Y-%m-%d') if data.get('deadline') else None
        )
        db.session.add(new_task)
        db.session.commit()
        return jsonify({"message": "Task created successfully"})
```
🔹 **Managers & Admins** can create tasks.

### **Update Task Details**
```python
    @jwt_required()
    @role_required(["manager", "admin"])
    def put(self, task_id):
        task = Task.query.get(task_id)
        if not task:
            return jsonify({"message": "Task not found"})

        data = request.get_json()
        task.title = data.get('title', task.title)
        task.description = data.get('description', task.description)
        task.status = data.get('status', task.status)
        db.session.commit()
        return jsonify({"message": "Task updated successfully"})
```
🔹 **Managers & Admins** can edit tasks.

### **Delete a Task (Admin Only)**
```python
    @jwt_required()
    @role_required(["admin"])
    def delete(self, task_id):
        task = Task.query.get(task_id)
        if not task:
            return jsonify({"message": "Task not found"})

        db.session.delete(task)
        db.session.commit()
        return jsonify({"message": "Task deleted successfully"})
```
🔹 **Only Admins** can delete tasks.

---

# **5. Assigning Tasks to Users**
```python
class AssignTaskResource(Resource):
    @jwt_required()
    @role_required(["manager"])
    def put(self, task_id):
        data = request.get_json()
        user_id = data.get("user_id")

        task = Task.query.get(task_id)
        user = User.query.get(user_id)

        if not task or not user:
            return jsonify({"message": "Task or User not found"})

        task.assigned_user_id = user.id
        db.session.commit()
        return jsonify({"message": "Task assigned successfully"})
```
🔹 **Managers** can assign tasks to employees.

---

# **6. Generating API Statistics**
```python
class StatsResource(Resource):
    @jwt_required()
    @role_required(["admin", "manager"])
    def get(self):
        total_users = User.query.count()
        total_tasks = Task.query.count()
        completed_tasks = Task.query.filter_by(status="completed").count()

        return jsonify({
            "total_users": total_users,
            "total_tasks": total_tasks,
            "completed_tasks": completed_tasks
        })
```
🔹 **Admins & Managers** can view system-wide stats.

---

<table>
  <thead>
    <tr>
      <th>Endpoint</th>
      <th>Method</th>
      <th>Roles Allowed</th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>/tasks</td>
      <td>GET</td>
      <td>Admin, Manager</td>
      <td>Fetch all tasks</td>
    </tr>
    <tr>
      <td>/task/&lt;task_id&gt;</td>
      <td>GET</td>
      <td>Admin, Manager</td>
      <td>Fetch a single task</td>
    </tr>
    <tr>
      <td>/task/&lt;task_id&gt;</td>
      <td>PUT</td>
      <td>Manager, Admin</td>
      <td>Update a task</td>
    </tr>
    <tr>
      <td>/task/&lt;task_id&gt;</td>
      <td>DELETE</td>
      <td>Admin</td>
      <td>Delete a task</td>
    </tr>
    <tr>
      <td>/users/pending</td>
      <td>GET</td>
      <td>Admin</td>
      <td>Fetch unapproved users</td>
    </tr>
    <tr>
      <td>/users/&lt;user_id&gt;/approve</td>
      <td>PUT</td>
      <td>Admin</td>
      <td>Approve a user</td>
    </tr>
    <tr>
      <td>/users/&lt;user_id&gt;/reject</td>
      <td>DELETE</td>
      <td>Admin</td>
      <td>Reject and remove a user</td>
    </tr>
    <tr>
      <td>/task/&lt;task_id&gt;/assign</td>
      <td>PUT</td>
      <td>Manager</td>
      <td>Assign a task to a user</td>
    </tr>
    <tr>
      <td>/stats</td>
      <td>GET</td>
      <td>Admin, Manager</td>
      <td>View statistics</td>
    </tr>
  </tbody>
</table>

---

### Final code
```python
from datetime import datetime, timedelta
from flask import Flask, request, jsonify, make_response
from flask_sqlalchemy import SQLAlchemy
from flask_jwt_extended import JWTManager, create_access_token, jwt_required, get_jwt_identity
from werkzeug.security import generate_password_hash, check_password_hash
from flask_restful import Api, Resource


# ================================================= app configuration =========================================================

app = Flask(__name__)
app.config["SQLALCHEMY_DATABASE_URI"] = "sqlite:///project.db"
app.config["JWT_SECRET_KEY"] = "aStrongSecretKey"
app.config["JWT_ACCESS_TOKEN_EXPIRES"] = timedelta(hours=24)

db = SQLAlchemy(app)
jwt = JWTManager(app)
api = Api(app)

# ================================================= XXXXXXXXXXXXXXXXXX =========================================================


# ================================================= models =========================================================

# User Model
class User(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    username = db.Column(db.String(80), unique=True, nullable=False)
    email = db.Column(db.String(120), unique=True, nullable=False)
    password_hash = db.Column(db.String(128), nullable=False)
    role = db.Column(db.String(20), default="employee")  # employee, manager, admin
    is_approved = db.Column(db.Boolean, default=False)  # Admin approval required
    created_at = db.Column(db.DateTime, default=datetime.utcnow)

    def set_password(self, password):
        self.password_hash = generate_password_hash(password)

    def check_password(self, password):
        return check_password_hash(self.password_hash, password)

# Task Model
class Task(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    title = db.Column(db.String(255), nullable=False)
    description = db.Column(db.Text, nullable=True)
    status = db.Column(db.String(20), default="pending")  # pending, in_progress, completed
    deadline = db.Column(db.DateTime, nullable=True)
    created_at = db.Column(db.DateTime, default=datetime.utcnow)
    assigned_user_id = db.Column(db.Integer, db.ForeignKey('user.id'), nullable=True)

with app.app_context():
    db.create_all()


# ================================================= XXXXXXXXXXXXXXXXXX =========================================================


# Helper Function to Check Role
def get_current_user():
    username = get_jwt_identity()
    return User.query.filter_by(username=username).first()

# decorator Function to Check Role
def role_required(required_roles):
    def decorator(func):
        def wrapper(*args, **kwargs):
            user = get_current_user()
            if user is None or user.role not in required_roles:
                return jsonify({"message": "Unauthorized access"}), 403
            return func(*args, **kwargs)
        return wrapper
    return decorator


    
# ================================================= Authentication =========================================================
# User Signup
class SignupResource(Resource):
    def post(self):
        data = request.get_json()
        username = data['username']
        email = data['email']
        password = data['password']
        
        if User.query.filter_by(username=username).first() or User.query.filter_by(email=email).first():
            return jsonify({'message': 'Username or Email already exists'})

        hashed_password = generate_password_hash(password)
        new_user = User(username=username, email=email, password_hash=hashed_password)
        db.session.add(new_user)
        db.session.commit()

        return jsonify({'message': 'User registered successfully'})


# User Login
class LoginResource(Resource):
    def post(self):
        data = request.get_json()
        email = data['email']
        password = data['password']
        
        user = User.query.filter_by(email=email).first()

        if user and check_password_hash(user.password_hash, password):
            access_token = create_access_token(identity=user.username)
            return jsonify({"msg": "successfully logged in", "token": access_token})
        
        return jsonify({"msg": "email or password incorrect."})

# ================================================= XXXXXXXXXXXXXXXXXX =========================================================



# ================================================= User Verification =========================================================

# User Approval (Admin only)
class UserApprovalResource(Resource):
    @jwt_required()
    @role_required(["admin"])
    def get(self):
        users = User.query.filter_by(is_approved=False).all()
        return jsonify([user.serialize() for user in users])

    @jwt_required()
    @role_required(["admin"])
    def put(self, user_id):
        user = User.query.get(user_id)
        if not user:
            return jsonify({"message": "User not found"})
        
        user.is_approved = True
        db.session.commit()
        return jsonify({"message": "User approved successfully"})

    @jwt_required()
    @role_required(["admin"])
    def delete(self, user_id):
        user = User.query.get(user_id)
        if not user:
            return jsonify({"message": "User not found"})
        
        db.session.delete(user)
        db.session.commit()
        return jsonify({"message": "User rejected and removed"})

# ================================================= XXXXXXXXXXXXXXXXXX =========================================================


# ================================================= Task Management =========================================================


# Fetch all tasks, create a new task, update, delete tasks
class TaskResource(Resource):
    @jwt_required()
    @role_required(["admin", "manager"])
    def get(self, task_id=None):
        if task_id:
            task = Task.query.get(task_id)
            return jsonify(task.serialize() if task else {"message": "Task not found"})
        tasks = Task.query.all()
        return jsonify([task.serialize() for task in tasks])

    @jwt_required()
    @role_required(["manager", "admin"])
    def post(self):
        data = request.get_json()
        new_task = Task(
            title=data['title'],
            description=data.get('description'),
            deadline=datetime.strptime(data['deadline'], '%Y-%m-%d') if data.get('deadline') else None
        )
        db.session.add(new_task)
        db.session.commit()
        return jsonify({"message": "Task created successfully"})

    @jwt_required()
    @role_required(["manager", "admin"])
    def put(self, task_id):
        task = Task.query.get(task_id)
        if not task:
            return jsonify({"message": "Task not found"})
        
        data = request.get_json()
        task.title = data.get('title', task.title)
        task.description = data.get('description', task.description)
        task.status = data.get('status', task.status)
        db.session.commit()
        return jsonify({"message": "Task updated successfully"})

    @jwt_required()
    @role_required(["admin"])
    def delete(self, task_id):
        task = Task.query.get(task_id)
        if not task:
            return jsonify({"message": "Task not found"})
        
        db.session.delete(task)
        db.session.commit()
        return jsonify({"message": "Task deleted successfully"})


# ================================================= XXXXXXXXXXXXXXXXXX =========================================================

# ================================================= XXXXXXXXXXXXXXXXXX =========================================================



# Assign Task to User
class AssignTaskResource(Resource):
    @jwt_required()
    @role_required(["manager"])
    def put(self, task_id):
        data = request.get_json()
        user_id = data.get("user_id")
        
        task = Task.query.get(task_id)
        user = User.query.get(user_id)

        if not task or not user:
            return jsonify({"message": "Task or User not found"})

        task.assigned_user_id = user.id
        db.session.commit()
        return jsonify({"message": "Task assigned successfully"})






# ================================================= XXXXXXXXXXXXXXXXXX =========================================================
# Stats API
class StatsResource(Resource):
    @jwt_required()
    @role_required(["admin", "manager"])
    def get(self):
        total_users = User.query.count()
        total_tasks = Task.query.count()
        completed_tasks = Task.query.filter_by(status="completed").count()
        
        return jsonify({
            "total_users": total_users,
            "total_tasks": total_tasks,
            "completed_tasks": completed_tasks
        })



# ================================================= XXXXXXXXXXXXXXXXXX =========================================================
# Register API Endpoints
api.add_resource(SignupResource, '/register')
api.add_resource(LoginResource, '/login')
api.add_resource(TaskResource, '/tasks', '/task/<int:task_id>')
api.add_resource(UserApprovalResource, '/users/pending', '/users/<int:user_id>/approve', '/users/<int:user_id>/reject')
api.add_resource(StatsResource, '/stats')
api.add_resource(AssignTaskResource, '/task/<int:task_id>/assign')

if __name__ == '__main__':
    app.run(debug=True, port=5000, host="127.0.0.1")


```






🚀 Next: 

[Vue.js Advanced API Integration with Role-Based Access]({{ site.baseurl }}/posts/App-Dev-II-bootcamp-t4/)