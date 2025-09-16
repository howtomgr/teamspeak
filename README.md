# TeamSpeak Installation Guide

TeamSpeak is a free and open-source Voice Server. Voice communication system for online gaming

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
  - Network: 9987/10011 ports
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port 9987/10011 (default teamspeak port)
- **Dependencies**:
  - none
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

# Install teamspeak
sudo dnf install -y teamspeak none

# Enable and start service
sudo systemctl enable --now teamspeak

# Configure firewall
sudo firewall-cmd --permanent --add-service=teamspeak
sudo firewall-cmd --reload

# Verify installation
teamspeak --version || systemctl status teamspeak
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install teamspeak
sudo apt install -y teamspeak none

# Enable and start service
sudo systemctl enable --now teamspeak

# Configure firewall
sudo ufw allow 9987/10011

# Verify installation
teamspeak --version || systemctl status teamspeak
```

### Arch Linux

```bash
# Install teamspeak
sudo pacman -S teamspeak

# Enable and start service
sudo systemctl enable --now teamspeak

# Verify installation
teamspeak --version || systemctl status teamspeak
```

### Alpine Linux

```bash
# Install teamspeak
apk add --no-cache teamspeak

# Enable and start service
rc-update add teamspeak default
rc-service teamspeak start

# Verify installation
teamspeak --version || rc-service teamspeak status
```

### openSUSE/SLES

```bash
# Install teamspeak
sudo zypper install -y teamspeak none

# Enable and start service
sudo systemctl enable --now teamspeak

# Configure firewall
sudo firewall-cmd --permanent --add-service=teamspeak
sudo firewall-cmd --reload

# Verify installation
teamspeak --version || systemctl status teamspeak
```

### macOS

```bash
# Using Homebrew
brew install teamspeak

# Start service
brew services start teamspeak

# Verify installation
teamspeak --version
```

### FreeBSD

```bash
# Using pkg
pkg install teamspeak

# Enable in rc.conf
echo 'teamspeak_enable="YES"' >> /etc/rc.conf

# Start service
service teamspeak start

# Verify installation
teamspeak --version || service teamspeak status
```

### Windows

```powershell
# Using Chocolatey
choco install teamspeak

# Or using Scoop
scoop install teamspeak

# Verify installation
teamspeak --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory if needed
sudo mkdir -p /opt/teamspeak

# Set up basic configuration
sudo tee /opt/teamspeak/teamspeak.conf << 'EOF'
# TeamSpeak Configuration
voice_ip=0.0.0.0
EOF

# Test configuration
sudo teamspeak -t || sudo teamspeak configtest

# Reload service
sudo systemctl reload teamspeak
```

### Security Hardening

```bash
# Set appropriate permissions
sudo chown -R teamspeak:teamspeak /opt/teamspeak
sudo chmod 750 /opt/teamspeak

# Enable security features
# See security section for detailed hardening steps
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable teamspeak

# Start service
sudo systemctl start teamspeak

# Stop service
sudo systemctl stop teamspeak

# Restart service
sudo systemctl restart teamspeak

# Reload configuration
sudo systemctl reload teamspeak

# Check status
sudo systemctl status teamspeak

# View logs
sudo journalctl -u teamspeak -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add teamspeak default

# Start service
rc-service teamspeak start

# Stop service
rc-service teamspeak stop

# Restart service
rc-service teamspeak restart

# Check status
rc-service teamspeak status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'teamspeak_enable="YES"' >> /etc/rc.conf

# Start service
service teamspeak start

# Stop service
service teamspeak stop

# Restart service
service teamspeak restart

# Check status
service teamspeak status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start teamspeak
brew services stop teamspeak
brew services restart teamspeak

# Check status
brew services list | grep teamspeak
```

### Windows Service Manager

```powershell
# Start service
net start teamspeak

# Stop service
net stop teamspeak

# Using PowerShell
Start-Service teamspeak
Stop-Service teamspeak
Restart-Service teamspeak

# Check status
Get-Service teamspeak
```

## Advanced Configuration

### Performance Optimization

```bash
# Configure performance settings
cat >> /opt/teamspeak/teamspeak.conf << 'EOF'
voice_ip=0.0.0.0
EOF

# Apply system tuning
sudo sysctl -w net.core.somaxconn=65535
sudo sysctl -w net.ipv4.tcp_max_syn_backlog=65535

# Restart service
sudo systemctl restart teamspeak
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
upstream teamspeak_backend {
    server 127.0.0.1:9987/10011;
    server 127.0.0.1:{default_port}1 backup;
}

server {
    listen 80;
    server_name teamspeak.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name teamspeak.example.com;

    ssl_certificate /etc/ssl/certs/teamspeak.example.com.crt;
    ssl_certificate_key /etc/ssl/private/teamspeak.example.com.key;

    location / {
        proxy_pass http://teamspeak_backend;
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
    ServerName teamspeak.example.com
    Redirect permanent / https://teamspeak.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName teamspeak.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/teamspeak.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/teamspeak.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:9987/10011/
    ProxyPassReverse / http://127.0.0.1:9987/10011/
    
    # WebSocket support (if needed)
    RewriteEngine on
    RewriteCond %{HTTP:Upgrade} websocket [NC]
    RewriteCond %{HTTP:Connection} upgrade [NC]
    RewriteRule ^/?(.*) "ws://127.0.0.1:9987/10011/$1" [P,L]
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend teamspeak_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/teamspeak.pem
    redirect scheme https if !{ ssl_fc }
    default_backend teamspeak_backend

backend teamspeak_backend
    balance roundrobin
    option httpchk GET /health
    server teamspeak1 127.0.0.1:9987/10011 check
    server teamspeak2 127.0.0.1:{default_port}1 check backup
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R teamspeak:teamspeak /opt/teamspeak
sudo chmod 750 /opt/teamspeak

# Configure firewall
sudo firewall-cmd --permanent --add-service=teamspeak
sudo firewall-cmd --reload

# Enable SELinux policies (if applicable)
sudo setsebool -P httpd_can_network_connect on

# Configure fail2ban
sudo tee /etc/fail2ban/jail.d/teamspeak.conf << 'EOF'
[teamspeak]
enabled = true
port = 9987/10011
filter = teamspeak
logpath = /var/log/teamspeak/*.log
maxretry = 5
bantime = 3600
EOF
```

### SSL/TLS Configuration

```bash
# Generate SSL certificates
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
    -keyout /etc/ssl/private/teamspeak.key \
    -out /etc/ssl/certs/teamspeak.crt

# Configure SSL in teamspeak
# See official documentation for SSL configuration
```

## Database Setup

### PostgreSQL Backend (if applicable)

```bash
# Create database and user
sudo -u postgres psql << EOF
CREATE DATABASE teamspeak_db;
CREATE USER teamspeak_user WITH ENCRYPTED PASSWORD 'secure_password';
GRANT ALL PRIVILEGES ON DATABASE teamspeak_db TO teamspeak_user;
EOF

# Configure teamspeak to use PostgreSQL
# See official documentation for database configuration
```

### MySQL/MariaDB Backend (if applicable)

```bash
# Create database and user
sudo mysql << EOF
CREATE DATABASE teamspeak_db;
CREATE USER 'teamspeak_user'@'localhost' IDENTIFIED BY 'secure_password';
GRANT ALL PRIVILEGES ON teamspeak_db.* TO 'teamspeak_user'@'localhost';
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

# TeamSpeak specific tuning
voice_ip=0.0.0.0
```

### Resource Limits

```bash
# Configure system limits
sudo tee -a /etc/security/limits.conf << EOF
teamspeak soft nofile 65535
teamspeak hard nofile 65535
teamspeak soft nproc 32768
teamspeak hard nproc 32768
EOF
```

## Monitoring

### Prometheus Integration

```yaml
# prometheus.yml configuration
scrape_configs:
  - job_name: 'teamspeak'
    static_configs:
      - targets: ['localhost:9987/10011']
    metrics_path: '/metrics'
```

### Health Checks

```bash
# Basic health check script
#!/bin/bash
if systemctl is-active --quiet teamspeak; then
    echo "TeamSpeak is running"
    exit 0
else
    echo "TeamSpeak is not running"
    exit 1
fi
```

### Log Monitoring

```bash
# Configure log rotation
sudo tee /etc/logrotate.d/teamspeak << 'EOF'
/var/log/teamspeak/*.log {
    daily
    rotate 14
    compress
    delaycompress
    missingok
    notifempty
    create 0640 teamspeak teamspeak
    postrotate
        systemctl reload teamspeak > /dev/null 2>&1 || true
    endscript
}
EOF
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# TeamSpeak backup script
BACKUP_DIR="/backup/teamspeak"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"

# Stop service (if required)
systemctl stop teamspeak

# Backup configuration
tar -czf "$BACKUP_DIR/teamspeak-config-$DATE.tar.gz" /opt/teamspeak

# Backup data (adjust paths as needed)
tar -czf "$BACKUP_DIR/teamspeak-data-$DATE.tar.gz" /var/lib/teamspeak

# Start service
systemctl start teamspeak

# Clean old backups (keep 30 days)
find "$BACKUP_DIR" -name "*.tar.gz" -mtime +30 -delete

echo "Backup completed: $BACKUP_DIR"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop teamspeak

# Restore configuration
sudo tar -xzf /backup/teamspeak/teamspeak-config-*.tar.gz -C /

# Restore data
sudo tar -xzf /backup/teamspeak/teamspeak-data-*.tar.gz -C /

# Set permissions
sudo chown -R teamspeak:teamspeak /opt/teamspeak
sudo chown -R teamspeak:teamspeak /var/lib/teamspeak

# Start service
sudo systemctl start teamspeak
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u teamspeak -n 100
sudo tail -f /var/log/teamspeak/*.log

# Check configuration
sudo teamspeak -t || sudo teamspeak configtest

# Check permissions
ls -la /opt/teamspeak
ls -la /var/lib/teamspeak
```

2. **Connection refused**:
```bash
# Check if service is listening
sudo ss -tlnp | grep 9987/10011
sudo netstat -tlnp | grep 9987/10011

# Check firewall
sudo firewall-cmd --list-all
sudo iptables -L -n

# Test connection
telnet localhost 9987/10011
nc -zv localhost 9987/10011
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep ts3server)
htop -p $(pgrep ts3server)

# Check connections
ss -ant | grep :9987/10011 | wc -l

# Monitor I/O
iotop -p $(pgrep ts3server)
```

### Debug Mode

```bash
# Run in debug mode
sudo teamspeak -d
# or
sudo teamspeak debug

# Increase log verbosity
# Edit configuration to enable debug logging
```

## Integration Examples

### Docker Compose

```yaml
version: '3.8'
services:
  teamspeak:
    image: teamspeak:latest
    container_name: teamspeak
    ports:
      - "9987/10011:9987/10011"
    volumes:
      - ./config:/opt/teamspeak
      - ./data:/var/lib/teamspeak
    environment:
      - teamspeak_CONFIG=/opt/teamspeak/teamspeak.conf
    restart: unless-stopped
    networks:
      - teamspeak_net

networks:
  teamspeak_net:
    driver: bridge
```

### Kubernetes Deployment

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: teamspeak
spec:
  replicas: 3
  selector:
    matchLabels:
      app: teamspeak
  template:
    metadata:
      labels:
        app: teamspeak
    spec:
      containers:
      - name: teamspeak
        image: teamspeak:latest
        ports:
        - containerPort: 9987/10011
        volumeMounts:
        - name: config
          mountPath: /opt/teamspeak
      volumes:
      - name: config
        configMap:
          name: teamspeak-config
---
apiVersion: v1
kind: Service
metadata:
  name: teamspeak
spec:
  selector:
    app: teamspeak
  ports:
  - port: 9987/10011
    targetPort: 9987/10011
  type: LoadBalancer
```

### Ansible Playbook

```yaml
---
- name: Install and configure TeamSpeak
  hosts: all
  become: yes
  tasks:
    - name: Install teamspeak
      package:
        name: teamspeak
        state: present
    
    - name: Configure teamspeak
      template:
        src: teamspeak.conf.j2
        dest: /opt/teamspeak/teamspeak.conf
        owner: teamspeak
        group: teamspeak
        mode: '0640'
      notify: restart teamspeak
    
    - name: Start and enable teamspeak
      systemd:
        name: teamspeak
        state: started
        enabled: yes
  
  handlers:
    - name: restart teamspeak
      systemd:
        name: teamspeak
        state: restarted
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update teamspeak

# Debian/Ubuntu
sudo apt update && sudo apt upgrade teamspeak

# Arch Linux
sudo pacman -Syu teamspeak

# Alpine Linux
apk update && apk upgrade teamspeak

# openSUSE
sudo zypper update teamspeak

# FreeBSD
pkg update && pkg upgrade teamspeak

# Always backup before updates
tar -czf /backup/teamspeak-pre-update-$(date +%Y%m%d).tar.gz /opt/teamspeak

# Restart after updates
sudo systemctl restart teamspeak
```

### Regular Maintenance Tasks

```bash
# Clean logs
find /var/log/teamspeak -name "*.log" -mtime +30 -delete

# Verify integrity
sudo teamspeak --verify || sudo teamspeak check

# Update databases (if applicable)
sudo teamspeak-update-db

# Optimize performance
sudo teamspeak-optimize

# Check for security updates
sudo teamspeak --security-check
```

## Additional Resources

- Official Documentation: https://docs.teamspeak.org/
- GitHub Repository: https://github.com/teamspeak/teamspeak
- Community Forum: https://forum.teamspeak.org/
- Wiki: https://wiki.teamspeak.org/
- Comparison vs Mumble, Discord, Ventrilo, Curse Voice: https://docs.teamspeak.org/comparison

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
