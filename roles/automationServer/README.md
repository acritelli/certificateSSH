The automationServer role is excuted on the Ansible server (localhost) and performs any setup items that are necessary.

# Tasks

Currently, the role is only responsible for creating a temporary directory for storing SSH public keys and certificates, which must be moved around between hosts during the deployment.
