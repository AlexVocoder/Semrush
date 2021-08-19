# Preparing an Application Image and the Produciton Environment.
# Deploying the Image



To use the Docker system you should have Docker installed. 
You need to start a container with an application in the Kubernetes cluster. For example, let our **ExampleApp** recieve requests on port 8800.

Create a directory and its subdirectories:

```
mkdir quickstart_docker
mkdir quickstart_docker/application
mkdir quickstart_docker/docker
mkdir quickstart_docker/docker/application
```

```
quickstart_docker/ # The directory of the entire project
├──application/      # The application code is kept here
└──docker/           # Different stuff for the docker is kept here
   └──application/    # Here, we put the Dockerfile for the application
```
   
Note:
Such a directory structure improves control.

To deploy the application you'll need a deploying application. Let's take an example app in Python *application.py* and put it in the *quickstart_docker/application* subdirectory (you should replace this example app later on).

```
import http.server
import socketserver

PORT = 8000
Handler = http.server.SimpleHTTPRequestHandler
httpd = socketserver.TCPServer(("", PORT), Handler)
print("serving at port", PORT)
httpd.serve_forever()
```

The application needs an environment. At least Python is needed, which itself needs an OS with all dependencies. All this stuff is available on the Dockerhub, so take it.

Put the file named as **Dockerfile** in the subdirectory *quickstart_docker/docker/application*. This file has the following content: 

```
# Use base image from the registry
FROM python:3.5

# Set the working directory to /app
WORKDIR /app

# Copy the 'application' directory contents into the container at /app
COPY ./application /app

# Make port 8000 available to the world outside this container
EXPOSE 8000

# Execute 'python /app/application.py' when container launches
CMD ["python", "/app/application.py"]
```

Type the following in the terminal:
```
docker build . -f-docker/application/Dockerfile -t exampleapp
```
The arguments are:
- . : the working directory, the building context;
- -f-docker/application/Dockerfile : the docker file; 
- -t exampleapp : put a tag to the image, so that we can find it here

Your can find details on buiding images for Docker here:
https://docs.docker.com/engine/reference/builder/.

Below is the list of images:

$ docker images

| REPOSITORY |           TAG |           IMAGE ID |           CREATED |            SIZE |
| ---------- | ------------- | ------------------ | ----------------- | --------------- |
| exampleapp |           latest |        83wse0edc28a |       2 seconds ago |      153MB |
| python |               3.6 |           05sob8636w3f |       6 weeks ago |        153MB |

Next put this image in the repository.
