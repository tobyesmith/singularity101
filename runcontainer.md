# 📄 Running containers

You've heard of containers, but have you ever tried running them? If you haven't, then now is the time to start learning how to run them. Containers are becoming increasingly important in today's HPC environment. They allow you to easily deploy applications without having to worry about managing servers or installing software.

The easiest part is to run a container and let it run its job. For this course, we are going to use an image from the default library, which is Sylabs SCS public library.

```{figure} /images/quickstart-image1.png
---
name: quickstart-image1-1
---
Example of singularity run.
```

That was easy, no need to install anything other than the singularity container engine, just go its way, right? Let's get a shell and get in the container.

```bash
singularity shell library://josue-sylabs/demo/wttr:latest
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

Now lets exit the shell:

```bash
Singularity> exit
exit
```

Normally a container have an application ready, the creator of the image has defined a default command (`runscript`) and is called with the `run` subcommand. The below example shows the output of this demonstration.

One can `inspect` the content of a given image and see the `runscript` value, for that, first download or `pull` the image, then `inspect` like so:

```bash
$ singularity pull wttr.sif library://josue-sylabs/demo/wttr:latest
...
$ singularity inspect --runscript wttr.sif
```

So we can see that by default, the image launches a common linux command to pull weather information. Another interesting fact about singularity is that images can be executed directly without the explicit use of the singularity command and at the same time, demonstrate we can pull any image from docker hub and convert it to SIF format really easily.

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

````{note}
Dockerhub limits its downloads, in order to skip the limit you can login with a user and password using the `singularity docker login` command first:

```bash
singularity remote login --username username docker://docker.io`
Password / Token:
INFO:    Token stored in /home/josue/.singularity/remote.yaml
```

````

## Running instances

Instances are another way to name a running container, the difference is that an instance is in the background doing its job, typically  a service like web servers, which are often configured to work as detached or daemon mode where the container runs a service.

A SingularityCE container running a service in the background is called an instance, to distinguish it from the default mode which runs containers in the foreground.

We will explore more about instances later in a Lab.
