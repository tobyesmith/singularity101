# 📓 Lab 4: Using Singularity Container Services

Singularity Container Service (SCS) is a free service offered to allow those who
don’t have root access, fakeroot or proot is not available and cannot build an
image properly.

This service allows you to remotely build a SIF image and share it with others
if you prefer to do so in the Singularity library, also allows you to store your
signing keys and let the community know you are the real author of the image.

Feel free to sign up at [SCS](https://cloud.sylabs.io/) and create an access
token afterwards.

## Sign up to SCS

In order to Sign up please follow these steps:

1. Open your preferred web browser and copy and paste the SCS address in the
address bar: https://cloud.sylabs.io
2. Click the "Sign Up" button.
3. Select your preferred Single Sign On option, you must select one of the 4
options available, if you do not have any of the approved providers, consider
creating an account.
4. Follow the instructions given by your provider such as, typing username and
password or the mechanism set by them.

5. Once you have created an account, you are automatically logged in.

## Create access tokens

Access tokens serves as a mechanisms to communicate securely to SCS without
exposing or disclosing the provider's credentials.

To create access tokens please follow these steps.

1. At the upper right corner, click over your username and select
“Access Tokens”.
2. Type a name and click on the green button “+ Create Access Token”.

Copy your access token and save it in a safe place, you are going to need it
when signing in with the singularity remote login command.

If in the future you loose or forget this token, you can create as many as you
wish, and also delete one if is your choice. However is adviced to login again
to SCS with the following command using a token.

```bash
$ singularity remote login
Generate an access token at https://cloud.sylabs.io/auth/tokens, and paste it here.
Token entered will be hidden for security.
Access Token: 
INFO:    Access Token Verified!
INFO:    Token stored in /home/josue/.singularity/remote.yaml
```

## Remote building

The ability to build a container on an external resource is built in since
Singularity 3.0, to demonstrate we can take the very same lab3.def file but in
this case, submit it to a remote build service. Note that the sudo is not needed
anymore so it is dropped and the -r flag is added, also you need to have an
account in SCS, an access token and login to the service.

```bash
$ singularity build --remote lab3-remote.sif lab3.def
INFO:    Starting build...
INFO:    Setting maximum build duration to 1h0m0s
INFO:    Remote "cloud.sylabs.io" added.
INFO:    Access Token Verified!
INFO:    Token stored in /root/.singularity/remote.yaml
INFO:    Remote "cloud.sylabs.io" now in use.
INFO:    Starting build...
INFO:    Downloading library image
INFO:    Downloaded 29816581 bytes in 4.475505819s
INFO:    Verifying bootstrap image /root/.singularity/cache/library/sha256...
INFO:    Creating SIF file...
INFO:    Build complete: /tmp/image-2540378504
INFO:    Performing post-build operations
INFO:    Calculating SIF image checksum
INFO:    Uploading image to library...
WARNING: Skipping container verification
INFO:    Uploading 29806592 bytes
INFO:    Image uploaded successfully.
INFO:    Build complete: lab3-remote.sif
```

The messages are streamed from the SCS to your terminal, and the resulting image
is pulled to your local environment. Also if you have any `%files` defined in
your `def` file, they are uploaded to SCS and discarded after some time.

In order to leave the resulting image in the Singularity library, assuming your
username is `Joe` you can
specify the destination to be in the form of:

```bash
$ singularity build --remote library://joe/directory/image:tag deffile
```
