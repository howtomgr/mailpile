# mailpile Installation Guide

mailpile is a free and open-source email client. Mailpile provides fast, secure webmail client with encryption focus

## Table of Contents
1. [Prerequisites](#prerequisites)
2. [Supported Operating Systems](#supported-operating-systems)
3. [Installation](#installation)
4. [Configuration](#configuration)
5. [Service Management](#service-management)
6. [Troubleshooting](#troubleshooting)
7. [Security Considerations](#security-considerations)
8. [Performance Tuning](#performance-tuning)
9. [Backup and Restore](#backup-and-restore)
10. [System Requirements](#system-requirements)
11. [Support](#support)
12. [Contributing](#contributing)
13. [License](#license)
14. [Acknowledgments](#acknowledgments)
15. [Version History](#version-history)
16. [Appendices](#appendices)

## 1. Prerequisites

- **Hardware Requirements**:
  - CPU: 1 core minimum
  - RAM: 512MB minimum
  - Storage: 1GB for mail
  - Network: HTTP/HTTPS access
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port 33411 (default mailpile port)
  - None
- **Dependencies**:
  - See official documentation for specific requirements
- **System Access**: root or sudo privileges required


## 2. Supported Operating Systems

This guide supports installation on:
- RHEL 8/9 and derivatives (CentOS Stream, Rocky Linux, AlmaLinux)
- Debian 11/12
- Ubuntu 20.04/22.04/24.04 LTS
- Arch Linux (rolling release)
- Alpine Linux 3.18+
- openSUSE Leap 15.5+ / Tumbleweed
- SUSE Linux Enterprise Server (SLES) 15+
- macOS 12+ (Monterey and later) 
- FreeBSD 13+
- Windows 10/11/Server 2019+ (where applicable)

## 3. Installation

### RHEL/CentOS/Rocky Linux/AlmaLinux

```bash
# Install EPEL repository if needed
sudo dnf install -y epel-release

# Install mailpile
sudo dnf install -y mailpile

# Enable and start service
sudo systemctl enable --now mailpile

# Configure firewall
sudo firewall-cmd --permanent --add-port=33411/tcp
sudo firewall-cmd --reload

# Verify installation
mailpile --version
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install mailpile
sudo apt install -y mailpile

# Enable and start service
sudo systemctl enable --now mailpile

# Configure firewall
sudo ufw allow 33411

# Verify installation
mailpile --version
```

### Arch Linux

```bash
# Install mailpile
sudo pacman -S mailpile

# Enable and start service
sudo systemctl enable --now mailpile

# Verify installation
mailpile --version
```

### Alpine Linux

```bash
# Install mailpile
apk add --no-cache mailpile

# Enable and start service
rc-update add mailpile default
rc-service mailpile start

# Verify installation
mailpile --version
```

### openSUSE/SLES

```bash
# Install mailpile
sudo zypper install -y mailpile

# Enable and start service
sudo systemctl enable --now mailpile

# Configure firewall
sudo firewall-cmd --permanent --add-port=33411/tcp
sudo firewall-cmd --reload

# Verify installation
mailpile --version
```

### macOS

```bash
# Using Homebrew
brew install mailpile

# Start service
brew services start mailpile

# Verify installation
mailpile --version
```

### FreeBSD

```bash
# Using pkg
pkg install mailpile

# Enable in rc.conf
echo 'mailpile_enable="YES"' >> /etc/rc.conf

# Start service
service mailpile start

# Verify installation
mailpile --version
```

### Windows

```bash
# Using Chocolatey
choco install mailpile

# Or using Scoop
scoop install mailpile

# Verify installation
mailpile --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory
sudo mkdir -p /etc/mailpile

# Set up basic configuration
# See official documentation for detailed configuration options

# Test configuration
mailpile --version
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable mailpile

# Start service
sudo systemctl start mailpile

# Stop service
sudo systemctl stop mailpile

# Restart service
sudo systemctl restart mailpile

# Check status
sudo systemctl status mailpile

# View logs
sudo journalctl -u mailpile -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add mailpile default

# Start service
rc-service mailpile start

# Stop service
rc-service mailpile stop

# Restart service
rc-service mailpile restart

# Check status
rc-service mailpile status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'mailpile_enable="YES"' >> /etc/rc.conf

# Start service
service mailpile start

# Stop service
service mailpile stop

# Restart service
service mailpile restart

# Check status
service mailpile status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start mailpile
brew services stop mailpile
brew services restart mailpile

# Check status
brew services list | grep mailpile
```

### Windows Service Manager

```powershell
# Start service
net start mailpile

# Stop service
net stop mailpile

# Using PowerShell
Start-Service mailpile
Stop-Service mailpile
Restart-Service mailpile

# Check status
Get-Service mailpile
```

## Advanced Configuration

See the official documentation for advanced configuration options.

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream mailpile_backend {
    server 127.0.0.1:33411;
}

server {
    listen 80;
    server_name mailpile.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name mailpile.example.com;

    ssl_certificate /etc/ssl/certs/mailpile.example.com.crt;
    ssl_certificate_key /etc/ssl/private/mailpile.example.com.key;

    location / {
        proxy_pass http://mailpile_backend;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

### Apache Configuration

```apache
<VirtualHost *:80>
    ServerName mailpile.example.com
    Redirect permanent / https://mailpile.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName mailpile.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/mailpile.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/mailpile.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:33411/
    ProxyPassReverse / http://127.0.0.1:33411/
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend mailpile_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/mailpile.pem
    redirect scheme https if !{ ssl_fc }
    default_backend mailpile_backend

backend mailpile_backend
    balance roundrobin
    server mailpile1 127.0.0.1:33411 check
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R mailpile:mailpile /etc/mailpile
sudo chmod 750 /etc/mailpile

# Configure firewall
sudo firewall-cmd --permanent --add-port=33411/tcp
sudo firewall-cmd --reload

# Enable SELinux policies (if applicable)
sudo setsebool -P httpd_can_network_connect on
```

## Database Setup

See official documentation for database configuration requirements.

## Performance Optimization

### System Tuning

```bash
# Basic system tuning
echo 'net.core.somaxconn = 65535' | sudo tee -a /etc/sysctl.conf
echo 'net.ipv4.tcp_max_syn_backlog = 65535' | sudo tee -a /etc/sysctl.conf
sudo sysctl -p
```

## Monitoring

### Basic Monitoring

```bash
# Check service status
sudo systemctl status mailpile

# View logs
sudo journalctl -u mailpile -f

# Monitor resource usage
top -p $(pgrep mailpile)
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Basic backup script
BACKUP_DIR="/backup/mailpile"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/mailpile-backup-$DATE.tar.gz" /etc/mailpile /var/lib/mailpile

echo "Backup completed: $BACKUP_DIR/mailpile-backup-$DATE.tar.gz"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop mailpile

# Restore from backup
tar -xzf /backup/mailpile/mailpile-backup-*.tar.gz -C /

# Start service
sudo systemctl start mailpile
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u mailpile -n 100
sudo tail -f /var/log/mailpile/mailpile.log

# Check configuration
mailpile --version

# Check permissions
ls -la /etc/mailpile
```

2. **Connection issues**:
```bash
# Check if service is listening
sudo ss -tlnp | grep 33411

# Test connectivity
telnet localhost 33411

# Check firewall
sudo firewall-cmd --list-all
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep mailpile)

# Check disk I/O
iotop -p $(pgrep mailpile)

# Check connections
ss -an | grep 33411
```

## Integration Examples

### Docker Compose Example

```yaml
version: '3.8'
services:
  mailpile:
    image: mailpile:latest
    ports:
      - "33411:33411"
    volumes:
      - ./config:/etc/mailpile
      - ./data:/var/lib/mailpile
    restart: unless-stopped
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update mailpile

# Debian/Ubuntu
sudo apt update && sudo apt upgrade mailpile

# Arch Linux
sudo pacman -Syu mailpile

# Alpine Linux
apk update && apk upgrade mailpile

# openSUSE
sudo zypper update mailpile

# FreeBSD
pkg update && pkg upgrade mailpile

# Always backup before updates
tar -czf /backup/mailpile-pre-update-$(date +%Y%m%d).tar.gz /etc/mailpile

# Restart after updates
sudo systemctl restart mailpile
```

### Regular Maintenance

```bash
# Log rotation
sudo logrotate -f /etc/logrotate.d/mailpile

# Clean old logs
find /var/log/mailpile -name "*.log" -mtime +30 -delete

# Check disk usage
du -sh /var/lib/mailpile
```

## Additional Resources

- Official Documentation: https://docs.mailpile.org/
- GitHub Repository: https://github.com/mailpile/mailpile
- Community Forum: https://forum.mailpile.org/
- Best Practices Guide: https://docs.mailpile.org/best-practices

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
