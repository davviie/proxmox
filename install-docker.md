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
HOST_IP="192.168.1.77"  # Current Proxmox host IP
IPADDR="192.168.1.100"  # Static IP for the VM's network interface
NETMASK="255.255.255.0"  # Subnet mask in legacy format
CIDR="24"  # CIDR notation for subnet mask
GATEWAY="192.168.1.1"  # Default gateway for the network
DNS1="192.168.1.1"  # Primary DNS server
DNS2="192.168.0.2"  # Secondary DNS server
DNS3="1.1.1.1"  # Tertiary DNS server

echo "=== Starting Network Configuration ==="

# --- Step 1: Configure ens18 IP ---
echo "[Step 1] Configuring IP address for $IFACE..."
echo "[INFO] Flushing any existing IP addresses on $IFACE to avoid conflicts..."
sudo ip addr flush dev $IFACE

echo "[INFO] Assigning static IP address $IPADDR/$CIDR to $IFACE..."
sudo ip addr add $IPADDR/$CIDR dev $IFACE

echo "[INFO] Bringing the interface $IFACE up..."
sudo ip link set $IFACE up

# --- Step 2: Configure Default Route ---
echo "[Step 2] Configuring default route via $GATEWAY..."
DEFAULT_ROUTE=$(ip route show default | grep -c "default via $GATEWAY dev $IFACE")
if [ "$DEFAULT_ROUTE" -eq 0 ]; then
    echo "[INFO] Default route is missing. Adding default route via $GATEWAY..."
    sudo ip route add default via $GATEWAY dev $IFACE
    echo "[SUCCESS] Default route added successfully."
else
    echo "[INFO] Default route already exists. Skipping this step."
fi

# --- Step 3: Fix resolv.conf with DNS servers ---
echo "[Step 3] Updating /etc/resolv.conf with DNS servers..."
cat <<EOF | sudo tee /etc/resolv.conf > /dev/null
nameserver $DNS1
nameserver $DNS2
nameserver $DNS3
EOF
echo "[SUCCESS] /etc/resolv.conf updated successfully."

echo "[INFO] Making DNS configuration persistent using systemd-resolved..."
RESOLVED_CONF="/etc/systemd/resolved.conf"
cat <<EOF | sudo tee $RESOLVED_CONF > /dev/null
[Resolve]
DNS=$DNS1 $DNS2 $DNS3
EOF
sudo ln -sf /run/systemd/resolve/resolv.conf /etc/resolv.conf
echo "[INFO] Restarting systemd-resolved to apply changes..."
sudo systemctl restart systemd-resolved
echo "[SUCCESS] systemd-resolved restarted successfully."

# --- Step 4: Restart systemd-networkd ---
echo "[Step 4] Restarting systemd-networkd service to apply network settings..."
sudo systemctl restart systemd-networkd
if [ $? -eq 0 ]; then
    echo "[SUCCESS] systemd-networkd restarted successfully."
else
    echo "[ERROR] Failed to restart systemd-networkd. Please check the service status."
    exit 1
fi

# --- Step 5: Verify Configuration ---
echo "[Step 5] Verifying network configuration..."
echo "[INFO] Displaying interface configuration for $IFACE:"
ip addr show $IFACE

echo "[INFO] Displaying routing table:"
ip route show

echo "[INFO] Displaying DNS configuration:"
cat /etc/resolv.conf

# --- Step 6: Check Connectivity ---
echo "[Step 6] Checking internet connectivity..."
if ping -c 4 1.1.1.1 >/dev/null 2>&1; then
    echo "[SUCCESS] Internet is reachable (ping to 1.1.1.1 succeeded)."
else
    echo "[ERROR] No internet access. Please verify the network connection and settings."
    exit 1
fi

echo "=== Network Configuration Completed Successfully ==="
```
