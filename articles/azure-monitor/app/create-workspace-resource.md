---
title: Új Azure Monitor Application Insights munkaterület-alapú erőforrás létrehozása | Microsoft Docs
description: Ismerkedjen meg az új Azure Monitor Application Insights munkaterület-alapú erőforrások engedélyezéséhez szükséges lépésekkel.
ms.topic: conceptual
ms.date: 10/06/2020
ms.openlocfilehash: 3ec0b25a24af13b29a3568165009f8a6d66e0218
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100578336"
---
# <a name="workspace-based-application-insights-resources"></a>Munkaterület-alapú Application Insights erőforrások

A munkaterület-alapú erőforrások a Application Insights és Log Analytics közötti teljes integrációt támogatják. Most már elküldheti Application Insights telemetria egy közös Log Analytics munkaterületre, amely teljes hozzáférést biztosít Log Analytics összes szolgáltatásához, miközben az alkalmazások, az infrastruktúra és a platformok naplóit egyetlen konszolidált helyen tartja.

Ez a közös Azure szerepköralapú hozzáférés-vezérlést (Azure RBAC) is lehetővé teszi az erőforrások között, és kiküszöböli az alkalmazások közötti vagy munkaterület-lekérdezések szükségességét.

> [!NOTE]
> A munkaterületen alapuló Application Insights erőforrások adatfeldolgozása és megőrzése a Log Analytics munkaterületen történik, ahol az adatok találhatók. [További]( ./pricing.md#workspace-based-application-insights) információ a munkaterület-alapú Application Insights erőforrások számlázásáról.

## <a name="new-capabilities"></a>Új képességek

A munkaterület-alapú Application Insights lehetővé teszi Azure Monitor és Log Analytics legújabb képességeinek kihasználását, beleértve a következőket:

* Az [ügyfél által felügyelt kulcsok (CMK)](../logs/customer-managed-keys.md) lehetővé teszi az adatok titkosítását olyan titkosítási kulcsokkal, amelyekhez csak Ön férhet hozzá.
* Az [Azure Private link](../logs/private-link-security.md) lehetővé teszi, hogy a privát végpontok használatával biztonságosan összekapcsolja az Azure Pásti-szolgáltatásokat a virtuális hálózattal.
* A [Profiler és a Snapshot Debugger saját tárterületének (BYOS)](./profiler-bring-your-own-storage.md) teljes körű vezérlést biztosít a titkosítást a REST-alapú házirend, az élettartam-kezelési házirend és a Application Insights Profiler és Snapshot Debuggerhoz kapcsolódó összes adat hálózati hozzáférése felett. 
* A [kapacitás-foglalási szintek](../logs/manage-cost-storage.md#pricing-model) lehetővé teszik, hogy akár 25%-ot is mentsen az utólagos elszámolású díjszabáshoz képest. 
* Gyorsabb adatfeldolgozás Log Analytics folyamatos átvitelsel.

## <a name="create-workspace-based-resource"></a>Munkaterület-alapú erőforrás létrehozása

Jelentkezzen be a [Azure Portalba](https://portal.azure.com), és hozzon létre egy Application Insights erőforrást:

> [!div class="mx-imgBorder"]
> ![Munkaterület-alapú Application Insights erőforrás](./media/create-workspace-resource/create-workspace-based.png)

Ha még nem rendelkezik meglévő Log Analytics munkaterülettel, [tekintse meg a log Analytics munkaterület-létrehozási dokumentációt](../logs/quick-create-workspace.md).

**A munkaterület-alapú erőforrások jelenleg minden kereskedelmi régióban elérhetők és Azure Government**

Miután létrehozta az erőforrást, megjelenik a megfelelő munkaterület-információ az **Áttekintés** ablaktáblán:

![Munkaterület neve](./media/create-workspace-resource/workspace-name.png)

A kék hivatkozás szövegére kattintva megtekintheti a társított Log Analytics munkaterületet, ahol kihasználhatja az új, egyesített munkaterület-lekérdezési környezetet.

> [!NOTE]
> Továbbra is visszamenőleges kompatibilitást biztosítunk a Application Insights klasszikus erőforrás-lekérdezésekhez, a munkafüzetekhez és a napló alapú riasztásokhoz a Application Insights felhasználói felületén belül. Az [Új munkaterület-alapú tábla struktúrájának/sémájának](apm-tables.md) lekérdezéséhez vagy megtekintéséhez először navigáljon a log Analytics munkaterületre. A **naplók (Analytics)** a Application Insights ablaktáblán való kiválasztásával hozzáférhet a klasszikus Application Insights lekérdezési felületéhez.

## <a name="copy-the-connection-string"></a>A kapcsolati sztring másolása

A [kapcsolódási karakterlánc](./sdk-connection-string.md?tabs=net) azonosítja azt az erőforrást, amelyhez hozzá szeretné rendelni a telemetria-adatait. Azt is lehetővé teszi, hogy módosítsa az erőforrás által a telemetria célként használt végpontokat. A kapcsolódási karakterláncot át kell másolnia, és hozzá kell adnia az alkalmazás kódjához vagy egy környezeti változóhoz.

## <a name="monitoring-configuration"></a>Figyelési konfiguráció

A munkaterület-alapú Application Insights erőforrás létrehozása után a figyelés konfigurálása viszonylag egyszerű.

### <a name="code-based-application-monitoring"></a>Kód alapú alkalmazások figyelése

A kód alapú alkalmazások figyeléséhez csak telepítse a megfelelő Application Insights SDK-t, és mutasson rá a kialakítási kulcsra vagy a kapcsolódási sztringre az újonnan létrehozott erőforráshoz.  

A Application Insights SDK kód alapú figyeléshez való beállításával kapcsolatos részletes dokumentációért forduljon a nyelvhez vagy a keretrendszerhez kapcsolódó dokumentációhoz:

- [ASP.NET](./asp-net.md)
- [ASP.NET Core ](./asp-net-core.md)
- [Háttér-feladatok & modern konzolon futó alkalmazások (.NET/.NET Core)](./worker-service.md)
- [Klasszikus konzolos alkalmazások (.NET)](./console.md) 
- [Java ](./java-get-started.md?tabs=maven)
- [JavaScript](./javascript.md)
- [Node.js](./nodejs.md)
- [Python](./opencensus-python.md)

### <a name="codeless-monitoring-and-visual-studio-resource-creation"></a>Kód-és Visual Studio-erőforrások létrehozása

A szolgáltatások (például a Azure Functions és az Azure App Services) kód nélkül történő figyeléséhez először létre kell hoznia a munkaterület-alapú Application Insights erőforrást, majd az adott erőforrásra kell mutatnia a figyelési konfigurációs fázisban.

Habár ezek a szolgáltatások lehetővé tehetik, hogy új Application Insights erőforrást hozzanak létre a saját erőforrás-létrehozási folyamaton belül, a felhasználói felületi beállításokon keresztül létrehozott erőforrások jelenleg a klasszikus Application Insights élményre korlátozódnak.

Ugyanez vonatkozik a Visual Studióban a ASP.NET és a ASP.NET Core Application Insights erőforrás-létrehozási felületére is. Ki kell választania egy meglévő munkaterület-alapú erőforrást a Visual Studio monitoring engedélyezése felhasználói felületén. Ha kiválasztja az új erőforrás létrehozása elemet a Visual Studióban, a rendszer korlátozza a klasszikus Application Insights-erőforrások létrehozását.

## <a name="creating-a-resource-automatically"></a>Erőforrás automatikus létrehozása

### <a name="azure-cli"></a>Azure CLI

Az előzetes verzió Application Insights Azure CLI-parancsokhoz való hozzáféréshez először futtatnia kell a következőt:

```azurecli
 az extension add -n application-insights
```

Ha nem futtatja a `az extension add` parancsot, a következő hibaüzenet jelenik meg: `az : ERROR: az monitor: 'app-insights' is not in the 'az monitor' command group. See 'az monitor --help'.`

Most futtathatja a következőt a Application Insights erőforrás létrehozásához:

```azurecli
az monitor app-insights component create --app
                                         --location
                                         --resource-group
                                         [--application-type]
                                         [--ingestion-access {Disabled, Enabled}]
                                         [--kind]
                                         [--only-show-errors]
                                         [--query-access {Disabled, Enabled}]
                                         [--tags]
                                         [--workspace]
```

#### <a name="example"></a>Példa

```azurecli
az monitor app-insights component create --app demoApp --location eastus --kind web -g my_resource_group --workspace "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/test1234/providers/microsoft.operationalinsights/workspaces/test1234555"
```

A parancshoz tartozó teljes Azure CLI-dokumentációért lásd az [Azure CLI dokumentációját](/cli/azure/ext/application-insights/monitor/app-insights/component#ext-application-insights-az-monitor-app-insights-component-create).

### <a name="azure-powershell"></a>Azure PowerShell

A `New-AzApplicationInsights` PowerShell-parancs jelenleg nem támogatja a munkaterület-alapú Application Insights erőforrás létrehozását. Ha munkaterület-alapú erőforrást szeretne létrehozni a PowerShell-lel, használja az alábbi Azure Resource Manager-sablonokat, és telepítse a PowerShell használatával.

### <a name="azure-resource-manager-templates"></a>Azure Resource Manager-sablonok

#### <a name="template-file"></a>Sablonfájl

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "string"
        },
        "type": {
            "type": "string"
        },
        "regionId": {
            "type": "string"
        },
        "tagsArray": {
            "type": "object"
        },
        "requestSource": {
            "type": "string"
        },
        "workspaceResourceId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('name')]",
            "type": "microsoft.insights/components",
            "location": "[parameters('regionId')]",
            "tags": "[parameters('tagsArray')]",
            "apiVersion": "2020-02-02-preview",
            "properties": {
                "ApplicationId": "[parameters('name')]",
                "Application_Type": "[parameters('type')]",
                "Flow_Type": "Redfield",
                "Request_Source": "[parameters('requestSource')]",
                "WorkspaceResourceId": "[parameters('workspaceResourceId')]"
            }
        }
    ]
}
```

#### <a name="parameters-file"></a>Parameters fájl

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "type": {
            "value": "web"
        },
        "name": {
            "value": "customresourcename"
        },
        "regionId": {
            "value": "eastus"
        },
        "tagsArray": {
            "value": {}
        },
        "requestSource": {
            "value": "Custom"
        },
        "workspaceResourceId": {
            "value": "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/my_resource_group/providers/microsoft.operationalinsights/workspaces/myworkspacename"
        }
    }
}

```

## <a name="modifying-the-associated-workspace"></a>A társított munkaterület módosítása

A munkaterület-alapú Application Insights erőforrás létrehozása után módosíthatja a társított Log Analytics munkaterületet.

A Application Insights erőforrás ablaktáblán válassza a **Tulajdonságok**  >  **változás munkaterület**  >  **log Analytics munkaterületek** lehetőséget.

## <a name="export-telemetry"></a>Telemetria exportálása

A örökölt folyamatos exportálás funkció nem támogatott a munkaterület-alapú erőforrások esetében. Ehelyett válassza a **diagnosztikai beállítások**  >  **Hozzáadás diagnosztikai beállítás** lehetőséget a Application Insights erőforráson belül. Kiválaszthatja az összes táblát vagy a táblák egy részhalmazát, hogy archiválja a Storage-fiókba, vagy egy Azure Event hub-ba továbbítson.

> [!NOTE]
> A telemetria-exportáláshoz jelenleg nem számítunk fel további díjakat. A szolgáltatás díjszabási információi a [Azure monitor díjszabási oldalán](https://azure.microsoft.com/pricing/details/monitor/)érhetők el.  A számlázás megkezdése előtt a rendszer értesítést küld. Ha úgy dönt, hogy továbbra is használja <feature name> a hirdetményt, akkor a megfelelő díjszabás alapján számítjuk fel a díjat. 
 

## <a name="next-steps"></a>Következő lépések

* [Metrikák böngészése](../essentials/metrics-charts.md)
* [Analytics-lekérdezések](../logs/log-query-overview.md)
