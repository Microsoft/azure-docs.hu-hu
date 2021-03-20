---
title: Adatfolyam-adatátvitel Azure Stream Analytics integrációval (előzetes verzió)
description: Azure Stream Analytics integráció használatával továbbíthatja az adatAzure SQL Databaseba.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: ajetasin
ms.author: ajetasi
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 36a96a1927aeedb5f841083241d487e0c61d6813
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96454031"
---
# <a name="stream-data-into-azure-sql-database-using-azure-stream-analytics-integration-preview"></a>Adatfolyam-továbbítás Azure SQL Database Azure Stream Analytics Integration (előzetes verzió)

A felhasználók mostantól a valós idejű adatfolyam-adatok táblázatba való betöltését, feldolgozását, megtekintését és elemzését közvetlenül egy Azure SQL Database adatbázisból is elvégezheti. Ezt a Azure Portal [Azure stream Analytics](../../stream-analytics/stream-analytics-introduction.md)használatával teszik meg. Ez a felhasználói élmény számos különböző forgatókönyvet támogat, például a csatlakoztatott autót, a távoli figyelést, a csalások észlelését és sok más lehetőséget. A Azure Portal kiválaszthatja az események forrását (Event hub/IoT Hub), megtekintheti a bejövő valós idejű eseményeket, és kiválaszthatja az események tárolására szolgáló táblázatot. Azure Stream Analytics lekérdezési nyelvi lekérdezéseket is írhat a portálon a bejövő események átalakításához és a kiválasztott táblában való tárolásához. Ez az új belépési pont a Stream Analyticsban már létező létrehozási és konfigurációs élményeken felül van. Ez a felület az adatbázis kontextusában indul el, amely lehetővé teszi, hogy gyorsan beállítson egy Stream Analytics feladatot, és zökkenőmentesen navigáljon az adatbázis között Azure SQL Database és Stream Analytics-élményben.

![Stream Analytics folyamat](./media/stream-data-stream-analytics-integration/stream-analytics-flow.png)

## <a name="key-benefits"></a>Főbb előnyök

- Minimális környezeti váltás: a portálon Azure SQL Database adatbázisból kezdheti a valós idejű adatok táblázatba való betöltését anélkül, hogy más szolgáltatásra kellene váltania.
- Csökkentett számú lépés: az adatbázis és a tábla kontextusa a Stream Analytics feladatok előzetes konfigurálására szolgál.
- További egyszerű használat az előzetes verziójú adatokkal: a beérkező adatok előnézete az események forrásáról (Event hub/IoT Hub) a kiválasztott tábla kontextusában

> [!IMPORTANT]
> Az Azure Stream Analytics-feladatok kimenete Azure SQL Database, Azure SQL felügyelt példány vagy Azure szinapszis Analytics számára végezhető el. További információt a [kimenetek](../../stream-analytics/stream-analytics-define-outputs.md)című témakörben talál.

## <a name="prerequisites"></a>Előfeltételek

Az cikkben ismertetett lépések elvégzéséhez az alábbi erőforrásokra lesz szüksége:

- Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/).
- Egy adatbázis a Azure SQL Databaseban. Részletekért lásd: [önálló adatbázis létrehozása Azure SQL Databaseban](single-database-create-quickstart.md).
- Egy tűzfalszabály, amely lehetővé teszi a számítógép számára a kiszolgálóhoz való kapcsolódást. Részletekért lásd: [kiszolgálói szintű tűzfalszabály létrehozása](firewall-create-server-level-portal-quickstart.md).

## <a name="configure-stream-analytics-integration"></a>A stream Analytics-integráció konfigurálása

1. Jelentkezzen be az Azure Portalra.
2. Navigáljon ahhoz az adatbázishoz, ahol be szeretné állítani a folyamatos átviteli adatait. Válassza a **stream Analytics (előzetes verzió)** lehetőséget.

    ![Stream Analytics](./media/stream-data-stream-analytics-integration/stream-analytics.png)

3. A folyamatos átviteli adatok ebbe az adatbázisba való betöltéséhez válassza a **Létrehozás** lehetőséget, és adjon nevet a folyamatos átviteli feladatnak, majd válassza a **Tovább: bemenet** lehetőséget.

    ![Stream Analytics feladatok alapalapjainak konfigurálása](./media/stream-data-stream-analytics-integration/create-job.png)

4. Adja meg az események forrásának adatait, majd válassza a **Tovább: kimenet** lehetőséget.

   - **Bemenet típusa**: Event Hub/IoT hub
   - **Bemeneti alias**: adjon meg egy nevet az események forrásának azonosításához
   - **Előfizetés**: ugyanaz, mint Azure SQL Database előfizetés
   - **Event hub-névtér**: névtér neve
   - **Event hub neve**: az Event hub neve a kiválasztott névtéren belül
   - **Event hub-házirend neve** (alapértelmezés szerint új létrehozása): adjon meg egy szabályzatot
   - **Event hub fogyasztói csoport** (alapértelmezés szerint új létrehozása): felhasználói csoport nevének megadása  

      Javasoljuk, hogy hozzon létre egy fogyasztói csoportot és egy szabályzatot minden olyan új Azure Stream Analytics feladatokhoz, amelyeket itt hoz létre. A fogyasztói csoportok csak öt egyidejű olvasót engedélyeznek, így az egyes feladatokhoz egy dedikált fogyasztói csoportot biztosítanak, így elkerülhetők a korlátot meghaladó hibák. A dedikált szabályzat lehetővé teszi a kulcs elforgatását vagy az engedélyek visszavonását anélkül, hogy ez hatással lenne más erőforrásokra.

     ![Stream Analytics feladatok kimenetének konfigurálása](./media/stream-data-stream-analytics-integration/create-job-output.png)

5. Válassza ki, hogy melyik táblát szeretné bevenni a streaming-adataiba. Ha elkészült, válassza a **Létrehozás** lehetőséget.

   - **Felhasználónév**, **jelszó**: adja meg az SQL Server-hitelesítéshez tartozó hitelesítő adatait. Válassza az **Érvényesítés** lehetőséget.
   - **Tábla**: válassza az **új létrehozása** vagy a **meglévő használata** lehetőséget. Ehhez a folyamathoz válassza a **Létrehozás** lehetőséget. Ez egy új táblát hoz létre a stream Analytics-feladatok indításakor.

     ![Stream Analytics-feladatok létrehozása](./media/stream-data-stream-analytics-integration/create.png)

6. Megnyílik egy lekérdezési oldal a következő részletekkel:

   - A **bemenet** (bemeneti események forrása), amelyből adatokat tölt be  
   - Az átalakított adatokat tároló **kimenet** (kimeneti tábla)
   - Példa [SAQL-lekérdezésre](../../stream-analytics/stream-analytics-stream-analytics-query-patterns.md) a SELECT utasítással.
   - **Bemeneti előnézet**: a bemeneti események forrásának legújabb bejövő adatainak pillanatképét jeleníti meg.
     - A rendszer automatikusan észleli a szerializálási típust az adataiban (JSON/CSV). Manuálisan is módosíthatja azt JSON/CSV/AVRO.
     - A bejövő adatértékeket táblázatos formátumban vagy nyers formátumban is megtekintheti.
     - Ha a megjelenített adatai nem aktuálisak, válassza a **frissítés** lehetőséget a legújabb események megtekintéséhez.
     - Válassza az **időtartomány kiválasztása** lehetőséget a lekérdezés teszteléséhez a bejövő események adott időtartománya alapján.
     - Válassza a **minta bemenet feltöltése** lehetőséget a lekérdezés teszteléséhez egy JSON-vagy CSV-fájl feltöltésével. A SAQL-lekérdezések tesztelésével kapcsolatos további információkért lásd: [Azure stream Analytics-feladatok tesztelése a mintaadatokkel](../../stream-analytics/stream-analytics-test-query.md).

     ![lekérdezés tesztelése](./media/stream-data-stream-analytics-integration/test-query.png)

   - **Tesztek eredményei**: válassza a **teszt lekérdezés** lehetőséget, és láthatja a folyamatos átviteli lekérdezés eredményét.

     ![teszteredmények](./media/stream-data-stream-analytics-integration/test-results.png)

   - **Teszteredmények sémája**: a tesztelés után megjeleníti a folyamatos átviteli lekérdezés eredményeinek sémáját. Győződjön meg arról, hogy a test Results séma megfelel a kimeneti sémának.

     ![teszteredmények sémája](./media/stream-data-stream-analytics-integration/test-results-schema.png)

   - **Kimeneti séma**: az 5. lépésben kiválasztott tábla sémáját tartalmazza (új vagy meglévő).

      - Új létrehozása: Ha ezt a beállítást választotta az 5. lépésben, a séma még nem jelenik meg, amíg el nem indítja a folyamatos átviteli feladatot. Új tábla létrehozásakor válassza ki a megfelelő tábla-indexet. A tábla indexelésével kapcsolatos további információkért tekintse meg a következő témakörben [leírt fürtözött és nem fürtözött indexeket](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described/):.
      - Meglévő használata: Ha ezt a beállítást választotta az 5. lépésben, megjelenik a kiválasztott tábla sémája.

7. Ha elkészült a lekérdezés & tesztelésével, válassza a **lekérdezés mentése** lehetőséget. Az átalakított adatok SQL-táblába való betöltésének megkezdéséhez válassza a **Start stream Analytics feladatok** lehetőséget. Miután véglegesíti a következő mezőket, **indítsa el** a feladatot.
   - **Kimeneti kezdési idő**: Ez határozza meg a feladatok első kimenetének időpontját.  
     - Most: a feladatsor most elindítja az új bejövő adatok feldolgozását.
     - Custom (egyéni): a rendszer most elindítja a feladatot, de egy adott időpontból dolgozza fel az adatait (ez lehet a múltban vagy a jövőben is). További információ: [Azure stream Analytics feladatok elindítása](../../stream-analytics/start-job.md).
   - **Folyamatos átviteli egységek**: a Azure stream Analytics díjszabása a szolgáltatásba való adatfeldolgozáshoz szükséges folyamatos átviteli egységek számával történik. További információ: [Azure stream Analytics díjszabása](https://azure.microsoft.com/pricing/details/stream-analytics/).
   - **Kimeneti adathibaok feldolgozása**:  
     - Újrapróbálkozás: Ha hiba lép fel, Azure Stream Analytics újrapróbálkozik az esemény határozatlan idejű írásával, amíg az írás nem sikerül. Nincs időkorlát az újrapróbálkozásokhoz. Végül az összes további eseményt le kell tiltani az újrapróbálkozást követően. Ez a beállítás az alapértelmezett kimeneti hiba kezelési szabályzata.
     - Drop: Azure Stream Analytics el fog dobni minden olyan kimeneti eseményt, amely Adatátalakítási hibát eredményez. Az eldobott események később nem állíthatók helyre újrafeldolgozásra. A rendszer az összes átmeneti hibát (például hálózati hibákat) újrapróbálkozik, függetlenül attól, hogy milyen kimeneti hiba történik a házirend-konfigurációban.
   - **SQL Database kimeneti beállítások**: az előző lekérdezési lépés particionálási sémájának öröklésére szolgáló lehetőség, amely lehetővé teszi, hogy teljesen párhuzamos topológiát engedélyezzen több író használatával a táblához. További információ: [Azure stream Analytics kimenet Azure SQL Database](../../stream-analytics/stream-analytics-sql-output-perf.md).
   - **Kötegek maximális száma**: az összes tömeges beszúrási tranzakcióval ellátott rekordok számának ajánlott felső korlátja.  
    A kimeneti hibák kezelésével kapcsolatos további információkért lásd: [kimeneti hibák házirendjei a Azure stream Analyticsban](../../stream-analytics/stream-analytics-output-error-policy.md).  

     ![kezdési feladatok](./media/stream-data-stream-analytics-integration/start-job.png)

8. Miután elindította a feladatot, látni fogja a futó feladatot a listában, és a következő műveleteket hajthatja végre:
   - **A művelet indítása/leállítása**: Ha a feladatot futtatja, leállíthatja a feladatot. Ha a feladatot leállítja, elkezdheti a feladatot.
   - **Feladatok szerkesztése**: szerkesztheti a lekérdezést. Ha további módosításokat szeretne végrehajtani a feladatban, vegyen fel további bemeneteket/kimeneteket, majd nyissa meg a feladatot Stream Analyticsban. A Szerkesztés lehetőség le van tiltva, amikor a feladatot futtatják.
   - **Előzetes verziójú kimeneti tábla**: megtekintheti a táblázatot az SQL-lekérdezés szerkesztőjében.
   - **Megnyitás stream Analyticsban**: Nyissa meg a feladatot a stream Analyticsban a figyelés, a feladathoz tartozó hibakeresés részleteinek megtekintéséhez.

     ![Stream Analytics-feladatok](./media/stream-data-stream-analytics-integration/jobs.png)

## <a name="next-steps"></a>Következő lépések

- [Az Azure Stream Analytics dokumentációja](../../stream-analytics/index.yml)
- [Az Azure Stream Analytics megoldásmintái](../../stream-analytics/stream-analytics-solution-patterns.md)
