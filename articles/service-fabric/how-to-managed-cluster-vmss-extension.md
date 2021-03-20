---
title: Virtuálisgép-méretezési csoport kiterjesztésének hozzáadása egy Service Fabric felügyelt fürtcsomópont-típushoz (előzetes verzió)
description: A virtuálisgép-méretezési csoport bővítményének egy Service Fabric felügyelt fürtcsomópont-típussal való hozzáadásának módja
ms.topic: article
ms.date: 09/28/2020
ms.openlocfilehash: a47908b511f79c18482e9d21e623f1cb4dc70ed1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101737766"
---
# <a name="add-a-virtual-machine-scale-set-extension-to-a-service-fabric-managed-cluster-node-type-preview"></a>Virtuálisgép-méretezési csoport kiterjesztésének hozzáadása egy Service Fabric felügyelt fürtcsomópont-típushoz (előzetes verzió)

Egy Service Fabric felügyelt fürt mindegyik csomópont-típusát egy virtuálisgép-méretezési csoport támogatja. Ez lehetővé teszi a [virtuálisgép-méretezési csoport bővítményeinek](../virtual-machines/extensions/overview.md) hozzáadását a Service Fabric felügyelt fürtcsomópontok típusaihoz.

Az [Add-AzServiceFabricManagedNodeTypeVMExtension](/powershell/module/az.servicefabric/add-azservicefabricmanagednodetypevmextension) PowerShell-paranccsal hozzáadhat egy virtuálisgép-méretezési csoport kiterjesztését egy csomópont-típushoz.

Másik lehetőségként egy virtuálisgép-méretezési csoport bővítményt is használhat Service Fabric felügyelt fürtcsomópont-típushoz a Azure Resource Manager-sablonban, például:

```json
{
    "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
    "apiVersion": "[variables('sfApiVersion')]",
    "name": "[concat(parameters('clusterName'), '/', parameters('nodeTypeName'))]",
    "dependsOn": [
        "[concat('Microsoft.ServiceFabric/managedclusters/', parameters('clusterName'))]"
    ],
    "location": "[resourceGroup().location]",
    "properties": {
        "isPrimary": true,
        "vmInstanceCount": 3,
        "dataDiskSizeGB": 100,
        "vmSize": "Standard_D2",
        "vmImagePublisher": "MicrosoftWindowsServer",
        "vmImageOffer": "WindowsServer",
        "vmImageSku": "2019-Datacenter",
        "vmImageVersion": "latest",
        "vmExtensions": [{
            "name": "ExtensionA",
            "properties": {
                "publisher": "ExtensionA.Publisher",
                "type": "KeyVaultForWindows",
                "typeHandlerVersion": "1.0",
                "autoUpgradeMinorVersion": true,
                "settings": {
                }
            }
        }]
    }
}
```

Service Fabric felügyelt fürtcsomópontok típusának konfigurálásával kapcsolatos további információkért lásd: [felügyelt fürtcsomópont típusa](/azure/templates/microsoft.servicefabric/2020-01-01-preview/managedclusters/nodetypes).

## <a name="next-steps"></a>Következő lépések

A Service Fabric felügyelt fürtökkel kapcsolatos további tudnivalókért tekintse meg a következőt:

> [!div class="nextstepaction"]
> [Service Fabric felügyelt fürtök gyakran ismételt kérdései](./faq-managed-cluster.md)
