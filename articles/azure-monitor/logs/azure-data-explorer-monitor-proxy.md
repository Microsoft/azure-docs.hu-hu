---
title: Azure Monitor lekérdezése az Azure Adatkezelő használatával (előzetes verzió)
description: Az Azure Adatkezelő az Azure Adatkezelő, Log Analytics munkaterületek és a klasszikus Application Insights alkalmazások közötti, Azure Monitor-alapú lekérdezések elvégzésére használható.
author: osalzberg
ms.author: bwren
ms.reviewer: bwren
ms.topic: conceptual
ms.date: 10/13/2020
ms.openlocfilehash: 65dba60a798b1157a44a7a198b8eba7de1e8fe81
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102031259"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer-preview"></a>Azure Monitor lekérdezése az Azure Adatkezelő használatával (előzetes verzió)

Az Azure Adatkezelő támogatja az Azure Adatkezelő, a [Application Insights (AI)](../app/app-insights-overview.md)és a [log Analytics (La)](./data-platform-logs.md)közötti több szolgáltatás lekérdezését. Ezután lekérdezheti az Log Analytics/Application Insights munkaterületet az Azure Adatkezelő eszközeivel, és egy több szolgáltatás lekérdezésében is hivatkozhat rá. A cikk bemutatja, hogyan hozhat létre több szolgáltatásból álló lekérdezést, és hogyan adhatja hozzá a Log Analytics/Application Insights munkaterületet az Azure Adatkezelő webes felhasználói felületéhez.

Az Azure Adatkezelő Cross Service lekérdezési folyamata: az :::image type="content" source="media\azure-data-explorer-monitor-proxy\azure-data-explorer-monitor-flow.png" alt-text="Azure adatkezelő proxy folyamata.":::

> [!NOTE]
> * Az Azure-Adatkezelőokból Azure Monitor adatok lekérdezése közvetlenül az Azure Adatkezelő-eszközökről, vagy közvetve, egy Azure Adatkezelő-fürtön futtatott lekérdezéssel, előzetes módban van.
>* Forduljon a [Cross Service lekérdezési](mailto:adxproxy@microsoft.com) csapatához bármilyen kérdéssel.

## <a name="add-a-log-analyticsapplication-insights-workspace-to-azure-data-explorer-client-tools"></a>Log Analytics/Application Insights munkaterület hozzáadása az Azure Adatkezelő ügyféleszközök

1. Ellenőrizze, hogy az Azure Adatkezelő natív fürtje (például a *Súgó* fürt) megjelenik-e a bal oldali menüben, mielőtt a Log Analytics vagy Application Insights fürthöz csatlakozna.

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-data-explorer-web-ui-help-cluster.png" alt-text="Azure Adatkezelő natív fürt.":::

 Az Azure Adatkezelő felhasználói felületén ( https://dataexplorer.azure.com/clusters) válassza a **fürt hozzáadása** elemet.

2. A **fürt hozzáadása** ablakban adja hozzá a La vagy AI-fürt URL-címét.

    * LA: `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`
    * AI esetén: `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`

    * Válassza a **Hozzáadás** lehetőséget.

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-add-cluster.png" alt-text="Fürt hozzáadása.":::
 
>[!NOTE]
>Ha egynél több Log Analytics/Application ininsights munkaterülethez hoz létre egy kapcsolódást, adjon meg egy másik nevet. Ellenkező esetben az összes neve megegyezik a bal oldali ablaktáblán.

 A létrehozást követően a Log Analytics vagy Application Insights munkaterület a natív Azure Adatkezelő-fürt bal oldali ablaktábláján jelenik meg.

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-azure-data-explorer-clusters.png" alt-text="Log Analytics és az Azure Adatkezelő-fürtök.":::
 
> [!NOTE]
> A leképezhető Azure Monitor munkaterületek száma legfeljebb 100 lehet.

## <a name="create-queries-using-azure-monitor-data"></a>Lekérdezések létrehozása a Azure Monitor adataival

A lekérdezéseket a Kusto-lekérdezéseket támogató ügyféleszközök használatával futtathatja, például: Kusto Explorer, Azure Adatkezelő webes felhasználói felület, Jupyter Kqlmagic, flow, PowerQuery, PowerShell, lencse, REST API.

> [!NOTE]
> A szolgáltatáson keresztüli lekérdezési képesség csak az adatok lekérésére szolgál. További információ: a [függvények támogatása](#function-supportability).

> [!TIP]
> * Az adatbázis nevének meg kell egyeznie a Cross Service lekérdezésben megadott erőforrás nevével. A rendszer a nevekben megkülönbözteti a kis- és nagybetűket.
> * A fürtök közötti lekérdezésekben ellenőrizze, hogy helyesek-e a Application Insights alkalmazások és Log Analytics munkaterületek elnevezése.
> * Ha a nevek speciális karaktereket tartalmaznak, azokat az URL-kódolás váltja fel a Cross Service lekérdezésben.
> * Ha a nevek olyan karaktereket tartalmaznak, amelyek nem felelnek meg a [KQL-azonosítók nevének](/azure/data-explorer/kusto/query/schema-entities/entity-names), a kötőjel karakter váltja fel őket **-** .

### <a name="direct-query-on-your-log-analytics-or-application-insights-workspaces-from-azure-data-explorer-client-tools"></a>Közvetlen lekérdezés Log Analytics vagy Application Insights munkaterületről az Azure Adatkezelő ügyféleszközök

Lekérdezéseket futtathat Log Analytics vagy Application Insights munkaterületeken. Ellenőrizze, hogy a bal oldali ablaktáblán a munkaterület van-e kiválasztva.
 
```kusto
Perf | take 10 // Demonstrate cross service query on the Log Analytics workspace
```

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-query-la.png" alt-text="Lekérdezés Log Analytics munkaterületen.":::

### <a name="cross-query-of-your-log-analytics-or-application-insights-and-the-azure-data-explorer-native-cluster"></a>Log Analytics vagy Application Insights és az Azure Adatkezelő natív fürt lekérdezése

Ha több fürtszolgáltatásos lekérdezést futtat, ellenőrizze az Azure Adatkezelő natív fürtöt a bal oldali ablaktáblán. Az alábbi példák azt mutatják be, hogyan kombinálható az Azure Adatkezelő-fürt táblái a Union és a Log Analytics munkaterület együttes [használatával](/azure/data-explorer/kusto/query/unionoperator) .

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <Azure Data Explorer table>, cluster(CL1).database(<workspace-name>).<table name>
```

:::image type="content" source="media\azure-data-explorer-monitor-proxy\azure-data-explorer-cross-query-proxy.png" alt-text="Több szolgáltatás lekérdezése az Azure Adatkezelőból.":::

Ha az [ `join` operátort](/azure/data-explorer/kusto/query/joinoperator)használja, az Unió helyett az [`hint`](/azure/data-explorer/kusto/query/joinoperator#join-hints) Azure adatkezelő natív fürtön kell futtatnia.

### <a name="join-data-from-an-azure-data-explorer-cluster-in-one-tenant-with-an-azure-monitor-resource-in-another"></a>Adatok csatlakoztatása Azure Adatkezelő-fürtből egyik bérlőn egy másik Azure Monitor erőforrással

A szolgáltatások közötti több-bérlős lekérdezések nem támogatottak. Egyetlen bérlőbe jelentkezett be a lekérdezés mindkét erőforrásra kiterjedő futtatásához.

Ha az Azure Adatkezelő erőforrás az "A" Bérlőben található, és Log Analytics munkaterület a "B" Bérlőben található, használja a következő két módszer egyikét:

1. Az Azure Adatkezelő lehetővé teszi szerepkörök hozzáadását a különböző bérlők résztvevői számára. Adja hozzá felhasználói AZONOSÍTÓját a (z) B Bérlőben az Azure Adatkezelő-fürtön lévő meghatalmazott felhasználóként. Ellenőrizze, hogy az Azure Adatkezelő-fürt *["TrustedExternalTenant"](/powershell/module/az.kusto/update-azkustocluster)* tulajdonsága tartalmazza-e a "B" bérlőt. Futtassa a teljes kereszt-lekérdezést a (z) "B" Bérlőben.

2. A [világítótorony](../../lighthouse/index.yml) használatával a Azure monitor-erőforrást az "a" bérlőhöz tervezheti.
### <a name="connect-to-azure-data-explorer-clusters-from-different-tenants"></a>Kapcsolódás Azure Adatkezelő-fürtökhöz különböző bérlők között

A Kusto Explorer automatikusan aláírja azt a bérlőt, amelyhez a felhasználói fiók eredetileg tartozik. Ha a többi bérlő erőforrásait ugyanazzal a felhasználói fiókkal szeretné elérni, `tenantId` explicit módon meg kell adni azt a kapcsolati karakterláncban: `Data Source=https://ade.applicationinsights.io/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName;Initial Catalog=NetDefaultDB;AAD Federated Security=True;Authority ID=` **TenantId**

## <a name="function-supportability"></a>Funkció-támogatás

Az Azure Adatkezelő Cross Service lekérdezi a Application Insights és a Log Analytics egyaránt támogatja a függvényeket.
Ez a funkció lehetővé teszi, hogy a fürtök közötti lekérdezések közvetlenül egy Azure Monitor táblázatos függvényre hivatkozzon.
A következő parancsokat támogatja a Cross Service lekérdezés:

* `.show functions`
* `.show function {FunctionName}`
* `.show database {DatabaseName} schema as json`

Az alábbi ábrán egy példa látható egy táblázatos függvény lekérdezésére az Azure Adatkezelő webes felhasználói felületéről.
A függvény használatához futtassa a nevet a lekérdezési ablakban.

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-function-query.png" alt-text="Táblázatos függvény lekérdezése az Azure Adatkezelő webes felhasználói felületéről.":::

## <a name="additional-syntax-examples"></a>További példák a szintaxisra

A Log Analytics vagy Application Insights fürtök meghívásakor a következő szintaktikai beállítások érhetők el:

|Szintaxis leírása  |Application Insights  |Log Analytics  |
|----------------|---------|---------|
| Olyan fürtön belüli adatbázis, amely csak az előfizetésben definiált erőforrást tartalmazza (**több fürt lekérdezéséhez ajánlott**) |   fürt ( `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>').database('<ai-app-name>` ) | fürt ( `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>` )     |
| Az előfizetésben szereplő összes alkalmazást/munkaterületet tartalmazó fürt    |     fürt ( `https://ade.applicationinsights.io/subscriptions/<subscription-id>` )    |    fürt ( `https://ade.loganalytics.io/subscriptions/<subscription-id>` )     |
|Az előfizetésben szereplő összes alkalmazást/munkaterületet tartalmazó fürt, amely az erőforráscsoport tagjai.    |   fürt ( `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>` )      |    fürt ( `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>` )      |
|Ebben az előfizetésben csak a definiált erőforrást tartalmazó fürt      |    fürt ( `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>` )    |  fürt ( `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>` )     |

## <a name="next-steps"></a>Következő lépések

- További információ a [log Analytics munkaterületek és Application Insights adatszerkezetéről](data-platform-logs.md).
- Megtudhatja, hogyan [írhat lekérdezéseket az Azure Adatkezelőban](/azure/data-explorer/write-queries).