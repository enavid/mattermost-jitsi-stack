## Overview

This project delivers a production-ready communication infrastructure featuring:

- **Mattermost Team Edition** for persistent team messaging, file sharing, and collaboration
- **Jitsi Meet** for secure video conferencing and screen sharing
- **PostgreSQL** as the reliable database backend
- **Nginx** reverse proxy with SSL/TLS termination
- Integrated health checks and automatic restart policies
- Optimized security configurations with read-only containers and resource limits

## Quick Start

### 1. Clone the Repository

```bash
git clone https://github.com/enavid/mattermost-jitsi-stack.git
cd mattermost-jitsi-stack
```

The project structure includes:

```
mattermost-jitsi-stack/
├── docker-compose.yml       # Main orchestration file
├── .env.example            # Environment variables template
├── nginx.conf              # Nginx configuration
├── certs/                  # SSL certificates directory
│   ├── chat/              # Mattermost certificates
│   └── meet/              # Jitsi certificates
└── README.md
```

### 2. Configure Environment Variables

Copy the example environment file and edit it with your values:

```bash
cp .env.example .env
nano .env
```

Update the following required variables in `.env`:

- **MATTERMOST_DOMAIN** - Your Mattermost domain (e.g., https://chat.yourdomain.com)
- **JITSI_DOMAIN** - Your Jitsi domain (e.g., https://meet.yourdomain.com)
- **DOCKER_HOST_ADDRESS** - Your server's public IP address
- **POSTGRES_PASSWORD** - Strong password for PostgreSQL database
- **SMTP_*** - Email server configuration for notifications
- **JICOFO_COMPONENT_SECRET** - Random secret for Jitsi component
- **JICOFO_AUTH_PASSWORD** - Random password for Jicofo authentication
- **JVB_AUTH_PASSWORD** - Random password for video bridge authentication

Generate secure random passwords using:

```bash
openssl rand -hex 16
```

**Important:** Never commit your `.env` file to version control. The `.env.example` file is provided as a template.

### 3. Prepare SSL Certificates

Create the certificate directories and place your SSL certificates:

```bash
mkdir -p certs/chat certs/meet

# Place your certificates:
# certs/chat/cert.crt
# certs/chat/private.key
# certs/meet/cert.crt
# certs/meet/private.key
```

### 4. Update Nginx Configuration

Edit `nginx.conf` and replace the domain names:

```nginx
# Line 62
server_name chat.yourdomain.com;

# Line 104
server_name meet.yourdomain.com;
```

### 5. Launch the Stack

```bash
docker-compose up -d
```

### 6. Verify Deployment

Check that all containers are running:

```bash
docker-compose ps
```

Access your services:

- Mattermost: https://chat.yourdomain.com
- Jitsi Meet: https://meet.yourdomain.com

## Configuration

### Mattermost Initial Setup

1. Navigate to your Mattermost domain
2. Create the first admin account
3. Configure team settings and invite users
4. Enable plugins and integrations as needed

### Jitsi Integration with Mattermost

To enable video calls from within Mattermost:

1. Go to System Console in Mattermost
2. Navigate to Plugins > Jitsi
3. Enable the Jitsi plugin
4. Set Jitsi URL to your Jitsi domain
5. Save configuration

### Security Considerations

This deployment includes several security hardening measures:

- Read-only root filesystems where possible
- Process limits to prevent resource exhaustion
- Security options preventing privilege escalation
- Isolated network with no direct external access to services
- SSL/TLS encryption for all external communications

### Email Configuration

For production use, configure SMTP settings to enable:

- User account verification
- Password reset emails
- Notification emails
- Team invitations

Update the SMTP variables in your `.env` file with your email provider's settings.

## Video Calls Not Working

Ensure UDP port 10000 is open on your firewall:

```bash
sudo ufw allow 10000/udp
```

Verify JVB can reach the XMPP server:

```bash
docker-compose logs jitsi-jvb
```

### SSL Certificate Issues

Verify certificate files are readable:

```bash
ls -la certs/chat/
ls -la certs/meet/
```

Test Nginx configuration:

```bash
docker-compose exec nginx nginx -t
```

## Ports

The following ports must be accessible:

- **80/TCP** - HTTP (redirects to HTTPS)
- **443/TCP** - HTTPS for web interfaces
- **10000/UDP** - Jitsi video bridge

cd /nginx

openssl dhparam -out dhparam.pem 2048

# Change ownership to UID 70 (postgres user in Alpine)

sudo chown 70:70 ./certs/postgres/server.key
sudo chown 70:70 ./certs/postgres/server.crt

# Ensure correct permissions

sudo chmod 600 ./certs/postgres/server.key
sudo chmod 644 ./certs/postgres/server.crt

# Verify the changes

ls -la ./certs/postgres/

docker exec -it jitsi-prosody /bin/bash

prosodyctl --config /config/prosody.cfg.lua register username meet.jitsi password
