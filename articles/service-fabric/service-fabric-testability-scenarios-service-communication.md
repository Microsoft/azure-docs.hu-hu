---
title: 'Tesztelés: szolgáltatások közötti kommunikáció'
description: A szolgáltatások közötti kommunikáció egy Service Fabric alkalmazás kritikus integrációs pontja. Ez a cikk a tervezési szempontokat és a tesztelési technikákat ismerteti.
ms.topic: conceptual
ms.date: 11/02/2017
ms.openlocfilehash: fbf889422d69d031d777acd80cf3becfb3184568
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96575924"
---
# <a name="service-fabric-testability-scenarios-service-communication"></a>Service Fabric tesztelési forgatókönyvek: szolgáltatás-kommunikáció
Az Azure Service Fabricban természetesen a szolgáltatások és a szolgáltatás-orientált építészeti stílusok is felszínre kerülnek. Ezekben az elosztott architektúrákban az összetevő-szolgáltatási alkalmazások jellemzően több olyan szolgáltatásból állnak, amelyeknek egymással kell kommunikálni. A legegyszerűbb esetekben általában legalább egy állapot nélküli webszolgáltatás és egy állapot-nyilvántartó adattároló szolgáltatásnak kell kommunikálnia.

A szolgáltatások közötti kommunikáció egy alkalmazás kritikus integrációs pontja, mivel minden egyes szolgáltatás távoli API-t tesz elérhetővé más szolgáltatásoknak. Az I/O-t tartalmazó API-határok készletének használata némi gondot igényel, jó tesztelési és érvényesítési művelettel.

Számos szempontot figyelembe kell venni, ha a szolgáltatási határok egy elosztott rendszerbe vannak kötve:

* *Átviteli protokoll*. A HTTP-t használja az együttműködés növeléséhez, vagy egy egyéni bináris protokollt a maximális átviteli sebességhez?
* *Hibakezelés*. Hogyan történik az állandó és az átmeneti hibák kezelése? Mi történik, ha egy szolgáltatás egy másik csomópontra kerül át?
* *Időtúllépések és késések*. A többrétegű alkalmazásokban hogyan fogja kezelni az egyes szolgáltatási rétegek késését a verem és a felhasználó között?

Függetlenül attól, hogy a Service Fabric által biztosított beépített szolgáltatás-kommunikációs összetevők valamelyikét használja-e, vagy saját fejlesztést végez, a szolgáltatások közötti interakciók tesztelése elengedhetetlen a rugalmasság biztosításához az alkalmazásban.

## <a name="prepare-for-services-to-move"></a>Felkészülés a szolgáltatások áthelyezésére
A szolgáltatási példányok az idő múlásával mozoghatnak. Ez különösen akkor igaz, ha az egyéni igényeknek megfelelő erőforrás-kiegyensúlyozáshoz terhelési metrikákkal vannak konfigurálva. Service Fabric áthelyezi a szolgáltatási példányokat a rendelkezésre állás maximalizálása érdekében, még a frissítések, a feladatátvételek, a Kibővítés és az elosztott rendszer élettartama során előforduló egyéb helyzetekben is.

Ahogy a szolgáltatások a fürtön mozognak, az ügyfeleknek és más szolgáltatásoknak fel kell készülniük a két forgatókönyv kezelésére, amikor a szolgáltatással kommunikálnak:

* A szolgáltatás példányának vagy partíciójának replikája a legutóbbi megbeszélés óta elindult. Ez a szolgáltatás életciklusának normális része, és várhatóan az alkalmazás élettartama alatt kell történnie.
* A szolgáltatás példányának vagy a partíció replikájának áthelyezése folyamatban van. Bár a szolgáltatás egyik csomópontról a másikra történő feladatátvétele nagyon gyorsan megtörténik Service Fabricban, előfordulhat, hogy a rendelkezésre állás késéssel jár, ha a szolgáltatás kommunikációs összetevője lassan indul el.

Ezeket a forgatókönyveket szabályosan kezelheti zökkenőmentesen futó rendszer szempontjából. Ehhez vegye figyelembe a következőket:

* Minden olyan szolgáltatás, amelyhez csatlakozni lehet, rendelkezik egy, az általa figyelt *címen* (például http vagy WebSockets). Amikor egy szolgáltatás példánya vagy partíciója áthelyeződik, a címe végpontja megváltozik. (Egy másik, eltérő IP-címmel rendelkező csomópontra vált.) Ha a beépített kommunikációs összetevőket használja, a rendszer a szolgáltatási címek ismételt feloldását fogja kezelni.
* Előfordulhat, hogy a szolgáltatási késés átmenetileg növekszik, mivel a szolgáltatási példány újra elindítja a figyelőt. Ez attól függ, hogy a szolgáltatás milyen gyorsan nyitja meg a figyelőt a szolgáltatási példány áthelyezése után.
* A meglévő kapcsolatokat be kell zárni és újra meg kell nyitni, miután a szolgáltatás megnyílik egy új csomóponton. Egy kecses csomópont leállítása vagy újraindítása lehetővé teszi, hogy a meglévő kapcsolatok zökkenőmentesen leálljanak.

### <a name="test-it-move-service-instances"></a>Tesztelés: szolgáltatási példányok áthelyezése
A Service Fabric tesztelési eszközeinek használatával tesztelési forgatókönyvet hozhat létre az ilyen helyzetek különböző módokon történő teszteléséhez:

1. Állapot-nyilvántartó szolgáltatás elsődleges replikájának áthelyezése.
   
    Egy állapot-nyilvántartó szolgáltatás partíciójának elsődleges replikája tetszőleges számú okból áthelyezhető. Ezzel a megoldással megcélozhatja egy adott partíció elsődleges replikáját, hogy megtekintse, hogyan reagálnak a szolgáltatások nagyon ellenőrzött módon.
   
    ```powershell
   
    PS > Move-ServiceFabricPrimaryReplica -PartitionId 6faa4ffa-521a-44e9-8351-dfca0f7e0466 -ServiceName fabric:/MyApplication/MyService
   
    ```
2. Állítsa le a csomópontot.
   
    Egy csomópont leállításakor Service Fabric áthelyezi az adott csomóponton lévő összes szolgáltatási példányt vagy partíciót a fürt egy másik elérhető csomópontjára. Ezzel a megoldással tesztelheti a csomópontot a fürtből, és a csomóponton lévő összes szolgáltatási példányt és replikát át kell helyezni.
   
    A csomópontot a PowerShell **stop-ServiceFabricNode** parancsmag használatával állíthatja le:
   
    ```powershell
   
    PS > Stop-ServiceFabricNode -NodeName Node_1
   
    ```

## <a name="maintain-service-availability"></a>A szolgáltatás rendelkezésre állásának fenntartása
Platformként a Service Fabric úgy lett kialakítva, hogy magas rendelkezésre állást biztosítson szolgáltatásai számára. Szélsőséges esetekben azonban a mögöttes infrastruktúra-problémák továbbra is elérhetetlenek lehetnek. Fontos, hogy ezeket a forgatókönyveket is tesztelje.

Az állapot-nyilvántartó szolgáltatások kvórum-alapú rendszer használatával replikálják az állapotot a magas rendelkezésre állás érdekében. Ez azt jelenti, hogy a replikák Kvórumának elérhetőnek kell lennie az írási műveletek végrehajtásához. Ritka esetekben, például egy széleskörű hardverhiba miatt előfordulhat, hogy a replikák kvóruma nem érhető el. Ezekben az esetekben nem fogja tudni végrehajtani az írási műveleteket, de továbbra is elvégezheti az olvasási műveleteket.

### <a name="test-it-write-operation-unavailability"></a>Tesztelés: írási művelet nem rendelkezésre állása
A Service Fabric tesztelési eszközeinek használatával olyan hibát adhat meg, amely a kvórum elvesztését eredményezi tesztként. Bár ez a forgatókönyv ritka, fontos, hogy az állapot-nyilvántartó szolgáltatástól függő ügyfelek és szolgáltatások felkészüljön olyan helyzetek kezelésére, amikor nem tudnak írási kéréseket készíteni. Fontos továbbá, hogy az állapot-nyilvántartó szolgáltatás maga is tisztában legyen ezzel a lehetőséggel, és szabályosan kommunikáljon a hívókkal.

A kvórumerőforrást a PowerShell **meghívása-ServiceFabricPartitionQuorumLoss** parancsmag használatával válthat:

```powershell

PS > Invoke-ServiceFabricPartitionQuorumLoss -ServiceName fabric:/Myapplication/MyService -QuorumLossMode QuorumReplicas -QuorumLossDurationInSeconds 20

```

Ebben a példában azt szeretnénk jelezni, hogy az `QuorumLossMode` `QuorumReplicas` összes replika leállása nélkül szeretnénk kiváltani a kvórum elvesztését. Így az olvasási műveletek továbbra is lehetségesek. Egy olyan forgatókönyv teszteléséhez, amelyben a teljes partíció nem érhető el, ezt a kapcsolót állíthatja be `AllReplicas` .

## <a name="next-steps"></a>Következő lépések
[További információ a tesztelési műveletekről](service-fabric-testability-actions.md)

[További információ a tesztelési forgatókönyvekről](service-fabric-testability-scenarios.md)

