# Automatizzazione Snapshot


Senza ZFS-

crontab:

10 21 * * * /usr/local/bin/cv4pve-autosnap --host=localhost --username=backup@pve --password=PASSWORDUTENTE --vmid="all" snap --label='daily' --keep=10 >/dev/null 2>&1
root@proxmox:~# 

wget https://github.com/Corsinvest/cv4pve-autosnap/releases/download/v1.8.1/cv4pve-autosnap-linux-x64.zip

apt install unzip

mv cv4pve-autosnap /usr/local/bin/