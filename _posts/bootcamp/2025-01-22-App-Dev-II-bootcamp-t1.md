---
title: "Bootcamp Tutorial 1: App Development II with SQLAlchemy, JWT, REST APIs, and CORS"
date: "2025-01-23 00:00:00"
categories: [bootcamp]
tags: [bootcamp, flask, RESTful APIs, JWT, CORS]
---

<!-- # Bootcamp: Comprehensive Tutorial for Flask API Development -->

## Introduction
This tutorial covers essential concepts and practical implementation of modern application development using Flask, SQLAlchemy, JWT, REST APIs, and CORS. By the end of this guide, you'll have a deep understanding of how to create a secure, scalable, and efficient backend API.

We will:
- Set up a project using Flask
- Configure and connect a SQLite database
- Implement JWT-based authentication
- Manage user and task resources with role-based access control
- Secure API communication using CORS

Below are detailed explanations followed by the complete source code and Postman API test details.

---

## Key Concepts

### 1. Flask Setup
To start building APIs, we initialize a Flask application and configure important services such as SQLAlchemy and JWT for secure communication.

```python
app = Flask(__name__)
app.config['CORS_HEADERS'] = 'Content-Type, Authorization'
app.config["SQLALCHEMY_DATABASE_URI"] = "sqlite:///project.db"
app.config["JWT_SECRET_KEY"] = "aStrongSecretKey"
app.config["JWT_ACCESS_TOKEN_EXPIRES"] = timedelta(hours=24)
```

### 2. Database Integration with SQLAlchemy
We define two tables, `User` and `Task`, to manage user information and task assignments. Each table uses SQLAlchemy models to define its structure and relationships.

#### **User Table**
The `User` table handles authentication, roles, and admin approval.

```python
class User(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    username = db.Column(db.String(80), unique=True, nullable=False)
    email = db.Column(db.String(120), unique=True, nullable=False)
    password_hash = db.Column(db.String(128), nullable=False)
    role = db.Column(db.String(20), default="employee")  # Roles: employee, manager, admin
    is_approved = db.Column(db.Boolean, default=False)
    created_at = db.Column(db.DateTime, default=datetime.utcnow)
```

#### **Task Table**
The `Task` table manages tasks and assigns them to users.

```python
class Task(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    title = db.Column(db.String(255), nullable=False)
    status = db.Column(db.String(20), default="pending")
    deadline = db.Column(db.DateTime, nullable=True)
    assigned_user_id = db.Column(db.Integer, db.ForeignKey('user.id'), nullable=True)
```

### 3. JWT for Authentication
We secure API endpoints using JSON Web Tokens (JWT). Users can sign up and log in, generating an access token.

#### **User Signup**
```python
class SignupResource(Resource):
    @cross_origin()
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
        return {"username": username, "email": email}
```

#### **User Login and Token Generation**
```python
class LoginResource(Resource):
    def post(self):
        data = request.get_json()
        email = data['email']
        password = data['password']
        user = User.query.filter_by(email=email).first()
        if user and check_password_hash(user.password_hash, password):
            access_token = create_access_token(identity=user.username)
            response = jsonify({"msg":"successfully logged in", "token": access_token})
            return make_response(response, 401)
        response = jsonify({"msg": "email or password incorrect."})
        return make_response(response, 200)
```

### 4. RESTful API Design
We use Flask-RESTful to define resources and manage API routing.

#### **Hello Resource (Protected)**
```python
class Hello(Resource):
    @cross_origin()
    @jwt_required()
    def get(self):
        user_name = get_jwt_identity()
        return jsonify({'msg': "hello world! from flask restful", 'user_name': user_name})
```

### 5. Enabling CORS
To enable cross-origin resource sharing, we configure CORS support:
```python
CORS(app, resources={r"/*": {"origins": "*"}}, supports_credentials=True)
```

---

## Complete Source Code
Below is the complete source code:

```python
from datetime import datetime
from flask import Flask, request, jsonify, make_response
from flask_cors import CORS
from flask_sqlalchemy import SQLAlchemy
from flask_jwt_extended import JWTManager, create_access_token, jwt_required, get_jwt_identity
from werkzeug.security import generate_password_hash, check_password_hash
from flask_restful import Api, Resource
from datetime import timedelta
from flask_cors import cross_origin

app = Flask(__name__)

app.config['CORS_HEADERS'] = 'Content-Type, Authorization'
app.config["SQLALCHEMY_DATABASE_URI"] = "sqlite:///project.db"
app.config["JWT_SECRET_KEY"] = "aStrongSecretKey"
app.config["JWT_ACCESS_TOKEN_EXPIRES"] = timedelta(hours=24)
db = SQLAlchemy(app)
jwt = JWTManager(app)
api = Api(app)

class User(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    username = db.Column(db.String(80), unique=True, nullable=False)
    email = db.Column(db.String(120), unique=True, nullable=False)
    password_hash = db.Column(db.String(128), nullable=False)
    role = db.Column(db.String(20), default="employee")
    is_approved = db.Column(db.Boolean, default=False)
    created_at = db.Column(db.DateTime, default=datetime.utcnow)
    tasks = db.relationship('Task', backref='assigned_user', lazy=True)

    def set_password(self, password):
        self.password_hash = generate_password_hash(password)

    def check_password(self, password):
        return check_password_hash(self.password_hash, password)

class Task(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    title = db.Column(db.String(255), nullable=False)
    description = db.Column(db.Text, nullable=True)
    status = db.Column(db.String(20), default="pending")
    deadline = db.Column(db.DateTime, nullable=True)
    created_at = db.Column(db.DateTime, default=datetime.utcnow)
    assigned_user_id = db.Column(db.Integer, db.ForeignKey('user.id'), nullable=True)

with app.app_context():
    db.create_all() 

class Hello(Resource):
    @cross_origin()
    @jwt_required()
    def get(self):
        user_name = get_jwt_identity()
        return jsonify({'msg': "hello world! from flask restful", 'user_name': user_name})

class SignupResource(Resource):
    @cross_origin()
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
        return {"username": username, "email": email}

class LoginResource(Resource):
    def post(self):
        data = request.get_json()
        email = data['email']
        password = data['password']
        user = User.query.filter_by(email=email).first()
        if user and check_password_hash(user.password_hash, password):
            access_token = create_access_token(identity=user.username)
            response = jsonify({"msg":"successfully logged in", "token": access_token})
            return make_response(response, 401)
        response = jsonify({"msg": "email or password incorrect."})
        return make_response(response, 200)

api.add_resource(Hello, '/hello')
api.add_resource(SignupResource, '/signup')
api.add_resource(LoginResource, '/login')

if __name__ == '__main__':
    app.run(debug=True, port=5000, host="127.0.0.1")
```

---

## API Testing with Postman

### 1. Signup API
- **URL:** `http://127.0.0.1:5000/signup`
- **Method:** `POST`
- **Headers:** `Content-Type: application/json`
- **Payload:**
  ```json
  {
    "username": "testuser",
    "email": "testuser@example.com",
    "password": "testpassword"
  }
  ```

### 2. Login API
- **URL:** `http://127.0.0.1:5000/login`
- **Method:** `POST`
- **Headers:** `Content-Type: application/json`
- **Payload:**
  ```json
  {
    "email": "testuser@example.com",
    "password": "testpassword"
  }
  ```
- **Response:**
  ```json
  {
    "msg": "successfully logged in",
    "token": "<JWT Token>"
  }
  ```

### 3. Protected Hello API
- **URL:** `http://127.0.0.1:5000/hello`
- **Method:** `GET`
- **Headers:**
  ```json
  {
    "Authorization": "Bearer <JWT Token>"
  }
  ```


🚀 Next: 

[Mastering Decorators & Database Communication in Flask API]({{ site.baseurl }}/posts/App-Dev-II-bootcamp-t3/)
[Vue.js Fundamentals: Setting Up Authentication & Routing]({{ site.baseurl }}/posts/App-Dev-II-bootcamp-t2/)

🚀 bonus: 
[API Testing with Postman]({{ site.baseurl }}/posts/App-Dev-II-bootcamp-postman/)







<!-- ---
title: "Bootcamp: App dev II sqlalchemy, Jwt, Rest APIs, Cors"
date: "2025-01-22 00:00:00"
categories: [Development]
tags: [bootcamp, vue, flask]
---

# Initializing Project

```python
from datetime import datetime
from flask import Flask, request, jsonify, make_response
from flask_cors import CORS
from flask_sqlalchemy import SQLAlchemy
from flask_jwt_extended import JWTManager, create_access_token, jwt_required, get_jwt_identity
from werkzeug.security import generate_password_hash, check_password_hash
from flask_restful import Api, Resource
from datetime import timedelta
from flask_cors import cross_origin



app = Flask(__name__)


app.config['CORS_HEADERS'] = 'Content-Type, Authorization'
app.config["SQLALCHEMY_DATABASE_URI"] = "sqlite:///project.db"
app.config["JWT_SECRET_KEY"] = "aStrongSecretKey"
app.config["JWT_ACCESS_TOKEN_EXPIRES"] = timedelta(hours=24)
db = SQLAlchemy(app)
jwt = JWTManager(app)
api = Api(app)

# CORS(app, resources={r"/*": {"origins": "*"}}, supports_credentials=True)


### 📌 USER TABLE (Authentication & Roles)
class User(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    username = db.Column(db.String(80), unique=True, nullable=False)
    email = db.Column(db.String(120), unique=True, nullable=False)
    password_hash = db.Column(db.String(128), nullable=False)
    role = db.Column(db.String(20), default="employee")  # employee, manager, admin
    is_approved = db.Column(db.Boolean, default=False)  # Admin approval required
    created_at = db.Column(db.DateTime, default=datetime.utcnow)
    
    # Relationship: A user can have multiple tasks
    tasks = db.relationship('Task', backref='assigned_user', lazy=True)

    def set_password(self, password):
        self.password_hash = generate_password_hash(password)

    def check_password(self, password):
        return check_password_hash(self.password_hash, password)

    def serialize(self):
        return {
            'id': self.id,
            "username": self.username,
            "email": self.email,
            "role": self.role,
            "is_approved": self.is_approved,
            "created_at": self.created_at
        }


### 📌 TASK TABLE (Task Management)
class Task(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    title = db.Column(db.String(255), nullable=False)
    description = db.Column(db.Text, nullable=True)
    status = db.Column(db.String(20), default="pending")  # pending, in_progress, completed
    deadline = db.Column(db.DateTime, nullable=True)
    created_at = db.Column(db.DateTime, default=datetime.utcnow)

    # Foreign Key: Assigned User (Only employees or managers can be assigned)
    assigned_user_id = db.Column(db.Integer, db.ForeignKey('user.id'), nullable=True)

    def serialize(self):
        return {
            "id": self.id,
            "title": self.title,
            "description": self.description,
            "status": self.status,
            "deadline": self.deadline.strftime('%Y-%m-%d') if self.deadline else None,
            "assigned_user": self.assigned_user.username if self.assigned_user else None,
            "created_at": self.created_at
        }


with app.app_context():
    db.create_all() 


# Hello world resourse
class Hello(Resource):
    @cross_origin()
    @jwt_required()
    def get(self):
        user_name = get_jwt_identity()
        print(user_name)
        return jsonify({'msg': "hello world! from flask restful", 'user_name':user_name})


class SignupResource(Resource):
    @cross_origin()
    def post(self):
        data = request.get_json()
        username = data['username']
        email = data['email']
        password = data['password']
        
        # Check if user already exists
        if User.query.filter_by(username=username).first() or User.query.filter_by(email=email).first():
            return jsonify({'message': 'Username or Email already exists'})

        hashed_password = generate_password_hash(password)

        new_user = User(username=username, email=email, password_hash=hashed_password)

        db.session.add(new_user)
        db.session.commit()

        return {username:username, email:email}
            

class LoginResource(Resource):
    def post(self):
        data = request.get_json()
        email = data['email']
        password = data['password']

        user = User.query.filter_by(email=email).first()

        if user and check_password_hash(user.password_hash, password):
            access_token = create_access_token(identity=user.username)
            response = jsonify({"msg":"successfully logged in", "token": access_token})
            print("\n"*4,access_token)
            return make_response(response, 401)

        response = jsonify({"msg": "email or password incorrect."})
        return make_response(response, 200)


api.add_resource(Hello, '/hello')
api.add_resource(SignupResource, '/signup')
api.add_resource(LoginResource, '/login')


if __name__ == '__main__':
    app.run(debug=True, port=5000, host="127.0.0.1")

``` -->