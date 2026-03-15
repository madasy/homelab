Nice moment to do that. A homelab that isn’t version-controlled is like a spaceship without a flight recorder. The YAML remembers what your future self will forget.

Goal: push your **infrastructure config** to Git without leaking secrets or runtime junk.

We’ll keep:

* `compose.yaml`
* Homepage config
* possibly Technitium config templates

We’ll exclude:

* container databases
* Let's Encrypt certificates
* runtime volumes
* logs

Your current structure roughly looks like:

```
/opt/homelab
 ├── compose.yaml
 └── data
      ├── homepage
      ├── technitium
      ├── npm-db
      ├── npm-data
      ├── npm-letsencrypt
      ├── kuma
      └── scan-inbox
```

We **do not want most of `/data` in Git**.

---

# 1. Create repo directory

Inside your homelab folder:

```bash
cd /opt/homelab
git init
```

---

# 2. Create `.gitignore`

Create:

```bash
nano .gitignore
```

Put this:

```
# runtime data
data/

# exceptions we want
!data/homepage/
!data/homepage/*.yaml

# docker junk
*.log
*.pid

# secrets
.env

# certificates
npm-letsencrypt/

# databases
npm-db/

# monitoring data
kuma/

# scans
scan-inbox/
```

The idea:
ignore runtime state, keep configuration.

---

# 3. Move config files into a cleaner structure (recommended)

A cleaner repo layout helps a lot later.

Create:

```bash
mkdir -p config/homepage
mkdir -p config/technitium
```

Move Homepage configs:

```bash
mv data/homepage/*.yaml config/homepage/
```

Update your compose to mount from there:

```yaml
volumes:
  - ./config/homepage:/app/config
```

That way Git contains only the config, not runtime artifacts.

---

# 4. Add a `.env` file for secrets

Create:

```bash
nano .env
```

Example:

```
TZ=Europe/Zurich

NPM_DB_ROOT_PASSWORD=change_this
NPM_DB_PASSWORD=change_this
```

Then in compose replace hardcoded values like:

```yaml
MYSQL_ROOT_PASSWORD: ${NPM_DB_ROOT_PASSWORD}
MYSQL_PASSWORD: ${NPM_DB_PASSWORD}
```

Now secrets are not committed.

---

# 5. Prepare a README

Create:

```bash
nano README.md
```

Example:

```
# Homelab Infrastructure

Server: Debian 13  
Host IP: 192.168.1.20  

## Services

- Nginx Proxy Manager
- Technitium DNS + DHCP
- Portainer
- Uptime Kuma
- Watchtower
- Homepage Dashboard
- SMB Scan Share

## Network

Technitium runs on macvlan:
192.168.1.2

Host:
192.168.1.20

Gateway:
192.168.1.1

## Start

docker compose up -d
```

Future you will thank you.

---

# 6. Commit

```bash
git add .
git commit -m "initial homelab infrastructure"
```

---

# 7. Create remote repo

Example GitHub:

```bash
git remote add origin git@github.com:USERNAME/homelab.git
git branch -M main
git push -u origin main
```

---

# 8. Optional: add a deployment script

Create:

```
deploy.sh
```

```
#!/bin/bash
docker compose pull
docker compose up -d
```

Make executable:

```bash
chmod +x deploy.sh
```

Now updates are easy.

---

# Final repo structure

Clean result:

```
homelab
├── compose.yaml
├── .env
├── .gitignore
├── README.md
├── deploy.sh
├── config
│   ├── homepage
│   │    ├── services.yaml
│   │    ├── widgets.yaml
│   │    └── settings.yaml
│   └── technitium
└── data   (ignored)
```

---

# One strong recommendation

Before pushing publicly, run:

```bash
git status
```

Check you are **not committing**:

* database files
* certificates
* `.env`

Secrets leak faster than water through a sieve.

---

If you want, I can also generate a **clean, production-ready `compose.yaml`** for your whole stack (NPM + Technitium macvlan + Portainer + Kuma + Homepage + SMB) that is perfect for Git repos. It’ll be shorter, safer, and easier to maintain.
