# Preparazione macchina in Stand-By sul secondo nodo

La replica sul secondo nodo avviene solo a livello storage, la [wiki di Proxmox](https://pve.proxmox.com/wiki/Storage_Replication#_error_handling), raccomanda la copia dei cfg della VM sul secondo nodo solo se si verificano problemi al primo, per evitare l'avvio della VM per sbaglio dal secondo nodo ( in caso si avviasse la macchina ci sarebbe un incongruenza dei dati).

## Avviare la macchina solo ed esclusivamente ci fossero problemi con il primo nodo (PVE)

> E' molto importante ricordare che un avvio della macchina, quando il server primario è ancora in funzione, comporterebbe una incongruenza sul disco, la wiki di Proxmox consiglia questo passaggio solo ed esclusivamente in caso il nodo primario sia down o che il processo di replica sia stoppato.

### Copia config VM

Copiamo la configurazione della VM (```pve```) al nodo secondario (```pve2```).

Grazie a corosync, i file di configurazione delle VM del primo nodo dovrebbero essere comunque accessibili, anche se il nodo non risponde:

```
root@pve:cat /etc/pve/nodes/pve/qemu-server/200.conf
agent: 1
bootdisk: scsi0
cores: 4
ide2: none,media=cdrom
memory: 4096
name: vm-nethservice
net0: virtio=92:C7:17:60:3E:68,bridge=vmbr0
numa: 0
ostype: l26
scsi0: local-zfs:vm-200-disk-0,cache=writeback,discard=on,size=32G
scsihw: virtio-scsi-pci
smbios1: uuid=064fa99f-f5a3-4f95-bbea-1b8770307ea9
sockets: 1
vmgenid: e45eee04-142e-4c16-bc23-f007104194a0
```
Copiamo il file di configurazione della vm 