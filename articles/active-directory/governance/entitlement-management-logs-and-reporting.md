---
title: Archív & jelentés Azure Monitor – Azure AD-jogosultságkezelés
description: Megtudhatja, hogyan archiválhatja a naplókat, és hogyan hozhat létre Azure Monitor a Azure Active Directory kezelésével.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 12/23/2020
ms.author: barclayn
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 354805b3e2b538c92ba2345df2bcd93968640068
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764386"
---
# <a name="archive-logs-and-reporting-on-azure-ad-entitlement-management-in-azure-monitor"></a>Az Azure AD-jogosultságok kezelésével kapcsolatos naplók és jelentéskészítés archiválásának Azure Monitor

Az Azure AD legfeljebb 30 napig tárolja a naplózási eseményeket az auditnaplóban. A naplózási adatokat azonban az alapértelmezett megőrzési időtartamnál hosszabb ideig is megtarthatja a Mennyi ideig tárolja az [Azure AD](../reports-monitoring/reference-reports-data-retention.md)a jelentéskészítési adatokat? című cikk alapján, ha az adatokat egy Azure Storage-fiókba vagy egy Azure Monitor. Ezután munkafüzeteket, egyéni lekérdezéseket és jelentéseket használhat ezekhez az adatokhoz.


## <a name="configure-azure-ad-to-use-azure-monitor"></a>Az Azure AD konfigurálása Azure Monitor
A munkafüzetek Azure Monitor előtt úgy kell konfigurálnia az Azure AD-t, hogy az auditnaplóiról másolatot küldjön a Azure Monitor.

Az Azure AD-auditnaplók archiválása megköveteli, hogy Azure Monitor azure-előfizetésben legyen. Az Azure AD-tevékenységnaplókban az Azure AD-tevékenységnaplókban Azure Monitor az előfeltételekről és a becsült [Azure Monitor.](../reports-monitoring/concept-activity-logs-azure-monitor.md)

**Előfeltételként szükséges** szerepkör: Globális rendszergazda

1. Jelentkezzen be a Azure Portal globális rendszergazda felhasználóként. Győződjön meg arról, hogy rendelkezik hozzáféréssel az Azure Monitor erőforráscsoporthoz.
 
1. Válassza **Azure Active Directory** a bal oldali navigációs **menü** Figyelés menüpontja alatt található Diagnosztikai beállítások elemet. Ellenőrizze, hogy van-e már olyan beállítás, amely az auditnaplókat erre a munkaterületre küldi.

1. Ha még nem létezik beállítás, kattintson a **Diagnosztikai beállítás hozzáadása lehetőségre.** Az [Azure AD-naplók](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md#send-logs-to-azure-monitor) integrálása Azure Monitor naplókba cikk utasításait követve küldje el az Azure AD auditnaplóját a Azure Monitor munkaterületre.

    ![Diagnosztikai beállítások panel](./media/entitlement-management-logs-and-reporting/audit-log-diagnostics-settings.png)


1. Miután a naplót elküldte a Azure Monitor válassza a **Log Analytics-munkaterületek** lehetőséget, és válassza ki az Azure AD-auditnaplókat tartalmazó munkaterületet.

1. Válassza **a Felhasználás és becsült költségek lehetőséget,** majd kattintson az **Adatmegőrzés elemre.** Módosítsa a csúszkát arra a napok számára, amelyeken meg szeretné tartani az adatokat, hogy megfeleljen a naplózási követelményeknek.

    ![Log Analytics-munkaterületek panel](./media/entitlement-management-logs-and-reporting/log-analytics-workspaces.png)

1. Később a munkaterületen található dátumtartományokat az Archivált napló dátumtartománya munkafüzetben *láthatja:*  
    
    1. Válassza **a Azure Active Directory,** majd kattintson a **Munkafüzetek elemre.** 
    
    1. Bontsa ki **a hibaelhárítási Azure Active Directory szakaszt,** majd kattintson az **Archivált napló dátumtartománya elemre.** 


## <a name="view-events-for-an-access-package"></a>Hozzáférési csomag eseményeinek megtekintése  

Egy hozzáférési csomag eseményeinek megtekintéséhez hozzáféréssel kell rendelkezik az [](../../azure-monitor/logs/manage-access.md#manage-access-using-azure-permissions) alapul szolgáló Azure Monitor-munkaterülethez (további információért lásd: Naplóadatok és munkaterületek hozzáférésének kezelése a Azure Monitor-ban), valamint az alábbi szerepkörök egyikében: 

- Globális rendszergazda  
- Biztonsági rendszergazda  
- Biztonsági olvasó  
- Jelentésolvasó  
- alkalmazás-rendszergazda  

Az események megtekintéséhez a következő eljárást használhatja: 

1. A Azure Portal **válassza** a Azure Active Directory, majd a **Munkafüzetek lehetőséget.** Ha csak egy előfizetéssel rendelkezik, lépjen tovább a 3. lépésre. 

1. Ha több előfizetéssel rendelkezik, válassza ki a munkaterületet tartalmazó előfizetést.  

1. Válassza ki az *Access Package Activity (Hozzáférési csomagtevékenység) nevű munkafüzetet.* 

1. Ebben a munkafüzetben válasszon ki  egy időtartományt (módosítsa az All (Mind) lehetőséget, ha nem biztos benne), majd válasszon egy hozzáférésicsomag-azonosítót az összes olyan hozzáférési csomag legördülő listájából, amely az időszakban tevékenységgel rendelkezik. Megjelennek a hozzáférési csomaghoz kapcsolódó, a kiválasztott időtartományban történt események.  

    ![Hozzáférésicsomag-események megtekintése](./media/entitlement-management-logs-and-reporting/view-events-access-package.png) 

    Minden sor tartalmazza az időt, a hozzáférési csomag azonosítóját, a művelet nevét, az objektum azonosítóját, az UPN-t, valamint a műveletet elkereső felhasználó megjelenített nevét.  További részleteket a JSON tartalmaz.   


## <a name="create-custom-azure-monitor-queries-using-the-azure-portal"></a>Egyéni lekérdezések Azure Monitor a Azure Portal
Létrehozhat saját lekérdezéseket az Azure AD naplózási eseményeihez, beleértve a jogosultságkezelési eseményeket is.  

1. A Azure Active Directory bal Azure Portal a bal oldali  navigációs menü Figyelés szakaszában kattintson a Naplók elemre egy új lekérdezési oldal létrehozásához.

1. A munkaterületnek a lekérdezési oldal bal felső sarkában kell lennie. Ha több munkaterülettel Azure Monitor, és az Azure AD naplózási eseményeinek tárolására használt munkaterület nem jelenik meg, kattintson a Hatókör kiválasztása **lehetőségre.** Ezután válassza ki a megfelelő előfizetést és munkaterületet.

1. Ezután a lekérdezés szövegterületen törölje a "search *" sztringet, és cserélje le a következő lekérdezésre:

    ```
    AuditLogs | where Category == "EntitlementManagement"
    ```

1. Ezután kattintson a **Futtatás gombra.** 

    ![Kattintson a Futtatás gombra a lekérdezés futtatásához](./media/entitlement-management-logs-and-reporting/run-query.png)

A táblázat alapértelmezés szerint az elmúlt egy óra jogosultságkezelési naplóeseményét mutatja. A régebbi események megtekintéséhez módosíthatja az "Időtartomány" beállítást. A beállítás módosítása azonban csak az Azure AD konfigurálása után történt eseményeket mutatja a Azure Monitor.

Ha szeretné tudni a Azure Monitor legrégebbi és legújabb naplózási eseményeit:

```
AuditLogs | where TimeGenerated > ago(3653d) | summarize OldestAuditEvent=min(TimeGenerated), NewestAuditEvent=max(TimeGenerated) by Type
```

A naplóesemények naplózási eseményeihez tárolt oszlopokkal kapcsolatos további Azure Monitor Az [Azure AD](../reports-monitoring/reference-azure-monitor-audit-log-schema.md)auditnaplók sémája értelmezése a Azure Monitor.

## <a name="create-custom-azure-monitor-queries-using-azure-powershell"></a>Egyéni lekérdezések Azure Monitor a Azure PowerShell

A naplókat a PowerShellen keresztül is elérheti, miután konfigurálta az Azure AD-t, hogy naplókat küldjön a Azure Monitor. Ezután küldjön lekérdezéseket szkriptek vagy a PowerShell parancssorból anélkül, hogy globális rendszergazdának kellene lennie a bérlőben. 

### <a name="ensure-the-user-or-service-principal-has-the-correct-role-assignment"></a>Győződjön meg arról, hogy a felhasználó vagy szolgáltatásnév a megfelelő szerepkör-hozzárendelést biztosítja

Győződjön meg arról, hogy Ön, az Azure AD-ban hitelesítést kapó felhasználó vagy szolgáltatásnév a megfelelő Azure-szerepkörben van a Log Analytics-munkaterületen. A szerepkör-beállítások a Log Analytics-olvasó vagy a Log Analytics közreműködője. Ha már rendelkezik ilyen szerepkörekkel, ugorjon a Log Analytics-azonosító lekérése [egyetlen Azure-előfizetéssel részhez.](#retrieve-log-analytics-id-with-one-azure-subscription)

A szerepkör-hozzárendelés beállításához és egy lekérdezés létrehozásához tegye a következőket:

1. A Azure Portal keresse meg a [Log Analytics-munkaterületet.](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.OperationalInsights%2Fworkspaces
)

1. Válassza **Access Control (IAM) lehetőséget.**

1. Ezután kattintson a **Hozzáadás gombra** egy szerepkör-hozzárendelés hozzáadásához.

    ![Szerepkör-hozzárendelés hozzáadása](./media/entitlement-management-logs-and-reporting/workspace-set-role-assignment.png)

### <a name="install-azure-powershell-module"></a>Az Azure PowerShell modul telepítése

Ha már rendelkezik a megfelelő szerepkör-hozzárendelésekkel, indítsa el [a](/powershell/azure/install-az-ps) PowerShellt, és telepítse a Azure PowerShell modult (ha még nem), a következő beírásával:

```azurepowershell
install-module -Name az -allowClobber -Scope CurrentUser
```
    
Most már készen áll az Azure AD-hitelesítésre és a lekérdezni használt Log Analytics-munkaterület azonosítójának lekérésére.

### <a name="retrieve-log-analytics-id-with-one-azure-subscription"></a>Log Analytics-azonosító lekérése egyetlen Azure-előfizetéssel
Ha csak egyetlen Azure-előfizetéssel és egyetlen Log Analytics-munkaterülettel rendelkezik, írja be a következőt az Azure AD-hitelesítéshez, az előfizetéshez való csatlakozáshoz és a munkaterület lekéréséhez:
 
```azurepowershell
Connect-AzAccount
$wks = Get-AzOperationalInsightsWorkspace
```
 
### <a name="retrieve-log-analytics-id-with-multiple-azure-subscriptions"></a>Log Analytics-azonosító lekérése több Azure-előfizetéssel

 [A Get-AzOperationalInsightsWorkspace](/powershell/module/Az.OperationalInsights/Get-AzOperationalInsightsWorkspace) egyszerre egy előfizetésben működik. Ha tehát több Azure-előfizetéssel rendelkezik, győződjön meg arról, hogy ahhoz csatlakozik, amely a Log Analytics-munkaterülettel rendelkezik az Azure AD-naplók használatával. 
 
 Az alábbi parancsmagok megjelenítik az előfizetések listáját, és megkeresik annak az előfizetésnek az azonosítóját, amely a Log Analytics-munkaterülettel rendelkezik:
 
```azurepowershell
Connect-AzAccount
$subs = Get-AzSubscription
$subs | ft
```
 
A PowerShell-munkamenetet újrahitelesítheti és társíthatja az előfizetéshez egy például a `Connect-AzAccount –Subscription $subs[0].id` paranccsal. Ha többet szeretne megtudni arról, hogyan hitelesítheti magát az Azure-ban a PowerShellből, beleértve a nem interaktív hitelesítést is, tekintse meg a bejelentkezést a [Azure PowerShell.](/powershell/azure/authenticate-azureps)

Ha több Log Analytics-munkaterület van az előfizetésben, akkor a [Get-AzOperationalInsightsWorkspace](/powershell/module/Az.OperationalInsights/Get-AzOperationalInsightsWorkspace) parancsmag visszaadja a munkaterületek listáját. Ezután megkeresheti azt, amely az Azure AD-naplókat is. A parancsmag által visszaadott mező megegyezik a Log Analytics-munkaterület áttekintésének Azure Portal munkaterület-azonosító `CustomerId` értékével.
 
```powershell
$wks = Get-AzOperationalInsightsWorkspace
$wks | ft CustomerId, Name
```

### <a name="send-the-query-to-the-log-analytics-workspace"></a>A lekérdezés küldése a Log Analytics-munkaterületre
Végül, miután azonosított egy munkaterületet, az [Invoke-AzOperationalInsightsQuery](/powershell/module/az.operationalinsights/Invoke-AzOperationalInsightsQuery) használatával elküldhet egy Kusto-lekérdezést erre a munkaterületre. Ezek a lekérdezések [Kusto lekérdezési nyelven vannak megírva.](/azure/kusto/query/)
 
A naplóesemények rekordjainak dátumtartományát például lekérheti a Log Analytics-munkaterületről PowerShell-parancsmagokkal, amelyek a következő lekérdezéseket küldik el:
 
```powershell
$aQuery = "AuditLogs | where TimeGenerated > ago(3653d) | summarize OldestAuditEvent=min(TimeGenerated), NewestAuditEvent=max(TimeGenerated) by Type"
$aResponse = Invoke-AzOperationalInsightsQuery -WorkspaceId $wks[0].CustomerId -Query $aQuery
$aResponse.Results |ft
```

A jogosultságkezelési eseményeket a következő lekérdezésekkel is lekérheti:

```azurepowershell
$bQuery = 'AuditLogs | where Category == "EntitlementManagement"'
$bResponse = Invoke-AzOperationalInsightsQuery -WorkspaceId $wks[0].CustomerId -Query $Query
$bResponse.Results |ft 
```

## <a name="next-steps"></a>Következő lépések:
- [Interaktív jelentések létrehozása Azure Monitor munkafüzetekkel](../../azure-monitor/visualize/workbooks-overview.md)
