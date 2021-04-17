---
title: Redundanciabeállítások azure-beli felügyelt lemezekhez
description: Ismerje meg a zónaredundáns tárolást és a helyileg redundáns tárolást az Azure-beli felügyelt lemezeken.
author: roygara
ms.author: rogarana
ms.date: 03/02/2021
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 0882efeccfc8dc83686d75ab39b8364219c3b5f1
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588088"
---
# <a name="redundancy-options-for-managed-disks"></a>Redundanciabeállítások felügyelt lemezekhez

Az Azure managed Disks két tárhelyredundanca-lehetőséget kínál, a zónaredundáns tárolást (ZRS) előzetes verzióként és a helyileg redundáns tárolást. A ZRS magasabb rendelkezésre állást biztosít a felügyelt lemezek számára, mint a helyileg redundáns tárolás (LRS). Az LRS-lemezek írási késése azonban jobb, mint a ZRS-lemezek esetén, mivel az LRS-lemezek szinkron módon három példányba írnak adatokat egyetlen adatközpontban.

## <a name="locally-redundant-storage-for-managed-disks"></a>Helyileg redundáns tárolás felügyelt lemezekhez

A helyileg redundáns tárolás (LRS) háromszor replikálja az adatokat a kiválasztott régió egyetlen adatközpontjában. Az LRS megvédi adatait a kiszolgálóállványok és meghajtóhibák ellen. 

Az alkalmazás több módon is védheti LRS-lemezekkel egy teljes zóna meghibásodása esetén, amely természeti katasztrófák vagy hardverhibák miatt következhet be:
- Használjon egy olyan alkalmazást, SQL Server AlwaysOn, amely szinkron módon tud adatokat írni két zónába, és automatikusan feladatátvételt képes átveszni egy másik zónába egy katasztrófa esetén.
- Az LRS-lemezek gyakori biztonsági mentése ZRS-pillanatképekkel.
- Az LRS-lemezek zónaközi vészhelyreállításának engedélyezése a [Azure Site Recovery.](../site-recovery/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md) A zónák közötti vészhelyreállítás azonban nem biztosít nulla helyreállítási időkorrekta (RPO) értéket.

Ha a munkafolyamat nem támogatja a zónák közötti alkalmazásszintű szinkron írásokat, vagy az alkalmazásnak nulla RPO-t kell teljesítenie, akkor a ZRS-lemezek ideálisak.

## <a name="zone-redundant-storage-for-managed-disks-preview"></a>Zónaredundáns tárolás felügyelt lemezekhez (előzetes verzió)

A zónaredundáns tárolás (ZRS) szinkron módon replikálja az Azure-beli felügyelt lemezt a kiválasztott régió három Azure rendelkezésre állási zónájában. Minden rendelkezésreállási zóna egy fizikailag elkülönített, független áramforrással, hűtéssel és hálózatkezelési megoldással rendelkező hely. 

A ZRS-lemezek lehetővé teszik a rendelkezésre állási zónákban lévő hibákból való helyreállítást. Ha egy teljes zóna leállt, egy ZRS-lemez csatolható egy másik zónában lévő virtuális géphez. A ZRS-lemezeket megosztott lemezként is használhatja, hogy jobb rendelkezésre állást biztosítson az olyan fürtözött vagy elosztott alkalmazások számára, mint az SQL FCI, az SAP ASCS/SCS vagy a GFS2. Egy megosztott ZRS-lemezt csatlakoztathat a különböző zónákban lévő elsődleges és másodlagos virtuális gépekhez, hogy kihasználja a ZRS és a [Availability Zones.](../availability-zones/az-overview.md) Ha az elsődleges zóna meghibásodik, gyorsan átveheti a feladatátvételt a másodlagos virtuális gépre az [állandó SCSI-foglalás használatával.](disks-shared-enable.md#supported-scsi-pr-commands)

### <a name="limitations"></a>Korlátozások

Az előzetes verzióban a felügyelt lemezek ZRS-ére a következő korlátozások vonatkoznak:

- Csak prémium SSD-meghajtók és standard SSD-k esetén támogatott.
- Jelenleg csak az EastUS2EUAP régióban érhető el.
- ZRS-lemezek csak API-t használó Azure Resource Manager hoznak `2020-12-01` létre.

Az előzetes verzióra itt [regisztrál.](https://aka.ms/ZRSDisksPreviewSignUp)

### <a name="billing-implications"></a>Számlázással kapcsolatos következmények

Részletekért lásd az [Azure díjszabását tartalmazó oldalt.](https://azure.microsoft.com/pricing/details/managed-disks/)

### <a name="comparison-with-other-disk-types"></a>Összehasonlítás más lemeztípusokkal

A nagyobb írási késés kivételével a ZRS-t használó lemezek megegyeznek az LRS-t használó lemezekkel. Azonos teljesítménycéljaik vannak. Javasoljuk, hogy végezzen lemez-teljesítménytesztet az alkalmazás számítási feladatainak szimulálása érdekében az LRS- és [ZRS-lemezek](disks-benchmarks.md) közötti késés összehasonlításához. 

### <a name="create-zrs-managed-disks"></a>ZRS felügyelt lemezek létrehozása

`2020-12-01`ZRS-lemez létrehozásához használja az API-Azure Resource Manager sablonnal.

#### <a name="create-a-vm-with-zrs-disks"></a>Virtuális gép létrehozása ZRS-lemezekkel

```
$vmName = "yourVMName" 
$adminUsername = "yourAdminUsername"
$adminPassword = ConvertTo-SecureString "yourAdminPassword" -AsPlainText -Force
$osDiskType = "StandardSSD_ZRS"
$dataDiskType = "Premium_ZRS"
$region = "eastus2euap"
$resourceGroupName = "yourResourceGroupName"

New-AzResourceGroup -Name $resourceGroupName -Location $region
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
-TemplateUri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/ZRSDisks/CreateVMWithZRSDataDisks.json" `
-resourceName $vmName `
-adminUsername $adminUsername `
-adminPassword $adminPassword `
-region $region `
-osDiskType $osDiskType `
-dataDiskType $dataDiskType
```

#### <a name="create-vms-with-a-shared-zrs-disk-attached-to-the-vms-in-different-zones"></a>Virtuális gépek létrehozása különböző zónákban lévő virtuális gépekhez csatolt megosztott ZRS-lemezzel

```
$vmNamePrefix = "yourVMNamePrefix"
$adminUsername = "yourAdminUserName"
$adminPassword = ConvertTo-SecureString "yourAdminPassword" -AsPlainText -Force
$osDiskType = "StandardSSD_LRS"
$sharedDataDiskType = "Premium_ZRS"
$region = "eastus2euap"
$resourceGroupName = "zrstesting1"

New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
-TemplateUri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/ZRSDisks/CreateVMsWithASharedDisk.json" `
-vmNamePrefix $vmNamePrefix `
-adminUsername $adminUsername `
-adminPassword $adminPassword `
-region $region `
-osDiskType $osDiskType `
-dataDiskType $sharedDataDiskType
```

#### <a name="create-a-virtual-machine-scale-set-with-zrs-disks"></a>Virtuálisgép-méretezési csoport létrehozása ZRS-lemezekkel

```
$vmssName="yourVMSSName"
$adminUsername="yourAdminName"
$adminPassword=ConvertTo-SecureString "yourAdminPassword" -AsPlainText -Force
$region="eastus2euap"
$osDiskType="StandardSSD_LRS"
$dataDiskType="Premium_ZRS"

New-AzResourceGroupDeployment -ResourceGroupName zrstesting `
-TemplateUri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/ZRSDisks/CreateVMSSWithZRSDisks.json" `
-vmssName "yourVMSSName" `
-adminUsername "yourAdminName" `
-adminPassword $password `
-region "eastus2euap" `
-osDiskType "StandardSSD_LRS" `
-dataDiskType "Premium_ZRS" `
```

## <a name="next-steps"></a>Következő lépések

- Ezekkel a [mintasablonokkal Azure Resource Manager virtuális gépet hozhat létre ZRS-lemezekkel.](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/ZRSDisks)
