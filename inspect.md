# 📄 Inspecting SIF files

Singularity stores metadata in their SIF files. For example, if you want to see the original definition file you can use the inspect command:

```bash
singularity inspect --deffile 

bootstrap: library
from: ubuntu:22.04
```

Similarly you can also show several of the sections like:

```bash
-r to show the runscript for the image.
-s to show the startscript.
-t to show the test script.
-e to show the environment settings.
```
