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
* [Usage](#usage)
* [How to Use Docker Commands](#how-to-use-docker-commands)
* [How to Configure Dockerfile](#how-to-configure-dockerfile)
* [Why Does Getting Started Work?](#why-does-getting-started-work)
* [Optional](#optional)
* [Deep Learning](#deep-learning)
* [References](#references)

## Why Python on Docker Container?
You don't have to create virtual environments because you can handle them respectively as a container.

## Who Is This Repository Designed for?
* A developer who feels that [Docker Tutorial](https://docs.docker.com/get-started/) may be too difficult to understand.
* A developer who writes Python codes and is new to Docker.
* A developer who writes Deep Learning codes and is new to Docker.

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

Next, [`docker build`](https://docs.docker.com/engine/reference/commandline/build/) creates Image from Dockerfile and
[`docker run`](https://docs.docker.com/engine/reference/commandline/run/) starts new container from Image. About Dockerfile, see [What is Dockerfile?](#what-is-dockerfile).


<details><summary>What is Image?</summary><div>
Docker Image is composed of multi layers. Each layer is read-only file system. New layer is created by every Dockerfile order and piled up on existing layers.  
If Image is converted to a container by [`docker run`](https://docs.docker.com/engine/reference/commandline/run/) or [`docker create`](https://docs.docker.com/engine/reference/commandline/create/), Docker Engine adds read/write file system on the image and configures settings about IPAddress, name, id and resource limit etc... **So stopped container is not the same as Image**. It keeps environmental settings such as IPAddress, changed settings, meta-data and file system. They are not saved in Image.
</div></details><br>

```
$ docker build -t ubuntu-python3:0.0.1 .
$ docker run -it ubuntu-python3:0.0.1 python3 hello.py
Hello python3-docker-devenv!
```

These commands syntax are below:
* [`docker build`](https://docs.docker.com/engine/reference/commandline/build/) -t ${NAME}:${TAG} ${BUILD_CONTEXT}
* [`docker run`](https://docs.docker.com/engine/reference/commandline/run/) -it ${IMAGE_NAME} ${COMMAND}

<details><summary>What is Build Context?</summary><div>
Build Context is just a set of local file or directories which can be referenced from [`ADD`](https://docs.docker.com/engine/reference/builder/#add) or [`COPY`](https://docs.docker.com/engine/reference/builder/#copy) in Dockerfile. Usually, it is specified as directory path.  
Build Context is sent to Docker Daemon as part of build process.  
You can specify Dockerfile path in Build Context with [`docker build -f ${PATH}`](https://docs.docker.com/engine/reference/commandline/build/), **but if not, Docker looks for Dockerfile in the root of Build Context**.</div></details><br>

`t` option with [`docker build`](https://docs.docker.com/engine/reference/commandline/build/) means specifying image name and optionally a tag in the `NAME[:TAG]` format.

`-it` is coupled with `-i (--interactive)` and `-t (--tty)`. `i` option means keeping STDIN open even if not attached, and `t` option means allocating a pseudo-TTY, so that you can use shell(Bash) inside a container.

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
* [`docker run`](https://docs.docker.com/engine/reference/commandline/run/) -v ${VOLUME_DIRECTORY} --name ${CONTAINER_NAME} ${NAME}:${TAG} ${COMMAND}

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

This command syntax is below:
* [`docker run`](https://docs.docker.com/engine/reference/commandline/run/) -it --name ${CONTAINER_NAME} --volumes-from ${DATA_CONTAINER_NAME} ${NAME}:${TAG}

In the container, you can also use python interactive interpreter.
```
root@51c45890a7ed:/app# python3
Python 3.6.7 (default, Oct 22 2018, 11:32:17)
[GCC 8.2.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>>
```

## How to Use Docker Commands
If you want to confirm container status, you can run [`docker ps`](https://docs.docker.com/engine/reference/commandline/ps/). If specifying `a` option, you can retrieve all containers status, while retrieving only running containers with no option.
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
Each command requires one argument, ${CONTAINER_NAME}.
```
$ docker start ubuntu-python3
$ docker exec -it ubuntu-python3 /bin/bash
```

If you want only to create a container without starting it, you can run [`docker create`](https://docs.docker.com/engine/reference/commandline/create/). You can run similar options with [`docker run`](https://docs.docker.com/engine/reference/commandline/run/).  
```
$ docker create --name ${CONTAINER_NAME}
```

You can escape from the container with typing `exit` or `ctrl + d`.  
When you escape from a container, it also exits in case you ran it with [`docker run`](https://docs.docker.com/engine/reference/commandline/run/), while it remains up in case you ran it with [`docker exec`](https://docs.docker.com/engine/reference/commandline/exec/).

Actually, [`docker run`](https://docs.docker.com/engine/reference/commandline/run/) is equivalent to [`docker create`](https://docs.docker.com/engine/reference/commandline/create/) + [`docker start`](https://docs.docker.com/engine/reference/commandline/start/) + [`docker exec`](https://docs.docker.com/engine/reference/commandline/exec/), but not equal to as the difference between [`docker run`](https://docs.docker.com/engine/reference/commandline/run/) and [`docker exec`](https://docs.docker.com/engine/reference/commandline/exec/).

You also can run [`docker stop`](https://docs.docker.com/engine/reference/commandline/stop/) in order to stop container, which requires ${CONTAINER_NAME}.  
Additionally, you can release host machine's port allocated to the container with [`docker stop`](https://docs.docker.com/engine/reference/commandline/stop/).
```
$ docker stop ubuntu-python3
```

You can remove container with [`docker rm`](https://docs.docker.com/engine/reference/commandline/rm/) which requires ${CONTAINER_NAME}.
```
$ docker rm ubuntu-python3
```

**Even after removing container, docker volume still remains**.  
So you can confirm volume status with [`docker volume ls`](https://docs.docker.com/engine/reference/commandline/volume_ls/) and remove it with [`docker volume rm`](https://docs.docker.com/engine/reference/commandline/volume_rm/).
```
$ docker volume ls
$ docker volume rm ${VOLUME_NAME}
```

After running a container, you may want to share files between host and the container.  
Then, you can run [`docker cp`](https://docs.docker.com/engine/reference/commandline/cp/).
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
Here, let's pick up some of them.

### Orders of Dockerfile
#### [`FROM`](https://docs.docker.com/engine/reference/builder/#from)  
- [`FROM`](https://docs.docker.com/engine/reference/builder/#from) **must be the first order in Dockerfile**.  
This specifies Docker Image with format `NAME[:TAG]`.
If `TAG` is omitted, it will be `latest`, but **`latest` cannot be recommended because the version of this tag may change in the future**.


#### [`WORKDIR`](https://docs.docker.com/engine/reference/builder/#workdir)
- This specifies working directory where orders are executed, such as [`COPY`](https://docs.docker.com/engine/reference/builder/#copy) or [`RUN`](https://docs.docker.com/engine/reference/builder/#run) etc... You also can specify relative path and use this order many times in Dockerfile.


#### [`COPY`](https://docs.docker.com/engine/reference/builder/#copy)
- This order copies file from Build Context to image. The format is `COPY src dest`. You cannot specify `src` which is out of Build Context.

#### [`RUN`](https://docs.docker.com/engine/reference/builder/#run)
- This order takes one argument, which is a command, executes it and commits the result.

Besides above, there are many Dockerfile orders, so please check [Dockerfile reference](https://docs.docker.com/engine/reference/builder/).

## Why Does Getting Started Work?
What you did at [Getting Started](#getting-started) was just two things:
1. Created Data Container.
2. Created and ran container whose data is based on the Data Container.

In step 1, when you did [`docker run ubuntu:18.04`]((https://docs.docker.com/engine/reference/commandline/run/), whose options are omitted here, Docker Daemon looks for the image from local first. If it isn't there, Docker Daemon tries to pull it from Registry, which is [Docker Hub](https://hub.docker.com/) by default.  
So you just pulled [`ubuntu:18.04`]((https://hub.docker.com/_/ubuntu?tab=description) image from [Docker Hub](https://hub.docker.com/).

<details><summary>What is Registry?</summary><div>
Registry is just a service that hosts and distributes Docker Image.</div></details><br>

<details><summary>What is Docker Daemon?</summary><div>
Docker Daemon is responsible for creating, running and monitoring containers, and building and saving images.  
Although host OS usually starts Docker Daemon, you also can start it explicitly with `docker daemon`.
</div></details><br>

How about `resotto/ubuntu-python3:0.0.1` in step2? Was this pulled from Docker Hub too? Exactly. Actually it had already been pushed to and hosted by Docker Hub. You can check it at [this page](https://cloud.docker.com/u/resotto/repository/docker/resotto/ubuntu-python3)!  
So what is the difference between `resotto/ubuntu-python3:0.0.1` and [`ubuntu:18.04`]((https://hub.docker.com/_/ubuntu?tab=description)?  
To know this, let's learn Docker Repository first.

<details><summary>What is Repository?</summary><div>
When falling into Docker category, Repository means set of relative images. Usually, it offers various versions of the same application or service. In short, images belong to Repository.
</div></details><br>

While the format of [`ubuntu:18.04`](https://hub.docker.com/_/ubuntu?tab=description) is `REPOSITORYNAME[:TAG]`, the format of the original image [`resotto/ubuntu-python3:0.0.1`](https://cloud.docker.com/u/resotto/repository/docker/resotto/ubuntu-python3) is `USERNAME/REPOSITORYNAME[:TAG]`. What is the `USERNAME` of [`ubuntu:18.04`]((https://hub.docker.com/_/ubuntu?tab=description)?  
In fact, the ubuntu image [`ubuntu:18.04`]((https://hub.docker.com/_/ubuntu?tab=description) doesn't have `USERNAME`.

This is because the ubuntu image [`ubuntu:18.04`](https://hub.docker.com/_/ubuntu?tab=description) belongs *"root"* namespace, which is managed by Docker Inc. *"root"* namespace is reserved for official images of prevailing softwares and distributions.  
On the other hand, original image such as [`resotto/ubuntu-python3:0.0.1`](https://cloud.docker.com/u/resotto/repository/docker/resotto/ubuntu-python3) belongs *"user"* namespace. This type of image is what is uploaded to Docker Hub by users.

Thus, you can judge whether it is official image or not, due to its format. For example, [`resotto/ubuntu-python3:0.0.1`](https://cloud.docker.com/u/resotto/repository/docker/resotto/ubuntu-python3) has `USERNAME` "resotto" so this is uploaded by the user, not official image.

By the way, how was it uploaded to Docker Hub?  
You need to follow 4 steps in order to upload image to Docker Hub:
1. Signup [Docker Hub](https://hub.docker.com/signup).
2. Login Docker Hub.
3. Tag image as appropriate Repository name.
4. Push the tagged image to Docker Hub.

### 1. Signup Docker Hub
In order to host your original image, please signup [Docker Hub](https://hub.docker.com/signup) first.

Click "Sign Up" in [Docker Hub](https://hub.docker.com/signup).
![click-signup](https://user-images.githubusercontent.com/19743841/51794288-ca484380-2212-11e9-9e93-2b33e238ccce.png)

And then, type your info into the form and click "Sign Up".
![type-your-info](https://user-images.githubusercontent.com/19743841/51794289-ca484380-2212-11e9-8bb6-2297dfe7510b.png)

### 2. Login Docker Hub
Please run [`docker login`](https://docs.docker.com/engine/reference/commandline/login/) and then type your Docker ID and password.
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
If you passed through [Usage](#usage), do you remember building an image from Dockerfile as follows?
```
$ docker build -t ubuntu-python3:0.0.1 .
```

Undoubtedly, you built an image from Dockerfile, but simultaneously you also tagged the image as `ubuntu-python3:0.0.1`. This tag has no `USERNAME`, so let's tag image as the name including your Docker ID:
```
$ docker build -t resotto/ubuntu-python3:0.0.1 .
```

After tagging the image, run [`docker images`](https://docs.docker.com/engine/reference/commandline/images/) or [`docker image ls`](https://docs.docker.com/engine/reference/commandline/image_ls/) in order to confirm that the image exists.
```
$ docker images
```

### 4. Push the tagged image to Docker Hub
After confirming that the image you tagged exists, let's push it to Docker Hub. Please run [`docker push`](https://docs.docker.com/engine/reference/commandline/push/).
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

If you omitted your username when running [`docker push`](https://docs.docker.com/engine/reference/commandline/push/), it would fail. Since *"root"* namespace is reserved for official images, you cannot push your original image there.
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
You should add your name and your email to git config.
```
$ git config --global user.email "you@example.com"
$ git config --global user.name "Your Name"
```

If you want to confirm git config parameters, please run [git config](https://git-scm.com/docs/git-config) with list option.
```
$ git config --list
```

#### Set up ssh
Let's generate private key and public key with `ssh-keygen`.
```
$ ssh-keygen -t rsa
```

Syntax of this command is below:
* ssh-keygen -t ${ALGORITHM}  

If you specify `t` option, you can specify generation algorithm such as RSA or DSA etc…

When you execute `ssh-keygen`, it asks you some questions about:
##### Enter file in which to save the key(/root/.ssh/id_rsa):
* Please specify path including filename where you want to save keys.
If specified directory doesn't exist, ssh-keygen command fails.  
If you omitted this path and `/root/.ssh` doesn't exist, directory `/root/.ssh` is created and keys are placed there.

##### Enter passphrase (empty for o passphrase):  
* Passphrase is a string of characters, which is longer than usual password (minimum five characters but 20 characters preferably).
It is used in encryption and decryption of private key.

##### Enter same passphrase again:
* Enter the same passphrase as you typed above.

After generating keys, register public key on GitHub.
At first, please copy the result of executing command below.
```
$ cat id_rsa.pub
```

And then, please register it on Github with following instructions.

###### Open your GitHub account settings.
![click-settings](https://user-images.githubusercontent.com/19743841/51783793-7ee65480-2182-11e9-847b-3e54b647fa01.png)

###### Click `SSH and GPG keys`.
![click-ssh-and-gpg-keys](https://user-images.githubusercontent.com/19743841/51783794-7ee65480-2182-11e9-8787-0ad6046b6da9.png)

###### Click `New SSH key`.
![click-new-ssh-key](https://user-images.githubusercontent.com/19743841/51783792-7ee65480-2182-11e9-84b5-11dbfa51ba21.png)

###### Specify Title, paste public key strings into Key and click `Add SSH key`.
![click-add-ssh-key](https://user-images.githubusercontent.com/19743841/51783858-b3a6db80-2183-11e9-9c56-21055d61c3d3.png)

Finally, run `ssh -T git@github.com`, which attempts to ssh to GitHub.  
If you are asked to continue connecting, answer *yes*. If you specified passphrase, please type it and press enter. If you didn't create passphrase, it is not required.
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
They are installed with pip3 when you run [`docker build`](https://docs.docker.com/engine/reference/commandline/build/).

### Install Vim
Actually, you already have installed vim and this repository also has `.vimrc` which is copied to *"/root"* in the container. Thus you can use vim by default!

## Deep Learning
If you can run learning models on container and show matplotlib figure about the result, it will be very useful.  
In order to implement this, you need to follow 5 steps below:
1. Install X11 on your host machine.
2. Configure settings for X11Forwarding.
3. Expose 22 port for ssh.
4. Install Tkinter and start ssh service.
5. Connect to the container with X11Forwarding.

### 1. Install X11 on your host machine
By default, if you try to show a figure on a container, you can't. Using X11, you can show it on your host machine, not the container.  
Please install X11 such as [XQuartz](https://www.xquartz.org/).

### 2. Configure settings for X11Forwarding
If you didn't pass through [Set up ssh](#set-up-ssh), please do it first in order to create private key in `~/.ssh/`.  
After confirming that private key exists in `~/.ssh/`, then you run following command:
```
$ echo -e Host deeplearning\\n"    "HostName 0.0.0.0\\n"    "Port 2222\\n"    "User root\\n"    "IdentityFile ~/.ssh/id_rsa\\n"    "ForwardX11Timeout 168h\\n"    "LogLevel DEBUG1\\n >> ~/.ssh/config
```

This command adds ssh settings into `~/.ssh/config`.
* `Host` is `deeplearning`, which you can specify at `HostName` argument of ssh command.
* `HostName` is `0.0.0.0`, which is container IPAddress by default.
* `Port` is `2222`, which is your host machine's port used when you connect to the container with ssh.
* `User` is `root`, which is the user when you connect to the container with ssh.
* `IdentityFile` is `~/.ssh/id_rsa`, which is your private key used on your host machine.
* `ForwardX11Timeout` is `168h`, which is duration which X11Forwarding is valid for. In some case, learning models may take some days so it is specified as one week total hours.
* `LogLevel` is `DEBUG1`, which is log level when you run ssh command. To get logs about X11Forwarding, it is specified as `DEBUG1`.

### 3. Expose 22 port for ssh
Then, expose 22 port of a container so that you can connect to the container with ssh.
In order to leave the container up after escaping from it, [`docker run`](https://docs.docker.com/engine/reference/commandline/run/) is not used here.  
```
$ docker create -it --name deeplearning -p 2222:22 resotto/deeplearning:0.0.1
$ docker start deeplearning
$ docker exec -it deeplearning /bin/bash
```

When you run [`docker create`](https://docs.docker.com/engine/reference/commandline/create/), you can expose port with `p` option, whose format is `${HOST_PORT}:${CONTAINER_PORT}`. When connecting to the container with ssh, you can specify `${IP_ADDRESS}:${HOST_PORT}` showed via [`docker ps`](https://docs.docker.com/engine/reference/commandline/ps/), so that you can connect to the container with ${CONTAINER_PORT}.

```
resotto:~$ docker ps -a
CONTAINER ID        IMAGE                          COMMAND                  CREATED             STATUS                   PORTS                     NAMES
3e1eb691372b        resotto/deeplearning:0.0.1     "/bin/bash"              24 hours ago        Up 24 minutes            0.0.0.0:2222->22/tcp      deeplearning
```

**Don't forget to add options `-it` when you run [`docker create`](https://docs.docker.com/engine/reference/commandline/create/)**. This is very important. If you did, the container would still remain exited even if you run [`docker start`](https://docs.docker.com/engine/reference/commandline/start/).

In fact, image `resotto/deeplearning:0.0.1` is already uploaded to [Docker Hub](https://hub.docker.com/signup). This image is different from the one used at [Getting Started](#getting-started), whose name is `resotto/ubuntu-python3:0.0.1`. This image is also built by Dockerfile, which some orders are added to this repository's one.  
Added orders are below:
```
# Deep Learning
RUN apt-get install -y xorg
RUN apt-get install -y openssh-server
RUN pip3 install numpy
RUN pip3 install matplotlib
RUN pip3 install sklearn
RUN pip3 install tensorflow
RUN pip3 install keras
RUN echo "root:root" | chpasswd
RUN echo PermitRootLogin yes >> /etc/ssh/sshd_config
RUN echo X11UseLocalhost no >> /etc/ssh/sshd_config
```

Some softwares are installed, and some settings are changed.
* `xorg` is used for `X11Forwarding` on container.
* `sshd` is installed via `openssh-server`, so that you can connect to the container with ssh.
* `chpasswd` is used for changing specific user's password. If you run `echo "${USER}:${PASSWORD}"` and pipe it to `chpasswd`, the password of the user is registered. Here, root user's password is set to `root`.
* `/etc/ssh/sshd_config` is used for `sshd`. Via redirect, if `PermitRootLogoin yes` is added into sshd_config, you can connect to the container with root user, and if `X11UseLocalhost no` is added into it, forwarding X server should be bound to the wildcard IPAddress.  
Please remember that container default IPAddress is [`0.0.0.0`](https://docs.docker.com/v17.09/engine/userguide/networking/default_network/binding/), which is wildcard IPAddress. So you can connect to the container with X11Forwarding.

### 4. Install Tkinter and start ssh service
After running [`docker exec`](https://docs.docker.com/engine/reference/commandline/exec/), let's upgrade installed softwares.
```
$ apt-get update && apt-get upgrade -y
$ pip3 install -U numpy && pip3 install -U matplotlib && pip3 install -U sklearn && pip3 install -U tensorflow && pip3 install -U keras
```

And then, please install [Tkinter](https://docs.python.org/3.7/library/tkinter.html).
```
$ apt-get install -y python3-tk
```

This install will ask you about your geographic area and city/region where you live.
```
Please select the geographic area in which you live. Subsequent configuration questions will narrow this down by presenting a list of cities, representing
the time zones in which they are located.

  1. Africa   3. Antarctica  5. Arctic Ocean  7. Atlantic Ocean  9. Indian Ocean    11. System V timezones  13. None of the above
  2. America  4. Australia   6. Asia          8. Europe          10. Pacific Ocean  12. US
Geographic area:

Please select the city or region corresponding to your time zone.

  1. Aden      11. Baku        21. Damascus          31. Hong Kong  41. Kashgar       51. Makassar      61. Pyongyang  71. Singapore      81. Ujung Pandang
  2. Almaty    12. Bangkok     22. Dhaka             32. Hovd       42. Katmandu      52. Manila        62. Qatar      72. Srednekolymsk  82. Ulaanbaatar
  3. Amman     13. Barnaul     23. Dili              33. Irkutsk    43. Khandyga      53. Muscat        63. Qostanay   73. Taipei         83. Urumqi
  4. Anadyr    14. Beirut      24. Dubai             34. Istanbul   44. Kolkata       54. Nicosia       64. Qyzylorda  74. Tashkent       84. Ust-Nera
  5. Aqtau     15. Bishkek     25. Dushanbe          35. Jakarta    45. Krasnoyarsk   55. Novokuznetsk  65. Rangoon    75. Tbilisi        85. Vientiane
  6. Aqtobe    16. Brunei      26. Famagusta         36. Jayapura   46. Kuala Lumpur  56. Novosibirsk   66. Riyadh     76. Tehran         86. Vladivostok
  7. Ashgabat  17. Chita       27. Gaza              37. Jerusalem  47. Kuching       57. Omsk          67. Sakhalin   77. Tel Aviv       87. Yakutsk
  8. Atyrau    18. Choibalsan  28. Harbin            38. Kabul      48. Kuwait        58. Oral          68. Samarkand  78. Thimphu        88. Yangon
  9. Baghdad   19. Chongqing   29. Hebron            39. Kamchatka  49. Macau         59. Phnom Penh    69. Seoul      79. Tokyo          89. Yekaterinburg
  10. Bahrain  20. Colombo     30. Ho Chi Minh City  40. Karachi    50. Magadan       60. Pontianak     70. Shanghai   80. Tomsk          90. Yerevan
Time zone:
```

Then, please start ssh service in order to connect to this container with ssh.
```
$ service ssh start
```

If you forgot to start ssh service and connected to the container with ssh, following message would be displayed:
```
ssh_exchange_identification: Connection closed by remote host
```

### 5. Connect to the container with X11Forwarding
At last, it is time for ssh.  
First of all, open your ssh-client which is capable of X11Forwarding. Here, let's assume using [XQuartz](https://www.xquartz.org/) terminal.

And then, please connect to the container you configured above with ssh.
```
$ ssh deeplearning
```

You also can connect to the container with ssh explicitly:
```
$ ssh -Xv -p 2222 root@0.0.0.0
```

* `-Xv` is coupled with `-X` and `-v`. `X` option means permitting X11Forwarding. `v` option means showing debug messages.
* `p` option means specifying port where you connect to the remote host.

If you run container from `resotto/deeplearning:0.0.1`, a python3 file, which operates learning and predicting Sine wave with TensorFlow GRU, is included in the container `/app`.  
So let's try it:
```
$ cd /app/
$ python3 gru_sin_tensorflow.py
```

After learning models, if this kind of figure is displayed, awesome!
<img width="639" alt="2019-02-20 8 47 43" src="https://user-images.githubusercontent.com/19743841/53055925-d9a66e00-34ec-11e9-8c28-f93eea9b71e9.png">

## References
* [Docker Reference documentation](https://docs.docker.com/reference/)
