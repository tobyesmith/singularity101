# 📓 Lab 1: Convert docker images to SIF

SIF is a file format tailored to container images. It allows for the storage of
different parts that makes up a container. For example, it may include OS
partitions images, user writable sections, DEF files used to create the
container, cryptographic signatures for data integrity and authenticity and the
design is prepared to integrate other sets of data for future use.

First, assuming you have a RedHat OS like Alma Linux or Rocky Linux, install
Singularity.

```bash
$ sudo dnf install -y epel-release
$ sudo dnf install -y singularity-ce
...
```

In cases where an image is not available in the Singularity library, you have
the option to convert a Docker image to the native Singularity Image Format.

```bash
$ singularity pull docker://ubuntu:22.04
...
```

This process is quite simple, as it downloads all the docker format layers and
compiles a SIF image for you.
