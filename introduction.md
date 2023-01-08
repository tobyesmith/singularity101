# 📄 Introduction to Singularity CE

Container technology is an effective way to package applications, no pre-requisites, no specific OS other than linux, and offer other many advantages to distribute applications.

Singularity conception is focused to cover the high performance market, big data and supercomputing. Other container technologies allows containers to run with root privileges, which is an issue wether in the supercomputing or commercial world and there is a lot of interest in containers for compute.

You can read more about at the [Singularity CE documentation site](https://docs.sylabs.io/guides/3.10/user-guide/introduction.html), or if you wish to know about [use cases](https://docs.sylabs.io/guides/3.10/user-guide/introduction.html#use-cases).

## Course features and tools

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

**What is the difference with virtual machines?**

Containers are a technology that run applications inside isolated enviroments. They provide several benefits over traditional VMs, such as better security, resource isolation, and faster deployment times. Containers also allow developers to build and deploy software without worrying about underlying infrastructure or operating system details.

* One can install a prebuilt virtual machine with all its dependencies, and that is true, but containers are lighter and portable, a container size normally is between 10 Mb and a few Gb, while a virtual machine image size is bigger.
* Containers are very fast to modify and iterate on.
* Singularity offers a public repository of premade containers, and offers the posibility to publish your very own image for others developers and consumers.

Singularity is a tool for developers who want to build and test their code before deploying it to a supercomputer. In addition, they offer several advantages over traditional VMs, such as increased security, reduced resource consumption, and faster deployment times.

```{figure} /images/introduction-image1.png
---
name: introduction-image1
---
Common architectures implemented in real world scenarios, bare metal, virtualized, containerized, and containerized on virtual machines.
```

**What is the difference with Docker?**

There are many container runtimes out there, Docker started it all, but this differed widely from how scientists and researchers wanted to use it in HPC with batch schedulers. So, the means to containerize applications was important, but the way in which Docker did this did not meet HPC needs for simplicity, security and repeatability. Singularity was created to address the gaps in the existing technology. While allowing easier access to the underlying hardware on a compute node.

[Source](https://sylabs.io/2022/09/who-is-sylabs/)

## Namespaces

The linux kernel resources can be partitioned, this means for example, that one set of programs or processes sees one set of resources, and another process sees another different set of resources, in other words, grants isolation. This is a feature of the linux kernel.

```{figure} /images/introduction-image2.png
---
name: introduction-image2
---
Namespace resource partition and isolation.
```

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
