---
title: fájl belefoglalása
titleSuffix: Azure
description: fájl belefoglalása
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 12d169697a35af446392843eb57e6ec3a5508e45
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "81678670"
---
Exchange-társítás esetén a következő módosítási műveletek támogatottak:
* Exchange-partneri kapcsolatok hozzáadása.
* Az Exchange-társi kapcsolatok eltávolítása.
* IPv4-vagy IPv6-munkamenet hozzáadása az aktív kapcsolatokhoz.
* IPv4-vagy IPv6-munkamenet eltávolítása az aktív kapcsolatokon.


### <a name="add-exchange-peering-connections"></a>Exchange-partneri kapcsolatok hozzáadása

Ez a példa azt ismerteti, hogyan lehet kapcsolatokat hozzáadni egy meglévő Exchange-társhoz.

```powershell

$exchangePeering = Get-AzPeering -Name "SeattleExchangePeering" -ResourceGroupName "PeeringResourceGroup"

$connection = New-AzPeeringExchangeConnectionObject `
    -PeeringDBFacilityId $exchangeLocation[1].PeeringDBFacilityId `
    -PeerSessionIPv4Address 198.32.134.22 `
    -PeerSessionIPv6Address  2001:504:12::22 `
    -MaxPrefixesAdvertisedIPv4 2000 `
    -MaxPrefixesAdvertisedIPv6 2000 `

$exchangePeering.Connections.Add($connection)

$exchangePeering | Update-AzPeering

```

### <a name="remove-exchange-peering-connections"></a>Exchange-társi kapcsolatok eltávolítása

Ez a példa azt ismerteti, Hogyan távolítható el a kapcsolatok egy meglévő Exchange-partneri kapcsolattal.

```powershell

$exchangePeering = Get-AzPeering -Name "SeattleExchangePeering" -ResourceGroupName "PeeringResourceGroup"

```

Tekintse meg az összes kapcsolatot, és válassza ki az eltávolítani kívánt kapcsolatot. 

```powershell

$exchangePeering

Name              : SeattleExchangePeering
Sku.Name          : Basic_Exchange_Free
Kind              : Exchange
Connections       : {11}
PeerAsn.Id        : /subscriptions/{subscriptionId}/providers/Microsoft.Peering/peerAsns/{peerAsnName}
PeeringLocation   : Seattle
ProvisioningState : Succeeded
Location          : West US 2
Id                : /subscriptions/{subscriptionId}/resourceGroups/PeeringResourceGroup/providers/Microsoft.Peering/peerings/SeattleExchangePeering
Type              : Microsoft.Peering/peerings
Tags              : {}

```

A következő parancsban a 0 helyett adja meg az eltávolítani kívánt kapcsolatok indexének számát.

```powershell

$connection = $exchangePeering.Connections[0]

$exchangePeering.Connections.Remove($connection)

$exchangePeering | Update-AzPeering

```

### <a name="add-an-ipv4-or-ipv6-session-on-active-connections"></a>IPv4-vagy IPv6-munkamenet hozzáadása az aktív kapcsolatokhoz

Ez a példa azt ismerteti, hogyan adhat hozzá IPv6-munkamenetet egy meglévő Exchange-kapcsolathoz.

```powershell

$exchangePeering = Get-AzPeering -Name "SeattleExchangePeering" -ResourceGroupName "PeeringResourceGroup"

$exchangePeering.Connections[0].BgpSession.PeerSessionIPv6Address = "2001:504:12::34"

$exchangePeering | Update-AzPeering

```

### <a name="remove-an-ipv4-or-ipv6-session-on-active-connections"></a>IPv4-vagy IPv6-munkamenet eltávolítása az aktív kapcsolatokon

Egy IPv4-vagy IPv6-munkamenet meglévő kapcsolatból való eltávolítása jelenleg nem támogatott a PowerShellen. További információért forduljon a [Microsoft-partnerekhez](mailto:peeringexperience@microsoft.com).