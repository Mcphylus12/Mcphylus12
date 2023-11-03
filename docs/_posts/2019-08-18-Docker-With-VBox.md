---
layout: page
Class: post-content

Title: Docker on Windows with docker-machine and VirtualBox
Author: Kyle Alexander

Category: Environments
Tags: [Docker, Jekyll, Windows, Chocolatey, VirtualBox]

#permalink: /draft/
visible: true
excerpt_separator: <!--more-->
---

# Docker from scratch on Windows Home

## A walkthrough on getting docker set up from the command line without Docker For Windows

---

### Intro and Packages

I have always liked the idea of using containers where ever possible but my most recent laptop purchases have all arrived installed with Windows Home and as some might know the lovely Docker for windows needs 64 bit pro or better. I knew that <!--more-->docker toolbox existed but docker themselves mark it as a legacy solution and so I was curious as to whether there is a more DIY way to getting docker working and functional on systems that cant use Docker for windows.

After a quick search I came across [This article](https://golb.hplar.ch/2019/01/docker-on-windows10-home-scratch.html) which explains how to use the docker-machine and virtual box to get started. Give the article a read since it covers compose and Kubernetes which this will not. I used the initial docker material in the article to get started myself but I wanted a more seamless way to get everything set up.

For getting everything you need if you're on Windows and haven't used Chocolatey or some other package manager then go check it out at [https://chocolatey.org/](https://chocolatey.org/). In a nutshell its a command line package manager for Windows. As a bonus for this walkthrough it also contains packages for everything we need to get started.

That would be
- docker-cli
- docker-machine
- VirtualBox

> Just a heads up that some of the future commands make use of pipes so its best to do this in PowerShell.

```PowerShell
choco install docker-cli
choco install docker-machine
choco install virtualbox
```

### Docker

If you've never used docker or VirtualBox then here is a quick summary of each package. The docker-cli does what is says on the tin and is the main tool for running and managing containers. Docker-machine is a command line tool by docker to manage other computers. It is used to set up hosts for the core docker daemon. In our case we are using it to create and manage(mostly) a guest machine inside VirtualBox. VirtualBox is software for creating and managing virtual machines. docker-machine only utilizes a small amount of VirtualBox's capabilities and this article only touches on a subset of that. If you want to learn all there is about VBox then head to [https://www.virtualbox.org/](https://www.virtualbox.org/).

It might be a bit late at this point but from here on out you'll need to know about the basic docker concepts like containers, images, volumes, and ports(by which I mean -p). If any of that is foreign to you then its well worth going through the [Docker Get Started Guide](https://docs.docker.com/get-started/).

Anyway for the purposes of this article I will be using the Jekyll image. For those who don't know Jekyll it's a static site builder on ruby that this whole site is built on. For now you only need to know that Jekyll has the following functionality.

- Jekyll new *projectName* - Creates a new Jekyll site in the folder *projectName* with a scaffolded welcome page and post
- Jekyll serve - Build and creates a static host for the project that can be viewed as a website (this is for development. use build and host statically for production)

### Getting Started

```PowerShell
docker-machine create -d "virtualbox" default
```

This will create a Virtual Machine (a VM) using virtual box with the name default. default is a nice name as the docker-machine cli assumes we are operating on this machine for most of its operations if a different VM name is not specified in the command.

As we have the VM between us and the docker containers we need to make any folders that we will use as docker volumes shared folders between our machine and the VM so everything is synced.

```PowerShell
docker-machine stop #stops the VM
vboxmanage sharedfolder add default --name dockermnt --hostpath "\\?\c:\dockermnt" --automount #requires a stopped VM
docker-machine start
```

This will share the folder at the root of both machines called dockermnt (make sure by this point you have a a folder called dockermnt existing in the location used in the above command). I know people will ask why the question mark and to be honest I don't know but it matches the folder share created by default if you look the VM settings in VirtualBox. I assume it has to do with how Unix and Windows path structures are mapped to one another. 

We need to do one more thing as the default user for docker operations does not have access to the dockermnt folder automatically.

```PowerShell
docker-machine ssh
sudo su
chown -R docker /dockermnt
exit
exit
```

This gives any operations performed by docker permissions to write into the folder. This is a bonus as most images need to write to the VM to create persistence for the containers.

Before we can use the docker client to mess with containers we need to make sure it is is pointing at the VM so any instructions are executed in the VM where the docker daemon is running.

```PowerShell
docker-machine env
```

This will print a collections of instructions to set the environment for the docker cli. I would advise using PowerShell and running

```PowerShell
docker-machine env | Invoke-Expression
```

This will evaluate the output and set up the variables automatically.

Now we can run

```PowerShell
docker run --rm -v="/dockermnt/jekyll:/srv/jekyll" -it jekyll/jekyll jekyll new dockertest
```

A quick overview of what's in the command first. 
- docker run is the base command for setting up and starting a container 
- --rm is a switch to tell docker to clean up any anonymous volumes or containers created by the running of the container. This includes the container itself as it hangs about after finishing for debugging. It is a nice switch to use if your container runs a single command and then dies as it avoids hanging containers piling up.
- -v specifies the mount. This exact command tells docker to link the /dockermnt/jekyll folder in the VM to the /srv/jekyll folder that will run inside the container (/srv/jekyll is a folder specified by the image we are using). /dockermnt/jekyll in the VM is automatically synced to the folder of the same name on our host machine due to the folder share.
- -it is a pair of switches to attach terminal in and out streams to the docker container. As this particular command doesn't need anymore input after the initial command then you may only need -t. -it is a nice switch to use in general if you want to interact in anyway with the terminal inside the docker container.
- jekyll/jekyll specifies the docker image we want to run. look for it on dockerhub for more info.
- jekyll new dockertest is the initial command for the container to run once started. This is a standard jekyll command to scaffold a new jekyll site with a welcome page and post.

Once the command has executed and the container has finished you should now be able to see the whole site in C:\dockermnt\jekyll on your host machine. If the container output looks successful but you cant see the folder then you should run

```PowerShell
docker ssh
```

and go into the dockermnt folder and see if the files are present in there. If they are present then the sharedfolder for virtualbox is not working otherwise the docker volume has not worked possibly due to a command typo.

If none of these issues have happened then you've successfully got docker working with volumes.

> Remember it will only work for volumes that are based out of the /dockermnt folder as this is the place we've given docker permission to write to.

### Networks

The other important thing with docker is you'll usually want to run a server from within the container. For this we can test with *jekyll serve* which starts a web server to serve the site from.

```PowerShell
docker run --rm -v="/dockermnt/jekyll/dockertest:/srv/jekyll" -p 80:4000 -it jekyll/jekyll jekyll serve
```

Just to note some changes.
- The volume mount is now /dockermnt/jekyll/dockertest as we want to execute the command inside the jekyll project that we previously created.
- the command is now *jekyll serve* not *jekyll new*
- The all new -p switch is used to define port forwarding from the container onto the host machine. Here we are mapping port 4000 from **within** the container to port 80 of the VM

The networking is another thing that potentially has an extra step as our container host is a VM. By default the VM has a host only adapter attached which means the VM is automatically accessible from the host machine. You can use 

```PowerShell
docker-machine ip
```

to get the IP of the VM and then access anything hosted on it. As we are forwarding to port 80 the ip is enough for a browser to find our hosted jekyll site. If everything is working you have the extra possibility of adding -d to the docker run to serve the jekyll site in a detached container (if you do this will need to run *docker attach* with the container id to cancel the command and end the container when finished). Usually this way of viewing the container is enough as anything beyond development should probably be done on Linux machines that can run docker without the need for VirtualBox. However if you do want to expose the server to a network you need to configure the VM to use a bridged network interface and potentially forward ports. More info at [https://www.virtualbox.org/manual/ch06.html](https://www.virtualbox.org/manual/ch06.html).

### Notes

Thanks for reading this is literally my first article of hopefully many. I imagine the articulation leaves something to be desired and the styling is a permanent work in progress to make for easy reading (hence PWIP). If you have any issues with the post feel free to raise them as issues on the repo's issues board (tag with the post title). I am looking into comments powered by a 3rd party script but haven't worried too much about it so far.