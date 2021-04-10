---
title: Az Azure Cosmos-tárolóhoz vagy-fiókhoz tartozó normalizált RU/s figyelése
description: Megtudhatja, hogyan figyelheti egy művelet normalizált kérési egység általi használatát Azure Cosmos DBban. Egy Azure Cosmos DB fiók tulajdonosai megtudhatják, hogy mely műveletek használják a több kérési egységet.
ms.service: cosmos-db
ms.topic: how-to
author: kanshiG
ms.author: govindk
ms.date: 01/07/2021
ms.openlocfilehash: ec82532b54e7834b62fcc03d3ee7de1345a0f546
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98027780"
---
# <a name="how-to-monitor-normalized-rus-for-an-azure-cosmos-container-or-an-account"></a>A normalizált RU/s figyelése Azure Cosmos-tárolóhoz vagy-fiókhoz
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

A Azure Cosmos DB Azure Monitor metrikai nézetet biztosít a fiók figyeléséhez és az irányítópultok létrehozásához. A rendszer alapértelmezés szerint összegyűjti a Azure Cosmos DB metrikákat, így nem kell explicit módon engedélyeznie vagy konfigurálnia a szolgáltatást.

A **normalizált ru** -használati metrika azt mutatja, hogy mennyire telített a partíciós kulcsok tartománya a forgalom tekintetében. Azure Cosmos DB az átviteli sebességet egyenlően osztja el a partíciós kulcsok összes tartománya között. Ez a metrika a partíciós kulcs tartományának maximális átviteli sebességét jeleníti meg másodpercenként. Ezzel a metrikával számíthatja ki az RU/s használatát a partíciós kulcs tartománya között az adott tárolónál. Ha ezt a metrikát használja, ha az Azure monitor összes partíciós kulcs-tartományának nagy hányadát látja, akkor növelje az átviteli sebességet, hogy megfeleljen a számítási feladatok igényeinek. Példa – a normalizált kihasználtságot a rendszer az összes partíciós kulcs tartományában lévő RU/s kihasználtság maximális értékeként határozza meg. Tegyük fel például, hogy a maximális átviteli sebesség 20 000 RU/s, és két partíciós kulcs tartománya van, P_1 és P_2, amelyek mindegyike 10 000 RU/s méretre képes. Egy adott másodpercben, ha P_1 6000 RUs-t használ, és P_2 8000 RUs, a normalizált kihasználtság MAX (6000 RU/10 000 RU, 8000 RU/10 000 RU) = 0,8.

## <a name="what-to-expect-and-do-when-normalized-rus-is-higher"></a>Mire számíthat, és ha a normalizált RU/s magasabb

Ha a normalizált RU/s-használat eléri a 100%-ot a megadott partíciós kulcs tartománya számára, és ha egy ügyfél továbbra is a kérelmeket az adott időintervallumban, az adott partíciós kulcs tartományára is elküldi, akkor a rendszer korlátozott mértékű hibát kap Az ügyfélnek tiszteletben kell tartania a javasolt várakozási időt, és újra kell próbálkoznia a kéréssel. Az SDK-val könnyedén kezelheti ezt a helyzetet azáltal, hogy a megfelelő várakozással újrapróbálkozik az előre konfigurált időpontokkal.  Nem szükséges, hogy az RU-ráta korlátozási hibát észlelt, mert a normalizált RU elérte a 100%-ot. Ennek az az oka, hogy a normalizált RU egyetlen érték, amely a maximális kihasználtságot jelöli az összes partíciós kulcs tartományán, az egyik partíciós kulcs foglalható, de a többi partíciós kulcs tartománya probléma nélkül kiszolgálhatja a kéréseket. Például egyetlen művelet, például egy tárolt eljárás, amely a partíciós kulcsok tartományában lévő összes RU/s-t felhasználja, a normalizált RU/s-fogyasztás rövid csúcsát fogja eredményezni. Ilyen esetekben a rendszer nem jelez sebességkorlátozással kapcsolatos hibát, ha a kérések száma alacsony, vagy ha különböző partíciókulcs-tartományokban lévő más partíciókra vonatkozó kérések történnek. 

A Azure Monitor metrikák segítségével megkeresheti az SQL API-hoz tartozó műveleti kódokat az **összes kérelem** metrikájának használatával. Később a 429 állapotkód alapján szűrheti ezeket a kéréseket, és a **művelet típusa** alapján feloszthatja őket.  

Ha meg szeretné keresni a kérelmeket, amelyek korlátozottak, az ajánlott módszer a diagnosztikai naplókon keresztüli információk beszerzése.

Ha folyamatos csúcsa 100%-os normalizált RU/s-fogyasztás, vagy a több partíciós kulcs tartományában a 100%-ra van lezárva, ajánlott az átviteli sebesség növelése. Az Azure monitor metrikáinak és az Azure monitor diagnosztikai naplóinak használatával megtudhatja, hogy mely műveletek jelentősek és a maximális kihasználtságuk.

Összefoglalva, a **normalizált ru** -használati metrika segítségével megtekintheti, hogy melyik partíciós kulcs tartománya sokkal meleg a használat szempontjából. Így a partíciós kulcs tartományához tartozó átviteli sebesség eldöntését teszi lehetővé. Később nyomon követheti a **PartitionKeyRUConsumption** -naplót, Azure monitor naplókat, amelyekkel információkat kaphat arról, hogy mely logikai partíciós kulcsok használják a használati feltételeket. Ez a partíciós kulcs választása vagy az alkalmazás logikájában megjelenő változásra mutat. A mérték korlátozásának feloldásához Ossza szét az adatok terhelését több partíció között, vagy csak növelje az átviteli sebességet, ahogy szükséges. 

## <a name="view-the-normalized-request-unit-consumption-metric"></a>A normalizált kérési egység felhasználási metrikájának megtekintése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. Válassza a **figyelő** lehetőséget a bal oldali navigációs sávon, és válassza a **metrikák** lehetőséget.

   :::image type="content" source="./media/monitor-normalized-request-units/monitor-metrics-blade.png" alt-text="Metrikák ablaktábla Azure Monitor":::

3. A **metrikák** ablaktáblán > **válasszon ki egy erőforrást** > válassza ki a szükséges **előfizetést** és **erőforráscsoportot**. Az **erőforrástípus** mezőben válassza a **Azure Cosmos db fiókok** lehetőséget, válasszon ki egy meglévő Azure Cosmos-fiókot, majd válassza az **alkalmaz** lehetőséget.

   :::image type="content" source="./media/monitor-normalized-request-units/select-cosmos-db-account.png" alt-text="Válasszon ki egy Azure Cosmos-fiókot a metrikák megtekintéséhez":::

4. Következő lépésként kiválaszthat egy mérőszámot az elérhető metrikák listájából. Kiválaszthatja a kérelmekhez tartozó mérőszámokat, a tárolást, a késést, a rendelkezésre állást, a Cassandra és másokat. A listán szereplő összes mérőszám részletes megismeréséhez tekintse meg a [metrikák kategória szerint](monitor-cosmos-db-reference.md) című cikket. Ebben a példában a **normalizált ru** használati metrikát és a **Max** értéket adjuk meg az összesítési értékként.

   Ezen részletek mellett kiválaszthatja a metrikák **időtartományát** és **időrészletességét** is. A maximális értéknél megtekintheti az elmúlt 30 nap mérőszámait.  A szűrő alkalmazása után egy diagram jelenik meg a szűrő alapján.

   :::image type="content" source="./media/monitor-normalized-request-units/normalized-request-unit-usage-metric.png" alt-text="Metrika kiválasztása a Azure Portal":::

### <a name="filters-for-normalized-request-unit-consumption"></a>A normalizált kérelmek egységének felhasználására szolgáló szűrők

A mérőszámokat és a diagramot egy adott **CollectionName**, a **databasename**, a **PartitionKeyRangeID** és a **régió** alapján is szűrheti. A metrikák szűréséhez válassza a **szűrő hozzáadása** lehetőséget, és válassza ki a szükséges tulajdonságot, például a **CollectionName** és a megfelelő értéket. A gráf ezután megjeleníti a megadott időszakra vonatkozóan a tárolóhoz felhasznált normalizált RU-felhasználási egységeket.  

A metrikákat a **felosztás alkalmazása** lehetőség használatával csoportosíthatja. A megosztott átviteli sebességű adatbázisok esetében a normalizált RU-metrika csak az adatbázis-részletességgel kapcsolatos adatokat jeleníti meg, nem jeleníti meg a gyűjteményben lévő adatokat. A megosztott átviteli sebességű adatbázis esetében tehát nem jelennek meg adatok a gyűjtemény neve alapján történő felosztás alkalmazása során.

Az egyes tárolók normalizált kérések egységenkénti mérőszáma az alábbi képen látható módon jelenik meg:

:::image type="content" source="./media/monitor-normalized-request-units/normalized-request-unit-usage-filters.png" alt-text="Szűrők alkalmazása a normalizált kérelmek egységének felhasználási metrikája":::

## <a name="next-steps"></a>Következő lépések

* Az Azure-ban [diagnosztikai beállításokkal](cosmosdb-monitor-resource-logs.md) figyelheti Azure Cosmos db az adataikat.
* [Azure Cosmos DB vezérlési sík műveleteinek naplózása](audit-control-plane-logs.md)
