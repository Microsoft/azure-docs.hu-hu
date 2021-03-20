---
title: Azure Cosmos DB Connector Power BI oktatóanyaga
description: Ez a Power BI oktatóanyag a JSON importálására, az éleslátó jelentések létrehozására és az adatmegjelenítésre az Azure Cosmos DB és Power BI összekötő használatával.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 05/21/2019
ms.author: sngun
ms.openlocfilehash: b27bab9ea3029264143caaacf094f0a799894356
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97359863"
---
# <a name="visualize-azure-cosmos-db-data-by-using-the-power-bi-connector"></a>Azure Cosmos DB-adatok vizualizációja a Power BI-összekötő használatával
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

A [Power bi](https://powerbi.microsoft.com/) egy online szolgáltatás, ahol irányítópultokat és jelentéseket hozhat létre és oszthat meg. A Power BI Desktop egy jelentéskészítő eszköz, amely lehetővé teszi a különböző adatforrásokból származó adatok lekérését. Az Azure Cosmos DB a Power BI Desktoppal használható egyik adatforrás. A Power BI Desktopot a Power BI-hoz készült Azure Cosmos DB-összekötővel csatlakoztathatja az Azure Cosmos DB-fiókhoz.  Miután importálta az Azure Cosmos DB-ben található adatokat a Power BI-ba, átalakíthatja őket, valamint jelentéseket hozhat létre, amelyeket közzé tehet a Power BI-ban.   

Ez a cikk az Azure Cosmos DB-fiók a Power BI Desktophoz való csatlakozásához szükséges lépéseket ismerteti. A csatlakoztatás után egy gyűjteményhez léphet, kinyerheti az adatok, átalakíthatja a JSON-adatokat táblázatos formátumba, majd közzétehet egy jelentést a Power BI-ban.

> [!NOTE]
> A Azure Cosmos DB Power BI-összekötő csatlakozik a Power BI Desktophoz. A Power BI Desktopban létrehozott jelentések közzétehető a PowerBI.com-ben. Azure Cosmos DB adatok közvetlen kibontása nem végezhető el a PowerBI.com. 

> [!NOTE]
> Az Power BI-összekötővel való csatlakozás Azure Cosmos DB jelenleg csak Azure Cosmos DB SQL API-és Gremlin API-fiókok esetében támogatott.

## <a name="prerequisites"></a>Előfeltételek
Az Power BI oktatóanyag utasításait követve ellenőrizze, hogy rendelkezik-e hozzáféréssel a következő erőforrásokhoz:

* [Töltse le a Power bi Desktop legújabb verzióját](https://powerbi.microsoft.com/desktop).

* Töltse le a [minta vulkán adatait](https://github.com/Azure-Samples/azure-cosmos-db-sample-data/blob/main/SampleData/VolcanoData.json) a githubról.

* [Hozzon létre egy Azure Cosmos Database-fiókot](create-cosmosdb-resources-portal.md#create-an-azure-cosmos-db-account) , és importálja a vulkáni adataikat a [Azure Cosmos db adatáttelepítési eszköz](import-data.md)használatával. Az adatimportálás során vegye figyelembe az alábbi beállításokat a forrás és a célhelyek számára az adatáttelepítési eszközben:

   * **Forrás paramétereinek** 

       * **Importálás forrása:** JSON-fájl (ok)

   * **Cél paraméterei** 

      * **Kapcsolatok karakterlánca:**`AccountEndpoint=<Your_account_endpoint>;AccountKey=<Your_primary_or_secondary_key>;Database= <Your_database_name>` 

      * **Partíciós kulcs:** /Country 

      * **Gyűjtési átviteli sebesség:** 1000 

A jelentések a PowerBI.com-ben való megosztásához fiókkal kell rendelkeznie a PowerBI.com-ben.  További információ a Power BI és a Power BI Proról: [https://powerbi.microsoft.com/pricing](https://powerbi.microsoft.com/pricing) .

## <a name="lets-get-started"></a>Első lépések
Ebben az oktatóanyagban képzeljük el, hogy Ön a geológus a világ különböző pontjain tanul. A vulkáni adatfájlok tárolása egy Azure Cosmos DB fiókban történik, a JSON-dokumentum formátuma pedig a következő:

```json
{
    "Volcano Name": "Rainier",
        "Country": "United States",
        "Region": "US-Washington",
        "Location": {
          "type": "Point",
          "coordinates": [
            -121.758,
            46.87
          ]
        },
        "Elevation": 4392,
        "Type": "Stratovolcano",
        "Status": "Dendrochronology",
        "Last Known Eruption": "Last known eruption from 1800-1899, inclusive"
}
```

A vulkáni adatok beolvasása a Azure Cosmos DB-fiókból, és az adatok megjelenítése interaktív Power BI-jelentésben történik.

1. Power BI Desktop futtatása.

2. **Lekérheti az adatait**, megtekintheti a **legutóbbi forrásokat**, vagy **megnyithatja az egyéb jelentéseket** közvetlenül az üdvözlőképernyőn. A képernyő bezárásához kattintson a jobb felső sarokban található "X" elemre. Megjelenik a Power BI Desktop **jelentés** nézete.
   
   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbireportview.png" alt-text="Power BI Desktop jelentés nézet – Power BI-összekötő":::

3. Válassza ki a **Kezdőlap** menüszalagot, majd kattintson az **adatlekérdezés** elemre.  Az **adatlekérdezés** ablaknak meg kell jelennie.

4. Kattintson az **Azure**-ra, válassza a **Azure Cosmos db (bétaverzió)** lehetőséget, majd kattintson a **kapcsolat** gombra. 

   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbigetdata.png" alt-text="Az adatPower BI-összekötő Power BI Desktop beolvasása":::

5. Az **előnézeti összekötő** lapon kattintson a **Continue (folytatás**) gombra. Megjelenik a **Azure Cosmos db** ablak.

6. Adja meg azt a Azure Cosmos DB-fiók végpontjának URL-címét, amelyet az alább látható módon szeretne beolvasni az adatokból, majd kattintson **az OK** gombra. Ha saját fiókját szeretné használni, az URL-címet a Azure Portal **kulcsok** paneljének URI mezőjéből kérheti le. Opcionálisan megadhatja az adatbázis nevét, a gyűjtemény nevét, vagy a navigátor használatával kiválaszthatja az adatbázist és a gyűjteményt annak meghatározásához, hogy az adatok honnan származnak.
   
7. Ha első alkalommal csatlakozik ehhez a végponthoz, a rendszer a fiók kulcsát kéri. Saját fiókja esetén a Azure Portal **írásvédett kulcsok** paneljének **elsődleges kulcs** mezőjéből kérje le a kulcsot. Adja meg a megfelelő kulcsot, majd kattintson a **kapcsolat** elemre.
   
   Javasoljuk, hogy a jelentések készítésekor a csak olvasási kulcsot használja. Ez megakadályozza az elsődleges kulcs szükségtelen expozícióját a lehetséges biztonsági kockázatokkal szemben. A csak olvasható kulcs a Azure Portal **kulcsok** paneljéről érhető el. 
    
8. Ha a fiók sikeresen csatlakoztatva van, megjelenik a **navigátor** panel. A **navigátor** a fiókban lévő adatbázisok listáját jeleníti meg.

9. Kattintson és bontsa ki azt az adatbázist, ahol a jelentés adatai származnak, válassza ki a **volcanodb** (az adatbázis neve eltérő lehet).   

10. Most válasszon ki egy gyűjteményt, amely tartalmazza a lekérdezni kívánt adatait, válassza ki a **volcano1** (a gyűjtemény neve eltérő lehet).
    
    A betekintő ablaktábla a **rekordok** listáját jeleníti meg.  A dokumentumok a Power BIban szereplő **bejegyzéstípusként** jelennek meg. Hasonlóképpen, a dokumentumban egy beágyazott JSON-blokk is **rekord**.
    
    :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbinavigator.png" alt-text="Azure Cosmos DB Power BI Connector – navigátor ablak Power BI oktatóanyaga":::

12. A **Szerkesztés** gombra kattintva elindíthatja a lekérdezés-szerkesztőt egy új ablakban az adatátalakításhoz.

## <a name="flattening-and-transforming-json-documents"></a>JSON-dokumentumok összeolvasztása és átalakítása
1. Váltson a Power BI lekérdezés-szerkesztő ablakára, ahol a középső ablaktábla **dokumentum** oszlopa látható.

   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbiqueryeditor.png" alt-text="Power BI Desktop Lekérdezésszerkesztő":::

1. Kattintson a **dokumentum** -oszlop fejlécének jobb oldalán található Expander elemre.  Ekkor megjelenik a helyi menü a mezők listájával.  Válassza ki a jelentéshez szükséges mezőket, például a vulkán nevét, az országot, a régiót, a helyet, a jogosultságszint-emelést, a típust, az állapotot és az utolsó ismeret kitörését. Törölje az **eredeti oszlopnév használata előtagként** négyzet jelölését, majd kattintson az **OK** gombra.
   
   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbiqueryeditorexpander.png" alt-text="Power BI oktatóanyag a Azure Cosmos DB Power BI-összekötőhöz – dokumentumok kibontása":::

1. A középső ablaktábla az eredmény előnézetét jeleníti meg a kijelölt mezőkkel.
   
   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbiresultflatten.png" alt-text="Power BI oktatóanyag a Azure Cosmos DB Power BI-összekötőhöz – eredmények összeolvasztása":::

1. A példánkban a Location tulajdonság egy GeoJSON-blokk a dokumentumban.  Amint láthatja, a hely a Power BI Desktopban szereplő **bejegyzéstípusként** jelenik meg.  

1. Kattintson a Document. location oszlopfejléc jobb oldalán található Expander elemre.  Megjelenik a típus és a koordináták mezőkkel rendelkező helyi menü.  Jelölje be a koordináták mezőt, ügyeljen arra, hogy az **eredeti oszlopnév használata előtagként** ne legyen kiválasztva, majd kattintson **az OK** gombra.
   
   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbilocationrecord.png" alt-text="Power BI oktatóanyag a Azure Cosmos DB Power BI Connector-hely rekordhoz":::

1. A középső ablaktábla ekkor egy **lista** típusú koordináták oszlopot jelenít meg.  Ahogy az oktatóanyag elején is látható, az ebben az oktatóanyagban található GeoJSON-adatok a koordináták tömbben rögzített szélességi és hosszúsági értékkel rendelkező pont típusúak.
   
   A koordináták [0] elem a hosszúságot jelöli, míg a koordináták [1] a szélességet jelöli.

   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbiresultflattenlist.png" alt-text="Power BI oktatóanyag a Azure Cosmos DB Power BI összekötőhöz – koordináták listája":::

1. A koordináták tömb összeolvasztásához hozzon létre egy LatLong nevű **Egyéni oszlopot** .  Válassza az **oszlop hozzáadása** menüszalagot, és kattintson az **Egyéni oszlop** elemre.  Megjelenik az **Egyéni oszlop** ablak.

1. Adja meg az új oszlop nevét, például LatLong.

1. Ezután adja meg az új oszlop egyéni képletét.  Példánkban a szélességi és a hosszúsági értékeket egy vesszővel elválasztva fogjuk összefűzve, az alábbi képlettel: `Text.From([coordinates]{1})&","&Text.From([coordinates]{0})` . Kattintson az **OK** gombra.
   
   Az adatelemzési kifejezésekkel (DAX) kapcsolatos további információkért, beleértve a DAX-függvényeket is, tekintse meg a [DAX alapjai Power bi Desktop](/power-bi/desktop-quickstart-learn-dax-basics).
   
   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbicustomlatlong.png" alt-text="Power BI oktatóanyag a Azure Cosmos DB Power BI-összekötőhöz – egyéni oszlop hozzáadása":::

1. A középső ablaktábla mostantól megjeleníti az új LatLong oszlopokat, amelyek az értékekkel vannak feltöltve.
    
    :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbicolumnlatlong.png" alt-text="Power BI oktatóanyag a Azure Cosmos DB Power BI connectorhoz – egyéni LatLong oszlop":::
    
    Ha hibaüzenetet kap az új oszlopban, győződjön meg arról, hogy a lekérdezési beállítások alatt alkalmazott lépések megfelelnek a következő ábrának:
    
    :::image type="content" source="./media/powerbi-visualize/power-bi-applied-steps.png" alt-text="Az alkalmazott lépések forrása, navigáció, kibontott dokumentum, kibontott dokumentum. hely, egyéni Hozzáadás":::
    
    Ha a lépések eltérnek, törölje a további lépéseket, és próbálkozzon újra az egyéni oszlop hozzáadásával. 

1. Kattintson a **Bezárás gombra, és alkalmazza** az adatmodell mentésére.

   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbicloseapply.png" alt-text="Power BI oktatóanyag a Azure Cosmos DB Power BI-összekötőhöz – Bezárás & alkalmazás":::

<a id="build-the-reports"></a>
## <a name="build-the-reports"></a>A jelentések összeállítása

Power BI Desktop jelentés nézetből megkezdheti az adatmegjelenítéshez szükséges jelentések létrehozását.  Jelentéseket úgy hozhat létre, hogy mezőket húz és eldobja a **jelentés** vásznon.

:::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbireportview2.png" alt-text="Power BI Desktop jelentés nézete – húzza a szükséges mezőket":::

A jelentés nézetben a következőket kell megkeresni:

1. A **mezők** panelen megtekintheti az adatmodellek listáját a jelentésekhez használható mezőkkel.
1. A **vizualizációk** panel. Egy jelentés tartalmazhat egyetlen vagy több vizualizációt is.  Válassza ki az igényeinek megfelelő vizuális típusokat a **vizualizációk** ablaktáblán.
1. A **jelentés** vászon, amely a jelentés vizualizációinak összeállítására szolgál.
1. A **jelentés** lapja. Power BI Desktopban több jelentés lapja is felvehető.

Az alábbi ábrán egy egyszerű interaktív térképes nézet létrehozásának alapvető lépései láthatók.

1. A példánkban egy Térkép nézetet fogunk létrehozni, amely az egyes vulkánok helyét mutatja.  A **vizualizációk** ablaktáblán kattintson a Térkép vizuális típusára, ahogy az a fenti képernyőképen ki van emelve.  A térkép típusú vizualizációt a **jelentés** vásznon festve kell megtekinteni.  A **vizualizáció** ablaktáblán a Térkép vizuális típusához kapcsolódó tulajdonságokat is meg kell adni.
1. Most húzza át a LatLong mezőt a **mezők** ablaktábláról a **vizualizációk** ablaktábla **Location (hely** ) tulajdonságára.
1. Ezután húzza a vulkán neve mezőt a **Jelmagyarázat** tulajdonságra.  
1. Ezután húzza a Jogosultságszint-emelés mezőt a **Size (méret** ) tulajdonságra.  
1. Ekkor megjelenik a Térkép vizualizációja, amely az egyes vulkánok helyét jelöli, és a buborék megemelésének mértékét jelzi.
1. Most létrehozott egy alapszintű jelentést.  A jelentést további vizualizációk hozzáadásával is testreszabhatja.  Ebben az esetben egy vulkán típusú szeletelőt adunk hozzá a jelentés interaktív létrehozásához.  
   
1. A Fájl menüben kattintson a **Mentés** elemre, és mentse a fájlt PowerBITutorial. pbix néven.

## <a name="publish-and-share-your-report"></a>A jelentés közzététele és megosztása
A jelentés megosztásához fiókkal kell rendelkeznie a PowerBI.com-ben.

1. A Power BI Desktop kattintson a **Kezdőlap** menüszalagra.
1. Kattintson a **Közzététel** gombra.  A rendszer felszólítja, hogy adja meg a PowerBI.com-fiókhoz tartozó felhasználónevet és jelszót.
1. A hitelesítő adatok hitelesítése után a jelentést a rendszer közzéteszi a kiválasztott célhelyen.
1. Kattintson a **Power bi PowerBITutorial. pbix** elemre a jelentés megtekintéséhez és megosztásához a PowerBI.com-on.
   
   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_open_in_powerbi.png" alt-text="Power BI sikeres közzététel! Oktatóanyag megnyitása Power BI":::

## <a name="create-a-dashboard-in-powerbicom"></a>Hozzon létre egy irányítópultot a PowerBI.com webhelyen
Most, hogy már van egy jelentés, lehetővé teszi a megosztást a PowerBI.com

Amikor a jelentést Power BI Desktopról a PowerBI.com-be teszi közzé, egy **jelentést** és egy **adatkészletet** hoz létre a PowerBI.com-bérlőben. Ha például közzétett egy **PowerBITutorial** nevű jelentést a PowerBI.com-ben, akkor a PowerBITutorial a PowerBI.com-on található **jelentések** és **adatkészletek** szakaszban is megjelenik.

   :::image type="content" source="./media/powerbi-visualize/powerbi-reports-datasets.png" alt-text="Képernyőkép az új jelentésről és adatkészletről a PowerBI.com-ben":::

Megosztható irányítópult létrehozásához kattintson az **élő oldal rögzítése** gombra a PowerBI.com-jelentésben.

   :::image type="content" source="./media/powerbi-visualize/power-bi-pin-live-tile.png" alt-text="Képernyőkép a jelentés rögzítéséről a PowerBI.com":::

Ezután kövesse a [csempe rögzítése a jelentésből](https://powerbi.microsoft.com/documentation/powerbi-service-pin-a-tile-to-a-dashboard-from-a-report/#pin-a-tile-from-a-report) című témakör utasításait egy új irányítópult létrehozásához. 

Az irányítópult létrehozása előtt ad hoc módosításokat is készíthet a jelentéshez. Javasoljuk azonban, hogy az Power BI Desktop használatával hajtsa végre a módosításokat, és tegye közzé újból a jelentést a PowerBI.com.

<!-- ## Refresh data in PowerBI.com
There are two ways to refresh data, ad hoc and scheduled.

For an ad hoc refresh, simply click on the eclipses (…) by the **Dataset**, e.g. PowerBITutorial. You should see a list of actions including **Refresh Now**. Click **Refresh Now** to refresh the data.

:::image type="content" source="./media/powerbi-visualize/power-bi-refresh-now.png" alt-text="Screenshot of Refresh Now in PowerBI.com":::

For a scheduled refresh, do the following.

1. Click **Schedule Refresh** in the action list. 

    :::image type="content" source="./media/powerbi-visualize/power-bi-schedule-refresh.png" alt-text="Screenshot of the Schedule Refresh in PowerBI.com":::
2. In the **Settings** page, expand **Data source credentials**. 
3. Click on **Edit credentials**. 
   
    The Configure popup appears. 
4. Enter the key to connect to the Azure Cosmos DB account for that data set, then click **Sign in**. 
5. Expand **Schedule Refresh** and set up the schedule you want to refresh the dataset. 
6. Click **Apply** and you are done setting up the scheduled refresh.
-->
## <a name="next-steps"></a>Következő lépések
* További információ a Power BIről: a [Power bi első lépései](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/).
* Ha többet szeretne megtudni a Azure Cosmos DBről, tekintse meg a [Azure Cosmos db dokumentáció kezdőlapját](https://azure.microsoft.com/documentation/services/cosmos-db/).
