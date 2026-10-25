# Project 2 – Technical Rationale

## 1. Base Image and Multi-Stage Build

For this project, I chose `nginx:alpine` as the final Docker image because the application is a simple static website. Node.js is needed to build the application, but it is not required to serve the finished files.

The Dockerfile uses two stages. The first stage uses Node.js to install dependencies and build the application. The second stage uses Nginx to serve the generated files. This keeps the build tools and development dependencies out of the final image.

I chose the Alpine-based Nginx image because it is lightweight and provides what this project needs without a larger operating system environment. Other images could also work, but there is no reason to include unnecessary tools in a container that only serves static files.

In T02, I measured the following image sizes:

- **Single-stage image (`demo:single`): 2.16 GB**
- **Multi-stage image (`demo:multi`): 93.6 MB**
- **Size reduction: approximately 95.7%**

The multi-stage image was about 23 times smaller. This demonstrates the benefit of separating the build environment from the runtime environment. The reduction is mainly due to excluding the Node.js build environment and other build-time files, not simply because Alpine was selected.

## 2. Service Split: Web and Redis

In `compose.yaml`, I used two separate services: `web` and `cache`. The web service runs Nginx, while the cache service uses the public `redis:7-alpine` image.

I kept them separate because they have different responsibilities. Nginx serves the website, while Redis provides a caching service. Keeping them in separate containers makes them easier to manage, restart, or replace independently. It also allows me to use an existing Redis image instead of installing and configuring Redis inside the web container.

Docker Compose uses `depends_on` and a health check to manage startup order. This helps ensure that Redis is healthy before the web service starts. However, the static website does not actually use Redis for its requests. During T05, the web service continued returning HTTP 200 even after Redis was stopped.

This design connects to Week 3's discussion of IaaS and PaaS. With containers, I manage the Dockerfile, service configuration, and how the containers run together. At the same time, I can use pre-built images such as Redis instead of developing every component myself. Containers therefore provide flexibility in deciding which parts of the environment I manage and which dependencies I reuse.

Overall, this project helped me understand why multi-stage builds are useful and how Docker Compose can organize services into a repeatable local environment.

## References

1. Docker. *Multi-stage builds.* https://docs.docker.com/build/building/multi-stage/
2. Docker Hub. *Nginx Official Image.* https://hub.docker.com/_/nginx
3. Docker. *Control startup and shutdown order in Compose.* https://docs.docker.com/compose/how-tos/startup-order/
4. Docker Hub. *Redis Official Image.* https://hub.docker.com/_/redis
