# 🚀 Personal Unbound DNS Server

My personal DNS server based on Unbound with DoT, DNSSEC and local domains support.

## 🎯 Why

- 🔒 Secure DNS queries via DoT
- 🛡️ DNSSEC validation for protection against spoofing
- 🏠 Local domain resolution (*.local)
- ⚡ Caching for faster queries

## 🚀 Quick Start

```bash
# Start
docker-compose up -d

# Test
dig @localhost google.com
```

## ⚙️ Configuration

Main settings in `.env`:

```bash
# Upstream servers with DoT
UPSTREAM_DNS_1="1.1.1.1@853#cloudflare-dns.com"
UPSTREAM_DNS_2="8.8.8.8@853#dns.google"

# Local domains
LOCAL_DOMAINS="local:192.168.1.100"

# DoT server
ENABLE_DOT_SERVER=true
```

Full documentation: [`app/README.md`](app/README.md)

## 🔐 Certificate Setup

After first start, add the generated certificate to system trust store:

```bash
# Extract certificate from container
docker-compose exec unbound cat /etc/unbound/tls/server.pem > unbound-dot.crt

# Add to trusted certificates (Arch Linux)
sudo cp unbound-dot.crt /etc/ca-certificates/trust-source/anchors/
sudo trust extract-compat

# Add to trusted certificates (Ubuntu/Debian)
sudo cp unbound-dot.crt /usr/local/share/ca-certificates/
sudo update-ca-certificates

# Add to trusted certificates (CentOS/RHEL)
sudo cp unbound-dot.crt /etc/pki/ca-trust/source/anchors/
sudo update-ca-trust
```

## 🧪 Testing

```bash
# DNS
dig @localhost google.com

# DNSSEC
dig @localhost +dnssec cloudflare.com

# DoT server (without certificate validation)
kdig @127.0.0.1 -p 853 +tls google.com

# DoT server (with certificate validation - after adding to trust store)
kdig @127.0.0.1 -p 853 +tls-ca +tls-host=localhost google.com
```
