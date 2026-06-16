# UniFi OS Server SSL Import Script
Automatically import SSL certificates into UniFi OS Server (`uosserver`) from supported certificate providers.

The script detects certificate changes, safely replaces the UniFi certificate and key, creates backups, and restarts UniFi OS Server when required.

## Quick Start
If you are using Certbot with Cloudflare DNS (recommended), see:

README-CERTBOT-CLOUDFLARE.md

## Features
* Certbot support
* acme.sh support
* DNS challenge support
* Automatic certificate change detection
* Backup of existing certificates
* Logging support
* Verbose troubleshooting mode
* Safe UniFi OS Server restart process

## Supported Certificate Providers

### Certbot
Supported with any DNS plugin supported by Certbot.

Examples:
* Cloudflare
* Route53
* DigitalOcean
* Google Cloud DNS
* Azure DNS

### acme.sh
Supported with all DNS providers supported by acme.sh.

Examples:
* Cloudflare
* Hetzner
* Route53
* DigitalOcean
* DNSMadeEasy

## Requirements
* Linux server
* Root access
* UniFi OS Server
* Valid SSL certificate
* RSA certificate format

### Important
UniFi OS Server currently requires RSA certificates.

Recommended:
```bash
--key-type rsa --rsa-key-size 4096
```

or for acme.sh:

```bash
--keylength 4096
```

NB: Do not use ECDSA certificates.

## Installation
Download the script:
```bash
wget https://raw.githubusercontent.com/MiranoVerhoef/UniFi-OS-Server-SSL-Import/main/unifi-osserver-ssl-import.sh
```

Install:
```bash
mkdir -p /opt/scripts
mv unifi-osserver-ssl-import.sh /opt/scripts/
chmod 750 /opt/scripts/unifi-osserver-ssl-import.sh
```

Edit configuration:
```bash
nano /opt/scripts/unifi-osserver-ssl-import.sh
```

Update:
```bash
UNIFI_HOSTNAME="unifi.example.com"
```

## Usage
Standard run:
```bash
/opt/scripts/unifi-osserver-ssl-import.sh
```

Verbose mode:
```bash
/opt/scripts/unifi-osserver-ssl-import.sh --verbose
```

Force certificate replacement:
```bash
/opt/scripts/unifi-osserver-ssl-import.sh --force
```

## Logging
Example:
```bash
tail -f /var/log/unifi-os-server-ssl-import.log
```

UniFi logs:
```bash
tail -f /home/uosserver/.local/share/containers/storage/volumes/uosserver_data/_data/unifi-core/logs/http.log
```

## Recommended Automation
Use either:
* systemd timer
* cron

Systemd timers are recommended for modern Linux distributions.

## License
MIT

## Author
Mirano Verhoef

## Contributors
Martin Seener
Jammin84
Cookie Monster Coder
Lee Goile
