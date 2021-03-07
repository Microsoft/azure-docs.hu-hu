---
title: Az átviteli sebesség optimalizálása Azure Cosmos DB
description: Ez a cikk azt ismerteti, hogyan optimalizálható a Azure Cosmos DBban tárolt adatok átviteli sebessége.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/07/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 0c95fc9e416399b5c8fe032e0d3af0c3b7f9cf6e
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102433573"
---
# <a name="optimize-provisioned-throughput-cost-in-azure-cosmos-db"></a>A kiosztott átviteli sebesség költségeinek optimalizálása az Azure Cosmos DB-ben
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

A kiépített átviteli sebességi modell használatával Azure Cosmos DB kiszámítható teljesítményt biztosít bármilyen méretben. Az átviteli sebesség megőrzése vagy kiépítés előtt az idő kiküszöböli a "zajos szomszéd" hatást a teljesítményre. Megadhatja a szükséges átviteli sebesség pontos mértékét, és Azure Cosmos DB garantálja a beállított átviteli sebességet, amely az SLA-ra vonatkozik.

Kezdődhet a minimális átviteli sebesség 400 RU/mp-ben, és akár több tízmillió kérést is beállíthat másodpercenként vagy akár többre. Minden, az Azure Cosmos-tárolón vagy-adatbázison kiállított kérelem, például olvasási kérelem, írási kérelem, lekérdezési kérelem, tárolt eljárások a kiosztott átviteli sebességtől számított megfelelő költségekkel rendelkeznek. Ha 400 RU/s-t hoz létre, és egy olyan lekérdezést ad ki, amely a 40 RUs költséggel jár, másodpercenként 10 ilyen lekérdezést fog kiadni. A fentieken túli kérések díjszabása korlátozott lesz, és újra kell próbálkoznia a kéréssel. Ha az ügyfél-illesztőprogramokat használja, az automatikus újrapróbálkozási logikát támogatja.

Az átviteli sebességet adatbázisok vagy tárolók között is kioszthatja, és a különféle stratégiákkal alacsonyabbak lehetnek a költségek a forgatókönyvtől függően.

## <a name="optimize-by-provisioning-throughput-at-different-levels"></a>Optimalizálás az átviteli sebesség különböző szinteken való kiépítés útján

* Ha egy adatbázison kiépíti az átviteli sebességet, az összes tároló, például a gyűjtemények/táblák/gráfok az adott adatbázison belül megoszthatják az átviteli sebességet a terhelés alapján. Az adatbázis szintjén fenntartott átviteli sebesség egyenlően oszlik meg, attól függően, hogy milyen munkaterhelést adott meg a tárolók adott készletén.

* Ha egy tárolón kiépíti az átviteli sebességet, az átviteli sebesség garantált az adott tároló számára, és az SLA-t is támogatja. A logikai partíciós kulcs kiválasztása elengedhetetlen a tároló összes logikai partícióján belüli terhelés elosztásához. További részletekért lásd: [particionálás](partitioning-overview.md) és [horizontális skálázási](partitioning-overview.md) cikkek.

Az alábbiakban néhány, a kiosztott átviteli sebességre vonatkozó stratégiát kell eldöntenie:

**Vegye fontolóra az átviteli sebesség kiszámítását egy Azure Cosmos-adatbázison (tárolók készletét tartalmazó), ha**:

1. Néhány tucat Azure Cosmos-tárolóval rendelkezik, és az átviteli sebességet szeretné megosztani egy vagy több között. 

2. Olyan egybérlős adatbázisból végez áttelepítést, amely IaaS üzemeltetett virtuális gépeken vagy helyszíni környezetben való futtatásra készült, például a NoSQL vagy a Azure Cosmos DB. Ha pedig sok gyűjtemény/táblázat/gráf van, és nem kívánja módosítani az adatmodellt. Vegye figyelembe, hogy a Azure Cosmos DB által kínált előnyök némelyikét meg kell sérteni, ha nem frissíti az adatmodellt egy helyszíni adatbázisból való Migrálás során. Javasoljuk, hogy az adatmodellt mindig újra hozzáférhessen, hogy a lehető legtöbbet hozza ki a teljesítmény szempontjából, és optimalizálja a költségeket is. 

3. A számítási feladatokban nem tervezett tüskéket kíván felvenni a számítási feladatokban a számítási feladatok váratlan terhelésének figyelembevételével. 

4. Az egyes tárolók egyedi átviteli sebességének beállítása helyett az összesített átviteli sebességet kell megszereznie az adatbázison belüli tárolók készletében.

**Vegye fontolóra az átviteli sebességet egy adott tárolón, ha:**

1. Néhány Azure Cosmos-tárolóval rendelkezik. Mivel Azure Cosmos DB a séma-agnosztikus, egy tároló olyan elemeket tartalmazhat, amelyek heterogén sémákkal rendelkeznek, és nem igénylik, hogy az ügyfelek több típusú tárolót hozzanak létre, egyet az egyes entitásokhoz. Mindig érdemes megfontolni, hogy a csoportosítás külön mondjuk 10-20 tárolót egyetlen tárolóba. Ha a tárolók számára legalább 400 RUs van, az összes 10-20-tárolót egyetlen költséghatékonyan egyesítheti. 

2. Egy adott tároló átviteli sebességét szeretné vezérelni, és egy adott tároló garantált átviteli sebességét kell lekérnie, amely SLA-t támogat.

**Vegye fontolóra a fenti két stratégia hibridjét:**

1. Ahogy korábban említettük, a Azure Cosmos DB lehetővé teszi a fenti két stratégia összekeverését és egyeztetését, így most már rendelkezik néhány tárolóval az Azure Cosmos-adatbázisban, amely megoszthatja az adatbázison kiosztott átviteli sebességet, valamint az ugyanazon az adatbázison belüli tárolókat, amelyekhez dedikált mennyiségű kiosztott átviteli sebesség tartozhat. 

2. A fenti stratégiák olyan hibrid konfigurációval is alkalmazhatók, ahol az adatbázis-szintű kiosztott átviteli sebesség is rendelkezésre áll, és egyes tárolók dedikált átviteli sebességgel rendelkeznek.

Ahogy az az alábbi táblázatban is látható, az API megválasztása alapján különböző részletességi szinten is kiépítheti az átviteli sebességet.

|API|**Megosztott** átviteli sebesség esetében konfigurálja a következőt: |**Dedikált** átviteli sebesség esetén konfigurálja a következőt: |
|----|----|----|
|SQL API|Adatbázis|Tároló|
|MongoDB-hez készült Azure Cosmos DB API|Adatbázis|Gyűjtemény|
|Cassandra API|Kulcstartomány|Tábla|
|Gremlin API|Adatbázisfiók|Graph|
|Table API|Adatbázisfiók|Tábla|

Az átviteli sebesség különböző szinteken való kiépítés révén a számítási feladatok jellemzői alapján optimalizálhatja költségeit. Ahogy azt korábban említettük, programozott módon és bármikor növelheti vagy csökkentheti a kiosztott átviteli sebességet az egyes tároló (k) esetében, vagy együttesen a különböző tárolók között. A számítási feladatok változásainak rugalmas skálázásával, csak a konfigurált átviteli sebességért kell fizetnie. Ha a tároló vagy a tárolók több régióban vannak elosztva, akkor a tárolón konfigurált átviteli sebesség és a tárolók halmaza garantáltan elérhető az összes régióban.

## <a name="optimize-with-rate-limiting-your-requests"></a>Optimalizálás a kérések sebességének korlátozásával

A késésre nem érzékeny munkaterhelések esetében kiépítheti a kevesebb átviteli sebességet, és engedélyezheti az alkalmazás-kezelői sebesség korlátozását, ha a tényleges átviteli sebesség meghaladja a kiosztott átviteli sebességet. A kiszolgáló a (429-es http-állapotkód) megelőző jelleggel befejezi a kérést, `RequestRateTooLarge` és visszaküldi a `x-ms-retry-after-ms` fejlécet, amely jelzi, hogy a felhasználónak mennyi idő elteltével kell megvárnia a kérés újrapróbálkozása előtt. 

```html
HTTP Status 429, 
 Status Line: RequestRateTooLarge 
 x-ms-retry-after-ms :100
```

### <a name="retry-logic-in-sdks"></a>Újrapróbálkozási logika az SDK-ban 

A natív SDK-k (.NET/.NET Core, Java, Node.js és Python) implicit módon elkapják ezt a választ, figyelembe veszik a kiszolgáló által megadott újrapróbálkozás utáni újrapróbálkozást, majd újra megpróbálkoznak a kéréssel. Ha a fiókját több ügyfél egyidejűleg nem fér hozzá, a következő újrapróbálkozás sikeres lesz.

Ha több ügyfél halmozottan működik, és a kérések aránya meghaladja a kérelmek arányát, akkor az újrapróbálkozások alapértelmezett száma, amely jelenleg 9, előfordulhat, hogy nem elegendő. Ilyen esetekben az ügyfél az `RequestRateTooLargeException` 429-as állapotkódot veti fel az alkalmazáshoz. Az újrapróbálkozások alapértelmezett száma módosítható a ConnectionPolicy-példányra való beállításával `RetryOptions` . Alapértelmezés szerint a `RequestRateTooLargeException` 429-as állapotkód a 30 másodperces kumulatív várakozási idő után tér vissza, ha a kérés továbbra is a kérelem arányán felül működik. Ez akkor is előfordul, ha a jelenlegi újrapróbálkozások száma kisebb, mint az újrapróbálkozások maximális száma, legyen az alapértelmezett 9-es vagy felhasználó által definiált érték. 

A [MaxRetryAttemptsOnThrottledRequests](/dotnet/api/microsoft.azure.documents.client.retryoptions.maxretryattemptsonthrottledrequests) értéke 3, tehát ebben az esetben ha egy kérési művelet a tároló számára fenntartott átviteli sebesség meghaladása miatt korlátozott, a kérési művelet háromszor újrapróbálkozik a kivételnek az alkalmazásba való eldobása előtt. A [MaxRetryWaitTimeInSeconds](/dotnet/api/microsoft.azure.documents.client.retryoptions.maxretrywaittimeinseconds#Microsoft_Azure_Documents_Client_RetryOptions_MaxRetryWaitTimeInSeconds) értéke 60, tehát ebben az esetben, ha az első kérelemnél nagyobb az újrapróbálkozási várakozási idő másodpercben, mivel az első kérés meghaladja a 60 másodpercet, a kivételt a rendszer eldobta.

```csharp
ConnectionPolicy connectionPolicy = new ConnectionPolicy(); 
connectionPolicy.RetryOptions.MaxRetryAttemptsOnThrottledRequests = 3; 
connectionPolicy.RetryOptions.MaxRetryWaitTimeInSeconds = 60;
```

## <a name="partitioning-strategy-and-provisioned-throughput-costs"></a>A particionálási stratégia és a kiosztott átviteli sebesség költségei

A megfelelő particionálási stratégia fontos az Azure Cosmos DB költségeinek optimalizálása érdekében. Győződjön meg arról, hogy a partíciók nincsenek elferdítve, amelyek a tárolási metrikákkal vannak kitéve. Győződjön meg arról, hogy a partíciók esetében nem áll rendelkezésre az átviteli sebesség, amely az átviteli mérőszámokkal van kitéve. Ügyeljen arra, hogy az adott partíciós kulcsok ne legyenek elferdítve. A tárolóban lévő domináns kulcsok a metrikák révén vannak kitéve, de a kulcs az alkalmazás-hozzáférési mintától függ. Érdemes meggondolni a logikai partíciók megfelelő kulcsát. A megfelelő partíciós kulcs várhatóan a következő tulajdonságokkal rendelkezik:

* Olyan partíciós kulcsot válasszon, amely egyenletesen osztja el a számítási feladatokat az összes partíción, és az idő múlásával. Más szóval nem kell bizonyos kulcsokat felvennie az adatmennyiséggel és néhány olyan kulccsal, amely kevesebb vagy semmilyen adattal rendelkezik. 

* Olyan partíciós kulcsot válasszon, amely lehetővé teszi, hogy a hozzáférési minták egyenletesen legyenek elosztva a logikai partíciók között. A számítási feladat az összes kulcs között ésszerűen is használható. Más szóval a munkaterhelés többsége nem koncentrálhat néhány konkrét kulcsra. 

* Válasszon olyan partíciós kulcsot, amely számos értéket tartalmaz. 

Az alapvető elképzelés az adatok és a tárolóban lévő tevékenységek elosztása a logikai partíciók készletében, így az adattárolásra és az átviteli sebességre vonatkozó erőforrások eloszthatók a logikai partíciók között. A partíciós kulcsok pályázói tartalmazhatják a lekérdezésekben gyakran megjelenő tulajdonságokat. A lekérdezések hatékony átirányításához a partíciókulcsot foglalja bele a szűrő predikátumába. A particionálási stratégia révén a kiépített átviteli sebesség sokkal egyszerűbb lesz. 

### <a name="design-smaller-items-for-higher-throughput"></a>Kisebb elemek tervezése nagyobb átviteli sebességhez 

Egy adott művelet kérésének díja vagy a kérelmek feldolgozási díja közvetlenül összefügg az elemek méretével. A nagyméretű elemeken végrehajtott műveletek a kisebb elemeknél több művelettel járnak. 

## <a name="data-access-patterns"></a>Adatelérési minták 

Mindig érdemes logikusan elkülöníteni az adatait logikai kategóriákba, attól függően, hogy milyen gyakran férhet hozzá az adataihoz. A gyakori, közepes vagy hideg adatok kategorizálásával finomíthatja a felhasznált tárterületet és a szükséges teljesítményt. A hozzáférés gyakorisága függvényében az adatok külön tárolóba helyezhetők (például táblázatok, diagramok és gyűjtemények), és a kiépített átviteli sebesség finomhangolása az adatok adott szegmensének igényei szerint. 

Emellett, ha Azure Cosmos DBt használ, és tudja, hogy nem bizonyos adatértékek alapján keres, vagy csak ritkán fér hozzájuk, akkor ezeket az attribútumok tömörített értékeit kell tárolnia. Ezzel a módszerrel mentheti a tárterületet, az indexet és a kiépített átviteli sebességet, és alacsonyabb költségeket eredményezhet.

## <a name="optimize-by-changing-indexing-policy"></a>Optimalizálás az indexelési szabályzat módosításával 

Alapértelmezés szerint a Azure Cosmos DB automatikusan indexel minden rekord összes tulajdonságát. Ennek célja, hogy megkönnyítse a fejlesztést, és kiváló teljesítményt biztosítson számos különböző típusú ad hoc lekérdezésben. Ha nagy mennyiségű, több ezer tulajdonságú rekorddal rendelkezik, akkor nem lehet hasznos, ha az indexelési költségeket nem érdemes kifizetni, különösen akkor, ha csak 10 vagy 20 ilyen tulajdonságot szeretne lekérdezni. Ahogy közelebb kerül az adott számítási feladathoz, az útmutatónk szerint hangoljuk be az index-szabályzatot. Azure Cosmos DB indexelési szabályzat részletes adatai [itt](index-policy.md)találhatók. 

## <a name="monitoring-provisioned-and-consumed-throughput"></a>Kiépített és felhasznált átviteli sebesség figyelése 

Nyomon követheti a kiépített RUs teljes számát, a korlátozott számú kérelmek számát, valamint a Azure Portal felhasznált RUs számát. Az alábbi képen egy példa használati metrika látható:

:::image type="content" source="./media/optimize-cost-throughput/monitoring.png" alt-text="A kérések egységeinek figyelése a Azure Portal":::

Riasztásokat is beállíthat, hogy ellenőrizze, hogy a korlátozott kérelmek száma meghaladja-e a megadott küszöbértéket. További részletekért tekintse [meg a Azure Cosmos db cikk figyelését](use-metrics.md) ismertető cikket. Ezek a riasztások e-mailt küldhetnek a fiók rendszergazdái számára, vagy meghívhatnak egy egyéni HTTP-webhookot vagy egy Azure-függvényt a kiépített átviteli sebesség automatikus növelésére. 

## <a name="scale-your-throughput-elastically-and-on-demand"></a>Az átviteli sebesség rugalmas és igény szerinti méretezése 

Mivel a kiosztott átviteli sebességért számítunk fel díjat, a kiépített átviteli sebességnek megfelelően kell megelőznie a fel nem használt átviteli sebességet. A kiosztott átviteli sebességet igény szerint bármikor fel-vagy lekicsinyítheti. Ha az átviteli sebesség igénye nagyon kiszámítható, használhat Azure Functions, és időzítő trigger használatával [növelheti vagy csökkentheti az átviteli sebességet](scale-on-schedule.md). 

* Az RUs felhasználásának figyelése és a korlátozott számú kérelem aránya azt mutatja, hogy a nap folyamán vagy a héten nem kell folyamatosan kiépíteni az állandókat. Előfordulhat, hogy kevesebb forgalmat kell megkapnia éjjel vagy a hétvégén. Azure Portal vagy Azure Cosmos DB natív SDK-k vagy REST API segítségével bármikor méretezheti a kiosztott átviteli sebességet. A Azure Cosmos DB REST API végpontokat biztosít a tárolók teljesítményi szintjének programozott frissítéséhez, így egyszerűvé teszi az átviteli sebesség beállítását a kód napjától vagy a hét napjától függően. A művelet leállás nélkül történik, és általában kevesebb, mint egy perc alatt lép érvénybe. 

* Az adatátviteli sebesség az egyik terület, amikor az adatok betöltését Azure Cosmos DBba, például az adatáttelepítés során. Miután befejezte az áttelepítést, a kiépített átviteli sebességet Lekicsinyítve kezelheti a megoldás állandó állapotát.  

* Ne feledje, hogy a számlázás egy órás részletességgel rendelkezik, így nem fog pénzt spórolni, ha a kiosztott átviteli sebességet gyakrabban, mint egy órát módosítják.

## <a name="determine-the-throughput-needed-for-a-new-workload"></a>Az új munkaterheléshez szükséges átviteli sebesség meghatározása 

Az új munkaterhelés kiépített átviteli sebességének meghatározásához a következő lépéseket használhatja: 

1. Végezzen el egy kezdeti, durva értékelést a Capacity Planner használatával, és állítsa be a becsléseket a Azure Portal Azure Cosmos DB Explorer segítségével. 

2. Azt javasoljuk, hogy a tárolókat a vártnál magasabb átviteli sebességgel hozza létre, majd szükség szerint méretezéssel. 

3. Javasoljuk, hogy a natív Azure Cosmos DB SDK-k egyikét használja az automatikus újrapróbálkozások kihasználása érdekében, amikor a kérelmek díjszabása korlátozott. Ha olyan platformon dolgozik, amely nem támogatott, és nem használja a Cosmos DB REST API, akkor a fejléc használatával hajtsa végre a saját újrapróbálkozási házirendjét `x-ms-retry-after-ms` . 

4. Győződjön meg arról, hogy az alkalmazás kódja szabályosan támogatja az esetet, amikor az összes újrapróbálkozás sikertelen lesz. 

5. A Azure Portal riasztásait úgy is konfigurálhatja, hogy a díjszabásra vonatkozó értesítéseket kapjon. Az elmúlt 15 percben megjelenő konzervatív korlátokkal, például a 10 sebességre korlátozott kérelmekkel kezdődhet, és a tényleges felhasználás megállapítása után átválthat több lelkes szabályra. Alkalmankénti díjszabás – a határértékek megfelelnek a beállított korlátoknak, és pontosan ezt kívánja elvégezni. 

6. A figyelés használatával megismerheti a forgalmi mintát, így érdemes megfontolnia, hogy dinamikusan kell módosítania az átviteli sebesség kiosztását a nap folyamán vagy hetente. 

7. Figyelje a kiépített és felhasznált átviteli sebesség arányát, és győződjön meg arról, hogy a szükséges számú tárolót és adatbázist nem osztotta ki. A kiosztott átviteli sebesség egy kis mértékben biztonságos ellenőrzés.  

### <a name="best-practices-to-optimize-provisioned-throughput"></a>Ajánlott eljárások a kiépített átviteli sebesség optimalizálásához 

A következő lépések segítségével a megoldásait rugalmasan méretezhető és költséghatékonyan teheti meg Azure Cosmos DB használatakor.  

1. Ha jelentősen meghaladja a tárolók és adatbázisok kiépített átviteli sebességét, tekintse át az RUs által kiépített vs felhasznált RUs-t, és finomítsa a számítási feladatokat.  

2. Az alkalmazás által igényelt fenntartott átviteli sebesség becslésének egyik módszere az, hogy rögzítse az alkalmazás által használt, jellemzően az Azure Cosmos-tárolón vagy-adatbázison alapuló, tipikus műveletekhez tartozó, a másodpercenkénti műveletek elvégzéséhez várhatóan elvégezhető műveletek számát. Ügyeljen arra, hogy a szokásos lekérdezéseket és azok használatát is mérje fel és vegye fel. Ha szeretné megtudni, hogyan becsülheti meg a lekérdezések RU-díjait programozott módon vagy a portál használatával, tekintse meg [a lekérdezési költségek optimalizálása](./optimize-cost-reads-writes.md)című témakört. 

3. A műveletek és azok költségeinek egy másik módja, ha engedélyezi a Azure Monitor-naplókat, így a művelet/időtartam és a kérések díjszabása is elérhető. A Azure Cosmos DB minden művelethez megadja a kérelmek díját, így minden műveleti díj visszatárolható a válaszból, majd elemzésre használható. 

4. A számítási teljesítmény igényének kielégítése érdekében a kiosztott átviteli sebesség rugalmasan méretezhető. 

5. Az Azure Cosmos-fiókjához tartozó régiókat a szükséges módon veheti fel és távolíthatja el, és szabályozhatja a költségeket. 

6. Győződjön meg arról, hogy az adatok és a munkaterhelések elosztása is megtörtént a tárolók logikai partíciói között. Ha a partíciók eloszlása egyenetlen, ez a szükségesnél nagyobb átviteli sebességet eredményezhet. Ha azt állapítja meg, hogy ferde eloszlással rendelkezik, javasoljuk, hogy egyenletesen osztja el a munkaterhelést a partíciók között, vagy particionálja újra az adatok mennyiségét. 

7. Ha sok tárolóval rendelkezik, és ezek a tárolók nem igénylik a SLA-kat, akkor használhatja az adatbázis-alapú ajánlatot azokra az esetekre, amikor a/Container átviteli sebesség SLA-kat nem kell alkalmazni. Meg kell határoznia, hogy az Azure Cosmos-tárolók melyikét szeretné áttelepíteni az adatbázis-szint átviteli sebességre, majd áttelepíteni őket egy Change feed-alapú megoldás használatával. 

8. Vegye fontolóra a "Cosmos DB ingyenes szintet" (egy évig ingyenes), próbálkozzon Cosmos DB (legfeljebb három régióval), vagy letölthető Cosmos DB Emulator fejlesztési és tesztelési forgatókönyvekhez. Ha ezeket a lehetőségeket a test-dev szolgáltatáshoz használja, jelentősen csökkentheti költségeit.  

9. Továbbra is elvégezheti a munkaterhelés-specifikus költségmegtakarításokat – például növeli a Batch-méretet, a terheléselosztási beolvasást több régióban, és visszaduplikálja az adatokat, ha van ilyen.

10. Azure Cosmos DB fenntartott kapacitással három évig akár 65%-os kedvezményt érhet el. Azure Cosmos DB fenntartott kapacitási modell előzetes kötelezettségvállalás a kérések egysége számára az idő múlásával. A kedvezményeket úgy kell megválasztani, hogy minél több kérési egységet használjanak hosszabb időszakra, annál nagyobb a kedvezmény. Ezeket a kedvezményeket azonnal alkalmazza a rendszer. A kiépített értékek fölött használt összes RUs díja a nem fenntartott kapacitás díja alapján történik. További részletekért tekintse meg [Cosmos db fenntartott kapacitást](cosmos-db-reserved-capacity.md)). Vegye fontolóra a fenntartott kapacitás megvásárlását, hogy tovább csökkentse a kiosztott átviteli sebességet.  

## <a name="next-steps"></a>Következő lépések

A következő cikkekben további tudnivalókat talál a Azure Cosmos DB a Cost optimizationról:

* További információ a [fejlesztés és a tesztelés optimalizálásáról](optimize-dev-test.md)
* További információ [a Azure Cosmos db-számla megismeréséről](understand-your-bill.md)
* További információ a [tárolási díjak optimalizálásáról](optimize-cost-storage.md)
* További információ [az olvasási és írási díjak optimalizálásáról](optimize-cost-reads-writes.md)
* További információ [a lekérdezések díjszabásának optimalizálásáról](./optimize-cost-reads-writes.md)
* További információ [a több régióból álló Azure Cosmos-fiókok díjainak optimalizálásáról](optimize-cost-regions.md)
