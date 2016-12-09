This collection of playbooks is designed to implement parts of the architecture described by Facebook's Engineering Blog post: [Scalable and secure access with SSH by Marlon Dutra](https://code.facebook.com/posts/365787980419535/scalable-and-secure-access-with-ssh/)

These roles are designed for RHEL-based systems (tested on CentOS 7.2), but could easily be extended to work with other systems.

# Roles

The following roles, in order of execution, are implemented. Additional information about each role can be found in the role's README file.

1. ntpClient - implemented by all hosts, as correct timing is necessary for a certificate-based system.
2. automationServer - implemented by the Ansible server (localhost), this role performs some initial prep work.
3. newCA - implemented by the Certificate Authority, this role performs the initial setup of a CA.
4. bastion - implemented by the bastion host, which contains accounts, public keys, and certificates for all users.
5. existingCA - implemented by the Certificate Authority, this role signs all of the public keys generated for users on the basion host.
..* A single task is called on bastionHosts in the root main.yml after this role completes. This task adds the certificates into user's .ssh directories. As this was only a single task, I decided not to split it into a role.
6. sshServers - implemented by all hosts that end-users will log into, this role configures the hosts to trust the CA and the appropriate principals

# Setup steps

Setup is designed to be fairly straightforward. First, the **hosts** file should be modified to accurately represent your environment. The following groups of hosts are implemented:

* certificateAuthority - this is the host that will sign and create certificates for all other users. Certificates are signed for a period of 1 week, per the existingCA role.
* bastionHosts - these hosts contain all user accounts and keypairs, and are used as "jump hosts" to other hosts in the environment
* Other hosts (i.e. webservers, dbservers) - these are the remaining hosts in the environment. Each additional host should also have a group_vars file with a list of the appropriate principals that can be used for login (see below).

The following modifications must be made to the group_vars:

* all.yml - variables that must be shared among multiple hosts
  * The **users** dictionary should be changed to represent the users in your organization. The dictionary uses the username as a key and the value is a comma-separated list of principals that apply to the user. Temporary users are provided in the included file for clarity.
  * The **ansible_temp_directory** variable is the path to a directory created on the ansible host. This directory is used for storage of downloaded public keys and certificates. The default is /tmp/ansible_ssh
* Each set of hosts that require principals must have an associated group_vars file. The file contains the **principals** list, which is simply a list of principals allowed for root login on the host. All hosts already receive the root-everywhere principal. Samples for "dbservers" and "webservers" are included.

# Tags

This set of playbooks implements the following tags:

* **initialBuild** - This creates everything from scratch. A new CA key is created, users are added, SSH keys are created for each user, and certificates are generated for the keys. This role can also be used whenever the CA should be re-keyed.
* **reSignOnly** - This creates new certificates for existing users using the existing CA key. This could be used after the expiration period has passed on the previously signed certificate.
* **reKeyReSign** - This creates new SSH key pairs and signed certificates for existing users.

# Improvement list

This is a basic implementation, with some caveats. Perfection is the enemy of greatness, so I decided to at least get this code out there. Please check the GitHub issues for an up-to-date TODO list. Some initial issues that I've found include:

* This method, while usable, isn't particularly scalable to thousands of users.
  * A better implementation would call an Ansible playbook to generate keys and a certificate for a user **when they log in** instead of performing all of the generations and signatures at once.
  * A better storage method for users, principals, and hosts would be more desirable instead of listing them out in group_vars.

* Editing configs may be better-handled by Jinja2 and templates.

* It may be nice to find a way to increment the serial numbers of user certificates

* A more fine-tuned sshd_config would be desirable. Disabling password login and tuning the accepted algorithms would be a good start.

* It would be useful to specify various parameters as variables, instead of hardcoding them into tasks (i.e. certificate validity period, SSH key type, etc.)

* Facebook's engineering article discusses the need for a capable accounting system so that when a user logs in as root, everything is tracked. This set of roles does not currently make any effort to configure such a system.

* Additional work to define firewall and access rules would be useful.
  * Bastion should only be accessible from trusted network
  * Hosts should only be accessible from bastions
  * CA should only be accessible from specific, trusted hosts
