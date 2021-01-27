---
title: Integrálás Azure Monitor naplókkal
description: Ez a cikk azt ismerteti, hogyan kell elküldeni a kívánt állapot-konfiguráció jelentési adatait Azure Automation állapotból a naplók Azure Monitor.
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 34bbf34d53c44dcef7b8e128a93ee64201423c3e
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98897037"
---
# <a name="integrate-with-azure-monitor-logs"></a>Integrálás Azure Monitor naplókkal

Azure Automation állapot-konfiguráció 30 napig őrzi meg a csomópontok állapotát. Ha inkább hosszabb ideig szeretné megőrizni ezeket az adatait, elküldheti a csomóponti állapot adatait a Log Analytics munkaterületre. A megfelelőségi állapot látható a Azure Portal vagy a PowerShell-lel, a csomópontok és a csomópont-konfigurációk egyes DSC-erőforrásai esetében. 

Azure Monitor naplók nagyobb működési láthatóságot biztosítanak az Automation-állapot konfigurációs adataihoz, és gyorsabban segíthetnek az incidensek kezelésében. Azure Monitor naplók segítségével:

- A felügyelt csomópontok és az egyes erőforrások megfelelőségi információinak beolvasása.
- A megfelelőségi állapot alapján indítson el egy e-mailt vagy riasztást.
- Speciális lekérdezések írása a felügyelt csomópontok között.
- A megfelelőségi állapot korrelációja az Automation-fiókok között.
- Egyéni nézeteket és keresési lekérdezéseket használhat a runbook eredményeinek, a runbook-feladatok állapotának, valamint az egyéb kapcsolódó kulcsfontosságú mutatók vagy mérőszámok megjelenítéséhez.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Előfeltételek

Az Automation-állapotra vonatkozó konfigurációs jelentések Azure Monitor naplókba való küldésének megkezdéséhez a következőkre lesz szüksége:

- A [Azure PowerShell](/powershell/azure/) november 2016-es vagy újabb kiadása (v 2.3.0).
- Egy Azure Automation-fiókra. További információ: [Bevezetés a Azure Automationba](automation-intro.md).
- Egy Log Analytics munkaterületen egy Automation & Control Service-ajánlattal. További információ: [Bevezetés a log Analytics használatába Azure monitor](../azure-monitor/log-query/log-analytics-tutorial.md).
- Legalább egy Azure Automation állapot-konfigurációs csomópont. További információ: [bevezetési gépek felügyeletre Azure Automation állapot-konfiguráció alapján](automation-dsc-onboarding.md).
- A [xDscDiagnostics](https://www.powershellgallery.com/packages/xDscDiagnostics/2.7.0.0) modul 2.7.0.0 vagy újabb verzió. A telepítési lépésekért lásd: [Azure Automation a kívánt állapot konfigurációjának hibaelhárítása](./troubleshoot/desired-state-configuration.md).

## <a name="set-up-integration-with-azure-monitor-logs"></a>Integráció beállítása Azure Monitor naplókhoz

Az adatok Azure Automation állapotból Azure Monitor naplókba való importálásának megkezdéséhez hajtsa végre a következő lépéseket:

1. Jelentkezzen be az Azure-fiókjába a PowerShellben. Lásd: [bejelentkezés Azure PowerShellsal](/powershell/azure/authenticate-azureps).
1. Szerezze be az Automation-fiók erőforrás-AZONOSÍTÓját az alábbi PowerShell-parancsmag futtatásával. Ha egynél több Automation-fiókkal rendelkezik, válassza ki a konfigurálni kívánt fiók erőforrás-AZONOSÍTÓját.

   ```powershell
   # Find the ResourceId for the Automation account
   Get-AzResource -ResourceType 'Microsoft.Automation/automationAccounts'
   ```

1. A következő PowerShell-parancsmag futtatásával szerezze be Log Analytics munkaterület erőforrás-AZONOSÍTÓját. Ha egynél több munkaterülettel rendelkezik, válassza ki a konfigurálni kívánt munkaterület erőforrás-AZONOSÍTÓját.

   ```powershell
   # Find the ResourceId for the Log Analytics workspace
   Get-AzResource -ResourceType 'Microsoft.OperationalInsights/workspaces'
   ```

1. Futtassa a következő PowerShell-parancsmagot, `<AutomationResourceId>` és cserélje `<WorkspaceResourceId>` le az `ResourceId` egyes előző lépések értékeit.

   ```powershell
   Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $true -Category 'DscNodeStatus'
   ```

1. Ha le szeretné állítani az adatok importálását Azure Automation állapotból Azure Monitor naplókba, futtassa a következő PowerShell-parancsmagot.

   ```powershell
   Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $false -Category 'DscNodeStatus'
   ```

## <a name="view-the-state-configuration-logs"></a>Az állapot-konfigurációs naplók megtekintése

Miután beállította az integrációt Azure Monitor naplókkal az Automation-állapot konfigurációs adataihoz, megtekintheti őket az állapot-konfiguráció (DSC) lap bal oldali ablaktáblájának **figyelés** szakaszában található **naplók** lehetőség kiválasztásával.

![Naplók](media/automation-dsc-diagnostics/automation-dsc-logs-toc-item.png)

Megnyílik a napló keresése ablaktábla az Automation-fiók erőforrására vonatkozó lekérdezési régióval. A DSC-műveletekhez tartozó állapot-konfigurációs naplók kereséséhez keresse meg Azure Monitor naplókat. A DSC-műveletekhez tartozó rekordokat a `AzureDiagnostics` táblázat tárolja. Ha például meg szeretné keresni a nem megfelelő csomópontokat, írja be a következő lekérdezést.

```AzureDiagnostics
| where Category == 'DscNodeStatus' 
| where OperationName contains 'DSCNodeStatusData'
| where ResultType != 'Compliant'
```

Szűrés részletei:

* Szűréssel `DscNodeStatusData` adja vissza az egyes állapot-konfigurációs csomópontok visszatérési műveleteit.
* `DscResourceStatusData`Az adott erőforrásra alkalmazott csomópont-konfigurációban meghívott összes DSC-erőforrás visszaküldési műveleteinek szűréséhez. 
* A szűrő bekapcsolásával `DscResourceStatusData` visszatérhet a hibás DSC-erőforrásokra vonatkozó információk.

Ha többet szeretne megtudni a naplófájlok adatainak megkereséséről, tekintse meg a [Azure monitorban található naplók áttekintését](../azure-monitor/log-query/log-query-overview.md).

### <a name="send-an-email-when-a-state-configuration-compliance-check-fails"></a>E-mail küldése, ha az állapot-konfiguráció megfelelőségének ellenőrzése sikertelen

Az egyik legfontosabb ügyfelünk, hogy e-mail vagy szöveg küldésére van lehetőség, ha a DSC-konfigurációval valamilyen hiba történik.

Riasztási szabály létrehozásához először létre kell hoznia egy naplót az állapot-konfigurációs jelentés azon rekordjaihoz, amelyeknek meg kell hívniuk a riasztást. A riasztási szabály létrehozásához és konfigurálásához kattintson az **új riasztási szabály** gombra.

1. A Log Analytics munkaterület áttekintés lapján kattintson a **naplók** elemre.
1. Hozzon létre egy naplóbeli keresési lekérdezést a riasztáshoz úgy, hogy beírja a következő keresést a lekérdezés mezőbe:  `Type=AzureDiagnostics Category='DscNodeStatus' NodeName_s='DSCTEST1' OperationName='DscNodeStatusData' ResultType='Failed'`

   Ha több Automation-fiókból vagy-előfizetésből állított be naplókat a munkaterületre, a riasztásokat az előfizetés és az Automation-fiók alapján csoportosíthatja. Származtatja az Automation-fiók nevét a `Resource` rekordok keresés mezőjéből `DscNodeStatusData` .
1. A **szabály létrehozása** képernyő megnyitásához kattintson az oldal tetején található **új riasztási szabály** elemre. 

A riasztás konfigurálásának lehetőségeiről további információt a [riasztási szabály létrehozása](../azure-monitor/platform/alerts-metric.md)című témakörben talál.

### <a name="find-failed-dsc-resources-across-all-nodes"></a>Nem sikerült a DSC-erőforrások keresése az összes csomóponton

A Azure Monitor naplók használatának egyik előnye, hogy a sikertelen ellenőrzéseket a csomópontokon is megkeresheti. A meghibásodott DSC-erőforrások összes példányának megkeresése:

1. A Log Analytics munkaterület áttekintése lapon kattintson a **naplók** elemre.
1. Hozzon létre egy naplóbeli keresési lekérdezést a riasztáshoz úgy, hogy beírja a következő keresést a lekérdezés mezőbe:  `Type=AzureDiagnostics Category='DscNodeStatus' OperationName='DscResourceStatusData' ResultType='Failed'`

### <a name="view-historical-dsc-node-status"></a>A DSC-csomópont korábbi állapotának megtekintése

A DSC-csomóponti állapot előzményeinek időbeli megjelenítéséhez használhatja ezt a lekérdezést:

`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=DscNodeStatus NOT(ResultType="started") | measure Count() by ResultType interval 1hour`

A lekérdezés megjeleníti a csomópont állapotának diagramját az idő múlásával.

## <a name="azure-monitor-logs-records"></a>Azure Monitor rekordok naplózása

Azure Automation diagnosztika két típusú rekordot hoz létre a Azure Monitor naplókban:

* Node status-adat ( `DscNodeStatusData` )
* Erőforrás-állapotadatok ( `DscResourceStatusData` )

### <a name="dscnodestatusdata"></a>DscNodeStatusData

| Tulajdonság | Leírás |
| --- | --- |
| TimeGenerated |Az a dátum és idő, amikor a megfelelőségi ellenőrzés futott. |
| OperationName |`DscNodeStatusData`. |
| ResultType |Az érték, amely azt jelzi, hogy a csomópont megfelelő-e. |
| NodeName_s |A felügyelt csomópont neve. |
| NodeComplianceStatus_s |Az állapot értéke, amely megadja, hogy a csomópont megfelelő-e. |
| DscReportStatus |Állapot értéke, amely azt jelzi, hogy a megfelelőségi ellenőrzés sikeresen futott-e. |
| ConfigurationMode | A konfiguráció a csomópontra való alkalmazásához használt mód. Lehetséges értékek: <ul><li>`ApplyOnly`: A DSC alkalmazza a konfigurációt, és nem tesz tovább mást, kivéve, ha új konfigurációt küld a cél csomópontra, vagy amikor új konfigurációt végez egy kiszolgálóról. Új konfiguráció kezdeti alkalmazása után a DSC nem keres eltolódást egy előzőleg konfigurált állapotból. A DSC addig próbálkozik a konfiguráció alkalmazásával, amíg az `ApplyOnly` érték nem lép érvénybe. </li><li>`ApplyAndMonitor`: Ez az alapértelmezett érték. Az LCD ChipOnGlas minden új konfigurációt alkalmaz. Egy új konfiguráció kezdeti alkalmazása után, ha a cél csomópont a kívánt állapotba sodródik, a DSC a naplókban lévő eltérést jelenti. A DSC addig próbálkozik a konfiguráció alkalmazásával, amíg az `ApplyAndMonitor` érték nem lép érvénybe.</li><li>`ApplyAndAutoCorrect`: A DSC minden új konfigurációt alkalmaz. Egy új konfiguráció kezdeti alkalmazása után, ha a cél csomópont a kívánt állapotba sodródik, a DSC a naplókban jelzi a következetlenségeket, majd újra alkalmazza az aktuális konfigurációt.</li></ul> |
| HostName_s | A felügyelt csomópont neve. |
| IPAddress | A felügyelt csomópont IPv4-címe. |
| Kategória | `DscNodeStatus`. |
| Erőforrás | A Azure Automation fiók neve. |
| Tenant_g | A hívó bérlőjét azonosító GUID. |
| NodeId_g | A felügyelt csomópontot azonosító GUID. |
| DscReportId_g | A jelentést azonosító GUID. |
| LastSeenTime_t | A jelentés legutóbbi megtekintésének dátuma és időpontja. |
| ReportStartTime_t | A jelentés elindításának dátuma és időpontja. |
| ReportEndTime_t | A jelentés befejezésének dátuma és időpontja. |
| NumberOfResources_d | A csomópontra alkalmazott konfigurációban meghívott DSC-erőforrások száma. |
| SourceSystem | A forrásrendszer azonosítja, hogy Azure Monitor naplók hogyan gyűjtötték össze az adatokat. Mindig `Azure` Az Azure Diagnostics szolgáltatáshoz. |
| ResourceId |A Azure Automation fiók erőforrás-azonosítója. |
| ResultDescription | A művelethez tartozó erőforrás leírása. |
| SubscriptionId | Az Automation-fiókhoz tartozó Azure-előfizetés azonosítója (GUID). |
| ResourceGroup | Az Automation-fiókhoz tartozó erőforráscsoport neve. |
| ResourceProvider | Microsoft. Automation. |
| ResourceType | AUTOMATIONACCOUNTS. |
| CorrelationId | A megfelelőségi jelentés korrelációs azonosítóját tartalmazó GUID. |

### <a name="dscresourcestatusdata"></a>DscResourceStatusData

| Tulajdonság | Leírás |
| --- | --- |
| TimeGenerated |Az a dátum és idő, amikor a megfelelőségi ellenőrzés futott. |
| OperationName |`DscResourceStatusData`.|
| ResultType |Azt határozza meg, hogy az erőforrás megfelelő-e. |
| NodeName_s |A felügyelt csomópont neve. |
| Kategória | DscNodeStatus. |
| Erőforrás | A Azure Automation fiók neve. |
| Tenant_g | A hívó bérlőjét azonosító GUID. |
| NodeId_g |A felügyelt csomópontot azonosító GUID. |
| DscReportId_g |A jelentést azonosító GUID. |
| DscResourceId_s |A DSC-erőforrás példányának neve. |
| DscResourceName_s |A DSC-erőforrás neve. |
| DscResourceStatus_s |Azt jelzi, hogy a DSC-erőforrás megfelel-e. |
| DscModuleName_s |A DSC-erőforrást tartalmazó PowerShell-modul neve. |
| DscModuleVersion_s |A DSC-erőforrást tartalmazó PowerShell-modul verziója. |
| DscConfigurationName_s |A csomópontra alkalmazott konfiguráció neve. |
| ErrorCode_s | A hibakód, ha az erőforrás sikertelen volt. |
| ErrorMessage_s |A hibaüzenet, ha az erőforrás sikertelen volt. |
| DscResourceDuration_d |Az az idő (másodpercben), ameddig a DSC-erőforrás futott. |
| SourceSystem | Hogyan gyűjtöttük össze az adatokat Azure Monitor naplókat. Mindig `Azure` Az Azure Diagnostics szolgáltatáshoz. |
| ResourceId |A Azure Automation fiók azonosítója. |
| ResultDescription | A művelet leírása. |
| SubscriptionId | Az Automation-fiókhoz tartozó Azure-előfizetés azonosítója (GUID). |
| ResourceGroup | Az Automation-fiókhoz tartozó erőforráscsoport neve. |
| ResourceProvider | Microsoft. Automation. |
| ResourceType | AUTOMATIONACCOUNTS. |
| CorrelationId |A megfelelőségi jelentés korrelációs AZONOSÍTÓjának GUID azonosítója. |

## <a name="next-steps"></a>Következő lépések

- Az áttekintést lásd: [Azure Automation állapot konfigurációjának áttekintése](automation-dsc-overview.md).
- Első lépésként tekintse meg [az Azure Automation állapot konfigurációjának első lépései](automation-dsc-getting-started.md)című témakört.
- Ha szeretne többet megtudni a DSC-konfigurációk fordításáról, hogy hozzá lehessen rendelni őket a célcsoportokhoz, tekintse meg [a DSC-konfigurációk fordítása Azure Automation állapot konfigurációjában](automation-dsc-compile.md)című témakört.
- A PowerShell-parancsmagok leírása: [az. Automation](/powershell/module/az.automation).
- A díjszabással kapcsolatos információkért lásd: [Azure Automation állapot konfigurációjának díjszabása](https://azure.microsoft.com/pricing/details/automation/).
- Ha szeretné megtekinteni a Azure Automation állapot konfigurációjának folyamatos üzembe helyezési folyamatban való használatát, tekintse [meg a folyamatos üzembe helyezés a chocolatey](automation-dsc-cd-chocolatey.md)használatával című témakört.
- Ha többet szeretne megtudni a különböző keresési lekérdezések létrehozásáról és az Automation-állapot konfigurációs naplófájljainak Azure Monitor-naplókkal való áttekintéséről, tekintse meg a naplók [keresése a Azure monitor naplókban](../azure-monitor/log-query/log-query-overview.md)című témakört.
- Ha többet szeretne megtudni Azure Monitor a naplókról és az adatgyűjtési forrásokról, tekintse meg [Az Azure Storage-adatok gyűjtése Azure monitor naplók áttekintését](../azure-monitor/platform/resource-logs.md#send-to-log-analytics-workspace)ismertető cikket.