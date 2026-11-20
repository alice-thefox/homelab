# Homelab on a repurposed laptop

A small home server on an old Asus Eee PC. This is a learning project about Linux server setup, secure remote access and web hosting.

## Hardware
- Asus Eee PC, Intel Atom N450 (2 threads, 1.66 GHz)
- Atheros Wi-Fi and Ethernet
- Runs on mains power only (old battery)
- Internet for the first setup came from a phone (USB tethering)

## Result
- Ubuntu Server 26.04 LTS running headless
- Wi-Fi connection that survives reboots
- SSH access with a key only
- Firewall allowing only SSH and HTTP
- nginx serving my own static HTML page to other devices on the home network

## Setup overview

### 1. Installation
- Installed Ubuntu Server LTS from a USB flash drive
- The installer froze several times, so I installed it all without a network initially

### 2. Networking
- Got a temporary connection through USB tethering 
- Installed the missing Wi-Fi service:
- Configured Wi-Fi with netplan (`/etc/netplan/*.yaml`, permissions `600`):
  ```yaml
  network:
    version: 2
    wifis:
      wlp2s0:
        dhcp4: true
        access-points: {"NETWORK_NAME": {password: "PASSWORD"}}
  ```
### 3. SSH hardening
- Created a key pair on my laptop: `ssh-keygen -t ed25519`
- Copied the public key to the server: `ssh-copy-id user@server`
- Disabled passwords and root login in `/etc/ssh/sshd_config.d/10-hardening.conf`:
  ```
  PasswordAuthentication no
  PermitRootLogin no
  ```

### 4. Firewall (UFW)
```
sudo ufw allow OpenSSH
sudo ufw allow 80/tcp
sudo ufw enable
sudo ufw status
```
### 5. Web server
```
sudo apt install nginx
sudo nano /var/www/html/index.html
```
- Replaced the default page with my own
- Checked it from a laptop and a phone on the same Wi-Fi

## What I learned
- How netplan, systemd services and apt fit together
- Why key-only SSH is safer than passwords, and why to test in a second session first
- Why a firewall must allow SSH before it is enabled
- How to read error messages and debug one step at a time
