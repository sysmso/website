---
title: "TP : How to build a Singularity container"
date: 2018-09-18T11:38:57+01:00
categories: ["tutorial"]
tags: ["HPC","HTC","Singularity","Linux","container"]
language: en
---
# How to build a Singularity container

This recipe describes how to build an Ubuntu image using Singularity, with the latest Singularity 2 version (2.6). You can download Singularity and find some docs here : https://www.sylabs.io/docs/

*All the files mentionned in this recipe are available in the following git repository : https://gitlab.in2p3.fr/souchal/tp-singularity*

**This recipe is for Linux only. You need superuser rights (sudo)**

## My first container

* Singularity installation
* Build images from scratch
* Interact with images

### Singularity installation

    git clone https://github.com/singularityware/singularity.git
    cd singularity
    git fetch --all
    git checkout 2.6.0
    ./autogen.sh
    ./configure --prefix=/usr/local
    make
    sudo make install

The --help option gives an overview of Singularity options and subcommands as follows:

singularity help
    USAGE: singularity [global options...] <command> [command options...] ...

    GLOBAL OPTIONS:
        -d|--debug    Print debugging information
        -h|--help     Display usage summary
        -s|--silent   Only print errors
        -q|--quiet    Suppress all normal output
        --version  Show application version
        -v|--verbose  Increase verbosity +1
        -x|--sh-debug Print shell wrapper debugging information

    GENERAL COMMANDS:
        help       Show additional help for a command or container                  
        selftest   Run some self tests for singularity install                      

    CONTAINER USAGE COMMANDS:
        exec       Execute a command within container                               
        run        Launch a runscript within container                              
        shell      Run a Bourne shell within container                              
        test       Launch a testscript within container                             

    CONTAINER MANAGEMENT COMMANDS:
        apps       List available apps within a container                           
        bootstrap  *Deprecated* use build instead                                   
        build      Build a new Singularity container                                
        check      Perform container lint checks                                    
        inspect    Display container's metadata                                     
        mount      Mount a Singularity container image                              
        pull       Pull a Singularity/Docker container to $PWD                      

    COMMAND GROUPS:
        image      Container image command group                                    
        instance   Persistent instance command group                                


    CONTAINER USAGE OPTIONS:
        see singularity help <command>

    For any additional help or support visit the Singularity
    website: https://www.sylabs.io/

### Build images from scratch

A Singularity Recipe is the driver of a custom build, and the starting point for designing any custom container. It includes specifics about installation software, environment variables, files to add, and container metadata. You can even write a help section, or define modular components.

In the Docker world, containers recipe are called Dockerfile. With Singularity you can use Dockerfile or Singularity Recipe. You can also import already built Docker container.

#### 1) Building containers from Singularity recipe files

The header is at the top of the file, and tells Singularity the base Operating System that it should use to build the container. It is composed of several keywords. Specifically:

* Bootstrap: references the kind of base you want to use (e.g., docker, debootstrap, shub). For example, a shub bootstrap will pull containers for shub as bases. A Docker bootstrap will pull docker layers to start your image. For a full list see build
* From: is the named container (shub) or reference to layers (Docker) that you want to use (e.g., vsoch/hello-world)

Depending on the value assigned to Bootstrap, other keywords may also be valid in the header.
For example, a very minimal Singularity Hub build might look like this:

    Bootstrap: docker
    From: ubuntu:latest

Create a text file with the recipe above. Call it example.def.

If you wanted to create a container within a writable directory (called a sandbox) you could do so with the --sandbox option. It’s possible to create a sandbox without root privileges, but to ensure proper file permissions it is recommended to do so as root.

Let's try to build our container with the minimal recipe, in sandbox mode to be able to add stuff later :

    sudo singularity build --sandbox example.img example.def

The resulting directory operates just like a container in an image file. You are permitted to make changes and write files within the directory, but those changes will not persist when you are finished using the container. To make your changes persistent, use the --writable flag when you invoke your container. 

Let's try to add wget command in our container :

    sudo singularity shell --writable example.img
    Singularity: Invoking an interactive shell within container...
    Singularity example.img:~> apt-get update
    Singularity example.img:~> apt-get install wget build-essential

To exit the container, just type "exit".

In a recipe file, the command above can be added in the %post section :

    %post
    apt-get update && apt-get -y install wget build-essential

Our example.def file now looks like this :

    Bootstrap: docker
    From: ubuntu:latest

    %post
    apt-get update && apt-get -y install wget build-essential

Commands in the %post section are executed within the container after the base OS has been installed at build time. Here we are installing gcc and wget for an Ubuntu bootstrap.

Let's rebuild our container :

   sudo singularity build --sandbox example.img example.def

#### 2) Building Singularity container from a Docker image

You can shell, import, run, and exec Docker images directly from the Docker Registry. You don’t need Docker installed on your machine, but you will need a working Internet connection. You don't need to be root to do Docker import.

Let’s use the same image from 1) already pushed in the Dockerhub :

    singularity build --sandbox example.img docker://sysmso/docker-openmpi

### Interact with images

Once you have an image, you can interact with it in several ways. 

The shell command allows you to spawn a new shell within your container and interact with it as though it were a small virtual machine.

    singularity shell example.img
    Singularity: Invoking an interactive shell within container...
    Singularity.example.img> $
    Singularity.example.img> $ gcc --version
    gcc (Ubuntu 7.3.0-16ubuntu3) 7.3.0
    Copyright (C) 2017 Free Software Foundation, Inc.
    This is free software; see the source for copying conditions.  There is NO
    warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
    Singularity.example.img> $ exit

As of Singularity v2.4 by default build produces immutable images in the squashfs file format. This ensures reproducible and verifiable images. However, during testing and debugging you may want an image format that is writable. This way you can shell into the image and install software and dependencies until you are satisfied that your container will fulfill your needs. To do that, use --writable option :

    singularity shell --writable example.img
    Singularity.example.img> $ apt install vim

If you enter exact command above, it should not work because your are not root. With Singularity, you are the same user outside and inside the container, it's why to be root in your container you have to use sudo command.

If you open a shell in your container, you will see every process running on the host. If you want to run your container in a different namespace, you can use -p option.

Try to run your container with and without -p and enter top command.

Files on the host are reachable from within the container :

    touch toto.txt
    singularity shell example.img
    Singularity.example.img> $ ls
    Singularity.example.img> $ toto.txt

By default singularity bind mounts /home/$USER, /tmp, and $PWD into your container at runtime. You can specify additional directories to bind mount into your container with the - -bind option. In this example, the data directory on the host system is bind mounted to the /mnt directory inside the container.

If you want a container without access to your home directory, you can use another home directory with the following option :

    mkdir -p /tmp/temp_home_${USER}
    singularity shell -H /tmp/temp_home_${USER}:/home/${USER} example.img
    Singularity.example.img> $ ls

This is very useful to avoid unexpected behavior due to configuration files in home directory. 

To add files inside the container, you have to use the %files section :

    %files
    tp-singularity/mpi-ping.c /data/mpi-ping.c

The full example.def file now looks like this :

   Bootstrap: docker
    From: ubuntu:latest

    %files
    tp-singularity/mpi-ping.c /mpi-ping.c

    %post
    apt-get update && apt-get -y install wget build-essential

To add the mpi-ping.c file inside your container, you have to rebuild :

    sudo singularity build --sandbox example.img example.def
    singularity shell example.img
    Singularity.example.img> $ ls /mpi-ping.c

Now you have the compilateur and the source code to compile with, inside your container.