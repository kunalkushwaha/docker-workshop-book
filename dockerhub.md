#### Docker-hub 

  - Docker Hub is a cloud-based registry service.
  - You can build your images and test them, stores manually pushed images.
  - It provides a centralized resource for container image discovery, distribution and change management, user and team collaboration, and workflow automation throughout the development pipeline.
  - It also allows you to link to code repositories, build your images and test them, stores manually pushed images, and links to Docker Cloud so you can deploy images to your hosts.


  - You can search for and pull Docker images from Hub without logging in, however to push images you must log in.
  - You can find public repositories and images from Docker Hub in two ways.
    - You can “Search” from the Docker Hub website.
    - use CLI ``docker search <image-name>``
  - Docker Hub contains a number of Official Repositories.
    - These are public, certified repositories from vendors and contributors to Docker.
    - They contain Docker images from vendors like Canonical, Oracle, and Red Hat that you can use as the basis to building your applications and services.

With Official Repositories you know you’re using an optimized and up-to-date image that was built by experts to power your applications.

  - Docker Hub provides the following major features:

    - ``Image Repositories`` : Find, manage, and push and pull images from community, official, and private image libraries.
    - ``Automated Builds`` : Automatically create new images when you make changes to a source code repository.
    - ``Webhooks`` : A feature of Automated Builds, Webhooks let you trigger actions after a successful push to a repository.
    - ``Organizations`` : Create work groups to manage access to image repositories.
    - ``GitHub and Bitbucket Integration`` : Add the Hub and your Docker Images to your current workflows.

  - ``Docker commands and Docker Hub``
    - Docker itself provides access to Docker Hub services via the docker search, pull, login, and push commands.

  - Commands.
    - docker pull
    - docker search
    - docker login
    - docker push