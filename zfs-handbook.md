# ZFS Comandi Utili

## Verificare se tutti i pool sono funzionanti

    root@pve:~# zpool status -x
    all pools are healthy


## Verificare lo stato del pool:

    root@pve:~# zpool status
    pool: rpool
    state: ONLINE
    scan: scrub repaired 0B in 0 days 00:00:08 with 0 errors on Thu Mar  5 09:59:03 2020
    config:

        NAME                                             STATE     READ WRITE CKSUM
        rpool                                            ONLINE       0     0     0
        mirror-0                                       ONLINE       0     0     0
            ata-VBOX_HARDDISK_VBab9283d6-24a521b8-part3  ONLINE       0     0     0
            ata-VBOX_HARDDISK_VBdd1a43b5-92ab5ce0-part3  ONLINE       0     0     0

    errors: No known data errors

## Eseguire Scrub del pool (scandisk)

Per eseguire uno scrub (scandisk) sul pool di default che crea Proxmox (rpool) lanciare:

    root@pve:~# zpool scrub rpool
e sucessivamente eseguire il comando 'zpool status' per verificare lo stato dello scan

    root@pve:~# zpool status
    pool: rpool
    state: ONLINE
    scan: scrub in progress since Thu Mar  5 12:37:46 2020
        925M scanned at 231M/s, 423M issued at 106M/s, 926M total
        0B repaired, 45.74% done, 0 days 00:00:04 to go
    config:

        NAME                                             STATE     READ WRITE CKSUM
        rpool                                            ONLINE       0     0     0
        mirror-0                                       ONLINE       0     0     0
            ata-VBOX_HARDDISK_VBab9283d6-24a521b8-part3  ONLINE       0     0     0
            ata-VBOX_HARDDISK_VBdd1a43b5-92ab5ce0-part3  ONLINE       0     0     0

    errors: No known data errors

Al termine del processo rilanciando il comando 'zpool status' avremo il resoconto alla voce scan:

    scan: scrub repaired 0B in 0 days 00:00:08 with 0 errors on Thu Mar  5 12:37:54 2020


# ZFS Troubleshooting

## Possibili problemi riscontrati su Proxmox

Il sistema non si avvia e mostra un cursore lampeggiante



Symptoms: stuck at boot with an blinking prompt.
Reason: If you ZFS raid it could happen that your mainboard does not initial all your disks correctly and Grub will wait for all RAID disk members - and fails. It can happen with more than 2 disks in ZFS RAID configuration - we saw this on some boards with ZFS RAID-0/RAID-10
Boot fails and goes into busybox
If booting fails with something like

No pool imported. Manually import the root pool
at the command prompt and then exit.
Hint: try: zpool import -R /rpool -N rpool
is because zfs is invoked too soon (it has happen sometime when connecting a SSD for future ZIL configuration). To prevent it there have been some suggestions in the forum. Try to boot following the suggestions of busybox or searching the forum, and try ONE of the following:

a) edit /etc/default/grub and add "rootdelay=10" at GRUB_CMDLINE_LINUX_DEFAULT (i.e. GRUB_CMDLINE_LINUX_DEFAULT="rootdelay=10 quiet") and then issue a # update-grub

b) edit /etc/default/zfs, set ZFS_INITRD_PRE_MOUNTROOT_SLEEP='4', and then issue a "update-initramfs -k 4.2.6-1-pve -u"