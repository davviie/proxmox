```bash
bash -c "$(curl -fsSL https://raw.githubusercontent.com/community-scripts/ProxmoxVE/main/vm/docker-vm.sh)"
```

# run the script below like this:

```bash
nano setup-network.sh
# Paste the script, save, and close (Ctrl+O, Enter, Ctrl+X).
```

```bash
chmod +x setup-network.sh # Make it executable

./setup-network.sh # Run with root privileges
```

```bash
#!/bin/bash

# --- Configuration ---
IFACE="ens18"
IPADDR="192.168.1.100"
NETMASK="255.255.255.0"  # Subnet mask in legacy format
GATEWAY="192.168.1.1"
DNS1="192.168.1.1"
DNS2="192.168.0.2"
DNS3="1.1.1.1"
DNS4="8.8.8.8"

# --- Convert NETMASK to CIDR ---
CIDR=$(echo "$NETMASK" | awk -F "." '{for (i=1; i<=NF; i++) n += sprintf("%08d", and($i, 255)); gsub("0", "", n); print length(n)}')

# --- Check and Assign IP Address Manually ---
echo "[+] Checking if .network file exists..."
CONFIG_DIR="/etc/systemd/network"
CONFIG_FILE="$CONFIG_DIR/$IFACE.network"

if [ ! -f "$CONFIG_FILE" ]; then
    echo "[!] $CONFIG_FILE not found. Assigning IP address manually..."
    sudo ip addr add $IPADDR/$CIDR dev $IFACE
    sudo ip link set $IFACE up
else
    echo "[+] $CONFIG_FILE exists. Skipping manual IP assignment."
fi

# --- Create systemd-networkd configuration ---
echo "[+] Configuring systemd-networkd for $IFACE..."

# Create the configuration directory if it doesn't exist
mkdir -p "$CONFIG_DIR"

# Write the configuration file
cat <<EOF > "$CONFIG_FILE"
[Match]
Name=$IFACE

[Network]
Address=$IPADDR/$CIDR
Gateway=$GATEWAY
DNS=$DNS1
DNS=$DNS2
DNS=$DNS3
DNS=$DNS4
EOF

echo "[+] Configuration written to $CONFIG_FILE."

# --- Restart systemd-networkd service ---
echo "[+] Restarting systemd-networkd service..."
if systemctl restart systemd-networkd; then
    echo "[✔] systemd-networkd restarted successfully."
else
    echo "[✘] Failed to restart systemd-networkd. Please check the service status."
fi

# --- Check and Set Default Route ---
echo "[+] Checking default route..."
DEFAULT_ROUTE=$(ip route show default | grep -c "default via $GATEWAY dev $IFACE")
if [ "$DEFAULT_ROUTE" -eq 0 ]; then
    echo "[!] Default route is missing. Adding default route via $GATEWAY."
    sudo ip route add default via $GATEWAY dev $IFACE
else
    echo "[✔] Default route is already set."
fi

# --- Persistent DNS Configuration ---
echo "[+] Configuring persistent DNS..."
RESOLVED_CONF="/etc/systemd/resolved.conf"

# Update the systemd-resolved configuration
cat <<EOF > "$RESOLVED_CONF"
[Resolve]
DNS=$DNS1 $DNS2 $DNS3 $DNS4
EOF

# Restart systemd-resolved to apply changes
systemctl restart systemd-resolved

# --- Connectivity Check ---
echo "[+] Checking internet connectivity..."
if ping -c 2 1.1.1.1 >/dev/null 2>&1; then
    echo "[✔] Internet is reachable (ping to 1.1.1.1 succeeded)."
else
    echo "[✘] No internet access. Please verify physical connection or gateway settings."
fi
```
