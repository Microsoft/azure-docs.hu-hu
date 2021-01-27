---
title: A szerepkör engedélyeinek és biztonságának kezelése Azure Automation
description: Ez a cikk az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatát ismerteti, amely lehetővé teszi az Azure-erőforrások hozzáférés-kezelését.
keywords: automation rbac, szerepköralapú hozzáférés-vezérlés, azure rbac
services: automation
ms.subservice: shared-capabilities
ms.date: 07/21/2020
ms.topic: conceptual
ms.openlocfilehash: 320668f9596376cf7aa12ed97872671404a07658
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98895917"
---
# <a name="manage-role-permissions-and-security"></a>Szerepköri engedélyek és biztonság kezelése

Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) lehetővé teszi az Azure-erőforrások hozzáférés-kezelését. Az [Azure RBAC](../role-based-access-control/overview.md)segítségével elkülönítheti a feladatait a csapaton belül, és csak a felhasználókhoz, csoportokhoz és alkalmazásokhoz férhet hozzá, amelyekre szükségük van a feladatok elvégzéséhez. Szerepköralapú hozzáférést biztosíthat a felhasználóknak a Azure Portal, az Azure Command-Line Tools vagy az Azure felügyeleti API-k használatával.

## <a name="roles-in-automation-accounts"></a>Az Automation-fiókok szerepkörei

Azure Automation a hozzáférést a megfelelő Azure-szerepkörnek a felhasználók, csoportok és alkalmazások számára az Automation-fiók hatókörében való hozzárendelésével lehet megadni. Alább láthatók az Automation-fiók által támogatott beépített szerepkörök:

| **Szerepkör** | **Leírás** |
|:--- |:--- |
| Tulajdonos |A tulajdonosi szerepkör lehetővé teszi az Automation-fiókon belüli összes erőforrás és művelet elérését, beleértve az Automation-fiók kezeléséhez szükséges más felhasználókhoz, csoportokhoz és alkalmazásokhoz való hozzáférést is. |
| Közreműködő |A közreműködői szerepkör segítségével minden elemet kezelhet, csak más felhasználók Automation-fiókokra vonatkozó hozzáférési jogosultságait nem módosíthatja. |
| Olvasó |Az olvasói szerep lehetővé teszi az összes erőforrás megtekintését egy Automation-fiókban, de módosítás nem hajtható vele végre. |
| Automation-operátor |Az Automation-operátori szerepkör lehetővé teszi a runbook nevének és tulajdonságainak megtekintését, valamint feladatok létrehozását és kezelését egy Automation-fiókban lévő összes runbookok. Ez a szerepkör akkor hasznos, ha az Automation-fiók erőforrásait, például a hitelesítő adatokat és a runbookok szeretné megtekinteni vagy módosítani, de továbbra is lehetővé teszi, hogy a szervezet tagjai végrehajtsák ezeket a runbookok. |
|Automation-feladatok operátora|Az Automation-feladat operátori szerepkör lehetővé teszi feladatok létrehozását és kezelését egy Automation-fiókban lévő összes runbookok.|
|Automation Runbook operátor|Az Automation Runbook-kezelő szerepkör lehetővé teszi a Runbook nevének és tulajdonságainak megtekintését.|
| Log Analytics közreműködő | A Log Analytics közreműködő szerepkör lehetővé teszi az összes figyelési adat olvasását és a figyelési beállítások szerkesztését. A figyelési beállítások szerkesztése magában foglalja a virtuálisgép-bővítmény virtuális gépekhez való hozzáadását, a Storage-fiók kulcsainak beolvasását, amelyekkel konfigurálhatja az Azure Storage-ból származó naplók gyűjtését, automatizálhatja és konfigurálhatja az Automation-fiókokat, Azure Automation szolgáltatásokat adhat hozzá, és az Azure|
| Log Analytics olvasó | A Log Analytics olvasó szerepkör lehetővé teszi az összes figyelési adat megtekintését és keresését, valamint a figyelési beállítások megtekintését. Ez magában foglalja az Azure Diagnostics konfigurációjának megtekintését az összes Azure-erőforráson. |
| Közreműködő figyelése | A figyelési közreműködő szerepkör lehetővé teszi az összes figyelési adat olvasását és a figyelési beállítások frissítését.|
| Figyelő olvasó | A figyelési olvasó szerepkör lehetővé teszi az összes figyelési adat olvasását. |
| Felhasználói hozzáférés rendszergazdája |A felhasználói hozzáférés rendszergazdájának szerepköre lehetővé teszi, hogy kezelje a felhasználói hozzáférést az Azure Automation-fiókokhoz. |

## <a name="role-permissions"></a>Szerepkör-engedélyek

A következő táblázatok ismertetik az egyes szerepkörökhöz adott engedélyeket. Ebbe beletartozhatnak olyan műveletek, amelyek az engedélyeket és a nem megfelelő jogosultságokat biztosítanak.

### <a name="owner"></a>Tulajdonos

A tulajdonosok mindent kezelhetnek, beleértve a hozzáférést is. A következő táblázat a szerepkörhöz megadott engedélyeket tartalmazza:

|Műveletek|Leírás|
|---|---|
|Microsoft. Automation/automationAccounts/|Minden típusú erőforrás létrehozása és kezelése.|

### <a name="contributor"></a>Közreműködő

A közreműködők A hozzáférés kivételével mindent kezelhetnek. A következő táblázat a szerepkörhöz megadott és megtagadott engedélyeket tartalmazza:

|**Műveletek**  |**Leírás**  |
|---------|---------|
|Microsoft. Automation/automationAccounts/|Minden típusú erőforrás létrehozása és kezelése|
|**Nem műveletek**||
|Microsoft. Authorization/*/delete| Szerepkörök és szerepkör-hozzárendelések törlése.       |
|Microsoft. Authorization/*/Write     |  Szerepkörök és szerepkör-hozzárendelések létrehozása.       |
|Microsoft. Authorization/elevateAccess/művelet    | Megtagadja a felhasználói hozzáférés rendszergazdájának létrehozását.       |

### <a name="reader"></a>Olvasó

Az olvasó megtekintheti egy Automation-fiók összes erőforrását, de nem végezhet módosításokat.

|**Műveletek**  |**Leírás**  |
|---------|---------|
|Microsoft. Automation/automationAccounts/READ|Egy Automation-fiók összes erőforrásának megtekintése. |

### <a name="automation-operator"></a>Automation-operátor

Az Automation-operátorok feladatokat hozhatnak létre és kezelhetnek, valamint runbook-neveket és-tulajdonságokat olvashatnak az Automation-fiókokban lévő összes runbookok.

>[!NOTE]
>Ha a kezelőt egyéni runbookok szeretné vezérelni, akkor ne állítsa be ezt a szerepkört. Ehelyett használja az **Automation-feladatok kezelőjét** és az **Automation Runbook operátori** szerepkört.

A következő táblázat a szerepkörhöz megadott engedélyeket tartalmazza:

|**Műveletek**  |**Leírás**  |
|---------|---------|
|Microsoft. Authorization/*/READ|Olvasási engedély.|
|Microsoft. Automation/automationAccounts/hybridRunbookWorkerGroups/READ|Hibrid Runbook Worker-erőforrások olvasása.|
|Microsoft. Automation/automationAccounts/feladatok/olvasás|A runbook feladatok listázása.|
|Microsoft. Automation/automationAccounts/feladatok/folytatás/művelet|Szüneteltetett feladat folytatása.|
|Microsoft. Automation/automationAccounts/feladatok/leállítás/művelet|Feladat megszakítása folyamatban.|
|Microsoft. Automation/automationAccounts/feladatok/streamek/olvasás|Olvassa el a feladatok adatfolyamait és kimenetét.|
|Microsoft. Automation/automationAccounts/feladatok/kimenet/olvasás|A feladatok kimenetének beolvasása.|
|Microsoft. Automation/automationAccounts/Jobs/felfüggesztés/művelet|Feladat felfüggesztése folyamatban.|
|Microsoft. Automation/automationAccounts/feladatok/írás|Hozzon létre feladatokat.|
|Microsoft. Automation/automationAccounts/jobSchedules/READ|Azure Automation-feladatütemezés beolvasása.|
|Microsoft. Automation/automationAccounts/jobSchedules/Write|Azure Automation feladatok ütemtervének létrehozása|
|Microsoft. Automation/automationAccounts/linkedWorkspace/READ|Az Automation-fiókhoz kapcsolódó munkaterület beolvasása.|
|Microsoft. Automation/automationAccounts/READ|Azure Automation fiók beszerzése.|
|Microsoft. Automation/automationAccounts/runbookok/READ|Azure Automation runbook beolvasása.|
|Microsoft. Automation/automationAccounts/ütemezett/beolvasás|Azure Automation ütemezett eszköz beolvasása.|
|Microsoft. Automation/automationAccounts/ütemterv/írás|Azure Automation ütemezett eszköz létrehozása vagy frissítése.|
|Microsoft. Resources/Subscriptions/resourceGroups/READ      |Szerepkörök és szerepkör-hozzárendelések olvasása.         |
|Microsoft. Resources/üzemelő példány/*      |Erőforráscsoport-telepítések létrehozása és kezelése.         |
|Microsoft. bepillantások/alertRules/*      | Riasztási szabályok létrehozása és kezelése.        |
|Microsoft. support/* |Támogatási jegyek létrehozása és kezelése.|

### <a name="automation-job-operator"></a>Automation-feladatok operátora

Az Automation-feladatok operátori szerepköre az Automation-fiók hatókörében adható meg.Ez lehetővé teszi, hogy az operátor engedélyeket hozzon létre és kezeljen a fiók összes runbookok. Ha a feladat-operátori szerepkör olvasási jogosultságokat kap az Automation-fiókot tartalmazó erőforráscsoporthoz, a szerepkör tagjai képesek elindítani a runbookok. Azonban nem képesek létrehozni, szerkeszteni vagy törölni őket.

A következő táblázat a szerepkörhöz megadott engedélyeket tartalmazza:

|**Műveletek**  |**Leírás**  |
|---------|---------|
|Microsoft. Authorization/*/READ|Olvasási engedély.|
|Microsoft. Automation/automationAccounts/feladatok/olvasás|A runbook feladatok listázása.|
|Microsoft. Automation/automationAccounts/feladatok/folytatás/művelet|Szüneteltetett feladat folytatása.|
|Microsoft. Automation/automationAccounts/feladatok/leállítás/művelet|Feladat megszakítása folyamatban.|
|Microsoft. Automation/automationAccounts/feladatok/streamek/olvasás|Olvassa el a feladatok adatfolyamait és kimenetét.|
|Microsoft. Automation/automationAccounts/Jobs/felfüggesztés/művelet|Feladat felfüggesztése folyamatban.|
|Microsoft. Automation/automationAccounts/feladatok/írás|Hozzon létre feladatokat.|
|Microsoft. Resources/Subscriptions/resourceGroups/READ      |  Szerepkörök és szerepkör-hozzárendelések olvasása.       |
|Microsoft. Resources/üzemelő példány/*      |Erőforráscsoport-telepítések létrehozása és kezelése.         |
|Microsoft. bepillantások/alertRules/*      | Riasztási szabályok létrehozása és kezelése.        |
|Microsoft. support/* |Támogatási jegyek létrehozása és kezelése.|

### <a name="automation-runbook-operator"></a>Automation Runbook operátor

A Runbook-hatókörben az Automation Runbook operátori szerepkört kell megadni. Az Automation Runbook-kezelők megtekinthetik a Runbook nevét és tulajdonságait.Ez a szerepkör az **Automation-feladat operátori** szerepkörével együtt lehetővé teszi, hogy az operátor létrehozza és felügyelje a runbook feladatait is. A következő táblázat a szerepkörhöz megadott engedélyeket tartalmazza:

|**Műveletek**  |**Leírás**  |
|---------|---------|
|Microsoft. Automation/automationAccounts/runbookok/READ     | A runbookok listázása.        |
|Microsoft. Authorization/*/READ      | Olvasási engedély.        |
|Microsoft. Resources/Subscriptions/resourceGroups/READ      |Szerepkörök és szerepkör-hozzárendelések olvasása.         |
|Microsoft. Resources/üzemelő példány/*      | Erőforráscsoport-telepítések létrehozása és kezelése.         |
|Microsoft. bepillantások/alertRules/*      | Riasztási szabályok létrehozása és kezelése.        |
|Microsoft. support/*      | Támogatási jegyek létrehozása és kezelése.        |

### <a name="log-analytics-contributor"></a>Log Analytics közreműködő

Egy Log Analytics közreműködő az összes figyelési és a figyelési beállítások szerkesztésével képes olvasni. A figyelési beállítások szerkesztése magában foglalja a virtuálisgép-bővítmény virtuális gépekhez való hozzáadását. a Storage-fiók kulcsainak beolvasása az Azure Storage-ból származó naplók gyűjtésének konfigurálásához; Automation-fiókok létrehozása és konfigurálása; szolgáltatások hozzáadása; és az Azure Diagnostics konfigurálása az összes Azure-erőforráshoz. A következő táblázat a szerepkörhöz megadott engedélyeket tartalmazza:

|**Műveletek**  |**Leírás**  |
|---------|---------|
|*/read|Az összes típusú erőforrás beolvasása, kivéve a titkokat.|
|Microsoft. Automation/automationAccounts/*|Automation-fiókok kezelése.|
|Microsoft. ClassicCompute/virtualMachines/Extensions/*|Virtuálisgép-bővítmények létrehozása és kezelése.|
|Microsoft. ClassicStorage/storageAccounts/Listkeys műveletének beolvasása/művelet|Klasszikus Storage-fiók kulcsainak listázása.|
|Microsoft. számítás/virtualMachines/Extensions/*|Klasszikus virtuálisgép-bővítmények létrehozása és kezelése.|
|Microsoft. bepillantások/alertRules/*|Riasztási szabályok olvasása/írása/törlése.|
|Microsoft. bepillantások/diagnosticSettings/*|Diagnosztikai beállítások olvasása/írása/törlése.|
|Microsoft. OperationalInsights/*|Azure Monitor naplók kezelése.|
|Microsoft. OperationsManagement/*|A munkaterületek Azure Automation funkcióinak kezelése.|
|Microsoft. Resources/üzemelő példány/*|Erőforráscsoport-telepítések létrehozása és kezelése.|
|Microsoft. Resources/Subscriptions/resourcegroups/Deployments/*|Erőforráscsoport-telepítések létrehozása és kezelése.|
|Microsoft. Storage/storageAccounts/Listkeys műveletének beolvasása/művelet|A Storage-fiók kulcsainak listázása.|
|Microsoft. support/*|Támogatási jegyek létrehozása és kezelése.|

### <a name="log-analytics-reader"></a>Log Analytics olvasó

Egy Log Analytics olvasó megtekintheti és megkeresheti az összes figyelési adatait, valamint megtekintheti a figyelési beállításokat, beleértve az Azure Diagnostics konfigurációjának megtekintését az összes Azure-erőforráson. A következő táblázat a szerepkörhöz megadott vagy megtagadott engedélyeket tartalmazza:

|**Műveletek**  |**Leírás**  |
|---------|---------|
|*/read|Az összes típusú erőforrás beolvasása, kivéve a titkokat.|
|Microsoft. OperationalInsights/munkaterületek/elemzés/lekérdezés/művelet|Azure Monitor naplókban lévő lekérdezések kezelése.|
|Microsoft. OperationalInsights/munkaterületek/keresés/művelet|Keressen Azure Monitor naplózási adatként.|
|Microsoft. support/*|Támogatási jegyek létrehozása és kezelése.|
|**Nem műveletek**| |
|Microsoft. OperationalInsights/munkaterületek/sharedKeys/olvasás|Nem sikerült beolvasni a közös hozzáférési kulcsokat.|

### <a name="monitoring-contributor"></a>Közreműködő figyelése

Egy figyelő közreműködői beolvashatja az összes figyelési és frissítési figyelési beállítást. A következő táblázat a szerepkörhöz megadott engedélyeket tartalmazza:

|**Műveletek**  |**Leírás**  |
|---------|---------|
|*/read|Az összes típusú erőforrás beolvasása, kivéve a titkokat.|
|Microsoft. AlertsManagement/riasztások/*|Riasztások kezelése.|
|Microsoft. AlertsManagement/alertsSummary/*|A riasztási irányítópult kezelése.|
|Microsoft. bepillantások/AlertRules/*|Riasztási szabályok kezelése.|
|Microsoft. bepillantások/összetevők/*|Application Insights-összetevők kezelése.|
|Microsoft. bepillantások/DiagnosticSettings/*|Diagnosztikai beállítások kezelése.|
|Microsoft. bepillantások/eventtypes/*|Az előfizetésben szereplő tevékenység-naplózási események (kezelési események) listázása. Ez az engedély mind a programozási, mind a portálhoz való hozzáférésre alkalmazható a tevékenység naplójában.|
|Microsoft. bepillantások/LogDefinitions/*|Ez az engedély olyan felhasználók számára szükséges, akiknek a portálon keresztül kell hozzáférnie a tevékenység naplóihoz. A műveletnapló kategóriáinak listázása a tevékenység naplójában.|
|Microsoft. bepillantások/MetricDefinitions/*|A metrikai definíciók (az adott erőforráshoz elérhető metrikai típusok listája) olvasása.|
|Microsoft. bepillantások/mérőszámok/*|Erőforrás metrikáinak olvasása.|
|Microsoft. elemzések/regisztráció/művelet|Regisztrálja a Microsoft. bepillantások szolgáltatóját.|
|Microsoft. bepillantások/webteszt/*|Application Insights webes tesztek kezelése.|
|Microsoft. OperationalInsights/munkaterületek/intelligencepacks/*|Kezelheti Azure Monitor naplók megoldásának csomagjait.|
|Microsoft. OperationalInsights/munkaterületek/savedSearches/*|Azure Monitor naplókat mentett keresések kezelése.|
|Microsoft. OperationalInsights/munkaterületek/keresés/művelet|Keressen Log Analytics munkaterületeket.|
|Microsoft. OperationalInsights/munkaterületek/sharedKeys/művelet|Log Analytics munkaterület kulcsainak listázása.|
|Microsoft. OperationalInsights/munkaterületek/storageinsightconfigs/*|A Azure Monitor naplózza a tárolási Insight-konfigurációkat.|
|Microsoft. support/*|Támogatási jegyek létrehozása és kezelése.|
|Microsoft. WorkloadMonitor/munkaterhelések/*|Munkaterhelések kezelése.|

### <a name="monitoring-reader"></a>Figyelő olvasó

A figyelési olvasó az összes figyelési adattal képes olvasni. A következő táblázat a szerepkörhöz megadott engedélyeket tartalmazza:

|**Műveletek**  |**Leírás**  |
|---------|---------|
|*/read|Az összes típusú erőforrás beolvasása, kivéve a titkokat.|
|Microsoft. OperationalInsights/munkaterületek/keresés/művelet|Keressen Log Analytics munkaterületeket.|
|Microsoft. support/*|Támogatási jegyek létrehozása és kezelése|

### <a name="user-access-administrator"></a>Felhasználói hozzáférés rendszergazdája

A felhasználói hozzáférés rendszergazdája az Azure-erőforrásokhoz való felhasználói hozzáférést is képes kezelni. A következő táblázat a szerepkörhöz megadott engedélyeket tartalmazza:

|**Műveletek**  |**Leírás**  |
|---------|---------|
|*/read|Az összes erőforrás olvasása|
|Microsoft. Authorization/*|Engedélyezés kezelése|
|Microsoft. support/*|Támogatási jegyek létrehozása és kezelése|

## <a name="feature-setup-permissions"></a>Szolgáltatás telepítési engedélyei

A következő szakaszok ismertetik a Update Management és a Change Tracking és a leltár funkcióinak engedélyezéséhez szükséges minimális engedélyeket.

### <a name="permissions-for-enabling-update-management-and-change-tracking-and-inventory-from-a-vm"></a>Engedélyek a virtuális gépek Update Management és Change Tracking és leltárának engedélyezéséhez

|**Művelet**  |**Engedély**  |**Minimális hatókör**  |
|---------|---------|---------|
|Új központi telepítés írása      | Microsoft. Resources/üzemelő példány/*          |Előfizetés          |
|Új erőforráscsoport írása      | Microsoft. Resources/Subscriptions/resourceGroups/Write        | Előfizetés          |
|Új alapértelmezett munkaterület létrehozása      | Microsoft. OperationalInsights/munkaterületek/írás         | Erőforráscsoport         |
|Új fiók létrehozása      |  Microsoft. Automation/automationAccounts/írás        |Erőforráscsoport         |
|Munkaterület és fiók összekapcsolása      |Microsoft. OperationalInsights/munkaterületek/írás</br>Microsoft. Automation/automationAccounts/READ|Munkaterület</br>Automation-fiók
|MMA-bővítmény létrehozása      | Microsoft. számítás/virtualMachines/írás         | Virtuális gép         |
|Mentett keresés létrehozása      | Microsoft. OperationalInsights/munkaterületek/írás          | Munkaterület         |
|Hatókör-konfiguráció létrehozása      | Microsoft. OperationalInsights/munkaterületek/írás          | Munkaterület         |
|Bevezetési állapot-ellenőrzési munkaterület      | Microsoft. OperationalInsights/munkaterületek/olvasás         | Munkaterület         |
|Bevezetési állapot – a fiók csatolt munkaterület-tulajdonságának beolvasása     | Microsoft. Automation/automationAccounts/READ      | Automation-fiók        |
|Bevezetési állapot-ellenőrzési megoldás      | Microsoft. OperationalInsights/munkaterületek/intelligencepacks/olvasás          | Megoldás         |
|Bevezetési állapot-ellenőrzési virtuális gép      | Microsoft. számítás/virtualMachines/olvasás         | Virtuális gép         |
|Bevezetési állapot-ellenőrzési fiók      | Microsoft. Automation/automationAccounts/READ  |  Automation-fiók   |
| Az<sup>1</sup> . virtuális gép előkészítési munkaterületének keresése       | Microsoft. OperationalInsights/munkaterületek/olvasás         | Előfizetés         |
| A Log Analytics-szolgáltató regisztrálása |Microsoft. elemzések/regisztráció/művelet | Előfizetés|

<sup>1</sup> ez az engedély szükséges a szolgáltatások virtuálisgép-portálon keresztüli engedélyezéséhez.

### <a name="permissions-for-enabling-update-management-and-change-tracking-and-inventory-from-an-automation-account"></a>Engedélyek a Update Management és a Change Tracking és a leltár automatizálási fiókból való engedélyezéséhez

|**Művelet**  |**Engedély** |**Minimális hatókör**  |
|---------|---------|---------|
|Új központi telepítés létrehozása     | Microsoft. Resources/üzemelő példány/*        | Előfizetés         |
|Új erőforráscsoport létrehozása     | Microsoft. Resources/Subscriptions/resourceGroups/Write         | Előfizetés        |
|AutomationOnboarding panel – Új munkaterület létrehozása     |Microsoft. OperationalInsights/munkaterületek/írás           | Erőforráscsoport        |
|AutomationOnboarding panel – csatolt munkaterület olvasása     | Microsoft. Automation/automationAccounts/READ        | Automation-fiók       |
|AutomationOnboarding panel – olvasási megoldás     | Microsoft. OperationalInsights/munkaterületek/intelligencepacks/olvasás         | Megoldás        |
|AutomationOnboarding panel – munkaterület olvasása     | Microsoft. OperationalInsights/munkaterületek/intelligencepacks/olvasás        | Munkaterület        |
|Munkaterület és fiók hivatkozásának létrehozása     | Microsoft. OperationalInsights/munkaterületek/írás        | Munkaterület        |
|Shoebox-fiók írása      | Microsoft. Automation/automationAccounts/írás        | Fiók        |
|Mentett keresés létrehozása/szerkesztése     | Microsoft. OperationalInsights/munkaterületek/írás        | Munkaterület        |
|Hatókör-konfiguráció létrehozása/szerkesztése     | Microsoft. OperationalInsights/munkaterületek/írás        | Munkaterület        |
| A Log Analytics-szolgáltató regisztrálása |Microsoft. elemzések/regisztráció/művelet | Előfizetés|
|**2. lépés – több virtuális gép engedélyezése**     |         |         |
|VMOnboarding panel – MMA-bővítmény létrehozása     | Microsoft. számítás/virtualMachines/írás           | Virtuális gép        |
|Mentett keresés létrehozása/szerkesztése     | Microsoft. OperationalInsights/munkaterületek/írás           | Munkaterület        |
|Hatókör-konfiguráció létrehozása/szerkesztése  | Microsoft. OperationalInsights/munkaterületek/írás   | Munkaterület|

## <a name="update-management-permissions"></a>Felügyeleti engedélyek frissítése

Az Update Management több szolgáltatásra is kiterjed a szolgáltatás nyújtásához. A következő táblázat az Update Management-telepítések kezeléséhez szükséges engedélyeket mutatja be:

|**Erőforrás**  |**Szerepkör**  |**Hatókör**  |
|---------|---------|---------|
|Automation-fiók     | Log Analytics közreműködő       | Automation-fiók        |
|Automation-fiók    | Virtuális gépek közreműködője        | A fiókhoz tartozó erőforráscsoport        |
|Log Analytics-munkaterület     | Log Analytics közreműködő| Log Analytics-munkaterület        |
|Log Analytics-munkaterület |Log Analytics olvasó| Előfizetés|
|Megoldás     |Log Analytics közreműködő         | Megoldás|
|Virtuális gép     | Virtuális gépek közreműködője        | Virtuális gép        |

## <a name="configure-azure-rbac-for-your-automation-account"></a>Az Azure RBAC konfigurálása az Automation-fiókhoz

A következő szakasz bemutatja, hogyan konfigurálhatja az Azure RBAC az Automation-fiókjában a [Azure Portal](#configure-azure-rbac-using-the-azure-portal) és a [PowerShell](#configure-azure-rbac-using-powershell)használatával.

### <a name="configure-azure-rbac-using-the-azure-portal"></a>Az Azure-RBAC konfigurálása a Azure Portal használatával

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/), és nyissa meg az Automation-fiókját az Automation-fiókok lapról.
2. Kattintson a **hozzáférés-vezérlés (iam)** elemre a hozzáférés-vezérlés (iam) lap megnyitásához. Ezen a lapon hozzáadhat új felhasználókat, csoportokat és alkalmazásokat az Automation-fiók kezeléséhez, és megtekintheti az Automation-fiókhoz konfigurálható meglévő szerepköröket.
3. Kattintson a **Szerepkör-hozzárendelések** fülre.

   ![Hozzáférés gomb](media/automation-role-based-access-control/automation-01-access-button.png)

#### <a name="add-a-new-user-and-assign-a-role"></a>Új felhasználó hozzáadása és szerepkör hozzárendelése

1. A hozzáférés-vezérlés (IAM) lapon kattintson a **+ szerepkör-hozzárendelés hozzáadása** lehetőségre. Ez a művelet megnyitja a szerepkör-hozzárendelés hozzáadása lapot, ahol hozzáadhat egy felhasználót, csoportot vagy alkalmazást, és hozzárendelheti a megfelelő szerepkört.

2. Válasszon egy szerepkört az elérhető szerepkörök listájáról. Kiválaszthatja az Automation-fiók által támogatott elérhető beépített szerepköröket, illetve az Ön által definiált egyéni szerepkört is.

3. Írja be annak a felhasználónak a nevét, akinek engedélyeket szeretne adni a **Select** mezőben. Válassza ki a felhasználót a listából, és kattintson a **Mentés** gombra.

   ![Felhasználók hozzáadása](media/automation-role-based-access-control/automation-04-add-users.png)

   Most meg kell jelennie a felhasználók lapjához hozzáadott felhasználónak, és a kiválasztott szerepkör hozzá van rendelve.

   ![Felhasználók listázása](media/automation-role-based-access-control/automation-05-list-users.png)

   A felhasználóhoz a Szerepkörök lapról is hozzárendelhet szerepkört.

4. Kattintson a **szerepkörök** elemre a hozzáférés-vezérlés (iam) lapon a szerepkörök lap megnyitásához. Megtekintheti a szerepkör nevét, valamint a szerepkörhöz rendelt felhasználók és csoportok számát.

    ![Szerepkör hozzárendelése a Felhasználók lapról](media/automation-role-based-access-control/automation-06-assign-role-from-users-blade.png)

   > [!NOTE]
   > A szerepköralapú hozzáférés-vezérlést csak az Automation-fiók hatókörében állíthatja be, és nem az Automation-fiók alatti erőforrásokhoz.

#### <a name="remove-a-user"></a>Felhasználó eltávolítása

Eltávolíthat egy olyan felhasználó hozzáférési engedélyét, aki nem kezeli az Automation-fiókot, vagy akik már nem működnek a szervezeten belül. Alább láthatók egy felhasználó eltávolításának lépései:

1. A hozzáférés-vezérlés (IAM) lapon válassza ki az eltávolítandó felhasználót, és kattintson az **Eltávolítás** gombra.
2. A hozzárendelés részleteit megjelenítő lapon kattintson az **Eltávolítás** gombra.
3. Az **Igen** gombra kattintva erősítse meg az eltávolítást.

   ![Felhasználók eltávolítása](media/automation-role-based-access-control/automation-08-remove-users.png)

### <a name="configure-azure-rbac-using-powershell"></a>Az Azure RBAC konfigurálása a PowerShell használatával

Az Automation-fiókokhoz a következő [Azure PowerShell parancsmagok](../role-based-access-control/role-assignments-powershell.md)használatával is konfigurálhat szerepköralapú hozzáférést:

A [Get-AzRoleDefinition](/powershell/module/Az.Resources/Get-AzRoleDefinition) felsorolja a Azure Active Directoryban elérhető összes Azure-szerepkört. Ezt a parancsmagot használhatja a `Name` paraméterrel az adott szerepkör által végrehajtható összes művelet listázásához.

```azurepowershell-interactive
Get-AzRoleDefinition -Name 'Automation Operator'
```

A következő példában a kimenet látható:

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

A [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) felsorolja az Azure szerepkör-hozzárendeléseket a megadott hatókörben. Paraméterek nélkül ez a parancsmag az előfizetés keretében végrehajtott összes szerepkör-hozzárendelést visszaadja. A `ExpandPrincipalGroups` paraméter használatával listázhatja a megadott felhasználóhoz tartozó hozzáférési hozzárendeléseket, valamint azokat a csoportokat, amelyekhez a felhasználó tartozik.

**Példa:** A következő parancsmag használatával listázhatja az összes felhasználót és azok szerepkörét egy Automation-fiókon belül.

```azurepowershell-interactive
Get-AzRoleAssignment -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

A következő példában a kimenet látható:

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

A [New-AzRoleAssignment](/powershell/module/Az.Resources/New-AzRoleAssignment) használatával a felhasználókhoz, csoportokhoz és alkalmazásokhoz való hozzáférést egy adott hatókörhöz rendelheti hozzá.

**Példa:** A következő parancs használatával rendelje hozzá az Automation-operátor szerepkört egy felhasználóhoz az Automation-fiók hatókörében.

```azurepowershell-interactive
New-AzRoleAssignment -SignInName <sign-in Id of a user you wish to grant access> -RoleDefinitionName 'Automation operator' -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

A következő példában a kimenet látható:

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

A [Remove-AzRoleAssignment](/powershell/module/Az.Resources/Remove-AzRoleAssignment) használatával távolíthatja el egy adott felhasználó, csoport vagy alkalmazás hozzáférését egy adott hatókörből.

**Példa:** A következő parancs használatával távolítsa el a felhasználót az Automation-operátori szerepkörből az Automation-fiók hatókörében.

```azurepowershell-interactive
Remove-AzRoleAssignment -SignInName <sign-in Id of a user you wish to remove> -RoleDefinitionName 'Automation Operator' -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

Az előző példában cserélje le a, a, `sign-in ID of a user you wish to remove` `SubscriptionID` `Resource Group Name` és `Automation account name` a fiók adatait. Ha a rendszer kéri, válassza az **Igen** lehetőséget, mielőtt továbblépne a felhasználói szerepkör-hozzárendelések eltávolítására.

### <a name="user-experience-for-automation-operator-role---automation-account"></a>Az Automation-kezelő szerepkör-Automation-fiók felhasználói felülete

Ha az Automation-fiók hatókörében az Automation-operátori szerepkörhöz hozzárendelt felhasználó megtekinti azt az Automation-fiókot, amelyhez hozzá van rendelve, a felhasználó csak az Automation-fiókban létrehozott runbookok, runbook és ütemezett feladatok listáját jeleníti meg. Ez a felhasználó nem tekintheti meg az elemek definícióit. A felhasználó elindíthatja, leállíthatja, felfüggesztheti, folytathatja vagy ütemezheti a runbook feladatot. A felhasználónak azonban nincs hozzáférése más Automation-erőforrásokhoz, például konfigurációkhoz, hibrid feldolgozói csoportokhoz vagy DSC-csomópontokhoz.

![Nincs hozzáférése az erőforrásokhoz](media/automation-role-based-access-control/automation-10-no-access-to-resources.png)

## <a name="configure-azure-rbac-for-runbooks"></a>Az Azure-RBAC konfigurálása a runbookok

Azure Automation lehetővé teszi, hogy Azure-szerepköröket rendeljen hozzá adott runbookok. Ehhez futtassa a következő szkriptet egy felhasználó adott runbook való hozzáadásához. Egy Automation-fiók rendszergazdája vagy egy Bérlői rendszergazda futtathatja ezt a parancsfájlt.

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

A parancsfájl futtatása után a felhasználó bejelentkezhet a Azure Portalba, és kiválaszthatja az **összes erőforrást**. A listán a felhasználó láthatja azokat a runbook, amelyekhez Automation Runbook-kezelőként hozzá lett adva.

![Azure-RBAC Runbook a portálon](./media/automation-role-based-access-control/runbook-rbac.png)

### <a name="user-experience-for-automation-operator-role---runbook"></a>Felhasználói élmény az Automation-operátori szerepkörhöz – Runbook

Ha a Runbook hatókörében az Automation-operátori szerepkörhöz hozzárendelt felhasználó egy hozzárendelt Runbook jelenít meg, a felhasználó csak a Runbook indítható el, és megtekintheti a Runbook-feladatokat.

![Csak az indításhoz van hozzáférése](media/automation-role-based-access-control/automation-only-start.png)

## <a name="next-steps"></a>Következő lépések

* Ha többet szeretne megtudni az Azure RBAC a PowerShell használatával kapcsolatban, tekintse meg az [Azure szerepkör-hozzárendelések hozzáadása vagy eltávolítása Azure PowerShell használatával](../role-based-access-control/role-assignments-powershell.md)című részt.
* A runbookok típusaival kapcsolatos részletekért lásd: [Azure Automation runbook-típusok](automation-runbook-types.md).
* A runbook elindításához tekintse [meg a Runbook elindítása a Azure Automationban](start-runbooks.md)című témakört.
