# 📄 Using Singularity Container Services

Singularity Container Service (SCS) is a free service offered to allow those who don’t have root access to their workstations and build images.

This service allows you to store remotely build a SIF image and share it with others if you preffer to do so in the Singularity library, also allows you to store your signing keys and let know the community you are the real author of the image.

Feel free to sign up at [SCS](https://cloud.sylabs.io/) and create an access token.

1. At the upper right corner, clic over your username and select “Access Tokens"
2. Type a name and clic on the green button “+ Create Access Token"
3. Copy your access token and save it in a safe place, you are going to need it when signing in with the `singularity remote login` command.

```bash
[josue@localhost ~]$ singularity remote login
Generate an access token at https://cloud.sylabs.io/auth/tokens, and paste it here.
Token entered will be hidden for security.
Access Token: 
INFO:    Access Token Verified!
INFO:    Token stored in /home/josue/.singularity/remote.yaml

```

## Remote building

The ability to build a container on an external resource is built in since Singularity 3.0, to demonstrate we can take the very same `demo.def` file but in this case, submit it to a remote build service. Note that the `sudo` is not needed anymore so it is dropped and the `-r` flag is added, also you need to have an account in SCS, an access token and login to the service.

```bash
singularity build -r demo.sif demo.def
INFO:    Starting build...
INFO:    Setting maximum build duration to 1h0m0s
INFO:    Remote "cloud.sylabs.io" added.
INFO:    Access Token Verified!
INFO:    Token stored in /root/.singularity/remote.yaml
INFO:    Remote "cloud.sylabs.io" now in use.
INFO:    Starting build...
INFO:    Downloading library image
INFO:    Downloaded 29816581 bytes in 4.475505819s
INFO:    Verifying bootstrap image /root/.singularity/cache/library/sha256.7a63c14842a5c9b9c0567c1530af87afbb82187444ea45fd7473726ca31a598b
INFO:    Creating SIF file...
INFO:    Build complete: /tmp/image-2540378504
INFO:    Performing post-build operations
INFO:    Calculating SIF image checksum
INFO:    Uploading image to library...
WARNING: Skipping container verification
INFO:    Uploading 29806592 bytes
INFO:    Image uploaded successfully.
INFO:    Build complete: demo.sif

```

The messages are streamed from the SCS to your terminal, and the resulting image is pulled to your local environment. Also if you have any `%files` defined in your def file, they are uploaded to SCS and discarded after some time.

In order to leave the resulting image in the Singularity library, you can specify the destination to be in the form of:

`singularity build -r library://<account>/directory/image:tag deffile`
