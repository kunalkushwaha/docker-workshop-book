#### Docker Images  

  Lets explore little more docker container.<br>
  This time, we will create a ubuntu container and try to create files inside it.

  Follow these steps on your docker container
  - Try ``ls``, ``whoami``, or any ubuntu command and see the result.
  - Now lets create a file in ``/tmp/myfile.txt``
  - Check it if it is created. You may write something to it.
  - Exit.

  You may see some similar output as below.
    ```
    $ docker run -it ubuntu bash
    root@e9a1bdd88483:/# ls
    bin  boot  dev  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
    root@e9a1bdd88483:/# whoami
    root
    root@e9a1bdd88483:/# touch /tmp/myfile.txt
    root@e9a1bdd88483:/# ls /tmp/myfile.txt
    /tmp/myfile.txt
    root@e9a1bdd88483:/# echo "Hello from shell" >> /tmp/myfile.txt
    root@e9a1bdd88483:/# cat /tmp/myfile.txt
    Hello from shell
    root@e9a1bdd88483:/# exit
    exit
    ```

  - Lets create again container ``$ docker run -it ubuntu bash``
  - Can you see the file ``/tmp/myfile``?
  - Why this file is not visible, which we created ?

  ```
  $ docker run -it ubuntu bash
  root@7f4fea5899e6:/# ls /tmp/myfile.txt
  ls: cannot access '/tmp/myfile.txt': No such file or directory
  root@7f4fea5899e6:/# exit
  exit
  ```

  To understand this lets understand, How docker image and containers works first.

##### Docker Image
 Docker images are read-only templates from which Docker containers are instantiated. <br>
 Each image consists of a series of layers. Docker uses [union file systems](https://en.wikipedia.org/wiki/UnionFS) to combine these layers into a single image. Union file systems allow files and directories of separate file systems, known as branches, to be transparently overlaid, forming a single coherent file system.

e.g. How unionFS works
```
$ tree
.
├── base
│   └── base.txt
├── layer1
│   └── layer1.txt
├── layer2
│   └── layer2.txt
└── mount

4 directories, 3 files

$ sudo mount -t aufs -o  br=./base:./layer1:./layer2 none ./mount

$ tree                                                           
.
├── base
│   └── base.txt
├── layer1
│   └── layer1.txt
├── layer2
│   └── layer2.txt
└── mount
    ├── base.txt
    ├── layer1.txt
    └── layer2.txt

4 directories, 6 files
```
You can see in above example, how easily multiple folders can be merged in single filesystem view using unionFS.
 - In example, we used [AUFS](http://aufs.sourceforge.net/aufs.html).

![Docker Image](./images/image-layers.jpg)
A Docker image representation

__Docker File System___:
_TODO_

__Image to Container__:
_TODO_

__IMAGE_SHARING__:
_TODO_

__CAS__:
_TODO_

__COPY_ON_WRITE__:
_TODO_

Now coming back to our example with docker images.
We have two containers, both with status ``Exited``.

```
$ docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                      PORTS               NAMES
7f4fea5899e6        ubuntu              "bash"              9 minutes ago       Exited (1) 9 minutes ago                        epic_colden
e9a1bdd88483        ubuntu              "bash"              10 minutes ago      Exited (0) 10 minutes ago                       distracted_aryabhata
```

Here, we have added our file in container which was create first i.e. id ``e9a1bdd88483``.

So we will create an image from this container. Docker provides ``docker commit`` command to create image from container.
After commit, all changes made in this container will be saved on this image.

```
$ docker commit -m "New file added" e9a1bdd88483 ubuntu:myfile
sha256:272916a272c42c389c4da448bf5cfa37d349090797462a2b0f69e5d4fae87f12
```

As you can see in above example, on successful commit command, an ``sha256:<64-digit-hex-string>`` is printed.
This is sha256 digest for new layer and also name of layer.

So lets check if new image is created on our system with ``docker images`` command.

```
$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
ubuntu              myfile              272916a272c4        12 minutes ago      130 MB
ubuntu              latest              0ef2e08ed3fa        5 weeks ago         130 MB
```

Here, in above example, you can see, now one more ubuntu image is created, with tag: ``myfile``. This tag is added as during commit , we specified image name as ``ubuntu:myfile``. An image name is ``<name:tag>``.
- default tag is ``latest`` i.e. if no tag is specified, docker resolves to ``<image-name>:latest``.
- Also, ``image-id`` is same as sha256 digest.

On printing history of new image, you can find the new layer added with comment passed as commit message.
Rest of layers are exactly same as ubuntu image.
- So even though a new image is created, but on disk, only layer of size 109 bytes are added and rest of the layers are share from ``ubuntu:latest`` image.

```
$ docker history ubuntu:myfile
IMAGE               CREATED             CREATED BY                                      SIZE                COMMENT
272916a272c4        12 minutes ago      bash                                            109 B               New file added
0ef2e08ed3fa        5 weeks ago         /bin/sh -c #(nop)  CMD ["/bin/bash"]            0 B                 
<missing>           5 weeks ago         /bin/sh -c mkdir -p /run/systemd && echo '...   7 B                 
<missing>           5 weeks ago         /bin/sh -c sed -i 's/^#\s*\(deb.*universe\...   1.9 kB              
<missing>           5 weeks ago         /bin/sh -c rm -rf /var/lib/apt/lists/*          0 B                 
<missing>           5 weeks ago         /bin/sh -c set -xe   && echo '#!/bin/sh' >...   745 B               
<missing>           5 weeks ago         /bin/sh -c #(nop) ADD file:efb254bc677d66d...   130 MB    
```

Now since, image is created, we can create a container from new image and see if we can find our file or not.

```
$ docker run -it ubuntu:myfile bash
root@0c89fb3e855c:/# cat /tmp/myfile.txt
Hello from shell
root@0c89fb3e855c:/#
```

Yes, We got our file from new image.<br>
But this not limited to creating file only. You can install any software package also.

Lets learn little more with our Assignment for this section.

-----------------------------------
__Assignments:__
1. Create an ubuntu container.
 - install ``curl`` or/and ``lynx``.
 - browse google.com.
 - exit & save the image name ``ubuntu:web.client``
 - Create new container with image ``ubuntu:web.client`` and try to browse web.

2. create an ubuntu container
 - install ``iproute2`` package.
 - print the ip address of container.

-----------------------------

- But this is not great way of building images right?
  - We have already evolved to use config management tools to configure OS and software, this is like going back in history.
  - So docker allows to automate the image building using ``Dockerfile``.
