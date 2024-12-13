## simple-java-docker

A simple java app that runs on docker.

## Tasks with Answers

**1. Create a Simple Java Application**
   - Clone the repository inside this directory:

      **Answer**

      ![image](https://github.com/RutvikMangukiya/simple-java-docker/blob/main/image/1-git-clone.png)

      ![image](https://github.com/RutvikMangukiya/simple-java-docker/blob/main/image/2-cd-src.png)

**2. Create a Dockerfile**
   - Create a file named `Dockerfile` in the same directory and add the following content:

      **Answer**

      ![image](https://github.com/RutvikMangukiya/simple-java-docker/blob/main/image/3-dockerfile.png)

**2. Build the image using the Dockerfile and run the container**
   - To build the Docker image, run the following command in the directory containing the Dockerfile:

      **Answer**

      ![image](https://github.com/RutvikMangukiya/simple-java-docker/blob/main/image/4-docker-build.png)

      ![image](https://github.com/RutvikMangukiya/simple-java-docker/blob/main/image/5-docker-images.png)

   - Run the Container
      - To run the container, use the following command:

      **Answer**

      ![image](https://github.com/RutvikMangukiya/simple-java-docker/blob/main/image/6-docker-run.png)

**3. Update the application code at ``src/main.java`` and rebuild the image**
   - Rebuild the image with tag name `java:v1` :

     **Answer**

     ![image](https://github.com/RutvikMangukiya/simple-java-docker/blob/main/image/7-update-code.png)

     ![image](https://github.com/RutvikMangukiya/simple-java-docker/blob/main/image/8-v1-build.png)

     ![image](https://github.com/RutvikMangukiya/simple-java-docker/blob/main/image/9-v1-list.png)

   - Run the java image with new tag name `java:v1` :

     **Answer**

     ![image](https://github.com/RutvikMangukiya/simple-java-docker/blob/main/image/10-v1-run.png)
   
   - Image with old tag name `java:latest` :

     ![image](https://github.com/RutvikMangukiya/simple-java-docker/blob/main/image/11-docker-run-latest-again.png)

**4. Push the image to a public or private repository (e.g. Docker Hub)**
   - To push the image to Docker Hub, you need to tag it with your Docker Hub username and repository name, then push it.
   - **1. Tag the Image:**

      **Answer**

      ![image](https://github.com/RutvikMangukiya/simple-java-docker/blob/main/image/12-docker-tag.png)

   - **2. Push the Image:**

      **Answer**

      ![image](https://github.com/RutvikMangukiya/simple-java-docker/blob/main/image/13-docker-push.png)

   - **3. Check your profile at Docker Hub Webpage:**

      **Answer**

      ![image](https://github.com/RutvikMangukiya/simple-java-docker/blob/main/image/14-dockerhub.png)

      ![image](https://github.com/RutvikMangukiya/simple-java-docker/blob/main/image/15-dockerhub-2.png)

      > **Note:** To push the image tagged `java:v1` to Docker Hub, follow the same process as above and use the commands given in the `code.txt` file.
