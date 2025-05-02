```bash
bash -c "$(curl -fsSL https://raw.githubusercontent.com/community-scripts/ProxmoxVE/main/vm/docker-vm.sh)"
```

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
