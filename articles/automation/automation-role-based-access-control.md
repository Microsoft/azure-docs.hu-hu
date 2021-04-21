---
title: Szerepkör-engedélyek és biztonság kezelése a Azure Automation
description: Ez a cikk bemutatja, hogyan használható az Azure szerepköralapú hozzáférés-vezérlése (Azure RBAC), amely lehetővé teszi az Azure-erőforrások hozzáférés-kezelését.
keywords: automation rbac, szerepköralapú hozzáférés-vezérlés, azure rbac
services: automation
ms.subservice: shared-capabilities
ms.date: 07/21/2020
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 0727d3342c73d9aa4d15e84aacb82bd8fea01d65
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833579"
---
# <a name="manage-role-permissions-and-security"></a>Szerepköri engedélyek és biztonság kezelése

Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) lehetővé teszi az Azure-erőforrások hozzáférés-kezelését. Az [Azure RBAC használatával](../role-based-access-control/overview.md)elkülönítheti a csapaton belüli feladatokat, és csak olyan mennyiségű hozzáférést adhat a felhasználóknak, csoportoknak és alkalmazásoknak, amelyekre szükségük van a feladataik elvégzéséhez. Szerepköralapú hozzáférést adhat a felhasználóknak a Azure Portal, Azure Command-Line vagy Azure Management API-k használatával.

## <a name="roles-in-automation-accounts"></a>Automation-fiókok szerepkörei

A Azure Automation hozzáférés úgy adható meg, hogy a megfelelő Azure-szerepkört rendeli felhasználókhoz, csoportokhoz és alkalmazásokhoz az Automation-fiók hatókörében. Alább láthatók az Automation-fiók által támogatott beépített szerepkörök:

| **Szerepkör** | **Leírás** |
|:--- |:--- |
| Tulajdonos |A Tulajdonos szerepkör hozzáférést biztosít az Automation-fiókon belüli összes erőforráshoz és művelethez, beleértve a hozzáférést más felhasználók, csoportok és alkalmazások számára az Automation-fiók kezeléséhez. |
| Közreműködő |A közreműködői szerepkör segítségével minden elemet kezelhet, csak más felhasználók Automation-fiókokra vonatkozó hozzáférési jogosultságait nem módosíthatja. |
| Olvasó |Az olvasói szerep lehetővé teszi az összes erőforrás megtekintését egy Automation-fiókban, de módosítás nem hajtható vele végre. |
| Automation-operátor |Az Automation-operátor szerepkör lehetővé teszi a runbook nevének és tulajdonságainak megtekintését, valamint az Automation-fiókban futó összes runbookhoz szükséges feladatok létrehozásához és kezeléséhez. Ez a szerepkör akkor hasznos, ha meg szeretné védeni az Automation-fiók erőforrásait, például a hitelesítő adateszközöket és a runbookokat a megtekintéstől vagy módosítástól, de továbbra is engedélyezni szeretné a szervezet tagjai számára ezen runbookok végrehajtását. |
|Automation-feladat operátora|Az Automation-feladatkezelő szerepkör lehetővé teszi, hogy feladatokat hozzon létre és kezeljen egy Automation-fiókban szereplő összes runbookhoz.|
|Automation Runbook-operátor|Az Automation Runbook Operator szerepkör lehetővé teszi egy runbook nevének és tulajdonságainak megtekintését.|
| Log Analytics közreműködő | A Log Analytics közreműködője szerepkör lehetővé teszi az összes monitorozási adat olvasását és a figyelési beállítások szerkesztését. A figyelési beállítások szerkesztése magában foglalja a virtuálisgép-bővítmény virtuális gépekhez való hozzáadását, a tárfiókkulcsok olvasását a naplók Azure Storage-ból való gyűjtésének konfigurálásához, Az Automation-fiókok létrehozásához és konfigurálásához, Azure Automation-funkciók hozzáadásához és az Azure-diagnosztika konfigurálásához az összes Azure-erőforráson.|
| Log Analytics olvasó | A Log Analytics-olvasó szerepkör lehetővé teszi az összes monitorozási adat megtekintését és keresését, valamint a figyelési beállítások megtekintését. Ebbe beletartozik az Azure Diagnostics konfigurációjának megtekintése az összes Azure-erőforráson. |
| Figyelési közreműködő | A Figyelési közreműködő szerepkör lehetővé teszi az összes figyelési adat olvasását és a figyelési beállítások frissítését.|
| Figyelési olvasó | A Figyelési olvasó szerepkör lehetővé teszi az összes figyelési adat olvasását. |
| Felhasználói hozzáférés rendszergazdája |A felhasználói hozzáférés rendszergazdájának szerepköre lehetővé teszi, hogy kezelje a felhasználói hozzáférést az Azure Automation-fiókokhoz. |

## <a name="role-permissions"></a>Szerepköri engedélyek

Az alábbi táblázatok az egyes szerepkörhöz megadott konkrét engedélyeket ismertetik. Ez magában foglalhatja a műveleteket, amelyek engedélyeket adnak, és a NotActions, amelyek korlátozzák őket.

### <a name="owner"></a>Tulajdonos

A tulajdonos mindent kezelhet, a hozzáférést is beleértve. Az alábbi táblázat a szerepkörhöz megadott engedélyeket tartalmazza:

|Műveletek|Leírás|
|---|---|
|Microsoft.Automation/automationAccounts/|Hozzon létre és kezeljen minden típusú erőforrást.|

### <a name="contributor"></a>Közreműködő

A Közreműködő a hozzáférés kivételével mindent kezelhet. Az alábbi táblázat a szerepkörhöz megadott és megtagadott engedélyeket tartalmazza:

|**Műveletek**  |**Leírás**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/|Minden típusú erőforrás létrehozása és kezelése|
|**Nem műveletek**||
|Microsoft.Authorization/*/Delete| Szerepkörök és szerepkör-hozzárendelések törlése.       |
|Microsoft.Authorization/*/Write     |  Szerepkörök és szerepkör-hozzárendelések létrehozása.       |
|Microsoft.Authorization/elevateAccess/Action    | Megtagadja a felhasználói hozzáférés rendszergazdájának létrehozási képességét.       |

### <a name="reader"></a>Olvasó

Az olvasó megtekintheti egy Automation-fiók összes erőforrását, de nem tud módosításokat tenni.

|**Műveletek**  |**Leírás**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/read|Az összes megtekintése Automation-fiók erőforrásainak létrehozása. |

### <a name="automation-operator"></a>Automation-operátor

Az Automation-operátorok feladatokat hozhatnak létre és kezelnek, valamint beolvashatja az Automation-fiókban szereplő összes runbook nevét és tulajdonságait.

>[!NOTE]
>Ha az egyes runbookok operátori hozzáférését szeretné szabályozni, ne állítsa be ezt a szerepkört. Ehelyett használja az **Automation-feladatkezelő és** az **Automation Runbook-operátor szerepkört** együtt.

Az alábbi táblázat a szerepkörhöz megadott engedélyeket tartalmazza:

|**Műveletek**  |**Leírás**  |
|---------|---------|
|Microsoft.Authorization/*/read|Olvasási engedélyezés.|
|Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read|Olvassa el a hibrid runbook-feldolgozó erőforrásait.|
|Microsoft.Automation/automationAccounts/jobs/read|Listába sorolja a runbookban található feladatokat.|
|Microsoft.Automation/automationAccounts/jobs/resume/action|Szüneteltetett feladat folytatása.|
|Microsoft.Automation/automationAccounts/jobs/stop/action|Folyamatban lévő feladat megszakítása.|
|Microsoft.Automation/automationAccounts/jobs/streams/read|Olvassa el a feladatstreameket és a kimenetet.|
|Microsoft.Automation/automationAccounts/jobs/output/read|Le kell kapnia egy feladat kimenetét.|
|Microsoft.Automation/automationAccounts/jobs/suspend/action|Folyamatban lévő feladat szüneteltetése.|
|Microsoft.Automation/automationAccounts/jobs/write|Feladatok létrehozása.|
|Microsoft.Automation/automationAccounts/jobSchedules/read|Lekért Azure Automation feladat ütemezését.|
|Microsoft.Automation/automationAccounts/jobSchedules/write|Hozzon létre Azure Automation feladat ütemezését.|
|Microsoft.Automation/automationAccounts/linkedWorkspace/read|Szerezze be az Automation-fiókhoz csatolt munkaterületet.|
|Microsoft.Automation/automationAccounts/read|Szerezze be Azure Automation-fiókot.|
|Microsoft.Automation/automationAccounts/runbooks/read|Szerezze be Azure Automation runbookot.|
|Microsoft.Automation/automationAccounts/schedules/read|Lekért egy Azure Automation-ütemezési eszközt.|
|Microsoft.Automation/automationAccounts/schedules/write|Hozzon létre vagy frissítsen egy Azure Automation-eszközt.|
|Microsoft.Resources/subscriptions/resourceGroups/read      |Szerepkörök és szerepkör-hozzárendelések olvasása.         |
|Microsoft.Resources/deployments/*      |Erőforráscsoportok üzembe helyezésének létrehozása és kezelése.         |
|Microsoft.Insights/alertRules/*      | Riasztási szabályok létrehozása és kezelése.        |
|Microsoft.Support/* |Támogatási jegyek létrehozása és kezelése.|

### <a name="automation-job-operator"></a>Automation-feladat operátora

Az Automation-feladatkezelői szerepkör az Automation-fiók hatókörében adható meg.Ez lehetővé teszi az operátor számára a fiókban szereplő összes runbookhoz szükséges feladatok létrehozására és kezelésére vonatkozó engedélyeket. Ha a feladatkezelői szerepkör olvasási engedélyt kap az Automation-fiókot tartalmazó erőforráscsoporthoz, akkor a szerepkör tagjai el tudjanak kezdeni runbookokat. Ezek azonban nem hozhatók létre, szerkeszthetők vagy törölhetők.

Az alábbi táblázat a szerepkörhöz megadott engedélyeket mutatja be:

|**Műveletek**  |**Leírás**  |
|---------|---------|
|Microsoft.Authorization/*/read|Olvasási engedélyezés.|
|Microsoft.Automation/automationAccounts/jobs/read|Listába sorolja a runbookban található feladatokat.|
|Microsoft.Automation/automationAccounts/jobs/resume/action|Szüneteltetett feladat folytatása.|
|Microsoft.Automation/automationAccounts/jobs/stop/action|Folyamatban lévő feladat megszakítása.|
|Microsoft.Automation/automationAccounts/jobs/streams/read|Olvassa el a feladatstreameket és a kimenetet.|
|Microsoft.Automation/automationAccounts/jobs/suspend/action|Szüneteltetheti a folyamatban lévő feladatot.|
|Microsoft.Automation/automationAccounts/jobs/write|Feladatok létrehozása.|
|Microsoft.Resources/subscriptions/resourceGroups/read      |  Szerepkörök és szerepkör-hozzárendelések olvasása.       |
|Microsoft.Resources/deployments/*      |Erőforráscsoportok üzembe helyezésének létrehozása és kezelése.         |
|Microsoft.Insights/alertRules/*      | Riasztási szabályok létrehozása és kezelése.        |
|Microsoft.Support/* |Támogatási jegyek létrehozása és kezelése.|

### <a name="automation-runbook-operator"></a>Automation Runbook-operátor

Az Automation Runbook-operátor szerepkör a Runbook hatókörében adható meg. Az Automation runbook-operátorok megtekinthetik a runbook nevét és tulajdonságait.Ez a szerepkör és az **Automation-feladatkezelő** szerepkör együttes használatával az operátor a runbookhoz is létrehozhat és kezelhet feladatokat. Az alábbi táblázat a szerepkörhöz megadott engedélyeket tartalmazza:

|**Műveletek**  |**Leírás**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/runbooks/read     | List the runbooks.        |
|Microsoft.Authorization/*/read      | Olvasási engedélyezés.        |
|Microsoft.Resources/subscriptions/resourceGroups/read      |Szerepkörök és szerepkör-hozzárendelések olvasása.         |
|Microsoft.Resources/deployments/*      | Erőforráscsoportok üzembe helyezésének létrehozása és kezelése.         |
|Microsoft.Insights/alertRules/*      | Riasztási szabályok létrehozása és kezelése.        |
|Microsoft.Support/*      | Támogatási jegyek létrehozása és kezelése.        |

### <a name="log-analytics-contributor"></a>Log Analytics közreműködő

A Log Analytics-közreműködők olvashatják az összes monitorozási adatot, és szerkeszthetik a figyelési beállításokat. A figyelési beállítások szerkesztéséhez hozzá kell adni a virtuálisgép-bővítményt a virtuális gépekhez; a tárfiókkulcsok olvasása a naplók Azure Storage-ból való gyűjtésének konfigurálása érdekében; Automation-fiókok létrehozása és konfigurálása; funkciók hozzáadása; és az Azure Diagnostics konfigurálása az összes Azure-erőforráson. Az alábbi táblázat a szerepkörhöz megadott engedélyeket tartalmazza:

|**Műveletek**  |**Leírás**  |
|---------|---------|
|*/olvasás|A titkos kulcsok kivételével minden típusú erőforrást beolvas.|
|Microsoft.Automation/automationAccounts/*|Automation-fiókok kezelése.|
|Microsoft.ClassicCompute/virtualMachines/extensions/*|Virtuálisgép-bővítmények létrehozása és kezelése.|
|Microsoft.ClassicStorage/storageAccounts/listKeys/action|Sorolja fel a klasszikus tárfiókkulcsokat.|
|Microsoft.Compute/virtualMachines/extensions/*|Klasszikus virtuálisgép-bővítmények létrehozása és kezelése.|
|Microsoft.Insights/alertRules/*|Riasztási szabályok olvasása/írása/törlése.|
|Microsoft.Insights/diagnosticSettings/*|Diagnosztikai beállítások olvasása/írása/törlése.|
|Microsoft.OperationalInsights/*|Kezelheti Azure Monitor naplókat.|
|Microsoft.OperationsManagement/*|A Azure Automation szolgáltatások kezelése.|
|Microsoft.Resources/deployments/*|Erőforráscsoportok üzembe helyezésének létrehozása és kezelése.|
|Microsoft.Resources/subscriptions/resourcegroups/deployments/*|Erőforráscsoportok üzembe helyezésének létrehozása és kezelése.|
|Microsoft.Storage/storageAccounts/listKeys/action|Tárfiókkulcsok listából.|
|Microsoft.Support/*|Támogatási jegyek létrehozása és kezelése.|

### <a name="log-analytics-reader"></a>Log Analytics olvasó

A Log Analytics-olvasó megtekintheti és megkeresheti az összes monitorozási adatot, valamint a figyelési beállításokat, beleértve az Azure-diagnosztika konfigurációjának megtekintését az összes Azure-erőforráson. Az alábbi táblázat a szerepkörhöz megadott vagy elutasított engedélyeket mutatja be:

|**Műveletek**  |**Leírás**  |
|---------|---------|
|*/olvasás|A titkos kulcsok kivételével minden típusú erőforrást beolvas.|
|Microsoft.OperationalInsights/workspaces/analytics/query/action|Lekérdezések kezelése a Azure Monitor naplókban.|
|Microsoft.OperationalInsights/workspaces/search/action|Keressen Azure Monitor naplóadatok között.|
|Microsoft.Support/*|Támogatási jegyek létrehozása és kezelése.|
|**Nem műveletek**| |
|Microsoft.OperationalInsights/workspaces/sharedKeys/read|Nem lehet olvasni a megosztott hozzáférési kulcsokat.|

### <a name="monitoring-contributor"></a>Figyelési közreműködő

A figyelési közreműködők az összes monitorozási adatot olvashatják, és frissítheti a figyelési beállításokat. Az alábbi táblázat a szerepkörhöz megadott engedélyeket tartalmazza:

|**Műveletek**  |**Leírás**  |
|---------|---------|
|*/olvasás|A titkos kulcsok kivételével minden típusú erőforrást beolvas.|
|Microsoft.AlertsManagement/alerts/*|Riasztások kezelése.|
|Microsoft.AlertsManagement/alertsSummary/*|A Riasztás irányítópult kezelése.|
|Microsoft.Insights/AlertRules/*|Riasztási szabályok kezelése.|
|Microsoft.Insights/components/*|A Application Insights kezelése.|
|Microsoft.Insights/DiagnosticSettings/*|Diagnosztikai beállítások kezelése.|
|Microsoft.Insights/eventtypes/*|Listás tevékenységnapló-események (felügyeleti események) egy előfizetésben. Ez az engedély a programozott és a portálon a tevékenységnaplóhoz való hozzáférésre is vonatkozik.|
|Microsoft.Insights/LogDefinitions/*|Ez az engedély olyan felhasználók számára szükséges, akiknek a portálon keresztül kell hozzáférniük a tevékenységnaplókhoz. Listásnapló-kategóriák a Tevékenységnaplóban.|
|Microsoft.Insights/MetricDefinitions/*|Olvassa el a metrikadefiníciókat (az erőforráshoz elérhető metrikatípusok listája).|
|Microsoft.Insights/Metrics/*|Egy erőforrás metrikák olvasása.|
|Microsoft.Insights/Register/Action|Regisztrálja a Microsoft.Insights szolgáltatót.|
|Microsoft.Insights/webtests/*|Webes Application Insights kezelése.|
|Microsoft.OperationalInsights/workspaces/intelligencepacks/*|A Azure Monitor naplók megoldáscsomagok kezelése.|
|Microsoft.OperationalInsights/workspaces/savedSearches/*|Kezelheti Azure Monitor mentett kereséseket.|
|Microsoft.OperationalInsights/workspaces/search/action|Log Analytics-munkaterületek keresése.|
|Microsoft.OperationalInsights/workspaces/sharedKeys/action|A Log Analytics-munkaterület kulcsait listába kell sorolni.|
|Microsoft.OperationalInsights/workspaces/storageinsightconfigs/*|A Azure Monitor naplók tárolási elemzésének konfigurációit.|
|Microsoft.Support/*|Támogatási jegyek létrehozása és kezelése.|
|Microsoft.WorkloadMonitor/workloads/*|Számítási feladatok kezelése.|

### <a name="monitoring-reader"></a>Figyelési olvasó

A figyelési olvasó az összes monitorozási adatot be tudja olvasni. Az alábbi táblázat a szerepkörhöz megadott engedélyeket tartalmazza:

|**Műveletek**  |**Leírás**  |
|---------|---------|
|*/olvasás|A titkos kulcsok kivételével minden típusú erőforrást beolvas.|
|Microsoft.OperationalInsights/workspaces/search/action|Log Analytics-munkaterületek keresése.|
|Microsoft.Support/*|Támogatási jegyek létrehozása és kezelése|

### <a name="user-access-administrator"></a>Felhasználói hozzáférés rendszergazdája

A felhasználói hozzáférés rendszergazdája kezelheti az Azure-erőforrások felhasználói hozzáférését. Az alábbi táblázat a szerepkörhöz megadott engedélyeket tartalmazza:

|**Műveletek**  |**Leírás**  |
|---------|---------|
|*/olvasás|Az összes erőforrás olvasása|
|Microsoft.Authorization/*|Engedélyezés kezelése|
|Microsoft.Support/*|Támogatási jegyek létrehozása és kezelése|

## <a name="feature-setup-permissions"></a>Szolgáltatásbeállítási engedélyek

A következő szakaszok ismertetik a minimálisan szükséges engedélyeket a Update Management és változáskövetés és leltározás engedélyezéséhez.

### <a name="permissions-for-enabling-update-management-and-change-tracking-and-inventory-from-a-vm"></a>Engedélyek a virtuális Update Management és változáskövetés és leltározás való változáskövetés és leltározás engedélyezéséhez

|**Művelet**  |**Engedély**  |**Minimális hatókör**  |
|---------|---------|---------|
|Új központi telepítés írása      | Microsoft.Resources/deployments/*          |Előfizetés          |
|Új erőforráscsoport írása      | Microsoft.Resources/subscriptions/resourceGroups/write        | Előfizetés          |
|Új alapértelmezett munkaterület létrehozása      | Microsoft.OperationalInsights/workspaces/write         | Erőforráscsoport         |
|Új fiók létrehozása      |  Microsoft.Automation/automationAccounts/write        |Erőforráscsoport         |
|Munkaterület és fiók összekapcsolása      |Microsoft.OperationalInsights/workspaces/write</br>Microsoft.Automation/automationAccounts/read|Munkaterület</br>Automation-fiók
|MMA-bővítmény létrehozása      | Microsoft.Compute/virtualMachines/write         | Virtuális gép         |
|Mentett keresés létrehozása      | Microsoft.OperationalInsights/workspaces/write          | Munkaterület         |
|Hatókör-konfiguráció létrehozása      | Microsoft.OperationalInsights/workspaces/write          | Munkaterület         |
|Állapot-ellenőrzés – Munkaterület olvasása      | Microsoft.OperationalInsights/workspaces/read         | Munkaterület         |
|Állapot-ellenőrzés – A fiók csatolt munkaterületi tulajdonságának olvasása     | Microsoft.Automation/automationAccounts/read      | Automation-fiók        |
|Állapot-ellenőrzés – Megoldás olvasása      | Microsoft.OperationalInsights/workspaces/intelligencepacks/read          | Megoldás         |
|A be- és beiratozás állapotának ellenőrzése – virtuális gép olvasása      | Microsoft.Compute/virtualMachines/read         | Virtuális gép         |
|Állapot-ellenőrzés – Fiók olvasása      | Microsoft.Automation/automationAccounts/read  |  Automation-fiók   |
| Munkaterület-ellenőrzés az<sup>1.</sup> virtuális géphez       | Microsoft.OperationalInsights/workspaces/read         | Előfizetés         |
| A Log Analytics-szolgáltató regisztrálása |Microsoft.Insights/register/action | Előfizetés|

<sup>1</sup> Erre az engedélyre szükség van a funkcióknak a virtuálisgép-portálon keresztüli engedélyezéséhez.

### <a name="permissions-for-enabling-update-management-and-change-tracking-and-inventory-from-an-automation-account"></a>Engedélyek a Update Management és változáskövetés és leltározás Automation-fiókból való engedélyezéséhez

|**Művelet**  |**Engedély** |**Minimális hatókör**  |
|---------|---------|---------|
|Új központi telepítés létrehozása     | Microsoft.Resources/deployments/*        | Előfizetés         |
|Új erőforráscsoport létrehozása     | Microsoft.Resources/subscriptions/resourceGroups/write         | Előfizetés        |
|AutomationOnboarding panel – Új munkaterület létrehozása     |Microsoft.OperationalInsights/workspaces/write           | Erőforráscsoport        |
|AutomationOnboarding panel – csatolt munkaterület olvasása     | Microsoft.Automation/automationAccounts/read        | Automation-fiók       |
|AutomationOnboarding panel – megoldás olvasása     | Microsoft.OperationalInsights/workspaces/intelligencepacks/read         | Megoldás        |
|AutomationOnboarding panel – munkaterület olvasása     | Microsoft.OperationalInsights/workspaces/intelligencepacks/read        | Munkaterület        |
|Munkaterület és fiók hivatkozásának létrehozása     | Microsoft.OperationalInsights/workspaces/write        | Munkaterület        |
|Fiók írása a shoeboxhoz      | Microsoft.Automation/automationAccounts/write        | Fiók        |
|Mentett keresés létrehozása/szerkesztése     | Microsoft.OperationalInsights/workspaces/write        | Munkaterület        |
|Hatókör-konfiguráció létrehozása/szerkesztése     | Microsoft.OperationalInsights/workspaces/write        | Munkaterület        |
| A Log Analytics-szolgáltató regisztrálása |Microsoft.Insights/register/action | Előfizetés|
|**2. lépés – Több virtuális gép engedélyezése**     |         |         |
|VMOnboarding panel – MMA-bővítmény létrehozása     | Microsoft.Compute/virtualMachines/write           | Virtuális gép        |
|Mentett keresés létrehozása/szerkesztése     | Microsoft.OperationalInsights/workspaces/write           | Munkaterület        |
|Hatókör-konfiguráció létrehozása/szerkesztése  | Microsoft.OperationalInsights/workspaces/write   | Munkaterület|

## <a name="update-management-permissions"></a>Frissítéskezelési engedélyek

A frissítéskezelés több szolgáltatás között is elérhető a szolgáltatás nyújtása érdekében. Az alábbi táblázat a frissítéskezelés központi telepítéseinek kezeléséhez szükséges engedélyeket mutatja be:

|**Erőforrás**  |**Szerepkör**  |**Hatókör**  |
|---------|---------|---------|
|Automation-fiók     | Log Analytics közreműködő       | Automation-fiók        |
|Automation-fiók    | Virtuális gépek közreműködője        | A fiók erőforráscsoportja        |
|Log Analytics-munkaterület     | Log Analytics közreműködő| Log Analytics-munkaterület        |
|Log Analytics-munkaterület |Log Analytics olvasó| Előfizetés|
|Megoldás     |Log Analytics közreműködő         | Megoldás|
|Virtuális gép     | Virtuális gépek közreműködője        | Virtuális gép        |

## <a name="configure-azure-rbac-for-your-automation-account"></a>Az Azure RBAC konfigurálása az Automation-fiókhoz

A következő szakasz bemutatja, hogyan konfigurálhatja az Azure RBAC-t az [Automation-fiókjában](#configure-azure-rbac-using-the-azure-portal) a Azure Portal [és a PowerShell használatával.](#configure-azure-rbac-using-powershell)

### <a name="configure-azure-rbac-using-the-azure-portal"></a>Az Azure RBAC konfigurálása a Azure Portal

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/), és nyissa meg az Automation-fiókját az Automation-fiókok lapról.
2. Kattintson a **Hozzáférés-vezérlés (IAM) elemre** a Hozzáférés-vezérlés (IAM) lap megnyitásához. Ezen a lapon új felhasználókat, csoportokat és alkalmazásokat adhat hozzá az Automation-fiók kezeléséhez és az Automation-fiókhoz konfigurálható meglévő szerepkörök megtekintéséhez.
3. Kattintson a **Szerepkör-hozzárendelések** fülre.

   ![Hozzáférés gomb](media/automation-role-based-access-control/automation-01-access-button.png)

#### <a name="add-a-new-user-and-assign-a-role"></a>Új felhasználó hozzáadása és szerepkör hozzárendelése

1. A Hozzáférés-vezérlés (IAM) lapon kattintson **a + Szerepkör-hozzárendelés hozzáadása elemre.** Ez a művelet megnyitja a Szerepkör-hozzárendelés hozzáadása lapot, ahol hozzáadhat egy felhasználót, csoportot vagy alkalmazást, és hozzárendelhet egy megfelelő szerepkört.

2. Válasszon egy szerepkört az elérhető szerepkörök listájáról. Kiválaszthatja az Automation-fiók által támogatott bármelyik elérhető beépített szerepkört, vagy bármilyen egyéni szerepkört, amit esetleg definiált.

3. Írja be annak a felhasználónak a nevét, aki számára engedélyeket kíván adni a **Kijelölés mezőben.** Válassza ki a listából a felhasználót, és kattintson a **Mentés gombra.**

   ![Felhasználók hozzáadása](media/automation-role-based-access-control/automation-04-add-users.png)

   Most látnia kell, hogy a felhasználó hozzá van adva a Felhasználók laphoz, és hozzá van rendelve a kiválasztott szerepkör.

   ![Felhasználók listázása](media/automation-role-based-access-control/automation-05-list-users.png)

   A felhasználóhoz a Szerepkörök lapról is hozzárendelhet szerepkört.

4. A **Hozzáférés-vezérlés** (IAM) lapon kattintson a Szerepkörök elemre a Szerepkörök lap megnyitásához. Megtekintheti a szerepkör nevét, valamint a szerepkörhöz rendelt felhasználók és csoportok számát.

    ![Szerepkör hozzárendelése a Felhasználók lapról](media/automation-role-based-access-control/automation-06-assign-role-from-users-blade.png)

   > [!NOTE]
   > Szerepköralapú hozzáférés-vezérlést csak az Automation-fiók hatókörében állíthat be, az Automation-fiók alatti erőforrásoknál nem.

#### <a name="remove-a-user"></a>Felhasználó eltávolítása

Eltávolíthatja egy olyan felhasználó hozzáférési engedélyét, aki nem az Automation-fiókot kezeli, vagy aki már nem a szervezetnél dolgozik. Alább láthatók egy felhasználó eltávolításának lépései:

1. A Hozzáférés-vezérlés (IAM) lapon válassza ki az eltávolítani kívánt felhasználót, majd kattintson az **Eltávolítás gombra.**
2. A hozzárendelés részleteit megjelenítő lapon kattintson az **Eltávolítás** gombra.
3. Az **Igen** gombra kattintva erősítse meg az eltávolítást.

   ![Felhasználók eltávolítása](media/automation-role-based-access-control/automation-08-remove-users.png)

### <a name="configure-azure-rbac-using-powershell"></a>Az Azure RBAC konfigurálása a PowerShell használatával

Az Automation-fiók szerepköralapú hozzáférését a következő parancsmagokkal [Azure PowerShell konfigurálhatja:](../role-based-access-control/role-assignments-powershell.md)

[A Get-AzRoleDefinition](/powershell/module/Az.Resources/Get-AzRoleDefinition) az összes olyan Azure-szerepkört felsorolja, amely elérhető a Azure Active Directory. Ezt a parancsmagot a paraméterrel együtt használva felsorolhatja az adott szerepkör `Name` által végrehajtható összes műveletet.

```azurepowershell-interactive
Get-AzRoleDefinition -Name 'Automation Operator'
```

A következő példakimenetet mutatjuk be:

```azurepowershell
Name             : Automation Operator
Id               : d3881f73-407a-4167-8283-e981cbba0404
IsCustom         : False
Description      : Automation Operators are able to start, stop, suspend, and resume jobs
Actions          : {Microsoft.Authorization/*/read, Microsoft.Automation/automationAccounts/jobs/read, Microsoft.Automation/automationAccounts/jobs/resume/action,
                   Microsoft.Automation/automationAccounts/jobs/stop/action...}
NotActions       : {}
AssignableScopes : {/}
```

[A Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) a megadott hatókörben található Azure-beli szerepkör-hozzárendeléseket sorolja fel. Paraméterek nélkül ez a parancsmag az előfizetésben történt összes szerepkör-hozzárendelést visszaadja. Használja a paramétert a megadott felhasználó, valamint azon csoportok hozzáférés-hozzárendelésének listához, amelyekhez a `ExpandPrincipalGroups` felhasználó tartozik.

**Példa:** A következő parancsmag használatával listizálja az Automation-fiókban található összes felhasználót és azok szerepkörét.

```azurepowershell-interactive
Get-AzRoleAssignment -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

A következő példakimenetet mutatjuk be:

```powershell
RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Automation/automationAccounts/myAutomationAccount/provid
                     ers/Microsoft.Authorization/roleAssignments/cc594d39-ac10-46c4-9505-f182a355c41f
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Automation/automationAccounts/myAutomationAccount
DisplayName        : admin@contoso.com
SignInName         : admin@contoso.com
RoleDefinitionName : Automation Operator
RoleDefinitionId   : d3881f73-407a-4167-8283-e981cbba0404
ObjectId           : 15f26a47-812d-489a-8197-3d4853558347
ObjectType         : User
```

A [New-AzRoleAssignment](/powershell/module/Az.Resources/New-AzRoleAssignment) használatával felhasználókhoz, csoportokhoz és alkalmazásokhoz rendelhet hozzáférést egy adott hatókörben.

**Példa:** Az alábbi paranccsal rendelheti hozzá az "Automation-operátor" szerepkört az Automation-fiók hatókörében szereplő felhasználókhoz.

```azurepowershell-interactive
New-AzRoleAssignment -SignInName <sign-in Id of a user you wish to grant access> -RoleDefinitionName 'Automation operator' -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

A következő példakimenetet mutatjuk be:

```azurepowershell
RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/myResourceGroup/Providers/Microsoft.Automation/automationAccounts/myAutomationAccount/provid
                     ers/Microsoft.Authorization/roleAssignments/25377770-561e-4496-8b4f-7cba1d6fa346
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/myResourceGroup/Providers/Microsoft.Automation/automationAccounts/myAutomationAccount
DisplayName        : admin@contoso.com
SignInName         : admin@contoso.com
RoleDefinitionName : Automation Operator
RoleDefinitionId   : d3881f73-407a-4167-8283-e981cbba0404
ObjectId           : f5ecbe87-1181-43d2-88d5-a8f5e9d8014e
ObjectType         : User
```

A [Remove-AzRoleAssignment használatával](/powershell/module/Az.Resources/Remove-AzRoleAssignment) eltávolíthatja egy adott felhasználó, csoport vagy alkalmazás hozzáférését egy adott hatókörből.

**Példa:** A következő paranccsal távolítsa el a felhasználót az Automation-operátor szerepkörből az Automation-fiók hatókörében.

```azurepowershell-interactive
Remove-AzRoleAssignment -SignInName <sign-in Id of a user you wish to remove> -RoleDefinitionName 'Automation Operator' -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

Az előző példában cserélje le a `sign-in ID of a user you wish to remove` , `SubscriptionID` , és `Resource Group Name` adatokat a fiók `Automation account name` adataira. Ha a **rendszer** megerősítést kér, válassza az Igen lehetőséget, mielőtt folytatná a felhasználói szerepkör-hozzárendelések eltávolítását.

### <a name="user-experience-for-automation-operator-role---automation-account"></a>Automation-operátori szerepkör felhasználói élménye – Automation-fiók

Ha az Automation-fiók hatókörében az Automation-operátor szerepkörhöz rendelt felhasználó megtekinti azt az Automation-fiókot, amelyhez hozzá van rendelve, a felhasználó csak az Automation-fiókban létrehozott runbookok, runbook-feladatok és ütemezések listáját tudja megtekinteni. Ez a felhasználó nem tudja megtekinteni ezeknek az elemeknek a definícióit. A felhasználó elindíthatja, leállíthatja, felfüggesztheti, folytathatja vagy ütemezheti a Runbook-feladatot. A felhasználónak azonban nincs hozzáférése más Automation-erőforrásokhoz, például konfigurációkhoz, hibrid feldolgozócsoportokhoz vagy DSC-csomópontokhoz.

![Nincs hozzáférése az erőforrásokhoz](media/automation-role-based-access-control/automation-10-no-access-to-resources.png)

## <a name="configure-azure-rbac-for-runbooks"></a>Az Azure RBAC konfigurálása runbookok számára

Azure Automation lehetővé teszi, hogy Azure-szerepköröket rendeljen adott runbookhoz. Ehhez futtassa a következő szkriptet egy felhasználó adott runbookhoz való hozzáadásához. Ezt a szkriptet egy Automation-Bérlői rendszergazda rendszergazda vagy egy rendszergazda futtathatja.

```azurepowershell-interactive
$rgName = "<Resource Group Name>" # Resource Group name for the Automation account
$automationAccountName ="<Automation account name>" # Name of the Automation account
$rbName = "<Name of Runbook>" # Name of the runbook
$userId = "<User ObjectId>" # Azure Active Directory (AAD) user's ObjectId from the directory

# Gets the Automation account resource
$aa = Get-AzResource -ResourceGroupName $rgName -ResourceType "Microsoft.Automation/automationAccounts" -ResourceName $automationAccountName

# Get the Runbook resource
$rb = Get-AzResource -ResourceGroupName $rgName -ResourceType "Microsoft.Automation/automationAccounts/runbooks" -ResourceName "$rbName"

# The Automation Job Operator role only needs to be run once per user.
New-AzRoleAssignment -ObjectId $userId -RoleDefinitionName "Automation Job Operator" -Scope $aa.ResourceId

# Adds the user to the Automation Runbook Operator role to the Runbook scope
New-AzRoleAssignment -ObjectId $userId -RoleDefinitionName "Automation Runbook Operator" -Scope $rb.ResourceId
```

A szkript futtatása után jelentkezzen be a felhasználóval a Azure Portal válassza a Minden erőforrás **lehetőséget.** A listában a felhasználó láthatja azt a runbookot, amelyhez Automation Runbook-operátorként hozzá lett adva.

![Runbook Azure RBAC a portálon](./media/automation-role-based-access-control/runbook-rbac.png)

### <a name="user-experience-for-automation-operator-role---runbook"></a>Az Automation-operátori szerepkör felhasználói élménye – Runbook

Ha a Runbook hatókörében az Automation-operátor szerepkörhöz rendelt felhasználó megtekint egy hozzárendelt runbookot, a felhasználó csak a runbookot tudja elindítani és megtekinteni a runbook-feladatokat.

![Csak az indításhoz van hozzáférése](media/automation-role-based-access-control/automation-only-start.png)

## <a name="next-steps"></a>Következő lépések

* További információ az Azure RBAC-ről a PowerShell használatával: Azure-beli szerepkör-hozzárendelések hozzáadása vagy eltávolítása [a Azure PowerShell.](../role-based-access-control/role-assignments-powershell.md)
* A runbookok típusaival kapcsolatos részletekért lásd: Azure Automation [runbooktípusok.](automation-runbook-types.md)
* Runbookok futtatásához [lásd: Runbook futtatása a Azure Automation.](start-runbooks.md)
