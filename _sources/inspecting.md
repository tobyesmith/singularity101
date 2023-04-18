# 📄 Inspecting SIF Files

Singularity stores metadata in their SIF files. For example, if you want to see
the original definition file you can use the inspect command:

```bash
$ singularity inspect --deffile 

bootstrap: library
from: ubuntu:22.04
```

Similarly you can also show several of the sections like:

* `-r` to show the `runscript` section for the image.
* `-s` to show the `startscript` section.
* `-t` to show the `test` script.
* `-e` to show the `environment` settings.

## Accessing host files

Normally some directories are available on the container, like your $HOME, /tmp,
/proc, /sys and /dev, it is possible to access and even create and modify files
and directories on the host system from the container, however the system will
always honor the current user permissions on the host..

You can specify other directories using the --bind option, for example, if we
want to access a specific directory or file, we can make it available to the
container.

Let's demonstrate this, we are going to expose /etc/os-release which is a file
that is defined by a linux operating system vendor, and is generally not changed
by anybody.

```bash
$ singularity exec library://ubuntu:22.04 cat /etc/os-release
INFO:    Downloading library image
PRETTY_NAME="Ubuntu 22.04.2 LTS"
NAME="Ubuntu"
VERSION_ID="22.04"
VERSION="22.04.2 LTS (Jammy Jellyfish)"
VERSION_CODENAME=jammy
ID=ubuntu
ID_LIKE=debian
HOME_URL="https://www.ubuntu.com/"
SUPPORT_URL="https://help.ubuntu.com/"
BUG_REPORT_URL="https://bugs.launchpad.net/ubuntu/"
PRIVACY_POLICY_URL="https://www.ubuntu.com/legal/terms-and-policies/privacy-policy"
UBUNTU_CODENAME=jammy
```

```bash
$ singularity exec —bind /etc/os-release library://ubuntu:22.04 cat /etc/os-release
INFO:    Using cached image
NAME="AlmaLinux"
VERSION="9.1 (Lime Lynx)"
ID="almalinux"
ID_LIKE="rhel centos fedora"
VERSION_ID="9.1"
PLATFORM_ID="platform:el9"
PRETTY_NAME="AlmaLinux 9.1 (Lime Lynx)"
ANSI_COLOR="0;34"
LOGO="fedora-logo-icon"
CPE_NAME="cpe:/o:almalinux:almalinux:9::baseos"
HOME_URL="https://almalinux.org/"
DOCUMENTATION_URL="https://wiki.almalinux.org/"
BUG_REPORT_URL="https://bugs.almalinux.org/"

ALMALINUX_MANTISBT_PROJECT="AlmaLinux-9"
ALMALINUX_MANTISBT_PROJECT_VERSION="9.1"
REDHAT_SUPPORT_PRODUCT="AlmaLinux"
REDHAT_SUPPORT_PRODUCT_VERSION="9.1"
```

Note how the first command shows Ubuntu information and the second command shows
the information about AlmaLinux, which is the file stored in the host.

````{note}
Do not execute the following commands as they may fail because directories are
fictional, lab 7 includes working examples.
````

In order to mount several directories, you can provide them as a comma separated
list like the following:

```bash
singularity exec --bind /dir1,/dir2,/dir3 library://ubuntu:22.04 ls -l /
```

You can even bind the directories and have them inside the containers with a
different name:

```bash
$ singularity exec --bind /dir1:/directory_one,/dir2:/directory_two library://ubuntu:22.04 ls -l /
```
