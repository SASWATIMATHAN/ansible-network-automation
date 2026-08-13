# Lab 04 — First Ansible Playbook

## Objective

The objective of this lab is to create and execute the first Ansible playbook for Cisco IOS routers.

This lab demonstrates the basic Ansible playbook workflow:

- Defining a play
- Selecting target hosts
- Disabling automatic fact gathering
- Executing a Cisco IOS command
- Registering task output
- Displaying registered output
- Verifying successful execution across multiple routers

---

## Lab Environment

| Component | Details |
|---|---|
| Automation Host | Ubuntu WSL |
| Automation Tool | Ansible |
| Network Devices | Cisco 3745 routers |
| IOS | 12.4(25d) |
| Routers | R1, R2, R3 |
| Connection Method | SSH / `network_cli` |
| Network OS | `cisco.ios.ios` |
| SSH User | `admin` |

### Router Addressing

| Router | IP Address |
|---|---|
| R1 | `192.168.40.10` |
| R2 | `192.168.40.11` |
| R3 | `192.168.40.12` |

---

## Network Topology

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
               .10   .11   .12

Network: 192.168.40.0/24
Windows VMnet1: 192.168.40.1
```

---

## Directory Structure

```text
lab04_first_playbook/
├── README.md
├── inventory/
│   └── hosts.ini
├── playbooks/
│   └── first_playbook.yml
├── outputs/
│   └── first_playbook_actual.txt
├── expected_outputs/
│   └── first_playbook_expected.md
└── screenshots/
```

---

# 1. Inventory

The Lab 04 inventory is located at:

```text
inventory/hosts.ini
```

The inventory contains the three Cisco IOS routers:

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

### Inventory Parameters

| Parameter | Purpose |
|---|---|
| `ansible_host` | IP address of the target router |
| `ansible_connection` | Uses Ansible Network CLI connection |
| `ansible_network_os` | Specifies Cisco IOS |
| `ansible_user` | SSH username |

The SSH password is deliberately not stored in the inventory. It is supplied interactively using Ansible's `-k` option during execution.

---

# 2. First Playbook

The playbook is located at:

```text
playbooks/first_playbook.yml
```

The complete playbook is:

```yaml
---
- name: First Cisco IOS Ansible Playbook
  hosts: cisco_routers
  gather_facts: false

  tasks:
    - name: Display router hostname
      cisco.ios.ios_command:
        commands:
          - show running-config | include hostname
      register: hostname_output

    - name: Display hostname output
      ansible.builtin.debug:
        var: hostname_output.stdout_lines
```

---

# 3. Playbook Structure

The playbook follows this structure:

```text
Playbook
   │
   └── Play
        │
        ├── hosts: cisco_routers
        │
        ├── gather_facts: false
        │
        └── Tasks
             │
             ├── Task 1
             │    └── cisco.ios.ios_command
             │         └── show running-config | include hostname
             │
             └── Task 2
                  └── ansible.builtin.debug
                       └── Display registered output
```

---

# 4. Task 1 — Display Router Hostname

The first task uses the `cisco.ios.ios_command` module to execute:

```text
show running-config | include hostname
```

on each router.

The result is stored using:

```yaml
register: hostname_output
```

This allows the result of the command to be used by subsequent tasks.

---

# 5. Task 2 — Display Registered Output

The second task uses:

```yaml
ansible.builtin.debug:
  var: hostname_output.stdout_lines
```

to display the result returned by the first task.

The basic workflow is:

```text
Execute command
      ↓
Register result
      ↓
Store result in variable
      ↓
Display variable
```

---

# 6. Syntax Validation

Before executing the playbook against the routers, the syntax was checked using:

```bash
ansible-playbook --syntax-check \
-i lab04_first_playbook/inventory/hosts.ini \
lab04_first_playbook/playbooks/first_playbook.yml
```

The syntax check completed successfully:

```text
playbook: lab04_first_playbook/playbooks/first_playbook.yml
```

This confirmed that Ansible could successfully parse the playbook.

---

# 7. First Execution Attempt

The playbook was initially executed without supplying the SSH password:

```bash
ansible-playbook \
-i lab04_first_playbook/inventory/hosts.ini \
lab04_first_playbook/playbooks/first_playbook.yml
```

The execution failed with:

```text
No authentication methods available
```

Ansible also displayed:

```text
[WARNING]: ansible-pylibssh not installed, falling back to paramiko
```

The `ansible-pylibssh` warning was not the actual cause of the failure.

The actual issue was that no SSH authentication method/password had been supplied to Ansible.

---

# 8. Successful Execution

The playbook was then executed using the `-k` option:

```bash
ansible-playbook -k \
-i lab04_first_playbook/inventory/hosts.ini \
lab04_first_playbook/playbooks/first_playbook.yml
```

The `-k` option instructs Ansible to prompt for the SSH password.

The same SSH password used for the Cisco routers was entered at the prompt.

---

# 9. Successful Execution Result

The playbook successfully connected to all three Cisco IOS routers.

The returned hostname results were:

```text
hostname R1
hostname R2
hostname R3
```

The corresponding Ansible task results were:

```text
ok: [R1]
ok: [R2]
ok: [R3]
```

The final play recap was:

```text
R1                         : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
R2                         : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
R3                         : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

---

# 10. Actual Output

The complete successful execution output was saved to:

```text
outputs/first_playbook_actual.txt
```

The recorded output contains:

- Play execution
- Task execution
- Paramiko fallback warning
- Successful execution on R1
- Successful execution on R2
- Successful execution on R3
- Returned hostnames
- Final play recap

---

# 11. Expected Output

The expected successful behavior is documented in:

```text
expected_outputs/first_playbook_expected.md
```

Expected hostname results:

```text
R1 → hostname R1
R2 → hostname R2
R3 → hostname R3
```

Expected final status:

```text
R1 → failed=0, unreachable=0
R2 → failed=0, unreachable=0
R3 → failed=0, unreachable=0
```

---

# 12. No Configuration Changes

This playbook only executes the read-only command:

```text
show running-config | include hostname
```

Therefore, it does not modify the router configuration.

The play recap confirms:

```text
changed=0
```

for all three routers.

---

# 13. SSH and Connectivity

The working topology is:

```text
Ubuntu WSL
     │
     ├── SSH → R1 192.168.40.10
     │
     ├── SSH → R2 192.168.40.11
     │
     └── SSH → R3 192.168.40.12
```

The routers are:

```text
Cisco 3745
IOS 12.4(25d)
FastEthernet0/0
```

The underlying SSH connectivity had already been verified before this Ansible lab.

No GNS3 topology, router IP addressing, or VMnet configuration changes were required during Lab 04.

---

# 14. Ansible Connection Method

The inventory uses:

```text
ansible_connection=ansible.netcommon.network_cli
```

This tells Ansible to use its network-device connection mechanism rather than treating the Cisco router as a normal Linux/Unix host.

The network operating system is specified as:

```text
ansible_network_os=cisco.ios.ios
```

This allows Ansible to use the appropriate Cisco IOS network modules and behavior.

---

# 15. Paramiko Fallback

During execution, Ansible displayed:

```text
[WARNING]: ansible-pylibssh not installed, falling back to paramiko
```

This means Ansible used Paramiko as the SSH backend.

The fallback did not prevent successful execution.

The final result proves that Ansible successfully connected to:

```text
R1
R2
R3
```

and executed the Cisco IOS command.

---

# 16. Key Concepts Learned

## Playbook

A YAML file containing one or more plays that define the automation workflow.

## Play

Defines the target hosts and the tasks to execute.

## Hosts

Specifies which inventory group or hosts should receive the tasks.

Example:

```yaml
hosts: cisco_routers
```

## Task

An individual operation performed by Ansible.

## Module

A reusable Ansible component that performs a specific operation.

Example:

```text
cisco.ios.ios_command
```

## Register

Stores the result of a task in an Ansible variable.

Example:

```yaml
register: hostname_output
```

## Debug

Displays the contents of a variable during playbook execution.

Example:

```yaml
ansible.builtin.debug:
  var: hostname_output.stdout_lines
```

## gather_facts

Controls automatic fact gathering.

For this network-device playbook:

```yaml
gather_facts: false
```

was used because the playbook only needed to execute a specific IOS command.

---

# 17. Lab Workflow

The complete workflow followed in this lab was:

```text
Create Lab 04 structure
        ↓
Create inventory
        ↓
Create first playbook
        ↓
Syntax check
        ↓
First execution attempt
        ↓
Authentication error identified
        ↓
Use -k to request SSH password
        ↓
Successful Ansible execution
        ↓
Verify R1/R2/R3 output
        ↓
Save actual output
        ↓
Create expected output
        ↓
Capture screenshots
        ↓
Final repository verification
        ↓
Git commit
        ↓
Git push
```

---

# 18. Files in This Lab

| File | Purpose |
|---|---|
| `README.md` | Lab documentation |
| `inventory/hosts.ini` | Cisco router inventory |
| `playbooks/first_playbook.yml` | First Ansible playbook |
| `outputs/first_playbook_actual.txt` | Actual execution output |
| `expected_outputs/first_playbook_expected.md` | Expected successful result |
| `screenshots/` | Lab evidence |

---

# 19. Result

**Lab 04 — First Ansible Playbook completed successfully.**

The playbook successfully connected to three Cisco IOS routers and retrieved their hostnames.

Final result:

```text
R1 → hostname R1 → SUCCESS
R2 → hostname R2 → SUCCESS
R3 → hostname R3 → SUCCESS
```

All three routers reported:

```text
failed=0
unreachable=0
changed=0
```

This lab establishes the foundation for the next stages of the Ansible network automation project, including variables, Jinja2 templates, Cisco IOS configuration, error handling, roles, Vault, NAPALM, NETCONF, RESTCONF, and the final enterprise automation project.
