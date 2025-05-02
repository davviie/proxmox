```bash
bash -c "$(curl -fsSL https://raw.githubusercontent.com/community-scripts/ProxmoxVE/main/vm/docker-vm.sh)"
```

```bash
# run the script below like this:

#!/bin/bash

# Step 1: Configure static IP
cat <<EOF | sudo tee /etc/systemd/network/10-ens18.network
[Match]
Name=ens18

[Network]
Address=192.168.1.152/24
Gateway=192.168.1.1
DNS=1.1.1.1
EOF

# Step 2: Restart systemd-networkd
sudo systemctl restart systemd-networkd
sleep 3

# Step 3: Confirm IP and routing
ip a show ens18
ip route

# Step 4: Test internet connectivity
echo "Testing ping to 1.1.1.1 (Cloudflare DNS)..."
ping -c 3 1.1.1.1

echo "Testing DNS resolution..."
ping -c 3 google.com

# Step 5: Install NetworkManager if ping succeeded
echo "Installing NetworkManager..."
sudo apt update && sudo apt install -y network-manager

# Step 6: Disable systemd-networkd and enable NetworkManager
sudo systemctl disable systemd-networkd
sudo systemctl stop systemd-networkd
sudo systemctl enable NetworkManager
sudo systemctl start NetworkManager

echo "✅ Static IP configured and NetworkManager installed."

----------------------

# Interface name
IFACE="ens18"

echo "Cleaning up systemd-networkd config (if exists)..."
sudo rm -f /etc/systemd/network/10-${IFACE}.network

echo "Re-enabling NetworkManager for $IFACE..."
# Make sure NetworkManager controls the interface
sudo nmcli dev set "$IFACE" managed yes

echo "Deleting existing NetworkManager connection for $IFACE (if any)..."
sudo nmcli connection delete "$IFACE" 2>/dev/null

echo "Creating new DHCP connection..."
sudo nmcli connection add type ethernet ifname "$IFACE" con-name "$IFACE" autoconnect yes

echo "Bringing up $IFACE..."
sudo nmcli connection up "$IFACE"

echo "✅ $IFACE is now set to DHCP via NetworkManager."

# Optional: show current IP config
ip a show "$IFACE"

```
