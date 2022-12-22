# Singularity tutorial

# Course introduction

You've heard about containers before, but don't really know much about them. What exactly are they?

Singularity containers are a new type of container designed specifically for HPC applications. The HPC community has long struggled with managing resources across multiple nodes. In addition, running large numbers of jobs simultaneously requires careful coordination between schedulers and job managers. This often leads to complex systems that are difficult to manage and debug.

In this course you will learn what Singularity is, how to install it, how to use it and how to take advantage of its features.

# Quickstart

This is how you run a container:

```bash
singularity run library://josue-sylabs/demo/wttr:latest
```

# Course features and tools

In this course it is expected to have an updated a major linux distribution, it is true that linux kernel offers namespaces since 2.4.19, features has been andded and bugs have been fixed until version 3.8 with user namespaces.

Having said this the recommended is:

* DEB based:
  * Debian 10 or later.
  * Ubuntu 20.04 or later.
* RPM based:
  * Redhat 8 or later.
  * Fedora 32 or later.
* OpenSUSE Leap 12 or later.
* x86_64 or ARM64 is supported.
* git
* curl

Have some command line experience in bash.

# Introduction to Singularity CE

Container technology is an effective way to package applications, no pre-requisites, no specific OS other than linux, and offer other many advantages to distribute applications.

Singularity conception is focused to cover the high performance market, big data and supercomputing. Other container technologies allows containers to run with root privileges, which is an issue wether in the supercomputing or commercial world and there is a lot of interest in containers for compute.

You can read more about at the [Singularity CE documentation site](https://docs.sylabs.io/guides/3.10/user-guide/introduction.html), or if you wish to know about [use cases](https://docs.sylabs.io/guides/3.10/user-guide/introduction.html#use-cases).

**What is the difference with virtual machines?**

Containers are a technology that run applications inside isolated enviroments. They provide several benefits over traditional VMs, such as better security, resource isolation, and faster deployment times. Containers also allow developers to build and deploy software without worrying about underlying infrastructure or operating system details.

* One can install a prebuilt virtual machine with all its dependencies, and that is true, but containers are lighter and portable, a container size normally is between 10 Mb and a few Gb, while a virtual machine image size is bigger.
* Containers are very fast to modify and iterate on.
* Singularity offers a public repository of premade containers, and offers the posibility to publish your very own image for others developers and consumers.

Singularity is a tool for developers who want to build and test their code before deploying it to a supercomputer. In addition, they offer several advantages over traditional VMs, such as increased security, reduced resource consumption, and faster deployment times.

**What is the difference with Docker?**

There are many container runtimes out there, Docker started it all, but use case for singularity is different, if you wish to read more about this, please read the blog post: [Who is sylabs?](https://link-here.com).

# Namespaces

The linux kernel resources can be partitioned, this means for example, that one set of programs or processes sees one set of resources, and another process sees another different set of resources, in other words, grants isolation. This is a feature of the linux kernel.

Namespaces limit resources in a way so the process program or application is not aware of this limitation. When an program runs, the application is not aware that is within a container, and is not necessary to make changes to the source code.

Examples of resources are:

* Processes.
  This provides processes with an independent set of IDs from other namespaces.
* Network interfaces.
  Each namespace can have private IP addresses, routing tables, firewall, this mean your containers can communicate with the external world.
* Filesystem mounts.
  Determine which filesystems are isolated from other containers.
* UTS Namespace.
  Isolates kernel and version identifiers, so you could have multiple containers running that may be running different kernel versions.

# Installation

Follow the instructions on the official [SingularityCE installation guide](https://docs.sylabs.io/guides/latest/user-guide/quick_start.html#quick-start).

At this point, singularity is installed, no service or daemon is configured.

# Running a container

You've heard of containers, but have you ever tried running them? If you haven't, then now is the time to start learning how to run them. Containers are becoming increasingly important in today's IT environment. They allow you to easily deploy applications without having to worry about managing servers or installing software.

The easiest part is to run a container and let run its job. For this course, we are going to use an image from the current configured library, which is sylabs public library.

```bash
singularity run library://josue-sylabs/demo/wttr:latest
```

That was easy, no need to install anything, just go its way, right? Let's get a shell and get in the container.

```bash
> singularity shell library://josue-sylabs/demo/wttr:latest
INFO:    Using cached image
Singularity> _
```

Let's print the contents of the /etc/os-release file, this file contains lots of variables about the container operating system.

```bash
Singularity> cat /etc/os-release 
PRETTY_NAME="Debian GNU/Linux 10 (buster)"
NAME="Debian GNU/Linux"
VERSION_ID="10"
VERSION="10 (buster)"
VERSION_CODENAME=buster
ID=debian
HOME_URL="https://www.debian.org/"
SUPPORT_URL="https://www.debian.org/support"
BUG_REPORT_URL="https://bugs.debian.org/"
```

Now let's take a look at the lsmem command, this commands lists the available memory in a block perspective.

```bash
Singularity> lsmem
RANGE                                  SIZE  STATE REMOVABLE  BLOCK
0x0000000000000000-0x000000006fffffff  1.8G online       yes   0-13
0x0000000100000000-0x0000000487ffffff 14.1G online       yes 32-144

Memory block size:       128M
Total online memory:    15.9G
Total offline memory:      0B
```

Now lets exit the shell:

```bash
Singularity> exit
exit
```

Normally a container have an application ready, the creator of the image has defined a default command (`runscript`) and is called with the `run` subcommand. The below example shows the output of this demonstration.

One can `inspect` the content of a given image and see the `runscript` value, for that, first download or `pull` the image, then `inspect` like so:

```bash
singularity pull wttr.sif library://josue-sylabs/demo/wttr:latest
singularity inspect --runscript wttr.sif
```

So we can see that by default, the image launches a common linux command to pull weather information. Another interesting fact about singularity is that images can be executed directly without the explicit use of the singularity command and at the same time, demonstrate we can pull any image from docker hub and convert it to SIF format really easy.

```bash
$ singularity pull ubuntu.sif docker://ubuntu
$ ./ubuntu.sif 
Singularity> whoami
josue
Singularity> ps
    PID TTY          TIME CMD
 157430 pts/0    00:00:00 bash
 158145 pts/0    00:00:00 starter-suid
 158164 pts/0    00:00:00 bash
 158187 pts/0    00:00:00 ps
```

Note that right after executing `./ubuntu.sif` a bash command is created ready to accept commands.

# Running instances

Instances are another way to name a running container, difference is that an instance is in the background doing its job, could be a time consuming processing a batch job or even a service.

Let's create a couple of instances as demonstration, the `instance` keyword is new, but you are probably wondering what does that `docker://` means. In this context, it will automatically pull an image from the docker hub and convert the image to SIF. Then it comes the name of the instance.

```bash
singularity instance start docker://alpine instance1
singularity instance start docker://alpine instance2
singularity instance start docker://alpine instance3
singularity instance list
INSTANCE NAME    PID      IP    IMAGE
instance1        99098          /home/josue/.singularity/cache/oci-tmp/bc41182d7ef5ffc53a40b044e725193bc10142a1243f395ee852a8d9730fc2ad
instance2        99166          /home/josue/.singularity/cache/oci-tmp/bc41182d7ef5ffc53a40b044e725193bc10142a1243f395ee852a8d9730fc2ad
instance3        99215          /home/josue/.singularity/cache/oci-tmp/bc41182d7ef5ffc53a40b044e725193bc10142a1243f395ee852a8d9730fc2ad
```

To stop the instances:

```bash
singularity instance stop instance1
singularity instance stop instance2
singularity instance stop instance3
```

> **NOTE**
> Dockerhub have limited downloads, in order to remove the limit you can login with a user and password using the `singularity docker login` command first:
>
> ```bash
> > singularity remote login --username username docker://docker.io
> Password / Token: 
> INFO:    Token stored in /home/josue/.singularity/remote.yaml
> ```

# Building images

A more realistic example could be to setup a real instance, before that, lets demonstrate the use of definition files. Definition file is a set of instructions or commands, with the goal to prepare the image for final user. This includes setting up environment variables, installing software.

This is the Singularity Workflow (a development cycle), which is similar to the below steps:

1. Create a `sandbox` container.
2. Get into the container with the `--writable` option.
3. Do whatever it needs to get your software running including: commands, environment variables, and take note of them in a definition file.
4. Build the container from the definition file and do a test-run, if something goes wrong, go to step 1.

The definition file (`def` for short) is divided into 2 parts, a **header** and **sections**.

* **Header**: Describes the core operating system to build within the container.
* **Sections**: These are group of commands to describe particular actions in the final image like: setup, copy files, set environment variables, download files, testing and many more.

This is a very basic definition file, which is going to serve as a demo for this tutorial. These are the contents of the `demo.def` file:

```bash
Bootstrap: library
From: ubuntu:22.04
```

And the build command looks like this:

```bash
[josue@localhost ~]$ sudo singularity build demo.sif demo.def
INFO:    Starting build...
INFO:    Downloading library image
28.4MiB / 28.4MiB [===================================================================] 100 % 3.4 MiB/s 0s
INFO:    Verifying bootstrap image /root/.singularity/cache/library/sha256.7a63c...98b
INFO:    Creating SIF file...
INFO:    Build complete: demo.sif
```

The resulting container image is named `demo.sif`.

# The Singularity Workflow

The definition file is the outcome of the Singularity workflow, it is useful to test commands and make annotations, the place where commands are being tested is called the sandbox.

First, lets take a base operating system image to start and create a sandbox.

```bash
sudo singularity build --sandbox sandbox/ docker://ubuntu:22.04
```

The above command, creates a directory called “sandbox”, this is where all changes to the base image are gonna be saved and later in a SIF file.

```bash
sudo singularity shell --writable sandbox/
```

Now you can test your commands, install packages, re-configure the system and make notes in your definition file.

# Components of a definition file
A definition file is similar to this, some of the header and sections are optional

```bash
Bootstrap: library
From: ubuntu:22.04
%setup
    touch /file1
    touch ${SINGULARITY_ROOTFS}/file2

%files
    /file1
    /file1 /opt

%environment
    export LISTEN_PORT=12345
    export LC_ALL=C

%post
    apt-get update && apt-get install -y netcat
    NOW=`date`
    echo "export NOW=\"${NOW}\"" >> $SINGULARITY_ENVIRONMENT

%runscript
    echo "Container was created $NOW"
    echo "Arguments received: $*"
    exec echo "$@"

%startscript
    nc -lp $LISTEN_PORT

%test
    grep -q NAME=\"Ubuntu\" /etc/os-release
    if [ $? -eq 0 ]; then
        echo "Container base is Ubuntu as expected."
    else
        echo "Container base is not Ubuntu."
        exit 1
    fi

%labels
    Author d@sylabs.io
    Version v0.0.1

%help
    This is a demo container used to illustrate a def file that uses all
    supported sections.
```

The `Bootstrap` serves as a source, protocol or format of the base image, there are many to select like: the Singularity Container Library (library), Docker hub, oras, yum, localimage, debootstrap, oci, busybox, and others. 
For more information, please see official [definition file](https://docs.sylabs.io/guides/latest/user-guide/definition_files.html)  documentation.

The `From` keyword depends on the bootstrap value, in this case, when using the library, the From keyword becomes valid and is used to specify the name and tag of the base OS.

Commands inside the `%setup` keyword are executed during the build process on the host system, outside of the container, after the base OS has been installed.

The `%files` section allows to copy files into the container. This is useful when a program is compiled and you want to move around the recently built program to a second stage container.

The `%environment` section allows to define environment variables that will be set at runtime. These variables are not available during build time.

The `%post` section is where you can download files from the internet with tools like `git` and `wget`, install new software, write configuration files, create directories inside the container.

The `%runscript` section contains commands that are written to a file inside the container that is executed when the container image is run with `singularity run`.

The `%startscript` is similar to `%runscript` section, with the difference that the commands are executed when the command `singularity instance start` is issued.

The `%test` section contains commands that are issued at the end of the build process. It usually contains validation commands of your choice, it is also executed when using the `singularity test` command.

The `%labels` section is used to add metadata to the file /.singularity.d/labels.json, the labels are defined by key-value pairs and can be displayed while executing the `singularity inspect` command.

The text in the `%help` section is transferred to a metadata file inside the container. It can be displayed using the `singularity run-help` command.

# Multi-Stage Build
Multi-Stage build are useful where one stage can be used for compilation, and the resulting binary can be copied to a light and final environment, allowing a slimmer image without any of the development stack.

This can be done adjusting a definition file by adding the `Stage` keyword to the header section like so:

```bash
Bootstrap: library
From: josue-sylabs/golang:1.19.4
Stage: compiler

%post
  git clone ...
  go build -o program program.go

Bootstrap: library
From: alpine:3.9
Stage: final

%files from compiler
  /root/program /bin/program
```

The names of the stages are up to you, and each of the sections will be executed in the same order as described. Note that if you re-arrange the order of the above stages:

```bash

Bootstrap: library
From: alpine:3.9
Stage: final

%files from compiler
  /root/program /bin/program

Bootstrap: library
From: josue-sylabs/golang:1.19.4
Stage: compiler

%post
  git clone ...
  go build -o program program.go

```

The “final" stage cannot get the files from the “compiler” stage because they do not exist.

# Convert docker images to SIF
There are ocassions where an image is not available in the Singularity library, you can take one docker and convert it to the native Singularity Image Format.

`singularity pull docker://ubuntu:22.04`

This is the most easy, it will download all the docker format layers and compile a SIF image for you.
Other method is to use a definition file and take advantage of the bootstrap agent like so:

```bash
Bootstrap: docker
From: ubuntu:22.04

%post
   ...
%runscript
   ...
   
```

# Using Singularity Container Services
Singularity Container Service (SCS) is a free service offered to allow those who don’t have root access to their workstations and build images.

This service allows you to store remotely build a SIF image and share it with others if you preffer to do so in the Singularity library, also allows you to store your signing keys and let know the community you are the real author of the image.

Feel free to sign up at [SCS](https://cloud.sylabs.io/) and create an access token.

1. At the upper right corner, clic over your username and select “Access Tokens"
2. Type a name and clic on the green button “+ Create Access Token"
3. Copy your access token and save it in a safe place, you are going to need it when signing in with the `singularity remote login` command.

```bash
[josue@localhost ~]$ singularity remote login
Generate an access token at https://cloud.sylabs.io/auth/tokens, and paste it here.
Token entered will be hidden for security.
Access Token: 
INFO:    Access Token Verified!
INFO:    Token stored in /home/josue/.singularity/remote.yaml

```
# Remote building
The ability to build a container on an external resource is built in since Singularity 3.0, to demonstrate we can take the very same `demo.def` file but in this case, submit it to a remote build service. Note that the `sudo` is not needed anymore so it is dropped and the `-r` flag is added, also you need to have an account in SCS, an access token and login to the service.

```bash
singularity build -r demo.sif demo.def
INFO:    Starting build...
INFO:    Setting maximum build duration to 1h0m0s
INFO:    Remote "cloud.sylabs.io" added.
INFO:    Access Token Verified!
INFO:    Token stored in /root/.singularity/remote.yaml
INFO:    Remote "cloud.sylabs.io" now in use.
INFO:    Starting build...
INFO:    Downloading library image
INFO:    Downloaded 29816581 bytes in 4.475505819s
INFO:    Verifying bootstrap image /root/.singularity/cache/library/sha256.7a63c14842a5c9b9c0567c1530af87afbb82187444ea45fd7473726ca31a598b
INFO:    Creating SIF file...
INFO:    Build complete: /tmp/image-2540378504
INFO:    Performing post-build operations
INFO:    Calculating SIF image checksum
INFO:    Uploading image to library...
WARNING: Skipping container verification
INFO:    Uploading 29806592 bytes
INFO:    Image uploaded successfully.
INFO:    Build complete: demo.sif

```

The messages are streamed from the SCS to your terminal, and the resulting image is pulled to your local environment. Also if you have any `%files` defined in your def file, they are uploaded to SCS and discarded after some time.

In order to leave the resulting image in the Singularity library, you can specify the destination to be in the form of:

`singularity build -r library://<account>/directory/image:tag deffile` 

# Signing and verifying images

Signing images provides a method of trust for sharing images to Singularity library community. It ensures a bit-for-bit reproduction of the original container as the author intended it.

To sign your own containers, first need to generate a key.
```bash
singularity key newpair
Enter your name (e.g., John Doe) : Firstname Lastname
Enter your email address (e.g., john.doe@example.com) : first.last@sylabs.io
Enter optional comment (e.g., development keys) : a comment
Enter a passphrase :
Retype your passphrase :
Would you like to push it to the keystore? [Y,n] Y
Generating Entity and OpenPGP Key Pair... done
```

To see the signing keys:

```bash
singularity key list

Public key listing (/home/anonymous/.singularity/sypgp/pgp-public):

0) U: David Trudgian (demo) <first.last@sylabs.io> <-- User
   C: 2022-11-15 09:54:54 -0600 CST                <-- Creation timestamp
   F: E5F780B2C22F59DF748524B435C3844412EE233B     <-- Fingerprint
   L: 4096                                         <-- Key length
   --------
```

Now that a key is generated, a SIF image can be signed:

```
singularity sign container.sif

Signing image: container.sif
Enter key passphrase :
Signature crated and applied to container.sif
```

In order to verify the SIF image:

```bash
singularity verify container.sif

Verifying image: container.sif
[LOCAL]   Signing entity: Firstname Lastname (Demo keys) <first.last@sylabs.io>
[LOCAL]   Fingerprint: 65833F473098C6215E750B3BDFD69E5CEE85D448
Objects verified:
ID  |GROUP   |LINK    |TYPE
------------------------------------------------
1   |1       |NONE    |Def.FILE
2   |1       |NONE    |JSON.Generic
3   |1       |NONE    |FS
Container verified: container.sif
```

Now you can push an image to the Singularity library:

`singularity push container.sif library://<account>/directory/image:tag`

# Using singularity inspect
Singularity stores metadata in their SIF files. For example, if you want to see the original definition file you can use the inspect command:

```
singularity inspect --deffile 

bootstrap: library
from: ubuntu:22.04
```

Similarly you can also show several of the sections like:

```bash
-r to show the runscript for the image.
-s to show the startscript.
-t to show the test script.
-e to show the environment settings.
```

# Accessing host files
Normally some directories are available on the container, like your $HOME, /tmp, /proc, /sys and /dev, it is possible to access and even create and modify files and directories on the host system from the container.

You can specify other directories using the `--bind` option, for example, if we want to access a specific directory, lets say /opt/data, we can make it available to the container.

First lets make that directory and add some data:

```bash
sudo mkdir -p /opt/data
sudo chown $USER:$USER /opt/data
echo $(date) > /opt/data/date.txt
singularity exec demo.sif cat /opt/data/date.txt #This will fail.
singularity exec —bind /opt/data demo.sif cat /opt/data/date.txt #This will succeed.
```

In order to mount several directories, you can provide them as a comma separated list like:
`singularity exec --bind /dir1,/dir2,/dir3 demo.sif ls -l /`

You can even bind the directories and have them inside the containers with a different name:
`singularity exec --bind /dir1:/directory_one,/dir2:/directory_two demo.sif ls -l /`
# Advanced Topics
## Limiting resources
It is useful to limit resource that are consumed by a container, while in a supercomputer the scheduler normally limits these, is extremely useful when testing code in a workstation and avoid excesive use of CPU and RAM making it unstable and even unusable.

For CPU limiting you can use the `—cpu` flag, the minimum is `0.01` and the maximum is the number of cpu cores on the system:
`singularity run --cpus 2.1 demo.sif` 

For memory limits you can use the --memory flag, it sets the maximum ammount of RAM that a container can use in bytes, or if you prefer to set suffixes like `M` for megabytes or `G` for gigabytes. Note that if the container tries to use more memory than its limit, the system will kill it:
`singularity run --memory 1G demo.sif`

Another setting is `--memory-reservation`, which sets a soft limit that can be lower than the `--memory` limit.
`singularity run --memory 10g --memory-reservation 8g demo.sif`

I/O limit can be set but it requires `cfq` or `bfq` schedulers to be configured for your disks on the system.

The `--blkio-weight` flag, sets a relative weight for the container when performing reading or writing to and from disk. This value can be set between 10 and 1000, and will control the I/O Access a container receives when there is contention for I/O with other containers.
```bash
singularity run --blkio-weight 1000 demo.sif
singularity run --blkio-weight 100 demo.sif
```

The second container will have ten times less block I/O Priority.

You could also set relative weight for a specific device with the `--blkio-weight-device`:
```bash
singularity run --blkio-weight-device /dev/sda:1000 demo.sif
singularity run --blkio-weight-device /dev/sda:100 demo.sif
```

Second container will have ten time less block I/O on disk sda.

## Using GPU
It is very important that the host has a driver and library installation for NVidia CUDA or AMD ROCm.


## MPI applications

First, create a working directory, then download a CentOS 8 image, and convert it to SIF format.

```bash
> mkdir mpi && cd mpi
> singularity pull centos8.sif docker://centos:8
INFO:    Converting OCI blobs to SIF format
INFO:    Starting build...
Getting image source signatures
Copying blob a1d0c7532777 done  
Copying config 8c1402b22a done  
Writing manifest to image destination
Storing signatures
2022/09/21 16:04:47  info unpack layer: sha256:a1d0c75327776413fa0db9ed3adcdbadedc95a662eb1d360dad82bb913f8a1d1
2022/09/21 16:04:47  warn rootless{usr/bin/newgidmap} ignoring (usually) harmless EPERM on setxattr "security.capability"
2022/09/21 16:04:47  warn rootless{usr/bin/newuidmap} ignoring (usually) harmless EPERM on setxattr "security.capability"
2022/09/21 16:04:47  warn rootless{usr/bin/ping} ignoring (usually) harmless EPERM on setxattr "security.capability"
2022/09/21 16:04:49  warn rootless{usr/sbin/arping} ignoring (usually) harmless EPERM on setxattr "security.capability"
2022/09/21 16:04:49  warn rootless{usr/sbin/clockdiff} ignoring (usually) harmless EPERM on setxattr "security.capability"
INFO:    Creating SIF file...
```

Confirm the image is there.

```bash
> ls -lh centos8.sif
-rwxrwxr-x 1 josue josue 76M sep 21 16:05 centos8.sif
```

TODO: finish this.

[Source](https://support.hpe.com/hpesc/public/docDisplay?docId=a00117940en_us&docLocale=en_US&page=Build_An_MPI_Application_Using_Singularity.html)
