# Project 2 — Design Rationale

## 1. Base Image and Multi-Stage Build

This project uses a multi-stage Dockerfile to separate building the web application from serving its final output. The first stage uses Node.js 24 to install dependencies and build the application. The final stage uses nginx:alpine to serve the generated files.

Nginx Alpine is suitable for the final stage because it provides a lightweight web server without including the full Node.js build toolchain. Keeping the build and runtime stages separate reduces the contents of the final image and limits the software needed to serve the application. Docker's official documentation explains that multi-stage builds allow selected artifacts to be copied from one stage into another, leaving unnecessary build dependencies behind.

In my recorded Docker image comparison, the multi-stage image (demo:multi) was approximately 93.6 MB, while the single-stage image (demo:single) was approximately 1.65 GB. These measurements show a substantial reduction in image size for this build. Actual sizes can vary with image versions, dependencies, and build changes.

## 2. Separate Web and Cache Services

The Compose configuration separates the web server from the Redis cache. The web service is built from the project's Dockerfile, while the cache service uses the public redis:7-alpine image.

Keeping Redis separate means that the project can use an existing cache implementation instead of installing and maintaining it inside the web container. Each service has a distinct responsibility and can be configured or replaced independently.

The Compose configuration uses depends_on with a health check so the web service waits for Redis to become healthy during startup. However, this dependency does not guarantee that Redis remains available throughout the web service's lifetime. During testing, the web server continued returning HTTP 200 after Redis was stopped because the current static web application does not make runtime requests to Redis.

This design demonstrates the container service model: the developer manages how services are composed, while relying on a pre-built Redis image for the cache implementation.

## References

1. Docker, Multi-stage builds:
   https://docs.docker.com/build/building/multi-stage/

2. Docker, Control startup and shutdown order in Compose:
   https://docs.docker.com/compose/how-tos/startup-order/

3. Docker, Compose config reference:
   https://docs.docker.com/reference/cli/docker/compose/config/

4. AWS, AWS SAM CLI installation:
   https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/install-sam-cli.html
