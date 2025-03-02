---
title: "Bootcamp Tutorial 4: Flask, Celery, Redis, and SMTP Integration"  
date: "2025-03-02 00:00:00"
categories: [bootcamp]
tags: [bootcamp, vue, flask]
---


Here’s a structured **Markdown file** for the tasks you want to implement using Flask, Celery, Redis, and SMTP. It includes the code you can follow, plus how to test it using Postman:

---

# Flask, Celery, Redis, and SMTP Integration

In this guide, we will integrate **Flask** with **Celery** and **Redis** for background task management, performance caching, and **SMTP** for email sending. We will also demonstrate how to set up basic APIs for sending emails, performing caching, and scheduling tasks.

## Prerequisites
1. Install required libraries:
   - `Flask`
   - `Flask-RESTful`
   - `Celery`
   - `redis`
   - `Flask-Mail`

```bash
pip install Flask Flask-RESTful Celery redis Flask-Mail
```

## Setup: Initialize Flask App

First, let’s initialize a basic Flask app:

```python
from flask import Flask
from flask_restful import Api, Resource
from flask_mail import Mail, Message
from celery import Celery
import redis

app = Flask(__name__)
api = Api(app)

# Configuration for Flask-Mail
app.config['MAIL_SERVER'] = 'smtp.gmail.com'
app.config['MAIL_PORT'] = 587
app.config['MAIL_USE_TLS'] = True
app.config['MAIL_USERNAME'] = 'your_email@gmail.com'
app.config['MAIL_PASSWORD'] = 'your_password'
mail = Mail(app)

# Configuration for Celery
app.config['broker_url'] = 'redis://localhost:6379/0'
app.config['result_backend'] = 'redis://localhost:6379/0'
celery = Celery(app.name, broker=app.config['broker_url'])
celery.conf.update(app.config)

# Initialize Redis Cache
cache = redis.StrictRedis(host='localhost', port=6379, db=0)

# =============================================================================
# Celery Initialization
# =============================================================================
def init_celery(flask_app):
    celery_app = Celery(
        flask_app.import_name,
        broker=flask_app.config['broker_url'],
        backend=flask_app.config['result_backend']
    )
    celery_app.conf.update(flask_app.config)
    class ContextTask(celery_app.Task):
        def __call__(self, *args, **kwargs):
            with flask_app.app_context():
                return super().__call__(*args, **kwargs)
    celery_app.Task = ContextTask
    return celery_app

celery = init_celery(app)
```

## Tasks

### 1. **Sending Emails with SMTP in Flask**

To send emails via SMTP using Flask-Mail, we'll create an API that allows sending a test email.

```python
class SendEmail(Resource):
    def get(self):
        msg = Message(
            subject="Test Email from Flask",
            recipients=["recipient@example.com"],
            body="This is a test email sent via Flask and SMTP."
        )
        try:
            mail.send(msg)
            return {'message': 'Email sent successfully!'}, 200
        except Exception as e:
            return {'message': f"Error: {str(e)}"}, 500

api.add_resource(SendEmail, '/send-email')
```

### 2. **Implementing Cache for Performance Improvement**

We will create a simple API to demonstrate caching with Redis.

```python
class CacheDemo(Resource):
    def get(self):
        # Check cache for existing data
        cached_data = cache.get('data')
        if cached_data:
            return {'data': cached_data.decode('utf-8')}, 200
        else:
            data = 'This is a cached response!'
            # Set cache for 60 seconds
            cache.set('data', data, ex=60)
            return {'data': data}, 200

api.add_resource(CacheDemo, '/cache')
```

We will also create an API to delete the cache:

```python
class DeleteCache(Resource):
    def post(self):
        cache.delete('data')
        return {'message': 'Cache deleted successfully!'}, 200

api.add_resource(DeleteCache, '/delete-cache')
```

### 3. **Queued Tasks with Celery**

Now, let’s create an API to queue a background task:

```python
@celery.task(name="tasks.background_task")
def background_task():
    # Simulate a background task
    return 'Task completed'

class QueuedTask(Resource):
    def get(self):
        task = background_task.apply_async()
        return {'task_id': task.id}, 202

api.add_resource(QueuedTask, '/queued-task')
```

### 4. **Scheduled Task with Celery**

This will schedule a task to run at a specific time (daily at 7:00 AM Kolkata time).

```python
from celery.schedules import crontab

# Set timezone to Kolkata, India
celery.conf.timezone = 'Asia/Kolkata'

# Celery Beat Configuration for Scheduled Task
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

```

### Final Flask App Setup

Make sure you add all the resources to the `Flask` app:

```python
api.add_resource(SendEmail, '/send-email')
api.add_resource(CacheDemo, '/cache')
api.add_resource(DeleteCache, '/delete-cache')
api.add_resource(QueuedTask, '/queued-task')
```

### Running the App

Run the Flask app by executing:

```bash
python app.py
```

### Running Celery

For Celery to work, you need to run the worker and the beat scheduler separately:

1. Start the Celery worker:

```bash
celery -A app.celery worker
```

2. Start the Celery beat scheduler:

```bash
celery -A app.celery beat
```

### Testing with Postman

1. **To Test Sending Email**:
   - Open Postman and send a **GET** request to: `http://localhost:5000/send-email`.
   - You should receive a response confirming the email was sent.

2. **To Test Caching**:
   - Send a **GET** request to `http://localhost:5000/cache` to see if the cache is returned.
   - Send a **POST** request to `http://localhost:5000/delete-cache` to delete the cache.

3. **To Test Queued Task**:
   - Send a **GET** request to `http://localhost:5000/queued-task` to trigger the background task.

4. **To Test Scheduled Task**:
   - Check the console/logs for the scheduled task output after it runs at 7:00 AM IST.

---

This Markdown file guides you through the setup and implementation of the tasks you mentioned, including code snippets and instructions on how to test using Postman. Let me know if you need further details!





🚀 Next: 

[Vue.js Advanced API Integration with Role-Based Access]({{ site.baseurl }}/posts/App-Dev-II-bootcamp-t4/)