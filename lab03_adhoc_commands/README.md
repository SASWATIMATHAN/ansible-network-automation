# 🚀 Lab 03 — Ansible Ad-Hoc Commands

## 📌 Overview

This lab introduces **Ansible ad-hoc commands** for network automation with Cisco IOS devices.

Ad-hoc commands allow individual automation tasks to be executed directly from the command line without creating a playbook.

The lab demonstrates:

* Ansible ad-hoc command syntax
* Cisco IOS network modules
* Gathering IOS device facts
* Executing operational IOS commands
* Executing multiple IOS commands
* Targeting individual routers
* Understanding Ansible command-line options
* Comparing expected and actual results
* Documenting network connectivity limitations

---

## 🎯 Objectives

By completing this lab, the following concepts are demonstrated:

1. Understand the structure of an Ansible ad-hoc command.
2. Use the `ansible.builtin.ping` module.
3. Use the `cisco.ios.ios_facts` module.
4. Use the `cisco.ios.ios_command` module.
5. Execute Cisco IOS operational commands.
6. Execute multiple IOS commands in a single ad-hoc task.
7. Target a specific inventory host.
8. Understand the purpose of `-i`, `-m`, `-a`, and `-k`.
9. Maintain separate expected and actual outputs.
10. Document environment-specific connectivity limitations.

---

## 🏗️ Lab Environment

The lab uses the existing Cisco network automation environment established in the previous labs.

### Network Topology

```text
                    ☁️ Cloud
                       │
                 GNS3 / VMware
                       │
                      SW1
                 ┌─────┼─────┐
                 │     │     │
                R1    R2    R3
                 │     │     │
          192.168.100.x management network
```

### Components

| Component        | Purpose                               |
| ---------------- | ------------------------------------- |
| GNS3             | Network simulation                    |
| GNS3 VM / VMware | Hosts the virtual network environment |
| Cisco IOS R1     | Managed router                        |
| Cisco IOS R2     | Managed router                        |
| Cisco IOS R3     | Managed router                        |
| SW1              | Network connectivity                  |
| WSL Ubuntu       | Ansible execution environment         |

---

## 📁 Directory Structure

```text
lab03_adhoc_commands/
│
├── .gitignore
├── README.md
│
├── commands/
│   └── adhoc_commands.md
│
├── expected_outputs/
│   ├── multiple_ios_commands_expected.md
│   ├── show_ip_interface_brief_expected.md
│   ├── show_version_expected.md
│   └── single_router_expected.md
│
├── notes/
│   └── lab03_notes.md
│
├── outputs/
│   ├── ios_facts_actual.txt
│   ├── multiple_ios_commands_actual.txt
│   ├── show_ip_interface_brief_actual.txt
│   ├── show_version_actual.txt
│   └── single_router_show_version_actual.txt
│
├── screenshots/
│
└── troubleshooting/
    └── connectivity_limitation.md
```

---

## 🔧 Inventory

Lab 03 uses the inventory created in **Lab 02**:

```text
../lab02_inventory/inventory/hosts.ini
```

The inventory contains:

```text
[cisco_routers]
R1 ansible_host=192.168.100.11
R2 ansible_host=192.168.100.12
R3 ansible_host=192.168.100.13
```

The Cisco IOS devices use the Ansible `network_cli` connection:

```text
ansible_connection=ansible.netcommon.network_cli
ansible_network_os=cisco.ios.ios
ansible_user=admin
```

---

# 🧩 Ad-Hoc Command Syntax

The general Ansible ad-hoc command structure is:

```bash
ansible <host-pattern> -i <inventory> -m <module> -a "<arguments>"
```

For SSH password authentication:

```bash
-k
```

can be added to prompt for the password.

### Important Options

| Option       | Meaning                               |
| ------------ | ------------------------------------- |
| `-i`         | Specifies the inventory               |
| `-m`         | Specifies the Ansible module          |
| `-a`         | Specifies module arguments            |
| `-k`         | Prompts for the SSH password          |
| Host pattern | Determines which devices are targeted |

---

# 🧪 Experiments

## 1. Ansible Ping

The Ansible ping module was used as the initial ad-hoc connectivity test:

```bash
ansible -i ../lab02_inventory/inventory/hosts.ini cisco_routers -m ansible.builtin.ping -k
```

The three routers were successfully reached during the test.

Expected response:

```text
"ping": "pong"
```

---

## 2. Cisco IOS Facts

The `cisco.ios.ios_facts` module was tested to collect structured information from the Cisco IOS devices:

```bash
ansible -i ../lab02_inventory/inventory/hosts.ini cisco_routers -m cisco.ios.ios_facts -k
```

The execution failed because SSH connectivity from WSL to the GNS3 routers was unavailable.

The actual result is documented in:

```text
outputs/ios_facts_actual.txt
```

---

## 3. Execute `show version`

The Cisco IOS `show version` command was executed using:

```bash
ansible -i ../lab02_inventory/inventory/hosts.ini cisco_routers -m cisco.ios.ios_command -a "commands='show version'" -k
```

The command timed out on R1, R2, and R3.

Expected behavior is documented in:

```text
expected_outputs/show_version_expected.md
```

Actual execution is documented in:

```text
outputs/show_version_actual.txt
```

---

## 4. Execute `show ip interface brief`

The command:

```bash
ansible -i ../lab02_inventory/inventory/hosts.ini cisco_routers -m cisco.ios.ios_command -a "commands='show ip interface brief'" -k
```

was used to request a summary of the router interfaces.

The execution timed out on all three routers.

Expected behavior:

```text
expected_outputs/show_ip_interface_brief_expected.md
```

Actual result:

```text
outputs/show_ip_interface_brief_actual.txt
```

---

## 5. Execute Multiple IOS Commands

Multiple operational commands can be supplied as a list:

```bash
ansible -i ../lab02_inventory/inventory/hosts.ini cisco_routers -m cisco.ios.ios_command -a "commands=['show version','show ip interface brief']" -k
```

This demonstrates the use of a command list:

```text
commands=['command1','command2']
```

The execution timed out because of the existing connectivity limitation.

Expected behavior:

```text
expected_outputs/multiple_ios_commands_expected.md
```

Actual result:

```text
outputs/multiple_ios_commands_actual.txt
```

---

## 6. Target a Single Router

Instead of targeting the entire `cisco_routers` group, an individual router can be specified:

```bash
ansible -i ../lab02_inventory/inventory/hosts.ini R1 -m cisco.ios.ios_command -a "commands='show version'" -k
```

Only R1 was targeted.

The command timed out because of the existing connectivity limitation.

Expected behavior:

```text
expected_outputs/single_router_expected.md
```

Actual result:

```text
outputs/single_router_show_version_actual.txt
```

---

# 📊 Expected vs Actual Results

| Test                      | Expected                  | Actual                   |
| ------------------------- | ------------------------- | ------------------------ |
| Ansible ping              | `pong`                    | ✅ Success                |
| `ios_facts`               | IOS facts returned        | ❌ SSH connection failure |
| `show version`            | IOS version information   | ❌ Timeout                |
| `show ip interface brief` | Interface summary         | ❌ Timeout                |
| Multiple IOS commands     | Output from both commands | ❌ Timeout                |
| Single-router targeting   | R1 command output         | ❌ Timeout                |

The failures are documented rather than repeatedly troubleshooting the previously investigated WSL-to-GNS3 connectivity limitation.

---

# ⚠️ Connectivity Limitation

The GNS3 routers are reachable within the GNS3 network environment, but the WSL Ubuntu environment has an established connectivity limitation when attempting SSH access to the routers.

Observed errors include:

```text
Unable to connect to port 22
```

and:

```text
timed out
```

This limitation has already been investigated during the previous network automation labs.

Therefore, Lab 03 focuses on:

* Correct Ansible syntax
* Correct module selection
* Host targeting
* Command construction
* Expected behavior
* Actual execution results
* Proper documentation

No repeated connectivity troubleshooting was performed.

Detailed documentation:

```text
troubleshooting/connectivity_limitation.md
```

---

# 🧠 Key Learning

Ansible ad-hoc commands follow a simple structure:

```text
                    Ansible
                       │
                       ▼
                   Inventory
                       │
                       ▼
                  Host Pattern
                       │
                       ▼
                     Module
                       │
                       ▼
                   Arguments
                       │
                       ▼
                 Target Device
```

For network automation:

```text
ansible
   ↓
inventory
   ↓
Cisco IOS device/group
   ↓
cisco.ios.ios_command
   ↓
show command
```

### Key distinction

`ansible.builtin.ping`:

> Tests Ansible connectivity.

`cisco.ios.ios_facts`:

> Collects structured facts from Cisco IOS devices.

`cisco.ios.ios_command`:

> Executes operational commands on Cisco IOS devices.

---

# 📸 Screenshots

Screenshots demonstrating the lab execution will be added here.

| Experiment                | Screenshot                                |
| ------------------------- | ----------------------------------------- |
| Ansible ad-hoc ping       | `ANSIBLE_ADHOC_PING.png`                  |
| IOS facts                 | `IOS_FACTS_SSH_FAILURE.png`               |
| `show version`            | `IOS_COMMAND_SHOW_VERSION_TIMEOUT.png`    |
| `show ip interface brief` | `IOS_COMMAND_INTERFACE_BRIEF_TIMEOUT.png` |
| Multiple IOS commands     | `MULTIPLE_IOS_COMMANDS_TIMEOUT.png`       |
| Single-router targeting   | `SINGLE_ROUTER_ADHOC_TIMEOUT.png`         |

---

# 📚 Related Labs

| Lab                              | Topic                              |
| -------------------------------- | ---------------------------------- |
| [Lab 01](../lab01_installation/) | Ansible Installation & Environment |
| [Lab 02](../lab02_inventory/)    | Ansible Inventory                  |
| **Lab 03**                       | **Ansible Ad-Hoc Commands**        |
| Lab 04                           | First Ansible Playbook             |

---

# 🚀 Next Lab

**Lab 04 — First Ansible Playbook**

The next lab will move from one-line ad-hoc commands to reusable Ansible playbooks.

---

## 👨‍💻 Author

**Saswati Anupama Mathan**

M.Tech — Electronics & Communication Engineering
Specialization: Communication

---

⭐ This lab is part of the **Ansible Network Automation** project.
