---
title: "Introduction to Docker"
date: 2018-05-03
slug: "introToDocker"
description: "Introduction to Docker"
keywords: ["docker", "container"]
draft: false
#tags: ["docker", "container"]
math: false
toc: true
---

Docker is an important tool for DevOps professionals to master.  This article offers a video tutorial of what Docker is and what its advantages are, and ends with a basic demonstration of Docker.  
  
{{< youtube E4URWSp5RmI >}}

# What is a Container?

A container image is a lightweight, stand-alone, executable package of a piece of software that includes everything needed to run it: code, runtime, system tools, system libraries, settings.

Containers can be thought of as the next generation of virtual machines – they are self-sufficient units that can be shared between developers and a production environment. A container has everything within it to run in the exact same way, regardless of which computer the container is running on.

# Containers vs. Virtual Machines

With virtual machines, each instance of a Virtual Machine requires all of the components of the guest operating system. This means that each VM requires a specified amount of RAM and storage space, and that is replicated over and over, even if the VM is doing nothing. Starting up a VM can take a couple of minutes; therefore, they have to be running at all times to meet customer expectations. VMs run on a hypervisor, which also takes up its own resources.

Containers only have the parts of the operating system that are absolutely necessary for them to operate correctly. This makes containers much more efficient. They don’t run unnecessary resources in the background, don’t take up idle space, and only run when necessary. Starting up a container is virtually instant, so containers can be stopped when demand is low and then generated when demand is high while still meeting customer expectations. Containers run on containerization software, which plays a similar role to a hypervisor.

# Is Docker the Only Type of Container?

There are many different companies that develop containers. Docker is not an entirely unique technology or even the first of its kind. However, since its release in 2014, it has exploded in popularity and is currently the most popular container in the IT industry.

# Installing Docker

Docker runs natively on Linux. In Windows and Mac, it needs a piece of software called Docker Machine or Docker Quickstart. This adds a small layer of complexity but is well worth the trouble.

Installing Docker on Ubuntu 17.10 is a really easy task. Simply type `sudo apt-get install docker.io` and then start the service with `sudo service docker start`. Docker is this easy to install on Linux distros. Runnable has written a nice guide to Install Docker on Linux for your preferred distribution.

# Running the First Container

After installing Docker, you can create your first simple container by typing `sudo docker run -ti ubuntu bash`.

This container is a scaled-back version of Ubuntu 16.04 with only the things that are absolutely necessary installed and running. In a moment, we’ll compare the running processes of an Ubuntu host computer to the running processes of an Ubuntu container. Watch the video above for a demonstration.

# Develop & Deploy Custom Containers

So here is how the Docker Workflow works. Typically, the starting point is from a publicly available image. The container is modified to your needs, stopped, and committed to serving as the starting point to build from. So we start a container from an image, which is usually downloaded from the internet.

You change that container to suit your needs – adding software packages, custom code, whatever you (or your organization) requires.

You stop the container and commit it to your own custom image. Initially, this resides on your local computer but can be stored in an online repository such as DockerHub.

From your custom container, you can create as many instances of the new custom container as is needed – so each developer can have their own copy of the environment, multiple containers can reside on the server, and so on.

# Containers Play Well Together

Each container has its own networking capability. It is possible to open and close ports, sending information to and from containers.

Containers can be created with DockerFiles, which are similar to BASH scripts. Each line creates a new container from the product of the last line. This means you can build up a huge system of containers using a Dockerfile.

Containers are environment-agnostic – they only require that Docker is installed and don’t care about the operating system. Containers will run the same anywhere and everywhere.

# Why Use Docker?


## Increase Robustness

Deploying and updating applications is easier and faster. This means faster to market and better customer experience.

Applications are more scalable because new instances of the software are instantly available.

Unfortunately, orchestrating numerous containers may increase the complexity of applications, but the benefits outweigh the complexity added. Most organizations (and individuals) find that the benefits outweigh this added complexity.

## Improve Performance

Eliminating an application’s reliance on Virtual Machines will save system resources. Instead of dedicated RAM being used improperly, containers use only what is actually necessary. It is anticipated that most enterprise clients will already have Docker installed.

## Modular Design

The most important benefit of containers is that sections of the code can be divided up and only used if the enterprise client needs them. This will make deployment much easier and scalable. As each aspect of the application being developed is separate, if there is a problem with one, the entire application need not fail. Finding and fixing bugs is easier, and sending patches to clients is easier (because they’re smaller!)

Finally, because containers are environment-agnostic, there will not be any need to develop multiple versions of the software at the time. There is no need to write software that will play nicely with various other software or hardware. If the target machine has Docker, the container will run the same as everywhere else.
