# Troubleshooting — Cisco IOS Facts Timeout

## Problem

The `cisco.ios.ios_facts` module timed out when executed against
the Cisco routers.

## Command

```bash
ansible -i lab02_inventory/inventory/hosts.ini cisco_routers -m cisco.ios.ios_facts -k
```

## Error

The command timed out on all three routers:

```text
R1 | FAILED! => timed out
R2 | FAILED! => timed out
R3 | FAILED! => timed out
```

## Initial Verification

Before testing `cisco.ios.ios_facts`, basic Ansible connectivity was
successfully verified using the Ansible ping module:

```bash
ansible -i lab02_inventory/inventory/hosts.ini cisco_routers -m ansible.builtin.ping -k
```

All three routers returned:

```text
R1 | SUCCESS => "ping": "pong"
R2 | SUCCESS => "ping": "pong"
R3 | SUCCESS => "ping": "pong"
```

This confirmed that the inventory and basic Ansible connectivity
were functioning.

## Troubleshooting Performed

The GNS3, Windows, WSL, and Cisco management-network path was
investigated.

The following were verified:

- Cisco router management interfaces were up/up.
- R1 could communicate with R2 and R3.
- Windows `Ethernet 2` was configured with `192.168.100.1/24`.
- R1 learned the MAC address of `192.168.100.1` through ARP.
- Windows Firewall was configured to allow ICMPv4 on `Ethernet 2`.
- IP forwarding was enabled on the Windows `Ethernet 2` interface.
- IP forwarding was enabled on the WSL virtual interface.
- A static route from WSL toward the `192.168.100.0/24` network was tested.
- Router ARP entries were cleared and connectivity was retested.
- Connectivity between WSL and the Windows `Ethernet 2` interface was verified.
- The GNS3 Cloud/Windows loopback path was investigated.

Despite these checks, connectivity between the WSL environment and
the Cisco management network remained unsuccessful.

## Conclusion

The `cisco.ios.ios_facts` timeout was associated with the
GNS3/Windows/WSL management-network connectivity path rather than
the Ansible inventory configuration.

Basic Ansible connectivity to the inventory was previously confirmed
with the `ansible.builtin.ping` module.

Further environmental troubleshooting was stopped to avoid blocking
the remaining Ansible labs. The issue and troubleshooting steps are
documented here for future reference.
