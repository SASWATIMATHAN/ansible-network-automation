# Expected Output — `show version`

## Command

```bash
ansible -i ../lab02_inventory/inventory/hosts.ini cisco_routers -m cisco.ios.ios_command -a "commands='show version'" -k
```

## Expected Behavior

When SSH connectivity is available, Ansible should connect to each Cisco IOS router and execute:

```text
show version
```

The command normally provides information such as:

* Cisco IOS software version
* Device model
* System uptime
* Processor information
* Memory information
* Configuration register
* Hardware information

## Expected Ansible Result

Each reachable router should return:

```text
SUCCESS
```

and the Cisco IOS `show version` output.

## Lab Observation

The actual execution resulted in:

```text
R1 → FAILED — timed out
R2 → FAILED — timed out
R3 → FAILED — timed out
```

The difference between the expected and actual results is attributed to the existing WSL-to-GNS3 router connectivity limitation.
