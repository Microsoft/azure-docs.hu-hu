---
title: Igény szerinti lemez-adatlok-igénylés engedélyezése
description: Igény szerinti lemez-adatlok-igénylés engedélyezése a felügyelt lemezen.
author: albecker1
ms.author: albecker
ms.date: 03/02/2021
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 5110e580bada7bb1090b17d6df22a9354622e8e4
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483146"
---
# <a name="enable-on-demand-bursting"></a>Igény szerinti ki- és bekapcsolás engedélyezése

A prémium SSD-meghajtók két rendelkezésre álló adatlok-sorozatos modellel érhetők el; kreditalapú és igény szerinti ki- és felhatolás. Ez a cikk bemutatja, hogyan válthat igény szerinti ki- és bekapcsolásos módra. Az igény szerinti modellt használni képes lemezek az eredeti kiépítési célnál nagyobb teljesítményre is képesek. Az igény szerinti teljesítménykorrekláció olyan gyakran történik, amennyire a számítási feladatnak szüksége van, a maximális burst célig. Az igény szerinti meglökkenés további költségeket követel meg.

További információ a lemez-adatlokozásról: [Felügyelt lemezek adatlok-ének kezelése.](disk-bursting.md)

> [!IMPORTANT]
> A kreditalapú meglövekezéshez nem kell követnie a cikkben található lépéseket. Alapértelmezés szerint a kreditalapú adatlökés engedélyezve van az összes jogosult lemezen.

Az igény szerinti bursting engedélyezése előtt értse meg a következőket:

[!INCLUDE [managed-disk-bursting-regions-limitations](../../includes/managed-disk-bursting-regions-limitations.md)]

### <a name="regional-availability"></a>Regionális elérhetőség

[!INCLUDE [managed-disk-bursting-availability](../../includes/managed-disk-bursting-availability.md)]

## <a name="get-started"></a>Bevezetés

Az igény szerinti bursting az Azure PowerShell modullal, az Azure CLI-val vagy a Azure Resource Manager engedélyezhető. Az alábbi példák bemutatják, hogyan hozható létre új lemez, engedélyezett igény szerinti adatlökésekkel, és hogyan engedélyezhető az igény szerinti adatlökés a meglévő lemezeken.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Az igény szerinti adatlökéses parancsmagok az Az modul 5.5.0-s és újabb verzióiban érhetők el. Másik lehetőségként használhatja a [Azure Cloud Shell.](https://shell.azure.com/)
### <a name="create-an-empty-data-disk-with-on-demand-bursting"></a>Üres adatlemez létrehozása igény szerinti adatlokozással

A felügyelt lemezeknek 512 GiB-osnál nagyobbnak kell lennie az igény szerinti adatlok-igényléshez. Cserélje le a és a paramétert, majd futtassa a következő szkriptet egy prémium SZINTŰ SSD igény szerinti `<myResourceGroupDisk>` `<myDataDisk>` adatlok-létrehozására:

```azurepowershell
Set-AzContext -SubscriptionName <yourSubscriptionName>

$diskConfig = New-AzDiskConfig -Location 'WestCentralUS' -CreateOption Empty -DiskSizeGB 1024 -SkuName Premium_LRS -BurstingEnabled $true

$dataDisk = New-AzDisk -ResourceGroupName <myResourceGroupDisk> -DiskName <myDataDisk> -Disk $diskConfig
```

### <a name="enable-on-demand-bursting-on-an-existing-disk"></a>Igény szerinti adatlok-igénylés engedélyezése meglévő lemezen

A felügyelt lemezeknek 512 GiB-osnál nagyobbnak kell lennie az igény szerinti adatlokozás engedélyezéséhez. Cserélje le a paramétert, és futtassa ezt a parancsot az igény szerinti adatlokozás engedélyezéséhez `<myResourceGroupDisk>` `<myDataDisk>` egy meglévő lemezen:

```azurepowershell
New-AzDiskUpdateConfig -BurstingEnabled $true | Update-AzDisk -ResourceGroupName <myResourceGroupDisk> -DiskName <myDataDisk> //Set the flag to $false to disable on-demand bursting
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az igény szerinti adatlökéses parancsmagok az Azure CLI modul 2.19.0-s és újabb [verzióiban érhetők el.](/cli/azure/install-azure-cli) Másik lehetőségként használhatja a [Azure Cloud Shell.](https://shell.azure.com/)

### <a name="create-and-attach-a-on-demand-bursting-data-disk"></a>Igény szerinti adatlúdolásos adatlemez létrehozása és csatolása

A felügyelt lemezeknek 512 GiB-osnál nagyobbnak kell lennie az igény szerinti adatlokozás engedélyezéséhez. Cserélje le a , és paramétereket, majd futtassa a következő parancsokat egy prémium SZINTŰ SSD igény szerinti `<yourDiskName>` adatlökésekkel `<yourResourceGroup>` `<yourVMName>` való létrehozásához:

```azurecli
az disk create -g <yourResourceGroup> -n <yourDiskName> --size-gb 1024 --sku Premium_LRS -l westcentralus --enable-bursting true

az vm disk attach --vm-name <yourVMName> --name <yourDiskName> --resource-group <yourResourceGroup>
```

### <a name="enable-on-demand-bursting-on-an-existing-disk---cli"></a>Igény szerinti adatlokozás engedélyezése meglévő lemezen – CLI

A felügyelt lemezeknek 512 GiB-osnál nagyobbnak kell lennie az igény szerinti adatlokozás engedélyezéséhez. Cserélje le a és a paramétert, és futtassa ezt a parancsot az igény szerinti adatlokozás engedélyezéséhez `<myResourceGroupDisk>` `<yourDiskName>` egy meglévő lemezen:

```azurecli
az disk update --name <yourDiskName> --resource-group <yourResourceGroup> --enable-bursting true //Set the flag to false to disable on-demand bursting
```

# <a name="azure-resource-manager"></a>[Azure Resource Manager](#tab/azure-resource-manager)

A lemez API-val igény szerinti adatlökéseket engedélyezhet az újonnan létrehozott vagy a `2020-09-30` meglévő, 512 GiB-osnál nagyobb prémium szintű SSD-ken. Az `2020-09-30` API egy új tulajdonságot vezetett be: `burstingEnabled` . Alapértelmezés szerint ez a tulajdonság false (hamis) értéket ad meg. A következő mintasablon egy 1TiB prémium SSD-t hoz létre az USA nyugati középső részén, engedélyezett lemez-adatlokozással:

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

Az adatlökésben lévő erőforrásokról további információt a Lemezes adatlökés metrikákban [olvashat.](disks-metrics.md)
