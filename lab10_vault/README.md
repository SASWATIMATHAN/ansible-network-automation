# 🔐 Lab 10 — Ansible Vault

> **Secure Credential Management for Cisco IOS Network Automation**

---

## 🎯 1. Lab Objective

The objective of **Lab 10** is to demonstrate how **Ansible Vault** can be used to securely store sensitive Cisco IOS authentication credentials while continuing to use Ansible for network configuration and verification.

In the previous labs, credentials were supplied interactively. In this lab, the Cisco authentication password is stored inside an **encrypted Ansible Vault file**.

The lab demonstrates:

* 🔐 Secure storage of `ansible_password`
* 🔑 Vault-based credential decryption
* 🌐 Cisco IOS authentication using `network_cli`
* ⚙️ Interface configuration using `cisco.ios.ios_interfaces`
* 🔎 Configuration verification using `cisco.ios.ios_command`
* ♻️ Ansible idempotency
* 🛡️ Protection of sensitive credentials from plaintext storage

---

## 🧠 2. What is Ansible Vault?

**Ansible Vault** is a feature of Ansible that allows sensitive information such as passwords, API keys, certificates, and other secrets to be stored in encrypted files.

Instead of storing a Cisco password directly in the inventory, the credential is stored in an encrypted Vault file.

### Without Vault ❌

```yaml
ansible_password: "<password>"
```

The credential would be visible as plaintext.

### With Ansible Vault 🔐

```text
$ANSIBLE_VAULT;1.1;AES256
...
```

The sensitive data is encrypted and can only be accessed when the correct Vault password is supplied.

---

## 🎯 3. Lab Learning Outcomes

After completing this lab, the following concepts are demonstrated:

| Concept                    | Demonstrated |
| -------------------------- | ------------ |
| Ansible Vault              | ✅            |
| Encrypted credentials      | ✅            |
| Vault password prompt      | ✅            |
| Cisco IOS authentication   | ✅            |
| `network_cli`              | ✅            |
| `vars_files`               | ✅            |
| Interface configuration    | ✅            |
| Configuration verification | ✅            |
| Idempotency                | ✅            |
| Secure credential handling | ✅            |

---

## 🏗️ 4. Lab Topology

The existing Cisco IOS topology is used without modification.

```text
                    GNS3 Network
                 192.168.40.0/24
                         │
          ┌──────────────┼──────────────┐
          │              │              │
       R1 .10          R2 .11         R3 .12
     Cisco 3745      Cisco 3745     Cisco 3745
```

### Network Details

| Device | IP Address      | Platform   | IOS       |
| ------ | --------------- | ---------- | --------- |
| R1     | `192.168.40.10` | Cisco 3745 | 12.4(25d) |
| R2     | `192.168.40.11` | Cisco 3745 | 12.4(25d) |
| R3     | `192.168.40.12` | Cisco 3745 | 12.4(25d) |

Management interface:

```text
FastEthernet0/0
```

---

## 📁 5. Lab Directory Structure

```text
lab10_vault/
├── README.md
├── ansible.cfg
├── expected_outputs/
│   └── vault_expected.md
├── group_vars/
│   └── cisco_routers/
│       └── vault.yml
├── inventory/
│   └── hosts.ini
├── outputs/
│   ├── vault_actual.txt
│   └── vault_verification_actual.txt
├── playbooks/
│   ├── vault_demo.yml
│   └── verify_vault.yml
└── screenshots/
```

### 📌 Directory Purpose

| Directory/File      | Purpose                           |
| ------------------- | --------------------------------- |
| `inventory/`        | Cisco router inventory            |
| `group_vars/`       | Group-specific variables          |
| `vault.yml`         | 🔐 Encrypted credentials          |
| `playbooks/`        | Automation playbooks              |
| `expected_outputs/` | Expected execution results        |
| `outputs/`          | Actual captured execution results |
| `screenshots/`      | Lab evidence                      |
| `ansible.cfg`       | Ansible connection configuration  |

---

## 🔐 6. Ansible Vault Configuration

The sensitive Cisco authentication variable is stored in:

```text
group_vars/cisco_routers/vault.yml
```

The file is encrypted using Ansible Vault.

A valid encrypted file begins with:

```text
$ANSIBLE_VAULT;1.1;AES256
```

The Vault file contains the protected:

```yaml
ansible_password
```

⚠️ **The actual password is intentionally not documented in this README.**

---

## 🌐 7. Inventory Configuration

The inventory contains the Cisco routers and their connection parameters.

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

### 🔒 Important

The Cisco password is **not stored in the inventory**.

Instead, it is supplied through the encrypted Vault file.

---

## ⚙️ 8. Ansible Configuration

The lab uses the following connection configuration:

```ini
[defaults]

[persistent_connection]
ssh_type = paramiko

[paramiko_connection]
look_for_keys = false
host_key_checking = false
```

This configuration is retained because the Cisco IOS 12.4 SSH implementation used in the lab is a legacy SSH environment.

---

## 🔑 9. Loading the Vault File

The playbooks explicitly load the encrypted Vault file using:

```yaml
vars_files:
  - ../group_vars/cisco_routers/vault.yml
```

This ensures that the Vault-protected `ansible_password` variable is available to the playbook.

The Vault password is requested interactively using:

```bash
--ask-vault-pass
```

---

## 🛠️ 10. Configuration Playbook

The main playbook is:

```text
playbooks/vault_demo.yml
```

Its purpose is to configure `FastEthernet0/0` on all Cisco routers.

The desired interface configuration is:

```yaml
config:
  - name: "FastEthernet0/0"
    description: "Managed by Ansible Vault - Lab 10"
    enabled: true
```

The playbook uses:

```text
cisco.ios.ios_interfaces
```

with:

```text
state: merged
```

---

## 🧪 11. Syntax Validation

Before execution, both playbooks are checked for syntax errors.

### `vault_demo.yml`

```bash
ansible-playbook \
  -i inventory/hosts.ini \
  playbooks/vault_demo.yml \
  --syntax-check \
  --ask-vault-pass
```

Expected:

```text
playbook: playbooks/vault_demo.yml
```

### `verify_vault.yml`

```bash
ansible-playbook \
  -i inventory/hosts.ini \
  playbooks/verify_vault.yml \
  --syntax-check \
  --ask-vault-pass
```

Expected:

```text
playbook: playbooks/verify_vault.yml
```

✅ Both syntax checks passed successfully.

---

## 🚀 12. Vault-Based Configuration Execution

The configuration is executed with:

```bash
ansible-playbook \
  -i inventory/hosts.ini \
  playbooks/vault_demo.yml \
  --ask-vault-pass
```

The Vault password is entered interactively.

### 🔐 Important Difference

The command does **not** use:

```text
-k
```

Instead, the Cisco password is obtained from the encrypted Vault file.

This is the main security improvement demonstrated by this lab.

---

## ♻️ 13. Idempotency Verification

The playbook was executed again after the desired configuration was already present.

The final execution produced:

```text
R1 : ok=1 changed=0 unreachable=0 failed=0
R2 : ok=1 changed=0 unreachable=0 failed=0
R3 : ok=1 changed=0 unreachable=0 failed=0
```

### ✅ What `changed=0` Proves

This confirms that:

* 🔐 Vault authentication succeeded
* 🔓 The encrypted Vault file was decrypted
* 🔑 `ansible_password` was successfully loaded
* 🌐 Cisco authentication succeeded
* ⚙️ The desired configuration already existed
* ♻️ No unnecessary configuration changes were made

---

## 🔎 14. Configuration Verification

The verification playbook is:

```text
playbooks/verify_vault.yml
```

It uses:

```text
cisco.ios.ios_command
```

to execute:

```text
show running-config interface FastEthernet0/0
```

on all routers.

Execution:

```bash
ansible-playbook \
  -i inventory/hosts.ini \
  playbooks/verify_vault.yml \
  --ask-vault-pass
```

---

## 📋 15. Verified Configuration

### R1

```text
interface FastEthernet0/0
 description Managed by Ansible Vault - Lab 10
 ip address 192.168.40.10 255.255.255.0
 duplex auto
 speed auto
end
```

### R2

```text
interface FastEthernet0/0
 description Managed by Ansible Vault - Lab 10
 ip address 192.168.40.11 255.255.255.0
 duplex auto
 speed auto
end
```

### R3

```text
interface FastEthernet0/0
 description Managed by Ansible Vault - Lab 10
 ip address 192.168.40.12 255.255.255.0
 duplex auto
 speed auto
end
```

---

## 📊 16. Final Verification Result

The verification completed successfully on all three routers.

```text
R1 : ok=2 changed=0 unreachable=0 failed=0
R2 : ok=2 changed=0 unreachable=0 failed=0
R3 : ok=2 changed=0 unreachable=0 failed=0
```

### 🟢 Final Status

| Validation           | Result |
| -------------------- | ------ |
| Vault encryption     | ✅ PASS |
| Vault decryption     | ✅ PASS |
| Credential loading   | ✅ PASS |
| Cisco authentication | ✅ PASS |
| R1 configuration     | ✅ PASS |
| R2 configuration     | ✅ PASS |
| R3 configuration     | ✅ PASS |
| Idempotency          | ✅ PASS |
| Verification         | ✅ PASS |
| Failed hosts         | ❌ 0    |
| Unreachable hosts    | ❌ 0    |

---

## 📄 17. Captured Outputs

Actual execution results are preserved in:

```text
outputs/vault_actual.txt
```

and:

```text
outputs/vault_verification_actual.txt
```

### `vault_actual.txt`

Contains the final idempotent configuration execution:

```text
changed=0
```

for R1, R2, and R3.

### `vault_verification_actual.txt`

Contains the complete verification output and the resulting interface configuration from all three routers.

---

## 🧾 18. Expected Output Documentation

Expected results are documented separately in:

```text
expected_outputs/vault_expected.md
```

This file describes:

* 🎯 Lab objectives
* 🔐 Vault expectations
* 🧪 Syntax-check results
* 🚀 Initial configuration expectations
* ♻️ Idempotent execution
* 🔎 Verification output
* 🛡️ Security requirements

---

## 🛡️ 19. Security Considerations

The following security practices are maintained throughout the lab:

* 🔐 `vault.yml` remains encrypted.
* 🚫 Cisco credentials are not stored in plaintext inventory.
* 🚫 The actual password is not documented in this README.
* 🚫 The Vault password is not documented anywhere in the project.
* 🚫 Plaintext credentials must not be committed to Git.
* 🚫 Screenshots must not expose sensitive credentials.
* 🔑 Vault decryption requires the Vault password at execution time.

> **Never commit plaintext credentials to a Git repository.**

---

## 🧠 20. Key Learning

### Before Ansible Vault

Credentials may need to be supplied interactively:

```text
-k
```

### With Ansible Vault

The credential is securely stored in an encrypted file:

```text
group_vars/cisco_routers/vault.yml
```

and the Vault is unlocked at runtime:

```text
--ask-vault-pass
```

This separates **credential storage** from the automation logic.

---

## 🔄 21. Lab Workflow

```text
        ┌──────────────────────────┐
        │ Encrypted vault.yml      │
        │ ansible_password         │
        └────────────┬─────────────┘
                     │
                     │ --ask-vault-pass
                     ▼
        ┌──────────────────────────┐
        │ Ansible Playbook         │
        │ vars_files: vault.yml    │
        └────────────┬─────────────┘
                     │
                     ▼
        ┌──────────────────────────┐
        │ network_cli              │
        │ Cisco IOS authentication │
        └────────────┬─────────────┘
                     │
             ┌───────┼───────┐
             ▼       ▼       ▼
            R1      R2      R3
             │       │       │
             └───────┼───────┘
                     ▼
        ┌──────────────────────────┐
        │ FastEthernet0/0          │
        │ Lab 10 description       │
        └──────────────────────────┘
```

---

## 🏁 22. Final Outcome

🎉 **Lab 10 — Ansible Vault is successfully completed.**

The lab successfully demonstrates secure credential management for Cisco IOS network automation using Ansible Vault.

### Final Result

```text
🔐 Vault Encryption       → PASS
🔓 Vault Decryption       → PASS
🔑 Credential Loading     → PASS
🌐 Cisco Authentication   → PASS
⚙️ Configuration          → PASS
♻️ Idempotency            → PASS
🔎 Verification           → PASS
🛡️ Security               → PASS
```

The final configuration execution produced:

```text
R1 → changed=0
R2 → changed=0
R3 → changed=0
```

Therefore, the desired configuration was already present and Ansible completed the operation without making unnecessary changes.

---

## 🚀 Next Lab

➡️ **Lab 11 — NAPALM**

The next lab will move from Ansible Vault and credential management toward **multi-vendor network automation using NAPALM**.
