# 🔐 Lab 10 — Ansible Vault Expected Output

## 🎯 1. Lab Objective

This expected-output document defines the results that should be observed when using **Ansible Vault** to securely store the Cisco IOS authentication password.

The lab demonstrates:

* 🔐 Secure storage of `ansible_password` using Ansible Vault
* 🔑 Successful Vault decryption using `--ask-vault-pass`
* 🌐 Cisco IOS authentication through `network_cli`
* ⚙️ Configuration of `FastEthernet0/0`
* ♻️ Ansible idempotency
* 🔎 Verification of the applied configuration
* 🛡️ Prevention of plaintext credential exposure

---

## 🔐 2. Vault-Protected Variable

The following variable must be stored inside the encrypted Vault file:

```yaml
ansible_password: "<vault-protected-password>"
```

⚠️ **The actual password must never appear in this document.**

The Vault file must remain encrypted and begin with:

```text
$ANSIBLE_VAULT;1.1;AES256
```

---

## 📁 3. Vault File Location

The encrypted Vault file is expected at:

```text
group_vars/cisco_routers/vault.yml
```

The playbooks should load this file using:

```yaml
vars_files:
  - ../group_vars/cisco_routers/vault.yml
```

---

## 🧪 4. Syntax-Check Expected Result

### `vault_demo.yml`

Command:

```bash
ansible-playbook \
  -i inventory/hosts.ini \
  playbooks/vault_demo.yml \
  --syntax-check \
  --ask-vault-pass
```

Expected result:

```text
playbook: playbooks/vault_demo.yml
```

### `verify_vault.yml`

Command:

```bash
ansible-playbook \
  -i inventory/hosts.ini \
  playbooks/verify_vault.yml \
  --syntax-check \
  --ask-vault-pass
```

Expected result:

```text
playbook: playbooks/verify_vault.yml
```

✅ Both playbooks should pass the syntax check successfully.

---

## 🚀 5. First Configuration Execution

On a clean configuration where the Lab 10 interface description has not yet been applied, the following command is expected to produce changes:

```bash
ansible-playbook \
  -i inventory/hosts.ini \
  playbooks/vault_demo.yml \
  --ask-vault-pass
```

Expected result:

```text
R1 : ok=1 changed=1 unreachable=0 failed=0
R2 : ok=1 changed=1 unreachable=0 failed=0
R3 : ok=1 changed=1 unreachable=0 failed=0
```

### Expected Configuration

The following configuration should be applied to `FastEthernet0/0`:

```text
description Managed by Ansible Vault - Lab 10
```

and:

```text
enabled: true
```

---

## ♻️ 6. Final Idempotent Execution

After the configuration has already been applied, running the same playbook again should produce **no further changes**.

Command:

```bash
ansible-playbook \
  -i inventory/hosts.ini \
  playbooks/vault_demo.yml \
  --ask-vault-pass
```

Expected result:

```text
R1 : ok=1 changed=0 unreachable=0 failed=0
R2 : ok=1 changed=0 unreachable=0 failed=0
R3 : ok=1 changed=0 unreachable=0 failed=0
```

### ✅ Idempotency Requirement

The final execution must show:

```text
changed=0
```

for **R1, R2, and R3**.

This confirms that:

* 🔐 Vault authentication succeeded
* 🔓 The encrypted Vault file was successfully decrypted
* 🔑 `ansible_password` was loaded
* 🌐 Cisco authentication succeeded
* ⚙️ The desired configuration already existed
* ♻️ Ansible made no unnecessary changes

---

## 🔎 7. Verification Expected Output

The verification playbook:

```bash
ansible-playbook \
  -i inventory/hosts.ini \
  playbooks/verify_vault.yml \
  --ask-vault-pass
```

should successfully retrieve the running configuration of:

```text
FastEthernet0/0
```

### R1

Expected:

```text
interface FastEthernet0/0
 description Managed by Ansible Vault - Lab 10
 ip address 192.168.40.10 255.255.255.0
 duplex auto
 speed auto
end
```

### R2

Expected:

```text
interface FastEthernet0/0
 description Managed by Ansible Vault - Lab 10
 ip address 192.168.40.11 255.255.255.0
 duplex auto
 speed auto
end
```

### R3

Expected:

```text
interface FastEthernet0/0
 description Managed by Ansible Vault - Lab 10
 ip address 192.168.40.12 255.255.255.0
 duplex auto
 speed auto
end
```

---

## 📊 8. Expected Verification Recap

The verification playbook should complete successfully on all three routers.

Expected recap:

```text
R1 : ok=2 changed=0 unreachable=0 failed=0
R2 : ok=2 changed=0 unreachable=0 failed=0
R3 : ok=2 changed=0 unreachable=0 failed=0
```

### 🟢 Success Criteria

| Check                   | Expected       |
| ----------------------- | -------------- |
| Vault file encrypted    | ✅ Yes          |
| Vault decryption        | ✅ Successful   |
| Cisco authentication    | ✅ Successful   |
| R1 configuration        | ✅ Correct      |
| R2 configuration        | ✅ Correct      |
| R3 configuration        | ✅ Correct      |
| Final configuration run | ♻️ `changed=0` |
| Verification            | 🔎 Successful  |
| Failed hosts            | ❌ None         |
| Unreachable hosts       | ❌ None         |

---

## 🛡️ 9. Security Requirements

The following security requirements must always be maintained:

* 🔐 `group_vars/cisco_routers/vault.yml` must remain encrypted.
* 🚫 The plaintext Cisco password must not be stored in `inventory/hosts.ini`.
* 🚫 The plaintext password must not be stored in `README.md`.
* 🚫 The plaintext password must not be stored in `expected_outputs/`.
* 🚫 The plaintext password must not be stored in Git commits.
* 🚫 Screenshots must not expose the Vault password.
* 🔑 The Vault password itself must never be documented in project files.

The purpose of Ansible Vault is to protect sensitive authentication information while still allowing Ansible automation to use it securely.

---

## 🏁 10. Final Expected Result

Lab 10 is considered successful when:

```text
Vault encryption        → PASS
Vault decryption        → PASS
Credential loading      → PASS
Cisco authentication    → PASS
Configuration           → PASS
Idempotency             → PASS
Verification            → PASS
Security requirements   → PASS
```

🎉 **Expected final state:**

```text
R1 → successful
R2 → successful
R3 → successful

Final configuration run:
changed=0

Verification:
successful
```

This demonstrates that **Ansible Vault can securely provide Cisco authentication credentials to an Ansible network automation workflow without storing the password directly in the inventory or playbook.**
