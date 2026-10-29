# Project 2: Containerize and Run Locally

## Prerequisites
- Docker with Docker Compose
- AWS SAM CLI
- WSL 2 with Ubuntu on Windows

## 1. Build and start the application
From the repository root, run:

    docker compose up -d --build

## 2. Verify the services
Check service status:

    docker compose ps

Test the web server:

    curl -s -o /dev/null -w "%{http_code}\n" http://localhost:8080

Expected result: 200

Test Redis:

    docker compose exec cache redis-cli ping

Expected result: PONG

## 3. Invoke the local Lambda function
Run:

    cd local-fn
    sam local invoke HelloWorldFunction

Expected result: statusCode 200 and message hello world.

## 4. Stop the services
Return to the project root and stop the containers:

    cd ..
    docker compose down

## Architecture
The web service uses a multi-stage Dockerfile to build the application and serve its output using Nginx. Redis runs separately as a cache service with a health check. The local-fn directory contains an AWS SAM Hello World function that can be invoked locally without an AWS account.
