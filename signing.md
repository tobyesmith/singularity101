# 📄 Signing and verifying images

SingularityCE’s SIF images can be signed and then verified, giving users
confidence that the images they obtain are bit-for-bit reproductions of the
original containers, as intended by the authors. The signature, which
encompasses  the container’s metadata and content, is generated using a private
key and directly added to the SIF file. This means that a signed container
carries its signature with it, eliminating the need for additional
infrastructure to distribute signatures to end users.

To confirm that a  container has not been modified since signing, users can
employ a public key or certificate. By default, SingularityCE uses PGP keys to
sign and verify containers. From version 3.11 onwards, it also supports signing
and verifying containers with X.509 key materials or certificates.

Besides indicating a container has not been modified, a valid signature can
signify that a container has been tested or reviewed, and is approved for
execution. Multiple signatures can be added to a container to document its
progress through an approval process.
