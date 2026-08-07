# 🧩 Lab 02 — Ansible Inventory & Cisco Device Connectivity

## 📌 Overview

This lab introduces **Ansible Inventory Management** for Cisco IOS network devices.

The objective is to create a structured Ansible inventory containing multiple Cisco routers and verify communication between Ansible and the network devices.

The lab uses three Cisco IOS routers running in **GNS3**, while Ansible is executed from **Ubuntu on WSL**.

This lab establishes the foundation for the subsequent Ansible network automation labs.

---

## 🎯 Objectives

By completing this lab, the following concepts were covered:

- Creating an Ansible inventory
- Creating host groups
- Defining Cisco router management IP addresses
- Configuring Ansible for Cisco IOS devices
- Using the `network_cli` connection plugin
- Identifying the Cisco IOS network platform
- Authenticating with Cisco IOS devices
- Verifying Ansible connectivity
- Executing Cisco IOS-specific modules
- Investigating a network automation timeout
- Documenting troubleshooting procedures
- Maintaining reproducible command and output records

---

## 🏗️ Lab Environment

| Component | Details |
|---|---|
| Automation OS | Ubuntu on WSL |
| Automation Tool | Ansible Core 2.16.3 |
| Network Platform | Cisco IOS |
| Network Emulator | GNS3 |
| Virtualization | GNS3 VM / VMware |
| Network Devices | R1, R2, R3 |
| Connection Method | `ansible.netcommon.network_cli` |
| Network OS | `cisco.ios.ios` |
| Cisco Collection | `cisco.ios` |
| Inventory Format | INI |

---

# 🌐 Network Devices

The inventory contains three Cisco IOS routers.

| Device | Management IP |
|---|---|
| R1 | `192.168.100.11` |
| R2 | `192.168.100.12` |
| R3 | `192.168.100.13` |

The routers use the following management network:

```text
192.168.100.0/24
```

---

# 📁 Lab Structure

```text
lab02_inventory/
│
├── commands/
│   ├── ansible_ping.txt
│   └── ios_facts_test.txt
│
├── inventory/
│   └── hosts.ini
│
├── notes/
│   └── lab02_inventory_notes.md
│
├── screenshots/
│   └── [Lab 02 screenshots]
│
├── troubleshooting_notes/
│   └── ios_facts_timeout.md
│
└── README.md
```

### Directory Purpose

| Directory | Purpose |
|---|---|
| `commands/` | Important commands and command outputs |
| `inventory/` | Ansible inventory files |
| `notes/` | Learning notes and concepts |
| `screenshots/` | Visual evidence of completed lab activities |
| `troubleshooting_notes/` | Problems, investigation and findings |
| `README.md` | Complete documentation of the lab |

---

# 📝 Step 1 — Create the Ansible Inventory

The inventory file is:

```text
inventory/hosts.ini
```

The inventory groups the three Cisco routers under `cisco_routers`.

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

---

## 🔍 Understanding the Inventory

### `[cisco_routers]`

Creates an Ansible host group containing:

```text
R1
R2
R3
```

This allows a command to be executed against all three routers using:

```text
cisco_routers
```

instead of specifying each router individually.

---

### `ansible_host`

Specifies the actual management IP address of the device.

For example:

```ini
R1 ansible_host=192.168.100.11
```

Here:

- `R1` → Ansible inventory hostname
- `192.168.100.11` → actual Cisco router management address

---

### `ansible_connection`

```ini
ansible_connection=ansible.netcommon.network_cli
```

This tells Ansible to use the network-device CLI connection framework.

Cisco IOS devices are managed through their CLI rather than through the normal Linux server connection model.

---

### `ansible_network_os`

```ini
ansible_network_os=cisco.ios.ios
```

This identifies the target platform as **Cisco IOS**.

It allows Ansible to load the appropriate Cisco IOS-specific connection and CLI plugins.

---

### `ansible_user`

```ini
ansible_user=admin
```

This specifies the username used when authenticating to the Cisco routers.

The password is supplied interactively during the lab using:

```bash
-k
```

---

# 🔌 Step 2 — Verify Ansible Connectivity

Before using Cisco-specific modules, basic Ansible connectivity was tested using the built-in Ansible ping module.

```bash
ansible -i lab02_inventory/inventory/hosts.ini cisco_routers -m ansible.builtin.ping -k
```

The three routers successfully returned:

```text
R1 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}

R2 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}

R3 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```

### ✅ Result

All three routers successfully responded.

This confirmed that:

- The inventory was correctly parsed.
- The `cisco_routers` group was correctly defined.
- Authentication was successful.
- Ansible could communicate with the inventory hosts.
- The configured network connection method was functioning for the basic connectivity test.

The command and output are preserved in:

```text
commands/ansible_ping.txt
```

---

# 📡 Step 3 — Cisco IOS Facts Collection

After successful basic connectivity verification, the Cisco IOS facts module was tested.

The command used was:

```bash
ansible -i lab02_inventory/inventory/hosts.ini cisco_routers -m cisco.ios.ios_facts -k
```

The command resulted in a timeout on all three routers:

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

The command and output are preserved in:

```text
commands/ios_facts_test.txt
```

---

# ⚠️ Troubleshooting — Cisco IOS Facts Timeout

Because basic Ansible connectivity had already succeeded, the `ios_facts` timeout was investigated separately.

The investigation covered the management-network path between:

```text
WSL
  ↓
Windows Virtual Networking
  ↓
Ethernet 2 / KM-TEST Loopback Adapter
  ↓
GNS3 Cloud
  ↓
GNS3 Switch
  ↓
Cisco Routers
```

The following areas were investigated:

- Cisco management interfaces
- Cisco ARP entries
- Router-to-router connectivity
- Windows Ethernet 2 configuration
- Windows IP forwarding
- WSL virtual interface forwarding
- Static routing
- Windows Firewall ICMP rules
- GNS3 Cloud connectivity
- Windows-to-WSL connectivity
- ARP resolution
- Management-network reachability

The detailed troubleshooting record is maintained separately in:

```text
troubleshooting_notes/ios_facts_timeout.md
```

### Important Observation

The inventory itself had already been validated through the successful Ansible ping test.

Therefore, the `ios_facts` timeout was documented as an environmental management-network connectivity issue rather than repeatedly modifying the inventory configuration.

Further environmental troubleshooting was stopped so that the remaining Ansible labs could continue.

---

# 🧠 Key Concepts Learned

## 1. Ansible Inventory

An Ansible inventory defines the devices that Ansible manages.

```text
Inventory
   │
   ├── R1
   ├── R2
   └── R3
```

---

## 2. Host Groups

Host groups allow the same operation to be executed against multiple devices.

For example:

```bash
ansible cisco_routers ...
```

can target:

```text
R1
R2
R3
```

simultaneously.

---

## 3. `network_cli`

Cisco network devices are managed differently from normal Linux servers.

Ansible uses:

```text
ansible.netcommon.network_cli
```

to establish CLI-based communication with supported network devices.

---

## 4. Network Platform Identification

Ansible needs to know which network platform it is communicating with.

For Cisco IOS:

```ini
ansible_network_os=cisco.ios.ios
```

This enables the appropriate Cisco IOS platform plugins.

---

## 5. Ansible Collections

Cisco IOS functionality is provided through the:

```text
cisco.ios
```

collection.

For example:

```text
cisco.ios.ios_facts
```

is a Cisco IOS-specific module used for gathering device information.

---

# 📚 Important Commands

### Check Ansible installation

```bash
ansible --version
```

### Test all Cisco routers

```bash
ansible -i lab02_inventory/inventory/hosts.ini cisco_routers -m ansible.builtin.ping -k
```

### Collect Cisco IOS facts

```bash
ansible -i lab02_inventory/inventory/hosts.ini cisco_routers -m cisco.ios.ios_facts -k
```

### Test a single router

```bash
ansible -i lab02_inventory/inventory/hosts.ini R1 -m cisco.ios.ios_facts -k
```

### View module documentation

```bash
ansible-doc ansible.netcommon.cli_command
```

---

# 📸 Screenshots

Screenshots documenting the successful portions of the lab are stored in:

```text
screenshots/
```

These provide visual evidence of the completed Lab 02 activities.

Command outputs are additionally preserved under:

```text
commands/
```

The project therefore maintains both:

- **Visual evidence** → screenshots
- **Reproducible command records** → command output files

---

# 🛠️ Troubleshooting Documentation

The `ios_facts` timeout has intentionally been preserved as part of the project documentation.

It is documented under:

```text
troubleshooting_notes/
└── ios_facts_timeout.md
```

This demonstrates a realistic network automation workflow:

```text
Execute
   ↓
Observe failure
   ↓
Verify assumptions
   ↓
Troubleshoot
   ↓
Document findings
   ↓
Continue development
```

A failed test is not discarded; it becomes part of the engineering record.

---

# ✅ Lab Outcome

### Completed

- [x] Ansible inventory created
- [x] Three Cisco routers added to inventory
- [x] Cisco IOS connection parameters configured
- [x] `network_cli` configured
- [x] Cisco IOS platform identified
- [x] Ansible connectivity verified
- [x] R1 connectivity verified
- [x] R2 connectivity verified
- [x] R3 connectivity verified
- [x] Cisco IOS facts collection attempted
- [x] `ios_facts` timeout documented
- [x] Troubleshooting performed
- [x] Troubleshooting notes created
- [x] Screenshots collected
- [x] Command outputs preserved

---

# 🚀 Next Lab

With the Ansible inventory established, the next lab moves from inventory and connectivity testing to executing useful commands on network devices.

```text
LAB 02
Inventory & Connectivity
        │
        ▼
LAB 03
Ad-Hoc Commands
        │
        ▼
LAB 04
First Playbook
        │
        ▼
LAB 05
Variables
        │
        ▼
LAB 06
Jinja2 Templates
        │
        ▼
LAB 07
Cisco IOS Configuration
```

### Next:

**Lab 03 — Ad-Hoc Commands**

The next lab will demonstrate how Ansible can execute commands on Cisco IOS devices without creating a playbook.

---

# 📌 Lab Status

```text
LAB 02 — INVENTORY & CISCO DEVICE CONNECTIVITY

STATUS: COMPLETED ✅
```

The lab is documented with:

```text
Inventory
   +
Commands
   +
Notes
   +
Screenshots
   +
Troubleshooting
   +
README
```