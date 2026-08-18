# 📊 Lab 09 — Ansible Roles Expected Output

> **Expected results for the `cisco_ios_config` Ansible Role**

---

## 🎯 1. First Role Execution

The role should successfully connect to all three Cisco IOS routers and configure the management interface.

Command:

```bash
ansible-playbook \
-i inventory/hosts.ini \
playbooks/apply_role.yml \
-k
```

### ✅ Expected Result

All three routers should report:

```text
R1 : changed=1
R2 : changed=1
R3 : changed=1
```

Expected play recap:

```text
R1 : ok=1 changed=1 unreachable=0 failed=0
R2 : ok=1 changed=1 unreachable=0 failed=0
R3 : ok=1 changed=1 unreachable=0 failed=0
```

The `changed=1` result indicates that the desired configuration was applied to each router.

---

## ⚙️ 2. Expected Configuration

The role should configure the following interface on each router:

```text
FastEthernet0/0
```

with the description:

```text
Managed by Ansible Role - Lab 09
```

Expected configuration:

```text
interface FastEthernet0/0
 description Managed by Ansible Role - Lab 09
```

This configuration should be present on:

```text
R1
R2
R3
```

---

## 🔍 3. Verification

The verification playbook should confirm that the configuration exists on all three routers.

Command:

```bash
ansible-playbook \
-i inventory/hosts.ini \
playbooks/verify_role.yml \
-k
```

### ✅ Expected Result

The verification should successfully complete for:

```text
R1
R2
R3
```

Expected play recap:

```text
R1 : ok=2 changed=0 unreachable=0 failed=0
R2 : ok=2 changed=0 unreachable=0 failed=0
R3 : ok=2 changed=0 unreachable=0 failed=0
```

The verification task should report the expected interface description:

```text
description Managed by Ansible Role - Lab 09
```

### 📌 Important

```text
changed=0
```

is expected during verification because the verification playbook is **read-only** and does not modify the routers.

---

## ♻️ 4. Second Role Execution — Idempotency Test

The same role should then be executed a second time.

Command:

```bash
ansible-playbook \
-i inventory/hosts.ini \
playbooks/apply_role.yml \
-k
```

### ✅ Expected Result

Because the required configuration already exists, Ansible should make no further changes.

Expected play recap:

```text
R1 : ok=1 changed=0 unreachable=0 failed=0
R2 : ok=1 changed=0 unreachable=0 failed=0
R3 : ok=1 changed=0 unreachable=0 failed=0
```

The important result is:

```text
changed=0
```

for all three routers.

---

## 🧠 5. Idempotency Demonstration

The Lab 09 role demonstrates the fundamental Ansible principle of **idempotency**.

### First Execution

```text
Configuration absent
        ↓
Role executes
        ↓
Configuration created
        ↓
changed=1
```

### Second Execution

```text
Configuration already present
        ↓
Role executes
        ↓
No configuration change required
        ↓
changed=0
```

Therefore:

```text
First execution  → changed=1
Second execution → changed=0
```

This confirms that the role is **idempotent**.

---

## 🏗️ 6. Expected Role Structure

The expected role directory structure is:

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

The surrounding Lab 09 structure should contain:

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

---

## 📋 7. Expected Successful Outcome

Lab 09 is considered successful when all of the following conditions are satisfied:

* ✅ The `cisco_ios_config` role executes successfully.
* ✅ R1 is configured successfully.
* ✅ R2 is configured successfully.
* ✅ R3 is configured successfully.
* ✅ `FastEthernet0/0` receives the required description.
* ✅ Verification confirms the expected configuration.
* ✅ Verification produces `changed=0`.
* ✅ A second role execution produces `changed=0`.
* ✅ No router is unreachable.
* ✅ No task fails.
* ✅ Idempotency is demonstrated.
* ✅ Role components are organized using the standard Ansible Role structure.

---

# 🎓 8. Lab 09 Result

```text
╔════════════════════════════════════════════════════╗
║              LAB 09 — SUCCESS                     ║
╠════════════════════════════════════════════════════╣
║ Ansible Role              : cisco_ios_config       ║
║ Devices Managed            : R1, R2, R3            ║
║ Interface                  : FastEthernet0/0       ║
║ Configuration              : Applied Successfully ║
║ Verification               : Successful           ║
║ First Execution             : changed=1            ║
║ Second Execution            : changed=0            ║
║ Idempotency                 : Demonstrated         ║
║ Device Failures             : 0                    ║
╚════════════════════════════════════════════════════╝
```

> 🎯 **Lab 09 successfully demonstrates how Ansible Roles provide a reusable and organized approach to Cisco network automation while maintaining idempotent configuration management.**
