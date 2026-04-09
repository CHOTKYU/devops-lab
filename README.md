# DevOps Lab — Vagrant + Vault + Jenkins + Zabbix

Single VM deployment with automated provisioning via Ansible.

## Requirements

- VirtualBox
- Vagrant
- Git

## Quick Start

### 1. Clone the repository

```bash
git clone <your-repo-url>
cd devops-lab
```

### 2. Add to your hosts file

On Windows open `C:\Windows\System32\drivers\etc\hosts` as Administrator and add:
192.168.56.10  vault.local jenkins.local zabbix.local

### 3. Start the VM

```bash
vagrant up
```

First run takes 10-15 minutes — downloads Debian image and installs all services.

## Access Services

| Service | URL | Credentials |
|---------|-----|-------------|
| Vault   | http://vault.local:8200 | Token: `root` (see `/opt/vault/vault-init.json`) |
| Jenkins | http://jenkins.local | `admin` / `admin123` |
| Zabbix  | http://zabbix.local | `Admin` / `zabbix` |

## How to write a secret in Vault

### Via Web UI
1. Open http://vault.local:8200
2. Login with token from `/opt/vault/vault-init.json`
3. Go to **Secrets** → **secret** → **Create secret**
4. Enter path: `myapp/config`
5. Add key-value: `db_password` = `supersecret`
6. Click **Save**

### Via CLI (inside VM)
```bash
vagrant ssh
export VAULT_ADDR="http://127.0.0.1:8200"
export VAULT_TOKEN="<token from /opt/vault/vault-init.json>"
vault kv put secret/myapp db_password="supersecret"
vault kv get secret/myapp
```

## How to create a job in Jenkins

1. Open http://jenkins.local
2. Login: `admin` / `admin123`
3. Click **New Item**
4. Enter job name, select **Freestyle project** → OK
5. Under **Source Code Management** select **Git**
6. Enter repository URL: `https://github.com/your/repo.git`
7. Click **Save** → **Build Now**

## Zabbix Monitoring

Zabbix monitors the VM itself with these metrics:
- CPU utilization
- Memory usage
- Disk usage
- Service status: Vault, Jenkins, Zabbix Server

### Check service status manually (inside VM)
```bash
vagrant ssh
/etc/zabbix/service_check.sh vault          # returns 1=running, 0=stopped
/etc/zabbix/service_check.sh jenkins        # returns 1=running, 0=stopped
/etc/zabbix/service_check.sh zabbix-server  # returns 1=running, 0=stopped
```

## Useful Vagrant commands

```bash
vagrant up        # start VM
vagrant halt      # stop VM
vagrant reload    # restart VM
vagrant ssh       # connect to VM
vagrant destroy   # delete VM completely
vagrant provision # re-run provisioning
```

## Security Notes

> **Important:** Vault unseal key is stored at `/opt/vault/vault-init.json` inside the VM.
> This is acceptable for a dev environment only.
> In production use AWS KMS, Azure Key Vault, or distributed unseal keys.