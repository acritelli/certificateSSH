The newCA role is used to deploy a fresh certificate authority for signing the public keys of users with the appropriate principals. The public key of the CA is trusted by other hosts in the environment.

# Tasks

The role ensures that an appropriate directory exists for the CA key pair. It then generates a fresh key pair of type ECDSA for the CA to use, and downloads the public key for distribution to other hosts in the environment.

**Note: This will overwrite an existing CA key pair**
