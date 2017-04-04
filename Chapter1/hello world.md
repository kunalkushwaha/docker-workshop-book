- Lets Run the ``docker run hello-world`` container again.  

  ```
  $ docker run hello-world
  To generate this message, Docker took the following steps:
  1. The Docker client contacted the Docker daemon.
  2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
  3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
  4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.
  ```

  This Output talks about few things like Docker Client, Docker Daemon, Image, container etc. Lets try to understand this in more details.


  ![Docker Architecture](./images/docker-architecture.svg)

- Docker is designed as client - server program, So we have one daemon process and one client.

  - __The Docker daemon__ : The Docker daemon runs on a host machine & responsible for managing all containers on one host
    - The user uses the Docker client to interact with the daemon.
    - It understands REST API's, so can be managed from outside of host too.
  - __The Docker client__ : The Docker client, in the form of the docker binary, is the primary user interface to Docker.
    -  It accepts commands and configuration flags from the user and communicates with a Docker daemon.
    - One client can even communicate with multiple unrelated daemons.


  - __Docker images__ : A Docker image is a read-only template with instructions for creating a Docker container.
    - For example, an image might contain an Ubuntu operating system with Apache web server and your web application installed.
    - So in simple words, _Docker Image is on disk representation of dockerized application_
    - This is __Build__ component of docker.


  - __Docker containers__ : A Docker container is a runnable instance of a Docker image.
      - You can run, start, stop, move, or delete a container using Docker API or CLI commands.
      - When you run a container, you can provide configuration metadata such as networking information or environment variables.
      - This is __Run__ component of docker.


  - __Docker registries__ : A docker registry is a library of images.
      - A registry can be public or private, and can be on the same server as the Docker daemon or Docker client, or on a totally separate server
      - This is __Ship__ component of docker.

  Now we have basic understanding of docker vocabulary, lets learn basic docker commands in next section. 
