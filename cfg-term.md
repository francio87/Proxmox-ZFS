## Aggiornamenti software Proxmox

Per effettuare un [aggiornamento](https://pve.proxmox.com/wiki/System_Software_Updates) eseguire da terminale:

    # apt-get update
    # apt-get dist-upgrade

## Configurazione Schede di rete aggiuntive

> Nota: Valutare l'utilità di aggiungere in questo momento i comandi da terminale o se concentrarsi sulla configurazione via WebUI 

In caso la macchina avesse a disposizione ulteriori schede di rete, è posssibile modificare manualmente il file **/etc/network/interfaces**

    auto lo
    iface lo inet loopback

    iface enp0s3 inet manual

    auto vmbr0
    iface vmbr0 inet static
            address 192.168.5.29
            netmask 255.255.255.0
            gateway 192.168.2.253
            bridge_ports enp0s3
            bridge_stp off
            bridge_fd 0

> /* Da terminare */
