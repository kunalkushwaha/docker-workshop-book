Lets run something serious now!
How about ubuntu?

  ```
  $ docker run -it ubuntu bash

  ```

  - __Docker Container__ 

  When you use the docker run CLI command or the equivalent API, the Docker Engine client instructs the Docker daemon to run a container. This example tells the Docker daemon to run a container using the ubuntu Docker image, to remain in the foreground in interactive mode (-i), and to run the /bin/bash command.

  ``$ docker run -i -t ubuntu /bin/bash``

  When you run this command, Docker Engine does the following:

    - ``Pulls the ubuntu image``: Docker Engine checks for the presence of the ubuntu image. If the image already exists locally, Docker Engine uses it for the new container. Otherwise, then Docker Engine pulls it from Docker Hub.
    - ``Creates a new container``: Docker uses the image to create a container.
    - ``Allocates a filesystem and mounts a read-write layer``: The container is created in the file system and a read-write layer is added to the image.
    - ``Allocates a network / bridge interface``: Creates a network interface that allows the Docker container to talk to the local host.
    - ``Sets up an IP address``: Finds and attaches an available IP address from a pool.
    - ``Executes a process that you specify``: Executes the /bin/bash executable.
    - ``Captures and provides application output``: Connects and logs standard input, outputs and errors for you to see how your application is running, because you requested interactive mode.
    - Your container is now running. You can manage and interact with it, use the services and applications it provides, and eventually stop and remove it.

    -  To see running containers

      ```
      $ docker ps
      ```

    - To see all containers including exited and created.

    ```
    $ docker ps -a
    ```

    - To create container in background mode.

    ```
    $ docker run -itd --name <container-name> <image-name> <command>
    $ docker ps
    $ docker exec -it <container-name| id>
    ```

      - Never run sshd in docker container. ``exec`` command enters into container namespace and runs the command.
      - Always use this.

    - To Stop container

    ```
    $ docker stop <container-id>
    ```

    - To start the old container again.

    ```
    $ docker start <container-id>
    ```

    - To remove container

    ```
    docker rm <container-id>
    ```

    So What we saw is how to run containers and do other operations in container.