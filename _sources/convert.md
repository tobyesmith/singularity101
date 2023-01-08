# 📄 Convert docker images to SIF

There are ocassions where an image is not available in the Singularity library, you can take one docker and convert it to the native Singularity Image Format.

`singularity pull docker://ubuntu:22.04`

This is the most easy, it will download all the docker format layers and compile a SIF image for you.
Other method is to use a definition file and take advantage of the bootstrap agent like so:

```bash
Bootstrap: docker
From: ubuntu:22.04

%post
   ...
%runscript
   ...
   
```
