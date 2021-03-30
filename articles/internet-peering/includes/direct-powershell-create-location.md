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
ms.openlocfilehash: dbaa0b5fc87cb5393b323b8a9b7a38b72efe9518
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "81680776"
---
A **Get-AzPeeringLocation PowerShell-** parancsmag a kötelező paraméterrel adja vissza a társítási helyszínek listáját `Kind` , amelyet a későbbi lépésekben fog használni.

```powershell
Get-AzPeeringLocation -Kind Direct
```

A közvetlen társítási helyszínek a következő mezőket tartalmazzák:
* PeeringLocation 
* Ország
* PeeringDBFacilityId
* PeeringDBFacilityLink
* BandwidthOffers

Győződjön meg arról, hogy a kívánt társ-létrehozási létesítményben jelen van a [PeeringDB](https://wwww.peeringdb.com).

Ebből a példából megtudhatja, hogyan használható a Seattle a közvetlen társítás létrehozásához.

```powershell
$peeringLocations = Get-AzPeeringLocation -Kind Direct
$peeringLocation = $peeringLocations | where {$_.PeeringLocation -contains "Seattle"}
$peeringLocation

PeeringLocation       : Seattle
Address               : 2001 Sixth Avenue
Country               : US
PeeringDBFacilityId   : 71
PeeringDBFacilityLink : https://www.peeringdb.com/fac/71
BandwidthOffers       : {10Gbps, 100Gbps}
```