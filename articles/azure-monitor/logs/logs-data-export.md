---
title: Log Analytics munkaterület-adatexportálás Azure Monitorban (előzetes verzió)
description: Log Analytics adatexportálás lehetővé teszi a kijelölt táblák adatainak folyamatos exportálását a Log Analytics munkaterületről egy Azure Storage-fiókba vagy az Azure-Event Hubsba a gyűjtött adatok alapján.
ms.subservice: logs
ms.topic: conceptual
ms.custom: references_regions, devx-track-azurecli
author: bwren
ms.author: bwren
ms.date: 02/07/2021
ms.openlocfilehash: 556570b02664a0afd01137f939bea67a1014b680
ms.sourcegitcommit: f6193c2c6ce3b4db379c3f474fdbb40c6585553b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2021
ms.locfileid: "102449492"
---
# <a name="log-analytics-workspace-data-export-in-azure-monitor-preview"></a>Log Analytics munkaterület-adatexportálás Azure Monitorban (előzetes verzió)
Log Analytics munkaterület-adatexportálás Azure Monitor lehetővé teszi, hogy folyamatosan exportálja a Log Analytics munkaterület kijelölt tábláiból származó adatokat egy Azure Storage-fiókba vagy az Azure-Event Hubsba az összegyűjtött adatok alapján. Ez a cikk részletesen ismerteti ezt a funkciót, valamint az adatexportálás konfigurálásának lépéseit a munkaterületeken.

## <a name="overview"></a>Áttekintés
Miután a Log Analytics munkaterületre konfigurálta az adatexportálást, a munkaterület kiválasztott tábláiba eljuttatott új adategységeket a rendszer automatikusan exportálja a Storage-fiókba az óránkénti hozzáfűzési blobokban vagy az Event hub-ban közel valós időben.

![Az adatexportálás áttekintése](media/logs-data-export/data-export-overview.png)

A befoglalt táblákból származó összes adatok szűrő nélkül exportálhatók. Ha például egy adatexportálási szabályt állít be a *SecurityEvent* táblához, a rendszer a *SecurityEvent* táblába érkező összes, a konfigurációs időpontból kiinduló adattípust exportálja.


## <a name="other-export-options"></a>Egyéb exportálási lehetőségek
Log Analytics munkaterület-adatok exportálásával folyamatosan exportálhatja az adatok Log Analytics munkaterületről. Az egyes forgatókönyvekben az adatok exportálásának egyéb lehetőségei a következők:

- Ütemezett exportálás egy logikai alkalmazás használatával a log lekérdezésből. Ez hasonló az adatexportálási szolgáltatáshoz, de lehetővé teszi, hogy szűrt vagy összesített adatokat küldjön az Azure Storage-ba. Ez a metódus azonban a [naplózási lekérdezési korlátokra](../service-limits.md#log-analytics-workspaces)is vonatkozik, lásd: [adatok archiválása log Analytics munkaterületről az Azure Storage-ba a Logic App használatával](logs-export-logic-app.md).
- Egyszeri exportálás a helyi gépre PowerShell-parancsfájl használatával. Lásd: [meghívás-AzOperationalInsightsQueryExport](https://www.powershellgallery.com/packages/Invoke-AzOperationalInsightsQueryExport).


## <a name="limitations"></a>Korlátozások

- A konfiguráció a CLI-vagy REST-kérelmekkel jelenleg is elvégezhető. A Azure Portal vagy a PowerShell még nem támogatott.
- A ```--export-all-tables``` CLI-ben és a REST-ben való beállítás nem támogatott, és el lesz távolítva. Explicit módon meg kell adnia a táblák listáját az exportálási szabályokban.
- A támogatott táblázatok jelenleg csak a [támogatott táblák](#supported-tables) szakaszban vannak korlátozva. Az egyéni naplózási táblák például jelenleg nem támogatottak.
- Ha az adatexportálási szabály nem támogatott táblát tartalmaz, a művelet sikeres lesz, de a tábla mindaddig nem exportálja az összes adatimportálást, amíg a tábla nem támogatott lesz. 
- Ha az adatexportálási szabály olyan táblát tartalmaz, amely nem létezik, hiba miatt sikertelen lesz ```Table <tableName> does not exist in the workspace``` .
- A Log Analytics munkaterület a következők kivételével bármely régióban lehet:
  - Azure Government-régiók
  - Nyugat-Japán
  - Dél-Brazília délkeleti régiója
  - Kelet-Norvégia
  - Észak-Egyesült Arab
- Létrehozhat két exportálási szabályt egy munkaterületen – a-ben egy szabály lehet az Event hub és egy szabály a Storage-fiókhoz.
- A célként megadott Storage-fióknak vagy az Event hub-nek ugyanabban a régióban kell lennie, mint a Log Analytics munkaterületnek.
- Az exportálandó táblázatok neve nem lehet hosszabb 60 karakternél egy Storage-fióknál, és legfeljebb 47 karakterből állhat az Event hub-ban. A hosszú névvel rendelkező táblákat a rendszer nem exportálja.
- A Azure Data Lake Storage blob-támogatásának hozzáfűzése mostantól [korlátozott nyilvános előzetes](https://azure.microsoft.com/updates/append-blob-support-for-azure-data-lake-storage-preview/) verzióban érhető el

## <a name="data-completeness"></a>Az adatteljesség
Az adatexportálás továbbra is újra próbálkozik az adatok küldésével akár 30 percig, ha a cél nem érhető el. Ha a 30 perc elteltével sem érhető el, akkor a rendszer elveti az adatvesztést, amíg a célhely elérhetővé nem válik.

## <a name="cost"></a>Költségek
Az adatexportálási szolgáltatáshoz jelenleg nem számítunk fel további díjakat. Az adatexportálás díjszabása a jövőben lesz bejelentve, és a számlázás megkezdése előtt megjelenő értesítés. Ha úgy dönt, hogy az adatexportálást a felmondási időszak után is használja, akkor a díjszabást a vonatkozó díjak alapján számítjuk fel.

## <a name="export-destinations"></a>Célhelyek exportálása

### <a name="storage-account"></a>A(z)
Az adatküldés a Storage-fiókokba történik, mivel eléri Azure Monitor és óránkénti hozzáfűzési blobokban tárolja azokat. Az adatexportálási konfiguráció egy tárolót hoz létre a Storage *-* fiókban lévő összes táblához a (z) nevű tárolóban, amelyet a tábla neve követ. Például a tábla *SecurityEvent* egy *am-SecurityEvent* nevű tárolóba fog elküldeni.

A Storage-fiók blobjának elérési útja a következő: *WorkspaceResourceId =/Subscriptions/Subscription-ID/resourcegroups/ \<resource-group\> /providers/Microsoft.operationalinsights/workspaces/ \<workspace\> /y = \<four-digit numeric year\> /m = \<two-digit numeric month\> /d =/h =/m = \<two-digit numeric day\> \<two-digit 24-hour clock hour\> 00/PT1H.js*. Mivel a hozzáfűzési Blobok a Storage-ban lévő 50K-írásokra korlátozódnak, az exportált Blobok száma kiterjeszthető, ha a Hozzáfűzések száma magas. Ilyen esetben a Blobok elnevezési mintája PT1H_ #. JSON lesz, ahol a # a növekményes Blobok száma.

A Storage-fiók adatformátuma [JSON-vonal](../essentials/resource-logs-blob-format.md). Ez azt jelenti, hogy az egyes rekordok egy sortöréssel vannak elválasztva, a külső rekordok tömbje és a JSON-rekordok közötti vesszők nélkül. 

[![Tárolási mintaadatok](media/logs-data-export/storage-data.png)](media/logs-data-export/storage-data.png#lightbox)

Log Analytics adatexportálás írási blobokat írhat a nem módosítható tárolási fiókokba, ha az időalapú adatmegőrzési házirendek engedélyezve vannak a *allowProtectedAppendWrites* beállításnál. Így új blokkokat írhat egy hozzáfűzési blobba, miközben megőrizheti a módosíthatatlansági védelmét és megfelelőségét. Lásd: a [védett hozzáfűzési Blobok írásának engedélyezése](../../storage/blobs/storage-blob-immutable-storage.md#allow-protected-append-blobs-writes).

> [!NOTE]
> A Azure Data Lake Storage blob-támogatásának hozzáfűzése az összes Azure-régióban már előzetes verzióban érhető el. [A korlátozott nyilvános előzetes](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR4mEEwKhLjlBjU3ziDwLH-pURDk2NjMzUTVEVzU5UU1XUlRXSTlHSlkxQS4u) verzióra való regisztrálás előtt hozzon létre egy exportálási szabályt Azure Data Lake tárterületre. Az Exportálás ezen regisztráció nélkül nem fog működni.

### <a name="event-hub"></a>Eseményközpont
A rendszer közel valós időben küldi el az adatait az Event hub számára, mivel Azure Monitor. A rendszer minden olyan adattípushoz létrehoz egy Event hub *-* t, amelyet a név és a tábla neve után exportál. Például a *SecurityEvent* tábla egy *am-SecurityEvent* nevű Event hub számára fog eljuttatni. Ha azt szeretné, hogy az exportált adatai egy adott esemény központhoz jussanak, vagy ha olyan névvel rendelkezik, amely meghaladja az 47 karakteres korlátot, akkor megadhatja a saját Event hub-nevét, és exportálhatja a megadott táblák összes adatait.

> [!IMPORTANT]
> A [támogatott esemény-hubok száma a névtérben 10](../../event-hubs/event-hubs-quotas.md#common-limits-for-all-tiers). Ha több mint 10 táblázatot exportál, adja meg a saját Event hub-nevét az összes tábla exportálásához az Event hub-ba.

Szempontok:
1. Az "alapszintű" Event hub SKU támogatja a kisebb méretű események [korlátját](../../event-hubs/event-hubs-quotas.md#basic-vs-standard-tiers) , és a munkaterület egyes naplói túllépik azt, és el is lehet dobni. Javasoljuk, hogy a "standard" vagy a "dedikált" Event hub legyen az Exportálás célhelye.
2. Az exportált adatok mennyisége gyakran növekszik az idő múlásával, és az Event hub-méretezést növelni kell a nagyobb adatátviteli sebesség kezelésére, valamint a szabályozás és az adatkésés elkerülésére. A Event Hubs automatikus feltöltési funkciójának használatával automatikusan méretezheti és növelheti az átviteli egységek számát, és megfelel a használati igényeknek. A részletekért lásd: az [Azure Event Hubs átviteli egységek automatikus vertikális felskálázása](../../event-hubs/event-hubs-auto-inflate.md) .

## <a name="prerequisites"></a>Előfeltételek
A következő előfeltételek szükségesek, amelyeket a Log Analytics adatexportálás konfigurálása előtt kell végrehajtani.

- A Storage-fióknak és az Event hub-nek már létre kell hoznia, és a Log Analytics munkaterülettel azonos régióban kell lennie. Ha más Storage-fiókba kell replikálnia az adatait, az [Azure Storage-redundancia bármely beállítását](../../storage/common/storage-redundancy.md)használhatja.  
- A Storage-fióknak StorageV1 vagy StorageV2 kell lennie. A klasszikus tároló nem támogatott  
- Ha úgy állította be a Storage-fiókot, hogy engedélyezze a hozzáférést a kiválasztott hálózatokból, a Storage-fiók beállításaiban kivételt kell hozzáadnia, hogy a Azure Monitor a tárhelyre írjon.

## <a name="enable-data-export"></a>Az adatexportálás engedélyezése
A következő lépéseket kell végrehajtani a Log Analytics adatexportálás engedélyezéséhez. A következő részekben talál további részleteket.

- Erőforrás-szolgáltató regisztrálása.
- Megbízható Microsoft-szolgáltatások engedélyezése.
- Hozzon létre egy vagy több olyan adatexportálási szabályt, amely meghatározza az exportálandó táblákat és azok célját.

### <a name="register-resource-provider"></a>Erőforrás-szolgáltató regisztrálása
A következő Azure-erőforrás-szolgáltatónak regisztrálnia kell az előfizetéséhez Log Analytics adatexportálás engedélyezéséhez. 

- Microsoft. bepillantások

Ez az erőforrás-szolgáltató valószínűleg már regisztrálva lesz a legtöbb Azure Monitor felhasználó számára. Az ellenőrzéshez lépjen a Azure Portal **előfizetések** elemre. Válassza ki az előfizetését, majd kattintson az **erőforrás-szolgáltatók** elemre a menü **Beállítások** szakaszában. Keresse meg a **Microsoft. ininsights** szolgáltatást. Ha az állapota **regisztrálva** van, akkor már regisztrálva van. Ha nem, kattintson a **regisztrálás** gombra a regisztráláshoz.

Az erőforrás-szolgáltatót az [Azure Resource Providers és types](../../azure-resource-manager/management/resource-providers-and-types.md)című témakörben leírtak szerint is használhatja. A következő egy minta parancs a PowerShell használatával:

```PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.insights
```

### <a name="allow-trusted-microsoft-services"></a>Megbízható Microsoft-szolgáltatások engedélyezése
Ha úgy állította be a Storage-fiókot, hogy az engedélyezze a hozzáférést a kiválasztott hálózatokból, vegyen fel egy kivételt, amely lehetővé teszi, hogy Azure Monitor írni a fiókba. A Storage-fiókhoz tartozó **tűzfalak és virtuális hálózatok** **lehetőségnél válassza a megbízható Microsoft-szolgáltatások engedélyezése a Storage-fiókhoz** lehetőséget.

[![A Storage-fiók tűzfala és virtuális hálózatai](media/logs-data-export/storage-account-vnet.png)](media/logs-data-export/storage-account-vnet.png#lightbox)

### <a name="create-or-update-data-export-rule"></a>Adatexportálási szabály létrehozása vagy frissítése
Az adatexportálási szabályok határozzák meg azokat a táblákat, amelyekhez a rendszer exportálja az exportált és a célhelyet. Az egyes célhelyekhez jelenleg egyetlen szabályt hozhat létre.

Ha az exportálási szabályok konfigurálásához a workapce található táblák listája szükséges, futtassa ezt a lekérdezést a munkaterületen.

```kusto
find where TimeGenerated > ago(24h) | distinct Type
```

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

N/A

# <a name="powershell"></a>[PowerShell](#tab/powershell)

N/A

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

A következő parancs használatával hozzon létre egy adatexportálási szabályt egy Storage-fiókhoz a parancssori felület használatával.

```azurecli
$storageAccountResourceId = '/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.Storage/storageAccounts/storage-account-name'
az monitor log-analytics workspace data-export create --resource-group resourceGroupName --workspace-name workspaceName --name ruleName --tables SecurityEvent Heartbeat --destination $storageAccountResourceId
```

A következő parancs használatával hozzon létre egy adatexportálási szabályt egy Event hub-hoz a parancssori felület használatával. Minden táblához külön Event hub jön létre.

```azurecli
$eventHubsNamespacesResourceId = '/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.EventHub/namespaces/namespaces-name'
az monitor log-analytics workspace data-export create --resource-group resourceGroupName --workspace-name workspaceName --name ruleName --tables SecurityEvent Heartbeat --destination $eventHubsNamespacesResourceId
```

A következő parancs használatával hozzon létre egy adatexportálási szabályt egy adott Event hubhoz a CLI használatával. Az összes tábla a megadott Event hub-névre lesz exportálva. 

```azurecli
$eventHubResourceId = '/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.EventHub/namespaces/namespaces-name/eventHubName/eventhub-name'
az monitor log-analytics workspace data-export create --resource-group resourceGroupName --workspace-name workspaceName --name ruleName --tables SecurityEvent Heartbeat --destination $eventHubResourceId
```

# <a name="rest"></a>[REST](#tab/rest)

Az alábbi kérelem használatával hozzon létre egy adatexportálási szabályt a REST API használatával. A kérelemnek tulajdonosi jogkivonat-engedélyezést és Content Type Application/JSON-t kell használnia.

```rest
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports/<data-export-name>?api-version=2020-08-01
```

A kérelem törzse megadja a táblák célhelyét. A következő egy minta törzs a Storage-fiók REST-kérelméhez.

```json
{
    "properties": {
        "destination": {
            "resourceId": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/Microsoft.Storage/storageAccounts/storage-account-name"
        },
        "tablenames": [
            "table1",
            "table2" 
        ],
        "enable": true
    }
}
```

Az alábbi példa egy Event hub REST-kérelmét követi.

```json
{
    "properties": {
        "destination": {
            "resourceId": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/Microsoft.EventHub/namespaces/eventhub-namespaces-name"
        },
        "tablenames": [
            "table1",
            "table2"
        ],
        "enable": true
    }
}
```

Az alábbi példa egy Event hub REST-kérelmére szolgál, ahol az Event hub neve van megadva. Ebben az esetben a rendszer az összes exportált adattovábbítást elküldi az Event hub-nak.

```json
{
    "properties": {
        "destination": {
            "resourceId": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/Microsoft.EventHub/namespaces/eventhub-namespaces-name",
            "metaData": {
                "EventHubName": "eventhub-name"
        },
        "tablenames": [
            "table1",
            "table2"
        ],
        "enable": true
    }
  }
}
```

# <a name="template"></a>[Sablon](#tab/json)

A következő parancs használatával hozzon létre egy adatexportálási szabályt egy Storage-fiókhoz sablon használatával.

```
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "defaultValue": "workspace-name",
            "type": "String"
        },
        "workspaceLocation": {
            "defaultValue": "workspace-region",
            "type": "string"
        },
        "storageAccountRuleName": {
            "defaultValue": "storage-account-rule-name",
            "type": "string"
        },
        "storageAccountResourceId": {
            "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Storage/storageAccounts/storage-account-name",
            "type": "String"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "microsoft.operationalinsights/workspaces",
            "apiVersion": "2020-08-01",
            "name": "[parameters('workspaceName')]",
            "location": "[parameters('workspaceLocation')]",
            "resources": [
                {
                  "type": "microsoft.operationalinsights/workspaces/dataexports",
                  "apiVersion": "2020-08-01",
                  "name": "[concat(parameters('workspaceName'), '/' , parameters('storageAccountRuleName'))]",
                  "dependsOn": [
                      "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]"
                  ],
                  "properties": {
                      "destination": {
                          "resourceId": "[parameters('storageAccountResourceId')]"
                      },
                      "tableNames": [
                          "Heartbeat",
                          "InsightsMetrics",
                          "VMConnection",
                          "Usage"
                      ],
                      "enable": true
                  }
              }
            ]
        }
    ]
}
```

A következő parancs használatával hozzon létre egy adatexportálási szabályt egy Event hub-sablon használatával. Minden táblához külön Event hub jön létre.

```
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "defaultValue": "workspace-name",
            "type": "String"
        },
        "workspaceLocation": {
            "defaultValue": "workspace-region",
            "type": "string"
        },
        "eventhubRuleName": {
            "defaultValue": "event-hub-rule-name",
            "type": "string"
        },
        "namespacesResourceId": {
            "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/microsoft.eventhub/namespaces/namespaces-name",
            "type": "String"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "microsoft.operationalinsights/workspaces",
            "apiVersion": "2020-08-01",
            "name": "[parameters('workspaceName')]",
            "location": "[parameters('workspaceLocation')]",
            "resources": [
              {
                  "type": "microsoft.operationalinsights/workspaces/dataexports",
                  "apiVersion": "2020-08-01",
                  "name": "[concat(parameters('workspaceName'), '/', parameters('eventhubRuleName'))]",
                  "dependsOn": [
                      "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]"
                  ],
                  "properties": {
                      "destination": {
                          "resourceId": "[parameters('namespacesResourceId')]"
                      },
                      "tableNames": [
                          "Usage",
                          "Heartbeat"
                      ],
                      "enable": true
                  }
              }
            ]
        }
    ]
}
```

A következő parancs használatával hozzon létre egy adatexportálási szabályt egy adott Event hubhoz sablon használatával. Az összes tábla a megadott Event hub-névre lesz exportálva.

```
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "defaultValue": "workspace-name",
            "type": "String"
        },
        "workspaceLocation": {
            "defaultValue": "workspace-region",
            "type": "string"
        },
        "eventhubRuleName": {
            "defaultValue": "event-hub-rule-name",
            "type": "string"
        },
        "namespacesResourceId": {
            "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/microsoft.eventhub/namespaces/namespaces-name",
            "type": "String"
        },
        "eventhubName": {
            "defaultValue": "event-hub-name",
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "microsoft.operationalinsights/workspaces",
            "apiVersion": "2020-08-01",
            "name": "[parameters('workspaceName')]",
            "location": "[parameters('workspaceLocation')]",
            "resources": [
              {
                  "type": "microsoft.operationalinsights/workspaces/dataexports",
                  "apiVersion": "2020-08-01",
                  "name": "[concat(parameters('workspaceName'), '/', parameters('eventhubRuleName'))]",
                  "dependsOn": [
                      "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]"
                  ],
                  "properties": {
                      "destination": {
                          "resourceId": "[parameters('namespacesResourceId')]",
                          "metaData": {
                              "eventHubName": "[parameters('eventhubName')]"
                          }
                      },
                      "tableNames": [
                          "Usage",
                          "Heartbeat"
                      ],
                      "enable": true
                  }
              }
            ]
        }
    ]
}
```

---

## <a name="view-data-export-rule-configuration"></a>Adatexportálási szabály konfigurációjának megtekintése

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

N/A

# <a name="powershell"></a>[PowerShell](#tab/powershell)

N/A

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az alábbi parancs használatával megtekintheti az adatexportálási szabály konfigurációját a parancssori felület használatával.

```azurecli
az monitor log-analytics workspace data-export show --resource-group resourceGroupName --workspace-name workspaceName --name ruleName
```

# <a name="rest"></a>[REST](#tab/rest)

Az alábbi kérelem használatával megtekintheti az adatexportálási szabály konfigurációját a REST API használatával. A kérelemnek tulajdonosi jogkivonat-hitelesítést kell használnia.

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports/<data-export-name>?api-version=2020-08-01
```

# <a name="template"></a>[Sablon](#tab/json)

N/A

---

## <a name="disable-an-export-rule"></a>Exportálási szabály letiltása

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

N/A

# <a name="powershell"></a>[PowerShell](#tab/powershell)

N/A

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az exportálási szabályok letilthatók, így leállíthatja az exportálást, ha nem kell megtartania az adott időszakra vonatkozó adatmegőrzési időt, például ha a teszt végrehajtása folyamatban van. Használja az alábbi parancsot egy adatexportálási szabály letiltásához a parancssori felület használatával.

```azurecli
az monitor log-analytics workspace data-export update --resource-group resourceGroupName --workspace-name workspaceName --name ruleName --enable false
```

# <a name="rest"></a>[REST](#tab/rest)

Az exportálási szabályok letilthatók, így leállíthatja az exportálást, ha nem kell megtartania az adott időszakra vonatkozó adatmegőrzési időt, például ha a teszt végrehajtása folyamatban van. Az alábbi kérelem használatával tilthatja le az adatexportálási szabályt a REST API használatával. A kérelemnek tulajdonosi jogkivonat-hitelesítést kell használnia.

```rest
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports/<data-export-name>?api-version=2020-08-01
Authorization: Bearer <token>
Content-type: application/json

{
    "properties": {
        "destination": {
            "resourceId": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/Microsoft.Storage/storageAccounts/storage-account-name"
        },
        "tablenames": [
"table1",
    "table2" 
        ],
        "enable": false
    }
}
```

# <a name="template"></a>[Sablon](#tab/json)

Az exportálási szabályok letilthatók, így leállíthatja az exportálást, ha nem kell megtartania az adott időszakra vonatkozó adatmegőrzési időt, például ha a teszt végrehajtása folyamatban van. Állítsa be a ```"enable": false``` sablont az adatexportálás letiltásához.

---

## <a name="delete-an-export-rule"></a>Exportálási szabály törlése

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

N/A

# <a name="powershell"></a>[PowerShell](#tab/powershell)

N/A

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az alábbi parancs használatával törölhet egy adatexportálási szabályt a parancssori felület használatával.

```azurecli
az monitor log-analytics workspace data-export delete --resource-group resourceGroupName --workspace-name workspaceName --name ruleName
```

# <a name="rest"></a>[REST](#tab/rest)

Az alábbi kérelem használatával törölheti az adatexportálási szabályt a REST API használatával. A kérelemnek tulajdonosi jogkivonat-hitelesítést kell használnia.

```rest
DELETE https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports/<data-export-name>?api-version=2020-08-01
```

# <a name="template"></a>[Sablon](#tab/json)

N/A

---

## <a name="view-all-data-export-rules-in-a-workspace"></a>A munkaterület összes adatexportálási szabályának megtekintése

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

N/A

# <a name="powershell"></a>[PowerShell](#tab/powershell)

N/A

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

A következő parancs használatával tekintheti meg a munkaterület összes adatexportálási szabályát a parancssori felület használatával.

```azurecli
az monitor log-analytics workspace data-export list --resource-group resourceGroupName --workspace-name workspaceName
```

# <a name="rest"></a>[REST](#tab/rest)

A következő kérelem használatával megtekintheti a munkaterület összes adatexportálási szabályát a REST API használatával. A kérelemnek tulajdonosi jogkivonat-hitelesítést kell használnia.

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports?api-version=2020-08-01
```

# <a name="template"></a>[Sablon](#tab/json)

N/A

---

## <a name="unsupported-tables"></a>Nem támogatott táblák
Ha az adatexportálási szabály nem támogatott táblát tartalmaz, akkor a konfiguráció sikeres lesz, de a rendszer nem exportálja az adott táblára vonatkozó adatvesztést. Ha a tábla később támogatott, akkor a rendszer az adott időpontban exportálja az adatforrásokat.

Ha az adatexportálási szabály olyan táblát tartalmaz, amely nem létezik, akkor a "táblázat nem <tableName> létezik a munkaterületen" hibaüzenet jelenik meg.


## <a name="supported-tables"></a>Támogatott táblák
A támogatott táblázatok jelenleg az alább megadott értékekre korlátozódnak. A rendszer minden, a táblából származó adattal exportál, hacsak nincsenek korlátozások megadva. A rendszer frissíti a listát a további táblázatok támogatásával.


| Tábla | Korlátozások |
|:---|:---|
| AADDomainServicesAccountLogon |  |
| AADDomainServicesAccountManagement |  |
| AADDomainServicesDirectoryServiceAccess |  |
| AADDomainServicesLogonLogoff |  |
| AADDomainServicesPolicyChange |  |
| AADDomainServicesPrivilegeUse |  |
| AADManagedIdentitySignInLogs |  |
| AADNonInteractiveUserSignInLogs |  |
| AADProvisioningLogs |  |
| AADServicePrincipalSignInLogs |  |
| ABSBotRequests |  |
| ACSBillingUsage |  |
| ACSSMSIncomingOperations |  |
| ADAssessmentRecommendation |  |
| ADFActivityRun |  |
| ADFPipelineRun |  |
| ADFTriggerRun |  |
| ADReplicationResult |  |
| ADSecurityAssessmentRecommendation |  |
| ADTDigitalTwinsOperation |  |
| ADTEventRoutesOperation |  |
| ADTModelsOperation |  |
| ADTQueryOperation |  |
| ADXCommand |  |
| ADXQuery |  |
| AegDeliveryFailureLogs |  |
| AegPublishFailureLogs |  |
| Riasztás |  |
| AmlOnlineEndpointConsoleLog |  |
| ApiManagementGatewayLogs |  |
| AppCenterError |  |
| AppPlatformSystemLogs |  |
| AppServiceAppLogs |  |
| AppServiceAuditLogs |  |
| AppServiceConsoleLogs |  |
| AppServiceFileAuditLogs |  |
| AppServiceHTTPLogs |  |
| AppServicePlatformLogs |  |
| AuditLogs |  |
| AutoscaleEvaluationsLog |  |
| AutoscaleScaleActionsLog |  |
| AWSCloudTrail |  |
| AzureAssessmentRecommendation |  |
| AzureDevOpsAuditing |  |
| BehaviorAnalytics |  |
| BlockchainApplicationLog |  |
| BlockchainProxyLog |  |
| CommonSecurityLog |  |
| ComputerGroup |  |
| ConfigurationData | Részleges támogatás – az egyes adatmennyiségek az exportáláshoz nem támogatott belső szolgáltatásokon keresztül kerülnek betöltésre. Ez a rész hiányzik az exportálási folyamatban. |
| ContainerImageInventory |  |
| ContainerInventory |  |
| ContainerLog |  |
| ContainerNodeInventory |  |
| ContainerServiceLog |  |
| CoreAzureBackup |  |
| DatabricksAccounts |  |
| DatabricksClusters |  |
| DatabricksDBFS |  |
| DatabricksInstancePools |  |
| DatabricksJobs |  |
| DatabricksNotebook |  |
| DatabricksSecrets |  |
| DatabricksSQLPermissions |  |
| DatabricksSSH |  |
| DatabricksWorkspace |  |
| DnsEvents |  |
| DnsInventory |  |
| Dynamics365Activity |  |
| Esemény | Részleges támogatás – a táblázatba tartozó egyes adatmennyiségek a Storage-fiókon keresztül kerülnek betöltésre. Ez a rész hiányzik az exportálási folyamatban. |
| ExchangeAssessmentRecommendation |  |
| FailedIngestion |  |
| FunctionAppLogs |  |
| Szívverés |  |
| HuntingBookmark |  |
| InsightsMetrics | Részleges támogatás – az egyes adatmennyiségek az exportáláshoz nem támogatott belső szolgáltatásokon keresztül kerülnek betöltésre. Ez a rész hiányzik az exportálási folyamatban. |
| IntuneAuditLogs |  |
| IntuneDevices |  |
| IntuneOperationalLogs |  |
| KubeEvents |  |
| KubeHealth |  |
| KubeMonAgentEvents |  |
| KubeNodeInventory |  |
| KubePodInventory |  |
| KubeServices |  |
| LAQueryLogs |  |
| McasShadowItReporting |  |
| MicrosoftAzureBastionAuditLogs |  |
| MicrosoftDataShareReceivedSnapshotLog |  |
| MicrosoftDataShareSentSnapshotLog |  |
| MicrosoftHealthcareApisAuditLogs |  |
| NWConnectionMonitorPathResult |  |
| NWConnectionMonitorTestResult |  |
| OfficeActivity | Részleges támogatás – a O365 webhookokon keresztül betöltött egyes adatok a LA-be. Ez a rész hiányzik az exportálási folyamatban. |
| Művelet | Részleges támogatás – az egyes adatmennyiségek az exportáláshoz nem támogatott belső szolgáltatásokon keresztül kerülnek betöltésre. Ez a rész hiányzik az exportálási folyamatban. |
| Teljesítmény | Részleges támogatás – jelenleg csak a Windows Teljesítményfigyelő-adatszolgáltatások támogatottak. Az Exportálás jelenleg nem tartalmazza a Linux-alapú Teljesítményfigyelőt. |
| PowerBIDatasetsTenant |  |
| PowerBIDatasetsWorkspace |  |
| PowerBIDatasetsWorkspacePreview |  |
| SCCMAssessmentRecommendation |  |
| SCOMAssessmentRecommendation |  |
| SecurityAlert |  |
| SecurityBaseline |  |
| SecurityBaselineSummary |  |
| SecurityDetection |  |
| Biztonsági esemény | Részleges támogatás – a táblázatba tartozó egyes adatmennyiségek a Storage-fiókon keresztül kerülnek betöltésre. Ez a rész hiányzik az exportálási folyamatban. |
| Biztonsági incidens |  |
| SecurityIoTRawEvent |  |
| SecurityNestedRecommendation |  |
| SecurityRecommendation |  |
| SfBAssessmentRecommendation |  |
| SfBOnlineAssessmentRecommendation |  |
| SharePointOnlineAssessmentRecommendation |  |
| SignalRServiceDiagnosticLogs |  |
| SigninLogs |  |
| SPAssessmentRecommendation |  |
| SQLAssessmentRecommendation |  |
| SucceededIngestion |  |
| SynapseBigDataPoolApplicationsEnded |  |
| SynapseBuiltinSqlPoolRequestsEnded |  |
| SynapseGatewayApiRequests |  |
| SynapseIntegrationActivityRuns |  |
| SynapseIntegrationPipelineRuns |  |
| SynapseIntegrationTriggerRuns |  |
| SynapseRbacOperations |  |
| SynapseSqlPoolDmsWorkers |  |
| SynapseSqlPoolExecRequests |  |
| SynapseSqlPoolRequestSteps |  |
| SynapseSqlPoolSqlRequests |  |
| SynapseSqlPoolWaits |  |
| Rendszernapló | Részleges támogatás – a táblázatba tartozó egyes adatmennyiségek a Storage-fiókon keresztül kerülnek betöltésre. Ez a rész hiányzik az exportálási folyamatban. |
| ThreatIntelligenceIndicator |  |
| Frissítés | Részleges támogatás – az egyes adatmennyiségek az exportáláshoz nem támogatott belső szolgáltatásokon keresztül kerülnek betöltésre. Ez a rész hiányzik az exportálási folyamatban. |
| UpdateRunProgress |  |
| UpdateSummary |  |
| Használat |  |
| Megnézendők |  |
| WindowsEvent |  |
| WindowsFirewall |  |
| WireData | Részleges támogatás – az egyes adatmennyiségek az exportáláshoz nem támogatott belső szolgáltatásokon keresztül kerülnek betöltésre. Ez a rész hiányzik az exportálási folyamatban. |
| WVDCheckpoints |  |
| WVDConnections |  |
| WVDErrors |  |
| WVDFeeds |  |
| WVDManagement |  |


## <a name="next-steps"></a>Következő lépések

- [Az exportált adatok lekérdezése az Azure Adatkezelőból](../logs/azure-data-explorer-query-storage.md).
