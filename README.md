# Dovecot Installation Guide

Dovecot is a free and open-source Mail Server. An open-source IMAP and POP3 server for Unix-like systems

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
  - CPU: 2 cores minimum (4+ cores recommended)
  - RAM: 2GB minimum (4GB+ recommended)
  - Storage: 1GB for installation
  - Network: 143/993 ports
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port 143/993 (default dovecot port)
- **Dependencies**:
  - dovecot-imapd, dovecot-pop3d, dovecot-lmtpd
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

# Install dovecot
sudo dnf install -y dovecot dovecot-imapd, dovecot-pop3d, dovecot-lmtpd

# Enable and start service
sudo systemctl enable --now dovecot

# Configure firewall
sudo firewall-cmd --permanent --add-service=dovecot
sudo firewall-cmd --reload

# Verify installation
dovecot --version || systemctl status dovecot
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install dovecot
sudo apt install -y dovecot dovecot-imapd, dovecot-pop3d, dovecot-lmtpd

# Enable and start service
sudo systemctl enable --now dovecot

# Configure firewall
sudo ufw allow 143/993

# Verify installation
dovecot --version || systemctl status dovecot
```

### Arch Linux

```bash
# Install dovecot
sudo pacman -S dovecot

# Enable and start service
sudo systemctl enable --now dovecot

# Verify installation
dovecot --version || systemctl status dovecot
```

### Alpine Linux

```bash
# Install dovecot
apk add --no-cache dovecot

# Enable and start service
rc-update add dovecot default
rc-service dovecot start

# Verify installation
dovecot --version || rc-service dovecot status
```

### openSUSE/SLES

```bash
# Install dovecot
sudo zypper install -y dovecot dovecot-imapd, dovecot-pop3d, dovecot-lmtpd

# Enable and start service
sudo systemctl enable --now dovecot

# Configure firewall
sudo firewall-cmd --permanent --add-service=dovecot
sudo firewall-cmd --reload

# Verify installation
dovecot --version || systemctl status dovecot
```

### macOS

```bash
# Using Homebrew
brew install dovecot

# Start service
brew services start dovecot

# Verify installation
dovecot --version
```

### FreeBSD

```bash
# Using pkg
pkg install dovecot

# Enable in rc.conf
echo 'dovecot_enable="YES"' >> /etc/rc.conf

# Start service
service dovecot start

# Verify installation
dovecot --version || service dovecot status
```

### Windows

```powershell
# Using Chocolatey
choco install dovecot

# Or using Scoop
scoop install dovecot

# Verify installation
dovecot --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory if needed
sudo mkdir -p /etc/dovecot

# Set up basic configuration
sudo tee /etc/dovecot/dovecot.conf << 'EOF'
# Dovecot Configuration
mail_max_userip_connections = 50, mail_plugins = $mail_plugins zlib
EOF

# Test configuration
sudo dovecot -t || sudo dovecot configtest

# Reload service
sudo systemctl reload dovecot
```

### Security Hardening

```bash
# Set appropriate permissions
sudo chown -R dovecot:dovecot /etc/dovecot
sudo chmod 750 /etc/dovecot

# Enable security features
# See security section for detailed hardening steps
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable dovecot

# Start service
sudo systemctl start dovecot

# Stop service
sudo systemctl stop dovecot

# Restart service
sudo systemctl restart dovecot

# Reload configuration
sudo systemctl reload dovecot

# Check status
sudo systemctl status dovecot

# View logs
sudo journalctl -u dovecot -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add dovecot default

# Start service
rc-service dovecot start

# Stop service
rc-service dovecot stop

# Restart service
rc-service dovecot restart

# Check status
rc-service dovecot status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'dovecot_enable="YES"' >> /etc/rc.conf

# Start service
service dovecot start

# Stop service
service dovecot stop

# Restart service
service dovecot restart

# Check status
service dovecot status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start dovecot
brew services stop dovecot
brew services restart dovecot

# Check status
brew services list | grep dovecot
```

### Windows Service Manager

```powershell
# Start service
net start dovecot

# Stop service
net stop dovecot

# Using PowerShell
Start-Service dovecot
Stop-Service dovecot
Restart-Service dovecot

# Check status
Get-Service dovecot
```

## Advanced Configuration

### Performance Optimization

```bash
# Configure performance settings
cat >> /etc/dovecot/dovecot.conf << 'EOF'
mail_max_userip_connections = 50, mail_plugins = $mail_plugins zlib
EOF

# Apply system tuning
sudo sysctl -w net.core.somaxconn=65535
sudo sysctl -w net.ipv4.tcp_max_syn_backlog=65535

# Restart service
sudo systemctl restart dovecot
```

### Clustering and High Availability

```bash
# Configure clustering (if supported)
# See official documentation for cluster setup

# Basic load balancing setup example
# Configure multiple instances on different ports
```

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream dovecot_backend {
    server 127.0.0.1:143/993;
    server 127.0.0.1:{default_port}1 backup;
}

server {
    listen 80;
    server_name dovecot.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name dovecot.example.com;

    ssl_certificate /etc/ssl/certs/dovecot.example.com.crt;
    ssl_certificate_key /etc/ssl/private/dovecot.example.com.key;

    location / {
        proxy_pass http://dovecot_backend;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        
        # WebSocket support (if needed)
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
    }
}
```

### Apache Configuration

```apache
<VirtualHost *:80>
    ServerName dovecot.example.com
    Redirect permanent / https://dovecot.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName dovecot.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/dovecot.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/dovecot.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:143/993/
    ProxyPassReverse / http://127.0.0.1:143/993/
    
    # WebSocket support (if needed)
    RewriteEngine on
    RewriteCond %{HTTP:Upgrade} websocket [NC]
    RewriteCond %{HTTP:Connection} upgrade [NC]
    RewriteRule ^/?(.*) "ws://127.0.0.1:143/993/$1" [P,L]
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend dovecot_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/dovecot.pem
    redirect scheme https if !{ ssl_fc }
    default_backend dovecot_backend

backend dovecot_backend
    balance roundrobin
    option httpchk GET /health
    server dovecot1 127.0.0.1:143/993 check
    server dovecot2 127.0.0.1:{default_port}1 check backup
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R dovecot:dovecot /etc/dovecot
sudo chmod 750 /etc/dovecot

# Configure firewall
sudo firewall-cmd --permanent --add-service=dovecot
sudo firewall-cmd --reload

# Enable SELinux policies (if applicable)
sudo setsebool -P httpd_can_network_connect on

# Configure fail2ban
sudo tee /etc/fail2ban/jail.d/dovecot.conf << 'EOF'
[dovecot]
enabled = true
port = 143/993
filter = dovecot
logpath = /var/log/dovecot/*.log
maxretry = 5
bantime = 3600
EOF
```

### SSL/TLS Configuration

```bash
# Generate SSL certificates
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
    -keyout /etc/ssl/private/dovecot.key \
    -out /etc/ssl/certs/dovecot.crt

# Configure SSL in dovecot
# See official documentation for SSL configuration
```

## Database Setup

### PostgreSQL Backend (if applicable)

```bash
# Create database and user
sudo -u postgres psql << EOF
CREATE DATABASE dovecot_db;
CREATE USER dovecot_user WITH ENCRYPTED PASSWORD 'secure_password';
GRANT ALL PRIVILEGES ON DATABASE dovecot_db TO dovecot_user;
EOF

# Configure dovecot to use PostgreSQL
# See official documentation for database configuration
```

### MySQL/MariaDB Backend (if applicable)

```bash
# Create database and user
sudo mysql << EOF
CREATE DATABASE dovecot_db;
CREATE USER 'dovecot_user'@'localhost' IDENTIFIED BY 'secure_password';
GRANT ALL PRIVILEGES ON dovecot_db.* TO 'dovecot_user'@'localhost';
FLUSH PRIVILEGES;
EOF
```

## Performance Optimization

### System Tuning

```bash
# Kernel parameters
sudo tee -a /etc/sysctl.conf << EOF
net.core.somaxconn = 65535
net.ipv4.tcp_max_syn_backlog = 65535
net.ipv4.ip_local_port_range = 1024 65535
net.core.netdev_max_backlog = 5000
vm.swappiness = 10
EOF

sudo sysctl -p

# Dovecot specific tuning
mail_max_userip_connections = 50, mail_plugins = $mail_plugins zlib
```

### Resource Limits

```bash
# Configure system limits
sudo tee -a /etc/security/limits.conf << EOF
dovecot soft nofile 65535
dovecot hard nofile 65535
dovecot soft nproc 32768
dovecot hard nproc 32768
EOF
```

## Monitoring

### Prometheus Integration

```yaml
# prometheus.yml configuration
scrape_configs:
  - job_name: 'dovecot'
    static_configs:
      - targets: ['localhost:143/993']
    metrics_path: '/metrics'
```

### Health Checks

```bash
# Basic health check script
#!/bin/bash
if systemctl is-active --quiet dovecot; then
    echo "Dovecot is running"
    exit 0
else
    echo "Dovecot is not running"
    exit 1
fi
```

### Log Monitoring

```bash
# Configure log rotation
sudo tee /etc/logrotate.d/dovecot << 'EOF'
/var/log/dovecot/*.log {
    daily
    rotate 14
    compress
    delaycompress
    missingok
    notifempty
    create 0640 dovecot dovecot
    postrotate
        systemctl reload dovecot > /dev/null 2>&1 || true
    endscript
}
EOF
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Dovecot backup script
BACKUP_DIR="/backup/dovecot"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"

# Stop service (if required)
systemctl stop dovecot

# Backup configuration
tar -czf "$BACKUP_DIR/dovecot-config-$DATE.tar.gz" /etc/dovecot

# Backup data (adjust paths as needed)
tar -czf "$BACKUP_DIR/dovecot-data-$DATE.tar.gz" /var/lib/dovecot

# Start service
systemctl start dovecot

# Clean old backups (keep 30 days)
find "$BACKUP_DIR" -name "*.tar.gz" -mtime +30 -delete

echo "Backup completed: $BACKUP_DIR"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop dovecot

# Restore configuration
sudo tar -xzf /backup/dovecot/dovecot-config-*.tar.gz -C /

# Restore data
sudo tar -xzf /backup/dovecot/dovecot-data-*.tar.gz -C /

# Set permissions
sudo chown -R dovecot:dovecot /etc/dovecot
sudo chown -R dovecot:dovecot /var/lib/dovecot

# Start service
sudo systemctl start dovecot
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u dovecot -n 100
sudo tail -f /var/log/dovecot/*.log

# Check configuration
sudo dovecot -t || sudo dovecot configtest

# Check permissions
ls -la /etc/dovecot
ls -la /var/lib/dovecot
```

2. **Connection refused**:
```bash
# Check if service is listening
sudo ss -tlnp | grep 143/993
sudo netstat -tlnp | grep 143/993

# Check firewall
sudo firewall-cmd --list-all
sudo iptables -L -n

# Test connection
telnet localhost 143/993
nc -zv localhost 143/993
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep dovecot)
htop -p $(pgrep dovecot)

# Check connections
ss -ant | grep :143/993 | wc -l

# Monitor I/O
iotop -p $(pgrep dovecot)
```

### Debug Mode

```bash
# Run in debug mode
sudo dovecot -d
# or
sudo dovecot debug

# Increase log verbosity
# Edit configuration to enable debug logging
```

## Integration Examples

### Docker Compose

```yaml
version: '3.8'
services:
  dovecot:
    image: dovecot:latest
    container_name: dovecot
    ports:
      - "143/993:143/993"
    volumes:
      - ./config:/etc/dovecot
      - ./data:/var/lib/dovecot
    environment:
      - dovecot_CONFIG=/etc/dovecot/dovecot.conf
    restart: unless-stopped
    networks:
      - dovecot_net

networks:
  dovecot_net:
    driver: bridge
```

### Kubernetes Deployment

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: dovecot
spec:
  replicas: 3
  selector:
    matchLabels:
      app: dovecot
  template:
    metadata:
      labels:
        app: dovecot
    spec:
      containers:
      - name: dovecot
        image: dovecot:latest
        ports:
        - containerPort: 143/993
        volumeMounts:
        - name: config
          mountPath: /etc/dovecot
      volumes:
      - name: config
        configMap:
          name: dovecot-config
---
apiVersion: v1
kind: Service
metadata:
  name: dovecot
spec:
  selector:
    app: dovecot
  ports:
  - port: 143/993
    targetPort: 143/993
  type: LoadBalancer
```

### Ansible Playbook

```yaml
---
- name: Install and configure Dovecot
  hosts: all
  become: yes
  tasks:
    - name: Install dovecot
      package:
        name: dovecot
        state: present
    
    - name: Configure dovecot
      template:
        src: dovecot.conf.j2
        dest: /etc/dovecot/dovecot.conf
        owner: dovecot
        group: dovecot
        mode: '0640'
      notify: restart dovecot
    
    - name: Start and enable dovecot
      systemd:
        name: dovecot
        state: started
        enabled: yes
  
  handlers:
    - name: restart dovecot
      systemd:
        name: dovecot
        state: restarted
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update dovecot

# Debian/Ubuntu
sudo apt update && sudo apt upgrade dovecot

# Arch Linux
sudo pacman -Syu dovecot

# Alpine Linux
apk update && apk upgrade dovecot

# openSUSE
sudo zypper update dovecot

# FreeBSD
pkg update && pkg upgrade dovecot

# Always backup before updates
tar -czf /backup/dovecot-pre-update-$(date +%Y%m%d).tar.gz /etc/dovecot

# Restart after updates
sudo systemctl restart dovecot
```

### Regular Maintenance Tasks

```bash
# Clean logs
find /var/log/dovecot -name "*.log" -mtime +30 -delete

# Verify integrity
sudo dovecot --verify || sudo dovecot check

# Update databases (if applicable)
sudo dovecot-update-db

# Optimize performance
sudo dovecot-optimize

# Check for security updates
sudo dovecot --security-check
```

## Additional Resources

- Official Documentation: https://docs.dovecot.org/
- GitHub Repository: https://github.com/dovecot/dovecot
- Community Forum: https://forum.dovecot.org/
- Wiki: https://wiki.dovecot.org/
- Comparison vs Cyrus IMAP, Courier, Zimbra, Exchange: https://docs.dovecot.org/comparison

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
