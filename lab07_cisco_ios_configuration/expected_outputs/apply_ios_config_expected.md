LAB 07 — APPLY IOS CONFIGURATION

Expected configuration change:

R1:
interface FastEthernet0/0
 description Managed by Ansible - Lab 07

R2:
interface FastEthernet0/0
 description Managed by Ansible - Lab 07

R3:
interface FastEthernet0/0
 description Managed by Ansible - Lab 07

Expected first-run result:

R1:
ok=1
changed=1
unreachable=0
failed=0

R2:
ok=1
changed=1
unreachable=0
failed=0

R3:
ok=1
changed=1
unreachable=0
failed=0

The configuration should be successfully applied to all three Cisco IOS routers.
