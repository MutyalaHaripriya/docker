Understand   DOCKER   through a project

Step 1: Set Up Project Structure

Create a folder for your project with the following structure.

docker-project/
|---- app/
|    |---- app.py     # Code for the Flask application
|    |---- requirements.txt    # Dependencies for the Flask
|--- Dockerfile                # Image definition for the Flask
|--- docker-compose.yml        # Docker Compose file


Step 2: Create the Python Web App

Inside the app/folder, create app.py with the following code :

from flask import Flask
import redis
import os

app = Flask(__name__)

# Connect to Redis
cache = redis.Redis(host = os.getenv('REDIS_HOST','redis'),port=6379)

@app.route("/")

def hello():
   count = cache.incr("visits")
   returnf ' Hello, World! You have visited {count} times'

if __name__=="__main__":
    app.run(host='0.0.0.0')

This small Flask app connect to Redis and counts visits to the page.

Step 3: Add Dependencies

In app/ , create requirements.txt to specify the dependencies:

Flask and Redis

Step 4: Create the Dockerfile

At the root of your project, create a Dockerfile to build the Flask app's image:

#Use the official Python image
FROM python:3.9-slim

#Set the working directory
WORKDIR /app

#Copy the app to the container
COPY ./app/app

# Install dependencies
RUN pip install --no-cache-dir -r requirements.txt

# Expose port 5000
EXPOSE 5000

# Run the app
CMD ["python", "app.py"]


Step 5 : Create the Docker Compose File

Create docker-compose.yml to define the multi-container setup:

version: '3'
services:
  web:
  build: .
  ports:
   - "5000:5000"
  depends_on:
   - redis
  environment:
   - REDIS_HOST=redis
  redis:
   image: "redis:alpine"
   ports:
    - "6379:6379"


Step 6 : Build and Run the App

In the root directory, run the following command to build the images and start the containers:

docker-compose up --build


Step 7 : Access the Web App

Open your browser and visit http://localhost:5000 to see the app in action.

The page will display:


     Hello, World!
You have visited 1 times.
























