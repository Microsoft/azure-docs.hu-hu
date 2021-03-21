---
title: Windows-tárolók figyelése és diagnosztizálása
description: Ebben az oktatóanyagban Azure Monitor naplókat konfigurál az Service Fabric Azure-beli Windows-tárolók figyelésére és diagnosztizálására.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc
ms.openlocfilehash: b7689d6e259055137a8d1d3c61552790ab9f28d3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100588240"
---
# <a name="tutorial-monitor-windows-containers-on-service-fabric-using-azure-monitor-logs"></a>Oktatóanyag: Windows-tárolók figyelése Service Fabric Azure Monitor naplók használatával

Ez az oktatóanyag harmadik része, amely végigvezeti a Azure Monitor naplók konfigurálásán, hogy figyelje a Windows-tárolókat Service Fabricn.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Azure Monitor naplók konfigurálása a Service Fabric-fürthöz
> * Log Analytics-munkaterület használata a tárolók és csomópontok naplóinak megtekintéséhez és lekérdezéséhez
> * A Log Analytics-ügynök konfigurálása tároló- és csomópontmetrikák felvételéhez

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdése előtt:

* Rendelkeznie kell egy fürttel az Azure-on, vagy [létre kell hoznia egyet ennek az oktatóanyagnak a segítségével](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* [Üzembe kell helyeznie hozzá egy tárolóba helyezett alkalmazást](service-fabric-host-app-in-a-container.md)

## <a name="setting-up-azure-monitor-logs-with-your-cluster-in-the-resource-manager-template"></a>Azure Monitor naplók beállítása a fürttel a Resource Manager-sablonban

Abban az esetben, ha az oktatóanyag első részében [megadott sablont](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-OMS-UnSecure) használta, tartalmaznia kell az általános Service Fabric Azure Resource Manager-sablon alábbi kiegészítéseit. Abban az esetben, ha olyan fürttel rendelkezik, amelyet a tárolók figyelésére szeretne beállítani Azure Monitor naplókkal:

* Hajtsa végre az alábbi módosításokat a Resource Manager-sablonon.
* Helyezze üzembe a PowerShell használatával a fürt a [sablon üzembe helyezésével](./service-fabric-cluster-creation-via-arm.md) történő frissítéséhez. Az Azure Resource Manager megállapítja, hogy az erőforrás létezik, ezután pedig frissítésként közzéteszi.

### <a name="adding-azure-monitor-logs-to-your-cluster-template"></a>Azure Monitor naplók hozzáadása a fürt sablonhoz

Hajtsa végre a következő módosításokat a *template.json* fájlban:

1. Adja hozzá a Log Analytics-munkaterület helyét és nevét a *parameters* (paraméterek) szakaszhoz:

    ```json
    "omsWorkspacename": {
      "type": "string",
      "defaultValue": "[toLower(concat('sf',uniqueString(resourceGroup().id)))]",
      "metadata": {
        "description": "Name of your Log Analytics Workspace"
      }
    },
    "omsRegion": {
      "type": "string",
      "defaultValue": "East US",
      "allowedValues": [
        "West Europe",
        "East US",
        "Southeast Asia"
      ],
      "metadata": {
        "description": "Specify the Azure Region for your Log Analytics workspace"
      }
    }
    ```

    A név vagy hely értékének módosításához adja hozzá ugyanazokat a paramétereket a *template.parameters.json* fájlhoz, és módosítsa az ott használt értékeket.

2. Adja hozzá a megoldás nevét és a megoldást a *variables* (változók) szakaszhoz:

    ```json
    "omsSolutionName": "[Concat('ServiceFabric', '(', parameters('omsWorkspacename'), ')')]",
    "omsSolution": "ServiceFabric"
    ```

3. Adja hozzá a Microsoft Monitoring Agentet virtuálisgép-bővítményként. A virtuálisgép-méretezési készletek erőforrásának megkeresése: *erőforrások*  >  *"apiVersion": "[változók (' vmssApiVersion ')]"*. A   >  *virtualMachineProfile*  >  *extensionProfile*  >  -*bővítmények* tulajdonságok területén adja hozzá a következő bővítmény leírását a *ServiceFabricNode* -bővítmény alatt: 
    
    ```json
    {
        "name": "[concat(variables('vmNodeType0Name'),'OMS')]",
        "properties": {
            "publisher": "Microsoft.EnterpriseCloud.Monitoring",
            "type": "MicrosoftMonitoringAgent",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "workspaceId": "[reference(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')), '2015-11-01-preview').customerId]"
            },
            "protectedSettings": {
                "workspaceKey": "[listKeys(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')),'2015-11-01-preview').primarySharedKey]"
            }
        }
    },
    ```

4. Adja hozzá a Log Analytics-munkaterületet önálló erőforrásként. A *resources* (erőforrások) szakaszban, a virtuálisgép-méretezési csoportok erőforrásait követően vegye fel az alábbiakat:

    ```json
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[parameters('omsWorkspacename')]",
        "type": "Microsoft.OperationalInsights/workspaces",
        "properties": {
            "sku": {
                "name": "Free"
            }
        },
        "resources": [
            {
                "apiVersion": "2015-11-01-preview",
                "name": "[concat(variables('applicationDiagnosticsStorageAccountName'),parameters('omsWorkspacename'))]",
                "type": "storageinsightconfigs",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]",
                    "[concat('Microsoft.Storage/storageAccounts/', variables('applicationDiagnosticsStorageAccountName'))]"
                ],
                "properties": {
                    "containers": [ ],
                    "tables": [
                        "WADServiceFabric*EventTable",
                        "WADWindowsEventLogsTable",
                        "WADETWEventTable"
                    ],
                    "storageAccount": {
                        "id": "[resourceId('Microsoft.Storage/storageaccounts/', variables('applicationDiagnosticsStorageAccountName'))]",
                        "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('applicationDiagnosticsStorageAccountName')),'2015-06-15').key1]"
                    }
                }
            },
            {
                "apiVersion": "2015-11-01-preview",
                "name": "System",
                "type": "datasources",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
                ],
                "kind": "WindowsEvent",
                "properties": {
                    "eventLogName": "System",
                    "eventTypes": [
                        {
                            "eventType": "Error"
                        },
                        {
                            "eventType": "Warning"
                        },
                        {
                            "eventType": "Information"
                        }
                    ]
                }
            }
        ]
    },
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[variables('omsSolutionName')]",
        "type": "Microsoft.OperationsManagement/solutions",
        "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('OMSWorkspacename'))]"
        ],
        "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
        },
        "plan": {
            "name": "[variables('omsSolutionName')]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('omsSolution'))]",
            "promotionCode": ""
        }
    },
    ```

Hivatkozási célból [itt](https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/d2ffa318581fc23ac7f1b0ab2b52db1a0d7b4ba7/5-VM-Windows-OMS-UnSecure/sfclusteroms.json) található egy (az oktatóanyag első részében használt) mintasablon, amely tartalmazza ezeket a módosításokat. Ezek a módosítások egy Log Analytics-munkaterületet adnak hozzá az erőforráscsoporthoz. A munkaterület úgy lesz konfigurálva, hogy felvegye a Service Fabric-platform eseményeit azon tárolótáblákból, amelyek a [Windows Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md)-ügynökkel lettek konfigurálva. A Log Analytics-ügynök (Microsoft Monitoring Agent) szintén hozzá lett adva a fürt minden csomópontjához virtuálisgép-bővítményként – ez azt jelenti, hogy a fürt méretezése közben az ügynököt minden számítógépen automatikusan konfigurálja és ugyanahhoz a munkaterülethez csatolja a rendszer.

Helyezze üzembe a sablont az új módosításokkal az aktuális fürt frissítéséhez. Ha a művelet befejeződött, az erőforráscsoport log Analytics-erőforrásait kell látnia. Amint a fürt kész, helyezze rajta üzembe a tárolóba helyezett alkalmazást. A következő lépésben a tárolók monitorozását állítjuk be.

## <a name="add-the-container-monitoring-solution-to-your-log-analytics-workspace"></a>A tárolómonitorozási megoldás hozzáadása a Log Analytics-munkaterülethez

A tárolómegoldások beállításához a munkaterületen keresse meg a *Tárolómonitorozási megoldást*, és hozzon létre egy tároló-erőforrást (a Monitoring és felügyelet kategóriában).

![Tárolómegoldások hozzáadása](./media/service-fabric-tutorial-monitoring-wincontainers/containers-solution.png)

Ha a rendszer kéri a *log Analytics munkaterületet*, válassza ki az erőforráscsoporthoz létrehozott munkaterületet, és válassza a **Létrehozás** lehetőséget. Ezzel hozzáadja a *tároló-figyelési megoldást* a munkaterülethez, és elindítja a sablon által központilag telepített log Analytics-ügynököt a Docker-naplók és-statisztikák gyűjtésének megkezdéséhez.

Lépjen vissza az *erőforráscsoporthoz*, ahol meg kell jelennie az újonnan hozzáadott monitorozási megoldásnak. Ha kijelöli, a kezdőlapon meg kell jeleníteni a futtatott tároló-lemezképek számát.

*Vegye figyelembe, hogy a fabrikam-tároló öt példánya futott az oktatóanyag [második részében](service-fabric-host-app-in-a-container.md)*

![Tárolómegoldás kezdőlapja](./media/service-fabric-tutorial-monitoring-wincontainers/solution-landing.png)

A **Container monitor megoldás** kiválasztásával részletesebb irányítópultot tekinthet meg, amely lehetővé teszi több panel görgetését, valamint a lekérdezések futtatását Azure monitor naplókban.

Mivel az ügynök Docker-naplókat vesz fel, alapértelmezés szerint az *stdout* és az *stderr* jelenik meg. Ha vízszintesen görget, a rendszer a tároló képleltára, az állapot, a metrikák és a példaként szolgáló lekérdezéseket fogja látni, amelyek segítségével további hasznos adatokat érhet el.

![Tárolómegoldás irányítópultja](./media/service-fabric-tutorial-monitoring-wincontainers/container-metrics.png)

Ha bármelyik panelre kattint, a megjelenített értéket generáló Kusto-lekérdezésre kerül. Módosítsa a lekérdezést úgy, hogy *\** megtekintse a beszedett különböző naplók összes típusát. Innen lekérdezéseket futtathat, szűrhet a tároló teljesítményére és naplóira, valamint megtekintheti a Service Fabric-platformeseményeket. Az ügynökök is folyamatosan szívveréseket bocsátanak ki minden csomópontból, és ezek a fürt konfigurációjának módosításakor történő ellenőrzésével meggyőződhet arról, hogy a rendszer továbbra is az összes számítógépről gyűjti az adatokat.

![Tároló lekérdezése](./media/service-fabric-tutorial-monitoring-wincontainers/query-sample.png)

## <a name="configure-log-analytics-agent-to-pick-up-performance-counters"></a>A Log Analytics-ügynök konfigurálása teljesítményszámlálók felvételéhez

Az Log Analytics ügynök használatának egy másik előnye, hogy módosítania kell a log Analytics felhasználói felületén keresztül felvenni kívánt teljesítményszámlálókat, nem kell konfigurálnia az Azure Diagnostics-ügynököt, és minden alkalommal el kell végeznie a Resource Manager-sablonokon alapuló frissítést. Ehhez válassza a **OMS munkaterület** elemet a tároló-figyelési (vagy Service Fabric) megoldás kezdőlapján.

Ekkor megnyílik a Log Analytics-munkaterület, ahol megtekintheti a megoldásokat, létrehozhat egyéni irányítópultokat, és konfigurálhatja a Log Analytics-ügynököt. 
* Válassza a **Speciális beállítások** lehetőséget a speciális beállítások menü megnyitásához.
* Válassza a **csatlakoztatott források**  >  **Windows-kiszolgálók** lehetőséget, hogy ellenőrizze, hogy van-e *5 Windows rendszerű számítógép csatlakoztatva*.
* Az   >  új teljesítményszámlálók kereséséhez és hozzáadásához válassza az **adatwindows** -teljesítményszámlálók lehetőséget. Itt megtekintheti az összegyűjteni kívánt teljesítményszámlálók Azure Monitor naplóiból származó javaslatok listáját, valamint az egyéb számlálók keresésének lehetőségét is. Ellenőrizze, hogy **Processzor(_Total)\% A processzor kihasználtsága** és a **Memória(*)\Rendelkezésre álló memória (megabájt)** számlálók össze vannak-e gyűjtve.

Néhány perc múlva **frissítse** a Tárolómonitorozási megoldást, és ezután látnia kell, ahogy érkeznek a *Számítógép teljesítményére* vonatkozó adatok. Ez segít megérteni, hogyan használja a rendszer az erőforrásokat. Ezeket a metrikákat a fürt méretezésére vonatkozó, megfelelő döntések meghozásához is használhatja, vagy annak megerősítéséhez, hogy a fürt a vártnak megfelelően osztja el a terhelést.

*Megjegyzés: A metrikák felhasználásához győződjön meg arról, hogy az idő szűrői megfelelően vannak beállítva.*

![Teljesítményszámlálók 2](./media/service-fabric-tutorial-monitoring-wincontainers/perf-counters2.png)

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Azure Monitor naplók konfigurálása a Service Fabric-fürthöz
> * Log Analytics-munkaterület használata a tárolók és csomópontok naplóinak megtekintéséhez és lekérdezéséhez
> * A Log Analytics-ügynök konfigurálása tároló- és csomópontmetrikák felvételéhez

Most, hogy konfigurálta a tároló alkalmazás figyelését, próbálkozzon az alábbiakkal:

* A Linux-fürtök Azure Monitor naplófájljainak konfigurálása az oktatóanyaghoz hasonló lépéseket követve. Hivatkozzon [erre a sablonra](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeType-Secure-OMS) a módosítások a Resource Manager-sablonban történő elvégzéséhez.
* Konfigurálja Azure Monitor naplókat az [automatizált riasztások](../azure-monitor/alerts/alerts-overview.md) beállításához az észlelés és a diagnosztika támogatásához.
* Tekintse meg a Service Fabric a fürthöz konfigurálható, [ajánlott teljesítményszámlálókat](service-fabric-diagnostics-event-generation-perf.md) tartalmazó listáját.
* Ismerkedjen meg az Azure Monitor naplók részeként kínált [naplóbeli keresési és lekérdezési](../azure-monitor/logs/log-query-overview.md) funkciókkal.
