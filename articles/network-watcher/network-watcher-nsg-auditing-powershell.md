---
title: NSG-naplózás automatizálása – biztonsági csoport nézet
titleSuffix: Azure Network Watcher
description: Ez a lap a hálózati biztonsági csoportok naplózásának konfigurálására vonatkozó utasításokat tartalmazza.
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
ms.openlocfilehash: 177215775c9e83286aa98872eed0ab211a8f36ff
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94948748"
---
# <a name="automate-nsg-auditing-with-azure-network-watcher-security-group-view"></a>A NSG-naplózás automatizálása az Azure Network Watcher biztonsági csoport nézettel

Az ügyfelek gyakran szembesülnek azzal a kihívással, hogy igazolják az infrastruktúrájuk biztonsági állapotát. Ez a kihívás nem különbözik az Azure-beli virtuális gépektől. Fontos, hogy a hálózati biztonsági csoport (NSG) szabályai alapján hasonló biztonsági profilt alkalmazzon. A biztonsági csoport nézet használatával lekérheti a virtuális gépen alkalmazott szabályok listáját egy NSG belül. Megadhat egy arany NSG biztonsági profilt, és elindíthatja a biztonsági csoport nézetet egy heti lépésszám esetében, és összehasonlíthatja a kimenetet az arany profiljával, és létrehozhat egy jelentést. Így könnyen azonosíthatja az összes olyan virtuális gépet, amely nem felel meg az előírt biztonsági profilnak.

Ha nem ismeri a hálózati biztonsági csoportokat, tekintse meg a [hálózati biztonság áttekintése](../virtual-network/network-security-groups-overview.md)című témakört.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Előkészületek

Ebben az esetben összehasonlítja egy ismert jó alapkonfigurációt a virtuális gép biztonsági csoport nézetének eredményeivel.

Ez a forgatókönyv feltételezi, hogy már követte a [Network Watcher létrehozása](network-watcher-create.md) című témakör lépéseit Network Watcher létrehozásához. A forgatókönyv azt is feltételezi, hogy egy érvényes virtuális géppel rendelkező erőforráscsoport használatban van.

## <a name="scenario"></a>Eset

A cikkben ismertetett forgatókönyv beolvassa a virtuális gép biztonsági csoportjának nézetét.

Ebben az esetben a következőket kell tennie:

- Ismert helyes szabálykészlet beolvasása
- Virtuális gép lekérése a REST API-val
- Biztonsági csoport nézetének beolvasása a virtuális géphez
- Válasz kiértékelése

## <a name="retrieve-rule-set"></a>Szabálykészlet beolvasása

Ebben a példában az első lépés egy meglévő alapterv használata. Az alábbi példa egy meglévő hálózati biztonsági csoportból kinyert JSON-t használja a `Get-AzNetworkSecurityGroup` példa alaptervként használt parancsmag használatával.

```json
[
    {
        "Description":  null,
        "Protocol":  "TCP",
        "SourcePortRange":  "*",
        "DestinationPortRange":  "3389",
        "SourceAddressPrefix":  "*",
        "DestinationAddressPrefix":  "*",
        "Access":  "Allow",
        "Priority":  1000,
        "Direction":  "Inbound",
        "ProvisioningState":  "Succeeded",
        "Name":  "default-allow-rdp",
        "Etag":  "W/\"d8859256-1c4c-4b93-ba7d-73d9bf67c4f1\"",
        "Id":  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/testvm1-nsg/securityRules/default-allow-rdp"
    },
    {
        "Description":  null,
        "Protocol":  "*",
        "SourcePortRange":  "*",
        "DestinationPortRange":  "111",
        "SourceAddressPrefix":  "*",
        "DestinationAddressPrefix":  "*",
        "Access":  "Allow",
        "Priority":  1010,
        "Direction":  "Inbound",
        "ProvisioningState":  "Succeeded",
        "Name":  "MyRuleDoNotDelete",
        "Etag":  "W/\"d8859256-1c4c-4b93-ba7d-73d9bf67c4f1\"",
        "Id":  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/testvm1-nsg/securityRules/MyRuleDoNotDelete"
    },
    {
        "Description":  null,
        "Protocol":  "*",
        "SourcePortRange":  "*",
        "DestinationPortRange":  "112",
        "SourceAddressPrefix":  "*",
        "DestinationAddressPrefix":  "*",
        "Access":  "Allow",
        "Priority":  1020,
        "Direction":  "Inbound",
        "ProvisioningState":  "Succeeded",
        "Name":  "My2ndRuleDoNotDelete",
        "Etag":  "W/\"d8859256-1c4c-4b93-ba7d-73d9bf67c4f1\"",
        "Id":  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/testvm1-nsg/securityRules/My2ndRuleDoNotDelete"
    },
    {
        "Description":  null,
        "Protocol":  "TCP",
        "SourcePortRange":  "*",
        "DestinationPortRange":  "5672",
        "SourceAddressPrefix":  "*",
        "DestinationAddressPrefix":  "*",
        "Access":  "Deny",
        "Priority":  1030,
        "Direction":  "Inbound",
        "ProvisioningState":  "Succeeded",
        "Name":  "ThisRuleNeedsToStay",
        "Etag":  "W/\"d8859256-1c4c-4b93-ba7d-73d9bf67c4f1\"",
        "Id":  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/testvm1-nsg/securityRules/ThisRuleNeedsToStay"
    }
]
```

## <a name="convert-rule-set-to-powershell-objects"></a>Szabálykészlet konvertálása PowerShell-objektumokra

Ebben a lépésben egy olyan JSON-fájlt olvasunk, amely korábban a hálózati biztonsági csoportra várt szabályokkal lett létrehozva ebben a példában.

```powershell
$nsgbaserules = Get-Content -Path C:\temp\testvm1-nsg.json | ConvertFrom-Json
```

## <a name="retrieve-network-watcher"></a>Network Watcher beolvasása

A következő lépés az Network Watcher példány beolvasása. A rendszer átadja a `$networkWatcher` változót a `AzNetworkWatcherSecurityGroupView` parancsmagnak.

```powershell
$networkWatcher = Get-AzResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" } 
```

## <a name="get-a-vm"></a>Virtuális gép beszerzése

A parancsmag futtatásához virtuális gépnek kell futnia `Get-AzNetworkWatcherSecurityGroupView` . A következő példa egy virtuálisgép-objektumot kap.

```powershell
$VM = Get-AzVM -ResourceGroupName "testrg" -Name "testvm1"
```

## <a name="retrieve-security-group-view"></a>Biztonsági csoport nézetének beolvasása

A következő lépés a biztonsági csoport nézet eredményének beolvasása. Ezt az eredményt a korábban bemutatott "alapkonfiguráció" JSON-hez hasonlítjuk.

```powershell
$secgroup = Get-AzNetworkWatcherSecurityGroupView -NetworkWatcher $networkWatcher -TargetVirtualMachineId $VM.Id
```

## <a name="analyzing-the-results"></a>Az eredmények elemzése

A válasz hálózati adapterek szerint van csoportosítva. A visszaadott szabályok különböző típusai érvényesek és alapértelmezett biztonsági szabályok. Az eredmény további bontása az alkalmazás működésével, vagy egy alhálózaton vagy egy virtuális hálózati adapteren.

A következő PowerShell-parancsfájl összehasonlítja a biztonsági csoport nézetének eredményét egy NSG meglévő kimenetével. Az alábbi példa egy egyszerű példa arra, hogy az eredményeket hogyan lehet összehasonlítani a `Compare-Object` parancsmaggal.

```powershell
Compare-Object -ReferenceObject $nsgbaserules `
-DifferenceObject $secgroup.NetworkInterfaces[0].NetworkInterfaceSecurityRules `
-Property Name,Description,Protocol,SourcePortRange,DestinationPortRange,SourceAddressPrefix,DestinationAddressPrefix,Access,Priority,Direction
```

A következő példa az eredmény. Láthatja, hogy az első szabálykészlet két szabálya nem volt jelen az összehasonlításban.

```
Name                     : My2ndRuleDoNotDelete
Description              : 
Protocol                 : *
SourcePortRange          : *
DestinationPortRange     : 112
SourceAddressPrefix      : *
DestinationAddressPrefix : *
Access                   : Allow
Priority                 : 1020
Direction                : Inbound
SideIndicator            : <=

Name                     : ThisRuleNeedsToStay
Description              : 
Protocol                 : TCP
SourcePortRange          : *
DestinationPortRange     : 5672
SourceAddressPrefix      : *
DestinationAddressPrefix : *
Access                   : Deny
Priority                 : 1030
Direction                : Inbound
SideIndicator            : <=
```

## <a name="next-steps"></a>Következő lépések

Ha a beállítások módosultak, tekintse meg a [hálózati biztonsági csoportok kezelése](../virtual-network/manage-network-security-group.md) a hálózati biztonsági csoport és a szóban forgó biztonsági szabályok nyomon követéséhez című témakört.