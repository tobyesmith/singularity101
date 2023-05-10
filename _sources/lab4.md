# 📓 Lab 4: Using Singularity Container Services

Singularity Container Service (SCS) is a free service for users who don’t have
root access to their workstations and build images.

This service allows you to build a SIF image, store it remotely, and share it
with other users of the Singularity library. It can also store your signing keys
and let the community know you are the real author of the image.

You are welcome to [sign up at SCS](https://cloud.sylabs.io/), after which you
can generate an access token.

## Sign up to SCS

To sign up, please follow these steps:

1. Open your preferred web browser and copy and paste the SCS address in the
address bar: https://cloud.sylabs.io
2. Click the "Sign Up" button.
3. Select your preferred Single Sign On (SSO) option  from one of the four
available options If you do not use any of the approved providers, consider
creating an account.
4. Follow your SSO provider’s instructions. Once you have created an account,
you are automatically logged in.

## Create access tokens

Access tokens are a mechanism to communicate securely to SCS without disclosing
any sensitive data like passwords.

Create an access token by following these steps:

1. In the upper right corner, click over your username and select
“Access Tokens”.
2. Type a name and click on the green button “+ Create Access Token”.
3. Copy your access token and save it in a safe location, as you will need it to
sign in with the `singularity remote login` command.
4. Should  you lose this token in the future, you can create as many additional
tokens as  needed and even delete them if desired. Nonetheless, it is
recommended to log in to SCS using the following command with a token.

```bash
$ singularity remote login
Generate an access token at https://cloud.sylabs.io/auth/tokens, and paste it here.
Token entered will be hidden for security.
Access Token: 
INFO:    Access Token Verified!
INFO:    Token stored in /home/josue/.singularity/remote.yaml
```

## Remote building

Since SingularityCE 3.0, the ability to build a container on an external
resource is built in. To demonstrate this feature, we can use the same
`demo.def` file and submit it to a remote build service. Note that sudo is no
longer necessary, so it is omitted, and the -r flag is added. Additionally, you
must have an SCS account, an access token, and be logged into the service.

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

The messages from SCS are displayed in your terminal, and the resulting image is
downloaded to your local environment.  Any `%files` defined in your def file
will be uploaded to SCS and removed after a period of time.

If you want to save the resulting image in the Singularity library, you can
specify the destination as follows:

```bash
$ singularity build --remote library://joe/directory/image:tag deffile
```
