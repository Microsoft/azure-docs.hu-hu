---
title: Az Azure Kubernetes Service (ak) diagnosztika áttekintése
description: Ismerje meg az Azure Kubernetes Service-beli öndiagnosztizáló fürtöket.
services: container-service
author: yunjchoi
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: yunjchoi
ms.openlocfilehash: ee11221e5484a796b8dbbcb10a323288d3e74756
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "107011558"
---
# <a name="azure-kubernetes-service-diagnostics-preview-overview"></a>Az Azure Kubernetes Service Diagnostics (előzetes verzió) áttekintése

Az Azure Kubernetes szolgáltatással (ak) kapcsolatos hibák elhárítása fontos szerepet játszik a fürt fenntartásában, különösen akkor, ha a fürt kritikus fontosságú számítási feladatokat futtat. Az AK-diagnosztika egy intelligens, öndiagnosztikai felület, amely a következőket nyújtja:
* Segítséget nyújt a fürttel kapcsolatos problémák azonosításában és megoldásában. 
* A Felhőbeli natív.
* Nem igényel további konfigurációs vagy számlázási költségeket.

Ez a funkció már nyilvános előzetes verzióban érhető el. 

## <a name="open-aks-diagnostics"></a>AK-diagnosztika megnyitása

Az AK-diagnosztika elérése:

1. A [Azure Portal](https://portal.azure.com)navigáljon a Kubernetes-fürthöz.
1. A bal oldali navigációs panelen kattintson a **probléma diagnosztizálása és megoldása** elemre, amely az AK-diagnosztika megnyitására szolgál.
1. Válasszon olyan kategóriát, amely a legjobban leírja a fürt problémáját, például a _fürt csomópontjaival kapcsolatos problémákat_:
    * A kulcsszavak használata a Kezdőlap csempén.
    * Írjon be egy kulcsszót, amely a legjobban leírja a problémát a keresősávban.

![Kezdőlap](./media/concepts-diagnostics/aks-diagnostics-homepage.png)

## <a name="view-a-diagnostic-report"></a>Diagnosztikai jelentés megtekintése

Miután rákattint egy kategóriára, megtekintheti a fürthöz tartozó diagnosztikai jelentést. A diagnosztikai jelentések intelligens módon meghívhatják a fürtben lévő esetleges hibákat az állapot ikonjaival. Az egyes témakörök részletezéséhez kattintson a **További információ** lehetőségre a részletes leírásának megtekintéséhez:
* Problémák
* Ajánlott műveletek
* Hasznos dokumentumokra mutató hivatkozások
* Kapcsolódó – mérőszámok
* Adatok naplózása 

A diagnosztikai jelentések a fürt aktuális állapotán alapulnak a különböző ellenőrzések futtatása után. A probléma megoldásához hasznos lehet a fürt problémáinak felismerése, és a következő lépések megértése a probléma elhárítása érdekében.

![Diagnosztikai jelentés](./media/concepts-diagnostics/diagnostic-report.png)

![Bővített diagnosztikai jelentés](./media/concepts-diagnostics/node-issues.png)

## <a name="cluster-insights"></a>Fürtbeli adatfelismerések

A következő diagnosztikai ellenőrzések érhetők el a **Fürtbeli** adatvizsgálatokban.

### <a name="cluster-node-issues"></a>Fürt csomópontjaival kapcsolatos problémák

A fürtcsomópont által okozott problémák megkeresik a csomóponttal kapcsolatos hibákat, amelyek miatt a fürt váratlanul viselkedik.

- Csomópont-készültségi problémák
- Csomóponti hibák
- Nincs elegendő erőforrás
- Hiányzó csomópont IP-konfiguráció
- Csomópont-CNI hibák
- A csomópont nem található
- Csomópont kikapcsolva
- Csomópont-hitelesítési hiba
- Csomópont Kube – elavult proxy

### <a name="create-read-update--delete-crud-operations"></a>& törlési (szifilisz) műveletek létrehozása, olvasása, frissítése

A szifilisz-műveletek minden olyan szifilisz-műveletet ellenőriznek, amely problémákat okoz a fürtben.

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

Az identitás-és biztonsági felügyelet észleli a fürtre irányuló kommunikációt megakadályozó hitelesítési és engedélyezési hibákat.

- Csomópont-engedélyezési hibák
- 401 hiba
- 403-as hibák

## <a name="next-steps"></a>Következő lépések

* Gyűjtsön naplókat, amelyek segítenek a fürtökkel kapcsolatos problémák további hibaelhárításában az [AK-periszkóp](https://aka.ms/aksperiscope)használatával.

* Olvassa el a 2. napon belüli üzemeltetési útmutató [osztályozási gyakorlatok szakaszát](/azure/architecture/operator-guides/aks/aks-triage-practices) .

* Tegye fel kérdéseit vagy visszajelzéseit a [UserVoice](https://feedback.azure.com/forums/914020-azure-kubernetes-service-aks) címen a "[diag]" cím hozzáadásával.