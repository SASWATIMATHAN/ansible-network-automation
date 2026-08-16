LAB 08 — IGNORE_ERRORS
EXPECTED OUTPUT

Objective:
Demonstrate how Ansible continues execution after a task failure when
ignore_errors is enabled.

Expected workflow:

1. The controlled failure task should report FAILED.
2. Ansible should display "...ignoring".
3. Execution should continue to the next task.
4. The continuation message should be displayed.
5. Router reachability should be verified successfully.
6. The final status message should be displayed.
7. No rescue section should execute.
8. No router configuration should be changed.

Expected recap:

R1:
ok=4
changed=0
unreachable=0
failed=0
skipped=0
rescued=0
ignored=1

R2:
ok=4
changed=0
unreachable=0
failed=0
skipped=0
rescued=0
ignored=1

R3:
ok=4
changed=0
unreachable=0
failed=0
skipped=0
rescued=0
ignored=1

Expected behavior:

FAIL
  ↓
ignore_errors
  ↓
CONTINUE
  ↓
NEXT TASK
  ↓
VERIFICATION
  ↓
COMPLETION

Important:
The intentional failure is generated using ansible.builtin.fail and does
not modify the Cisco routers.

Unlike rescue, ignore_errors does not execute dedicated recovery tasks.
