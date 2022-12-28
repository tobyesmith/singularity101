# Accessing host files

Normally some directories are available on the container, like your $HOME, /tmp, /proc, /sys and /dev, it is possible to access and even create and modify files and directories on the host system from the container.

You can specify other directories using the `--bind` option, for example, if we want to access a specific directory, lets say /opt/data, we can make it available to the container.

First lets make that directory and add some data:

```bash
sudo mkdir -p /opt/data
sudo chown $USER:$USER /opt/data
echo $(date) > /opt/data/date.txt
singularity exec demo.sif cat /opt/data/date.txt #This will fail.
singularity exec —bind /opt/data demo.sif cat /opt/data/date.txt #This will succeed.
```

In order to mount several directories, you can provide them as a comma separated list like:
`singularity exec --bind /dir1,/dir2,/dir3 demo.sif ls -l /`

You can even bind the directories and have them inside the containers with a different name:
`singularity exec --bind /dir1:/directory_one,/dir2:/directory_two demo.sif ls -l /`
