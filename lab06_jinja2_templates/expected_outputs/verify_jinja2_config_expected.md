# Lab 06 — Jinja2 Configuration Verification Expected Output

## Expected Result

The Jinja2-generated configuration should be successfully applied to all three Cisco routers.

## R1 — 192.168.40.10

interface FastEthernet0/0
 description Managed by Ansible - R1
 ip address 192.168.40.10 255.255.255.0
 duplex auto
 speed auto
!

## R2 — 192.168.40.11

interface FastEthernet0/0
 description Managed by Ansible - R2
 ip address 192.168.40.11 255.255.255.0
 duplex auto
 speed auto
!

## R3 — 192.168.40.12

interface FastEthernet0/0
 description Managed by Ansible - R3
 ip address 192.168.40.12 255.255.255.0
 duplex auto
 speed auto
!

## Expected Play Recap

R1 : ok=2 changed=0 unreachable=0 failed=0
R2 : ok=2 changed=0 unreachable=0 failed=0
R3 : ok=2 changed=0 unreachable=0 failed=0

## Validation

- All three routers should be reachable through Ansible.
- The Jinja2-generated interface descriptions should be present.
- R1 management IP should remain 192.168.40.10.
- R2 management IP should remain 192.168.40.11.
- R3 management IP should remain 192.168.40.12.
- Existing interface settings should remain unchanged.
- Verification should complete with zero unreachable hosts.
- Verification should complete with zero failed hosts.
- Actual output should match the expected configuration and play recap.

## Expected Result Status

PASS — Expected output should match the actual verification output for R1, R2, and R3.
