1. What is a Docker Image?
A Docker image is a read-only template used to create Docker containers.

Think about it this way:
- Docker Image = Blueprint of a house.
- Docker Container = Actual house built from that blueprint.
An image contains everything an application needs to run, including:
- Application code.
- Runtime (Node.js, Python, Java, etc.).
- System libraries.
- Dependencies.
- Configuration.
- Default startup command.
** For example, if you have a Node.js application, its Docker image could contain: **
```Node.js runtime
Application source code
package.json
node_modules
Environment configuration
Startup command
```


When you run that image, Docker creates a container.

Remember: An image is not a running application. A container is the running instance of an image.


| Header 1 | Header 2 | Header 3 |
| :--- | :----: | ---: |
| Left aligned text | Centered text | Right aligned text |
| Item A | Item B | Item C |
