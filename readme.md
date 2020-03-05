
Procedura installazione **Proxmox** con **RAID1 ZFS**
Nota: Proxmox non supporta [mdraid](https://pve.proxmox.com/wiki/Software_RAID) per garantire la rindondanza dei dati si consiglia l'uso di ZFS


## Preparazione media installazione

### Chiavetta USB
Procedura realizzazione USB

## Installazione

Avviare l'installazione dal supporto creato, selezionare **"Install Proxmox VE"** 
Nella schermata selezione dischi, premere il pulsante **Options** 

    **Filesystem** : **zfs (RAID1)**
    Harddisk 0: /dev/sda 
    Harddisk 1: /dev/sdb
    Selezionare Paese / Timezone / Keyboard Layout secondo preferenze
    MGMT Network Configuration: Selezionare l'interfaccia di rete
    Hostname FQDN: 
    IP: 
    Netmask:
    GW:
    DNS Server:
