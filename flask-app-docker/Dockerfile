# Base image: Using official Python 3.11 slim image
FROM python:3.11-slim

# Set working directory inside the container
WORKDIR /app

# Copy all project files to the working directory 
COPY . /app

# Upgrade pip and install required dependencies from requirements.txt
RUN pip install --upgrade pip
RUN pip install -r requirements.txt

# Expose the port your app will run on 
EXPOSE 4000

# Default command to run the app
CMD ["python", "app.py"]