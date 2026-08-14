LAB 07 — IDEMPOTENCY TEST

Expected result when the configuration playbook is executed a second time:

R1:
ok=1
changed=0
unreachable=0
failed=0

R2:
ok=1
changed=0
unreachable=0
failed=0

R3:
ok=1
changed=0
unreachable=0
failed=0

Expected behavior:

The Cisco IOS configuration should already match the desired state.
Ansible should therefore make no additional configuration changes.

This demonstrates Ansible idempotency.
