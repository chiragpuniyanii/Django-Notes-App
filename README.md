# Django Notes App

A **Django Notes App** designed for efficiently managing your notes. This app uses Django as the backend framework, containerized with Docker, and configured for deployment on AWS EC2.

---
## Features

- Create, read, update, and delete (CRUD) notes.
- User authentication and secure access.
- SQLite database integration for simplicity.
- Dockerized application for cross-platform compatibility.
- Production-ready with `nginx` as a reverse proxy.
- Deployment on AWS EC2 using Docker Compose.

---
## Project Overview

This project includes the following components:

1. **Dockerfile**:
   - Builds the Django app image.
   - Includes all dependencies and configurations to run the app.

2. **docker-compose.yml**:
   - Orchestrates multiple services:
     - Django application container.
     - Nginx container for reverse proxy.
   - Ensures the app is networked and production-ready.

3. **nginx Configuration**:
   - Serves as a reverse proxy to forward requests from port `80` to the Django app's port `8000`.

4. **AWS EC2 Deployment**:
   - Hosts the app for external access using Docker containers.

---

## Prerequisites

Ensure the following tools are installed:

- **AWS EC2 Instance** (Ubuntu 20.04 or later)
- **Docker** and **Docker Compose**
- **Git**
- **Python 3.8 or above**

---

## Getting Started

## How to Deploy the Application
### 1. Clone the Repository
SSH into your AWS EC2 instance and run:

```bash
git clone https://github.com/chiragpuniyanii/Django-Notes-App.git
cd Django-Notes-App
```

### 2. Understand the Dockerfile
The Dockerfile contains instructions to build the image for the Django app.

**Dockerfile Explanation**
```dockerfile
# Use the official Python 3.9 image as base
FROM python:3.9

# Set the working directory inside the container
WORKDIR /app/backend

# Copy the Python dependencies file
COPY requirements.txt .

# Install required Python dependencies
RUN pip install --no-cache-dir -r requirements.txt

# Copy the entire project into the container
COPY . .

# Expose the port the Django app runs on
EXPOSE 8000

# Run Django commands (migration and server)
CMD ["gunicorn", "--bind", "0.0.0.0:8000", "notesapp.wsgi:application"]
```

**Key Points:**
  - Base Image: Python 3.9 is used to ensure a consistent environment.
  - Dependencies: requirements.txt includes all necessary libraries (e.g., Django, Gunicorn).
  - Gunicorn: A WSGI server for production environments.
  - Expose: Opens port 8000 for the application.

### 3. Build the Docker Image
Run the following command to build the Docker image for the app:

```bash

docker build -t django-notes-app .
```
This command creates a Docker image named django-notes-app.

### 4. Understand docker-compose.yml
The docker-compose.yml orchestrates the deployment of multiple services.


**Key Points:**
  - django_app:

    - Builds the image using the Dockerfile.
    - Runs the Django app on port 8000.
    - Uses environment variables from .env.
  - nginx:

    - Acts as a reverse proxy.
    - Maps port 80 (public) to the app's port 8000.
    - Depends on the django_app service.
  - Networks:

    - Both containers are part of a custom bridge network notes-app-nw.

### 5. Run Docker Compose
Start the application with Docker Compose:

```bash

docker-compose up -d
```
This command:

  - Builds the Docker image for django_app.
  - Starts the Django and nginx services in the background.

### 6. Verify Deployment
  1) Visit http://<EC2-Public-IP> in your browser.
  2) The application should load successfully.
   ![Screenshot 2024-12-25 174204](https://github.com/user-attachments/assets/ec4326b4-286d-4df1-b0f9-aa9e3bd8c004)

     
### 7. Stop the Application
To stop the running containers:

```bash

docker-compose down
```

### Troubleshooting
**Common Issues**
1) Docker Build Fails:
   - Ensure requirements.txt and project files are in the correct location.
2) App Not Accessible:
   - Check AWS security group settings. Ensure ports 22, 80, and 8000 are open.
3) Environment Variable Issues:
   - Verify that the .env file is present and correctly configured.
  
### Project Structure
Here’s how your project directory should look:
```plaintext

django-notes-app-main/

│
├── .env               # Environment variables
├── Dockerfile         # Docker configuration for Django app
├── docker-compose.yml # Orchestration file
├── nginx/             # Nginx reverse proxy configuration
├── db.sqlite3         # SQLite database
├── requirements.txt   # Python dependencies
├── manage.py          # Django management script
├── notesapp/          # Django app directory
└── README.md          # Project documentation
```


### AWS EC2 Deployment Notes
  - Ensure that your EC2 instance has Docker and Docker Compose installed.
  - Update your EC2 instance’s security groups to allow traffic on ports 80 and 8000.
  - If using a public IP, make sure the ALLOWED_HOSTS in .env includes the EC2 public IP.
  
