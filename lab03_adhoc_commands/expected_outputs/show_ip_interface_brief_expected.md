# Expected Output — `show ip interface brief`

## Command

```bash
ansible -i ../lab02_inventory/inventory/hosts.ini cisco_routers -m cisco.ios.ios_command -a "commands='show ip interface brief'" -k
```

## Expected Behavior

When SSH connectivity to the Cisco IOS routers is available, Ansible should connect to each router and execute:

```text
show ip interface brief
```

The expected Cisco IOS response is a concise table containing information similar to:

```text
Interface              IP-Address      OK? Method Status                Protocol
FastEthernet0/0        <IP-address>    YES ...    up                    up
FastEthernet0/1        <IP-address>    YES ...    up                    up
```

The exact interfaces, IP addresses, status, and protocol values depend on the individual router configuration.

## Expected Ansible Result

Each reachable router should return:

```text
SUCCESS
```

with the Cisco IOS command output contained in the result.

## Lab Observation

The actual execution in this environment resulted in:

```text
R1 → FAILED — timed out
R2 → FAILED — timed out
R3 → FAILED — timed out
```

The difference between the expected and actual results is attributed to the existing WSL-to-GNS3 router connectivity limitation.
