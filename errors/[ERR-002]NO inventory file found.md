# [ERR-002] Ansible: Inventory File Not Found / Misconfigured Path

**Environment:** CentOS Control Node → Ubuntu Managed Node (192.168.8.107)

## Error

```bash
ansible webservers -m ping



**Output:**

<img width="1525" height="148" alt="image" src="https://github.com/user-attachments/assets/18c166bb-6935-46ab-b5e8-e343f420a347" />

```
[WARNING]: Unable to parse /ansible/hosts as an inventory source
[WARNING]: No inventory was parsed, only implicit localhost is available
[WARNING]: provided hosts list is empty, only localhost is available. Note that the implicit localhost does not match 'all'
[WARNING]: Could not match supplied host pattern, ignoring: webservers
```

## Root Cause

Ansible was configured to look for an inventory file at `/ansible/hosts`, but:

1. The directory `/ansible/` did not exist.
2. The inventory file `hosts` was not created at that path.

## Solution

 Added the following content (using a **relative path** to my actual inventory file):
   ```ini
   [defaults]
   inventory = inventory/lab-hosts.ini
   remote_user = soc
   private_key_file = ~/.ssh/id_ed25519
   host_key_checking = False
   ```

## Verification

With the project-level `ansible.cfg` in place:
```bash
ansible webservers -m ping
```**Success Output:**
```json
192.168.8.107 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
```

---
<img width="883" height="209" alt="image" src="https://github.com/user-attachments/assets/f12fe46d-7727-4036-91af-022b1905a6c6" />






