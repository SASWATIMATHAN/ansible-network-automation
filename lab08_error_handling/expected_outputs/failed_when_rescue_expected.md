LAB 08 — FAILED_WHEN WITH RESCUE
EXPECTED OUTPUT

Objective:
Demonstrate condition-based task failure using failed_when and recovery using rescue.

Expected workflow:

1. Execute "show ip interface brief" on R1, R2 and R3.
2. Register the command output.
3. Evaluate the failed_when condition.
4. Because FAKE_STATE is not present, failed_when should evaluate to true.
5. The validation task should be marked FAILED.
6. The next task inside the block should not execute.
7. The rescue section should execute.
8. The rescue message should report that the validation failure was handled.
9. Router reachability should be verified successfully after the failure.
10. The always section should execute.
11. No Cisco router configuration should be changed.

Expected recap:

R1:
ok=3
changed=0
unreachable=0
failed=0
skipped=0
rescued=1
ignored=0

R2:
ok=3
changed=0
unreachable=0
failed=0
skipped=0
rescued=1
ignored=0

R3:
ok=3
changed=0
unreachable=0
failed=0
skipped=0
rescued=1
ignored=0

Important:
The Cisco command itself succeeds. The task is marked failed only because the failed_when condition evaluates to true.

The failure is intentionally created for demonstration and does not modify the routers.
