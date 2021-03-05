---
title: Figyelő Azure Cosmos DB | Microsoft Docs
description: Megtudhatja, hogyan figyelheti Azure Cosmos DB teljesítményét és rendelkezésre állását.
author: SnehaGunda
services: cosmos-db
ms.service: cosmos-db
ms.topic: how-to
ms.date: 12/01/2020
ms.author: sngun
ms.custom: subject-monitoring
ms.openlocfilehash: f18d1850cb6ccf28ff70f826e3d4bfe74ae05c40
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102178728"
---
# <a name="monitor-azure-cosmos-db"></a>Az Azure Cosmos DB monitorozása
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Ha kritikus fontosságú alkalmazásokat és üzleti folyamatokat kíván használni az Azure-erőforrásokon, figyelnie kell ezeket az erőforrásokat a rendelkezésre állással, a teljesítménnyel és a művelettel kapcsolatban. Ez a cikk az Azure Cosmos-adatbázisok által létrehozott figyelési információkat ismerteti, valamint azt, hogy miként használhatók a Azure Monitor funkciói az adatelemzéshez és riasztáshoz.

Az adatok figyelése ügyféloldali és kiszolgálóoldali metrikákkal végezhető el. Kiszolgálóoldali mérőszámok használatakor a Azure Cosmos DBban tárolt adatokat a következő beállításokkal figyelheti:

* **Figyelő Azure Cosmos db portálról:** Az Azure Cosmos-fiók **metrikák** lapján elérhető metrikák figyelésére is lehetősége van. Az ezen a lapon található mérőszámok közé tartozik az átviteli sebesség, a tárterület, a rendelkezésre állás, a késés, a konzisztencia és a rendszerszintű metrika. Alapértelmezés szerint ezek a metrikák 7 napos megőrzési időtartammal rendelkeznek. További információkért tekintse meg a jelen cikk Azure Cosmos DB című részében [gyűjtött figyelési adatokat](#monitoring-from-azure-cosmos-db) .

* **Monitorozás metrikákkal az Azure monitorban:** Nyomon követheti az Azure Cosmos-fiók metrikáit, és irányítópultokat hozhat létre a Azure Monitorból. Az Azure Monitor alapértelmezés szerint gyűjti a Azure Cosmos DB metrikákat, és nem konfigurál explicit módon semmit. Ezek a metrikák egyperces részletességgel vannak összegyűjtve, a részletesség a választott metrika alapján változhat. Alapértelmezés szerint ezek a metrikák 30 napos megőrzési időtartammal rendelkeznek. Az előző beállításokban elérhető metrikák többsége ezekben a metrikákban is elérhető. A metrikák, például a tároló neve nem megkülönbözteti a kis-és nagybetűket. Ezért a kis-és nagybetűket megkülönböztető összehasonlítást kell használnia, amikor karakterlánc-összehasonlítást végez ezen dimenzió értékeken. További információért lásd a jelen cikk [metrikus adatok elemzése](#analyze-metric-data) című szakaszát.

* **Figyelő a Azure monitor diagnosztikai naplóival:** Nyomon követheti az Azure Cosmos-fiók naplóit, és irányítópultokat hozhat létre a Azure Monitorból. A telemetria, például az eseményeket és a nyomkövetéseket, amelyek egy második részletességgel történnek, naplóként tárolódnak. Ha például egy tároló átviteli sebessége megváltozik, a Cosmos-fiók tulajdonságai módosulnak, ezek az események a naplókon belül lesznek rögzítve. Ezeket a naplókat az összegyűjtött adatok lekérdezésének futtatásával elemezheti. További információért lásd a jelen cikk [naplózási információk elemzése](#analyze-log-data) című szakaszát.

* **Programozott módon figyelheti az SDK** -kat: Az Azure Cosmos-fiókot programozott módon, a .NET, a Java, a Python, a Node.js SDK-k és a REST API fejlécei segítségével figyelheti. További információért lásd a jelen cikk [figyelés Azure Cosmos db programozott](#monitor-cosmosdb-programmatically) módon című szakaszát.

Az alábbi képen a Azure Cosmos DB fiókok figyelésére szolgáló különböző lehetőségek láthatók Azure Portalon keresztül:

:::image type="content" source="media/monitor-cosmos-db/monitoring-options-portal.png" alt-text="A Azure Portalban elérhető figyelési lehetőségek" border="false":::

Azure Cosmos DB használatakor az ügyfél oldalán összegyűjtheti a kérelem díjait, a tevékenység AZONOSÍTÓját, a kivétel/verem nyomkövetési adatait, a HTTP-állapotot, az alállapot kódját, a diagnosztikai karakterláncot az esetlegesen előforduló problémák hibakereséséhez. Ezekre az információkra akkor is szükség van, ha a Azure Cosmos DB támogatási csapatot kell elérnie. 

## <a name="monitor-overview"></a>Figyelő áttekintése

Az egyes Azure Cosmos DB-fiókokhoz tartozó Azure Portal **áttekintő** lapja magában foglalja az erőforrás-használat rövid nézetét, például az összes kérést, az adott HTTP-állapotkódot eredményező kérelmeket, valamint az óránkénti számlázást. Ezek az információk hasznosak, de csak kis mennyiségű figyelési adat érhető el ezen a panelen. Az adatok némelyikét automatikusan gyűjti a rendszer, és az erőforrás létrehozásakor az elemzéshez azonnal elérhetővé válik. További típusú adatgyűjtést is engedélyezhet néhány konfigurációval.

## <a name="what-is-azure-monitor"></a>Mi az Azure Monitor?

A Azure Cosmos DB az Azure-ban teljes körű figyelési szolgáltatásként hozza létre a figyelési adatok [Azure monitor](../azure-monitor/overview.md) , amely teljes körű funkciókat biztosít az Azure-erőforrások figyeléséhez más Felhőbeli és helyszíni erőforrások mellett.

Ha még nem ismeri az Azure-szolgáltatások figyelését, kezdje az Azure- [erőforrások monitorozása Azure monitor](../azure-monitor/essentials/monitor-azure-resource.md) az alábbi fogalmakat ismertető cikktel:

* Mi az Azure Monitor?
* A figyeléshez kapcsolódó költségek
* Az Azure-ban összegyűjtött adatok figyelése
* Adatgyűjtés konfigurálása
* Szabványos eszközök az Azure-ban a figyelési adatok elemzéséhez és riasztásához

Az alábbi részekben a cikk a Azure Cosmos DB gyűjtött adatok leírásával, valamint az adatok gyűjtésének konfigurálására és az Azure-eszközökkel történő elemzésére szolgáló példákat tartalmaz.

## <a name="azure-monitor-for-azure-cosmos-db"></a>Azure Cosmos DB Azure Monitor

A Azure Cosmos DB Azure Monitor a [Azure monitor munkafüzetek szolgáltatásán](../azure-monitor/visualize/workbooks-overview.md) alapul, és ugyanazokat a figyelési adatokat használja, amelyeket az alábbi szakaszokban ismertetett Azure Cosmos DBhoz gyűjtöttek be. A Azure Monitor segítségével megtekintheti az összes Azure Cosmos DB-erőforrás teljes teljesítményét, hibáit, kapacitását és működési állapotát egy egységes interaktív felhasználói élményben, és a Azure Monitor egyéb funkcióit kihasználhatja részletes elemzés és riasztás céljából. További információért lásd a [Azure Cosmos DB Azure monitor megismerése](../azure-monitor/insights/cosmosdb-insights-overview.md) című cikket.

> [!NOTE]
> Tárolók létrehozásakor ügyeljen arra, hogy ne hozzon létre két tárolót ugyanazzal a névvel, de a különböző burkolattal. Ennek az az oka, hogy az Azure-platform egyes részei nem megkülönböztetik a kis-és nagybetűket, így az ilyen nevű tárolók telemetria és műveleteinek összekeveredését és ütközését okozhatják.

## <a name="monitoring-data"></a><a id="monitoring-from-azure-cosmos-db"></a> Figyelési adatgyűjtés 

Azure Cosmos DB ugyanolyan típusú figyelési adatokat gyűjt, mint az [Azure-erőforrások monitorozásával](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data)kapcsolatos további Azure-erőforrások. A Azure Cosmos DB által létrehozott naplók és metrikák részletes ismertetését lásd: [Azure Cosmos db monitorozási adatok referenciája](monitor-cosmos-db-reference.md) .

Az egyes Azure Cosmos-adatbázisokhoz tartozó Azure Portal **áttekintő** lapja az adatbázis-használat rövid áttekintését tartalmazza, beleértve a kérését és az óránkénti számlázási használatot. Ez hasznos információ, de csak kis mennyiségű figyelési adat érhető el. Ezeket az adatokat a rendszer automatikusan gyűjti, és az elemzéshez azonnal elérhetővé válik, miközben a további adatgyűjtést is engedélyezheti néhány konfigurációval.

:::image type="content" source="media/monitor-cosmos-db/overview-page.png" alt-text="Áttekintő oldal":::

## <a name="collection-and-routing"></a>Gyűjtés és Útválasztás

A platform metrikáit és a tevékenység naplóját a rendszer automatikusan összegyűjti és tárolja, de a diagnosztikai beállítások segítségével más helyekre is átirányíthatja őket.  

Az erőforrás-naplók gyűjtése és tárolása addig nem történik meg, amíg létre nem hozza a diagnosztikai beállításokat, és egy vagy több helyre irányítja őket.

A diagnosztikai beállítások létrehozásához a Azure Portal és néhány diagnosztikai lekérdezési példa segítségével olvassa el a [diagnosztikai beállítás létrehozása a platform-naplók és-mérőszámok létrehozásához az Azure-ban](cosmosdb-monitor-resource-logs.md) című témakört. Diagnosztikai beállítás létrehozásakor meg kell adnia, hogy a rendszer milyen típusú naplókat gyűjtsön.

A gyűjtött mérőszámokat és naplókat a következő szakaszokban tárgyaljuk.

## <a name="analyzing-metrics"></a><a id="analyze-metric-data"></a> Mérőszámok elemzése

A Azure Cosmos DB a metrikák használatának egyéni élményét biztosítja. A metrikákat a **Azure monitor** menüből **megnyitva a metrikák** segítségével elemezheti az Azure Cosmos db metrikáit más Azure-szolgáltatásoktól származó metrikákkal. Az eszköz használatával kapcsolatos részletekért lásd: az [Azure Metrikaböngésző használatának első lépései](../azure-monitor/essentials/metrics-getting-started.md) . Azt is megteheti, hogyan figyelheti a [kiszolgálóoldali késést](monitor-server-side-latency.md), a [kérés egység használatát](monitor-request-unit-usage.md)és a [normalizált kérések egység használatát](monitor-normalized-request-units.md) a Azure Cosmos db erőforrásaihoz.

A Azure Cosmos DB összegyűjtött platform-metrikák listáját az [Azure Cosmos db adatreferenciák metrikáinak monitorozása](monitor-cosmos-db-reference.md#metrics) című cikkben tekintheti meg.

Az Azure Cosmos DB összes mérőszáma a névtérben **Cosmos dB standard mérőszámokban** szerepel. A következő dimenziókat használhatja ezekkel a metrikákkal, amikor szűrőt ad hozzá egy diagramhoz:

* CollectionName
* DatabaseName
* OperationType
* Region
* StatusCode

A hivatkozásokat a [Azure monitor által támogatott összes erőforrás-metrika](../azure-monitor/essentials/metrics-supported.md)listáját láthatja.

### <a name="view-operation-level-metrics-for-azure-cosmos-db"></a>Azure Cosmos DBhoz tartozó műveleti szint metrikáinak megtekintése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Válassza a **figyelő** lehetőséget a bal oldali navigációs sávon, és válassza a **metrikák** lehetőséget.

   :::image type="content" source="./media/monitor-cosmos-db/monitor-metrics-blade.png" alt-text="Metrikák ablaktábla Azure Monitor":::

1. A **metrikák** ablaktáblán > **válasszon ki egy erőforrást** > válassza ki a szükséges **előfizetést** és **erőforráscsoportot**. Az **erőforrástípus** mezőben válassza a **Azure Cosmos db fiókok** lehetőséget, válasszon ki egy meglévő Azure Cosmos-fiókot, majd válassza az **alkalmaz** lehetőséget.

   :::image type="content" source="./media/monitor-cosmos-db/select-cosmosdb-account.png" alt-text="Cosmos DB fiók kiválasztása a metrikák megtekintéséhez":::

1. Következő lépésként kiválaszthat egy mérőszámot az elérhető metrikák listájából. Kiválaszthatja a kérelmekhez tartozó mérőszámokat, a tárolást, a késést, a rendelkezésre állást, a Cassandra és másokat. A listán szereplő összes mérőszám részletes megismeréséhez tekintse meg a [metrikák kategória szerint](monitor-cosmos-db-reference.md) című cikket. Ebben a példában a **kérelmek egységeit** és az **átlagot** adja meg az összesítési értékként.

   Ezen részletek mellett kiválaszthatja a metrikák **időtartományát** és **időrészletességét** is. A maximális értéknél megtekintheti az elmúlt 30 nap mérőszámait.  A szűrő alkalmazása után egy diagram jelenik meg a szűrő alapján. A kiválasztott időszakra vonatkozóan percenként felhasználható kérelmek átlagos számát láthatja.  

   :::image type="content" source="./media/monitor-cosmos-db/metric-types.png" alt-text="Metrika kiválasztása a Azure Portal":::

### <a name="add-filters-to-metrics"></a>Szűrők hozzáadása metrikához

Az adott **CollectionName**, **databasename**, **OperationType**, **régió** és **statuscode** által megjelenített mérőszámokat és diagramokat is szűrheti. A metrikák szűréséhez válassza a **szűrő hozzáadása** lehetőséget, és válassza ki a szükséges tulajdonságot (például **OperationType** ), és válasszon egy értéket (például **lekérdezés**). A gráf ezután megjeleníti a kiválasztott időszakban a lekérdezési művelethez felhasznált kérelmek egységeit. A tárolt eljáráson keresztül végrehajtott műveletek nincsenek naplózva, így azok nem érhetők el az OperationType metrika alatt.

:::image type="content" source="./media/monitor-cosmos-db/add-metrics-filter.png" alt-text="Szűrő hozzáadása a metrika részletességének kiválasztásához":::

A metrikákat a **felosztás alkalmazása** lehetőség használatával csoportosíthatja. Például csoportosíthatja a kérelmek egységeit, és megtekintheti az összes művelet gráfját, amint az alábbi képen is látható:

:::image type="content" source="./media/monitor-cosmos-db/apply-metrics-splitting.png" alt-text="Alkalmazás-felosztási szűrő hozzáadása":::

## <a name="analyzing-logs"></a><a id="analyze-log-data"></a> Naplók elemzése

Azure Monitor naplókban lévő, az egyes táblákban található, egyedi tulajdonságokkal rendelkező táblázatokban tárolt adathalmazok.

Azure Monitor összes erőforrás-naplója ugyanazokkal a mezőkkel rendelkezik, amelyeket a szolgáltatás-specifikus mezők követnek. Az általános séma [Azure monitor erőforrás-naplózási sémában](../azure-monitor/essentials/resource-logs-schema.md#top-level-common-schema)szerepel. A Azure Cosmos DBhoz gyűjtött erőforrás-naplók típusai listáját az [adatAzure Cosmos DB monitorozása](monitor-cosmos-db-reference.md#resource-logs)című témakörben találhatja meg.

A [műveletnapló](../azure-monitor/essentials/activity-log.md) olyan platform-bejelentkezési Azure, amely betekintést nyújt az előfizetési szintű eseményekre. Megtekintheti egymástól függetlenül, vagy átirányíthatja Azure Monitor naplókba, ahol a Log Analytics használatával jóval összetettebb lekérdezéseket végezhet.  

A Azure Cosmos DB az alábbi táblázatokban tárolja az adattárakat.

| Táblázat | Leírás |
|:---|:---|
| AzureDiagnostics | Az erőforrás-naplók tárolására több szolgáltatás által használt közös táblázat. A Azure Cosmos DB található erőforrás-naplók azonosíthatók a következővel: `MICROSOFT.DOCUMENTDB` .   |
| AzureActivity    | A tevékenység naplójában lévő összes rekordot tároló általános tábla.

### <a name="sample-kusto-queries"></a>Példa Kusto-lekérdezésekre

> [!IMPORTANT]
> Amikor kijelöli a **naplók** lehetőséget a Azure Cosmos db menüben, log Analytics a jelenlegi Azure Cosmos db-fiókhoz beállított lekérdezési hatókörrel nyílik meg. Ez azt jelenti, hogy a naplók lekérdezése csak az adott erőforrás adatait fogja tartalmazni. Ha olyan lekérdezést szeretne futtatni, amely más fiókoktól vagy más Azure-szolgáltatásoktól származó adatokból származó adatokkal is rendelkezik, válassza a **naplók** lehetőséget a **Azure monitor** menüből. Részletekért lásd: [a naplózási lekérdezés hatóköre és időbeli tartománya Azure Monitor log Analytics](../azure-monitor/logs/scope.md) .

Íme néhány lekérdezés, amelyet beírhat a **log Search** keresési sávjába, hogy segítsen az Azure Cosmos-erőforrások figyelésében. Ezek a lekérdezések az [új nyelvvel](../azure-monitor/logs/log-query-overview.md)működnek.

* Azure Cosmos DB összes diagnosztikai naplójának lekérdezése egy adott időszakban:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="Microsoft.DocumentDb" and Category=="DataPlaneRequests"

    ```

* Az összes művelet lekérdezése erőforrás szerint csoportosítva:

    ```Kusto
    AzureActivity 
    | where ResourceProvider=="Microsoft.DocumentDb" and Category=="DataPlaneRequests" 
    | summarize count() by Resource

    ```

* Az összes felhasználói tevékenység lekérdezése erőforrás szerint csoportosítva:

    ```Kusto
    AzureActivity 
    | where Caller == "test@company.com" and ResourceProvider=="Microsoft.DocumentDb" and Category=="DataPlaneRequests" 
    | summarize count() by Resource
    ```

## <a name="alerts"></a>Riasztások

Azure Monitor riasztások proaktívan értesítik Önt, ha fontos feltételek találhatók a megfigyelési adataiban. Lehetővé teszik a rendszeren észlelt problémák azonosítását és megoldását, mielőtt az ügyfelek bejelentkeznek. Riasztásokat állíthat be a [metrikák](../azure-monitor/alerts/alerts-metric-overview.md), [naplók](../azure-monitor/alerts/alerts-unified-log.md)és a [tevékenység naplójában](../azure-monitor/alerts/activity-log-alerts.md). A különböző típusú riasztások előnyei és hátrányai

Az alábbi táblázat például az erőforrásokra vonatkozó riasztási szabályokat sorolja fel. A riasztási szabályok részletes listáját a Azure Portalban találja. További információt a [riasztások konfigurálása](create-alerts.md) című cikkben talál.  

| Riasztástípus | Feltétel | Leírás  |
|:---|:---|:---|
|Kérelmek egységeinek korlátozása (metrikus riasztás) |Dimenzió neve: StatusCode, operátor: egyenlő, dimenzió értékei: 429  | Riasztás, ha a tároló vagy az adatbázis túllépte a kiosztott átviteli sebességet. |
|A régió feladatátvétele megtörtént |Operátor: nagyobb, mint, összesítés típusa: darabszám, küszöbérték: 1 | Egyetlen régió feladatátvétele esetén. Ez a riasztás akkor hasznos, ha nem engedélyezte az automatikus feladatátvételt. |
| Kulcsok elforgatása (műveletnapló riasztása)| Esemény szintje: tájékoztatás, állapot: elindítva| Riasztások a fiókok kulcsainak elforgatásakor. Az alkalmazást az új kulcsokkal frissítheti. |

## <a name="monitor-azure-cosmos-db-programmatically"></a><a id="monitor-cosmosdb-programmatically"></a> Figyelő Azure Cosmos DB programozott módon

A portálon elérhető fiók szintű mérőszámok, mint például a fiók tárterület-használata és az összes kérelem, nem érhetők el az SQL API-kon keresztül. A használati adatokat azonban a gyűjtemény szintjén is lekérheti az SQL API-k használatával. A gyűjtési szintű adatgyűjtés lekéréséhez tegye a következőket:

* A REST API használatához [hajtsa végre a Get a gyűjteményt](/rest/api/cosmos-db/get-a-collection). A rendszer a gyűjteményre vonatkozó kvóta-és használati adatokat az x-MS-Resource-kvóta és az x-MS-Resource-használati fejlécekben adja vissza a válaszban.

* A .NET SDK használatához használja a [DocumentClient. ReadDocumentCollectionAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readdocumentcollectionasync) metódust, amely egy olyan [ResourceResponse](/dotnet/api/microsoft.azure.documents.client.resourceresponse-1) ad vissza, amely számos használati tulajdonságot tartalmaz, például **CollectionSizeUsage**, **DatabaseUsage**, **DocumentUsage** és sok más.

További metrikák eléréséhez használja a [Azure monitor SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights)-t. A rendelkezésre álló metrika-definíciókat a meghívásával kérheti le:

```http
https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/providers/microsoft.insights/metricDefinitions?api-version=2018-01-01
```

Az egyes metrikák beolvasásához használja a következő formátumot:

```http
https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/providers/microsoft.insights/metrics?timespan={StartTime}/{EndTime}&interval={AggregationInterval}&metricnames={MetricName}&aggregation={AggregationType}&`$filter={Filter}&api-version=2018-01-01
```

További információ: [Azure monitoring REST API](../azure-monitor/essentials/rest-api-walkthrough.md) .

## <a name="next-steps"></a>Következő lépések

* A Azure Cosmos DB által létrehozott naplók és metrikák hivatkozását a [Azure Cosmos db monitorozási adatok referenciája](monitor-cosmos-db-reference.md) tartalmazza.
* Az Azure-erőforrások figyelésével kapcsolatos részletekért lásd: az [Azure-erőforrások figyelése Azure monitorokkal](../azure-monitor/essentials/monitor-azure-resource.md) .
