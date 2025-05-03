```bash
bash -c "$(curl -fsSL https://raw.githubusercontent.com/community-scripts/ProxmoxVE/main/vm/docker-vm.sh)"
```

```bash
#!/bin/bash

set -e

INTERFACE="ens18"
STATIC_IP="192.168.1.100/24"
GATEWAY="192.168.1.1"
DNS_SERVER="192.168.0.2"

echo "[+] Creating /etc/systemd/network/10-${INTERFACE}.network"
cat <<EOF > /etc/systemd/network/10-${INTERFACE}.network
[Match]
Name=${INTERFACE}

[Network]
Address=${STATIC_IP}
Gateway=${GATEWAY}
DNS=${DNS_SERVER}
EOF

echo "[+] Linking resolv.conf to systemd stub"
ln -sf /run/systemd/resolve/stub-resolv.conf /etc/resolv.conf

echo "[+] Restarting network and DNS services"
systemctl daemon-reexec
systemctl restart systemd-networkd
systemctl restart systemd-resolved
systemctl enable systemd-networkd
systemctl enable systemd-resolved

echo "[+] Network status for ${INTERFACE}:"
ip a show ${INTERFACE}

echo "[+] Testing connectivity:"
ping -c 3 8.8.8.8 || echo "❌ Ping to 8.8.8.8 failed"
ping -c 3 google.com || echo "❌ Ping to google.com failed"

echo "[✓] Static IP and DNS setup complete."


```
