---
title: A hírcsatorna kialakítási mintáinak módosítása Azure Cosmos DB
description: A Common Change feed tervezési mintáinak áttekintése
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: 443d00e61e593daacca04a4451b90bb78cc7d854
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93334585"
---
# <a name="change-feed-design-patterns-in-azure-cosmos-db"></a>A hírcsatorna kialakítási mintáinak módosítása Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

A Azure Cosmos DB változási hírcsatorna lehetővé teszi a nagy adatkészletek hatékony feldolgozását nagy mennyiségű írási művelettel. A Change feed a teljes adatkészlet lekérdezését is lehetővé teszi a megváltozott változások azonosítására. Ez a dokumentum a gyakori módosítási adatcsatornák kialakítási mintáit, a tervezési kompromisszumokat és a hírcsatornák korlátozásait összpontosítja.

A Azure Cosmos DB kiválóan alkalmas a IoT, a játékok, a kiskereskedelmi és az operatív naplózási alkalmazások számára. Ezeknek az alkalmazásoknak a közös kialakítási mintája az, hogy a további műveletek elindításához használja az adatmódosításokat. Példák a további műveletekre:

* Értesítés vagy API meghívásának indítása, amikor egy elem be van helyezve vagy frissül.
* Valós idejű adatfolyam-feldolgozás a IoT vagy a valós idejű elemzési folyamatok esetében az operatív adatfeldolgozás során.
* Adatáthelyezés, például egy gyorsítótár, egy keresőmotor, egy adattárház vagy egy hideg tárolóhely szinkronizálása.

A Azure Cosmos DB változási csatornája lehetővé teszi, hogy hatékony és méretezhető megoldásokat hozzon létre az egyes mintákhoz, ahogy az alábbi képen is látható:

:::image type="content" source="./media/change-feed/changefeedoverview.png" alt-text="A Azure Cosmos DB változási csatornájának használata valós idejű elemzési és eseményvezérelt számítástechnikai forgatókönyvekhez" border="false":::

## <a name="event-computing-and-notifications"></a>Esemény-számítástechnika és értesítések

A Azure Cosmos DB változási hírcsatorna leegyszerűsítheti az olyan forgatókönyveket, amelyeknek egy adott esemény alapján be kell indítaniuk egy értesítést, vagy hívást kell küldeniük egy API-nak. Az [adatcsatorna módosítása](change-feed-processor.md) lehetőséggel automatikusan lekérdezheti a tárolót a változásokhoz, és minden alkalommal meghívhat egy külső API-t, amikor van írási vagy frissítési művelet.

Az értesítéseket szelektíven is elindíthatja, vagy megadhatja az API-nak adott feltételek alapján történő hívását. Ha például a változási csatornát a [Azure functions](change-feed-functions.md)használatával olvassa, a függvénybe úgy helyezheti el a logikát, hogy csak akkor küldjön értesítést, ha teljesül egy adott feltétel. Míg az Azure-függvény kódja az egyes írások és frissítések során végrehajtódik, az értesítést csak akkor küldi el a rendszer, ha a megadott feltételek teljesültek.

## <a name="real-time-stream-processing"></a>Valós idejű adatfolyam-feldolgozás

A Azure Cosmos DB változási csatornával valós idejű adatfolyam-feldolgozást lehet használni a IoT vagy a valós idejű elemzési feldolgozáshoz az operatív adatműveletekben.
Előfordulhat például, hogy az eszközökről, érzékelőkről, infrastruktúrából és alkalmazásokból fogadja és tárolja az események adatait, és valós időben dolgozza fel ezeket az eseményeket a [Spark](../hdinsight/spark/apache-spark-overview.md)használatával. Az alábbi képen látható, hogyan valósítható meg a lambda architektúra a Azure Cosmos DB használatával a Change feeden keresztül:

:::image type="content" source="./media/change-feed/lambda.png" alt-text="Azure Cosmos DB-alapú lambda-folyamat a betöltéshez és a lekérdezéshez" border="false":::

Sok esetben a stream-feldolgozási implementációk először nagy mennyiségű bejövő adatsort kapnak egy ideiglenes üzenetsor-várólistába, például az Azure Event hub-ba vagy a Apache Kafkaba. A változási hírcsatorna nagyszerű alternatíva, mivel Azure Cosmos DB az adatfeldolgozás tartós, alacsony olvasási és írási késéssel való ellátásának lehetősége. Az üzenet-várólista Azure Cosmos DB változásának előnyei a következők:

### <a name="data-persistence"></a>Adatmegőrzés

A Azure Cosmos DBba írt adat megjelenik a változási hírcsatornában, és a törlésig megőrzi őket. Az üzenetsor-várólisták általában maximális megőrzési időtartammal rendelkeznek. Az [Azure Event hub](https://azure.microsoft.com/services/event-hubs/) például 90 napos maximális adatmegőrzést biztosít.

### <a name="querying-ability"></a>Lekérdezési képesség

A Cosmos-tároló változási csatornájának olvasásán kívül SQL-lekérdezéseket is futtathat a Azure Cosmos DBban tárolt adatokon. A változási csatorna nem duplikálja a tárolóban lévő adatmennyiséget, hanem inkább egy másik mechanizmust az adatolvasáshoz. Ezért ha a változási hírcsatornából olvassa be az adatait, az mindig konzisztens lesz az azonos Azure Cosmos DB tárolóval rendelkező lekérdezésekkel.

### <a name="high-availability"></a>Magas rendelkezésre állás

A Azure Cosmos DB akár 99,999%-os olvasási és írási rendelkezésre állást kínál. A sok üzenetsor miatt a Azure Cosmos DBi az adatmennyiséget egyszerűen globálisan eloszthatja és konfigurálhatja a [RTO (helyreállítási idő célkitűzés)](./consistency-levels.md#rto) nulla értékkel.

A változási hírcsatorna elemeinek feldolgozása után létrehozhat egy jelentős nézetet, és visszaállíthatja az összesített értékeket Azure Cosmos DB. Ha Azure Cosmos DBt használ egy játék létrehozásához, például a változási hírcsatorna használatával valós idejű ranglistákat hozhat létre a befejezett játékok eredményei alapján.

## <a name="data-movement"></a>Adatáthelyezés

A változási csatornát is beolvashatja a valós idejű adatáthelyezéshez.

A változási hírcsatorna például a következő feladatok hatékony elvégzését teszi lehetővé:

* A gyorsítótár, a keresési index vagy az adatraktár frissítése a Azure Cosmos DBban tárolt értékekkel.

* Nulla idejű áttelepítést végezhet egy másik Azure Cosmos-fiókba vagy egy másik Azure Cosmos-tárolóba egy másik logikai partíciós kulccsal.

* Alkalmazás-szintű adatréteget és archiválást valósíthat meg. Tárolhatja például a "forró adatok" kifejezést a Azure Cosmos DBban, és a "hideg adatok" állapotot más tárolási rendszerekre (például az [Azure Blob Storage](../storage/common/storage-introduction.md)).

Ha a [partíciók és tárolók közötti adatok denormalizálása](how-to-model-partition-example.md#v2-introducing-denormalization-to-optimize-read-queries
)mellett van szüksége, a tároló változási csatornáját az adatreplikálás forrásaként is elolvashatja. A változási hírcsatornával való valós idejű adatreplikálás csak a végleges konzisztencia biztosítására használható. [Megfigyelheti, hogy a változási hírcsatorna processzora milyen messze marad](how-to-use-change-feed-estimator.md) a Cosmos-tároló változásainak feldolgozása közben.

## <a name="event-sourcing"></a>Esemény-beszerzés

Az [esemény-beszerzési minta](/azure/architecture/patterns/event-sourcing) egy csak Hozzáfűzéses tároló használatával rögzíti az adatokon végzett műveletek teljes sorozatát. A Azure Cosmos DB változási csatornája az események beszerzési architektúráinak központi adattára, ahol az összes adatfeldolgozás az írásoknak megfelelően van modellezve (nincs frissítés vagy törlés). Ebben az esetben minden Azure Cosmos DB egy "esemény", amely a változási hírcsatornában található múltbeli eseményekről tartalmaz teljes rekordot. A központi esemény-áruház által közzétett események tipikus felhasználási módjai a jelentős nézetek fenntartása vagy a külső rendszerekkel való integráció. Mivel a változási hírcsatornában nincs időkorlát a megőrzéshez, az összes korábbi eseményt visszajátszhatja a Cosmos-tároló változási csatornájának elejéről.

[Több módosítási hírcsatorna-felhasználó is előfizethet ugyanarra a tároló változási csatornára](how-to-create-multiple-cosmos-db-triggers.md#optimizing-containers-for-multiple-triggers). A [címbérlet](change-feed-processor.md#components-of-the-change-feed-processor) kiépített átviteli sebességén kívül a változási csatornát nem kell használnia. A változási hírcsatorna minden tárolóban elérhető, függetlenül attól, hogy használatban van-e.

A Azure Cosmos DB az esemény-beszerzési minta nagy, központi Hozzáfűzéses, állandó adattára, a horizontális skálázhatóság és a magas rendelkezésre állás erőssége miatt. Emellett a változási hírcsatorna-feldolgozó függvénytár "legalább [egyszer"](change-feed-processor.md#error-handling) garanciát nyújt, így nem fogja kihagyni az események feldolgozását.

## <a name="current-limitations"></a>Aktuális korlátozások

A módosítási hírcsatorna fontos korlátozásokkal rendelkezik, amelyeket meg kell értenie. Míg a Cosmos-tárolóban lévő elemek mindig a változási hírcsatornában maradnak, a változási hírcsatorna nem teljes műveleti napló. Fontos területeket kell figyelembe venni a változási csatornát használó alkalmazások tervezésekor.

### <a name="intermediate-updates"></a>Köztes frissítések

A módosítási hírcsatorna csak az adott elemek legutóbbi módosítását tartalmazza. A változások feldolgozása során a legújabb elérhető elem-verziót fogja olvasni. Ha egy rövid időn belül több frissítés is van ugyanarra az elemre, akkor a köztes frissítések feldolgozását nem lehet kihagyni. Ha nyomon szeretné követni a frissítéseket, és képesnek kell lennie a korábbi frissítések egy elemre való visszajátszására, javasoljuk, hogy ezeket a frissítéseket az írások sorozatának megfelelően modellezi.

### <a name="deletes"></a>Törli

A módosítási hírcsatorna nem rögzíti a törléseket. Ha töröl egy elemet a tárolóból, azt a változási csatornából is eltávolítja. A leggyakoribb kezelési módszer, ha egy lágy jelölőt ad hozzá a törölt elemekhez. Hozzáadhat egy "törölt" nevű tulajdonságot, és a törlés időpontjában "true" értékre állíthatja azt. A dokumentum frissítése a változási hírcsatornában fog megjelenni. Ezen az elemen beállíthatja az ÉLETTARTAMot, hogy később is automatikusan törölhető legyen.

### <a name="guaranteed-order"></a>Garantált megrendelés

A módosítási hírcsatorna a partíciós kulcs értékén belül garantált, de a partíciós kulcs értékein kívül nem. Válasszon ki egy olyan partíciós kulcsot, amely a mérvadó rendelési garanciát biztosítja.

Vegyünk például egy kiskereskedelmi alkalmazást az esemény-beszerzési tervezési minta használatával. Ebben az alkalmazásban a különböző felhasználói műveletek mindegyike "Events", amely a Azure Cosmos DBba írt írásként van modellezve. Képzelje el, ha néhány példa történt a következő sorozatban:

1. Az ügyfél hozzáadja A "A" tételt a kosárhoz
2. Az ügyfél hozzáadja a B pontot a kosárhoz
3. Az ügyfél eltávolítja az A elemet a kosárból
4. A vásárló megkeresi és leszállítja a bevásárlókocsi tartalmát

Minden ügyfél esetében az aktuális bevásárlókocsi tartalmának egy lényeges nézetét kell megtartani. Az alkalmazásnak biztosítania kell, hogy ezek az események a bekövetkezett sorrendben legyenek feldolgozva. Ha például a kosárhoz tartozó pénztárat fel kellett dolgozni az A elem eltávolítása előtt, akkor valószínű, hogy az ügyfélnek egy leszállított elemre lenne szükségük, szemben a kívánt B elemmel. Annak biztosítása érdekében, hogy a négy eseményt a rendszer az előfordulásuk sorrendjében dolgozza fel, a partíciós kulcs értékének kell esnie. Ha a **username** (minden ügyfél egyedi felhasználóneve) lehetőséget választja a partíciós kulcsként, garantálhatja, hogy ezek az események ugyanolyan sorrendben jelenjenek meg a változási csatornában, ahogy a Azure Cosmos db.

## <a name="examples"></a>Példák

Íme néhány, a Microsoft docs-ban megadott minták hatókörén kívüli, valós idejű változásokra vonatkozó példák:

- [A változási hírcsatorna bemutatása](https://azurecosmosdb.github.io/labs/dotnet/labs/08-change_feed_with_azure_functions.html)
- [IoT-használati eset a változási csatorna köré](https://github.com/AzureCosmosDB/scenario-based-labs)
- [Kiskereskedelmi használati eset a változási hírcsatorna köré](https://github.com/AzureCosmosDB/scenario-based-labs)

## <a name="next-steps"></a>Következő lépések

* [Változáscsatorna áttekintése](change-feed.md)
* [Beállítások a változási csatorna olvasásához](read-change-feed.md)
* [A Change feed használata a Azure Functions](change-feed-functions.md)