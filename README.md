# Project 2 – Containerize and Run Locally

This project demonstrates Docker multi-stage builds, Docker Compose, and local AWS Lambda testing with AWS SAM CLI.

## Requirements

- Docker Engine or Docker Desktop with Docker Compose
- AWS SAM CLI
- Git

Docker must be running before executing the commands below. No AWS account is required.

## 1. Build the Docker Image

From the repository root:

```bash
docker build -t demo:multi .
```

The Dockerfile uses Node.js to build the application and Nginx to serve the final static files.

## 2. Start the Services

```bash
docker compose up -d
docker compose ps
```

Docker Compose starts two services:

- `web`: Nginx serving the application
- `cache`: Redis with a health check

## 3. Verify the Services

Check the web service:

```bash
curl -I http://localhost:8080
```

Expected HTTP status: `200 OK`.

Check Redis:

```bash
docker compose exec cache redis-cli ping
```

Expected output:

```text
PONG
```

## 4. Stop the Services

```bash
docker compose down
```

## 5. Invoke the Local Lambda Function

```bash
cd local-fn
sam local invoke HelloWorldFunction
```

The function runs locally using Docker. Check the output for the expected response, including `statusCode: 200`.

## 6. Project Structure

```text
t02app/
├── Dockerfile
├── Dockerfile.single
├── compose.yaml
├── .dockerignore
├── package.json
├── package-lock.json
├── README.md
├── RATIONALE.md
└── local-fn/
```

The `local-fn` directory contains the AWS SAM template and Lambda handler.

## Notes

All commands are intended to run locally. Cloud deployment and AWS credentials are not required for Project 2.
