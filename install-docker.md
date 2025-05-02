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

sudo ./setup-network.sh # Run with root privileges
```

```bash
#!/bin/bash

# --- Configuration ---
IFACE="ens18"
IPADDR="192.168.1.100"  # Static IP for the VM's network interface
CIDR="24"  # CIDR notation for subnet mask
GATEWAY="192.168.1.1"  # Default gateway for the network
DNS1="192.168.1.1"  # Primary DNS server
DNS2="192.168.0.2"  # Secondary DNS server
DNS3="1.1.1.1"  # Tertiary DNS server

echo "=== Starting Network Configuration ==="

# --- Step 1: Write Netplan Configuration ---
echo "[Step 1] Writing static IP configuration to Netplan..."
NETPLAN_FILE="/etc/netplan/01-netcfg.yaml"
cat <<EOF | sudo tee $NETPLAN_FILE > /dev/null
network:
    version: 2
    ethernets:
        $IFACE:
            addresses:
                - $IPADDR/$CIDR
            routes:
                - to: default
                  via: $GATEWAY
            nameservers:
                addresses:
                    - $DNS1
                    - $DNS2
                    - $DNS3
EOF
echo "[INFO] Netplan configuration written to $NETPLAN_FILE."

# Fix file permissions
echo "[INFO] Setting correct permissions for $NETPLAN_FILE..."
sudo chmod 600 $NETPLAN_FILE

# Apply the Netplan configuration
echo "[INFO] Applying Netplan configuration..."
sudo netplan apply
if [ $? -eq 0 ]; then
    echo "[SUCCESS] Netplan applied successfully."
else
    echo "[ERROR] Failed to apply Netplan configuration."
    exit 1
fi

# --- Step 2: Verify Configuration ---
echo "[Step 2] Verifying netplan configuration..."
ip addr show $IFACE
ip route show
cat /etc/resolv.conf

# --- Step 3: Check Connectivity ---
echo "[Step 3] Checking internet connectivity..."
if ping -c 4 1.1.1.1 >/dev/null 2>&1; then
    echo "[SUCCESS] Internet is reachable (ping to 1.1.1.1 succeeded)."
else
    echo "[ERROR] No internet access. Please verify the network connection and settings."
    exit 1
fi

echo "=== Network Configuration Completed Successfully ==="
```
