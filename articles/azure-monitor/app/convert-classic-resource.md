---
title: Azure Monitor Application Insights klasszikus erőforrás migrálása munkaterület-alapú erőforrásba | Microsoft Docs
description: Ismerje meg a Azure Monitor Application Insights klasszikus erőforrás új munkaterület-alapú modellre való frissítéséhez szükséges lépéseket.
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: 5791abe33dee2e62aadb00ae1024338e1e44a900
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100584258"
---
# <a name="migrate-to-workspace-based-application-insights-resources"></a>Áttelepítés munkaterületen alapuló Application Insights erőforrásokra

Ez az útmutató végigvezeti a klasszikus Application Insights erőforrásnak a munkaterület-alapú erőforrásba való áttelepítésének folyamatán. A munkaterület-alapú erőforrások a Application Insights és Log Analytics közötti teljes integrációt támogatják. A munkaterület-alapú erőforrások Application Insights telemetria küldenek egy közös Log Analytics munkaterületre, amely lehetővé teszi [a Azure monitor legújabb funkcióinak](#new-capabilities) elérését, miközben az alkalmazások, az infrastruktúra és a platformok naplóit egyetlen konszolidált helyen tartja.

A munkaterület-alapú erőforrások lehetővé teszik a közös Azure szerepköralapú hozzáférés-vezérlést (Azure RBAC) az erőforrások között, és szükségtelenné teszi az alkalmazások közötti vagy munkaterület-lekérdezések szükségességét.

**A munkaterület-alapú erőforrások jelenleg minden kereskedelmi régióban és az Egyesült Államok kormányzati szerveiben érhetők el**

## <a name="new-capabilities"></a>Új képességek

A munkaterület-alapú Application Insights lehetővé teszi Azure Monitor és Log Analytics legújabb képességeinek kihasználását, beleértve a következőket:

* Az [ügyfél által felügyelt kulcsok (CMK)](../logs/customer-managed-keys.md) lehetővé teszi az adatok titkosítását olyan titkosítási kulcsokkal, amelyekhez csak Ön férhet hozzá.
* Az [Azure Private link](../logs/private-link-security.md) lehetővé teszi, hogy a privát végpontok használatával biztonságosan összekapcsolja az Azure Pásti-szolgáltatásokat a virtuális hálózattal.
* A [Profiler és a Snapshot Debugger saját tárterületének (BYOS)](./profiler-bring-your-own-storage.md) teljes körű vezérlést biztosít a titkosítást a REST-alapú házirend, az élettartam-kezelési házirend és a Application Insights Profiler és Snapshot Debuggerhoz kapcsolódó összes adat hálózati hozzáférése felett. 
* A [kapacitás-foglalási szintek](../logs/manage-cost-storage.md#pricing-model) lehetővé teszik, hogy akár 25%-ot is mentsen az utólagos elszámolású díjszabáshoz képest. 
* Gyorsabb adatfeldolgozás Log Analytics folyamatos átvitelsel.

## <a name="migration-process"></a>Migrálási folyamat

Ha munkaterületen alapuló erőforrásra végez áttelepítést, a rendszer a klasszikus erőforrás tárterületéről nem helyez át adatátvitelt az új munkaterület-alapú tárolóba. Az áttelepítés kiválasztása Ehelyett megváltoztatja azt a helyet, ahol az új adatai Log Analytics munkaterületre íródnak, miközben megőrizheti a klasszikus erőforrások adataihoz való hozzáférést. 

A klasszikus erőforrás-adatok továbbra is megmaradnak, és a klasszikus Application Insights erőforrás megőrzési beállításai vonatkoznak rájuk. Az áttelepítés utáni összes új adatot a társított Log Analytics munkaterület [megőrzési beállításai](../logs/manage-cost-storage.md#change-the-data-retention-period) szabályozzák, amely más [adatmegőrzési beállításokat is támogat adattípus szerint](../logs/manage-cost-storage.md#retention-by-data-type).
Az áttelepítési folyamat **állandó, és nem vonható vissza**. Miután áttelepítette az erőforrást a munkaterület-alapú Application Insightsre, mindig munkaterület-alapú erőforrás lesz. A Migrálás után azonban a kívánt gyakorisággal módosíthatja a cél munkaterületet. 

> [!NOTE]
> A munkaterületen alapuló Application Insights erőforrások adatfeldolgozása és megőrzése a [log Analytics munkaterületen](../logs/manage-cost-storage.md) történik, ahol az adatok találhatók. Ha 90 napnál nagyobb adatmegőrzést jelölt ki a klasszikus Application Insights erőforrásba való áttelepítés előtt betöltött adatokon, az adatmegőrzés továbbra is a Application Insights erőforráson keresztül történik. [További]( ./pricing.md#workspace-based-application-insights) információ a munkaterület-alapú Application Insights erőforrások számlázásáról.

Ha nem kell áttelepítenie egy meglévő erőforrást, és ehelyett új munkaterület-alapú Application Insights erőforrást szeretne létrehozni, használja a [munkaterület-alapú erőforrás-létrehozási útmutatót](create-workspace-resource.md).

## <a name="pre-requisites"></a>Előfeltételek 

- Egy Log Analytics munkaterület, amelynek hozzáférés-vezérlési módja a **`use resource or workspace permissions`** beállításra van beállítva. 

    - A munkaterület-alapú Application Insights erőforrások nem kompatibilisek a dedikált beállításhoz beállított munkaterületekkel **`workspace based permissions`** . A Log Analytics munkaterület-hozzáférés-vezérléssel kapcsolatos további tudnivalókért tekintse meg a [hozzáférés-vezérlési mód konfigurálása című log Analytics](../logs/manage-access.md#configure-access-control-mode)

    - Ha még nem rendelkezik meglévő Log Analytics munkaterülettel, [tekintse meg a log Analytics munkaterület-létrehozási dokumentációt](../logs/quick-create-workspace.md).
    
- A folyamatos exportálás nem támogatott a munkaterület-alapú erőforrások esetében, és le kell tiltani.
Az áttelepítés befejezése után a [diagnosztikai beállításokkal](../essentials/diagnostic-settings.md) konfigurálhatja az adatarchiválást egy Storage-fiókba vagy az Azure Event hub szolgáltatásba való folyamatos átvitelre.  

- A jelenlegi adatmegőrzési beállításokat az **általános**  >  **használat és a becsült költségek**  >  **adatmegőrzés** az log Analytics munkaterületen című szakaszban találja. Ez a beállítás befolyásolja, hogy mennyi ideig tárolja a rendszer az új betöltött adatot, miután áttelepítette Application Insights erőforrását. Ha jelenleg az alapértelmezett 90 napnál hosszabb ideig tárolja Application Insights adatait, és meg szeretné őrizni ezt a nagyobb megőrzési időt, előfordulhat, hogy módosítania kell a munkaterület adatmegőrzési beállításait.

## <a name="migrate-your-resource"></a>Az erőforrás migrálása

Ez a szakasz végigvezeti a klasszikus Application Insights erőforrásnak az új munkaterület-alapú erőforrástípus való áttelepítésének folyamatán.

1. A Application Insights erőforrásból válassza ki a **Tulajdonságok** lehetőséget a **configure (Konfigurálás** ) fejlécben a bal oldali menüsorban.

    ![Piros mezőben Kiemelt tulajdonságok](./media/convert-classic-resource/properties.png)

2. Válassza az **`Migrate to Workspace-based`** lehetőséget.
    
     ![Erőforrás átmigrálása gomb](./media/convert-classic-resource/migrate.png)

3. Válassza ki azt a Log Analytics munkaterületet, ahol az összes jövőbeli bevitt Application Insights telemetria tárolni szeretné.

     ![Áttelepítési varázsló KEZELŐFELÜLETe a Targe-munkaterület kiválasztásához](./media/convert-classic-resource/migration.png)
    

Az erőforrás áttelepítését követően a kapcsolódó munkaterület-információk az **Áttekintés** ablaktáblán jelennek meg:

![Munkaterület neve](./media/create-workspace-resource/workspace-name.png)

A kék hivatkozás szövegére kattintva megtekintheti a társított Log Analytics munkaterületet, ahol kihasználhatja az új, egyesített munkaterület-lekérdezési környezetet.

## <a name="understanding-log-queries"></a>A naplózási lekérdezések ismertetése

Továbbra is visszamenőleges kompatibilitást biztosítunk a Application Insights klasszikus erőforrás-lekérdezésekhez, a munkafüzetekhez és a napló alapú riasztásokhoz a Application Insights felhasználói felületén belül. 

Ha lekérdezéseket szeretne írni az [Új munkaterület-alapú tábla-struktúra/séma alapján](apm-tables.md), először navigáljon a log Analytics munkaterületre. 

Ha közvetlenül a munkaterületen lévő Log Analytics felhasználói felületről kérdez le lekérdezést, csak az áttelepítés után betöltött adatok jelennek meg. Ha azt szeretné, hogy a klasszikus Application Insights adatok és a Migrálás után betöltött új adatok egy egyesített lekérdezési környezetben legyenek, használja a naplók (Analytics) lekérdezési nézetét az áttelepített Application Insights erőforráson belül.

## <a name="programmatic-resource-migration"></a>Programozott erőforrás-áttelepítés

### <a name="azure-cli"></a>Azure CLI

Az előzetes verzió Application Insights Azure CLI-parancsokhoz való hozzáféréshez először futtatnia kell a következőt:

```azurecli
 az extension add -n application-insights
```

Ha nem futtatja a `az extension add` parancsot, a következő hibaüzenet jelenik meg: `az : ERROR: az monitor: 'app-insights' is not in the 'az monitor' command group. See 'az monitor --help'.`

Most futtathatja a következőt a Application Insights erőforrás létrehozásához:

```azurecli
az monitor app-insights component update --app
                                         --resource-group
                                         [--ingestion-access {Disabled, Enabled}]
                                         [--kind]
                                         [--query-access {Disabled, Enabled}]
                                         [--retention-time]
                                         [--workspace]
```

#### <a name="example"></a>Példa

```azurecli
az monitor app-insights component update --app your-app-insights-resource-name -g your_resource_group --workspace "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/test1234/providers/microsoft.operationalinsights/workspaces/test1234555"
```

A parancshoz tartozó teljes Azure CLI-dokumentációért lásd az [Azure CLI dokumentációját](/cli/azure/ext/application-insights/monitor/app-insights/component#ext-application-insights-az-monitor-app-insights-component-update).

### <a name="azure-powershell"></a>Azure PowerShell

A `Update-AzApplicationInsights` PowerShell-parancs jelenleg nem támogatja a klasszikus Application Insights erőforrásoknak a munkaterület-alapú áttelepítését. Ha munkaterület-alapú erőforrást szeretne létrehozni a PowerShell-lel, használja az alábbi Azure Resource Manager-sablonokat, és telepítse a PowerShell használatával.

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

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="access-mode"></a>Hozzáférési mód

**Hibaüzenet:** *a kiválasztott munkaterület munkaterület-alapú hozzáférési móddal van konfigurálva. Előfordulhat, hogy egyes APM-funkciók befolyásolhatják a funkciót. Válasszon másik munkaterületet, vagy engedélyezze az erőforrás-alapú hozzáférést a munkaterület beállításai között. Ezt a hibát felülbírálhatja a parancssori felület használatával.* 

Ahhoz, hogy a munkaterület-alapú Application Insights erőforrás megfelelően működjön, módosítania kell a cél Log Analytics munkaterület hozzáférés-vezérlési módját az **erőforrás vagy a munkaterület engedélyeinek** beállítására. Ez a beállítás a log Analytics munkaterület felhasználói felületén található a **Tulajdonságok**  >  **hozzáférés-vezérlési módban**. Részletes útmutatásért olvassa el az [log Analytics hozzáférés-vezérlési mód konfigurálása című útmutatót](../logs/manage-access.md#configure-access-control-mode). Ha a hozzáférés-vezérlési mód a kizárólagos **munkaterülethez szükséges engedélyek** beállításra van beállítva, a portál áttelepítési felületén keresztüli áttelepítés továbbra is blokkolva marad.

Ha a hozzáférés-vezérlési mód nem módosítható biztonsági okokból az aktuális célként megadott munkaterületen, javasoljuk, hogy hozzon létre egy új Log Analytics munkaterületet az áttelepítéshez. 

### <a name="continuous-export"></a>Folyamatos exportálás

**Hibaüzenet:** *a folytatás előtt le kell tiltani a folyamatos exportálást. Az áttelepítés után az exportáláshoz használja a diagnosztikai beállításokat.* 

A örökölt folyamatos exportálás funkció nem támogatott a munkaterület-alapú erőforrások esetében. Az áttelepítés előtt le kell tiltania a folyamatos exportálást.

1. A Application Insights erőforrás nézetében a configure ( **Konfigurálás** ) fejléc alatt válassza a **folyamatos exportálás** lehetőséget.

    ![Folyamatos exportálás menüpont](./media/convert-classic-resource/continuous-export.png)

2. Válassza a **Letiltás** lehetőséget.

    ![Folyamatos exportálás letiltása gomb](./media/convert-classic-resource/disable.png)

- Miután kiválasztotta a Letiltás lehetőséget, visszatérhet az áttelepítési felhasználói felületre. Ha a folyamatos exportálás szerkesztése lapon rákérdez, hogy a beállítások nem lesznek-e mentve, válassza az OK lehetőséget a kéréshez, mivel az nem vonatkozik a folyamatos exportálás letiltására/engedélyezésére.

- Miután sikeresen áttelepítette Application Insights erőforrását a munkaterület-alapúra, diagnosztikai beállításokkal lecserélheti a folyamatos exportáláshoz használt funkciókat. Válassza a **diagnosztikai beállítások**  >  **Hozzáadás diagnosztikai beállítás** lehetőséget a Application Insights erőforráson belül. Kiválaszthatja az összes táblát vagy a táblák egy részhalmazát, hogy archiválja a Storage-fiókba, vagy egy Azure Event hub-ba továbbítson. A diagnosztikai beállításokkal kapcsolatos részletes útmutatásért tekintse meg a [Azure monitor diagnosztikai beállítások útmutatását](../essentials/diagnostic-settings.md).

### <a name="retention-settings"></a>Adatmegőrzési beállítások

**Figyelmeztető üzenet:** *a testreszabott Application Insights adatmegőrzési beállítások nem lesznek érvényesek a munkaterületre küldött adatokra. Ezt külön kell konfigurálnia.*

Az áttelepítés előtt nem kell módosítania az összes módosítást, de ez az üzenet arra figyelmezteti, hogy az aktuális Application Insights megőrzési beállításai nem az alapértelmezett 90 napos megőrzési időtartamra vannak beállítva. Ez a figyelmeztető üzenet azt jelenti, hogy módosítani szeretné a Log Analytics munkaterület megőrzési beállításait a Migrálás és az új adatok betöltésének megkezdése előtt. 

A log Analytics aktuális megőrzési beállításait az **általános** használat szakaszban tekintheti meg,  >  a **becsült költségek**  >   pedig az log Analytics felhasználói felületen. Ez a beállítás befolyásolja, hogy mennyi ideig tárolja a rendszer az új betöltött adatot, miután áttelepítette Application Insights erőforrását.

## <a name="next-steps"></a>Következő lépések

* [Metrikák böngészése](../essentials/metrics-charts.md)
* [Analytics-lekérdezések](../logs/log-query-overview.md)
