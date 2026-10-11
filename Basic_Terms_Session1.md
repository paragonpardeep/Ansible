# Ansible Interview Questions and Answers



Ansible History: 5 Lesser-Known Facts
Quick notes for SRE learning and interview preparation.
1. Created in 2012
Ansible was created by Michael DeHaan in 2012. He had previously worked on infrastructure automation tools such as Cobbler and Func.
2. The Name Comes from Science Fiction
The word “ansible” refers to a fictional device that enables instantaneous communication across vast distances. The term was popularized by author Ursula K. Le Guin.
3. Designed to Be Agentless
Ansible can manage remote Linux systems over SSH without requiring a dedicated Ansible agent to be installed on every managed host. This helps keep setup and maintenance simple.
4. Red Hat Acquired Ansible in 2015
Red Hat acquired Ansible in 2015, helping expand its use in enterprise IT automation.
5. YAML Makes Playbooks Readable
Ansible playbooks use YAML to describe automation tasks in a human-readable format. Ansible modules perform the actual work on managed systems.
Bonus Fact: The Name's Literary Origin
The name “ansible” appeared in Ursula K. Le Guin’s 1966 novel Rocannon’s World.

This file consolidates the most important Ansible interview questions and answers based on the notes and examples in the `Ansible` folder.

## 1. What is Ansible?

Ansible is an open-source automation tool used for configuration management, application deployment, orchestration, and infrastructure automation. It is agentless, uses SSH/WinRM, and works with YAML-based playbooks.

## 2. What is the difference between Ansible and Shell scripting?

- Ansible is declarative and idempotent.
- Shell scripts are imperative and often state-based.
- Ansible can manage multiple hosts at once.
- Ansible tracks tasks and handles retries, error management, and orchestration better.

## 3. What are the main components of Ansible?

- Control Node: machine where Ansible is installed.
- Managed Nodes: remote machines being managed.
- Inventory: list of hosts and groups.
- Modules: reusable units of work.
- Tasks: actions defined in a playbook.
- Plays: mapping of hosts to tasks.
- Playbooks: YAML files that define automation.
- Handlers: tasks triggered by notifications.

## 4. What is an inventory file in Ansible?

An inventory file contains the list of managed hosts and groups. Example:

```ini
[web]
192.168.6.129
192.168.6.130
```

It can also define group variables and host variables.

## 5. What is a playbook?

A playbook is a YAML file that tells Ansible what to do on which hosts. It is written in a declarative style.

```yaml
- name: Example playbook
  hosts: all
  tasks:
    - name: create directory
      file:
        path: /tmp/data
        state: directory
```

## 6. What is the difference between a play and a task?

- A play is a mapping between hosts and tasks.
- A task is an individual action executed by a module.

## 7. What are modules in Ansible?

Modules are small programs that do specific work like file creation, package installation, service management, user creation, etc.

Examples:
- `yum`
- `apt`
- `file`
- `service`
- `copy`
- `template`
- `user`
- `shell`
- `command`

## 8. What is idempotency?

Idempotency means running the same task multiple times produces the same result without changing the system unnecessarily. Example: creating a directory only if it does not exist.

## 9. What is a handler?

A handler is a task that runs only when notified by another task. It is commonly used to restart a service after a config change.

```yaml
- name: update config
  copy:
    src: app.conf
    dest: /etc/app.conf
  notify: restart app

handlers:
  - name: restart app
    service:
      name: httpd
      state: restarted
```

## 10. What is the difference between `include` and `import` in Ansible?

- `include_tasks` includes files dynamically during execution.
- `import_tasks` loads the file before execution begins.
- `import` checks syntax at parse time.
- `include` is more dynamic and often used when conditions decide which files to include.

## 11. What is `when` in Ansible?

`when` conditionally runs a task based on variable values, facts, or logic.

```yaml
- name: install package only on RedHat
  yum:
    name: httpd
    state: installed
  when: ansible_os_family == 'RedHat'
```

## 12. What is `failed_when`?

`failed_when` is used to define custom failure conditions. The task does not fail unless the expression matches.

```yaml
- name: validate memory
  service:
    name: crond
    state: started
  failed_when: ansible_memtotal_mb < 5000
```

## 13. What is `ignore_errors`?

`ignore_errors: yes` tells Ansible to continue running even if the task fails.

```yaml
- name: check service status
  command: systemctl is-active crond
  ignore_errors: yes
  register: xyz
```

This is useful when a failed command is expected and you want to inspect the return code or stdout.

## 14. What is a variable in Ansible?

A variable stores values and allows us to reuse configuration across tasks. Variables can be defined in playbooks, files, inventory, or command line.

```yaml
vars:
  websvc: httpd
  webroot: /var/www/html
```

Use:

```yaml
name: "{{ websvc }}"
```

## 15. What are the types of variables in Ansible?

- String
- List
- Map/dictionary

Examples:

```yaml
vars:
  websvc: httpd
  myusers:
    - ram
    - shyam
  mydata:
    ram:
      uid: 1010
      pw: 1000
```

## 16. How do you define variables from a separate file?

Use `vars_files`:

```yaml
- hosts: all
  vars_files:
    - ./pardeepvars.yml
```

Example file:

```yaml
websvc: httpd
webroot: /var/www/html
mypass: redhat
```

## 17. What are extra variables (`-e`)?

Extra variables are passed during command-line execution.

```bash
ansible-playbook p6.yml -e myuser=pardeep -e mypass=123
```

These override the values defined in the playbook.

## 18. What is `vars_prompt`?

`vars_prompt` asks the user for values when the playbook runs.

```yaml
- hosts: all
  vars_prompt:
    - name: myuser
      prompt: "Please give the user name"
      private: no
    - name: mypass
      prompt: "Please give the password"
      private: yes
```

## 19. What is the difference between playbook variables and inventory variables?

- Playbook variables are for general values in the playbook.
- Inventory variables are useful when values differ by host or group.

Example inventory:

```ini
[web]
192.168.6.129 webroot=/var/www/data

[dev]
192.168.6.130 webroot=/var/www/demo
```

## 20. What are `group_vars` and `host_vars`?

These are special directories used to store host-specific and group-specific variable files.

Example:

```bash
mkdir group_vars
mkdir host_vars
```

Sample files:

```yaml
# group_vars/web.yml
webroot: /var/www/html
ansible_ssh_user: root
ansible_ssh_pass: redhat
```

```yaml
# host_vars/192.168.6.129
webroot: /var/www/data
```

## 21. What is the role of `ansible_ssh_user`, `ansible_ssh_pass`, and `ansible_ssh_port`?

These values are used for SSH connection to managed nodes.

```yaml
ansible_ssh_user: root
ansible_ssh_pass: redhat
ansible_ssh_port: 2222
```

In production, passwords should not be stored in plain text. Use SSH keys or Ansible Vault.

## 22. What is Ansible Vault?

Ansible Vault is used to encrypt sensitive data such as passwords and secrets.

Example:

```bash
ansible-vault create secrets.yml
```

Use:

```bash
ansible-playbook playbook.yml --ask-vault-pass
```

## 23. What are ad hoc commands in Ansible?

Ad hoc commands allow single-task execution without writing a playbook.

Examples:

```bash
ansible all -m ping
ansible web -a 'uptime'
ansible 192.168.6.129 -m service -a 'name=crond state=stopped'
```

## 24. What is `Gathering Facts`?

Ansible collects information about the managed hosts before running tasks. These are stored in `ansible_facts` and include details like OS, hostname, IP address, memory, and disk.

Example:

```bash
ansible web -m setup
```

## 25. What are `facts` in Ansible?

Facts are discovered runtime details of a managed node such as:

- `ansible_hostname`
- `ansible_default_ipv4.address`
- `ansible_os_family`
- `ansible_memtotal_mb`
- `ansible_devices`

## 26. What is Jinja2 templating in Ansible?

Jinja2 is a template engine used to generate dynamic content. Ansible templates use the `.j2` extension.

Example:

```jinja
Welcome to Pardeep Devops Learning
Your server name is {{ ansible_hostname }}
My kernel version is {{ ansible_kernel }}
```

Then use the `template` module:

```yaml
- name: apply the jinja file
  template:
    src: motd.j2
    dest: /etc/motd
```

## 27. Why do we use the `template` module instead of `copy`?

`copy` transfers the same content to all machines. `template` allows dynamic values based on node-specific facts, variables, loops, and conditions.

## 28. What are conditionals in Jinja templates?

Example:

```jinja
{% if ansible_distribution == 'RedHat' %}
your system is redhat family
{% else %}
system are of different family
{% endif %}
```

## 29. What are loops in Jinja templates?

Example:

```jinja
{% for username in myusers %}
your username is {{ username }}
{% endfor %}
```

## 30. How do you gather host details for all machines and write them to `/etc/hosts`?

Use a Jinja template with `groups.all` and `hostvars`.

```jinja
{% for host in groups.all %}
{{ hostvars[host].ansible_default_ipv4.address }} {{ hostvars[host].ansible_hostname }}
{% endfor %}
```

Playbook:

```yaml
- hosts: all
  tasks:
    - name: copy host file template
      template:
        src: exam.j2
        dest: /etc/hosts
```

## 31. What is a YAML file and why is it used in Ansible?

YAML is a human-readable data serialization format. It is used in Ansible because it is simple to read and write, and it fits the declarative structure of playbooks.

YAML supports:
- string values
- list values
- mappings/dictionaries

## 32. What is a playbook execution output showing `changed`, `ok`, and `failed`?

- `ok`: task succeeded and no changes were needed.
- `changed`: task made a modification.
- `failed`: task failed.
- `skipped`: task was skipped due to conditions.

Example from notes:

```text
TASK [file] **********************************************************************
changed: [43.204.211.100]
```

## 33. What is the role of `gather_facts: false`?

By default Ansible gathers facts. To skip this step, set:

```yaml
- hosts: all
  gather_facts: false
```

This may be used when facts are not required or to speed up execution.

## 34. What is a multi-play playbook?

A playbook can contain multiple plays, each targeting different hosts or groups.

```yaml
- hosts: web
  tasks:
    - file:
        path: /tmp/data
        state: directory

- hosts: dev
  tasks:
    - group:
        name: ibm
        state: present
```

## 35. What is the use of `service` module?

The `service` module is used to start, stop, enable, or restart system services.

```yaml
- name: start crond service
  service:
    name: crond
    state: started
```

## 36. What is `copy` vs `template` vs `file` module?

- `file`: create directories, files, symlinks, etc.
- `copy`: copy a static file from control node to remote hosts.
- `template`: copy a file while processing Jinja variables and logic.

## 37. What is the importance of Ansible in DevOps?

Ansible is essential in DevOps because it helps automate:
- server provisioning
- package installation
- configuration management
- application deployment
- patching and maintenance
- infrastructure-as-code workflows

## 38. Why is Ansible preferred for automation?

- Agentless architecture
- Simple YAML syntax
- Easy to read and maintain
- Great for multi-node automation
- Works well with CI/CD workflows
- Designed for idempotency and repeatable deployments

## 39. What is the challenge of plain-text passwords in inventory or variables?

Passwords stored in plain text are risky and not recommended. Instead:
- use SSH keys
- use Ansible Vault
- use encrypted credentials in secret stores

## 40. How do you create a secure password in Ansible?

Use password hashing:

```yaml
user:
  name: "{{ myuser }}"
  password: "{{ mypass | password_hash('sha512') }}"
```

This stores the password in hashed form in `/etc/shadow`.

## 41. What is the difference between `command` and `shell` modules?

- `command`: executes a command without shell processing.
- `shell`: executes through the shell and supports redirection, pipes, and environment variables.

Use `command` when possible because it is safer and more predictable.

## 42. What is `register` in Ansible?

`register` stores the output of a task for later use.

```yaml
- name: check service status
  command: systemctl is-active crond
  ignore_errors: yes
  register: xyz
```

Then you can use:

```yaml
- debug:
    var: xyz.rc
```

## 43. What is `block`, `rescue`, and `always`?

These are used for structured error handling.

```yaml
- block:
    - name: run app setup
      command: somecommand
  rescue:
    - name: rollback or notify
      debug:
        msg: "Failure handled"
  always:
    - name: cleanup
      debug:
        msg: "Cleanup always runs"
```

This helps manage failures in a controlled manner.

## 44. What is the difference between `copy` and `fetch`?

- `copy`: copies files from control node to managed nodes.
- `fetch`: pulls files from remote host to control node.

## 45. What is a static inventory vs dynamic inventory?

- Static inventory: host list is defined in a file.
- Dynamic inventory: hosts are discovered from cloud providers or external sources at runtime.

## 46. How do you test syntax of a playbook?

```bash
ansible-playbook playbook.yml --syntax-check
```

## 47. How do you run a playbook in dry-run mode?

```bash
ansible-playbook playbook.yml -C
```

This is a check mode where changes are not applied.

## 48. How do you run a playbook step by step?

```bash
ansible-playbook playbook.yml --step
```

This prompts before each task execution.

## 49. What is an Ansible collection?

Ansible Collections are packaged sets of roles, modules, plugins, and documentation from different vendors or communities.

## 50. What is an Ansible role?

A role is a structured way to organize playbooks and tasks into reusable components.

Typical folder structure:

```text
roles/
  web/
    tasks/
    handlers/
    templates/
    vars/
```

## 51. What are the common modules used in interview questions?

- `yum` / `apt`
- `service`
- `copy`
- `file`
- `user`
- `group`
- `debug`
- `shell`
- `command`
- `template`
- `stat`
- `mount`
- `cron`
- `lineinfile`

## 52. What is `lineinfile` used for?

It is used to manage content in a file by inserting, updating, or deleting lines.

```yaml
- lineinfile:
    path: /etc/hosts
    line: '192.168.6.130 ManageNode2'
```

## 53. What is a `stat` module used for?

It checks whether a file or path exists.

```yaml
- stat:
    path: /dev/sdb
  register: xyz
```

You can then use the result in a conditional.

## 54. What is the purpose of `debug` module?

It prints messages or variable values for troubleshooting.

```yaml
- debug:
    var: webroot
```

## 55. What is the difference between `name` and `hosts` in a playbook?

- `name`: human-readable label for the play
- `hosts`: target machines/groups to run against

## 56. What does `hosts: all` mean?

It means the play runs on every host defined in the inventory.

## 57. What is inventory grouping?

Hosts can be grouped logically, for example:

```ini
[web]
192.168.6.129

[db]
192.168.6.130
```

This makes it easy to target different tasks to different sets of machines.

## 58. What is the benefit of automation in a DevOps environment?

Automation reduces manual work, prevents human errors, improves consistency, speeds up deployments, and ensures repeatable infrastructure management.

## 59. What is the role of Ansible in configuration management?

It ensures the desired state of services, files, users, packages, and configurations remains consistent across all hosts.

## 60. What are the most important interview topics for Ansible?

- Ansible architecture
- Inventory and variables
- Playbooks and YAML
- Modules and tasks
- Handlers
- Templates/Jinja2
- Facts and conditions
- Error handling
- Vault and security
- Ad hoc commands
- Roles and reusable automation

## Summary

Ansible is a powerful automation tool widely used for configuration management, system provisioning, and deployment tasks. The most important interview skills are understanding YAML playbooks, inventory structure, variables, templates, conditions, loops, handlers, and error-handling patterns.

This file covers the major concepts taught in the repository notes and is suitable for interview preparation.

---

Prepared for the `parkmr027/Devops` Ansible learning repository.
