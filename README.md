# Raspberry Pi Cluster v2.0

## Pre-Requisites

- Access to your router/network settings to check the Pi's connectivity & IP addresses
- [Raspberry Pi Imager](https://www.raspberrypi.com/documentation/computers/getting-started.html#set-up-your-raspberry-pi)
- [Ansible](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html)
- [Python 3](https://www.python.org/downloads/)

---

## Step 1

- Install the Raspberry Pi OS on each Pi [using the Raspberry Pi Imager](https://www.raspberrypi.com/documentation/computers/getting-started.html#set-up-your-raspberry-pi)
- During the installation process pay special attention to the **OS Customization** section
  - Set a host name
  - Set a username
  - Set a password
  - Ensure only SSH is enabled, disable Wi-Fi (if using Ethernet)
  - _Tip: using sequential numbering helps keep track of each node_
    - \_ex. pi-1, pi-2, pi-3 for a 3 node cluster
- In your network settings, find the IP address of each Pi and make a note of it
- In your terminal, ping each pi to ensure connectivity
  - `ping -c 5 <IP ADDRESS>`
- SSH into each Pi to ensure you can connect
  - `ssh <USERNAME>@<IP ADDRESS>`
  - The default username is `pi` and the default password is `raspberry` if you chose the defaults during the installation process. Otherwise this is the username and password you set during the **OS Customization** process.
- While SSH'd into each Pi, run the following command to update the OS
  - `sudo rpi-update`
- Once the OS is updated, reboot the Pi
  - `sudo reboot`
- Repeat this process for each Pi in your cluster

---

## Step 2

- Rename the `inventory.template` file to `inventory` and update the IP addresses & credentials of each Pi in your cluster
- Run the ping test to ensure Ansible can connect to each Pi
  - `ansible-playbook -i inventory.yml ping.yml`
- You should see something like this:
- ![ping-test](/imgs/ping.png)

---

## Step 3

- Run the `setup.yml` playbook to install the base configuration on each Pi
  - `ansible-playbook -i inventory.yml setup.yml`

---

## Step 4

- Run the `configure_ip.yml` playbook to configure the static IP addresses on each Pi
  - `ansible-playbook -i inventory.yml configure_ip.yml`

---

## Step 5

- Run the `configure_ssh.yml` playbook to enable SSH keys (password-less login) on each Pi
  - `ansible-playbook -i inventory.yml configure_ssh.yml`

---

## Step 6

- Run the `install_ansible.yml` playbook to install Ansible v8.0+ on the master node
  - `ansible-playbook -i inventory.yml install_ansible.yml`

---

## Step 7 - k3s Installation

- Follow the directions in the `/k3s-ansible` directory to install k3s on your cluster
- [k3s-ansible](/k3s-ansible/README.md)
