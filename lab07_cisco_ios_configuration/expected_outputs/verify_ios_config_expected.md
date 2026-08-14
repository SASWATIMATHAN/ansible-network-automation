LAB 07 — VERIFY IOS CONFIGURATION

Expected verified configuration:

R1:
interface FastEthernet0/0
 description Managed by Ansible - Lab 07
 ip address 192.168.40.10 255.255.255.0
 duplex auto
 speed auto

R2:
interface FastEthernet0/0
 description Managed by Ansible - Lab 07
 ip address 192.168.40.11 255.255.255.0
 duplex auto
 speed auto

R3:
interface FastEthernet0/0
 description Managed by Ansible - Lab 07
 ip address 192.168.40.12 255.255.255.0
 duplex auto
 speed auto

Expected verification result:

R1:
ok=2
changed=0
unreachable=0
failed=0

R2:
ok=2
changed=0
unreachable=0
failed=0

R3:
ok=2
changed=0
unreachable=0
failed=0

The Ansible-managed interface description should be present on FastEthernet0/0 of all three routers.
