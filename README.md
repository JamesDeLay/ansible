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

## Step 3

- Run the `setup.yml` playbook to install the base configuration on each Pi
  - `ansible-playbook -i inventory.yml setup.yml`
  -

## Step 4

- Run the `configure_ip.yml` playbook

## Step 5

- Configure passwordless SSH between each Pi in the cluster
  - `cd keys && ssh-copy-id -i cluster_key.pub {{user}}@{{host}}`
  - Restart sshd on each Pi
    - `sudo systemctl restart sshd`
  - `ssh <USERNAME>@<IP ADDRESS>`
  - You should now be able to SSH into each Pi without a password

---

## Playbook Outlines

---

### `ping.yml`

**Run CMD:** `ansible-playbook -i inventory.yml ping.yml`

##### Ping Test

- Ping each Pi in the inventory file.
- Display the results of the ping test.

---

### `setup.yml`

**Run CMD:** `ansible-playbook -i inventory.yml setup.yml`

#### Tasks

##### Set Timezone

- Set to **America/New_York**.

##### Update & Upgrade

- Update the package list.
- Perform a system upgrade (including kernel).
- Remove unnecessary packages.
- Clean up leftover package files.

##### Disable Unneeded Services

- Disable **Bluetooth** (on ARM-based models).
- Disable **serial console** access.
- Stop and disable **Avahi** (mDNS).
- Remove **Telnet** if installed.

##### Firewall Setup

- Install and enable **UFW** (Uncomplicated Firewall).
- Allow **SSH** through the firewall.
- Set default firewall to **deny** all incoming connections.

##### Reboot Management

- Check if a reboot is needed.
- Reboot the system if required.
- Wait for the Raspberry Pi to come back online.
- Re-check for updates after reboot.
- Apply any missed updates after reboot.

---

### `configure_ip.yml`

**Run CMD:** `ansible-playbook -i inventory.yml configure_ip.yml`

#### Tasks

##### Set Static IP in `dhcpcd.conf`

- Ensure the static IP configuration is added to `/etc/dhcpcd.conf`.
- Use the `interface` variable (default `eth0`) to set the static IP and router IP.
- If the file doesn't exist, it will be created and a backup will be made.
- Insert the configuration after the existing `interface` line if not already present.

##### Restart `dhcpcd` Service

- Restart the `dhcpcd` service to apply the static IP changes.
- Only restart if the specified interface is correctly defined.

##### Verify Static IP

- Run the `ip addr show` command to verify the static IP configuration on the specified interface.
- Store the result in the `ip_config` variable.

##### Output Configured Static IP

- Output the configured static IP for the specified interface using the `debug` module.
