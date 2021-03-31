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
ms.openlocfilehash: 610bac5c08d7f3aa3c93e273bc6573a08ca1239f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "81680748"
---
Az alábbi példa bemutatja, hogyan hozhat létre egy 10 GB/s-os közvetlen társas kapcsolatot Seattle-ben.

A **New-AzPeeringDirectConnectionObject PowerShell-** parancsmaggal hozzon létre olyan DirectConnection-objektumokat, amelyeket használni szeretne az új egyenrangú kérelemben.

Ebből a példából megtudhatja, hogyan hozhat létre egy DirectConnection objektumot.

```powershell
$connection1 = New-AzPeeringDirectConnectionObject `
    -PeeringDBFacilityId $peeringLocation[0].PeeringDBFacilityId `
    -SessionPrefixV4 10.21.31.0/31 `
    -SessionPrefixV6 fe01::3e:0/127 `
    -MaxPrefixesAdvertisedIPv4 1000 `
    -MaxPrefixesAdvertisedIPv6 100 `
    -BandwidthInMbps 10000
```

> [!NOTE]
> Az <index> előző példában szereplő **$peeringLocation []** értékének <index> meg kell egyeznie az Ön által választott egyenrangú hellyel.

Hozzon létre egy másik kapcsolatot abban az esetben, ha redundancia szükséges egy adott összevonási helyen.

```powershell
$connection2 = New-AzPeeringDirectConnectionObject `
    -PeeringDBFacilityId $peeringLocation[0].PeeringDBFacilityId `
    -SessionPrefixV4 10.21.33.0/31 `
    -SessionPrefixV6 fe01::3f:0/127 `
    -MaxPrefixesAdvertisedIPv4 1000 `
    -MaxPrefixesAdvertisedIPv6 100 `
    -BandwidthInMbps 10000
```

Új közvetlen társítás létrehozásához használja a **New-AzPeering PowerShell-** parancsmagot. Ehhez a parancshoz szükség van egy ASN erőforrás-AZONOSÍTÓra, amely az itt látható módon kérhető le.


```powershell
$asn = Get-AzPeerAsn
New-AzPeering `
    -Name "SeattleDirectPeering" `
    -ResourceGroupName "PeeringResourceGroup" `
    -PeeringLocation  $peeringLocation[0].PeeringLocation `
    -PeerAsnResourceId $asn.Id `
    -DirectConnection $connection1 [, $connection2]
```
&nbsp;

Ez a példa a kérelem sikeres feldolgozásakor a választ mutatja.

```powershell

    Name                 : SeattleDirectPeering
    Sku.Name             : Basic_Direct_Free
    Kind                 : Direct
    Connections          : 71
    PeerAsn.Id           : /subscriptions/{subscriptionId}/providers/Microsoft.Peering/peerAsns/SeattleDirectPeering
    UseForPeeringService : False
    PeeringLocation      : Seattle
    ProvisioningState    : Succeeded
    Location             : centralus
    Id                   : /subscriptions/{subscriptionId}/resourceGroups/PeeringResourceGroup/providers/Microsoft.Peering/peerings/SeattleDirectPeering
    Type                 : Microsoft.Peering/peerings
    Tags                 : {}

```
Vegye figyelembe, hogy ebben a kimenetben **{subscriptionId}** helyett a tényleges előfizetés-azonosító fog megjelenni.
