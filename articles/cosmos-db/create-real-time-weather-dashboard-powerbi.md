---
title: Valós idejű irányítópult létrehozása Azure Cosmos DB, Azure Analysis Services és Power BI használatával
description: Megtudhatja, hogyan hozhat létre élő időjárási irányítópultot Power BI a Azure Cosmos DB és a Azure Analysis Services használatával.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 09/04/2019
ms.reviewer: sngun
ms.openlocfilehash: 73251fcbe9f149979d3fd62d14bbca86d77027f2
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2021
ms.locfileid: "105640135"
---
# <a name="create-a-real-time-dashboard-using-azure-cosmos-db-and-power-bi"></a>Valós idejű irányítópult létrehozása Azure Cosmos DB és Power BI használatával
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Ez a cikk a Power BI élő időjárási irányítópult létrehozásához szükséges lépéseket ismerteti Azure Cosmos DB és Azure Analysis Services használatával. A Power BI irányítópulton diagramokat jeleníthet meg, amelyek valós idejű információkat jelenítenek meg a hőmérsékletről és a csapadékról egy adott régióban.

## <a name="reporting-scenarios"></a>Jelentéskészítési forgatókönyvek

A jelentéskészítési irányítópultok több módon is beállíthatók a Azure Cosmos DBban tárolt adatszolgáltatásokhoz. Az elavult követelményektől és az adatmérettől függően a következő táblázat ismerteti az egyes forgatókönyvek jelentéskészítési beállításait:


|Eset |Beállítás |
|---------|---------|
|1. ad-hoc jelentések létrehozása (frissítés nélkül)    |  [Power BI Azure Cosmos DB-összekötő importálási móddal](powerbi-visualize.md)       |
|2. ad hoc jelentések létrehozása rendszeres frissítéssel   |  [Power BI Azure Cosmos DB-összekötő importálási móddal (ütemezett rendszeres frissítés)](powerbi-visualize.md)       |
|3. nagyméretű adatkészletek jelentése (< 10 GB)     |  Power BI Azure Cosmos DB-összekötő növekményes frissítéssel       |
|4. valós idejű jelentéskészítés nagyméretű adatkészleteken    |  Power BI Azure Analysis Services-összekötő közvetlen lekérdezéssel + Azure Analysis Services (Azure Cosmos DB összekötő)       |
|5. az élő adatokat tartalmazó jelentéskészítés összesítésekkel     |  [Power BI Spark-összekötő közvetlen lekérdezéssel + Azure Databricks + Cosmos DB Spark-összekötővel.](https://github.com/Azure/azure-cosmosdb-spark/wiki/Connecting-Cosmos-DB-with-PowerBI-using-spark-and-databricks-premium)       |
|6. a nagy adathalmazok összesítéseit tartalmazó élő adatokat jelentéskészítő   |  Power BI Azure Analysis Services-összekötő közvetlen lekérdezéssel + Azure Analysis Services + Azure Databricks + Cosmos DB Spark-összekötővel.       |

Az 1. és a 2. forgatókönyv egyszerűen beállítható a Azure Cosmos DB Power BI-összekötő használatával. Ez a cikk a 3. és 4. forgatókönyvek beállításait ismerteti.

### <a name="power-bi-with-incremental-refresh"></a>Power BI növekményes frissítéssel

Power BI a Növekményes frissítés konfigurálható üzemmódot tartalmaz. Ez a mód nem teszi szükségessé Azure Analysis Services partíciók létrehozását és felügyeletét. A Növekményes frissítés beállítható úgy, hogy csak a legújabb frissítéseket szűrje a nagyméretű adatkészletekben. Ez a mód azonban csak olyan Power BI Premium szolgáltatással működik, amelyhez 10 GB méretű adatkészlet-korlátozás tartozik.

### <a name="power-bi-azure-analysis-connector--azure-analysis-services"></a>Power BI Azure Analysis Connector + Azure Analysis Services

A Azure Analysis Services teljes körűen felügyelt platformot biztosít a felhőben nagyvállalati szintű adatmodelleket üzemeltető szolgáltatásként. A nagyméretű adatkészletek betölthetők Azure Cosmos DBból a Azure Analysis Servicesba. Ha nem szeretné, hogy az adathalmazok teljes egészében le lehessen kérdezni, az adatkészletek felosztható Azure Analysis Services partícióra, amelyek különböző gyakorisággal egymástól függetlenül frissíthetők.

## <a name="power-bi-incremental-refresh"></a>Növekményes frissítés Power BI

### <a name="ingest-weather-data-into-azure-cosmos-db"></a>Időjárási adatmennyiség beolvasása a Azure Cosmos DBba

Betöltési folyamat beállítása az [időjárási adatmennyiség](https://catalog.data.gov/dataset?groups=climate5434&#topic=climate_navigation) Azure Cosmos DBba való betöltéséhez. Beállíthat egy [Azure Data Factory (ADF)](../data-factory/connector-azure-cosmos-db.md) feladatot a legújabb időjárási adatoknak a Azure Cosmos db a http-forrás és a Cosmos db fogadó használatával történő rendszeres betöltéséhez.


### <a name="connect-power-bi-to-azure-cosmos-db"></a>Power BI összekötése a Azure Cosmos DB

1. **Csatlakoztassa az Azure Cosmos-fiókot Power bi** – nyissa meg a Power bi Desktop, és a Azure Cosmos db-összekötő használatával válassza ki a megfelelő adatbázist és tárolót.

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/cosmosdb-powerbi-connector.png" alt-text="Azure Cosmos DB Power BI-összekötő":::

1. **Növekményes frissítés konfigurálása** – az adathalmaz növekményes frissítésének konfigurálásához kövesse a [növekményes frissítés a Power BIval](/power-bi/service-premium-incremental-refresh) című cikkben ismertetett lépéseket. Adja hozzá a **RangeStart** és a **RangeEnd** paramétert az alábbi képernyőképen látható módon:

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/configure-range-parameters.png" alt-text="Tartomány paramétereinek konfigurálása":::

   Mivel az adatkészlet egy szöveges formátumú oszlop, a **RangeStart** és a **RangeEnd** paramétereket át kell alakítani a következő szűrő használatára. A **speciális szerkesztő** ablaktáblán módosítsa a lekérdezést, és adja hozzá a következő szöveget a sorok szűréséhez a RangeStart és a RangeEnd paraméterek alapján:

   ```
   #"Filtered Rows" = Table.SelectRows(#"Expanded Document", each [Document.date] > DateTime.ToText(RangeStart,"yyyy-MM-dd") and [Document.date] < DateTime.ToText(RangeEnd,"yyyy-MM-dd"))
   ```
   
   Attól függően, hogy melyik oszlop és adattípus szerepel a forrás adatkészletben, akkor a RangeStart és a RangeEnd mezőket ennek megfelelően módosíthatja.

   
   |Tulajdonság  |Adattípus  |Szűrő  |
   |---------|---------|---------|
   |_ts        |   Numerikus      |  [_ts] > időtartam. összesmásodperc (RangeStart-#datetime (1970, 1, 1, 0, 0, 0)) és [_ts] < időtartama. összesmásodperc (RangeEnd-#datetime (1970, 1, 1, 0, 0, 0))       |
   |Dátum (például:-2019-08-19)     |   Sztring      | [Document. Date] > DateTime. ToText (RangeStart, "éééé-hh-nn") és [Document. Date] < DateTime. ToText (RangeEnd, "éééé-hh-nn")        |
   |Dátum (például:-2019-08-11 12:00:00)   |  Sztring       |  [Document. Date] > DateTime. ToText (RangeStart, "éééé-hh-nn óó: PP: SS") és [Document. Date] < DateTime. ToText (RangeEnd, "éééé-hh-nn óó: PP: SS")       |


1. **A frissítési házirend meghatározása** – a frissítési házirend megadása a táblázat **helyi** menüjének **Növekményes frissítés** lapján. Állítsa be a frissítési szabályzatot, hogy **minden nap** frissítsen, és tárolja az utolsó hónap adatát.

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/define-refresh-policy.png" alt-text="Frissítési szabályzat definiálása":::

   Hagyja figyelmen kívül azt a figyelmeztetést, amely szerint *az M-lekérdezést nem lehet megerősíteni*. Az Azure Cosmos DB-összekötő kibontja a szűrési lekérdezéseket.

1. **Töltse be az adatgyűjtést, és hozza létre a jelentéseket** – a korábban betöltött adatai alapján hozza létre a diagramokat a hőmérséklet és a csapadék jelentéséhez.

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/load-data-generate-report.png" alt-text="Adattöltés és jelentés készítése":::

1. **Tegye közzé a jelentést Power bi Premium** – mivel a Növekményes frissítés csak prémium szintű funkció, a közzétételi párbeszédpanel csak a munkaterületek kiválasztását teszi lehetővé a prémium szintű kapacitásban. Az első frissítés tovább tarthat az előzményadatok importálása miatt. A későbbi adatfrissítések sokkal gyorsabban működnek, mivel a növekményes frissítést használják.


## <a name="power-bi-azure-analysis-connector--azure-analysis-services"></a>Power BI Azure Analysis Connector + Azure Analysis Services 

### <a name="ingest-weather-data-into-azure-cosmos-db"></a>Időjárási adatmennyiség beolvasása a Azure Cosmos DBba 

Betöltési folyamat beállítása az [időjárási adatmennyiség](https://catalog.data.gov/dataset?groups=climate5434&#topic=climate_navigation) Azure Cosmos DBba való betöltéséhez. Beállíthat egy Azure Data Factory (ADF) feladatot a legújabb időjárási adatoknak a Azure Cosmos DB a HTTP-forrás és a Cosmos DB fogadó használatával történő rendszeres betöltéséhez.

### <a name="connect-azure-analysis-services-to-azure-cosmos-account"></a>Azure Analysis Services összekötése az Azure Cosmos-fiókkal

1. **Új Azure Analysis Services-fürt létrehozása**  -  [Hozzon létre egy Azure Analysis Services-példányt](../analysis-services/analysis-services-create-server.md) ugyanabban a régióban, mint az Azure Cosmos-fiókot és a Databricks-fürtöt.

1. **Új Analysis Services táblázatos projekt létrehozása a Visual Studióban**  -   [Telepítse a SQL Server Data Tools (SSDT)](/sql/ssdt/download-sql-server-data-tools-ssdt) , és hozzon létre egy Analysis Services táblázatos projektet a Visual Studióban.

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/create-analysis-services-project.png" alt-text="Azure Analysis Services projekt létrehozása":::

   Válassza ki az **integrált munkaterület** -példányt, és a kompatibilitási szint beállítása a **következőre SQL Server 2017/Azure Analysis Services (1400)**

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/tabular-model-designer.png" alt-text="Azure Analysis Services táblázatos modell tervezője":::

1. **Adja hozzá a Azure Cosmos DB adatforrást** – **navigáljon az** adatforrások új adatforrásához, >    >   és adja hozzá a Azure Cosmos DB adatforrást az alábbi képernyőképen látható módon:

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/add-data-source.png" alt-text="Cosmos DB adatforrás hozzáadása":::

   A Azure Cosmos DBhoz való kapcsolódáshoz adja meg a **fiók URI-ját**, az **adatbázis nevét** és a **tároló nevét**. Most már láthatja az Azure Cosmos-tárolóból származó adatok importálását a Power BIba.

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/preview-cosmosdb-data.png" alt-text="Előnézet Azure Cosmos DB":::

1. Hozza **létre a Analysis Services modellt** – nyissa meg a lekérdezés-szerkesztőt, hajtsa végre a szükséges műveleteket a betöltött adatkészlet optimalizálása érdekében:

   * Csak az időjárással kapcsolatos oszlopok (hőmérséklet és csapadék) kinyerése

   * A hónap adatainak kinyerése a táblából. Ezek az információk hasznosak a partíciók létrehozásához a következő szakaszban leírtak szerint.

   * A hőmérséklet oszlopok konvertálása számra

   Az eredményül kapott M kifejezés a következő:

   ```
    let
        Source=#"DocumentDB/https://[ACCOUNTNAME].documents.azure.com:443/",
        #"Expanded Document" = Table.ExpandRecordColumn(Source, "Document", {"id", "_rid", "_self", "_etag", "fogground", "snowfall", "dust", "snowdepth", "mist", "drizzle", "hail", "fastest2minwindspeed", "thunder", "glaze", "snow", "ice", "fog", "temperaturemin", "fastest5secwindspeed", "freezingfog", "temperaturemax", "blowingsnow", "freezingrain", "rain", "highwind", "date", "precipitation", "fogheavy", "smokehaze", "avgwindspeed", "fastest2minwinddir", "fastest5secwinddir", "_attachments", "_ts"}, {"Document.id", "Document._rid", "Document._self", "Document._etag", "Document.fogground", "Document.snowfall", "Document.dust", "Document.snowdepth", "Document.mist", "Document.drizzle", "Document.hail", "Document.fastest2minwindspeed", "Document.thunder", "Document.glaze", "Document.snow", "Document.ice", "Document.fog", "Document.temperaturemin", "Document.fastest5secwindspeed", "Document.freezingfog", "Document.temperaturemax", "Document.blowingsnow", "Document.freezingrain", "Document.rain", "Document.highwind", "Document.date", "Document.precipitation", "Document.fogheavy", "Document.smokehaze", "Document.avgwindspeed", "Document.fastest2minwinddir", "Document.fastest5secwinddir", "Document._attachments", "Document._ts"}),
        #"Select Columns" = Table.SelectColumns(#"Expanded Document",{"Document.temperaturemin", "Document.temperaturemax", "Document.rain", "Document.date"}),
        #"Duplicated Column" = Table.DuplicateColumn(#"Select Columns", "Document.date", "Document.month"),
        #"Extracted First Characters" = Table.TransformColumns(#"Duplicated Column", {{"Document.month", each Text.Start(_, 7), type text}}),
        #"Sorted Rows" = Table.Sort(#"Extracted First Characters",{{"Document.date", Order.Ascending}}),
        #"Changed Type" = Table.TransformColumnTypes(#"Sorted Rows",{{"Document.temperaturemin", type number}, {"Document.temperaturemax", type number}}),
        #"Filtered Rows" = Table.SelectRows(#"Changed Type", each [Document.month] = "2019-07")
    in
        #"Filtered Rows"
   ```

   Emellett módosítsa a hőmérséklet-oszlopok adattípusát decimális értékre, hogy megbizonyosodjon róla, hogy ezek az értékek a Power BIban is megtekinthetők.

1. **Azure Analysis Partitions** – hozzon létre partíciókat a Azure Analysis Servicesban, hogy az adatkészletet logikai partíciók számára Ossza szét, amelyek egymástól függetlenül és különböző gyakorisággal frissíthetők. Ebben a példában két partíciót hoz létre, amelyek az adatkészletet a legújabb havi adatra és minden másra osztják.

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/create-analysis-services-partitions.png" alt-text="Analysis Services-partíciók létrehozása":::

   Hozza létre a következő két partíciót a Azure Analysis Servicesban:

   * **Legutóbbi hónap** - `#"Filtered Rows" = Table.SelectRows(#"Sorted Rows", each [Document.month] = "2019-07")`
   * **Korábbi** -  `#"Filtered Rows" = Table.SelectRows(#"Sorted Rows", each [Document.month] <> "2019-07")`

1. **Telepítse a modellt az Azure Analysis Serverba** – kattintson a jobb gombbal a Azure Analysis Services projektre, és válassza a **telepítés** lehetőséget. Adja hozzá a kiszolgáló nevét a **központi telepítési kiszolgáló tulajdonságai** ablaktáblán.

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/analysis-services-deploy-model.png" alt-text="Azure Analysis Services modell üzembe helyezése":::

1. A **partíciók frissítésének és egyesítésének konfigurálása** – Azure Analysis Services lehetővé teszi a partíciók független feldolgozását. Mivel azt szeretnénk, hogy a **legújabb havi** partíció folyamatosan frissüljön a legfrissebb adataival, állítsa a frissítési időközt 5 percre. Az adatai a [REST API](../analysis-services/analysis-services-async-refresh.md), az [Azure Automation](../analysis-services/analysis-services-refresh-azure-automation.md)vagy egy [logikai alkalmazás](../analysis-services/analysis-services-refresh-logic-app.md)használatával frissíthetők. A korábbi partíciókban lévő Adatfrissítés nem szükséges. Emellett meg kell írnia egy kódot, hogy összevonja a legújabb havi partíciót a korábbi partícióra, és hozzon létre egy új, legújabb havi partíciót.

## <a name="connect-power-bi-to-analysis-services"></a>Power BI összekötése a Analysis Services

1. **Kapcsolódjon az Azure Analysis Server a Azure Analysis Services adatbázis-összekötő használatával** – válassza az **élő módot** , és kapcsolódjon a Azure Analysis Services példányhoz az alábbi képernyőképen látható módon:

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/analysis-services-get-data.png" alt-text="Adatlekérdezés az Azure Analysis Servicesből":::

1. **Töltse be az adataikat, és készítsen jelentéseket** – a korábban betöltött adataival, hozzon létre diagramokat a hőmérséklet és a csapadék jelentéséhez. Mivel élő kapcsolatokat hoz létre, a lekérdezéseket az előző lépésben üzembe helyezett Azure Analysis Services modellben lévő adattípuson kell végrehajtani. A hőmérséklet-diagramok az új adatAzure Cosmos DBba való betöltésük után öt percen belül frissülnek.

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/load-data-generate-report.png" alt-text="Az adatterhelés és a jelentések készítése":::

## <a name="next-steps"></a>Következő lépések

* További információ a Power BIről: a [Power bi első lépései](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/).

* [Az Qlik-értelem összekapcsolásával Azure Cosmos DBhatja és megjelenítheti az adatait](visualize-qlik-sense.md)
