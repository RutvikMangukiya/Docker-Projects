# Stable official Java runtime base image
FROM openjdk:17-jdk-alpine

# Working directory
WORKDIR /app

# metadata
LABEL maintainer="your-email@example.com"
LABEL version="1.0"
LABEL description="A simple Java application"

# Copy source code into the container
COPY src/Main.java /app/Main.java

# Compile the Java code
RUN javac Main.java

# Run the Java application when the container starts
CMD ["java", "Main"]