---
title: Azure Service Fabric platform szintű monitorozás
description: Ismerje meg az Azure Service Fabric-fürtök figyelésére és diagnosztizálására szolgáló platform szintű eseményeket és naplókat.
author: srrengar
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: f9db0df9370197190c11b740f1fd1af3fb69f19c
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100581409"
---
# <a name="monitoring-the-cluster"></a>A fürt figyelése

Fontos, hogy a fürt szintjén figyelje azt, hogy a hardver és a fürt a várt módon viselkedik-e. Bár a Service Fabric a hardverhiba miatt megtarthatja az alkalmazások futtatását, azonban továbbra is meg kell vizsgálnia, hogy egy alkalmazásban vagy a mögöttes infrastruktúrában történt-e hiba. Emellett érdemes figyelnie a fürtöket a kapacitás megtervezésére, ami segít a hardver hozzáadásával vagy eltávolításával kapcsolatos döntések meghozatalában.

Service Fabric több strukturált platform-eseményt tesz elérhetővé [Service Fabric eseményekként](service-fabric-diagnostics-events.md)a EventStore és a különböző naplózási csatornákon. 

Windows rendszeren Service Fabric az események egyetlen ETW-szolgáltatóból érhetők el, amelyek az `logLevelKeywordFilters` operatív és az adat& üzenetkezelési csatornák közötti választáshoz szükségesek. Ez a módszer, amellyel elkülönítjük a kimenő Service Fabric eseményeket, hogy szükség szerint szűrni lehessen.

* **Működési műveletek** A Service Fabric és a fürt által végrehajtott magas szintű műveletek, beleértve az esetlegesen megjelenő csomópontok eseményeit, egy új alkalmazás üzembe helyezését, vagy egy frissítés visszaállítását stb. Az események teljes listáját [itt](service-fabric-diagnostics-event-generation-operational.md)tekintheti meg.  

* **Működés – részletes**  
Állapotjelentés és terheléselosztási döntések.

A műveleti csatorna számos különböző módon érhető el, például a ETW-/Windows-eseménynaplók, a [EventStore](service-fabric-diagnostics-eventstore.md) (a Windows rendszerben, a Windows-fürtök esetében pedig a 6,2-es és újabb verzióiban érhető el). A EventStore (entitások, csomópontok, alkalmazások, szolgáltatások, partíciók, replikák és tárolók) számára lehetővé teszi a fürt eseményeinek elérését, és a REST API-k és a Service Fabric ügyféloldali kódtár használatával teszi elérhetővé őket. A EventStore segítségével figyelheti a fejlesztési és tesztelési fürtöket, és megkezdheti az éles fürtök állapotának az időponthoz való bevezetését.

* **Adat& üzenetkezelés**  
Az üzenetkezelésben létrehozott kritikus naplók és események (jelenleg csak a ReverseProxy) és az adatelérési út (megbízható szolgáltatások modelljei).

* **& üzenetkezelés – részletes információk**  
A fürtben lévő adatokból és üzenetküldésből származó összes nem kritikus naplót tartalmazó részletes csatorna (ez a csatorna nagyon nagy mennyiségű eseményt tartalmaz).

Ezen kívül két strukturált EventSource-csatornát is biztosítunk, valamint a támogatási célokra gyűjtött naplókat.

* [A Reliable Services eseményei](service-fabric-reliable-services-diagnostics.md)  
Programozási modell adott eseményei.

* [A Reliable Actors eseményei](service-fabric-reliable-actors-diagnostics.md)  
Programozási modell – adott események és teljesítményszámlálók.

* Támogatási naplók  
A Service Fabric által létrehozott rendszernaplókat csak akkor használhatja, ha támogatást biztosít.

Ezek a különböző csatornák a platform szintű naplózás nagy részét fedik le. A platformok szintjének naplózásának javítása érdekében érdemes megfontolni az egészségügyi modell jobb megismerését és az egyéni állapotadatok hozzáadását, valamint az egyéni **teljesítményszámlálók** hozzáadását, amelyekkel valós időben megismerheti a szolgáltatások és alkalmazások hatását a fürtön.

A naplók kihasználása érdekében erősen ajánlott a "diagnosztika" engedélyezése a fürt létrehozásakor az Azure Portalon. A diagnosztika bekapcsolásával a fürt üzembe helyezésekor a Windows Azure Diagnostics képes fogadni az operatív, Reliable Services és megbízható Actors-csatornákat, és az adatokat az [Azure Diagnostics használatával összesített események](service-fabric-diagnostics-event-aggregation-wad.md)szerint tárolja.

## <a name="azure-service-fabric-health-and-load-reporting"></a>Azure Service Fabric állapot-és betöltési jelentéskészítés

A Service Fabric saját Health modellel rendelkezik, amely részletesen szerepel a következő cikkekben:

- [A Service Fabric állapotmonitorozásának bemutatása](service-fabric-health-introduction.md)
- [Szolgáltatásállapot jelentése és ellenőrzése](service-fabric-diagnostics-how-to-report-and-check-service-health.md)
- [Egyéni Service Fabric állapotjelentés hozzáadása](service-fabric-report-health.md)
- [Service Fabric állapottal kapcsolatos jelentések megtekintése](service-fabric-view-entities-aggregated-health.md)

Az állapotfigyelő szolgáltatás kritikus fontosságú a szolgáltatások működtetésének több aspektusa szempontjából, különösen az alkalmazások frissítésekor. A szolgáltatás minden frissítési tartományának frissítése után a frissítési tartománynak át kell adnia az állapot-ellenőrzéseket, mielőtt a központi telepítés a következő frissítési tartományra mozdul. Ha az OK állapota nem érhető el, a rendszer visszaállítja a központi telepítést, hogy az alkalmazás egy ismert OK állapotban maradjon. Bár előfordulhat, hogy egyes ügyfeleket a szolgáltatások visszavonása előtt érintenek, a legtöbb ügyfél nem fog problémát tapasztalni. Emellett a megoldás viszonylag gyorsan zajlik anélkül, hogy meg kellene várnia egy emberi operátor műveletét. Minél több egészségügyi ellenőrzés van beépítve a kódban, annál rugalmasabb a szolgáltatás üzembe helyezési problémái.

A szolgáltatás állapotának egy másik aspektusa a szolgáltatásból származó jelentések mérőszámai. A metrikák fontosak a Service Fabricban, mert az erőforrás-használat kiegyensúlyozására szolgálnak. A metrikák a rendszerállapot kijelzője is lehetnek. Előfordulhat például, hogy egy olyan alkalmazással rendelkezik, amely számos szolgáltatással rendelkezik, és mindegyik példány másodpercenkénti (RPS) mérőszámot jelent. Ha egy szolgáltatás több erőforrást használ, mint egy másik szolgáltatást, Service Fabric a fürt körül helyezi el a szolgáltatási példányokat, hogy az erőforrás-használatot is meg lehessen próbálni. Az erőforrás-kihasználtság működésének részletes ismertetését lásd: erőforrás- [felhasználás kezelése és terhelés Service Fabric a metrikákkal](service-fabric-cluster-resource-manager-metrics.md).

A metrikák segítségével betekintést nyerhet a szolgáltatás teljesítményére. Az idő múlásával a metrikák segítségével ellenőrizhető, hogy a szolgáltatás a várt paramétereken belül működik-e. Ha például a trendek azt mutatják be, hogy hétfőn reggel 9 ÓRAKOR az átlagos RPS érték 1 000, akkor beállíthat egy állapotjelentést, amely riasztást küld, ha az RPS értéke a 500 vagy a feletti 1 500. Lehet, hogy minden rendben jól működik, de érdemes lehet meggyőződni arról, hogy az ügyfelek nagy tapasztalattal rendelkeznek. A szolgáltatás meghatározhatja az állapot-ellenőrzési célokat szolgáló mérőszámok készletét, de ez nem befolyásolja a fürt erőforrás-kiegyensúlyozását. Ehhez állítsa nulla értékre a metrika súlyozását. Javasoljuk, hogy az összes mérőszámot nulla értékű súlyozással indítsa el, és ne növelje a súlyozást, amíg nem biztos benne, hogy tisztában van azzal, hogy a metrikák súlyozása milyen hatással van a fürt erőforrás-kiegyensúlyozására.

> [!TIP]
> Ne használjon túl sok súlyozott mérőszámot. Nehéz lehet megérteni, hogy a szolgáltatási példányok hogyan mozdulnak el a terheléselosztásban. Néhány mérőszám hosszú utat mutathat be.

Minden olyan információ, amely az alkalmazás állapotának és teljesítményének megadására utal, a mérőszámok és az állapotadatok jelöltje. **A CPU-teljesítményszámláló megtudhatja, hogyan használja a rendszer a csomópontot, de nem jelzi, hogy egy adott szolgáltatás kifogástalan állapotú-e, mert több szolgáltatás is futhat egyetlen csomóponton.** Azonban a metrikák, például az RPS, a feldolgozott elemek és a kérelmek késése is jelezheti egy adott szolgáltatás állapotát.

## <a name="service-fabric-support-logs"></a>Service Fabric támogatási naplók

Ha kapcsolatba kell lépnie a Microsoft ügyfélszolgálatával az Azure Service Fabric-fürttel kapcsolatos segítségért, a támogatási naplók szinte mindig szükségesek. Ha a fürt az Azure-ban üzemel, a rendszer automatikusan konfigurálja és gyűjti a támogatási naplókat a fürt létrehozása során. A naplók tárolása egy dedikált Storage-fiókban történik a fürt erőforráscsoport-csoportjában. A Storage-fiók nem rendelkezik rögzített névvel, de a fiókban a blob-tárolók és-táblák a *hálóval* kezdődő névvel jelennek meg. További információ a naplófájlok önálló fürthöz való beállításáról: [önálló Azure Service Fabric-fürt és konfigurációs beállítások létrehozása és kezelése](service-fabric-cluster-creation-for-windows-server.md) [önálló Windows-fürthöz](service-fabric-cluster-manifest.md). Önálló Service Fabric-példányok esetén a naplókat egy helyi fájlmegosztás számára kell elküldeni. Ezeknek a naplóknak támogatásra van **szükségük** , de nem használhatók a Microsoft ügyfélszolgálati csapatán kívüli személyek számára.

## <a name="measuring-performance"></a>Teljesítmény mérése

A fürt teljesítményének mérése segít megérteni, hogyan képes kezelni a terhelést és a meghajtóval kapcsolatos döntéseket a fürt méretezése terén (lásd: a fürt méretezése az [Azure](service-fabric-cluster-scale-in-out.md)-ban vagy [a helyszínen](service-fabric-cluster-windows-server-add-remove-nodes.md)). A teljesítményadatok abban az esetben is hasznosak, ha az Ön vagy alkalmazásai és szolgáltatásai a jövőben is elvégezték a naplók elemzését. 

A Service Fabric használatakor gyűjtött teljesítményszámlálók listáját lásd: [teljesítményszámlálók a Service Fabric](service-fabric-diagnostics-event-generation-perf.md)

Íme két gyakori módszer, amelyekkel a fürt teljesítményadatokat gyűjthet:

* **Ügynök használata**  
Ez az előnyben részesített módszer a gépek teljesítményének begyűjtésére, mivel az ügynökök általában a lehetséges teljesítmény-mérőszámok listáját gyűjtik, és viszonylag egyszerű folyamat, amellyel kiválaszthatja a begyűjteni vagy módosítani kívánt mérőszámokat. Az Azure Monitor ajánlat Azure Monitor naplók beolvasása Service Fabric [Azure monitor naplók integrációja](service-fabric-diagnostics-event-analysis-oms.md) és [a log Analytics-ügynök beállítása](../azure-monitor/agents/agent-windows.md) további információk a log Analytics ügynökről, amely egy ilyen figyelő ügynök, amely képes a fürtözött virtuális gépek és a telepített tárolók teljesítményadatait felvenni.

* **Teljesítményszámlálók az Azure Table Storage**  
A teljesítménymutatókat is elküldheti ugyanahhoz a tábla-tárolóhoz, mint az eseményeket. Ehhez módosítania kell a Azure Diagnostics konfigurációt a fürtben lévő virtuális gépek megfelelő teljesítményszámlálói kiválasztásához, és lehetővé kell tenni a Docker-statisztika felvételét, ha tárolókat helyez üzembe. További információ a teljesítményszámlálók [a Service FABRIC wad](service-fabric-diagnostics-event-aggregation-wad.md) -ben történő konfigurálásáról a teljesítményszámláló-gyűjtemény beállításához.

## <a name="next-steps"></a>Következő lépések

* További információ a Service Fabric [Azure monitor naplók integrálásáról](service-fabric-diagnostics-event-analysis-oms.md) a fürt diagnosztika gyűjtéséhez és egyéni lekérdezések és riasztások létrehozásához
* Ismerkedjen meg Service Fabric a beépített diagnosztikai felülettel, a [EventStore](service-fabric-diagnostics-eventstore.md)
* Tekintse át a Service Fabric [gyakori diagnosztikai forgatókönyveit](service-fabric-diagnostics-common-scenarios.md)
