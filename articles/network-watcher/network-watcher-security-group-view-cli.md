---
title: Hálózati biztonság elemzése biztonsági csoport nézettel – Azure CLI
titleSuffix: Azure Network Watcher
description: Ez a cikk leírja, hogyan elemezheti a virtuális gépek biztonságát a biztonsági csoport nézettel az Azure CLI használatával.
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
ms.openlocfilehash: b3aa963c4da7802a9db714f25e7b544b3a132d4b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94948646"
---
# <a name="analyze-your-virtual-machine-security-with-security-group-view-using-azure-cli"></a>A virtuális gép biztonságának elemzése biztonsági csoport nézetben az Azure CLI használatával

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-security-group-view-powershell.md)
> - [Azure CLI](network-watcher-security-group-view-cli.md)
> - [REST API](network-watcher-security-group-view-rest.md)

> [!NOTE]
> A biztonsági csoport nézetének API-ját már nem tartja karban, és hamarosan elavulttá válik. Használja az [érvényes biztonsági szabályok funkciót](./network-watcher-security-group-view-overview.md) , amely ugyanazokat a funkciókat biztosítja.


A biztonsági csoport nézet a virtuális gépekre alkalmazott konfigurált és érvényes hálózati biztonsági szabályokat adja vissza. Ez a képesség hasznos lehet a hálózati biztonsági csoportok és a virtuális gépen konfigurált szabályok naplózására és diagnosztizálására, hogy a forgalom megfelelő engedélyezése vagy elutasítása sikeres legyen. Ebben a cikkben bemutatjuk, hogyan kérheti le a konfigurált és érvényes biztonsági szabályokat egy virtuális gépre az Azure CLI használatával

A cikkben szereplő lépések végrehajtásához [telepítenie kell az Azure parancssori felületét Mac, Linux és Windows rendszerre (CLI)](/cli/azure/install-azure-cli).

## <a name="before-you-begin"></a>Előkészületek

Ez a forgatókönyv feltételezi, hogy már követte a [Network Watcher létrehozása](network-watcher-create.md) című témakör lépéseit Network Watcher létrehozásához.

## <a name="scenario"></a>Eset

A cikkben ismertetett forgatókönyv egy adott virtuális gép konfigurált és érvényes biztonsági szabályait kérdezi le.

## <a name="get-a-vm"></a>Virtuális gép beszerzése

A parancsmag futtatásához virtuális gépnek kell futnia `vm list` . A következő parancs egy erőforráscsoport virtuális gépei listáját tartalmazza:

```azurecli
az vm list -resource-group resourceGroupName
```

Ha ismeri a virtuális gépet, használhatja a `vm show` parancsmagot az erőforrás-azonosító lekéréséhez:

```azurecli
az vm show -resource-group resourceGroupName -name virtualMachineName
```

## <a name="retrieve-security-group-view"></a>Biztonsági csoport nézetének beolvasása

A következő lépés a biztonsági csoport nézet eredményének beolvasása.

```azurecli
az network watcher show-security-group-view --resource-group resourceGroupName --vm vmName
```

## <a name="viewing-the-results"></a>Az eredmények megtekintése

Az alábbi példa a visszaadott eredmények rövidített válasza. Az eredmények a virtuális gép összes hatályos és alkalmazott biztonsági szabályát megjelenítik a **NetworkInterfaceSecurityRules**, a **DefaultSecurityRules** és a **EffectiveSecurityRules** csoportokban lebontva.

```json
{
  "networkInterfaces": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkInterfaces/{nicName}",
      "resourceGroup": "{resourceGroupName}",
      "securityRuleAssociations": {
        "defaultSecurityRules": [
          {
            "access": "Allow",
            "description": "Allow inbound traffic from all VMs in VNET",
            "destinationAddressPrefix": "VirtualNetwork",
            "destinationPortRange": "*",
            "direction": "Inbound",
            "etag": null,
            "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups//providers/Microsoft.Network/networkSecurityGroups/{nsgName}/defaultSecurityRules/AllowVnetInBound",
            "name": "AllowVnetInBound",
            "priority": 65000,
            "protocol": "*",
            "provisioningState": "Succeeded",
            "resourceGroup": "",
            "sourceAddressPrefix": "VirtualNetwork",
            "sourcePortRange": "*"
          }...
        ],
        "effectiveSecurityRules": [
          {
            "access": "Deny",
            "destinationAddressPrefix": "*",
            "destinationPortRange": "0-65535",
            "direction": "Outbound",
            "expandedDestinationAddressPrefix": null,
            "expandedSourceAddressPrefix": null,
            "name": "DefaultOutboundDenyAll",
            "priority": 65500,
            "protocol": "All",
            "sourceAddressPrefix": "*",
            "sourcePortRange": "0-65535"
          },
          {
            "access": "Allow",
            "destinationAddressPrefix": "VirtualNetwork",
            "destinationPortRange": "0-65535",
            "direction": "Outbound",
            "expandedDestinationAddressPrefix": [
              "10.1.0.0/24",
              "168.63.129.16/32"
            ],
            "expandedSourceAddressPrefix": [
              "10.1.0.0/24",
              "168.63.129.16/32"
            ],
            "name": "DefaultRule_AllowVnetOutBound",
            "priority": 65000,
            "protocol": "All",
            "sourceAddressPrefix": "VirtualNetwork",
            "sourcePortRange": "0-65535"
          },...
        ],
        "networkInterfaceAssociation": {
          "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkInterfaces/{nicName}",
          "resourceGroup": "{resourceGroupName}",
          "securityRules": [
            {
              "access": "Allow",
              "description": null,
              "destinationAddressPrefix": "*",
              "destinationPortRange": "3389",
              "direction": "Inbound",
              "etag": "W/\"efb606c1-2d54-475a-ab20-da3f80393577\"",
              "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}/securityRules/default-allow-rdp",
              "name": "default-allow-rdp",
              "priority": 1000,
              "protocol": "TCP",
              "provisioningState": "Succeeded",
              "resourceGroup": "{resourceGroupName}",
              "sourceAddressPrefix": "*",
              "sourcePortRange": "*"
            }
          ]
        },
        "subnetAssociation": null
      }
    }
  ]
}
```

## <a name="next-steps"></a>Következő lépések

A hálózati biztonsági csoportok érvényesítésének automatizálásához látogasson el [Network Watcher a hálózati biztonsági csoportok (NSG) naplózása](network-watcher-nsg-auditing-powershell.md) című témakörre.

További információ a hálózati erőforrásokra alkalmazott biztonsági szabályokról: a [biztonsági csoport megtekintésének áttekintése](network-watcher-security-group-view-overview.md)