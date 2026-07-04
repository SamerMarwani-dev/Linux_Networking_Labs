# Lab Challenge: Jakarta - It's always DNS

## Challenge Overview
* **Difficulty:** Hard
* **Type:** Fix / Configuration Repair
* **Objective:** Resolve the system-wide hostname resolution failure. Initially, executing `ping google.com` fails with the error: `ping: google.com: Name or service not known`. The goal is to successfully translate the domain name into an IP address.

---

## Technical Investigation & Troubleshooting Steps

### 1. Verifying System Name Servers
The initial step involved inspecting the primary DNS configuration file located at `/etc/resolv.conf` to check if valid upstream resolvers or local stubs were defined. 

Running `cat /etc/resolv.conf` showed that the system is properly utilizing `systemd-resolved` via the local loopback stub address `127.0.0.53`. This confirmed that the upstream name resolution stack was locally intact but shadowed by an intermediate layer.

![DNS Resolution Configuration](./1.png)

### 2. Auditing Name Service Switch Order
Since the global resolvers were configured correctly, the investigation shifted to the Name Service Switch configuration file (`/etc/nsswitch.conf`). This file dictates the lookup order for system databases, including hostnames.

Executing `cat /etc/nsswitch.conf` revealed the root cause: the `hosts:` database directive was restricted solely to `files`. The standard `dns` keyword was completely omitted, meaning the operating system was strictly looking at local files (like `/etc/hosts`) and entirely ignoring network DNS queries.

![Original Switch Configuration](./2.png)

### 3. Modifying the Name Service Database Priority
To restore standard internet name resolution capabilities, administrative privileges were used to modify the switch order. 

The configuration file was opened using `sudo nano /etc/nsswitch.conf`, and the `hosts:` entry was updated to append `dns` right after `files`. This ensures the system checks the local lookup files first, then falls back to full DNS resolution if the hostname is not locally defined.

```text
hosts:      files dns