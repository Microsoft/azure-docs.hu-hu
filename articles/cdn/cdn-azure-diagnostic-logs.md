---
title: Diagnosztikai naplók
titleSuffix: Azure Content Delivery Network
description: Ismerje meg, hogyan használhatja az Azure diagnosztikai naplókat az alapvető elemzések mentéséhez, amely lehetővé teszi a használati metrikák exportálását az Azure Content Delivery Network-végpontból.
services: cdn
author: asudbring
manager: KumudD
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/15/2020
ms.author: allensu
ms.openlocfilehash: 96e80de5b8b5ab0a046913ce40ca2d7254dd0133
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100573228"
---
# <a name="diagnostic-logs---azure-content-delivery-network"></a>Diagnosztikai naplók – Azure Content Delivery Network

Az Azure diagnosztikai naplóival megtekintheti az alapvető elemzéseket, és egy vagy több célhelyre mentheti őket, beleértve a következőket:

* Azure Storage-fiók
* Log Analytics-munkaterület
* Azure Event Hubs

Ez a funkció minden díjszabási szinten elérhető CDN-végpontokon. 

A diagnosztikai naplók lehetővé teszik az alapszintű használati metrikák exportálását a CDN-végpontról különböző típusú forrásokra, így testreszabott módon használhatja azokat. A következő típusú adatexportálást végezheti el:

* Az adatexportálás blob Storage-ba, exportálás CSV-be és diagramok készítése az Excelben.
* Adatok exportálása Event Hubs és más Azure-szolgáltatásokból származó adatokkal.
* Adatexportálás Azure Monitor naplókba és a saját Log Analytics-munkaterületen tárolt adatmegjelenítés

A következő lépésekhez Azure CDN profil szükséges. A folytatás előtt tekintse meg a [Azure CDN-profil és-végpont létrehozása](cdn-create-new-endpoint.md) című témakört.

## <a name="enable-logging-with-the-azure-portal"></a>Az Azure Portallal való bejelentkezés engedélyezése

Kövesse az alábbi lépéseket a Azure CDN végpont naplózásának engedélyezéséhez:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 

2. A Azure Portal navigáljon a   ->  **-CDN-profil** összes erőforrásához

2. Válassza ki azt a CDN-végpontot, amelyhez engedélyezni szeretné a diagnosztikai naplókat:

    :::image type="content" source="./media/cdn-diagnostics-log/02_browse-to-diagnostics-logs.png" alt-text="Válassza ki a CDN-végpontot." border="true":::

3. Válassza ki a **diagnosztikai naplókat** a **figyelés** szakaszban:

    :::image type="content" source="./media/cdn-diagnostics-log/03_diagnostics-logs-options.png" alt-text="Válassza ki a diagnosztikai naplókat." border="true":::

### <a name="enable-logging-with-azure-storage"></a>Naplózás engedélyezése az Azure Storage-ban

Ha Storage-fiókot szeretne használni a naplók tárolásához, kövesse az alábbi lépéseket:

 >[!NOTE] 
 >A lépések elvégzéséhez szükség van egy Storage-fiókra. További információkért lásd: **[Azure Storage-fiók létrehozása](../storage/common/storage-account-create.md?tabs=azure-portal&toc=%2fazure%2fstorage%2fblobs%2ftoc.json)** .
    
1. A **diagnosztikai beállítások neve** mezőben adja meg a diagnosztikai napló beállításainak nevét.
 
2. Válassza az **archiválás egy Storage-fiókba** lehetőséget, majd válassza a **CoreAnalytics** lehetőséget. 

3. A **megőrzési idő (nap)** mezőben adja meg a megőrzési napok számát. A nulla nap megőrzése határozatlan ideig tárolja a naplókat. 

4. Válassza ki az előfizetést és a Storage-fiókot a naplókhoz.

    :::image type="content" source="./media/cdn-diagnostics-log/04_diagnostics-logs-storage.png" alt-text="Diagnosztikai naplók – tárterület." border="true":::

3. Kattintson a **Mentés** gombra.

### <a name="send-to-log-analytics"></a>Küldés a Log Analyticsnek

A naplók Log Analytics használatához kövesse az alábbi lépéseket:

>[!NOTE] 
>A lépések végrehajtásához log Analytics-munkaterület szükséges. További információért lásd: **[log Analytics munkaterület létrehozása a Azure Portalban](../azure-monitor/logs/quick-create-workspace.md)** .
    
1. A **diagnosztikai beállítások neve** mezőben adja meg a diagnosztikai napló beállításainak nevét.

2. Válassza **a küldés log Analytics**, majd a **CoreAnalytics** lehetőséget. 

3. Válassza ki az előfizetést és Log Analytics munkaterületet a naplókhoz.

   :::image type="content" source="./media/cdn-diagnostics-log/05-la-workspace.png" alt-text="Diagnosztikai naplók – Log Analytics." border="true":::

4. Kattintson a **Mentés** gombra.

### <a name="stream-to-an-event-hub"></a>Streamelés eseményközpontba

Ha egy Event hub-t szeretne használni a naplókhoz, kövesse az alábbi lépéseket:

>[!NOTE] 
>A lépések végrehajtásához egy Event hub szükséges. Lásd: rövid útmutató **[: Event hub létrehozása Azure Portal használatával](../event-hubs/event-hubs-create.md)** további információ.
    
1. A **diagnosztikai beállítások neve** mezőben adja meg a diagnosztikai napló beállításainak nevét.

2. Válassza **az adatfolyam lehetőséget az Event hub számára**, majd válassza a **CoreAnalytics** lehetőséget. 

3. Válassza ki az előfizetést és az Event hub-névteret a naplókhoz.

   :::image type="content" source="./media/cdn-diagnostics-log/06-eventhub-namespace.png" alt-text="Diagnosztikai naplók – Event hub." border="true":::

4. Kattintson a **Mentés** gombra.


## <a name="enable-logging-with-powershell"></a>Naplózás engedélyezése a PowerShell-lel

Az alábbi példa bemutatja, hogyan engedélyezheti a diagnosztikai naplókat a Azure PowerShell-parancsmagok segítségével.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="enable-diagnostic-logs-in-a-storage-account"></a>Diagnosztikai naplók engedélyezése a Storage-fiókokban

1. Jelentkezzen be Azure PowerShellba:

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```

2. Ha egy Storage-fiókban szeretné engedélyezni a diagnosztikai naplókat, adja meg ezeket a parancsokat. Cserélje le a változókat az értékekre:

    ```azurepowershell-interactive
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $storageacct = <your-storage-account-name>
    $diagname = <your-diagnostic-setting-name>

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    $storage = Get-AzStorageAccount -ResourceGroupName $rsg -Name $storageacct

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -StorageAccountId $storage.id -Enabled $true -Categories CoreAnalytics
    ```

### <a name="enable-diagnostics-logs-for-log-analytics-workspace"></a>Diagnosztikai naplók engedélyezése Log Analytics munkaterülethez

1. Jelentkezzen be Azure PowerShellba:

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```
2. Log Analytics munkaterülethez tartozó diagnosztikai naplók engedélyezéséhez adja meg ezeket a parancsokat. Cserélje le a változókat az értékekre:

    ```azurepowershell-interactive
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $workspacename = <your-log-analytics-workspace-name>
    $diagname = <your-diagnostic-setting-name>

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    $workspace = Get-AzOperationalInsightsWorkspace -ResourceGroupName $rsg -Name $workspacename

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -WorkspaceId $workspace.ResourceId -Enabled $true -Categories CoreAnalytics
    ```
### <a name="enable-diagnostics-logs-for-event-hub-namespace"></a>Diagnosztikai naplók engedélyezése az Event hub-névtérhez

1. Jelentkezzen be Azure PowerShellba:

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```
2. Log Analytics munkaterülethez tartozó diagnosztikai naplók engedélyezéséhez adja meg ezeket a parancsokat. Cserélje le a változókat az értékekre:

    ```azurepowershell-interactive
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $eventhubname = <your-event-hub-namespace-name>
    $diagname = <your-diagnostic-setting-name>

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -EventHubName $eventhubname -Enabled $true -Categories CoreAnalytics
    ```

## <a name="consuming-diagnostics-logs-from-azure-storage"></a>Diagnosztikai naplók felhasználása az Azure Storage-ből
Ez a szakasz a CDN alapanalitikai sémáját, az Azure Storage-fiókban lévő szervezetet ismerteti, és a naplófájlokat egy CSV-fájlban tölti le.

### <a name="using-microsoft-azure-storage-explorer"></a>Microsoft Azure Storage Explorer használata
Az eszköz letöltéséhez lásd: [Azure Storage Explorer](https://storageexplorer.com/). A szoftver letöltését és telepítését követően konfigurálja úgy, hogy ugyanazt az Azure Storage-fiókot használja, amelyet a CDN diagnosztikai naplói célhelyként konfigurált.

1.  **Microsoft Azure Storage Explorer** megnyitása
2.  A Storage-fiók megkeresése
3.  Bontsa ki a **blob-tárolók** csomópontot ebben a Storage-fiókban.
4.  Válassza ki az elemzések *-naplók-coreanalytics* nevű tárolót.
5.  Az eredmények a jobb oldali ablaktáblán jelennek meg, az első szinttől kezdve, ahogy a *resourceId =*. Folytassa az egyes szintek kiválasztását, amíg meg nem találja a *PT1H.js* fájlt. Az elérési út magyarázatát lásd: [blob Path Format](cdn-azure-diagnostic-logs.md#blob-path-format).
6.  A fájlokban lévő összes blob- *PT1H.js* egy órányi elemzési naplókat jelöl egy adott CDN-végpont vagy az egyéni tartomány számára.
7.  A JSON-fájl tartalmának sémáját az alapvető elemzési naplók sémája ismerteti.


#### <a name="blob-path-format"></a>BLOB elérési útjának formátuma

Az alapvető elemzési naplók óránként jönnek létre, és az adatok gyűjtése és tárolása egy Azure-blobon belül, JSON-adattartalomként történik. A Storage Explorer eszköz a "/" karaktert a címtár-elválasztóként értelmezi, és megjeleníti a hierarchiát. Az Azure-Blob elérési útja úgy jelenik meg, mintha hierarchikus struktúra van, és a blob nevét jelöli. A blob neve a következő elnevezési konvenciót követi:    

```resourceId=/SUBSCRIPTIONS/{Subscription Id}/RESOURCEGROUPS/{Resource Group Name}/PROVIDERS/MICROSOFT.CDN/PROFILES/{Profile Name}/ENDPOINTS/{Endpoint Name}/ y={Year}/m={Month}/d={Day}/h={Hour}/m={Minutes}/PT1H.json```

**Mezők leírása:**

|Érték|Leírás|
|-------|---------|
|Előfizetés azonosítója    |Az Azure-előfizetés azonosítója GUID formátumban.|
|Erőforráscsoport neve |Azon erőforráscsoport neve, amelyhez a CDN-erőforrások tartoznak.|
|Profilnév |A CDN-profil neve|
|Végpont neve |A CDN-végpont neve|
|Year|  Az év négyjegyű ábrázolása, például 2017|
|Month (hónap)| A hónap számának kétszámjegyű ábrázolása. 01 = Január... 12 = december|
|Nap|   A hónap napjának kétszámjegyű ábrázolása|
|PT1H.jsbekapcsolva| Az elemzési adatokat tároló tényleges JSON-fájl|

### <a name="exporting-the-core-analytics-data-to-a-csv-file"></a>Az alapvető elemzési adatfájlok exportálása CSV-fájlba

Az alapvető elemzések eléréséhez meg kell adni az eszközhöz tartozó mintakód kódját. Ez az eszköz lehetővé teszi, hogy a JSON-fájlokat sima, vesszővel tagolt fájlformátumba töltse le, amely diagramok vagy egyéb összesítések létrehozásához használható.

A következőképpen használhatja az eszközt:

1.  Látogasson el a GitHub-hivatkozásra: [https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv](https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv)
2.  Töltse le a kódot.
3.  A fordításhoz és a konfiguráláshoz kövesse az utasításokat.
4.  Futtassa az eszközt.
5.  Az eredményül kapott CSV-fájl egy egyszerű, lapos hierarchiában jeleníti meg az elemzési adatforrásokat.

## <a name="log-data-delays"></a>Naplózási adatkésések

Az alábbi táblázat a **microsofttól Azure CDN standard** Azure CDN, a **Akamai standard** és a **Verizon Azure CDN standard/prémium** szintű adatnaplózási késéseit mutatja be.

Microsoft-naplózási adatkésések | A Verizon-napló adatkésései | Akamai-naplózási adatkésések
--- | --- | ---
Késleltetve 1 órával. | Késleltetve 1 órával, és a végpont-propagálás befejezése után akár 2 órát is igénybe vehet. | 24 órával késleltetve; Ha több mint 24 órája lett létrehozva, a kezdéshez akár 2 órát is igénybe vesz. Ha nemrég jött létre, akár 25 órát is igénybe vehet, hogy a naplók megjelenjenek.

## <a name="diagnostic-log-types-for-cdn-core-analytics"></a>A CDN Core Analytics diagnosztikai naplóinak típusai

A Microsoft jelenleg csak alapszintű elemzési naplókat kínál, amelyek a HTTP-válaszok statisztikáit és a kilépési statisztikákat bemutató mérőszámokat tartalmaznak a CDN-pop/Edge-ből.

### <a name="core-analytics-metrics-details"></a>Alapvető elemzési mérőszámok részletei
A következő táblázat az alapvető elemzési naplókban elérhető metrikák listáját tartalmazza:

* **Azure CDN standard a Microsofttól**
* **Azure CDN standard a Akamai**
* **Standard/prémium szintű Azure CDN a Verizontól**

Nem minden metrika érhető el az összes szolgáltatótól, bár az ilyen eltérések minimálisak. A tábla azt is megjeleníti, hogy egy adott metrika elérhető-e a szolgáltatótól. A metrikák csak azokra a CDN-végpontokra érhetők el, amelyeken forgalom van rajtuk.


|Metric                     | Leírás | Microsoft | Verizon | Akamai |
|---------------------------|-------------|-----------|---------|--------|
| RequestCountTotal         | A kérelem összes találatának száma ebben az időszakban. | Igen | Igen |Yes |
| RequestCountHttpStatus2xx | Az 2xx HTTP-kódot eredményező kérések száma (például 200, 202). | Igen | Igen |Yes |
| RequestCountHttpStatus3xx | Az 3xx HTTP-kódot eredményező kérések száma (például 300, 302). | Igen | Igen |Yes |
| RequestCountHttpStatus4xx | Az 4xx HTTP-kódot eredményező kérések száma (például 400, 404). | Igen | Igen |Yes |
| RequestCountHttpStatus5xx | Az 5xx HTTP-kódot eredményező kérések száma (például 500, 504). | Igen | Igen |Yes |
| RequestCountHttpStatusOthers | Az összes többi HTTP-kód száma (a 2xx-5xx kívül). | Igen | Igen |Yes |
| RequestCountHttpStatus200 | Az 200 HTTP-kód válaszát eredményező kérések száma. | Igen | Nem  |Igen |
| RequestCountHttpStatus206 | Az 206 HTTP-kód válaszát eredményező kérések száma. | Igen | Nem  |Igen |
| RequestCountHttpStatus302 | Az 302 HTTP-kód válaszát eredményező kérések száma. | Igen | Nem  |Igen |
| RequestCountHttpStatus304 | Az 304 HTTP-kód válaszát eredményező kérések száma. | Igen | Nem  |Igen |
| RequestCountHttpStatus404 | Az 404 HTTP-kód válaszát eredményező kérések száma. | Igen | Nem  |Igen |
| RequestCountCacheHit | A gyorsítótárban találatot eredményező kérések száma. Az eszköz közvetlenül a POP-ból a-ügyfélhez lett kézbesítve. | Igen | Igen | Nem  |
| RequestCountCacheMiss | A gyorsítótárból kihagyott összes kérelem száma. A gyorsítótár-kihagyás azt jelenti, hogy az eszköz nem található az ügyfélhez legközelebb lévő POP-ban, és a forrásból lett beolvasva. | Igen | Igen | Nem |
| RequestCountCacheNoCache | Az eszközre irányuló, a peremhálózati felhasználói konfiguráció miatt nem gyorsítótárazott kérelmek száma. | Igen | Igen | Nem |
| RequestCountCacheUncacheable | Azon eszközökre irányuló kérelmek száma, amelyek megakadályozták az objektum Cache-Control gyorsítótárazását és a fejlécek lejáratát. Ez a szám azt jelzi, hogy nem szabad gyorsítótárazni a POP-ban vagy a HTTP-ügyfélen. | Igen | Igen | Nem |
| RequestCountCacheOthers | Az összes olyan kérelem száma, amelynél a gyorsítótár állapota nem szerepel a fentiekben. | Nem | Igen | Nem  |
| EgressTotal | Kimenő adatforgalom GB-ban | Igen |Igen |Yes |
| EgressHttpStatus2xx | Kimenő adatátvitel * a 2xx HTTP-állapotkódok GB-ban való kitöltéséhez. | Igen | Igen | Nem  |
| EgressHttpStatus3xx | Kimenő adatforgalom a 3xx HTTP-állapotkódot tartalmazó válaszok esetében GB-ban. | Igen | Igen | Nem  |
| EgressHttpStatus4xx | Kimenő adatforgalom a 4xx HTTP-állapotkódot tartalmazó válaszok esetében GB-ban. | Igen | Igen | Nem  |
| EgressHttpStatus5xx | Kimenő adatforgalom a 5xx HTTP-állapotkódot tartalmazó válaszok esetében GB-ban. | Igen | Igen | Nem |
| EgressHttpStatusOthers | Kimenő adatforgalom a más HTTP-állapotkódot tartalmazó válaszok esetében GB-ban. | Igen | Igen | Nem  |
| EgressCacheHit | Kimenő adatforgalom a CDN-gyorsítótárban/-éleken közvetlenül a CDN-gyorsítótárból továbbított válaszok esetében. | Igen | Igen | Nem |
| EgressCacheMiss. | Kimenő adatforgalom a legközelebbi POP-kiszolgálón nem található válaszok esetében, és a rendszer lekéri a forrás-kiszolgálóról. | Igen | Igen | Nem |
| EgressCacheNoCache | A kimenő adatforgalom olyan eszközök esetében, amelyek a peremhálózat felhasználói konfigurációja miatt nem gyorsítótárazva vannak. | Igen | Igen | Nem |
| EgressCacheUncacheable | Kimenő adatforgalom olyan eszközökön, amelyeket nem sikerült gyorsítótárazni az objektum Cache-Control és a fejlécek lejárata miatt. Azt jelzi, hogy nem szabad gyorsítótárazni a POP-ban vagy a HTTP-ügyfélen. | Igen | Igen | Nem |
| EgressCacheOthers | Kimenő adatforgalom más gyorsítótár-forgatókönyvekhez. | Nem | Igen | Nem |

* A kimenő adatátvitel a CDN POP-kiszolgálókról az ügyfélnek továbbított forgalomra utal.


### <a name="schema-of-the-core-analytics-logs"></a>Az alapvető elemzési naplók sémája 

Az összes napló JSON formátumban van tárolva, és minden bejegyzés sztring típusú mezőket tartalmaz a következő séma szerint:

```json
    "records": [
        {
            "time": "2017-04-27T01:00:00",
            "resourceId": "<ARM Resource Id of the CDN Endpoint>",
            "operationName": "Microsoft.Cdn/profiles/endpoints/contentDelivery",
            "category": "CoreAnalytics",
            "properties": {
                "DomainName": "<Name of the domain for which the statistics is reported>",
                "RequestCountTotal": integer value,
                "RequestCountHttpStatus2xx": integer value,
                "RequestCountHttpStatus3xx": integer value,
                "RequestCountHttpStatus4xx": integer value,
                "RequestCountHttpStatus5xx": integer value,
                "RequestCountHttpStatusOthers": integer value,
                "RequestCountHttpStatus200": integer value,
                "RequestCountHttpStatus206": integer value,
                "RequestCountHttpStatus302": integer value,
                "RequestCountHttpStatus304": integer value,
                "RequestCountHttpStatus404": integer value,
                "RequestCountCacheHit": integer value,
                "RequestCountCacheMiss": integer value,
                "RequestCountCacheNoCache": integer value,
                "RequestCountCacheUncacheable": integer value,
                "RequestCountCacheOthers": integer value,
                "EgressTotal": double value,
                "EgressHttpStatus2xx": double value,
                "EgressHttpStatus3xx": double value,
                "EgressHttpStatus4xx": double value,
                "EgressHttpStatus5xx": double value,
                "EgressHttpStatusOthers": double value,
                "EgressCacheHit": double value,
                "EgressCacheMiss": double value,
                "EgressCacheNoCache": double value,
                "EgressCacheUncacheable": double value,
                "EgressCacheOthers": double value,
            }
        }

    ]
}
```

Az *idő* az óra határának kezdő időpontját jelöli, amelynél a statisztikát jelenteni kell. A CDN-szolgáltató által nem támogatott mérőszámok dupla vagy egész érték helyett null értéket eredményeznek. Ez a Null érték jelzi a metrika hiányát, és eltér a 0 értéktől. Ezen mérőszámok egy készlete a végponton van konfigurálva.

Példa tulajdonságai:

```json
{
     "DomainName": "manlingakamaitest2.azureedge.net",
     "RequestCountTotal": 480,
     "RequestCountHttpStatus2xx": 480,
     "RequestCountHttpStatus3xx": 0,
     "RequestCountHttpStatus4xx": 0,
     "RequestCountHttpStatus5xx": 0,
     "RequestCountHttpStatusOthers": 0,
     "RequestCountHttpStatus200": 480,
     "RequestCountHttpStatus206": 0,
     "RequestCountHttpStatus302": 0,
     "RequestCountHttpStatus304": 0,
     "RequestCountHttpStatus404": 0,
     "RequestCountCacheHit": null,
     "RequestCountCacheMiss": null,
     "RequestCountCacheNoCache": null,
     "RequestCountCacheUncacheable": null,
     "RequestCountCacheOthers": null,
     "EgressTotal": 0.09,
     "EgressHttpStatus2xx": null,
     "EgressHttpStatus3xx": null,
     "EgressHttpStatus4xx": null,
     "EgressHttpStatus5xx": null,
     "EgressHttpStatusOthers": null,
     "EgressCacheHit": null,
     "EgressCacheMiss": null,
     "EgressCacheNoCache": null,
     "EgressCacheUncacheable": null,
     "EgressCacheOthers": null
}

```

## <a name="additional-resources"></a>További források

* [Azure-beli diagnosztikai naplók](../azure-monitor/essentials/platform-logs-overview.md)
* [Core Analytics Azure CDN kiegészítő portálon keresztül](./cdn-analyze-usage-patterns.md)
* [Azure Monitor-naplók](../azure-monitor/logs/log-query-overview.md)
* [Azure Log Analytics REST API](/rest/api/loganalytics)