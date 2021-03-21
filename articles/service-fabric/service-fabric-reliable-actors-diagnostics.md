---
title: Actors diagnosztika és monitorozás
description: Ez a cikk a Service Fabric Reliable Actors Runtime diagnosztikai és teljesítmény-figyelési funkcióit ismerteti, beleértve az általa kibocsátott eseményeket és teljesítményszámlálókat is.
author: abhishekram
ms.topic: conceptual
ms.date: 10/26/2017
ms.author: abhisram
ms.openlocfilehash: ebaedb5369f3b39372262bfde526706e8d069418
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98789616"
---
# <a name="diagnostics-and-performance-monitoring-for-reliable-actors"></a>A Reliable Actors diagnosztizálása és teljesítmény-figyelése
A Reliable Actors futtatókörnyezet [EventSource](/dotnet/api/system.diagnostics.tracing.eventsource) [eseményeket és teljesítményszámlálókat](/dotnet/api/system.diagnostics.performancecounter)bocsát ki. Ezek betekintést nyújtanak a futtatókörnyezet működésével, és segítenek a hibaelhárításban és a teljesítmény-figyelésben.

## <a name="eventsource-events"></a>EventSource események
A Reliable Actors Runtime EventSource-szolgáltatójának neve "Microsoft-ServiceFabric-Actors". Az eseményforrás eseményei a [diagnosztikai események](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) ablakban jelennek meg, amikor a Actor alkalmazást a [Visual Studióban](service-fabric-debugging-your-application.md)végzik.

Példák olyan eszközökre és technológiákra, amelyek a EventSource-események összegyűjtését és/vagy megtekintését segítik [perfview eszköz](https://www.microsoft.com/download/details.aspx?id=28567), [Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md), [szemantikai naplózás](/previous-versions/msp-n-p/dn774980(v=pandp.10))és a [Microsoft TraceEvent könyvtárában](https://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent).

### <a name="keywords"></a>Kulcsszavak
A Reliable Actors EventSource tartozó összes esemény egy vagy több kulcsszóhoz van társítva. Ez lehetővé teszi a gyűjtött események szűrését. A következő kulcsszavas bitek definiálva vannak.

| Bites | Description |
| --- | --- |
| 0x1 |Olyan fontos események összessége, amelyek összefoglalják a Fabric Actors futtatókörnyezet működését. |
| 0x2 |A színészi metódusok hívásait leíró események halmaza. További információ: a [szereplőkkel kapcsolatos bevezető témakör](service-fabric-reliable-actors-introduction.md). |
| 0x4 |A Actor állapotával kapcsolatos események halmaza. További információkért lásd a [színészi állapot kezelésével](service-fabric-reliable-actors-state-management.md)foglalkozó témakört. |
| 0x8 |A színészben a turn-based egyidejűséghez kapcsolódó események halmaza. További információ: [egyidejűségi](service-fabric-reliable-actors-introduction.md#concurrency)témakör. |

## <a name="performance-counters"></a>Teljesítményszámlálók
A Reliable Actors futtatókörnyezet a következő teljesítményszámláló-kategóriákat határozza meg.

| Kategória | Leírás |
| --- | --- |
| Service Fabric színész |Az Azure Service Fabric Actors-ra jellemző számlálók, például a színész állapotának mentéséhez szükséges idő |
| Service Fabric Actors metódus |Service Fabric szereplők által megvalósított metódusokra jellemző számlálók, például hogy milyen gyakran hívja meg a Actor metódust |

A fenti kategóriák mindegyike egy vagy több számlálóval rendelkezik.

A Windows [Teljesítményfigyelő](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc749249(v=ws.11)) alkalmazás, amely alapértelmezés szerint elérhető a Windows operációs rendszerben, a teljesítményszámláló-adatok gyűjtésére és megtekintésére használható. [Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md) egy másik lehetőség a teljesítményszámláló-adatok gyűjtésére és az Azure-táblákba való feltöltésére.

### <a name="performance-counter-instance-names"></a>Teljesítményszámláló-példányok nevei
A nagy számú Actor Services-vagy színész-szolgáltatási partícióval rendelkező fürtök nagy számú Actors teljesítményszámláló-példánnyal rendelkeznek. A teljesítményszámláló-példányok nevei segíthetnek a teljesítményszámláló-példányhoz társított adott [partíció](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors) és Actor metódus (ha alkalmazható) azonosításában.

#### <a name="service-fabric-actor-category"></a>Service Fabric Actor kategória
A kategória esetében `Service Fabric Actor` a számláló példányainak neve a következő formátumú:

`ServiceFabricPartitionID_ActorsRuntimeInternalID`

A *ServiceFabricPartitionID* annak a Service Fabric partíció-azonosítónak a karakterlánc-ábrázolása, amelyhez a teljesítményszámláló-példány társítva van. A partíció-azonosító egy GUID, és a karakterlánc-ábrázolás a [`Guid.ToString`](/dotnet/api/system.guid.tostring#System_Guid_ToString_System_String_) "D" formátumú metódussal jön létre.

A *ActorRuntimeInternalID* egy 64 bites egész szám karakterlánc-ábrázolása, amelyet a Fabric Actors futtatókörnyezet generál belső használatra. Ez a teljesítményszámláló-példány neve része, amely biztosítja az egyediségét, és a teljesítményszámláló-példányok más neveivel való ütközés elkerülését. A felhasználók nem próbálják értelmezni a teljesítményszámláló-példány nevének ezen részét.

A következőkben egy példa látható egy számláló példányának nevére a kategóriához tartozó számlálóhoz `Service Fabric Actor` :

`2740af29-78aa-44bc-a20b-7e60fb783264_635650083799324046`

A fenti példában a `2740af29-78aa-44bc-a20b-7e60fb783264` Service Fabric partíció-azonosító karakterlánc-ábrázolása, `635650083799324046` amely a futtatókörnyezet belső használatára generált 64 bites azonosító.

#### <a name="service-fabric-actor-method-category"></a>Service Fabric Actors metódus kategóriája
A kategória esetében `Service Fabric Actor Method` a számláló példányainak neve a következő formátumú:

`MethodName_ActorsRuntimeMethodId_ServiceFabricPartitionID_ActorsRuntimeInternalID`

A *MethodName* annak a Actor metódusnak a neve, amelyhez a teljesítményszámláló-példány társítva van. A metódus nevének formátuma a Fabric Actors futtatókörnyezetben lévő néhány logika alapján van meghatározva, amely a név olvashatóságát a Windows rendszeren a teljesítményszámláló-példányok neveinek maximális hosszára vonatkozó korlátozásokkal kiegyenlíti.

A *ActorsRuntimeMethodId* egy 32 bites egész szám karakterlánc-ábrázolása, amelyet a Fabric Actors futtatókörnyezet generál belső használatra. Ez a teljesítményszámláló-példány neve része, amely biztosítja az egyediségét, és a teljesítményszámláló-példányok más neveivel való ütközés elkerülését. A felhasználók nem próbálják értelmezni a teljesítményszámláló-példány nevének ezen részét.

A *ServiceFabricPartitionID* annak a Service Fabric partíció-azonosítónak a karakterlánc-ábrázolása, amelyhez a teljesítményszámláló-példány társítva van. A partíció-azonosító egy GUID, és a karakterlánc-ábrázolás a [`Guid.ToString`](/dotnet/api/system.guid.tostring#System_Guid_ToString_System_String_) "D" formátumú metódussal jön létre.

A *ActorRuntimeInternalID* egy 64 bites egész szám karakterlánc-ábrázolása, amelyet a Fabric Actors futtatókörnyezet generál belső használatra. Ez a teljesítményszámláló-példány neve része, amely biztosítja az egyediségét, és a teljesítményszámláló-példányok más neveivel való ütközés elkerülését. A felhasználók nem próbálják értelmezni a teljesítményszámláló-példány nevének ezen részét.

A következőkben egy példa látható egy számláló példányának nevére a kategóriához tartozó számlálóhoz `Service Fabric Actor Method` :

`ivoicemailboxactor.leavemessageasync_2_89383d32-e57e-4a9b-a6ad-57c6792aa521_635650083804480486`

A fenti példában a `ivoicemailboxactor.leavemessageasync` metódus neve, a `2` futásidejű belső használatra generált 32 bites azonosító, a `89383d32-e57e-4a9b-a6ad-57c6792aa521` Service FABRIC partíciós azonosító karakterláncos ábrázolása, és a `635650083804480486` futtatókörnyezet belső használatára generált 64 bites azonosító.

## <a name="list-of-events-and-performance-counters"></a>Események és teljesítményszámlálók listája
### <a name="actor-method-events-and-performance-counters"></a>Színészi metódus eseményei és teljesítményszámlálói
A Reliable Actors Runtime a következő, a [Actor metódusokkal](service-fabric-reliable-actors-introduction.md)kapcsolatos eseményeket bocsátja ki.

| Esemény neve | Eseményazonosító | Level | Kulcsszó | Description |
| --- | --- | --- | --- | --- |
| ActorMethodStart |7 |Részletes |0x2 |A Actors futtatókörnyezet egy Actor metódus meghívására készül. |
| ActorMethodStop |8 |Részletes |0x2 |Egy Actor metódus befejezte a végrehajtást. Ez azt eredményezi, hogy a futásidejű aszinkron hívást kapott a Actor metódus, és a Actor metódus által visszaadott feladat befejeződött. |
| ActorMethodThrewException |9 |Figyelmeztetés |0x3 |Kivétel történt egy Actor metódus végrehajtása során, vagy a futásidejű aszinkron hívása során, vagy a Actor metódus által visszaadott feladat végrehajtása során. Ez az esemény bizonyos típusú hibát jelez a színészi kódban, amelynek vizsgálatára van szükség. |

A Reliable Actors futtatókörnyezet a Actor metódusok végrehajtásával kapcsolatos következő teljesítményszámlálók közzétételét teszi közzé.

| Kategória neve | Számláló neve | Description |
| --- | --- | --- |
| Service Fabric Actors metódus |Hívás/mp |A Actor szolgáltatás metódusának meghívása másodpercenkénti számú alkalommal |
| Service Fabric Actors metódus |Hívás átlagos száma ezredmásodpercben |A Actor szolgáltatás metódusának végrehajtásához szükséges idő ezredmásodpercben |
| Service Fabric Actors metódus |Kivételek száma másodpercenként |Az a szám, ahányszor a Actor Service metódus kivételt okozott másodpercenként |

### <a name="concurrency-events-and-performance-counters"></a>Egyidejűségi események és teljesítményszámlálók
A Reliable Actors futtatókörnyezet az [egyidejűséghez](service-fabric-reliable-actors-introduction.md#concurrency)kapcsolódó alábbi eseményeket bocsátja ki.

| Esemény neve | Eseményazonosító | Level | Kulcsszó | Description |
| --- | --- | --- | --- | --- |
| ActorMethodCallsWaitingForLock |12 |Részletes |0x8 |Ezt az eseményt a színész minden új bekapcsolásának elején kell megírni. Tartalmazza azon függőben lévő Actor-hívások számát, amelyek arra várnak, hogy beszerezzék a bekapcsolási egyidejűséget végrehajtó, a színészi zárolást. |

A Reliable Actors futtatókörnyezet a párhuzamossághoz kapcsolódó következő teljesítményszámlálók közzétételét teszi közzé.

| Kategória neve | Számláló neve | Description |
| --- | --- | --- |
| Service Fabric színész |a színészi zárolásra várakozó Actors-hívások száma |A függőben lévő szereplők által kezdeményezett hívások száma, amelyek kikényszerítik a Turn-alapú párhuzamosságot |
| Service Fabric színész |Zárolási várakozás átlagos száma ezredmásodpercben |Igénybe vett idő (ezredmásodpercben) a többtényezős zárolás beolvasásához, amely kikényszeríti a párhuzamos párhuzamosságot |
| Service Fabric színész |Átlagosan ezredmásodpercben tartott színész zárolása |Az az idő (ezredmásodpercben), amely esetében az egytényezős zárolást tárolják |

### <a name="actor-state-management-events-and-performance-counters"></a>Színészi állapot-felügyeleti események és teljesítményszámlálók
A Reliable Actors Runtime a következő eseményeket bocsátja ki a [szereplők állapotának kezelésével](service-fabric-reliable-actors-state-management.md)kapcsolatban.

| Esemény neve | Eseményazonosító | Level | Kulcsszó | Description |
| --- | --- | --- | --- | --- |
| ActorSaveStateStart |10 |Részletes |0x4 |A Actors futtatókörnyezet a színész állapotának mentésére készül. |
| ActorSaveStateStop |11 |Részletes |0x4 |A Actors Runtime befejezte a színész állapotának mentését. |

A Reliable Actors Runtime közzéteszi a szereplők állapotának kezelésével kapcsolatos következő teljesítményszámlálókat.

| Kategória neve | Számláló neve | Description |
| --- | --- | --- |
| Service Fabric színész |Átlagosan ezredmásodperc/mentési állapot művelete |A színészi állapot mentéséhez szükséges idő ezredmásodpercben |
| Service Fabric színész |Terhelési állapot átlagos száma ezredmásodpercben |A Actor állapotának betöltéséhez szükséges idő ezredmásodpercben |

### <a name="events-related-to-actor-replicas"></a>Actor-replikákkal kapcsolatos események
A Reliable Actors futtatókörnyezet a következő, [színészi replikákkal](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors)kapcsolatos eseményeket bocsátja ki.

| Esemény neve | Eseményazonosító | Level | Kulcsszó | Description |
| --- | --- | --- | --- | --- |
| ReplicaChangeRoleToPrimary |1 |Tájékoztató |0x1 |A Actor replika megváltoztatta a szerepkört az elsődleges értékre. Ez azt jelenti, hogy a partíció résztvevői a replikán belül lesznek létrehozva. |
| ReplicaChangeRoleFromPrimary |2 |Tájékoztató |0x1 |A Actor replika a szerepkört nem elsődlegesre módosította. Ez azt jelenti, hogy a partíció résztvevői többé nem jönnek létre a replikán belül. A rendszer nem küld új kérelmeket a replikán belül már létrehozott résztvevőknek. A rendszer a folyamatban lévő kérések befejezése után megsemmisíti a szereplőket. |

### <a name="actor-activation-and-deactivation-events-and-performance-counters"></a>Színészi aktiválási és inaktiválási események és teljesítményszámlálók
A Reliable Actors Runtime a következő, a [színész aktiválásával és inaktiválásával](service-fabric-reliable-actors-lifecycle.md)kapcsolatos eseményeket bocsátja ki.

| Esemény neve | Eseményazonosító | Level | Kulcsszó | Description |
| --- | --- | --- | --- | --- |
| ActorActivated |5 |Tájékoztató |0x1 |Egy színész aktiválva lett. |
| ActorDeactivated |6 |Tájékoztató |0x1 |Egy szereplő inaktiválva lett. |

A Reliable Actors futtatókörnyezet a következő teljesítményszámlálók közzétételét teszi közzé a Actor aktiválásával és inaktiválásával kapcsolatban.

| Kategória neve | Számláló neve | Description |
| --- | --- | --- |
| Service Fabric színész |Átlagos OnActivateAsync ezredmásodpercben |A OnActivateAsync metódus végrehajtásához szükséges idő ezredmásodpercben |

### <a name="actor-request-processing-performance-counters"></a>Színészi kérelmek feldolgozási teljesítményszámlálók
Amikor az ügyfél egy metódust egy Actor proxy objektumon keresztül hív meg, a rendszer a hálózaton keresztül küldött kérést küld a Actor szolgáltatásnak. A szolgáltatás feldolgozza a kérelem üzenetét, és visszaküldi a választ az ügyfélnek. A Reliable Actors futtatókörnyezet a következő teljesítményszámlálók közzétételét teszi közzé a Actor Request Processing szolgáltatással kapcsolatban.

| Kategória neve | Számláló neve | Description |
| --- | --- | --- |
| Service Fabric színész |függőben lévő kérelmek száma |A szolgáltatásban feldolgozott kérelmek száma |
| Service Fabric színész |Kérelmek átlagos száma ezredmásodpercben |A szolgáltatás által a kérelem feldolgozásához szükséges idő (ezredmásodpercben) |
| Service Fabric színész |Kérelem deszerializálásának átlagos ezredmásodperce |Igénybe vett idő (ezredmásodpercben) a színészi kérelem üzenetének deszerializálásához, amikor a szolgáltatás megkapja a szolgáltatást |
| Service Fabric színész |Válasz szerializálásának átlagos ezredmásodperce |Igénybe vett idő (ezredmásodpercben), hogy a rendszer a válasz küldése előtt szerializálja a szolgáltatásbeli válaszüzenetet az ügyfélnek. |

## <a name="next-steps"></a>Következő lépések
* [Hogyan Reliable Actors használni a Service Fabric platformot?](service-fabric-reliable-actors-platform.md)
* [A Actor API-referenciájának dokumentációja](/previous-versions/azure/dn971626(v=azure.100))
* [Mintakód](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [EventSource-szolgáltatók a Perfview eszköz-ben](/archive/blogs/vancem/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource)
