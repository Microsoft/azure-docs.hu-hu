---
title: Teljesítmény és méretezés a Durable Functions-ben – Azure
description: Ismerje meg a Azure Functions Durable Functions bővítményének egyedi méretezési jellemzőit.
author: cgillum
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: d41b06bb0c2b26776f9d9c195c3a713e4dae9f82
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105626628"
---
# <a name="performance-and-scale-in-durable-functions-azure-functions"></a>Teljesítmény és méretezés a Durable Functionsben (Azure Functions)

A teljesítmény és a méretezhetőség optimalizálása érdekében fontos megérteni a [Durable functions](durable-functions-overview.md)egyedi méretezési jellemzőit.

A méretezési viselkedés megértéséhez ismernie kell az alapul szolgáló Azure Storage-szolgáltató részleteit.

## <a name="history-table"></a>Előzmények táblázat

Az **Előzmények** tábla egy Azure Storage-tábla, amely a tevékenység központján belüli összes előkészítési példány előzményi eseményeit tartalmazza. Ennek a táblának a neve *TaskHubName*-előzmények formájában szerepel. A példányok futtatásakor a rendszer új sorokat ad hozzá ehhez a táblához. Ennek a táblának a partíciós kulcsát a rendszer az előkészítési példány azonosítójával származtatja. A példányok azonosítója a legtöbb esetben véletlenszerű, ami biztosítja a belső partíciók optimális elosztását az Azure Storage-ban.

Ha egy előkészítési példány futtatására van szükség, az előzmények tábla megfelelő sorai betöltődik a memóriába. Ezeket az *előzményeket* a rendszer a Orchestrator függvény kódjában játssza újra, hogy visszakapja a korábban ellenőrzőpontos állapotba. A végrehajtási előzményeknek az ilyen módon történő újraépítésére való használatát az [esemény-beszerzési minta](/azure/architecture/patterns/event-sourcing)befolyásolja.

## <a name="instances-table"></a>Példányok tábla

A **instances** tábla egy másik Azure Storage-tábla, amely az összes előkészítési és entitási példány állapotát tartalmazza egy adott feladatsoron belül. A példányok létrehozásakor a rendszer új sorokat ad hozzá ehhez a táblához. A tábla partíciós kulcsa a megszervezési példány azonosítója vagy az entitás kulcsa, és a sor kulcsa üres karakterlánc. A rendszer egy összehangoló vagy egy entitás-példányon egy sort jelöl.

Ez a tábla a `GetStatusAsync` (.net) és `getStatus` a (JavaScript) API-k, valamint az [állapot-lekérdezés http API](durable-functions-http-api.md#get-instance-status)-hoz tartozó példány-lekérdezési kérelmek kielégítésére szolgál. A rendszer végül konzisztensen tartja a korábban említett **History (korábbi** ) tábla tartalmát. Egy különálló Azure Storage-tábla használata a példányok lekérdezési műveleteinek hatékony kielégítése érdekében a [lekérdezési és manipulációs szerepek szétválasztása (CQRS) minta](/azure/architecture/patterns/cqrs)befolyásolja.

## <a name="internal-queue-triggers"></a>Belső üzenetsor-eseményindítók

A Orchestrator functions és a Activity függvények is a belső várólisták által aktiválódnak a Function alkalmazás feladatának központja. A várólisták ily módon történő használata megbízható "legalább egyszeri" üzenet-kézbesítési garanciát biztosít. A Durable Functionsban kétféle várólista található: a **vezérlő üzenetsor** és a **munkaelem-várólista**.

### <a name="the-work-item-queue"></a>A munkahelyi elem várólistája

A Durable Functionsben egy munkaelem-várólista (Task hub) van. Ez egy alapszintű várólista, és hasonlóan viselkedik a Azure Functions összes többi `queueTrigger` várólistájának. Ez a várólista az állapot nélküli *tevékenységek működésének* elindítására szolgál egy adott üzenet egyetlen üzenetből való törlésével. Mindegyik üzenet tartalmaz tevékenység-és egyéb metaadatokat, például a végrehajtandó műveletet. Ha egy Durable Functions alkalmazás több virtuális gépre is kiterjed, ezek a virtuális gépek mind versenyeznek a munkaelemek várólistából való munkához.

### <a name="control-queues"></a>Vezérlési várólista (ok)

A Durable Functionsban több *ellenőrzési várólista* található. A *vezérlő üzenetsor* sokkal kifinomultabb, mint az egyszerűbb munkaelem-várólista. A vezérlési várólisták az állapot-nyilvántartó Orchestrator és az entitás funkcióinak aktiválására szolgálnak. Mivel a Orchestrator és az Entity függvény példányai az állapot-nyilvántartó példányok, fontos, hogy az egyes feldolgozókat vagy entitásokat egyszerre csak egy feldolgozó dolgozza fel. Ennek eléréséhez minden egyes összehangoló példány vagy entitás egyetlen vezérlő várólistához van rendelve. Ezeket a vezérlési várólistákat a feldolgozók terheléselosztással biztosítják, hogy az egyes várólistákat egyszerre csak egy feldolgozó dolgozza fel. Ennek a viselkedésnek a további részletei a következő részekben olvashatók.

A vezérlési várólisták számos különböző előkészítési életciklus-típusú üzenetet tartalmaznak. Ilyenek például a [Orchestrator-vezérlési üzenetek](durable-functions-instance-management.md), a tevékenység- *visszajelzési* üzenetek és az időzítő üzenetek. Az 32-as számú üzenetek egy lekérdezési sorból lesznek elküldve egyetlen lekérdezésben. Ezek az üzenetek hasznos adatokat és metaadatokat tartalmaznak, beleértve a kívánt előkészítési példányt is. Ha ugyanahhoz a hangelőkészítési példányhoz több elküldött üzenetet kíván, a rendszer kötegként dolgozza fel őket.

### <a name="queue-polling"></a>Üzenetsor-lekérdezés

A tartós feladathoz tartozó bővítmény egy véletlenszerű exponenciális visszakapcsolási algoritmust valósít meg, amely csökkenti az üresjárati üzenetsor lekérdezésének hatását a tárolási tranzakciós költségekre. Ha egy üzenet található, a futtatókörnyezet azonnal egy másik üzenetet keres; Ha nem talál üzenetet, egy ideig várakozik, mielőtt újra próbálkozik. A várakozási sor üzenetének későbbi sikertelen próbálkozásai után a várakozási idő továbbra is növekszik, amíg el nem éri a maximális várakozási időt, amely az alapértelmezett érték 30 másodperc.

A maximális lekérdezési késleltetés a `maxQueuePollingInterval` [ fájlhost.js](../functions-host-json.md#durabletask)tulajdonságán keresztül konfigurálható. Ha ezt a tulajdonságot magasabb értékre állítja, akkor az üzenet feldolgozási késése magasabb lehet. A nagyobb késések csak a tétlenségi időszakok után várhatók. Ha ez a tulajdonság alacsonyabb értékre van állítva, a megnövekedett tárolási tranzakciók miatt magasabb tárolási költségek léphetnek fel.

> [!NOTE]
> A Azure Functions-felhasználás és a prémium csomagok futtatásakor a [Azure functions skálázási vezérlő](../event-driven-scaling.md) 10 másodpercenként egyszer lekérdezi az egyes vezérlőket és a munkaelemek várólistáit. Ez a további lekérdezés a Function app-példányok aktiválásához és a méretezési döntések elvégzéséhez szükséges. Az írás időpontjában ez a 10 másodperces intervallum állandó, és nem konfigurálható.

### <a name="orchestration-start-delays"></a>Előkészítési kezdési késések
Az előkészítési példányok elkezdődnek, ha egy üzenetet helyeznek el `ExecutionStarted` a tevékenység központja egyik vezérlő-várólistáján. Bizonyos körülmények között megfigyelheti a több másodperces késleltetést, ha a rendszer futtatásakor ütemezi a futtatást, és amikor ténylegesen elindul. Ebben az időintervallumban a koordináló példány az állapotban marad `Pending` . Ennek a késleltetésnek két lehetséges oka van:

1. **Várakozó-vezérlési várólisták**: Ha a példányhoz tartozó vezérlő-várólista nagy mennyiségű üzenetet tartalmaz, akkor az `ExecutionStarted` üzenet fogadása és a futtatókörnyezet általi feldolgozása előtt időbe telik. Üzenetben várakozó üzenetek akkor fordulnak elő, ha az előkészítés során a rendszer egyszerre sok eseményt dolgoz fel. A vezérlési várólistába bejelentkező események közé tartoznak az előkészítési események, a tevékenységek befejezése, a tartós időzítők, a megszakítások és a külső események. Ha ez a késés normális körülmények között történik, érdemes lehet egy új, nagyobb számú partícióval rendelkező feladatot létrehozni. A további partíciók konfigurálásával a futtatókörnyezet további vezérlési várólistákat hoz létre a terhelés elosztásához. Az egyes partíciók a 1:1-as számú vezérlővel rendelkeznek, legfeljebb 16 partícióval.

2. A **lekérdezési késések** visszakeresése: egy másik gyakori ok, hogy az előkészítési késések a [vezérlési várólisták korábban leírt visszatartási lekérdezési viselkedését](#queue-polling)jelentik. Ez a késleltetés azonban csak akkor várható, ha egy alkalmazás két vagy több példányra van kibővítve. Ha csak egy alkalmazás példánya van, vagy ha a koordinálást elindító alkalmazás is ugyanaz a példány, amely lekérdezi a cél vezérlőelem-várólistát, akkor nem lesz várólista-lekérdezési késleltetés. A lekérdezési késések visszautasítása a korábban leírtaknak megfelelően a beállítások **host.js** frissítésével csökkenthető.

## <a name="storage-account-selection"></a>Storage-fiók kiválasztása

Az Durable Functions által használt várólisták, táblák és Blobok egy konfigurált Azure Storage-fiókban jönnek létre. A használni kívánt fiók a `durableTask/storageProvider/connectionStringName` (z `durableTask/azureStorageConnectionStringName` )host.jsfájljában a (z) Durable functions 1. x fájlban megadott beállítással adható **meg** .

### <a name="durable-functions-2x"></a>Durable Functions 2. x

```json
{
  "extensions": {
    "durableTask": {
      "storageProvider": {
        "connectionStringName": "MyStorageAccountAppSetting"
      }
    }
  }
}
```

### <a name="durable-functions-1x"></a>Durable Functions 1. x

```json
{
  "extensions": {
    "durableTask": {
      "azureStorageConnectionStringName": "MyStorageAccountAppSetting"
    }
  }
}
```

Ha nincs megadva, a rendszer az alapértelmezett `AzureWebJobsStorage` Storage-fiókot használja. A teljesítményre érzékeny munkaterhelések esetében azonban ajánlott a nem alapértelmezett Storage-fiók konfigurálása. Durable Functions az Azure Storage szolgáltatást erősen használja, és egy dedikált Storage-fiók elkülöníti Durable Functions tárterület-használatot a Azure Functions gazdagép belső használatáról.

## <a name="orchestrator-scale-out"></a>Orchestrator kibővíthető

Habár a tevékenység-funkciók a lehető leghatékonyabban méretezhetők a további virtuális gépek hozzáadásával, az egyes Orchestrator-példányok és entitások csak egyetlen partícióra vannak korlátozva, és a partíciók maximális száma a `partitionCount` beállításban van kötve `host.json` . 

> [!NOTE]
> Általánosságban elmondható, hogy a Orchestrator függvények egyszerűek, és nem igényelnek nagy mennyiségű számítási teljesítményt. Ezért nem szükséges nagy számú vezérlési várólista-partíciót létrehozni, hogy nagyszerű teljesítményt kapjon a feladatokhoz. A nagy részét az állapot nélküli tevékenységek függvényében kell elvégezni, ami a végtelen méretekben méretezhető.

A vezérlő-várólisták száma a fájl **host.jsban** van definiálva. Az alábbi példában szereplő host.jsa (z `durableTask/storageProvider/partitionCount` ) tulajdonságot (vagy `durableTask/partitionCount` Durable functions 1. x) a (z) értékre állítja be `3` . Vegye figyelembe, hogy a partíciók számának több vezérlő várólistája van.

### <a name="durable-functions-2x"></a>Durable Functions 2. x

```json
{
  "extensions": {
    "durableTask": {
      "storageProvider": {
        "partitionCount": 3
      }
    }
  }
}
```

### <a name="durable-functions-1x"></a>Durable Functions 1. x

```json
{
  "extensions": {
    "durableTask": {
      "partitionCount": 3
    }
  }
}
```

A feladatok központja 1 és 16 partíciót is konfigurálhat. Ha nincs megadva, az alapértelmezett partíciók száma **4**.

Az alacsony forgalmú forgatókönyvek során az alkalmazás skálázásra kerül, így a partíciók kis számú feldolgozóval lesznek kezelve. Vegyük például az alábbi diagramot.

![Skálázási diagram](./media/durable-functions-perf-and-scale/scale-progression-1.png)

Az előző ábrán azt láthatjuk, hogy az 1 – 6. Összehangolók terheléselosztást használnak a partíciók között. Hasonlóképpen, a partíciók, mint például a tevékenységek, terheléselosztást végeznek a feldolgozók között. A partíciók terheléselosztása a munkavégzők között, függetlenül az első lépések elsajátításának számától.

Ha a Azure Functions vagy a rugalmas prémium csomagokon fut, vagy ha már konfigurálta a terheléses automatikus méretezést, a rendszer több feldolgozót kap a forgalom növekedésével, és a partíciók végül terheléselosztást végeznek az összes feldolgozón. Ha továbbra is kibővítjük a méretezést, az egyes partíciókat végül egyetlen feldolgozó fogja felügyelni. A tevékenységek viszont továbbra is terheléselosztást végeznek az összes feldolgozón belül. Ez az alábbi képen látható.

![Első Felskálázási előkészítési diagram](./media/durable-functions-perf-and-scale/scale-progression-2.png)

Az egyidejű _aktív_ Összehangolók maximális száma a *megadott időpontnál* egyenlő _az alkalmazás_ időpontjára kiosztott munkavégzők számával `maxConcurrentOrchestratorFunctions` . Ez a felső határ pontosabban végezhető el, ha a partíciók teljes mértékben méretezhetők a feldolgozók között. Ha teljes mértékben felskálázást végez, és mivel minden feldolgozó csak egyetlen functions Host-példánnyal fog rendelkezni, az _aktív_ egyidejű Orchestrator-példányok maximális száma egyenlő lesz _a partíciók_ számával `maxConcurrentOrchestratorFunctions` . Az alábbi ábrán egy teljes mértékben kibővíthető forgatókönyv látható, ahol további hangvezéreltség adódik hozzá, de néhány inaktív, szürke színnel.

![Második Felskálázási előkészítési diagram](./media/durable-functions-perf-and-scale/scale-progression-3.png)

A méretezési folyamat során a rendszer a vezérlési várólista zárolásait újra elosztja a functions Host-példányok között, így biztosítva a partíciók egyenletes eloszlását. Ezek a zárolások a blob Storage-bérletekben belsőleg valósulnak meg, és gondoskodnak arról, hogy az egyes előkészítési példányok vagy entitások egyszerre csak egy gazdagép-példányon fussanak. Ha egy feladatsor három partícióval van konfigurálva (és így három vezérlési sor van), a rendszer-előkészítési példányok és entitások mind a három címbérleti tárolási példányban elosztott terhelésű lehetnek. További virtuális gépek is hozzáadhatók a tevékenységi funkciók végrehajtásának kapacitásának növeléséhez.

Az alábbi ábra azt szemlélteti, hogy a Azure Functions gazdagép hogyan kommunikál a tárolási entitásokkal egy kibővíthető környezetben.

![Diagram méretezése](./media/durable-functions-perf-and-scale/scale-interactions-diagram.png)

Ahogy az előző ábrán is látható, az összes virtuális gép versenyez a munkaelem-várólistán lévő üzenetekkel. Azonban csak három virtuális gép kaphat üzeneteket a vezérlési várólistákból, és mindegyik virtuális gép egyetlen vezérlő várólistát zárol.

Az előkészítési példányok és entitások az összes vezérlő üzenetsor-példányon vannak elosztva. A terjesztés az összehangolás vagy az entitás neve és a kulcspár példány-AZONOSÍTÓjának kivonatolásával történik. Az előkészítési példányok alapértelmezett értékei véletlenszerű GUID azonosítók, így biztosítható, hogy a példányok egyenlően oszlanak meg az összes vezérlő várólistán.

Általánosságban elmondható, hogy a Orchestrator függvények egyszerűek, és nem igényelnek nagy mennyiségű számítási teljesítményt. Ezért nem szükséges nagy számú vezérlési várólista-partíciót létrehozni, hogy nagyszerű teljesítményt kapjon a feladatokhoz. A nagy részét az állapot nélküli tevékenységek függvényében kell elvégezni, ami a végtelen méretekben méretezhető.

## <a name="auto-scale"></a>Automatikus méretezés

Csakúgy, mint a használat és a rugalmas prémium csomagokban futó összes Azure Functions esetében, Durable Functions az [Azure functions skálázási vezérlőn](../event-driven-scaling.md#runtime-scaling)keresztül támogatja az automatikus méretezést. A skálázási vezérlő figyeli az összes várólista késését a _betekintés_ parancsainak rendszeres kiállításával. A beérkező üzenetek késése alapján a méretezési vezérlő eldönti, hogy virtuális gépeket kíván-e hozzáadni vagy eltávolítani.

Ha a méretezési vezérlő meghatározza, hogy a vezérlő üzenetsor-üzeneteinek késése túl magas, akkor a virtuálisgép-példányok hozzáadására csak akkor kerül sor, ha az üzenet késése elfogadható szintre csökken, vagy eléri a vezérlési várólista partícióinak darabszámát. Hasonlóképpen, a skálázási vezérlő folyamatosan hozzá fogja adni a virtuálisgép-példányokat, ha a munkaelem-várólista késése magas, a partíciók számától függetlenül.

> [!NOTE]
> A Durable Functions 2,0-es verziótól kezdődően a Function apps konfigurálható úgy, hogy a VNET-védelemmel ellátott szolgáltatás-végpontokon fusson a rugalmas prémium csomagon belül. Ebben a konfigurációban a Durable Functions eseményindítók elindítják a méretezési kérelmeket a skálázási vezérlő helyett.

## <a name="thread-usage"></a>Szál használata

A Orchestrator függvények egyetlen szálon futnak, így biztosítható, hogy a végrehajtás a sok visszajátszásban is determinisztikus. Az egyszálas végrehajtás miatt fontos, hogy a Orchestrator-függvények ne végezzenek CPU-igényes feladatokat, legyenek az I/O-műveletek, illetve a blokkolás bármilyen okból. Az I/O, blokkoló vagy több szálat igénylő bármilyen munkát tevékenységi funkciókba kell áthelyezni.

A tevékenység-függvények mindegyike ugyanazokkal a viselkedésekkel rendelkezik, mint a normál üzenetsor által aktivált függvények. Biztonságosan végezhetik el az I/O-t, végrehajtják a CPU-intenzív műveleteket, és több szálat is használhatnak. Mivel a tevékenység-eseményindítók állapot nélküliek, szabadon méretezhetők a virtuális gépek számára.

Az Entity functions szolgáltatást egy szálon is végrehajtja a rendszer, és egy-egy időben dolgozza fel a műveleteket. Azonban az Entity functions nem tartalmaz korlátozásokat a végrehajtható kód típusára vonatkozóan.

## <a name="concurrency-throttles"></a>Egyidejűségi szabályozások

Azure Functions egyszerre több függvényt hajt végre egyetlen alkalmazás-példányon belül. Az egyidejű végrehajtás segít a párhuzamosság növelésében, és minimálisra csökkenti a "hideg indulások" számát, amelyet egy tipikus alkalmazás az idő múlásával fog tapasztalni. A magas Egyidejűség azonban a virtuálisgép-rendszererőforrások (például a hálózati kapcsolatok vagy a rendelkezésre álló memória) esetében is kimeríthető. A Function alkalmazás igényeitől függően szükség lehet a felhasználónkénti Egyidejűség szabályozására, hogy elkerülje a nagy terhelésű helyzetekben a memória kifutásának lehetőségét.

A tevékenység, a Orchestrator és az entitás függvényének párhuzamossági korlátja a fájl **host.js** is konfigurálható. A megfelelő beállítások a `durableTask/maxConcurrentActivityFunctions` tevékenységek és az `durableTask/maxConcurrentOrchestratorFunctions` Orchestrator és az Entity functions esetében egyaránt érvényesek.

### <a name="functions-20"></a>Függvények 2,0

```json
{
  "extensions": {
    "durableTask": {
      "maxConcurrentActivityFunctions": 10,
      "maxConcurrentOrchestratorFunctions": 10
    }
  }
}
```

### <a name="functions-1x"></a>Functions 1.x

```json
{
  "durableTask": {
    "maxConcurrentActivityFunctions": 10,
    "maxConcurrentOrchestratorFunctions": 10
  }
}
```

Az előző példában legfeljebb 10 Orchestrator vagy Entity függvény és 10 tevékenységi funkció futhat egyszerre egyetlen virtuális gépen. Ha nincs megadva, az egyidejű tevékenység-és Orchestrator, illetve az entitások függvényének végrehajtásának száma a virtuális gépen lévő magok száma alapján, 10X-re van korlátozva.

> [!NOTE]
> Ezek a beállítások a memória és a CPU-használat egyetlen virtuális gépen való kezeléséhez hasznosak. Ha azonban több virtuális gépen bővíti a skálázást, minden virtuális gépnek saját korlátai vannak. Ezek a beállítások nem használhatók globális szintű Egyidejűség vezérlésére.

## <a name="extended-sessions"></a>Bővített munkamenetek

A kiterjesztett munkamenetek olyan beállítások, amelyek az üzenetek feldolgozásának befejezése után is megőrzik a munkafolyamatokat és az entitásokat a memóriában. A kiterjesztett munkamenetek engedélyezésének tipikus hatása az Azure Storage-fiókra és a teljes továbbfejlesztett átviteli sebességre csökken.

A kiterjesztett munkamenetek engedélyezéséhez állítsa a beállítást a `durableTask/extendedSessionsEnabled` `true` fájl **host.js** . `durableTask/extendedSessionIdleTimeoutInSeconds`Ezzel a beállítással szabályozhatja, hogy mennyi ideig tart a memóriában az üresjárati munkamenet:

**Függvények 2,0**
```json
{
  "extensions": {
    "durableTask": {
      "extendedSessionsEnabled": true,
      "extendedSessionIdleTimeoutInSeconds": 30
    }
  }
}
```

**Függvények 1,0**
```json
{
  "durableTask": {
    "extendedSessionsEnabled": true,
    "extendedSessionIdleTimeoutInSeconds": 30
  }
}
```

Ennek a beállításnak két lehetséges hátránya van:

1. Összességében megnő a Function app memóriahasználat használata.
2. Ha sok egyidejű, rövid életű Orchestrator vagy entitás-függvény végrehajtást használ, az átviteli sebesség összességében csökkenhet.

Ha például `durableTask/extendedSessionIdleTimeoutInSeconds` 30 másodpercre van beállítva, akkor egy rövid életű Orchestrator vagy Entity függvény, amely 1 másodpercnél rövidebb ideig fut, továbbra is 30 másodpercig memóriát foglal le. Emellett a `durableTask/maxConcurrentOrchestratorFunctions` korábban említett kvótára is vonatkozik, ami esetleg megakadályozza más Orchestrator vagy Entity függvények futtatását.

A következő szakaszok ismertetik a kiterjesztett munkamenetek a Orchestrator és az Entity functions szolgáltatásban megadott effektusait.

> [!NOTE]
> A kiterjesztett munkamenetek jelenleg csak .NET nyelveken támogatottak, például C# vagy F #. A `extendedSessionsEnabled` `true` más platformokra való beállítása futtatókörnyezeti problémákhoz vezethet, például a tevékenységek és a folyamat által aktivált függvények csendes meghibásodása esetén.


### <a name="orchestrator-function-replay"></a>Orchestrator függvény újrajátszása

Ahogy azt korábban említettük, az Orchestrator függvények az **Előzmények** tábla tartalmával lesznek újrajátszva. Alapértelmezés szerint a rendszer minden alkalommal újra lejátssza a Orchestrator-függvény kódját, amikor az üzenetek egy kötegét a rendszer elvégzi a vezérlési sorból. Még ha a ventilátort is használja, és az összes feladatra vár (például a `Task.WhenAll` .net-ben vagy `context.df.Task.all` a JavaScript-ben), akkor a feladat-visszajelzések kötegei között időben lesznek feldolgozva. Ha a kiterjesztett munkamenetek engedélyezve vannak, a Orchestrator függvény példányai a memóriában maradnak, és az új üzenetek teljes előzmény-újrajátszás nélkül is feldolgozhatók.

A kiterjesztett munkamenetek teljesítményének növelése leggyakrabban a következő helyzetekben figyelhető meg:

* Ha egy korlátozott számú összehangoló példány egyidejűleg fut.
* Ha a hangfelismerések nagy számú szekvenciális művelettel rendelkeznek (több száz tevékenység-hívás), amely gyorsan elkészül.
* Az összehangolás és a ventilátor – nagy mennyiségű művelet, amely egy időben fejeződik be.
* Ha a Orchestrator függvénynek nagy méretű üzeneteket kell feldolgoznia, vagy bármilyen CPU-igényes adatfeldolgozást kell végeznie.

Minden más esetben a Orchestrator függvények esetében általában nem észlelhető a teljesítmény.

> [!NOTE]
> Ezeket a beállításokat csak akkor kell használni, ha egy Orchestrator függvényt teljesen fejlesztettek ki és teszteltek. Az alapértelmezett agresszív újraindítási viselkedés hasznos lehet a [Orchestrator-kódok](durable-functions-code-constraints.md) észlelésére a fejlesztési időszakban, ezért alapértelmezés szerint le van tiltva.

### <a name="entity-function-unloading"></a>Entitás funkciójának eltávolítása

Az Entity functions legfeljebb 20 műveletet dolgoz fel egyetlen kötegben. Amint egy entitás befejezi a műveletek kötegelt feldolgozását, megőrzi az állapotát és a memóriából való eltávolítását. A kibővített munkamenetek beállítás használatával késleltetheti az entitások memóriából való eltávolítását. Az entitások továbbra is megőrzik az állapotukat az előzőekben leírtaknak megfelelően, de a beállított ideig továbbra is a memóriában maradnak az Azure Storage-ból betöltött terhelés csökkentése érdekében. Az Azure Storage-ból betöltött terhelések csökkentése növelheti a gyakran használt entitások teljes átviteli sebességét.

## <a name="performance-targets"></a>Teljesítménybeli célok

Ha éles alkalmazások Durable Functions használatát tervezi használni, fontos, hogy a tervezési folyamat elején vegye figyelembe a teljesítménnyel kapcsolatos követelményeket. Ez a szakasz néhány alapszintű használati forgatókönyvet és a várt maximális átviteli sebességet ismerteti.

* **Szekvenciális tevékenység-végrehajtás**: Ez a forgatókönyv egy Orchestrator függvényt ismertet, amely egy sor tevékenységi funkciót futtat egy másik után. A leginkább hasonlít a [függvény láncolására](durable-functions-sequence.md) szolgáló mintára.
* **Párhuzamos tevékenység-végrehajtás**: Ez a forgatókönyv egy Orchestrator függvényt ismertet, amely sok tevékenység-funkciót hajt végre párhuzamosan a [ventilátor-out, ventilátor-in](durable-functions-cloud-backup.md) mintázat használatával.
* **Párhuzamos reagálások feldolgozása**: Ez a forgatókönyv a [kiugró, ventilátoros](durable-functions-cloud-backup.md) minta második fele. A ventilátor teljesítményére koncentrál. Fontos megjegyezni, hogy a ventilátortól eltérően a ventilátort egyetlen Orchestrator-függvény példánya végzi el, ezért csak egyetlen virtuális gépen futtatható.
* **Külső események feldolgozása**: Ez a forgatókönyv egyetlen Orchestrator-függvényt képvisel, amely a [külső eseményekre](durable-functions-external-events.md)vár egyszerre.
* **Entitás-művelet feldolgozása**: Ez a forgatókönyv azt vizsgálja, hogy egy _adott_ [számláló entitás](durable-functions-entities.md) milyen gyorsan dolgozhat fel egy állandó adatfolyamot.

> [!TIP]
> A ventilátorokkal ellentétben a ventilátoros műveletek egyetlen virtuális gépre korlátozódnak. Ha az alkalmazás a ventilátort, a ventilátort használja, és Ön aggódik a ventilátorok teljesítményével kapcsolatban, vegye fontolóra a tevékenység funkcióinak [több alfolyamaton](durable-functions-sub-orchestrations.md)belüli felosztását.

A következő táblázat a korábban ismertetett forgatókönyvek várható *maximális* átviteli számát mutatja. A "példány" egy Orchestrator függvény egyetlen példányára hivatkozik, amely egy kisméretű ([a1](../../virtual-machines/sizes-previous-gen.md)) virtuális gépen fut Azure app Serviceban. A rendszer minden esetben feltételezi, hogy a [kiterjesztett munkamenetek](#orchestrator-function-replay) engedélyezve vannak. A tényleges eredmények a kód által végzett CPU-vagy I/O-műveletektől függően változhatnak.

| Eset | Maximális átviteli sebesség |
|-|-|
| Szekvenciális tevékenységek végrehajtása | 5 tevékenység másodpercenként, a példányok száma szerint |
| Párhuzamos tevékenységek végrehajtása (ventilátor – kimenő) | 100 tevékenység/másodperc/példány |
| Párhuzamos reagálások feldolgozása (ventilátor) | 150 válasz másodpercenként, a példányok száma szerint |
| Külső események feldolgozása | 50 esemény másodpercenként, a példányok száma szerint |
| Entitás műveletének feldolgozása | 64 művelet másodpercenként |

> [!NOTE]
> Ezek a számok a Durable Functions bővítmény v 1.4.0 (GA) kiadásában aktuálisak. Ezek a számok idővel megváltozhatnak, amikor a funkció kiforr, és optimalizálást végez.

Ha nem látja a várt átviteli sebességet, és a CPU és a memóriahasználat állapota Kifogástalan, ellenőrizze, hogy az ok a [Storage-fiók állapotára](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#troubleshooting-guidance)vonatkozik-e. Az Durable Functions bővítmény jelentős terhelést helyezhet el egy Azure Storage-fiókban, és a megfelelő terhelés miatt a Storage-fiókok szabályozása is lehetséges.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Tudnivalók a vész-helyreállításról és a földrajzi eloszlásról](durable-functions-disaster-recovery-geo-distribution.md)
