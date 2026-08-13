# Lab 04 — First Playbook Expected Output

## Expected Result

The Ansible playbook should successfully connect to all three Cisco IOS routers and execute the hostname command.

### Expected Hostname Output


R1 → hostname R1
R2 → hostname R2
R3 → hostname R3
### Expected Play Recap


R1 : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
R2 : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
R3 : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

## Success Criteria

- All three routers are reachable through Ansible.
- The `Display router hostname` task succeeds on R1, R2 and R3.
- The returned hostname matches the corresponding router.
- The `Display hostname output` task succeeds on all three routers.
- No task reports `failed` or `unreachable`.
- No configuration changes are made to the routers.

