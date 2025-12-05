# sys_loader

Ansible playbooks for provisioning Ubuntu and Manjaro systems.

## Structure

```
├── inventory_ubuntu.ini
├── inventory_manjaro.ini
├── playbook_ubuntu.yml
├── playbook_manjaro.yml
└── roles/
    ├── # Shared (distro-agnostic)
    ├── gitconfig/          # Git user config, hosts file
    ├── nvm/                # Node.js via nvm
    ├── omz/                # Oh My Zsh, Powerlevel10k, plugins
    │
    ├── # Ubuntu-specific
    ├── common_ubuntu/      # apt packages, fonts
    ├── devtools_ubuntu/    # Podman, Distrobox
    ├── regolith_ubuntu/    # Regolith desktop
    ├── v2raya_ubuntu/      # V2Ray proxy
    │
    ├── # Manjaro-specific
    ├── common_manjaro/     # pacman packages
    ├── devtools_manjaro/   # Podman, Distrobox
    └── v2raya_manjaro/     # V2Ray proxy
```

## Usage

### Ubuntu
```bash
ansible-playbook -K -i inventory_ubuntu.ini playbook_ubuntu.yml
```

### Manjaro
```bash
ansible-playbook -K -i inventory_manjaro.ini playbook_manjaro.yml
```

## Docker Testing

### 1. Start a test container

```bash
# Ubuntu
docker run -d --name ubuntu-test --privileged ubuntu:24.04 sleep infinity

# Manjaro
docker run -d --name manjaro-test --privileged manjarolinux/base sleep infinity
```

> Python is bootstrapped automatically via `pre_tasks` in the playbooks.

### 2. Create a docker inventory

```ini
# inventory_docker.ini
[ubuntu]
ubuntu-test ansible_connection=docker ansible_user=root

[manjaro]
manjaro-test ansible_connection=docker ansible_user=root
```

### 3. Run the playbook

```bash
# Ubuntu container
ansible-playbook -i inventory_docker.ini playbook_ubuntu.yml

# Manjaro container
ansible-playbook -i inventory_docker.ini playbook_manjaro.yml
```

### 4. Cleanup

```bash
docker stop ubuntu-test manjaro-test
docker rm ubuntu-test manjaro-test
```

> **Note:** Some tasks (systemd services, user shell changes) won't work in Docker without full init system.
