```bash
bash -c "$(curl -fsSL https://raw.githubusercontent.com/community-scripts/ProxmoxVE/main/vm/docker-vm.sh)"
```

```bash
#!/bin/bash

#!/bin/bash

set -e

INTERFACE="ens18"
STATIC_IP="192.168.1.100/24"
GATEWAY="192.168.1.1"
DNS_SERVER="192.168.0.2"

echo "[+] Writing static config to /etc/systemd/network/10-${INTERFACE}.network"
cat <<EOF > /etc/systemd/network/10-${INTERFACE}.network
[Match]
Name=${INTERFACE}

[Network]
Address=${STATIC_IP}
Gateway=${GATEWAY}
DNS=${DNS_SERVER}
EOF

echo "[+] Linking resolv.conf to systemd-resolved stub"
ln -sf /run/systemd/resolve/stub-resolv.conf /etc/resolv.conf

echo "[+] Restarting networkd and resolved services"
systemctl daemon-reexec
systemctl restart systemd-networkd
systemctl restart systemd-resolved
systemctl enable systemd-networkd
systemctl enable systemd-resolved

echo "[+] Verifying IP and DNS connectivity..."
ip a show ${INTERFACE}
ping -c 3 8.8.8.8 || echo "❌ Ping to 8.8.8.8 failed"
ping -c 3 google.com || echo "❌ DNS resolution failed"

echo "[✓] Static network setup complete."


```
