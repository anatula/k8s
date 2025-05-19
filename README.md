# Kubernetes The Hard Way 

## Vagrant Lab Environment
This Vagrant configuration sets up a multi-machine lab environment with Debian-based virtual machines for Kelsey Hightower's [kubernetes-the-hard-way](https://github.com/kelseyhightower/kubernetes-the-hard-way).


- Creates 4 virtual machines with predefined hostnames and IP addresses
- Configures a private network (192.168.56.0/24) for inter-VM communication
- Sets up root access with password authentication
- Includes basic SSH configuration for easy access
- Provides DNS configuration with Google's public DNS
- Currently using debian/bookworm64

| Hostname | IP Address    | RAM Allocation | Role         |
|----------|---------------|----------------|--------------|
| jumpbox  | 192.168.56.5  | 512MB          | Access point |
| server   | 192.168.56.10 | 2GB            | Main server  |
| node-0   | 192.168.56.11 | 2GB            | Worker node  |
| node-1   | 192.168.56.12 | 2GB            | Worker node  |

### Requirements
- Vagrant (https://www.vagrantup.com/)
- VirtualBox (https://www.virtualbox.org/)
- 5GB+ of free RAM recommended for running all VMs simultaneously

### Getting Started

Run `vagrant up` to create all virtual machines

To create a specific machine: `vagrant up <hostname>` (e.g., vagrant up server)

### Access Information
- All VMs are configured with root access:

    Username: root
    Password: root

- SSH access is enabled with password authentication

- Host-only network allows communication between VMs

### Usage
Access a VM via SSH: `vagrant ssh <hostname>`
Destroy all VMs: `vagrant destroy`


### Notes
The jumpbox VM (512MB) is intended as a lightweight access point

The server and node VMs have 2GB RAM for running applications

DNS is configured to use 8.8.8.8 (Google DNS) by default