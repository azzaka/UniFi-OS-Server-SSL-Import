# UniFi OS Server SSL Import Script

Automatically renew and import Let's Encrypt SSL certificates into a UniFi OS Server deployment using Certbot and Cloudflare DNS validation.

The script:
* Optionally runs a Certbot renewal check
* Detects certificate changes using SHA256 checksums
* Stops UniFi OS Server cleanly
* Backs up existing certificate files
* Installs the latest certificate and private key
* Sets correct ownership and permissions
* Restarts UniFi OS Server
* Logs activity for troubleshooting

---
# Features

* Certbot + Cloudflare DNS-01 support
* SHA256 certificate change detection
* Automatic backups before replacement
* Detailed logging
* Safe restart of UniFi OS Server
* Designed for automation with systemd timers
* Supports manual execution for testing

---
# Requirements

* Ubuntu/Debian-based Linux server
* UniFi OS Server installed and operational
* Root access
* Certbot
* Cloudflare DNS API Token

---
# Install Certbot
```bash
apt update
apt install certbot python3-certbot-dns-cloudflare -y
```

---
# Create Cloudflare Credentials
Create a credentials file:
```bash
mkdir -p /etc/letsencrypt
nano /etc/letsencrypt/cloudflare.ini
```

Add:
```ini
dns_cloudflare_api_token = YOUR_API_TOKEN
```

Restrict permissions:
```bash
chmod 600 /etc/letsencrypt/cloudflare.ini
```

## Recommended Cloudflare Permissions
Use a Cloudflare API Token rather than a Global API Key.

Minimum permissions:
* Zone → DNS → Edit
* Zone → Zone → Read

Limit access to only the required zone.

---
# Request Initial Certificate
Replace the hostname with your own:
```bash
certbot certonly \
  --dns-cloudflare \
  --dns-cloudflare-credentials /etc/letsencrypt/cloudflare.ini \
  --dns-cloudflare-propagation-seconds 60 \
  --key-type rsa \
  --rsa-key-size 4096 \
  -d unifi.example.com
```

## Important
UniFi OS Server requires RSA certificates.

Use:
```bash
--key-type rsa
```

Do not use ECDSA certificates.

---
# Install Script
Recommended location:
```bash
mkdir -p /opt/scripts
```

Copy the script:
```bash
cp unifi-cert-import.sh /opt/scripts/
chmod 750 /opt/scripts/unifi-cert-import.sh
```

Edit configuration:
```bash
nano /opt/scripts/unifi-cert-import.sh
```

Update:
```bash
UNIFI_HOSTNAME="unifi.example.com"
```

---
# Manual Execution
Run manually:
```bash
/opt/scripts/unifi-cert-import.sh
```

Force certificate import:
```bash
/opt/scripts/unifi-cert-import.sh --force
```

Verbose mode:
```bash
/opt/scripts/unifi-cert-import.sh --verbose
```

Skip Certbot renewal:
```bash
/opt/scripts/unifi-cert-import.sh --no-renew
```

---
# Logging
Script log:
```bash
tail -f /var/log/scripts/unifi-cert-import.log
```

UniFi OS Server log:
```bash
tail -f /home/uosserver/.local/share/containers/storage/volumes/uosserver_data/_data/unifi-core/logs/http.log
```

---
# Automation with systemd
Create:
```bash
/etc/systemd/system/unifi-cert-import.service
```

```ini
[Unit]
Description=UniFi OS Server Certificate Import
After=network-online.target

[Service]
Type=oneshot
User=root
Group=root
ExecStart=/opt/scripts/unifi-cert-import.sh
```

Create:
```bash
/etc/systemd/system/unifi-cert-import.timer
```

```ini
[Unit]
Description=Run UniFi Certificate Import Daily

[Timer]
OnCalendar=*-*-* 03:30:00
Persistent=true
RandomizedDelaySec=300

[Install]
WantedBy=timers.target
```

Enable:
```bash
systemctl daemon-reload
systemctl enable --now unifi-cert-import.timer
```

Check status:
```bash
systemctl list-timers
systemctl status unifi-cert-import.timer
```

---
# Directory Layout
Recommended layout:
```text
/opt/scripts/unifi-cert-import.sh
/etc/letsencrypt/cloudflare.ini
/var/log/scripts/unifi-cert-import.log
/etc/systemd/system/unifi-cert-import.service
/etc/systemd/system/unifi-cert-import.timer
```

---
# License
MIT

---
# Author
Mirano Verhoef

# Contributors
Martin Seener
Jammin84
Cookie Monster Coder
Lee Goile
