---
title: Az Azure Stream Analytics használata dedikált SQL-készletben
description: Tippek a Azure Stream Analytics SQL-készletekkel való Azure Synapse valós idejű megoldások fejlesztésével kapcsolatban.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 9/25/2020
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 0c7f139b50cd43e3e8862fda3f5401a853ced8d0
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566579"
---
# <a name="use-azure-stream-analytics-with-dedicated-sql-pool-in-azure-synapse-analytics"></a>Az Azure Stream Analytics használata dedikált SQL-készletekkel a Azure Synapse Analytics

Azure Stream Analytics egy teljes körűen felügyelt szolgáltatás, amely kis késésű, magas rendelkezésre állékonyságú, skálázható, összetett eseményfeldolgozást biztosít a felhőben található streamelési adatokon. Az alapokat a Bevezetés a [Azure Stream Analytics.](../../stream-analytics/stream-analytics-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) Ezután az Ismerkedés a Azure Stream Analytics útmutatóval megtudhatja, hogyan hozhat [](../../stream-analytics/stream-analytics-real-time-fraud-detection.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) létre végpontok Stream Analytics megoldásokat.

Ebből a cikkből megtudhatja, hogyan használhatja a dedikált SQL-készletet kimeneti fogadóként a nagy átviteli sebességű adatbe Azure Stream Analytics feladatokhoz.

## <a name="prerequisites"></a>Előfeltételek

* Azure Stream Analytics feladat – Azure Stream Analytics létrehozásához kövesse az Első lépések a Azure Stream Analytics [használatával](../../stream-analytics/stream-analytics-real-time-fraud-detection.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) oktatóanyag lépéseit:  

    1. Eseményközpont-bemenet létrehozása
    2. Eseménygenerátor alkalmazás konfigurálása és elindítani
    3. Új Stream Analytics kiépítése
    4. Feladat bemenetének és lekérdezésének megadása
* Dedikált SQL-készlet – Új dedikált SQL-készlet létrehozásához kövesse a dedikált SQL-készlet létrehozása című rövid [útmutató lépéseit.](../quickstart-create-sql-pool-portal.md)

## <a name="specify-streaming-output-to-point-to-your-dedicated-sql-pool"></a>Streamelési kimenet megadása, hogy a dedikált SQL-készletre mutasson

### <a name="step-1"></a>1. lépés

A Azure Portal a feladat Stream Analytics, és kattintson a **Kimenetek** elemre a Feladat **topológiája menüben.**

### <a name="step-2"></a>2. lépés

Kattintson a **Hozzáadás gombra,** **és** Azure Synapse Analytics a legördülő menüből.

![Válassza a Azure Synapse Analytics](./media/sql-data-warehouse-integrate-azure-stream-analytics/sql-pool-azure-stream-analytics-output.png)

### <a name="step-3"></a>3. lépés

Írja be a következő értékeket:

* *Kimeneti alias:* Adjon meg egy rövid nevet a feladat kimenetének.
* *Előfizetés:*
  * Ha a dedikált SQL-készlet ugyanabban az előfizetésben található, mint a Stream Analytics feladat, kattintson a Select Azure Synapse Analytics from your subscriptions (Előfizetések Azure Synapse Analytics ***kiválasztása) elemre.***
  * Ha a dedikált SQL-készlet egy másik előfizetésben található, kattintson a Manuális Azure Synapse Analytics megadása elemre.
* *Adatbázis:* Válassza ki a céladatbázist a legördülő listából.
* *Felhasználónév:* Adja meg egy olyan fiók felhasználónevét, amely írási engedéllyel rendelkezik az adatbázishoz.
* *Jelszó:* Adja meg a megadott felhasználói fiók jelszavát.
* *Tábla:* Adja meg a céltábla nevét az adatbázisban.
* kattintson a **Mentés gombra**

![Kitöltött Azure Synapse Analytics űrlap](./media/sql-data-warehouse-integrate-azure-stream-analytics/sql-pool-azure-stream-analytics-output-db-settings.png)

### <a name="step-4"></a>4. lépés

A teszt futtatása előtt létre kell hoznia a táblát a dedikált SQL-készletben.  Futtassa a következő tábla-létrehozási szkriptet SQL Server Management Studio (SSMS) vagy a választott lekérdezési eszköz használatával.

```sql
CREATE TABLE SensorLog
(
    RecordType VARCHAR(2)
    , SystemIdentity VARCHAR(2)
    , FileNum INT
    , SwitchNum VARCHAR(50)
    , CallingNum VARCHAR(25)
    , CallingIMSI VARCHAR(25)
    , CalledNum VARCHAR(25)
    , CalledIMSI VARCHAR(25)
    , DateS VARCHAR(25)
    , TimeS VARCHAR(25)
    , TimeType INT
    , CallPeriod INT
    , CallingCellID VARCHAR(25)
    , CalledCellID VARCHAR(25)
    , ServiceType VARCHAR(25)
    , [Transfer] INT
    , IncomingTrunk VARCHAR(25)
    , OutgoingTrunk VARCHAR(25)
    , MSRN VARCHAR(25)
    , CalledNum2 VARCHAR(25)
    , FCIFlag VARCHAR(25)
    , callrecTime VARCHAR(50)
    , EventProcessedUtcTime VARCHAR(50)
    , PartitionId int
    , EventEnqueuedUtcTime VARCHAR(50)
    )
WITH (DISTRIBUTION = ROUND_ROBIN)
```

### <a name="step-5"></a>5. lépés

A Azure Portal feladat Stream Analytics kattintson a feladat nevére.  Kattintson a ***Teszt** _ gombra a _ *_Kimenet részletei_** panelen.

![A Outpout details (Ki- és előlépés részletei) gomb Ha az adatbázissal való kapcsolat sikeres, egy értesítés jelenik meg ](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asatest.png) a portálon.

### <a name="step-6"></a>6. lépés

Kattintson a ***Lekérdezés** _ menü Feladat _*_topológiája_*_ területén, és módosítsa a lekérdezést úgy, hogy adatokat szúrjon be a létrehozott Stream-kimenetbe.  Kattintson a _*_Kiválasztott lekérdezés tesztelése gombra_*_ a lekérdezés teszteléséhez.  Ha a *_lekérdezési teszt_* sikeres, kattintson a _ Lekérdezés mentése * gombra.

![Lekérdezés mentése](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asaquery.png)

### <a name="step-7"></a>7. lépés

Indítsa el Azure Stream Analytics feladatot.  Kattintson a ***Start** _ gombra az _ *_Áttekintés_** menüben.

![Stream Analytics-feladat indítása](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asastart.png)

Kattintson a ***Start gombra*** a Start feladat panelen.

![Kattintson az Indítás gombra.](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asastartconfirm.png)

## <a name="next-steps"></a>Következő lépések

Az integráció áttekintését lásd: [Más szolgáltatások integrálása.](sql-data-warehouse-overview-integrate.md)
További fejlesztési tippekért lásd: Tervezési döntések [és kódolási technikák dedikált SQL-készlethez.](sql-data-warehouse-overview-develop.md)
