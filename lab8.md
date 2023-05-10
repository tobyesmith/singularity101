# 📓 Lab 8: Putting it all together

In this lab, you’ll start integrating all the knowledge you’ve acquired about
SingularityCE to forge your own path. You’ll practice the majority of the
SingularityCE subcommands you have learned in this course, and your scripting
skills.

By the end of this lab, you'll:
* Understand the benefits of containerizing applications in HPC environments
* Be able to write simple definition files
* Learn how the building blocks of the SingularityCE workflow fit together
* Combine all of this knowledge to help you in your research or solving a
complex problem. 

We will start by solving a real container problem. Along the way, you will get
hands-on experience writing a definition file and you will quickly start to see
how containers can be portable and reproducible.

## Real Use Case

The use case explained here covers how to containerize an application and gives
you the opportunity to apply the concepts. 

We are going to containerize RStudio, a popular programming language development
platform.

## RStudio

The RStudio developers offer their software for various operating systems,
including Ubuntu 22.04. In this demonstration, we guide you through the RStudio
installation process on this operating system.

Let’s begin by creating a sandbox environment:

```bash
$ singularity build --sandbox rstudio library://ubuntu:22.04
```

Next, following the installation instructions, let’s install the R language
interpreter, and then RStudio and RServer packages.

To install the software in the container, we’ll need to access it as the root
user. Let’s proceed by entering the container as root.

```bash
$ singularity shell --fakeroot --writable rstudio
```

````{note}
Setting the DEBIAN_FRONTEND environment variable allows for unattended package
installation, eliminating the need for user interaction. This is crucial for pip
CI/CD pipelines. Similarly, the TZ (Timezone) variable should be set for the
tzdata package to function properly.
````

```bash
Singularity > export DEBIAN_FRONTEND=noninteractive
Singularity > export TZ=Etc/UTC
Singularity > apt update -qq
Singularity > apt install -y --no-install-recommends software-properties-common dirmngr wget locales
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following additional packages will be installed:
...
[Output removed for clarity]
Singularity > wget -qO- \
  https://cloud.r-project.org/bin/linux/ubuntu/marutter_pubkey.asc | \
  tee -a /etc/apt/trusted.gpg.d/cran_ubuntu_key.asc

-----BEGIN PGP PUBLIC KEY BLOCK-----

mQENBEy9tcUBCACnWQfqdrcz7tQL/iCeWDYSYPwXpPMUMLE721HfFH7d8ErunPKP
Iwq1v4CrNmMjcainofbu/BfuZESSK1hBAItOk/5VTkzCJlzkrHY9g5v+XlBMPDQC
[Output removed for clarity]

Singularity > add-apt-repository -y "deb https://cloud.r-project.org/bin/linux/ubuntu $(lsb_release -cs)-cran40/"
Repository: 'deb https://cloud.r-project.org/bin/linux/ubuntu jammy-cran40/'
Description:
Archive for codename: jammy-cran40/ components: 
[Output removed]

Singularity > apt install -y --no-install-recommends r-base
```

We are going to pause here and review how our definition file is looking. This
file must contain all the commands we have issued, including the operating
system version. Let's open a text editor and start building a def file.

```bash
Bootstrap: library
From: ubuntu:22.04

%post
  export DEBIAN_FRONTEND=noninteractive
  export TZ=Etc/UTC
  apt update -qq
  apt install -y --no-install-recommends \
      software-properties-common dirmngr wget
  wget -qO- \
  https://cloud.r-project.org/bin/linux/ubuntu/marutter_pubkey.asc | \
  tee -a /etc/apt/trusted.gpg.d/cran_ubuntu_key.asc
  add-apt-repository -y "deb https://cloud.r-project.org/bin/linux/ubuntu $(lsb_release -cs)-cran40/"
  apt install -y --no-install-recommends r-base
```

Next, we will download and install RStudio.

```bash
Singularity > wget -O /rstudio-2023.03.0-386-amd64.deb https://download1.rstudio.org/electron/jammy/amd64/rstudio-2023.03.0-386-amd64.deb
Singularity > apt -y install /rstudio-2023.03.0-386-amd64.deb
```

To install RStudio Server on Ubuntu,follow the instructions on the RStudio
Server website. Execute the following 3 commands:

```bash
Singularity > apt install -y gdebi-core
Singularity > wget -O /rstudio-server-2023.03.0-386-amd64.deb https://download2.rstudio.org/server/jammy/amd64/rstudio-server-2023.03.0-386-amd64.deb
Singularity > gdebi -n rstudio-server-2023.03.0-386-amd64.deb
```

It is a good practice in the container ecosystem to redirect all application
logs to the standard error output or stderr, which enables external agents to
collect these logs. Let’s configure this:

```bash
Singularity > echo "[*]\nlog-level=error\nlogger-type=stderr\n" > /etc/rstudio/logging.conf
Singularity > cat /etc/rstudio/logging.conf
[*]
log-level=error
logger-type=stderr
```

It is also a good idea to set the CRAN repository (which is a collection of
libraries) by adding the following code to the /etc/R/Rprofile.site file.

```bash
Singularity > echo 'local({
 r <- getOption("repos")
 r["CRAN"] = "https://cran.us.r-project.org"
 options(repos = r)
})' >> /etc/R/Rprofile.site
```

Next, it is important to configure the container to work with UTF-8 character
set. Let's configure this:

```bash
$ sed -i '/en_US.UTF-8/s/^# //g' /etc/locale.gen
$ locale-gen
$ update-locale
```

After completing the configuration, it’s important to verify everything works as
expected. You can do this by running a simple R script.

Now, exit the sandbox mode and re-launch the shell without the fakeroot flag:

```bash
Singularity > exit
$ singularity shell --writable rstudio
```

Use the  following command to start the RServer:

```bash
Singularity > /usr/lib/rstudio-server/bin/rserver --server-user=$(whoami) 
```

After executing the above command, open a web browser and navigate to
http://127.0.0.1:8787 if you are working on your local desktop system. If you’re
using a remote system, determine its IP address and use it in place of
`127.0.0.1`. 

In modern Linux systems, the IP addresses are typically shown when you issue the
“ip ad” command. Depending on the complexity of your system configuration, you
may see anywhere from a single IP address to a large number of them. 

After identifying the correct IP address, load the URL in a web browser and open
a new file by selecting File > New File > R Script.

```{figure} /images/lab8-image1.png
---
name: lab8-image1
---
Location of the new R Script.
```

Next paste the following lines:

```R
install.packages("ggplot2")
library(ggplot2)
ggplot(iris, aes(x=Sepal.Length, y=Sepal.Width, color=Species))+ 
  geom_point(size=6)
```

Highlight or select the first line, and click on the “Run” button as shown below.

```{figure} /images/lab8-image2.png
---
name: lab8-image2
---
Highlighting the first line.
```

During the installation of the ggplot2 package, you may encounter some errors.
These issues typically arise in the development cycle when additional packages
need to be installed as dependencies. Let's examine the errors, with the
problematic lines highlighted in red:

```bash
During startup - Warning messages:
* installing *source* package 'isoband' ...
** package 'isoband' successfully unpacked and MD5 sums checked
** using staged installation
** libs
sh: 1: make: not found
Error in system(paste(MAKE, p1(paste("-f", shQuote(makefiles))), "compilers"),  : 
  error in running command
* removing '/usr/local/lib/R/site-library/isoband'
Warning in install.packages :
  installation of package 'isoband' had non-zero exit status
During startup - Warning messages:
ERROR: dependency 'farver' is not available for package 'scales'
* removing '/usr/local/lib/R/site-library/scales'
Warning in install.packages :
...
```

The previous error message indicates that RServer needs the ‘make’ utility to
build and compile source code, but it isn't installed by default. To resolve
this issue, you can install the ‘make’ package at the operating system level.
However, a better solution would be to install the ‘r-base-dev’ package, which
includes a large set of packages for compiling and building R programs.

Close the browser and press CTRL+C in the terminal to terminate R Server. Next,
exit the shell and initiate a new one with root privileges  to install the
package.

```bash
Singularity> exit
exit
[josue@eagle]~/Singularity% singularity shell --fakeroot --writable rstudio
Singularity> apt install r-base-dev
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following additional packages will be installed:
  build-essential bzip2 bzip2-doc dpkg-dev fakeroot g++ g++-11 gcc gfortran
  gfortran-11 gnupg gnupg-l10n gnupg-utils gpg-agent gpg-wks-client
  gpg-wks-server gpgsm gpgv icu-devtools libalgorithm-diff-perl
[Output removed for clarity]

Singularity> exit
exit
[josue@eagle]~/Singularity% singularity shell --writable rstudio          
Singularity> /usr/lib/rstudio-server/bin/rserver --server-user=$(whoami) 
```

After installing the package, try running the installation again. Highlight the
first line and click the ‘Run’ button.he RServer will install the package, and
there should be no further issues. Highlight the remaining lines and click Run’
to see the output.

```{figure} /images/lab8-image3.png
---
name: lab8-image3
---
RStudio Server running in a SingularityCE Container.
```

Our final definition file requires additional configuration items. Since RServer
is expected to run as a server, it’s normal to define a %startscript section. We
can also add the missing packages required for building and compiling as well as
clean up our .deb packages. The new text is highlighted in blue, so our final
RServer.def file looks like this:

```bash
Bootstrap: library
From: ubuntu:22.04

%post
  export DEBIAN_FRONTEND=noninteractive
  export TZ=Etc/UTC
  apt update -qq
  apt install -y --no-install-recommends \
      software-properties-common dirmngr wget
  wget -qO- \
  https://cloud.r-project.org/bin/linux/ubuntu/marutter_pubkey.asc | \
  tee -a /etc/apt/trusted.gpg.d/cran_ubuntu_key.asc
  add-apt-repository -y "deb https://cloud.r-project.org/bin/linux/ubuntu $(lsb_release -cs)-cran40/"
  apt install -y --no-install-recommends r-base r-base-dev
  wget -O /rstudio-2023.03.0-386-amd64.deb https://download1.rstudio.org/electron/jammy/amd64/rstudio-2023.03.0-386-amd64.deb
  apt -y install /rstudio-2023.03.0-386-amd64.deb
  apt install -y gdebi-core
  wget -O /rstudio-server-2023.03.0-386-amd64.deb https://download2.rstudio.org/server/jammy/amd64/rstudio-server-2023.03.0-386-amd64.deb
  gdebi -n rstudio-server-2023.03.0-386-amd64.deb
  useradd -c "RStudio Account" -u 500 -d /home/rstudio -s /bin/bash rstudio
  echo "rstudio:rstudio" | /usr/sbin/chpasswd
  echo "[*]\nlog-level=error\nlogger-type=stderr\n" > /etc/rstudio/logging.conf
  echo 'local({
 r <- getOption("repos")
 r["CRAN"] = "https://cran.us.r-project.org"
 options(repos = r)
})' >> /etc/R/Rprofile.site
  sed -i '/en_US.UTF-8/s/^# //g' /etc/locale.gen
  locale-gen
  update-locale
  rm /rstudio*.deb

%startscript
  /usr/lib/rstudio-server/bin/rserver --server-user=$(whoami) 
```

At this point, we only have a definition file. The last step is to build the
actual SIF file like so:

```bash
$ singularity build --fakeroot rstudio.sif rstudio.def
```
You can start RStudio by running the SIF file as an instance.

```
$ singularity instance start rstudio.sif rs
```

Once you execute this, however, we encounter another problem: the system doesn’t
start and RStudio throws an error:

```bash
2023-04-28T22:50:57.409255Z [rserver] ERROR system error 30 (Read-only file system) [path: /var/lib/rstudio-server/rstudio-os.sqlite]; OCCURRED AT rstudio::core::Error rstudio::core::{anonymous}::changeFileModeImpl(const string&, mode_t) src/cpp/shared_core/FilePath.cpp:317; LOGGED FROM: int main(int, char* const*) src/cpp/server/ServerMain.cpp:753
```

As we can see, the system reports a read-only file system while attempting to
write data to a file located in `/var/lib/rstudio-server`. We can address this
issue by binding a directory to make it writable,  and simultaneously save the
state of RStudio, ensuring the settings are not lost between restarts.

Stop the instance, create working directories, and restart the instance with the
bind flags this time:

```bash
$ singularity instance stop rs
$ mkdir -p {run,var-lib-rstudio-server}
$ singularity instance start \
  --bind run:/run,var-lib-rstudio-server:/var/lib/rstudio-server \
  rstudio.sif rs
```

To confirm the system is running, use of the following command:

```
$ ss -ntlp | grep 8787
LISTEN 0      4096         0.0.0.0:8787       0.0.0.0:*    users:(("rserver",pid=2067530,fd=18))
```

The previous command lists all the TCP ports opened by a process, filtered by
the default 8787 port used by RStudio.

We now have a portable RStudio container image that can be shared with peers
using the SCS library. The final step is to sign the SIF file and upload it to
the SCS library:

```bash
$ singularity sign rstudio.sif 
INFO:    Signing image with PGP key material
0) U: Josue Balbuena (Master) <josue@sylabs.io>
   C: 2023-03-30 17:09:33 -0600 CST
   F: 7C2767…DA030D0
   L: 4096
   --------
1) U: Josue Balbuena (Staging - NOT MASTER) <josue@sylabs.io>
   C: 2023-03-30 17:17:28 -0600 CST
   F: 9B7B4…1AB57A2C
   L: 4096
   --------
Enter # of private key to use : 0
Enter key passphrase : 
INFO:    Signature created and applied to image 'rstudio.sif'
```

And last but not least, push the signed SIF file to the library. Before doing
so, make sure to type your account name (replace library) and your desired
collection name (replace tutorial101).

```bash
$ singularity push rstudio.sif library://<account name>/<collection name>/rstudio:2023.03
```

For demonstration purposes, this is the command used to push the SIF file to the SCS library:

```bash
singularity push rstudio.sif library://library/tutorial101/rstudio:2023.03
```
