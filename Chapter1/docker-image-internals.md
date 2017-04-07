#### Docker Image internals


_NOTE: Optional Section_

__Docker Image on Disk__

Now lets understand how docker uses unionFS for its image management.<br>
Lets dissect the ``ubuntu`` image and try to understand docker image structure.

We have ubuntu image on our docker host. Type and see output of ``docker history ubuntu``.
This command outputs the history of an image and also each line represent various layers used in this image.
If a layer size is 0, it won't have any representation on disk.

History also tells, on any changes made to image, new layer is added, So essentially the image is read-only and any changes results into new layer to image.

You will see similar output as below.

```
$ docker history ubuntu
IMAGE               CREATED             CREATED BY                                      SIZE                COMMENT
0ef2e08ed3fa        5 weeks ago         /bin/sh -c #(nop)  CMD ["/bin/bash"]            0 B                 
<missing>           5 weeks ago         /bin/sh -c mkdir -p /run/systemd && echo '...   7 B                 
<missing>           5 weeks ago         /bin/sh -c sed -i 's/^#\s*\(deb.*universe\...   1.9 kB              
<missing>           5 weeks ago         /bin/sh -c rm -rf /var/lib/apt/lists/*          0 B                 
<missing>           5 weeks ago         /bin/sh -c set -xe   && echo '#!/bin/sh' >...   745 B               
<missing>           5 weeks ago         /bin/sh -c #(nop) ADD file:efb254bc677d66d...   130 MB                  
```

To view further details of each layer, we will run ``docker inspect`` command.

```
$ docker inspect ubuntu
[
    {
        "Id": "sha256:0ef2e08ed3fabfc44002ccb846c4f2416a2135affc3ce39538834059606f32dd",
        "RepoTags": [
            "ubuntu:latest"
        ],
        .
        <snip>
        .
        .
        "RootFS": {
            "Type": "layers",
            "Layers": [
                "sha256:745f5be9952c1a22dd4225ed6c8d7b760fe0d3583efd52f91992463b53f7aea3",
                "sha256:85782553e37a2998422ecb14fb34ac3fda94dbc90c6630d721a3bcc770939946",
                "sha256:29660d0e5bb2bae1d415f5638fa6011ab4063d1c0895e889d51ad365186d1995",
                "sha256:440e02c3dcde277c7426c07c6e240a40b1e53da4a8a0cc22a8cecd4e6f419a98",
                "sha256:56827159aa8b327a1b15c2102040ee87f3ca0bf8285aab00a1286e8af79a4beb"
            ]
        }
    }
]
```

Here, lot of information is dumped to screen in JSON format.
But currently only two information are of our interest.

1. __"Id"__ : ID is a digest, which contains an SHA256 hash of the image's JSON configuration object (We will cover this later)
    - So far you can relate to first entry in ``docker history`` output.
2. __"Layers"__ : This section actually gives information about all layers using which the image is build.
    - As you can see from history, only four layers have size more then ``0``, hence four layers.
    - These layers are named as sha256 hash digest for contents inside the layer.

To look at the image structure on disk, you can take look at ``/var/lib/docker/image/aufs/imagedb`` & ``/var/lib/docker/image/aufs/layerdb``.
- Here, content folder have image configuration with name same as ``image ID``.
- All layers in tar format will be present at ``layerdb`` folder.
Whenever an image is download, these are the places, where contents will be copied first.

```
$ sudo tree  /var/lib/docker/image/aufs/imagedb
/var/lib/docker/image/aufs/imagedb
|-- content
|   `-- sha256
|       `-- 0ef2e08ed3fabfc44002ccb846c4f2416a2135affc3ce39538834059606f32dd
`-- metadata
    `-- sha256

4 directories, 1 file
```

```
docker@testbox:~$ sudo tree  /var/lib/docker/image/aufs/layerdb

/var/lib/docker/image/aufs/layerdb
|-- sha256
|   |-- 0d58a35162057295d273c5fb8b7e26124a31588cdadad125f4bce63b638dddb5
|   |   |-- cache-id
|   |   |-- diff
|   |   |-- parent
|   |   |-- size
|   |   `-- tar-split.json.gz
|   |-- 745f5be9952c1a22dd4225ed6c8d7b760fe0d3583efd52f91992463b53f7aea3
|   |   |-- cache-id
|   |   |-- diff
|   |   |-- size
|   |   `-- tar-split.json.gz
|   |-- b53cd3273b78f7f9e7059231fe0a7ed52e0f8e3657363eb015c61b2a6942af87
|   |   |-- cache-id
|   |   |-- diff
|   |   |-- parent
|   |   |-- size
|   |   `-- tar-split.json.gz
|   |-- cb7f997e049c07cdd872b8354052c808499937645f6164912c4126015df036cc
|   |   |-- cache-id
|   |   |-- diff
|   |   |-- parent
|   |   |-- size
|   |   `-- tar-split.json.gz
|   `-- fcb4581c4f016b2e9761f8f69239433e1e123d6f5234ca9c30c33eba698487cc
|       |-- cache-id
|       |-- diff
|       |-- parent
|       |-- size
|       `-- tar-split.json.gz
`-- tmp
```

Once, all layers are downloaded successfully and docker find no corruption in images, it is extracted in ``diff`` folder at ``/var/lib/docker/aufs/``
_NOTE: The reference folder structure are on debain/ubuntu system. It may change slightly with other systems_

So basically when, container is created using image, these folders are mounted as unionFS branches to provide filesystem structure.

Reference folder structure for ``ubuntu`` image.
```
$ sudo tree -L 4 /var/lib/docker/
/var/lib/docker/
|-- aufs
|   |-- diff
|   |   |-- 7d5c1daf44d6cfa7567f2fd6ede52147397d4ae024eba25bcf8ad74f85af7804
|   |   |   `-- var
|   |   |-- 9c3623f1effcd8a5873be9cf99f907dc77a49887b0074fcf68c4e9ee5c86bb7f
|   |   |   |-- etc
|   |   |   |-- sbin
|   |   |   |-- usr
|   |   |   `-- var
|   |   |-- a9d800e3fda8265a2adcd99b44c889735485de06d29f20dae55ed9296a8a5693
|   |   |   `-- etc
|   |   |-- c83c436228b74479701aa6162de5db3f104999d41878dbfb6308773db87cd873
|   |   |   `-- run
|   |   `-- e48b1f0a9243c562174596032c6d4b1b91f362e285b75db737bb844f6c631ed0
|   |       |-- bin
|   |       |-- boot
|   |       |-- dev
|   |       |-- etc
|   |       |-- home
|   |       |-- lib
|   |       |-- lib64
|   |       |-- media
|   |       |-- mnt
|   |       |-- opt
|   |       |-- proc
|   |       |-- root
|   |       |-- run
|   |       |-- sbin
|   |       |-- srv
|   |       |-- sys
|   |       |-- tmp
|   |       |-- usr
|   |       `-- var
|   |-- layers
|   |   |-- 7d5c1daf44d6cfa7567f2fd6ede52147397d4ae024eba25bcf8ad74f85af7804
|   |   |-- 9c3623f1effcd8a5873be9cf99f907dc77a49887b0074fcf68c4e9ee5c86bb7f
|   |   |-- a9d800e3fda8265a2adcd99b44c889735485de06d29f20dae55ed9296a8a5693
|   |   |-- c83c436228b74479701aa6162de5db3f104999d41878dbfb6308773db87cd873
|   |   `-- e48b1f0a9243c562174596032c6d4b1b91f362e285b75db737bb844f6c631ed0
|   `-- mnt
|       |-- 7d5c1daf44d6cfa7567f2fd6ede52147397d4ae024eba25bcf8ad74f85af7804
|       |-- 9c3623f1effcd8a5873be9cf99f907dc77a49887b0074fcf68c4e9ee5c86bb7f
|       |-- a9d800e3fda8265a2adcd99b44c889735485de06d29f20dae55ed9296a8a5693
|       |-- c83c436228b74479701aa6162de5db3f104999d41878dbfb6308773db87cd873
|       `-- e48b1f0a9243c562174596032c6d4b1b91f362e285b75db737bb844f6c631ed0
```
