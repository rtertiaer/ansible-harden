`ansible-harden`
=========

This is the collection of tasks I run to harden my Debian hosts. It does a couple of things:
* installs & turns on fail2ban
* installs ufw, a firewall (default is to install but leave it disabled)
* installs apparmor profiles
* turns on unattended upgrades, optionally allowing the system to reboot
* optionally: manages an sshd configuration to:
  * use a specific port (default 22, but see Role Variables)
  * disable password authentication
  * only permit sudo and user groups to log in via ssh

I tried to make this safe to run by default; please check out the role variables and turn on functionality as you see fit. This also means it is extremely basic and generic; contributions are welcome! If you wanna see the guts, start by exploring in `tasks`.

Notes
-----
This role adds no firewall rules beyond the configured SSH port inbound, so if you enable `ufw` take care to allow any other services. If you have an existing system and you want to set more firewall rules right off the bat, you might want to install ufw and add them manually before running this role. (This is usually one of the first roles I apply to any fresh system, so it's typically not a concern for me.)

As a result of moving SSH to a custom port and turning UFW on, you may need to run this once against the OG SSH port and once again using your newly configured port. You can avoid this one-two setup by configuring Ansible to use multiplexing and persistent connections. That's beyond the scope of this readme, but [here's a helpful stack overflow question](https://stackoverflow.com/questions/68496003/how-can-i-make-ansible-reuse-ssh-sessions-instead-of-creating-a-new-one-for-each).

Requirements
------------

* Debian 12 (mildly tested on 13; ymmv on other releases & distros)
* Keyed authentication to your servers (use `ssh-copy-id user@server` to add your public key to servers before running this role)
* Your user in the `user` or `sudo` groups to permit ssh logins

Role Variables
--------------

A description of the settable variables for this role should go here, including any variables that are in defaults/main.yml, vars/main.yml, and any variables that can/should be set via parameters to the role. Any variables that are read from other roles and/or the global scope (ie. hostvars, group vars, etc.) should be mentioned here as well.

* `harden_sshd_port`: default is port 22 to avoid breaking systems unexpectedly
* `harden_allow_unattended_update_reboots`: default is false
* `harden_ufw_enabled`: default is `disabled`, [valid values here](https://docs.ansible.com/ansible/latest/collections/community/general/ufw_module.html#parameter-state)
* `harden_ufw_logging_enabled`: default is "on", [valid values here](https://docs.ansible.com/ansible/latest/collections/community/general/ufw_module.html#parameter-logging)


Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

    - hosts: servers
      roles:
         - role: rtertiaer.ansible_harden
           vars:
              harden_sshd_port: 2222
              harden_allow_unattended_update_reboots: true
              harden_ufw_enabled: enabled

License
-------

GPLv3
