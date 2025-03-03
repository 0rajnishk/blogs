---
title: "Bootcamp Tutorial 4: Flask, Celery, Redis, and SMTP Integration"  
date: "2025-03-02 00:00:00"
categories: [bootcamp]
tags: [bootcamp, vue, flask]
---

---

# Flask, Celery, Redis, and SMTP Integration

In this guide, we will integrate **Flask** with **Celery** and **Redis** for background task management, caching, and **SMTP** for email sending. We will also set up API endpoints to:
- Send emails
- Cache data with Redis
- Queue background tasks
- Schedule recurring tasks with Celery Beat

## Prerequisites
1. Install required dependencies:
```bash
pip install Flask Flask-RESTful Celery redis Flask-Mail python-dotenv
```

2. Ensure **Redis** is installed and running:
```bash
sudo systemctl start redis  # For Linux
```

---

## Setup: Initialize Flask App

Create a file named `main.py` and add the following code:

```python
import os
from flask import Flask, request
from flask_restful import Api, Resource
from flask_mail import Mail, Message
from celery import Celery
import redis
from dotenv import load_dotenv
from celery.schedules import crontab

# Load environment variables
load_dotenv()

app = Flask(__name__)
api = Api(app)

# ==========================
# Flask-Mail Configuration
# ==========================
app.config['MAIL_SERVER'] = 'smtp.gmail.com'
app.config['MAIL_PORT'] = 587
app.config['MAIL_USE_TLS'] = True
app.config['MAIL_USERNAME'] = os.getenv('MAIL_USER')
app.config['MAIL_PASSWORD'] = os.getenv('MAIL_PASS')
app.config['MAIL_DEFAULT_SENDER'] = os.getenv('MAIL_USER')

mail = Mail(app)

# ==========================
# Celery Configuration
# ==========================
app.config['broker_url'] = os.getenv('BROKER_URL', 'redis://localhost:6379/0')
app.config['result_backend'] = os.getenv('RESULT_BACKEND', 'redis://localhost:6379/0')

celery = Celery(app.name, broker=app.config['broker_url'], backend=app.config['result_backend'])
celery.conf.broker_connection_retry_on_startup = True

# ==========================
# Redis Cache Setup
# ==========================
cache = redis.StrictRedis(host='localhost', port=6379, db=0, decode_responses=True)

# ==========================
# Celery Initialization
# ==========================
def init_celery(flask_app):
    celery_app = Celery(
        flask_app.import_name,
        broker=flask_app.config['broker_url'],
        backend=flask_app.config['result_backend']
    )
    celery_app.conf.update(flask_app.config)
    celery_app.conf.broker_connection_retry_on_startup = True

    class ContextTask(celery_app.Task):
        def __call__(self, *args, **kwargs):
            with flask_app.app_context():
                return super().__call__(*args, **kwargs)

    celery_app.Task = ContextTask
    return celery_app

celery = init_celery(app)
```

---

## Tasks

### 1. **Sending Emails with SMTP in Flask**

This API allows users to send an email using **Flask-Mail** by passing an email address as a query parameter.

```python
class SendEmail(Resource):
    def get(self):
        email = request.args.get('email')
        if not email:
            return {'message': 'Error: No email provided'}, 400

        msg = Message(
            subject="Test Email from Flask",
            sender=app.config['MAIL_DEFAULT_SENDER'],
            recipients=[email],
            body="This is a test email sent via Flask and SMTP."
        )
        try:
            mail.send(msg)
            return {'message': f'Email sent successfully to {email}!'}, 200
        except Exception as e:
            return {'message': f"Error: {str(e)}"}, 500

api.add_resource(SendEmail, '/send-email')
```

#### **Testing Email API**
```bash
curl "http://127.0.0.1:5000/send-email?email=recipient@example.com"
```

---

### 2. **Implementing Cache for Performance Improvement**

This API demonstrates **caching with Redis**.

```python
class CacheDemo(Resource):
    def get(self):
        try:
            cached_data = cache.get('data')
            if cached_data:
                return {'data': cached_data}, 200
            else:
                data = 'This is a cached response!'
                cache.set('data', data, ex=60)
                return {'data': data}, 200
        except Exception as e:
            return {'message': f"Redis Error: {str(e)}"}, 500

api.add_resource(CacheDemo, '/cache')
```

A separate endpoint to **clear the cache**:
```python
class DeleteCache(Resource):
    def post(self):
        try:
            cache.delete('data')
            return {'message': 'Cache deleted successfully!'}, 200
        except Exception as e:
            return {'message': f"Redis Error: {str(e)}"}, 500

api.add_resource(DeleteCache, '/delete-cache')
```

#### **Testing Cache API**
```bash
curl http://127.0.0.1:5000/cache
curl -X POST http://127.0.0.1:5000/delete-cache
```

---

### 3. **Queued Tasks with Celery**

This API triggers a **background task** using Celery.

```python
@celery.task(name="tasks.background_task")
def background_task():
    return 'Task completed'

class QueuedTask(Resource):
    def get(self):
        task = background_task.apply_async()
        return {'task_id': task.id}, 202

api.add_resource(QueuedTask, '/queued-task')
```

#### **Testing Celery Task**
```bash
curl http://127.0.0.1:5000/queued-task
```

---

### 4. **Scheduled Task with Celery Beat**

Schedules a **daily reminder task at 7:00 AM IST**.

```python
from celery.schedules import crontab

celery.conf.timezone = 'Asia/Kolkata'
celery.conf.beat_schedule = {
    'send_reminders': {
        'task': 'tasks.send_reminders',
        'schedule': crontab(hour=7, minute=0),
        'args': ()
    },
}

@celery.task(name="tasks.send_reminders")
def send_reminders():
    print("Reminder sent at 7:00 AM IST!")
    return "Reminder sent successfully!"
```

---

## Running the Application

1️⃣ **Start the Flask app**
```bash
python3 main.py
```

2️⃣ **Start Celery Worker**
```bash
celery -A main.celery worker --loglevel=info
```

3️⃣ **Start Celery Beat**
```bash
celery -A main.celery beat --loglevel=info
```

---

## **Final `.env` File**
Create a `.env` file in the project directory:

```
MAIL_USER=your_email@gmail.com
MAIL_PASS=your_email_password
BROKER_URL=redis://localhost:6379/0
RESULT_BACKEND=redis://localhost:6379/0
```

---

## **Testing with Postman**
1. **Send Email**  
   `GET http://localhost:5000/send-email?email=recipient@example.com`
2. **Check Cache**  
   `GET http://localhost:5000/cache`
3. **Clear Cache**  
   `POST http://localhost:5000/delete-cache`
4. **Run Background Task**  
   `GET http://localhost:5000/queued-task`
5. **Check Scheduled Task Output**  
   Look at logs after 7:00 AM IST.

---

### 🚀 **Final code**
```python


import os
from flask import Flask, request
from flask_restful import Api, Resource
from flask_mail import Mail, Message
from celery import Celery
import redis
from dotenv import load_dotenv
from celery.schedules import crontab

# Load environment variables from .env
load_dotenv()

app = Flask(__name__)
api = Api(app)

# ==========================
# Flask-Mail Configuration
# ==========================
app.config['MAIL_SERVER'] = 'smtp.gmail.com'
app.config['MAIL_PORT'] = 587
app.config['MAIL_USE_TLS'] = True
app.config['MAIL_USERNAME'] = os.getenv('MAIL_USER')
app.config['MAIL_PASSWORD'] = os.getenv('MAIL_PASS')
app.config['MAIL_DEFAULT_SENDER'] = os.getenv('MAIL_USER')

mail = Mail(app)

# ==========================
# Celery Configuration (Updated)
# ==========================
app.config['broker_url'] = os.getenv('BROKER_URL', 'redis://localhost:6379/0')
app.config['result_backend'] = os.getenv('RESULT_BACKEND', 'redis://localhost:6379/0')

celery = Celery(app.name, broker=app.config['broker_url'], backend=app.config['result_backend'])
celery.conf.broker_connection_retry_on_startup = True  # Fix Celery 6.0 deprecation warning

# ==========================
# Redis Cache Setup
# ==========================
cache = redis.StrictRedis(host='localhost', port=6379, db=0, decode_responses=True)

# ==========================
# Celery Initialization
# ==========================
def init_celery(flask_app):
    celery_app = Celery(
        flask_app.import_name,
        broker=flask_app.config['broker_url'],
        backend=flask_app.config['result_backend']
    )
    celery_app.conf.update(flask_app.config)
    celery_app.conf.broker_connection_retry_on_startup = True  # Ensure retry on startup

    class ContextTask(celery_app.Task):
        def __call__(self, *args, **kwargs):
            with flask_app.app_context():
                return super().__call__(*args, **kwargs)

    celery_app.Task = ContextTask
    return celery_app

celery = init_celery(app)

# ==========================
# API Routes
# ==========================
class SendEmail(Resource):
    def get(self):
        email = request.args.get('email')
        if not email:
            return {'message': 'Error: No email provided'}, 400

        msg = Message(
            subject="Test Email from Flask",
            sender=app.config['MAIL_DEFAULT_SENDER'],
            recipients=[email],
            body="This is a test email sent via Flask and SMTP."
        )
        try:
            mail.send(msg)
            return {'message': f'Email sent successfully to {email}!'}, 200
        except Exception as e:
            return {'message': f"Error: {str(e)}"}, 500

class CacheDemo(Resource):
    def get(self):
        try:
            cached_data = cache.get('data')
            if cached_data:
                return {'data': cached_data}, 200
            else:
                data = 'This is a cached response!'
                cache.set('data', data, ex=60)
                return {'data': data}, 200
        except Exception as e:
            return {'message': f"Redis Error: {str(e)}"}, 500

class DeleteCache(Resource):
    def post(self):
        try:
            cache.delete('data')
            return {'message': 'Cache deleted successfully!'}, 200
        except Exception as e:
            return {'message': f"Redis Error: {str(e)}"}, 500

@celery.task(name="tasks.background_task")
def background_task():
    return 'Task completed'

class QueuedTask(Resource):
    def get(self):
        task = background_task.apply_async()
        return {'task_id': task.id}, 202

# ==========================
# Celery Beat Configuration
# ==========================
celery.conf.timezone = 'Asia/Kolkata'
celery.conf.beat_schedule = {
    'send_reminders': {
        'task': 'tasks.send_reminders',
        'schedule': crontab(hour=7, minute=0),  # Runs daily at 7:00 AM IST
        'args': ()
    },
}

@celery.task(name="tasks.send_reminders")
def send_reminders():
    print("Reminder sent at 7:00 AM IST!")
    return "Reminder sent successfully!"

# Register API routes
api.add_resource(SendEmail, '/send-email')
api.add_resource(CacheDemo, '/cache')
api.add_resource(DeleteCache, '/delete-cache')
api.add_resource(QueuedTask, '/queued-task')

if __name__ == '__main__':
    app.run(debug=True)

```



```bash
# install redis
sudo apt update
sudo apt install redis-server

# Start Redis
sudo systemctl start redis

# Start the Flask app
python3 main.py

# Start Celery Worker
celery -A main.celery worker --loglevel=info

# Start Celery Beat
celery -A main.celery beat --loglevel=info
```

<!-- 🔗 **Next:** [Vue.js Advanced API Integration]({{ site.baseurl }}/posts/App-Dev-II-bootcamp-t4/) -->
