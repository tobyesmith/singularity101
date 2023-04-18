# 📓 Lab 2.2: Running Containers as Instances

Let’s run some instances as demonstration, the instance keyword is new, but you
are probably wondering what does that docker:// means. In this context, it will
automatically pull an image from the docker hub and convert the image to SIF.
Then it comes the name of the instance.

```bash
$ sudo singularity instance start --writable-tmpfs –net \
       --network-args “portmap=8080:80/tcp” \
       docker://nginx:1.23.4-alpine webserver
INFO:    Converting OCI blobs to SIF format
INFO:    Starting build...
Getting image source signatures
Copying blob f56be85fc22e done  
Copying blob 1f21f983520d done  
Copying blob 59b9d2200e63 done  
Copying blob 3e1e579c95fe done  
Copying blob 547a97583f72 done  
Copying blob 2ce963c369bc done  
Copying blob c23b4f8cf279 done  
Copying config 3f8c932fa7 done  
Writing manifest to image destination
Storing signatures
2023/03/30 00:57:11  info unpack layer: sha256:f56be85fc223d[...]d7c4e5add29d7f64b87abdaa09
2023/03/30 00:57:11  info unpack layer: sha256:2ce963c369b51[...]dfcc1e286051b5a5d9a7b0cc5c
2023/03/30 00:57:11  info unpack layer: sha256:59b9d2200e646[...]244c38ebe8d3beef5c476c4c55
2023/03/30 00:57:11  info unpack layer: sha256:3e1e579c95f8c[...]af9dbe6219dc6495abb9902040
2023/03/30 00:57:11  info unpack layer: sha256:547a97583f77d[...]83ffa18e0c40fd87adb5c06025
2023/03/30 00:57:11  info unpack layer: sha256:1f21f98352062[...]dd79878071181b56b82efa9ef3
2023/03/30 00:57:11  info unpack layer: sha256:c23b4f8cf27eb[...]ac215ab5b529aa8b5a060294c8
INFO:    Creating SIF file...
INFO:    instance started successfully
```

The above command creates an instance, it starts the official NGINX image
running in the background. The instance needs writing to disk, so we’ve set
the --writable-tmpfs flag to allocate space in memory.

The --net flag is necessary when using the --network-args which is needed
because in this example we are mapping the port 80 inside the container to
the port 8080 in the host or workstation.

Note: We are using sudo and not fakeroot or proot because real administration
privilege is needed in order to manipulate internal system firewalls (iptables)
and network setup (create bridged network interface).

Next, we can list our instances running in the host:

```bash
$ sudo singularity instance list
INSTANCE NAME    PID       IP           IMAGE
web              232106    10.22.0.6    /root/.singularity/cache/oci...

Next, let's start the NGINX web server inside the container:
$ sudo singularity exec instance://webserver nginx
023/03/30 01:06:50 [notice] 14#14: using the "epoll" event method
2023/03/30 01:06:50 [notice] 14#14: nginx/1.23.4
2023/03/30 01:06:50 [notice] 14#14: built by gcc 12.2.1 20220924 (Alpine 12.2.1_git20220924-r4) 
2023/03/30 01:06:50 [notice] 14#14: OS: Linux 6.2.7-200.fc37.x86_64
2023/03/30 01:06:50 [notice] 14#14: getrlimit(RLIMIT_NOFILE): 524288:524288
2023/03/30 01:06:50 [notice] 20#20: start worker processes
2023/03/30 01:06:50 [notice] 20#20: start worker process 21
2023/03/30 01:06:50 [notice] 20#20: start worker process 22
2023/03/30 01:06:50 [notice] 20#20: start worker process 23
2023/03/30 01:06:50 [notice] 20#20: start worker process 24
2023/03/30 01:06:50 [notice] 20#20: start worker process 25
2023/03/30 01:06:50 [notice] 20#20: start worker process 26
2023/03/30 01:06:50 [notice] 20#20: start worker process 27
2023/03/30 01:06:50 [notice] 20#20: start worker process 28
2023/03/30 01:06:50 [notice] 20#20: start worker process 29
2023/03/30 01:06:50 [notice] 20#20: start worker process 30
2023/03/30 01:06:50 [notice] 20#20: start worker process 31
2023/03/30 01:06:50 [notice] 20#20: start worker process 32
```

Then we can open our browser and load http://localhost:8080 to verify we are
running a local webserver.

```bash
$ curl http://localhost:8080
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
10.22.0.1 - - [30/Mar/2023:01:08:27 -0600] "GET / HTTP/1.1" 200 615 "-" "curl/7.85.0" "-"
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```

In Addition to running a command against an instance and listing the current
instances in our host, other operations can be performed, for example, services
are normally running in the background and are normally writing to the standard
output. SingularityCE collects standard output and error into log files.

To view the location of such log files, use the --log option like so:

```bash
$ sudo singularity instance list --logs
INSTANCE NAME    PID       LOGS
web              263932    /root/.singularity/web.err
                           /root/.singularity/web.out
```

Our last operation against an instance in this tutorial is to actually
terminate and stop it.

```bash
$ sudo singularity instance stop webserver
INFO:    Stopping web instance of /root/.singularity/...a5538d0158f (PID=232106)
```
