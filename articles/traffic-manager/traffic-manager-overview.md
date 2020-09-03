---
title: Azure Traffic Manager | Microsoft Docs
description: Ez a cikk az Azure Traffic Managerről nyújt áttekintést. Ismerje meg, hogy ez-e a megfelelő választás az alkalmazáshoz érkező felhasználói forgalom terheléselosztásához.
services: traffic-manager
author: duongau
manager: twooley
ms.service: traffic-manager
customer intent: As an IT admin, I want to learn about Traffic Manager and what I can use it for.
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/23/2019
ms.author: duau
ms.openlocfilehash: 830700fb4a5ac57405877364e9cc4828e5d1a5a4
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/02/2020
ms.locfileid: "89392544"
---
# <a name="what-is-traffic-manager"></a>Mi az a Traffic Manager?
Az Azure Traffic Manager egy DNS-alapú forgalom-terheléselosztó, amely lehetővé teszi a szolgáltatásokhoz érkező forgalom optimális elosztását a globális Azure-régiókban, miközben magas rendelkezésre állást és válaszkészséget biztosít.

A Traffic Manager DNS használatával a leginkább megfelelő szolgáltatási végpontra irányítja az ügyfélkéréseket a forgalom-útválasztási módszer és a végpont állapota alapján. A végpont egy, az Azure-on kívül vagy belül üzemeltetett, internetkapcsolattal rendelkező szolgáltatás. A Traffic Manager különböző [forgalom-útválasztási módszereket](traffic-manager-routing-methods.md) és [végpont-monitorozási lehetőségeket](traffic-manager-monitoring.md) biztosít, hogy megfeleljen a különböző alkalmazások igényeinek és az automatikus feladatátvételi modelleknek. A Traffic Manager ellenáll a meghibásodásoknak, beleértve akár egy egész Azure-régió meghibásodását is.

>[!NOTE]
> Az Azure teljeskörűen felügyelt terheléselosztási megoldások együttesét biztosítja a különböző forgatókönyvekre. Ha Transport Layer Security (TLS) protokoll-lezárást („SSL-kiszervezés”) vagy per-HTTP/HTTPS kérelmeket vagy alkalmazásréteg-feldolgozást keres, tekintse át az [Application Gatewayt](../application-gateway/application-gateway-introduction.md) ismertető cikket. Ha regionális terheléselosztást keres, tekintse át [Load Balancer](../load-balancer/load-balancer-overview.md). A végpontok közötti forgatókönyvek esetében előnyt jelenthet ezen megoldások igény szerinti kombinációja.
>
> Az Azure terheléselosztási lehetőségeinek összehasonlítását lásd: [Az Azure terheléselosztási lehetőségeinek áttekintése](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview).

Traffic Manager a következő funkciókat kínálja:

## <a name="increase-application-availability"></a>Alkalmazás rendelkezésre állásának növelése

A Traffic Manager lehetővé teszi a kritikus fontosságú alkalmazások magas rendelkezésre állását azáltal, hogy figyeli a végpontokat, és automatikus feladatátvételt biztosít, ha egy végpont leáll.
    
## <a name="improve-application-performance"></a>Az alkalmazásteljesítmény javítása

Az Azure lehetővé teszi a felhőszolgáltatások vagy webhelyek futtatását a világ különböző pontjain található adatközpontokban. A Traffic Manager javítja az alkalmazások válaszkészségét azáltal, hogy az ügyfél számára a legalacsonyabb hálózati késéssel rendelkező végpontra irányítja a forgalmat.

## <a name="perform-service-maintenance-without-downtime"></a>Állásidő nélküli rendszerkarbantartás végrehajtása

Állásidő nélkül hajthat végre tervezett karbantartási műveleteket az alkalmazásokon. A Traffic Manager a karbantartás folyamatban van, és átirányítja a forgalmat az alternatív végpontokra.

## <a name="combine-hybrid-applications"></a>Hibrid alkalmazások kombinációja

A Traffic Manager támogatja a külső, nem Azure-végpontokat, ami lehetővé teszi a hibrid felhőalapú és helyszíni telepítéssel történő használatot, beleértve a „[felhőbe irányítás](https://azure.microsoft.com/overview/what-is-cloud-bursting/)”, „felhőbe migrálás” és „felhő általi feladatátvétel” eseteket is.

## <a name="distribute-traffic-for-complex-deployments"></a>Forgalom elosztása összetett üzemelő példányokhoz

A [beágyazott Traffic Manager profilok](traffic-manager-nested-profiles.md)használatával több forgalom-útválasztási módszer kombinálható összetett és rugalmas szabályok létrehozásához a nagyobb, összetettebb központi telepítések igényei szerint.

## <a name="pricing"></a>Díjszabás

Díjszabási információkért tekintse meg a [Traffic Manager díjszabását](https://azure.microsoft.com/pricing/details/traffic-manager/) ismertető részt.


## <a name="next-steps"></a>Következő lépések

- Útmutató a [Traffic Manager-profil létrehozásához](traffic-manager-create-profile.md).
- Útmutató a [Traffic Manager működéséhez](traffic-manager-how-it-works.md).
- Tekintse meg a [gyakori kérdések](traffic-manager-FAQs.md) részt a Traffic Managerrel kapcsolatban.




