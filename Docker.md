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

> [!IMPORTANT]
> For every docker command, you can call them using `--help` to see the list of options that command will take
> ```sh
> docker ps --help
> ```

### Understand Docker `attached` and `detached` mode

`Attached` mode is a mode that is default when you run a new container from an image using `docker run` command. In `Attached` mode you'll see the logs of the container because it blocks your terminal with running session.

`Detached` mode on the other hand is running in the background. It means that by running in `detached` mode your session doesn't block your active and current terminal and it will run container in background. When you start an existing container using `docker start` command, by default it will run in `detached` mode.

> [!NOTE]
> You can also run a new container from an image in `detach` mode by adding extra `-d` flag into `docker run` command:
> ```sh
> docker run -d my_back_end_image:1.0.0
> ```



### Docker commands

#### docker start <container_name> | <container_id>

This command will **start** `exited` and `existed` container.

##### Flags

- `-a`: Start container in `attach` mode,  **Attach STDOUT/STDERR and forward signals**
- `-i`: Attach container's STDIN

> [!IMPORTANT]
> If you have a container that you want to start it and have interact with it using terminal, you can start container by `docker start -ai` flag.

For example, Imagine that you have this code:

```python
name = input("Enter your name:")
print(f"Hello {name}")
```

Imagine that you have built your python image, and then you want to run it, You can run it using:

```sh
docker run -it hello-python-code:1.0.0
```

After done, you can re start that container by:

```sh
docker start -ai hello-python-code:1.0.0
```

#### docker attach <container_name> | <container_id>

This command will `attach` terminal to running container, by doing this you can see the container logs in your active terminal, but your terminal will block by running session.

#### docker logs <container_name> | <container_id>

Fetch the logs of a container.

##### Flags

- `-f`: Follow the container logs, and attach the terminal to the logs outputted by container

#### docker run <container_name> | <container_id>

Create and run a new container from an image

##### Flags

- `-d` | `--detach`: Run container in background and print container ID
- `-i` | `--interactive` : Keep STDIN open even it not attached
- `-t` | `--tty`: Allocate a pseudo-TTY `Means that it will allocate a pseudo terminal to the container for us`
- `-it`: You can simply run a container in attach mode and interact with container for example `STDIN`
- `-p` | `--publish`: Publish a container's port(s) to the host
- `--restart`: Restart policy to apply when a container exits (default "no"), Example: `docker run --restart=always busybox:latest`
- `--rm`: Automatically remove the container and its associated anonymous volumes when it exits
- `--name`: Assign a name to the container
 
#### docker rm  <container_name> | <container_id>

Remove one or more containers. You can pass container names or ids separated by space, to remove them all

##### Flags

- `-f` | `--force`: Force the removal of a running container

> [!NOTE]
> For removing all stopped container you can run `docker container prune`
> It will `Remove all stopped containers`

#### docker container prune

Remove all stopped containers

##### Flags

- `-f` | `--force`: Do not prompt for confirmation

#### docker ps 

List containers

**Aliases:**

```
docker container ls, docker container list, docker container ps, docker ps
```

##### Flags 
- `-a` | `--all`: Show all containers (default shows just running)
- `-q` | `--quiet`: Only display container IDs

> [!NOTE]
> A trick for running all stopped containers is to run:
> ```sh
> docker rm $(docker ps -aq)
> ```
> And for remove all containers, including running ones:
> ```sh
> docker rm -f $(docker ps -aq)
> ```


#### docker rmi <image_name> | <image_id>

Remove one or more images

**Aliases:**

```
docker image rm, docker image remove, docker rmi
```

##### Flags 
- `-f` | `--force`: Force removal of the image

> [!CAUTION]
> When running `docker rmi`, it only remove images that are not using by any container, even that container is `stopped`. For removing an image even if is attached to a existing container, you must run `docker rmi -f`, **using force flag!**.

#### docker image prune

Remove unused images

##### Flags 
- `-a` | `--all`: Remove all unused images, not just dangling ones
- `-f` | `--force`: Do not prompt for confirmation

---

### What are dangling images in docker?

Dangling images in Docker are unused or unreferenced image layers that are no longer associated with any tagged image. They occur when you build, pull, or update Docker images, and the old layers are left behind without being cleaned up. These images are not directly useful and can take up disk space.

### How Dangling Images Are Created:
1. **Rebuilding an Image**: When you rebuild a Docker image with the same tag, the old image layers become untagged and are left as dangling images.
2. **Pulling Updated Images**: If you pull a newer version of an image with the same tag, the previous version becomes dangling.
3. **Intermediate Layers**: During the build process, intermediate layers may be created and left behind if they are not part of the final image.

### Identifying Dangling Images:
You can list dangling images using the following Docker command:
```bash
docker images -f "dangling=true"
```
This will show all images that are untagged and not associated with any container.

### Cleaning Up Dangling Images:
To remove dangling images and free up disk space, you can use:
```bash
docker image prune
```

This command removes all dangling images. If you want to remove unused images (not just dangling ones), you can use:

```bash
docker image prune -a
```

Be cautious with `-a`, as it removes all images not associated with a container, including potentially useful ones.

### Preventing Dangling Images:
- Use unique tags for different versions of your images.
- Regularly clean up unused images using `docker image prune`.
- Use multi-stage builds to minimize intermediate layers.

**By managing dangling images, you can keep your Docker environment clean and efficient.**

---

#### docker image inspect <image_name> | <image_id>

Display detailed information on one or more images
By running this command, you can see a detailed information about that image including `layers`, `entrypoint`, `exposes ports`, ...

#### docker cp 

Copy files/folders between a container and the local filesystem.
By using this command, you can copy files/folders from your machine to a container or copy something from container to your local filesystem.

**Aliases:**

```
docker container cp, docker cp
```

##### Example 1: copy from local filesystem to container

This command will copy everything under the `src/` directory due to `.`, and paste it in my container with name: `my_container_name` and in the `/app` directory.

```sh
docker cp src/. my_container_name:/app
```

##### Example 1: copy from container to local filesystem

This command will copy single file `error_logs.log` from my container, to my `src` directory which is located in my local machine.

```sh
docker cp my_container_name:/app/error_logs.log src/
```

### Sharing Docker Images

> [!TIP]
> For sharing dockerized application, we don't share containers at all, but instead, we share images
> But for sharing images there is 2 way:
> 1. Share raw Dockerfile
> 2. Share built image

1. Share raw Dockerfile:

By sharing a raw Dockerfile, we also should contain our dependencies for building image to share with Dockerfile, like source code.

2. Share built image:

For sharing a built image, we just need to access the built image and no dependencies are required, because they are already existed in built image.
To share a built image there is 2 way:

- Sharing with DockerHub (Public registery)
- Sharing with Private registeries

#### docker push <image_name>

Upload an image to a registry

#### docker pull <image_name>

Download an image from a registry

> [!WARNING]
> If you want to push or pull from a private registery, you have to include the private registery url before the image name
> By default, if you don't modify tag for a image to pull, it download the `latest` image tag **by default**.

---
