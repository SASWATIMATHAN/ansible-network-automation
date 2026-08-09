# Expected Output — Multiple IOS Commands

## Command

```bash
ansible -i ../lab02_inventory/inventory/hosts.ini cisco_routers -m cisco.ios.ios_command -a "commands=['show version','show ip interface brief']" -k
```

## Expected Behavior

When SSH connectivity is available, Ansible should connect to each Cisco IOS router and execute both commands:

```text
show version
show ip interface brief
```

The response should contain the output of both commands for each reachable router.

## Expected Result

Each reachable router should return:

```text
SUCCESS
```

along with the corresponding IOS command output.

## Lab Observation

The actual execution resulted in timeouts for R1, R2, and R3 because of the existing WSL-to-GNS3 router connectivity limitation.

The multiple-command syntax itself was valid.
