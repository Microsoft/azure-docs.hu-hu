---
title: Redundancia-beállítások az Azure Managed Disks szolgáltatáshoz
description: Ismerje meg a zóna-redundáns tárolást és a helyileg redundáns tárolást az Azure Managed Disks szolgáltatásban.
author: roygara
ms.author: rogarana
ms.date: 03/02/2021
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: f0f3baf1bf56f958408f789961812c0555f289f1
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102043643"
---
# <a name="redundancy-options-for-managed-disks"></a>A felügyelt lemezek redundancia beállításai

Az Azure Managed Disks két tárolási redundancia-beállítást kínál, a zóna-redundáns tárolást (ZRS) előzetes verzióként, valamint helyileg redundáns tárolást. A ZRS magasabb rendelkezésre állást biztosít a felügyelt lemezeken, mint a helyileg redundáns tárolás (LRS). A LRS lemezek írási késése azonban jobb, mint a ZRS-lemezek esetében, mert a LRS-lemezek szinkron módon írják le az adatközpont három példányára.

## <a name="locally-redundant-storage-for-managed-disks"></a>Helyileg redundáns tárolás a felügyelt lemezekhez

A helyileg redundáns tárolás (LRS) háromszor replikálja az adatait a kiválasztott régió egyetlen adatközpontjában. A LRS megvédi adatait a Server rack és a meghajtó meghibásodása ellen. 

Az alkalmazás a természeti katasztrófák vagy a hardveres problémák miatt esetlegesen előforduló teljes LRS-lemezek használatával többféleképpen védhető:
- Használjon olyan alkalmazást, mint például a SQL Server AlwaysOn, amely szinkron módon tud írni két zónába, és egy katasztrófa során automatikusan feladatátvételt hajt végre egy másik zónában.
- Készítsen gyakori biztonsági mentést a LRS-lemezekről a ZRS-pillanatképekkel.
- A LRS-lemezeken a [Azure site Recovery](../site-recovery/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md)használatával engedélyezhető a zónák közötti vész-helyreállítás. A zónák közötti vész-helyreállítás azonban nem biztosít nulla helyreállítási időkorlátot (RPO).

Ha a munkafolyamat nem támogatja az alkalmazás szintű szinkron írásokat a zónák között, vagy az alkalmazásnak meg kell felelnie a nulla RPO, akkor a ZRS-lemezek ideálisak.

## <a name="zone-redundant-storage-for-managed-disks-preview"></a>Zóna – redundáns tárolás felügyelt lemezekhez (előzetes verzió)

A Zone-redundáns tárolás (ZRS) a kiválasztott régió három Azure-beli rendelkezésre állási zónájában szinkron módon replikálja az Azure felügyelt lemezt. Minden rendelkezésreállási zóna egy fizikailag elkülönített, független áramforrással, hűtéssel és hálózatkezelési megoldással rendelkező hely. 

A ZRS lemezek lehetővé teszik a helyreállítást a rendelkezésre állási zónák hibáiból. Ha egy teljes zóna leállt, egy ZRS-lemez csatlakoztatható egy másik zónában lévő virtuális géphez. A ZRS-lemezeket megosztott lemezként is használhatja a fürtözött vagy elosztott alkalmazások, például az SQL-verzió, az SAP ASCS/SCS vagy a GFS2 jobb rendelkezésre állásának biztosításához. Az elsődleges és a másodlagos virtuális gépekhez közös ZRS csatlakoztathat különböző zónákban, így kihasználhatja a ZRS és a [Availability Zones](../availability-zones/az-overview.md)előnyeit is. Ha az elsődleges zóna meghibásodik, gyorsan átadhatja a feladatátvételt a másodlagos virtuális géphez a [SCSI állandó foglalás](disks-shared-enable.md#supported-scsi-pr-commands)használatával.

### <a name="limitations"></a>Korlátozások

Az előzetes verzióban a felügyelt lemezek ZRS a következő korlátozásokkal rendelkezik:

- Csak prémium szintű SSD-meghajtók és standard SSD-k esetén támogatott.
- Jelenleg csak az EastUS2EUAP régióban érhető el.
- Az ZRS-lemezek csak az API használatával hozhatók létre Azure Resource Manager-sablonokkal `2020-12-01` .

Regisztráljon [az előzetes](https://aka.ms/ZRSDisksPreviewSignUp)verzióra.

### <a name="billing-implications"></a>Számlázási következmények

További részletekért tekintse meg az [Azure díjszabási oldalát](https://azure.microsoft.com/pricing/details/managed-disks/).

### <a name="comparison-with-other-disk-types"></a>Összehasonlítás más típusú lemezekkel

A további írási késések kivételével a ZRS-t használó lemezek azonosak a LRS-t használó lemezekkel. Ugyanazok a teljesítménnyel kapcsolatos célok.

### <a name="create-zrs-managed-disks"></a>ZRS Managed Disks létrehozása

`2020-12-01`ZRS-lemez létrehozásához használja az API-t a Azure Resource Manager sablonnal.

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

#### <a name="create-vms-with-a-shared-zrs-disk-attached-to-the-vms-in-different-zones"></a>Virtuális gépek létrehozása a különböző zónákba tartozó virtuális gépekhez csatlakoztatott megosztott ZRS lemezzel

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

- Ezekkel a minta [Azure Resource Manager sablonokkal ZRS-lemezekkel rendelkező virtuális gépeket hozhat létre](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/ZRSDisks).