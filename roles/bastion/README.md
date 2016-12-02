The bastion role is excuted on any hosts that act as bastion hosts for the environment. Thease are essentially "jump hosts." End-users log into the bastion, and then jump to a server in the environment. The bastion servers contain all users accounts, keypairs, and certificates.

# Tasks

The role creates user accounts for all users, generates public/private keypairs, and then downloads the public keys to the Ansible server so that they can later be signed.
