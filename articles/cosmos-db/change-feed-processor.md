---
title: A változáscsatorna feldolgozója az Azure Cosmos DB-ben
description: Megtudhatja, hogyan használhatja a Azure Cosmos DB Change feed processzort a változási hírcsatorna beolvasására, a változási hírcsatorna processzorának összetevőire
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 10/12/2020
ms.reviewer: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: 409b51682700a8b13b2840f171642bdcbee6f6d2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93340226"
---
# <a name="change-feed-processor-in-azure-cosmos-db"></a>A változáscsatorna feldolgozója az Azure Cosmos DB-ben
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

A Change feed processzor a [Azure Cosmos db SDK v3](https://github.com/Azure/azure-cosmos-dotnet-v3)részét képezi. Leegyszerűsíti a változási csatorna olvasásának folyamatát, és az események feldolgozását a több fogyasztó számára is hatékonyan terjeszti.

A Change feed Processor Library fő előnye a hibatűrő viselkedés, amely biztosítja a változási hírcsatornában lévő összes esemény "legalább egyszeri" kézbesítését.

## <a name="components-of-the-change-feed-processor"></a>A módosítási hírcsatorna processzorának összetevői

A változáscsatorna-feldolgozó implementálásának négy fő összetevője van:

1. **A monitorozott tároló:** A monitorozott tároló tartalmazza azokat az adatokat, amelyekből létrejön a változáscsatorna. A monitorozott tárolóba való beszúrások és a tároló frissítései megjelennek a tároló változáscsatornájában.

1. **A bérlettároló:** A bérlettároló állapottárolóként működik, és több feldolgozó között koordinálja a változáscsatorna feldolgozását. A bérlettároló tárolható ugyanabban a fiókban, mint a monitorozott tároló, de akár külön fiókban is.

1. **A gazdagép:** A gazdagép egy olyan alkalmazáspéldány, amely a változáscsatorna feldolgozóját használja a módosítások figyeléséhez. Az azonos bérletkonfigurációval rendelkező példányok párhuzamosan is futhatnak, de minden példánynak más **példánynévvel** kell rendelkeznie.

1. **A delegált:** A delegált az a kód, amely meghatározza, hogy Ön, mint fejlesztő mit szeretne tenni azon egyes módosításkötegekkel, amelyeket a változáscsatorna feldolgozója beolvas. 

Ha szeretné jobban megismerni, hogy a változási hírcsatorna processzorának négy eleme hogyan működik együtt, tekintsük át az alábbi ábrán látható példát. A figyelt tároló tárolja a dokumentumokat, és a "City" partíciót használja a partíciós kulcsként. Láthatjuk, hogy a partíciós kulcs értékei az elemeket tartalmazó tartományokban vannak elosztva. Két gazdagép-példány létezik, és a módosítási hírcsatorna processzora különböző tartományokat rendel az egyes példányokhoz a számítási eloszlás maximalizálása érdekében. Minden tartományt párhuzamosan kell beolvasni, és az előrehaladását a bérlet tárolójában lévő többi tartománytól elkülönítve kell karbantartani.

:::image type="content" source="./media/change-feed-processor/changefeedprocessor.png" alt-text="Példa a hírcsatorna processzorának módosítására" border="false":::

## <a name="implementing-the-change-feed-processor"></a>A változási csatorna processzorának implementálása

A belépési pont mindig a figyelt tároló, amely egy `Container` meghívott példányból áll `GetChangeFeedProcessorBuilder` :

[!code-csharp[Main](~/samples-cosmosdb-dotnet-change-feed-processor/src/Program.cs?name=DefineProcessor)]

Ahol az első paraméter egy különálló név, amely leírja a processzor célját, a második pedig a módosításokat kezelő delegált implementáció. 

Egy delegált példa:


[!code-csharp[Main](~/samples-cosmosdb-dotnet-change-feed-processor/src/Program.cs?name=Delegate)]

Végül meghatároz egy nevet ehhez a processzor-példányhoz a `WithInstanceName` és a használatával, amely a bérlet állapotának fenntartására szolgáló tároló `WithLeaseContainer` .

A hívással megadhatja `Build` a processzor azon példányát, amelyet elindíthat a hívásával `StartAsync` .

## <a name="processing-life-cycle"></a>Életciklus feldolgozása

A gazdagéppéldány normál életciklusa:

1. Olvassa el a változási csatornát.
1. Ha nincsenek változások, Aludjon előre meghatározott ideig (testreszabható a `WithPollInterval` -ben a Builder-ben), és lépjen a #1.
1. Ha vannak változások, küldje el azokat a **delegált** számára.
1. Ha a delegálás **sikeresen** dolgozza fel a módosításokat, frissítse a címbérleti tárolót a legutóbbi feldolgozott időponttal, és lépjen #1.

## <a name="error-handling"></a>Hibakezelés

A módosítási hírcsatorna processzora a felhasználói kódok hibáira is rugalmas. Ez azt jelenti, hogy ha a delegált implementációja kezeletlen kivételt tartalmaz (#4. lépés), akkor a rendszer leállítja a szál feldolgozását, és létrehoz egy új szálat. Az új szál azt vizsgálja, hogy a címbérleti tároló milyen legkésőbbi időpontot adott a partíciós kulcs értékeinek, és onnan indítsa újra a rendszert, és így gyakorlatilag ugyanazt a köteget küldi el a delegált állapotnak. Ez a viselkedés mindaddig folytatódni fog, amíg a delegált nem dolgozza fel megfelelően a módosításokat, és ez az oka, hogy a változási hírcsatorna processzorának "legalább egyszer" garanciája van, mert ha a delegált kód kivételt jelez, akkor a köteget újra próbálkozik.

Ha meg szeretné akadályozni, hogy a Change feed processzora "ragadt" állapotba lépjen, és ne próbálkozzon ugyanazzal a módosítással, adja hozzá a meghatalmazott kódjában található logikát, hogy a rendszer kivétel esetén a kézbesítetlen levelek várólistáján jegyezze fel a dokumentumokat. Ez a kialakítás biztosítja, hogy nyomon követheti a feldolgozatlan módosításokat, miközben továbbra is képes tovább dolgozni a jövőbeli változásokat. Lehet, hogy a kézbesítetlen levelek várólistája egy másik Cosmos-tároló. A pontos adattár nem számít, csupán azt, hogy a feldolgozatlan módosítások megmaradnak.

Emellett a váltás a hírcsatorna- [kalkulátor](how-to-use-change-feed-estimator.md) használatával című cikk segítségével figyelheti a változási csatornához tartozó példányok állapotát, ahogy beolvasták a változási csatornát. Ezzel a becsléssel megtudhatja, hogy a változási csatorna processzora "ragadt" vagy lemaradt-e a rendelkezésre álló erőforrások, például a CPU, a memória és a hálózati sávszélesség miatt.

## <a name="deployment-unit"></a>Üzembe helyezési egység

Egyetlen módosítási hírcsatorna-feldolgozó egység egy vagy több példányból áll, amelyek azonos `processorName` és címbérleti tároló-konfigurációval rendelkeznek. Több üzembe helyezési egység is megadható, amelyek mindegyike különböző üzleti folyamattal rendelkezik a változásokhoz és minden központi telepítési egységhez, amely egy vagy több példányból áll. 

Előfordulhat például, hogy egy olyan központi telepítési egységgel rendelkezik, amely egy külső API-t indít el bármikor, ha megváltozik a tárolóban. Egy másik telepítési egység valós időben helyezheti át az adatátvitelt, valahányszor módosul a változás. Ha a figyelt tárolóban változás történik, az összes üzembe helyezési egység értesítést kap.

## <a name="dynamic-scaling"></a>Dinamikus méretezés

Ahogy korábban említettük, egy üzembe helyezési egységen belül egy vagy több példány is megadható. A központi telepítési egységen belüli számítási eloszlás kihasználása érdekében az egyetlen kulcsfontosságú követelmény a következő:

1. Minden példánynak ugyanazzal a bérlettároló-konfigurációval kell rendelkeznie.
1. Minden példánynak azonosnak kell lennie `processorName` .
1. Minden példánynak különböző példánynévvel kell rendelkeznie (`WithInstanceName`).

Ha ezt a három feltételt alkalmazza, akkor a módosítási hírcsatorna processzora egyenlő terjesztési algoritmussal osztja el a bérleti tároló összes bérletét az adott üzembe helyezési egység összes futó példánya és a integrálással számítási szolgáltatás között. Egy bérlet csak egy példány tulajdonosa lehet egy adott időpontban, így a példányok maximális száma megegyezik a bérletek számával.

A példányok száma növelhető és csökkenthető, és a módosítási hírcsatorna processzora a megfelelő újraterjesztéssel dinamikusan módosítja a terhelést.

Emellett a változási hírcsatorna processzora dinamikusan alkalmazkodik a tárolók méretezéséhez, az átviteli sebesség vagy a tárterület növekedése miatt. Ha a tároló növekszik, a változási hírcsatorna-feldolgozó transzparens módon kezeli ezeket a forgatókönyveket a bérletek dinamikus növelésével és az új bérletek meglévő példányok közötti elosztásával.

## <a name="change-feed-and-provisioned-throughput"></a>A hírcsatorna és a kiosztott átviteli sebesség módosítása

A figyelt tárolón a hírcsatorna olvasási műveleteinek módosítása az RUs-t fogja használni. 

A címbérleti tároló műveletei a RUs-t használják. Minél nagyobb a példányok száma ugyanazzal a bérlet-tárolóval, annál nagyobb a potenciális RU-felhasználás. Ne felejtse el figyelni az RU-felhasználást a bérletek tárolón, ha úgy dönt, hogy méretezi és növeli a példányok számát.

## <a name="starting-time"></a>Kezdés időpontja

Alapértelmezés szerint, amikor a változási hírcsatorna processzora először indul el, inicializálja a bérletek tárolóját, és megkezdi a [feldolgozási életciklusát](#processing-life-cycle). A figyelt tárolóban történt minden olyan módosítást, amely a változási hírcsatorna processzorának első inicializálása előtt történt, a rendszer nem észleli.

### <a name="reading-from-a-previous-date-and-time"></a>Olvasás egy korábbi dátumból és időpontból

A módosítási hírcsatorna processzora inicializálható úgy, hogy egy **adott dátummal és** időponttal kezdődő módosításokat olvasson be, az a példányának átadásával `DateTime` a `WithStartTime` Builder bővítménnyel:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=TimeInitialization)]

A módosítási hírcsatorna processzora az adott dátumra és időpontra lesz inicializálva, és megkezdi az azt követő módosítások olvasását.

### <a name="reading-from-the-beginning"></a>Olvasás az elejétől

Más forgatókönyvekben, például az adatáttelepítés során vagy egy tároló teljes előzményeinek elemzéséhez a **tároló élettartamának elejéről** kell olvasni a változási csatornát. Ehhez használhatja a `WithStartTime` Builder bővítményt, de a továbbítást `DateTime.MinValue.ToUniversalTime()` , amely a minimális érték UTC szerinti megjelenítését eredményezi, például `DateTime` :

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartFromBeginningInitialization)]

A módosítási hírcsatorna processzora inicializálva lesz, és megkezdi a módosítások olvasását a tároló élettartamának elejétől kezdve.

> [!NOTE]
> Ezek a testreszabási beállítások csak akkor működnek, ha a változási csatorna processzorának kiindulási pontját szeretné beállítani. Ha a bérletek tárolója első alkalommal lett inicializálva, a módosításnak nincs hatása.

## <a name="where-to-host-the-change-feed-processor"></a>A módosítási hírcsatorna processzorának üzemeltetése

A módosítási hírcsatorna processzora bármely olyan platformon üzemeltethető, amely támogatja a hosszú ideig futó folyamatokat vagy feladatokat:

* Folyamatos futtatású [Azure-webjobs](/learn/modules/run-web-app-background-task-with-webjobs/).
* Egy folyamat egy Azure-beli [virtuális gépen](/azure/architecture/best-practices/background-jobs#azure-virtual-machines).
* A háttérben futó feladatok az [Azure Kubernetes szolgáltatásban](/azure/architecture/best-practices/background-jobs#azure-kubernetes-service).
* Egy [ASP.net által üzemeltetett szolgáltatás](/aspnet/core/fundamentals/host/hosted-services).

Míg a változási csatorna processzora rövid életű környezetekben futtatható, mivel a címbérleti tároló fenntartja az állapotot, a környezetek indítási ciklusa késleltetést ad az értesítések fogadásához (a processzor a környezet indításakor fellépő terhelés miatt).

## <a name="additional-resources"></a>További források

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [Minta alkalmazás teljes alkalmazása a GitHubon](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)
* [További használati minták a GitHubon](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [Cosmos DB workshop Labs az adatcsatornás processzor módosításához](https://azurecosmosdb.github.io/labs/dotnet/labs/08-change_feed_with_azure_functions.html#consume-cosmos-db-change-feed-via-the-change-feed-processor)

## <a name="next-steps"></a>Következő lépések

A következő cikkekben további tudnivalókat olvashat a hírcsatorna-feldolgozó szolgáltatással kapcsolatos változásokról:

* [A hírcsatorna változásának áttekintése](change-feed.md)
* [Változáscsatorna lekérési modellje](change-feed-pull-model.md)
* [Áttelepítés a Change feed Processor Library webhelyről](how-to-migrate-from-change-feed-library.md)
* [A változáscsatorna-becslő használata](how-to-use-change-feed-estimator.md)
* [Változáscsatorna-feldolgozó indításának időpontja](#starting-time)