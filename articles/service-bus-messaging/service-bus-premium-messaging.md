---
title: Azure Service Bus standard és prémium szintű csomagok
description: Ez a cikk a szolgáltatás standard és prémium Azure Service Bus. Összehasonlítja ezeket a szinteket, és technikai különbségeket mutat be.
ms.topic: conceptual
ms.date: 02/17/2021
ms.openlocfilehash: b7117da6a959181704dd136c6d5be5ab62edef55
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389485"
---
# <a name="service-bus-premium-and-standard-messaging-tiers"></a>A Service Bus prémium és standard szintű üzenetkezelés szintjei

A Service Bus-üzenetkezelés, amely a várólistákhoz és témakörökhöz hasonló entitásokat is tartalmaz, a vállalati üzenetkezelési képességeket ötvözi a gazdag közzétételi/előfizetési szemantikákkal a felhőbeli skálázással. A Service Bus-üzenetkezelés számos kifinomult felhőalapú megoldás kommunikációs vázaként szolgál.

A Service Bus-üzenetkezelés *prémium* szintje a méretezéssel, teljesítménnyel és a legfontosabb alkalmazásokkal kapcsolatos általános ügyfélkérelmeket kezeli. A prémium szintű csomag éles forgatókönyvekhez ajánlott. Noha a szolgáltatáskészletek csaknem azonosak, a Service Bus-üzenetkezelés két szintje különböző felhasználói esetekhez lett tervezve.

A következő táblázat néhány fontos eltérést emel ki.

| Prémium | Standard |
| --- | --- |
| Nagy átviteli sebesség |Változó teljesítmény |
| Kiszámítható teljesítmény |Változó késés |
| Rögzített díjszabás |Használatalapú változó díjszabás |
| Lehetőség a munkaterhelés vertikális fel- és leskálázására |N/A |
| Legfeljebb 1 MB méretű üzenet. Ez a korlát a jövőben megemelkedhet. A szolgáltatás legújabb fontos frissítéseit lásd: [Üzenetkezelés az Azure blogján.](https://techcommunity.microsoft.com/t5/messaging-on-azure/bg-p/MessagingonAzureBlog) |Legfeljebb 256 KB méretű üzenet |

A **Service Bus prémium szintű üzenetkezelés** erőforrás-elkülönítést biztosít a CPU és a memória szintjén, így az ügyfél minden számítási feladata elkülönítve fut. Ennek az erőforrás-tárolónak a neve *üzenetkezelési egység*. Legalább egy üzenetkezelési egység van lefoglalva minden prémium névtérhez. Minden prémium szintű névtérhez 1, 2, 4, 8 vagy 16 Service Bus vásárolhat. Egyetlen számítási feladat vagy entitás több üzenetkezelési egységre is kihathat, és az üzenetkezelési egységek száma is módosítható. Az eredmény a Service Bus-alapú megoldás kiszámítható és ismételhető teljesítménye.

Nem csak kiszámíthatóbb és nagyobb rendelkezésre állású a teljesítmény, de gyorsabb is. Prémium szintű üzenetkezelés esetén a csúcsteljesítmény jóval gyorsabb, mint a standard szinten.

> [!NOTE]
> A prémium szintű üzenetkezelés kötegméretkorlátja 1 MB.

## <a name="premium-messaging-technical-differences"></a>Prémium szintű üzenetkezelés – műszaki eltérések

Az alábbi szakaszokban a prémium és a standard szintű üzenetkezelési szintek közötti egyes különbségek olvashatók.

### <a name="partitioned-queues-and-topics"></a>Particionált üzenetsorok és témakörök

A particionált üzenetsorok és témakörök nem támogatottak a prémium szintű üzenetkezelésben. A particionálásra vonatkozó további információkat a [Partitioned queues and topics](service-bus-partitioning.md) (Particionált üzenetsorok és témakörök) című rész tartalmazza.

### <a name="express-entities"></a>Expressz entitások

Mivel a prémium szintű üzenetkezelés izolált futásidős környezetben fut, az expressz entitások nem támogatottak a prémium névterek esetében. Az expressz entitások ideiglenesen a memóriában tölnek üzenetet, mielőtt állandó tárolóba írják azt. Ha a kódot a Standard szintű üzenetkezelés alatt futtatja, és a Prémium szintre szeretné portolást használni, győződjön meg arról, hogy az expressz entitás funkció le van tiltva.

## <a name="premium-messaging-resource-usage"></a>Prémium szintű üzenetkezelés erőforrás-használata
Az entitások minden művelete általában cpu- és memóriahasználatot okozhat. Néhány ilyen művelet: 

- Kezelési műveletek, például CRUD(Létrehozás, Lekérés, Frissítés és Törlés) műveletek az üzenetsorok, témakörök és előfizetések esetén.
- Futásidejű műveletek (üzenetek küldése és fogadása)
- Figyelési műveletek és riasztások

A processzor- és memóriahasználat további ára azonban nem. A prémium szintű üzenetkezelés esetében az üzenetegységnek egyetlen ára van.

A processzor- és memóriahasználat nyomon követése és megjelenítése a következő okok miatt lehetséges: 

- Átláthatóság biztosítása a rendszer belső rendszerében
- A megvásárolt erőforrások kapacitásának ása.
- Kapacitástervezés, amely segít a fel- és leskálás eldöntésében.

## <a name="messaging-unit---how-many-are-needed"></a>Üzenetkezelési egység – Hányra van szükség?

A prémium Azure Service Bus kiépítésekor meg kell adni a lefoglalt üzenetkezelési egységek számát. Ezek az üzenetkezelési egységek dedikált erőforrások, amelyek a névtérhez vannak lefoglalva.

A prémium szintű névtérhez lefoglalt üzenetkezelési egységek Service Bus  a számítási feladatok változásának (növekedésének vagy csökkenésének) megfelelően dinamikusan módosíthatók.

Az architektúra üzenetkezelési egységeinek számának meghatározásakor több tényezőt is figyelembe kell venni:

- Kezdje a ***névtérhez*** lefoglalt 1 vagy 2 üzenetkezelési egységsel.
- Tanulmányozza a CPU-használati metrikákat [a](service-bus-metrics-azure-monitor.md#resource-usage-metrics) névtér erőforrás-használati metrikákban.
    - Ha a CPU-használat ***20%** alatt van ** _, akkor lehetséges, hogy leskálát * skálázhat le * a névtérhez lefoglalt üzenetkezelési egységek száma.
    - Ha a CPU-használat ***70%** felett van _, az alkalmazás számára előnyös a *___* felméretezés * a névtérhez lefoglalt üzenetkezelési egységek száma.

Az üzenetkezelési egységek automatikus frissítésével Service Bus (az üzenetkezelési egységek számának növelésével vagy csökkentésével) konfigurálható egy [névtér.](automate-update-messaging-units.md)

> [!NOTE]
> **A névtérhez** lefoglalt erőforrások skálázása lehet preempív vagy reaktív.
>
>  * **Preemptive**: Ha további számítási feladatok várhatók (a szezonalitás vagy a trendek miatt), akkor további üzenetkezelési egységeket foglalhat le a névtérhez, mielőtt a számítási feladatok eltelnek.
>
>  * **Reaktív:** Ha további számítási feladatokat azonosít az erőforrás-használati metrikák tanulmányozása, akkor további erőforrások foglalhatóak le a névtérhez a növekvő terhelés beépítése érdekében.
>
> A számlázási mérők a Service Bus óránként vannak meg. Felméretezés esetén csak a felhasznált órákért kell fizetnie a további erőforrásokért.
>

## <a name="get-started-with-premium-messaging"></a>Ismerkedés a prémium szintű üzenetkezeléssel

A prémium szintű üzenetkezelés használatba vétele egyszerű, a folyamat pedig a standard szintű üzenetkezeléséhez hasonló. Első lépésként [hozzon létre egy névteret](service-bus-create-namespace-portal.md) az [Azure Portalon](https://portal.azure.com). Győződjön meg arról, hogy a **Tarifacsomag** alatt a **Prémium** tarifacsomagot választotta ki. Az egyes tarifacsomagokkal kapcsolatos információk megtekintéséhez kattintson a **Díjszabási részletek megtekintése** elemre.

![create-premium-namespace][create-premium-namespace]

[Az Azure Resource Manager-sablonok használatával is létrehozhat prémium szintű névtereket](https://azure.microsoft.com/resources/templates/101-servicebus-pn-ar/).

## <a name="next-steps"></a>Következő lépések

A Service Bus üzenetküldési funkcióival kapcsolatos további információkért tekintse meg a következőket:

- [Üzenetkezelési egységek automatikus frissítése.](automate-update-messaging-units.md)
- [A Azure Service Bus üzenetkezelés bevezetése (blogbejegyzés)](https://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
- [Az Azure Service Bus prémium szintű üzenetkezelés bemutatása (Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)

<!--Image references-->

[create-premium-namespace]: ./media/service-bus-premium-messaging/select-premium-tier.png
