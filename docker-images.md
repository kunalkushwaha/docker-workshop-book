#### Docker Images  

  Now what has happening here. We have got a shell in ubuntu system.
  - Try ``ls``, ``whoami``, or any ubuntu command and see the result.
  - Now lets create a file in ``/tmp/myfile.txt``
  - Check it if it is created. You may write something to it.
  - Exit.
  - Lets create again container ``$ docker run -it ubuntu bash``
  - Can you see the file?
  - Why this file is not visible, which we created ?
  - To understand this lets try to understand, How docker image and containers works?


  - Lets type ``docker images``
  - You can see your ubuntu image.
  - Check history

```
$ docker history ubuntu
IMAGE               CREATED             CREATED BY                                      SIZE                COMMENT
104bec311bcd        3 weeks ago         /bin/sh -c #(nop)  CMD ["/bin/bash"]            0 B
<missing>           3 weeks ago         /bin/sh -c mkdir -p /run/systemd && echo '...   7 B
<missing>           3 weeks ago         /bin/sh -c sed -i 's/^#\s*\(deb.*universe\...   1.9 kB
<missing>           3 weeks ago         /bin/sh -c rm -rf /var/lib/apt/lists/*          0 B
<missing>           3 weeks ago         /bin/sh -c set -xe   && echo '#!/bin/sh' >...   745 B
<missing>           3 weeks ago         /bin/sh -c #(nop) ADD file:7529d28035b43a2...   129 MB
```

NOTE:  Under the content addressable storage model introduced with Docker 1.10, image history data is no longer stored in a config file with each image layer. It is now stored as a string of text in a single config file that relates to the overall image. This can result in some image layers showing as “missing” in the output of the docker history command. This is normal behaviour and can be ignored.


```
$ docker ps -a
CONTAINER ID        IMAGE                   COMMAND                  CREATED             STATUS                        PORTS               NAMES
d62dd55defd3        ubuntu                  "bash"                   30 minutes ago      Exited (2) 30 minutes ago                         zen_murdock
0340d8a393cf        ubuntu                  "bash"                   47 minutes ago      Exited (127) 31 minutes ago                       vibrant_euclid

$ docker commit -m "New file added" 0340d8a393cf ubuntu:myfile
sha256:b71d4ce94b98d7129980bec598bccfaec2d082d07a1515925d81073504b2c214

$ docker images
REPOSITORY                        TAG                  IMAGE ID            CREATED                  SIZE
ubuntu                            myfile               b71d4ce94b98        Less than a second ago   168 MB

$ docker history ubuntu:myfile
IMAGE               CREATED             CREATED BY                                      SIZE                COMMENT
b71d4ce94b98        2 seconds ago       bash                                            39.5 MB             New file added
104bec311bcd        3 weeks ago         /bin/sh -c #(nop)  CMD ["/bin/bash"]            0 B
<missing>           3 weeks ago         /bin/sh -c mkdir -p /run/systemd && echo '...   7 B
<missing>           3 weeks ago         /bin/sh -c sed -i 's/^#\s*\(deb.*universe\...   1.9 kB
<missing>           3 weeks ago         /bin/sh -c rm -rf /var/lib/apt/lists/*          0 B
<missing>           3 weeks ago         /bin/sh -c set -xe   && echo '#!/bin/sh' >...   745 B
<missing>           3 weeks ago         /bin/sh -c #(nop) ADD file:7529d28035b43a2...   129 MB
```

- But this is not great way of building images right?
  - We have already evolved to use config management tools to configure OS and software, this is like going back in history.
  - So docker allows to automate the image building using ``Dockerfile``.