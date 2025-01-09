# Docker Network & Volumes: Deploying a Two-Tier Flask App with MySQL

Introduction

DevOps methodologies, emphasizing automation and collaboration, have revolutionized the software development lifecycle. This article will explore using Docker to deploy a two-tier Flask application integrated with a MySQL database. The project highlights key concepts such as Docker volumes, which ensure data persistence, and Docker networks, which facilitate seamless communication between containers, showcasing their pivotal role in modern containerized application development.

Prerequisites

Before diving into the tutorial, make sure you have Docker and Git installed on your machine.

Step 1: Clone the Repository

Begin by cloning the project repository from GitHub:

Step 2: Run MySQL Container

Pull the MySQL image and start the MySQL container with the following command:

docker pull mysql
docker run -d -e MYSQL_ROOT_PASSWORD=root mysql

Step 3: Create a new database inside the MySQL container

To create a new database, exec into the container and run these commands:

docker ps
docker exec -it <container id> bash
mysql -u root -p

List databases and create a new database named ‘devops’:

SHOW databases;
CREATE database devops;

Step 4: Build the Flask App Image

Navigate to the Flask app directory and build the Docker image by creating a Dockerfile.

Dockerfile Preview:

# Use an official Python runtime as the base image
FROM python:3.9-slim

# Set the working directory in the container
WORKDIR /app

# install required packages for system
RUN apt-get update \
    && apt-get upgrade -y \
    && apt-get install -y gcc default-libmysqlclient-dev pkg-config \
    && rm -rf /var/lib/apt/lists/*

# Copy the requirements file into the container
COPY requirements.txt .

# Install app dependencies
RUN pip install mysqlclient
RUN pip install --no-cache-dir -r requirements.txt

# Copy the rest of the application code
COPY . .

# Specify the command to run your application
CMD ["python", "app.py"]

Command to build Docker image:

docker build -t two-tier-flask-backend .

Step 5: Run Flask App Container

Run the Flask app container using the below command:

docker run -d -p 5000:5000 -e MYSQL_HOST=mysql -e MYSQL_USER=root -e MYSQL_PASSWORD=root -e MYSQL_DB=devops two-tier-flask-backend:latest

Step 6: Creating a Docker Network

We need to create a Docker network to allow the containers to communicate with each other. As shown in the container logs, they won't be able to connect properly without it.

Stop both containers and remove them for now:

docker stop <container id> && docker rm <container id>

Create a Docker network named ‘two-tier’ and run both MySQL and Flask containers:

docker network create two-tier -d bridge
docker run -d --name mysql --network two-tier -e MYSQL_ROOT_PASSWORD=root -e MYSQL_DATABASE=devops mysql
docker run -d -p 5000:5000 --network two-tier -e MYSQL_HOST=mysql -e MYSQL_USER=root -e MYSQL_PASSWORD=root -e MYSQL_DB=devops two-tier-flask-backend:latest

Use the inspect command to view the containers connected to the ‘two-tier’ Docker network.

docker network ls
docker network inspect two-tier

Step 7: View the application

Open your browser and go to http://<ec2-public-ip>:5000 to access the application.

Step-8 Check data persistency in the database

Check the data entries inside the database using the exec command.

When the MySQL container is stopped and removed, and you attempt to exec into a newly started container, all previous data will be lost, as containers by default do not persist data between restarts unless configured to use persistent storage.

Step 9: Creating a Docker Volume

Create a Docker network to facilitate communication between the Flask app and MySQL containers:

docker volume create mysql-data
docker inspect mysql-data

Stop and remove the MySQL container, then restart it using the command below:

docker run -d --name mysql --network two-tier -v mysql-data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=root -e MYSQL_DATABASE=devops mysql

Now, even if the MySQL container is stopped and removed, all the database data will persist in the volume we created, ensuring that it remains intact across container restarts.

Simplify Deployment with Docker Compose

So far, we've meticulously followed the step-by-step process for deploying our two-tier Flask app using individual Docker commands. Now, let's take it a step further and streamline the entire deployment process with Docker Compose.

Docker Compose Configuration

Create a docker-compose.yml file in your project directory with the following content:

version: "3.8"

services:
  mysql:
    container_name: mysql
    image: mysql
    environment:
      MYSQL_DATABASE: "devops"
      MYSQL_ROOT_PASSWORD: "root"
    ports:
      - "3306:3306"
    volumes:
      - mysql-data:/var/lib/mysql
    networks:
      - two-tier
    restart: always
    healthcheck:
      test: ["CMD", "mysqladmin", "ping", "-h", "localhost", "-uroot", "-proot"]
      interval: 10s
      timeout: 5s
      retries: 5
      start_period: 60s

  flask:
    build:
      context: .
    container_name: two-tier-flask-backend
    ports:
      - "5000:5000"
    environment:
      MYSQL_HOST: mysql
      MYSQL_USER: root
      MYSQL_PASSWORD: root
      MYDQL_DB: devops
    networks:
      - two-tier
    depends_on:
      - mysql
    restart: always
    healthcheck:
      test: ["CMD-SHELL", "curl -f htttp://localhost:5000/health || exit 1"]
      interval: 10s
      timeout: 5s
      retries: 5
      start_period: 30s

volumes:
  mysql-data:
 
networks:
  two-tier:

Deploy with Docker Compose

With the docker-compose.yml file in place, deploying the entire stack becomes a breeze.

First, installation of docker-compose on the machine is required. Then run the following command:

docker-compose up
or
docker-compose up -d

Conclusion

This article provides a comprehensive guide on deploying a two-tier Flask application with a MySQL database using Docker. It covers essential steps such as running MySQL and Flask containers, creating Docker networks and volumes for data persistence and container communication, and finally, simplifying the deployment process with Docker Compose. The tutorial emphasizes the importance of Docker volumes and networks in ensuring seamless integration and data retention across container restarts, showcasing their significance in modern DevOps practices.