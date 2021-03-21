---
title: Automatizált teljesítmény, Cost, biztonsági javaslatok a Azure Cosmos DB
description: Megtudhatja, hogyan tekintheti meg a munkaterhelési minták alapján a Azure Cosmos DB testreszabott teljesítményét, költségeit, biztonságát és egyéb javaslatait.
author: ThomasWeiss
ms.author: thweiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/28/2020
ms.reviewer: sngun
ms.openlocfilehash: d9eb55030c7ec52f9b2ac79fbab19944f0a3e190
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93087825"
---
# <a name="automated-recommendations-for-azure-cosmos-db"></a>Automatizált javaslatok a Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Az összes felhőalapú szolgáltatás, többek között a Azure Cosmos DB a gyakori frissítések új funkciókkal, képességekkel és fejlesztési lehetőségekkel. Fontos, hogy az alkalmazás naprakészen tartsa a legújabb teljesítmény-és biztonsági frissítéseket. A Azure Portal testreszabott javaslatokat kínál, amelyek lehetővé teszik az alkalmazás teljesítményének maximalizálását. A Azure Cosmos DB tanácsadó motorja folyamatosan elemzi a Azure Cosmos DB erőforrásainak használati előzményeit, és javaslatokat tesz a számítási feladatok mintái alapján. Ezek a javaslatok olyan területekhez tartoznak, mint a particionálás, az indexelés, a hálózat, a biztonság stb. Ezek a testreszabott javaslatok segítenek az alkalmazás teljesítményének javításában.

## <a name="view-recommendations"></a>Javaslatok megtekintése

A Azure Cosmos DB javaslatai a következő módokon tekinthetők meg:

- A javaslatok megtekintésének egyik módja az értesítések lapon található. Ha új javaslatok vannak, egy üzenetsáv jelenik meg. Jelentkezzen be [Azure Portal](https://portal.azure.com) , és navigáljon az Azure Cosmos-fiókjához. Az Azure Cosmos-fiókjában nyissa meg az **értesítések** panelt, majd válassza a **javaslatok** lapot. Kiválaszthatja az üzenetet, és megtekintheti a javaslatokat.  

   :::image type="content" source="./media/automated-recommendations/cosmos-db-pane-recommendations.png" alt-text="Javaslatok megtekintése Azure Cosmos DB panelen":::

- Az ajánlásokat a különböző gyűjtők által kategorizált [Azure Advisor](../advisor/advisor-overview.md) is megtalálhatja, például a költségeket, a biztonságot, a megbízhatóságot, a teljesítményt és az operatív kiválóságot. Kiválaszthat bizonyos előfizetéseket és szűrheti az erőforrástípus alapján, amely **Azure Cosmos db fiók**.  Amikor kiválaszt egy konkrét javaslatot, megjeleníti azokat a műveleteket, amelyeket el tud végezni a számítási feladatok kihasználása érdekében.

   :::image type="content" source="./media/automated-recommendations/advisor-pane-recommendations.png" alt-text="Javaslatok megtekintése Azure Advisor panelen":::

A Azure Cosmos DB ablaktáblán látható összes javaslat nem érhető el a Azure Advisorban, és fordítva. Ennek oka az, hogy a Azure Advisor ablaktáblán, Azure Cosmos DB ablaktáblán vagy mindkettőn lévő javaslat típusa alapján.

Jelenleg Azure Cosmos DB támogatja a következő területekre vonatkozó javaslatokat. A javaslatok mindegyike tartalmaz egy hivatkozást a dokumentáció vonatkozó szakaszára, így egyszerűen elvégezheti a következő lépéseket.

## <a name="sdk-usage-recommendations"></a>SDK-használati javaslatok

Ebben a kategóriában az Advisor észleli az SDK-k régi verziójának használatát, és azt javasolja, hogy újabb verzióra frissítsen a hibajavítások és a teljesítménnyel kapcsolatos javítások kihasználása érdekében. Jelenleg a következő SDK-specifikus javaslatok érhetők el:

|Név  |Leírás  |
|---------|---------|
| Régi Spark-összekötő | Észleli a Spark-összekötő régi verzióinak használatát, és a frissítését javasolja. |
| Régi .NET SDK | Észleli a .NET SDK régi verzióinak használatát, és a frissítését javasolja. |
| Régi Java SDK | Észleli a Java-összekötő régi verzióinak használatát, és a frissítését javasolja. |

## <a name="indexing-recommendations"></a>Indexelési javaslatok

Ebben a kategóriában az Advisor észleli az indexelési módot, az indexelési házirendet, az indexelt elérési utakat, és azt javasolja, hogy az aktuális konfiguráció hatással legyen a lekérdezés teljesítményére. Jelenleg a következő indexelésre vonatkozó javaslatok érhetők el:

|Név  |Leírás  |
|---------|---------|
| Lusta indexelés | Észleli a lusta indexelési mód használatát, és inkább konzisztens indexelési mód használatát javasolja. A Azure Cosmos DB lusta indexelési üzemmódjának célja korlátozott, és hatással lehet a lekérdezés eredményeinek frissességére bizonyos helyzetekben, ezért a konzisztens indexelési mód használata ajánlott. |
| Összetett indexelés| Észleli azokat a fiókokat, amelyekben a lekérdezések kihasználhatják az összetett indexeket, és azok használatát ajánljuk. Az összetett indexek jelentősen növelhetik egyes lekérdezések teljesítményét és teljesítménybeli felhasználását.|
| Alapértelmezett indexelési házirend számos indexelt elérési úttal | Észleli az alapértelmezett indexelés során futó tárolókat számos indexelt elérési úttal, és az indexelési szabályzat testreszabását javasolja.|
| ORDER BY lekérdezések magas RU/s díj ellenében| Észleli a RENDELÉSeket a magas RU/s díjat tartalmazó lekérdezések alapján, és az összetett indexek feltárását ajánlja.|
| MongoDB 3,6-es fiókok index és magas RU/s-fogyasztás nélkül| Észleli Azure Cosmos DB API-ját a MongoDB a magas RU/s díjjal rendelkező lekérdezéseket kiállító tárolók 3,6-es verziójával, és az indexek hozzáadását javasolja.|

## <a name="cost-optimization-recommendations"></a>A Cost Optimization javaslatai

Ebben a kategóriában az Advisor észleli az RU/s-használatot, és megállapítja, hogy az erőforrások módosításával vagy más díjszabási modell használatával optimalizálhatja az árakat. Jelenleg a következő költséghatékony optimalizációra vonatkozó javaslatok érhetők el:

|Név  |Leírás  |
|---------|---------|
| Fenntartott kapacitás | Észleli az RU/s-kihasználtságot, és a fenntartott példányokat ajánlja azoknak a felhasználóknak, akik számára hasznosak lehetnek. |
| Inaktív tárolók | Észleli a 30 napnál hosszabb ideig nem használt tárolókat, és javasolja az ilyen tárolók átviteli sebességének csökkentését vagy törlését.|
| Új előfizetések nagy átviteli sebességgel | Észleli az új előfizetéseket, amelyekben a fiókok nem általában magas RU/s-t költenek, és értesítéseket biztosítanak. Ez az értesítés kifejezetten arra hívja fel az új ügyfeleket, hogy Azure Cosmos DB a kiépített átviteli sebességen alapuló modellen, nem pedig a fogyasztáson alapuló modellen működik. |

## <a name="migration-recommendations"></a>Áttelepítési javaslatok

Ebben a kategóriában az Advisor azt észleli, hogy örökölt funkciókat használ a Migrálás érdekében, hogy kihasználja Azure Cosmos DB nagy skálázhatóságát és egyéb előnyeit. Jelenleg a következő áttelepítésre vonatkozó javaslatok érhetők el:

|Név  |Leírás  |
|---------|---------|
| Nem particionált tárolók | Észleli a maximális tárolási korlátot megközelítő rögzített méretű tárolókat, és azt javasolja, hogy particionált tárolók migrálja őket.|

## <a name="query-usage-recommendations"></a>Használati javaslatok lekérdezése

Ebben a kategóriában az Advisor észleli a lekérdezés végrehajtását, és megállapítja, hogy a lekérdezési teljesítmény hangolható bizonyos változásokkal. Jelenleg a következő lekérdezési használati javaslatok érhetők el:

|Név  |Leírás  |
|---------|---------|
| Rögzített méretű lekérdezések | Észleli a rögzített oldalméret miatt kiadott lekérdezéseket, és az-1 értéket (az oldalméret korlátozását) használja a megadott érték meghatározása helyett. Ezzel a beállítással csökkenthető az összes eredmény beolvasásához szükséges hálózati kör alakú utak száma. |

## <a name="next-steps"></a>Következő lépések

* [A lekérdezés teljesítményének finomhangolása Azure Cosmos DB](sql-api-query-metrics.md)
* [Lekérdezési problémák elhárítása](troubleshoot-query-performance.md) Azure Cosmos db használatakor
