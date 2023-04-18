# 📄 Signing and verifying images

SingularityCE’s SIF images can be signed, and subsequently verified, so that a
user can be confident that an image they have obtained is a bit-for-bit
reproduction of the original container as the author intended it. The signature,
over the metadata and content of the container, is created using a private key,
and directly added to the SIF file. This means that a signed container carries
its signature with it, avoiding the need for extra infrastructure to distribute
signatures to end users of the container.

A user verifies the container has not been modified since signing using a public
key or certificate. By default, SingularityCE uses PGP keys to sign and verify
containers. Since 3.11, signing and verifying containers with X.509 key material
 / certificates is also supported.

As well as indicating a container has not been modified, a valid signature may
be used to indicate a container has undergone testing or review, and is approved
for execution. Multiple signatures can be added to a container, to document its
progress through an approval process.
