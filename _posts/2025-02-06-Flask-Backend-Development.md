---
title: "Flask Backend Development: API, Authentication, Caching & Background Jobs"
date: "2025-02-08 00:00:00"
categories: [Development ]
tags: [vue]
---

<!-- ### **Preface**   -->

Welcome to this **Flask Backend Development Tutorial**, where we will build a robust, scalable, and production-ready backend using Flask, SQLite for database management, JWT for authentication, Redis for caching, Celery for background tasks, and SMTP for email functionality.  

Backend development is a crucial part of any web application, ensuring smooth data processing, secure authentication, and efficient request handling. This tutorial is designed to provide a **hands-on approach**, walking you through each component step by step, from setting up the environment to deploying a fully functional backend.  

By the end of this tutorial, you will:  
✅ Understand the fundamentals of Flask and RESTful API development  
✅ Learn how to use **SQLite** for lightweight and efficient database management  
✅ Implement **JWT-based authentication** for secure session management  
✅ Integrate **Redis caching** to optimize API performance  
✅ Use **Celery** for handling asynchronous and batch jobs  
✅ Configure **SMTP** for sending emails programmatically  

<!-- This guide is **beginner-friendly** but assumes you have basic knowledge of Python and web development concepts. Each chapter will include **code examples, best practices, and real-world scenarios** to solidify your understanding.   -->
This guide is **beginner-friendly** but assumes you have basic knowledge of Python and web development concepts. Each chapter will include **code examples** to solidify your understanding.  

Let’s get started! 🚀  

---

### **Prerequisites**  

Before diving into this tutorial, ensure you have the following:  

#### **1. Technical Skills**  
✔️ Basic understanding of **Python** (functions, classes, modules)  
✔️ Familiarity with **Flask** and HTTP concepts (requests, responses, status codes)  
✔️ Knowledge of **SQL databases** (CRUD operations, SQL queries)  
✔️ Awareness of **authentication concepts** (tokens, sessions, hashing)  

#### **2. System Requirements**  
🔹 Python 3.8+ installed (Recommended: Python 3.10)  
🔹 `pip` (Python package manager) installed  
🔹 SQLite installed (comes with Python by default)  
🔹 Redis installed and running  
🔹 SMTP credentials for email testing (Gmail, SendGrid, etc.)  

<!--  #### **3. Required Python Libraries**  
Before starting, install the following packages:  
```bash
pip install flask flask-jwt-extended flask-sqlalchemy flask-migrate redis celery flask-mail
```

#### **4. Development Environment**  
✔️ VS Code, PyCharm, or any preferred IDE  
✔️ Postman or cURL for API testing  
✔️ Virtual Environment (`venv`) for dependency management  

With these prerequisites in place, you're all set to begin your **Flask Backend Development Journey**! 🚀 -->



## 📚 Table of Contents

### **1️⃣ Introduction to Flask Backend Development**
- [What is Flask?](#-what-is-flask)
- [Why Choose Flask for Backend Development?](#-why-choose-flask-for-backend-development)
- [Core Components of a Backend System](#-core-components-of-a-backend-system)
- [Overview of Technologies Used](#-overview-of-technologies-used)

### **2️⃣ Setting Up the Development Environment**
- [Installing Python and Flask](#installing-python-and-flask)
- [Setting Up a Virtual Environment](#setting-up-a-virtual-environment)
- [Project Structure Overview](#project-structure-overview)
- [Creating the First Flask App](#creating-the-first-flask-app)
- [Running the Flask Server](#running-the-flask-server)

### **3️⃣ Working with SQLite Database**
- [Introduction to SQLite](#introduction-to-sqlite)
- [Setting Up Flask-SQLAlchemy](#setting-up-flask-sqlalchemy)
- [Creating Models and Migrations](#creating-models-and-migrations)
- [Performing CRUD Operations](#performing-crud-operations)
- [Querying and Filtering Data](#querying-and-filtering-data)

### **4️⃣ Implementing Authentication with JWT**
- [What is JWT and Why Use It?](#what-is-jwt-and-why-use-it)
- [Setting Up Flask-JWT-Extended](#setting-up-flask-jwt-extended)
- [User Registration and Login](#user-registration-and-login)
- [Generating and Validating Tokens](#generating-and-validating-tokens)
- [Role-Based Access Control (RBAC)](#role-based-access-control-rbac)

### **5️⃣ Implementing Redis for Caching**
- [What is Redis and Why Use It?](#what-is-redis-and-why-use-it)
- [Installing and Configuring Redis](#installing-and-configuring-redis)
- [Using Flask-Redis for Caching](#using-flask-redis-for-caching)
- [Implementing Rate Limiting with Redis](#implementing-rate-limiting-with-redis)
- [Caching API Responses](#caching-api-responses)

### **6️⃣ Background Tasks with Celery**
- [What is Celery?](#what-is-celery)
- [Installing and Configuring Celery](#installing-and-configuring-celery)
- [Using Celery with Flask](#using-celery-with-flask)
- [Creating and Running Background Jobs](#creating-and-running-background-jobs)
- [Monitoring Celery Tasks](#monitoring-celery-tasks)

### **7️⃣ Sending Emails with SMTP**
- [Introduction to SMTP and Email Sending](#introduction-to-smtp-and-email-sending)
- [Setting Up Flask-Mail](#setting-up-flask-mail)
- [Configuring Email Credentials](#configuring-email-credentials)
- [Sending Emails in Flask](#sending-emails-in-flask)
- [Handling Email Templates](#handling-email-templates)

### **8️⃣ Building a RESTful API**
- [What is a REST API?](#what-is-a-rest-api)
- [Designing RESTful Endpoints](#designing-restful-endpoints)
- [Handling Requests and Responses](#handling-requests-and-responses)
- [Returning JSON Data](#returning-json-data)
- [API Versioning Best Practices](#api-versioning-best-practices)

### **9️⃣ Securing the Flask Application**
- [Handling CORS with Flask-CORS](#handling-cors-with-flask-cors)
- [Rate Limiting and Security Best Practices](#rate-limiting-and-security-best-practices)
- [Using Environment Variables for Security](#using-environment-variables-for-security)
- [Protecting Sensitive Data](#protecting-sensitive-data)

### **🔟 Testing and Debugging**
- [Writing Unit Tests for Flask](#writing-unit-tests-for-flask)
- [Testing API Endpoints with Postman](#testing-api-endpoints-with-postman)
- [Logging and Debugging Errors](#logging-and-debugging-errors)
- [Using Flask Debug Mode](#using-flask-debug-mode)

### **1️1 Deployment and Scaling**
- [Deploying Flask on DigitalOcean](#deploying-flask-on-digitalocean)
- [Using Docker for Flask Deployment](#using-docker-for-flask-deployment)
- [Setting Up Gunicorn for Production](#setting-up-gunicorn-for-production)
- [Using Nginx as a Reverse Proxy](#using-nginx-as-a-reverse-proxy)
- [Monitoring and Scaling the Application](#monitoring-and-scaling-the-application)

### **12 Final Project: Building a Full-Stack Application**
- [Project Overview](#project-overview)
- [Designing the Database Schema](#designing-the-database-schema)
- [Implementing Authentication and Authorization](#implementing-authentication-and-authorization)
- [Building RESTful API Endpoints](#building-restful-api-endpoints)
- [Caching Data and Using Celery Tasks](#caching-data-and-using-celery-tasks)
- [Integrating Email Notifications](#integrating-email-notifications)
- [Deploying the Final Application](#deploying-the-final-application)

---



## **1️⃣ Introduction to Flask Backend Development**

In modern web applications, the **backend** is responsible for handling requests, processing data, managing databases, and ensuring security. Flask, a lightweight yet powerful web framework in Python, provides developers with the flexibility and simplicity needed to build scalable backend systems.

This section will introduce you to Flask, its advantages, the core components of a backend system, and the technologies we will be using throughout this tutorial.

---

### **📌 What is Flask?**

Flask is a **micro web framework** for Python that enables developers to create web applications quickly and efficiently. Unlike Django, which follows a "batteries-included" approach, Flask is minimalistic and allows developers to integrate only the components they need.

**Key Features of Flask:**
✅ **Lightweight & Minimal** – Provides only the essential features, allowing flexibility.  
✅ **Modular & Extensible** – Easily integrates with other libraries and tools.  
✅ **RESTful API Support** – Ideal for building APIs and microservices.  
✅ **Jinja2 Templating Engine** – Helps render dynamic content in web applications.  
✅ **Built-in Development Server & Debugger** – Speeds up the development process.  

🔹 **Official Flask Documentation**: [https://flask.palletsprojects.com/](https://flask.palletsprojects.com/)

---

### **📌 Why Choose Flask for Backend Development?**

Flask is an excellent choice for backend development because of its flexibility and ease of use. Here’s why:

🔹 **Minimal Boilerplate Code:** Unlike Django, Flask requires minimal setup, making it ideal for small to medium-sized applications.  
🔹 **API-First Approach:** Perfect for creating RESTful APIs, which are widely used in modern applications.  
🔹 **Scalability:** Can be used for both simple applications and large-scale projects by integrating extensions like Flask-SQLAlchemy, Flask-JWT-Extended, etc.  
🔹 **Full Control:** Developers have complete control over how components are used and structured.  
🔹 **Active Community:** A large and active community means continuous improvements, extensions, and support.  

When to use Flask?  
✔ Small to medium-sized applications  
✔ REST APIs for frontend applications (Vue.js, React, Angular)  
✔ Microservices architecture  
✔ Prototyping new ideas quickly  

---

### **📌 Core Components of a Backend System**

A well-structured backend consists of several key components:

1️⃣ **Web Framework** - Handles HTTP requests and responses.  
   - We will use **Flask** for this.  

2️⃣ **Database Management** - Stores and manages application data.  
   - We will use **SQLite** for simplicity and lightweight storage.  

3️⃣ **Authentication & Authorization** - Ensures secure access control.  
   - We will use **JWT (JSON Web Token)** for user authentication.  

4️⃣ **Caching System** - Improves performance by storing frequently accessed data.  
   - We will integrate **Redis** for caching.  

5️⃣ **Background Jobs** - Handles time-consuming tasks asynchronously.  
   - We will use **Celery** for batch processing and scheduled tasks.  

6️⃣ **Email Service** - Sends automated emails for notifications.  
   - We will configure **SMTP** for email sending.  

7️⃣ **Deployment & Scaling** - Optimizes for production and performance.  
   - We will explore **Gunicorn, Docker, and Nginx** for deployment.  

Each of these components plays a crucial role in making the backend **secure, scalable, and efficient**.

---

### **📌 Overview of Technologies Used**

Here’s a quick rundown of the technologies and tools we will be using:

| **Technology**  | **Purpose**  |
|---------------|-------------|
| Flask | Web framework for handling API requests |
| SQLite | Lightweight database for storing data |
| Flask-JWT-Extended | Token-based authentication system |
| Redis | In-memory caching for performance optimization |
| Celery | Task queue for background job processing |
| Flask-Mail | Library for sending emails via SMTP |
| Gunicorn | WSGI server for deploying Flask applications |
| Nginx | Reverse proxy and load balancer for production |
| Docker | Containerization for deployment |

By mastering these tools, you'll be able to build a **scalable, secure, and production-ready** backend system.

---

---

## **2️⃣ Setting Up the Development Environment**

Before diving into Flask development, we need to set up our environment properly. This section covers installing Python and Flask, setting up a virtual environment, understanding the project structure, creating a basic Flask application, and running the Flask server.

---

### **📌 Installing Python and Flask** {#installing-python-and-flask}

### **Step 1: Install Python**
Flask is a Python-based framework, so ensure you have Python installed.

🔹 **Check if Python is installed:**  
Run the following command in your terminal or command prompt:
```bash
python --version
```
or
```bash
python3 --version
```
If Python is not installed, download and install it from [Python's official website](https://www.python.org/downloads/).

🔹 **Ensure `pip` is installed:**  
Check if `pip` (Python package manager) is available:
```bash
pip --version
```
If missing, install it using:
```bash
python -m ensurepip --default-pip
```

### **Step 2: Install Flask**
Once Python is installed, install Flask using `pip`:
```bash
pip install flask
```
Verify the installation:
```bash
python -m flask --version
```

---

### **📌 Setting Up a Virtual Environment** {#setting-up-a-virtual-environment}

A virtual environment isolates project dependencies, preventing conflicts between different Python projects.

🔹 **Create a Virtual Environment:**  
Navigate to your project directory and run:
```bash
python -m venv venv
```
This creates a folder `venv/` containing the virtual environment.

🔹 **Activate the Virtual Environment:**  
- **Windows:**  
  ```bash
  venv\Scripts\activate
  ```
- **Mac/Linux:**  
  ```bash
  source venv/bin/activate
  ```

Once activated, you should see `(venv)` in the terminal prompt, indicating that the virtual environment is active.

🔹 **Install Dependencies in the Virtual Environment:**  
```bash
pip install flask flask-sqlalchemy flask-jwt-extended redis celery flask-mail
```

---

### **📌 Project Structure Overview** {#project-structure-overview}

A well-structured Flask project makes development easier. Below is a suggested structure:

```
flask_app/
│── app/
│   ├── __init__.py
│   ├── models.py
│   ├── routes.py
│   ├── services.py
│   ├── tasks.py
│── migrations/
│── static/
│── templates/
│── tests/
│── .env
│── config.py
│── main.py
│── requirements.txt
│── README.md
```

### **Folder & File Explanation:**
- **`app/`** → Contains the main application code.
  - **`__init__.py`** → Initializes Flask and extensions.
  - **`models.py`** → Defines database models.
  - **`routes.py`** → Defines API endpoints.
  - **`services.py`** → Contains business logic.
  - **`tasks.py`** → Defines background tasks (Celery).
- **`migrations/`** → Contains database migration files (for Flask-Migrate).
- **`static/`** → Stores static files (CSS, JS, images).
- **`templates/`** → Stores HTML templates (if using Jinja2).
- **`tests/`** → Stores test scripts for API testing.
- **`.env`** → Contains environment variables.
- **`config.py`** → Stores Flask configuration settings.
- **`main.py`** → Entry point for running the Flask app.
- **`requirements.txt`** → Lists dependencies.
- **`README.md`** → Documentation file.

---

### **📌 Creating the First Flask App** {#creating-the-first-flask-app}

Now, let's create a basic **Flask app**.

🔹 **Step 1: Create `main.py`**  
Inside your project folder (`flask_app/`), create `main.py` and add the following code:

```python
from flask import Flask

app = Flask(__name__)

@app.route("/")
def home():
    return "Hello, Flask!"

if __name__ == "__main__":
    app.run(debug=True)
```

🔹 **Step 2: Run the Flask App**  
To run the Flask server, use:
```bash
python main.py
```
This starts a development server at **`http://127.0.0.1:5000/`**.

---

### **📌 Running the Flask Server** {#running-the-flask-server}

To start the Flask server:

```bash
python main.py
```

After running the command, you should see output like this:

```
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
 * Debug mode: on
```

🔹 **Access your Flask app in a browser:**  
Open **`http://127.0.0.1:5000/`**, and you should see:
```
Hello, Flask!
```

🔹 **Stopping the Server:**  
Press `CTRL+C` in the terminal to stop the server.

---

<!-- ### **🎯 Next: [Working with SQLite Database →](#introduction-to-sqlite)** -->
---

## **3️⃣ Working with SQLite Database**

SQLite is a lightweight, file-based relational database that is ideal for small to medium-sized applications. It requires no separate server and is easy to set up with Flask using **Flask-SQLAlchemy**.

In this section, we'll cover the basics of SQLite, setting up SQLAlchemy in Flask, creating models, performing CRUD operations, and querying data efficiently.

---

### **📌 Introduction to SQLite** {#introduction-to-sqlite}

SQLite is a self-contained, serverless database engine stored as a **single file** on disk. It is widely used for local storage in applications due to its simplicity and lightweight nature.

**Why use SQLite for Flask?**  
✅ **No setup required** – Comes pre-installed with Python  
✅ **Lightweight & Fast** – Suitable for development and small applications  
✅ **Single file storage** – Stores data in a `.sqlite3` file  
✅ **SQL Support** – Uses standard SQL syntax  

<table>
  <thead>
    <tr>
      <th>Command</th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><code>SELECT</code></td>
      <td>Retrieve data from the database</td>
    </tr>
    <tr>
      <td><code>INSERT</code></td>
      <td>Add new records to a table</td>
    </tr>
    <tr>
      <td><code>UPDATE</code></td>
      <td>Modify existing records</td>
    </tr>
    <tr>
      <td><code>DELETE</code></td>
      <td>Remove records from a table</td>
    </tr>
    <tr>
      <td><code>CREATE TABLE</code></td>
      <td>Define a new table</td>
    </tr>
    <tr>
      <td><code>DROP TABLE</code></td>
      <td>Delete a table</td>
    </tr>
  </tbody>
</table>

By integrating **Flask-SQLAlchemy**, we can manage our SQLite database using **Python ORM (Object-Relational Mapping)** instead of raw SQL queries.

---

### **📌 Setting Up Flask-SQLAlchemy** {#setting-up-flask-sqlalchemy}

🔹 **Step 1: Install Flask-SQLAlchemy**  
If you haven’t installed it yet, run:
```bash
pip install flask-sqlalchemy
```

🔹 **Step 2: Configure Flask to Use SQLite**  
Modify your `main.py` or `app/__init__.py`:

```python
from flask import Flask
from flask_sqlalchemy import SQLAlchemy
import os

app = Flask(__name__)

# Configure the SQLite database path
BASE_DIR = os.path.abspath(os.path.dirname(__file__))
app.config["SQLALCHEMY_DATABASE_URI"] = "sqlite:///" + os.path.join(BASE_DIR, "database.sqlite3")
app.config["SQLALCHEMY_TRACK_MODIFICATIONS"] = False

# Initialize SQLAlchemy
db = SQLAlchemy(app)
```

🔹 **Step 3: Create the Database File**  
Run:
```bash
python
```
```python
from main import db
db.create_all()
exit()
```
This will generate a `database.sqlite3` file in your project directory.

---

### **📌 Creating Models and Migrations** {#creating-models-and-migrations}

In Flask-SQLAlchemy, **models** represent database tables. Each model class extends `db.Model` and defines fields as columns.

🔹 **Step 1: Define a Model (Table Structure)**  
Create a file `models.py`:

```python
from main import db

class User(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    username = db.Column(db.String(80), unique=True, nullable=False)
    email = db.Column(db.String(120), unique=True, nullable=False)
    password = db.Column(db.String(128), nullable=False)

    def __repr__(self):
        return f"<User {self.username}>"
```

🔹 **Step 2: Apply Migrations**  
Install Flask-Migrate:
```bash
pip install flask-migrate
```

Modify `main.py` to initialize Flask-Migrate:
```python
from flask_migrate import Migrate

migrate = Migrate(app, db)
```

Run these commands to apply migrations:
```bash
flask db init
flask db migrate -m "Initial migration"
flask db upgrade
```
This creates a `migrations/` folder and updates the database schema.

---

### **📌 Performing CRUD Operations** {#performing-crud-operations}

Let's implement **Create, Read, Update, and Delete (CRUD)** operations using Flask routes.

### **1️⃣ Creating a New User (INSERT)**
```python
from flask import request, jsonify
from main import app, db
from models import User

@app.route("/user", methods=["POST"])
def create_user():
    data = request.json
    new_user = User(username=data["username"], email=data["email"], password=data["password"])
    db.session.add(new_user)
    db.session.commit()
    return jsonify({"message": "User created successfully"}), 201
```

### **2️⃣ Retrieving Users (SELECT)**
```python
@app.route("/users", methods=["GET"])
def get_users():
    users = User.query.all()
    return jsonify([{"id": u.id, "username": u.username, "email": u.email} for u in users])
```

### **3️⃣ Updating a User (UPDATE)**
```python
@app.route("/user/<int:user_id>", methods=["PUT"])
def update_user(user_id):
    data = request.json
    user = User.query.get(user_id)
    if user:
        user.username = data["username"]
        user.email = data["email"]
        db.session.commit()
        return jsonify({"message": "User updated successfully"})
    return jsonify({"error": "User not found"}), 404
```

### **4️⃣ Deleting a User (DELETE)**
```python
@app.route("/user/<int:user_id>", methods=["DELETE"])
def delete_user(user_id):
    user = User.query.get(user_id)
    if user:
        db.session.delete(user)
        db.session.commit()
        return jsonify({"message": "User deleted successfully"})
    return jsonify({"error": "User not found"}), 404
```

---

### **📌 Querying and Filtering Data** {#querying-and-filtering-data}

Flask-SQLAlchemy provides easy methods to query data.

### **1️⃣ Fetch a User by ID**
```python
@app.route("/user/<int:user_id>", methods=["GET"])
def get_user(user_id):
    user = User.query.get(user_id)
    if user:
        return jsonify({"id": user.id, "username": user.username, "email": user.email})
    return jsonify({"error": "User not found"}), 404
```

### **2️⃣ Fetch Users by a Specific Field**
```python
@app.route("/user/email/<string:email>", methods=["GET"])
def get_user_by_email(email):
    user = User.query.filter_by(email=email).first()
    if user:
        return jsonify({"id": user.id, "username": user.username, "email": user.email})
    return jsonify({"error": "User not found"}), 404
```

### **3️⃣ Fetch Users with Sorting and Filtering**
```python
@app.route("/users/sorted", methods=["GET"])
def get_sorted_users():
    users = User.query.order_by(User.username.asc()).all()
    return jsonify([{"id": u.id, "username": u.username, "email": u.email} for u in users])
```

---

### **📌 Summary**
✔ **SQLite** is a lightweight database ideal for small applications.  
✔ **Flask-SQLAlchemy** provides an ORM to interact with SQLite using Python.  
✔ **CRUD operations** allow us to create, retrieve, update, and delete user records.  
✔ **Querying and filtering** helps us fetch and manage data efficiently.  

---

<!-- ### **🎯 Next: [Implementing Authentication with JWT →](#what-is-jwt-and-why-use-it)** -->

---

## **4️⃣ Implementing Authentication with JWT**

Authentication is a crucial part of any web application. **JWT (JSON Web Token)** is a widely used method for securing APIs by issuing signed tokens to users upon authentication. These tokens can be used for **stateless authentication**, meaning the server does not need to store session data.

In this section, we will explore JWT, set up authentication using **Flask-JWT-Extended**, implement **user registration & login**, generate and validate JWT tokens, and implement **role-based access control (RBAC).**

---

### **📌 What is JWT and Why Use It?** {#what-is-jwt-and-why-use-it}

JWT (**JSON Web Token**) is an encoded string that securely transmits authentication information between a client and a server.

### **🔹 How JWT Works**
1. The user logs in with a **username & password**.
2. The server **validates** the credentials and **issues a JWT token**.
3. The client **stores the token** (usually in local storage or cookies).
4. For subsequent API requests, the token is **included in the Authorization header**.
5. The server verifies the token and **grants access** if valid.

### **🔹 JWT Token Structure**
A JWT consists of three parts:  
```
Header.Payload.Signature
```
Example JWT:
```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

### **🔹 Why Use JWT?**
<table>
<tr><td> ✅ Stateless Authentication </td><td> No need to store session data on the server. </td></tr>
<tr><td> 🔐 Secure </td><td> Uses **signatures** to prevent tampering. </td></tr>
<tr><td> ⚡ Fast </td><td> Reduces server-side lookup times. </td></tr>
<tr><td> 🔄 Scalable </td><td> Ideal for microservices and distributed systems. </td></tr>
<tr><td> 🌍 Cross-Platform </td><td> Works across web, mobile, and APIs. </td></tr>
</table>

---

### **📌 Setting Up Flask-JWT-Extended** {#setting-up-flask-jwt-extended}

To implement JWT authentication in Flask, we will use **Flask-JWT-Extended**.

🔹 **Step 1: Install Flask-JWT-Extended**
```bash
pip install flask-jwt-extended
```

🔹 **Step 2: Configure JWT in `main.py`**
```python
from flask import Flask
from flask_jwt_extended import JWTManager

app = Flask(__name__)

# Secret key for signing JWTs (store securely in .env)
app.config["JWT_SECRET_KEY"] = "your_secret_key_here"

# Initialize JWT
jwt = JWTManager(app)
```

---

### **📌 User Registration and Login** {#user-registration-and-login}

We will create **register** and **login** routes that store user credentials securely and generate JWT tokens.

🔹 **Step 1: Modify `models.py` to Add User Model**
```python
from main import db

class User(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    username = db.Column(db.String(80), unique=True, nullable=False)
    email = db.Column(db.String(120), unique=True, nullable=False)
    password = db.Column(db.String(128), nullable=False)  # Store hashed passwords
```

🔹 **Step 2: User Registration Route**
```python
from flask import request, jsonify
from werkzeug.security import generate_password_hash
from models import User, db

@app.route("/register", methods=["POST"])
def register():
    data = request.json
    hashed_password = generate_password_hash(data["password"], method="pbkdf2:sha256")
    new_user = User(username=data["username"], email=data["email"], password=hashed_password)
    db.session.add(new_user)
    db.session.commit()
    return jsonify({"message": "User registered successfully"}), 201
```

🔹 **Step 3: User Login Route**
```python
from flask_jwt_extended import create_access_token
from werkzeug.security import check_password_hash

@app.route("/login", methods=["POST"])
def login():
    data = request.json
    user = User.query.filter_by(email=data["email"]).first()

    if not user or not check_password_hash(user.password, data["password"]):
        return jsonify({"error": "Invalid credentials"}), 401

    access_token = create_access_token(identity={"id": user.id, "username": user.username})
    return jsonify({"access_token": access_token}), 200
```

---

### **📌 Generating and Validating Tokens** {#generating-and-validating-tokens}

Once a user logs in, we generate a **JWT token** that must be included in API requests.

🔹 **Protecting Routes Using JWT**
```python
from flask_jwt_extended import jwt_required, get_jwt_identity

@app.route("/protected", methods=["GET"])
@jwt_required()
def protected():
    current_user = get_jwt_identity()
    return jsonify({"message": f"Hello, {current_user['username']}!"})
```

🔹 **Sending Token in API Request**
The frontend must include the JWT token in the `Authorization` header:
```
Authorization: Bearer <your_jwt_token>
```

🔹 **Token Expiration Handling**
Set token expiry time in `main.py`:
```python
from datetime import timedelta
app.config["JWT_ACCESS_TOKEN_EXPIRES"] = timedelta(hours=1)
```

---

### **📌 Role-Based Access Control (RBAC)** {#role-based-access-control-rbac}

RBAC allows defining user roles (**admin, user, editor**) to restrict access to certain API endpoints.

🔹 **Step 1: Modify `models.py` to Add Role Field**
```python
class User(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    username = db.Column(db.String(80), unique=True, nullable=False)
    email = db.Column(db.String(120), unique=True, nullable=False)
    password = db.Column(db.String(128), nullable=False)
    role = db.Column(db.String(20), default="user")  # Default role: user
```

🔹 **Step 2: Protect Routes Based on Role**
```python
def admin_required(fn):
    @jwt_required()
    def wrapper(*args, **kwargs):
        current_user = get_jwt_identity()
        user = User.query.get(current_user["id"])
        if user.role != "admin":
            return jsonify({"error": "Admins only!"}), 403
        return fn(*args, **kwargs)
    return wrapper
```

🔹 **Step 3: Use RBAC in API Routes**
```python
@app.route("/admin", methods=["GET"])
@admin_required
def admin_dashboard():
    return jsonify({"message": "Welcome, Admin!"})
```

---

### **📌 Summary**
✔ **JWT** enables secure and stateless authentication.  
✔ **Flask-JWT-Extended** simplifies JWT token creation and validation.  
✔ **User registration & login** allows authentication using hashed passwords.  
✔ **JWT-protected routes** ensure access only to authenticated users.  
✔ **Role-Based Access Control (RBAC)** restricts actions based on user roles.  

---

<!-- ### **🎯 Next: [Implementing Redis for Caching →](#what-is-redis-and-why-use-it)** -->
---

## **5️⃣ Implementing Redis for Caching**

Caching is an essential technique for optimizing application performance by storing frequently accessed data in memory instead of making repeated database queries. **Redis** is an in-memory data store that provides lightning-fast access to data and is widely used for caching, session storage, and real-time analytics.

In this section, we will explore **Redis**, set up Flask-Redis for caching, implement **rate limiting**, and learn how to cache API responses.

---

### **📌 What is Redis and Why Use It?** {#what-is-redis-and-why-use-it}

**Redis (Remote Dictionary Server)** is a **high-performance, in-memory key-value store** that can be used for caching, message brokering, and real-time analytics.

### **🔹 Why Use Redis for Caching?**
<table>
<tr><td> ⚡ Ultra-Fast </td><td> Redis stores data in RAM, making it significantly faster than databases. </td></tr>
<tr><td> 🔁 Reduces DB Load </td><td> Cached data prevents repeated queries, improving performance. </td></tr>
<tr><td> 🔄 Supports Expiry </td><td> Cached items can expire automatically, preventing stale data. </td></tr>
<tr><td> 🚀 Scalable </td><td> Works well for large-scale applications and distributed systems. </td></tr>
<tr><td> 🔑 Key-Value Store </td><td> Data is stored as key-value pairs for fast lookups. </td></tr>
</table>

---

### **📌 Installing and Configuring Redis** {#installing-and-configuring-redis}

### **🔹 Step 1: Install Redis**
On **Linux/macOS**, install Redis via:
```bash
sudo apt install redis -y  # Ubuntu/Debian
brew install redis  # macOS
```

On **Windows**, use **WSL (Windows Subsystem for Linux)** or install Redis from [Memurai](https://memurai.com/) or [Docker](https://hub.docker.com/_/redis).

Start Redis:
```bash
redis-server
```

Verify Redis is running:
```bash
redis-cli ping
# Output: PONG
```

### **🔹 Step 2: Install Flask-Redis**
```bash
pip install redis flask-redis
```

### **🔹 Step 3: Configure Redis in `main.py`**
```python
from flask import Flask
from redis import Redis

app = Flask(__name__)

# Redis configuration
app.config["REDIS_URL"] = "redis://localhost:6379/0"
redis_client = Redis.from_url(app.config["REDIS_URL"], decode_responses=True)
```

---

### **📌 Using Flask-Redis for Caching** {#using-flask-redis-for-caching}

We can store and retrieve data from Redis to **avoid frequent database queries**.

🔹 **Step 1: Store and Retrieve Data**
```python
@app.route("/set_cache/<key>/<value>")
def set_cache(key, value):
    redis_client.set(key, value, ex=60)  # Set key with 60 seconds expiry
    return f"Cached {key}: {value}"

@app.route("/get_cache/<key>")
def get_cache(key):
    value = redis_client.get(key)
    if value:
        return f"Cached Value: {value}"
    return "Cache miss!"
```

🔹 **Step 2: Flush Redis Cache**
```python
@app.route("/clear_cache")
def clear_cache():
    redis_client.flushall()
    return "Cache cleared!"
```

---

### **📌 Implementing Rate Limiting with Redis** {#implementing-rate-limiting-with-redis}

Rate limiting prevents API abuse by **restricting the number of requests per user/IP**.

🔹 **Step 1: Create a Rate Limiting Middleware**
```python
import time
from flask import request, jsonify

def rate_limiter(limit: int, window: int):
    def decorator(fn):
        def wrapper(*args, **kwargs):
            user_ip = request.remote_addr
            key = f"rate_limit:{user_ip}"
            current = redis_client.get(key)

            if current and int(current) >= limit:
                return jsonify({"error": "Too many requests, slow down!"}), 429
            
            if not current:
                redis_client.setex(key, window, 1)
            else:
                redis_client.incr(key)
            
            return fn(*args, **kwargs)
        return wrapper
    return decorator
```

🔹 **Step 2: Apply Rate Limiting to Routes**
```python
@app.route("/limited_api")
@rate_limiter(limit=5, window=60)  # Max 5 requests per minute
def limited_api():
    return jsonify({"message": "This API is rate-limited!"})
```

---

### **📌 Caching API Responses** {#caching-api-responses}

Caching API responses can significantly **reduce database queries and response times**.

🔹 **Step 1: Create a Caching Wrapper**
```python
import json
from functools import wraps

def cache_response(timeout: int):
    def decorator(fn):
        @wraps(fn)
        def wrapper(*args, **kwargs):
            cache_key = f"cache:{request.path}"
            cached_data = redis_client.get(cache_key)

            if cached_data:
                return jsonify(json.loads(cached_data))

            response = fn(*args, **kwargs)
            redis_client.setex(cache_key, timeout, json.dumps(response.json))
            return response
        return wrapper
    return decorator
```

🔹 **Step 2: Apply Caching to an API Endpoint**
```python
@app.route("/expensive_query")
@cache_response(timeout=120)  # Cache response for 2 minutes
def expensive_query():
    data = {"message": "This is a cached response!", "time": time.time()}
    return jsonify(data)
```

🔹 **Step 3: Clear Cache for an API Endpoint**
```python
@app.route("/clear_api_cache")
def clear_api_cache():
    redis_client.flushdb()
    return "API Cache Cleared!"
```

---

### **📌 Summary**
✔ **Redis** is a fast, in-memory key-value store used for caching.  
✔ **Flask-Redis** enables easy integration with Redis in Flask apps.  
✔ **Rate limiting** prevents excessive API requests using Redis keys.  
✔ **Caching API responses** reduces unnecessary database queries and improves performance.  

---

<!-- ### **🎯 Next: [Background Tasks with Celery →](#what-is-celery)** -->
---
<!-- 
## **8️⃣ Building a RESTful API**

A **RESTful API (Representational State Transfer API)** is a widely used standard for designing web services that interact with clients like web applications, mobile apps, or IoT devices. It follows **stateless** communication, where each request from a client must contain all necessary information.

In this section, we will explore what a REST API is, design RESTful endpoints, handle requests and responses, return JSON data, and follow API versioning best practices.

---

### **📌 What is a REST API?** {#what-is-a-rest-api}

A **REST API** allows clients to communicate with a backend service using **HTTP methods**.

### **🔹 Key Principles of REST API**
<table>
<tr><td> 🌐 Stateless </td><td> The server does not store client state between requests. </td></tr>
<tr><td> 🔄 Cacheable </td><td> Responses can be cached to improve performance. </td></tr>
<tr><td> 🏗️ Layered Architecture </td><td> The API structure can include multiple layers (e.g., security, caching, logging). </td></tr>
<tr><td> 🔑 Resource-Based </td><td> Each entity (user, product, order) is represented as a resource with a unique URL. </td></tr>
<tr><td> 🔍 Standard HTTP Methods </td><td> CRUD operations are mapped to HTTP methods (GET, POST, PUT, DELETE). </td></tr>
</table>

### **🔹 Common HTTP Methods in REST APIs**
<table>
<tr><td> **Method** </td><td> **Usage** </td><td> **Example URL** </td></tr>
<tr><td> `GET` </td><td> Retrieve a resource </td><td> `/users/1` </td></tr>
<tr><td> `POST` </td><td> Create a new resource </td><td> `/users` </td></tr>
<tr><td> `PUT` </td><td> Update an existing resource </td><td> `/users/1` </td></tr>
<tr><td> `DELETE` </td><td> Remove a resource </td><td> `/users/1` </td></tr>
</table>

---

### **📌 Designing RESTful Endpoints** {#designing-restful-endpoints}

A RESTful API follows a consistent **URL structure** for better scalability.

### **🔹 Endpoint Design Guidelines**
1️⃣ **Use nouns for resource names, not verbs**  
✅ `/users` (Good)  
❌ `/getUsers` (Bad)  

2️⃣ **Use HTTP methods properly**  
✅ `GET /users/1` (Fetch user)  
✅ `POST /users` (Create user)  
✅ `PUT /users/1` (Update user)  
✅ `DELETE /users/1` (Delete user)  

3️⃣ **Use plural nouns for collections**  
✅ `/users` (Good)  
❌ `/user` (Bad)  

4️⃣ **Nested routes for related resources**  
✅ `/users/1/orders` (Orders for User 1)  

---

### **📌 Handling Requests and Responses** {#handling-requests-and-responses}

A REST API interacts with **JSON data** sent in requests and responses.

### **🔹 Basic API Example in Flask**
```python
from flask import Flask, jsonify, request

app = Flask(__name__)

# Sample user data
users = [
    {"id": 1, "name": "Alice", "email": "alice@example.com"},
    {"id": 2, "name": "Bob", "email": "bob@example.com"},
]

@app.route("/users", methods=["GET"])
def get_users():
    return jsonify(users)

@app.route("/users/<int:user_id>", methods=["GET"])
def get_user(user_id):
    user = next((u for u in users if u["id"] == user_id), None)
    if user:
        return jsonify(user)
    return jsonify({"error": "User not found"}), 404

@app.route("/users", methods=["POST"])
def create_user():
    data = request.json
    new_user = {"id": len(users) + 1, "name": data["name"], "email": data["email"]}
    users.append(new_user)
    return jsonify(new_user), 201

@app.route("/users/<int:user_id>", methods=["PUT"])
def update_user(user_id):
    user = next((u for u in users if u["id"] == user_id), None)
    if user:
        data = request.json
        user.update({"name": data["name"], "email": data["email"]})
        return jsonify(user)
    return jsonify({"error": "User not found"}), 404

@app.route("/users/<int:user_id>", methods=["DELETE"])
def delete_user(user_id):
    global users
    users = [u for u in users if u["id"] != user_id]
    return jsonify({"message": "User deleted"}), 200

if __name__ == "__main__":
    app.run(debug=True)
```

---

### **📌 Returning JSON Data** {#returning-json-data}

Flask uses the `jsonify()` method to return JSON responses.

### **🔹 Example Response**
```json
{
    "id": 1,
    "name": "Alice",
    "email": "alice@example.com"
}
```

### **🔹 Setting HTTP Status Codes**
<table>
<tr><td> **Status Code** </td><td> **Meaning** </td></tr>
<tr><td> `200 OK` </td><td> Successful request </td></tr>
<tr><td> `201 Created` </td><td> Resource successfully created </td></tr>
<tr><td> `400 Bad Request` </td><td> Invalid input data </td></tr>
<tr><td> `401 Unauthorized` </td><td> User authentication required </td></tr>
<tr><td> `404 Not Found` </td><td> Resource not found </td></tr>
<tr><td> `500 Internal Server Error` </td><td> Server encountered an error </td></tr>
</table>

🔹 **Example: Returning a Custom Status Code**
```python
@app.route("/custom_response")
def custom_response():
    return jsonify({"message": "Resource created"}), 201
```

---

### **📌 API Versioning Best Practices** {#api-versioning-best-practices}

As APIs evolve, **versioning** ensures backward compatibility.

### **🔹 API Versioning Methods**
<table>
<tr><td> **Method** </td><td> **Example** </td><td> **Pros & Cons** </td></tr>
<tr><td> URL Versioning </td><td> `/api/v1/users` </td><td> ✅ Easy to implement, ❌ URL changes </td></tr>
<tr><td> Header Versioning </td><td> `Accept: application/vnd.api.v1+json` </td><td> ✅ Clean URLs, ❌ Requires extra headers </td></tr>
<tr><td> Query Parameter Versioning </td><td> `/users?version=1` </td><td> ✅ Simple, ❌ Pollutes query params </td></tr>
</table>

🔹 **Example: URL Versioning in Flask**
```python
@app.route("/api/v1/users", methods=["GET"])
def get_users_v1():
    return jsonify({"version": "v1", "users": users})
```

---

### **📌 Summary**
✔ **REST API** follows stateless, resource-based architecture.  
✔ **Proper endpoint design** improves scalability.  
✔ **Handling requests & responses** ensures clean API interaction.  
✔ **Returning JSON data** with correct status codes helps debugging.  
✔ **API versioning** ensures smooth upgrades without breaking clients.  

--- -->

<!-- ### **🎯 Next: [Securing the Flask Application →](#handling-cors-with-flask-cors)** -->


---

## **8️⃣ Building a RESTful API with Flask-RESTful**

Flask-RESTful is an extension for Flask that simplifies building **REST APIs** by providing a structured way to define resources and request handling.

In this section, we will explore **what a REST API is**, learn how to **design RESTful endpoints**, handle requests and responses using `Flask-RESTful`, return JSON data, and implement **API versioning**.

---

### **📌 What is a REST API?** {#what-is-a-rest-api}

A **REST API** allows clients (like web applications, mobile apps) to interact with a backend service using **HTTP methods**.

### **🔹 Key Principles of REST API**
<table>
<tr><td> 🌐 Stateless </td><td> The server does not store client session data. </td></tr>
<tr><td> 🔄 Cacheable </td><td> Responses can be cached to improve performance. </td></tr>
<tr><td> 🏗️ Layered Architecture </td><td> The API can have multiple layers (e.g., security, caching, logging). </td></tr>
<tr><td> 🔑 Resource-Based </td><td> Each entity (user, product) is a resource with a unique URL. </td></tr>
<tr><td> 🔍 Uses HTTP Methods </td><td> CRUD operations map to GET, POST, PUT, DELETE. </td></tr>
</table>

---

### **📌 Setting Up Flask-RESTful** {#setting-up-flask-restful}

To build APIs using `Flask-RESTful`, we need to install it first.

### **🔹 Install Flask-RESTful**
```bash
pip install flask-restful
```

### **🔹 Modify `main.py` to Use Flask-RESTful**
```python
from flask import Flask
from flask_restful import Api

app = Flask(__name__)

# Initialize Flask-RESTful API
api = Api(app)
```

---

### **📌 Designing RESTful Endpoints** {#designing-restful-endpoints}

A RESTful API should follow **clear endpoint naming conventions**.

### **🔹 API Endpoint Design Guidelines**
1️⃣ **Use nouns for resource names**  
✅ `/users` (Good)  
❌ `/getUsers` (Bad)  

2️⃣ **Use HTTP methods properly**  
✅ `GET /users/1` (Fetch user)  
✅ `POST /users` (Create user)  
✅ `PUT /users/1` (Update user)  
✅ `DELETE /users/1` (Delete user)  

3️⃣ **Use plural nouns for collections**  
✅ `/users` (Good)  
❌ `/user` (Bad)  

4️⃣ **Nested routes for related resources**  
✅ `/users/1/orders` (Orders for User 1)  

---

### **📌 Handling Requests and Responses with Flask-RESTful** {#handling-requests-and-responses}

Flask-RESTful provides the `Resource` class to define API endpoints.

### **🔹 Define API Resources in `resources/user.py`**
```python
from flask_restful import Resource
from flask import request

# Dummy in-memory user data
users = [
    {"id": 1, "name": "Alice", "email": "alice@example.com"},
    {"id": 2, "name": "Bob", "email": "bob@example.com"},
]

class UserList(Resource):
    def get(self):
        """Get all users"""
        return users, 200

    def post(self):
        """Create a new user"""
        data = request.json
        new_user = {"id": len(users) + 1, "name": data["name"], "email": data["email"]}
        users.append(new_user)
        return new_user, 201

class User(Resource):
    def get(self, user_id):
        """Get a user by ID"""
        user = next((u for u in users if u["id"] == user_id), None)
        if user:
            return user, 200
        return {"error": "User not found"}, 404

    def put(self, user_id):
        """Update a user"""
        user = next((u for u in users if u["id"] == user_id), None)
        if user:
            data = request.json
            user.update({"name": data["name"], "email": data["email"]})
            return user, 200
        return {"error": "User not found"}, 404

    def delete(self, user_id):
        """Delete a user"""
        global users
        users = [u for u in users if u["id"] != user_id]
        return {"message": "User deleted"}, 200
```

### **🔹 Register API Endpoints in `main.py`**
```python
from resources.user import UserList, User

# Register API Endpoints
api.add_resource(UserList, "/users")
api.add_resource(User, "/users/<int:user_id>")

if __name__ == "__main__":
    app.run(debug=True)
```

---

### **📌 Returning JSON Data** {#returning-json-data}

Flask-RESTful automatically **formats responses as JSON**.

### **🔹 Example Response**
```json
{
    "id": 1,
    "name": "Alice",
    "email": "alice@example.com"
}
```

### **🔹 Handling HTTP Status Codes**
<table>
<tr><td> **Status Code** </td><td> **Meaning** </td></tr>
<tr><td> `200 OK` </td><td> Successful request </td></tr>
<tr><td> `201 Created` </td><td> Resource successfully created </td></tr>
<tr><td> `400 Bad Request` </td><td> Invalid input data </td></tr>
<tr><td> `401 Unauthorized` </td><td> User authentication required </td></tr>
<tr><td> `404 Not Found` </td><td> Resource not found </td></tr>
<tr><td> `500 Internal Server Error` </td><td> Server encountered an error </td></tr>
</table>

---

### **📌 API Versioning Best Practices** {#api-versioning-best-practices}

API **versioning** ensures compatibility for future updates.

### **🔹 API Versioning Methods**
<table>
<tr><td> **Method** </td><td> **Example** </td><td> **Pros & Cons** </td></tr>
<tr><td> URL Versioning </td><td> `/api/v1/users` </td><td> ✅ Easy to implement, ❌ URL changes required </td></tr>
<tr><td> Header Versioning </td><td> `Accept: application/vnd.api.v1+json` </td><td> ✅ Clean URLs, ❌ Requires custom headers </td></tr>
<tr><td> Query Parameter Versioning </td><td> `/users?version=1` </td><td> ✅ Simple, ❌ Pollutes query parameters </td></tr>
</table>

🔹 **Example: URL Versioning in Flask-RESTful**
```python
api.add_resource(UserList, "/api/v1/users")
api.add_resource(User, "/api/v1/users/<int:user_id>")
```

---

### **📌 Summary**
✔ **Flask-RESTful** simplifies building structured APIs.  
✔ **`Resource` classes** define API endpoints cleanly.  
✔ **Proper endpoint design** ensures scalability.  
✔ **Returning JSON data** is easy with `Flask-RESTful`.  
✔ **API versioning** ensures smooth upgrades.  

---

<!-- ### **🎯 Next: [Securing the Flask Application →](#handling-cors-with-flask-cors)** -->

---

## **9️⃣ Securing the Flask Application**

Security is a crucial aspect of backend development. A **Flask application must be secured** against common vulnerabilities like **CORS issues, excessive API requests, credential leaks, and sensitive data exposure**.

In this section, we will cover **CORS handling**, **rate limiting**, **using environment variables for security**, and **protecting sensitive data**.

---

### **📌 Handling CORS with Flask-CORS** {#handling-cors-with-flask-cors}

**CORS (Cross-Origin Resource Sharing)** is a security feature that restricts resources on a web server to be accessed by **different domains**.

### **🔹 Why Use Flask-CORS?**
<table>
<tr><td> 🌍 Cross-Origin Support </td><td> Allows secure API access from different domains. </td></tr>
<tr><td> 🔒 Security Control </td><td> Prevents unauthorized scripts from making API calls. </td></tr>
<tr><td> 🛠️ Fine-Grained Rules </td><td> Restrict access to specific domains, methods, or headers. </td></tr>
</table>

### **🔹 Install Flask-CORS**
```bash
pip install flask-cors
```

### **🔹 Enable CORS for Flask API**
Modify `main.py`:
```python
from flask import Flask
from flask_cors import CORS

app = Flask(__name__)

# Allow all origins (useful for development)
CORS(app)

# Allow only specific origins
CORS(app, resources={r"/api/*": {"origins": "https://yourdomain.com"}})
```

🔹 **CORS for Flask-RESTful API**
```python
from flask_restful import Api
from flask_cors import CORS

app = Flask(__name__)
CORS(app)  # Enables CORS globally

api = Api(app)
```

---

### **📌 Rate Limiting and Security Best Practices** {#rate-limiting-and-security-best-practices}

**Rate limiting** prevents excessive API requests, reducing the risk of **DDoS attacks and API abuse**.

### **🔹 Install Flask-Limiter**
```bash
pip install flask-limiter
```

### **🔹 Apply Rate Limiting to API**
Modify `main.py`:
```python
from flask_limiter import Limiter
from flask_limiter.util import get_remote_address

# Initialize rate limiter
limiter = Limiter(
    key_func=get_remote_address,  # Uses client's IP address
    default_limits=["100 per hour", "10 per minute"]  # Global limit
)

limiter.init_app(app)
```

🔹 **Apply Rate Limiting to API Endpoints**
```python
from flask_restful import Resource

class LimitedResource(Resource):
    decorators = [limiter.limit("5 per minute")]  # Limit to 5 requests per minute

    def get(self):
        return {"message": "This API is rate-limited"}, 200

api.add_resource(LimitedResource, "/limited")
```

---

### **📌 Using Environment Variables for Security** {#using-environment-variables-for-security}

Environment variables **hide sensitive data**, such as **API keys, database credentials, and secret keys**.

### **🔹 Install Python Dotenv**
```bash
pip install python-dotenv
```

### **🔹 Create a `.env` File**
```ini
FLASK_SECRET_KEY="your_super_secret_key"
DATABASE_URL="sqlite:///database.sqlite3"
JWT_SECRET_KEY="your_jwt_secret_key"
```

### **🔹 Load Environment Variables in Flask**
Modify `main.py`:
```python
import os
from dotenv import load_dotenv

# Load .env file
load_dotenv()

app.config["SECRET_KEY"] = os.getenv("FLASK_SECRET_KEY")
app.config["SQLALCHEMY_DATABASE_URI"] = os.getenv("DATABASE_URL")
app.config["JWT_SECRET_KEY"] = os.getenv("JWT_SECRET_KEY")
```

🔹 **Best Practices for Environment Variables**
<table>
<tr><td> ✅ Use `.env` files </td><td> Store secrets securely in `.env`. </td></tr>
<tr><td> 🚀 Do not commit `.env` </td><td> Add `.env` to `.gitignore` to prevent leaks. </td></tr>
<tr><td> 🔑 Use strong secrets </td><td> Generate secure API keys and passwords. </td></tr>
</table>

---

### **📌 Protecting Sensitive Data** {#protecting-sensitive-data}

Applications must ensure **data is stored and transmitted securely**.

### **🔹 Hash User Passwords**
Store hashed passwords instead of plain text.

```python
from werkzeug.security import generate_password_hash, check_password_hash

# Hashing a password
hashed_password = generate_password_hash("securepassword")

# Verifying a password
check_password_hash(hashed_password, "securepassword")  # Returns True
```

### **🔹 Secure Database Connections**
Use **parameterized queries** to prevent SQL injection.

```python
db.session.execute("SELECT * FROM users WHERE email = :email", {"email": email})
```

### **🔹 Use HTTPS**
Enable HTTPS to secure API communication.

<table>
<tr><td> ✅ Use TLS/SSL </td><td> Encrypt API communication. </td></tr>
<tr><td> 🚀 Use strong headers </td><td> Set **Strict-Transport-Security** headers. </td></tr>
<tr><td> 🔒 Protect against CSRF </td><td> Use **Flask-WTF** or **CSRF tokens**. </td></tr>
</table>

### **🔹 Secure JWT Authentication**
Set **token expiration** to limit misuse.

```python
from datetime import timedelta

app.config["JWT_ACCESS_TOKEN_EXPIRES"] = timedelta(hours=1)  # Expire tokens in 1 hour
```

---

### **📌 Summary**
✔ **Flask-CORS** enables secure cross-origin requests.  
✔ **Rate limiting** prevents excessive API calls.  
✔ **Environment variables** protect sensitive credentials.  
✔ **Secure password hashing** prevents data leaks.  
✔ **HTTPS & encryption** safeguard communication.  

---

<!-- ### **🎯 Next: [Testing and Debugging →](#writing-unit-tests-for-flask)** -->


---

## **11 Deployment and Scaling**

Deploying a Flask application involves configuring **production-ready environments**, optimizing performance, and ensuring scalability. In this section, we will cover **deploying Flask on DigitalOcean**, **using Docker for containerized deployment**, **configuring Gunicorn for production**, **setting up Nginx as a reverse proxy**, and **monitoring and scaling the application**.

---

### **📌 Deploying Flask on DigitalOcean** {#deploying-flask-on-digitalocean}

**DigitalOcean** is a cloud provider that offers an easy way to deploy web applications.

### **🔹 Steps to Deploy on DigitalOcean**
1️⃣ **Create a DigitalOcean Droplet**  
- Go to [DigitalOcean](https://www.digitalocean.com/)
- Choose an **Ubuntu 22.04** droplet
- Select the required CPU & RAM (Minimum: 1GB RAM)
- Add SSH keys for secure access

2️⃣ **Connect to the Droplet via SSH**
```bash
ssh root@your-server-ip
```

3️⃣ **Install Required Packages**
```bash
sudo apt update && sudo apt install python3-pip python3-venv git -y
```

4️⃣ **Clone Your Flask Project**
```bash
git clone https://github.com/your-repo/flask-app.git
cd flask-app
```

5️⃣ **Set Up a Virtual Environment**
```bash
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
```

6️⃣ **Run the Flask App (For Testing)**
```bash
python main.py
```
Access your app at `http://your-server-ip:5000/`.

---

### **📌 Using Docker for Flask Deployment** {#using-docker-for-flask-deployment}

**Docker** makes deployment **consistent, portable, and scalable** by packaging the app into a container.

### **🔹 Install Docker on the Server**
```bash
sudo apt install docker.io -y
sudo systemctl start docker
sudo systemctl enable docker
```

### **🔹 Create a `Dockerfile` in Your Flask Project**
```dockerfile
# Use an official Python runtime as a parent image
FROM python:3.10

# Set the working directory
WORKDIR /app

# Copy the project files
COPY . /app

# Install dependencies
RUN pip install -r requirements.txt

# Expose the port Flask runs on
EXPOSE 5000

# Define the command to run the app
CMD ["python", "main.py"]
```

### **🔹 Build and Run the Docker Container**
```bash
docker build -t flask-app .
docker run -d -p 5000:5000 flask-app
```

Your Flask app will now be running inside a **Docker container**.

---

### **📌 Setting Up Gunicorn for Production** {#setting-up-gunicorn-for-production}

Gunicorn (**Green Unicorn**) is a **WSGI** server used for running Flask in production.

### **🔹 Install Gunicorn**
```bash
pip install gunicorn
```

### **🔹 Run Flask with Gunicorn**
```bash
gunicorn -w 4 -b 0.0.0.0:5000 main:app
```
- `-w 4` → Uses **4 worker processes** for handling requests.
- `-b 0.0.0.0:5000` → Binds Gunicorn to port **5000**.

### **🔹 Create a Gunicorn Systemd Service**
```bash
sudo nano /etc/systemd/system/flask.service
```
Paste the following content:
{% raw %}
```ini
[Unit]
Description=Flask Application
After=network.target

[Service]
User=root
WorkingDirectory=/home/your-user/flask-app
ExecStart=/home/your-user/flask-app/venv/bin/gunicorn -w 4 -b 0.0.0.0:5000 main:app
Restart=always

[Install]
WantedBy=multi-user.target
```
{% endraw %}


### **🔹 Start and Enable Gunicorn**
```bash
sudo systemctl start flask
sudo systemctl enable flask
```

Gunicorn is now **managing your Flask app in production**.

---

### **📌 Using Nginx as a Reverse Proxy** {#using-nginx-as-a-reverse-proxy}

Nginx is a **high-performance reverse proxy** that sits between **Flask (Gunicorn)** and the internet, improving **security and performance**.

### **🔹 Install Nginx**
```bash
sudo apt install nginx -y
```

### **🔹 Configure Nginx for Flask**
```bash
sudo nano /etc/nginx/sites-available/flask
```

Paste the following configuration:
```ini
server {
    listen 80;
    server_name your-domain.com;

    location / {
        proxy_pass http://127.0.0.1:5000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
```

### **🔹 Enable and Restart Nginx**
```bash
sudo ln -s /etc/nginx/sites-available/flask /etc/nginx/sites-enabled/
sudo systemctl restart nginx
sudo systemctl enable nginx
```

Now, **Nginx will forward requests** to your Flask app running via **Gunicorn**.

---

### **📌 Monitoring and Scaling the Application** {#monitoring-and-scaling-the-application}

Monitoring ensures that **your application runs efficiently and scales properly**.

### **🔹 Monitor Flask Logs**
Check **Flask logs** to debug issues:
```bash
journalctl -u flask -f
```

### **🔹 Monitor Gunicorn Performance**
```bash
ps aux | grep gunicorn
```

### **🔹 Monitor Nginx Logs**
```bash
sudo tail -f /var/log/nginx/access.log
sudo tail -f /var/log/nginx/error.log
```

### **🔹 Scaling Flask Application**
<table>
<tr><td> **Method** </td><td> **Description** </td></tr>
<tr><td> **Increase Gunicorn Workers** </td><td> More processes to handle requests (e.g., `gunicorn -w 8`). </td></tr>
<tr><td> **Use Load Balancers** </td><td> Distribute traffic between multiple Flask servers. </td></tr>
<tr><td> **Use Docker Swarm/Kubernetes** </td><td> Containerized scaling of Flask apps. </td></tr>
<tr><td> **Optimize Database Queries** </td><td> Use indexes, caching, and connection pooling. </td></tr>
</table>

---

### **📌 Summary**
✔ **Deploy Flask on DigitalOcean** with a secure droplet setup.  
✔ **Use Docker** for containerized deployment.  
✔ **Run Flask with Gunicorn** for production performance.  
✔ **Configure Nginx** as a reverse proxy.  
✔ **Monitor logs and scale** Flask for high traffic.  

---

<!-- ### **🎯 Next: [Final Project: Building a Full-Stack Application →](#project-overview)** -->

  

---

## **1️2 Final Project: Building a Full-Stack Application**

This project is a **Task Management System**, allowing **admins, managers, and employees** to manage **tasks, users, reports, and notifications** using a **secure and scalable backend**.

🚀 **GitHub Repo:** [Task Management App](https://github.com/0rajnishk/Task-management-App)  
📺 **YouTube Tutorial:** [Full Flask Backend Development Guide](https://youtu.be/m_J8205VCAa)

### ✅ **Key Features**
✔ **User Registration & Authentication** with **JWT**  
✔ **Role-Based Access Control (RBAC)** for admins, managers, and employees  
✔ **Task Management System** with CRUD operations  
✔ **Automated Email Notifications** using **Flask-Mail**  
✔ **Daily Task Reminders for Employees** at **10 AM**  
✔ **Admin & Manager Reports via Email**  
✔ **Redis Caching & Celery for Background Jobs**  
✔ **RESTful API Design using Flask-RESTful**  
✔ **Deployed with Docker, Gunicorn, and Nginx**  

---

### **📌 Project Overview** {#project-overview}

This Task Management System supports:
- **User Management** (Registration, Approval, Role Management)
- **Task Assignment & Tracking** (CRUD operations for tasks)
- **Automated Email Notifications** (Signup, Daily Reminders, Reports)
- **Security Features** (Role-Based Access Control, JWT Authentication)
- **Deployment & Scaling** (Dockerized Flask App with Gunicorn & Nginx)

The system follows a **role-based hierarchy**:

| **Role**     | **Permissions** |
|-------------|----------------|
| **Admin**   | Full control over users & tasks, generate reports |
| **Manager** | Assign tasks, manage employees, generate reports |
| **Employee** | View & complete assigned tasks |

---

### **📌 Designing the Database Schema** {#designing-the-database-schema}

This system consists of two main tables: **User** and **Task**.

### **🔹 User Model**
Each user has an **ID, username, email, password, role (admin/manager/employee), approval status, and timestamps**.

### **🔹 Task Model**
Each task contains an **ID, title, description, status (pending/in-progress/completed), deadline, assigned user, and timestamps**.

🔹 **Users need admin approval before they can access the system**.  
🔹 **Tasks can only be modified by assigned employees, managers, or admins**.

---

### **📌 Implementing Authentication and Authorization** {#implementing-authentication-and-authorization}

### **🔹 User Registration & Login**
- **Users can sign up** with their details (name, email, password, role).
- **Admins approve or reject users** before they gain access.
- **JWT-based authentication** ensures secure session management.

### **🔹 Role-Based Access Control (RBAC)**
- **Admin-only endpoints** (`/users/pending`, `/users/<id>/approve`, `/stats`)
- **Manager-only endpoints** (`/tasks`, `/task/<id>/assign`)
- **Employee-specific tasks** (`/tasks/mine`, `/task/<id>/status`)

🔹 **RBAC is enforced using decorators** like `@role_required(["admin"])`.

---

### **📌 Building RESTful API Endpoints** {#building-restful-api-endpoints}

The API follows **RESTful principles**, using **Flask-RESTful** for structured request handling.
<table>
  <thead>
    <tr>
      <th colspan="4">🔹 Authentication Endpoints</th>
    </tr>
    <tr>
      <th>Endpoint</th>
      <th>Method</th>
      <th>Access</th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><code>/login</code></td>
      <td>POST</td>
      <td>Public</td>
      <td>Authenticate users and generate JWT tokens</td>
    </tr>
    <tr>
      <td><code>/register</code></td>
      <td>POST</td>
      <td>Public</td>
      <td>Allow new users to register</td>
    </tr>
    <tr>
      <td><code>/users/pending</code></td>
      <td>GET</td>
      <td>Admin</td>
      <td>List all pending user approvals</td>
    </tr>
    <tr>
      <td><code>/users/&lt;id&gt;/approve</code></td>
      <td>PUT</td>
      <td>Admin</td>
      <td>Approve a user</td>
    </tr>
    <tr>
      <td><code>/users/&lt;id&gt;/reject</code></td>
      <td>DELETE</td>
      <td>Admin</td>
      <td>Reject a user</td>
    </tr>
  </tbody>
</table>

<table>
  <thead>
    <tr>
      <th colspan="4">🔹 Task Management Endpoints</th>
    </tr>
    <tr>
      <th>Endpoint</th>
      <th>Method</th>
      <th>Access</th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><code>/tasks</code></td>
      <td>GET</td>
      <td>Admin</td>
      <td>Retrieve all tasks</td>
    </tr>
    <tr>
      <td><code>/tasks/mine</code></td>
      <td>GET</td>
      <td>Employee</td>
      <td>Fetch assigned tasks for the logged-in employee</td>
    </tr>
    <tr>
      <td><code>/task/&lt;id&gt;</code></td>
      <td>GET</td>
      <td>Assigned User, Manager, Admin</td>
      <td>Get task details</td>
    </tr>
    <tr>
      <td><code>/tasks</code></td>
      <td>POST</td>
      <td>Manager, Admin</td>
      <td>Create a new task</td>
    </tr>
    <tr>
      <td><code>/task/&lt;id&gt;</code></td>
      <td>PUT</td>
      <td>Manager, Admin</td>
      <td>Update task details</td>
    </tr>
    <tr>
      <td><code>/task/&lt;id&gt;/status</code></td>
      <td>PUT</td>
      <td>Employee, Manager, Admin</td>
      <td>Update task status</td>
    </tr>
    <tr>
      <td><code>/task/&lt;id&gt;/assign</code></td>
      <td>PUT</td>
      <td>Manager</td>
      <td>Assign a task to an employee</td>
    </tr>
  </tbody>
</table>

<table>
  <thead>
    <tr>
      <th colspan="4">🔹 Reports &amp; Statistics</th>
    </tr>
    <tr>
      <th>Endpoint</th>
      <th>Method</th>
      <th>Access</th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><code>/stats</code></td>
      <td>GET</td>
      <td>Admin, Manager</td>
      <td>Get system-wide statistics</td>
    </tr>
  </tbody>
</table>

---

### **📌 Caching Data and Using Celery Tasks** {#caching-data-and-using-celery-tasks}

### **🔹 Redis for Caching**
- API responses are cached to reduce **database load**.
- Frequently accessed queries (like **task lists**) are **stored in Redis**.

### **🔹 Celery for Background Jobs**
- **Sends emails asynchronously** without blocking user requests.
- **Daily Task Reminders** run at **10 AM** for employees.
- **Admin & Manager Reports** are sent automatically.

---

### **📌 Integrating Email Notifications** {#integrating-email-notifications}

### **🔹 Welcome Email on Signup**
- Sends a **welcome email** to new employees upon **successful registration**.

### **🔹 Daily Task Reminder Emails**
- Every morning at **10 AM**, employees receive an email **listing pending tasks**.

### **🔹 Task Summary Reports**
- Managers and Admins can **request a task summary** to be sent via email.

🔹 **Flask-Mail Configuration**
- Uses **SMTP** (`smtpout.secureserver.net`) for email delivery.
- Stores **credentials securely** in **environment variables**.

---

### **📌 Deploying the Final Application** {#deploying-the-final-application}

<table>
  <thead>
    <tr>
      <th>🔹 Deployment Stack</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><strong>Technology</strong></td>
      <td><strong>Purpose</strong></td>
    </tr>
    <tr>
      <td><strong>Gunicorn</strong></td>
      <td>WSGI server for handling multiple requests</td>
    </tr>
    <tr>
      <td><strong>Nginx</strong></td>
      <td>Reverse proxy to handle traffic efficiently</td>
    </tr>
    <tr>
      <td><strong>Docker</strong></td>
      <td>Containerized deployment for consistency</td>
    </tr>
    <tr>
      <td><strong>Redis</strong></td>
      <td>Caching system to optimize database performance</td>
    </tr>
    <tr>
      <td><strong>Celery</strong></td>
      <td>Asynchronous task execution for background jobs</td>
    </tr>
  </tbody>
</table>

### **🔹 Deployment Steps**
1️⃣ **Run Flask with Gunicorn**
```bash
gunicorn -w 4 -b 0.0.0.0:5000 main:app
```
2️⃣ **Set Up Nginx Reverse Proxy**

{% raw %}
```bash
server {
    listen 80;
    server_name your-domain.com;
    location / {
        proxy_pass http://127.0.0.1:5000;
    }
}
```
{% endraw %}

3️⃣ **Run the Application with Docker**
```bash
docker build -t flask-task-app .
docker run -d -p 5000:5000 flask-task-app
```
4️⃣ **Set Up a Background Scheduler for Emails**
```bash
celery -A tasks.celery worker --loglevel=info
```

---

### **📌 Summary**
✔ **Role-Based Access Control (RBAC)** for secure authentication.  
✔ **Flask-RESTful API** for structured backend design.  
✔ **Redis caching & Celery background tasks** for performance.  
✔ **Automated email notifications** for task management.  
✔ **Dockerized deployment** using **Gunicorn & Nginx**.  

🚀 **GitHub Repo:** [Task Management App](https://github.com/0rajnishk/Task-management-App)  
📺 **YouTube Tutorial:** [Full Flask Backend Development Guide](https://youtu.be/m_J8205VCAa)  

---

### **🎯 Congratulations! You've built a complete Flask backend system! 🎯**
