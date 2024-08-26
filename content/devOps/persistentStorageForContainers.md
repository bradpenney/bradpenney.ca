---
title: "Persistent Storage For Containers"
date: 2021-03-20
slug: "persistentStorageForContainers"
description: "Persistent Storage For Containers"
keywords: ["storage", "container"]
draft: false
#tags: ["storage", "container"]
math: false
toc: true
---

With the ongoing prevalence of containerized applications and container orchestration, one of the main questions a new developer faces is: “How do containerized applications access and write to permanent data?” This brief article will introduce persistent storage for containers and how multiple containers can access the persistent changes to data.

## The Problem: Containers Have Ephemeral Storage

One of the first challenges for a developer is that any changes that are made within a container are lost as soon as the container is destroyed. For example, if an instance of Apache was created, and we changed the default website a little, once that container is destroyed the changes are lost. Here’s how that looks:

``` bash
brad@ryzen5:~$ docker run -d --name myApacheApp -p 8999:80 httpd:2.4
Unable to find image 'httpd:2.4' locally
2.4: Pulling from library/httpd
6f28985ad184: Pull complete 
3a141a09d1d0: Pull complete 
1633384edb75: Pull complete 
acb3e3b931b8: Pull complete 
f6dc6b8b1d70: Pull complete 
Digest: sha256:9625118824bc2514d4301b387c091fe802dd9e08da7dd9f44d93ee65497e7c1c
Status: Downloaded newer image for httpd:2.4
00faeccd0631db6b0ff6f27dae39c99336d9de0737342c38c20d0ca8b77a2603
```

Which results in a page that looks like this (note it is using port 8999):

![Apache App Start Page](/devops/apacheAppStartPage01.png)

The page is working, and the web design is sublime. Making changes to this basic webpage can be made by simply hopping inside the container and modifying the

`index.html` file:

``` bash
brad@ryzen5:~$ docker exec -it myApacheApp /bin/bash
root@00faeccd0631:/usr/local/apache2# cd /usr/local/apache2/htdocs
root@00faeccd0631:/usr/local/apache2/htdocs# ls -ltr
total 4
-rw-r--r-- 1 root src 45 Jun 11  2007 index.html
root@00faeccd0631:/usr/local/apache2/htdocs# echo '<html><body><h1>Stellar Web Design Ahead!</h1></body></html>' > index.html
root@00faeccd0631:/usr/local/apache2/htdocs# exit
exit
```

Our new redesigned and re-imagined web page now looks like this:

![Apache App Start Page](/devops/apacheAppStartPage02.png)

All joking aside, we’ve clearly changed the contents of a website inside a container. The problem: once that container is destroyed the changes will be gone. Or if a new container is built, the changes won’t persist over to it. By default, containers only have ephemeral storage. To prove that, lets spin up another container from the same image, but pointing to a different port (8555) and see that the changes aren’t permanent:

``` bash
brad@ryzen5:~$ docker run -d --name mySecondApacheApp -p 8555:80 httpd:2.4
180f146643c7c7546bc9e43490f702128de5c9ac5a705e4199dd716f2f839a16
```

Which results in:

![Apache App Start Page](/devops/apacheAppStartPage03.png)

Making changes inside a container is possible (as demonstrated) but that isn’t a smart solution. Designing a whole web site (or web application) inside one container is a very risky idea – what if the container gets destroyed? What if the web app needs more than one container to balance the load? There is a whole industry of software systems like Kubernetes and RedHat OpenShift that orchestrate containers based on demand. Containers are meant to be treated like interchangeable widgets coming off the factory line, not unique snowflakes.

## The Solution: Persistent Storage!

Pointing containers at persistent storage solves this problem. There are two basic ways to do this – Docker volumes and manual directory pointing. For this demonstration, we’ll stick with the (simple) manual method and have a discussion about Docker volumes at another time.

The one requirement is to have some appropriate content to point your container towards. This could be a basic HTML/CSS/JS website, a PHP application, a full on MySQL database, or many other resources. I happen to have a PHP website laying around on my local system at `/var/www/bradpenney/html`. Using the `-v` (volume) flag in the docker command points it at that directory, and it will pick up the website and display it. The container uses the local host storage as its own:

``` bash
brad@ryzen5:~$ docker run -d -p 8005:80 --name myApachePhpApp -v /var/www/bradpenney/html:/var/www/html php:7.2-apache
Unable to find image 'php:7.2-apache' locally
7.2-apache: Pulling from library/php
6ec7b7d162b2: Pull complete 
db606474d60c: Pull complete 
afb30f0cd8e0: Pull complete 
3bb2e8051594: Pull complete 
4c761b44e2cc: Pull complete 
c2199db96575: Pull complete 
1b9a9381eea8: Pull complete 
fd07bbc59d34: Pull complete 
72b73ab27698: Pull complete 
983308f4f0d6: Pull complete 
6c13f026e6da: Pull complete 
e5e6cd163689: Pull complete 
5c5516e56582: Pull complete 
154729f6ba86: Pull complete 
Digest: sha256:4dc0f0115acf8c2f0df69295ae822e49f5ad5fe849725847f15aa0e5802b55f8
Status: Downloaded newer image for php:7.2-apache
53cb541bc10f6b5c826e6222e7badaf2a6a246766e1833d0a1c8d6f946d98004
```

And now the website is being displayed at Port 8005 on my local computer:

![Apache App Start Page](/devops/apacheAppStartPage04.png)

Now, if I modify the index.php file found at `/var/www/bradpenney/html` on my local machine by adding `<h1>Persistent Container Storage Is Interesting!</h1>`, this shows up in the containerized website (after a refresh):

![Apache App Start Page](/devops/apacheAppStartPage05.png)

Now for the payoff – if we create another container that points to the same persistent storage location but on a different port, the changes will persist! That looks like this:

``` bash
brad@ryzen5:/var/www/bradpenney/html$ docker run -d -p 8666:80 --name mySecondApachePhpApp -v /var/www/bradpenney/html:/var/www/html php:7.2-apache
a2fe1b18e55665f5d487f85252d8bb4946055daaae730d6cc0d343311feaab24
```

Which spins up a second container with a second PHP application (on port 8666), but the changes persist:

![Apache App Start Page](/devops/apacheAppStartPage06.png)

Of course, this is the most basic example to demonstrate persistent storage in containers – it gets a lot more complicated from here. As mentioned above, it is possible to orchestrate multi-container applications use full-fledged databases, all of which are accessing persistent storage volumes. However, the basic concept is the same – initialize containers as needed, but keep the required persistent (stateful) data in standard storage solution on local machine, not within the container.
