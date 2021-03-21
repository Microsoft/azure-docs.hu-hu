---
title: Azure Traffic Manager | Microsoft Docs
description: Ez a cikk az Azure Traffic Managerről nyújt áttekintést. Megtudhatja, hogy a megfelelő választás-e a felhasználói forgalom terheléselosztásához az alkalmazáshoz.
services: traffic-manager
author: duongau
ms.service: traffic-manager
customer intent: As an IT admin, I want to learn about Traffic Manager and what I can use it for.
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/19/2021
ms.author: duau
ms.openlocfilehash: 8c8897218b153c8584c89abab98934268ccd555d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102182162"
---
# <a name="what-is-traffic-manager"></a>Mi az a Traffic Manager?
Az Azure Traffic Manager egy DNS-alapú forgalom terheléselosztó. Ez a szolgáltatás lehetővé teszi, hogy a globális Azure-régiókon keresztül továbbítsa a forgalmat a nyilvánosan elérhető alkalmazásokra. A Traffic Manager magas rendelkezésre állású és gyors reagálású nyilvános végpontokat is biztosít.

A Traffic Manager a DNS használatával irányítja az ügyfelek kéréseit a megfelelő szolgáltatási végpontra egy forgalom-útválasztási módszer alapján. A Traffic Manager az összes végpont állapotának figyelését is biztosítja. A végpont bármely, az Azure-on belüli vagy kívül üzemeltetett internetes szolgáltatás lehet. A Traffic Manager különböző [forgalom-útválasztási módszereket](traffic-manager-routing-methods.md) és [végpont-monitorozási lehetőségeket](traffic-manager-monitoring.md) biztosít, hogy megfeleljen a különböző alkalmazások igényeinek és az automatikus feladatátvételi modelleknek. A Traffic Manager ellenáll a meghibásodásoknak, beleértve akár egy egész Azure-régió meghibásodását is.

>[!NOTE]
> Az Azure teljeskörűen felügyelt terheléselosztási megoldások együttesét biztosítja a különböző forgatókönyvekre. 
> * Ha terheléselosztást szeretne betölteni a kiszolgálók között az alkalmazási réteg egyik régiójában, tekintse át [Application Gateway](../application-gateway/overview.md).
> * Ha optimalizálni szeretné a webes forgalom globális útválasztását, és a gyors globális feladatátvételsel optimalizálja a legfelső szintű végfelhasználói teljesítményt és megbízhatóságot, tekintse meg a [bejárati ajtót](../frontdoor/front-door-overview.md).
> * A hálózati réteg terheléselosztásának elvégzéséhez tekintse át [Load Balancer](../load-balancer/load-balancer-overview.md). 
> 
> A végpontok közötti forgatókönyvek igény szerint összekapcsolják ezeket a megoldásokat.
> Az Azure terheléselosztási lehetőségeinek összehasonlítását lásd: [Az Azure terheléselosztási lehetőségeinek áttekintése](/azure/architecture/guide/technology-choices/load-balancing-overview).

Traffic Manager a következő funkciókat kínálja:

## <a name="increase-application-availability"></a>Alkalmazás rendelkezésre állásának növelése

A Traffic Manager lehetővé teszi a kritikus fontosságú alkalmazások magas rendelkezésre állását azáltal, hogy figyeli a végpontokat, és automatikus feladatátvételt biztosít, ha egy végpont leáll.
    
## <a name="improve-application-performance"></a>Az alkalmazásteljesítmény javítása

Az Azure lehetővé teszi, hogy felhőalapú szolgáltatásokat és webhelyeket futtasson a világ minden pontján található adatközpontokban. A Traffic Manager a legalacsonyabb késéssel a végpont felé irányuló forgalmat irányítva növelheti a webhely rugalmasságát.

## <a name="service-maintenance-without-downtime"></a>Szolgáltatás karbantartása állásidő nélkül

A tervezett karbantartást állásidő nélkül is elvégezheti az alkalmazásokban. A Traffic Manager a karbantartás folyamatban van, és átirányítja a forgalmat az alternatív végpontokra.

## <a name="combine-hybrid-applications"></a>Hibrid alkalmazások kombinációja

A Traffic Manager támogatja a külső, nem Azure-végpontokat, ami lehetővé teszi a hibrid felhőalapú és helyszíni telepítéssel történő használatot, beleértve a „[felhőbe irányítás](https://azure.microsoft.com/overview/what-is-cloud-bursting/)”, „felhőbe migrálás” és „felhő általi feladatátvétel” eseteket is.

## <a name="distribute-traffic-for-complex-deployments"></a>Forgalom elosztása összetett üzemelő példányokhoz

A [beágyazott Traffic Manager profilok](traffic-manager-nested-profiles.md)használatával több forgalom-útválasztási módszer kombinálható összetett és rugalmas szabályok létrehozásához a nagyobb, összetettebb központi telepítések igényei szerint.

## <a name="pricing"></a>Díjszabás

Díjszabási információkért tekintse meg a [Traffic Manager díjszabását](https://azure.microsoft.com/pricing/details/traffic-manager/) ismertető részt.


## <a name="next-steps"></a>Következő lépések

- Útmutató a [Traffic Manager-profil létrehozásához](./quickstart-create-traffic-manager-profile.md).
- Útmutató a [Traffic Manager működéséhez](traffic-manager-how-it-works.md).
- Tekintse meg a [gyakori kérdések](traffic-manager-FAQs.md) részt a Traffic Managerrel kapcsolatban.