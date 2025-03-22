# Docker notes

> [!CAUTION]
> This note is from **[Maximilian Schwarzmüller - Docker & Kubernetes](https://www.udemy.com/course/docker-kubernetes-the-practical-guide/)** course

## Images vs Containers

- **Container**: The running unit of software
- **Image**: The templates/blueprints for containers, Images contains code and runtime tools and then used to create multiple **containers**. 

The `image` is the shareable package with all the setup instructions and all the codes, and `container` will be the concrete running instance off such an image.

**We run containers, which are based on images**

![Docker images vs containers](./assets/images/docker-images-vs-containers.png)

## RUN vs CMD in Dockerfile

- **RUN**: `RUN` is a image command, it means that it will executed when we are build the image, and it will not executed when we want to create a container base on that particular image
- **CMD**: `CMD` is a Runtime command, and it will executed at creating a new container, or running the container.

> [!NOTE]
> The syntax of writting `RUN` command and `CMD` command has differences
> The commands for `CMD` should be in a brackets `[]` and isolated from them by double qoutes.

**Example:**

```dockerfile
FROM node:latest

WORKDIR /app

COPY . .

EXPOSE 3000

RUN npm install

CMD ["node", "server.js"]
```

---

### Docker image has layered structure

- [Source](https://www.linkedin.com/pulse/understanding-docker-layers-efficient-image-building-majid-sheikh/)

At its core, a Docker image is composed of a series of read-only layers stacked on top of each other. Each layer represents a set of file system changes, and every Dockerfile instruction adds a new layer to the image. These layers are cached by Docker, enabling quicker image builds and efficient use of resources.

#### How does docker know about changes in file?

Imagine that you copied project source code to image, but in development process you made a few changes and you want to apply them in your container/containers as well, But first of all if you don't bind the source code to the container, you have to re-build the image and then create containers from that new image.

But docker used layered structure for better performance in creating images, and it will notice about changes in your source code and then from the `COPY` instruction till the end, it won't use cached layers and it will build new ones. 

Docker will notice changes in source code by this:

##### How Docker Detects Changes

Docker uses the content of the files and the Dockerfile instructions to decide whether to use the cache or rebuild a layer.
For the `COPY` or `ADD` instructions, Docker checks the files being copied (e.g., your source code) to see if they have changed since the last build.

##### Key Points for `COPY` or `ADD`:

- Docker calculates a **checksum (hash)** for the files being copied.
- If the checksum of the files matches the checksum from the previous build, Docker uses the cached layer.
- If the checksum differs (e.g., because you modified your source code), Docker invalidates the cache and rebuilds the layer.

##### What is a Checksum?

A **checksum** is a value calculated from a set of data (e.g., a file or a group of files) using a mathematical algorithm. It acts like a `"fingerprint"` for the data. If the data changes, even slightly, the checksum will also change. This makes checksums useful for detecting changes in files.

In Docker, checksums are used to determine whether files (like your source code) have changed since the last build. If the checksum of a file changes, Docker knows it needs to rebuild the corresponding layer.

##### How is a Checksum Calculated?

Checksums are calculated using hashing algorithms. These algorithms take the input data (e.g., the contents of a file) and produce a fixed-length string of characters (the checksum). Common hashing algorithms include:

- MD5 (Message Digest Algorithm 5)
- SHA-1 (Secure Hash Algorithm 1)
- SHA-256 (part of the SHA-2 family)

##### Does Hashing Take Time?

`Yes`, hashing takes time, but **it’s usually very fast**. The time it takes depends on:

**The Size of the Data**: Larger files take longer to hash because the algorithm has to process more data.

**The Hashing Algorithm:** Some algorithms are faster than others. For example, `MD5` is faster than `SHA-256`, but `SHA-256` is **more secure**.

**Hardware Performance:** Faster CPUs and SSDs can speed up the hashing process.

##### In the context of Docker:

Docker only needs to hash the files that are being copied (e.g., your source code), not the entire image.
For most projects, the hashing process is negligible compared to the time it takes to build the image (e.g., installing dependencies or compiling code).

##### Best Practices

1. Place instructions that change frequently (e.g., COPY . /app) toward the end of the Dockerfile to maximize cache usage for earlier layers (e.g., installing dependencies).

###### Example of a regular Dockerfile

```dockerfile
FROM node:latest

WORKDIR /app

COPY . .

EXPOSE 3000

RUN npm install

CMD ["node", "server.js"]
```

###### Example of an optimized Dockerfile

```dockerfile
FROM node:latest

WORKDIR /app

COPY package.json /app

RUN npm install

COPY . .

EXPOSE 3000

CMD ["node", "server.js"]
```

2. Use `.dockerignore` to exclude unnecessary files from being copied, which can help avoid unnecessary cache invalidations.

---
