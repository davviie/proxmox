# proxmox

The script has been relocated within the repository. The correct URL to download the script is:

```bash
wget https://github.com/tteck/Proxmox/raw/main/misc/post-pve-install.sh
```
After downloading, make it executable:

```bash
chmod +x post-pve-install.sh
```
Configure after 

Fix: Modify the Script to Accept 8.4
If you downloaded post-pve-install.sh, do this:

Open the script:

```bash
nano post-pve-install.sh
```
Find the line that looks like:

```bash
if ! pveversion | grep -Eq "pve-manager/8.[0-2]"; then
```
Change it to:

```bash
if ! pveversion | grep -Eq "pve-manager/8.[0-4]"; then
```
Save & exit (Ctrl + X, press Y, then Enter).

Re-run it:

```bash
./post-pve-install.sh
```
Let me know what version shows up from pveversion, and I can help tailor the fix if needed.
