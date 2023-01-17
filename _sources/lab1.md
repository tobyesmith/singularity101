# 📓 Lab 1: Running Containers

First, install Singularity, assuming you have a RedHat OS.

```bash
sudo dnf install -y epel-release
sudo dnf install -y singularity-ce
```

Once installed you can run your first Singularity container:

```bash
singularity run library://josue-sylabs/demo/wttr:latest
```

Secondly lets build a very simple image, we will create a file and name it lab1.def,
type the contents of the below text.

```{note}
This tutorial asumes you know vi editor, this is not a mandatory requisite, feel free to
substitute the editor with your favorite one.
```

```bash
vi lab1.def
```

Then type the below contents:

```bash
bootstrap: docker
from: almalinux:9
```

Then type `:wq!` to exit the editor and save changes, then proceed to build the image, the compiled
image is going to be named lab1.sif:

```bash
sudo singularity build lab1.sif lab1.def
```

Now, lets suppose we want to do a more realistic image, lets copy some files, install some software:

Here we create a very simple text file, backup the lab1.def file into a file 
named lab1-v2.def and modify the contents.

```bash
echo "This is a test file." > testfile
cp lab1.def lab1-v2.def
vi lab1-v2.def
```

Add the below lines, feel free to copy and paste:

```bash
bootsrap: docker
from: almalinux:9

%files
    testfile

%post
    dnf install -y epel-release
    dnf install -y figlet

%runscript
    figlet testfile

```

Type `:wq!` to exit the editor and save changes, at this point, the SIF image of this new container
have a new file, and a new software taking AlmaLinux 9 as the base operating system.

Give it a try, build it and then run it:

```bash
singularity build lab1-v2.sif lab1-v2.def
singularity run lab1-v2.sif
```

Go ahead and experiment with different file contents, or even different sofware,
try installing `lolcat`:

```bash
cp lab1-v2.def lab1-v3.def
vi lab1-v3.def
```

Add the below lines, feel free to copy and paste:

```bash
bootsrap: docker
from: almalinux:9

%files
    testfile

%post
    dnf install -y epel-release
    dnf install -y figlet ruby wget unzip
    wget https://github.com/busyloop/lolcat/archive/master.zip
    unzip master.zip && cd lolcat-master/bin && gem install lolcat

%runscript
    figlet testfile | lolcat

```

Then type `:wq!` to exit and save the changes, then build and run:

```bash
singularity build lab1.sif lab1.def
singularity run lab1.sif
```

Nice and colored. This is an example, and being honest, the file `lab1.def` was written
many many times, until it worked, this approach of writting a def file, building and testing
is not always the best practice, you might try the *Singularity worfklow*, which is described on
the next section.
