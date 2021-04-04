---
title: Hálózati biztonság – biztonsági csoport nézetének elemzése – Azure PowerShell
titleSuffix: Azure Network Watcher
description: Ez a cikk leírja, hogyan elemezheti a virtuális gépek biztonságát a biztonsági csoport nézettel a PowerShell használatával.
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
ms.openlocfilehash: 2da4ebfa6a9ce64b03e6e1d29956f740f6cb3c1b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94960610"
---
# <a name="analyze-your-virtual-machine-security-with-security-group-view-using-powershell"></a>A virtuális gép biztonságának elemzése a biztonsági csoport nézetben a PowerShell használatával

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-security-group-view-powershell.md)
> - [Azure CLI](network-watcher-security-group-view-cli.md)
> - [REST API](network-watcher-security-group-view-rest.md)

> [!NOTE]
> A biztonsági csoport nézetének API-ját már nem tartja karban, és hamarosan elavulttá válik. Használja az [érvényes biztonsági szabályok funkciót](./network-watcher-security-group-view-overview.md) , amely ugyanazokat a funkciókat biztosítja. 

A biztonsági csoport nézet a virtuális gépekre alkalmazott konfigurált és érvényes hálózati biztonsági szabályokat adja vissza. Ez a képesség hasznos lehet a hálózati biztonsági csoportok és a virtuális gépen konfigurált szabályok naplózására és diagnosztizálására, hogy a forgalom megfelelő engedélyezése vagy elutasítása sikeres legyen. Ebben a cikkben bemutatjuk, hogyan kérheti le a konfigurált és érvényes biztonsági szabályokat egy virtuális gépre a PowerShell használatával


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Előkészületek

Ebben az esetben a `Get-AzNetworkWatcherSecurityGroupView` parancsmagot futtatva kéri le a biztonsági szabály adatait.

Ez a forgatókönyv feltételezi, hogy már követte a [Network Watcher létrehozása](network-watcher-create.md) című témakör lépéseit Network Watcher létrehozásához.

## <a name="scenario"></a>Eset

A cikkben ismertetett forgatókönyv egy adott virtuális gép konfigurált és érvényes biztonsági szabályait kérdezi le.

## <a name="retrieve-network-watcher"></a>Network Watcher beolvasása

Az első lépés az Network Watcher példány beolvasása. A rendszer ezt a változót adja át a `Get-AzNetworkWatcherSecurityGroupView` parancsmagnak.

```powershell
$networkWatcher = Get-AzResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" }
```

## <a name="get-a-vm"></a>Virtuális gép beszerzése

A parancsmag futtatásához virtuális gépnek kell futnia `Get-AzNetworkWatcherSecurityGroupView` . A következő példa egy virtuálisgép-objektumot kap.

```powershell
$VM = Get-AzVM -ResourceGroupName testrg -Name testvm1
```

## <a name="retrieve-security-group-view"></a>Biztonsági csoport nézetének beolvasása

A következő lépés a biztonsági csoport nézet eredményének beolvasása.

```powershell
$secgroup = Get-AzNetworkWatcherSecurityGroupView -NetworkWatcher $networkWatcher -TargetVirtualMachineId $VM.Id
```

## <a name="viewing-the-results"></a>Az eredmények megtekintése

Az alábbi példa a visszaadott eredmények rövidített válasza. Az eredmények a virtuális gép összes hatályos és alkalmazott biztonsági szabályát megjelenítik a **NetworkInterfaceSecurityRules**, a **DefaultSecurityRules** és a **EffectiveSecurityRules** csoportokban lebontva.

```
NetworkInterfaces : [
                      {
                        "NetworkInterfaceSecurityRules": [
                          {
                            "Name": "default-allow-rdp",
                            "Etag": "W/\"d4c411d4-0d62-49dc-8092-3d4b57825740\"",
                            "Id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg2/providers/Microsoft.Network/networkSecurityGroups/testvm2-nsg/securityRules/default-allow-rdp",
                            "Protocol": "TCP",
                            "SourcePortRange": "*",
                            "DestinationPortRange": "3389",
                            "SourceAddressPrefix": "*",
                            "DestinationAddressPrefix": "*",
                            "Access": "Allow",
                            "Priority": 1000,
                            "Direction": "Inbound",
                            "ProvisioningState": "Succeeded"
                          }
                          ...
                        ],
                        "DefaultSecurityRules": [
                          {
                            "Name": "AllowVnetInBound",
                            "Id": "/subscriptions00000000-0000-0000-0000-000000000000/resourceGroups/testrg2/providers/Microsoft.Network/networkSecurityGroups/testvm2-nsg/defaultSecurityRules/",
                            "Description": "Allow inbound traffic from all VMs in VNET",
                            "Protocol": "*",
                            "SourcePortRange": "*",
                            "DestinationPortRange": "*",
                            "SourceAddressPrefix": "VirtualNetwork",
                            "DestinationAddressPrefix": "VirtualNetwork",
                            "Access": "Allow",
                            "Priority": 65000,
                            "Direction": "Inbound",
                            "ProvisioningState": "Succeeded"
                          }
                          ...
                        ],
                        "EffectiveSecurityRules": [
                          {
                            "Name": "DefaultOutboundDenyAll",
                            "Protocol": "All",
                            "SourcePortRange": "0-65535",
                            "DestinationPortRange": "0-65535",
                            "SourceAddressPrefix": "*",
                            "DestinationAddressPrefix": "*",
                            "ExpandedSourceAddressPrefix": [],
                            "ExpandedDestinationAddressPrefix": [],
                            "Access": "Deny",
                            "Priority": 65500,
                            "Direction": "Outbound"
                          },
                          ...
                        ]
                      }
                    ]
```

## <a name="next-steps"></a>Következő lépések

A hálózati biztonsági csoportok érvényesítésének automatizálásához látogasson el [Network Watcher a hálózati biztonsági csoportok (NSG) naplózása](network-watcher-nsg-auditing-powershell.md) című témakörre.