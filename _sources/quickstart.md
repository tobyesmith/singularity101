# Quickstart

Assuming you have a Redhat based operating system like AlmaLinux, CentOS, Oracle Linux, Rocky Linux, singularity CE is available in EPEL, to quickly install it:

```bash
sudo dnf install epel-release
sudo dnf update
sudo dnf install singularity-ce
```

If your operating system is Debian based, please follow the [SingularityCE installation guide](https://docs.sylabs.io/guides/3.10/admin-guide/installation.html#install-from-provided-rpm-deb-packages).

Once SingularityCE is installed, you can issue this command to run a container:

```bash
singularity run library://josue-sylabs/demo/wttr:latest
```

Here is an example output for the above command.

```{figure} /images/quickstart-image1.png
---
name: quickstart-image1
---
Example of singunlarity run.
```
