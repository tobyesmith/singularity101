# Multi-Stage Build

Multi-Stage build are useful where one stage can be used for compilation, and the resulting binary can be copied to a light and final environment, allowing a slimmer image without any of the development stack.

This can be done adjusting a definition file by adding the `Stage` keyword to the header section like so:

```bash
Bootstrap: library
From: josue-sylabs/golang:1.19.4
Stage: compiler

%post
  git clone ...
  go build -o program program.go

Bootstrap: library
From: alpine:3.9
Stage: final

%files from compiler
  /root/program /bin/program
```

The names of the stages are up to you, and each of the sections will be executed in the same order as described. Note that if you re-arrange the order of the above stages:

```bash

Bootstrap: library
From: alpine:3.9
Stage: final

%files from compiler
  /root/program /bin/program

Bootstrap: library
From: josue-sylabs/golang:1.19.4
Stage: compiler

%post
  git clone ...
  go build -o program program.go

```

The “final" stage cannot get the files from the “compiler” stage because they do not exist.
