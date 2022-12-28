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
