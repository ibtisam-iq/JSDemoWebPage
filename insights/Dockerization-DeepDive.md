# Dockerizing a ReactJS Project

The project is a frontend-only ReactJS application with no backend or database integration, making it a 1-tier application. This means it handles the presentation layer (UI/UX) only, without connecting to a backend or database.

> **Note:** This is quite lenghty file, you can read its short version [here](dockerization.md).

Sure, here is the table of contents for the document:

## Table of Contents

1. Dockerizing a ReactJS Project
2. Project Structure
3. Project Breakdown
    - HTML Files (about.html, index.html)
    - JavaScript File (app.js)
    - package.json
4. Where Does This Project Fall?
5. Comparison to Other Projects
6. Conclusion
7. How to Extend This Static Web Project to a Dynamic Web Application
    - Option 1: Convert to a 2-Tier Web Application (Frontend + Backend)
        - New Folder Structure (2-Tier)
        - Key Changes
        - Code Changes
            - Backend (server.js)
            - Frontend (app.js)
    - Option 2: Convert to a 3-Tier Web Application (Frontend + Backend + Database)
        - New Folder Structure (3-Tier)
        - New Components
        - Code Changes
            - Connect to Database (database.js)
            - Define a User Model (models/User.js)
            - Backend Routes (routes/userRoutes.js)
            - Frontend API Call (app.js)
    - Final Comparison
    - Conclusion
8. Convert it into 3-Tier JavaScript Web Application
    - Backend Implementation
        - Changes I made
9. Dockerizing the 3-Tier JavaScript Web Application
    - Single-Stage Dockerfile (Basic)
        - Dockerfile (Single Build - Nginx)
    - Multi-Stage Dockerfile (Optimized)
        - Dockerfile (Multi-Stage - Node.js + Nginx)
    - Docker Compose (With MongoDB & Frontend)
        - Dockerfile for Frontend (React)
    - Which One Should You Use?
10. Single Dockerfile for Multiple Environments
    - Single Dockerfile for Multiple Environments
        - Dockerfile
    - Build & Run for Different Environments
        - Build for Development
        - Build for Production
    - Using Docker Compose
        - docker-compose.yml
    - Run for Different Environments
    - Summary
11. Multi-Stage Dockerfile for Different Environments
    - Multi-Stage Dockerfile for Different Environments
        - Dockerfile
    - Building and Running Different Environments
        - Run in Production Mode
        - Run in Development Mode
    - Docker Compose for Environment-Specific Builds
        - docker-compose.yml
    - Run for Different Environments
    - Summary
    - Multi-Stage Builds Benefits

## Project Structure

```text
.
├── about.html
├── app.js
├── index.html
├── package.json
```

### Project Breakdown

#### HTML Files (about.html, index.html)
- These are static HTML files that represent the frontend of the application.
- HTML is used to structure the content of the pages, such as text, images, forms, etc.

#### JavaScript File (app.js)
- This JavaScript file likely contains the frontend logic, handling events, interactions, or calling APIs to update the DOM dynamically.
- It is not using any advanced JavaScript framework (e.g., React, Vue) but rather pure JavaScript.

#### package.json
- This is a Node.js project descriptor file. It typically lists dependencies, scripts, and metadata for the project.
- Since there is no Node.js server file (like server.js or app.js handling HTTP requests), this suggests that the JavaScript might just be used for frontend functionalities (like enhancing the static HTML files), but the project does not have backend logic.

### Where Does This Project Fall?

| Feature         | Classification                  |
|-----------------|---------------------------------|
| Architecture    | 1-tier (Single Page/Static Webpage) |
| Frontend        | Static HTML + JavaScript (No Frameworks) |
| Backend         | None                            |
| Server          | None (Only static files served) |

This project does not have a backend or a full-stack architecture. It appears to be a static web page with basic interactivity powered by JavaScript.

### Comparison to Other Projects

| Project Type                | Backend           | Frontend       | Architecture |
|-----------------------------|-------------------|----------------|--------------|
| React + Node.js + MySQL     | Node.js (Express) | React          | 3-tier       |
| Flask + React + PostgreSQL  | Flask             | React          | 3-tier       |
| JSP + Tomcat                | Java Servlets     | JSP            | 2-tier       |
| JSDemoWebPage               | None              | HTML + JS      | 1-tier (Static Web) |


### Conclusion
- This is a 1-tier static web project with no backend.
- The JavaScript (likely for DOM manipulation or basic client-side logic) is enhancing the static HTML pages.
- If you were to make this a dynamic web application, you could introduce a backend (like Node.js with Express) and database to make it a 2-tier or 3-tier application.

---

## How to Extend This Static Web Project to a Dynamic Web Application

Since `JSDemoWebPage` is currently a static website with no backend or database, let’s explore how you can extend it into a 2-tier or 3-tier web application.

### Option 1: Convert to a 2-Tier Web Application (Frontend + Backend)

In a 2-tier architecture, we introduce a backend that processes logic and serves dynamic content.

#### New Folder Structure (2-Tier)

```
JSDemoWebPage/
├── backend/                  # Backend folder (New)
│   ├── server.js             # Express.js server (Handles API requests)
│   ├── routes/               # Route handlers
│   │   ├── api.js            # Example API routes
│   ├── package.json          # Dependencies for backend
│   ├── .env                  # Environment variables (e.g., PORT)
│   ├── controllers/          # Business logic
│   │   ├── userController.js
│   ├── models/               # Data models
│   │   ├── userModel.js
│   └── database.js           # Database connection file (Optional for now)
├── frontend/                 # Frontend folder (Existing files moved here)
│   ├── about.html
│   ├── index.html
│   ├── app.js
│   ├── package.json
│   ├── public/               # Static assets (CSS, images, etc.)
│   ├── styles.css
└── README.md
```

#### Key Changes
- `backend/` folder: Introduces Node.js with Express to handle API requests.
- Separation of Concerns: The backend now manages logic, while the frontend only handles UI.
- APIs: The frontend will fetch data from the backend using AJAX or fetch() calls.

#### Code Changes

##### Backend (server.js)

```javascript
const express = require('express');
const cors = require('cors');

const app = express();
app.use(cors()); // Allows frontend to communicate with backend
app.use(express.json());

app.get('/api/greet', (req, res) => {
    res.json({ message: "Hello from the backend!" });
});

const PORT = process.env.PORT || 5000;
app.listen(PORT, () => {
    console.log(`Server running on port ${PORT}`);
});
```

##### Frontend (app.js)

Modify it to fetch data dynamically from the backend:

```javascript
fetch('http://localhost:5000/api/greet')
    .then(response => response.json())
    .then(data => {
        document.getElementById('message').innerText = data.message;
    })
    .catch(error => console.error('Error fetching data:', error));
```

### Option 2: Convert to a 3-Tier Web Application (Frontend + Backend + Database)

In a 3-tier architecture, we introduce a database to store and retrieve data dynamically.

#### New Folder Structure (3-Tier)

```
JSDemoWebPage/
├── backend/
│   ├── server.js
│   ├── routes/
│   │   ├── userRoutes.js
│   ├── models/
│   │   ├── User.js
│   ├── database.js
│   ├── package.json
│   ├── .env
├── frontend/
│   ├── public/
│   │   ├── about.html
│   │   ├── index.html
│   ├── src/
│   │   ├── app.js
│   ├── styles.css
├── database/
│   ├── schema.sql            # SQL Schema (Tables and relations)
│   ├── seed.sql              # Sample data
├── README.md
```

#### New Components
- **Database Layer (database.js)**: Connects to a database (e.g., MySQL, MongoDB).
- **Models (models/User.js)**: Defines database schema.
- **Routes (routes/userRoutes.js)**: Handles user-related API requests.

#### Code Changes

##### 1. Connect to Database (database.js)

```javascript
const mongoose = require('mongoose');

const connectDB = async () => {
    try {
        await mongoose.connect(process.env.MONGO_URI, {
            useNewUrlParser: true,
            useUnifiedTopology: true,
        });
        console.log('MongoDB Connected');
    } catch (error) {
        console.error(error);
        process.exit(1);
    }
};

module.exports = connectDB;
```

##### 2. Define a User Model (models/User.js)

```javascript
const mongoose = require('mongoose');

const UserSchema = new mongoose.Schema({
    name: String,
    email: String,
});

module.exports = mongoose.model('User', UserSchema);
```

##### 3. Backend Routes (routes/userRoutes.js)

```javascript
const express = require('express');
const User = require('../models/User');

const router = express.Router();

router.post('/register', async (req, res) => {
    const { name, email } = req.body;
    const newUser = new User({ name, email });
    await newUser.save();
    res.json({ message: 'User registered successfully!' });
});

module.exports = router;
```

##### 4. Frontend API Call (app.js)

```javascript
fetch('http://localhost:5000/api/register', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ name: 'Ibtisam', email: 'ibtisam@example.com' })
})
.then(response => response.json())
.then(data => console.log(data))
.catch(error => console.error('Error:', error));
```

### Final Comparison

| Architecture                  | Features                                      |
|-------------------------------|-----------------------------------------------|
| 1-Tier (Static Web)           | No backend, only HTML + JS                    |
| 2-Tier (Frontend + Backend)   | Introduces Express.js backend, APIs           |
| 3-Tier (Frontend + Backend + Database) | Adds database for persistent storage |

### Conclusion
- Your current project is a 1-tier static web page.
- To make it dynamic, introduce Node.js (backend) → 2-tier.
- To add persistent data, introduce a database → 3-tier.

---
## Convert it into 3-Tier JavaScript Web Application

This project extends the **JSDemoWebPage** into a **3-tier web application**. It includes:

1. **Frontend**: HTML, CSS, and JavaScript (*Vanilla JS or React can be used*)
2. **Backend**: Node.js with Express.js for API handling
3. **Database**: MongoDB for persistent storage

### Backend Implementation

```javascript
// Import required modules
const express = require('express');
const mongoose = require('mongoose');
const cors = require('cors');
const dotenv = require('dotenv');

// Load environment variables
dotenv.config();

// Initialize Express app
const app = express();
app.use(cors());
app.use(express.json());

// Connect to MongoDB
mongoose.connect(process.env.MONGO_URI, {
    useNewUrlParser: true,
    useUnifiedTopology: true,
}).then(() => console.log('MongoDB Connected')).catch(err => console.error(err));

// Define a User Model
const User = mongoose.model('User', new mongoose.Schema({
    name: String,
    email: String,
}));

// API Routes
app.post('/api/register', async (req, res) => {
    const { name, email } = req.body;
    const newUser = new User({ name, email });
    await newUser.save();
    res.json({ message: 'User registered successfully!' });
});

app.get('/api/users', async (req, res) => {
    const users = await User.find();
    res.json(users);
});

// Start Server
const PORT = process.env.PORT || 5000;
app.listen(PORT, () => {
    console.log(`Server running on port ${PORT}`);
});
```

#### Changes I made

I've set up a **3-tier JavaScript-based web application** with a structured backend using **Node.js (Express.js) and MongoDB**. This includes:

1️⃣ **Frontend**: Can be **React** or **Vanilla JS**
2️⃣ **Backend**: **Express.js** handles API requests
3️⃣ **Database**: **MongoDB** for user data storage

---

## Dockerizing the 3-Tier JavaScript Web Application

### Single-Stage Dockerfile (Basic)

This is the simplest way to containerize the backend.

```dockerfile
# Use Node.js base image
FROM node:18

# Set working directory
WORKDIR /app

# Copy package files and install dependencies
COPY package.json package-lock.json ./
RUN npm install --only=production

# Copy application source code
COPY . .

# Expose application port
EXPOSE 3000

# Start the application
CMD ["node", "server.js"]
```

### Multi-Stage Dockerfile (Optimized)

Reduces image size by excluding unnecessary files.

```dockerfile
# Build stage
FROM node:18 AS builder
WORKDIR /app
COPY package.json package-lock.json ./
RUN npm install --only=production
COPY . .

# Runtime stage
FROM node:18-alpine
WORKDIR /app

# Copy only necessary files from builder stage
COPY --from=builder /app /app

# Expose port and run app
EXPOSE 3000
CMD ["node", "server.js"]
```

### Docker Compose (With MongoDB & Frontend)

If you have a React frontend, Node.js backend, and MongoDB database, use docker-compose:

```yaml
version: '3.8'

services:
  backend:
    build: ./backend
    ports:
      - "3000:3000"
    depends_on:
      - database
    environment:
      - MONGO_URI=mongodb://database:27017/mydb

  frontend:
    build: ./frontend
    ports:
      - "80:80"
    depends_on:
      - backend

  database:
    image: mongo:latest
    restart: always
    ports:
      - "27017:27017"
```

#### Dockerfile for Frontend (React)

```dockerfile
# Build stage
FROM node:18 AS builder
WORKDIR /app
COPY package.json package-lock.json ./
RUN npm install
COPY . .
RUN npm run build

# Serve stage using Nginx
FROM nginx:alpine
COPY --from=builder /app/build /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

---

## Dockerizing a Static Web Page

Since your project is a 1-tier static web page (HTML, CSS, JS), let's write Dockerfiles in multiple ways for different deployment scenarios.

### Single-Stage Build (Basic Static Server)

This is the simplest way to serve a static website using Nginx.

#### Dockerfile (Single Build - Nginx)

```dockerfile
# Use official Nginx image
FROM nginx:alpine

# Copy website files to Nginx public folder
COPY . /usr/share/nginx/html

# Expose port 80 for web traffic
EXPOSE 80

# Start Nginx
CMD ["nginx", "-g", "daemon off;"]
```

### Multi-Stage Build (Minimized Final Image)

This approach builds assets using Node.js (if you have dependencies like npm/yarn) and then serves them using Nginx.

#### Dockerfile (Multi-Stage - Node.js + Nginx)

```dockerfile
# Stage 1: Build HTML/JS/CSS if needed
FROM node:18 AS builder
WORKDIR /app
COPY . .
RUN npm install && npm run build  # Only needed if using a package manager

# Stage 2: Serve the built files using Nginx
FROM nginx:alpine
COPY --from=builder /app /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

### Docker Compose (With Nginx)

If you want to define everything using docker-compose.yml.

#### Dockerfile

(Same as Single Build - Nginx)

#### docker-compose.yml

```yaml
version: "3.8"
services:
  web:
    build: .
    ports:
      - "8080:80"
    restart: unless-stopped
```

### Multi-Environment Setup

If you want separate configurations for development, staging, and production.

#### Dockerfile

```dockerfile
FROM nginx:alpine
ARG ENV=production
COPY . /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

#### docker-compose.override.yml (For Development)

```yaml
version: "3.8"
services:
  web:
    build:
      context: .
      args:
        - ENV=development
    ports:
      - "8080:80"
    volumes:
      - .:/usr/share/nginx/html
```

### Which One Should You Use?

| Scenario                | Approach                       |
|-------------------------|--------------------------------|
| Quick deploy            | Single Build (Nginx)           |
| Optimized Image         | Multi-Stage (Node.js → Nginx)  |
| Local Development       | Docker Compose                 |
| Multi-Environment Setup | ARG + Compose Override         |

---

## Single Dockerfile for Multiple Environments

You can write a single Dockerfile that supports multiple environments by using build arguments (ARG) and environment variables (ENV). This allows you to build different images for different environments (e.g., development and production) using the same Dockerfile.

### Single Dockerfile for Multiple Environments

This Dockerfile serves the static site using Nginx and allows dynamic environment-based configurations.

#### Dockerfile

```dockerfile
# Use Nginx as the base image
FROM nginx:alpine

# Define an argument for the environment (default to production)
ARG ENV=production

# Set an environment variable based on the argument
ENV APP_ENV=$ENV

# Copy static files to Nginx's public directory
COPY . /usr/share/nginx/html

# Expose port 80 for web traffic
EXPOSE 80

# Command to run Nginx
CMD ["nginx", "-g", "daemon off;"]
```

### Build & Run for Different Environments

Now, you can create two different images for development and production using the same Dockerfile.

#### Build for Development

```sh
docker build -t my-web-dev --build-arg ENV=development .
docker run -d -p 8080:80 --name web-dev my-web-dev
```

- Image name: my-web-dev
- ENV: development
- Runs on port 8080

#### Build for Production

```sh
docker build -t my-web-prod --build-arg ENV=production .
docker run -d -p 80:80 --name web-prod my-web-prod
```

- Image name: my-web-prod
- ENV: production
- Runs on port 80

### Using Docker Compose

Instead of manually building and running images, use Docker Compose to handle environment differences.

#### docker-compose.yml

```yaml
version: "3.8"
services:
  web:
    build:
      context: .
      args:
        - ENV=${APP_ENV}
    ports:
      - "8080:80"
    restart: unless-stopped
```

### Run for Different Environments

```sh
APP_ENV=development docker-compose up -d
APP_ENV=production docker-compose up -d
```

### Summary

| Approach                | How It Works                                      |
|-------------------------|---------------------------------------------------|
| Single Dockerfile       | Uses ARG ENV to switch environments               |
| Manual Build            | docker build --build-arg ENV=development          |
| Docker Compose          | Uses ${APP_ENV} to set environment                |

With this approach, you can maintain one Dockerfile and build multiple images for different environments without modifying the file!

---

## Multi-Stage Dockerfile for Different Environments

You're referring to a multi-stage Docker build where the second stage extends from the first one. Here's how we can structure a single Dockerfile that builds different environments from the previous stage.

### Multi-Stage Dockerfile for Different Environments

This Dockerfile uses:

- Stage 1 (build) → Installs only production dependencies.
- Stage 2 (dev) → Extends from build and installs development dependencies.

#### Dockerfile

```dockerfile
# Stage 1: Build production-ready app
FROM node:alpine as build

# Define a build argument for port
ARG PORT=8000
ENV PORT=$PORT

# Set working directory
WORKDIR /app

# Copy all files into the container
COPY . .

# Install only production dependencies
RUN npm install --only=prod

# Expose the defined port
EXPOSE $PORT

# Command for production
CMD ["npm", "run", "start:prod"]

# -----

# Stage 2: Development environment (Extends from build)
FROM build as dev

# Install development dependencies
RUN npm install --only=dev

# Command for development
CMD ["npm", "start"]
```

### Building and Running Different Environments

Now, you can build and run the application using different stages.

#### Run in Production Mode

```sh
docker build -t my-app-prod --target build .
docker run -d -p 8000:8000 --name app-prod my-app-prod
```

- Uses first stage (build).
- Runs with production dependencies only.

#### Run in Development Mode

```sh
docker build -t my-app-dev --target dev .
docker run -d -p 8000:8000 --name app-dev my-app-dev
```

- Uses second stage (dev).
- Runs with development dependencies.

### Docker Compose for Environment-Specific Builds

Instead of manually building with --target, use Docker Compose.

#### docker-compose.yml

```yaml
version: "3.8"
services:
  app:
    build:
      context: .
      target: ${BUILD_STAGE}
    ports:
      - "8000:8000"
    restart: unless-stopped
```

### Run for Different Environments

```sh
BUILD_STAGE=build docker-compose up -d  # Production
BUILD_STAGE=dev docker-compose up -d    # Development
```
### Summary

| Approach             | Command                                      | Details                                      |
|----------------------|----------------------------------------------|----------------------------------------------|
| Production Build     | `docker build --target build .`             | Installs only production dependencies       |
| Development Build    | `docker build --target dev .`               | Installs development dependencies           |
| Docker Compose      | `BUILD_STAGE=dev docker-compose up -d`       | Automates environment switching             |

### 🚀 Multi-Stage Builds Benefits

✅ Use a **single Dockerfile**.
✅ Create **separate images** for production & development.
✅ Avoid **bloated images** with unnecessary dependencies.

---

## Understanding `npm run build`

```Dockerfile
FROM node:14-alpine AS build
ENV APP_HOME=/usr/src/app
WORKDIR $APP_HOME
COPY package.json ./
RUN npm install
COPY . .
RUN npm run build

FROM nginx:alpine
WORKDIR /usr/share/nginx/html
COPY --from=build /usr/src/app/dist .
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

```json
{
  "dependencies": {
    "express": "*"
  },
  "scripts": {
    "start": "node app.js"
  },
  "name": "basic-nodejs",
  "version": "1.0.0",
  "main": "app.js",
  "devDependencies": {},
  "author": "IbtisamX",
  "license": "MIT",
  "description": "NodeJS Project with Expres FrameWork and Running in Docker Container"
}
```
The provided `package.json` file indicates that this is a **Node.js project using Express**. The multi-stage build Dockerfile you provided earlier is specifically for a React application. For a Node.js project with Express, you would need a different Dockerfile.

Here is a Dockerfile suitable for your Node.js Express project:

```dockerfile
# Stage 1: Build the Node.js application
FROM node:14-alpine AS build

# Set environment variable for the application home directory
ENV APP_HOME=/usr/src/app

# Set the working directory inside the container
WORKDIR $APP_HOME

# Copy the package.json file to the working directory
COPY package.json ./

# Install the dependencies specified in package.json
RUN npm install

# Copy the rest of the application code to the working directory
COPY . .

# Stage 2: Create a lightweight image for running the application
FROM node:14-alpine

# Set the working directory inside the container
WORKDIR /usr/src/app

# Copy the built files from the previous stage to the working directory
COPY --from=build /usr/src/app .

# Expose port 3000 to allow traffic to the Node.js server
EXPOSE 3000

# Start the Node.js application
CMD ["npm", "start"]
```

### Explanation of Directives:

1. **Stage 1: Build the Node.js application**
   - `FROM node:14-alpine AS build`: Use the official Node.js image based on Alpine Linux for a lightweight build environment. The `AS build` syntax names this stage "build".
   - `ENV APP_HOME=/usr/src/app`: Set an environment variable for the application home directory.
   - `WORKDIR $APP_HOME`: Set the working directory inside the container to `$APP_HOME`.
   - `COPY package.json./`: Copy the package.json file to the working directory to install dependencies.
   - `RUN npm install`: Install the dependencies specified in package.json.
   - `COPY . .`: Copy the rest of the application code to the working directory.

2. **Stage 2: Create a lightweight image for running the application**
   - `FROM node:14-alpine`: Use the official Node.js image based on Alpine Linux for a lightweight runtime environment.
   - `WORKDIR /usr/src/app`: Set the working directory inside the container to the application home directory.
   - `COPY --from=build /usr/src/app .`: Copy the built files from the build stage to the working directory.
   - `EXPOSE 3000`: Expose port 3000 to allow traffic to the Node.js server.
   - `CMD ["npm", "start"]`: Start the Node.js application using the `npm start` script defined in package.json.

This Dockerfile will work for your Node.js Express project, creating a lightweight image that includes only the necessary files and dependencies to run the application.

---

Using Nginx to serve a Node.js application directly is not typical because Nginx is generally used as a reverse proxy or to serve static files, while Node.js handles dynamic content and application logic. However, you can use Nginx as a reverse proxy to forward requests to your Node.js application running in the background.

Here’s how you can set up a multi-stage Dockerfile to use Nginx as a reverse proxy for your Node.js application:

### Multi-Stage Dockerfile with Nginx as Reverse Proxy

```dockerfile
# Stage 1: Build the Node.js application
FROM node:14-alpine AS build

# Set environment variable for the application home directory
ENV APP_HOME=/usr/src/app

# Set the working directory inside the container
WORKDIR $APP_HOME

# Copy the package.json and package-lock.json files to the working directory
COPY package.json package-lock.json ./

# Install the dependencies specified in package.json
RUN npm install

# Copy the rest of the application code to the working directory
COPY . .

# Stage 2: Create a lightweight image for running the application with Nginx
FROM nginx:alpine

# Set the working directory inside the Nginx container
WORKDIR /usr/src/app

# Copy the built files from the previous stage to the working directory
COPY --from=build /usr/src/app /usr/src/app

# Copy the custom Nginx configuration file
COPY nginx.conf /etc/nginx/conf.d/default.conf

# Expose port 80 to allow traffic to the Nginx server
EXPOSE 80

# Start Nginx in the foreground
CMD ["nginx", "-g", "daemon off;"]
```

### Custom Nginx Configuration File (`nginx.conf`)

Create a file named `nginx.conf` in the same directory as your Dockerfile with the following content:

```nginx
server {
    listen 80;

    server_name localhost;

    location / {
        proxy_pass http://localhost:3000; # Use the Docker service name instead of localhost.
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }

    error_page 500 502 503 504 /50x.html;
    location = /50x.html {
        root /usr/share/nginx/html;
    }
}
```

### Explanation of Directives:

1. **Stage 1: Build the Node.js application**
   - `FROM node:14-alpine AS build`: Use the official Node.js image based on Alpine Linux for a lightweight build environment. The `AS build` syntax names this stage "build".
   - `ENV APP_HOME=/usr/src/app`: Set an environment variable for the application home directory.
   - `WORKDIR $APP_HOME`: Set the working directory inside the container to `$APP_HOME`.
   - `COPY package.json ./`: Copy the package.json file to the working directory to install dependencies.
   - `RUN npm install`: Install the dependencies specified in package.json.
   - `COPY . .`: Copy the rest of the application code to the working directory.

2. **Stage 2: Create a lightweight image for running the application with Nginx**
   - `FROM nginx:alpine`: Use the official Nginx image based on Alpine Linux for a lightweight web server.
   - `WORKDIR /usr/src/app`: Set the working directory inside the container to the application home directory.
   - `COPY --from=build /usr/src/app /usr/src/app`: Copy the built files from the build stage to the working directory.
   - `COPY nginx.conf /etc/nginx/conf.d/default.conf`: Copy a custom Nginx configuration file to the Nginx configuration directory.
   - `EXPOSE 80`: Expose port 80 to allow traffic to the Nginx server.
   - `CMD ["nginx", "-g", "daemon off;"]`: Start Nginx in the foreground to keep the container running.

### Building and Running the Docker Container

1. **Build the Docker image**:
   ```bash
   docker build -t node-nginx-app .
   ```

2. **Run the Docker container**:
   ```bash
   docker run -p 80:80 node-nginx-app
   ```

Your Node.js application should now be accessible at `http://localhost`, with Nginx acting as a reverse proxy to forward requests to the Node.js server running on port 3000 inside the container.

---

## Difference Between CMD ["node", "app.js"] and CMD ["npm", "start"] in Docker

### **1. CMD ["node", "app.js"]**
- Directly runs the **Node.js runtime** with `app.js`.
- Assumes `app.js` is the main entry point of your application.
- **Bypasses `npm` scripts** and runs the application without using `package.json`.

### **2. CMD ["npm", "start"]**
- Runs the command defined under the `"start"` script in `package.json`.
- Example of `package.json`:
  
  ```json
  {
    "scripts": {
      "start": "node app.js"
    }
  }
  ```
  
- Allows flexibility to run pre-start scripts or additional environment configurations.

### **Key Differences**
| Aspect                  | CMD ["node", "app.js"] | CMD ["npm", "start"] |
|-------------------------|-------------------------|---------------------|
| Execution Method       | Directly runs Node.js   | Runs via `npm`     |
| Dependency on package.json | No                     | Yes                 |
| Flexibility            | Less flexible, runs only `app.js` | More flexible, allows pre-start scripts |
| Best for               | Production environments | Development, setups with additional scripts |

### **When to Use Which?**
- **Use `CMD ["node", "app.js"]`** when you want to directly run the Node.js application without dependency on `npm` scripts.
- **Use `CMD ["npm", "start"]`** when your project relies on environment variables, additional setup, or other `npm` lifecycle scripts.

In most **production** cases, `CMD ["node", "app.js"]` is preferred for a lightweight and direct execution, while `CMD ["npm", "start"]` is useful for development and projects with additional script requirements.

