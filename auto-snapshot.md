# Automatizzazione Snapshot

Seguire le indicazioni disponibili in questo [repo](https://github.com/francio87/ansible-proxmox), per automatizzare l'installazione attraverso Ansible



## Appunti sparsi

### Crontab:

```
10 21 * * * /usr/local/bin/cv4pve-autosnap --host=localhost --username=backup@pve --password=PASSWORDUTENTE --vmid="all" snap --label='daily' --keep=10 >/dev/null 2>&1
```

### Corsinvest

```
wget https://github.com/Corsinvest/cv4pve-autosnap/releases/download/v1.8.1/cv4pve-autosnap-linux-x64.zip

apt install unzip

mv cv4pve-autosnap /usr/local/bin/
```

```
   16  wget https://github.com/Corsinvest/cv4pve-autosnap/releases/download/v1.11.0/cv4pve-autosnap-linux-x64.zip
   17  ls
   18  apt install unzip
   19  ls
   20  unzip cv4pve-autosnap-linux-x64.zip 
   21  ls
   22  chmod +x cv4pve-autosnap
   23  ./cv4pve-autosnap 
   24  ./cv4pve-autosnap --help
   25  mv cv4pve-autosnap /usr/local/bin/
   26  ls
   27  rm cv4pve-autosnap-linux-x64.zip 
   28  cv4pve-autosnap --help
   29  crontab -l
   30  /usr/local/bin/cv4pve-autosnap --host=localhost --username=nsbackup@pve --password="pass,word1111" --vmid="all" snap --label='daily' --keep=10
``




root@pve-02:~# cat /etc/cron.d/zfs-autosnap
#45 13 * * * root /usr/local/bin/cv4pve-autosnap --host=localhost --username="nsbackup@pve" --password="lapassword" --vmid="all" snap --label='daily' --keep=14 >> /tmp/zfs-autosnap.log
#10 21 * * * root /usr/local/bin/cv4pve-autosnap --host=localhost --username="nsbackup@pve" --password="lapassword" --vmid="all" snap --label='daily' --keep=14 >> /tmp/zfs-autosnap.log
10 21 * * * root /usr/local/bin/cv4pve-autosnap --host=localhost --username="nsbackup@pve" --password="lapassword" --vmid="all" snap --label='daily' --keep=7 >> /tmp/zfs-autosnap.log
