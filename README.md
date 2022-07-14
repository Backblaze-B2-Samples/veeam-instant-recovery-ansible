<h1 align="center">
Veeam Instant Recovery on PhoenixNAP Bare Metal Cloud (BMC)
</h1>

<p>
This repo has been forked from https://github.com/phoenixnap/ansible-collection-pnap and the docs have been updated to address how to recovery instantly using Veeam on PhoenixNAP BMC</p>

<p align="center">
Ansible collection of modules for interacting with the <a href="https://developers.phoenixnap.com/apis">Bare Metal Cloud API</a>. This collection contains the <i><b>server</b></i> module which allows you to automate <a href="https://phoenixnap.com/bare-metal-cloud">Bare Metal Cloud</a> server provisioning and management.
</p>

<p align="center">
  <a href="https://phoenixnap.com/bare-metal-cloud">Bare Metal Cloud</a> •
  <a href="https://galaxy.ansible.com/phoenixnap/bmc">Ansible Galaxy</a> •
  <a href="https://developers.phoenixnap.com/">Developers Portal</a> •
  <a href="http://phoenixnap.com/kb">Knowledge Base</a> •
  <a href="https://developers.phoenixnap.com/support">Support</a>
</p>

## Requirements

- [Bare Metal Cloud](https://bmc.phoenixnap.com) account
- Ansible 2.9+
- Python 2 (version 2.7) or Python 3 (versions 3.5 and higher)
  - Python **_requests_** package

## Creating a Bare Metal Cloud account

1. Go to the [Bare Metal Cloud signup page](https://support.phoenixnap.com/wap-jpost3/bmcSignup).
2. Follow the prompts to set up your account.
3. Use your credentials to [log in to Bare Metal Cloud portal](https://bmc.phoenixnap.com).

:arrow_forward: **Video tutorial:** [How to Create a Bare Metal Cloud Account in Minutes](https://www.youtube.com/watch?v=hPR60XWOSsQ)
<br>

:arrow_forward: **Video tutorial:** [How to Deploy a Bare Metal Server in a Minute](https://www.youtube.com/watch?v=BzBBwLxR80o)

## Installing Ansible

Follow these helpful tutorials to learn how to install Ansible on Ubuntu and Windows machines.

- [How to Install and Configure Ansible on Ubuntu 20.04](https://phoenixnap.com/kb/install-ansible-ubuntu-20-04)
- [How to Install and Configure Ansible on Windows](https://phoenixnap.com/kb/install-ansible-on-windows)
- [How to Install and Configure Ansible on Mac OSX](https://www.toptechskills.com/ansible-tutorials-courses/how-to-install-ansible-mac-os-x-tutorial/)

## Installing the Bare Metal Cloud Ansible module

This Ansible collection contains the **_server_** module which requires the Python **_requests_** HTTP library to work properly. If you don't have it installed on your machine already, run this command to install it:

    pip install requests

Now install the Ansible collection by running:

    ansible-galaxy collection install phoenixnap.bmc

You can view the **_server_** module documentation with this command:

    ansible-doc phoenixnap.bmc.server

## Authentication

You need to create a configuration file called `config.yaml` and save it in the user home directory. This file is used to authenticate access to your Bare Metal Cloud resources.

In your home directory, create a folder `.pnap` and a `config.yaml` file inside it.

This file needs to contain only two lines of code:

    clientId: <enter your client id>
    clientSecret: <enter your client secret>

To get the values for the clientId and clientSecret, follow these steps:

1. [Log in to the Bare Metal Cloud portal](https://bmc.phoenixnap.com).
2. On the left side menu, click on API Credentials.
3. Click the Create Credentials button.
4. Fill in the Name and Description fields, select the permissions scope and click Create.
5. In the table, click on Actions and select View Credentials from the dropdown.
6. Copy the values from the Client ID and Client Secret fields into your `config.yaml` file.

## Ansible Playbook for Veeam Instant Recovery on PhoenixNAP

Ansible Playbooks allow you to interact with your Bare Metal Cloud resources. You can create and delete servers as well as perform power actions with simple code instructions.

This example shows you how to deploy a Windows and ESXi Server in PhoenixNAP. On the Windows server, you can install Veeam VBR and then configure the ESXi Server as a host server in Veeam

Ansible playbooks are YAML files and follow the format `playbook_name.yml`. The _name_ part of the filename should contain the action you want to perform. In our case, since we are creating a couple of servers, the filename is `playbook_create.yaml`.

Once you've created the file, open it and paste this code (this file is also checked into this repo):
<br><br>
<b>Note:</b> Be default, the servers you provision will be locked down. Please update the <i>management_access_allowed_ips</i> and <i>rdp_allowed_ips</i> config values below to reflect your public IP.

```yaml

- name: Create servers for Instant Recovery
  hosts: localhost
  gather_facts: false
  vars_files:

    - ~/.pnap/config.yaml
  collections:
    - phoenixnap.bmc
  tasks:

  - server:
      client_id: "{{clientId}}"
      client_secret: "{{clientSecret}}"
      hostnames: veeam-ir-esxi
      location: PHX
      os: esxi/esxi70u2
      type: d1.c2.medium
      state: present
      ssh_key: "{{ lookup('file', '~/.ssh/id_rsa.pub') }}"
      management_access_allowed_ips: ["192.168.1.100"]
  
  - server:
      client_id: "{{clientId}}"
      client_secret: "{{clientSecret}}"
      hostnames: veeam-ir-windows
      location: PHX
      os: windows/srv2019dc
      type: s1.c2.medium
      state: present
      rdp_allowed_ips: [192.168.1.100]
      ssh_key: "{{ lookup('file', '~/.ssh/id_rsa.pub') }}"

```

## Running the playbook
To run the playbook use the following command
``` sh
ansible-playbook playbook_create.yaml -v
```
The `-v` option is important as this option will print out the credentials that you need to log into the resources


For more examples, check out this helpful tutorial: [Bare Metal Cloud Playbook Examples](https://phoenixnap.com/kb/how-to-install-phoenixnap-bmc-ansible-module#htoc-bmc-playbook-examples)

## References
<p>
  <a href="https://phoenixnap.com/bare-metal-cloud">Bare Metal Cloud</a> •
  <a href="https://galaxy.ansible.com/phoenixnap/bmc">Ansible Galaxy</a> •
</p>
