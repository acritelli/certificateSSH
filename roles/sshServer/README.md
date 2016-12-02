The sshServer role is the fundamental role used by all hosts in the environment that use certificates for user login. It is applied to all hosts **except** for the certificate authority and bastion hosts.

# Tasks

The role adds the public key of the CA and configures sshd to trust the key. It also adds the principals specified under the appropriate group_vars file for the hosts that the role is called on.

Currently, this is all implemented using the "lineinfile" module. This is less than ideal, and future implementations may use Jinja2.
