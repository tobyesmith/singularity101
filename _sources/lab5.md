# 📓 Lab 5:  Signing containers with PGP

In this lab we are going to sign our own containers, and then push it to the SCS.

If you don't have a PGP key yet, you will need to generate one.

```bash
$ singularity key newpair
Enter your name (e.g., John Doe) : Firstname Lastname
Enter your email address (e.g., john.doe@example.com) : first.last@sylabs.io
Enter optional comment (e.g., development keys) : a comment
Enter a passphrase :
Retype your passphrase :
Would you like to push it to the keystore? [Y,n] Y
Generating Entity and OpenPGP Key Pair... done
```

To see the signing keys available:

```bash
$ singularity key list

Public key listing (/home/anonymous/.singularity/sypgp/pgp-public):

0) U: David Trudgian (demo) <first.last@sylabs.io> <-- User
   C: 2022-11-15 09:54:54 -0600 CST                <-- Creation timestamp
   F: E5F780B2C22F59DF748524B435C3844412EE233B     <-- Fingerprint
   L: 4096                                         <-- Key length
   --------
```

Now that a key is generated, a SIF image can be signed, lets pull an unsigned
image:

```bash
$ singularity pull container_lab5.sif docker://alpine:latest
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

```bash
$ singularity sign container_lab5.sif

Signing image: container_lab5.sif
Enter key passphrase :
Signature created and applied to container_lab5.sif
````

In order to verify the SIF image:

```bash
singularity verify container_lab5.sif

Verifying image: container_lab5.sif
[LOCAL]   Signing entity: Firstname Lastname (Demo keys) <first.last@sylabs.io>
[LOCAL]   Fingerprint: 65833F473098C62D69E5CEE85D448
Objects verified:
ID  |GROUP   |LINK    |TYPE
------------------------------------------------
1   |1       |NONE    |Def.FILE
2   |1       |NONE    |JSON.Generic
3   |1       |NONE    |FS
Container verified: container_lab5.sif
```

Now you can push an image to the Singularity library, assuming your username is
`Joe` you can specify the destination to be in the form of:

```bash
$ singularity push container_lab5.sif library://<account>/directory/image:tag
...
```
