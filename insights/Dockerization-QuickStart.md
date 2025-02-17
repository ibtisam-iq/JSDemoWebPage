# Dockerizing a Static Web Page

## Project Structure

```text
.
├── about.html
├── app.js
├── index.html
├── package.json
```

## Architecture

| Feature         | Classification                  |
|-----------------|---------------------------------|
| Architecture    | 1-tier (Single Page/Static Webpage) |
| Frontend        | Static HTML + JavaScript (No Frameworks) |
| Backend         | None                            |
| Server          | None (Only static files served) |

## Multi-Stage Dockerfile

### One Environment (Production)

```dockerfile
# Stage 1: Build HTML/JS/CSS if needed
FROM node:18 AS builder
WORKDIR /app
COPY . .
RUN npm install  
# npm run build  (only for building React Apps, and it's not a React App.)

# Stage 2: Serve the built files using Nginx
FROM nginx:alpine
COPY --from=builder /app /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

### With Multi Environment (Maybe not working, see `package.json`)

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

## Docker Compose File

```yaml
version: "3.8"
services:
  web:
    build:
      context: .
      target: ${BUILD_STAGE}
    ports:
      - "8080:80"
    restart: unless-stopped
```

## Commands to Run

### Build and Run for Development

```sh
docker build -t my-web-dev --build-arg ENV=development .
docker run -d -p 8080:80 --name web-dev my-web-dev
```

### Build and Run for Production

```sh
docker build -t my-web-prod --build-arg ENV=production .
docker run -d -p 80:80 --name web-prod my-web-prod
```

### Using Docker Compose

```sh
BUILD_STAGE=build docker-compose up -d  # Production
BUILD_STAGE=dev docker-compose up -d    # Development
```

## Reference

You can find in-depth information [here](Dockerization.md).

---

Here is a Dockerfile suitable for your **Node.js Express project**:

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
