Summary

# Self-Hosted Docker Container Catalog

This repository documents and organizes my self-hosted Docker containers by category and preferred hosting method.

---

## 🗂️ Services by Category

### 🧠 Machine Learning & AI
- `ollama/ollama` - Run large language models locally

---

### 🌐 Networking & Web Servers
- `traefik` - Edge router and reverse proxy
- `haproxy` - TCP/HTTP Load Balancer
- `caddy` - Web server with automatic HTTPS
- `linuxserver/swag` - Secure Web Application Gateway
- `vaultwarden/server` - Bitwarden-compatible password manager
- `pihole/pihole` - DNS-level ad blocking
- `adguard/adguardhome` - Alternative to Pi-hole
- `linuxserver/heimdall` - Application dashboard

---

### 📦 Operating Systems / Base Images
- `ubuntu`
- `alpine`
- `busybox`
- `docker` (Docker-in-Docker)

---

### 🧰 Developer Tools
- `gitea/gitea` - Git service
- `hashicorp/consul` - Service discovery
- `hashicorp/vault` - Secrets management

---

### 📚 Content Management Systems (CMS)
- `nextcloud` / `linuxserver/nextcloud`
- `onlyoffice/documentserver`
- `linuxserver/calibre-web`
- `linuxserver/lazylibrarian`
- `stashapp/stash`
- `linuxserver/sonarr`
- `linuxserver/radarr`
- `linuxserver/lidarr`
- `linuxserver/bazarr`
- `recyclarr/recyclarr`
- `hotio/whisparr`
- `linuxserver/grocy`

---

### 📡 Media Servers & Indexers
- `linuxserver/plex`
- `jellyfin/jellyfin` / `linuxserver/jellyfin`
- `linuxserver/qbittorrent`
- `linuxserver/jackett`
- `linuxserver/prowlarr`
- `linuxserver/nzbget`
- `linuxserver/sabnzbd`

---

### 🧾 Budgeting & Finance
- `phoenixashes/actual`
- `actualbudget/actual-server`

---

### 🏡 Home Automation
- `homeassistant/home-assistant`

---

### 🛡️ Security & Monitoring
- `netdata/netdata`
- `tautulli/tautulli` / `linuxserver/tautulli`
- `containrrr/watchtower`
- `portainer/portainer-ce`
- `portainer/agent`

---

### 🗄️ Databases & Storage
- `linuxserver/mariadb`
- `postgres`
- `redis`
- `memcached`
- `linuxserver/duplicati`
- `linuxserver/syncthing`

---

## 🧭 Deployment by Host Type

### 🐳 Docker Containers (Primary Method)
> Deployed in Docker unless otherwise noted.

- `traefik`
- `caddy`
- `haproxy`
- `vaultwarden/server`
- `pihole/pihole`
- `adguard/adguardhome`
- `linuxserver/swag`
- `netdata/netdata`
- `portainer/portainer-ce`
- `portainer/agent`
- `ollama/ollama`
- `nextcloud` / `linuxserver/nextcloud`
- `onlyoffice/documentserver`
- `gitea/gitea`
- `stashapp/stash`
- `linuxserver/sonarr`
- `linuxserver/radarr`
- `linuxserver/lidarr`
- `linuxserver/prowlarr`
- `linuxserver/bazarr`
- `recyclarr/recyclarr`
- `hotio/whisparr`
- `linuxserver/jackett`
- `linuxserver/qbittorrent`
- `linuxserver/nzbget`
- `linuxserver/sabnzbd`
- `linuxserver/plex`
- `jellyfin/jellyfin` / `linuxserver/jellyfin`
- `linuxserver/calibre-web`
- `linuxserver/lazylibrarian`
- `linuxserver/duplicati`
- `linuxserver/heimdall`
- `linuxserver/grocy`
- `phoenixashes/actual`
- `actualbudget/actual-server`
- `linuxserver/mariadb`
- `postgres`
- `redis`
- `memcached`
- `linuxserver/syncthing`
- `tautulli/tautulli` / `linuxserver/tautulli`
- `containrrr/watchtower`
- `homeassistant/home-assistant`
- `hashicorp/vault`
- `hashicorp/consul`

---

### 🧱 LXC Containers (For Lightweight, Persistent Services)
> Suitable for non-Docker services or persistent storage nodes.

- `postgres` *(for heavy DB loads)*
- `nextcloud` *(for long-term use or when separating storage/data)*
- `homeassistant/home-assistant`
- `pihole/pihole` or `adguard/adguardhome`

---

### 🖥️ Native on Host OS (Optional / When Low Latency or Hardware Access Needed)
> Useful when Docker abstraction adds unnecessary complexity.

- `ollama/ollama` *(if GPU passthrough or better model performance is required)*
- `netdata/netdata` *(for full system observability)*
- `vaultwarden/server` *(if managing passwords locally w/ full control)*
- `linuxserver/duplicati` *(when accessing mounted file systems)*
- `homeassistant/home-assistant` *(if tight hardware integration is needed)*

---

## ✅ Notes
- Most containers are sourced from `linuxserver.io` for consistency and active maintenance.
- Containers tagged with `/linuxserver` typically support Docker Compose and well-documented volumes.
- Use `Watchtower` for automatic updates, or manage updates via `Portainer`.

---
