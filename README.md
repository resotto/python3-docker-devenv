<h1 align="center">python3-docker-devenv</h1>

<div align="center">
  <p>
    <img src="https://user-images.githubusercontent.com/19743841/51781628-8a278900-215e-11e9-9290-eb4b4a097023.png">
  </p>
  <p>
    Python3 Development Environment on Docker Container
  </p>
  <p>    
    <a href="https://twitter.com/home?status=Python%20Docker%20Development%20Environment%20by%20%40resotto3%20https://github.com/resotto/python3-docker-devenv"><img src="https://img.shields.io/badge/twitter-tweet-blue.svg"/></a>
    <a href="https://twitter.com/resotto3"><img src="https://img.shields.io/badge/feedback-@resotto3-blue.svg" /></a>
  </p>
</div>

## Getting Started
Please run only 2 commands below:
```
$ docker run -v /app --name pydata ubuntu:18.04 echo "Data-only container for python3"
$ docker run -it --name ubuntu-python3 --volumes-from pydata resotto/ubuntu-python3:0.0.1
```

## Index
* [Why Python on Docker Container?](#why-python-on-docker-container)
* [Who Is This Repository Designed for?](#who-is-this-repository-designed-for)
* [What You Can Learn with This Repository](#what-you-can-learn-with-this-repository)
* [Installed Software Version Details](#installed-software-version-details)
* [Prerequisites](#prerequisites)
* [Getting Started](#quick-start)
* [Usage](#usage)
* [How to Use Docker Commands](#how-to-use-docker-commands)
* [How to Configure Dockerfile](#how-to-configure-dockerfile)
* [Why Does Quick Start Work?](#why-does-quick-start-work)
* [Optional](#optional)

## Why Python on Docker Container?
You don't have to create virtual environments with `venv` because you can handle them respectively as a container.

## Who Is This Repository Designed for?
* A developer who feels that [Docker Tutorial](https://docs.docker.com/get-started/) may be too difficult to understand.
* A developer who writes Python codes and is new to Docker.

## What You Can Learn with This Repository
* The way to handle Docker Container.
* The concepts of Docker.

## Installed Software Version Details
* ubuntu: 18.04.1 LTS
* python: 3.6.7
* git: 2.17.1

## Prerequisites
* [git](https://git-scm.com/downloads)
* [Docker](https://www.docker.com/get-started)

## Usage
First of all, you need to clone this repository and go into the directory.  
```
$ git clone git@github.com:resotto/python3-docker-devenv.git
$ cd python3-docker-devenv
```

Next, [`docker build`](https://docs.docker.com/engine/reference/commandline/build/) creates image from Dockerfile and
[`docker run`](https://docs.docker.com/engine/reference/commandline/run/) starts new container from image.
```
$ docker build -t ubuntu-python3:0.0.1 .
$ docker run -it ubuntu-python3:0.0.1 python3 hello.py
Hello python3-docker-devenv!
```

These commands syntax are below:
* [`docker build`](https://docs.docker.com/engine/reference/commandline/build/) -t ${NAME:TAG} ${BUILD_CONTEXT}
* [`docker run`](https://docs.docker.com/engine/reference/commandline/run/) -it ${IMAGE_NAME} ${COMMAND}

<details><summary>What is Build Context?</summary><div>
Build Context is just a set of local file or directories which can be referenced from [`ADD`](https://docs.docker.com/engine/reference/builder/#add) or [`COPY`](https://docs.docker.com/engine/reference/builder/#copy) in Dockerfile. Usually, it is specified as directory path.  
Build Context is sent to Docker Daemon as part of build process.  
You can specify Dockerfile path in Build Context with [`docker build -f ${PATH}`](https://docs.docker.com/engine/reference/commandline/build/), **but if not, Docker looks for Dockerfile in the root of Build Context**.</div></details><br>

If you confirmed *"Hello python3-docker-devenv!"*, congratulation! You can develop on this container.  

In order to save contents in the container, create Data Container in advance.<br>  

<details><summary>What is Data Container?</summary><div>
Data Container only aims to share data with other containers.
The advantage of creating Data Container is that we can load Docker Volume NameSpace
with `--volumes-from ${DATA_CONTAINER_NAME}` easily, which is [`docker run`](https://docs.docker.com/engine/reference/commandline/run/) command option.</div></details><br>

```
$ docker run -v /app --name pydata ubuntu:18.04 echo "Data-only container for python3"
Data-only container for python3
```

This command syntax is below:
* [`docker run`](https://docs.docker.com/engine/reference/commandline/run/) -v ${VOLUME_DIRECTORY} --name ${CONTAINER_NAME} ${IMAGE_NAME} ${COMMAND}

Specifying `-v ${VOLUME_DIRECTORY}` is very important to create Docker Volume. If don't, Docker Volume is not created.  
`${VOLUME_DIRECTORY}` is just a directory in the container and the files in it are
copied to a Docker Volume.  
Thus, you work in this directory in order to save files.

After creating Data Container, this container stops. However, it is possible for
it to be stopped and it should be so.

Then, run following command and you will enter the container such as running
ssh command.
```
$ docker run -it --name ubuntu-python3 --volumes-from pydata ubuntu-python3:0.0.1
```

In the container you can also use python interactive interpreter.
```
root@51c45890a7ed:/app# python3
Python 3.6.7 (default, Oct 22 2018, 11:32:17)
[GCC 8.2.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>>
```

## How to Use Docker Commands
If you want to confirm container status, you can use [`docker ps`](https://docs.docker.com/engine/reference/commandline/ps/). If specifying `a` option, you can retrieve all containers status, while retrieving only running containers with no option.
```
$ docker ps -a
```

If you specify `q` option, it returns only container ids, so that you can do something
like this when you want to remove all containers.
```
$ docker rm $(docker stop $(docker ps -aq))
```

You can check settings of the container with [`docker inspect`](https://docs.docker.com/engine/reference/commandline/inspect/).
```
$ docker inspect ${CONTAINER_NAME}
```

You can resume the stopped container.  
[`docker start`](https://docs.docker.com/engine/reference/commandline/start/) starts the container and [`docker exec`](https://docs.docker.com/engine/reference/commandline/exec/) runs commands in the container.
Each commands require one argument, container name.
```
$ docker start ubuntu-python3
$ docker exec -it ubuntu-python3 /bin/bash
```

If you want only to create a container without starting it, you can use [`docker create`](https://docs.docker.com/engine/reference/commandline/create/). You can use similar options with [`docker run`](https://docs.docker.com/engine/reference/commandline/run/).  
```
$ docker create --name ${CONTAINER_NAME}
```

You can escape from the container with typing `exit` or `ctrl + d`.  
When you escape from the container, it also exits in case you ran it with [`docker run`](https://docs.docker.com/engine/reference/commandline/run/), while it remains up in case you ran it with [`docker exec`](https://docs.docker.com/engine/reference/commandline/exec/).

Actually, [`docker run`](https://docs.docker.com/engine/reference/commandline/run/) is equivalent to [`docker create`](https://docs.docker.com/engine/reference/commandline/create/) + [`docker start`](https://docs.docker.com/engine/reference/commandline/start/) + [`docker exec`](https://docs.docker.com/engine/reference/commandline/exec/), but not equal to as discussed above.

You also can run [`docker stop`](https://docs.docker.com/engine/reference/commandline/stop/) in order to stop container, which requires container name.  
Additionally, you can release the port you allocated to the container with [`docker stop`](https://docs.docker.com/engine/reference/commandline/stop/).
```
$ docker stop ubuntu-python3
```

You can remove container with [`docker rm`](https://docs.docker.com/engine/reference/commandline/rm/) which is required container name.
```
$ docker rm ubuntu-python3
```

**Even after removing the container, docker volume remains**.  
So you can confirm volume status with [`docker volume ls`](https://docs.docker.com/engine/reference/commandline/volume_ls/) and remove it with [`docker volume rm`](https://docs.docker.com/engine/reference/commandline/volume_rm/).
```
$ docker volume ls
$ docker volume rm ${VOLUME_NAME}
```

After running container, you may want to share files between host and the container.  
Then, you can use [`docker cp`](https://docs.docker.com/engine/reference/commandline/cp/).
This command enables us to share files from host to container, and also from container to host.
```
$ docker cp ${SRC_DIR} ${CONTAINER_NAME}:${DEST_DIR}
$ docker cp ${CONTAINER_NAME}:${SRC_DIR} ${DEST_DIR}
```

Besides above, there are many Docker commands, so please check [docker reference](https://docs.docker.com/engine/reference/commandline/docker/).

## How to Configure Dockerfile
### What is Dockerfile?
Dockerfile is just a file which has a series of processes for the purpose of creating Docker Image.
There are some orders in Dockerfile.  

### Orders of Dockerfile
#### [`FROM`](https://docs.docker.com/engine/reference/builder/#from)  
- [`FROM`](https://docs.docker.com/engine/reference/builder/#from) **must be the first order in Dockerfile**.  
This specifies Docker Image with format `IMAGE[:TAG]`.  
 If `TAG` is omitted, it will be `latest`, but **`latest` cannot be recommended because the version of the tag may change in the future**.


#### [`WORKDIR`](https://docs.docker.com/engine/reference/builder/#workdir)
 - This specifies working directory where orders are executed, such as [`COPY`](https://docs.docker.com/engine/reference/builder/#copy) or [`RUN`](https://docs.docker.com/engine/reference/builder/#run) etc... You also can specify relative path and use this order many times in Dockerfile.


#### [`COPY`](https://docs.docker.com/engine/reference/builder/#copy)
- This order copies file from build context to image. The format is `COPY src dest`. You cannot specify `src` which is out of Build Context.

#### [`RUN`](https://docs.docker.com/engine/reference/builder/#run)
- This order takes one argument, which is a command, executes it and commits the result.

Besides above, there are many Dockerfile orders, so please check [Dockerfile reference](https://docs.docker.com/engine/reference/builder/).

## Why Does Quick Start Work?
What you did at [Quick Start](#quick-start) was just two things:
1. Created Data Container.
2. Created and ran container whose data is based on the container created by step 1.

In step 1, when you did [`docker run ubuntu:18.04`]((https://docs.docker.com/engine/reference/commandline/run/), whose options are omitted here, Docker Daemon looks for the image from local first. If it isn't there, Docker Daemon tries to pull it from Registry, which is [Docker Hub](https://hub.docker.com/) by default.  
So you just pulled [`ubuntu:18.04`]((https://hub.docker.com/_/ubuntu?tab=description) image from Docker Hub.

<details><summary>What is Registry?</summary><div>
Registry is just a service that hosts and distributes Docker images.</div></details><br>

<details><summary>What is Docker Daemon?</summary><div>
Docker Daemon is responsible for creating, running and monitoring containers, and building and saving images.  
Although host OS usually starts Docker Daemon, you also can start it with `docker daemon`.
</div></details><br>

How about `resotto/ubuntu-python3:0.0.1` in step2? Is this pulled from Docker Hub too? Exactly. Indeed this image was build by you if you passed through [Usage](#usage), actually it had already been pushed and hosted by Docker Hub. You can check it at [this page](https://cloud.docker.com/u/resotto/repository/docker/resotto/ubuntu-python3)!  

While the format of the ubuntu image [`ubuntu:18.04`](https://hub.docker.com/_/ubuntu?tab=description) is `REPOSITORYNAME[:TAG]`, the format of the original image [`resotto/ubuntu-python3:0.0.1`](https://cloud.docker.com/u/resotto/repository/docker/resotto/ubuntu-python3) is `USERNAME/REPOSITORYNAME[:TAG]`. What is the `USERNAME` of the ubuntu image?  
 In fact, the ubuntu image doesn't have `USERNAME`.

<details><summary>What is Repository?</summary><div>
When falling into Docker category, Repository means set of relative images. Usually, it offers various versions of the same application or service. In short, images belong to Repository.
</div></details><br>

This is because the ubuntu image [`ubuntu:18.04`](https://hub.docker.com/_/ubuntu?tab=description) belongs *"root"* namespace, which is managed by Docker Inc. *"Root"* namespace is reserved for official images of prevailing softwares and distributions.  
On the other hand, original image such as [`resotto/ubuntu-python3:0.0.1`](https://cloud.docker.com/u/resotto/repository/docker/resotto/ubuntu-python3) belongs *"user"* namespace. This type of image is what is uploaded to Docker Hub by users.

Thus, you can judge whether it is official image or not, due to its format. For example, [`resotto/ubuntu-python3:0.0.1`](https://cloud.docker.com/u/resotto/repository/docker/resotto/ubuntu-python3) has `USERNAME` "resotto" so this is uploaded by the user, not official image.

By the way, how was it uploaded to Docker Hub?  
You need follow 4 steps in order to upload image to Docker Hub:
1. Signup [Docker Hub](https://hub.docker.com/signup).
2. Login Docker Hub
3. Tag image as appropriate Repository name.
4. Push the image to Docker Hub.

### 1. Signup Docker Hub
In order to host your original image, signup [Docker Hub](https://hub.docker.com/signup) first.

Click "Sign Up" in [Docker Hub](https://hub.docker.com/signup).
![click-signup](https://user-images.githubusercontent.com/19743841/51794288-ca484380-2212-11e9-9e93-2b33e238ccce.png)

And then, type your info into the form and click "Sign Up".
![type-your-info](https://user-images.githubusercontent.com/19743841/51794289-ca484380-2212-11e9-8bb6-2297dfe7510b.png)

### 2. Login Docker Hub
Run [`docker login`](https://docs.docker.com/engine/reference/commandline/login/) and then type your Docker ID and password.
```
$ docker login
Login with your Docker ID to push and pull images from Docker Hub. If you don't have a Docker ID, head over to https://hub.docker.com to create one.
Username: resotto
Password:
Login Succeeded
```

If you have already succeeded at login, you don't have to do anything.
```
$ docker login
Authenticating with existing credentials...
Login Succeeded
```

When you logout of your registry, you can run [`docker logout`](https://docs.docker.com/engine/reference/commandline/logout/).

### 3. Tag image as appropriate Repository name
If you passed through [Usage](#usage), did you remember build image from Dockerfile as follows?
```
$ docker build -t ubuntu-python3:0.0.1 .
```

Undoubtedly, you built image from Dockerfile but simultaneously, you also did tag image as `ubuntu-python3:0.0.1`. This tag has no `USERNAME`, so let's tag image as the name which includes your Docker ID:
```
$ docker build -t resotto/ubuntu-python3:0.0.1 .
```

After tagging the image, run [`docker images`](https://docs.docker.com/engine/reference/commandline/images/) or [`docker image ls`](https://docs.docker.com/engine/reference/commandline/image_ls/) in order to confirm that the image exists.
```
$ docker images
```

### 4. Push the image to Docker Hub
After confirming the image you tagged exists, let's push it to Docker Hub. Please run [`docker push`](https://docs.docker.com/engine/reference/commandline/push/).
```
$ docker push resotto/ubuntu-python3:0.0.1
The push refers to repository [docker.io/resotto/ubuntu-python3]
5ce5f63f24c0: Pushed
d8d3b3e8a557: Pushed
0e6f3b804d02: Pushed
8b32fa684eea: Pushed
327a00a42c3b: Pushed
1c77a0c741d2: Pushed
773f076615b0: Pushed
75c251c8063a: Pushed
c9d22d4a720c: Pushed
37907c98853d: Pushed
27a216ffe825: Mounted from library/ubuntu
9e9d3c3a7458: Mounted from library/ubuntu
7604c8714555: Mounted from library/ubuntu
adcb570ae9ac: Mounted from library/ubuntu
0.0.1: digest: sha256:893e46dffdde445500c05b6200b0af0771412c1a97c774ca17ade29f61b8b25e size: 3245
```

When you re-built the image and pushed it, the result of running [`docker push`](https://docs.docker.com/engine/reference/commandline/push/) was below:
```
$ docker push resotto/ubuntu-python3:0.0.1
The push refers to repository [docker.io/resotto/ubuntu-python3]
5ce5f63f24c0: Layer already exists
d8d3b3e8a557: Layer already exists
0e6f3b804d02: Layer already exists
8b32fa684eea: Layer already exists
327a00a42c3b: Layer already exists
1c77a0c741d2: Layer already exists
773f076615b0: Layer already exists
75c251c8063a: Layer already exists
c9d22d4a720c: Layer already exists
37907c98853d: Layer already exists
27a216ffe825: Layer already exists
9e9d3c3a7458: Layer already exists
7604c8714555: Layer already exists
adcb570ae9ac: Layer already exists
0.0.1: digest: sha256:893e46dffdde445500c05b6200b0af0771412c1a97c774ca17ade29f61b8b25e size: 3245
```

If you omitted your username when running [`docker push`](https://docs.docker.com/engine/reference/commandline/push/), it would fail. Because *"root"* namespace is reserved for official images so that you cannot push it.
```
$ docker push ubuntu-python3:0.0.1
The push refers to repository [docker.io/library/ubuntu-python3]
ab40990d7169: Preparing
57c7268ba395: Preparing
2b8e0e911375: Preparing
bbf5176f74b4: Preparing
a862c3e0e8f6: Preparing
61cf449bba00: Waiting
0f4f1d0eed43: Waiting
029c04e04f41: Waiting
03b5d5310008: Waiting
665e12eb9565: Waiting
27a216ffe825: Waiting
9e9d3c3a7458: Waiting
7604c8714555: Waiting
adcb570ae9ac: Waiting
denied: requested access to the resource is denied
```

If you forgot to login, following message would be displayed.
```
$ docker push resotto/ubuntu-python3:0.0.1
The push refers to repository [docker.io/resotto/ubuntu-python3]
5ce5f63f24c0: Layer already exists
d8d3b3e8a557: Layer already exists
0e6f3b804d02: Layer already exists
8b32fa684eea: Layer already exists
327a00a42c3b: Layer already exists
1c77a0c741d2: Layer already exists
773f076615b0: Layer already exists
75c251c8063a: Layer already exists
c9d22d4a720c: Layer already exists
37907c98853d: Layer already exists
27a216ffe825: Layer already exists
9e9d3c3a7458: Layer already exists
7604c8714555: Layer already exists
adcb570ae9ac: Layer already exists
errors:
denied: requested access to the resource is denied
unauthorized: authentication required
```

## Optional
### Install and Configure git
You already have installed git because it is specified in Dockerfile.
So, let's configure it.

#### Set up git
First of all, you should add your name and your email to git config.
```
$ git config --global user.email "you@example.com"
$ git config --global user.name "Your Name"
```

If you confirm git config parameters, use [git config](https://git-scm.com/docs/git-config) with list option.
```
$ git config --list
```

#### Set up ssh
Is there `.ssh` directory in home directory? If not, make it first.
```
$ cd ~
$ ls -a
$ mkdir .ssh # if there is no .ssh directory
$ cd .ssh
```

And then, generate private key and public key with `ssh-keygen`.
```
$ ssh-keygen -t rsa
```

Syntax of this command is below:
* ssh-keygen -t ${ALGORITHM}  

If you specify `t` option, you can specify generation algorithm such as RSA or DSA etc…

When you execute `ssh-keygen`, it asks you some questions:
##### Enter file in which to save the key(/root/.ssh/id_rsa):
* Specify path including filename where you want to save keys, for example `/root/temp/id_rsa`.
If specified directory doesn't exist, ssh-keygen command fails.  

##### Enter passphrase (empty for o passphrase):  
* Passphrase is a string of characters, which is longer than usual password (minimum five characters but 20 characters preferably).
It is used in an encryption or a decryption of private key.

##### Enter same passphrase again:
* Enter the same passphrase as you typed above.

After generating keys, register public key on GitHub.
At first, copy the result of executing command below.
```
$ cat id_rsa.pub
```

And then, register it on Github following instructions.

###### Open your GitHub account settings.
![click-settings](https://user-images.githubusercontent.com/19743841/51783793-7ee65480-2182-11e9-847b-3e54b647fa01.png)

###### Click `SSH and GPG keys`.
![click-ssh-and-gpg-keys](https://user-images.githubusercontent.com/19743841/51783794-7ee65480-2182-11e9-8787-0ad6046b6da9.png)

###### Click `New SSH key`.
![click-new-ssh-key](https://user-images.githubusercontent.com/19743841/51783792-7ee65480-2182-11e9-84b5-11dbfa51ba21.png)

###### Specify Title, paste public key strings in Key and click `Add SSH key`.
![click-add-ssh-key](https://user-images.githubusercontent.com/19743841/51783858-b3a6db80-2183-11e9-9c56-21055d61c3d3.png)

Finally, run `ssh -T git@github.com`, which attempts to ssh to GitHub.  
If you are asked to continue connecting, answer *yes* and if you specified passphrase, type it and press enter. If you didn't create passphrase, it is not required.
```
root@01cb00143067:~/.ssh# ssh -T git@github.com
The authenticity of host 'github.com (192.30.255.113)' can't be established.
RSA key fingerprint is SHA256:nThbg6kXUpJWGl7E1IGOCspRomTxdCARLviKw6E5SY8.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'github.com,192.30.255.113' (RSA) to the list of known hosts.
Enter passphrase for key '/root/.ssh/id_rsa':
Hi resotto! You've successfully authenticated, but GitHub does not provide shell access.
```

Could you confirm the message *"Hi username!"*? Fantastic! From now, you can pull your private repository in this container.

It doesn't work? Please check [Testing your SSH connection](https://help.github.com/articles/testing-your-ssh-connection/).

### Install python packages
This repository has `requirements.txt` so you can add python packages into it.  
They are installed with pip when you run [`docker build`](https://docs.docker.com/engine/reference/commandline/build/).

### Install Vim
Actually, you already have installed vim and this repository also has `.vimrc` which is copied to *"/root"* in the container. Thus you can use vim by default!  

If you want to use other text editor, you can do same thing like this.
