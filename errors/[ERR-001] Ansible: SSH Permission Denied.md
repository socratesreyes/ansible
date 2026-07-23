# [ERR-001] Ansible: SSH Permission Denied (Publickey/Password)

**Environment:** CentOS Control Node → Ubuntu Managed Node (192.168.8.107)
## Error

```bash
ansible webservers -m ping -u soc -i /etc/ansible/hosts

<img width="902" height="179" alt="image" src="https://github.com/user-attachments/assets/b11f5bda-c53b-4b27-8aad-440f4362a904" />

**Output:**
```json
192.168.8.107 | UNREACHABLE! => {
    "changed": false,
    "msg": "Failed to connect to the host via ssh: soc@192.168.8.107: Permission denied (publickey,password).",
    "unreachable": true
}
```

## Root Cause

SSH key authentication was not configured between the control node (CentOS) and the managed node (Ubuntu) for user `soc`.
public key was not copied to the target host's `~/.ssh/authorized_keys`.


## Solution
```bash
ssh-keygen -t ed25519 -C "ansible-control-node"
```

Configure `ansible.cfg`
[defaults]
inventory = inventory          # Path to my inventory file
remote_user = soc              # Default SSH user
private_key_file = ~/.ssh/id_ed25519   # Explicitly point to the private key
host_key_checking = False      # Skip host key verification (lab environment)


```json
192.168.8.107 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```

