---
title: Metrikák és riasztások az Azure Virtual Network NAT-hoz
titleSuffix: Azure Virtual Network
description: A Virtual Network NAT-hoz elérhető Azure Monitor mérőszámok és riasztások ismertetése.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: nat
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/04/2020
ms.author: allensu
ms.openlocfilehash: b22d7823ac961de53914325d7ebf2b5d53b7c5af
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106055824"
---
# <a name="azure-virtual-network-nat-metrics"></a>Azure Virtual Network NAT-metrikák

Az Azure Virtual Network NAT-átjáró erőforrásai több dimenziós mérőszámokat biztosítanak. Ezekkel a metrikákkal megfigyelheti a műveletet, és [hibaelhárítást](troubleshoot-nat.md)hajthat meg.  A riasztások olyan kritikus fontosságú problémákhoz konfigurálhatók, mint például a SNAT-kimerültség.

<p align="center">
  <img src="media/nat-overview/flow-direction1.svg" alt="Figure depicts a NAT gateway resource that consumes all IP addresses for a public IP prefix and directs that traffic to and from two subnets of virtual machines and a virtual machine scale set." width="256" title="NAT Virtual Network az internet felé">
</p>

*Ábra: Virtual Network NAT a kimenő internethez*

## <a name="metrics"></a>Mérőszámok

A NAT-átjáró erőforrásai a következő többdimenziós mérőszámokat biztosítják a Azure Monitorban:

| Metric | Leírás | Ajánlott összesítés | Dimenziók |
|---|---|---|---|
| Bájt | Bejövő és kimenő bájtok feldolgozott száma | Sum | Irány (a; Kimenő), protokoll (6 TCP; 17 UDP) |
| Csomagok | A bejövő és kimenő csomagok feldolgozása folyamatban | Sum | Irány (a; Kimenő), protokoll (6 TCP; 17 UDP) |
| Eldobott csomagok | A NAT-átjáró által eldobott csomagok | Sum | / |
| SNAT-kapcsolatok száma | Állapot-átmenetek/időköz | Sum | Kapcsolat állapota, protokoll (6 TCP; 17 UDP) |
| SNAT-kapcsolatok száma összesen | Aktuális aktív SNAT-kapcsolatok (~ SNAT-portok használatban) | Sum | Protokoll (6 TCP; 17 UDP) |


## <a name="alerts"></a>Riasztások

A metrikákkal kapcsolatos riasztások az összes korábbi [metrika](#metrics)esetében Azure monitor konfigurálhatók.

## <a name="limitations"></a>Korlátozások

A Resource Health nem támogatott.

## <a name="next-steps"></a>Következő lépések

* Tudnivalók a [Virtual Network NAT](nat-overview.md) -ról
* Tudnivalók a [NAT-átjáró erőforrásáról](nat-gateway-resource.md)
* Tudnivalók a [Azure monitor](../azure-monitor/overview.md)
* További információ a [NAT-átjárók erőforrásainak hibaelhárításáról](troubleshoot-nat.md).
* [Ossza meg velünk a következőt Virtual Network NAT UserVoice-ben való létrehozásához](https://aka.ms/natuservoice).


