---
title: Egy művelet teljesítménybeli használatának figyelése Azure Cosmos DB
description: Megtudhatja, hogyan figyelheti a művelet átviteli sebességét vagy a kérések egység általi használatát Azure Cosmos DBban. Egy Azure Cosmos DB fiók tulajdonosai megtudhatják, hogy mely műveletek vesznek igénybe több kérési egységet.
ms.service: cosmos-db
ms.topic: conceptual
author: kanshiG
ms.author: govindk
ms.date: 04/09/2020
ms.openlocfilehash: bc39ef199a5d40d3eaa75023277d3e00b93e131a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81115429"
---
# <a name="how-to-monitor-throughput-or-request-unit-usage-of-an-operation-in-azure-cosmos-db"></a>Az átviteli sebesség figyelése vagy a művelet kérése Azure Cosmos DB

A Azure Cosmos DB Azure Monitor metrikai nézetet biztosít a fiók figyeléséhez és az irányítópultok létrehozásához. A rendszer alapértelmezés szerint összegyűjti a Azure Cosmos DB metrikákat, így nem kell explicit módon engedélyeznie vagy konfigurálnia a szolgáltatást. A **kérelmek teljes** mérőszáma a különböző típusú műveletek esetében a kérési egységek használatának beolvasására szolgál. Később elemezheti, hogy mely műveletek használják a legtöbb átviteli sebességet. Alapértelmezés szerint az adatátviteli adatok összesítése egyperces időközönként történik. Az összesítési egységet azonban módosíthatja az idő részletességi beállításának módosításával.

Kétféle módon elemezheti a kérési egység használati adatait:

* Az adott időszakon belül, amelynél a műveletek több kérési egységet vesznek igénybe.
* A munkaterhelést általában a további kérelmek elfogyasztásával uralják.
Ez az elemzés lehetővé teszi, hogy olyan műveletekre koncentráljon, mint például az INSERT, a upsert, és nézze meg az indexelést. Megtudhatja, hogy a megadott mezők indexelése alatt vagy alatt van-e, és módosítja az [indexelési házirendet](index-policy.md#include-exclude-paths) az elérési utak belefoglalásához vagy kizárásához.

Ha észreveszi, hogy bizonyos lekérdezések több kérési egységet vesznek igénybe, olyan műveleteket hajthat végre, mint például a következők:

* Gondolja át, hogy a megfelelő mennyiségű adatokat kéri-e.
* Módosítsa a lekérdezést úgy, hogy az indexet használja a Filter záradék használatával.
* Kevésbé költséges UDF-függvények elvégzése.
* Adja meg a partíciós kulcsokat, hogy a ventilátorok ne legyenek a különböző partíciók között.
* Használhatja a hívási válaszban visszaadott lekérdezési metrikákat, a diagnosztikai napló részleteit, valamint a [lekérdezési teljesítmény finomhangolásáról](sql-api-query-metrics.md) szóló cikket a lekérdezés végrehajtásával kapcsolatos további információért.
* Elindíthatja a Sum függvényt, majd megtekintheti az átlagos kihasználtságot a megfelelő dimenzió használatával.

## <a name="view-the-total-request-unit-usage-metric"></a>Az összes kérési egység használati metrikájának megtekintése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Válassza a **figyelő** lehetőséget a bal oldali navigációs sávon, és válassza a **metrikák**lehetőséget.

   ![Metrikák ablaktábla Azure Monitor](./media/monitor-request-unit-usage/monitor-metrics-blade.png)

1. A **metrikák** ablaktáblán > **válasszon ki egy erőforrást** > válassza ki a szükséges **előfizetést**és **erőforráscsoportot**. Az **erőforrástípus**mezőben válassza a **Azure Cosmos db fiókok**lehetőséget, válasszon ki egy meglévő Azure Cosmos-fiókot, majd válassza az **alkalmaz**lehetőséget.

   ![Válassza ki a Azure Cosmos DB fiókot a metrikák megtekintéséhez](./media/monitor-request-unit-usage/select-cosmos-db-account.png)

1. Ezután válassza ki a **teljes kérelmek egységének** metrikáját az elérhető metrikák listájából. A listán szereplő összes mérőszám részletes megismeréséhez tekintse meg a [metrikák kategória szerint](monitor-cosmos-db-reference.md) című cikket. Ebben a példában a **teljes kérelmeket** és az **átlagot** adja meg az összesítési értékként. Ezen részletek mellett kiválaszthatja a metrikák **időtartományát** és **időrészletességét** is. A maximális értéknél megtekintheti az elmúlt 30 nap mérőszámait.  A szűrő alkalmazása után egy diagram jelenik meg a szűrő alapján. A kiválasztott időszakra vonatkozóan percenként felhasználható kérelmek átlagos számát láthatja.  

   ![Metrika kiválasztása a Azure Portal](./media/monitor-request-unit-usage/request-unit-usage-metric.png)

## <a name="filters-for-request-unit-usage"></a>A kérések egységének használatára vonatkozó szűrők

A mérőszámokat szűrheti, és megtekintheti az adott **CollectionName**, **databasename**, **OperationType**, **régió**, **állapot**és **statuscode**által megjelenített diagramokat is. A **szűrő hozzáadása** és a **felosztási beállítások alkalmazása** lehetővé teszi a kérési egység használatának szűrését és a metrikák csoportosítását.

Ha az egyes műveletek esetében az összes művelethez az összeg (Sum) vagy az átlag lehetőséget választja, válassza a **felosztás alkalmazása** elemet, és válassza a **művelet típusát** és a szűrő értékét a következő képen látható módon:

   ![Az Azure monitoron végzett műveletekhez Cosmos DB kérelmek egységei](./media/monitor-request-unit-usage/request-unit-usage-operations.png)

Ha meg szeretné tekinteni a kérési egység használatát gyűjtemény szerint, válassza a **felosztás alkalmazása** lehetőséget, és válassza ki a gyűjtemény nevét szűrőként. A következőhöz hasonló csevegés jelenik meg, az irányítópulton található gyűjtemények közül választhat. Ezt követően kiválaszthat egy adott gyűjtemény nevét a további részletek megtekintéséhez:

   ![A gyűjtemény által az Azure monitoron végzett összes művelethez Cosmos DB kérelmek egységei](./media/monitor-request-unit-usage/request-unit-usage-collection.png)

## <a name="next-steps"></a>További lépések

* Az Azure-ban [diagnosztikai beállításokkal](cosmosdb-monitor-resource-logs.md) figyelheti Azure Cosmos db az adataikat.
* [Azure Cosmos DB vezérlési sík műveleteinek naplózása](audit-control-plane-logs.md)