---
title: "Deploying OpenFaas on Docker Swarm"
date: 2018-02-22T16:41:22+05:30
draft: true
---
In this blog post, I am going to talk about how to deploy OpenFaas on Docker Swarm. OpenFaaS (Functions as a Service) is a  framework for building serverless functions with Docker and Kubernetes. To know more about OpenFaas visit this [link](https://www.openfaas.com/).

I have already setup a two node Docker Swarm cluster running on cloud service provider [Civo](https://www.civo.com/). OpenFaas repository contains a script `deploy_stack.sh` which can be used to deploy OpenFaas stack.

* Clone the OpenFaaS repository on `swarm manager` node.
```sh
$ git clone https://github.com/openfaas/faas
```

* To deploy OpenFaaS stack, checkout to particular version tag and run `deploy_stack.sh`.
```sh
$ cd faas && \
    git checkout 0.7.0 && \
    ./deploy_stack.sh
```

* List all running services:
```sh
$ docker service ls
Ouput:
ID                  NAME                MODE                REPLICAS            IMAGE                              PORTS
9ul5ba334nme        func_alertmanager   replicated          1/1                 prom/alertmanager:v0.7.1
fmjjw04nayun        func_base64         replicated          1/1                 functions/alpine:latest
kfp4q6oafprq        func_echoit         replicated          1/1                 functions/alpine:latest
pfqylhhsb97t        func_faas-swarm     replicated          1/1                 functions/faas-swarm:0.1.3a
fg6u3cnp862h        func_gateway        replicated          1/1                 functions/gateway:0.6.16-rc1     *:8080->8080/tcp
7jvwaqaresjg        func_hubstats       replicated          1/1                 functions/hubstats:latest
19hs72xjfop4        func_markdown       replicated          1/1                 functions/markdown-render:latest
l14inuqg4mc1        func_nats           replicated          1/1                 nats-streaming:0.6.0
ws8zvp3pcgck        func_nodeinfo       replicated          1/1                 functions/nodeinfo:latest
rksml98bnk27        func_prometheus     replicated          1/1                 prom/prometheus:v2.0.0           *:9090->9090/tcp
z5nezr3j593i        func_queue-worker   replicated          1/1                 functions/queue-worker:0.4
uodchmg0xyqi        func_wordcount      replicated          1/1                 functions/alpine:latest
```

Once everything is deployed, you should have gateway running at `http://<ip>:8080` and prometheus at `http://<ip>:9090`.

As we can see, OpenFaas also depolys few sample functions like `markdown-render`, `hubstats`, `nodeinfo` along with the stack.
OpenFaas also has function store which has sample functions created by OpenFaas community. You can use OpenFaas CLI or gateway interface to deploy functions from store.

Now we will have look at OpenFaas CLI which can be used to create, list, deploy, invoke and remove functions.

* Let's install OpenFaaS CLI
```sh
$ curl -sL cli.openfaas.com | sh
```

* Create a new function using one of the existing function template like (python, python3, node, go, node etc)
```sh
$ faas-cli new func1 --lang=python3
```

* Create function command should create files as follows
```sh
total 24
drwxrwxr-x  4 ubuntu ubuntu 4096 Feb 25 06:11 .
drwxr-xr-x  7 ubuntu ubuntu 4096 Feb 23 16:00 ..
drwx------  2 ubuntu ubuntu 4096 Feb 25 06:11 func1
-rw-------  1 ubuntu ubuntu  133 Feb 25 06:11 func1.yml
-rw-r--r--  1 ubuntu ubuntu   15 Feb 25 06:11 .gitignore
drwxrwxr-x 13 ubuntu ubuntu 4096 Feb 25 06:11 template
```

`func1.yml` file contains the configuration like gateway URL, image name etc. Prepend the image name with dockerhub or your docker registry name to push images.

```sh
provider:
  name: faas
  gateway: http://localhost:8080

functions:
  func1:
    lang: python3
    handler: ./func1
    image: viveksyngh/func1
```

* Build the function image
```sh
$ faas-cli build -f func1.yml
```

* Push image to remote repository
```sh
$ faas-cli push -f func1.yml
```

* Deploy the function
```sh
$ faas-cli deploy -f func1.yml
```
function will be deployed and endpoint to access function will be `http://<gateway>/function/<function name>`.

* CLI can also be used to invoke the functions
```sh
$ echo -n "Hello" | faas-cli invoke func1
```
or CURL can also be used to invoke functions
```
$ curl http://localhost:8080/function/func1 -d "Hello"
```
