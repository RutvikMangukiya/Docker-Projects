# Docker Compose: Django Notes App

- Docker makes deploying applications simple and efficient by containerizing your application and its dependencies. In this guide, we will walk through building a Django-based notes application with MySQL as the database, orchestrated using Docker Compose.

# Application Code Repository

- [Application Code](https://github.com/RutvikMangukiya/django-notes-app)

# Prerequisites

- Ensure you have Docker and Git installed on your machine.

# Docker File and Docker Compose Setup
## Step 1: Clone the Project Repository

- Clone the project from Git Hub.

```bash
git clone https://github.com/RutvikMangukiya/django-notes-app.git
cd django-notes-app/
```

![gitclone](https://github.com/RutvikMangukiya/Docker-Projects/blob/master/django-nginx-mysql-docker/image/1-git-clone.png)

## Step 2: Create a Dockerfile for the Backend

- Below is the Dockerfile for the Django backend:

- [Dockerfile Preview](https://github.com/RutvikMangukiya/Docker-Projects/blob/master/django-nginx-mysql-docker/Dockerfile)

- This Dockerfile specifies a Python 3.9 base image, installs the necessary dependencies, and prepares the Django backend for containerization.

## Step 3: Build the Docker Image

- Build the Docker image for the notes application.

```bash
docker build -t notes-app .
```
- This command creates a Docker image tagged as notes-app. Make sure to run this command in the root directory of your project.

![dockerbuild](https://github.com/RutvikMangukiya/Docker-Projects/blob/master/django-nginx-mysql-docker/image/2-docker-build-notes-app.png)

## Step 4: Create a Docker Network

- Create a network for the application containers.

```bash
docker network create notes-app -d bridge
```
- This ensures that all containers can communicate with each other seamlessly within an isolated network.

![dockernetwork](https://github.com/RutvikMangukiya/Docker-Projects/blob/master/django-nginx-mysql-docker/image/3-docker-network.png)

## Step 5: Set Up Persistent Storage

- Create a volume to store MySQL data persistently.

```bash
docker volume create mysql-data
```
- This step ensures that MySQL data remains intact even if the database container is stopped or removed.

![dockervolume](https://github.com/RutvikMangukiya/Docker-Projects/blob/master/django-nginx-mysql-docker/image/4-docker-volume.png)

## Step 6: Configure Docker Compose

- Below is the docker-compose.yml file for orchestrating the application:

- [Docker Compose File Preview](https://github.com/RutvikMangukiya/Docker-Projects/blob/master/django-nginx-mysql-docker/docker-compose.yml)
    
- This configuration defines services for Nginx, MySQL, and the Django application, ensuring seamless integration.

## Step 7: Run the Application

- Execute the following command to start the application.

```bash
docker-compose up -d --build
```
- This command launches the containers in detached mode, ensuring that the application is accessible and running.

![dockercompose](https://github.com/RutvikMangukiya/Docker-Projects/blob/master/django-nginx-mysql-docker/image/5-docker-compose-up.png)

## Step 8: Verify the Output

- Access the application in your browser at http://<ec2-public-ip>:80 to verify that everything is running as expected.

![output](https://github.com/RutvikMangukiya/Docker-Projects/blob/master/django-nginx-mysql-docker/image/6-output.png)

## Step 9: Test Data Persistence

- Verify that data persists by stopping and removing containers.

```bash
docker-compose down
```
- Restart the containers and ensure the data remains intact, thanks to the mysql-data volume.

![volumetest](https://github.com/RutvikMangukiya/Docker-Projects/blob/master/django-nginx-mysql-docker/image/7-volume-persist-test.png)

### Conclusion

- Congratulations on successfully building and deploying a containerized application using Docker and Docker Compose! By following this guide, you’ve created a robust and scalable setup while ensuring seamless networking and persistent data storage using Docker's capabilities.

- This walkthrough highlights the power and versatility of Docker in modern development workflows, giving you a strong foundation to explore advanced containerization techniques and optimize your projects further.