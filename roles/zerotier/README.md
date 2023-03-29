# zerotier-bridge

Installation and configuration and Zerotier One. It requires two interfaces. One interface connected to a LAN, and other with Internet connection (WAN).

## Requirements

Two interfaces. One interface connected to a LAN, and other with Internet connection (WAN).

Only tested on Ubuntu 22.04. It should work on other Debian based distributions. But it is not tested.

Uses netplan to configure the bridge interface.

## Role Variables

Bold variables are recommended to be changed. The rest usually are not required to be changed.

- **lan_interface**: This variable is the name of the interface connected to the LAN. (default: ```eth0```)
- **wan_interface**: This variable is the name of the interface connected to the WAN. (default: ```eth1```)
- **zerotier_ip_address**: This variable is the IP address assigned to the Zerotier interface. It must be in CIDR notation. (default: ```172.26.115.180/20```)
- **zerotier_network_id**: This variable is the network ID of the Zerotier network to join. (default: ```<empty_value>``)
- *zerotier_download_base_url*: This variable is the base URL for downloading Zerotier One. (default: ```https://download.zerotier.com```)
- *zerotier_gpg_key_url*: This variable is the URL for downloading the GPG key for Zerotier One. (default: ```https://raw.githubusercontent.com/zerotier/ZeroTierOne/master/doc/contact%40zerotier.com.gpg```)
- *zerotier_apt_repository*: This variable is the URL for the Zerotier One APT repository. (default: ```deb {{ zerotier_download_base_url }}/debian/{{ ansible_distribution_release }} {{ ansible_distribution_release }} main```)
- *allow_managed*: zero tier is allowed to manage resources on the target system. If set to 1, Ansible is allowed to manage resources. If set to 0, Ansible is not allowed to manage resources. (default: ```0```)
- *allow_global*: zero tier is allowed to access global resources on the target system. Global resources are typically system-wide resources that can affect the behavior of the entire system. If set to 1, Ansible is allowed to access global resources. If set to 0, Ansible is not allowed to access global resources. (default: ```0```)
- *allow_default*: zero tier is allowed to use default settings on the target system. Default settings are typically pre-configured settings that come with the system or a particular software package. If set to 1, Ansible is allowed to use default settings. If set to 0, Ansible is not allowed to use default settings. (default: ```0```)
- *allow_dns*: zero tier is allowed to use DNS resolution on the target system. If set to 1, Ansible is allowed to use DNS resolution. If set to 0, Ansible is not allowed to use DNS resolution. This can be useful in certain scenarios where DNS resolution may not be desirable or available. (default: ```0```)
- *bridge_utils_package*: This variable is the name of the package for bridge-utils. (default: ```bridge-utils```)
- *netplan_config_file_dest*: This variable is the destination path for the netplan configuration file. (default: ```/etc/netplan/50-cloud-init.yaml```)


Tags
----

If you don't want to run all tasks, there are some tags available:

- *install*: Install and setup Zerotier One
- *join*: Join a network
- *udev*: Configure udev rules, for applying configuration changes when Zerotier connects and disconnects.
- *bridge*: Configure bridge interface using netplan.

Example Playbook
----------------

inventory.yaml:

```yaml
all:
  children:
    iot-gw-hyperv:
      hosts:
        10.2.0.171:
  vars:
    ansible_ssh_common_args: '-o StrictHostKeyChecking=no'
    ansible_user: 'oriol'
```

test.yml

```yaml
---
- name: Zerotier bridge
  hosts: all
  gather_facts: true
  become: true
  roles:
    - name: "i40sys.zerotier_bridge_ansible_role"
      vars:
        zerotier_network_id: "abfd31bd474d749a"
        zerotier_ip_address: "172.26.115.180/20"
        lan_interface: "eth0"
        wan_interface: "eth1"
```

CLI commands, assuming that we're in the tests directory:
```bash
# install ansible role from ansible-galaxy
ansible-galaxy install i40sys.zerotier_bridge_ansible_role
# or, if you want to install it from a local requirements file
ansible-galaxy install -r roles/requirements.yml

# launch the playbook
ansible-playbook -i inventory.yaml test.yml

# if you want to use tags
ansible-playbook -i inventory.yaml test.yml --tags "install,join,udev,bridge"
# or, for instance, skipping udev
ansible-playbook -i inventory.yaml test.yml --skip-tags "udev"
```

License
-------

The code in this repository is licensed under the MIT License (MIT). Please see the [LICENSE](LICENSE) file for details.


Author Information
------------------

Oriol Rius <oriol@joor.net>
Raimon Figueras <raimon@riotsis.com>

Project site: https://industry40.systems
