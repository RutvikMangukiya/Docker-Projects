# Flask Web Application 
This is a simple Flask web application built with Python 3.11. It features a homepage and an API endpoint. Additionally, it includes Docker support for containerization.

# Flask App with Docker on AWS EC2

This project demonstrates how to deploy a Flask app using Docker on an AWS EC2 instance. Below are the steps to set up and run the Flask app on an EC2 instance.

## Prerequisites

- AWS Account
- Basic knowledge of EC2, SSH, and Docker
- GitHub repository with a Flask app

---

## 1. Launch an EC2 Instance

1. Log in to your [AWS Management Console](https://aws.amazon.com/console/).
2. Navigate to the **EC2 Dashboard**.
3. Click **Launch Instance**.
4. Choose the following configurations:
   - **Amazon Machine Image (AMI)**: Select Amazon Linux 2 or Ubuntu Server 24.04 LTS (both free tier eligible).
   - **Instance Type**: Select `t2.micro` (free tier eligible).
   - **Key Pair**: Choose an existing key pair or create a new one (this is required to SSH into your instance).
5. Configure the security group:
   - **Port 22 (SSH)**: Allow SSH access to connect to the instance.
   - **Port 4000 (Custom TCP)**: To access your Flask app.
6. Click **Launch** to start the EC2 instance.

---

## 2. Connect to Your EC2 Instance

1. After your instance is launched, connect to it via SSH:
    ```bash
    ssh -i your-key.pem ec2-user@<EC2_PUBLIC_IP>
    ```

   > **Note**: Replace `your-key.pem` with the path to your key file, and `<EC2_PUBLIC_IP>` with your EC2 instance's public IP. **or** you can directly copy the SSH client command shown in the 'Connect to instance' GUI."

---

## 3. Install Docker on EC2

Once you're logged into your EC2 instance, follow the steps below to install Docker:

### For Amazon Linux 2:
```bash
sudo yum update -y
sudo amazon-linux-extras install docker
sudo service docker start
sudo usermod -a -G docker ec2-user
```

### For Ubuntu: 
```bash
sudo apt update
sudo apt install -y docker.io
sudo systemctl start docker
sudo usermod -aG docker ubuntu
```
> **Note**: After adding your user to the Docker group, log out and log back in to apply the changes.

## 4. Clone Flask Project from GitHub

After Docker is installed, clone project repository to the EC2 instance:

```bash
git clone https://github.com/RutvikMangukiya/flask-app-docker.git
cd flask-app-docker
```

![image](https://github.com/RutvikMangukiya/Docker-Projects/blob/master/flask-app-docker/image/T2-git-clone.png)

## 5. Create a Dockerfile

Create a file named `Dockerfile` in the same directory and add the following content:

![image](https://github.com/RutvikMangukiya/Docker-Projects/blob/master/flask-app-docker/image/T3.1-vim-dockerfile.png)
![image](https://github.com/RutvikMangukiya/Docker-Projects/blob/master/flask-app-docker/image/T3-New-dockerfile.png)

## 6. Build the image using the Dockerfile and run the container

**Build the Docker image**:
  - To build the Docker image, run the following command in the directory containing the Dockerfile:

```bash
docker build -t flask-app-docker:latest .
```

![image](https://github.com/RutvikMangukiya/Docker-Projects/blob/master/flask-app-docker/image/T4-docker-build.png)

**Run the Docker container**:
  - To run the container, use the following command:

```bash
docker run -d -p 4000:4000 flask-app-docker:latest
```
The -d flag runs the container in detached mode, and -p 4000:4000 maps port 4000 of the container to port 4000 on the EC2 instance.

![image](https://github.com/RutvikMangukiya/Docker-Projects/blob/master/flask-app-docker/image/T5-docker-run.png)

**List the running Docker containers**:

```bash
docker ps
```
![image](https://github.com/RutvikMangukiya/Docker-Projects/blob/master/flask-app-docker/image/T6-docker-ps.png)

## 6. Access the Flask App
Once the Docker container is running, open a browser and navigate to:

```bash
http://<EC2_PUBLIC_IP>:4000
```
![image](https://github.com/RutvikMangukiya/Docker-Projects/blob/master/flask-app-docker/image/T7-output.png)
You should now see your Flask app running!

## 7. Configure Security Groups (if required)

If you can't access the Flask app, ensure that your EC2 security group allows inbound traffic on port 4000.

1. Navigate to **EC2 Dashboard** > **Security Groups**.
2. Select the security group associated with your EC2 instance.
3. Edit the **Inbound Rules** and add a rule for **Custom TCP**:
    - **Port range**: 4000
    - **Source**: 0.0.0.0/0 (to allow public access)

## 8. Push the image to a public or private repository (e.g. Docker Hub)

To push the image to Docker Hub, you need to tag it with your Docker Hub username and repository name, then push it.

**1. Tag the Image**:
 ```bash
 docker tag flask-app-docker:latest rutvikmangukiya/flask-app-docker:latest
 ```

![image](https://github.com/RutvikMangukiya/Docker-Projects/blob/master/flask-app-docker/image/T8-docker-tag.png)

**2. Push the Image**:
 ```bash
 docker push rutvikmangukiya/flask-app-docker:latest
 docker login
 ```

![image](https://github.com/RutvikMangukiya/Docker-Projects/blob/master/flask-app-docker/image/T9-Dcoker-push.png)

**3. Check your profile at Docker Hub Webpage**:

![image](https://github.com/RutvikMangukiya/Docker-Projects/blob/master/flask-app-docker/image/T10-dockerhub.png)

![image](https://github.com/RutvikMangukiya/Docker-Projects/blob/master/flask-app-docker/image/T10-Dockerhub-2.png)
