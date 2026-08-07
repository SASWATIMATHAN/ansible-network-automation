# Lab 02 — Ansible Inventory

## Objective

The objective of this lab was to create an Ansible inventory for
multiple Cisco IOS routers and establish the basic configuration
required for network automation.

## Network Devices

The lab uses three Cisco IOS routers:

| Device | Management IP |
|--------|---------------|
| R1 | 192.168.100.11 |
| R2 | 192.168.100.12 |
| R3 | 192.168.100.13 |

## Inventory

The devices were defined in:

```text
lab02_inventory/inventory/hosts.ini
```

The inventory contains the following group:

```ini
[cisco_routers]
R1 ansible_host=192.168.100.11
R2 ansible_host=192.168.100.12
R3 ansible_host=192.168.100.13

[cisco_routers:vars]
ansible_connection=ansible.netcommon.network_cli
ansible_network_os=cisco.ios.ios
ansible_user=admin
```

## Important Inventory Parameters

### `ansible_host`

Specifies the IP address used by Ansible to connect to the device.

### `ansible_connection`

```text
ansible.netcommon.network_cli
```

Specifies that Ansible should use its network CLI connection
mechanism rather than a normal Linux/Unix SSH connection.

### `ansible_network_os`

```text
cisco.ios.ios
```

Identifies the network operating system and allows Ansible to use
the appropriate Cisco IOS connection and command handling.

### `ansible_user`

Specifies the username used to authenticate to the Cisco devices.

## Basic Connectivity Test

Basic Ansible connectivity was tested using:

```bash
ansible -i lab02_inventory/inventory/hosts.ini cisco_routers -m ansible.builtin.ping -k
```

All three routers successfully responded with:

```text
R1 | SUCCESS => "ping": "pong"
R2 | SUCCESS => "ping": "pong"
R3 | SUCCESS => "ping": "pong"
```

This confirmed that Ansible could successfully communicate with the
three routers using the configured inventory.

## Cisco IOS Facts

The `cisco.ios.ios_facts` module was then tested using:

```bash
ansible -i lab02_inventory/inventory/hosts.ini cisco_routers -m cisco.ios.ios_facts -k
```

The command timed out on all three routers.

The failure and the subsequent GNS3, Windows, WSL, and management
network troubleshooting are documented separately in:

```text
lab02_inventory/troubleshooting_notes/ios_facts_timeout.md
```

The troubleshooting was intentionally stopped after the relevant
environmental checks so that development of the automation labs
could continue.

## Lab Outcome

The following concepts were completed in this lab:

- Created an Ansible inventory.
- Grouped multiple Cisco IOS routers under `cisco_routers`.
- Configured Cisco IOS network connection parameters.
- Used an Ansible ad-hoc command.
- Successfully verified basic Ansible connectivity to all three
  routers.
- Tested Cisco IOS facts collection.
- Documented the `ios_facts` connectivity issue and troubleshooting.

## Key Learning

Ansible inventory defines **which devices Ansible manages and how
those devices are reached**.

For network automation, the inventory works together with the
appropriate network connection plugin and network operating system
configuration.

The successful `ansible.builtin.ping` test confirmed that the
inventory configuration and basic Ansible network connectivity were
working before proceeding to subsequent labs.
