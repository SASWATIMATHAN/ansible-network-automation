# 🛡️ Lab 08 — Ansible Error Handling

## 📌 Overview

This lab demonstrates how **Ansible handles failures, validation errors, recovery, and controlled execution flow** during network automation.

The experiments were performed against a **Cisco 3745 GNS3 topology** containing three routers: **R1, R2, and R3**.

> ✅ **All failures in this lab were intentional and controlled.**
> 🔒 **No router configuration was modified during the experiments.**

---

## 🎯 Objectives

The main objectives of this lab are to understand and demonstrate:

* 🧱 `block`
* 🚑 `rescue`
* 🔄 `always`
* ⚠️ `failed_when`
* 🛠️ `failed_when` with `rescue`
* ⏭️ `ignore_errors`
* 🔍 Post-failure verification
* 🌐 Multi-device error handling
* ✅ Safe validation without changing device configuration

---

## 🌐 Network Topology

| Device         | Management IP    | Platform           |
| -------------- | ---------------- | ------------------ |
| R1             | `192.168.40.10`  | Cisco 3745         |
| R2             | `192.168.40.11`  | Cisco 3745         |
| R3             | `192.168.40.12`  | Cisco 3745         |
| GNS3 VM        | `192.168.40.128` | GNS3               |
| Windows VMnet1 | `192.168.40.1`   | Management Network |

**Network:** `192.168.40.0/24`

**Management Interface:** FastEthernet0/0

**IOS:** 12.4(25d)

---

## ⚙️ Ansible Environment

* 🐧 **OS:** Ubuntu WSL
* 📦 **Ansible:** `ansible-core 2.16.3`
* 🐍 **Python:** `3.12.3`
* 🧩 **Jinja:** `3.1.2`
* 🔌 **Connection:** `ansible.netcommon.network_cli`
* 📡 **Network OS:** `cisco.ios.ios`

### Modules Used

* `ansible.netcommon.cli_command`
* `cisco.ios.ios_command`
* `ansible.builtin.fail`
* `ansible.builtin.debug`

> ℹ️ The environment displayed a warning that `ansible-pylibssh` was not installed and Ansible was falling back to Paramiko. This was only a warning and did **not** prevent successful execution.

---

# 🧪 Experiments

## 1️⃣ Block / Rescue / Always

**Playbook:**

`playbooks/block_rescue_always.yml`

### Purpose

Demonstrates how Ansible can:

1. 🧱 Group tasks inside a `block`
2. 💥 Encounter a controlled failure
3. 🚑 Execute `rescue`
4. 🔄 Execute `always`
5. 🔍 Continue with post-failure verification

The failure was intentionally generated for demonstration purposes.

### Result

All three routers completed successfully:

* `ok=4`
* `changed=0`
* `unreachable=0`
* `failed=0`
* `rescued=1`
* `ignored=0`

> ✅ The intentional failure was successfully handled by `rescue`.

---

## 2️⃣ Failed_When Validation

**Playbook:**

`playbooks/failed_when_validation.yml`

### Purpose

Demonstrates **condition-based task validation** using `failed_when`.

The playbook collected:

```text
show ip interface brief
```

The management interface on all routers was:

```text
FastEthernet0/0 — up/up
```

The validation therefore succeeded.

### Result

**R1, R2 and R3:**

* `ok=3`
* `changed=0`
* `unreachable=0`
* `failed=0`

Final status:

```text
ALWAYS: Interface validation completed
```

> ✅ The actual Cisco interface state satisfied the validation condition.

---

## 3️⃣ Failed_When + Rescue

**Playbook:**

`playbooks/failed_when_rescue.yml`

### Purpose

Demonstrates how Ansible can use `failed_when` to **convert command output into a task failure**, and then handle that failure using `rescue`.

The Cisco command itself completed successfully and showed:

```text
FastEthernet0/0 — up/up
```

However, the playbook intentionally searched for a nonexistent state:

```text
FAKE_STATE
```

Therefore:

```text
failed_when_result = true
```

Ansible intentionally marked the task as failed.

### Recovery

The `rescue` section executed:

```text
RESCUE: failed_when detected a validation failure
```

Router reachability was then verified successfully.

Final status:

```text
ALWAYS: failed_when validation workflow completed
```

### Result

For all three routers:

* `ok=3`
* `changed=0`
* `unreachable=0`
* `failed=0`
* `rescued=1`
* `ignored=0`

> 🟢 This demonstrates a **controlled validation failure**, not an actual router failure.

---

## 4️⃣ Ignore Errors

**Playbook:**

`playbooks/ignore_errors.yml`

### Purpose

Demonstrates how `ignore_errors: true` allows Ansible to continue execution after a task fails.

A controlled failure was generated using:

```yaml
ansible.builtin.fail
```

with:

```yaml
ignore_errors: true
```

Execution flow:

```text
💥 FAILURE
     ↓
⏭️ IGNORE
     ↓
▶️ CONTINUE EXECUTION
     ↓
🔍 VERIFY CONNECTIVITY
     ↓
✅ FINAL STATUS
```

### Result

For R1, R2 and R3:

* `ok=4`
* `changed=0`
* `unreachable=0`
* `failed=0`
* `rescued=0`
* `ignored=1`

> ⚠️ `ignore_errors` **does not execute `rescue`**. It simply allows the playbook to continue after the failed task.

---

# 🔑 Key Concept

The most important distinction demonstrated in this lab is:

### 🚑 `rescue`

```text
FAIL
 ↓
RECOVERY
 ↓
CONTINUE
```

### ⏭️ `ignore_errors`

```text
FAIL
 ↓
IGNORE
 ↓
CONTINUE
```

`rescue` is therefore useful when **specific recovery or handling actions are required**, while `ignore_errors` is useful when a failure is **allowed to occur without stopping the remaining tasks**.

---

# 📁 Lab Structure

```text
lab08_error_handling/
├── expected_outputs/
│   ├── block_rescue_always_expected.md
│   ├── failed_when_rescue_expected.md
│   └── ignore_errors_expected.md
│
├── inventory/
│   └── hosts.ini
│
├── outputs/
│   ├── block_rescue_always_actual.txt
│   ├── failed_when_rescue_actual.txt
│   └── ignore_errors_actual.txt
│
├── playbooks/
│   ├── block_rescue_always.yml
│   ├── failed_when_rescue.yml
│   ├── failed_when_validation.yml
│   └── ignore_errors.yml
│
└── screenshots/
```

---

# 🛡️ Safety

All experiments were designed as **non-destructive demonstrations**.

No router configuration was changed.

The existing:

* 🌐 GNS3 topology
* 🔌 VMnet1 network
* 🖥️ GNS3 VM
* 📡 Router IP addresses
* 🔐 SSH configuration
* 🔑 RSA keys

were preserved.

---

# 📚 What I Learnt

This lab demonstrates how Ansible can make network automation more reliable by providing mechanisms to handle unexpected conditions.

### `block`

🧱 Groups related tasks into a logical unit.

### `rescue`

🚑 Handles failures occurring inside a `block`.

### `always`

🔄 Executes regardless of whether the preceding tasks succeed or fail.

### `failed_when`

⚠️ Allows task success/failure to be determined from custom conditions or command output.

### `ignore_errors`

⏭️ Allows execution to continue after a failed task.

### Overall Workflow

```text
Network Automation
       │
       ▼
   Execute Task
       │
       ▼
   ┌─── Success ────► Continue
   │
   └─── Failure
          │
          ├──► rescue ──► Recovery
          │
          └──► ignore_errors ──► Continue
                         │
                         ▼
                      always
```

---

## ✅ Lab 08 Status

**Technical experiments:** ✅ Complete

**Expected outputs:** ✅ Complete

**Actual outputs:** ✅ Captured

**README:** 📝 Documentation

**Screenshots:** 📸 Complete

**Git commit/push:** ⏳ Documentation

---

## 🚀 Next Lab

After completing the documentation and GitHub commit for Lab 08:

### **Lab 09 — Ansible Roles**

Topics will include:

* 📁 Role structure
* `tasks/main.yml`
* `handlers/main.yml`
* `defaults/main.yml`
* `vars/main.yml`
* 📄 Templates
* 🔁 Reusable network automation
* ▶️ Calling roles from a playbook
* 🌐 Cisco IOS configuration using Ansible Roles

> 🎯 **Lab 08 completes the error-handling foundation needed before moving to reusable Ansible automation with Roles.**
