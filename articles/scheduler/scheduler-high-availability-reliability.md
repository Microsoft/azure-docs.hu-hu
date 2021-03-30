---
title: Magas rendelkezésre állás és megbízhatóság
description: Tudnivalók a magas rendelkezésre állásról és a megbízhatóságról az Azure Schedulerben
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: article
ms.date: 08/16/2016
ms.openlocfilehash: 98a6672af7e74fdd0732f3ba03264d2f674eb44f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92368145"
---
# <a name="high-availability-and-reliability-for-azure-scheduler"></a>Magas rendelkezésre állás és megbízhatóság az Azure Schedulerben

> [!IMPORTANT]
> [Azure Logic apps](../logic-apps/logic-apps-overview.md) az Azure Scheduler cseréje [folyamatban](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date)van. Ha továbbra is szeretne dolgozni a Feladatütemezőben beállított feladatokkal, akkor a lehető leghamarabb [telepítse át Azure Logic apps](../scheduler/migrate-from-scheduler-to-logic-apps.md) . 
>
> Az ütemező már nem érhető el a Azure Portalban, de a [REST API](/rest/api/scheduler) és az [Azure Scheduler PowerShell-parancsmagjai](scheduler-powershell-reference.md) jelenleg is elérhetők maradnak, így a feladatok és a feladatok gyűjteményei kezelhetők.

Az Azure Scheduler [magas rendelkezésre állást](/azure/architecture/framework/#resiliency) és megbízhatóságot biztosít a feladatokhoz. További információ: [a Feladatütemező SLA](https://azure.microsoft.com/support/legal/sla/scheduler)-ja.

## <a name="high-availability"></a>Magas rendelkezésre állás

Az Azure Scheduler [kiválóan elérhető], és a Geo-redundáns szolgáltatások üzembe helyezését és a földrajzilag regionális feladatok replikálását is alkalmazza.

### <a name="geo-redundant-service-deployment"></a>Geo-redundáns szolgáltatás központi telepítése

Az Azure Scheduler csaknem minden, [Az Azure által támogatott földrajzi régióban](https://azure.microsoft.com/global-infrastructure/regions/#services)elérhető. Ha tehát egy üzemeltetett régióban lévő Azure-adatközpont elérhetetlenné válik, továbbra is használhatja az Azure Schedulert, mert a szolgáltatás feladatátvételi képességei elérhetővé teszik a Scheduler szolgáltatást egy másik adatközpontból.

### <a name="geo-regional-job-replication"></a>Földrajzilag regionális feladatok replikációja

Az Azure Scheduler saját feladatait az Azure-régiók között replikálja a rendszer. Így ha az egyik régió leáll, az Azure Scheduler feladatátvételt hajt végre, és gondoskodik arról, hogy a feladata a párosított földrajzi régió egy másik adatközpontjában fusson.

Ha például az USA déli középső régiójában hoz létre feladatot, az Azure Scheduler automatikusan replikálja ezt a feladatot az USA északi középső régiójában. Ha hiba történik az USA déli középső régiójában, az Azure Scheduler az USA északi középső régiójában futtatja a feladatot. 

![Földrajzilag regionális feladatok replikációja](./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image1.png)

Az Azure Scheduler azt is biztosítja, hogy az adatai ugyanabban a régióban maradnak, mint az Azure-ban. Így nem kell megdupláznia a feladatokat, amikor csak magas rendelkezésre állást szeretne. Az Azure Scheduler automatikusan magas rendelkezésre állást biztosít a feladatokhoz.

## <a name="reliability"></a>Megbízhatóság

Az Azure Scheduler garantálja a saját magas rendelkezésre állását, de a felhasználó által létrehozott feladatokhoz más megközelítést is igénybe vesz. Tegyük fel például, hogy a feladata egy nem elérhető HTTP-végpontot hív meg. Az Azure Scheduler továbbra is megkísérli a feladatok sikeres futtatását azáltal, hogy alternatív módszereket biztosít a hibák kezelésére: 

* Újrapróbálkozási szabályzatok beállítása.
* Alternatív végpontok beállítása.

<a name="retry-policies"></a>

### <a name="retry-policies"></a>Újrapróbálkozási szabályzatok

Az Azure Scheduler lehetővé teszi az újrapróbálkozási szabályzatok beállítását. Ha a feladatok sikertelenek, akkor alapértelmezés szerint a Scheduler még négy alkalommal újra próbálkozik a feladattal, 30 másodperces időközönként. Ezt az újrapróbálkozási szabályzatot agresszíven is megteheti, például 10-szer, 30 másodperces időközönként, vagy kevésbé agresszív, például naponta kétszer.

Tegyük fel például, hogy egy heti feladatot hoz létre, amely egy HTTP-végpontot hív meg. Ha a HTTP-végpont néhány óráig elérhetetlenné válik a feladat futása közben, akkor előfordulhat, hogy nem szeretné, hogy a feladat újra fusson egy másik héten, ami azért történik, mert az alapértelmezett újrapróbálkozási házirend ebben az esetben nem fog működni. Ezért előfordulhat, hogy módosítani szeretné a normál újrapróbálkozási házirendet, hogy az újrapróbálkozások megtörténjenek, például 3 óránként, nem pedig 30 másodpercenként. 

Az újrapróbálkozási szabályzatok beállításával kapcsolatos további információkért lásd: [retryPolicy](scheduler-concepts-terms.md#retrypolicy).

### <a name="alternate-endpoints"></a>Másodlagos végpontok

Ha az Azure Scheduler-feladat olyan végpontot hív meg, amely nem érhető el, még az újrapróbálkozási szabályzat követése után sem, a Scheduler visszatérhet egy másik végpontra, amely képes kezelni az ilyen hibákat. Így ha beállítja ezt a végpontot, a Scheduler meghívja ezt a végpontot, amely a hibák bekövetkezésekor a saját feladatok számára is kiemelkedően elérhetővé válik.

Ez az ábra például azt mutatja be, hogyan követi az ütemező az újrapróbálkozási szabályzatot a webszolgáltatás New Yorkban való meghívásakor. Ha az újrapróbálkozások sikertelenek, a Scheduler egy másik végpontot keres. Ha a végpont létezik, a Scheduler elkezdi elküldeni a kérelmeket a másodlagos végpontnak. Ugyanez az újrapróbálkozási szabályzat az eredeti műveletre és a másodlagos műveletre is vonatkozik.

![A Scheduler viselkedése az újrapróbálkozási szabályzattal és a másodlagos végponttal](./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image2.png)

Az alternatív művelet Művelettípus eltérhet az eredeti művelettől. Ha például az eredeti művelet HTTP-végpontot hív meg, akkor a másodlagos művelet egy tárolási üzenetsor, Service Bus üzenetsor vagy Service Bus témakör művelet használatával naplózhatja a hibákat.

A másodlagos végpontok beállításával kapcsolatos további információkért lásd: [errorAction](scheduler-concepts-terms.md#error-action).

## <a name="next-steps"></a>Következő lépések

* [Alapfogalmak, terminológia és entitáshierarchia](scheduler-concepts-terms.md)
* [Az Azure Scheduler REST API-jának leírása](/rest/api/scheduler)
* [Az Azure Scheduler PowerShell-parancsmagjainak leírása](scheduler-powershell-reference.md)
* [Korlátok, kvóták, alapértékek és hibakódok](scheduler-limits-defaults-errors.md)