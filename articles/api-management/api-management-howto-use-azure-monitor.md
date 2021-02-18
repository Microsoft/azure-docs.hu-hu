---
title: Oktatóanyag – közzétett API-k monitorozása az Azure API Managementban | Microsoft Docs
description: Az oktatóanyag lépéseit követve megtudhatja, hogyan használhatók metrikák, riasztások, tevékenység-naplók és erőforrás-naplók az API-k Azure-API Management való figyeléséhez.
services: api-management
author: vladvino
ms.service: api-management
ms.custom: mvc
ms.topic: tutorial
ms.date: 10/14/2020
ms.author: apimpm
ms.openlocfilehash: 1cb902c4b59193c46dbeca47bb355f0695a0f2c7
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100572791"
---
# <a name="tutorial-monitor-published-apis"></a>Oktatóanyag: közzétett API-k figyelése

A Azure Monitor segítségével megjelenítheti, lekérdezheti, átirányíthatja, archiválhatja és elvégezheti az Azure API Management szolgáltatásból származó mérőszámokat vagy naplókat.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az API-k mérőszámainak megtekintése 
> * Riasztási szabály beállítása 
> * Tevékenységnaplók megtekintése
> * Erőforrás-naplók engedélyezése és megtekintése

Az API-k használatának és teljesítményének figyeléséhez API Management beépített [elemzéseit](howto-use-analytics.md) is használhatja.

## <a name="prerequisites"></a>Előfeltételek

+ Az [Azure API Management terminológiájának](api-management-terminology.md) ismerete.
+ Tekintse át a következő rövid útmutatót: [Azure API Management-példány létrehozása](get-started-create-service-instance.md).
+ Végezze el a következő oktatóanyagot is: [Az első API importálása és közzététele](import-and-publish.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="view-metrics-of-your-apis"></a>Az API-k metrikáinak megtekintése

API Management percenként bocsát ki [metrikákat](../azure-monitor/essentials/data-platform-metrics.md) , így közel valós idejű láthatóságot biztosít az API-k állapotáról és állapotáról. A két leggyakrabban használt mérőszám a következő: Az összes elérhető metrika listáját itt tekintheti meg: [támogatott mérőszámok](../azure-monitor/essentials/metrics-supported.md#microsoftapimanagementservice).

* **Kapacitás** – segít döntéseket hozni a APIM-szolgáltatások frissítésével/lefokozásával kapcsolatban. A mérőszám percentként keletkezik, és az átjáró a jelentés pillanatában érvényes kapacitását tükrözi. A mérőszám értéke a 0–100 tartományban mozog, és az érték számítása az átjáró erőforrásai, például a processzor és a memória kihasználtsága alapján történik.
* **Kérelmek** – segít az API-forgalom elemzésében a API Management szolgáltatásain keresztül. A metrikát percenként bocsátja ki, és az átjáróra vonatkozó kérelmek számát jelenti, beleértve a válasz kódokat, a helyet, az állomásnevet és a hibákat. 

> [!IMPORTANT]
> A következő metrikák elavultak a május 2019-es verziótól kezdve, és augusztus 2023: az átjáró összes kérelme, sikeres átjáró kérése, jogosulatlan átjáró kérése, sikertelen átjáró kérések, egyéb átjáró kérelmek. Telepítse át a kérelmek metrikáját, amely egyenértékű funkciókat biztosít.

:::image type="content" source="media/api-management-howto-use-azure-monitor/apim-monitor-metrics.png" alt-text="A mérőszámok képernyőképe API Management áttekintése":::

A mérőszámok elérése:

1. A [Azure Portal](https://portal.azure.com)navigáljon a API Management-példányhoz. Az **Áttekintés** oldalon tekintse át az API-k fő mérőszámait.
1. A metrikák részletes vizsgálatához válassza a lap alján található menü **mérőszámok** elemét.

    :::image type="content" source="media/api-management-howto-use-azure-monitor/api-management-metrics-blade.png" alt-text="A figyelés menü metrikák elemének képernyőképe":::

1. A legördülő listából válassza ki a megtekinteni kívánt mérőszámokat. Például: **kérelmek**. 
1. A diagram az API-hívások teljes számát mutatja,
1. A diagram a **kérelmek** metrikájának méretei alapján szűrhető. Válassza például a **szűrő hozzáadása** lehetőséget, válassza a **háttérbeli válasz kódja kategóriát**, a 500 értéket adja meg értékként. Most a diagramon az API-háttérbeli sikertelen kérelmek száma látható.   

## <a name="set-up-an-alert-rule"></a>Riasztási szabály beállítása 

A [riasztások](../azure-monitor/alerts/alerts-metric-overview.md) a mérőszámok és a tevékenységek naplói alapján is fogadhatók. Azure Monitor lehetővé teszi a [riasztások konfigurálását](../azure-monitor/alerts/alerts-metric.md) az alábbi műveletek elvégzéséhez:

* E-mail-értesítés küldése
* Webhook meghívása
* Egy Azure Logic App-alkalmazás meghívása

Példa riasztási szabály konfigurálása kérelem metrikája alapján:

1. A [Azure Portal](https://portal.azure.com)navigáljon a API Management-példányhoz.
1. Válassza ki a **riasztások** elemet az oldal alján található menüsorban.

    :::image type="content" source="media/api-management-howto-use-azure-monitor/alert-menu-item.png" alt-text="A figyelés menü riasztások lehetőségének képernyőképe":::

1. Válassza a **+ Új riasztási szabály** lehetőséget.
1. A **riasztási szabály létrehozása** ablakban **válassza a feltétel elemet**.
1. A **jel logikai beállítása** ablakban:
    1. A **jel típusa** mezőben válassza a **metrikák** lehetőséget.
    1. A **jel neve** mezőben válassza a **kérések** lehetőséget.
    1. A **dimenziók felosztása** területen, a **dimenzió neve** területen válassza az **átjáró-válasz kódja kategóriát**.
    1. A **Dimension Values (dimenzió értékek**) területen válassza az **4xx** lehetőséget, például az illetéktelen vagy érvénytelen kérelmeket.
    1. A **riasztási logikában** adjon meg egy küszöbértéket, amely után a riasztást aktiválni kell, majd válassza a **kész** lehetőséget.

    :::image type="content" source="media/api-management-howto-use-azure-monitor/threshold.png" alt-text="A jel logikai ablakok konfigurálásának képernyőképe":::

1. Válasszon egy meglévő műveleti csoportot, vagy hozzon létre egy újat. A következő példában egy új műveleti csoport jön létre. A rendszer értesítő e-mailt küld admin@contoso.com . 

    :::image type="content" source="media/api-management-howto-use-azure-monitor/action-details.png" alt-text="Képernyőkép az új műveleti csoport értesítéseiről":::

1. Adja meg a riasztási szabály nevét és leírását, és válassza ki a súlyossági szintet. 
1. Válassza a **Riasztási szabály létrehozása** lehetőséget.
1. Most tesztelje a riasztási szabályt úgy, hogy az API-kulcs nélkül hívja meg a konferencia API-t. Például:

    ```bash
    curl GET https://apim-hello-world.azure-api.net/conference/speakers HTTP/1.1 
    ```

    A rendszer a próbaidőszak alapján aktiválja a riasztást, és a rendszer elküldi az e-mailt admin@contoso.com . 

    A riasztások a API Management példány **riasztások** lapján is megjelennek.

    :::image type="content" source="media/api-management-howto-use-azure-monitor/portal-alerts.png" alt-text="A riasztások képernyőképe a portálon":::

## <a name="activity-logs"></a>Tevékenységnaplók

A tevékenységnaplók betekintést engednek az API Management-szolgáltatásokban végrehajtott műveletekbe. A tevékenységnaplók segítségével az API Management-szolgáltatásokban végrehajtott írási műveletek (PUT, POST, DELETE) kapcsán megállapíthatja, hogy a „ki, mit és mikor” hajtott végre.

> [!NOTE]
> A tevékenységnaplók az olvasási (GET) műveleteket, illetve az Azure Portalon vagy az eredeti felügyeleti API-k használatával végzett műveleteket nem tartalmazzák.

A tevékenységnaplók az API Management szolgáltatásban, az összes Azure-erőforrás naplói pedig az Azure Monitorban érhetők el. 

:::image type="content" source="media/api-management-howto-use-azure-monitor/api-management-activity-logs.png" alt-text="Képernyőfelvétel a tevékenység-naplóról a portálon":::

A tevékenység naplójának megtekintése:

1. A [Azure Portal](https://portal.azure.com)navigáljon a API Management-példányhoz.

1. Válassza a **műveletnapló** lehetőséget.

    :::image type="content" source="media/api-management-howto-use-azure-monitor/api-management-activity-logs-blade.png" alt-text="Képernyőfelvétel a figyelés menü tevékenység napló eleméről":::
1. Válassza ki a kívánt szűrési hatókört, majd **alkalmazza** a t.

## <a name="resource-logs"></a>Erőforrásnaplók

Az erőforrás-naplók részletes információkat biztosítanak a naplózáshoz és a hibaelhárításhoz fontos műveletekről és hibákról. Az erőforrás-naplók különböznek a tevékenység naplóitól. A műveletnapló betekintést nyújt az Azure-erőforrásokon végrehajtott műveletekre. Az erőforrás-naplók betekintést nyújtanak az erőforrás által végrehajtott műveletekre.

Az erőforrás-naplók konfigurálása:

1. A [Azure Portal](https://portal.azure.com)navigáljon a API Management-példányhoz.
2. Válassza a **diagnosztikai beállítások** lehetőséget.

    :::image type="content" source="media/api-management-howto-use-azure-monitor/api-management-diagnostic-logs-blade.png" alt-text="A figyelés menü diagnosztikai beállítások elemének képernyőképe":::

1. Válassza a **+ diagnosztikai beállítások hozzáadása** elemet.
1. Válassza ki a gyűjteni kívánt naplókat vagy metrikákat.

   Archiválhatja az erőforrás-naplókat a metrikákkal együtt egy Storage-fiókba, továbbíthatja őket egy Event hub-ba, vagy elküldheti azokat egy Log Analytics munkaterületre. 

További információ: [diagnosztikai beállítások létrehozása a platform naplófájljainak és metrikáinak különböző célhelyekre küldéséhez](../azure-monitor/essentials/diagnostic-settings.md).

## <a name="view-diagnostic-data-in-azure-monitor"></a>Diagnosztikai adatAzure Monitorek megtekintése

Ha engedélyezi a GatewayLogs vagy metrikák gyűjtését egy Log Analytics munkaterületen, eltarthat néhány percig, amíg az adatok megjelennek a Azure Monitorban. Az adatmegjelenítés:

1. A [Azure Portal](https://portal.azure.com)navigáljon a API Management-példányhoz.
1. A lap alján található menüből válassza a **naplók** lehetőséget.

    :::image type="content" source="media/api-management-howto-use-azure-monitor/logs-menu-item.png" alt-text="Képernyőfelvétel a figyelési menüben található naplók elemről":::

Lekérdezések futtatása az adatmegjelenítéshez. Több [minta lekérdezés](../azure-monitor/logs/example-queries.md) van megadva, vagy saját maga futtathatja. A következő lekérdezés például lekéri a legutóbbi 24 órás adatmennyiséget a GatewayLogs táblából:

```kusto
ApiManagementGatewayLogs
| where TimeGenerated > ago(1d) 
```

További információ a API Management erőforrás-naplóinak használatáról:

* Ismerkedjen meg [Azure Monitor log Analyticsval](../azure-monitor/logs/log-analytics-tutorial.md), vagy próbálja ki a [log Analytics bemutató környezetét](https://portal.loganalytics.io/demo).

* [A Azure monitor lévő naplók áttekintése](../azure-monitor/logs/log-query-overview.md).

A következő JSON a GatewayLogs egy mintául szolgáló bejegyzést jelez egy sikeres API-kéréshez. Részletekért tekintse meg a [séma-referenciát](gateway-log-schema-reference.md). 

```json
{
    "Level": 4,
    "isRequestSuccess": true,
    "time": "2020-10-14T17:xx:xx.xx",
    "operationName": "Microsoft.ApiManagement/GatewayLogs",
    "category": "GatewayLogs",
    "durationMs": 152,
    "callerIpAddress": "xx.xx.xxx.xx",
    "correlationId": "3f06647e-xxxx-xxxx-xxxx-530eb9f15261",
    "location": "East US",
    "properties": {
        "method": "GET",
        "url": "https://apim-hello-world.azure-api.net/conference/speakers",
        "backendResponseCode": 200,
        "responseCode": 200,
        "responseSize": 41583,
        "cache": "none",
        "backendTime": 87,
        "requestSize": 526,
        "apiId": "demo-conference-api",
        "operationId": "GetSpeakers",
        "apimSubscriptionId": "master",
        "clientTime": 65,
        "clientProtocol": "HTTP/1.1",
        "backendProtocol": "HTTP/1.1",
        "apiRevision": "1",
        "clientTlsVersion": "1.2",
        "backendMethod": "GET",
        "backendUrl": "https://conferenceapi.azurewebsites.net/speakers"
    },
    "resourceId": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group>/PROVIDERS/MICROSOFT.APIMANAGEMENT/SERVICE/APIM-HELLO-WORLD"
}
```

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Az API-k mérőszámainak megtekintése
> * Riasztási szabály beállítása 
> * Tevékenységnaplók megtekintése
> * Erőforrás-naplók engedélyezése és megtekintése


Folytassa a következő oktatóanyaggal:

> [!div class="nextstepaction"]
> [Hívások nyomon követése](api-management-howto-api-inspector.md)