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
RUN npm install && npm run build  # Only needed if using a package manager

# Stage 2: Serve the built files using Nginx
FROM nginx:alpine
COPY --from=builder /app /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

### With Multi Environment

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
