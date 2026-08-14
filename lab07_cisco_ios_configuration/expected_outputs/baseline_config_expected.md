LAB 07 — BASELINE CONFIGURATION

R1:
interface FastEthernet0/0
 ip address 192.168.40.10 255.255.255.0
 duplex auto
 speed auto

R2:
interface FastEthernet0/0
 ip address 192.168.40.11 255.255.255.0
 duplex auto
 speed auto

R3:
interface FastEthernet0/0
 ip address 192.168.40.12 255.255.255.0
 duplex auto
 speed auto

Expected result:
R1, R2 and R3 reachable.
changed=0
unreachable=0
failed=0
