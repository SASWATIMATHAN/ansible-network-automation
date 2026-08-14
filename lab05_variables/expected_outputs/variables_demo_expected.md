# Expected Output — Lab 05 Variables

## Lab Variables

The playbook should display the following variables for all three routers:

- Lab: Lab 05 - Variables
- Project: Ansible Network Automation
- Management Network: 192.168.40.0/24

## Router Variables

### R1
- Router: R1
- Management IP: 192.168.40.10

### R2
- Router: R2
- Management IP: 192.168.40.11

### R3
- Router: R3
- Management IP: 192.168.40.12

## Cisco Hostname

The Cisco IOS hostname command should return:

- R1 → `hostname R1`
- R2 → `hostname R2`
- R3 → `hostname R3`

## Expected Play Recap

All three routers should complete successfully:


R1 : ok=4 changed=0 unreachable=0 failed=0
R2 : ok=4 changed=0 unreachable=0 failed=0
R3 : ok=4 changed=0 unreachable=0 failed=0



