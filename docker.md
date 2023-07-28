# Docker
![Docker logo](https://i.imgur.com/p3Rf0J8.png)

## What is Docker?
- Open platform for developing, shipping, and running applications
- It enables you to separate your applications from your infrastructure so you can deliver software quickly
- Can automate the deployment of applications as portable, self-sufficient containers that can run on the cloud or on-premises

## What are Docker containers?
Standardised, executable components that combine application source code with the operating system (OS) libraries and dependencies required to run that code in any environment3.

## Installation
Source: <https://github.com/khanmaster/docker_setup>

Run `docker --version` to make sure it's installed.

![docker --version](https://i.imgur.com/I95TrkN.png)

Make sure the **Docker Desktop** app is downloaded and Docker is running.

## How does a Docker API work?
Docker provides a REST API that allows you to interact with the Docker daemon.

The Docker Engine API is accessed by sending HTTP requests to the Docker daemon.

The API uses standard HTTP verbs such as GET, POST, PUT, and DELETE to perform operations on resources such as containers, images, and networks.

## Pulling an Image
![Docker Architecture](https://i.imgur.com/vZZ6XQP.png)

Use `docker` to see a list of useful docker commands.

`docker images` lists the Docker images.

Entering `docker run hello-world` will allow the Docker daemon to get the image from the registry and run it in the local machine.

![Hello World](https://i.imgur.com/Hdq7HAg.png)

Using `docker ps` lists the containers that are running.

`docker ps -a` lists all containers.

![docker ps](https://i.imgur.com/xoweAYw.png)


## Running Nginx

Create a repo in **Docker Hub**.

We want to create a container with the Nginx image. Port 80 will need to be mapped with the localhost.

```
docker run -d -p 80:80 nginx
```

Going to `localhost` in the browser will take you to the default nginx page.

This instance will be running in the background, so to stop it run:

```
docker stop <container id>
```

To start it up again, run `docker start <container id>`.

## Interacting with the container
This command allows you to ssh into the container.
```
docker exec -it <container id> sh
```
The container needs to have sudo and nano installed, so do `apt update`, `apt install sudo` and `apt install nano`.

The container is hosting the nginx image. To locate the html file for the nginx home page, follow the path `/usr/share/nginx/html`.

Use `nano index.html` to modify the file.

Once the file has been modified, when refreshing the browser, the modifications should be displayed.

## Building my own image and container

Nginx images are immutable.

To make a change, I would need to save that change, commit, and rebuild an image with the new version.

## Updating to my Docker repo
Once the changes are saved, the container needs to be committed to the Docker Hub repo.

```
docker commit <container_id> <docker_id>/<docker_repo>
```

![Commit & Push](https://i.imgur.com/GcZWuwT.png)


As port 80 is already in use, I can use the `docker run` command at a different port.

```
docker run -d -p 100:80 ddoxton/tech241-nginx
```
The amended Nginx page is now at `localhost:100`.

## Automation

We can automate the above my creating a script - specifically a **Dockerfile**.

We want to be able to make index.html available in out localhost.

Use `mkdir` to create a docker directory. 

Create index.html in the directory and write whatever in html. 

![Imgur](https://i.imgur.com/UxB1Um5.png)

Create a Dockerfile using `nano Dockerfile`.

```
# Select the base image
FROM nginx
# Label
LABEL MAINTAINER=deanne:sparta
# Copy indx.html from localhost to default nginx index.htmml location
COPY index.html /usr/share/nginx/html
# Port mapping : to the required port
EXPOSE 80
# Command to launch the web server (found in official documentation)
CMD ["nginx", "-g", "daemon off;"]
```

To build it, run:

```
docker build -t ddoxton/tech241-nginx:v1 .
```
**Don't forget the . at the end**

The image is built!

![Imgur](https://i.imgur.com/1MERd10.png)

Now run.

```
docker run -d -p 97:80 ddoxton/tech241-nginx:v1
```

![Imgur](https://i.imgur.com/vyrd0uD.png)

Then commit and push this to the repo.

## Containerising the app

Copy the app folder from github:

```
git clone https://github.com/ddockers/tech241-sparta-app.git repo
```

`cd` into the app folder and do `nano Dockerfile`.

```
# select base image
FROM node:12

# Set the working directory in the Docker image
WORKDIR /app/

# Copy the package.json and package-lock.json files into the Docker image
COPY package*.json ./

# Install the application dependencies inside the Docker image
RUN npm install

# Copy the rest of the application into the Docker image
COPY . .

# Expose port 3000 to have it mapped by Docker daemon
EXPOSE 3000

# The command to run when the container starts
CMD [ "node", "app.js" ]
```
Build.
```
docker build -t ddoxton/tech241-node-app .
```
The app should now install.

Once it's done, run!

```
docker run -d -p 3000:3000 ddoxton/tech241-node-app
```
The app is now running on port 3000!