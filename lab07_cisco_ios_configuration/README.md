# Lab 07 — Cisco IOS Configuration

## 🎯 Objective

This lab demonstrates how to use **Ansible to configure Cisco IOS devices** using the `cisco.ios.ios_config` module.

The lab builds on the concepts introduced in:

* Lab 04 — First Ansible Playbook
* Lab 05 — Variables
* Lab 06 — Jinja2 Templates

The main focus is direct Cisco IOS configuration management, verification, and **Ansible idempotency**.

---

## 🧠 Concepts Demonstrated

* Cisco IOS configuration with Ansible
* `cisco.ios.ios_config`
* `cisco.ios.ios_command`
* `parents`
* `lines`
* Interface configuration
* Configuration management
* Before/after validation
* Idempotency
* Real configuration verification
* Expected vs actual outputs
* Multi-device configuration
* Ansible `network_cli` connection

---

## 🌐 Network Topology

```text
                    GNS3 VM
                 192.168.40.128
                       |
                     Cloud
                       |
                      SW1
                  /     |     \
                 /      |      \
               R1       R2       R3
        192.168.40.10  .11      .12
```

### Network

```text
192.168.40.0/24
```

### Devices

| Device | Management IP    | IOS                 |
| ------ | ---------------- | ------------------- |
| R1     | 192.168.40.10/24 | Cisco IOS 12.4(25d) |
| R2     | 192.168.40.11/24 | Cisco IOS 12.4(25d) |
| R3     | 192.168.40.12/24 | Cisco IOS 12.4(25d) |

Management interface:

```text
FastEthernet0/0
```

---

## 🔐 SSH Configuration

All routers use:

```text
Username: admin
Privilege: 15
Domain: lab.local
SSH version: 2
RSA keys: 2048-bit
```

VTY configuration:

```text
line vty 0 4
 login local
 transport input ssh
```

The SSH password is intentionally not stored in the inventory.

Playbooks requiring authentication are executed with:

```bash
ansible-playbook -k
```

---

## ⚙️ Ansible Configuration

Inventory connection:

```ini
ansible_connection=ansible.netcommon.network_cli
ansible_network_os=cisco.ios.ios
ansible_user=admin
```

Ansible:

```text
ansible [core 2.16.3]
```

Python:

```text
3.12.3
```

Jinja:

```text
3.1.2
```

---

## 📁 Directory Structure

```text
lab07_cisco_ios_configuration/
├── README.md
├── expected_outputs/
│   ├── baseline_config_expected.md
│   ├── apply_ios_config_expected.md
│   ├── verify_ios_config_expected.md
│   └── idempotency_expected.md
├── inventory/
│   └── hosts.ini
├── outputs/
│   ├── baseline_config_actual.txt
│   ├── apply_ios_config_actual.txt
│   └── idempotency_actual.txt
├── playbooks/
│   ├── baseline_config.yml
│   ├── apply_ios_config.yml
│   └── verify_ios_config.yml
└── screenshots/
```

---

# 1. Baseline Configuration

Before modifying the routers, the existing FastEthernet0/0 configuration was retrieved using:

```text
show running-config | section interface FastEthernet0/0
```

The baseline confirmed that the three routers had their expected management IP addresses.

### R1

```text
interface FastEthernet0/0
 ip address 192.168.40.10 255.255.255.0
 duplex auto
 speed auto
```

### R2

```text
interface FastEthernet0/0
 ip address 192.168.40.11 255.255.255.0
 duplex auto
 speed auto
```

### R3

```text
interface FastEthernet0/0
 ip address 192.168.40.12 255.255.255.0
 duplex auto
 speed auto
```

Baseline execution completed successfully:

```text
R1: ok=2 changed=0 unreachable=0 failed=0
R2: ok=2 changed=0 unreachable=0 failed=0
R3: ok=2 changed=0 unreachable=0 failed=0
```

Actual baseline output:

```text
outputs/baseline_config_actual.txt
```

---

# 2. Cisco IOS Configuration with Ansible

The `cisco.ios.ios_config` module was used to configure FastEthernet0/0.

Playbook:

```text
playbooks/apply_ios_config.yml
```

Configuration applied:

```text
interface FastEthernet0/0
 description Managed by Ansible - Lab 07
```

The `parents` parameter places Ansible inside the interface configuration context:

```yaml
parents:
  - interface FastEthernet0/0
```

The desired configuration line is supplied using:

```yaml
lines:
  - description Managed by Ansible - Lab 07
```

---

# 3. Configuration Application Result

The first execution successfully changed all three routers.

```text
R1: ok=1 changed=1 unreachable=0 failed=0
R2: ok=1 changed=1 unreachable=0 failed=0
R3: ok=1 changed=1 unreachable=0 failed=0
```

This confirms that Ansible successfully applied the configuration to:

* R1
* R2
* R3

Actual execution output:

```text
outputs/apply_ios_config_actual.txt
```

---

# 4. Verification

The resulting configuration was verified using:

```text
show running-config | section interface FastEthernet0/0
```

The resulting configuration was:

### R1

```text
interface FastEthernet0/0
 description Managed by Ansible - Lab 07
 ip address 192.168.40.10 255.255.255.0
 duplex auto
 speed auto
```

### R2

```text
interface FastEthernet0/0
 description Managed by Ansible - Lab 07
 ip address 192.168.40.11 255.255.255.0
 duplex auto
 speed auto
```

### R3

```text
interface FastEthernet0/0
 description Managed by Ansible - Lab 07
 ip address 192.168.40.12 255.255.255.0
 duplex auto
 speed auto
```

Verification completed successfully:

```text
R1: ok=2 changed=0 unreachable=0 failed=0
R2: ok=2 changed=0 unreachable=0 failed=0
R3: ok=2 changed=0 unreachable=0 failed=0
```

---

# 5. Idempotency Demonstration

The configuration playbook was executed a second time after the desired configuration was already present.

The second execution produced:

```text
R1: ok=1 changed=0 unreachable=0 failed=0
R2: ok=1 changed=0 unreachable=0 failed=0
R3: ok=1 changed=0 unreachable=0 failed=0
```

This demonstrates **Ansible idempotency**.

Ansible recognized that the desired configuration already existed and therefore did not make unnecessary changes.

Actual idempotency output:

```text
outputs/idempotency_actual.txt
```

---

# ⚠️ Ansible-pylibssh Warning

During execution, Ansible displayed:

```text
[WARNING]: ansible-pylibssh not installed, falling back to paramiko
```

This is a warning and not a failure.

Ansible successfully established the Cisco network connection using Paramiko, and all three routers completed successfully.

No GNS3, router, IP addressing, or SSH configuration changes were required.

---

# 📊 Lab Results

| Test                     | R1          | R2          | R3          |
| ------------------------ | ----------- | ----------- | ----------- |
| Baseline                 | ✅ Success   | ✅ Success   | ✅ Success   |
| Configuration            | ✅ Changed   | ✅ Changed   | ✅ Changed   |
| Verification             | ✅ Success   | ✅ Success   | ✅ Success   |
| Second configuration run | ✅ No change | ✅ No change | ✅ No change |

---

# 📂 Output Evidence

### Actual Outputs

* `outputs/baseline_config_actual.txt`
* `outputs/apply_ios_config_actual.txt`
* `outputs/idempotency_actual.txt`

### Expected Outputs

* `expected_outputs/baseline_config_expected.md`
* `expected_outputs/apply_ios_config_expected.md`
* `expected_outputs/verify_ios_config_expected.md`
* `expected_outputs/idempotency_expected.md`

Actual outputs represent real Ansible executions and are not manually fabricated.

---

# 📸 Screenshots

Screenshots for this lab are stored in:

```text
screenshots/
```

They provide visual evidence of the Lab 07 workflow, including:

* Lab directory structure
* Inventory configuration
* Baseline playbook
* IOS configuration playbook
* Successful configuration
* Verification
* Idempotency
* Final project structure

---

# 🧪 Validation Workflow

The lab followed this workflow:

```text
Baseline
   ↓
Apply IOS Configuration
   ↓
Verify Configuration
   ↓
Run Configuration Again
   ↓
changed=0
   ↓
Idempotency Confirmed
```

This demonstrates the basic Ansible configuration-management lifecycle.

---

# 🎓 Learning Outcomes

After completing this lab, the following concepts were demonstrated:

1. Using Ansible to configure Cisco IOS devices.
2. Using `cisco.ios.ios_config`.
3. Using `parents` to enter IOS configuration contexts.
4. Applying configuration to multiple network devices.
5. Separating configuration from verification.
6. Validating configuration using `cisco.ios.ios_command`.
7. Capturing real execution results.
8. Comparing expected and actual results.
9. Understanding Ansible idempotency.
10. Managing network configuration without manually entering IOS configuration commands on each router.

---

# 🏁 Lab Status

**LAB 07 — COMPLETE**

Cisco IOS configuration was successfully applied and verified on R1, R2 and R3.

Idempotency was successfully demonstrated with:

```text
changed=0
```

for all three routers on the second configuration run.

---

## 🔗 Lab Navigation

**Previous Lab:** Lab 06 — Jinja2 Templates

**Current Lab:** Lab 07 — Cisco IOS Configuration

**Next Lab:** Lab 08 — Error Handling
