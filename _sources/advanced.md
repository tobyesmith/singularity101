# Advanced Topics

## Limiting resources

It is useful to limit resource that are consumed by a container, while in a supercomputer the scheduler normally limits these, is extremely useful when testing code in a workstation and avoid excesive use of CPU and RAM making it unstable and even unusable.

### CPU Limits

For CPU limiting you can use the `—cpu` flag, the minimum is `0.01` and the maximum is the number of cpu cores on the system:

```bash
singularity run --cpus 2.1 demo.sif
```

### Memory Limits

For memory limits you can use the --memory flag, it sets the maximum ammount of RAM that a container can use in bytes, or if you prefer to set suffixes like `M` for megabytes or `G` for gigabytes. Note that if the container tries to use more memory than its limit, the system will kill it:

```bash
singularity run --memory 1G demo.sif
```

Another setting is `--memory-reservation`, which sets a soft limit that can be lower than the `--memory` limit.

```bash
singularity run --memory 10g --memory-reservation 8g demo.sif
```

### I/O Limits

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
