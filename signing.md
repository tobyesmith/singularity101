# Signing and verifying images

Signing images provides a method of trust for sharing images to Singularity library community. It ensures a bit-for-bit reproduction of the original container as the author intended it.

To sign your own containers, first need to generate a key.

```bash
singularity key newpair
Enter your name (e.g., John Doe) : Firstname Lastname
Enter your email address (e.g., john.doe@example.com) : first.last@sylabs.io
Enter optional comment (e.g., development keys) : a comment
Enter a passphrase :
Retype your passphrase :
Would you like to push it to the keystore? [Y,n] Y
Generating Entity and OpenPGP Key Pair... done
```

To see the signing keys:

```bash
singularity key list

Public key listing (/home/anonymous/.singularity/sypgp/pgp-public):

0) U: David Trudgian (demo) <first.last@sylabs.io> <-- User
   C: 2022-11-15 09:54:54 -0600 CST                <-- Creation timestamp
   F: E5F780B2C22F59DF748524B435C3844412EE233B     <-- Fingerprint
   L: 4096                                         <-- Key length
   --------
```

Now that a key is generated, a SIF image can be signed:

```bash
singularity sign container.sif

Signing image: container.sif
Enter key passphrase :
Signature crated and applied to container.sif
```

In order to verify the SIF image:

```bash
singularity verify container.sif

Verifying image: container.sif
[LOCAL]   Signing entity: Firstname Lastname (Demo keys) <first.last@sylabs.io>
[LOCAL]   Fingerprint: 65833F473098C6215E750B3BDFD69E5CEE85D448
Objects verified:
ID  |GROUP   |LINK    |TYPE
------------------------------------------------
1   |1       |NONE    |Def.FILE
2   |1       |NONE    |JSON.Generic
3   |1       |NONE    |FS
Container verified: container.sif
```

Now you can push an image to the Singularity library:

`singularity push container.sif library://<account>/directory/image:tag`
