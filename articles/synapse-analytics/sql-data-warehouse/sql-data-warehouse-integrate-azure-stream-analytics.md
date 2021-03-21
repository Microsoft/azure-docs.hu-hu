---
title: Azure Stream Analytics használata dedikált SQL-készletben
description: Tippek a Azure Stream Analytics és az Azure szinapszis dedikált SQL-készletével való használatához a valós idejű megoldások fejlesztéséhez.
services: synapse-analytics
author: gaursa
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 9/25/2020
ms.author: gaursa
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 023cf55a01f34277dd5c5707d0d123f54c1674df
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104600088"
---
# <a name="use-azure-stream-analytics-with-dedicated-sql-pool-in-azure-synapse-analytics"></a>A Azure Stream Analytics használata dedikált SQL-készlettel az Azure szinapszis Analyticsben

A Azure Stream Analytics egy teljes körűen felügyelt szolgáltatás, amely kis késleltetésű, magasan elérhető, skálázható, összetett eseményt biztosít a felhőben tárolt adatfolyam-adatfeldolgozáshoz. Az alapvető tudnivalókat a [Azure stream Analytics bevezetésének](../../stream-analytics/stream-analytics-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)olvasásával ismerheti meg. Ezt követően megtudhatja, hogyan hozhat létre teljes körű megoldást a Stream Analytics az [Azure stream Analytics oktatóanyag használatának első lépéseivel](../../stream-analytics/stream-analytics-real-time-fraud-detection.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) .

Ebből a cikkből megtudhatja, hogyan használhatja a dedikált SQL-készletet kimeneti fogadóként a nagy átviteli sebességű adatfeldolgozáshoz Azure Stream Analytics feladatokkal.

## <a name="prerequisites"></a>Előfeltételek

* Azure Stream Analytics feladat – Azure Stream Analytics feladat létrehozásához kövesse az [Ismerkedés a Azure stream Analytics](../../stream-analytics/stream-analytics-real-time-fraud-detection.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) oktatóanyagban című témakör lépéseit a következőre:  

    1. Event hub-bemenet létrehozása
    2. Event Generator-alkalmazás konfigurálása és elindítása
    3. Stream Analytics-feladatok kiépítése
    4. A feladatok bemenetének és lekérdezésének megadása
* Dedikált SQL-készlet – új dedikált SQL-készlet létrehozásához kövesse a rövid útmutató [: DEDIKÁLT SQL-készlet létrehozása](../quickstart-create-sql-pool-portal.md)című témakör lépéseit.

## <a name="specify-streaming-output-to-point-to-your-dedicated-sql-pool"></a>Adja meg a folyamatos átviteli kimenetet, hogy az a dedikált SQL-készletre mutasson

### <a name="step-1"></a>1. lépés

A Azure Portal lépjen a Stream Analytics feladatra, és kattintson a **kimenetek** elemre a **feladatok topológiai** menüjében.

### <a name="step-2"></a>2. lépés

Kattintson a **Hozzáadás** gombra, és válassza az **Azure szinapszis Analytics** elemet a legördülő menüből.

![Az Azure szinapszis Analytics kiválasztása](./media/sql-data-warehouse-integrate-azure-stream-analytics/sql-pool-azure-stream-analytics-output.png)

### <a name="step-3"></a>3. lépés

Írja be a következő értékeket:

* *Kimeneti alias*: adjon meg egy rövid nevet a feladatok kimenetének.
* *Előfizetés*:
  * Ha a dedikált SQL-készlet ugyanahhoz az előfizetéshez tartozik, mint a Stream Analytics feladattal, kattintson az ***Azure szinapszis Analytics kiválasztása az előfizetések közül***.
  * Ha a dedikált SQL-készlet egy másik előfizetésben található, kattintson az Azure szinapszis Analytics beállításainak manuális megadása lehetőségre.
* *Adatbázis*: válassza ki a célként szolgáló adatbázist a legördülő listából.
* *Felhasználónév*: adja meg egy olyan fiók felhasználónevét, amely rendelkezik írási engedéllyel az adatbázishoz.
* *Password (jelszó*): adja meg a megadott felhasználói fiók jelszavát.
* *Tábla*: adja meg a cél tábla nevét az adatbázisban.
* kattintson a **Save (Mentés** ) gombra

![Befejezett Azure szinapszis Analytics-űrlap](./media/sql-data-warehouse-integrate-azure-stream-analytics/sql-pool-azure-stream-analytics-output-db-settings.png)

### <a name="step-4"></a>4. lépés

A teszt futtatása előtt létre kell hoznia a táblát a dedikált SQL-készletben.  Futtassa az alábbi táblázat-létrehozási parancsfájlt a SQL Server Management Studio (SSMS) vagy a választott lekérdezési eszköz használatával.

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

A Azure Portal Stream Analytics feladatokhoz kattintson a feladatokra.  Kattintson a ***test** _ gombra a _ *_output részletek_** panelen.

![](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asatest.png)Ha az adatbázishoz való csatlakozás sikeres volt, megjelenik egy értesítés a portálon.

### <a name="step-6"></a>6. lépés

Kattintson a ***lekérdezés** _ menüpontra a _*_feladatok topológiája_*_ alatt, és módosítsa a lekérdezést, hogy adatokat szúrjon be a létrehozott stream-kimenetbe.  A lekérdezés teszteléséhez kattintson a _*_kiválasztott lekérdezés tesztelése_*_ gombra.  Ha a lekérdezési teszt sikeres, kattintson a *_lekérdezés mentése_* gombra.

![Lekérdezés mentése](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asaquery.png)

### <a name="step-7"></a>7. lépés

Indítsa el a Azure Stream Analytics feladatot.  Kattintson a ***Start** _ gombra az _ *_Overview_** menüben.

![Stream Analytics-feladat indítása](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asastart.png)

Kattintson a ***Start gombra a kezdési*** feladatok ablaktáblán.

![Kattintson a Start gombra](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asastartconfirm.png)

## <a name="next-steps"></a>Következő lépések

Az integráció áttekintését lásd: [egyéb szolgáltatások integrálása](sql-data-warehouse-overview-integrate.md).
További fejlesztési tippek: [tervezési döntések és kódolási technikák a DEDIKÁLT SQL-készlethez](sql-data-warehouse-overview-develop.md).
