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
ms.openlocfilehash: b2609a069872ec55ac9068fadcbb3f312d68a630
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "81680769"
---
Ez a szakasz azt ismerteti, hogyan hajtható végre a következő módosítási műveletek a közvetlen társításhoz:

* Közvetlen kapcsolódási kapcsolatok hozzáadása.
* Távolítsa el a közvetlen társas kapcsolatokat.
* A sávszélesség frissítése vagy visszalépése az aktív kapcsolatokon.
* IPv4-vagy IPv6-munkamenetek hozzáadása az aktív kapcsolatokon.
* IPv4-vagy IPv6-munkamenetek eltávolítása az aktív kapcsolatokon.

### <a name="add-direct-peering-connections"></a>Közvetlen társas kapcsolatok hozzáadása

Ez a példa azt ismerteti, hogyan lehet kapcsolatokat hozzáadni a meglévő közvetlen társításokhoz.

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"

$connection = New-AzPeeringDirectConnection `
    -PeeringDBFacilityId $peeringLocation.PeeringDBFacilityId `
    -SessionPrefixV4 "10.22.31.0/31" `
    -SessionPrefixV6 "fe02::3e:0/127" `
    -MaxPrefixesAdvertisedIPv4 1000 `
    -MaxPrefixesAdvertisedIPv6 100 `
    -BandwidthInMbps 10000

$directPeering.Connections.Add($connection)

$directPeering | Update-AzPeering
```

### <a name="remove-direct-peering-connections"></a>Közvetlen társas kapcsolatok eltávolítása

A kapcsolatok eltávolítása jelenleg nem támogatott a PowerShellben. További információért forduljon a [Microsoft-partnerekhez](mailto:peeringexperience@microsoft.com).

<!--
```powershell
$directPeering.Connections.Remove($directPeering.Connections[0])

$directPeering | Update-AzPeering
```
-->

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>A sávszélesség frissítése vagy visszalépése aktív kapcsolatokon

Ez a példa azt ismerteti, hogyan adhat hozzá 10 GB/s-t egy meglévő közvetlen kapcsolódáshoz.

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"
$directPeering.Connections[0].BandwidthInMbps  = 20000
$directPeering | Update-AzPeering

```

### <a name="add-ipv4-or-ipv6-sessions-on-active-connections"></a>IPv4-vagy IPv6-munkamenetek hozzáadása az aktív kapcsolatokon

Ez a példa azt ismerteti, hogyan adhat hozzá IPv6-munkamenetet egy meglévő közvetlen kapcsolathoz csak egy IPv4-munkamenettel. 

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"
$directPeering.Connections[0].BGPSession.SessionPrefixv6 = "fe01::3e:0/127"
$directPeering | Update-AzPeering

```

### <a name="remove-ipv4-or-ipv6-sessions-on-active-connections"></a>IPv4-vagy IPv6-munkamenetek eltávolítása az aktív kapcsolatokon

Egy IPv4-vagy IPv6-munkamenet meglévő kapcsolatból való eltávolítása jelenleg nem támogatott a PowerShellen. További információért forduljon a [Microsoft-partnerekhez](mailto:peeringexperience@microsoft.com).