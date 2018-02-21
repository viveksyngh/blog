---
title: "Install Docker on Ubuntu"
date: 2018-02-20T17:46:10+05:30
categories: ["docker", "ubuntu"]
author: "Vivek Singh"
---

In this blog post, I am going to show how to install `docker` on `ubuntu 16.04`. We will install community edition of docker.

1. Update the `apt` package index:
```sh
$ sudo apt-get update
```

2. Install packages to allow `apt` to use a repository over HTTPS:
```sh
$ sudo apt-get install \
        apt-transport-https \
        ca-certificates \
        curl \
        software-properties-common
```

3. Add Docker's official GPG key:
```sh
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```

4. Verify the key fingerprint, It should match with `9DC8 5822 9FC7 DD38 854A E2D8 8D81 803C 0EBF CD88`
```sh
$ sudo apt-key fingerprint 0EBFCD88
```

5. Add docker `apt` repository
```sh
$ sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
```

6. Update the `apt` package index
```sh
$ sudo apt-get update
```

7. Install the latest version of Docker CE,
```sh
$ sudo apt-get install docker-ce
```

8. To validate docker installation run below command.
```sh
$ sudo docker run hello-world
```

If Docker is installed successfully, the above command should download the `hello-world` latest image from docker hub and run it.
It will also print some welcome message as well.

Remember, we used `sudo` to run `docker run` command above. There are some post-installation steps that you can follow to run `docker` command a non-root user.

To run docker commands as a non-root user,

9. Create the `docker` group
```sh
$ sudo groupadd docker
```

10. Add your user to the `docker` group
```sh
$ sudo usermod -aG docker $USER
```

11. Log out and log back in so that your group membership gets updated. Now you run `hello-world` command as below.
```sh
$ docker run hello-world
```
