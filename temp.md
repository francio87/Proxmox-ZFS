# Script opt zfs

```
#!/usr/bin/env bash

## based on https://github.com/extremeshok/xshok-proxmox/blob/master/install-post.sh


## Optimise ZFS arc size
if [ "$(command -v zfs)" != "" ] ; then
  RAM_SIZE_GB=$(( $(vmstat -s | grep -i "total memory" | xargs | cut -d" " -f 1) / 1024 / 1000))
  if [[ RAM_SIZE_GB -lt 16 ]] ; then
    # 1GB/1GB
    MY_ZFS_ARC_MIN=1073741824
    MY_ZFS_ARC_MAX=1073741824
  else
    MY_ZFS_ARC_MIN=$((RAM_SIZE_GB * 1073741824 / 16))
    MY_ZFS_ARC_MAX=$((RAM_SIZE_GB * 1073741824 / 8))
  fi
  # Enforce the minimum, incase of a faulty vmstat
  if [[ MY_ZFS_ARC_MIN -lt 1073741824 ]] ; then
    MY_ZFS_ARC_MIN=1073741824
  fi
  if [[ MY_ZFS_ARC_MAX -lt 1073741824 ]] ; then
    MY_ZFS_ARC_MAX=1073741824
  fi
  cat <<EOF > /etc/modprobe.d/zfs.conf
# eXtremeSHOK.com ZFS tuning
# Use 1/16 RAM for MAX cache, 1/8 RAM for MIN cache, or 1GB
options zfs zfs_arc_min=$MY_ZFS_ARC_MIN
options zfs zfs_arc_max=$MY_ZFS_ARC_MAX
# use the prefetch method
options zfs l2arc_noprefetch=0
# max write speed to l2arc
# tradeoff between write/read and durability of ssd (?)
# default : 8 * 1024 * 1024
# setting here : 500 * 1024 * 1024
options zfs l2arc_write_max=524288000
EOF
fi

# propagate the setting into the kernel
update-initramfs -u -k all

```




# NON SEGUIRE
# Automatizzazione snapshot
# da decidere
# come muoversi


In questo punto bisogna valutare bene, quello che vogliamo fare... 
è possibile automatizzare gli snapshot in 2 modi, o utilizzare le api di proxmox, con cv4pve, in sostanza installiamo un container lxc, dove all'interno mettiamo questo script che non fà altro che fare richieste api all'host, e lancia la creazione ed eliminazione degli snapshot delle vm, possiamo decidere se farlo per tutte le macchine o solo per determinate vm

### o in alternativa >

usiamo sanoid, che ci consente di fare lo snapshot a livello zfs, quindi senza dover utilizzare uno script che gira all'interno del container, ma direttamente sull'host con un crontab

differenza sostanziale è che se uno lancia uno snapshot manuale quando utilizza sanoid, a volte per esperienza personale quando lo script fà il prune dei vecchi snapshot, va in confusione dato che il nome non rispetta il formato prefix-data-ora

punto a favore per sanoid è che possiamo fare lo snapshot anche dell'intero tank dove è installato l'os quindi paradossalmente potremmo ripristinare anche l'os se magari l'aggiornamento ha danneggiato qualche componente, addirittura se non fà + il boot la macchina si entra con la usb in debug, si installa zfs tools e si fa il ripristino da live env.

Da testare per quanto riguarda **sanoid** se facciamo un restore dell'intero tank, se si porta dietro anche gli snapshot della vm, ovvero io ripristino

    rpool-giorno-ora/

## lui mi tira dietro 

    rpool-giorno-ora/vm100/disco-100/

## Sanoid
Per abilitare gli snapshot automatici della VM, utilizziamo [sanoid](https://github.com/jimsalterjrs/sanoid)

> Nota : Posso fare la guida step by step per la configurazione dello script, ma magari se lo mettiamo in produzione, potremmo fare noi uno script standard che installa, copia il template e crea i file di cfg no ?

# cv4pve-autosnap

Da ricordarsi, cv4pve utilizza le api, quindi bisogna creare l'utente su proxmox, altrimenti c'è il rischio che se l'utente cambia la password di root, non abbiamo più accesso alle API

```
root@debian:~# cv4pve-autosnap --host=192.168.0.100 --username=root@pam --password=fagiano --vmid=111 clean --label='4hours' --keep=2
----- VM 100 -----
Remove snapshot: auto4hours190617080002
Remove snapshot: auto4hours190617120002
Remove snapshot: auto4hours190617160002
Remove snapshot: auto4hours190617200002
```
Scarichiamo [cv4pve](https://github.com/Corsinvest/cv4pve-autosnap)