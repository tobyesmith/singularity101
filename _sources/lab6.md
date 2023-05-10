# 📓 Lab 6:  Signing with x509 Key Materials

Signatures are implemented using the [Dead Simple Signing Envelope](https://github.com/secure-systems-lab/dsse)
 (DSSE) standard.

To sign a container, a set of keys is typically required. A private key is used
for signing, while a public key, which is usually distributed, servers to verify
the signature.

For the purpose of this lab, let’s create this pair of keys. First, we’ll
generate a 2048-bit private key:

```bash
$ openssl genrsa -out private.pem 2048
```

Then, we can extract the public key:

```bash
$ openssl rsa -in private.pem -outform PEM -pubout -out public.pem
```

Normally, you will sign your own containers, for this lab, we are pulling an
unsigned image:

```bash
$ singularity pull container_lab6.sif docker://alpine:latest
INFO:    Converting OCI blobs to SIF format
INFO:    Starting build...
Getting image source signatures
Copying blob f56be85fc22e done  
Copying config 4798f93a2c done  
Writing manifest to image destination
Storing signatures
2023/04/18 08:56:29  info unpack layer: sha256:f56be8[...]d7f64b87abdaa09
INFO:    Creating SIF file...
```

Let's sign the container with a private key:

```bash
$ singularity sign --key private.pem container_lab6.sif
INFO:    Signing image with key material from 'private.pem'
INFO:    Signature created and applied to image 'container_lab6.sif'
```

You can now verify the image using a PEM public key like so:

```bash
$ singularity verify --key public.pem container_lab6.sif
INFO:    Verifying image with key material from 'public.pem'
Objects verified:
ID  |GROUP   |LINK    |TYPE
------------------------------------------------
1   |1       |NONE    |Def.FILE
2   |1       |NONE    |JSON.Generic
3   |1       |NONE    |JSON.Generic
4   |1       |NONE    |FS
INFO:    Verified signature(s) from image 'container_lab6.sif'
```
