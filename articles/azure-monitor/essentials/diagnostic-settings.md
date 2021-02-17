---
title: Diagnosztikai beállítások létrehozása a platformnaplók és -metrikák más célhelyekre való küldéséhez
description: Diagnosztikai beállítás használatával Azure Monitor platform metrikáit és naplóit Azure Monitor naplókba, Azure Storage-ba vagy Azure-ba Event Hubs.
author: bwren
ms.author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 02/08/2021
ms.subservice: logs
ms.openlocfilehash: c7e18250a6f11504aa29d8df190da974499470ab
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100609046"
---
# <a name="create-diagnostic-settings-to-send-platform-logs-and-metrics-to-different-destinations"></a>Diagnosztikai beállítások létrehozása a platformnaplók és -metrikák más célhelyekre való küldéséhez
Az Azure [platform-naplói](../platform/platform-logs-overview.md) , beleértve az Azure-tevékenység naplóját és az erőforrás-naplókat, részletes diagnosztikai és naplózási információkat biztosítanak az Azure-erőforrásokhoz és az Azure-platformtól függenek. A [platform metrikáit](../platform/data-platform-metrics.md) a rendszer alapértelmezés szerint gyűjti, és általában a Azure monitor metrikai adatbázisban tárolja. Ez a cikk a diagnosztikai beállítások létrehozásával és konfigurálásával kapcsolatos részletes információkat tartalmaz a platform metrikáinak és a platformok naplóinak különböző célhelyekre küldéséhez.

> [!IMPORTANT]
> Mielőtt diagnosztikai beállítást hozna létre a műveletnapló számára, először le kell tiltania az örökölt konfigurációkat. További részletek: [örökölt gyűjteményi módszerek](../essentials/activity-log.md#legacy-collection-methods) .

Minden Azure-erőforráshoz saját diagnosztikai beállítás szükséges, amely a következő feltételeket határozza meg:

- A beállításban meghatározott célhelyekre továbbított naplók és metrikai adatok kategóriái. A rendelkezésre álló kategóriák a különböző erőforrástípusok esetében eltérőek lesznek.
- Egy vagy több célhely a naplók elküldéséhez. Az aktuális célhelyek közé tartozik Log Analytics munkaterület, Event Hubs és az Azure Storage.

Egyetlen diagnosztikai beállítás a célhelyek közül legfeljebb egyet tud meghatározni. Ha szeretne adatokat küldeni egy adott célhelyhez (például két különböző Log Analytics-munkaterületre), akkor hozzon létre több beállítást. Minden erőforrás legfeljebb 5 diagnosztikai beállítással rendelkezhet.

Az alábbi videó végigvezeti az útválasztási platform naplófájljainak a diagnosztikai beállításokkal való használatával.
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4AvVO]

> [!NOTE]
> A rendszer automatikusan elküldi a [platform metrikáit](../platform/metrics-supported.md) [Azure monitor mérőszámok](../platform/data-platform-metrics.md)számára. A diagnosztikai beállítások használatával bizonyos Azure-szolgáltatások metrikái küldhetők Azure Monitor naplókba más megfigyelési adatokkal való elemzéshez, bizonyos korlátozásokkal rendelkező [naplók](../log-query/log-query-overview.md) használatával. 
>  
>  
> A többdimenziós metrikák diagnosztikai beállításokon keresztül történő küldése jelenleg nem támogatott. A dimenziókkal rendelkező metrikák egybesimított, egydimenziós metrikákként vannak exportálva, összesített dimenzióértékekkel. *Például*: a Blockchain "IOReadBytes" mérőszáma megvizsgálható és a csomópontok szintjén is felvehető. A diagnosztikai beállításokon keresztüli exportáláskor azonban az exportált metrika az összes csomópont olvasási bájtjaiként jelenik meg. Emellett a belső korlátozások miatt nem minden metrika exportálható Azure Monitor naplókba/Log Analyticsba. További információkért tekintse meg az [exportálható mérőszámok listáját](../platform/metrics-supported-export-diagnostic-settings.md). 
>  
>  
> Az egyes mérőszámokra vonatkozó korlátozások megszerzéséhez javasoljuk, hogy manuálisan kibontsa azokat a [metrikák használatával REST API](/rest/api/monitor/metrics/list) és importálja őket Azure monitor naplókba a [Azure monitor adatgyűjtő API](../platform/data-collector-api.md)használatával.  


## <a name="destinations"></a>Célhelyek
A platform naplói és metrikái a következő táblázatban található célhelyekre küldhetők. 

| Cél | Description |
|:---|:---|
| [Log Analytics munkaterület](../platform/design-logs-deployment.md) | A naplók és mérőszámok Log Analytics-munkaterületre való küldése lehetővé teszi, hogy a Azure Monitor által gyűjtött más figyelési adatokkal elemezze azokat a hatékony naplózási lekérdezésekkel, valamint más Azure Monitor funkciók, például riasztások és vizualizációk kihasználása érdekében. |
| [Event Hubs](../../event-hubs/index.yml) | A naplók és metrikák küldésének Event Hubs lehetővé teszi az adatok továbbítását külső rendszerekre, például harmadik féltől származó SIEM-re és más log Analytics-megoldásokra.  |
| [Azure Storage-fiók](../../storage/blobs/index.yml) | A naplók és mérőszámok Azure Storage-fiókba való archiválása hasznos lehet a naplózás, a statikus elemzés vagy a biztonsági mentés során. Azure Monitor naplókhoz és egy Log Analytics munkaterülethez képest az Azure Storage kevésbé költséges, és a naplók határozatlan ideig tarthatók.  |


### <a name="destination-requirements"></a>Célhelyre vonatkozó követelmények

A diagnosztikai beállítások létrehozása előtt létre kell hozni a diagnosztikai beállítás célhelyeit. A célhelynek nem kell ugyanabban az előfizetésben lennie, mint az erőforrás-küldési naplók, feltéve, hogy a beállítást konfiguráló felhasználó mindkét előfizetéshez megfelelő Azure RBAC-hozzáféréssel rendelkezik. Az alábbi táblázat az egyes rendeltetési helyekre vonatkozó egyedi követelményeket tartalmazza, beleértve a regionális korlátozásokat is.

| Cél | Követelmények |
|:---|:---|
| Log Analytics-munkaterület | A munkaterületnek nem kell ugyanabban a régióban lennie, mint a figyelt erőforrás.|
| Event Hubs | A névtérhez tartozó megosztott hozzáférési házirend határozza meg az adatfolyam-mechanizmushoz tartozó engedélyeket. A Event Hubs való folyamatos átvitelhez a kezelés, a Küldés és a figyelés engedélyek szükségesek. Ha szeretné frissíteni a diagnosztikai beállítást, hogy a folyamatos átvitelt is tartalmazza, rendelkeznie kell a ListKey engedéllyel az adott Event Hubs engedélyezési szabályhoz.<br><br>Az Event hub-névtérnek ugyanabban a régióban kell lennie, mint a figyelt erőforrás, ha az erőforrás regionális. |
| Azure Storage-fiók | Ne használjon olyan meglévő Storage-fiókot, amely más, nem monitorozási adattárolási információkkal rendelkezik, így jobban szabályozhatja az adathozzáférést. Ha a tevékenység naplóját és az erőforrás-naplókat együtt archiválja, akkor dönthet úgy, hogy ugyanazt a Storage-fiókot használja, hogy az összes figyelési adattal egy központi helyen maradjon.<br><br>Ha nem módosítható tárolóba szeretné elküldeni az adattárolást, állítsa be a Storage-fiók megváltoztathatatlan házirendjét a [blob Storage módosíthatatlansági szabályzatok beállítása és kezelése](../../storage/blobs/storage-blob-immutability-policies-manage.md)című témakörben leírtak szerint. A cikkben ismertetett lépéseket kell követnie, beleértve a védett hozzáfűzési Blobok írásának engedélyezését is.<br><br>A Storage-fióknak ugyanabban a régióban kell lennie, mint a figyelt erőforrás, ha az erőforrás regionális. |

> [!NOTE]
> Az Azure Data Lake Storage Gen2-fiókok jelenleg nem támogatottak a diagnosztikai beállítások célhelyeként, még akkor sem, ha az Azure Portalon esetleg érvényes lehetőségként vannak felsorolva.

> [!NOTE]
> Azure Monitor (diagnosztikai beállítások) nem férhetnek hozzá Event Hubs erőforrásokhoz, ha a virtuális hálózatok engedélyezve vannak. Engedélyeznie kell a megbízható Microsoft-szolgáltatások számára, hogy megkerüljék ezt a tűzfal-beállítást az Event hub-ban, így Azure Monitor (diagnosztikai beállítások) szolgáltatás hozzáférést kap a Event Hubs erőforrásaihoz. 


## <a name="create-in-azure-portal"></a>Létrehozás az Azure Portalon

A Azure Portal diagnosztikai beállításait a Azure Monitor menüből vagy az erőforrás menüjéből is konfigurálhatja.

1. A Azure Portal diagnosztikai beállításainak konfigurálása az erőforrástól függ.

   - Egyetlen erőforrás esetén kattintson a **diagnosztikai beállítások** elemre az erőforrás menüjében a **figyelés** lehetőség alatt.

        ![Képernyőkép: Azure Portal erőforrás menüjének figyelés szakasza, Kiemelt diagnosztikai beállításokkal.](media/diagnostic-settings/menu-resource.png)

   - Egy vagy több erőforrás esetében kattintson a Azure Monitor menü **Beállítások** területén található **diagnosztikai beállítások** elemre, majd kattintson az erőforrásra.

        ![Képernyőkép: a Azure Monitor menü Beállítások szakasza, a diagnosztikai beállítások kiemelten.](media/diagnostic-settings/menu-monitor.png)

   - A tevékenység naplójában kattintson a **Azure monitor** menüben a **tevékenység napló** elemre, majd a **diagnosztikai beállítások** lehetőségre. Győződjön meg arról, hogy letiltja a tevékenység naplójának örökölt konfigurációját. További részletek: a [meglévő beállítások letiltása](./activity-log.md#legacy-collection-methods) .

        ![Képernyőfelvétel a Azure Monitor menüjéről és a tevékenységek naplójának kiválasztásáról és a diagnosztikai beállításokról a Monitor-Activity log menüsávban.](media/diagnostic-settings/menu-activity-log.png)

2. Ha nem találhatók beállítások a kiválasztott erőforráson, a rendszer kéri, hogy hozzon létre egy beállítást. Kattintson a **diagnosztikai beállítás hozzáadása** elemre.

   ![Diagnosztikai beállítás hozzáadása – nincsenek meglévő beállítások](media/diagnostic-settings/add-setting.png)

   Ha vannak meglévő beállítások az erőforráson, a már konfigurált beállítások listája jelenik meg. Kattintson a **diagnosztikai beállítás hozzáadása** lehetőségre egy új beállítás hozzáadásához vagy egy meglévő **szerkesztési beállítás** megadásához. Az egyes beállítások nem lehetnek többek között a célhelyek közül.

   ![Diagnosztikai beállítások hozzáadása – meglévő beállítások](media/diagnostic-settings/edit-setting.png)

3. Adja meg a beállítás nevét, ha még nem rendelkezik ilyennel.

    ![Diagnosztikai beállítás hozzáadása](media/diagnostic-settings/setting-new-blank.png)

4. **Kategória részletei (mit kell átirányítani)** – jelölje be a jelölőnégyzetet minden olyan adatkategória esetében, amelyet később szeretne elküldeni a célhelyekre. A kategóriák listája az egyes Azure-szolgáltatásokra változik.

     - A **AllMetrics** az erőforrás platformjának mérőszámait az Azure naplók tárolójába irányítja, de a napló formájában. Ezeket a mérőszámokat általában csak a Azure Monitor metrikák idősorozat-adatbázisába küldik. Küldje el őket a Azure Monitor naplók tárolójába (amely Log Analyticson keresztül kereshető) segíti a különböző naplókon belüli keresésre szolgáló lekérdezésekben való integrálását. Lehetséges, hogy ez a beállítás nem érhető el az összes erőforrástípus esetében. Ha ez támogatott, [Azure monitor támogatott mérőszámok](../platform/metrics-supported.md) felsorolják, hogy milyen mérőszámokat gyűjt a rendszer a milyen típusú erőforrásokhoz.

       > [!NOTE]
       > Tekintse meg a jelen cikk korábbi részében Azure Monitor naplókra vonatkozó útválasztási mérőszámok korlátozását.  


     - A **naplók** az erőforrástípus típusától függően az elérhető különböző kategóriákat listázza. Tekintse át a célhelyre átirányítani kívánt kategóriákat.

5. **Célhely részletei** – jelölje be az egyes célhelyek jelölőnégyzetét. Ha bejelöli az egyes mezők jelölőnégyzetét, úgy tűnik, hogy további információkat adhat hozzá.

      ![Küldés Log Analytics vagy Event Hubs](media/diagnostic-settings/send-to-log-analytics-event-hubs.png)

    1. **Log Analytics** – adja meg az előfizetést és a munkaterületet.  Ha nem rendelkezik munkaterülettel, a [továbblépés előtt létre kell hoznia egyet](../learn/quick-create-workspace.md).

    1. **Event hubok** – Itt adhatja meg a következő feltételeket:
       - Az az előfizetés, amely az Event hub részét képezi
       - Az Event hub-névtér – ha még nem rendelkezik ilyennel, [létre kell hoznia egyet](../../event-hubs/event-hubs-create.md)
       - Az Event hub neve (nem kötelező) az összes érték küldéséhez. Ha nem ad meg nevet, a rendszer minden egyes naplózási kategóriához létrehoz egy Event hub-t. Ha több kategóriát küld, érdemes megadnia egy nevet a létrehozott Event hubok számának korlátozásához. A részletekért tekintse meg az [Azure Event Hubs kvótáit és korlátozásait](../../event-hubs/event-hubs-quotas.md) .
       - Az Event hub szabályzata (nem kötelező) a szabályzat meghatározza a folyamatos átviteli mechanizmushoz szükséges engedélyeket. További információ: [Event-hubok-features](../../event-hubs/event-hubs-features.md#publisher-policy).

    1. **Storage** – válassza ki az előfizetést, a Storage-fiókot és a megőrzési szabályzatot.

        ![Küldés a tárolóba](media/diagnostic-settings/storage-settings-new.png)

        > [!TIP]
        > Érdemes lehet 0-ra beállítani az adatmegőrzési szabályt, és manuálisan törölni az adatait a tárterületről egy ütemezett feladatokkal, hogy elkerülje a jövőben felhasználható félreértéseket.
        >
        > Először is, ha a Storage-ot archiválásra használja, az adatait általában több mint 365 napig érdemes megtekinteni. Másodszor, ha a 0 értéknél nagyobb adatmegőrzési szabályt választ, a lejárati dátum a tároláskor a naplókhoz van csatolva. A naplók dátuma a tárolás után nem módosítható.
        >
        > Ha például a *WorkflowRuntime* 180 napra állítja be az adatmegőrzési szabályzatot, majd 24 órával később beállítja azt 365 napra, akkor az első 24 órában tárolt naplók automatikusan törlődnek a 180 nap után, míg az adott típusú összes további napló automatikusan törlődik a 365 nap után. Az adatmegőrzési szabályzat későbbi módosítása nem teszi elérhetővé a naplók első 24 óráját 365 napig.

6. Kattintson a **Mentés** gombra.

Néhány pillanat elteltével megjelenik az új beállítás az erőforráshoz tartozó beállítások listájában, és a rendszer a naplókat a megadott célhelyre továbbítja az új esemény-adatforrások létrehozásakor. Egy esemény kibocsátásakor akár 15 percet is igénybe vehet, amikor megjelenik [egy log Analytics munkaterületen](../platform/data-ingestion-time.md).

## <a name="create-using-powershell"></a>Létrehozás a PowerShell használatával

A [set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) parancsmaggal hozzon létre diagnosztikai beállítást [Azure PowerShell](../samples/powershell-samples.md)használatával. A parancsmag dokumentációját a paraméterek leírását ismertető cikkben találja.

> [!IMPORTANT]
> Ez a metódus nem használható az Azure-beli tevékenység naplójában. Ehelyett [hozzon létre diagnosztikai beállítást a Azure monitor Resource Manager-sablonnal](../samples/resource-manager-diagnostic-settings.md) egy Resource Manager-sablon létrehozásához és a PowerShell használatával történő telepítéséhez.

A következő példa egy PowerShell-parancsmagot tartalmaz a diagnosztikai beállítások mind a három célhely használatával történő létrehozásához.

```powershell
Set-AzDiagnosticSetting -Name KeyVault-Diagnostics -ResourceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault -Category AuditEvent -MetricCategory AllMetrics -Enabled $true -StorageAccountId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount -WorkspaceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace  -EventHubAuthorizationRuleId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```

## <a name="create-using-azure-cli"></a>Létrehozás az Azure CLI használatával

Az az [monitor diagnosztikai-Settings Create](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) paranccsal hozzon létre egy diagnosztikai beállítást az [Azure CLI](/cli/azure/monitor)használatával. A paraméterek leírását a parancs dokumentációjában találja.

> [!IMPORTANT]
> Ez a metódus nem használható az Azure-beli tevékenység naplójában. Ehelyett a [Azure monitor diagnosztikai beállítás használatával Resource Manager-sablonnal](../samples/resource-manager-diagnostic-settings.md) hozzon létre egy Resource Manager-sablont, és telepítse azt a CLI-vel.

A következő példa egy CLI-parancsot mutat be a diagnosztikai beállítások mind a három célhely használatával történő létrehozásához.

```azurecli
az monitor diagnostic-settings create  \
--name KeyVault-Diagnostics \
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault \
--logs    '[{"category": "AuditEvent","enabled": true}]' \
--metrics '[{"category": "AllMetrics","enabled": true}]' \
--storage-account /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount \
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace \
--event-hub-rule /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```

## <a name="create-using-resource-manager-template"></a>Létrehozás Resource Manager-sablonnal
A diagnosztikai beállítások a Resource Manager-sablonnal történő létrehozásához vagy frissítéséhez tekintse meg a [Resource Manager-sablonok mintákat a Azure monitor diagnosztikai beállításaihoz](../samples/resource-manager-diagnostic-settings.md) .

## <a name="create-using-rest-api"></a>Létrehozás a REST API használatával
A diagnosztikai beállítások a [Azure Monitor REST API](/rest/api/monitor/)használatával történő létrehozásához vagy frissítéséhez tekintse meg a [diagnosztikai beállításokat](/rest/api/monitor/diagnosticsettings) .

## <a name="create-using-azure-policy"></a>Létrehozás a Azure Policy használatával
Mivel minden egyes Azure-erőforráshoz létre kell hozni egy diagnosztikai beállítást, Azure Policy használatával automatikusan létrehozhat egy diagnosztikai beállítást, mivel minden erőforrás létrejön. A részletekért lásd: [Azure monitor üzembe helyezése méretezéssel Azure Policy](../deploy-scale.md) .

## <a name="metric-category-is-not-supported-error"></a>A metrika kategóriája nem támogatott hiba
Diagnosztikai beállítások telepítésekor a következő hibaüzenet jelenik meg:

   A "metrika kategóriája"*XXXX*"nem támogatott"

Például: 

   A "metrika kategóriája" ActionsFailed "nem támogatott"

az üzemelő példány sikeres volt. 

A probléma egy Resource Manager-sablon, a diagnosztikai beállítások REST API, az Azure CLI vagy a Azure PowerShell használatakor fordul elő. A Azure Portal használatával létrehozott diagnosztikai beállításokat a rendszer nem érinti, mert csak a támogatott kategóriájú nevek jelennek meg.

Ezt a problémát az alapul szolgáló API legutóbbi változása okozza. A "AllMetrics"-től eltérő metrikus kategóriák nem támogatottak, és soha nem voltak kivételek néhány nagyon konkrét Azure-szolgáltatás esetében. A múltban a diagnosztikai beállítások telepítésekor a rendszer figyelmen kívül hagyja a többi kategória nevét. A Azure Monitor háttér egyszerűen átirányítja ezeket a kategóriákat a "AllMetrics" értékre.  Február 2021-án a háttér frissítése megtörtént, hogy pontosan megerősítse a megadott metrikai kategóriát. Ez a változás bizonyos központi telepítések meghibásodását okozta.

Ha ezt a hibaüzenetet kapja, frissítse az üzemelő példányokat, hogy a probléma megoldásához cserélje le a metrikus kategóriájú neveket a "AllMetrics" értékre. Ha a központi telepítés korábban több kategóriát adott hozzá, csak egyet kell megőrizni a "AllMetrics" hivatkozással. Ha továbbra is fennáll a probléma, vegye fel a kapcsolatot az Azure ügyfélszolgálatával a Azure Portal. 



## <a name="next-steps"></a>Következő lépések

- [További információ az Azure platform naplóiról](../platform/platform-logs-overview.md)
