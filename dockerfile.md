  - Dockerfile is a script, composed of various commands and arguments listed successively to automatically perform actions on a base image in order to create a new one.
  - Dockerfiles begin with defining an image FROM which the build process starts.
     - It is followed by commands with arguments, to perform various actions.

  - Dockerfile have their own syntax, docker daemon understands, and it uses it for interpreting the actions.
    - Dockerfile syntax consists of two kind of main line blocks: comments and commands + arguments.

    ```
    # Line blocks used for commenting
    command argument argument ..
    ```
    e.g

    ```
    # Print "Hello docker!"
    RUN echo "Hello docker!"
    ```

    Few important and frequently used Dockerfile Commands.
    - __FROM__ :
      - This is probably the most crucial amongst all others for Dockerfiles
      - It defines the base image to use to start the build process.
      - If a FROM image is not found on the host, docker will try to find it (and download) from the docker image index.
      - It needs to be the first command declared inside a Dockerfile.

      e.g

      ```
      # Usage: FROM [image name]
      FROM ubuntu
      ```

    -  __ENV__ :
      - The ENV command is used to set the environment variables (one or more).
      - These variables consist of “key = value” pairs, which can be accessed within the container by scripts and applications alike.
      - This functionality of docker offers an enormous amount of flexibility for running programs.

      e.g.

      ```
      # Usage: ENV key value
      ENV HTTP_PROXY "http://example.com:8080"
      ENV SERVER_WORKS 4
      ```

    - __RUN__ :
      - The RUN command is the central executing directive for Dockerfiles.
      - It takes a command as its argument and runs it to form the image.
      - It is used to build the image (forming another layer on top of the previous one which is committed).

      e.g.

      ```
      # Usage: RUN [command]
      RUN apt-get install -y httpd
      RUN mkdir -p /opt/myapp
      RUN echo "version=1" > /opt/myapp/myapp.config
      ```

    - __ADD__ :
      - The ADD command gets two arguments: a source and a destination.
      - It basically copies the files from the source on the host into the container's own filesystem at the set destination.
      - If, however, the source is a URL (e.g. http://github.com/user/file/), then the contents of the URL are downloaded and placed at the destination.

      e.g.

      ```
      # Usage: ADD [source directory or URL] [destination directory]
      ADD /my_app_folder /my_app_folder
      ```

    - __VOLUME__ :
      - The VOLUME command is used to enable access from your container to a directory on the host machine (i.e. mounting it).

      e.g.

      ```
      # Usage: VOLUME ["/dir_1", "/dir_2" ..]
      VOLUME ["/home/kunal/app1"]
      ```

    - __EXPOSE__
      - The EXPOSE command is used to associate a specified port to enable networking between the running process inside the container and the outside world (i.e. the host).

      e.g.

      ```
      # Usage: EXPOSE [port]
      EXPOSE 8080
      ```

    - __WORKDIR__
      - The WORKDIR directive is used to set where the command defined with CMD is to be executed.

      e.g.

      ```
      # Usage: WORKDIR /path
      WORKDIR ~/
      ```

    - __CMD__ :

      - The command CMD, similarly to RUN, can be used for executing a specific command. However, unlike RUN it is not executed during build, but when a container is instantiated using the image being built. Therefore, it should be considered as an initial, default command that gets executed (i.e. run) with the creation of containers based on the image.

      - To clarify: an example for CMD would be running an application upon creation of a container which is already installed using RUN (e.g. RUN apt-get install …) inside the image. This default application execution command that is set with CMD becomes the default and replaces any command which is passed during the creation.

      e.g

      ```
      # Usage 1: CMD application "argument", "argument", ..
      CMD "echo" "Hello docker!"
      ```

    - __ENTRYPOINT__ :
      - This sets the concrete default application that is used every time a container is created using the image.
      - For example, if you have installed a specific application inside an image and you will use this image to only run that application, you can state it with ENTRYPOINT and whenever a container is created from that image, your application will be the target.

      - If you couple ENTRYPOINT with CMD, you can remove "application" from CMD and just leave "arguments" which will be passed to the ENTRYPOINT.

      e.g.

      ```
      # Usage: ENTRYPOINT application "argument", "argument", ..
      # Remember: arguments are optional. They can be provided by CMD
      #           or during the creation of a container.
      ENTRYPOINT echo

      # Usage example with CMD:
      # Arguments set with CMD can be overridden during *run*
      CMD "Hello docker!"
      ENTRYPOINT echo
      ```