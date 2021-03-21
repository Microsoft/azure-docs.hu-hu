---
title: Gyakran előforduló hibák a klasszikusból Azure Resource Manager-alapú környezetbe való migrálás során
description: Ez a cikk a IaaS-erőforrások Azure Service managementről Azure Resource Managerre történő áttelepítése során leggyakrabban előforduló hibákat és enyhítéseket ismerteti.
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.subservice: classic-to-arm-migration
ms.workload: infrastructure-services
ms.topic: troubleshooting
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: 6d803d1a66c069f5eb42deead453a8526577f76b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102615210"
---
# <a name="errors-that-commonly-occur-during-classic-to-azure-resource-manager-migration"></a>A Klasszikusnál gyakran előforduló hibák Azure Resource Manager áttelepítésre

> [!IMPORTANT]
> Napjainkban a IaaS virtuális gépek 90%-a [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)használ. 2020. február 28-án a klasszikus virtuális gépek elavultak, és 2023. március 1-jén teljesen megszűnnek. [További]( https://aka.ms/classicvmretirement) információ erről az elavult szolgáltatásról, valamint arról, [hogy Ön hogyan befolyásolja Önt](classic-vm-deprecation.md#how-does-this-affect-me).

Ez a cikk összegyűjti az IaaS-erőforrások klasszikus Azure üzemi modellből Azure Resource Manager verembe történő migrálása során leggyakrabban előforduló hibákat és kezelési lehetőségeiket.


## <a name="list-of-errors"></a>Hibalista

| Hibasztring | Kockázatcsökkentés |
| --- | --- |
| Belső kiszolgálóhiba |Bizonyos esetekben ez egy átmeneti hiba, ami újbóli próbálkozással megszűnik. Ha továbbra is fennáll, [vegye fel a kapcsolatot az Azure ügyfélszolgálatával](../azure-portal/supportability/how-to-create-azure-support-request.md), mivel a platformnaplók vizsgálata szükséges. <br><br> **MEGJEGYZÉS:** Miután a támogatási csapat azonosította a hibát, ne próbálkozzon további javító intézkedésekkel, mivel azoknak nem várt következményei lehetnek a környezetben. |
| A migrálás az {üzemeltetett szolgáltatás} üzemeltetett szolgáltatás {üzemelő példány neve} üzemelő példánya esetében nem támogatott, mivel ez egy PaaS üzemelő példány (webes/feldolgozó). |Ez akkor fordul elő, ha az üzemelő példány egy webes/feldolgozói szerepkört tartalmaz. Mivel a migrálás csak a Virtual Machines esetében támogatott, távolítsa el a webes/feldolgozói szerepkört az üzemelő példányból, és próbálkozzon újra a migrálással. |
| A {sablonnév} sablon üzembe helyezése meghiúsult. CorrelationId={guid} |A migrálási szolgáltatás hátterében Azure Resource Manager-sablonok használatával hozunk létre erőforrásokat az Azure Resource Manager veremben. Mivel a sablonok idempotensek, ezért általában a migrálási művelet ismételt végrehajtásával biztonságosan megkerülheti ezt a hibát. Ha a hiba továbbra is fennáll, [forduljon az Azure ügyfélszolgálatához](../azure-portal/supportability/how-to-create-azure-support-request.md), és adja meg számokra a CorrelationId azonosítót. <br><br> **MEGJEGYZÉS:** Miután a támogatási csapat azonosította a hibát, ne próbálkozzon további javító intézkedésekkel, mivel azoknak nem várt következményei lehetnek a környezetben. |
| A {virtuális hálózat neve} virtuális hálózat nem létezik. |Ez akkor fordulat elő, ha a Virtual Network hálózatot az új Azure Portalon hozta létre. A tényleges Virtual Network hálózat neve a „Csoport * \<VNET name>” mintát követi |
| Az {üzemeltetett szolgáltatás neve} üzemeltetett szolgáltatás {virtuális gép neve} virtuális gépe tartalmazza a {bővítmény neve} bővítményt, amely az Azure Resource Managerben nem támogatott. Javasolt a virtuális gépről eltávolítani a bővítményt, mielőtt folytatná a migrálást. |XML-bővítmények, például BGInfo 1. \* Azure Resource Manager nem támogatottak. Ezért az ilyen bővítmények nem migrálhatók. Ha az ilyen bővítmények telepítve maradnak a virtuális gépen, a rendszer automatikusan eltávolítja azokat a migrálás befejezése előtt. |
| Az {üzemeltetett szolgáltatás neve} üzemeltetett szolgáltatás {virtuális gép neve} virtuális gépe tartalmazza a VMSnapshot/VMSnapshotLinux bővítményt, amely jelenleg a migrálási szolgáltatásban nem támogatott. Távolítsa el a bővítményt a virtuális gépről, majd a migrálás befejezése után adja újra hozzá az Azure Resource Manager használatával |Ez az a forgatókönyv, amelyikben a virtuális gép konfigurálva van az Azure Backup szolgáltatáshoz. Mivel ez jelenleg nem támogatott forgatókönyv, kövesse a megkerülő megoldást a következő helyen: https://aka.ms/vmbackupmigration |
| Az {üzemeltetett szolgáltatás neve} üzemeltetett szolgáltatás {virtuális gép neve} virtuális gépe tartalmazza a {bővítmény neve} bővítményt, amelynek az állapotát a virtuális gép nem jelenti. Ezért ez a virtuális gép nem migrálható. Gondoskodjon róla, hogy a virtuális gép jelentse a bővítmény állapotát, vagy távolítsa el a bővítményt a virtuális gépről, és próbálkozzon újra a migrálással. <br><br> Az {üzemeltetett szolgáltatás neve} üzemeltetett szolgáltatás {virtuális gép neve} virtuális gépe tartalmazza a {bővítmény neve} bővítményt, amely a {kezelői állapot} kezelői állapotot jelenti. Ezért a virtuális gép nem migrálható. Gondoskodjon róla, hogy a bővítmény jelentett állapota {kezelői állapot}, vagy távolítsa el a virtuális gépről, és próbálkozzon újra a migrálással. <br><br> Az {üzemeltetett szolgáltatás neve} üzemeltetett szolgáltatás {virtuális gép neve} virtuális gépének virtuálisgép-ügynöke az ügynök átfogó állapotát Not Ready állapotként jelenti. Ezért ez a virtuális gép nem migrálható, ha migrálható bővítménnyel rendelkezik. Gondoskodjon róla, hogy a virtuálisgép-ügynök átfogó állapotát Ready állapotként jelentse. Tekintse meg a következőt: https://aka.ms/classiciaasmigrationfaqs . |Az Azure vendég ügynök és a VM-bővítmény számára szükség van kimenő internetkapcsolatra a VM-tárfiókhoz az állapotuk közléséhez. Az állapothibák gyakori okai a következők lehetnek <li> egy hálózati biztonsági csoport, amely blokkolja a kimenő internetkapcsolatokat <li> Ha a VNET helyszíni DNS-kiszolgálókkal rendelkezik, és a DNS-kapcsolat megszakadt <br><br> Ha továbbra is nem támogatott állapot látható, a bővítmények eltávolításával átugorhatja ezt az ellenőrzést, és folytathatja a migrálást. |
| A migrálás az {üzemeltetett szolgáltatás neve} üzemeltetett szolgáltatás {üzemelő példány neve} üzemelő példánya esetében nem támogatott, mivel ez több rendelkezésre állási csoporttal rendelkezik. |Jelenleg csak az 1 vagy kevesebb rendelkezésre állási csoporttal rendelkező üzemeltetett szolgáltatások migrálhatók. A probléma megkerüléséhez helyezze át a további rendelkezésre állási csoportokat és a virtuális gépeket a rendelkezésre állási csoportokban egy másik üzemeltetett szolgáltatásba. |
| A migrálás az {üzemeltetett szolgáltatás neve} üzemeltetett szolgáltatás {üzemelő példány neve} üzemelő példánya esetében nem támogatott, mivel olyan virtuális gépeket tartalmaz, amelyek nem részei rendelkezésre állási csoportnak, bár az üzemeltetett szolgáltatás tartalmaz egyet. |Ebben a forgatókönyvben megkerülő megoldásként áthelyezheti az összes virtuális gépet egyetlen rendelkezésre állási csoportba, vagy eltávolíthatja az összes virtuális gépet a rendelkezésre állási csoportból az üzemeltetett szolgáltatásban. |
| A {virtuális hálózat neve} tárfiók/üzemeltetett szolgáltatás/virtuális hálózat migrálása folyamatban van, és ezért nem módosítható |Ez a hiba akkor lép fel, ha az „Előkészítés” migrálási művelet már végre lett hajtva az erőforráson, és egy olyan művelet aktiválódik, amely módosítaná az erőforrást. Mivel az „Előkészítés” művelet után a felügyeleti sík zárolva van, az erőforrás bármilyen módosítását blokkolja a rendszer. A felügyeleti sík feloldásához futtassa a „Véglegesítés” migrálási műveletet a migrálás befejezéséhez, vagy a „Megszakítás” migrálási műveletet az „Előkészítés” művelet visszavonásához. |
| Az {üzemeltetett szolgáltatás neve} üzemeltetett szolgáltatás migrálása nem engedélyezett, mivel a benne lévő {virtuális gép neve} virtuális gép állapota RoleStateUnknown. A migrálás csak abban az esetben engedélyezett, ha a virtuális gép a következő állapotok valamelyikében van: Running (Fut), Stopped (Leállítva), Stopped Deallocated (Leállítva és felszabadítva). |Előfordulhat, hogy a virtuális gép egy átmeneti állapotba kerül, ami általában akkor fordul elő, amikor egy frissítési művelet során a üzemeltetett, például egy újraindítás, a bővítmények telepítése stb. Azt javasoljuk, hogy a frissítési művelet befejeződjön a üzemeltetett az áttelepítés megkísérlése előtt. |
| Az {üzemeltetett szolgáltatás neve} üzemeltetett szolgáltatás {üzemelő példány neve} üzemelő példányában lévő {virtuális gép neve} virtuális gép {adatlemez neve} adatlemezének {adatlemez mögötti vhd blob mérete} bájtos fizikai blobmérete nem egyezik a virtuális gép adatlemezének {az adatlemez vm api-ban megadott mérete} bájtos logikai méretével. A migrálás során nem lesz megadva méret az Azure Resource Manager virtuális gép adatlemeze számára. | Ez a hiba akkor lép fel, ha anélkül méretezte át a virtuálismerevlemez-blobot, hogy frissítette volna a méretet a virtuális gép API modelljében. A hibakezelés részletes lépéseit az [alábbi szakasz](#vm-with-data-disk-whose-physical-blob-size-bytes-does-not-match-the-vm-data-disk-logical-size-bytes) ismerteti.|
| Tárolási hiba lépett fel a {felhőszolgáltatás neve} felhőszolgáltatásban lévő {virtuális gép neve} virtuális gépre mutató {adatlemez URI azonosítója} adathordozó-hivatkozással rendelkező {adatlemez neve} adatlemez érvényesítése során. Győződjön meg róla, hogy a virtuális merevlemez adathordozó-hivatkozása elérhető erről a virtuális gépről | Ez a hiba akkor léphet fel, ha a virtuális gép lemezei törölve lettek, és már nem érhetőek el. Gondoskodjon róla, hogy a virtuális gép lemezei léteznek.|
| A {felhőszolgáltatás neve} üzemeltetett szolgáltatás {virtuális gép neve} virtuális gépe tartalmazza a(z) {virtuális merevlemez uri-azonosítója} adathordozó-hivatkozással rendelkező lemezt, amely a {virtuális merevlemez blob neve} nevű blobot tartalmazza, amely az Azure Resource Managerben nem támogatott. | Ez a hiba akkor fordul elő, ha a blob neve tartalmazza a „/” karaktert, ami jelenleg nem támogatott a Compute erőforrás-szolgáltatóban. |
| A migrálás a {felhőszolgáltatás neve} üzemeltetett szolgáltatás {üzemelő példány neve} üzemelő példánya esetében nem engedélyezett, mivel az nem regionális hatókörű. A \/ központi telepítés regionális hatókörbe való áthelyezéséhez a https:/aka.MS/regionalscope címen tájékozódhat. | Az Azure 2014-ben jelentette be, hogy a hálózati erőforrások a fürtszintű hatókörből átkerülnek a regionális hatókörbe. [https://aka.ms/regionalscope](https://aka.ms/regionalscope)További részletekért tekintse meg a következőt:. Ez a hiba akkor lép fel, ha a migrált üzemelő példányhoz nincs definiálva egy olyan frissítési művelet, amely automatikusan átmozgatná regionális hatókörbe. A legjobb megoldás, ha egy végpontot ad hozzá egy virtuális géphez vagy egy adatlemezt a virtuális géphez, majd próbálja megismételni az áttelepítést. <br> Tekintse meg, [Hogyan lehet végpontokat beállítani egy klasszikus virtuális gépen az Azure-ban](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints#create-an-endpoint) , vagy [adatlemezt csatlakoztatni a klasszikus üzemi modellel létrehozott virtuális géphez](./linux/attach-disk-portal.md)|
| A (z) {vnet} Virtual Network nem támogatja az áttelepítést, mert az átjárón kívüli Pásti üzemelő példányokkal rendelkezik. | Ez a hiba akkor fordul elő, ha olyan nem átjárós Pásti-telepítésekkel rendelkezik, mint például a Application Gateway vagy API Management a Virtual Networkhoz csatlakoztatott szolgáltatások.|


## <a name="detailed-mitigations"></a>Részletes hibakezelés

### <a name="vm-with-data-disk-whose-physical-blob-size-bytes-does-not-match-the-vm-data-disk-logical-size-bytes"></a>Virtuális gép olyan adatlemezzel, amelynek fizikai blobmérete (bájtban) nem egyezik a virtuális gép adatlemezének logikai méretével (bájtban).

Ez akkor fordulhat elő, ha az adatlemez logikai mérete nincs szinkronban a virtuális merevlemez tényleges blobméretével. Ez a következő parancsok használatával könnyen ellenőrizhető:

#### <a name="verifying-the-issue"></a>A probléma ellenőrzése

```powershell
# Store the VM details in the VM object
$vm = Get-AzureVM -ServiceName $servicename -Name $vmname

# Display the data disk properties
# NOTE the data disk LogicalDiskSizeInGB below which is 11GB. Also note the MediaLink Uri of the VHD blob as we'll use this in the next step
$vm.VM.DataVirtualHardDisks


HostCaching         : None
DiskLabel           : 
DiskName            : coreosvm-coreosvm-0-201611230636240687
Lun                 : 0
LogicalDiskSizeInGB : 11
MediaLink           : https://contosostorage.blob.core.windows.net/vhds/coreosvm-dd1.vhd
SourceMediaLink     : 
IOType              : Standard
ExtensionData       : 

# Now get the properties of the blob backing the data disk above
# NOTE the size of the blob is about 15 GB which is different from LogicalDiskSizeInGB above
$blob = Get-AzStorageblob -Blob "coreosvm-dd1.vhd" -Container vhds 

$blob

ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudPageBlob
BlobType          : PageBlob
Length            : 16106127872
ContentType       : application/octet-stream
LastModified      : 11/23/2016 7:16:22 AM +00:00
SnapshotTime      : 
ContinuationToken : 
Context           : Microsoft.WindowsAzure.Commands.Common.Storage.AzureStorageContext
Name              : coreosvm-dd1.vhd
```

#### <a name="mitigating-the-issue"></a>A probléma kezelése

```powershell
# Convert the blob size in bytes to GB into a variable which we'll use later
$newSize = [int]($blob.Length / 1GB)

# See the calculated size in GB
$newSize

15

# Store the disk name of the data disk as we'll use this to identify the disk to be updated
$diskName = $vm.VM.DataVirtualHardDisks[0].DiskName

# Identify the LUN of the data disk to remove
$lunToRemove = $vm.VM.DataVirtualHardDisks[0].Lun

# Now remove the data disk from the VM so that the disk isn't leased by the VM and it's size can be updated
Remove-AzureDataDisk -LUN $lunToRemove -VM $vm | Update-AzureVm -Name $vmname -ServiceName $servicename

OperationDescription OperationId                          OperationStatus
-------------------- -----------                          ---------------
Update-AzureVM       213xx1-b44b-1v6n-23gg-591f2a13cd16   Succeeded  

# Verify we have the right disk that's going to be updated
Get-AzureDisk -DiskName $diskName

AffinityGroup        : 
AttachedTo           : 
IsCorrupted          : False
Label                : 
Location             : East US
DiskSizeInGB         : 11
MediaLink            : https://contosostorage.blob.core.windows.net/vhds/coreosvm-dd1.vhd
DiskName             : coreosvm-coreosvm-0-201611230636240687
SourceImageName      : 
OS                   : 
IOType               : Standard
OperationDescription : Get-AzureDisk
OperationId          : 0c56a2b7-a325-123b-7043-74c27d5a61fd
OperationStatus      : Succeeded

# Now update the disk to the new size
Update-AzureDisk -DiskName $diskName -ResizedSizeInGB $newSize -Label $diskName

OperationDescription OperationId                          OperationStatus
-------------------- -----------                          ---------------
Update-AzureDisk     cv134b65-1b6n-8908-abuo-ce9e395ac3e7 Succeeded 

# Now verify that the "DiskSizeInGB" property of the disk matches the size of the blob 
Get-AzureDisk -DiskName $diskName


AffinityGroup        : 
AttachedTo           : 
IsCorrupted          : False
Label                : coreosvm-coreosvm-0-201611230636240687
Location             : East US
DiskSizeInGB         : 15
MediaLink            : https://contosostorage.blob.core.windows.net/vhds/coreosvm-dd1.vhd
DiskName             : coreosvm-coreosvm-0-201611230636240687
SourceImageName      : 
OS                   : 
IOType               : Standard
OperationDescription : Get-AzureDisk
OperationId          : 1v53bde5-cv56-5621-9078-16b9c8a0bad2
OperationStatus      : Succeeded

# Now we'll add the disk back to the VM as a data disk. First we need to get an updated VM object
$vm = Get-AzureVM -ServiceName $servicename -Name $vmname

Add-AzureDataDisk -Import -DiskName $diskName -LUN 0 -VM $vm -HostCaching ReadWrite | Update-AzureVm -Name $vmname -ServiceName $servicename

OperationDescription OperationId                          OperationStatus
-------------------- -----------                          ---------------
Update-AzureVM       b0ad3d4c-4v68-45vb-xxc1-134fd010d0f8 Succeeded      
```

### <a name="moving-a-vm-to-a-different-subscription-after-completing-migration"></a>Virtuális gép áthelyezése egy másik előfizetésre a migrálás végeztével

A migrálási folyamat után esetleg szeretné átmozgatni a virtuális gépet egy másik előfizetésre. Ha azonban olyan titokkal/tanúsítvánnyal rendelkezik a virtuális gépen, amely egy Key Vault-erőforrásra hivatkozik, az átmozgatás jelenleg nem támogatott. Az alábbi utasítások segítségével megkerülheti a problémát. 

#### <a name="powershell"></a>PowerShell

```powershell
$vm = Get-AzVM -ResourceGroupName "MyRG" -Name "MyVM"
Remove-AzVMSecret -VM $vm
Update-AzVM -ResourceGroupName "MyRG" -VM $vm
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az vm update -g "myrg" -n "myvm" --set osProfile.Secrets=[]
```


## <a name="next-steps"></a>Következő lépések

* [A IaaS-erőforrások platform által támogatott áttelepítésének áttekintése klasszikusról Azure Resource Manager](migration-classic-resource-manager-overview.md)
* [Részletes műszaki útmutató a klasszikusból az Azure Resource Manager-alapú üzemi modellbe történő, platform által támogatott migrálásról](migration-classic-resource-manager-deep-dive.md)
* [Az IaaS-erőforrások klasszikusból Azure Resource Manager-alapú környezetbe való áttelepítésének megtervezése](migration-classic-resource-manager-plan.md)
* [A IaaS-erőforrások migrálása a Klasszikusból a Azure Resource Managerba a PowerShell használatával](migration-classic-resource-manager-ps.md)
* [A CLI használatával IaaS-erőforrásokat telepíthet át a klasszikusról Azure Resource Manager](migration-classic-resource-manager-cli.md)
* [Közösségi eszközök a IaaS-erőforrások Klasszikusból Azure Resource Managerba való áttelepítésének támogatásához](migration-classic-resource-manager-community-tools.md)
* [Tekintse át a IaaS-erőforrások klasszikusról Azure Resource Managerra való áttelepítésével kapcsolatos leggyakoribb kérdéseket](migration-classic-resource-manager-faq.md)