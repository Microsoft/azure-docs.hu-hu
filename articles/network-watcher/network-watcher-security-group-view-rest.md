---
title: Hálózati biztonság elemzése – biztonsági csoport nézet – Azure REST API
titleSuffix: Azure Network Watcher
description: Ez a cikk leírja, hogyan elemezheti az Azure REST API a virtuális gépek biztonságának elemzését biztonsági csoport nézetben.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 2efd3e9c9ca97ea3d94b03bd5e440cd24d5da5da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94960582"
---
# <a name="analyze-your-virtual-machine-security-with-security-group-view-using-rest-api"></a>A virtuális gép biztonságának elemzése a biztonsági csoport nézetben REST API használatával

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-security-group-view-powershell.md)
> - [Azure CLI](network-watcher-security-group-view-cli.md)
> - [REST API](network-watcher-security-group-view-rest.md)

> [!NOTE]
> A biztonsági csoport nézetének API-ját már nem tartja karban, és hamarosan elavulttá válik. Használja az [érvényes biztonsági szabályok funkciót](./network-watcher-security-group-view-overview.md) , amely ugyanazokat a funkciókat biztosítja. 

A biztonsági csoport nézet a virtuális gépekre alkalmazott konfigurált és érvényes hálózati biztonsági szabályokat adja vissza. Ez a képesség hasznos lehet a hálózati biztonsági csoportok és a virtuális gépen konfigurált szabályok naplózására és diagnosztizálására, hogy a forgalom megfelelő engedélyezése vagy elutasítása sikeres legyen. Ebben a cikkben bemutatjuk, hogyan kérhető le a hatályos és alkalmazott biztonsági szabályok egy virtuális gépre REST API használatával


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Előkészületek

Ebben az esetben a Network Watcher REST API-t hívja meg egy virtuális gép biztonsági csoportjának nézetének beolvasásához. A ARMclient a REST API a PowerShell használatával történő meghívására szolgál. A ARMClient a chocolatey címen található a [ARMClient-on](https://chocolatey.org/packages/ARMClient)

Ez a forgatókönyv feltételezi, hogy már követte a [Network Watcher létrehozása](network-watcher-create.md) című témakör lépéseit Network Watcher létrehozásához. A forgatókönyv azt is feltételezi, hogy egy érvényes virtuális géppel rendelkező erőforráscsoport használatban van.

## <a name="scenario"></a>Eset

A cikkben ismertetett forgatókönyv az adott virtuális gép hatályos és alkalmazott biztonsági szabályait kérdezi le.

## <a name="log-in-with-armclient"></a>Bejelentkezés a ARMClient

```powershell
armclient login
```

## <a name="retrieve-a-virtual-machine"></a>Virtuális gép beolvasása

Futtassa a következő szkriptet egy virtuális machineThe visszaadásához a kód igény szerinti változói közül:

- **subscriptionId** – az előfizetés-azonosító a **Get-AzSubscription** parancsmaggal is lekérhető.
- **resourceGroupName** – a virtuális gépeket tartalmazó erőforráscsoport neve.

```powershell
$subscriptionId = '<subscription id>'
$resourceGroupName = '<resource group name>'

armclient get https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Compute/virtualMachines?api-version=2015-05-01-preview
```

A szükséges információk a válaszban szereplő **azonosítók** `Microsoft.Compute/virtualMachines` , ahogy az a következő példában látható:

```json
...,
        "networkProfile": {
          "networkInterfaces": [
            {
              "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft
.Network/networkInterfaces/{nicName}"
            }
          ]
        },
        "provisioningState": "Succeeded"
      },
      "resources": [
        {
          "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Com
pute/virtualMachines/{vmName}/extensions/CustomScriptExtension"
        }
      ],
      "type": "Microsoft.Compute/virtualMachines",
      "location": "westcentralus",
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute
/virtualMachines/{vmName}",
      "name": "{vmName}"
    }
  ]
}
```

## <a name="get-security-group-view-for-virtual-machine"></a>Biztonsági csoport nézetének beolvasása a virtuális géphez

A következő példa egy célként megadott virtuális gép biztonsági csoport nézetét kéri le. Az ebből a példából származó eredményekkel összehasonlítható a konfiguráció eltolódása által meghatározott szabályokkal és biztonsággal.

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "<resource group name>"
$networkWatcherName = "<network watcher name>"
$targetUri = "<uri of target resource>" # Example: /subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.compute/virtualMachine/$vmName

$requestBody = @"
{
    'targetResourceId': '${targetUri}'

}
"@
armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/securityGroupView?api-version=2016-12-01" $requestBody -verbose
```

## <a name="view-the-response"></a>Válasz megtekintése

Az alábbi példa az előző parancs válaszát adja vissza. Az eredmények a virtuális gép összes hatályos és alkalmazott biztonsági szabályát megjelenítik a **NetworkInterfaceSecurityRules**, a **DefaultSecurityRules** és a **EffectiveSecurityRules** csoportokban lebontva.

```json

{
  "networkInterfaces": [
    {
      "securityRuleAssociations": {
        "networkInterfaceAssociation": {
          "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkInterfaces/{nicName}",
          "securityRules": [
            {
              "name": "default-allow-rdp",
              "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}/securityRules/default-allow-rdp",
              "etag": "W/\"d4c411d4-0d62-49dc-8092-3d4b57825740\"",
              "properties": {
                "provisioningState": "Succeeded",
                "protocol": "TCP",
                "sourcePortRange": "*",
                "destinationPortRange": "3389",
                "sourceAddressPrefix": "*",
                "destinationAddressPrefix": "*",
                "access": "Allow",
                "priority": 1000,
                "direction": "Inbound"
              }
            }
          ]
        },
        "defaultSecurityRules": [
          {
            "name": "AllowVnetInBound",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}/defaultSecurityRules/",
            "properties": {
              "provisioningState": "Succeeded",
              "description": "Allow inbound traffic from all VMs in VNET",
              "protocol": "*",
              "sourcePortRange": "*",
              "destinationPortRange": "*",
              "sourceAddressPrefix": "VirtualNetwork",
              "destinationAddressPrefix": "VirtualNetwork",
              "access": "Allow",
              "priority": 65000,
              "direction": "Inbound"
            }
          },
          ...
        ],
        "effectiveSecurityRules": [
          {
            "name": "DefaultOutboundDenyAll",
            "protocol": "All",
            "sourcePortRange": "0-65535",
            "destinationPortRange": "0-65535",
            "sourceAddressPrefix": "*",
            "destinationAddressPrefix": "*",
            "access": "Deny",
            "priority": 65500,
            "direction": "Outbound"
          },
          ...
        ]
      }
    }
  ]
}
```

## <a name="next-steps"></a>Következő lépések

A hálózati biztonsági csoportok érvényesítésének automatizálásához látogasson el [Network Watcher a hálózati biztonsági csoportok (NSG) naplózása](network-watcher-security-group-view-powershell.md) című témakörre.