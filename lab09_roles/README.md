# 🚀 Lab 09 — Ansible Roles

> **Ansible Network Automation — Cisco IOS**

Lab 09 demonstrates how **Ansible Roles** can be used to organize network automation tasks into a **reusable, structured, and maintainable automation component**.

In this lab, a custom role named **`cisco_ios_config`** is created and applied to three Cisco IOS routers:

* 🖥️ **R1** — `192.168.40.10`
* 🖥️ **R2** — `192.168.40.11`
* 🖥️ **R3** — `192.168.40.12`

The role configures the management interface **FastEthernet0/0** with the description:

```text
Managed by Ansible Role - Lab 09
```

The lab also demonstrates:

* ✅ Role-based organization
* ✅ Reusable role components
* ✅ Cisco IOS configuration
* ✅ Configuration verification
* ✅ Ansible idempotency
* ✅ Separation of role logic from playbook logic
* ✅ Actual automation output capture

---

# 🎯 1. Objectives

The main objectives of Lab 09 are to understand and demonstrate **Ansible Roles** in a network automation environment.

By completing this lab, the following concepts are demonstrated:

* 📚 Understand the purpose of Ansible Roles.
* 🏗️ Create a structured Ansible Role.
* 🧩 Separate automation logic into role components.
* ⚙️ Define role variables using `defaults` and `vars`.
* 📝 Create role tasks for Cisco IOS configuration.
* ▶️ Apply the role through an Ansible playbook.
* 🔍 Verify the resulting router configuration.
* ♻️ Demonstrate Ansible idempotency.
* 📊 Capture actual automation output.
* 🗂️ Organize a network automation project using a scalable directory structure.

---

# 🧠 2. What Is an Ansible Role?

An **Ansible Role** is a standardized way of organizing related automation content into a reusable directory structure.

Instead of placing every task, variable, template, and handler directly inside a large playbook, a role separates these components into dedicated directories.

A simplified role can be represented as:

```text
Role
│
├── Tasks
├── Variables
├── Defaults
├── Handlers
├── Templates
└── Files
```

This separation makes automation projects easier to:

* 🔄 Reuse
* 🛠️ Maintain
* 📖 Understand
* 🧪 Test
* 📦 Extend
* 👥 Share

For network automation, roles become especially useful as the number of devices and configuration requirements increases.

---

# 🔄 3. Why Use Ansible Roles?

A small playbook may work well for a single configuration task.

However, a larger network automation project can contain:

* Device configuration
* VLAN configuration
* Interface configuration
* Routing configuration
* ACL configuration
* Security configuration
* Backup operations
* Validation tasks
* Monitoring configuration

Putting everything into a single playbook can quickly make the project difficult to maintain.

Roles provide a cleaner approach.

### Without Roles

```text
Large Playbook
│
├── Variables
├── Tasks
├── Configuration
├── Validation
├── Handlers
├── Templates
└── More Tasks...
```

### With Roles

```text
Playbook
   │
   └── Role
       ├── Tasks
       ├── Defaults
       ├── Vars
       ├── Handlers
       └── Templates
```

This allows the **playbook to describe what should be executed**, while the **role contains the reusable implementation**.

---

# 🏗️ 4. Lab 09 Role Structure

The main role created in this lab is:

```text
cisco_ios_config
```

The role follows the standard Ansible role structure:

```text
roles/
└── cisco_ios_config/
    ├── defaults/
    │   └── main.yml
    │
    ├── handlers/
    │   └── main.yml
    │
    ├── tasks/
    │   └── main.yml
    │
    ├── templates/
    │
    └── vars/
        └── main.yml
```

### 📌 Role Components

| Component           | Purpose                                |
| ------------------- | -------------------------------------- |
| `tasks/main.yml`    | Contains the main configuration tasks  |
| `defaults/main.yml` | Defines default role variables         |
| `vars/main.yml`     | Defines role-specific variables        |
| `handlers/main.yml` | Standard location for role handlers    |
| `templates/`        | Standard location for Jinja2 templates |

The role is intentionally kept simple for this lab so that the **core concept of role-based organization** remains clear.

---

# 🖥️ 5. Lab Environment

The lab uses the existing Cisco network automation topology.

### 🔹 Network

```text
Network: 192.168.40.0/24
```

### 🔹 Management Devices

| Device  | IP Address       | Platform   |
| ------- | ---------------- | ---------- |
| GNS3 VM | `192.168.40.128` | GNS3       |
| R1      | `192.168.40.10`  | Cisco 3745 |
| R2      | `192.168.40.11`  | Cisco 3745 |
| R3      | `192.168.40.12`  | Cisco 3745 |

### 🔹 Additional Environment

```text
Windows VMnet1 : 192.168.40.1
Cisco IOS      : 12.4(25d)
Management     : FastEthernet0/0
```

The existing topology and SSH configuration are reused without modification.

---

# 🌐 6. Network Topology

The Lab 09 automation environment follows this structure:

```text
                    ┌─────────────────────┐
                    │      GNS3 VM        │
                    │    192.168.40.128   │
                    └──────────┬──────────┘
                               │
                               │
                           ┌───▼───┐
                           │ Cloud │
                           └───┬───┘
                               │
                           ┌───▼───┐
                           │  SW1  │
                           └───┬───┘
                               │
              ┌────────────────┼────────────────┐
              │                │                │
          ┌───▼───┐        ┌───▼───┐        ┌───▼───┐
          │  R1   │        │  R2   │        │  R3   │
          │ .40.10│        │ .40.11│        │ .40.12│
          └───────┘        └───────┘        └───────┘
```

All three routers are managed through Ansible using the existing network automation environment.

---

# 📋 7. Inventory

The inventory file is:

```text
inventory/hosts.ini
```

The inventory contains the three Cisco routers:

```ini
[cisco_routers]
R1
R2
R3
```

The network automation connection uses:

```ini
ansible_connection=ansible.netcommon.network_cli
ansible_network_os=cisco.ios.ios
ansible_user=admin
```

The inventory was verified using:

```bash
ansible-inventory -i inventory/hosts.ini --graph
```

and:

```bash
ansible-inventory -i inventory/hosts.ini --list
```

Successful inventory verification confirmed that all three routers were correctly recognized by Ansible.

---

# 🔐 8. Cisco IOS Connectivity

The routers use the existing SSH-based network automation configuration.

Ansible communicates with the devices using:

```text
ansible.netcommon.network_cli
```

with:

```text
cisco.ios.ios
```

as the network operating system.

The legacy Cisco IOS SSH environment required compatibility handling for older cryptographic algorithms. After the existing connectivity configuration was correctly established, Ansible successfully communicated with:

```text
R1 → SUCCESS
R2 → SUCCESS
R3 → SUCCESS
```

A Cisco IOS command was successfully executed using:

```bash
ansible cisco_routers \
-i inventory/hosts.ini \
-m cisco.ios.ios_command \
-a "commands='show version'" \
-k
```

This confirmed that Ansible was able to communicate with all three Cisco routers before applying the role.

---

# ▶️ 9. Applying the Role

The role is applied through the playbook:

```text
playbooks/apply_role.yml
```

The playbook invokes the `cisco_ios_config` role against the Cisco routers.

The role execution command is:

```bash
ansible-playbook \
-i inventory/hosts.ini \
playbooks/apply_role.yml \
-k
```

### ✅ First Execution Result

The first execution successfully configured all three routers:

```text
R1 : ok=1 changed=1 unreachable=0 failed=0
R2 : ok=1 changed=1 unreachable=0 failed=0
R3 : ok=1 changed=1 unreachable=0 failed=0
```

The `changed=1` result indicates that Ansible made the required configuration change on each router.

---

# ⚙️ 10. Role Components

The `cisco_ios_config` role is divided into standard Ansible Role components.

### 📝 `tasks/main.yml`

Contains the main Cisco IOS configuration task responsible for configuring `FastEthernet0/0`.

### ⚙️ `defaults/main.yml`

Provides default values used by the role.

### 📦 `vars/main.yml`

Contains role-specific variables.

### 🔔 `handlers/main.yml`

Provides the standard location for role handlers. The directory is included as part of the role structure even though this lab does not require a handler-driven operation.

### 🧩 `templates/`

Provides the standard location for Jinja2 templates that can be used by the role when template-based configuration is required.

This organization keeps the role modular and makes it easier to extend in future network automation labs.

---

# 🔍 11. Verification

After applying the role, a separate verification playbook is used to confirm the resulting configuration.

The verification playbook is:

```text
playbooks/verify_role.yml
```

It is executed using:

```bash
ansible-playbook \
-i inventory/hosts.ini \
playbooks/verify_role.yml \
-k
```

The expected interface description is:

```text
description Managed by Ansible Role - Lab 09
```

### ✅ Verification Result

All three routers successfully passed verification:

```text
R1 : ok=2 changed=0 unreachable=0 failed=0
R2 : ok=2 changed=0 unreachable=0 failed=0
R3 : ok=2 changed=0 unreachable=0 failed=0
```

The verification playbook is **read-only**. Therefore, `changed=0` is expected because it checks the configuration without modifying the routers.

---

# ♻️ 12. Idempotency

One of the most important characteristics of Ansible is **idempotency**.

After the first execution created the required configuration, the same role was executed again.

```bash
ansible-playbook \
-i inventory/hosts.ini \
playbooks/apply_role.yml \
-k
```

### ✅ Second Execution Result

```text
R1 : ok=1 changed=0 unreachable=0 failed=0
R2 : ok=1 changed=0 unreachable=0 failed=0
R3 : ok=1 changed=0 unreachable=0 failed=0
```

No additional configuration changes were required.

### 🧠 Idempotency Flow

```text
First Execution
      ↓
Configuration absent
      ↓
Role applies configuration
      ↓
changed=1
      ↓
Configuration exists
      ↓
Second Execution
      ↓
No change required
      ↓
changed=0
```

This confirms that the role behaves **idempotently**.

---

# 🔄 13. Role Workflow

The complete Lab 09 workflow can be summarized as:

```text
Inventory
    ↓
Cisco IOS Connectivity
    ↓
Apply Role
    ↓
Configure FastEthernet0/0
    ↓
Verify Configuration
    ↓
Run Role Again
    ↓
changed=0
    ↓
Idempotency Confirmed
```

This demonstrates the separation between **inventory**, **playbooks**, **role logic**, and **verification**.

---

# 📊 14. Results

| Test                  |          R1 |          R2 |          R3 | Result                   |
| --------------------- | ----------: | ----------: | ----------: | ------------------------ |
| First role execution  | `changed=1` | `changed=1` | `changed=1` | ✅ Configuration applied  |
| Verification          | `changed=0` | `changed=0` | `changed=0` | ✅ Configuration verified |
| Second role execution | `changed=0` | `changed=0` | `changed=0` | ✅ Idempotency confirmed  |

### 🎯 Configuration Result

The following description was successfully configured on `FastEthernet0/0` of all three routers:

```text
Managed by Ansible Role - Lab 09
```

All executions completed with:

```text
unreachable=0
failed=0
```

---

# 🧠 15. Important Concepts Learned

Lab 09 reinforces the following Ansible concepts:

* 🏗️ **Roles** — organize reusable automation into a standard structure.
* 🧩 **Role Tasks** — contain the actions performed by the role.
* ⚙️ **Defaults and Variables** — separate configuration data from automation logic.
* 🔗 **Playbook and Role Integration** — a playbook can invoke reusable role logic.
* 🌐 **Network Modules** — Cisco IOS devices can be managed using Ansible network modules.
* 🔍 **Verification** — configuration should be validated after deployment.
* ♻️ **Idempotency** — repeated execution should not produce unnecessary changes.
* 📦 **Project Organization** — structured automation is easier to maintain and extend.

---

# 📁 16. Directory Structure

The completed Lab 09 directory is organized as follows:

```text
lab09_roles/
├── README.md
├── ansible.cfg
│
├── expected_outputs/
│   └── role_expected.md
│
├── inventory/
│   └── hosts.ini
│
├── outputs/
│   ├── role_actual.txt
│   └── role_verification_actual.txt
│
├── playbooks/
│   ├── apply_role.yml
│   └── verify_role.yml
│
├── roles/
│   └── cisco_ios_config/
│       ├── defaults/
│       │   └── main.yml
│       ├── handlers/
│       │   └── main.yml
│       ├── tasks/
│       │   └── main.yml
│       ├── templates/
│       └── vars/
│           └── main.yml
│
└── screenshots/
```

The `screenshots/` directory contains the complete lab evidence. Screenshots are intentionally **not embedded in this individual lab README**. Selected representative screenshots can later be included in the root project README.

---

# 📝 17. Learning Summary

Lab 09 extends the earlier Ansible network automation work by introducing **Roles** as a scalable method of organizing automation.

Instead of keeping configuration logic directly inside a playbook, the `cisco_ios_config` role provides a dedicated structure containing tasks, variables, defaults, handlers, and templates.

The role was successfully applied to three Cisco IOS routers, the resulting configuration was verified, and repeated execution demonstrated idempotency.

This provides a foundation for building more complex and reusable network automation components in future labs.

---

# 🎯 18. Result

> ✅ **LAB 09 COMPLETED SUCCESSFULLY**

The **`cisco_ios_config`** Ansible Role successfully configured `FastEthernet0/0` on R1, R2, and R3 with the required interface description.

The configuration was verified successfully, and a second role execution returned `changed=0` on all routers, demonstrating **Ansible idempotency**.

### 🏆 Final Status

```text
Ansible Role       : cisco_ios_config
Devices             : R1, R2, R3
Interface           : FastEthernet0/0
Configuration       : Successful
Verification        : Successful
First Execution     : changed=1
Second Execution    : changed=0
Idempotency         : Demonstrated
Unreachable Devices : 0
Failed Tasks        : 0
```

---

# ➡️ Next Lab

With **Lab 09 — Ansible Roles** completed, the project can proceed to **Lab 10**.

The next lab will build upon the structured automation concepts developed so far and continue expanding the Ansible network automation framework.

---

> 🚀 **Progress:** Lab 09 completes the transition from individual Ansible playbooks toward **reusable, modular, and scalable network automation using Roles**.
