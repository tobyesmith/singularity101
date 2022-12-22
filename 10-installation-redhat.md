#### Redhat, fedora, OpenSUSE, etc.

Open a terminal and execute the following commands.

If you have Redhat Enterprise Linux 7 or CentOS 7, set the below variables:
```bash
sudo yum -y install epel-release
sudo yum update
sudo yum install singularity-ce
```

For RHEL, Alma Linux, Rocky Linux 8 & 9

```bash
sudo dnf -y install epel-release
sudo dnf update
sudo dnf install singularity-ce
```

For Opensuse Leap 15 / SLES 15 you can install by using zypper command:

```bash
export VERSION="3.10.4"
export OSNAME="el8"
sudo zypper -n install -y squashfs runc criu
sudo rpm -Uvh --nodeps https://github.com/sylabs/singularity/releases/download/v${VERSION}/singularity-ce-${VERSION}-1.${OSNAME}.x86_64.rpm
```

Confirm installation by executing:
```bash
singularity --version
```
The output is the actual version of singularity CE installed.

You can continue with the next topic: [running containers](intro.md#running-a-container).