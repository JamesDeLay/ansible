# Raspberry Pi Cluster Ansible Automation

## What does this stuff do?

This repository contains opinionated playbooks that can be used to prepare a cluster of raspberry pis. The `prep.yml` playbook will update the base software and run an initial update & upgrade. The `intialize.yml` playbook will change the default credentials, lock the default account, create a new sudo enabled user, enable ssh authenticating via a key-pair, install a firewall, and much more. After running this playbook you can remove the `ansible_ssh_pass` entry from the `inventory.yml` file; ssh will now use key-pair authentication.

**Warning: The intialize.yml playbook disables Wi-Fi & Bluetooth**

## Directions


1. Flash RaspianOS Lite onto your SD card using the Raspian Imager
2. Perform Initial Boot
   1. Plug the SD card into the Pi
   2. plug in power
   3. wait 2 minutes
   4. Disconnect power
   5. Remove SD card 
1. Run `ansible-playbook prep-local.yml`
2. Plug the SD card back into the Pi & connect power
3. Rename `inventory_template.yml` to `inventory.yml`
4. Fill out the inventory file with information about your devices
   1. The attached template is geared toward a cluster, mine has one master and 4 nodes
5. Run `ansible-playbook ping.yml`
6. Run `ansible-playbook prep-remote.yml`
7. Run `ansible-playbook initialize.yml`
8. Edit the `inventory.yml` and remove the `ansible_ssh_password` entry and change the `ansible_ssh_host` to your new username

## Requirements

`ansible` installed on your control node

`sshpass` for initial `ssh` connection until the key-pair is configured:

    Ubuntu
    sudo apt-get install sshpass

    MacOS
    brew install hudochenkov/sshpass/sshpass

`ansible-galaxy collection install community.general`

`ansible-galaxy collection install ansible.posix`


## Useful Commands

SSH to an IP
```
ssh user@0.0.0.0
```

Start a playbook
`ansible-playbook PLAYBOOK_NAME`

Review the inventory JSON
`ansible-inventory --list`
    Use `-i /path/to/inventoryfile` if different from inventory file specified in `ansible.cfg`

Generate SSH keys (save them to the `/keys` dir)
```bash
#!/bin/bash
ssh-keygen -t rsa -b 4096
```

Ansible - pass extra variables when running commands
```
--extra-vars newpassword=12345678 example=lilyhammer
```
## Reference Material

### Videos
- [Network Chuck Pi Cluster - Video](https://www.youtube.com/watch?v=X9fSMGkjtug&t=1058s)
- [The Digital Life Ansible Automation - Video](https://www.youtube.com/watch?v=uR1_hlHxvhc&t=1382s)
- [How to Secure a Raspberry Pi - Video](https://www.youtube.com/watch?v=ukHcTCdOKrc)


### Articles and Docs
- https://www.cyberciti.biz/faq/ansible-apt-update-all-packages-on-ubuntu-debian-linux/
- https://docs.ansible.com/ansible-core/devel/user_guide/intro_inventory.html#inventory-basics-formats-hosts-and-groups
- https://bitsanddragons.wordpress.com/2021/05/27/avoid-typing-ssh-passwords-with-sshpass-on-macos/
- https://github.com/kenfallon/fix-ssh-on-pi
- https://serverfault.com/questions/566762/how-do-i-add-sudo-permissions-to-a-user-created-with-ansible
- https://www.digitalocean.com/community/tutorials/how-to-use-nmap-to-scan-for-open-ports
- https://kofler.info/kernel-4-0-auf-dem-raspberry-pi/
- https://galaxy.ansible.com/weareinteractive/ufw
- https://www.redhat.com/sysadmin/ansible-playbooks-secrets