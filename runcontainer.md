# Running containers

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

## Running instances

Instances are another way to name a running container, difference is that an instance is in the background doing its job, could be a time consuming processing a batch job or even a service.

Let's create a couple of instances as demonstration, the `instance` keyword is new, but you are probably wondering what does that `docker://` means. In this context, it will automatically pull an image from the docker hub and convert the image to SIF. Then it comes the name of the instance.

```bash
singularity instance start docker://alpine instance1
singularity instance start docker://alpine instance2
singularity instance start docker://alpine instance3
singularity instance list
INSTANCE NAME    PID      IP    IMAGE
instance1        99098          /home/josue/.singularity/.../bc41...9730fc2ad
instance2        99166          /home/josue/.singularity/.../bc41...9730fc2ad
instance3        99215          /home/josue/.singularity/.../bc41...9730fc2ad
```

To stop the instances:

```bash
singularity instance stop instance1
singularity instance stop instance2
singularity instance stop instance3
```

````{note}
Dockerhub have limited downloads, in order to remove the limit you can login with a user and password using the `singularity docker login` command first:

```bash
singularity remote login --username username docker://docker.io`
Password / Token:
INFO:    Token stored in /home/josue/.singularity/remote.yaml
```

````
