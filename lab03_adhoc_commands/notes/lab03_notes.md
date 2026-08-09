# Lab 03 Notes

This file contains concepts, syntax, observations, and key learning points related to Ansible ad-hoc commands.

## Ansible Ad-Hoc Commands

Ansible ad-hoc commands allow individual tasks to be executed directly from the command line without creating a playbook.

### General Syntax

```bash
ansible <host-pattern> -i <inventory> -m <module> -a "<arguments>"
```

### Important Options

| Option          | Meaning                                     |
| --------------- | ------------------------------------------- |
| `-i`            | Specifies the inventory file                |
| `-m`            | Specifies the Ansible module                |
| `-a`            | Provides arguments to the module            |
| `-k`            | Prompts for the SSH password                |
| `cisco_routers` | Targets the `cisco_routers` inventory group |

---

## `ansible.builtin.ping`

The Ansible ping module tests whether Ansible can communicate with the target.

A successful result returns:

```text
"ping": "pong"
```

It is an Ansible connectivity test and should not be confused with ICMP network ping.

---

## `cisco.ios.ios_facts`

The `cisco.ios.ios_facts` module collects structured information from Cisco IOS devices.

It can be used to obtain information such as:

* IOS version
* hostname
* hardware/platform information
* interfaces
* serial information
* configuration-related facts

Unlike `ansible.builtin.ping`, this module performs an actual network-device information-gathering operation.

### Lab Observation

The `ios_facts` command could not establish an SSH connection to the three GNS3 routers from the WSL environment.

The observed error was:

```text
Unable to connect to port 22
```

This is documented as an existing environment connectivity limitation rather than repeatedly troubleshooting the GNS3/WSL network.

---

## Important Learning Point

An Ansible module can be syntactically correct and still fail because the underlying network connection to the managed device is unavailable.

Therefore:

```text
Correct Ansible command
        +
Correct inventory
        +
Correct module
        +
Available SSH connectivity
        =
Successful execution
```
---

## `cisco.ios.ios_command`

The `cisco.ios.ios_command` module executes operational commands on Cisco IOS devices.

### General Syntax

```bash
ansible -i <inventory> <host-pattern> -m cisco.ios.ios_command -a "commands='<IOS command>'" -k
```

### Example

```bash
ansible -i ../lab02_inventory/inventory/hosts.ini cisco_routers -m cisco.ios.ios_command -a "commands='show version'" -k
```

### Common IOS Operational Commands

Examples include:

```text
show version
show ip interface brief
show ip route
show interfaces
show running-config
```

### Important Concept

`cisco.ios.ios_command` is intended primarily for **operational/show commands**.

It is different from configuration modules that are used to change the device configuration.

### Lab Observation

The `show version` ad-hoc command timed out on all three Cisco IOS routers because of the existing WSL-to-GNS3 connectivity limitation.

The command syntax and module selection were nevertheless demonstrated successfully at the Ansible level.
---

## `show ip interface brief`

`show ip interface brief` is a commonly used Cisco IOS operational command.

It provides a compact overview of the device interfaces.

### Typical Information

```text
Interface
IP-Address
Status
Protocol
```

### Why It Is Useful

It allows an administrator or automation system to quickly determine:

* Which interfaces exist
* Which interfaces have IP addresses
* Whether an interface is administratively up/down
* Whether the line protocol is up/down

### Ansible Usage

The command can be executed through:

```bash
ansible -i <inventory> <host-pattern> -m cisco.ios.ios_command -a "commands='show ip interface brief'" -k
```

In this lab, execution against the GNS3 routers resulted in timeouts because of the existing WSL-to-GNS3 connectivity limitation.
---

## Executing Multiple IOS Commands

The `cisco.ios.ios_command` module accepts a list of commands.

### Single Command

```bash
-a "commands='show version'"
```

### Multiple Commands

```bash
-a "commands=['show version','show ip interface brief']"
```

The commands are placed inside a list using square brackets.

### General Pattern

```text
commands=['command_1','command_2','command_3']
```

This allows multiple operational commands to be requested in a single Ansible ad-hoc task.

### Important Distinction

`cisco.ios.ios_command` is intended for operational commands such as:

```text
show version
show ip interface brief
show ip route
show interfaces
```

It is not the module we will use later for making persistent configuration changes on Cisco IOS devices.

---

## Host Targeting in Ad-Hoc Commands

The second argument after the inventory specifies the **host pattern**.

### Target an Inventory Group

```bash
ansible -i <inventory> cisco_routers -m <module>
```

This targets all hosts belonging to:

```text
[cisco_routers]
```

### Target One Host

```bash
ansible -i <inventory> R1 -m <module>
```

This targets only `R1`.

### Target Another Host

```bash
ansible -i <inventory> R2 -m <module>
```

This targets only `R2`.

### Target All Inventory Hosts

The special pattern:

```text
all
```

can be used to target all hosts defined in the inventory.

### Key Concept

The host pattern controls **WHO** Ansible operates on.

```text
ansible
   ↓
inventory
   ↓
host pattern
   ↓
module
   ↓
arguments
```

For network automation, this allows the same ad-hoc operation to be applied to an entire device group or to a specific router.
