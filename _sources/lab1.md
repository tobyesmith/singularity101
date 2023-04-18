# 📓 Lab 1: Convert docker images to SIF

First, install Singularity, assuming you have a RedHat OS like Alma Linux or Rocky Linux.

```bash
$ sudo dnf install -y epel-release
$ sudo dnf install -y singularity-ce
...
```

There are occasions where an image is not available in the Singularity library, you
can take one docker and convert it to the native Singularity Image Format.

```bash
$ singularity pull docker://ubuntu:22.04
...
```

This is the most easy, it will download all the docker format layers and compile
a SIF image for you.
