---
title: Az Azure Kubernetes Service (ak) diagnosztika áttekintése
description: Ismerje meg az Azure Kubernetes Service-beli öndiagnosztizáló fürtöket.
services: container-service
author: yunjchoi
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: yunjchoi
ms.openlocfilehash: e8921152177d3e4534ca9fb48cf209aed6e1b27c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96183362"
---
# <a name="azure-kubernetes-service-diagnostics-preview-overview"></a>Az Azure Kubernetes Service Diagnostics (előzetes verzió) áttekintése

Az Azure Kubernetes Service (ak) fürtökkel kapcsolatos hibák elhárítása fontos része a fürt karbantartásának, különösen akkor, ha a fürt kritikus fontosságú számítási feladatokat futtat. Az AK-diagnosztika egy intelligens, öndiagnosztikai felület, amely segítséget nyújt a fürtben felmerülő problémák azonosításában és megoldásában. Az AK-diagnosztika a felhőben natív, és további konfiguráció vagy számlázási díj nélkül is felhasználható.

Ez a funkció már nyilvános előzetes verzióban érhető el.

## <a name="open-aks-diagnostics"></a>AK-diagnosztika megnyitása

Az AK-diagnosztika elérése:

- A [Azure Portal](https://portal.azure.com)navigáljon a Kubernetes-fürthöz.
- A bal oldali navigációs panelen kattintson a **probléma diagnosztizálása és megoldása** elemre, amely az AK-diagnosztika megnyitására szolgál.
- Válassza ki azt a kategóriát, amely a legjobban leírja a fürt problémáját a Kezdőlap csempén található kulcsszavakat használva, vagy írjon be egy kulcsszót, amely a legjobban leírja a problémát a keresősáv esetében, például a _fürt csomópontjaival kapcsolatos problémákat_.

![Kezdőlap](./media/concepts-diagnostics/aks-diagnostics-homepage.png)

## <a name="view-a-diagnostic-report"></a>Diagnosztikai jelentés megtekintése

Miután rákattint egy kategóriára, megtekintheti a fürthöz tartozó diagnosztikai jelentést. A diagnosztikai jelentés intelligens módon hívja fel, hogy van-e probléma a fürtben állapot ikonokkal. Az egyes témakörök részletezéséhez kattintson a **További információ** lehetőségre a probléma részletes leírásának, a javasolt műveletek, a hasznos dokumentumok hivatkozásainak, a kapcsolódó metrikák és a naplózási adatok megtekintéséhez. A diagnosztikai jelentések intelligens módon jönnek létre a fürt aktuális állapotán alapuló különböző ellenőrzések futtatása után. A diagnosztikai jelentések hasznos eszközt jelenthetnek a fürt problémájának meghatározásához és a probléma megoldásához szükséges következő lépések megkereséséhez.

![Diagnosztikai jelentés](./media/concepts-diagnostics/diagnostic-report.png)

![Bővített diagnosztikai jelentés](./media/concepts-diagnostics/node-issues.png)

## <a name="cluster-insights"></a>Fürtbeli adatfelismerések

A következő diagnosztikai ellenőrzések érhetők el a **Fürtbeli** adatvizsgálatokban.

### <a name="cluster-node-issues"></a>Fürt csomópontjaival kapcsolatos problémák

A fürtcsomópont hibát észlel a csomópontokkal kapcsolatos hibáknál, amelyek miatt a fürt váratlanul működhet.

- Csomópont-készültségi problémák
- Csomóponti hibák
- Nincs elegendő erőforrás
- Hiányzó csomópont IP-konfiguráció
- Csomópont-CNI hibák
- A csomópont nem található
- Csomópont kikapcsolva
- Csomópont-hitelesítési hiba
- Csomópont Kube – elavult proxy

### <a name="create-read-update--delete-operations"></a>& törlési műveletek létrehozása, olvasása, frissítése

A szifilisz-műveletek minden olyan szifilisz-műveletet megkeresnek, amely problémákat okozhat a fürtben.

- A használatban lévő alhálózat törlési műveletének hibája
- Hálózati biztonsági csoport törlési műveletének hibája
- Használatban lévő útválasztási tábla törlési műveletének hibája
- Hivatkozott erőforrás-kiépítési hiba
- A nyilvános IP-cím törlési műveletének hibája
- Telepítési hiba az üzembe helyezési kvóta miatt
- Működési hiba a szervezeti házirend miatt
- Hiányzó előfizetés-regisztráció
- Virtuálisgép-bővítmény kiépítési hibája
- Alhálózat kapacitása
- A kvóta túllépte a hibát.

### <a name="identity-and-security-management"></a>Identitás és biztonsági felügyelet

Az identitás-és biztonsági felügyelet észleli a hitelesítési és engedélyezési hibákat, amelyek megakadályozhatják a fürtön keresztüli kommunikációt.

- Csomópont-engedélyezési hibák
- 401 hiba
- 403-as hibák

## <a name="next-steps"></a>Következő lépések

Gyűjtsön naplókat, amelyek segítenek a fürtökkel kapcsolatos problémák további hibaelhárításában az [AK-periszkóp](https://aka.ms/aksperiscope)használatával.

Olvassa el a 2. napon belüli üzemeltetési útmutató [osztályozási gyakorlatok szakaszát](/azure/architecture/operator-guides/aks/aks-triage-practices) .

Tegye fel kérdéseit vagy visszajelzéseit a [UserVoice](https://feedback.azure.com/forums/914020-azure-kubernetes-service-aks) címen a "[diag]" cím hozzáadásával.