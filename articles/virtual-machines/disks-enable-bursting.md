---
title: Igény szerinti lemez kitörésének engedélyezése
description: Az igény szerinti lemez kitörésének engedélyezése a felügyelt lemezen.
author: albecker1
ms.author: albecker
ms.date: 03/02/2021
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: f5865646200a783e7139bb5e22576ea404f58203
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102216649"
---
# <a name="enable-on-demand-bursting"></a>Igény szerinti kitörés engedélyezése

A prémium szintű SSD-meghajtók két elérhető kitört modellel rendelkeznek; kredit-alapú burst és igény szerinti kitörés. Ez a cikk azt ismerteti, hogyan válthat igény szerinti adatfeldolgozásra. Az igény szerinti modellt használó lemezek az eredeti kiépített célokon túl is kitörtek. Az igény szerinti bursás a munkaterhelés által a maximális burst-célhoz szükségesnél gyakrabban fordul elő. Igény szerinti burst további díjak.

A lemezek kitörésével kapcsolatos részletekért lásd: [felügyelt lemez kitörése](disk-bursting.md).

> [!IMPORTANT]
> Az ebben a cikkben ismertetett lépéseket nem kell követnie a kredit alapú kitörés használatához. Alapértelmezés szerint az összes jogosult lemezen engedélyezve van a kredit alapú kitörés.

Az igény szerinti kitörés engedélyezése előtt Ismerje meg a következőket:

[!INCLUDE [managed-disk-bursting-regions-limitations](../../includes/managed-disk-bursting-regions-limitations.md)]

### <a name="regional-availability"></a>Régiónkénti rendelkezésre állás

[!INCLUDE [managed-disk-bursting-availability](../../includes/managed-disk-bursting-availability.md)]

## <a name="get-started"></a>Bevezetés

Igény szerinti bursás engedélyezhető a Azure PowerShell modullal, az Azure CLI-vel vagy Azure Resource Manager sablonnal. Az alábbi példák azt mutatják be, hogyan hozhat létre egy új lemezt igény szerinti kikapcsolással, és hogyan engedélyezheti a meglévő lemezeken igény szerinti kitörést.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Az igény szerinti burst parancsmagok az az modul 5.5.0 és újabb verzióiban érhetők el. Azt is megteheti, hogy használja a [Azure Cloud Shell](https://shell.azure.com/).
### <a name="create-an-empty-data-disk-with-on-demand-bursting"></a>Igény szerinti kitöréssel rendelkező üres adatlemez létrehozása

A felügyelt lemeznek nagyobbnak kell lennie a 512 GiB-nál, hogy az igény szerinti kitörést engedélyezze. Cserélje le a `<myResourceGroupDisk>` és a `<myDataDisk>` paramétereket, majd futtassa a következő szkriptet egy prémium SSD létrehozásához igény szerinti kitöréssel:

```azurepowershell
Set-AzContext -SubscriptionName <yourSubscriptionName>

$diskConfig = New-AzDiskConfig -Location 'WestCentralUS' -CreateOption Empty -DiskSizeGB 1024 -SkuName Premium_LRS -BurstingEnabled $true

$dataDisk = New-AzDisk -ResourceGroupName <myResourceGroupDisk> -DiskName <myDataDisk> -Disk $diskConfig
```

### <a name="enable-on-demand-bursting-on-an-existing-disk"></a>Igény szerinti bursás engedélyezése meglévő lemezen

A felügyelt lemeznek nagyobbnak kell lennie a 512 GiB-nál, hogy az igény szerinti kitörést engedélyezze. Cserélje le a `<myResourceGroupDisk>` , `<myDataDisk>` paramétereit, és futtassa ezt a parancsot az igény szerinti bursás engedélyezéséhez egy meglévő lemezen:

```azurepowershell
New-AzDiskUpdateConfig -BurstingEnabled $true | Update-AzDisk -ResourceGroupName <myResourceGroupDisk> -DiskName <myDataDisk> //Set the flag to $false to disable on-demand bursting
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az igény szerinti burst parancsmagok az [Azure CLI-modul](https://docs.microsoft.com/cli/azure/install-azure-cli)2.19.0 és újabb verzióiban érhetők el. Azt is megteheti, hogy használja a [Azure Cloud Shell](https://shell.azure.com/).

### <a name="create-and-attach-a-on-demand-bursting-data-disk"></a>Igény szerinti burst adatlemez létrehozása és csatolása

A felügyelt lemeznek nagyobbnak kell lennie a 512 GiB-nál, hogy az igény szerinti kitörést engedélyezze. Cserélje le a `<yourDiskName>` , a `<yourResourceGroup>` és a `<yourVMName>` paramétereket, majd futtassa a következő PARANCSOKAT egy prémium SSD létrehozásához igény szerinti kitöréssel:

```azurecli
az disk create -g <yourResourceGroup> -n <yourDiskName> --size-gb 1024 --sku Premium_LRS -l westcentralus --enable-bursting true

az vm disk attach --vm-name <yourVMName> --name <yourDiskName> --resource-group <yourResourceGroup>
```

### <a name="enable-on-demand-bursting-on-an-existing-disk---cli"></a>Igény szerinti bursás engedélyezése meglévő lemezeken – parancssori felület

A felügyelt lemeznek nagyobbnak kell lennie a 512 GiB-nál, hogy az igény szerinti kitörést engedélyezze. Cserélje le a `<myResourceGroupDisk>` és a `<yourDiskName>` paramétereket, és futtassa ezt a parancsot egy meglévő lemez igény szerinti kitörésének engedélyezéséhez:

```azurecli
az disk update --name <yourDiskName> --resource-group <yourResourceGroup> --enable-bursting true //Set the flag to false to disable on-demand bursting
```

# <a name="azure-resource-manager"></a>[Azure Resource Manager](#tab/azure-resource-manager)

A `2020-09-30` Disk API-val a 512 GIB-nál nagyobb, újonnan létrehozott vagy meglévő prémium szintű SSD-k esetében is engedélyezheti az igény szerinti kitörést. Az `2020-09-30` API új tulajdonságot vezetett be `burstingEnabled` . Alapértelmezés szerint ez a tulajdonság hamis értékre van állítva. Az alábbi sablon egy 1TiB prémium SSD-t hoz létre az USA nyugati középső régiójában, a lemez kitörése pedig engedélyezve van:

```
{
  "$schema": "http://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "diskSkuName": {
        "type": "string",
        "defaultValue": "Premium_LRS" //Supported on premium SSDs only
},
    "dataDiskSizeInGb": {
      "type": "string",
      "defaultValue": "1024" //Supported on disk size > 512 GiB
},
    "location": {
      "type": "string",
      "defaultValue": "westcentralus" //Preview regions: West Central US
},
"diskApiVersion": {
      "type": "string",
      "defaultValue": "2020-09-30" //Preview supported version: 2020-09-30 or above
    }
  },
  "resources": [
    {
      "apiVersion": "[parameters('diskApiVersion')]",
      "type": "Microsoft.Compute/disks",
      "name": "[parameters('diskName')]",
      "location": "[parameters(location)]",
      "properties": {
        "creationData": {
          "createOption": "Empty"
        },
        "diskSizeGB": "[parameters('dataDiskSizeInGb')]",
        "burstingEnabled": "true" //Feature flag to enable disk bursting on disks > 512 GiB
      },
      "sku": {
        "name": "[parameters('diskSkuName')]"
      }
    ]
}
```
---
 
## <a name="next-steps"></a>Következő lépések

Ha meg szeretné tudni, hogyan szerezhet be betekintést a feltört erőforrásaiba, tekintse meg a [lemezek felszakadási mérőszámait](disks-metrics.md).