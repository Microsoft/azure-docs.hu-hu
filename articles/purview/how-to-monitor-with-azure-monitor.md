---
title: Az Azure-beli hatáskörébe figyelése
description: Megtudhatja, hogyan konfigurálhatja az Azure-beli hatáskörébe tartozó metrikákat, riasztásokat és diagnosztikai beállításokat Azure Monitor használatával.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 12/03/2020
ms.openlocfilehash: 4cb3965d359980856c238cd563ed8b761754660b
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101667744"
---
# <a name="azure-purview-metrics-in-azure-monitor"></a>Azure-beli hatáskörébe metrikák a Azure Monitor

Ez a cikk azt ismerteti, hogyan konfigurálható az Azure hatáskörébe tartozó mérőszámok, riasztások és diagnosztikai beállítások az Azure Monitor használatával.

## <a name="monitor-azure-purview"></a>Az Azure-beli felügyelet figyelése

Az Azure-beli hatáskörébe tartozó rendszergazdák a Azure Monitor segítségével követhetik nyomon a hatáskörébe tartozó fiók működési állapotát. A rendszer összegyűjti a metrikákat, hogy adatpontokat biztosítson a lehetséges problémák nyomon követéséhez, a hibaelhárításhoz és a hatáskörébe tartozó fiók megbízhatóságának javításához. A metrikákat az Azure-figyelőnek küldik az Azure hatáskörébe tartozó események esetében.

## <a name="aggregated-metrics"></a>Összesített mérőszámok

A metrikák a Azure Portal egy hatáskörébe tartozó fiókhoz érhetők el. A metrikák elérését a hatáskörébe tartozó fiók szerepkör-hozzárendelése szabályozza. A metrikák megtekintéséhez a felhasználóknak az Azure-beli felügyelet olvasó szerepkör részét kell képezniük. A szerepkörök hozzáférési szintjeivel kapcsolatos további tudnivalókért tekintse meg a [figyelési olvasói szerepkör engedélyeit](../azure-monitor/roles-permissions-security.md#built-in-monitoring-roles) .

Az a személy, aki létrehozta a hatáskörébe tartozó fiókot, automatikusan megkapja a metrikák megtekintéséhez szükséges engedélyeket. Ha valaki más szeretné megjeleníteni a metrikákat, adja hozzá őket a **figyelési olvasó** szerepkörhöz a következő lépések végrehajtásával:

### <a name="add-a-user-to-the-monitoring-reader-role"></a>Felhasználó hozzáadása a figyelési olvasó szerepkörhöz

Ha felhasználót szeretne hozzáadni a **figyelési olvasó** szerepkörhöz, akkor a hatáskörébe tartozó fiók tulajdonosa vagy az előfizetés tulajdonosa a következő lépéseket követheti:

1. Lépjen a [Azure Portalra](https://portal.azure.com) , és keresse meg az Azure hatáskörébe tartozó fiók nevét.

2. Válassza a **Hozzáférés-vezérlés (IAM)** lehetőséget.

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/access-iam.png" alt-text="A IAM elérését bemutató képernyőkép.":::

3. Válassza **a szerepkör-hozzárendelés hozzáadása** lehetőséget.

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/add-role-assignment.png" alt-text="A szerepkör-hozzárendelés hozzáadását bemutató képernyőkép.":::

4. Válassza ki a szerepkör- **figyelési olvasót** , és állítsa be az **Azure ad-felhasználó,-csoport vagy egyszerű szolgáltatásnév** hozzáférésének hozzárendelését. És rendelje hozzá a HRE fiókot a metrikák eléréséhez.  

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/add-monitoring-reader.png" alt-text="A figyelési olvasó szerepkör hozzáadását bemutató képernyőkép.":::

## <a name="metrics-visualization"></a>Metrikák vizualizációja

A **figyelési olvasó** szerepkör felhasználói megtekinthetik az Azure monitorba eljuttatott összesített mérőszámokat és diagnosztikai naplókat. A metrikák a megfelelő hatáskörébe tartozó fiók Azure Portal szerepelnek. A Azure Portal az összes elérhető metrika listájának megtekintéséhez válassza a metrikák szakaszt.

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/purview-metrics.png" alt-text="Képernyőfelvétel: az elérhető hatáskörébe tartozó metrikák szakasz." lightbox="./media/how-to-monitor-with-azure-monitor/purview-metrics.png":::

Az Azure hatáskörébe tartozó felhasználók közvetlenül az Azure hatáskörébe tartozó fiók felügyeleti központjából is el tudják érni a metrikák lapot. A Azure Portal megnyitásához válassza Azure Monitor a hatáskörébe tartozó felügyeleti központ főoldalán.

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/launch-metrics-from-management.png" alt-text="Képernyőkép a hatáskörébe tartozó metrikák a felügyeleti központból történő elindításához." lightbox="./media/how-to-monitor-with-azure-monitor/launch-metrics-from-management.png":::

### <a name="available-metrics"></a>Rendelkezésre álló metrikák

A Azure Portal metrika szakaszának megismeréséhez olvassa el a következő két dokumentumot. A metrika Explorer és [a metrikus Explorer speciális funkcióinak](../azure-monitor/essentials/metrics-charts.md) [első lépései](../azure-monitor/essentials/metrics-getting-started.md) .

A következő táblázat tartalmazza a Azure Portal vizsgálatához elérhető metrikák listáját:

| Metrika neve | Metrika névtere | Összesítés típusa | Leírás |
| ------------------- | ------------------- | ------------------- | ----------------- |
| Ellenőrzés megszakítva | Automatizált vizsgálat | Sum <br> Darabszám | A megszakított adatforrás összesítése az időszak során |
| A vizsgálat befejeződött | Automatizált vizsgálat | Sum <br> Darabszám | A befejezett adatforrás összesítése az időszak során |
| Sikertelen vizsgálat | Automatizált vizsgálat | Sum <br> Darabszám | A sikertelen adatforrás összesítése az időszak során |
| Vizsgálat ideje elvégezve | Automatizált vizsgálat | Min <br> Max <br> Sum <br> Átlag | A vizsgálatok során elvégzett teljes idő összesítése az időszakon belül |

## <a name="diagnostic-logs-to-azure-storage-account"></a>Diagnosztikai naplók az Azure Storage-fiókba

A rendszer kibocsátja a nyers telemetria eseményeket a Azure Monitor. Az események további elemzés céljából bejelentkezhetnek az ügyfél Storage-fiókjába. A naplók exportálása a Azure Portal-beli hatáskörébe tartozó fiók diagnosztikai beállításain keresztül végezhető el.

Kövesse az Azure-beli hatáskörébe tartozó fiók diagnosztikai beállításainak létrehozásához szükséges lépéseket.

1. Hozzon létre egy új diagnosztikai beállítást a platform naplófájljainak és metrikáinak összegyűjtéséhez a következő cikk alapján: [diagnosztikai beállítások létrehozása a platform-naplók és-metrikák különböző célhelyekre küldéséhez](../azure-monitor/essentials/diagnostic-settings.md). Válassza ki a célhelyet Azure Storage-fiókként.

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/step-one-diagnostic-setting.png" alt-text="A diagnosztikai napló létrehozását bemutató képernyőkép." lightbox="./media/how-to-monitor-with-azure-monitor/step-one-diagnostic-setting.png":::

2. Naplózza az eseményeket egy Storage-fiókba. A diagnosztikai naplók archiválásához dedikált Storage-fiók használata javasolt. Ezt a cikket követve [hozzon létre egy Storage-fiókot](../storage/common/storage-account-create.md?tabs=azure-portal).

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/step-two-diagnostic-setting.png" alt-text="A diagnosztikai naplóhoz tartozó Storage-fiók hozzárendelését bemutató képernyőkép." lightbox="./media/how-to-monitor-with-azure-monitor/step-two-diagnostic-setting.png":::

Akár 15 percet is igénybe vehet, és megkezdheti a naplók fogadását az újonnan létrehozott Storage-fiókban. [Lásd: az Azure Storage-fiókban található erőforrás-naplók adatmegőrzési és sémája](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage). A diagnosztikai naplók konfigurálása után az események a Storage-fiókba áramlanak.

### <a name="scanstatuslogevent"></a>ScanStatusLogEvent

Az esemény nyomon követi a vizsgálat életciklusát. A vizsgálati művelet az állapotok sorrendjén halad át, a várólistán lévő, a futó és végül a sikeres terminál állapota alapján | Sikertelen | Visszavont. A rendszer az egyes állapot-átváltási eseményeket naplózza, és az esemény sémája a következő tulajdonságokkal fog rendelkezni.

```JSON
{
  "time": "<The UTC time when the event occurred>",
  "properties": {
    "dataSourceName": "<Registered data source friendly name>",
    "dataSourceType": "<Registered data source type>",
    "scanName": "<Scan instance friendly name>",
    "assetsDiscovered": "<If the resultType is succeeded, count of assets discovered in scan run>",
    "assetsClassified": "<If the resultType is succeeded, count of assets classified in scan run>",
    "scanQueueTimeInSeconds": "<If the resultType is succeeded, total seconds the scan instance in queue>",
    "scanTotalRunTimeInSeconds": "<If the resultType is succeeded, total seconds the scan took to run>",
    "runType": "<How the scan is triggered>",
    "errorDetails": "<Scan failure error>",
    "scanResultId": "<Unique GUID for the scan instance>"
  },
  "resourceId": "<The azure resource identifier>",
  "category": "<The diagnostic log category>",
  "operationName": "<The operation that cause the event Possible values for ScanStatusLogEvent category are: 
                    |AdhocScanRun 
                    |TriggeredScanRun 
                    |StatusChangeNotification>",
  "resultType": "Queued – indicates a scan is queued. 
                 Running – indicates a scan entered a running state. 
                 Succeeded – indicates a scan completed successfully. 
                 Failed – indicates a scan failure event. 
                 Cancelled – indicates a scan was cancelled. ",
  "resultSignature": "<Not used for ScanStatusLogEvent category. >",
  "resultDescription": "<This will have an error message if the resultType is Failed. >",
  "durationMs": "<Not used for ScanStatusLogEvent category. >",
  "level": "<The log severity level. Possible values are:
            |Informational
            |Error >",
  "location": "<The location of the Azure Purview account>",
}
```

Az Event példány mintájának naplója az alábbi szakaszban látható.

```JSON
{
  "time": "2020-11-24T20:25:13.022860553Z",
  "properties": {
    "dataSourceName": "AzureDataExplorer-swD",
    "dataSourceType": "AzureDataExplorer",
    "scanName": "Scan-Kzw-shoebox-test",
    "assetsDiscovered": "0",
    "assetsClassified": "0",
    "scanQueueTimeInSeconds": "0",
    "scanTotalRunTimeInSeconds": "0",
    "runType": "Manual",
    "errorDetails": "empty_value",
    "scanResultId": "0dc51a72-4156-40e3-8539-b5728394561f"
  },
  "resourceId": "/SUBSCRIPTIONS/111111111111-111-4EB2/RESOURCEGROUPS/FOOBAR-TEST-RG/PROVIDERS/MICROSOFT.PURVIEW/ACCOUNTS/FOOBAR-HEY-TEST-NEW-MANIFEST-EUS",
  "category": "ScanStatusLogEvent",
  "operationName": "TriggeredScanRun",
  "resultType": "Delayed",
  "resultSignature": "empty_value",
  "resultDescription": "empty_value",
  "durationMs": 0,
  "level": "Informational",
  "location": "eastus",
}
```

## <a name="next-steps"></a>Következő lépések

[Asset-áttekintések megtekintése](asset-insights.md)