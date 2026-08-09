# Connectivity Limitation

The lab environment uses Cisco IOS routers running inside GNS3/GNS3 VM.

A known WSL-to-GNS3 router connectivity limitation exists in the environment. Previous troubleshooting was performed during the network automation project.

This lab will not repeat that troubleshooting. Any connectivity-related failure encountered during Lab 03 will be documented as an environment limitation rather than investigated repeatedly.

## WSL to GNS3 Router Connectivity

The Cisco IOS routers used in this lab are running inside the existing GNS3/GNS3 VM environment.

The Lab 03 `cisco.ios.ios_facts` ad-hoc command produced the following SSH connection failure:

```text
Unable to connect to port 22 on 192.168.100.11
Unable to connect to port 22 on 192.168.100.12
Unable to connect to port 22 on 192.168.100.13
```

The same environment has previously been subjected to extensive connectivity troubleshooting during the network automation project.

Therefore, Lab 03 does not repeat the previously performed troubleshooting steps.

The failure is documented as an environment limitation while the Ansible command, module syntax, inventory, and expected behavior are studied.

## Ansible Warning

Ansible also reported:

```text
ansible-pylibssh not installed, falling back to paramiko
```

This is a transport-library warning and is separate from the observed SSH connection failure.

## Status

`cisco.ios.ios_facts` command syntax and module usage were successfully demonstrated, but execution against the GNS3 routers was unsuccessful because SSH connectivity from the WSL environment was unavailable at the time of testing.

