# xen-to-pve

## PRIMEIRO PASSO

Via GUI, crie uma VM no pve para gerar o VMID.

## SEGUNDO PASSO

1. Faça login via ssh no pve
1. cd /pool-zfs-bacula/subvol-108-disk-0/XEN-MIGRATE

Execute o comando abaixo:

```
wget --http-user=root --http-password=xxxxx http://172.16.8.183/export?uuid=<VM_UUID> -O - | tar --to-command=./xva-conv.sh -xf -
```

Após isso, renomeie o arquivo gerado para, por ex., disk0.raw

```
qm importdisk 100 disk0.raw pool-zfs-pve
```

Onde `100` é o id da VM, `disks1.raw` é a imagem do disco extraido e renomeado
e `local-zfs` é o pool de onde os discos ficarão armazenados.

## TERCEIRO PASSO

Possivelmente o boot estará corrompido.

1. Coloque o `boot-repair-disk` no drive de DVD
1. Modifique o boot para que o DVD seja o primeiro da lista
1. Após os `boot-repair-disk` realizar as modificações, reinicie a Vm.
1. Cairá no prompt do grub

Execute os comandos:

```
grub> set root=(hd0,msdos2)
grub> linux /boot/vmlinu (tab complete) root=/dev/sda1 (or the root linux partition)
grub> initrd /boot/initrd (tab complete)
grub> boot
```
Após faça o login na VM.

Remova os pacotes:

```
apt purge --autoremove grub-pc grub-common
```

e instale novamente:

```
apt install grub-pc grub-common
```

Modifique o `/etc/default/grub` e deixe como abaixo:

```
GRUB_DEFAULT=0
GRUB_TIMEOUT=10
GRUB_DISTRIBUTOR=`lsb_release...`
GRUB_CMDLINE_LINUX_DEFAULT="quiet"
GRUB_CMDLINE_LINUX=""
```

Execute um `update-grup2`

Possivelmente estará ok para o boot normal.
