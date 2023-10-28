
# Vagrant Setup 

This is a basic setup script to create a Vagrant environment for two nodes (master-slave configuration). The master VM is set up with a Lamp stack using the `provision.sh` script, and the slave VM is configured with Ansible to run a playbook called `laravel-deployment.yml`. The Vagrant environment is defined in a `Vagrantfile`.

## Prerequisites

Before running this script, make sure you have the following prerequisites installed on your system:

- [Vagrant](https://www.vagrantup.com/)
- [VirtualBox](https://www.virtualbox.org/) (as the provider for Vagrant)
- [Ansible](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html) (for provisioning the slave VM with `laravel-deployment.yml`)
- The `provision.sh` script for provisioning the master VM.

## Lamp script
The lamp.sh script is used to install a LAMP stack on an Ubuntu 18.04 server,preferably ubuntu jammy so that the the php8,2 runs . It includes components such as
Apache2, PHP8.2 and MySQL. 

### The following applications must be installed and configured 
- Apache2 webserver
- PHP and dependencies
- MySQL and user details configuration
- Composer
- git clone repository link

## Instructions

1. Save the script as a file, for example, `lamp.sh`.

2. Make the script executable by running the following command in your terminal:

   ```bash
   chmod +x setup.sh
   ```

3. Run the script using the following command:

   ```bash
   ./setup.sh
   ```

4. The script will create a `Vagrantfile` and configure the master and slave VMs.

5. To start the Vagrant environment, execute the following command:

   ```bash
   vagrant up
   ```

## Vagrantfile Configuration

The Vagrantfile created by the script configures the Vagrant environment with two VMs: master and slave. Here's an overview of the configuration:

### Master VM

- **Box**: The master VM is based on the "ubuntu/jammy64" box.
- **Hostname**: The hostname is set to "master."
- **Network Configuration**: The master VM is assigned a static IP address of "192.168.56.13" on a private network.
- **Provider**: The provider is set to VirtualBox with 1GB of RAM and 1 CPU core.
- **Provisioning**: The `provision.sh` script is executed on the master VM.

### Slave VM

- **Box**: The slave VM is also based on the "ubuntu/jammy64" box.
- **Hostname**: The hostname is set to "slave."
- **Network Configuration**: The slave VM is assigned a static IP address of "192.168.56.14" on a private network.
- **Provider**: The provider is set to VirtualBox with 1GB of RAM and 1 CPU core.
- **Provisioning**: Ansible is used to provision the slave VM with the `laravel-deployment.yml` playbook, and an inventory file located at "/home/Theodora/Desktop/confirmed/inventory.ini" is provided.
- The ansible.cfg file is also configured and available in the root directory where the execution took place.

## Ansible Playbook (laravel.yml)
The following Ansible playbook, laravel.yml, is used to provision the slave VM:

yaml
Copy code
---
- hosts: all
  become: yes
  vars:
    laravel_app_url: https://{{ ansible_host }}/laravel
    laravel_db_name: laravel

  tasks:cccccc
  - name: Deploy Laravel application
    script: provision.sh
    args:
      laravel_app_url: "{{ laravel_app_url }}"
      laravel_db_name: "{{ laravel_db_name }}"

  - name: Verify PHP application
    uri:
      url: "{{ laravel_app_url }}"
      status_code: 200
    register: result

  - name: Take screenshot of PHP application
    command: "gnome-screenshot -f /tmp/laravel_screenshot.png"
    when: result.status == 200

  - name: Create Cron Job
      cron:
        name: Check Server Uptime
        job: /usr/bin/uptime > /var/log/uptime.log
        minute: 0
        hour: 0
      become_user: root


## Starting the Vagrant Environment

Once the script has been executed and the Vagrantfile is created, you can start the Vagrant environment by running:

```bash
vagrant up
```

This will launch both the master and slave VMs and provision them according to the defined configurations.

You can access the master and slave VMs by using SSH. For example, to access the master VM, you can run:

```bash
vagrant ssh master
```

To access the slave VM, replace "master" with "slave" in the command.

That's it! You now have a Vagrant environment set up with a master-slave configuration ready for your use.

Check the files listed in this directory to understand the whole setup better.
