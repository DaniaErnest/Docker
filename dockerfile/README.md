What is a Dockerfile?

A Dockerfile is a text file containing instructions Docker uses to build an image.

Think about the relationship:
Dockerfile
    |
    | docker build
    v
Docker Image
    |
    | docker run
    v
Docker Container


For example, imagine you have a Node.js application.

You need to tell Docker:
1. Which base image to use.
2. Where to put the application.
3. Which dependencies to install.
4. Which application files to copy.
5. Which port the application uses.
6. How to start the application.
You define all these instructions in a Dockerfile.
2. Understanding Dockerfile Instructions
Let's learn the most important instructions one by one.

**A. FROM — Choosing a base image**

Every normal Dockerfile starts with FROM.

Example:
```Dockerfile 
FROM node:22-alpine
```


This tells Docker to use the Node.js 22 Alpine image as the foundation for your application.

Other examples:
```Dockerfile 
FROM python:3.12-slim
```


```Dockerfile 
FROM nginx:alpine
```


```Dockerfile 
FROM golang:1.24-alpine
```


DevOps tip: Choose a trusted, maintained base image and use a deliberate version. Smaller images can reduce download time and attack surface, but don't sacrifice compatibility or security.

**B. WORKDIR — Setting the working directory**

Example:
```Dockerfile
WORKDIR /app
```


This creates /app if necessary and sets it as the working directory for subsequent instructions.

Instead of executing commands all over the filesystem, you work inside /app.

For example:
```Dockerfile
FROM node:22-alpine

WORKDIR /app
```


Subsequent commands will use /app as their working directory unless you specify another location.
**C. COPY — Copying application files**

Example:
```Dockerfile 
COPY . .
```
This copies files from your build context into the image.

The syntax is:
```Dockerfile
COPY <source> <destination>
```
For example:
```Dockerfile
COPY package.json .
```

Copies package.json into the current working directory (/app).

Another example:
```Dockerfile
COPY src/ ./src/
```
Copies your local src directory into /app/src.

**D. RUN — Executing commands during image building**
Example:
```Dockerfile
RUN npm install
```

This executes a command while building the image.

Other examples:
```Dockerfile
RUN apt-get update
```
```Dockerfile
RUN pip install -r requirements.txt
```
```Dockerfile
RUN npm ci
```
Important distinction:
- RUN executes during image building.
- CMD executes when a container starts.
We'll explore CMD shortly.

**E. ENV — Setting environment variables**
Example:
```Dockerfile
ENV NODE_ENV=production
```

This sets an environment variable inside the image, which is available to containers created from it.

Another example:
```Dockerfile
ENV PORT=3000
```

Your application can access this variable at runtime.

Be careful: don't put passwords, API keys, or other secrets directly into Dockerfiles using ENV or ARG. Use runtime secret management instead.

**F. ARG — Build-time variables**
Example:
```Dockerfile
ARG APP_VERSION=1.0.0
```

ARG defines a variable available during the image build.

You can override it:
```Dockerfile
docker build --build-arg APP_VERSION=2.0.0 -t myapp:v2 .
```

The important difference:
| ARG |	ENV |
| :--- | ---: |
| Primarily build-time | Runtime environment |
| Can be overridden during build | Can be overridden when running container |
| Not automatically available at runtime | Available in the container |
| Not suitable for secrets | Not suitable for secrets |

**G. EXPOSE — Documenting application ports**

Example:
```Dockerfile
EXPOSE 3000
```

This documents that the application listens on port 3000.

Important: EXPOSE does not publish the port to your host machine.

To publish it:
```Dockerfile
docker run -d -p 3000:3000 myapp:v1
```

Remember:
```Dockerfile
-p HOST_PORT:CONTAINER_PORT
```

**H. CMD — Default container startup command**

Example:
```Dockerfile
CMD ["npm", "start"]
```

This specifies the default command executed when a container starts.

Another example:
```Dockerfile
CMD ["python", "app.py"]
```

CMD can be overridden when you run the container.

For example:
```Dockerfile
docker run myapp:v1 npm run test
```

**I. ENTRYPOINT — Defining the main executable**

Example:
```Dockerfile
ENTRYPOINT ["python", "app.py"]
```

ENTRYPOINT defines the main executable for the container.

Unlike CMD, it is not normally replaced simply by providing another command to docker run.

For example:
```Dockerfile
ENTRYPOINT ["python", "app.py"]

CMD ["--port", "3000"]
```


Running:
```Dockerfile
docker run myapp:v1 --port 5000
```
Executes:
```Dockerfile
python app.py --port 5000
```

**CMD vs ENTRYPOINT**

This is a very popular Docker interview question.
|CMD	ENTRYPOINT |
Provides default command or arguments	Defines the main executable
Easily overridden by docker run command	Normally remains in place
Often used for default arguments	Often used for the main application

Professional practice: The exec form using JSON arrays is generally preferred for application startup because it handles signals and process management more correctly.
J. USER — Running containers without root
Example:
USER node


This tells Docker to run subsequent build instructions and the container's default process as the specified user.

Why does this matter?

Running applications as root increases the impact of a potential compromise.

In production, you should generally use a non-root user unless there is a specific reason not to.