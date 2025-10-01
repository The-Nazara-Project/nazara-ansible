# Nazara Ansbile

This repository contains an Ansible playbook and role to deploy [Nazara](https://github.com/The-Nazara-Project/Nazara) to Linux hosts.
The playbook installs Nazara, generates a configuration file, runs it and then cleans up temporary files.

> [!Danger]
> This repo is currently under heavy development and not ready for production use. Please stand by.

## Features

- Supports installation via:
    - `cargo install` from [crates.io](https://crates.io)
    - Install from latest release tarball if no `cargo` exists
- Automatically generates the Nazara cofnig file from an Ansible template
- Runs Nazara as root
- Cleans up downloaded artifacts
- Works for both devices and VMs (configurable via variables)

## Requirements

- Control machine:
    - Ansible >= 2.9
    - SSH access to hosts
- Target hosts:
    - Linux
    - Root privileges
    - Cargo package manager installed

## Quick Start

1. Make sure you have Ansible installed

2. Clone this repo

```bash
git clone https://github.com/The-Nazara-Project/Nazara.git
cd nazara-ansible
```

3. Define your inventory

Edit `inventory/hosts.ini` with the machines you want to deploy to:

```yml
device1 ansible_host=192.168.1.10
device2 ansible_host=192.168.1.11
```

4. Set variables

Edit `inventory/group_vars/all.yml` to configure Nazara:

```toml
# NetBox connection
netbox_uri: "https://netbox.example.com"
netbox_api_token: "CHANGE_ME"

# How to install Nazara
nazara_version: "0.1.0-beta.2"
use_cargo: false   # true = install with cargo, false = download GitHub release

# Common settings
nazara_type: "vm"   # "device" or "vm"
nazara_status: "active"
nazara_description: "Provisioned via Nazara Ansible"
nazara_comments: "Automatically registered by Nazara."

# Device-specific vars (only used if nazara_type = "device")
device_type: 12
device_role: 5
device_site: 2

# VM-specific vars (only used if nazara_type = "vm")
vm_cluster: 3
```

5. Run the playbook

```bash
ansible-playbook playbooks/deploy-nazara.yml
```

This will:
1. Install Nazara (via Cargo)
2. Create `/root/.config/nazara/config.toml` on each host
3. Run `sudo nazara`
4. Clean up

## Security Note

- Keep the NetBox API token **out of version control**. Use Ansible Vault or environment variables instead.
- Permissions for `/root/.config/nazara/config.toml` are restricted to `root`

## TODO

- Support installation via distribution package manager