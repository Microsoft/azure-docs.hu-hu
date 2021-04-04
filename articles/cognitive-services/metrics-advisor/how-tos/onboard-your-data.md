---
title: Adatcsatorna előkészítése a metrikai TANÁCSADÓBA
titleSuffix: Azure Cognitive Services
description: Ismerkedés az adatcsatornák metrikai TANÁCSADÓBA való bevezetésével.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/14/2020
ms.author: mbullwin
ms.openlocfilehash: fe3b87c733f54d8bd52c4d973977e3c8cbfefe19
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "92043203"
---
# <a name="how-to-onboard-your-metric-data-to-metrics-advisor"></a>Útmutató: a metrikai adatok előkészítése a metrikai TANÁCSADÓBA

Ebből a cikkből megtudhatja, hogyan hozhatja be az adatait a metrikai TANÁCSADÓBA. 

## <a name="data-schema-requirements-and-configuration"></a>Az Adatséma követelményei és konfigurációja

[!INCLUDE [data schema requirements](../includes/data-schema-requirements.md)]

## <a name="avoid-loading-partial-data"></a>Ne töltsön be részleges adatbevitelt

A részleges adatokat a metrikai tanácsadóban tárolt adatok és az adatforrás közötti következetlenségek okozzák. Ez akkor fordulhat elő, ha az adatforrás frissül, miután a metrikai tanácsadó befejezte az adatok lekérését. A metrikai tanácsadó csak egyszer kéri le az adatokat egy adott adatforrásból.

Ha például egy metrika a metrikai TANÁCSADÓBA lett bevezetve a figyeléshez. A metrikai tanácsadó sikeresen megragadja A metrikus adatokat az A időbélyegzőn, és elvégzi a anomáliák észlelését. Ha azonban az adott időbélyeghez tartozó metrikus adatok az adatok betöltését követően frissültek. Nem lehet beolvasni az új adatértéket.

A korábbi adatokat (lásd később) a következetlenségek enyhítése érdekében [backfill](manage-data-feeds.md#backfill-your-data-feed) , de ez nem vált ki új anomália-riasztásokat, ha az adott időpontokra vonatkozó riasztások már aktiválva lettek. Ez a folyamat további számítási feladatokat is hozzáadhat a rendszerhez, és nem automatikus.

A részleges adatbevitel elkerülése érdekében két módszert ajánlunk:

* Adatszolgáltatások egy tranzakcióban:

    Győződjön meg arról, hogy az adott időbélyegen belül az összes dimenzió-kombináció metrikai értéke egy tranzakcióban az adatforrásban van tárolva. A fenti példában várjon, amíg az összes adatforrás adatai készen állnak, majd egy tranzakcióban töltse be a metrikai TANÁCSADÓBA. A metrikák tanácsadója rendszeresen lekérdezheti az adatcsatornát, amíg az adatok sikeresen (vagy részlegesen) le nem állnak.

* Az adatfeldolgozás késleltetése a betöltési **idő eltolása** paraméter megfelelő értékének megadásával:

    Állítsa be az adatcsatornához tartozó betöltési **idő eltolási** paraméterét, hogy az adatmennyiséget addig késleltesse, amíg az adatai teljesen fel nem készülnek. Ez olyan adatforrások esetében lehet hasznos, amelyek nem támogatják az Azure Table Storage tranzakciókat. További részletekért lásd a [Speciális beállítások](manage-data-feeds.md#advanced-settings) című témakört.

## <a name="add-a-data-feed-using-the-web-based-workspace"></a>Adatcsatorna hozzáadása a webalapú munkaterületről

Miután bejelentkezett a metrikai tanácsadó portálra, és kiválasztja a munkaterületet, kattintson az első **lépések** elemre. Ezután a munkaterület főoldalán kattintson az **adatcsatorna hozzáadása** elemre a bal oldali menüben.

### <a name="add-connection-settings"></a>Kapcsolatbeállítások hozzáadása

Ezután adja meg az idősoros adatforrás összekapcsolásához szükséges paramétereket. 
* **Forrás típusa**: az adatforrások típusa, ahol az idősorozat-adatait tárolják.
* **Részletesség**: az idősorozat adatai között az egymást követő adatpontok közötti intervallum. Az aktuális metrikai tanácsadó a következőket támogatja: éves, havi, heti, napi, óránkénti és egyéni. A testreszabási beállítás által támogatott legalacsonyabb időköz 60 másodperc.
  * **Másodperc**: azon másodpercek száma, ameddig a *granularityName* beállítása *testreszabható*.
* Az **adatgyűjtés óta (UTC)**: az adatfeldolgozás alaptervének kezdési ideje. a *startOffsetInSeconds* gyakran egy eltolás hozzáadására használják az adatkonzisztencia elősegítése érdekében.

Ezután meg kell adnia az adatforrás kapcsolódási adatait, valamint az adatoknak a szükséges sémába való konvertálásához használt egyéni lekérdezéseket. További információ a többi mezőről és a különböző típusú adatforrások összekapcsolásáról: [adatcsatornák hozzáadása különböző adatforrásokból](../data-feeds-from-different-sources.md).

[!INCLUDE [query requirements](../includes/query-requirements.md)]

### <a name="verify-and-get-schema"></a>Séma ellenőrzése és lekérése

A kapcsolódási karakterlánc és a lekérdezési karakterlánc beállítása után válassza a **hitelesítés és a séma beolvasása** lehetőséget a kapcsolódás ellenőrzéséhez és a lekérdezés futtatásához, hogy az adatforrásból beolvassa az adatsémát. Általában eltarthat néhány másodpercig az adatforrás-kapcsolatoktól függően. Ha hiba lép fel ebben a lépésben, győződjön meg arról, hogy:

* A kapcsolatok karakterlánca és a lekérdezés helyes.
* A metrikai tanácsadó példánya képes csatlakozni az adatforráshoz, ha vannak tűzfalbeállítások.

### <a name="schema-configuration"></a>Séma konfigurációja

Az Adatséma betöltését követően válassza ki a megfelelő mezőket.

Ha az adatpont időbélyegzője ki van hagyva, a metrikák tanácsadója az adatpont betöltésének időbélyegét fogja használni. Minden adatcsatorna esetében legfeljebb egy oszlopot adhat meg időbélyegként. Ha üzenet jelenik meg arról, hogy egy oszlop nem adható meg időbélyegként, ellenőrizze a lekérdezést vagy az adatforrást, valamint azt, hogy van-e több időbélyeg a lekérdezés eredményében – nem csak az előnézeti adatként. Az adatok betöltésének végrehajtásakor a metrikai tanácsadó csak egy adathalmazt tud használni (például egy nap, egy óra – a részletességnek megfelelően) az idősorozat-adatoknak a megadott forrásból való használatakor.

|Kiválasztás  |Leírás  |Jegyzetek  |
|---------|---------|---------|
| **Megjelenítendő név** | Az eredeti oszlopnév helyett a munkaterületen megjelenítendő név. | |
|**Időbélyeg**     | Egy adatpont időbélyege Ha nincs megadva, a metrikák tanácsadója az adatpont betöltésének időbélyegét fogja használni. Minden adatcsatorna esetében legfeljebb egy oszlopot adhat meg timestamp típusúként.        | Választható. Legfeljebb egy oszlopnak kell megadnia. Ha egy **oszlop nem** adható meg időbélyeg-hibaként, ellenőrizze a lekérdezést vagy az adatforrást ismétlődő időbélyegek esetén.      |
|**Measure**     |  Az adatcsatorna numerikus értékei. Minden adatcsatorna esetében több mértéket is megadhat, de legalább egy oszlopot ki kell jelölni mértékként.        | Legalább egy oszloppal kell megadni.        |
|**dimenzió**     | Kategorikus értékek. A különböző értékek kombinációja egy adott egydimenziós idősorozatot azonosít, például: ország, nyelv, bérlő. Nulla vagy több oszlopot is kijelölhet dimenzióként. Megjegyzés: Ügyeljen arra, hogy a nem karakterlánc típusú oszlopok dimenzióként való kiválasztásakor legyen körültekintő. | Választható.        |
|**Figyelmen kívül**     | A kijelölt oszlop figyelmen kívül hagyása.        | Választható. Lásd az alábbi szöveget.       |

Ha figyelmen kívül szeretné hagyni az oszlopokat, javasoljuk, hogy a lekérdezés vagy az adatforrás frissítésével zárja ki ezeket az oszlopokat. Az oszlopokat figyelmen kívül hagyhatja az **oszlopok mellőzése** lehetőséggel, majd **figyelmen kívül hagyhatja** az adott oszlopokat. Ha egy oszlopnak dimenziónak kell lennie, és a rendszer tévesen állítja be a *figyelmen kívül hagyott* adatokat, a metrikák tanácsadója a részleges adatok betöltését követően végezhető el. Tegyük fel például, hogy a lekérdezés adatai a következők:

| Sor azonosítója | Időbélyeg | Ország | Nyelv | Bevétel |
| --- | --- | --- | --- | --- |
| 1 | 2019/11/10 | Kína | ZH-CN | 10000 |
| 2 | 2019/11/10 | Kína | EN-US | 1000 |
| 3 | 2019/11/10 | USA | ZH-CN | 12000 |
| 4 | 2019/11/11 | USA | EN-US | 23000 |
| ... | ...| ... | ... | ... |

Ha az *ország* dimenzió és a *nyelv* a *figyelmen kívül hagyott* értékre van beállítva, akkor az első és a második sor azonos dimenziókkal fog rendelkezni. A metrikák tanácsadója tetszőlegesen használhat egy értéket a két sorból. A metrikai tanácsadó nem összesíti a sorokat ebben az esetben.

### <a name="automatic-roll-up-settings"></a>Automatikus összesítő beállítások

> [!IMPORTANT]
> Ha engedélyezni szeretné a kiváltó okok elemzését és más diagnosztikai képességeket, konfigurálnia kell az **automatikus összesítő beállításokat** . Ha engedélyezve van, az automatikus összesítő beállítások nem módosíthatók.

A metrikai tanácsadó automatikusan végrehajtja az összesítést (például SUM, MAX, MIN) az egyes dimenziókban a betöltés során, majd létrehoz egy hierarchiát, amelyet a rendszer a legfelső szintű elemzéshez és más diagnosztikai funkciókhoz fog használni. 

Vegyük példaként a következő forgatókönyveket:

* *Nem kell belefoglalni az adataim összesítő elemzését.*

    A metrikai tanácsadót nem kell használnia.

* *Az adataim már fel vannak vonva, és a dimenzióérték a következő: NULL vagy üres (alapértelmezett), csak NULL, mások.*

    Ez a beállítás azt jelenti, hogy a metrikai tanácsadónak nem kell felkészítenie az adatokat, mert a sorok már össze vannak összegezve. Ha például a *csak null értéket* választja, akkor az alábbi példában szereplő második adatsort az összes ország és nyelv összesítésének tekinti az *en-us*; a negyedik adatsort, amely üres értékkel rendelkezik az *ország* számára, azonban egy normál sorként jelenik meg, amely hiányos adathalmazt jelez.
    
    | Ország | Nyelv | Bevétel |
    |---------|----------|--------|
    | Kína   | ZH-CN    | 10000  |
    | NULL  | EN-US    | 999999 |
    | USA      | EN-US    | 12000  |
    |         | EN-US    | 5000   |

* *Metrikus tanácsadóra van szükségem az adatok összesítéséhez a Sum/max/min/Átl/Count kiszámításával, és a következő jelöléssel <some string>*

    Egyes adatforrások, például a Cosmos DB vagy az Azure Blob Storage nem támogatnak bizonyos számításokat, például *a Group By vagy a* *Cube* típust. A metrikai tanácsadó biztosítja az adatkockának a betöltés során történő automatikus létrehozásához szükséges összesítő beállítást.
    Ez a beállítás azt jelenti, hogy metrikai tanácsadóra van szükség, hogy kiszámítsa a felskálázást a kiválasztott algoritmus használatával, és a megadott karakterláncot használja a metrikai tanácsadóban való megjelenítéshez. Ez nem változtatja meg az adatforrásban lévő összes adathalmazt.
    Tegyük fel például, hogy van egy idősorozata, amely a dimenzióval (ország, régió) rendelkező értékesítési mérőszámokra áll. Egy adott időbélyeg esetében a következőhöz hasonló lehet:


    | Country       | Region           | Sales |
    |---------------|------------------|-------|
    | Kanada        | Alberta          | 100   |
    | Kanada        | Brit Columbia | 500   |
    | Egyesült Államok | Montana          | 100   |


    Miután engedélyezte az automatikus összesítést az *összeggel*, a metrikus tanácsadó kiszámítja a dimenzió kombinációkat, és összegzi a metrikákat az adatok betöltése során. Az eredmény lehet:

    | Country       | Region           | Sales |
    | ------------ | --------------- | ---- |
    | Kanada        | Alberta          | 100   |
    | NULL          | Alberta          | 100   |
    | Kanada        | Brit Columbia | 500   |
    | NULL          | Brit Columbia | 500   |
    | Egyesült Államok | Montana          | 100   |
    | NULL          | Montana          | 100   |
    | NULL          | NULL             | 700   |
    | Kanada        | NULL             | 600   |
    | Egyesült Államok | NULL             | 100   |

    `(Country=Canada, Region=NULL, Sales=600)` a Kanada (az összes régió) értékesítésének összegét jelenti, 600.

    A következő a transzformáció az SQL nyelvben.

    ```mssql
    SELECT
        dimension_1,
        dimension_2,
        ...
        dimension_n,
        sum (metrics_1) AS metrics_1,
        sum (metrics_2) AS metrics_2,
        ...
        sum (metrics_n) AS metrics_n
    FROM
        each_timestamp_data
    GROUP BY
        CUBE (dimension_1, dimension_2, ..., dimension_n);
    ```

    Az automatikus összesítő funkció használata előtt vegye figyelembe a következőket:

    * Ha az *összeget* az adatok összesítéséhez szeretné használni, győződjön meg róla, hogy a metrikák az egyes dimenziókban adalékanyagok. Íme néhány példa a *nem additív* mérőszámokra:
      * Tört-alapú mérőszámok. Ide tartozik az arány, a százalék stb. Például ne adja hozzá az egyes állapotok munkanélküliségi arányát a teljes ország munkanélküliségi arányának kiszámításához.
      * Átfedés a dimenzióban. Például ne adja hozzá az egyes sportágakhoz tartozó személyek számát a sporthoz hasonló személyek számának kiszámításához, mert átfedések vannak egymás között, egy személy több sporthoz is hasonló lehet.
    * A teljes rendszer állapotának biztosítása érdekében a kocka mérete korlátozott. Jelenleg a korlát 1 000 000. Ha az adatai túllépik ezt a korlátot, a betöltés sikertelen lesz az adott időbélyegnél.

## <a name="advanced-settings"></a>Speciális beállítások

Több speciális beállítás is lehetővé teszi az adatfeldolgozás testreszabott módon történő betöltését, például a betöltési eltolás vagy a Egyidejűség megadását. További információkért lásd az adatcsatorna-kezelés [Speciális beállítások](manage-data-feeds.md#advanced-settings) című szakaszát.

## <a name="specify-a-name-for-the-data-feed-and-check-the-ingestion-progress"></a>Adja meg az adatcsatorna nevét, és vizsgálja meg a betöltési folyamatot
 
Adja meg az adatcsatorna egyéni nevét, amely a munkaterületen fog megjelenni. Ezután kattintson a **Submit (Küldés**) gombra. Az adatcsatorna részletei lapon a betöltési folyamatjelző sáv használatával megtekintheti az állapotadatok adatait.

:::image type="content" source="../media/datafeeds/ingestion-progress.png" alt-text="Betöltési folyamatjelző sáv" lightbox="../media/datafeeds/ingestion-progress.png":::


A betöltési hiba részleteinek megtekintése: 

1. Kattintson a **Részletek megjelenítése** lehetőségre.
2. Kattintson az **állapot** elemre, majd válassza a **sikertelen** vagy a **hiba** lehetőséget.
3. Vigye az egérmutatót egy sikertelen betöltés fölé, és tekintse meg a megjelenő részleteket tartalmazó üzenetet.

:::image type="content" source="../media/datafeeds/check-failed-ingestion.png" alt-text="Sikertelen betöltési vizsgálat":::

A *sikertelen* állapot azt jelzi, hogy az adatforrás betöltését később újra megkísérli a rendszer.
A *hiba* állapota azt jelzi, hogy a metrikai tanácsadó nem próbálkozik újra az adatforrással. Az adatfrissítéshez manuálisan kell backfill/újratöltést indítania.

A betöltés előrehaladását a **frissítés folyamata** lehetőségre kattintva is betöltheti. Az adatfeldolgozás befejezése után a metrikák elemre kattintva és a anomáliák észlelési eredményeinek ellenőrzésével is elvégezhető.

## <a name="next-steps"></a>Következő lépések
- [Adatcsatornák kezelése](manage-data-feeds.md)
- [Különböző adatforrások beállításai](../data-feeds-from-different-sources.md)
- [Metrikák konfigurálása és a konfiguráció észlelésének finomhangolása](configure-metrics.md)
