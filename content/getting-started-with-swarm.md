---
title: "Getting started with Docker Swarm"
date: 2018-02-21T12:17:47+05:30
categories: ["docker", "swarm", "ubuntu"]
author: "Vivek Singh"
---

In this blog post, I am going to talk about how to set up a swarm cluster and some swarm basic commands. I will set up two node swarm cluster as an example.

I am going to provision two small size instances on cloud service provider called [Civo](https://www.civo.com). You can use any other cloud service provider like AWS, GCP, Azure or private cloud to provision instances. After setting up instances make sure that all instances are running on the same network and able to talk to each other.

Before we go to actual work, let's discuss some of the important `swarm` terminologies. A `swarm` is a group of machines that are running Docker and joined into the cluster. Every swarm has a `swarm manager` which can execute your commands, or authorize other machines to join the swarm as `workers`. Every machine in a swarm is referred as `nodes`.

Now, we will go ahead and install docker engine on both instances. You can follow docker installation guide from official docker documentation [here](https://docs.docker.com/install/) or [this](/blog/install-docker-on-ubuntu) blog post. Follow corresponding documentation based on the operating system of your instances.

Once you have Docker installed on every instance, choose one of the instances for `swarm manager` and run the below command to   initiate the `swarm` cluster.

```sh
$ docker swarm init
```
Above command output, prints out a command with a token to be run from `workers` to join the cluster.

Go to all your worker's instances and run the command:
```sh
$ docker swarm join --token <token> <ip>:<port>
```
Where `token`, `ip` and `port` are output from the `init` command.

List the worker's token for join command:
```sh
$ docker swarm join-token worker
```

You can also add more `swarm manager` to cluster. To get token for `manager` run the below command from `swarm manager` and follow the instructions.
```sh
$ docker swarm join-token manager
```

List all nodes in swarm:
```sh
$ docker node ls
ID                            HOSTNAME            STATUS              AVAILABILITY        MANAGER STATUS
zkocztdahzkdjbk6bhu3jqb8l *   node-1              Ready               Active              Leader
zb8329nsvdqhhq239k16yo89p     node-2              Ready               Active
```

List all services running in swarm:
```sh
$ docker service ls
```

Remove one or more services from swarm:
```sh
$ docker service rm <service-name-1> <service-name-2> ...
```

See logs of a service:
```sh
$ docker service logs <service-name>
```

Scale a service:
```sh
$ docker service scale <service-name>=<replicas>
```
Where `<replicas>` is number of replica like 2, 3 etc.

Inspect one or more services:
```sh
$ docker service inspect <service-name-1> <service-name-2> ...
```

Create a service:
```sh
$ docker service create [OPTIONS] <image-name>
```

Update a service:
```sh
$ docker service update [OPTIONS] <service-name>
```

For detailed reference of commands, please refer [this](https://docs.docker.com/swarm/reference/) official documentation.
