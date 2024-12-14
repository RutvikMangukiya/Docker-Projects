Introduction

Docker makes deploying applications simple and efficient by containerizing your application and its dependencies. In this guide, we will walk through building a Django-based notes application with MySQL as the database, orchestrated using Docker Compose.

# Prerequisites

Before diving into the tutorial, ensure you have Docker and Git installed on your machine.

Step 1: Clone the Project Repository

1-git-clone.png Clone the project from Git Hub.

git clone https://github.com/RutvikMangukiya/django-notes-app.git
cd django-notes-app/

Step 2: Create a Dockerfile for the Backend

Below is the Dockerfile for the Django backend:


This Dockerfile specifies a Python 3.9 base image, installs the necessary dependencies, and prepares the Django backend for containerization.

Step 3: Build the Docker Image

2-docker-build-notes-app.png Build the Docker image for the notes application.

docker build -t notes-app .

This command creates a Docker image tagged as notes-app. Make sure to run this command in the root directory of your project.

Step 4: Create a Docker Network

3-docker-network.png Create a network for the application containers.

docker network create notes-app -d bridge

This ensures that all containers can communicate with each other seamlessly within an isolated network.

Step 5: Set Up Persistent Storage

4-docker-volume.png Create a volume to store MySQL data persistently.

docker volume create mysql-data

This step ensures that MySQL data remains intact even if the database container is stopped or removed.

Step 6: Configure Docker Compose

Below is the docker-compose.yml file for orchestrating the application:

version: "3.8"

services:
  nginx:
    build: 
      context: ./nginx
    container_name: "nginx_cont"
    ports:
      - "80:80"
    restart: always
    depends_on:
      - djnago
    networks:
      - notes-app

  db:
    container_name: "db_cont"
    image: mysql
    ports:
      - "3306:3306"
    environment:
      MYSQL_ROOT_PASSWORD: root
      MYSQL_DATABASE: test_db
    volumes:
      - ./mysql-data:/var/lib/mysql
    restart: always
    healthcheck:
      test: ["CMD", "mysqladmin", "ping", "-h", "localhost", "-uroot", "-proot"]
      interval: 10s
      timeout: 5s
      retries: 5
      start_period: 60s
    networks:
      - notes-app
  
  djnago:
    build: 
      context: .
    container_name: "django_cont"
    command: sh -c "python manage.py migrate --noinput && gunicorn notesapp.wsgi --bind 0.0.0.0:8000"
    ports:
      - "8000:8000"
    env_file:
      - ".env"
    depends_on:
      - db
    restart: always
    healthcheck:
      test: ["CMD-SHELL", "curl -f http://localhost:8000/admin || exit 1"]
      interval: 10s
      timeout: 5s
      retries: 5
      start_period: 60s
    networks:
      - notes-app

networks:
  notes-app:

This configuration defines services for Nginx, MySQL, and the Django application, ensuring seamless integration.

Step 7: Run the Application

5-docker-compose-up.png Execute the following command to start the application.

docker-compose up -d --build

This command launches the containers in detached mode, ensuring that the application is accessible and running.

Step 8: Verify the Output

6-output.png Access the application in your browser at http://<ec2-public-ip>:80 to verify that everything is running as expected.

Step 9: Test Data Persistence

7-volume-persist-test.png Verify that data persists by stopping and removing containers.

docker-compose down

Restart the containers and ensure the data remains intact, thanks to the mysql-data volume.

Conclusion

Congratulations! You have successfully built and deployed a containerized Django notes application using Docker and Docker Compose. By leveraging Docker’s networking and volume capabilities, you’ve ensured a scalable and persistent application setup.

This guide not only demonstrates the power of Docker in modern application development but also provides a foundation for exploring advanced containerization techniques. Happy Learning!