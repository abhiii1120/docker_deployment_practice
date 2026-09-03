# Docker Deployment

## Overview

This project is a full-stack application consisting of a **React frontend** and a **Node.js/Express backend**.

Docker is used to containerize the application and make the deployment process consistent across different environments.

The project uses a **multi-stage Docker build**, where the frontend is built first and its production files are then included in the backend image.

### Technologies

* **React + Vite**
* **Node.js**
* **Express.js**
* **Docker**

---

## Project Architecture

The application uses a **single Docker container** for both the frontend and backend.

The React application is first compiled into a production build. The generated `dist` folder is then copied into the backend's `public` directory.

The Express server serves both:

* Backend API endpoints
* React frontend files

This allows the complete application to be accessed through a **single server and port**.

---

## Docker Build Process

The Dockerfile uses **multi-stage builds**.

### Stage 1 — Frontend Build

The first stage uses a Node.js Alpine image to install the frontend dependencies and create the production build.

The React application is compiled using the Vite build process.

The resulting production files are stored inside the `dist` directory.

### Stage 2 — Backend

The second stage creates the production environment for the Node.js backend.

Backend dependencies are installed and the backend source code is copied into the container.

The frontend production build from the first stage is then copied into the backend's `public` directory.

The final Docker image therefore contains everything required to run the complete application.

---

## Why Multi-Stage Docker Builds?

Multi-stage builds allow the frontend build environment and the final production environment to be separated.

The frontend requires development dependencies to create the production build, but those dependencies are not required when serving the already-built frontend.

This results in a **cleaner and more efficient production image**.

---

## Running the Application

After creating the Docker image, the application can be started as a Docker container.

The container exposes the backend port, and the React frontend is served through the Express server.

Once the container is running, the application can be accessed through the exposed port.

---

# Docker Image Creation and Deployment

## 1. Build the Docker Image

First, create a Docker image using the project's `Dockerfile`.

```bash
docker build -t kodex-fullstack .
```

This command builds the complete application image, including the frontend production build and backend.

### What happens?

```text
Frontend
   ↓
Install Dependencies
   ↓
Build React Application
   ↓
Generate dist/
   ↓
Build Backend Image
   ↓
Copy dist/ → public/
   ↓
Final Docker Image
```

---

## 2. Run the Docker Container Locally

After creating the Docker image, run it locally to verify that the application is working correctly.

```bash
docker run -p 3000:3000 kodex-fullstack
```

The application can then be accessed through:

```text
http://localhost:3000
```

Running the container locally allows us to test the application before deploying it to production.

---

## 3. Login to Docker Hub

Once the application has been tested successfully, log in to Docker Hub.

```bash
docker login
```

Enter your Docker Hub username and password when prompted.

After successful authentication, the Docker CLI can push images to your Docker Hub repositories.

---

## 4. Push the Docker Image to Docker Hub

After logging in, push the Docker image to the Docker Hub repository.

```bash
docker push abhii1120/kodex-fullstack
```

The Docker image is now available on Docker Hub and can be accessed by the deployment platform.

> **Note:** The local image must be tagged with the Docker Hub repository name before pushing if it was originally created with a different tag.

---

## 5. Deploy Using Render

After successfully pushing the Docker image to Docker Hub, go to **Render** and create a new service using the Docker image.

Provide the Docker Hub image:

```text
abhii1120/kodex-fullstack
```

Render pulls the Docker image from Docker Hub and uses it to create and run the application container.

Configure the required:

* **Environment variables**
* **Port**
* **Deployment settings**

Then start the deployment.

Once the deployment is successful, Render provides a **public URL** for the application.

---

# Deployment Flow

```text
                 Dockerfile
                     │
                     ▼
              docker build
                     │
                     ▼
               Docker Image
                     │
                     ▼
                docker run
                     │
                     ▼
              Test Locally
                     │
                     ▼
                docker login
                     │
                     ▼
                docker push
                     │
                     ▼
                 Docker Hub
                     │
                     ▼
                  Render
                     │
                     ▼
            Pull Docker Image
                     │
                     ▼
             Deploy Container
                     │
                     ▼
             Live Application 🚀
```

---

## Result

With this setup, the **frontend and backend are packaged together into a single Docker image**.

The frontend is served as a production build by Express, while the backend handles API requests and communicates with the database.

This makes the application **portable, consistent, and easy to deploy** across different environments.

The same Docker image that was tested locally can be used for production deployment on Render.
