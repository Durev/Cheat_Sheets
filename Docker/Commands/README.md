# Docker & Docker Compose

<img src="images/docker.png" width="456">

## Docker

### Build an image

Before running a container, Docker needs to build its image, by downloading all the files that will be needed to run the container. There are 2 cases :

 ##### 1. Use an existing image

You can run directly a container from an existing image in an repository on [DockerHub](https://hub.docker.com/explore/) (or locally), without adding anything. No need for a Dockerfile. If you don't have the image locally, it will first be pulled before creating the container. Just run `docker run image-name` to execute the Docker image.
(btw `docker run` = `docker create` + `docker start`)

###### *Options :*
- *-ti* : Interactive mode : Container stays open in terminal, showing its logs
- *-d* : The container runs in the background but doesn't stay open in the terminal. Don't forget to close it
- *-p machineport:containerport* : Link a container port to a machine port
*ex: `docker run -p 5432:5432 postgres`*

*(Note : check the README on DockerHub to see the configuration options specific to an image.)*

###### *Commands :*
You can launch a command in the container at the same time, for instance : *`docker run -ti ubuntu bash`*

##### 2. Set a custom image
Starting from an existing image, you can configure your portable environment with custom dependencies, runtime, environment variables, etc.
All the steps used to build the image need to be listed in a **Dockerfile**.

*Dockerfile example :*
```Dockerfile
# Use an official Python runtime as a parent image
FROM python:2.7-slim

# Set the working directory to /app
WORKDIR /app

# Copy the current directory contents into the container at /app
ADD . /app

# Install any needed packages specified in requirements.txt
RUN pip install --trusted-host pypi.python.org -r requirements.txt

# Make port 80 available to the world outside this container
EXPOSE 80

# Define environment variable
ENV NAME World

# Run app.py when the container launches
CMD ["python", "app.py"]
```

Then build the image with `docker build`.

###### *Options*
- *-t myimagename* : Tag the image with a custom name

### Volumes
Volume are needed for persisting data generated by and used by Docker containers.

### Commands
 - `docker build [-options]` : Build image from the Dockerfile
 - `docker run [-options] image-name [command]` : Execute Docker image
 - `docker image ls` : List all images
 - `docker ps` : List all running containers
 - `docker stop <hash>` : Stop the specified container
 - `docker kill <hash>` : Force shutdown the specified container
 - `docker rmi <image id>` : Remove specified image from this machine
 - `docker rm <hash>`: Remove specified container from this machine

## Docker Compose

### *docker-compose.yml* file
In order to set the environment for a multi-container application, we need a *docker-compose.yml* file to define each service with :
 - the image to use for the container
 - the ports to connect with others containers
 - the volumes to use
 - the name of the containers it depends on, and that need to be started first
 - the commands to run
 - the environment variables ...

*docker-compose.yml example :*
```yml
version: '3'
services:
  db:
    image: postgres
  web:
    build: .
    command: bundle exec rails s -p 3000 -b '0.0.0.0'
    volumes:
      - .:/myapp
    ports:
      - "3000:3000"
    depends_on:
      - db
```

### Commands

 - `docker-compose build` : Build all images needed for the services listed in the docker-compose.yml
 - `docker-compose build service-name` : Build the needed images for the specified service
 - `docker-compose up` : Start all the services
 - `docker-compose up service-name` : Start the specified container/service (and the containers it depends on)
 - `docker-compose run container-name command` : Run the container and launch the specified command in it
 *(ex: `docker-compose run web rails c`)*
 - `docker-compose ps` : List all running containers among the ones in the docker-compose.yml
 - `docker-compose down`  : Stop all running services