# 📓 Lab 7: SIF file & Host Filesystem

A very useful technique to recover def files from a SIF file:

Let's get a SIF image from the SCS library:

```bash
$ singularity pull container_lab7.sif library://library/tutorial101/demo:latest
INFO:    Downloading library image
62.6MiB / 62.6MiB [=========================================] 100 % 8.3 MiB/s 0s
```
Then, execute it to see its output:

```bash
$ ./test.sif
 _   _      _ _          __                     
| | | | ___| | | ___    / _|_ __ ___  _ __ ___  
| |_| |/ _ \ | |/ _ \  | |_| '__/ _ \| '_ ` _ \ 
|  _  |  __/ | | (_) | |  _| | | (_) | | | | | |
|_| |_|\___|_|_|\___/  |_| |_|  \___/|_| |_| |_|
                                                
 ____        _       _         _ 
/ ___| _   _| | __ _| |__  ___| |
\___ \| | | | |/ _` | '_ \/ __| |
 ___) | |_| | | (_| | |_) \__ \_|
|____/ \__, |_|\__,_|_.__/|___(_)
       |___/                     
```

````{note}
Unlike docker containers, Singularity containers can be directly executed as if
they were commands. Use `./` before the sif filename and it will run.
````

Lets see the `runscript` section:

```bash
$ singularity inspect -r container_lab7.sif 
#!/bin/sh

    cat testfile | figlet | lolcat
```

When debugging why a certain script or piece of software is not running, it is
often common to forget to set environment variables, we can check for that by
running `-e` flag to the inspect subcommand to see which ones are currently set.

```bash
$ singularity inspect -e container_lab7.sif 
=== /.singularity.d/env/10-docker2singularity.sh ===
#!/bin/sh
export PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"

=== /.singularity.d/env/90-environment.sh ===
#!/bin/sh
# Custom environment shell code should follow
```

Now, lets extract the entire definition file:

```bash
$ singularity inspect --deffile container_lab7.sif

bootstrap: docker
from: almalinux:9

#%files
#    testfile

%post
    echo "hello" > testfile
    dnf install -y epel-release
    dnf install -y figlet ruby wget unzip
    wget https://github.com/busyloop/lolcat/archive/master.zip
    unzip master.zip && cd lolcat-master/bin && gem install lolcat

%runscript
    cat testfile | figlet | lolcat
```

If you are interested on taking a look at the SIF image sections lets take a
look at the `sif` subcommand, for example, lets list them:

```bash
$ singularity sif list container_lab7.sif
------------------------------------------------------------------------------
ID   |GROUP   |LINK    |SIF POSITION (start-end)  |TYPE
------------------------------------------------------------------------------
1    |1       |NONE    |32176-32522               |Def.FILE
2    |1       |NONE    |32522-33644               |JSON.Generic
3    |1       |NONE    |33644-33741               |JSON.Generic
4    |1       |NONE    |36864-127049728           |FS (Squashfs/*System/amd64)
5    |NONE    |1   (G) |127049728-127051525       |Signature (SHA-256)
```

We can also extract those by executing `sif dump` followed by the group ID, for
example, another way to extract the def file, which is Group ID #1:

````{note}
The below commands output are sent to the standard output, you may want to
redirect to a file instead with the use of the `>` character.
````

```bash
$ singularity sif dump 1 container_lab7.sif > container_lab7.def
$ cat container_lab7.def
bootstrap: docker
from: almalinux:9

#%files
#    testfile

%post
    echo "hello" > testfile
    dnf install -y epel-release
    dnf install -y figlet ruby wget unzip
    wget https://github.com/busyloop/lolcat/archive/master.zip
    unzip master.zip && cd lolcat-master/bin && gem install lolcat

%runscript
    cat testfile | figlet | lolcat
```

And now we have a DEF file back.

We can also remove headers and add our own, for example, lets remove the
signature and add our own:

```bash
$ singularity sif del 5 container_lab7.sif
$ singularity sif list container_lab7.sif
------------------------------------------------------------------------------
ID   |GROUP   |LINK    |SIF POSITION (start-end)  |TYPE
------------------------------------------------------------------------------
1    |1       |NONE    |32176-32522               |Def.FILE
2    |1       |NONE    |32522-33644               |JSON.Generic
3    |1       |NONE    |33644-33741               |JSON.Generic
4    |1       |NONE    |36864-127049728           |FS (Squashfs/*System/amd64)
```

You can see the signature is removed, now we can sign the sif our own signature:

```bash
$ singularity sign container_lab7.sif
INFO:    Signing image with PGP key material
0) U: Josue Balbuena (Master) <josue@sylabs.io>
   C: 2023-03-30 17:09:33 -0600 CST
   F: 670A2299CDA030D0
   L: 4096
   --------
Enter # of private key to use : 0
Enter key passphrase : 
INFO:    Signature created and applied to image 'container_lab7.sif'
```
