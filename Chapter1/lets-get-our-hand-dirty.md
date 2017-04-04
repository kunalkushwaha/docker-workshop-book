#### Build you first image.
- Image for a container that should print "Hello Docker"
- Expected output.
  ```
  $ ls
  Dockerfile
  $ docker build -t first-image .
  $
  $ # first-image:latest should be visible in output
  $ docker images
  $
  $ docker run first-image
  Hello World

  ```

#### Lets add some fun 
- Modify the Dockerfile for following.
   - On image creation only ``echo`` command should get executed.
   - By default it should output "How can I help you?"
   - "How can I help you?" should be overridden by any starting that is passed as argument to ``docker run`` command.
   
- Expected Output

```
$ ls
Dockerfile
$ docker build -t first-image:v1 .
$ docker images
$ docker run first-image:v1
How can I help you?
$ docker run first-image:v1 Bingo!!
Bingo!!
```

#### Lets create something useful.
__How about building a webserver__

- Take base image as ubuntu.
- install nginx
- Create container to test if image have nginx running.
- Expected output

```
$ ls
Dockerfile
$ docker build -t nginx-image:v1 .
$ docker images
$ docker run -itd -p 5000:80 nginx-image:v1
$ curl localhost:5000
```
  