# 📘 Lab 05 — Ansible Variables

## 🎯 Objective

The objective of **Lab 05** is to understand and demonstrate the use of **variables in Ansible network automation**.

This lab builds on the first Ansible playbook created in **Lab 04** and introduces:

* 🧠 **Playbook variables**
* 🔄 **Variable interpolation** using `{{ variable_name }}`
* 🖥️ Ansible's built-in `inventory_hostname` variable
* 🌐 The inventory-defined `ansible_host` variable
* 🔧 Using variables while automating **Cisco IOS devices**
* 📡 Executing a real Cisco IOS command through `ansible.netcommon.network_cli`

The playbook was tested against **all three Cisco 3745 routers** in the GNS3 topology.

---

## 🏗️ Project Information

| Item                 | Details                         |
| -------------------- | ------------------------------- |
| **Project**          | Ansible Network Automation      |
| **Lab**              | Lab 05 — Variables              |
| **Automation Host**  | Ubuntu WSL                      |
| **Automation Tool**  | Ansible                         |
| **Network Platform** | Cisco IOS                       |
| **Connection**       | `ansible.netcommon.network_cli` |
| **Cisco Collection** | `cisco.ios`                     |
| **Routers Tested**   | R1, R2, R3                      |
| **Network**          | `192.168.40.0/24`               |

---

## 🌐 Network Topology

```text
                    GNS3 VM
                  192.168.40.128
                       │
                     Cloud
                       │
                      SW1
                 ┌─────┼─────┐
                 │     │     │
                R1    R2    R3
              .10    .11   .12
```

### 📍 IP Addressing

| Device         | IP Address       |
| -------------- | ---------------- |
| Windows VMnet1 | `192.168.40.1`   |
| GNS3 VM        | `192.168.40.128` |
| R1             | `192.168.40.10`  |
| R2             | `192.168.40.11`  |
| R3             | `192.168.40.12`  |

> ⚠️ **The network topology and IP addressing were not changed during this lab.**

---

## 🖥️ Cisco Router Details

All three routers use:

* **Platform:** Cisco 3745
* **IOS:** 12.4(25d)
* **IOS Image:** `C3745-ADVENTERPRISEK9-M`
* **Management Interface:** FastEthernet0/0
* **Management Network:** `192.168.40.0/24`

### 🔐 SSH

The routers were already configured for SSH before this lab.

* **Username:** `admin`
* **Privilege:** 15
* **SSH Version:** 2
* **Domain:** `lab.local`
* **RSA Key:** 2048-bit
* **VTY:** SSH with local authentication

The SSH password was **not stored in the inventory**. Authentication was performed interactively using the Ansible `-k` option.

---

## 📁 Directory Structure

```text
lab05_variables/
├── README.md
├── inventory/
│   └── hosts.ini
├── playbooks/
│   └── variables_demo.yml
├── outputs/
│   └── variables_demo_actual.txt
├── expected_outputs/
│   └── variables_demo_expected.md
└── screenshots/
```

### 📌 File Purpose

| File/Directory                                | Purpose                                            |
| --------------------------------------------- | -------------------------------------------------- |
| `README.md`                                   | 📘 Lab documentation                               |
| `inventory/hosts.ini`                         | 🌐 Cisco router inventory and connection variables |
| `playbooks/variables_demo.yml`                | ⚙️ Variables demonstration playbook                |
| `outputs/variables_demo_actual.txt`           | ✅ Actual output captured from Ansible              |
| `expected_outputs/variables_demo_expected.md` | 📋 Expected lab results                            |
| `screenshots/`                                | 📸 Visual evidence of the completed lab            |

---

## ⚙️ Inventory Configuration

The inventory defines the three Cisco routers:

```ini
[cisco_routers]
R1 ansible_host=192.168.40.10
R2 ansible_host=192.168.40.11
R3 ansible_host=192.168.40.12

[cisco_routers:vars]
ansible_connection=ansible.netcommon.network_cli
ansible_network_os=cisco.ios.ios
ansible_user=admin
```

### 🔎 Important Variables

| Variable             | Purpose                                     |
| -------------------- | ------------------------------------------- |
| `ansible_host`       | 🌐 Defines the management IP address        |
| `ansible_connection` | 🔌 Selects Ansible's network CLI connection |
| `ansible_network_os` | 🖥️ Identifies Cisco IOS                    |
| `ansible_user`       | 👤 Defines the SSH username                 |

The SSH password is intentionally **not stored in the inventory**.

---

## 🧠 Variables Demonstrated

The playbook defines variables using the `vars:` section:

```yaml
vars:
  lab_name: "Lab 05 - Variables"
  project_name: "Ansible Network Automation"
  management_network: "192.168.40.0/24"
```

These variables are referenced using Ansible's Jinja2 expression syntax:

```text
{{ lab_name }}
{{ project_name }}
{{ management_network }}
```

The playbook also uses Ansible-provided variables:

```text
{{ inventory_hostname }}
{{ ansible_host }}
```

For example:

```text
R1 → inventory_hostname = R1
R1 → ansible_host = 192.168.40.10
```

while:

```text
R2 → inventory_hostname = R2
R2 → ansible_host = 192.168.40.11
```

and:

```text
R3 → inventory_hostname = R3
R3 → ansible_host = 192.168.40.12
```

This demonstrates how **one playbook can operate across multiple devices without hard-coding each router's identity into the tasks**.

---

## ▶️ Execution

The playbook was first checked for YAML and Ansible syntax:

```bash
ansible-playbook --syntax-check \
-i inventory/hosts.ini \
playbooks/variables_demo.yml
```

The syntax check completed successfully.

The actual lab was then executed using:

```bash
ansible-playbook -k \
-i inventory/hosts.ini \
playbooks/variables_demo.yml
```

The `-k` option prompted for the SSH password without storing the password in the project files.

---

## 🔧 Cisco IOS Task

The playbook retrieves the configured Cisco hostname using:

```yaml
- name: Retrieve Cisco hostname
  cisco.ios.ios_command:
    commands:
      - show running-config | include hostname
  register: hostname_output
```

The resulting output is then displayed using:

```yaml
- name: Display Cisco hostname
  ansible.builtin.debug:
    var: hostname_output.stdout_lines
```

---

## ✅ Actual Results

The playbook successfully executed against **all three Cisco routers**.

### R1

```text
Router: R1
Management IP: 192.168.40.10
hostname R1
```

### R2

```text
Router: R2
Management IP: 192.168.40.11
hostname R2
```

### R3

```text
Router: R3
Management IP: 192.168.40.12
hostname R3
```

### 🎉 Final Play Recap

```text
R1 : ok=4    changed=0    unreachable=0    failed=0
R2 : ok=4    changed=0    unreachable=0    failed=0
R3 : ok=4    changed=0    unreachable=0    failed=0
```

### 🟢 Result

**Lab 05 execution was successful.**

All three routers:

* ✅ Were reachable
* ✅ Accepted SSH authentication
* ✅ Processed the Ansible playbook
* ✅ Received the Cisco IOS command
* ✅ Returned the expected hostname
* ✅ Completed with `failed=0`
* ✅ Completed with `unreachable=0`

---

## ⚠️ Ansible-PylibSSH Warning

During execution, Ansible displayed:

```text
[WARNING]: ansible-pylibssh not installed, falling back to paramiko
```

This warning did **not** cause a failure.

Ansible successfully used **Paramiko as the fallback SSH transport**, and all three routers completed successfully.

Therefore:

> **No GNS3 or Cisco router configuration changes were required.**

---

## 📊 Validation Summary

| Validation                 | Result           |
| -------------------------- | ---------------- |
| Inventory syntax           | ✅ Passed         |
| Inventory device discovery | ✅ Passed         |
| Playbook syntax check      | ✅ Passed         |
| R1 connectivity            | ✅ Passed         |
| R2 connectivity            | ✅ Passed         |
| R3 connectivity            | ✅ Passed         |
| Playbook variables         | ✅ Passed         |
| Inventory variables        | ✅ Passed         |
| Cisco hostname retrieval   | ✅ Passed         |
| R1 tasks                   | ✅ 4/4 successful |
| R2 tasks                   | ✅ 4/4 successful |
| R3 tasks                   | ✅ 4/4 successful |
| Failed tasks               | ✅ 0              |
| Unreachable routers        | ✅ 0              |

---

## 📸 Evidence

Screenshots for the completed lab are stored in:

```text
screenshots/
```

They provide visual evidence of:

* 🌐 Network topology
* 📁 Lab directory structure
* 📝 Inventory configuration
* ⚙️ Variables playbook
* ▶️ Playbook execution
* ✅ Successful results
* 📊 Final Ansible recap

Screenshots are captured **after successful lab execution**.

---

## 📚 Key Learning Outcomes

After completing this lab, the following concepts were demonstrated:

1. 🧠 **Defining variables in an Ansible playbook**
2. 🔄 **Using variables with Jinja2 syntax**
3. 🌐 **Using inventory-defined variables**
4. 🖥️ Understanding `inventory_hostname`
5. 📡 Understanding `ansible_host`
6. 🔧 Using variables with Cisco IOS automation
7. 📋 Registering command output with `register`
8. 🔍 Displaying registered output using `debug`
9. 🔐 Using interactive SSH password authentication with `-k`
10. 🚀 Running a single playbook against multiple Cisco routers

---

## 🔗 Relationship to Previous and Future Labs

### Previous

**Lab 04 — First Ansible Playbook**

Introduced the basic structure of an Ansible network playbook and successfully executed a Cisco IOS command against R1, R2 and R3.

### Current

**Lab 05 — Variables**

Introduces variables and demonstrates how a single playbook can dynamically use device-specific information.

### Next

**Lab 06 — Jinja2 Templates**

Will build on the variable concepts introduced here and use **Jinja2 templating** for dynamic network configuration generation.

---

## 🏁 Lab Status

**Lab 05 — Variables: ✅ COMPLETE**

The lab was successfully tested against the complete three-router Cisco IOS topology.

**R1 ✅ | R2 ✅ | R3 ✅**

**No topology changes were required.**
