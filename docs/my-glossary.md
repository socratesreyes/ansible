become: true          -> run task as root (or defined become_user)
gather_facts: true    -> collects system info (OS, IP, etc.) before tasks
state: present        -> ensure it's there; idempotent
state: restarted      -> restart if already running
register: var_name    -> save task output into a variable
when: condition       -> only run task if condition is true
with_items:           -> loop over a list
