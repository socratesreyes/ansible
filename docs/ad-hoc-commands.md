# Ansible Ad-Hoc Commands Reference


**Purpose:** Quick reference for running one-off commands across multiple servers without writing a playbook.  

**Playlist Reference:** Ansible 101 (Ad-hoc tasks & Inventory)


## Syntax Reminder

modules
https://docs.ansible.com/projects/ansible-core/2.13/collections/ansible/builtin/index.html
or 
ansible-doc -l or ansible-doc <module>


```bash
ansible <host-pattern> -m <module> -a "<command>" -i <inventory-file>
```
| Argument | Purpose | Example |
| :--- | :--- | :--- |
| `<host-pattern>` | Target group (e.g., `webservers`, `all`, `*.example.com`) | `webservers` |
| `-m <module>` | Module to use (`command` is default, `shell` for advanced) | `-m shell` |
| `-a "<command>"` | The actual Linux command to execute | `-a "free -h"` |
| `-i <inventory>` | Path to inventory file (optional if set in `ansible.cfg`) | `-i inventory/lab-hosts.ini` |


> **Important Distinction:** `-m command` (the default) does NOT support pipes (`|`), redirects (`>`), or shell variables. Use `-m shell` if you need those.


## System Health Checks

### Memory Usage
**Command:**
```bash
ansible webservers -a "free -h"
```
**Output:**
```
192.168.8.107 | CHANGED | rc=0 >>
               total        used        free      shared  buff/cache   available
Mem:           3.8Gi       689Mi       3.0Gi       6.0Mi       332Mi       3.2Gi
Swap:          2.0Gi          0B       2.0Gi
```
**Use Case:** Quick memory check across all web servers during a performance incident.

### Disk Usage
**Command:**
```bash
ansible webservers -a "df -h"
```
**Output:**
```
192.168.8.107 | CHANGED | rc=0 >>
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda1        20G   12G  7.2G  63% /
```
**Use Case:** Check for full partitions across your entire infrastructure.

---

### Uptime & Load Average
**Command:**
```bash
ansible webservers -a "uptime"
```
**Output:**
```
192.168.8.107 | CHANGED | rc=0 >>
 14:32:17 up 3 days,  2:15,  1 user,  load average: 0.08, 0.03, 0.01
```
**Use Case:** Identify which servers have been restarted recently or are experiencing high load.

---

## Service Management

### Check Service Status (Nginx)
**Command:**
```bash
ansible webservers -m shell -a "systemctl status nginx | grep Active"
```
**Output:**
```
192.168.8.107 | CHANGED | rc=0 >>
   Active: active (running) since Mon 2026-07-20 10:00:00 UTC
```
**Use Case:** Quick health check of critical services across the fleet.

### Restart a Service (with confirmation)
**Command:**
```bash
ansible webservers -m shell -a "systemctl restart nginx && echo 'Restart successful'"
```
**Output:**
```
192.168.8.107 | CHANGED | rc=0 >>
Restart successful
```
**Use Case:** Rolling restarts during maintenance windows.

---

## File & Log Management

### Find Large Files (>100MB) in /var/log
**Command:**
```bash
ansible webservers -m shell -a "find /var/log -type f -size +100M -exec ls -lh {} \; 2>/dev/null"
```
**Use Case:** Find rogue log files filling up disk space.

### Check for Specific Errors in Logs
**Command:**
```bash
ansible webservers -m shell -a "tail -100 /var/log/nginx/error.log | grep -i 'connect refused'"
```
**Use Case:** Debug connectivity issues across multiple load balancers.

---

## User & Permission Management

### Check Which Users Have Sudo Access
**Command:**
```bash
ansible webservers -m shell -a "getent group sudo | cut -d: -f4"
```
**Use Case:** Audit sudo privileges across your infrastructure.

---

## Package Management (Ubuntu/Debian)

### Check All Installed Packages
**Command:**
```bash
ansible webservers -a "dpkg -l | wc -l"
```
**Output:**
```
192.168.8.107 | CHANGED | rc=0 >>
304
```
**Use Case:** Count the number of installed packages on a server.


