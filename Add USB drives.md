Step 1: Identify the Drive
Plug in the external USB HDD, then run:

bash
lsblk -o NAME,SIZE,FSTYPE,MOUNTPOINT,MODEL

You're looking for something like:

r

sdb      2T                   
└─sdb1   2T   ext4     /mnt/external

Take note of:

Device name (/dev/sdb or similar)

Partition (/dev/sdb1)

Filesystem (e.g., ext4, ntfs, etc.)

✅ Step 2: Mount the Drive on the Proxmox Host
Create a mount point:

bash

mkdir -p /mnt/external

Then mount it:

bash
mount /dev/sdb1 /mnt/external

If it's NTFS, you may need ntfs-3g:

If it's an NTFS drive and you want to use the ntfs-3g driver explicitly (optional):

bash
ntfs-3g /dev/sde1 /mnt/20tb

apt install ntfs-3g

✅ Step 3: Make It Persistent

Edit /etc/fstab so it remounts on reboot.

First, get the UUID:

bash

blkid /dev/sdb1

You'll see something like:

pgsql

/dev/sdb1: UUID="abc123-xyz" TYPE="ext4"

Then add a line to /etc/fstab:

bash

UUID=abc123-xyz /mnt/external ext4 defaults 0 2

(Adjust ext4 if it's NTFS or exFAT)

Test it:

bash

mount -a

✅ Step 4: Expose the HDD to VMs
Now you have 3 options:

🔹 Option A: Bind Mount a Directory into an LXC
Add a mount point in the container config:

bash

nano /etc/pve/lxc/100.conf

Add:

bash
mp0: /mnt/external,mp=/mnt/usb,backup=0

Then restart the container. /mnt/usb will be available inside.

🔹 Option B: Add as a Disk to a VM (via passthrough)
If you want the entire HDD accessible in a VM (e.g. Windows or Linux guest):

Shut down the VM.

Go to VM → Hardware → Add → Physical Disk.

Select /dev/sdb.

Set "Use Entire Disk" if desired.

Then start the VM — the drive should show as a new disk.

🛑 Warning: Don’t mount it on the host and passthrough at the same time — one or the other!

🔹 Option C: Share the Drive via NFS/SMB
You can share /mnt/external via NFS or Samba to multiple VMs/containers — great for shared media.

1. Edit the /etc/fstab file
Run:

bash
nano /etc/fstab

Then add this line at the bottom:

ini
UUID=B08F-0B49 /mnt/2tb exfat defaults,uid=0,gid=0,umask=000 0 0

Make sure the folder /mnt/2tb exists first:

bash
mkdir -p /mnt/2tb

2. Test the mount
After saving the file, run:

bash
mount -a

If there’s no error, your drive is now properly mounted and will auto-mount at boot.
