# 📄 Inspecting SIF Files

Singularity stores metadata in their SIF files. For example, if you want to see
the original definition file you can use the inspect command:

```bash
$ singularity inspect --deffile 

bootstrap: library
from: ubuntu:22.04
```

You can also use the following prompts to show the following sections:

* `-r` to show the `runscript` section for the image.
* `-s` to show the `startscript` section.
* `-t` to show the `test` script.
* `-e` to show the `environment` settings.

## Accessing host files

In a container, certain directories such as $HOME, /tmp, /proc, /sys and /dev
are usually accessible. While it is possible to access, create, and modify files
and directories on the host system from within the container,  the host’s user
permissions will always be honored.

To make other directories accessible, you can use the –bind option. For example,
if you want to access a specific directory or file, you can make it available
inside the container.

As an example, we’ll expose the /etc/os-release file, which is defined by a
Linux operating system vendor and generally remains constant.

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
$ singularity exec --bind /etc/os-release library://ubuntu:22.04 cat /etc/os-release
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

To mount multiple directories, you can provide them as a comma-separated list
like the following:

```bash
$ mkdir -p {run,var-log}
$ singularity exec --bind run:/run,var-log:/var/log library://ubuntu:22.04 ls -l /var/log
```

The previous command lists the files inside the host’s /var/log directory.
