#### Debian, Ubuntu, etc.

Open a terminal and execute the following commands.

For Ubuntu 18.04 LTS / Debian (bionic) set the following variables:
```bash
export VERSION="3.10.2"
export OSNAME="bionic"
```

For Ubuntu 20.04 LTS or Debian (Focal):
```bash
export VERSION="3.10.2"
export OSNAME="focal"
```
For Ubuntu 22.04 LTS or Debian (Jammy):
```bash
export VERSION="3.10.2"
export OSNAME="jammy"
```
Once the variables are set:
```bash
wget https://github.com/sylabs/singularity/releases/download/v${VERSION}/singularity-ce_${VERSION}-${OSNAME}_amd64.deb
sudo apt install ./singularity-ce_${VERSION}-${OSNAME}_amd64.deb
```

Confirm installation by executing:
```bash
singularity version
```
The output is the actual version of singularity CE installed.

You can continue with the next topic: [running containers](intro.md#running-a-container).