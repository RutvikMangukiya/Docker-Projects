# Docker Network & Volumes: Deploying a Two-Tier Flask App with MySQL

## Introduction

- DevOps methodologies, emphasizing automation and collaboration, have revolutionized the software development lifecycle. This documentation will explore using Docker to deploy a two-tier Flask application integrated with a MySQL database. The project highlights key concepts such as Docker volumes, which ensure data persistence, and Docker networks, which facilitate seamless communication between containers, showcasing their pivotal role in modern containerized application development.

# Application Code Repository

- [Application Code](https://github.com/RutvikMangukiya/two-tier-flask-app)

## Prerequisites

- Before diving into the tutorial, make sure you have Docker and Git installed on your machine.

## Step 1: Clone the Repository

- Begin by cloning the project repository from GitHub:

![git-clone](https://github.com/RutvikMangukiya/Docker-Projects/blob/master/flask-docker-network-volumes/image/1-git-clone.png)

## Step 2: Run MySQL Container

- Pull the MySQL image and start the MySQL container with the following command:

```bash
docker pull mysql
docker run -d -e MYSQL_ROOT_PASSWORD=root mysql
```

![docker-pull-mysql](https://github.com/RutvikMangukiya/Docker-Projects/blob/master/flask-docker-network-volumes/image/4-docker-pull-mysql.png)

![docker-run-mysql](https://github.com/RutvikMangukiya/Docker-Projects/blob/master/flask-docker-network-volumes/image/5-docker-run-mysql.png)

## Step 3: Create a new database inside the MySQL container

- To create a new database, exec into the container and run these commands:

```bash
docker ps
docker exec -it <container id> bash
mysql -u root -p
```

![docker-exec-mysql](https://github.com/RutvikMangukiya/Docker-Projects/blob/master/flask-docker-network-volumes/image/6-docker-exec-mysql.png)

![mysql-db](https://github.com/RutvikMangukiya/Docker-Projects/blob/master/flask-docker-network-volumes/image/7-mysql-db.png)

- List databases and create a new database named ‘devops’:

```bash
SHOW databases;
CREATE database devops;
```

![create-db-devops](https://github.com/RutvikMangukiya/Docker-Projects/blob/master/flask-docker-network-volumes/image/8-create-db-devops.png)

## Step 4: Build the Flask App Image

- Navigate to the Flask app directory and build the Docker image by creating a Dockerfile.

- Dockerfile Preview:
[Dockerfile](https://github.com/RutvikMangukiya/Docker-Projects/blob/master/flask-docker-network-volumes/Dockerfile)

- Command to build Docker image:

```bash
docker build -t two-tier-flask-backend .
```

![docker-build](https://github.com/RutvikMangukiya/Docker-Projects/blob/master/flask-docker-network-volumes/image/3-docker-build.png)

## Step 5: Run Flask App Container

- Run the Flask app container using the below command:

```bash
docker run -d -p 5000:5000 -e MYSQL_HOST=mysql -e MYSQL_USER=root -e MYSQL_PASSWORD=root -e MYSQL_DB=devops two-tier-flask-backend:latest
```

![docker-run-flask](https://github.com/RutvikMangukiya/Docker-Projects/blob/master/flask-docker-network-volumes/image/9-docker-run-flask-backend.png)

## Step 6: Creating a Docker Network

- We need to create a Docker network to allow the containers to communicate with each other. As shown in the container logs, they won't be able to connect properly without it.

![docker-logs](https://github.com/RutvikMangukiya/Docker-Projects/blob/master/flask-docker-network-volumes/image/10-docker-logs.png)

- Stop both containers and remove them for now:

```bash
docker stop <container id> && docker rm <container id>
```

![remove-mysql-container](https://github.com/RutvikMangukiya/Docker-Projects/blob/master/flask-docker-network-volumes/image/11-remove-mysql-container.png)

- Create a Docker network named ‘two-tier’ and run both MySQL and Flask containers:

```bash
docker network create two-tier -d bridge
docker run -d --name mysql --network two-tier -e MYSQL_ROOT_PASSWORD=root -e MYSQL_DATABASE=devops mysql
docker run -d -p 5000:5000 --network two-tier -e MYSQL_HOST=mysql -e MYSQL_USER=root -e MYSQL_PASSWORD=root -e MYSQL_DB=devops two-tier-flask-backend:latest
```

![create-network](https://github.com/RutvikMangukiya/Docker-Projects/blob/master/flask-docker-network-volumes/image/12-create-nw-run-both-container.png)

- Use the inspect command to view the containers connected to the ‘two-tier’ Docker network.

```bash
docker network ls
docker network inspect two-tier
```

![docker-network](https://github.com/RutvikMangukiya/Docker-Projects/blob/master/flask-docker-network-volumes/image/13-docker-nw-ls.png)

![docker-inspect](https://github.com/RutvikMangukiya/Docker-Projects/blob/master/flask-docker-network-volumes/image/14-docker-inspecting.png)

## Step 7: View the application

- Open your browser and go to http://<ec2-public-ip>:5000 to access the application.

![output](https://github.com/RutvikMangukiya/Docker-Projects/blob/master/flask-docker-network-volumes/image/15-output-app.png)

## Step-8 Check data persistency in the database

- Check the data entries inside the database using the exec command.

![docker-exec](https://github.com/RutvikMangukiya/Docker-Projects/blob/master/flask-docker-network-volumes/image/16-docker-exec-mysql.png)

![mysql-table](https://github.com/RutvikMangukiya/Docker-Projects/blob/master/flask-docker-network-volumes/image/17-mysql-table-db.png)

- When the MySQL container is stopped and removed, and you attempt to exec into a newly started container, all previous data will be lost, as containers by default do not persist data between restarts unless configured to use persistent storage.

![remove-mysql](https://github.com/RutvikMangukiya/Docker-Projects/blob/master/flask-docker-network-volumes/image/18-remove-mysql.png)

![data-gone](https://github.com/RutvikMangukiya/Docker-Projects/blob/master/flask-docker-network-volumes/image/19-data-gone.png)

![database-gone](https://github.com/RutvikMangukiya/Docker-Projects/blob/master/flask-docker-network-volumes/image/20-data-gone.png)

## Step 9: Creating a Docker Volume

- Create a Docker network to facilitate communication between the Flask app and MySQL containers:

```bash
docker volume create mysql-data
docker inspect mysql-data
```

![create-docker-volume](https://github.com/RutvikMangukiya/Docker-Projects/blob/master/flask-docker-network-volumes/image/21-create-docker-volume.png)

- Stop and remove the MySQL container, then restart it using the command below:

```bash
docker run -d --name mysql --network two-tier -v mysql-data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=root -e MYSQL_DATABASE=devops mysql
```

![run-mysql-volume](https://github.com/RutvikMangukiya/Docker-Projects/blob/master/flask-docker-network-volumes/image/22-running-mysql-with-volume-bind.png)

- Now, even if the MySQL container is stopped and removed, all the database data will persist in the volume we created, ensuring that it remains intact across container restarts.

![volume-data](https://github.com/RutvikMangukiya/Docker-Projects/blob/master/flask-docker-network-volumes/image/23-volume-data.png)

# Simplify Deployment with Docker Compose

- So far, we've meticulously followed the step-by-step process for deploying our two-tier Flask app using individual Docker commands. Now, let's take it a step further and streamline the entire deployment process with Docker Compose.

## Docker Compose Configuration

- Create a docker-compose.yml file in your project directory with the following content:

![delete-composefile](https://github.com/RutvikMangukiya/Docker-Projects/blob/master/flask-docker-network-volumes/image/24-delete-compose-file.png)

- Docker Compose File Preview:
[Docker Compose File](https://github.com/RutvikMangukiya/Docker-Projects/blob/master/flask-docker-network-volumes/docker-compose.yml)

## Deploy with Docker Compose

- With the docker-compose.yml file in place, deploying the entire stack becomes a breeze.

- First, installation of docker-compose on the machine is required. Then run the following command:

```bash
docker-compose up
or
docker-compose up -d
```

![install-docker-compose](https://github.com/RutvikMangukiya/Docker-Projects/blob/master/flask-docker-network-volumes/image/25-install-docker-compose.png)

![docker-compose-up](https://github.com/RutvikMangukiya/Docker-Projects/blob/master/flask-docker-network-volumes/image/26-docker-compose-up.png)

![output](https://github.com/RutvikMangukiya/Docker-Projects/blob/master/flask-docker-network-volumes/image/19-data-gone.png)

## Conclusion

- This documentation provides a comprehensive guide on deploying a two-tier Flask application with a MySQL database using Docker. It covers essential steps such as running MySQL and Flask containers, creating Docker networks and volumes for data persistence and container communication, and finally, simplifying the deployment process with Docker Compose. The tutorial emphasizes the importance of Docker volumes and networks in ensuring seamless integration and data retention across container restarts, showcasing their significance in modern DevOps practices.