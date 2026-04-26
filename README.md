# SSH Cheat Sheet

Secure Shell (SSH) protocol for secure remote login and command execution.

---

## Table of Contents
- [Basic Connection](#basic-connection)
- [Key Management](#key-management)
- [Port Forwarding](#port-forwarding)
- [File Transfer](#file-transfer)
- [SSH Config](#ssh-config)
- [Advanced Options](#advanced-options)
- [Troubleshooting](#troubleshooting)

---

## Basic Connection

| Command | Description | Example |
|---------|-------------|---------|
| `ssh user@host` | Connect to host | `ssh john@192.168.1.1` |
| `ssh -p port user@host` | Connect using specific port | `ssh -p 2222 john@server.com` |
| `ssh -i keyfile user@host` | Connect using private key | `ssh -i ~/.ssh/id_rsa john@server.com` |
| `ssh user@host command` | Execute remote command | `ssh john@server.com ls -la` |
| `ssh -v user@host` | Verbose mode (debugging) | `ssh -v john@server.com` |
| `ssh -C user@host` | Enable compression | `ssh -C john@server.com` |
| `ssh -X user@host` | Enable X11 forwarding | `ssh -X john@server.com` |

## Key Management

| Command | Description | Example |
|---------|-------------|---------|
| `ssh-keygen` | Generate SSH key pair | `ssh-keygen -t rsa -b 4096` |
| `ssh-keygen -t ed25519` | Generate Ed25519 key (recommended) | `ssh-keygen -t ed25519 -C "email@example.com"` |
| `ssh-copy-id user@host` | Copy public key to server | `ssh-copy-id john@server.com` |
| `ssh-add keyfile` | Add key to SSH agent | `ssh-add ~/.ssh/id_rsa` |
| `ssh-add -l` | List keys in SSH agent | `ssh-add -l` |
| `ssh-add -D` | Remove all keys from agent | `ssh-add -D` |
| `ssh-keygen -p -f keyfile` | Change key passphrase | `ssh-keygen -p -f ~/.ssh/id_rsa` |
| `ssh-keygen -R hostname` | Remove host from known_hosts | `ssh-keygen -R server.com` |

## Port Forwarding

### Local Port Forwarding
- `ssh -L local_port:remote_host:remote_port user@ssh_server`
- Forward local port to remote host through SSH server
- Example: `ssh -L 8080:localhost:80 john@server.com`

### Remote Port Forwarding
- `ssh -R remote_port:local_host:local_port user@ssh_server`
- Forward remote port to local host
- Example: `ssh -R 9090:localhost:3000 john@server.com`

### Dynamic Port Forwarding (SOCKS Proxy)
- `ssh -D local_port user@ssh_server`
- Create SOCKS proxy
- Example: `ssh -D 1080 john@server.com`

## File Transfer

| Command | Description | Example |
|---------|-------------|---------|
| `scp file user@host:path` | Copy file to remote | `scp file.txt john@server.com:/home/john/` |
| `scp user@host:file .` | Copy file from remote | `scp john@server.com:/home/john/file.txt .` |
| `scp -r dir user@host:path` | Copy directory recursively | `scp -r mydir john@server.com:/home/john/` |
| `scp -P port file user@host:` | SCP with custom port | `scp -P 2222 file.txt john@server.com:` |
| `rsync -avz file user@host:path` | Sync files (better for large transfers) | `rsync -avz mydir/ john@server.com:/backup/` |
| `sftp user@host` | Interactive SFTP session | `sftp john@server.com` |

## SSH Config

Create `~/.ssh/config` for connection shortcuts:

```
Host myserver
    HostName server.example.com
    User john
    Port 2222
    IdentityFile ~/.ssh/id_rsa_server

Host tunnel
    HostName tunnel.example.com
    User admin
    LocalForward 8080 localhost:80
    DynamicForward 1080

Host *
    ServerAliveInterval 60
    ServerAliveCountMax 3
```

### Using SSH Config
- `ssh myserver` - Connect using config alias
- `scp file myserver:` - SCP using config alias

## Advanced Options

| Command | Description | Example |
|---------|-------------|---------|
| `ssh -J jumphost user@target` | Connect via jump host | `ssh -J bastion@jump.com john@internal.server` |
| `ssh -N -f user@host` | Background connection (no shell) | `ssh -N -f -L 8080:localhost:80 john@server.com` |
| `ssh -o option user@host` | Set SSH option | `ssh -o "StrictHostKeyChecking=no" john@server.com` |
| `ssh -A user@host` | Enable agent forwarding | `ssh -A john@server.com` |
| `ssh -t user@host command` | Force TTY allocation | `ssh -t john@server.com vim file.txt` |
| `ssh -q user@host` | Quiet mode | `ssh -q john@server.com` |

## Troubleshooting

### Common SSH Options for Debugging
- `-v` - Verbose mode (use -vv or -vvv for more detail)
- `-o "LogLevel=DEBUG3"` - Maximum debug output
- `-o "ConnectTimeout=10"` - Set connection timeout
- `-o "StrictHostKeyChecking=no"` - Skip host key verification (insecure)
- `-o "UserKnownHostsFile=/dev/null"` - Don't save host key

### Check SSH Service
```bash
# Check if SSH is running
systemctl status sshd       # SystemD
service ssh status          # SysV

# Restart SSH service
sudo systemctl restart sshd
sudo service ssh restart
```

### Common Issues and Solutions

| Issue | Solution |
|-------|----------|
| Permission denied (publickey) | Check key permissions: `chmod 600 ~/.ssh/id_rsa` |
| Host key verification failed | Remove old key: `ssh-keygen -R hostname` |
| Connection refused | Check if SSH service is running on server |
| Connection timeout | Check firewall rules and network connectivity |
| Too many authentication failures | Specify key: `ssh -o "IdentitiesOnly=yes" -i keyfile user@host` |

### SSH Agent Issues
```bash
# Start SSH agent
eval "$(ssh-agent -s)"

# Check agent is running
echo $SSH_AUTH_SOCK

# Add key to agent
ssh-add ~/.ssh/id_rsa
```

---

## Security Best Practices

1. **Use key-based authentication** instead of passwords
2. **Disable root login** in `/etc/ssh/sshd_config`
3. **Use Ed25519 or RSA keys** with at least 2048 bits
4. **Keep private keys secure** with proper permissions (600)
5. **Use SSH agent forwarding carefully** - can be security risk
6. **Enable two-factor authentication** when possible
7. **Regularly update SSH software** and keys
8. **Use fail2ban** or similar to prevent brute force attacks

---

## Resources
- [OpenSSH Documentation](https://www.openssh.com/manual.html)
- [SSH Academy](https://www.ssh.com/academy/ssh)
- [SSH Config Manual](https://man.openbsd.org/ssh_config)

---
*Essential SSH commands for secure remote access and file transfer.*