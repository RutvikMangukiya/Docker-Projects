# Stage 1 - Build the JAR (Java Application Runtime) using image of maven

FROM maven:3.8.3-openjdk-17 AS builder

# Setup working directory

WORKDIR /app

# Copy source code from host machine to container

COPY . /app

# Build application and skip test cases

# EXPOSE 8080

# Create JAR File
RUN mvn clean install -DskipTests=true

#CMD ["java", "-jar", "/expenseapp.jar"]

#--------------------------------------------
# Stage 2 - Execute JAR File from above stage
#--------------------------------------------

# Use a lightweight Java base image

FROM openjdk:17-alpine

WORKDIR /app

# Copy build from stage 1 (builder)

COPY --from=builder /app/target/*.jar /app/target/expenseapp.jar

# Expose application port

EXPOSE 8080

# Start the application

CMD [ "java", "-jar", "/app/target/expenseapp.jar" ]