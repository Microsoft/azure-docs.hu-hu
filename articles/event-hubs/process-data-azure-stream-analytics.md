---
title: Adatok feldolgozása Event Hubs Azure-ból Stream Analytics használatával | Microsoft Docs
description: Ez a cikk bemutatja, hogyan dolgozhatja fel az Azure Event hub adatait egy Azure Stream Analytics feladatokkal.
ms.date: 06/23/2020
ms.topic: article
ms.openlocfilehash: 2db4fba59a1a06a24ee2939c51ecdf65aa06cef3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "85312895"
---
# <a name="process-data-from-your-event-hub-using-azure-stream-analytics"></a>Az Event hub adatainak feldolgozása Azure Stream Analytics használatával 
A Azure Stream Analytics szolgáltatással könnyedén betöltheti, feldolgozhatja és elemezheti az Azure Event Hubs adatfolyamait, így hatékony elemzéseket készíthet a valós idejű műveletek elvégzéséhez. Ez az integráció lehetővé teszi, hogy gyorsan létrehozzon egy gyors elérésű elemzési folyamatot. A Azure Portal használatával megjelenítheti a bejövő és a Stream Analytics lekérdezéseket. Ha a lekérdezés elkészült, csak néhány kattintással áthelyezheti az éles környezetbe. 

## <a name="key-benefits"></a>Főbb előnyök
Az Azure Event Hubs és a Azure Stream Analytics integráció legfőbb előnyei: 
- **Adatok előnézete** – megtekintheti a bejövő adatok előnézetét az Azure Portal egy Event hub-ból.
- **Tesztelje a lekérdezést** – készítsen elő egy átalakítási lekérdezést, és tesztelje közvetlenül a Azure Portal. A lekérdezés nyelvének szintaxisát a [stream Analytics lekérdezési nyelv](/stream-analytics-query/built-in-functions-azure-stream-analytics) dokumentációjában találja.
- A **lekérdezés üzembe helyezése éles** környezetben – Azure stream Analytics feladatok létrehozásával és elindításával üzembe helyezheti a lekérdezést éles környezetben.

## <a name="end-to-end-flow"></a>Végpontok közötti folyamat

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 
1. Navigáljon a **Event Hubs névtérhez** , majd navigáljon az **Event hubhoz**, amely a bejövő adataival rendelkezik. 
1. Válassza az **adatfeldolgozás** lehetőséget az Event hub lapon.  

    ![Adatcsempe feldolgozása](./media/process-data-azure-stream-analytics/process-data-tile.png)
1. Válassza a **Tallózás** lehetőséget a **valós idejű bepillantást az események** csempén. 

    ![Stream Analytics kiválasztása](./media/process-data-azure-stream-analytics/process-data-page-explore-stream-analytics.png)
1. A következő mezőkhöz már beállított értékeket tartalmazó lekérdezési oldal jelenik meg:
    1. Az **Event hub** a lekérdezés bemenete.
    1. Minta **SQL-lekérdezés** a SELECT utasítással. 
    1. A lekérdezési teszt eredményeire hivatkozó **kimeneti** alias. 

        ![Lekérdezésszerkesztő](./media/process-data-azure-stream-analytics/query-editor.png)
        
        > [!NOTE]
        >  Ha első alkalommal használja ezt a funkciót, ez a lap kéri, hogy az Ön engedélye legyen a fogyasztói csoportok létrehozására, valamint az Event hub házirendjét a beérkező adatmegjelenítéshez.
1. Válassza a **Létrehozás** lehetőséget a **bemeneti előnézet** ablaktáblán az előző képen látható módon. 
1. Ezen a lapon azonnal megtekintheti a legfrissebb beérkező adatok pillanatképét.
    - A rendszer automatikusan észleli a szerializálási típust az adataiban (JSON/CSV). Manuálisan is módosíthatja azt JSON/CSV/AVRO.
    - A bejövő adatértékeket táblázatos formátumban vagy nyers formátumban is megtekintheti. 
    - Ha a megjelenített adatai nem aktuálisak, válassza a **frissítés** lehetőséget a legújabb események megtekintéséhez. 

        Íme egy példa a **táblázat formátumára**:   ![ a táblázat formátumát eredményezi.](./media/process-data-azure-stream-analytics/snapshot-results.png)

        Íme egy példa a **nyers formátumban** tárolt adatmennyiségre: 

        ![A nyers formátum eredménye](./media/process-data-azure-stream-analytics/snapshot-results-raw-format.png)
1. Válassza a teszt **lekérdezés** lehetőséget a lekérdezés eredményeinek a **teszteredmények** lapon való megjelenítéséhez. Az eredményeket letöltheti is.

    ![Lekérdezési eredmények tesztelése](./media/process-data-azure-stream-analytics/test-results.png)
1. Saját lekérdezést írhat az adatátalakításhoz. Lásd: [stream Analytics lekérdezés nyelvi leírása](/stream-analytics-query/stream-analytics-query-language-reference).
1. Miután tesztelte a lekérdezést, és az éles környezetben szeretné áthelyezni, válassza a **lekérdezés központi telepítése** lehetőséget. A lekérdezés üzembe helyezéséhez hozzon létre egy Azure Stream Analytics feladatot, amelyen beállíthatja a feladatok kimenetét, és elindíthatja a feladatot. Stream Analytics-feladatok létrehozásához adja meg a feladattípus nevét, majd válassza a **Létrehozás** lehetőséget.

      ![Azure Stream Analytics-feladat létrehozása](./media/process-data-azure-stream-analytics/create-stream-analytics-job.png)

      > [!NOTE] 
      >  Javasoljuk, hogy hozzon létre egy fogyasztói csoportot és egy szabályzatot minden olyan új Azure Stream Analytics feladatokhoz, amelyet a Event Hubs lapról hoz létre. A fogyasztói csoportok csak öt egyidejű olvasót engedélyeznek, így az egyes feladatokhoz egy dedikált fogyasztói csoportot biztosítanak, így elkerülhetők a korlátot meghaladó hibák. A dedikált szabályzat lehetővé teszi a kulcs elforgatását vagy az engedélyek visszavonását anélkül, hogy ez hatással lenne más erőforrásokra. 
1. Ekkor létrejön a Stream Analytics-feladata, ahol a lekérdezés ugyanaz, mint amit tesztelt, és a bemenet az Event hub. 

9.  A folyamat befejezéséhez állítsa be a lekérdezés **kimenetét** , majd az **Indítás** gombra kattintva indítsa el a feladatot.

    > [!NOTE]
    > A művelet megkezdése előtt ne felejtse el lecserélni a outputalias az Azure Stream Analyticsban létrehozott kimeneti név alapján.

      ![Állítsa be a kimenetet, és indítsa el a feladatot](./media/process-data-azure-stream-analytics/set-output-start-job.png)


## <a name="known-limitations"></a>Ismert korlátozások
A lekérdezés tesztelésekor a teszt körülbelül 6 másodpercet vesz igénybe a betöltéshez. Dolgozunk a tesztelés teljesítményének javításán. Éles környezetben történő üzembe helyezéskor azonban a Azure Stream Analytics a másodlagos késleltetést is elvégzi.

## <a name="streaming-units"></a>Streamelési egységek
A Azure Stream Analytics-feladatainak alapértelmezett értéke három folyamatos átviteli egység (SUs). A beállítás módosításához válassza a Azure Portal **stream Analytics feladatok** lapjának bal oldali menüjének **Méretezés** elemét. További információ a folyamatos átviteli egységekről: a [folyamatos átviteli egységek ismertetése és módosítása](../stream-analytics/stream-analytics-streaming-unit-consumption.md).

![Folyamatos átviteli egységek méretezése](./media/process-data-azure-stream-analytics/scale.png)

## <a name="next-steps"></a>Következő lépések
Stream Analytics lekérdezésekkel kapcsolatos további tudnivalókért tekintse meg a [stream Analytics lekérdezési nyelvét](/stream-analytics-query/built-in-functions-azure-stream-analytics) ismertető témakört.
