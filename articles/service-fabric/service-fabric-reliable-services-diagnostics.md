---
title: Azure Service Fabric állapot-nyilvántartó Reliable Services diagnosztika
description: Az Azure állapot-nyilvántartó Reliable Services diagnosztikai funkciója Service Fabric
ms.topic: conceptual
ms.date: 8/24/2018
ms.openlocfilehash: 774a771d0c9701076a5d6c070963bf6224a571dd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98789330"
---
# <a name="diagnostic-functionality-for-stateful-reliable-services"></a>A Stateful Reliable Services diagnosztikai funkciói
Az Azure Service Fabric állapot-nyilvántartó Reliable Services StatefulServiceBase osztály a szolgáltatás hibakereséséhez használható [EventSource](/dotnet/api/system.diagnostics.tracing.eventsource) -eseményeket bocsát ki, betekintést nyújt a futtatókörnyezet működéséhez, és segít a hibaelhárításban.

## <a name="eventsource-events"></a>EventSource események
Az állapot-nyilvántartó Reliable Services StatefulServiceBase osztály EventSource neve "Microsoft-ServiceFabric-Services". Az eseményforrás eseményei a [diagnosztikai események](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) ablakban jelennek meg, amikor a szolgáltatás [hibakeresése a Visual Studióban](service-fabric-debugging-your-application.md)folyamatban van.

Példák olyan eszközökre és technológiákra, amelyek a EventSource-események összegyűjtését és/vagy megtekintését segítik a [perfview eszköz](https://www.microsoft.com/download/details.aspx?id=28567), a [Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md)és a [Microsoft TraceEvent könyvtárában](https://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent).

## <a name="events"></a>Események
| Esemény neve | Eseményazonosító | Level | Esemény leírása |
| --- | --- | --- | --- |
| StatefulRunAsyncInvocation |1 |Tájékoztató |A szolgáltatás RunAsync feladat elindításakor lett kibocsátva |
| StatefulRunAsyncCancellation |2 |Tájékoztató |A szolgáltatás RunAsync feladatának megszakításakor lett kibocsátva |
| StatefulRunAsyncCompletion |3 |Tájékoztató |A szolgáltatás RunAsync feladat befejezése után lett kibocsátva |
| StatefulRunAsyncSlowCancellation |4 |Figyelmeztetés |Kibocsátva, ha a szolgáltatás RunAsync feladata túl sokáig tart a lemondás befejezéséhez |
| StatefulRunAsyncFailure |5 |Hiba |Kibocsátva, ha a szolgáltatás RunAsync feladata kivételt vet fel |

## <a name="interpret-events"></a>Események értelmezése
A StatefulRunAsyncInvocation, a StatefulRunAsyncCompletion és a StatefulRunAsyncCancellation események hasznosak a szolgáltatás írója számára a szolgáltatás életciklusának megismeréséhez, valamint a szolgáltatás elindulásának, megszakításának vagy befejezésének időzítéséhez. Ezek az információk hasznosak lehetnek a szolgáltatással kapcsolatos problémák hibakeresése vagy a szolgáltatás életciklusának megismerése során.

A szolgáltatás-íróknak Kiemelt figyelmet kell fordítaniuk az események StatefulRunAsyncSlowCancellation és StatefulRunAsyncFailure, mivel a szolgáltatással kapcsolatos problémákat jeleznek.

A StatefulRunAsyncFailure akkor kerül kibocsátásra, ha a Service RunAsync () feladat kivételt jelez. Az eldobott kivételek általában hibát vagy hibát jeleznek a szolgáltatásban. Emellett a kivétel hatására a szolgáltatás meghibásodik, így azt egy másik csomópontra helyezi át. Ez a művelet költséges lehet, és késleltetheti a bejövő kérelmeket a szolgáltatás áthelyezésekor. A szolgáltatás-íróknak meg kell határozniuk a kivétel okát, és ha lehetséges, enyhíteni kell.

A StatefulRunAsyncSlowCancellation akkor bocsátja ki, ha a RunAsync feladat megszakítási kérelme négy másodpercnél hosszabb időt vesz igénybe. Ha egy szolgáltatás túl sokáig tart a lemondás befejezéséhez, azzal hatással van arra, hogy a szolgáltatás gyorsan újrainduljon egy másik csomóponton. Ez a forgatókönyv hatással lehet a szolgáltatás általános rendelkezésre állására.

## <a name="performance-counters"></a>Teljesítményszámlálók
A Reliable Services futtatókörnyezet a következő teljesítményszámláló-kategóriákat definiálja:

| Kategória | Leírás |
| --- | --- |
| Tranzakciós replikátor Service Fabric |Az Azure Service Fabric tranzakciós Replikátorra jellemző számlálók |
| Service Fabric TStore |Az Azure Service Fabric TStore kapcsolódó számlálók |

A [megbízható állapot-kezelő](./service-fabric-reliable-services-introduction.md) a Service Fabric tranzakciós replikálása használatával replikálja a tranzakciókat egy adott [replikán](service-fabric-concepts-replica-lifecycle.md)belül.

A Service Fabric TStore a [megbízható gyűjteményekben](./service-fabric-reliable-services-introduction.md) a kulcs-érték párok tárolására és lekérésére használt összetevő.

A Windows [Teljesítményfigyelő](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc749249(v=ws.11)) alkalmazás, amely alapértelmezés szerint elérhető a Windows operációs rendszerben, a teljesítményszámláló-adatok gyűjtésére és megtekintésére használható. [Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md) egy másik lehetőség a teljesítményszámláló-adatok gyűjtésére és az Azure-táblákba való feltöltésére.

### <a name="performance-counter-instance-names"></a>Teljesítményszámláló-példányok nevei
A nagy mennyiségű megbízható szolgáltatást vagy megbízható szolgáltatási partíciót tartalmazó fürtök nagy számú tranzakciós replikáló teljesítményszámláló-példánnyal rendelkeznek. Ez a TStore teljesítményszámlálók esetében is igaz, de a megbízható szótárak és a használt megbízható várólisták száma is megszorozva. A teljesítményszámláló-példányok nevei segítenek a TStore esetében az adott [partíció](service-fabric-concepts-partitioning.md), a szolgáltatás replikájának és az állami szolgáltatónak az azonosításában, hogy a teljesítményszámláló-példány társítva legyen.

#### <a name="service-fabric-transactional-replicator-category"></a>Service Fabric tranzakciós replikátor kategóriája
A kategória esetében `Service Fabric Transactional Replicator` a számláló példányainak neve a következő formátumú:

`ServiceFabricPartitionId:ServiceFabricReplicaId`

A *ServiceFabricPartitionId* annak a Service Fabric partíció-azonosítónak a karakterlánc-ábrázolása, amelyhez a teljesítményszámláló-példány társítva van. A partíció azonosítója egy GUID, és a karakterlánc-ábrázolás [`Guid.ToString`](/dotnet/api/system.guid.tostring#System_Guid_ToString_System_String_) a "D" formátum megadásával jön létre.

A *ServiceFabricReplicaId* egy megbízható szolgáltatás adott replikájának azonosítója. A rendszer a teljesítményszámláló-példány neve tartalmazza a replika AZONOSÍTÓját, hogy biztosítsa annak egyediségét, és elkerülje az azonos partíció által generált teljesítményszámláló-példányokkal való ütközést. A replikákkal és a megbízható szolgáltatásokban lévő szerepével kapcsolatos további részletek [itt](service-fabric-concepts-replica-lifecycle.md)találhatók.

A következő számláló-példány neve általában a kategóriához tartozó számlálóhoz tartozik `Service Fabric Transactional Replicator` :

`00d0126d-3e36-4d68-98da-cc4f7195d85e:131652217797162571`

Az előző példában a `00d0126d-3e36-4d68-98da-cc4f7195d85e` Service Fabric partíció azonosítójának karakterlánc-ábrázolása, és `131652217797162571` a replika azonosítója.

#### <a name="service-fabric-tstore-category"></a>Service Fabric TStore kategóriája
A kategória esetében `Service Fabric TStore` a számláló példányainak neve a következő formátumú:

`ServiceFabricPartitionId:ServiceFabricReplicaId:StateProviderId_PerformanceCounterInstanceDifferentiator_StateProviderName`

A *ServiceFabricPartitionId* annak a Service Fabric partíció-azonosítónak a karakterlánc-ábrázolása, amelyhez a teljesítményszámláló-példány társítva van. A partíció azonosítója egy GUID, és a karakterlánc-ábrázolás [`Guid.ToString`](/dotnet/api/system.guid.tostring#System_Guid_ToString_System_String_) a "D" formátum megadásával jön létre.

A *ServiceFabricReplicaId* egy megbízható szolgáltatás adott replikájának azonosítója. A rendszer a teljesítményszámláló-példány neve tartalmazza a replika AZONOSÍTÓját, hogy biztosítsa annak egyediségét, és elkerülje az azonos partíció által generált teljesítményszámláló-példányokkal való ütközést. A replikákkal és a megbízható szolgáltatásokban lévő szerepével kapcsolatos további részletek [itt](service-fabric-concepts-replica-lifecycle.md)találhatók.

A *állapotszolgáltató azonosítója* egy megbízható szolgáltatáson belüli állami szolgáltatóhoz tartozó azonosító. A teljesítményszámláló-példány neve tartalmazza az állapot-szolgáltató AZONOSÍTÓját, hogy megkülönböztesse a TStore egy másikból.

A *PerformanceCounterInstanceDifferentiator* egy, az állami szolgáltatón belüli teljesítményszámláló-példányhoz társított megkülönböztető azonosító. Ezt a megkülönböztető nevet a teljesítményszámláló példányának neve tartalmazza, hogy biztosítsa annak egyediségét, és elkerülje az azonos állami szolgáltató által generált teljesítményszámláló-példányok ütközését.

A *StateProviderName* egy megbízható szolgáltatáson belüli állami szolgáltatóhoz tartozó név. Az állapot-szolgáltató neve tartalmazza a teljesítményszámláló-példány nevét, amellyel a felhasználók könnyen azonosíthatják, hogy milyen állapotot biztosít.

A következő számláló-példány neve általában a kategóriához tartozó számlálóhoz tartozik `Service Fabric TStore` :

`00d0126d-3e36-4d68-98da-cc4f7195d85e:131652217797162571:142652217797162571_1337_urn:MyReliableDictionary/dataStore`

Az előző példában a `00d0126d-3e36-4d68-98da-cc4f7195d85e` Service Fabric partíció azonosítójának karakterlánc-ábrázolása, `131652217797162571` a replika azonosítója, az `142652217797162571` állami szolgáltató azonosítója, és `1337` a teljesítményszámláló-példány megkülönböztethető. `urn:MyReliableDictionary/dataStore` annak az állami szolgáltatónak a neve, amely a nevű gyűjteményhez tartozó adattárolást tárolja `urn:MyReliableDictionary` .

### <a name="transactional-replicator-performance-counters"></a>Tranzakciós replikáló teljesítményszámlálók

A Reliable Services futtatókörnyezet a következő eseményeket bocsátja ki a `Service Fabric Transactional Replicator` kategóriába

 Számláló neve | Leírás |
| --- | --- |
| Tranzakció művelet/s megkezdése | A másodpercenként létrehozott új írási tranzakciók száma|
| Tranzakció művelet/mp | A megbízható gyűjteményeken végrehajtott hozzáadási/frissítési/törlési műveletek száma másodpercenként.|
| Napló kiürítési sebessége (bájt/s) | A tranzakciós replikátor által a lemezre kiürített bájtok száma másodpercenként |
| Szabályozott művelet/s | A tranzakciós replikátor által a szabályozás miatt másodpercenként visszautasított műveletek száma. |
| Átlagos tranzakció: MS/commit | Átlagos végrehajtási késleltetés másodpercenként ezredmásodpercben |
| Átlagos ürítési késés (MS) | A tranzakciós replikátor által kezdeményezett lemezes ürítési műveletek átlagos időtartama ezredmásodpercben |

### <a name="tstore-performance-counters"></a>TStore teljesítményszámlálók

A Reliable Services futtatókörnyezet a következő eseményeket bocsátja ki a `Service Fabric TStore` kategóriába

 Számláló neve | Leírás |
| --- | --- |
| Elemek száma | Az áruházban lévő elemek száma.|
| Lemezméret | Az áruházhoz tartozó ellenőrzőpont-fájlok teljes mérete (bájtban).|
| Ellenőrzőpont-fájl írási sebessége (bájt/s) | A legutóbbi ellenőrzőpont-fájl másodpercenként írt bájtjainak száma.|
| Lemez átviteli sebességének másolása (bájt/s) | Az elolvasott lemezes bájtok száma (az elsődleges replikán) vagy a másodpercenként írt (másodlagos replikán).|

## <a name="next-steps"></a>Következő lépések
[EventSource-szolgáltatók a Perfview eszköz-ben](/archive/blogs/vancem/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource)
