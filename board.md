
## **The bad old day**

Every time a business needed a new application, it had to buy a new server. Unfortunately, we weren’t very good at modelling the performance requirements of new applications, and the IT departments had to guess.

This often resulted in businesses buying very expensive servers with a lot more performance capability than the apps needed.

As a result, companies ended up with racks and racks of overpowered servers operating as low as 5-10% of their potential capacity

## **Hello VMware**

VMware allowed us to safely run multiple business applications on a single server.

Businesses could run new apps on the spare capacity of existing servers, spawning a golden age of maximizing the value of existing assets.

#### drawback
A feature of the VM model is every VM needing its own dedicated operating system (OS). Unfortunately, this has several drawbacks, including: • Every OS consumes CPU, RAM, and other resources we’d rather use on applications • Every VM and OS needs patching • Every VM and OS needs monitoring VMs are also slow to boot and not very portable

## **Hello Containers!**

A feature of the container model is that every container shares the OS of the host it’s running on. This means a single host can run more containers than VMs. For example, a host that can run 10 VMs might be able to run 50 containers, making containers far more efficient than VMs.

Containers are also faster and more portable than VMs.


## **The Docker technology**

At a high level, there are two major parts to the Docker platform: 
	• The CLI (client) 
	• The engine (server)

The CLI is the familiar docker command-line tool for deploying and managing containers. It converts simple commands into API requests and sends them to the engine.

The engine comprises all the server-side components that run and manage containers.

The client and engine can be on the same host or connected over the network.
![[Pasted image 20250107123402.png]]

Type docker commands into the CLI, the CLI converts them to API requests and sends them to the daemon, and the daemon takes care of everything else.

![[Pasted image 20250107123540.png]]

---


==Docker daemon and client on the same host or connect your local Docker client to a remote daemon running on another host==

### 1. **Running Docker Daemon and Client on the Same Host (Default)**

By default, the Docker **client** (CLI) communicates with the **Docker daemon** running on the same machine. The Docker daemon listens on a **Unix socket** (`/var/run/docker.sock`), and we can use the `docker` commands without needing additional configuration.

---

### Docker images

Images are the building blocks of the Docker world. Images are the ”build” part of Docker’s life cycle. They have a layered format, using Union file systems, that are built step-by-step using a series of instructions. 
For example: 
	• Add a file. 
	• Run a command. 
	• Open a port. You can consider images to be the ”source code” for your containers.

### Registries
Docker stores the images you build in registries. There are two types of registries:
public and private. Docker, Inc., operates the public registry for images, called
the Docker Hub. Create an account on the Docker Hub and use it to share
and store your own images.

The Docker Hub also contains, at last count, over 10,000 images that other people
have built and shared.

### Containers
Containers are launched from images and can contain one or more running processes. Think about images as the building or packing aspect of Docker and the containers as the running or execution aspect of Docker

A Docker container is: 
	• An image format. 
	• A set of standard operations. 
	• An execution environment.

Like a shipping container, Docker doesn’t care about the contents of the container when performing these actions; for example, whether a container is a web server, a database, or an application server. Each container is loaded the same as any other container. 

Docker also doesn’t care where you ship your container: you can build on your laptop, upload to a registry, then download to a physical or virtual server, test, deploy to a cluster of a dozen Amazon EC2 hosts, and run. Like a normal shipping container, it is interchangeable, stackable, portable, and as generic as possible.

### Compose and Swarm
In addition to solitary containers we can also run Docker containers in stacks and clusters, what Docker calls swarms. 
The Docker ecosystem contains two more tools: 
	•Docker Compose - which allows you to run stacks of containers to represent application stacks, for example web server, application server and database server containers running together to serve a specific application. 
	• Docker Swarm - which allows you to create clusters of containers, called swarms, that allow you to run scalable workloads.

---

## Why to use Docker?

Docker make excellent sandboxes for a variety of testing purposes.

Some of the examples of Docker running out in the wild include: 
- Helping make your local development and build workflow faster, more efficient, and more lightweight. Local developers can build, run, and share Docker containers. Containers can be built in development and promoted to testing environments and, in turn, to production. 
- Running stand-alone services and applications consistently across multiple environments, a concept especially useful in service-oriented architectures and deployments that rely heavily on micro-services. 
- Using Docker to create isolated instances to run tests like, for example, those launched by a Continuous Integration (CI) suite like Jenkins CI. 
- Building and testing complex applications and architectures on a local host prior to deployment into a production environment.
- Building a multi-user Platform-as-a-Service (PAAS) infrastructure. • Providing lightweight stand-alone sandbox environments for developing, testing, and teaching technologies, such as the Unix shell or a programming language. 
- Software as a Service applications;
- Highly performant, hyperscale deployments of hosts

---

## Installing Docker

### Debian
#### Checking for prerequisites
##### Kernel
First,confirm that we're using sufficiently recent Linux kernel. 
```
$ uname -a 
Linux darknight.example.com 4.4.0-64-generic #85-Ubuntu SMP Mon Feb 20 11:50:30 UTC 2017 x86_64 x86_64 x86_64 GNU/Linux 
```

We see that we’ve got a 4.4.0 x86_64 kernel installed. This is the default for Ubuntu 16.04 and later.
We also want to install the linux-image-extra and linux-image-extra-virtual packages that contain the aufs storage driver.
Installing the linux-image-extra package 
```
$ sudo apt-get install linux-image-extra-$(uname -r) linux-image-extra-virtual 
```

If we’re using an earlier release of Ubuntu we may have an earlier kernel. 
We should be able to upgrade our Ubuntu to the later kernel via apt-get
Installing a 3.10 or later kernel on Ubuntu 
```
$ sudo apt-get update 
$ sudo apt-get install linux-headers-3.16.0-34-generic linuximage-3.16.0-34-generic linux-headers-3.16.0-34
```

Update the Grub boot loader to load our new kernel. 
```
$ sudo update-grub 
```
After installation,reboot host to enable the new 3.10 or later kernel.

To install Docker, we’re going to use the Docker team’s DEB packages.
```
sudo apt-get install \ apt-transport-https \ ca-certificates \ curl \ software-properties-common
```

Then add the official Docker GPG key.
```
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
```

And then add the Docker APT repository.
```
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

Update APT sources and install the Docker packages.
```
$ sudo apt-get update
$ sudo apt install -y docker-ce docker-ce-cli containerd.io
```

##### Docker and UFW
If using the UFW, or Uncomplicated Firewall, on Ubuntu. Docker uses a network bridge to manage the networking on containers. By default, UFW drops all forwarded packets. You’ll need to enable forwarding in UFW for Docker to function correctly. We can do this by editing the /etc/default/ufw file. Inside this file, change:

```
DEFAULT_FORWARD_POLICY="DROP"
```

To: 

```
DEFAULT_FORWARD_POLICY="ACCEPT"
```


### RHEL

```
$ sudo subscription-manager repos --enable=rhel-7-server-extras-rpms 
$ sudo yum install -y docker
```

### Using Script
```
$ sudo curl https://get.docker.com/ | sudo sh
```

#### The Docker daemon
By default, the daemon listens on a Unix socket at /var/run/docker. sock for incoming Docker requests. If a group named docker exists on our system, Docker will apply ownership of the socket to that group. Hence, any user that belongs to the docker group can run Docker without needing to use the sudo command.

---

## Running docker
```
$ sudo docker run -i -t ubuntu /bin/bash 
Unable to find image 'ubuntu:latest' locally latest: Pulling from library/ubuntu 43db9dbdcb30: Pull complete 2dc64e8f8d4f: Pull complete 670a583e1b50: Pull complete 183b0bfcd10e: Pull complete Digest: sha256: c6674c44c6439673bf56536c1a15916639c47ea04c3d6296c5df938add67b54b Status: Downloaded newer image for ubuntu:latest
```

The -i flag keeps STDIN open from the container, even if we’re not attached to it. This persistent standard input is one half of what we need for an interactive shell. The -t flag is the other half and tells Docker to assign a pseudo-tty to the container we’re about to create. This provides us with an interactive shell in the new container.

the command will provide this shell
```
root@f7cbdac22a02:/#
```

Docker has also added a host entry for our container with its IP address,so to get the ip
```
root@f7cbdac22a02:/# hostname -I 
172.17.0.4
```

The container only runs for as long as the command we specified, /bin/bash, is running. Once we exited the container, that command ended, and the container was stopped.
```
$sudo docker ps -a
CONTAINER ID IMAGE COMMAND CREATED STATUS PORTS NAMES 1cd57c2cdf7f ubuntu "/bin/bash" A minute Exited gray_cat
```

Press Ctrl-PQ to exit the container without terminating it. This will land your shell back at your local terminal, and your shell prompt will revert.

There are three ways containers can be identified: a short UUID (like f7cbdac22a02), a longer UUID (like f7cbdac22a02e03c9438c729345e54db9d 20cfa2ac1fc3494b6eb60872e74778), and a name (like gray_cat).

#### To name a container
```
$ sudo docker run --name bob_the_builder -i -t ubuntu /bin/bash 
root@aa3f365f0f4e:/# exit
```

#### Starting a stopped container
```
sudo docker start bob_the_container
```

```
$ sudo docker start aa3f365f0f4e
```

docker create command creates a container but does not run it.

#### Attaching to a container

```
$ sudo docker attach aa3f365f0f4e
```

`docker attach` and `docker exec -it` allow to interact with a running container, but they behave differently

- **`docker attach`**: Connects to the main process, so if you exit that process (e.g., exiting the shell), you'll stop the container.
	![[Pasted image 20250115155813.png]]
	![[Pasted image 20250115155854.png]]
	
- **`docker exec`**: Starts a new process, and even if you exit the new shell, the container will continue running.![[Pasted image 20250115155731.png]]


#### Creating daemonized containers
```
$ sudo docker run --name daemon_dave -d ubuntu /bin/sh -c "while true; do echo hello world; sleep 1; done"
1333bb1a66af402138485fe44a335b382c09a887aa9f95cb9725e309ce5b7db3
```

The docker run command with the -d flag to tell Docker to detach the container to the background.

With this combination of flags, instead of being attached to a shell like last container, the docker run command has instead returned a container ID and returned to command prompt.

#### Seeing what’s happening inside our container
```
$ sudo docker logs daemon_dave 
hello world
hello world 
hello world 
hello world 
hello world 
hello world 
hello world 
. . .
```

```
$ sudo docker logs -f daemon_dave 
hello world 
hello world 
hello world 
hello world 
hello world 
hello world 
hello world 
. . .
```

```
$ sudo docker logs -ft daemon_dave 
2024-10-02T03:31:16.743679596Z hello world 
2024-10-02T03:31:17.744769494Z hello world 
2024-10-02T03:31:18.745786252Z hello world 
2024-10-02T03:31:19.746839926Z hello world 
. . .
```

#### Docker log drivers
We can control the logging driver used by your daemon and container. This is done using the `--log-driver` option. You can pass this option to both the daemon and the docker run command.

There are a variety of options including the default json-file. Also available is syslog which disables the docker logs command and redirects all container log output to Syslog. Specify this with the Docker daemon to output all container logs to Syslog or override it using docker run to direct output from individual containers.

```
$ sudo docker run --log-driver="syslog" --name daemon_dwayne -d ubuntu /bin/sh -c "while true; do echo hello world; sleep 1; done" . . .
```

This will cause the daemon_dwayne container to log to Syslog and result in the docker logs command showing no output. 
Lastly, also available is none, which disables all logging in containers and results in the docker logs command being disabled.

#### Inspecting the container’s processes
```
$ sudo docker top daemon_dave
```

#### Docker statistics
```
$ sudo docker stats daemon_dave daemon_dwayne 
CONTAINER CPU % MEM USAGE/LIMIT MEM % NET I/O 
BLOCK I/O daemon_dave 0.14% 212 KiB/994 MiB 0.02% 5.062 KiB/648 B 1.69 MB / 0 B daemon_dwayne 0.11% 216 KiB/994 MiB 0.02% 1.402 KiB/648 B 24.43 MB / 0 B
```

#### Running a process inside an already running container
There are two types of commands we can run inside a container: background and interactive. Background tasks run inside the container without interaction and interactive tasks remain in the foreground.

```
$ sudo docker exec -d daemon_dave touch /etc/new_config_file
```

Command will create a new empty file called /etc/ new_config_file inside daemon_dave container.

Run interactive tasks like opening a shell inside daemon_dave container
```
$ sudo docker exec -t -i daemon_dave /bin/bash
```

The -t and -i flags, like the flags used when running an interactive container, create a TTY and capture STDIN for our executed process. We then specify the name of the container to run the command inside and the command to be executed. In this case our command will create a new bash session inside the container daemon_dave. We could then use this session to issue other commands inside our container.

#### Automatic container restarts
If container has stopped because of a failure we can configure Docker to restart it using the --restart flag. The --restart flag checks for the container’s exit code and makes a decision whether or not to restart it.

```
$ sudo docker run --restart=always --name daemon_alice -d ubuntu /bin/sh -c "while true; do echo hello world; sleep 1; done"
```
In this example the --restart flag has been set to always. Docker will try to restart the container no matter what exit code is returned. Alternatively, we can specify a value of on-failure which restarts the container if it exits with a non-zero exit code.

The on-failure flag also accepts an optional restart count.
`--restart=on-failure:5`
This will attempt to restart the container a maximum of five times if a non-zero exit code is received.

#### Finding out more about our container
The docker inspect command will interrogate our container and return its configuration information, including names, commands, networking configuration, and a wide variety of other useful data.

```
$ sudo docker inspect sad_edison 
[{ "ID": " c2c4e57c12c4c142271c031333823af95d64b20b5d607970c334784430bcbd0f ", "Created": "2014-05-10T11:49:01.902029966Z", "Path": "/bin/sh", "Args": [ "-c", "while true; do echo hello world; sleep 1; done" ], "Config": { "Hostname": "c2c4e57c12c4", 
. . .
```

We can also selectively query the inspect results hash using the -f or --format flag.
```
$ sudo docker inspect --format='{{ .State.Running }}' sad_edison 
true
```

```
sudo docker inspect --format '{{ .NetworkSettings.IPAddress }}' sad_edison 
172.17.0.2
```

#### Deleting a container

```
$ sudo docker rm 80430f8d0921 
80430f8d0921
```

There isn’t currently a way to delete all containers, but you can slightly cheat with a command like the following:
```
$ sudo docker rm -f `sudo docker ps -a -q`
```

---

## Working with Docker images and repositories

#### What is a Docker image?
- **Boot Filesystem (bootfs):**
    - The **`bootfs`** layer is akin to the boot filesystem in a typical Linux/Unix system. It contains the files and libraries needed for the initial boot process of the system.
    - In Docker's context, this is a temporary layer used during the container's initialization phase.

- **Interaction with Docker Users:**   
    - Docker users generally do not interact directly with the **boot filesystem**.
    - This layer is abstracted away, as Docker's primary focus is on the **root filesystem** (e.g., application and runtime dependencies).

- **Container Boot Process:**
    - During a container's startup, the **bootfs** layer is loaded into memory to initialize the environment.
    - Once the initialization completes, the **boot filesystem** is unmounted to:
        - Free up memory.
        - Transition the container into its operational state with the **root filesystem** (`rootfs`).

- **RAM and Initrd Disk Image:**    
    - The boot filesystem operates similarly to the `initrd` (initial RAM disk) in Linux, which is a temporary root filesystem loaded into memory during system boot.
    - After booting, the memory used by the **initrd** is released.

Docker next layers a root filesystem, rootfs, on top of the boot filesystem. This rootfs can be one or more operating systems (e.g., a Debian or Ubuntu filesystem).

###### **Traditional Linux Boot:**

- During the boot process in a traditional Linux system, the **root filesystem** is initially mounted in **read-only** mode to prevent accidental changes and to ensure system integrity.
- After booting, the system runs an **integrity check** (often using `fsck`) and, if everything is fine, the filesystem is switched to **read-write** mode, allowing the system to operate normally.
- The root filesystem contains all the critical system files, libraries, and directories that the OS uses.

###### **Docker Filesystem:**

- In Docker, however, the **root filesystem** remains **read-only** for the container's lifetime. This prevents accidental modification and ensures the integrity of the base filesystem.
- Instead of directly modifying the root filesystem, Docker uses a **union filesystem** to overlay additional layers on top of it.
	    A **union filesystem** allows multiple filesystems to be mounted simultaneously but appear as one single filesystem. This is key to Docker’s **image layering system**.
	- The union filesystem overlays layers on top of one another, creating a unified view.
	- Each layer is **read-only** except the **topmost layer**, which is **read-write** and allows containers to write data during execution.

###### **Layers and Images in Docker:**
Docker organizes images in a **layered stack**. Here's how it works:

- **Base Image**:
    - At the very bottom of the image stack is the **base image** (e.g., `ubuntu:20.04`), which contains a minimal operating system with just enough functionality to run applications.
    - This layer is read-only.
- **Parent Images**:
    - On top of the base image, Docker can add layers (called **parent images**), each one adding specific software, libraries, or configurations.
    - Each parent image adds additional read-only layers to the stack. These layers inherit from the one beneath it.
- **Final Image**:
    - The final image is composed of several layers stacked together. For example, an image for a Python application might have layers for:
        - The base OS image (e.g., `ubuntu`).
        - A layer with Python installed.
        - Another layer with specific application dependencies.
    - Each of these layers is immutable (read-only) and can be shared across containers.
- **Top Layer (Writable)**:
    - When you launch a **container** from an image, Docker adds a **read-write layer** on top of the read-only layers. This is where any changes (e.g., files created or modified) happen during the container's runtime.
    - The writable layer allows processes inside the container to modify files, but those changes will not affect the underlying read-only layers.

#### Listing Docker images

```
$ sudo docker images 
REPOSITORY TAG IMAGE ID CREATED VIRTUAL SIZE 
ubuntu latest c4ff7513909d 6 days ago 225.4 MB
```

find all your containers in the /var/lib/docker/containers directory.

That image was downloaded from a repository. Images live inside repositories, and repositories live on registries. The default registry is the public registry managed by Docker, Inc., Docker Hub.

Inside Docker Hub, images are stored in repositories. You can think of an image repository as being much like a Git repository. It contains images, layers, and metadata about those images.

There are two types of repositories: user repositories, which contain images contributed by Docker users, and top-level repositories, which are controlled by the people behind Docker.

A user repository takes the form of a username and a repository name; 
for example, jamtur01/puppet.
• Username: jamtur01 
• Repository name: puppet

Alternatively, a top-level repository only has a repository name like ubuntu. The top-level repositories are managed by Docker Inc and by selected vendors who provide curated base images that you can build upon (e.g., the Fedora team provides a fedora image). 
The top-level repositories also represent a commitment from vendors and Docker Inc that the images contained in them are well constructed, secure, and up to date.

#### Pulling images
When we run a container from images with the docker run command, if the image isn’t present locally already then Docker will download it from the Docker Hub. 

By default, if you don’t specify a specific tag, Docker will download the latest tag, for example:

```
$ sudo docker run -t -i --name next_container ubuntu /bin/bash 
root@23a42cee91c3:/#
```

#### Searching for images
Search all of the publicly available images on Docker Hub using the docker search command

```
sudo docker search puppet 
NAME                   DESCRIPTION        STARS      OFFICIAL       AUTOMATED 
macadmins/puppetmaster Simple puppetmaster 21 [ OK] devopsil/puppet Dockerfile for a 18 [ OK] . . .
```

• Repository names 
• Image descriptions 
• Stars - these measure the popularity of an image 
• Official - an image managed by the upstream developer (e.g., the fedora image managed by the Fedora team) 
• Automated - an image built by the Docker Hub’s Automated Build process

#### Building our own images
There are two ways to create a Docker image: 
- Via the docker commit command 
- Via the docker build command with a Dockerfile

###### Using Docker commit to create images
The first method of creating images uses the docker commit command. 
You can think about this method as much like making a commit in a version control system.
We create a container, make changes to that container as you would change code, and then commit those changes to a new image.

```
$ sudo docker run -i -t ubuntu /bin/bash 
root@4aab3ce3cb76:/#
```

```
root@4aab3ce3cb76:/# apt-get -yqq update 
. . . 
root@4aab3ce3cb76:/# apt-get -y install apache2 
. . .
```

We’ve launched our container and then installed Apache within it. We’re going to use this container as a web server, so we’ll want to save it in its current state. 
That will save us from having to rebuild it with Apache every time we create a new container.

```
$ sudo docker commit 4aab3ce3cb76 jiggaman/apache2 
8ce0ea7a1528
```

here,
jiggaman/apache2 : target repository and image name

docker commit command only commits the differences between the image the container was created from and the current state of the container. This means updates are lightweight.

We can also provide some more data about our changes when committing our image, including tags. For example:
```
$ sudo docker commit -m "A new custom image" -a "Jigga Man" \ 4aab3ce3cb76 jiggaman/apache2:webserver f99ebb6fed1f559258840505a0f5d5b6173177623946815366f3e3acff01adef
```

Here, we’ve specified some more information while committing our new image. We’ve added the -m option which allows us to provide a commit message explaining our new image. We’ve also specified the -a option to list the author of the image.

```
$ sudo docker inspect jiggaman/apache2:webserver 
[{ "Architecture": "amd64", 
"Author": "Jigga Man", 
"Comment": "A new custom image", 
. . . }]
```

###### Building images with a Dockerfile
The Dockerfile uses a basic DSL (Domain Specific Language) with instructions for building Docker images.

```
$ mkdir static_web 
$ cd static_web 
$ touch Dockerfile
```

This directory is our build environment, which is what Docker calls a context or build context. Docker will upload the build context, as well as any files and directories contained in it, to our Docker daemon when the build is run. This provides the Docker daemon with direct access to any code, files or other data you might want to include in the image.

Creating a dockerfile to create a Docker image that will act as a Web server
```
# Version: 0.0.1 
FROM ubuntu:16.04 
MAINTAINER jiggaman "jigggaman@jigga.com" 
RUN apt-get update; apt-get install -y nginx 
RUN echo 'Hi, I am in your container' \ >/var/www/html/index.html 
EXPOSE 80
```

Dockerfile are processed from the top down, so order them accordingly.

Each instruction adds a new layer to the image and then commits the image.

Docker executing instructions roughly follow a workflow:
• Docker runs a container from the image.
• An instruction executes and makes a change to the container. 
• Docker runs the equivalent of docker commit to commit a new layer. 
• Docker then runs a new container from this new image. 
• The next instruction in the file is executed, and the process repeats until all instructions have been executed.

The first instruction in a Dockerfile must be FROM.
The `FROM` instruction specifies an existing image that the following instructions will operate on; this image is called the base image.

`MAINTAINER` instruction, tells Docker who the author of the image is and what their email address is. This is useful for specifying an owner and contact for an image.

The `RUN` instruction executes commands on the current image.

Each of these instructions will create a new layer and, if successful, will commit that layer and then execute the next instruction.

By default, the `RUN` instruction executes inside a shell using the command wrapper `/bin/sh -c`. If you are running the instruction on a platform without a shell or you wish to execute without a shell (for example, to avoid shell string munging), specify the instruction in exec format:

```
RUN [ "apt-get", " install", "-y", "nginx" ]
```

EXPOSE instruction, tells Docker that the application in this container will use this specific port on the container.

 Multiple EXPOSE instructions can be specified to mark multiple ports to be exposed.

###### Building the image from our Dockerfile
```
$ cd static_web 

$ sudo docker build -t="jiggaman/static_web" . 
Sending build context to Docker daemon 2.56 kB Sending build context to Docker daemon Step 0 : FROM ubuntu:16.04 ---> ba5877dc9bec 

Step 1 : MAINTAINER James Turnbull "james@example.com" ---> Running in b8ffa06f9274 ---> 4c66c9dcee35 Removing intermediate container b8ffa06f9274 

Step 2 : RUN apt-get update ---> Running in f331636c84f7 ---> 9d938b9e0090 Removing intermediate container f331636c84f7 

Step 3 : RUN apt-get install -y nginx ---> Running in 4b989d4730dd ---> 93fb180f3bc9 Removing intermediate container 4b989d4730dd 

Step 4 : RUN echo 'Hi, I am in your container' >/var/www/html/index.html ---> Running in b51bacc46eb9 ---> b584f4ac1def Removing intermediate container b51bacc46eb9 

Step 5 : EXPOSE 80 ---> Running in 7ff423bd1f4d ---> 22d47c8cb6e5 Successfully built 22d47c8cb6e5
```

Use -t option to mark resulting image with a repository and a name

The trailing . tells Docker to look in the local directory to find the Dockerfile.

Git repository can also be specified as a source for the Dockerfile
```
$ sudo docker build -t="jiggaman/static_web:v2" \ github.com/turnbullpress/docker-static_web
```

###### Dockerfile instructions
But there are also a variety of other instructions we can put in our Dockerfile. These include CMD, ENTRYPOINT, ADD, COPY, VOLUME, WORKDIR, USER, ONBUILD, LABEL, STOPSIGNAL, ARG, SHELL, HEALTHCHECK and ENV..

##### **1.CMD
Difference b/w RUN and CMD

**`RUN`**
- **Purpose**: Executes commands during the **image build** phase to install software, configure the system, or modify the filesystem.
- **Execution Context**: The commands specified in `RUN` are executed **once** during the build process, and their results are saved in the image as a new layer.

**`CMD`**

- **Purpose**: Specifies the default command to be executed when a container is started from the image.
- **Execution Context**: The command specified in `CMD` is executed **each time a container runs**, and it is not stored as part of the image's filesystem.

```
$ sudo docker run -i -t jiggaman/static_web:v1 /bin/true
```

This would be articulated in the Dockerfile as:
```
CMD ["/bin/true"]
```


If we specify a CMD in our Dockerfile and one on the docker run command line, then the command line will override the Dockerfile’s CMD instruction.
example:
```
CMD [ "/bin/bash" ]
```

```
$ sudo docker run -t -i jiggaman/static_web:v1
root@e643e6218589:/#
```

We didn’t specify the command to be executed at the end of the docker run. Instead, Docker used the command specified by the CMD instruction

If, however, We did specify a command
```
$ sudo docker run -i -t jiggaman/static_web:v1 /bin/ps 
PID TTY TIME CMD 1 ? 00:00:00 ps $
```

Here we have specified the /bin/ps command to list running processes. Instead of launching a shell, the container merely returned the list of running processes and stopped, overriding the command specified in the CMD instruction

We can only specify one CMD instruction in a Dockerfile. If more than one is specified, then the last CMD instruction will be used. If you need to run multiple processes or commands as part of starting a container you should use a service management tool like Supervisor.

##### **2.ENTRYPOINT**
Closely related to the CMD instruction, and often confused with it, is the ENTRYPOINT instruction.

WHY ENTRYPOINT IS NEEDED?
We can override the CMD instruction on the docker run command line. Sometimes this isn’t great when we want a container to behave in a certain way.

The ENTRYPOINT instruction provides a command that isn’t as easily overridden. Instead, any arguments we specify on the docker run command line will be passed as arguments to the command specified in the ENTRYPOINT.

```
ENTRYPOINT ["/usr/sbin/nginx", "-g", "daemon off;"]
```

++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
==As with the CMD instruction above, you can see that we’ve specified the ENTRYPOINT command in an array to avoid any issues with the command being prepended with /bin/sh -c.==

If `ENTRYPOINT` is specified in a string format, Docker implicitly wraps the command with `/bin/sh -c`. This can cause unintended behavior.
###### Dockerfile
```
FROM ubuntu:20.04 ENTRYPOINT echo "Hello, World!"
```
###### Behavior
When this container is run:
```
docker build -t example-string-entrypoint . 
docker run example-string-entrypoint
```

Docker interprets the `ENTRYPOINT` like this:
`/bin/sh -c "echo Hello, World!"`

Specifying `ENTRYPOINT` as an array ensures the command runs directly, bypassing the `/bin/sh -c` wrapper.
###### Dockerfile
```
FROM ubuntu:20.04 ENTRYPOINT ["echo", "Hello, World!"]
```
###### Behavior
When this container is run:
```
docker build -t example-array-entrypoint . docker run example-array-entrypoint
```

Docker executes the command **directly** as:
`echo Hello, World!`
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

```
ENTRYPOINT ["/usr/sbin/nginx"]
```

```
$ sudo docker build -t="jiggaman/static_web" .
```

```
$ sudo docker run -t -i jiggaman/static_web -g "daemon off;"
```

We’ve rebuilt our image and then launched an interactive container. We specified the argument -g "daemon off;".

This argument will be passed to the command specified in the ENTRYPOINT instruction, which will thus become /usr/sbin/nginx -g "daemon off;".

This command would then launch the Nginx daemon in the foreground and leave the container running as a web server.


```
ENTRYPOINT ["/usr/sbin/nginx"] 
CMD ["-h"]
```
Now when we launch a container, any option we specify will be passed to the Nginx daemon; for example, we could specify -g "daemon off"; as we did above to run the daemon in the foreground. If we don’t specify anything to pass to the container, then the -h is passed by the CMD instruction and returns the Nginx help text: /usr/sbin/nginx -h.


##### **3.WORKDIR**
The WORKDIR instruction provides a way to set the working directory for the container and the ENTRYPOINT and/or CMD to be executed when a container is launched from the image.

We can use it to set the working directory for a series of instructions or for the final container.
```
WORKDIR /opt/webapp/db 
RUN bundle install 
WORKDIR /opt/webapp 
ENTRYPOINT [ "rackup" ]
```

we’ve changed into the /opt/webapp/db directory to run bundle install and then changed into the /opt/webapp directory prior to specifying our ENTRYPOINT instruction of rackup.

**If `/opt/webapp/db` Doesn't Exist**:

- `WORKDIR /opt/webapp/db` will **create** the directory `/opt/webapp/db` (and its parent `/opt/webapp` if needed).
- After `WORKDIR ..`, Docker moves up to `/opt/webapp`.

We can override the working directory at runtime with the -w flag, 
for example:
```
$ sudo docker run -ti -w /var/log ubuntu pwd /var/log
```
This will set the container’s working directory to /var/log.

##### **4.ENV**
The ENV instruction is used to set environment variables during the image build process

```
ENV RVM_PATH /home/rvm/
```

This new environment variable will be used for any subsequent RUN instructions, as if we had specified an environment variable prefix to a command like so:

```
RUN gem install unicorn
```

would be executed as:

```
RVM_PATH=/home/rvm/ gem install unicorn
```

We can specify single environment variables in an ENV instruction or since Docker
```
ENV RVM_PATH=/home/rvm RVM_ARCHFLAGS="-arch i386"
```

We can also use these environment variables in other instructions.
```
ENV TARGET_DIR /opt/app 
WORKDIR $TARGET_DIR
```

You can also pass environment variables on the docker run command line using the -e flag. These variables will only apply at runtime.
```
$ sudo docker run -ti -e "WEB_PORT=8080" ubuntu env 
HOME=/ 
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin 
HOSTNAME=792b171c5e9f 
TERM=xterm 
WEB_PORT=8080
```

##### **5.USER**
The USER instruction specifies a user that the image should be run as; for example:
```
USER nginx
```

This will cause containers created from the image to be run by the nginx user. We can specify a username or a UID and group or GID. Or even a combination thereof, for example:
```
USER user 
USER user:group 
USER uid 
USER uid:gid 
USER user:gid 
USER uid:group
```

We can also override this at runtime by specifying the -u flag with the docker run command.

The default user if you don’t specify the USER instruction is root

##### **6.VOLUME**
The VOLUME instruction adds volumes to any container created from the image.

A volume is a specially designated directory within one or more containers that bypasses the Union File System to provide several useful features for persistent or shared data:

- Volumes can be shared and reused between containers. 
- A container doesn’t have to be running to share its volumes.
- Changes to a volume are made directly. 
- Changes to a volume will not be included when you update an image. 
- Volumes persist even if no containers use them.

This allows us to add data (like source code), a database, or other content into an image without committing it to the image and allows us to share that data between containers.

```
VOLUME ["/opt/project"]
```

This would attempt to create a mount point /opt/project to any container created from the image.

##### **7.ADD**
The ADD instruction adds files and directories from our build environment into our image; for example, when installing an application.

The ADD instruction specifies a source and a destination for the files, like so:

```
ADD software.lic /opt/application/software.lic
```

This ADD instruction will copy the file software.lic from the build directory to / opt/application/software.lic in the image.

We cannot ADD files from outside the build directory or context.

the ADD instruction has some special magic for taking care of local tar archives. If a tar archive (valid archive types include gzip, bzip2, xz) is specified as the source file, then Docker will automatically unpack it
```
ADD latest.tar.gz /var/www/wordpress/
```

This will unpack the latest.tar.gz archive into the /var/www/wordpress/ directory. The archive is unpacked with the same behavior as running tar with the -x option

Finally, if the destination doesn’t exist, Docker will create the full path for us, including any directories. New files and directories will be created with a mode of 0755 and a UID and GID of 0.

##### **8.COPY**
The COPY instruction is closely related to the ADD instruction. 

The key difference is that the COPY instruction is purely focused on copying local files from the build context and does not have any extraction or decompression capabilities.

```
COPY conf.d/ /etc/apache2/
```

This will copy files from the conf.d directory to the /etc/apache2/ directory.

The source of the files must be the path to a file or directory relative to the build context, the local source directory in which your Dockerfile resides. You cannot copy anything that is outside of this directory, because the build context is uploaded to the Docker daemon, and the copy takes place there.

Anything outside of the build context is not available.

Any files and directories created by the copy will have a UID and GID of 0.

If the destination doesn’t exist, it is created along with all missing directories in its path, much like how the mkdir -p command works.


##### **9.LABEL**
The LABEL instruction adds metadata to a Docker image. The metadata is in the form of key/value pairs.

```
LABEL version="1.0" 
LABEL location="New York" type="Data Center" role="Web Server"
```

We can specify  multiple items separated with white space.

```
$ sudo docker inspect jamtur01/apache2 
. . . 
	"Labels": { 
	"version": "1.0", 
	"location": "New York", 
	"type": "Data Center", 
	"role": "Web Server" 
	},
```

##### **10.STOPSIGNAL**
The STOPSIGNAL instruction sets the system call signal that will be sent to the container when you tell it to stop.

This signal can be a valid number from the kernel syscall table, for instance 9, or a signal name in the format SIGNAME, for instance SIGKILL.

##### **11.ARG**
The ARG instruction defines variables that can be passed at build-time via the docker build command. 

This is done using the --build-arg flag.

We can only specify build-time arguments that have been defined in the Dockerfile.

```
ARG build 
ARG webapp_user=user
```

The second ARG instruction sets a default, if no value is specified for the argument at build-time then the default is used.

```
$ docker build --build-arg build=1234 -t jamtur01/webapp .
```

As the jamtur01/webapp image is built the build variable will be set to 1234 and the webapp_user variable will inherit the default value of user.

Docker has a set of predefined ARG variables that you can use at build-time without a corresponding ARG instruction in the Dockerfile.
```
HTTP_PROXY 
http_proxy 
HTTPS_PROXY 
https_proxy 
FTP_PROXY 
ftp_proxy 
NO_PROXY 
no_proxy
```

##### **11.SHELL**
The SHELL instruction allows the default shell used for the shell form of commands to be overridden.

The default shell 
on Linux is [/bin/sh", "-c"] 
on Windows is ["cmd", "/S", "/C"].

##### **12.HEALTHCHECK**
The HEALTHCHECK instruction tells Docker how to test a container to check that it is still working correctly.

This allows you to check things like a web site being served or an API endpoint responding with the correct data, allowing you to identify issues that appear, even if an underlying process still appears to be running normally.

```
HEALTHCHECK --interval=10s --timeout=1m --retries=5 CMD curl http ://localhost || exit 1
```

The HEALTHCHECK instruction contains options and then the command you wish to run itself, separated by a CMD keyword. 
We’ve first specified three default options: 
• --interval - defaults to 30 seconds. This is the period between health checks. In this case the first health check will run 10 seconds after container launch and subsequently every 10 seconds. 
• --timeout - defaults to 30 seconds. If the health check takes longer the timeout then it is deemed to have failed. 
• --retries - defaults to 3. The number of failed checks before the container is marked as unhealthy.

We can see the state of the health check if HEALTHCHECK instruction is mapped, using the docker inspect command.
```
$ sudo docker inspect --format '{{.State.Health.Status}}' static_web 
healthy
```

##### **13.ONBUILD**
The ONBUILD instruction adds triggers to images. A trigger is executed when the image is used as the basis of another image

The trigger inserts a new instruction in the build process, as if it were specified right after the FROM instruction.

```
ONBUILD ADD . /app/src 
ONBUILD RUN cd /app/src; make
```

We now have an image with an ONBUILD instruction that uses the ADD instruction to add the contents of the directory we’re building from to the /var/www/ directory in our image.

```
$ sudo docker inspect 508efa4e4bf8 
... 
"OnBuild": [ 
"ADD . /app/src", 
"RUN cd /app/src/; make" 
] 
...
```


Creating a new Dockerfile for an apache2 image

```
FROM ubuntu:16.04 
MAINTAINER Jigga Man "jigga@who.com" 
RUN apt-get update; apt-get install -y apache2 
ENV APACHE_RUN_USER www-data 
ENV APACHE_RUN_GROUP www-data 
ENV APACHE_LOG_DIR /var/log/apache2 
ONBUILD ADD . /var/www/ 
EXPOSE 80 
ENTRYPOINT ["/usr/sbin/apache2"] 
CMD ["-D", "FOREGROUND"]
```

```
$ sudo docker build -t="jiggaman/apache2" . 
... 
Step 7 : ONBUILD ADD . /var/www/ 
---> Running in 0e117f6ea4ba 
---> a79983575b86 
Successfully built a79983575b86
```
We now have an image with an ONBUILD instruction that uses the ADD instruction to add the contents of the directory we’re building from to the /var/www/ directory in our image.

now building a new image called webapp from the following Dockerfile:
```
FROM jigga/apache2 
MAINTAINER Jigga Man "jigga@who.com" 
ENV APPLICATION_NAME webapp 
ENV ENVIRONMENT development
```

On building a new image
```
$ sudo docker build -t="jamtur01/webapp" . 
... 
Step 0 : FROM jamtur01/apache2 # Executing 1 build triggers Step onbuild-0 : ADD . /var/www/ ---> 1a018213a59d ---> 1a018213a59d Step 1 : MAINTAINER James Turnbull "james@example.com" ... Successfully built 04829a360d86
```






---
## VOLUME

The -v option works by specifying a directory or mount on the local host separated from the directory on the container with a :. If the container directory doesn’t exist Docker will create it. 

```
FROM ubuntu:latest 
MAINTAINER Jigga Man "jigga@who.com"  
RUN apt-get -yqq update; apt-get -yqq install nginx 
RUN mkdir -p /var/www/html/website 
ADD global.conf /etc/nginx/conf.d/ 
ADD nginx.conf /etc/nginx/nginx.conf 
EXPOSE 80
```

```
$ sudo docker build -t jiggaman/nginx
```

```
$ sudo docker run -d -p 80 --name website -v $PWD/website:/var/www/html/website
jiggaman/nginx nginx
```

We can also specify the read/write status of the container directory by adding either rw or ro after that directory.

