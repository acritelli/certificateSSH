The existingCA role is used to sign certificates using an existing certificate authority, which was created by the newCA role.

# Tasks

The role performs the tasks necessary for transferring and signing the public keys of users on the bastion host. The signed public key certificate is then downloaded to the Ansible server.

Keys are signed with a validity period of 1 week.
