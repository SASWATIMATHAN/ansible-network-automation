# Ansible Ad-Hoc Commands

This file contains the Ansible ad-hoc commands used during Lab 03.
# Ansible Ad-Hoc Commands

## 1. Test Connectivity Using Ansible Ping

### Command

```bash
ansible -i ../lab02_inventory/inventory/hosts.ini cisco_routers -m ansible.builtin.ping -k
```

### Purpose

Tests Ansible connectivity to all Cisco IOS routers defined in the `cisco_routers` inventory group.

### Module

`ansible.builtin.ping`

### Authentication

The `-k` option prompts for the SSH password.

### Expected Result

All three routers should return:

```text
"ping": "pong"
```

### Result

R1, R2, and R3 were successfully reached using the existing Lab 02 inventory and Ansible `network_cli` configuration.

### Screenshot

`../screenshots/ANSIBLE_ADHOC_PING.png`
## 2. Collect Cisco IOS Facts

### Command

```bash
ansible -i ../lab02_inventory/inventory/hosts.ini cisco_routers -m cisco.ios.ios_facts -k
```

### Purpose

The `cisco.ios.ios_facts` module is used to collect structured information and operational facts from Cisco IOS devices.

### Result

The command was executed against R1, R2, and R3.

All three devices returned an SSH connectivity failure:

```text
R1 | FAILED! => {
    "changed": false,
    "msg": "[Errno None] Unable to connect to port 22 on 192.168.100.11"
}

R2 | FAILED! => {
    "changed": false,
    "msg": "[Errno None] Unable to connect to port 22 on 192.168.100.12"
}

R3 | FAILED! => {
    "changed": false,
    "msg": "[Errno None] Unable to connect to port 22 on 192.168.100.13"
}
```

### Warning

Ansible reported:

```text
ansible-pylibssh not installed, falling back to paramiko
```

This warning indicates that Ansible fell back to Paramiko for the SSH transport. The actual failure was the inability to establish an SSH connection to port 22 on the Cisco routers.

### Environment Note

The WSL-to-GNS3 router connectivity limitation is a known issue in this lab environment and was previously investigated during the network automation project. No repeated connectivity troubleshooting was performed during this lab.

### Screenshot

`../screenshots/IOS_FACTS_SSH_FAILURE.png`
## 3. Execute `show version` Using `cisco.ios.ios_command`

### Command

```bash
ansible -i ../lab02_inventory/inventory/hosts.ini cisco_routers -m cisco.ios.ios_command -a "commands='show version'" -k
```

### Purpose

The `cisco.ios.ios_command` module is used to execute operational commands on Cisco IOS devices.

The `show version` command displays information about the Cisco IOS software, hardware platform, uptime, and related system details.

### Result

The command was sent to R1, R2, and R3, but all three executions timed out:

```text
R1 | FAILED! => {
    "changed": false,
    "msg": "timed out"
}

R2 | FAILED! => {
    "changed": false,
    "msg": "timed out"
}

R3 | FAILED! => {
    "changed": false,
    "msg": "timed out"
}
```

### Warning

Ansible reported:

```text
ansible-pylibssh not installed, falling back to paramiko
```

This warning was also observed during the `ios_facts` test.

### Observation

The `cisco.ios.ios_command` syntax was accepted by Ansible, but the command could not be executed because communication with the Cisco IOS devices timed out.

This is consistent with the existing WSL-to-GNS3 connectivity limitation documented for this lab.

### Screenshot

The execution result is captured in the Lab 03 screenshots directory.

## 4. Execute `show ip interface brief`

### Command

```bash
ansible -i ../lab02_inventory/inventory/hosts.ini cisco_routers -m cisco.ios.ios_command -a "commands='show ip interface brief'" -k
```

### Purpose

The `show ip interface brief` command provides a concise summary of the interfaces configured on a Cisco IOS device.

It is commonly used to check:

* Interface names
* IP addresses
* Interface status
* Line protocol status

### Result

The command was executed against the `cisco_routers` group.

All three routers returned a timeout:

```text
R1 | FAILED! => {
    "changed": false,
    "msg": "timed out"
}

R2 | FAILED! => {
    "changed": false,
    "msg": "timed out"
}

R3 | FAILED! => {
    "changed": false,
    "msg": "timed out"
}
```

### Observation

The command syntax was accepted by Ansible, but the Cisco IOS devices could not be reached for command execution because of the existing WSL-to-GNS3 connectivity limitation.

### Screenshot

The command execution and timeout results were captured in the Lab 03 screenshots directory.

## 5. Execute Multiple IOS Commands

### Command

```bash
ansible -i ../lab02_inventory/inventory/hosts.ini cisco_routers -m cisco.ios.ios_command -a "commands=['show version','show ip interface brief']" -k
```

### Purpose

The `cisco.ios.ios_command` module can execute multiple operational commands during a single Ansible task.

In this example, two Cisco IOS commands are requested:

```text
show version
show ip interface brief
```

### Important Syntax

A single command is specified as:

```text
commands='show version'
```

Multiple commands are specified as a list:

```text
commands=['show version','show ip interface brief']
```

### Result

The command was executed against R1, R2, and R3.

All three routers timed out because of the existing WSL-to-GNS3 connectivity limitation.

### Observation

The multiple-command syntax was successfully constructed and submitted to Ansible. The failure occurred during communication with the managed devices rather than because of the command-list syntax.

### Screenshot

The execution result is captured in the Lab 03 screenshots directory.

## 6. Target a Single Router

### Command

```bash
ansible -i ../lab02_inventory/inventory/hosts.ini R1 -m cisco.ios.ios_command -a "commands='show version'" -k
```

### Purpose

Ansible can target an individual device from the inventory instead of targeting the entire inventory group.

In this example:

```text
R1
```

targets only the router named `R1`.

### Result

The command was accepted by Ansible and targeted R1 specifically.

The execution timed out:

```text
R1 | FAILED! => {
    "changed": false,
    "msg": "timed out"
}
```

### Observation

The host-pattern determines which inventory hosts Ansible operates on.

For example:

```text
cisco_routers → R1, R2, R3
R1            → R1 only
R2            → R2 only
R3            → R3 only
```

The timeout is attributed to the existing WSL-to-GNS3 connectivity limitation.

### Screenshot

The execution result is captured in the Lab 03 screenshots directory.
