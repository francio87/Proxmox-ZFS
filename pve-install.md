# Proxmox

Procedura installazione **Proxmox** con **RAID1 ZFS**

Nota: Proxmox non supporta [mdraid](https://pve.proxmox.com/wiki/Software_RAID) per garantire la ridondanza dei dati si consiglia l'uso di **ZFS**


## Preparazione media installazione

### Chiavetta USB

### Procedura Linux :
Dopo aver scaricato la ISO di Proxmox, scrivere con **dd** l'immagine sulla penna USB:

Se non avete permessi di scrittura, utilizzate **sudo**

Sostituire i valori if="" con il percorso corretto della iso e of="" con il path corretto della USB

```
dd bs=1M conv=fdatasync if=/path/to/iso/proxmox-ve_*.iso of=/dev/XYZ status=progress
```

Esempio

```
dav@davidef  ~: sudo dd bs=1M conv=fdatasync if=/home/dav/Scaricati/proxmox-ve_6.1-1.iso of=/dev/sdb
776+1 record dentro
776+1 record fuori
813922304 bytes (814 MB, 776 MiB) copied, 318,393 s, 2,6 MB/s
```

### Procedura Windows

Utilizzare [BalenaEtcher](https://www.balena.io/etcher/) oppure [Rufus](https://rufus.ie/)
> Rufus non testato personalmente


## Installazione

Avviare l'installazione dal supporto creato, selezionare **"Install Proxmox VE"**

![Installazione](img/pve-grub-menu.png)

Nella schermata selezione dischi, premere il pulsante **Options** 

![Selezione Dischi](img/pve-select-target-disk.png)

    Filesystem : zfs (RAID1)
    Harddisk 0: /dev/xxx
    Harddisk 1: /dev/xxx


Selezionare **Paese** / **Timezone** / **Keyboard Layout** secondo preferenze

![Timezone](img/pve-select-location.png)

## Impostazioni di Rete

Selezionare l'interfaccia di rete utilizzata per la gestione dell'HV

```
Hostname FQDN: pve.dominio.lan
IP: 192.168.x.x
Netmask: 255.255.255.0
GW: 192.168.x.x
DNS Server: 192.168.x.x
```

![Impostazioni rete](img/pve-setup-network.png)

### Impostazione Password ed indirizzo mail

```
Password : Nethesis,1234
Confirm : Nethesis,1234
email : email@valida.com
```

Nota: Proxmox di default reindirizza le email di sistema all'account specificato sopra 

![Impostazione Password ed indirizzo mail](img/pve-set-password.png)
### Verifica parametri

Verificare nel sommario che tutte le impostazioni di siano corrette e premere il pulsante **Install**

![Verifica sommario informazioni](img/pve-install-summary.png)

# Login WebGUI

E' possibile accedere alla WebUI per la configurazione dell'hypervisor utilizzando un qualsiasi browser e collegandosi all'indirizzo https://ipmgmt:8006 (sostituire IP con l'indirizzo IP assegnato alla MGMT Interface)

Effettuare l'accesso come **root** utilizzando la password impostata precedentemente, nel nostro caso **Nethesis,1234**

```
username: root
password: Nethesis,1234
```

![Login WebUI](img/pve-login-web.png)

> Nota: è possibile accedere alla macchina anche via SSH

```
dav@davidef ~ >> ssh root@192.168.5.29
Linux pve 5.3.10-1-pve #1 SMP PVE 5.3.10-1 (Thu, 14 Nov 2019 10:43:13 +0100) x86_64
```

# Attivazione ?

Dovremmo attivare la macchina, ottenendo una licenza ecc ecc... altrimenti non avremmo accesso ai repo stabili... Eventualmente se vogliamo usare i community posso integrare con i step da effettuare per abilitare i repo community.

```
PVE > Subscription > Upload Key
```

![Attivazione chiave](img/pve-sub-key.png)

## Aggiornamento Proxmox

Dopo aver attivato la licenza di Proxmox, aggiornare il sistema:

```
PVE > Updates > Refresh > Upgrade
```
Verrà aperta una shell, sulla quale è necessario immettere Y per procedere all'aggiornamento

In alternativa da terminale :

```
# apt-get update
# apt-get dist-upgrade
```

![Ricerca Aggiornamenti](img/pve-update-1.png)

![Upgrade](img/pve-update-2.png)

![Riavvio Richiesto](img/pve-update-3.png)

In caso fosse un update del kernel, è consigliato eseguire il riavvio dell'host, **Proxmox** ci avvisa con il messaggio:

    Seems you installed a new kernel update - Please consider rebooting this node to activate the new kernel.

Possiamo riavviare via interfaccia grafica di **Proxmox**

 ```
 pve > Summary > Reboot > Yes
 ```
 
 Oppure da terminale con :

```
root@pve:~# reboot
 ```


# DA SPLITTARE
# SU UN ALTRA GUIDA
# IL CLUSTER DEVE ESSERE CREATO
# PRIMA DI CREARE UNA NUOVA VM


# Caricare ISO NethServer

Carichiamo la ISO di NethServer all'interno di Proxmox

```
PVE > local (pve) > Content
```

Facciamo click su **Upload**

![Click Upload](img/pve-load-iso-1.png)

Facciamo click su **Select File...** e cerchiamo il file ISO precedentemente Scaricato

![Select File](img/pve-load-iso-2.png) ![Upload ISO](img/pve-load-iso-4.png)

Una volta completato l'upload, sarà visibile sulla tab **content** del local storage

![sda](img/pve-load-iso-5.png)

# Creazione VM

E' possbile utilizzare la WebUI per la creazione della VM

Fare clik su **Create VM** in alto a destra ed inserire i parametri nella schermata e fare clic su **Next** a fondo schermata

```
Inserire VM ID: 200
Inserire Name: vm-nethservice
```

![Crea VM Step 1](img/pve-crea-vm-1.png)

Selezioniamo il file **ISO** precedentemente caricato e facciamo click su **Next**

![Crea VM Step 2](img/pve-crea-vm-2.png)

Mettiamo il segno di spunta su : **Qemu Agent**

```
Qemu Agent: [V]
```

![Crea VM Step 2](img/pve-crea-vm-3.png)

Verifichiamo che **Disk size (GiB)** sia della grandezza da noi richiesta e controlliamo impostiamo seguenti parametri :

```
Storage: local-zfs
Cache: Write back
Discard [V]
```

![Crea VM Step 2](img/pve-crea-vm-4.png)

Assegnamo almeno 4 core alla VM

```
Cores: 4
```

![Crea VM Step 2](img/pve-crea-vm-5.png)

Assegnamo almeno 4GB di RAM alla VM

```
Memory (MiB): 4096
```

![Crea VM Step 2](img/pve-crea-vm-6.png)

Rimuoviamo il segno di spunta da:

```
Firewall [ ]
```

![Crea VM Step 2](img/pve-crea-vm-7.png)

Verifichiamo che tutti i parametri siano corretti nella sommario finale, **spuntiamo la flag**

```
[V] Start after created
```

e facciamo click su **Finish**

![Crea VM Step 2](img/pve-crea-vm-8.png)

# Installazione NethService sulla VM

Apriamo la console della VM creata e procediamo all'installazione Standard di NethServer [Link alla Guida](https://nethserver.docs.nethesis.it/it/v7/installation.html)

Lato destro della schermata, espandere **pve**, selezionare la VM **200 (vm-nethservice)** e fare click in alto a destra su **>_Console**

```
pve > 200 (vm-nethservice) > Console
```

![Start Console VM](img/pve-start-vm-console-1.png)

![Start Console VM](img/pve-neth-install-1.png)

# Procedura Post Installazione Nethserver

## Installazione Guest Agent

Una volta terminata la normale installazione di NethServer, dobbiamo installare le **Qemu-Guest-Agent** da terminale di NethServer:

```
[root@ns ~]# yum -y install qemu-guest-agent
```

## Rimozione Disco Installazione

Dopo la corretta installazione di NethServer, rimuovere la ISO dalla VM.

```
pve > 200 (vm-nethservice) > Hardware > CD/DVD Drive > Edit > Do not use any media
```
![Start Console VM](img/pve-crea-vm-9.png)







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