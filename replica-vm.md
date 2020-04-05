
# Replica VM su secondo nodo

### Abilitare la replica dal primo nodo al secondo

Dal nodo primario, posizioniamoci su ```pve > Replication > Add```

![Replica 1](img/pve-replica-1.png)

Impostiamo i parametri necessari, nel nostro caso:

```
VM ID    : 200
Target   : pve2
Schedule : */2:00
Comment  : Replica VM
Enabled  : [V]
```

![Replica 1](img/pve-replica-2.png)

Finito il primo sync, se è avvenuto tutto correttamente avremo ```status OK```

![Replica OK](img/pve-replica-ok.png)

## Log Replica

La replica avviene attraverso modifiche incrementali, il sistema genera uno snapshot e invia il delta al secondo nodo dove viene integrato con la base del primo sync, quindi dovrebbe essere abbastanza rapida.
Ovviamente maggiore è la quantità dei dati scritti tra un sync e l'altro, maggiore saranno i dati e di conseguenza il tempo impiegato.

```
2020-03-10 10:00:00 200-0: start replication job
2020-03-10 10:00:00 200-0: guest => VM 200, running => 5036
2020-03-10 10:00:00 200-0: volumes => local-zfs:vm-200-disk-0
2020-03-10 10:00:01 200-0: freeze guest filesystem
2020-03-10 10:00:01 200-0: create snapshot '__replicate_200-0_1583830800__' on local-zfs:vm-200-disk-0
2020-03-10 10:00:01 200-0: thaw guest filesystem
2020-03-10 10:00:01 200-0: incremental sync 'local-zfs:vm-200-disk-0' (__replicate_200-0_1583823600__ => __replicate_200-0_1583830800__)
2020-03-10 10:00:02 200-0: send from @__replicate_200-0_1583823600__ to rpool/data/vm-200-disk-0@__replicate_200-0_1583830800__ estimated size is 9.39M
2020-03-10 10:00:02 200-0: total estimated size is 9.39M
2020-03-10 10:00:02 200-0: rpool/data/vm-200-disk-0@__replicate_200-0_1583823600__	name	rpool/data/vm-200-disk-0@__replicate_200-0_1583823600__	-
2020-03-10 10:00:02 200-0: TIME        SENT   SNAPSHOT rpool/data/vm-200-disk-0@__replicate_200-0_1583830800__
2020-03-10 10:00:02 200-0: delete previous replication snapshot '__replicate_200-0_1583823600__' on local-zfs:vm-200-disk-0
2020-03-10 10:00:03 200-0: (remote_finalize_local_job) delete stale replication snapshot '__replicate_200-0_1583823600__' on local-zfs:vm-200-disk-0
2020-03-10 10:00:03 200-0: end replication job
```