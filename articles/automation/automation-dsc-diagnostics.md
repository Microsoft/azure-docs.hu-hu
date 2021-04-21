---
title: Integráció a Azure Monitor naplókba
description: Ez a cikk bemutatja, hogyan küldhet Desired State Configuration jelentéskészítési adatokat Azure Automation State Configuration Azure Monitor naplókba.
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 11/06/2018
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
manager: carmonm
ms.openlocfilehash: 8952ea87cfd9317225ecb9e313174f8d1fe8e519
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834803"
---
# <a name="integrate-with-azure-monitor-logs"></a>Integráció a Azure Monitor naplókba

Azure Automation State Configuration 30 napig őrzi meg a csomópont állapotadatokat. Ha hosszabb ideig szeretné megőrizni az adatokat, elküldheti a csomópont állapotadatokat a Log Analytics-munkaterületre. A megfelelőségi állapot látható a Azure Portal a PowerShellben, a csomópontok és az egyes DSC-erőforrások csomópontkonfigurációiban. 

Azure Monitor naplók nagyobb működési rálátást kínálnak az Automation State Configuration adataira, és gyorsabban segíthetnek az incidensek megoldásában. A Azure Monitor a következőt használhatja:

- Szerezze be a felügyelt csomópontok és az egyes erőforrások megfelelőségi információit.
- E-mail vagy riasztás aktiválása a megfelelőségi állapot alapján.
- Speciális lekérdezéseket írhat a felügyelt csomópontokon.
- A megfelelőségi állapot korrelálhat az Automation-fiókok között.
- Egyéni nézetek és keresési lekérdezések használatával vizualizálhatja a runbook eredményeit, a runbook feladatának állapotát és egyéb kapcsolódó fő mutatókat vagy metrikákat.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Előfeltételek

Ahhoz, hogy az Automation-State Configuration jelentéseket elküld Azure Monitor naplókba, a következőre lesz szüksége:

- A Azure PowerShell (2.3.0-s verzió) 2016. novemberi vagy újabb kiadása. [](/powershell/azure/)
- Egy Azure Automation-fiókra. További információ: [Bevezetés a](automation-intro.md)Azure Automation.
- Egy Automation-vezérlési szolgáltatásajánlatot & Log Analytics-munkaterület. További információ: [Get started with Log Analytics in Azure Monitor](../azure-monitor/logs/log-analytics-tutorial.md).
- Legalább egy Azure Automation State Configuration csomópont. További információ: [Gépek](automation-dsc-onboarding.md)Azure Automation State Configuration.
- Az [xDscDiagnostics](https://www.powershellgallery.com/packages/xDscDiagnostics/2.7.0.0) modul 2.7.0.0-s vagy újabb verziója. A telepítés lépéseit lásd: [Hibaelhárítás Azure Automation Desired State Configuration.](./troubleshoot/desired-state-configuration.md)

## <a name="set-up-integration-with-azure-monitor-logs"></a>Integráció beállítása Azure Monitor naplókhoz

Az adatok importálásának megkezdéséhez Azure Automation State Configuration Azure Monitor naplókba, kövesse az alábbi lépéseket:

1. Jelentkezzen be Azure-fiókjába a PowerShellben. Lásd: [Bejelentkezés Azure PowerShell.](/powershell/azure/authenticate-azureps)
1. Az Alábbi PowerShell-parancsmag futtatásával szerezze be az Automation-fiók erőforrás-azonosítóját. Ha több Automation-fiókkal is van, válassza ki a konfigurálni kívánt fiók erőforrás-azonosítóját.

   ```powershell
   # Find the ResourceId for the Automation account
   Get-AzResource -ResourceType 'Microsoft.Automation/automationAccounts'
   ```

1. Az alábbi PowerShell-parancsmag futtatásával szerezze be a Log Analytics-munkaterület erőforrás-azonosítóját. Ha több munkaterülettel is dolgozik, válassza ki a konfigurálni kívánt munkaterület erőforrás-azonosítóját.

   ```powershell
   # Find the ResourceId for the Log Analytics workspace
   Get-AzResource -ResourceType 'Microsoft.OperationalInsights/workspaces'
   ```

1. Futtassa a következő PowerShell-parancsmagot, és cserélje le a és a értékét az előző lépésekben `<AutomationResourceId>` `<WorkspaceResourceId>` található `ResourceId` értékekre.

   ```powershell
   Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $true -Category 'DscNodeStatus'
   ```

1. Ha le szeretné állítani az adatok importálását Azure Automation State Configuration a Azure Monitor naplókba, futtassa a következő PowerShell-parancsmagot.

   ```powershell
   Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $false -Category 'DscNodeStatus'
   ```

## <a name="view-the-state-configuration-logs"></a>A naplók State Configuration megtekintése

Miután beállította az automation State Configuration-adatok Azure Monitor-naplóival való integrációt,  a Naplók lehetőséget választva megtekintheti őket az Állapotkonfiguráció (DSC) lap bal oldali panelén található Figyelés szakaszban. 

![Naplók](media/automation-dsc-diagnostics/automation-dsc-logs-toc-item.png)

Megnyílik a Naplókeresés panel az Automation-fiók erőforrására vonatkozó lekérdezési régióval. A keresési naplókban State Configuration DSC-műveleteket a naplókban való Azure Monitor kereshet. A DSC-műveletek rekordjai a táblában vannak `AzureDiagnostics` tárolva. A nem megfelelő csomópontok kereséséhez például írja be a következő lekérdezést.

```AzureDiagnostics
| where Category == 'DscNodeStatus' 
| where OperationName contains 'DSCNodeStatusData'
| where ResultType != 'Compliant'
```

Szűrés részletei:

* Szűrje a `DscNodeStatusData` szűrőt az egyes csomópontok műveleteinek State Configuration érdekében.
* Szűrje a szűrőt, hogy az adott erőforrásra alkalmazott csomópont-konfigurációban minden nevű `DscResourceStatusData` DSC-erőforráshoz visszaadja a műveleteket. 
* Szűrje a `DscResourceStatusData` szűrőt a hibás DSC-erőforrások hibainformációinak visszaadása érdekében.

További információ a naplólekérdezések adatok kereséséhez való felépítéséről: A naplólekérdezések áttekintése a [Azure Monitor.](../azure-monitor/logs/log-query-overview.md)

### <a name="send-an-email-when-a-state-configuration-compliance-check-fails"></a>E-mail küldése, ha State Configuration megfelelőségi ellenőrzés meghiúsul

Az egyik legfontosabb ügyfélkérésünk az, hogy küldhet e-mailt vagy sms-t, ha egy DSC-konfigurációval kapcsolatos hiba történik.

Riasztási szabály létrehozásához először hozzon létre naplókeresést a riasztást meghívó State Configuration jelentésrekordjaihoz. Kattintson az **Új riasztási szabály** gombra a riasztási szabály létrehozásához és konfiguráláshoz.

1. A Log Analytics-munkaterület Áttekintés lapján kattintson a Naplók **elemre.**
1. Hozzon létre egy naplókeresési lekérdezést a riasztáshoz úgy, hogy beírja a következő keresést a lekérdezés mezőbe:  `Type=AzureDiagnostics Category='DscNodeStatus' NodeName_s='DSCTEST1' OperationName='DscNodeStatusData' ResultType='Failed'`

   Ha több Automation-fiókból vagy -előfizetésből származó naplókat is beállított a munkaterületre, a riasztásokat előfizetés és Automation-fiók szerint csoportosíthatja. Származtatjuk az Automation-fiók nevét a rekordok `Resource` keresésében a `DscNodeStatusData` mezőből.
1. A Szabály **létrehozása képernyő megnyitásához** kattintson az **Új riasztási** szabály elemre az oldal tetején. 

A riasztás konfigurálható beállításaival kapcsolatos további információkért lásd: [Riasztási szabály létrehozása.](../azure-monitor/alerts/alerts-metric.md)

### <a name="find-failed-dsc-resources-across-all-nodes"></a>Sikertelen DSC-erőforrások megkeresása az összes csomóponton

A naplók használatának Azure Monitor előnye, hogy a csomópontok között keresheti meg a sikertelen ellenőrzéseket. A sikertelen DSC-erőforrások összes példányának megkeresása:

1. A Log Analytics-munkaterület Áttekintés lapján kattintson a Naplók **elemre.**
1. Hozzon létre egy naplókeresési lekérdezést a riasztáshoz úgy, hogy beírja a következő keresést a lekérdezés mezőbe:  `Type=AzureDiagnostics Category='DscNodeStatus' OperationName='DscResourceStatusData' ResultType='Failed'`

### <a name="view-historical-dsc-node-status"></a>Korábbi DSC-csomópont állapotának megtekintése

A DSC-csomópontok állapotelőzmények adott időszakra vonatkozó megjelenítéséhez használja ezt a lekérdezést:

`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=DscNodeStatus NOT(ResultType="started") | measure Count() by ResultType interval 1hour`

Ez a lekérdezés egy diagramot jelenít meg a csomópont állapotáról az idő alapján.

## <a name="azure-monitor-logs-records"></a>Azure Monitor naplórekordok

Azure Automation diagnosztika két rekordkategóriát hoz létre a Azure Monitor naplókban:

* Csomópont állapotadata ( `DscNodeStatusData` )
* Erőforrás állapotadata ( `DscResourceStatusData` )

### <a name="dscnodestatusdata"></a>DscNodeStatusData

| Tulajdonság | Leírás |
| --- | --- |
| TimeGenerated |A megfelelőségi ellenőrzés lefutott dátuma és időpontja. |
| OperationName |`DscNodeStatusData`. |
| ResultType |Érték, amely azt jelzi, hogy a csomópont megfelelő-e. |
| NodeName_s |A felügyelt csomópont neve. |
| NodeComplianceStatus_s |Állapotérték, amely meghatározza, hogy a csomópont megfelelő-e. |
| DscReportStatus |Állapotérték, amely azt jelzi, hogy a megfelelőségi ellenőrzés sikeresen lefutott-e. |
| ConfigurationMode (Konfigurációmód) | A konfiguráció csomópontra való alkalmazásának módja. Lehetséges értékek: <ul><li>`ApplyOnly`: A DSC alkalmazza a konfigurációt, és semmi mást nem tesz, kivéve, ha egy új konfigurációt leküld a célcsomópontnak, vagy amikor új konfigurációt leküld egy kiszolgálóról. Az új konfiguráció első alkalmazása után a DSC nem ellenőrzi a korábban konfigurált állapottól való eltérést. A DSC mindaddig megkísérli alkalmazni a konfigurációt, amíg az érték effektusba nem `ApplyOnly` kerül. </li><li>`ApplyAndMonitor`: Ez az alapértelmezett érték. Az LCM minden új konfigurációt alkalmaz. Egy új konfiguráció első alkalmazása után, ha a célcsomópont a célállapottól való eltérést tapasztalja, a DSC jelenti a naplók eltérését. A DSC addig próbálja alkalmazni a konfigurációt, amíg az érték életbe nem `ApplyAndMonitor` lép.</li><li>`ApplyAndAutoCorrect`: A DSC minden új konfigurációt alkalmaz. Egy új konfiguráció első alkalmazása után, ha a célcsomópont a célállapottól való eltérést tapasztalja, a DSC jelenti a naplók eltérését, majd újraalkalmazza az aktuális konfigurációt.</li></ul> |
| HostName_s | A felügyelt csomópont neve. |
| IPAddress | A felügyelt csomópont IPv4-címe. |
| Kategória | `DscNodeStatus`. |
| Erőforrás | A Azure Automation neve. |
| Tenant_g | A hívó bérlőét azonosító GUID. |
| NodeId_g | A felügyelt csomópontot azonosító GUID. |
| DscReportId_g | A jelentést azonosító GUID. |
| LastSeenTime_t | A jelentés legutóbbi megtekintésének dátuma és időpontja. |
| ReportStartTime_t | A jelentés elindulásának dátuma és időpontja. |
| ReportEndTime_t | A jelentés befejezésének dátuma és időpontja. |
| NumberOfResources_d | A csomópontra alkalmazott konfigurációban a nevű DSC-erőforrások száma. |
| SourceSystem | A forrásrendszer, amely meghatározza, Azure Monitor naplók hogyan gyűjtötték össze az adatokat. Mindig `Azure` az Azure-diagnosztika számára. |
| ResourceId |A Azure Automation erőforrás-azonosítója. |
| ResultDescription | A művelet erőforrás-leírása. |
| SubscriptionId | Az Automation-fiók Azure-előfizetés-azonosítója (GUID). |
| ResourceGroup | Az Automation-fiók erőforráscsoportja neve. |
| ResourceProvider | Microsoft. Automatizálás. |
| ResourceType | AUTOMATIONACCOUNTS. |
| CorrelationId | Egy GUID, amely a megfelelőségi jelentés korrelációs azonosítója. |

### <a name="dscresourcestatusdata"></a>DscResourceStatusData

| Tulajdonság | Leírás |
| --- | --- |
| TimeGenerated |A megfelelőségi ellenőrzés lefutott dátuma és időpontja. |
| OperationName |`DscResourceStatusData`.|
| ResultType |Azt határozza meg, hogy az erőforrás megfelelő-e. |
| NodeName_s |A felügyelt csomópont neve. |
| Kategória | DscNodeStatus. |
| Erőforrás | A Azure Automation neve. |
| Tenant_g | A hívó bérlőét azonosító GUID. |
| NodeId_g |A felügyelt csomópontot azonosító GUID. |
| DscReportId_g |A jelentést azonosító GUID. |
| DscResourceId_s |A DSC-erőforráspéldány neve. |
| DscResourceName_s |A DSC-erőforrás neve. |
| DscResourceStatus_s |Azt határozza meg, hogy a DSC-erőforrás megfelelő-e. |
| DscModuleName_s |A DSC-erőforrást tartalmazó PowerShell-modul neve. |
| DscModuleVersion_s |A DSC-erőforrást tartalmazó PowerShell-modul verziója. |
| DscConfigurationName_s |A csomópontra alkalmazott konfiguráció neve. |
| ErrorCode_s | A hibakód, ha az erőforrás meghibásodott. |
| ErrorMessage_s |A hibaüzenet, ha az erőforrás meghibásodott. |
| DscResourceDuration_d |A DSC-erőforrás által futtatott idő másodpercben. |
| SourceSystem | Hogyan Azure Monitor a naplók az adatokat? Mindig `Azure` az Azure-diagnosztika számára. |
| ResourceId |A Azure Automation azonosítója. |
| ResultDescription | A művelet leírása. |
| SubscriptionId | Az Automation-fiók Azure-előfizetés-azonosítója (GUID). |
| ResourceGroup | Az Automation-fiók erőforráscsoportja neve. |
| ResourceProvider | Microsoft. Automatizálás. |
| ResourceType | AUTOMATIONACCOUNTS. |
| CorrelationId |A GUID a megfelelőségi jelentés korrelációs azonosítója. |

## <a name="next-steps"></a>Következő lépések

- Áttekintésért lásd a Azure Automation State Configuration [áttekintését.](automation-dsc-overview.md)
- Első lépések: [Első lépések a Azure Automation State Configuration.](automation-dsc-getting-started.md)
- A DSC-konfigurációk célcsomópontokhoz való hozzárendelése érdekében való fordításával kapcsolatos további információkért lásd: DSC-konfigurációk fordítása a [Azure Automation State Configuration.](automation-dsc-compile.md)
- A PowerShell-parancsmagok referenciáját lásd: [Az.Automation](/powershell/module/az.automation).
- Díjszabási információkért lásd: [Azure Automation State Configuration díjszabása.](https://azure.microsoft.com/pricing/details/automation/)
- A folyamatos üzembe helyezési folyamatban Azure Automation State Configuration példát a Folyamatos üzembe helyezés beállítása a [Chocolatey használatával.](automation-dsc-cd-chocolatey.md)
- A különböző keresési lekérdezések felépítésével és az Automation State Configuration-naplók Azure Monitor naplók használatával való áttekintését lásd: Naplókeresések az Azure Monitor [naplókban.](../azure-monitor/logs/log-query-overview.md)
- További információ a Azure Monitor és adatgyűjtési forrásokról: Azure Storage-adatok gyűjtése a naplókban [Azure Monitor áttekintése.](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace)
