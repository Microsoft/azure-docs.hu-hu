---
title: Adatok archiválása Log Analytics munkaterületről az Azure Storage-ba a logikai alkalmazás használatával
description: Leírja, hogyan lehet Azure Logic Apps használatával lekérdezni egy Log Analytics munkaterület adatait, és hogyan küldhet az Azure Storage-ba.
ms.service: azure-monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/02/2020
ms.openlocfilehash: fd66aa1f10a32d94d515a1f0aa25c25331796a8d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102035687"
---
# <a name="archive-data-from-log-analytics-workspace-to-azure-storage-using-logic-app"></a>Adatok archiválása Log Analytics munkaterületről az Azure Storage-ba a logikai alkalmazás használatával
Ez a cikk azt ismerteti, hogyan lehet a [Azure Logic apps](../../logic-apps/index.yml) segítségével lekérdezni a Azure monitor és az Azure Storage szolgáltatásba küldött log Analytics munkaterületről származó adatok lekérdezésére szolgáló módszert. Akkor használja ezt a folyamatot, ha a naplózási és megfelelőségi forgatókönyvek Azure Monitor naplózási adatait szeretné exportálni, vagy egy másik szolgáltatás számára engedélyezi az adatok lekérését.  

## <a name="other-export-methods"></a>Egyéb exportálási módszerek
A cikkben ismertetett módszer egy logikai alkalmazás használatával ütemezett exportálást mutat be a naplókból. Az egyes forgatókönyvekben az adatok exportálásának egyéb lehetőségei a következők:

- Az Log Analytics munkaterületről származó összes adatok Azure Storage-fiókba vagy Event hub-ba való exportálásához használja Azure Monitor naplók Log Analytics munkaterület adatexportálási szolgáltatását. Lásd: [log Analytics munkaterület-adatexportálás Azure monitorban (előzetes verzió)](logs-data-export.md)
- Egyszeri exportálás logikai alkalmazás használatával. [A Logic apps és a Power automatizálás Azure monitor naplók összekötője](logicapp-flow-connector.md)című témakörben talál.
- Egyszeri exportálás a helyi gépre PowerShell-parancsfájl használatával. Lásd: [meghívás-AzOperationalInsightsQueryExport]] ( https://www.powershellgallery.com/packages/Invoke-AzOperationalInsightsQueryExport) .

## <a name="overview"></a>Áttekintés
Ez az eljárás a [Azure monitor naplók összekötőt](/connectors/azuremonitorlogs/) használja, amely lehetővé teszi a log-lekérdezés futtatását egy logikai alkalmazásból, és a kimenetét használja a munkafolyamat más műveleteiben. Ebben az eljárásban az [azure blob Storage-összekötőt](/connectors/azureblob/) használjuk a lekérdezés kimenetének az Azure Storage-ba való küldéséhez. A többi műveletet az alábbi szakaszokban ismertetjük.

![A Logic app áttekintése](media/logs-export-logic-app/logic-app-overview.png)

Amikor adatokat exportál egy Log Analytics munkaterületről, érdemes szűrnie és összesíteni a naplózási adatokat, és optimalizálni a lekérdezést, hogy korlátozza a logikai alkalmazás munkafolyamata által a szükséges adatokhoz feldolgozott adatok mennyiségét. Ha például a bejelentkezési események archiválására van szükség, akkor szűrheti a szükséges eseményeket és csak a szükséges mezőket a következő lekérdezéssel: 

```json
SecurityEvent
| where EventID == 4624 or EventID == 4625
| project TimeGenerated , Account , AccountType , Computer
```

Ha ütemterv szerint exportálja az adatait, a lekérdezésben használja a ingestion_time () függvényt annak biztosításához, hogy ne maradjon le későn érkező adat. Ha a hálózati vagy a platformmal kapcsolatos problémák miatt késleltetik az adatmennyiséget, a betöltési idő használatával biztosítható, hogy a rendszer a következő logikai alkalmazás-végrehajtás része legyen. Példa: [Azure monitor naplók hozzáadása művelet](#add-azure-monitor-logs-action) .

## <a name="prerequisites"></a>Előfeltételek
A következő előfeltételek szükségesek, amelyeket az eljárás végrehajtása előtt kell végrehajtani.

- Log Analytics munkaterület. A logikai alkalmazást létrehozó felhasználónak legalább olvasási engedéllyel kell rendelkeznie a munkaterülethez. 
- Azure Storage-fiók. A Storage-fióknak nem kell ugyanabban az előfizetésben lennie, mint a Log Analytics munkaterületen. A logikai alkalmazást létrehozó felhasználónak írási engedéllyel kell rendelkeznie a Storage-fiókhoz. 


## <a name="connector-limits"></a>Összekötő korlátai
Log Analytics a munkaterület és a naplók lekérdezése Azure Monitor olyan bérlős-szolgáltatások, amelyek olyan korlátozásokat tartalmaznak, amelyek védik és elkülönítik az ügyfeleket, és karbantartják a szolgáltatás minőségét. Nagy mennyiségű adatlekérdezés esetén a következő korlátokat kell figyelembe vennie, ami hatással lehet a logikai alkalmazás ismétlődésének és a napló lekérdezésének konfigurálására:

- A naplók lekérdezése legfeljebb 500 000 sort adhat vissza.
- A naplók lekérdezése legfeljebb 64 000 000 bájtot adhat vissza.
- A naplók lekérdezése alapértelmezés szerint 10 percnél hosszabb ideig nem futhat. 
- Log Analytics-összekötő percenként 100 hívásra van korlátozva.


## <a name="create-container-in-the-storage-account"></a>Tároló létrehozása a Storage-fiókban
A tároló [létrehozása](../../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container) lehetőséggel vegyen fel egy tárolót a Storage-fiókjába az exportált adattároláshoz. A cikkben a tárolóhoz használt név **loganalytics**, de bármilyen nevet használhat.


## <a name="create-logic-app"></a>Logikai alkalmazás létrehozása

Lépjen a Azure Portal **Logic apps** elemre, majd kattintson a **Hozzáadás** gombra. Válassza ki az új logikai alkalmazás tárolására szolgáló **előfizetést**, **erőforráscsoportot** és **régiót** , majd adjon meg egy egyedi nevet. **Log Analytics** beállítás bekapcsolásával információkat gyűjthet a futtatókörnyezet adatairól és eseményeiről az [Azure monitor naplók beállítása és a diagnosztikai adatok összegyűjtése a Azure Logic apps számára](../../logic-apps/monitor-logic-apps-log-analytics.md)című témakörben leírtak szerint. Ez a beállítás nem szükséges a Azure Monitor naplók összekötő használatához.

![Logikai alkalmazás létrehozása](media/logs-export-logic-app/create-logic-app.png)


Kattintson a **felülvizsgálat + létrehozás** , majd a **Létrehozás** elemre. Ha a telepítés befejeződött, kattintson az **erőforrás** megnyitása lehetőségre a **Logic apps Designer** megnyitásához.

## <a name="create-a-trigger-for-the-logic-app"></a>Trigger létrehozása a logikai alkalmazáshoz
Az **Indítás általános eseményindítóval** területen válassza az **Ismétlődés** lehetőséget. Ez egy olyan logikai alkalmazást hoz létre, amely rendszeres időközönként automatikusan fut. A művelet **gyakorisága** mezőben válassza a **nap** és az **intervallum** mezőben az **1** értéket, ha naponta egyszer szeretné futtatni a munkafolyamatot.

![Ismétlődési művelet](media/logs-export-logic-app/recurrence-action.png)


### <a name="add-azure-monitor-logs-action"></a>Azure Monitor naplók hozzáadása művelet
Kattintson az **+ új lépés** gombra egy olyan művelet hozzáadásához, amely az ismétlődési művelet után fut. A **művelet kiválasztása** területen írja be az **Azure monitor** parancsot, majd válassza ki **Azure monitor naplókat**.

![Azure Monitor naplók művelete](media/logs-export-logic-app/select-azure-monitor-connector.png)

Kattintson **Az Azure log Analytics – lekérdezési és listázási eredmények futtatása** elemre.

![Képernyőkép a Logic app Designer egyik lépéséhez hozzáadott új műveletről. Azure Monitor naplók ki vannak emelve a művelet kiválasztása lehetőség alatt.](media/logs-export-logic-app/select-query-action-list.png)

A rendszer kérni fogja, hogy válasszon ki egy bérlőt, és adjon hozzáférést a Log Analytics munkaterülethez azzal a fiókkal, amelyet a munkafolyamat a lekérdezés futtatásához használni fog.


## <a name="add-azure-monitor-logs-action"></a>Azure Monitor naplók hozzáadása művelet
A Azure Monitor naplók művelettel megadhatja a futtatandó lekérdezést. Az ebben a példában használt naplózási lekérdezés óránkénti ismétlődésre van optimalizálva, és az adott végrehajtási időpontra vonatkozó adatokat gyűjti. Ha például a munkafolyamat a 4:35-es időpontban fut, az időtartomány 4:00 és 5:00. Ha úgy módosítja a logikai alkalmazást, hogy más gyakorisággal fusson, szüksége lesz a lekérdezés módosítására is. Ha például úgy állítja be az ismétlődést, hogy naponta fusson, az időpontot a lekérdezésben startofday (make_datetime (év, hónap, nap, 0, 0) értékre állítja be. 

Válassza ki az **előfizetést** és az **erőforráscsoportot** a log Analytics munkaterülethez. Válassza ki *log Analytics munkaterületet* az **erőforrás típusához** , majd válassza ki a munkaterület nevét az **Erőforrás neve** alatt.

Adja hozzá a következő naplózási lekérdezést a **lekérdezési** ablakhoz.  

```Kusto
let dt = now();
let year = datetime_part('year', dt);
let month = datetime_part('month', dt);
let day = datetime_part('day', dt);
let hour = datetime_part('hour', dt);
let startTime = make_datetime(year,month,day,hour,0)-1h;
let endTime = startTime + 1h - 1tick;
AzureActivity
| where ingestion_time() between(startTime .. endTime)
| project 
    TimeGenerated,
    BlobTime = startTime, 
    OperationName ,
    OperationNameValue ,
    Level ,
    ActivityStatus ,
    ResourceGroup ,
    SubscriptionId ,
    Category ,
    EventSubmissionTimestamp ,
    ClientIpAddress = parse_json(HTTPRequest).clientIpAddress ,
    ResourceId = _ResourceId 
```

Az **időtartomány** meghatározza azokat a rekordokat, amelyeket a lekérdezés a **TimeGenerated** oszlop alapján fog tartalmazni. Ezt a lekérdezésben kiválasztott időtartomány értékével megegyező vagy annál nagyobb értékre kell beállítani. Mivel ez a lekérdezés nem a **TimeGenerated** oszlopot használja, **a lekérdezés** beállításban nem érhető el. Az időtartományra vonatkozó további részletekért lásd a [lekérdezés hatókörét](./scope.md) . 

Válassza az **utolsó 4 óra** lehetőséget az **időtartománynál**. Ezzel biztosíthatja, hogy a **TimeGenerated** nagyobb betöltési idővel rendelkező rekordok szerepeljenek az eredményekben.
   
![Képernyőfelvétel: az új Azure Monitor naplók futtatása művelet, futtatási lekérdezés, eredmények megjelenítése.](media/logs-export-logic-app/run-query-list-action.png)


### <a name="add-parse-json-activity-optional"></a>JSON-elemzési tevékenység hozzáadása (nem kötelező)
A **futtatási lekérdezés és a lista eredményei** művelet kimenete JSON-formátumban van formázva. Ezeket az adatelemzéseket elemezheti, és az **összeállítási** művelet előkészítésének részeként kezelheti. 

Megadhat egy JSON-sémát, amely leírja a várt adattartalmat. A tervező elemzi a JSON-tartalmat a séma használatával, és olyan felhasználóbarát jogkivonatokat hoz létre, amelyek a JSON-tartalomban szereplő tulajdonságokat jelölik. Ezután egyszerűen hivatkozhat és felhasználhatja ezeket a tulajdonságokat a logikai alkalmazás munkafolyamataiban. 


Kattintson az **+ új lépés**, majd **a + művelet hozzáadása** lehetőségre. A **művelet kiválasztása** területen írja be a **JSON** -t, majd válassza a **JSON** elemzése lehetőséget.

![A JSON elemzési tevékenység kiválasztása](media/logs-export-logic-app/select-parse-json.png)

Kattintson a **tartalom** mezőbe az előző tevékenységekből származó értékek listájának megjelenítéséhez. Válassza ki a **törzs** elemet a **lekérdezés futtatása és az eredmények listázása** műveletből. Ez a napló lekérdezés kimenete.

[![Törzs kijelölése](media/logs-export-logic-app/select-body.png)](media/logs-export-logic-app/select-body.png#lightbox)

5.  **A séma létrehozásához kattintson a minta hasznos adatok használata** elemre. Futtassa a napló lekérdezését, és másolja a minta hasznos adatokhoz használni kívánt kimenetet.  A minta lekérdezéshez a következő kimenetet használhatja:


```json
{
    "TimeGenerated": "2020-09-29T23:11:02.578Z",
    "BlobTime": "2020-09-29T23:00:00Z",
    "OperationName": "Returns Storage Account SAS Token",
    "OperationNameValue": "MICROSOFT.RESOURCES/DEPLOYMENTS/WRITE",
    "Level": "Informational",
    "ActivityStatus": "Started",
    "ResourceGroup": "monitoring",
    "SubscriptionId": "00000000-0000-0000-0000-000000000000",
    "Category": "Administrative",
    "EventSubmissionTimestamp": "2020-09-29T23:11:02Z",
    "ClientIpAddress": "192.168.1.100",
    "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/monitoring/providers/microsoft.storage/storageaccounts/my-storage-account"
}
```



![JSON-adattartalom elemzése](media/logs-export-logic-app/parse-json-payload.png)

## <a name="add-the-compose-action"></a>Az összeállítás művelet hozzáadása
Az **összeállítás** művelet végrehajtja az elemzett JSON-kimenetet, és létrehozza azt az objektumot, amelyet a blobban tárolnia kell.

Kattintson az **+ új lépés**, majd **a + művelet hozzáadása** lehetőségre. A **válasszon műveletet** területen írja be az **összeállítás** elemet, majd válassza ki az **összeállítás** műveletet.

![Összeállítási művelet kiválasztása](media/logs-export-logic-app/select-compose.png)


Kattintson a **bemenetek** mezőre az előző tevékenységekből származó értékek listájának megjelenítéséhez. A **JSON** elemzése műveletből válassza ki a **törzs** lehetőséget. Ez a napló lekérdezésének elemzett kimenete.

[![Összeállítási művelet törzsének kiválasztása](media/logs-export-logic-app/select-body-compose.png)](media/logs-export-logic-app/select-body-compose.png#lightbox)


## <a name="add-the-create-blob-action"></a>A blob létrehozása művelet hozzáadása
A blob létrehozása művelet beírja a komponált JSON-t a tárolóba.

Kattintson az **+ új lépés**, majd **a + művelet hozzáadása** lehetőségre. A **művelet kiválasztása** területen írja be a **blob** parancsot, majd válassza a **blob létrehozása** műveletet.

![Válassza a blob létrehozása lehetőséget.](media/logs-export-logic-app/select-create-blob.png)

Adja meg a Storage-fiókhoz való kapcsolódás nevét a **kapcsolódási névben** , majd kattintson a mappa **elérési útja** mezőben található mappa ikonra a Storage-fiókban lévő tároló kiválasztásához. Kattintson a **blob nevére** az előző tevékenységek értékeinek megjelenítéséhez. Kattintson a **kifejezés** elemre, és adjon meg egy kifejezést, amely megfelel az adott időintervallumnak. Ehhez a lekérdezéshez, amely óránként fut, a következő kifejezés beállítja a blob nevét az előző órában: 

```json
subtractFromTime(formatDateTime(utcNow(),'yyyy-MM-ddTHH:00:00'), 1,'Hour')
```

[![BLOB kifejezés](media/logs-export-logic-app/blob-expression.png)](media/logs-export-logic-app/blob-expression.png#lightbox)

Kattintson a **blob-tartalom** mezőre az előző tevékenységekből származó értékek listájának megjelenítéséhez, majd válassza a **kimenetek** lehetőséget az **összeállítás** szakaszban.


![BLOB-kifejezés létrehozása](media/logs-export-logic-app/create-blob.png)


## <a name="test-the-logic-app"></a>A logikai alkalmazás tesztelése
A munkafolyamat teszteléséhez kattintson a **Futtatás** gombra. Ha a munkafolyamat hibákat tartalmaz, azt a rendszer a problémával együtt jelzi. Az egyes lépésekhez megtekintheti a végrehajtást és a részletezést, így megtekintheti a bemeneti és kimeneti adatokat a hibák kivizsgálásához. Ha szükséges, olvassa el [a Azure Logic apps munkafolyamat-meghibásodások elhárítása és diagnosztizálása](../../logic-apps/logic-apps-diagnosing-failures.md) című témakört.

[![Futtatási előzmények](media/logs-export-logic-app/runs-history.png)](media/logs-export-logic-app/runs-history.png#lightbox)


## <a name="view-logs-in-storage"></a>A tárolóban lévő naplók megtekintése
Lépjen a Azure Portal **Storage-fiókok** menüjére, és válassza ki a Storage-fiókját. Kattintson a **Blobok csempére** , és válassza ki a blob létrehozása műveletben megadott tárolót. Válassza ki az egyik blobot, majd **szerkessze a blobot**.

[![BLOB-adathalmazok](media/logs-export-logic-app/blob-data.png)](media/logs-export-logic-app/blob-data.png#lightbox)

## <a name="next-steps"></a>Következő lépések

- További információ a [Azure monitor lévő naplók lekérdezéséről](./log-query-overview.md).
- További információ a [Logic apps](../../logic-apps/index.yml)
- További információ az [energiagazdálkodás automatizálásáról](https://flow.microsoft.com).