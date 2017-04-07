
In hello-world section, we created a container, which prints some messages.

Lets run something serious now!<br>
How about running lightweight [linux distro ``alpine``](https://alpinelinux.org/)?

Irrespective of what OS you are running on host, we will run an ``alpine`` container.

  ```
  $ docker run -i -t alpine sh

  ```

  - __Docker Container__

  When you use the ``docker run`` CLI command, the Docker Engine client instructs the Docker daemon to run a container. This example tells the Docker daemon to run a container using the ubuntu Docker image, to remain in the foreground in interactive mode (-i), and to run the /bin/bash command.

  ```
  $ docker run -i -t alpine sh
  / #
  ```

  But this command does lot of magic in background. Lets understand what things it did after command if run.
  When you run this command, Docker Engine does the following:

    1. ``Pulls the alpine image``: Docker Engine checks for the presence of the ubuntu image. If the image already exists locally, Docker Engine uses it for the new container. Otherwise, then Docker Engine pulls it from Docker Hub.
    - ``Creates a new container``: Docker uses the image to create a container i.e. setting up [namespaces](http://man7.org/linux/man-pages/man7/namespaces.7.html) , [cgroups](http://man7.org/linux/man-pages/man7/cgroups.7.html) and applying [seccomp](http://man7.org/linux/man-pages/man2/seccomp.2.html) profile etc.
    - ``Allocates a filesystem and mounts a read-write layer``: The container is created in the file system and a read-write layer is added to the image.
    - ``Allocates a network / bridge interface``: Creates a network interface that allows the Docker container to talk to the local host.
    - ``Sets up an IP address``: Finds and attaches an available IP address from a pool.
    - ``Executes a process that you specify``: Executes the /bin/bash executable.
    - ``Captures and provides application output``: Connects and logs standard input, outputs and errors for you to see how your application is running, because you requested interactive mode.

    Your container is now running. You can manage and interact with it, use the services and applications it provides, and eventually stop and remove it.

    Since we have already got the container prompt, you can try some commands

    ```
    $ docker run -i -t alpine sh
    / # ls
    bin    dev    etc    home   lib    media  mnt    proc   root   run    sbin   srv    sys    tmp    usr    var
    / # cat /etc/hostname
    34375b48549e
    / # ifconfig
    eth0      Link encap:Ethernet  HWaddr 02:42:AC:11:00:02  
              inet addr:172.17.0.2  Bcast:0.0.0.0  Mask:255.255.0.0
              inet6 addr: fe80::42:acff:fe11:2/64 Scope:Link
              UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
              RX packets:43 errors:0 dropped:0 overruns:0 frame:0
              TX packets:8 errors:0 dropped:0 overruns:0 carrier:0
              collisions:0 txqueuelen:0
              RX bytes:5305 (5.1 KiB)  TX bytes:648 (648.0 B)

    lo        Link encap:Local Loopback  
              inet addr:127.0.0.1  Mask:255.0.0.0
              inet6 addr: ::1/128 Scope:Host
              UP LOOPBACK RUNNING  MTU:65536  Metric:1
              RX packets:0 errors:0 dropped:0 overruns:0 frame:0
              TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
              collisions:0 txqueuelen:1
              RX bytes:0 (0.0 B)  TX bytes:0 (0.0 B)
    / # exit
    ```

    You can see, we tried to see hostname, printed the network information etc, as we can do on installed alpine OS.
    with ``exit`` command, you can exit the container.

    > _NOTE: you may observe if instead of alpine, you use ubuntu, fedora etc image, they do not have much binaries installed like ``ip`` , ``ifconfig``, ``vim`` etc.
    Since, ``ubuntu``, ``fedora`` images is used by many developers as base image to package their application, these image are strip-down version to make container image size lean._


- To manage containers, we need to see status and listing of all containers.
    -  To see running containers, you can simply use ``docker ps`` command.

      ```
      $ docker ps
      CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
      ```
      You can see there is no running containers right now on system, as we exit from alpine container.

    - To see all containers including exited and created, we can use ``docker ps -a`` command.

    ```
    $ docker ps -a
    CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                        PORTS               NAMES
    34375b48549e        alpine              "sh"                19 minutes ago      Exited (0) 9 minutes ago                          musing_feynman
    b2cca4a26312        hello-world         "/hello"            45 minutes ago      Exited (0) 45 minutes ago                         distracted_mcnulty
    ```
    Here you can see, we have two containers, from images hello-world & alpine. The status of both containers are ``Exited``.
    - Container names are automatically generated, if not mentioned in ``docker run`` command.
    - A container is identified by ``name`` or ``container id``, So you can perform an operation on container using either.

- __To create container in background mode.__<br>
  Most of times, we need to run the containers in background. Think, running your webservice or microservices of applications in containers.<br>
  To run in background mode, use option ``-d`` in ``docker run``.

  ``docker run -itd --name <container-name> <image-name> <command>``

   Lets run a container in background.

    ```
    $ docker run -itd --name test alpine top
    7c69dd806a5c4a042530caa600ebcc2c9b865129123475538dc71ccdf8a78056
    ```

    You see, it prints a 64 digit hex string, This is basically Container id assigned to your container.

    Now you can see status of your newly created container by ``docker ps`` command.

    ```
    $ docker ps
    CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
    7c69dd806a5c        alpine              "top"               9 seconds ago       Up 8 seconds                            test
    ```

    As you can see our container named "test" is running with status "Up".<br>
    One more thing you will observe, ``container id`` shown is output have only 12 digit hex  , which is first 12 digits of 64 digit hex string.<br>
    Normally that is sufficient to identify container on single host.

    __Connecting to a running container__<br>
    On VMs, normally we use ``ssh`` to connect to running instance, but in containers ``ssh`` is not required.<br>
    There are two main reasons for not connecting docker containers through ``ssh``

    1. Docker containers ideally run single application in one container.<br>
       If you are running ``sshd`` along with application, docker may not notify you if ``sshd`` got stopped or killed.
    2. Docker containers are created using [namespaces](http://man7.org/linux/man-pages/man7/namespaces.7.html), So it is possible to share namespaces, using which we can enter containers.

    Docker provides ``docker exec`` command to enter into the running containers.

    ``$ docker exec -it <container-name| id>``

    So lets try to enter our running container.

    ```
    $ docker exec -it test sh
    / # ps
    PID   USER     TIME   COMMAND
        1 root       0:00 top
        7 root       0:00 sh
       13 root       0:00 ps
    / #
    ```
    As you can see in above example, using ``docker exec``, we were able to get into container. <br>
    Also, ``ps`` command inside has shown the ``top`` command with ``PID 1``.

    > This is very important to understand, while creating container whatever application is passed as argument to image, runs as ``PID 1``. Docker daemon monitor PID1 process in container, and if it stops, it marks and stops the container, irrespective of if other processes are running inside the container.

    __Output of Container__<br>
    To see output of Container's running in background, you can use ``docker logs`` command. All output that was supposed to redirected to stdout/stderr will be logged in a file by default.

    ```
    $ docker logs test
    <snip...>
  Load average: 0.62 0.34 0.27 4/726 13
    PID  PPID USER     STAT   VSZ %VSZ CPU %CPU COMMAND
      1     0 root     R     1536   0%   2   0% top
      7     0 root     S     1536   0%   3   0% sh
  Mem: 6056220K used, 6229260K free, 63920K shrd, 646596K buff, 2214516K cached
  CPU:   1% usr   0% sys   0% nic  97% idle   0% io   0% irq   0% sirq
  Load average: 0.57 0.33 0.27 3/709 13
    PID  PPID USER     STAT   VSZ %VSZ CPU %CPU COMMAND
    ```


- __Stop Container__<br>
    To Stop a running container, use ``docker stop <container-id> or <name>``


    ```
    $ docker stop test
    test
    $ docker ps -a
    CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                        PORTS               NAMES
    7c69dd806a5c        alpine              "top"               About an hour ago   Exited (143) 10 seconds ago                       test
    b2cca4a26312        hello-world         "/hello"            4 hours ago         Exited (0) 4 hours ago                            distracted_mcnulty
    ```
    You can verify if container is stopped by ``docker ps -a`` command.



- __Start a (stopped) container again__
    Just like VM, you can start a stopped container. Use ``docker start`` command.

    ``$ docker start <container-id>``

    Lets start our test container and try to understand what happens.
    ```
    $ docker start test
    test
    $ docker ps        
    CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
    7c69dd806a5c        alpine              "top"               About an hour ago   Up 3 seconds                            test
    ```

    Here, as you can see, we have started a container ``test``, whose status was ``Exited`` earlier.<br>
    Now, the status has changed to ``UP``. ``Creation`` time is still same, as now we have just restarted it.<br>
    You will also realize, name and ID has not changed.<br>

    So this is just like other machine or VM, all properties are retained.


- __Removing a container__<br>
    To remove a container, it is necessary, that container is not running.
    ``docker rm`` command can be used to remove a container.

    ```
    $ docker stop test
    test
    $ docker rm test    
    test
    $ docker ps -a
    CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                     PORTS               NAMES
    b2cca4a26312        hello-world         "/hello"            4 hours ago         Exited (0) 4 hours ago                         distracted_mcnulty
    ```

    So in this section we saw, how to run containers and do various operations on container.<br>
--------------
__Assignment__

1. Create a ubuntu container in interactive mode with name "ubuntu".
    - Type hostname.
    - exit from container.
    - delete the container.! (_hint: You may require to do something before this?_)

2. create a fedora container in background mode with name "fedora" with command ``top``.
    - print the hostname of "fedora" container using ``docker exec`` command. (_hint: No need to enter into container using ``sh/bash``_)
    - stop the container.
    - Check output of container using ``docker logs``.
-------------------------------

It is highly recommended to explore other commands that docker offer for container manipulation/management.<br>
You can find all such commands by typing ``$ docker container --help``, Also explore all options of ``docker run`` command.

We will explore such commands and options later in this workshop.
