---
title: Irányítópult-integráció Power BI Azure Stream Analytics
description: Ez a cikk azt ismerteti, hogyan lehet valós idejű Power BI irányítópultot használni az adatok Azure Stream Analytics feladatokból való megjelenítéséhez.
author: jseb225
ms.author: jeanb
ms.service: stream-analytics
ms.topic: how-to
ms.date: 11/16/2020
ms.openlocfilehash: 3bd35df91e836245de52d8959dff0671582ebc3f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98012444"
---
# <a name="stream-analytics-and-power-bi-a-real-time-analytics-dashboard-for-streaming-data"></a>Stream Analytics és Power BI: valós idejű elemzési irányítópult az adatfolyam-továbbításhoz

Azure Stream Analytics lehetővé teszi, hogy kihasználhassa az egyik vezető üzleti intelligencia-eszközt, a [Microsoft Power BIt](https://powerbi.com/). Ebből a cikkből megtudhatja, hogyan hozhat létre az üzleti intelligencia-eszközöket a Azure Stream Analytics feladatok kimenetének Power BI használatával. Azt is megtudhatja, hogyan hozhat létre és használhat olyan valós idejű irányítópultot, amelyet folyamatosan frissít a Stream Analytics-feladatokkal.

Ez a cikk a Stream Analytics [valós idejű csalások észlelését](stream-analytics-real-time-fraud-detection.md) ismertető oktatóanyagban folytatódik. Ez az oktatóanyagban létrehozott munkafolyamatra épül, és hozzáadja a Power BI kimenetet, hogy megjelenítse a streaming Analytics-feladatok által észlelt csalárd telefonhívásokat. 

Megtekintheti [a](https://www.youtube.com/watch?v=SGUpT-a99MA)  forgatókönyvet bemutató videót.


## <a name="prerequisites"></a>Előfeltételek

Mielőtt hozzálátna, győződjön meg róla, hogy rendelkezik az alábbiakkal:

* Egy Azure-fiók.
* Egy fiók a Power BI Prohoz. Munkahelyi fiókot vagy iskolai fiókot is használhat.
* A [valós idejű csalások észlelésére](stream-analytics-real-time-fraud-detection.md) vonatkozó oktatóanyag befejezett verziója. Az oktatóanyag egy olyan alkalmazást tartalmaz, amely fiktív telefonhívási metaadatokat hoz létre. Az oktatóanyagban létrehoz egy Event hubot, és elküldi a streaming Phone-hívási adatátvitelt az Event hub-nak. Olyan lekérdezést kell írnia, amely észleli a csalárd hívásokat (az azonos számú hívások a különböző helyeken található azonos időpontban). 


## <a name="add-power-bi-output"></a>Power BI kimenet hozzáadása
A valós idejű csalások észlelését ismertető oktatóanyagban a kimenetet az Azure Blob Storage-ba küldi a rendszer. Ebben a szakaszban olyan kimenetet ad hozzá, amely adatokat küld a Power BInak.

1. A Azure Portal nyissa meg a korábban létrehozott streaming Analytics-feladatot. Ha a javasolt nevet használta, a rendszer elnevezi a feladatot `sa_frauddetection_job_demo` .

2. A bal oldali menüben válassza a **kimenetek** lehetőséget a **feladatok topológiája** alatt. Ezután válassza a **+ Hozzáadás** lehetőséget, majd a legördülő menüből válassza a **Power bi** lehetőséget.

3. Válassza a **+**  >  **Power bi** hozzáadása elemet. Ezután töltse ki az űrlapot a következő adatokkal, és válassza az **Engedélyezés** lehetőséget a saját felhasználói identitásának használatára a Power BIhoz való kapcsolódáshoz (a jogkivonat 90 napig érvényes). 

>[!NOTE]
>Üzemi feladatokhoz javasoljuk, hogy a [felügyelt identitás használatára való kapcsolódással hitelesítse Azure stream Analytics-feladatát Power bi](./powerbi-output-managed-identity.md).

   |**Beállítás**  |**Ajánlott érték**  |
   |---------|---------|
   |Kimeneti alias  |  CallStream-PowerBI  |
   |Adatkészlet neve  |   SA-adatkészlet  |
   |Table name (Táblázat neve) |  csalárd – hívások  |

   ![Stream Analytics kimenet konfigurálása](media/stream-analytics-power-bi-dashboard/configure-stream-analytics-output.png)

   > [!WARNING]
   > Ha Power BI rendelkezik olyan adatkészlettel és táblával, amelynek neve megegyezik a Stream Analytics feladatban megadott névvel, a rendszer felülírja a meglévőket.
   > Azt javasoljuk, hogy ne hozzon létre explicit módon ezt az adatkészletet és táblát a Power BI-fiókjában. Ezek automatikusan létrejönnek, amikor elindítja a Stream Analytics feladatot, és a művelet elindítja a kimenetet Power BIba. Ha a feladathoz tartozó lekérdezés nem ad vissza eredményt, az adatkészlet és a tábla nem jön létre.
   >

4. Miután kiválasztotta az **Engedélyezés** lehetőséget, megjelenik egy előugró ablak, ahol meg kell adnia a hitelesítő adatait a Power BI-fiókja hitelesítéséhez. A sikeres hitelesítés után válassza a **Mentés** lehetőséget a beállítások mentéséhez.

8. Kattintson a **Létrehozás** lehetőségre.

Az adatkészlet a következő beállításokkal jön létre:

* **defaultRetentionPolicy: BasicFIFO** – az adatmennyiség FIFO, amely legfeljebb 200 000 sorral rendelkezik.
* **defaultMode: hibrid** – az adatkészlet támogatja a folyamatos átviteli csempéket (más néven leküldéses) és a hagyományos jelentéseken alapuló vizualizációkat. A leküldéses tartalom esetében ebben az esetben az adatok folyamatosan frissülnek a stream Analytics-feladatból, és nem kell ütemezni a Power BI oldalról történő frissítést.

Jelenleg nem hozhat létre más jelzőket tartalmazó adatkészleteket.

Power BI adatkészletekkel kapcsolatos további információkért tekintse meg a [Power BI REST API](/rest/api/power-bi/) referenciát.


## <a name="write-the-query"></a>A lekérdezés írása

1. Zárjuk be a **kimenetek** panelt, és térjen vissza a feladatok panelhez.

2. Kattintson a **lekérdezés** mezőre. 

3. Adja meg a következő lekérdezést. Ez a lekérdezés hasonló a csalások észlelését ismertető oktatóanyagban létrehozott saját illesztési lekérdezéshez. A különbség az, hogy ez a lekérdezés eredményeket küld a létrehozott új kimenetnek ( `CallStream-PowerBI` ). 

    >[!NOTE]
    >Ha nem adta meg a bevitelt `CallStream` a csalások észlelése oktatóanyagban, akkor a `CallStream` lekérdezésben szereplő **from** és **JOIN** záradékban helyettesítse be a nevét.

   ```SQL
   /* Our criteria for fraud:
   Calls made from the same caller to two phone switches in different locations (for example, Australia and Europe) within five seconds */

   SELECT System.Timestamp AS WindowEnd, COUNT(*) AS FraudulentCalls
   INTO "CallStream-PowerBI"
   FROM "CallStream" CS1 TIMESTAMP BY CallRecTime
   JOIN "CallStream" CS2 TIMESTAMP BY CallRecTime

   /* Where the caller is the same, as indicated by IMSI (International Mobile Subscriber Identity) */
   ON CS1.CallingIMSI = CS2.CallingIMSI

   /* ...and date between CS1 and CS2 is between one and five seconds */
   AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5

   /* Where the switch location is different */
   WHERE CS1.SwitchNum != CS2.SwitchNum
   GROUP BY TumblingWindow(Duration(second, 1))
   ```

4. Kattintson a **Mentés** gombra.


## <a name="test-the-query"></a>A lekérdezés tesztelése

Ez a szakasz nem kötelező, de ajánlott. 

1. Ha a TelcoStreaming alkalmazás jelenleg nem fut, indítsa el a következő lépéseket:

    * Nyisson meg egy parancssort.
    * Lépjen arra a mappára, ahol a telcogenerator.exe és a módosított telcodatagen.exe.config fájlok találhatók.
    * Futtassa az alábbi parancsot:

       `telcodatagen.exe 1000 .2 2`

2. A Stream Analytics-feladathoz tartozó **lekérdezés** lapon kattintson a bemenet melletti pontokra, `CallStream` majd válassza a **mintaadatok bemenetből** lehetőséget.

3. Itt adhatja meg, hogy három perces értékű adatot kíván használni, majd kattintson **az OK** gombra. Várjon, amíg a rendszer értesíti arról, hogy az adatok mintavételezése befejeződött.

4. Kattintson a **teszt** gombra, és tekintse át az eredményeket.

## <a name="run-the-job"></a>A feladat futtatása

1. Győződjön meg arról, hogy a TelcoStreaming alkalmazás fut.

2. Navigáljon a Stream Analytics-feladatokhoz tartozó **Áttekintés** lapra, és válassza az **Indítás** lehetőséget.

    ![A Stream Analytics-feladatok elindítása](./media/stream-analytics-power-bi-dashboard/stream-analytics-sa-job-start-output.png)

A streaming Analytics-feladatok a bejövő adatfolyamban megjelenő csalárd hívásokat keresik. A feladattal létrejön a Power BI adatkészlet és táblázat is, és megkezdi az adatok küldését a hamis hívásokról.


## <a name="create-the-dashboard-in-power-bi"></a>Az irányítópult létrehozása Power BI

1. Nyissa meg a [Powerbi.com](https://powerbi.com) , és jelentkezzen be munkahelyi vagy iskolai fiókjával. Ha a Stream Analytics feladatok lekérdezése kimenetet eredményez, láthatja, hogy az adatkészlet már létre van hozva:

    ![Folyamatos átviteli adatkészlet helye Power BI](./media/stream-analytics-power-bi-dashboard/stream-analytics-streaming-dataset.png)

2. A munkaterületen kattintson a **+ &nbsp; Létrehozás** gombra.

    ![A létrehozás gomb Power BI munkaterületen](./media/stream-analytics-power-bi-dashboard/pbi-create-dashboard.png)

3. Hozzon létre egy új irányítópultot, és nevezze el `Fraudulent Calls` .

    ![Irányítópult létrehozása és név megadása Power BI munkaterületen](./media/stream-analytics-power-bi-dashboard/pbi-create-dashboard-name.png)

4. Az ablak tetején kattintson a **csempe hozzáadása** elemre, válassza az **Egyéni adatfolyam**-adatátvitelek lehetőséget, majd kattintson a **tovább** gombra.

    ![Egyéni folyamatos átviteli adatkészlet csempe Power BI](./media/stream-analytics-power-bi-dashboard/custom-streaming-data.png)

5. **A DATSETS** területen válassza ki az adatkészletet, majd kattintson a **tovább** gombra.

    ![A folyamatos átviteli adatkészlet Power BI](./media/stream-analytics-power-bi-dashboard/your-streaming-dataset.png)

6. A **vizualizáció típusa** területen válassza a **kártya** lehetőséget, majd a **mezők** listában válassza a **fraudulentcalls** lehetőséget.

    ![Új csempe vizualizációs adatai](./media/stream-analytics-power-bi-dashboard/add-fraudulent-calls-tile.png)

7. Kattintson a **Tovább** gombra.

8. Töltse ki a csempe részleteit, például egy címet és egy feliratot.

    ![Új csempe címe és alcíme](./media/stream-analytics-power-bi-dashboard/pbi-new-tile-details.png)

9. Kattintson az **Alkalmaz** gombra.

    Most már van egy csalás elleni számláló!

    ![Csalási számláló Power BI irányítópulton](./media/stream-analytics-power-bi-dashboard/power-bi-fraud-counter-tile.png)

8. Kövesse újra a lépéseket a csempék hozzáadásához (a 4. lépéstől kezdve). Ezúttal tegye a következőket:

    * Ha a **vizualizáció típusát** választja, válassza a **vonal diagram** lehetőséget. 
    * Adjon hozzá egy tengelyt, és válassza ki **windowend** lehetőséget. 
    * Adjon meg egy értéket, és válassza a **fraudulentcalls** lehetőséget.
    * A **Megjelenítendő időtartomány** beállításnál válassza ki az utolsó 10 percet.

      ![Csempe létrehozása diagramhoz Power BI](./media/stream-analytics-power-bi-dashboard/pbi-create-tile-line-chart.png)

9. Kattintson a **tovább** gombra, adjon meg egy címet és egy feliratot, majd kattintson az **alkalmaz** gombra.

     A Power BI irányítópultja mostantól két, az adatfolyamban észlelt, csalárd hívásokkal kapcsolatos információt nyújt.

     ![Elkészült Power BI irányítópulton két csempe látható a csalárd hívásokhoz](./media/stream-analytics-power-bi-dashboard/pbi-dashboard-fraudulent-calls-finished.png)

## <a name="learn-about-limitations-and-best-practices"></a>További tudnivalók a korlátozásokról és az ajánlott eljárásokról
Jelenleg Power BI nagyjából egyszer hívható meg másodpercenként. A streaming vizualizációk 15 KB-os csomagokat támogatnak. Ezen túlmenően a streaming vizualizációk meghiúsulnak (de a leküldések továbbra is működni fognak). Ezeknek a korlátozásoknak a miatt a Power BI a legtermészetesebb esetben olyan esetekhez nyújtja magát, amikor az Azure Stream Analytics jelentős adatterhelést eredményez. Azt javasoljuk, hogy a kiugró ablak vagy a ugráló ablak használatával ellenőrizze, hogy az adatküldés másodpercenként legfeljebb egy leküldéses legyen, és hogy a lekérdezés az átviteli sebességre vonatkozó követelményeken belül landol-e.

A következő egyenlettel számíthatja ki az értéket, hogy másodpercek alatt adja meg az ablakot:

![A számítási értékhez tartozó egyenlet, amely másodpercek alatt megadja az ablakokat](./media/stream-analytics-power-bi-dashboard/compute-window-seconds-equation.png)  

Például:

* A 1 000-es eszközök egymásodperces időközönként küldik el az adatokat.
* Az Power BI Pro SKU-t használja, amely óránként 1 000 000 sort támogat.
* Egy eszköz átlagos adatmennyiségét szeretné közzétenni Power BI.

Ennek eredményeképpen az egyenlet a következőképpen változik:

![Példa a feltételek alapján](./media/stream-analytics-power-bi-dashboard/power-bi-example-equation.png)  

Ebben a konfigurációban az eredeti lekérdezést a következőre módosíthatja:

```SQL
    SELECT
        MAX(hmdt) AS hmdt,
        MAX(temp) AS temp,
        System.TimeStamp AS time,
        dspl
    INTO "CallStream-PowerBI"
    FROM
        Input TIMESTAMP BY time
    GROUP BY
        TUMBLINGWINDOW(ss,4),
        dspl
```

### <a name="renew-authorization"></a>Engedélyezés megújítása
Ha a jelszó megváltozott a feladatok létrehozása vagy utolsó hitelesítése óta, újra kell hitelesítenie Power BI-fiókját. Ha az Azure AD-Multi-Factor Authentication konfigurálva van a Azure Active Directory (Azure AD) bérlőn, akkor két hetente meg kell újítania Power BI engedélyt. Ha nem újítja meg, olyan tüneteket láthat, mint például a feladatok kimenetének hiánya vagy `Authenticate user error` a műveleti naplók.

Hasonlóképpen, ha egy feladatot a jogkivonat lejárta után is elindít, hiba történik, és a művelet meghiúsul. A probléma megoldásához állítsa le a futó feladatot, és nyissa meg a Power BI kimenetét. Az adatvesztés elkerülése érdekében válassza az **Engedélyezés megújítása** hivatkozást, majd indítsa újra a feladatot az **utolsó leállítási időpontból**.

Miután az engedélyezést Power BItel frissítette, egy zöld riasztás jelenik meg az engedélyezési területen, hogy tükrözze a probléma megoldását.

## <a name="next-steps"></a>Következő lépések
* [Bevezetés a Azure Stream Analyticsba](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Stream Analytics kimenetek](stream-analytics-define-outputs.md)
* [Azure Stream Analytics lekérdezés nyelvi referenciája](/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics felügyeleti REST API referenciája](/rest/api/streamanalytics/)
* [Felügyelt identitás használata a Azure Stream Analytics-feladatok hitelesítéséhez Power BI](./powerbi-output-managed-identity.md)
