---
title: Azure Monitor munkafüzet-leképezési vizualizációk
description: Ismerkedjen meg Azure Monitor munkafüzet térképi vizualizációkkal.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 11/25/2020
ms.author: lagayhar
ms.openlocfilehash: dd9bafab982923785898b7f135e5c0408aa19a14
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100615988"
---
# <a name="map-visualization"></a>Térkép vizualizáció

A Térkép vizualizációja az adott régiókban a PIN-kóddal kapcsolatos problémákat mutatja be, és a figyelési adatokat magas szintű összesített nézeteket jeleníti meg azáltal, hogy lehetővé teszi az egyes helyekre/országokra/régiókra leképezett összes adatmennyiség összesítését.

Az alábbi képernyőképen a különböző tárolási fiókok teljes tranzakciói és végpontok közötti késése látható. Itt a méretet a tranzakciók teljes száma határozza meg, a térkép alatti színmetrikák pedig a végpontok közötti késést mutatják. Első megfigyeléskor az USA **nyugati** régiójában lévő tranzakciók száma kicsi az **USA keleti** régiójához képest, de az USA **nyugati** régiója felé irányuló teljes késés az **USA keleti** régiójában is magasabb. Ez kezdeti betekintést nyújt arra, hogy valami nincs rendjén az **USA nyugati** régiójában.

![Az Azure Location Map képernyőképe](./media/workbooks-map-visualizations/map-performance-example.png)

## <a name="adding-a-map"></a>Térkép hozzáadása

A Térkép megjeleníthetők, ha a mögöttes adatok/mérőszámok szélességi/hosszúsági információkkal, Azure-erőforrásokkal kapcsolatos információkkal, Azure-beli helyekkel kapcsolatos információkkal vagy ország/régióval, névvel vagy ország/régió kóddal rendelkeznek.

### <a name="using-azure-location"></a>Az Azure Location használata

1. Az eszköztár **szerkesztése** elemre kattintva váltson át a munkafüzet szerkesztési módjára.
2. Válassza a **Hozzáadás** , majd a *lekérdezés hozzáadása* lehetőséget.
3. Módosítsa az *adatforrást* , `Azure Resource Graph` majd válassza ki a Storage-fiókkal rendelkező előfizetéseket.
4. Az elemzéshez és a **futtatási lekérdezés** kiválasztása elemhez adja meg az alábbi lekérdezést.

    ```kusto
    where  type =~ 'microsoft.storage/storageaccounts'
    | summarize count() by location
    ```

5. A *méret* beállítása a következőre: `Large` .
6. Állítsa be a *vizualizációt* a következőre: `Map` .
7. Az összes beállítás automatikusan ki lesz töltve. Az egyéni beállítások lapon kattintson a **Térkép beállításai** gombra a beállítások ablaktábla megnyitásához.
8. Az alábbiakban látható a Térkép vizualizációja, amely az egyes Azure-régiókhoz tartozó Storage-fiókokat jeleníti meg a kiválasztott előfizetéshez.

![Képernyőkép az Azure Location Map-ről a fenti lekérdezéssel](./media/workbooks-map-visualizations/map-azure-location-example.png)

### <a name="using-azure-resource"></a>Az Azure-erőforrás használata

1. Az eszköztár **szerkesztése** elemre kattintva váltson át a munkafüzet szerkesztési módjára.
2. Válassza a **Hozzáadás** , majd a *metrika hozzáadása* elemet.
3. Használjon Storage-fiókkal rendelkező előfizetést.
4. Módosítsa az *erőforrás típusát* a (z `storage account` ) *erőforrásra* , és válassza a több Storage-fiók lehetőséget.
5. Válassza a **metrika hozzáadása** és a tranzakciós metrika hozzáadása elemet.
    1. Névtér: `Account`
    2. Metrika `Transactions`
    3. Összesítési `Sum`
    
    ![A tranzakciós metrika képernyőképe](./media/workbooks-map-visualizations/map-transaction-metric.png)
1. Válassza a **metrika hozzáadása** lehetőséget, és adja hozzá a sikeres E2E késés metrikáját.
    1. Névtér: `Account`
    1. Metrika `Success E2E Latency`
    1. Összesítési `Average`
    
    ![A sikeres végpontok közötti késleltetési metrika képernyőképe](./media/workbooks-map-visualizations/map-e2e-latency-metric.png)
1. A *méret* beállítása a következőre: `Large` .
1. *Vizualizáció* méretének beállítása a következőre: `Map` .
1. A **Térkép beállításainál** állítsa be a következő beállításokat:
    1. Helyadatok a használatával: `Azure Resource`
    1. Azure-erőforrás mező: `Name`
    1. Méret: `microsoft.storage/storageaccounts-Transaction-Transactions`
    1. A hely összesítése: `Sum of values`
    1. Színezés típusa: `Heatmap`
    1. Szín: `microsoft.storage/storageaccounts-Transaction-SuccessE2ELatency`
    1. Összesítés a színhez: `Sum of values`
    1. Színpaletta: `Green to Red`
    1. Minimális érték: `0`
    1. Metrika értéke: `microsoft.storage/storageaccounts-Transaction-SuccessE2ELatency`
    1. Egyéb mérőszámok összesítése: `Sum of values`
    1. Válassza az **egyéni formázási** mezőt
    1. Egység `Milliseconds`
    1. Stílusa `Decimal`
    1. Maximális tört számjegyek: `2`

### <a name="using-countryregion"></a>Ország/régió használata

1. Az eszköztár **szerkesztése** elemre kattintva váltson át a munkafüzet szerkesztési módjára.
2. Válassza a **Hozzáadás*, majd a *lekérdezés hozzáadása* lehetőséget.
3. Az *adatforrás* módosítása a következőre: `Log` .
4. Válassza az *Erőforrás típusa* lehetőséget `Application Insights` , majd válasszon olyan Application Insights erőforrást, amely oldalmegtekintési adattal rendelkezik.
5. A lekérdezéstervező segítségével adja meg az elemzés KQL, és válassza a **lekérdezés futtatása** lehetőséget.

    ```kusto
    pageViews
    | project duration, itemCount, client_CountryOrRegion
    | limit 20
    ```

6. Állítsa a méret értékeket a következőre: `Large` .
7. Állítsa be a vizualizációt a következőre: `Map` .
8. Az összes beállítás automatikusan ki lesz töltve. Egyéni beállítások esetén válassza a **Térkép beállításai** lehetőséget.

### <a name="using-latitudelocation"></a>Szélesség/hely használata

1. Az eszköztár **szerkesztése** elemre kattintva váltson át a munkafüzet szerkesztési módjára.
2. Válassza a **Hozzáadás*, majd a *lekérdezés hozzáadása* lehetőséget.
3. Az *adatforrás* módosítása a következőre: `JSON` .
1. A lekérdezés-szerkesztőben adja meg a JSON-adatfájlokat, és válassza a **lekérdezés futtatása** lehetőséget.
1. Állítsa a *méret* értékeket a következőre: `Large` .
1. Állítsa be a *vizualizációt* a következőre: `Map` .
1. A "metrika beállításai" alatt a **Térkép beállításainál** állítsa be a *metrika címkét* , `displayName` majd válassza a **Mentés és bezárás** lehetőséget.

Az alábbi Térkép-vizualizáció az egyes szélességi és hosszúsági helyek felhasználóit jeleníti meg a metrikák kiválasztott címkéjével.

![Képernyőkép egy Térkép-vizualizációról, amely megjeleníti az egyes szélességi és hosszúsági helyekhez tartozó felhasználókat a metrikák kiválasztott címkéjével](./media/workbooks-map-visualizations/map-latitude-longitude-example.png)

## <a name="map-settings"></a>Térkép beállításai

### <a name="layout-settings"></a>Elrendezési beállítások

| Beállítás | Magyarázat |
|:-------------|:-------------|
| `Location info using` | Válassza ki a térképen megjelenített elemek helyét. <br> **Szélesség/hosszúság**: akkor válassza ezt a lehetőséget, ha a szélességi és a hosszúsági adatokat tartalmazó oszlopok vannak. A szélességi és a hosszúsági értékkel rendelkező sorok a térképen külön elemként jelennek meg. <br> **Azure**-beli hely: válassza ezt a lehetőséget, ha van olyan oszlop, amely az Azure Location (eastus, westeurope, centralindia stb.) adatokat tartalmaz. Adja meg az oszlopot, és az egyes Azure-helyek megfelelő szélességi és hosszúsági helyét fogja lekérni, és csoportosítsa az azonos helyen lévő sorokat (a megadott összesítés alapján), hogy megjelenjenek a helyek a térképen. <br> **Azure-erőforrás**: válassza ezt a lehetőséget, ha van olyan oszlop, amely rendelkezik Azure-erőforrás-információkkal (Storage-fiók, cosmosdb-fiók stb.). Adja meg az oszlopot, és lekéri az egyes Azure-erőforrások megfelelő szélességi és hosszúsági helyét, csoportosítsa ugyanazt a helyet (Azure Location) (a megadott összesítés alapján), hogy megjelenjenek a helyek a térképen. <br> **Ország/régió**: válassza ezt a lehetőséget, ha van olyan oszlop, amely az ország/régió neve/kódja (US, Egyesült Államok, in, India, CN, China) adatokat tartalmaz. Adja meg az oszlopot, és minden ország/régió/kód esetében beolvassa a megfelelő szélességet és hosszúságot, és csoportosítsa a sorokat ugyanazzal a Country-Region kóddal/régiónként, hogy megjelenjenek-e a térképen. Az ország neve és az országkód nem csoportosítható egyetlen entitásként a térképen.
| `Latitude/Longitude` | Ez a két beállítás akkor látható, ha a Location info mező értéke: szélesség/hosszúság. Válassza ki azt az oszlopot, amely a szélesség mezőben és a hosszúság mezőben a szélesség mezővel rendelkezik. |
| `Azure location field` | Ez a beállítás akkor látható, ha a Location info mező értéke: Azure-hely. Válassza ki az Azure-beli hely információit tartalmazó oszlopot. |
| `Azure resource field` | Ez a beállítás akkor látható, ha a Location info mező értéke: Azure-erőforrás. Válassza ki az Azure-erőforrás adatait tartalmazó oszlopot. |
| `Country/Region field` | Ez a beállítás akkor látható, ha a Location info mező értéke: ország vagy régió. Válassza ki az ország/régió információit tartalmazó oszlopot. |
| `Size by` | Ezzel a beállítással szabályozható a térképen megjelenített elemek mérete. A méret a felhasználó által megadott oszlopban szereplő értéktől függ. Jelenleg a kör sugara közvetlenül az oszlop értékének szögletes gyökerével arányos. Ha "nincs..." van kiválasztva, az összes kör megjeleníti az alapértelmezett régió méretét.|
| `Aggregation for location` | Ez a mező azt adja meg, hogyan lehet összesíteni a **méretet** olyan oszlopok alapján, amelyek azonos Azure-beli hely/Azure-erőforrás/ország területtel rendelkeznek. |
| `Minimum region size` | Ez a mező határozza meg, hogy mi a minimális sugár a térképen látható elemnek. Ez akkor használatos, ha az oszlop értékei között jelentős különbség van, így a kisebb elemek nem láthatók a térképen. |
| `Maximum region size` | Ez a mező határozza meg, hogy mi a maximális sugár a térképen megjelenített elemnél. Ezt akkor kell használni, ha a méret oszlop értékei rendkívül nagy méretűek, és a Térkép hatalmas területére terjednek ki.|
| `Default region size` | Ez a mező határozza meg, hogy mi a térképen látható elem alapértelmezett sugara. Az alapértelmezett RADIUS akkor van használatban, ha a méret oszlop szerint "nincs..." vagy az érték 0.|
| `Minimum value` | A régió méretének kiszámításához használt minimális érték. Ha nincs megadva, a minimális érték az Összesítés után a legkisebb érték lesz. |
| `Maximum value` | A régió méretének kiszámításához használt maximális érték. Ha nincs megadva, a maximális érték lesz a legnagyobb érték az Összesítés után.|
| `Opacity of items on Map` | Ez a mező határozza meg, hogy az átlátszó elemek hogyan jelenjenek meg a térképen. Az 1 érték opacitása – nincs áttetszőség, ahol a 0 opacitása azt jelenti, hogy az elemek nem lesznek láthatók a térképen. Ha túl sok elem van a térképen, a fedettség értéke alacsony érték lehet, hogy az átfedésben lévő elemek láthatók legyenek.|

### <a name="color-settings"></a>Színbeállítások

| Színezés típusa | Magyarázat |
|:------------- |:-------------|
| `None` | Minden csomópontnak azonos színnel kell rendelkeznie. |
| `Thresholds` | Ebben a típusban a cellák színét küszöbérték-szabályok határozzák meg (például _cpu > 90% => piros, 60% > CPU > 90% => sárga, CPU < 60% => zöld_). <ul><li> **Color by**: az oszlop értékét a küszöbértékek/hő logika fogja használni.</li> <li>**Összesítés a színhez**: Ez a mező azt adja meg, hogyan összesítheti a színeket olyan oszlopok **alapján** , amelyek ugyanazzal az Azure-beli hellyel/Azure-erőforrással/régióval rendelkeznek. </li> <ul> |
| `Heatmap` | Ebben a típusban a cellák színe a színpaletta és a szín mező alapján történik. Ugyanez a szín a színbeállításokkal és az **összesítéssel** is azonos lesz a küszöbértékek **alapján** . |

### <a name="metric-settings"></a>Metrika beállításai
| Beállítás | Magyarázat |
|:------------- |:-------------|
| `Metric Label` | Ez a beállítás akkor látható, ha a Location info mező értéke: szélesség/hosszúság. A szolgáltatás használatával a felhasználó kiválaszthatja a térkép alatti mérőszámok megjelenítéséhez használandó címkét. |
| `Metric Value` | Ez a mező a Térkép alatt megjeleníteni kívánt metrikai értéket adja meg. |
| `Create 'Others' group after` | Ez a mező a "mások" csoport létrehozása előtti korlátot határozza meg. |
| `Aggregate 'Others' metrics by` | Ez a mező határozza meg a "mások" csoportnál használt összesítést, ha az megjelenik. |
| `Custom formatting` | Ezzel a mezővel állíthatja be a számértékek egységeit, stílusát és formázási beállításait. Ez ugyanaz, mint a [rács egyéni formázása](../visualize/workbooks-grid-visualizations.md#custom-formatting).|

## <a name="next-steps"></a>Következő lépések

- Megtudhatja, hogyan hozhat létre [mézes fésű-vizualizációkat a munkafüzetekben](../visualize/workbooks-honey-comb.md).