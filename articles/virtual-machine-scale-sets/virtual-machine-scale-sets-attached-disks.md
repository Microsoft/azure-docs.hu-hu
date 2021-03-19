---
title: Az Azure Virtual Machine Scale Sets csatolt adatlemezei
description: Megtudhatja, hogyan használhatja a csatlakoztatott adatlemezeket a virtuálisgép-méretezési csoportokkal meghatározott használati esetekből.
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 4/25/2017
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 9e4bdf868d3f8ddf3a049509ead30a4b1ba341b7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "86527438"
---
# <a name="azure-virtual-machine-scale-sets-and-attached-data-disks"></a>Azure-beli virtuálisgép-méretezési csoportok és csatlakoztatott adatlemezek
A rendelkezésre álló tárterület kiterjesztése érdekében az Azure-beli [virtuálisgép-méretezési csoportok](./index.yml) támogatják a csatlakoztatott adatlemezekkel rendelkező virtuálisgép-példányokat. Adatlemezeket a méretezési csoport létrehozásakor, vagy egy már létező méretezési csoporthoz is hozzáadhat.

> [!NOTE]
> Amikor olyan méretezési csoportot hoz létre, amely rendelkezik csatlakoztatott adatlemezekkel, ugyanúgy csatlakoztatnia kell a lemezeket, és formáznia kell őket a virtuális gépen belül a használatukhoz (akárcsak a különálló Azure-beli virtuális gépek esetén). Ez a folyamat kényelmesen elvégezhető egy olyan szkriptbővítmény használatával, amely egy szkript hívásával particionálja és formázza a virtuális gép összes adatlemezét. Példákért lásd: [Azure CLI](tutorial-use-disks-cli.md#prepare-the-data-disks) [Azure PowerShell](tutorial-use-disks-powershell.md#prepare-the-data-disks).


## <a name="create-and-manage-disks-in-a-scale-set"></a>Lemezek létrehozása és felügyelete egy méretezési csoportban
A csatlakoztatott adatlemezekkel rendelkező méretezési csoport létrehozásával, valamint az adatlemezek előkészítésével, formázásával, hozzáadásával és eltávolításával kapcsolatos részletes információt a következő oktatóanyagokban talál:

- [Azure CLI](tutorial-use-disks-cli.md)
- [Azure PowerShell](tutorial-use-disks-powershell.md)

A cikk további részében adott használati eseteket mutatunk be, például az adatlemezeket igénylő Service Fabric-fürtöket, vagy a tartalommal rendelkező meglévő adatlemezek csatlakoztatását méretezési csoportokhoz.


## <a name="create-a-service-fabric-cluster-with-attached-data-disks"></a>Service Fabric-fürt létrehozása csatlakoztatott adatlemezekkel
Az Azure-ban futó [Service Fabric](../service-fabric/index.yml)-fürtök mindegyik [csomóponttípusa](../service-fabric/service-fabric-cluster-nodetypes.md) egy virtuálisgép-skálázási csoporton alapul. Egy Azure Resource Manager-sablonnal adatlemezeket csatlakoztathat a Service Fabric-fürtöt alkotó méretezési csoport(ok)hoz. Kezdőpontként használhat egy [meglévő sablont](https://github.com/Azure-Samples/service-fabric-cluster-templates). A sablonban adjon egy _dataDisks_ szakaszt a _Microsoft.Compute/virtualMachineScaleSets_ erőforrás(ok) _storageProfile_ eleméhez, és helyezze üzembe a sablont. A következő példa egy 128 GB-os adatlemezt csatlakoztat:

```json
"dataDisks": [
    {
    "diskSizeGB": 128,
    "lun": 0,
    "createOption": "Empty"
    }
]
```

Automatikusan particionálhatja, formázhatja és csatlakoztathatja az adatlemezeket a fürt üzembe helyezésekor. Adjon egy egyéni szkriptbővítményt a skálázási csoport(ok) _virtualMachineProfile_ elemének _extensionProfile_ profiljához.

Ha Windows-fürtön szeretné automatikusan előkészíteni az adatlemez(eke)t, adja hozzá a következőket:

```json
{
    "name": "customScript",
    "properties": {
        "publisher": "Microsoft.Compute",
        "type": "CustomScriptExtension",
        "typeHandlerVersion": "1.8",
        "autoUpgradeMinorVersion": true,
        "settings": {
        "fileUris": [
            "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.ps1"
        ],
        "commandToExecute": "powershell -ExecutionPolicy Unrestricted -File prepare_vm_disks.ps1"
        }
    }
}
```
Ha Linux-fürtön szeretné automatikusan előkészíteni az adatlemez(eke)t, adja hozzá a következőket:
```json
{
    "name": "lapextension",
    "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
        "fileUris": [
            "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.sh"
        ],
        "commandToExecute": "bash prepare_vm_disks.sh"
        }
    }
}
```


## <a name="adding-pre-populated-data-disks-to-an-existing-scale-set"></a>Adatokkal előre feltöltött adatlemezek hozzáadása már létező méretezési csoporthoz
A méretezésicsoport-modellben megadott adatlemezek mindig üresek. Csatolhat azonban meglévő adatlemezt egy méretezési csoport meghatározott virtuális gépéhez. Ha a méretezési csoport összes virtuális gépén át szeretné terjeszteni az adatait, duplikálhatja az adatlemezt, és csatolhatja azt a méretezési csoportba tartozó egyes virtuális gépekhez, vagy létrehozhat egy egyéni rendszerképet, amely tartalmazza az adatait, és kiépíti a méretezési készletet ebből az egyéni lemezképből, vagy használhat Azure Files vagy egy hasonló adattárolási ajánlatot.


## <a name="additional-notes"></a>További megjegyzések
Az Azure Managed Disks, valamint a csatlakoztatott adatlemezzel rendelkező méretezési csoportok támogatása elérhető a Microsoft.Compute API [_2016-04-30-preview_](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/compute/resource-manager/Microsoft.Compute/preview/2016-04-30-preview/compute.json) és újabb verzióiban.

A méretezési csoportokban lévő csatlakoztatott adatlemezek Azure Portal támogatása korlátozott. A követelményektől függően Azure-sablonok, a parancssori felület, a PowerShell, SDK-k és a REST API használatával is kezelheti a csatlakoztatott lemezeket.
