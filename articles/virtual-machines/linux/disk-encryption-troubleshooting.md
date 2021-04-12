---
title: Linux rendszerű virtuális gépek hibaelhárítási Azure Disk Encryption
description: Ez a cikk hibaelhárítási tippeket tartalmaz a Linux rendszerű virtuális gépekhez Microsoft Azure lemezes titkosításhoz.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: troubleshooting
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 58ea788d6959b039ff5de8a4a889c531b987d826
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102563370"
---
# <a name="azure-disk-encryption-for-linux-vms-troubleshooting-guide"></a>Azure Disk Encryption Linux rendszerű virtuális gépek hibaelhárítási útmutatója

Ez az útmutató olyan informatikai szakemberek, Információbiztonsági elemzők és Felhőbeli rendszergazdák számára készült, akiknek a vállalata Azure Disk Encryptiont használ. Ez a cikk segítséget nyújt a lemezes titkosítással kapcsolatos problémák elhárításában.

Az alábbi lépések bármelyikének megkezdése előtt győződjön meg arról, hogy a titkosítani kívánt virtuális gépek a támogatott virtuálisgép- [méretek és operációs rendszerek](disk-encryption-overview.md#supported-vms-and-operating-systems)közé tartoznak, és az összes előfeltétel teljesült:

- [A virtuális gépekre vonatkozó további követelmények](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Hálózati követelmények](disk-encryption-overview.md#networking-requirements)
- [Titkosítási kulcs tárolási követelményei](disk-encryption-overview.md#encryption-key-storage-requirements)

 

## <a name="troubleshooting-linux-os-disk-encryption"></a>A Linux operációsrendszer-lemez titkosításának hibaelhárítása

A Linux operációs rendszer (OS) lemezének titkosítása előtt le kell választania az operációsrendszer-meghajtót a teljes lemezes titkosítási folyamat futtatása előtt. Ha nem tudja leválasztani a meghajtót, a "nem sikerült leválasztani..." hibaüzenet jelenik meg. valószínűleg előfordul.

Ez a hiba akkor fordulhat elő, ha az operációsrendszer-lemez titkosítását egy olyan környezettel kísérli meg, amely a támogatott Stock Gallery-rendszerképből lett módosítva. A támogatott rendszerképtől való eltérések zavarhatják a bővítménynek az operációs rendszer meghajtójának leválasztására való képességét. Az eltérések például a következők lehetnek:
- A testreszabott lemezképek már nem egyeznek a támogatott fájlrendszerrel vagy particionálási sémával.
- Azok a nagyméretű alkalmazások, mint az SAP, a MongoDB, az Apache Cassandra és a Docker, nem támogatottak, ha a titkosítás előtt telepítik és futtatják az operációs rendszert. Azure Disk Encryption nem tudja leállítani ezeket a folyamatokat az operációsrendszer-meghajtó lemezes titkosításhoz való előkészítésekor szükséges módon. Ha még vannak aktív folyamatok, amelyek nyitott fájlkezelőket tárolnak az operációsrendszer-meghajtón, az operációsrendszer-meghajtó nem választható le, így az operációs rendszer meghajtójának titkosítása nem sikerült. 
- Az olyan egyéni parancsfájlok, amelyek a titkosítás engedélyezésekor közel vannak a titkosításhoz, vagy ha a titkosítási folyamat során a virtuális gépen bármilyen egyéb módosítás történik. Ez az ütközés akkor fordulhat elő, ha egy Azure Resource Manager-sablon egyszerre több bővítményt határoz meg, vagy ha egy egyéni parancsfájl-kiterjesztés vagy más művelet egyidejűleg fut a lemez titkosítása során. Az ilyen lépések szerializálása és elkülönítése megoldhatja a problémát.
- A titkosítás engedélyezése előtt a fokozott biztonságú Linux (SELinux) nem lett letiltva, így a leválasztási lépés sikertelen lesz. A SELinux újraengedélyezhető a titkosítás befejeződése után.
- Az operációsrendszer-lemez egy logikai kötet-kezelő (LVM) sémát használ. Bár a korlátozott LVM adatlemez-támogatás elérhető, az LVM operációsrendszer-lemez nem.
- Nem teljesülnek a minimális memória-követelmények (az operációsrendszer-lemez titkosításához 7 GB ajánlott).
- Az adatmeghajtók rekurzív módon vannak csatlakoztatva a/mnt/könyvtárában vagy egymáshoz (például/mnt/Data1,/mnt/data2,/data3 +/data3/data4).

## <a name="update-the-default-kernel-for-ubuntu-1404-lts"></a>Az Ubuntu 14,04 LTS alapértelmezett kernelének frissítése

Az Ubuntu 14,04 LTS-rendszerkép a 4,4-es alapértelmezett kernel-verzióval rendelkezik. Ennek a kernel-verziónak van egy ismert hibája, amely miatt a memóriából való gyilkos helytelenül leállítja a dd parancsot az operációs rendszer titkosítási folyamata során. Ezt a hibát a legújabb Azure tuned Linux kernelben javítottuk. Ha el szeretné kerülni ezt a hibát, mielőtt engedélyezi a titkosítást a rendszerképben, frissítsen az Azure-ban [hangolt kernel 4,15](https://packages.ubuntu.com/trusty/linux-azure) -es vagy újabb verziójára az alábbi parancsok használatával:

```
sudo apt-get update
sudo apt-get install linux-azure
sudo reboot
```

Miután a virtuális gép újra lett indítva az új kernelbe, az új kernel verziója a következő használatával erősíthető meg:

```
uname -a
```

## <a name="update-the-azure-virtual-machine-agent-and-extension-versions"></a>Az Azure-beli virtuálisgép-ügynök és-bővítmények verziójának frissítése

A Azure Disk Encryption műveletek a virtuálisgép-rendszerképeken az Azure Virtual Machine Agent nem támogatott verzióit használva sikertelenek lehetnek. A titkosítás engedélyezése előtt frissíteni kell a 2.2.38-nál korábbi ügynök-verziókkal rendelkező Linux-lemezképeket. További információkért lásd: [Az Azure Linux-ügynök frissítése](../extensions/update-linux-agent.md) virtuális gépen, valamint a [virtuálisgép-ügynökök minimális verziójának támogatása az Azure-ban](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

A Microsoft. Azure. Security. AzureDiskEncryption vagy a Microsoft. Azure. Security. AzureDiskEncryptionForLinux megfelelő verziója is szükséges. A bővítmények verzióit a platform automatikusan karbantartja és frissíti, amikor az Azure-beli virtuális gépek ügynökének előfeltételei teljesülnek, és a rendszer a virtuálisgép-ügynök támogatott verzióját használja.

A Microsoft. OSTCExtensions. AzureDiskEncryptionForLinux kiterjesztés elavult, és már nem támogatott.  

## <a name="unable-to-encrypt-linux-disks"></a>A Linux-lemezek nem titkosíthatók

Bizonyos esetekben a Linux-lemez titkosítása úgy tűnik, hogy az "operációs rendszer lemezének titkosítása megkezdődött", és az SSH le van tiltva. A titkosítási folyamat 3-16 óráig is eltarthat, hogy befejezze a Stock Gallery-rendszerképét. Ha több terabájt méretű adatlemezt ad hozzá, a folyamat napokat is igénybe vehet.

A Linux operációs rendszer lemezének titkosítási folyamata ideiglenesen leválasztja az operációsrendszer-meghajtót. Ezután a teljes operációsrendszer-lemez blokkolása blokkolja a titkosítást, mielőtt a rendszer titkosított állapotba csatlakoztatja azt. A Linux-lemez titkosítása nem teszi lehetővé a virtuális gép egyidejű használatát, miközben a titkosítás folyamatban van. A virtuális gép teljesítménybeli jellemzői jelentős eltérést okozhatnak a titkosítás befejezéséhez szükséges idő alatt. Ezek a tulajdonságok tartalmazzák a lemez méretét, valamint azt, hogy a Storage-fiók standard vagy prémium (SSD) tároló-e.

Az operációs rendszer meghajtójának titkosítása közben a virtuális gép karbantartási állapotba kerül, és letiltja az SSH-t a folyamatban lévő folyamat megszakadásának megakadályozása érdekében.  A titkosítás állapotának megtekintéséhez használja a Azure PowerShell [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) parancsot, és tekintse meg a **feladatnézetben** mezőt. A **feladatnézetben** az állapotok sorozatát fogja jelenteni, mivel az adatmennyiség és az operációsrendszer-lemezek titkosítva vannak:

```azurepowershell
PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyResourceGroup" -VMName "myVM"

OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : EncryptionInProgress
OsVolumeEncryptionSettings :
ProgressMessage            : Transitioning

PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyResourceGroup" -VMName "myVM"

OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : EncryptionInProgress
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : Encryption succeeded for data volumes

PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyResourceGroup" -VMName "myVM"

OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : EncryptionInProgress
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : Provisioning succeeded

PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyResourceGroup" -VMName "myVM"

OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : EncryptionInProgress
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk encryption started
```

A **feladatnézetben** továbbra is az **operációsrendszer-lemez titkosítása megkezdődött** a legtöbb titkosítási folyamat során.  Ha a titkosítás befejeződött és sikeres, a **feladatnézetben** a következőket fogja visszaadni:

```azurepowershell
PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyResourceGroup" -VMName "myVM"

OsVolumeEncrypted          : Encrypted
DataVolumesEncrypted       : NotMounted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : Encryption succeeded for all volumes
```

Az üzenet elérhetővé tétele után a rendszer készen áll a titkosított operációsrendszer-meghajtó használatára, és a virtuális gép készen áll a használatra.

Ha a folyamat során a rendszerindítási információ, az állapotjelző üzenet vagy egy olyan hibajelentés, amely miatt az operációs rendszer titkosítása meghiúsult, állítsa vissza a virtuális gépet a pillanatképbe, vagy a biztonsági mentést közvetlenül a titkosítás előtt. Ilyen üzenet például az útmutatóban ismertetett "nem sikerült leválasztani" hibaüzenet.

A titkosítás újrapróbálkozása előtt értékelje újra a virtuális gép jellemzőit, és ellenőrizze, hogy az összes előfeltétel teljesül-e.

## <a name="troubleshooting-azure-disk-encryption-behind-a-firewall"></a>Hibaelhárítás Azure Disk Encryption tűzfal mögött

Lásd: [lemez titkosítása elszigetelt hálózaton](disk-encryption-isolated-network.md)

## <a name="troubleshooting-encryption-status"></a>Titkosítási állapot hibaelhárítása 

A portál titkosított lemezként jelenhet meg, miután a virtuális gépen titkosítva lett.  Ez akkor fordulhat elő, ha az alacsony szintű parancsok használatával a rendszer a virtuális gépről közvetlenül titkosítja a lemezt a magasabb szintű Azure Disk Encryption felügyeleti parancsok használata helyett.  A magasabb szintű parancsok nem csak a virtuális gépről titkosítják a lemezt, hanem a virtuális GÉPEN kívül is frissítik a virtuális géphez tartozó fontos platform szintű titkosítási beállításokat és bővítmény-beállításokat.  Ha ezeket nem tartja összhangban, a platform nem fogja tudni bejelenteni a titkosítási állapotot, vagy megfelelően kiépíteni a virtuális gépet.

A Azure Disk Encryption PowerShell-lel való letiltásához használja a [disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) parancsot, majd a [Remove-AzVMDiskEncryptionExtension](/powershell/module/az.compute/remove-azvmdiskencryptionextension). A Remove-AzVMDiskEncryptionExtension futtatása a titkosítás letiltása előtt sikertelen lesz.

A CLI-vel való Azure Disk Encryption letiltásához használja [az az VM encryption disable](/cli/azure/vm/encryption)parancsot.

## <a name="next-steps"></a>Következő lépések

Ebben a dokumentumban többet is megtudhat a Azure Disk Encryption és a problémák elhárításával kapcsolatos gyakori problémákról. A szolgáltatással és képességeivel kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Lemez titkosításának alkalmazása Azure Security Center](../../security-center/asset-inventory.md)
- [Inaktív adatok titkosítása az Azure-ban](../../security/fundamentals/encryption-atrest.md)