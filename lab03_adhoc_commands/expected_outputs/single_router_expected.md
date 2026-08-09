# Expected Output — Single Router Targeting

## Command

```bash
ansible -i ../lab02_inventory/inventory/hosts.ini R1 -m cisco.ios.ios_command -a "commands='show version'" -k
```

## Expected Behavior

Only the inventory host `R1` should be targeted.

When SSH connectivity is available, Ansible should connect to R1 and execute:

```text
show version
```

The result should contain the Cisco IOS `show version` output for R1.

## Expected Result

```text
R1 | SUCCESS
```

with the corresponding Cisco IOS command output.

## Lab Observation

The actual execution targeted only R1, but the command timed out because of the existing WSL-to-GNS3 router connectivity limitation.

No other router was targeted by this command.
