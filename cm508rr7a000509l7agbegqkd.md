---
title: "How to Dockerize a Go Application with Multi-Stage Builds | Smaller Than 30MB"
seoTitle: "How to Dockerize Go App with Multi-Stage Builds"
seoDescription: "Learn how to dockerize a Go application using multi-stage builds for efficient, secure, and smaller images under 30MB"
datePublished: Sun Dec 22 2024 23:30:53 GMT+0000 (Coordinated Universal Time)
cuid: cm508rr7a000509l7agbegqkd
slug: how-to-dockerize-a-go-application-with-multi-stage-builds-smaller-than-30mb
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1734009249844/2909b6e1-61c0-4685-91a3-52bbeb5f0164.png
tags: docker, go, github, golang, docker-images

---

If you’re containerizing your Go app and losing sleep over the image size even for a simple project. Then you’re probably overlooking Docker’s multi-stage builds feature. This technique lets you separate the build environment from the production environment. In practice, you can use a larger GoLang image (which is around 800MB) for building your application, and then copy only the compiled binary and any necessary dependencies into a much smaller Alpine image (typically under 8MB). This not only reduces the final image size but also improves deployment speed and minimizes security vulnerabilities.

In this guide, I’ll walk you through the process of dockerizing **GitBack**, a handy command-line tool for backing up your GitHub repositories, gists, and wikis. First things first: clone the project from [`https://github.com/flarexes/gitback.git`](https://github.com/flarexes/gitback.git). Once that’s done, make sure to get rid of any existing `Dockerfile`—we’re going to create a fresh one from scratch.

## Prerequisites

* Docker Installed
    
* Familiarity with Docker
    

## First Stage: Build the GoLang Project

We’ll start by creating a `Dockerfile` under the root directory of the project. This section will define the steps necessary to build your Go application inside the container.

```dockerfile
# Use the official Go image as a base with the required version
FROM golang:1.23 AS builder

# Set the working directory inside the container
WORKDIR /app

# Copy the entire project into the working directory
COPY . .

# Download dependencies
RUN go mod download

# Build the Go application
RUN CGO_ENABLED=0 GOOS=linux go build -o gitback main.go
```

* **AS builder**: Naming this build stage as "builder" so Docker knows where to look when we want to refer to it later.
    
* **CGO\_ENABLED=0**: Disables the use of C libraries, resulting in a statically linked binary that’s portable and self-contained.
    
* **GOOS=linux**: Instruct the Go compiler to generate a binary that is compatible with Linux.
    

## Second Stage: Ship the Compiled Binary

After we've built the Go binary, the next step is to create a efficient base image that will run the compiled binary. We’ll keep it simple by copying only what we need from `builder` stage.

```dockerfile
# Start a new stage from scratch
FROM alpine:3.17

# Install git, a dependency for GitBack to clone resources
RUN apk add --no-cache git

# Set the working directory for the final image
WORKDIR /root/

# Copy the binary from the builder stage
COPY --from=builder /app/gitback .

# Command to run the executable
ENTRYPOINT ["/root/gitback"]
```

* **COPY --from=builder**: This is where the magic happens. We pull the compiled binary from the previous "builder" stage and drop it into our Alpine image. This is where we achieve the serious size reduction—shrinking the image by up to 100 times!
    

## Final Stage: Combine First & Second Stage to Build the Final Production Image

```dockerfile
# Use the official Go image as a base for building the Go application
FROM golang:1.23 AS builder

# Set the working directory inside the container
WORKDIR /app

# Copy only the Go modules to leverage caching in Docker
COPY go.mod go.sum ./

# Download dependencies
RUN go mod download

# Copy the rest of the project
COPY . .

# Build the Go application for a Linux OS with no CGo dependency
RUN CGO_ENABLED=0 GOOS=linux go build -o gitback main.go

# Start a new stage from scratch to reduce image size
FROM alpine:3.17

# Install git and other required dependencies
RUN apk add --no-cache git

# Set the working directory for the final image
WORKDIR /root/

# Copy the Go binary from the builder stage
COPY --from=builder /app/gitback .

# Define the entry point to run the binary
ENTRYPOINT ["/root/gitback"]
```

### Build the Image

To create the Docker image, run:

```bash
sudo docker build -t gitback:latest .
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1732453041400/ac45c40c-12d0-40f0-a872-4ad7d74efb2c.png align="left")

As you can see in the above picture, the final `gitback` image is significantly smaller than the original Go image. If you haven't pulled the `golang:1.23` image explicitly, you won't see it listed in the images after the build. Instead, you'll only see the `gitback:latest` image as the final result.

To verify that everything is functioning correctly, you can run:

```bash
sudo docker run --rm -it gitback:latest --help
```

This should display the help output for GitBack, confirming that the setup was successful.

# Visual Walkthrough of the Process

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1732453028520/970d9de0-2818-4922-a5f6-d299dedc6880.gif align="left")