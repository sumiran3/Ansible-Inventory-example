# Ansible Static Inventory Example

A sample `inventory.ini` demonstrating host variables, group variables, and
nested (`:children`) groups — built for RHCE (EX294) practice and general
Ansible homelab use.

## 📁 Files

| File            | Description                                  |
|-----------------|-----------------------------------------------|
| `inventory.ini` | Static INI-style Ansible inventory            |

## 🗂 Inventory Structure

```
datacenter (parent group)
├── webservers
│   ├── web01  → 192.168.1.11
│   └── web02  → 192.168.1.12
├── dbservers
│   ├── db01   → 192.168.1.21
│   └── db02   → 192.168.1.22
└── appservers
    ├── app01  → 192.168.1.31
    └── app02  → 192.168.1.32
```

## 🖥 Groups & Hosts

### `[webservers]`
| Host  | IP            | FQDN                    |
|-------|---------------|--------------------------|
| web01 | 192.168.1.11  | web01.example.local      |
| web02 | 192.168.1.12  | web02.example.local      |

### `[dbservers]`
| Host  | IP            | FQDN                    |
|-------|---------------|--------------------------|
| db01  | 192.168.1.21  | db01.example.local       |
| db02  | 192.168.1.22  | db02.example.local       |

### `[appservers]`
| Host  | IP            | SSH User | SSH Port | SSH Key                  |
|-------|---------------|----------|----------|---------------------------|
| app01 | 192.168.1.31  | devops   | 2222     | `~/.ssh/app_key`          |
| app02 | 192.168.1.32  | devops   | 2222     | `~/.ssh/app_key`          |

## ⚙️ Group Variables

| Group        | Variables                                          |
|--------------|-----------------------------------------------------|
| `webservers` | `http_port=80`, `ansible_user=ansible`, key: `id_rsa` |
| `dbservers`  | `db_port=5432`, `ansible_user=ansible`              |
| `datacenter` | `ansible_python_interpreter=/usr/bin/python3`       |

## 🌳 Parent Group (`:children`)

```ini
[datacenter:children]
webservers
dbservers
appservers
```

Combines all three groups under one target (`datacenter`), while each
sub-group can still be targeted independently.

## 🚀 Usage

Clone the repo and run any of the following from the project root:

```bash
# List the full inventory as parsed by Ansible
ansible-inventory -i inventory.ini --list

# Ping every host in the parent group
ansible datacenter -i inventory.ini -m ping

# Ping only the web servers
ansible webservers -i inventory.ini -m ping

# List hosts in a group without connecting
ansible appservers -i inventory.ini --list-hosts

# Check FQDN fact gathered at runtime
ansible webservers -i inventory.ini -m setup -a "filter=ansible_fqdn"
```

## 📝 Notes

- `ansible_host` sets the actual IP/DNS Ansible connects to, separate from
  the inventory hostname (e.g. `web01`).
- `ansible_fqdn` here is a **custom** variable for reference; the FQDN fact
  Ansible gathers at runtime is `ansible_facts['fqdn']`.
- `appservers` uses a non-default SSH port (`2222`) and a dedicated private
  key, kept separate from the `webservers`/`dbservers` key (`id_rsa`).
- Group vars cascade down through `:children` — so `datacenter:vars`
  applies to all six hosts across all three groups.

## 📋 Requirements

- Ansible ≥ 2.14
- SSH access configured for each host/group as defined above

## 📄 License

MIT
