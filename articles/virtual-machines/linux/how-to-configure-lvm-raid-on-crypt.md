---
title: Az LVM és a RAID konfigurálása titkosított eszközökön – Azure Disk Encryption
description: Ez a cikk útmutatást nyújt az LVM és a RAID konfigurálásához a Linux rendszerű virtuális gépek titkosított eszközein.
author: jofrance
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: how-to
ms.author: jofrance
ms.date: 03/17/2020
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: ec9f99d0a13b5b92bc267f184d364ebabe36a050
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102566107"
---
# <a name="configure-lvm-and-raid-on-encrypted-devices"></a>Az LVM és a RAID konfigurálása titkosított eszközökön

Ez a cikk lépésről lépésre bemutatja, hogyan végezhető el a logikai kötetek kezelése (LVM) és a RAID a titkosított eszközökön. A folyamat a következő környezetekre vonatkozik:

- Linux-disztribúciók
    - RHEL 7.6 +
    - Ubuntu 18.04 +
    - SUSE 12 +
- Azure Disk Encryption Single-pass bővítmény
- Azure Disk Encryption Dual-pass bővítmény


## <a name="scenarios"></a>Forgatókönyvek

A cikkben ismertetett eljárások a következő forgatókönyveket támogatják:  

- Az LVM konfigurálása titkosított eszközökön (LVM-on-Crypt)
- RAID konfigurálása titkosított eszközökön (RAID-on-Crypt)

Ha a mögöttes eszköz vagy eszköz titkosítva van, akkor az LVM vagy RAID-struktúrákat a titkosított réteg fölé is létrehozhatja. 

A fizikai kötetek (PVs-EK) a titkosított réteg felett jönnek létre. A fizikai kötetek a kötet csoport létrehozásához használatosak. Létrehozza a köteteket, és hozzáadja a szükséges bejegyzéseket a/etc/fstab. 

![Az LVM-struktúrák rétegeinek ábrája](./media/disk-encryption/lvm-raid-on-crypt/000-lvm-raid-crypt-diagram.png)

A RAID-eszköz hasonló módon jön létre a lemezeken található titkosított rétegen. A rendszer létrehoz egy fájlrendszert a RAID-eszköz tetején, és az/etc/fstab-t normál eszközként adja hozzá.

## <a name="considerations"></a>Megfontolandó szempontok

Javasoljuk, hogy használjon LVM-on-Crypt-t. A RAID olyan lehetőség, ha az LVM nem használható meghatározott alkalmazás-vagy környezeti korlátozások miatt.

A **EncryptFormatAll** kapcsolót fogja használni. További információ erről a lehetőségről: [a Linux rendszerű virtuális gépeken található adatlemezek EncryptFormatAll funkciójának használata](./disk-encryption-linux.md#use-encryptformatall-feature-for-data-disks-on-linux-vms).

Bár ez a módszer akkor használható, ha az operációs rendszert is titkosítja, csak az adatmeghajtókat titkosítjuk.

Az eljárások feltételezik, hogy már áttekintette az előfeltételeket a Linux rendszerű [virtuális gépeken Azure Disk Encryption forgatókönyvekben](./disk-encryption-linux.md) , valamint a gyors üzembe helyezést [: linuxos virtuális gép létrehozása és titkosítása az Azure CLI](./disk-encryption-cli-quickstart.md)használatával.

A Azure Disk Encryption Dual-pass verzió elavult elérési úton van, és az új titkosítások nem használhatók fel többé.

## <a name="general-steps"></a>Általános lépések

Ha a "rendszertitkosítási" konfigurációt használja, használja az alábbi eljárásokban leírt eljárást.

>[!NOTE] 
>A cikk során változókat használunk. Cserélje le az értékeket ennek megfelelően.

### <a name="deploy-a-vm"></a>Virtuális gép üzembe helyezése 
A következő parancsok nem kötelezőek, de azt javasoljuk, hogy alkalmazza őket egy újonnan telepített virtuális gépre (VM).

PowerShell:

```powershell
New-AzVm -ResourceGroupName ${RGNAME} `
-Name ${VMNAME} `
-Location ${LOCATION} `
-Size ${VMSIZE} `
-Image ${OSIMAGE} `
-Credential ${creds} `
-Verbose
```
Azure CLI:

```azurecli
az vm create \
-n ${VMNAME} \
-g ${RGNAME} \
--image ${OSIMAGE} \
--admin-username ${username} \
--admin-password ${password} \
-l ${LOCATION} \
--size ${VMSIZE} \
-o table
```
### <a name="attach-disks-to-the-vm"></a>Lemezek csatlakoztatása a virtuális géphez
A `$N` virtuális géphez csatolni kívánt új lemezek számának megismétléséhez ismételje meg a következő parancsokat.

PowerShell:

```powershell
$storageType = 'Standard_LRS'
$dataDiskName = ${VMNAME} + '_datadisk0'
$diskConfig = New-AzDiskConfig -SkuName $storageType -Location $LOCATION -CreateOption Empty -DiskSizeGB 5
$dataDisk1 = New-AzDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName ${RGNAME}
$vm = Get-AzVM -Name ${VMNAME} -ResourceGroupName ${RGNAME} 
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 0
Update-AzVM -VM ${VM} -ResourceGroupName ${RGNAME}
```

Azure CLI:

```azurecli
az vm disk attach \
-g ${RGNAME} \
--vm-name ${VMNAME} \
--name ${VMNAME}datadisk1 \
--size-gb 5 \
--new \
-o table
```

### <a name="verify-that-the-disks-are-attached-to-the-vm"></a>Annak ellenőrzése, hogy a lemezek csatlakoztatva vannak-e a virtuális géphez
PowerShell:
```powershell
$VM = Get-AzVM -ResourceGroupName ${RGNAME} -Name ${VMNAME}
$VM.StorageProfile.DataDisks | Select-Object Lun,Name,DiskSizeGB
```
![Csatolt lemezek listája a PowerShellben](./media/disk-encryption/lvm-raid-on-crypt/001-lvm-raid-check-disks-powershell.png)

Azure CLI:

```azurecli
az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.dataDisks -o table
```
![Csatolt lemezek listája az Azure CLI-ben](./media/disk-encryption/lvm-raid-on-crypt/002-lvm-raid-check-disks-cli.png)

Portál:

![A portálon található csatolt lemezek listája](./media/disk-encryption/lvm-raid-on-crypt/003-lvm-raid-check-disks-portal.png)

Operációs rendszer:

```bash
lsblk 
```
![Az operációs rendszerhez csatlakoztatott lemezek listája](./media/disk-encryption/lvm-raid-on-crypt/004-lvm-raid-check-disks-os.png)

### <a name="configure-the-disks-to-be-encrypted"></a>A lemezek titkosításának konfigurálása
Ez a konfiguráció az operációs rendszer szintjén történik. A megfelelő lemezek a Azure Disk Encryptionon keresztül hagyományos titkosításra vannak konfigurálva:

- A fájlrendszereket a rendszer a lemezek tetején hozza létre.
- Ideiglenes csatlakoztatási pontok jönnek létre a fájlrendszerek csatlakoztatásához.
- A fájlrendszerek a rendszerindítási időben való csatlakoztatáshoz vannak konfigurálva az/etc/fstab eszközön.

Keresse meg az új lemezekhez rendelt eszköz betűjelét. Ebben a példában négy adatlemezt használunk.

```bash
lsblk 
```
![Az operációs rendszerhez csatolt adatlemezek](./media/disk-encryption/lvm-raid-on-crypt/004-lvm-raid-check-disks-os.png)

### <a name="create-a-file-system-on-top-of-each-disk"></a>Fájlrendszer létrehozása az egyes lemezeken
Ez a parancs egy ext4 fájlrendszer létrehozását ismétli meg a "for" ciklus "in" részében meghatározott összes lemezen.

```bash
for disk in c d e f; do echo mkfs.ext4 -F /dev/sd${disk}; done |bash
```
![Ext4 fájlrendszer létrehozása](./media/disk-encryption/lvm-raid-on-crypt/005-lvm-raid-create-temp-fs.png)

Keresse meg a közelmúltban létrehozott fájlrendszerek univerzálisan egyedi azonosítóját (UUID), hozzon létre egy ideiglenes mappát, adja hozzá a megfelelő bejegyzéseket az/etc/fstab fájlhoz, és csatlakoztassa az összes fájlrendszert.

Ez a parancs a "for" ciklus "in" szakaszában meghatározott minden lemezen is megismétli a következőt:

```bash
for disk in c d e f; do diskuuid="$(blkid -s UUID -o value /dev/sd${disk})"; \
mkdir /tempdata${disk}; \
echo "UUID=${diskuuid} /tempdata${disk} ext4 defaults,nofail 0 0" >> /etc/fstab; \
mount -a; \
done
``` 

### <a name="verify-that-the-disks-are-mounted-properly"></a>Ellenőrizze, hogy a lemezek megfelelően vannak-e csatlakoztatva
```bash
lsblk
```
![A csatlakoztatott ideiglenes fájlrendszerek listája](./media/disk-encryption/lvm-raid-on-crypt/006-lvm-raid-verify-temp-fs.png)

Ellenőrizze azt is, hogy a lemezek konfigurálva vannak-e:

```bash
cat /etc/fstab
```
![Konfigurációs információk az fstab-on keresztül](./media/disk-encryption/lvm-raid-on-crypt/007-lvm-raid-verify-temp-fstab.png)

### <a name="encrypt-the-data-disks"></a>Adatlemezek titkosítása
PowerShell a kulcs titkosítási kulcsának (KEK) használatával:

```powershell
$sequenceVersion = [Guid]::NewGuid() 
Set-AzVMDiskEncryptionExtension -ResourceGroupName $RGNAME `
-VMName ${VMNAME} `
-DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl `
-DiskEncryptionKeyVaultId $KeyVaultResourceId `
-KeyEncryptionKeyUrl $keyEncryptionKeyUrl `
-KeyEncryptionKeyVaultId $KeyVaultResourceId `
-VolumeType 'DATA' `
-EncryptFormatAll `
-SequenceVersion $sequenceVersion `
-skipVmBackup;
```

Azure CLI a KEK használatával:

```azurecli
az vm encryption enable \
--resource-group ${RGNAME} \
--name ${VMNAME} \
--disk-encryption-keyvault ${KEYVAULTNAME} \
--key-encryption-key ${KEYNAME} \
--key-encryption-keyvault ${KEYVAULTNAME} \
--volume-type "DATA" \
--encrypt-format-all \
-o table
```
### <a name="verify-the-encryption-status"></a>A titkosítási állapot ellenőrzése

Csak akkor folytassa a következő lépéssel, ha az összes lemez titkosítva van.

PowerShell:

```powershell
Get-AzVmDiskEncryptionStatus -ResourceGroupName ${RGNAME} -VMName ${VMNAME}
```
![Titkosítási állapot a PowerShellben](./media/disk-encryption/lvm-raid-on-crypt/008-lvm-raid-verify-encryption-status-ps.png)

Azure CLI:

```azurecli
az vm encryption show -n ${VMNAME} -g ${RGNAME} -o table
```
![Titkosítási állapot az Azure CLI-ben](./media/disk-encryption/lvm-raid-on-crypt/009-lvm-raid-verify-encryption-status-cli.png)

Portál:

![Titkosítási állapot a portálon](./media/disk-encryption/lvm-raid-on-crypt/010-lvm-raid-verify-encryption-status-portal.png)

Operációs rendszer szintje:

```bash
lsblk
```
![Titkosítási állapot az operációs rendszerben](./media/disk-encryption/lvm-raid-on-crypt/011-lvm-raid-verify-encryption-status-os.png)

A bővítmény felveszi a fájlrendszereket a/var/lib/azure_disk_encryption_config/azure_crypt_mount (régi titkosítás) vagy a/etc/crypttab (új titkosítások).

>[!NOTE] 
>Ne módosítsa ezeket a fájlokat.

Ez a fájl a rendszerindítási folyamat során gondoskodik a lemezek aktiválásáról, így az LVM vagy a RAID később is használhatja őket. 

Ne aggódjon a fájl csatlakoztatási pontjaival kapcsolatban. A Azure Disk Encryption elveszíti a normál fájlrendszerhez csatlakoztatott lemezek beolvasásának lehetőségét, miután létrehozunk egy fizikai kötetet vagy egy RAID-eszközt a titkosított eszközökön. (Ez a művelet eltávolítja az előkészítési folyamat során használt fájlrendszer-formátumot.)

### <a name="remove-the-temporary-folders-and-temporary-fstab-entries"></a>Az ideiglenes mappák és az ideiglenes fstab-Bejegyzések eltávolítása
Leválasztja azokat a lemezeket, amelyeket a rendszer az LVM részeként fog használni.

```bash
for disk in c d e f; do unmount /tempdata${disk}; done
```
És távolítsa el az/etc/fstab-bejegyzéseket:

```bash
vi /etc/fstab
```
### <a name="verify-that-the-disks-are-not-mounted-and-that-the-entries-on-etcfstab-were-removed"></a>Győződjön meg arról, hogy a lemezek nincsenek csatlakoztatva, és hogy az/etc/fstab bejegyzései el lettek távolítva

```bash
lsblk
```
![Annak ellenőrzése, hogy az ideiglenes fájlrendszerek le vannak-e választva](./media/disk-encryption/lvm-raid-on-crypt/012-lvm-raid-verify-disks-not-mounted.png)

Ellenőrizze, hogy a lemezek konfigurálva vannak-e:
```bash
cat /etc/fstab
```
![Az ideiglenes fstab-bejegyzések eltávolításának ellenőrzése](./media/disk-encryption/lvm-raid-on-crypt/013-lvm-raid-verify-fstab-temp-removed.png)

## <a name="steps-for-lvm-on-crypt"></a>Az LVM-on-Crypt lépések lépései
Most, hogy az alapul szolgáló lemezek titkosítva vannak, létrehozhatók az LVM-struktúrák.

Az eszköz neve helyett az egyes lemezek/dev/Mapper elérési útjait használva hozzon létre egy fizikai kötetet (a lemez tetején lévő Crypt rétegen, ne a lemezen).

### <a name="configure-lvm-on-top-of-the-encrypted-layers"></a>Az LVM konfigurálása a titkosított rétegek felett
#### <a name="create-the-physical-volumes"></a>Fizikai kötetek létrehozása
Megjelenik egy figyelmeztetés, amely megkérdezi, hogy rendben van-e a fájlrendszer aláírásának törlése. Folytassa az **y** érték megadásával, vagy használja az **echo "y" karaktert** az alábbiak szerint:

```bash
echo "y" | pvcreate /dev/mapper/c49ff535-1df9-45ad-9dad-f0846509f052
echo "y" | pvcreate /dev/mapper/6712ad6f-65ce-487b-aa52-462f381611a1
echo "y" | pvcreate /dev/mapper/ea607dfd-c396-48d6-bc54-603cf741bc2a
echo "y" | pvcreate /dev/mapper/4159c60a-a546-455b-985f-92865d51158c
```
![Fizikai kötet létrehozásának ellenőrzése](./media/disk-encryption/lvm-raid-on-crypt/014-lvm-raid-pvcreate.png)

>[!NOTE] 
>A/dev/Mapper/Device nevét a **lsblk** kimenete alapján kell kicserélni a tényleges értékekre.

#### <a name="verify-the-information-for-physical-volumes"></a>A fizikai kötetek adatainak ellenőrzése
```bash
pvs
```

![Fizikai kötetek adatai](./media/disk-encryption/lvm-raid-on-crypt/015-lvm-raid-pvs.png)

#### <a name="create-the-volume-group"></a>A kötet csoport létrehozása
Hozza létre a kötetet ugyanazon eszközök használatával, amelyek már inicializálva vannak:

```bash
vgcreate vgdata /dev/mapper/
```

### <a name="check-the-information-for-the-volume-group"></a>A mennyiségi csoport információinak megtekintése

```bash
vgdisplay -v vgdata
```
```bash
pvs
```
![A kötet csoportjának adatai](./media/disk-encryption/lvm-raid-on-crypt/016-lvm-raid-pvs-on-vg.png)

#### <a name="create-logical-volumes"></a>Logikai kötetek létrehozása

```bash
lvcreate -L 10G -n lvdata1 vgdata
lvcreate -L 7G -n lvdata2 vgdata
``` 

#### <a name="check-the-created-logical-volumes"></a>A létrehozott logikai kötetek keresése

```bash
lvdisplay
lvdisplay vgdata/lvdata1
lvdisplay vgdata/lvdata2
```
![A logikai kötetek információi](./media/disk-encryption/lvm-raid-on-crypt/017-lvm-raid-lvs.png)

#### <a name="create-file-systems-on-top-of-the-structures-for-logical-volumes"></a>Fájlrendszerek létrehozása a logikai kötetek struktúráján

```bash
echo "yes" | mkfs.ext4 /dev/vgdata/lvdata1
echo "yes" | mkfs.ext4 /dev/vgdata/lvdata2
```

#### <a name="create-the-mount-points-for-the-new-file-systems"></a>Az új fájlrendszerek csatlakoztatási pontjainak létrehozása

```bash
mkdir /data0
mkdir /data1
```

#### <a name="add-the-new-file-systems-to-etcfstab-and-mount-them"></a>Az új fájlrendszerek hozzáadása az/etc/fstabhez és csatlakoztatása

```bash
echo "/dev/mapper/vgdata-lvdata1 /data0 ext4 defaults,nofail 0 0" >>/etc/fstab
echo "/dev/mapper/vgdata-lvdata2 /data1 ext4 defaults,nofail 0 0" >>/etc/fstab
mount -a
```

#### <a name="verify-that-the-new-file-systems-are-mounted"></a>Ellenőrizze, hogy az új fájlrendszerek csatlakoztatva vannak-e

```bash
lsblk -fs
df -h
```
![A képernyőképen egy data0 és Data1-ként csatlakoztatott fájlrendszerek láthatók.](./media/disk-encryption/lvm-raid-on-crypt/018-lvm-raid-lsblk-after-lvm.png)

A **lsblk** ezen változatában a függőségeket a fordított sorrendben megjelenítő eszközöket listázjuk. Ezzel a beállítással azonosíthatók azok az eszközök, amelyek a logikai kötet szerint csoportosítva vannak, az eredeti/dev/SD [Disk]-eszközök nevei helyett.

Fontos, hogy a **nem** megfelelő beállítás bekerüljön a Azure Disk Encryption használatával titkosított eszközön létrehozott LVM kötetek csatlakoztatási pontjának beállításaiba. Megakadályozza, hogy az operációs rendszer elragadjon a rendszerindítási folyamat során (vagy karbantartási módban).

Ha nem használja a nem **sikertelen** beállítást:

- Az operációs rendszer soha nem fog bekerülni az Azure Disk Encryption elindításának szakaszába, és az adatlemezek fel vannak oldva és csatlakoztatva vannak. 
- A titkosított lemezek a rendszerindítási folyamat végén fel lesznek oldva. Az LVM-kötetek és-fájlrendszerek automatikusan csatlakoztatva lesznek, amíg Azure Disk Encryption feloldja őket. 

Tesztelheti a virtuális gép újraindítását, és ellenőrizheti, hogy a fájlrendszerek a rendszerindítás után is automatikusan bekerülnek-e. Ez a folyamat több percet is igénybe vehet, a fájlrendszerek számától és méretétől függően.

#### <a name="reboot-the-vm-and-verify-after-reboot"></a>Indítsa újra a virtuális gépet, és ellenőrizze az újraindítás után

```bash
shutdown -r now
```
```bash
lsblk
df -h
```
## <a name="steps-for-raid-on-crypt"></a>RAID – Crypt – lépések
Most, hogy az alapul szolgáló lemezek titkosítva vannak, továbbra is létrehozhatja a RAID-struktúrákat. A folyamat ugyanaz, mint az LVM, de az eszköz neve helyett használja az egyes lemezek/dev/Mapper elérési útját.

#### <a name="configure-raid-on-top-of-the-encrypted-layer-of-the-disks"></a>A RAID konfigurálása a lemezek titkosított rétegének tetején
```bash
mdadm --create /dev/md10 \
--level 0 \
--raid-devices=4 \
/dev/mapper/c49ff535-1df9-45ad-9dad-f0846509f052 \
/dev/mapper/6712ad6f-65ce-487b-aa52-462f381611a1 \
/dev/mapper/ea607dfd-c396-48d6-bc54-603cf741bc2a \
/dev/mapper/4159c60a-a546-455b-985f-92865d51158c
```
![A konfigurált RAID adatai a mdadm parancs használatával](./media/disk-encryption/lvm-raid-on-crypt/019-lvm-raid-md-creation.png)

>[!NOTE] 
>A/dev/Mapper/Device nevét a **lsblk** kimenete alapján a tényleges értékekkel kell helyettesíteni.

### <a name="checkmonitor-raid-creation"></a>RAID-létrehozás ellenőrzés/figyelése
```bash
watch -n1 cat /proc/mdstat
mdadm --examine /dev/mapper/[]
mdadm --detail /dev/md10
```
![RAID állapota](./media/disk-encryption/lvm-raid-on-crypt/020-lvm-raid-md-details.png)

### <a name="create-a-file-system-on-top-of-the-new-raid-device"></a>Fájlrendszer létrehozása az új RAID-eszközön
```bash
mkfs.ext4 /dev/md10
```

Hozzon létre egy új csatlakozási pontot a fájlrendszerhez, adja hozzá az új fájlrendszert az/etc/fstab-hez, és csatlakoztassa a következőhöz:

```bash
for device in md10; do diskuuid="$(blkid -s UUID -o value /dev/${device})"; \
mkdir /raiddata; \
echo "UUID=${diskuuid} /raiddata ext4 defaults,nofail 0 0" >> /etc/fstab; \
mount -a; \
done
```

Ellenőrizze, hogy az új fájlrendszer csatlakoztatva van-e:

```bash
lsblk -fs
df -h
```
![A képernyőképen egy, a raiddata csatlakoztatott fájlrendszerrel rendelkező konzolablak látható.](./media/disk-encryption/lvm-raid-on-crypt/021-lvm-raid-lsblk-md-details.png)

Fontos, hogy a **nem** megfelelő beállítás bekerüljön a Azure Disk Encryption használatával titkosított eszközön létrehozott RAID-kötetek csatlakoztatási pontjának beállításaiba. Megakadályozza, hogy az operációs rendszer elragadjon a rendszerindítási folyamat során (vagy karbantartási módban).

Ha nem használja a nem **sikertelen** beállítást:

- Az operációs rendszer soha nem fog bekerülni az Azure Disk Encryption elindításának szakaszába, és az adatlemezek fel vannak oldva és csatlakoztatva vannak.
- A titkosított lemezek a rendszerindítási folyamat végén fel lesznek oldva. A rendszer automatikusan csatlakoztatja a RAID-köteteket és a fájlrendszereket, amíg Azure Disk Encryption feloldja őket.

Tesztelheti a virtuális gép újraindítását, és ellenőrizheti, hogy a fájlrendszerek a rendszerindítás után is automatikusan bekerülnek-e. Ez a folyamat több percet is igénybe vehet, a fájlrendszerek számától és méretétől függően.

```bash
shutdown -r now
```

És ha be tud jelentkezni:

```bash
lsblk
df -h
```
## <a name="next-steps"></a>Következő lépések

- [A Azure Disk Encryption-mel titkosított logikai kötet-felügyeleti eszközök átméretezése](how-to-resize-encrypted-lvm.md)
- [Azure Disk Encryption – hibaelhárítás](disk-encryption-troubleshooting.md)
