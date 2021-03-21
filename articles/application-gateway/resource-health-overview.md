---
title: Az Azure Application Gateway Resource Health áttekintése
description: Ez a cikk az Azure Resource Health szolgáltatásának áttekintését Application Gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 7/9/2019
ms.author: victorh
ms.openlocfilehash: 7e30a93f8270cfaf8910130cc1e2633bb80c2b8e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93397172"
---
# <a name="azure-application-gateway-resource-health-overview"></a>Az Azure Application Gateway Resource Health áttekintése

[Az Azure Resource Health](../service-health/resource-health-overview.md) segítséget nyújt a diagnosztizálásban és a támogatás igénylésében, ha egy Azure-szolgáltatással kapcsolatos probléma hatással van az erőforrásaira. Tájékoztatja az erőforrások aktuális és korábbi állapotáról. Emellett technikai támogatást nyújt a problémák enyhítéséhez.

Application Gateway esetén a Resource Health az átjáró által kibocsátott jelekre támaszkodik annak felmérésére, hogy kifogástalan-e vagy sem. Ha az átjáró nem kifogástalan állapotú, Resource Health elemzi a probléma forrásának megállapításához szükséges további információkat. Emellett azonosítja a Microsoft által végzett műveleteket, vagy a probléma megoldásához szükséges lépéseket.

Az állapot értékelésével kapcsolatos további részletekért tekintse át a [Azure Resource Healthban](../service-health/resource-health-checks-resource-types.md#microsoftnetworkapplicationgateways)található erőforrástípusok és állapot-ellenőrzések teljes listáját.


A Application Gateway állapota a következő állapotok egyike jelenik meg:

## <a name="available"></a>Elérhető

A **rendelkezésre álló** állapot azt jelenti, hogy a szolgáltatás nem észlelt olyan eseményt, amely hatással van az erőforrás állapotára. Ekkor megjelenik a **nemrég megoldott** értesítés azokban az esetekben, amikor az átjáró a nem tervezett állásidőből lett helyreállítva az elmúlt 24 órában.

![Rendelkezésre álló állapot](media/resource-health-overview/available-full.png)

## <a name="unavailable"></a>Nem érhető el

A nem **elérhető** állapot azt jelenti, hogy a szolgáltatás egy folyamatos platformot észlelt, vagy nem platformos eseményt észlelt az átjáró állapotának hatására.

### <a name="platform-events"></a>Platformesemények

A platform eseményeire az Azure-infrastruktúra több összetevője is aktiválódik. Ezek közé tartoznak az ütemezett műveletek (például a tervezett karbantartás) és a váratlan incidensek (például egy nem tervezett gazdagép újraindítása).

Resource Health további részleteket tartalmaz az eseményről és a helyreállítási folyamatról. Azt is lehetővé teszi, hogy forduljon az ügyfélszolgálathoz, még akkor is, ha nincs aktív Microsoft-támogatási szerződése.

![Nem elérhető állapot](media/resource-health-overview/unavailable.png)

## <a name="unknown"></a>Ismeretlen

Az **ismeretlen** állapot azt jelzi, Resource Health több mint 10 percen belül nem érkezett információ az átjáróról. Ez az állapot nem az átjáró állapotának végleges jelzése. A hibaelhárítási folyamat azonban fontos adatpont.

Ha az átjáró a várt módon fut, az állapot pár perc elteltével **elérhetővé** vált.

Ha problémákat tapasztal, az **ismeretlen** állapot arra utalhat, hogy a platformon egy esemény hatással van az átjáróra.

![Ismeretlen állapot](media/resource-health-overview/unknown.png)

## <a name="degraded"></a>Csökkentett teljesítményű

A **csökkentett** állapotú állapot azt jelzi, hogy az átjáró a teljesítmény csökkenését észlelte, bár még mindig elérhető a használathoz.

![Degratis állapot](media/resource-health-overview/degraded.png)

## <a name="next-steps"></a>Következő lépések

A webalkalmazási tűzfal (WAF) Application Gateway hibaelhárításával kapcsolatos további információkért lásd: [webalkalmazási tűzfal (WAF) hibaelhárítása Az Azure Application Gatewayhoz](../web-application-firewall/ag/web-application-firewall-troubleshoot.md).