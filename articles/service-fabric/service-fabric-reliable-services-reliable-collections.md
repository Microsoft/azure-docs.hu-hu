---
title: A megbízható gyűjtemények bemutatása
description: Service Fabric állapot-nyilvántartó szolgáltatások megbízható gyűjteményeket biztosítanak, amelyek lehetővé teszik a nagyfokú rendelkezésre állású, méretezhető és kis késleltetésű felhőalapú alkalmazások írását.
ms.topic: conceptual
ms.date: 3/10/2020
ms.openlocfilehash: 7d705f81b4ad31559886e43226febcd4cf1d345d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98784376"
---
# <a name="introduction-to-reliable-collections-in-azure-service-fabric-stateful-services"></a>A megbízható gyűjtemények bemutatása az Azure Service Fabric állapot-nyilvántartó szolgáltatásokban

A megbízható gyűjtemények lehetővé teszik a kiválóan elérhető, méretezhető és kis késleltetésű felhőalapú alkalmazások írását, mintha egyetlen számítógépes alkalmazást írna. A **Microsoft. ServiceFabric.** Rea. Collections névtér osztályai olyan gyűjteményeket biztosítanak, amelyek automatikusan teszik elérhetővé az állapotot. A fejlesztőknek csak a megbízható gyűjtemény API-khoz kell programozniuk, és lehetővé kell tenniük a megbízható gyűjtemények számára a replikált és a helyi állapot kezelését.

A megbízható gyűjtemények és más, magas rendelkezésre állású technológiák (például a Redis, az Azure Table service és az Azure Queue szolgáltatás) közötti fő különbség az, hogy az állam a szolgáltatási példányon helyileg marad, és magas rendelkezésre állású. Ez azt jelenti, hogy:

* Minden olvasás helyi, ami alacsony késést és nagy adatátviteli sebességet eredményez.
* Minden írás a hálózati IOs minimális számát vonja maga után, ami alacsony késést és nagy adatátviteli sebességet eredményez.

![Gyűjtemények alakulásának képe.](media/service-fabric-reliable-services-reliable-collections/ReliableCollectionsEvolution.png)

A megbízható gyűjtemények olyanok, mint a rendszer természetes fejlődése **. gyűjtemények** osztályok: új gyűjtemények, amelyek a Felhőbeli és a többszámítógépes alkalmazások számára lettek kialakítva, a fejlesztő összetettségének növelése nélkül. Így a megbízható gyűjtemények a következők:

* Replikálva: az állapot módosításait a rendszer replikálja a magas rendelkezésre állás érdekében.
* Aszinkron: az API-k aszinkron módon biztosítják, hogy a szálak ne legyenek letiltva az i/o-műveletek során.
* Tranzakciós: az API-k a tranzakciók absztrakcióját használják, így a szolgáltatáson belül több megbízható gyűjtemény is kezelhető.
* Megőrzött vagy változékony: az adatmennyiséget a nagy léptékű leállások (például adatközpont-áramkimaradás) miatt tartós lemezre lehet megőrizni. Bizonyos megbízható gyűjtemények olyan illékony üzemmódot is támogatnak (a [megkötésekkel](service-fabric-reliable-services-reliable-collections-guidelines.md#volatile-reliable-collections)), ahol az összes adat memóriában marad, például egy replikált memóriában tárolt gyorsítótár.

A megbízható gyűjtemények erős konzisztencia-garanciát biztosítanak az alkalmazás állapotának megkönnyítése érdekében.
Az erős konzisztencia úgy érhető el, hogy a tranzakciók véglegesítését csak akkor hajtja végre, ha a teljes tranzakciót a replikák többségi kvórumán naplózták, beleértve az elsődlegest is.
A gyengébb konzisztencia elérése érdekében az alkalmazások visszaigazolják az ügyfél/kérelmező számára az aszinkron véglegesítés visszatérését.

A megbízható gyűjtemények API-k az egyidejű gyűjtemények API-k (a **System. Collections. párhuzamos** névtér) fejlődése:

* Aszinkron: feladat visszaadása, mivel a párhuzamos gyűjteményektől eltérően a rendszer replikálja és megőrzi a műveleteket.
* Nincsenek paraméterek: `ConditionalValue<T>` az a `bool` és a értéket a paraméterek helyett egy érték visszaadására használja. `ConditionalValue<T>` hasonló, `Nullable<T>` de nem igényel T-t struct-ként.
* Tranzakciók: tranzakció-objektum használatával lehetővé teszi, hogy a felhasználó több megbízható gyűjteményben csoportosítsa a műveleteket egy tranzakcióban.

Ma a **Microsoft. ServiceFabric. Recollections. Collections** három gyűjteményt tartalmaz:

* [Megbízható szótár](/dotnet/api/microsoft.servicefabric.data.collections.ireliabledictionary-2#microsoft_servicefabric_data_collections_ireliabledictionary_2): a kulcs/érték párok replikált, tranzakciós és aszinkron gyűjteményét jelöli. A **ConcurrentDictionary** hasonlóan a kulcs és az érték is bármilyen típusú lehet.
* [Megbízható üzenetsor](/dotnet/api/microsoft.servicefabric.data.collections.ireliablequeue-1#microsoft_servicefabric_data_collections_ireliablequeue_1): a replikált, tranzakciós és aszinkron, szigorú első-be, első kimenő (FIFO) várólistát jelöli. A **ConcurrentQueue** hasonlóan az érték bármilyen típusú lehet.
* [Megbízható párhuzamos üzenetsor](service-fabric-reliable-services-reliable-concurrent-queue.md): a nagy átviteli sebesség érdekében a rendszer replikált, tranzakciós és aszinkron módon kezeli a legjobb rendezési várakozási sort. A **ConcurrentQueue** hasonlóan az érték bármilyen típusú lehet.

## <a name="next-steps"></a>Következő lépések

* [Megbízható gyűjteményekre vonatkozó irányelvek & javaslatok](service-fabric-reliable-services-reliable-collections-guidelines.md)
* [A Reliable Collections használata](service-fabric-work-with-reliable-collections.md)
* [Tranzakciók és zárolások](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
* Az adatkezelés
  * [Biztonsági mentés és visszaállítás](service-fabric-reliable-services-backup-restore.md)
  * [Értesítések](service-fabric-reliable-services-notifications.md)
  * [Reliable Collections-szerializáció](service-fabric-reliable-services-reliable-collections-serialization.md)
  * [Szerializálás és frissítés](service-fabric-application-upgrade-data-serialization.md)
  * [Megbízható állapot-kezelő konfigurációja](service-fabric-reliable-services-configuration.md)
* Egyéb
  * [Reliable Services rövid útmutató](service-fabric-reliable-services-quick-start.md)
  * [Fejlesztői referenciák megbízható gyűjteményekhez](/dotnet/api/microsoft.servicefabric.data.collections#microsoft_servicefabric_data_collections)
