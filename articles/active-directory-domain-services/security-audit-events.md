---
title: A Azure AD Domain Services biztonsági naplózásának engedélyezése | Microsoft Docs
description: Megtudhatja, hogyan engedélyezheti a biztonsági naplózást az elemzési és riasztási események naplózásának központosításához Azure AD Domain Services
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 662362c3-1a5e-4e94-ae09-8e4254443697
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: caf46850b3d8d6946225575b8a9a732a90847482
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100574145"
---
# <a name="enable-security-audits-for-azure-active-directory-domain-services"></a>Azure Active Directory Domain Services biztonsági naplózásának engedélyezése

A Azure Active Directory Domain Services (Azure AD DS) biztonsági naplózás lehetővé teszi az Azure stream biztonsági eseményeinek megadását a célként megadott erőforrásokhoz. Ezek az erőforrások például az Azure Storage, az Azure Log Analytics-munkaterületek vagy az Azure Event hub. A biztonsági naplózási események engedélyezése után az Azure AD DS a kijelölt kategóriába tartozó összes naplózott eseményt elküldi a célként megadott erőforrásnak.

Az eseményeket az Azure Storage-ba és a streambe való beküldéssel is archiválhatja az Azure Event Hubs használatával, vagy saját elemzéssel és az Azure Log Analytics-munkaterületek használatával az Azure Portal.

> [!IMPORTANT]
> Az Azure AD DS biztonsági naplózás csak Azure Resource Manager-alapú felügyelt tartományok esetében érhető el. Az áttelepítéssel kapcsolatos információkért lásd: [Az Azure AD DS áttelepítése a klasszikus virtuális hálózati modellből a Resource Managerbe][migrate-azure-adds].

## <a name="security-audit-destinations"></a>Biztonsági naplózási célhelyek

Az Azure Storage, az Azure Event Hubs vagy az Azure Log Analytics-munkaterületek célként megadott erőforrásként használhatók Azure AD DS biztonsági naplózáshoz. Ezek a célhelyek kombinálhatók. Használhatja például az Azure Storage-t a biztonsági naplózási események archiválásához, de egy Azure Log Analytics-munkaterületet az információk rövid távú elemzéséhez és jelentéséhez.

Az alábbi táblázat az egyes forrásokhoz tartozó erőforrás-típusokra vonatkozó forgatókönyveket ismerteti.

> [!IMPORTANT]
> Az Azure AD DS biztonsági naplózás engedélyezése előtt létre kell hoznia a cél-erőforrást. Ezeket az erőforrásokat a Azure Portal, Azure PowerShell vagy az Azure CLI használatával hozhatja létre.

| Cél erőforrás | Eset |
|:---|:---|
|Azure Storage| Ezt a célt akkor kell használni, ha elsődlegesen a biztonsági naplózási események archiválási célból történő tárolására van szükség. Más célok archiválási célokra is használhatók, azonban ezek a célok az archiválás elsődleges igényén felüli képességeket biztosítanak. <br /><br />Mielőtt engedélyezi az Azure AD DS biztonsági naplózási eseményeit, először [hozzon létre egy Azure Storage-fiókot](../storage/common/storage-account-create.md).|
|Azure Event Hubs| Ezt a célt akkor érdemes használni, ha az elsődlegesen a biztonsági naplózási események megosztására van szükség további szoftverekkel, például az adatelemzési szoftverekkel vagy a biztonsági információkkal & az Event Management (SIEM) szoftverrel.<br /><br />Az Azure AD DS biztonsági naplózási események engedélyezése előtt [hozzon létre egy Event hub-t a Azure Portal használatával](../event-hubs/event-hubs-create.md)|
|Azure Log Analytics munkaterület| Ezt a célt akkor érdemes használni, ha az elsődleges szükséglet a Azure Portal közvetlen biztonságos naplózásának elemzése és ellenőrzése.<br /><br />Az Azure AD DS biztonsági naplózási események engedélyezése előtt [hozzon létre egy log Analytics munkaterületet a Azure Portal.](../azure-monitor/logs/quick-create-workspace.md)|

## <a name="enable-security-audit-events-using-the-azure-portal"></a>Biztonsági naplózási események engedélyezése a Azure Portal használatával

Az Azure AD DS biztonsági naplózási események az Azure Portal használatával történő engedélyezéséhez hajtsa végre a következő lépéseket.

> [!IMPORTANT]
> Az Azure AD DS biztonsági naplózása nem visszamenőleges. Múltbeli események nem kérhetők le és nem játszhatók vissza. Az Azure AD DS csak a biztonsági naplózás engedélyezése után elvégezhető eseményeket küldheti el.

1. Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.
1. A Azure Portal tetején keresse meg és válassza a **Azure ad Domain Services** lehetőséget. Válassza ki a felügyelt tartományt, például *aaddscontoso.com*.
1. Az Azure AD DS ablakban válassza a **diagnosztikai beállítások** elemet a bal oldali oldalon.
1. Alapértelmezés szerint egyetlen diagnosztika sincs konfigurálva. Első lépésként válassza a **diagnosztikai beállítás hozzáadása** elemet.

    ![Diagnosztikai beállítás hozzáadása a Azure AD Domain Serviceshoz](./media/security-audit-events/add-diagnostic-settings.png)

1. Adja meg a diagnosztikai konfiguráció nevét, például *aadds-audit*.

    Jelölje be a kívánt biztonsági naplózási célhely jelölőnégyzetét. Választhat egy Azure Storage-fiókból, egy Azure Event hub-ból vagy egy Log Analytics-munkaterületből is. Ezeknek a cél erőforrásoknak már léteznie kell az Azure-előfizetésében. A célként megadott erőforrások nem hozhatók létre ebben a varázslóban.

    ![A szükséges cél és naplózási események típusának engedélyezése a rögzítéshez](./media/security-audit-events/diagnostic-settings-page.png)

    * **Azure Storage**
        * Válassza az **archiválás egy Storage-fiókba** lehetőséget, majd válassza a **Konfigurálás** lehetőséget.
        * Válassza ki az **előfizetést** és azt a **Storage-fiókot** , amelyet a biztonsági naplózási események archiválásához használni kíván.
        * Ha elkészült, kattintson **az OK gombra**.
    * **Azure Event hubok**
        * Válassza a stream elemet az **Event hubhoz**, majd válassza a **Konfigurálás** lehetőséget.
        * Válassza ki az **előfizetést** és az **Event hub-névteret**. Ha szükséges, válassza ki az **Event hub nevét** , majd az **Event hub-szabályzat nevét**.
        * Ha elkészült, kattintson **az OK gombra**.
    * **Azure log analitikai munkaterületek**
        * Válassza a **küldés log Analytics** lehetőséget, majd válassza ki azt az **előfizetést** és **log Analytics munkaterületet** , amelyet a biztonsági naplózási események tárolására kíván használni.

1. Válassza ki az adott cél erőforráshoz szerepeltetni kívánt naplózási kategóriákat. Ha a naplózási eseményeket egy Azure Storage-fiókba küldi, beállíthat egy adatmegőrzési szabályt is, amely meghatározza, hogy hány nap elteltével kell megőrizni az adatok mennyiségét. A *0* alapértelmezett értéke minden adat megtartása, és az események nem forognak egy adott időszak után.

    Egyetlen konfiguráción belül különböző naplózási kategóriákat választhat az egyes megcélzó erőforrásokhoz. Ezzel a képességgel kiválaszthatja, hogy mely naplókhoz kívánja megőrizni a Log Analytics és az archiválni kívánt kategóriákat (például:).

1. Ha elkészült, kattintson a **Mentés** gombra a módosítások elvégzéséhez. A cél erőforrásai hamarosan megkezdik az Azure AD DS biztonsági naplózási események fogadását a konfiguráció mentése után.

## <a name="enable-security-audit-events-using-azure-powershell"></a>Biztonsági naplózási események engedélyezése Azure PowerShell használatával

Az Azure AD DS biztonsági naplózási események Azure PowerShell használatával történő engedélyezéséhez hajtsa végre az alábbi lépéseket. Ha szükséges, először [telepítse a Azure PowerShell modult, és kapcsolódjon az Azure-előfizetéséhez](/powershell/azure/install-az-ps).

> [!IMPORTANT]
> Az Azure AD DS biztonsági naplózása nem visszamenőleges. Múltbeli események nem kérhetők le és nem játszhatók vissza. Az Azure AD DS csak a biztonsági naplózás engedélyezése után elvégezhető eseményeket küldheti el.

1. Hitelesítse az Azure-előfizetését a [AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) parancsmag használatával. Ha a rendszer kéri, adja meg a fiókja hitelesítő adatait.

    ```azurepowershell
    Connect-AzAccount
    ```

1. Hozza létre a cél erőforrást a biztonsági naplózási eseményekhez.

    * **Azure Storage**  -  [Storage-fiók létrehozása Azure PowerShell használatával](../storage/common/storage-account-create.md?tabs=azure-powershell)
    * **Azure Event hubok**  -  [Hozzon létre egy Event hubot a Azure PowerShell használatával](../event-hubs/event-hubs-quickstart-powershell.md). Előfordulhat, hogy a [New-AzEventHubAuthorizationRule](/powershell/module/az.eventhub/new-azeventhubauthorizationrule) parancsmagot kell használnia olyan engedélyezési szabály létrehozásához, amely Azure AD DS engedélyeket biztosít az Event hub- *névtérhez*. Az engedélyezési szabálynak tartalmaznia kell a **kezelés**, a **figyelés** és a **Küldés** jogosultságokat.

        > [!IMPORTANT]
        > Győződjön meg arról, hogy az Event hub-névtérben az engedélyezési szabályt állítja be, nem pedig magára az Event hub-ra.

    * **Azure log analitikai munkaterületek**  -  [Hozzon létre egy log Analytics munkaterületet Azure PowerShell](../azure-monitor/logs/powershell-workspace-configuration.md).

1. Szerezze be az Azure AD DS felügyelt tartomány erőforrás-AZONOSÍTÓját a [Get-AzResource](/powershell/module/Az.Resources/Get-AzResource) parancsmag használatával. Hozzon létre egy $aadds nevű változót *. ResourceId* az érték megtartásához:

    ```azurepowershell
    $aadds = Get-AzResource -name aaddsDomainName
    ```

1. Konfigurálja az Azure diagnosztikai beállításait a [set-AzDiagnosticSetting](/powershell/module/Az.Monitor/Set-AzDiagnosticSetting) parancsmag használatával a Azure ad Domain Services biztonsági naplózási események céljának erőforrásának használatához. A következő példákban a változó *$aadds. A ResourceId* az előző lépésben használatos.

    * **Azure Storage** – cserélje le a *storageAccountId* a Storage-fiók nevére:

        ```powershell
        Set-AzDiagnosticSetting `
            -ResourceId $aadds.ResourceId `
            -StorageAccountId storageAccountId `
            -Enabled $true
        ```

    * **Azure Event hubok** – cserélje le az *eventHubName* -t az Event hub nevére és a *eventHubRuleId* az engedélyezési szabály azonosítójával:

        ```powershell
        Set-AzDiagnosticSetting -ResourceId $aadds.ResourceId `
            -EventHubName eventHubName `
            -EventHubAuthorizationRuleId eventHubRuleId `
            -Enabled $true
        ```

    * **Azure log Analytics-munkaterületek** – cserélje le a *munkaterület azonosítója* -t a log Analytics munkaterület azonosítójával:

        ```powershell
        Set-AzureRmDiagnosticSetting -ResourceId $aadds.ResourceId `
            -WorkspaceID workspaceId `
            -Enabled $true
        ```

## <a name="query-and-view-security-audit-events-using-azure-monitor"></a>Biztonsági naplózási események lekérdezése és megtekintése Azure Monitor használatával

A log analitikai munkaterületek lehetővé teszik a biztonsági naplózási események megtekintését és elemzését Azure Monitor és a Kusto lekérdezési nyelv használatával. Ezt a lekérdezési nyelvet csak olvasható használatra tervezték, amely egy könnyen olvasható szintaxissal büszkélkedhet a Power analitikai funkciókkal. További információ a Kusto-lekérdezési nyelvek használatáról:

* [Az Azure Monitor dokumentációja](../azure-monitor/index.yml)
* [Ismerkedés a Log Analyticsával Azure Monitor](../azure-monitor/logs/log-analytics-tutorial.md)
* [Ismerkedés az Azure Monitor-naplólekérdezésekkel](../azure-monitor/logs/get-started-queries.md)
* [Irányítópultok létrehozása és megosztása Log Analytics-adatokból](../azure-monitor/visualize/tutorial-logs-dashboards.md)

Az alábbi példák segítségével megkezdheti az Azure AD DS biztonsági naplózási eseményeinek elemzését.

### <a name="sample-query-1"></a>1. minta lekérdezés

Az összes fiókzárolási esemény megtekintése az elmúlt hét napban:

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
```

### <a name="sample-query-2"></a>2. minta lekérdezés

Tekintse meg az összes fiókzárolási eseményt (*4740*) a 2020. június 3. között, 9 órakor és 2020. június 10. között a dátum és idő szerint növekvő sorrendbe rendezve:

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= datetime(2020-06-03 09:00) and TimeGenerated <= datetime(2020-06-10)
| where OperationName has "4740"
| sort by TimeGenerated asc
```

### <a name="sample-query-3"></a>3. minta lekérdezés

A fiók bejelentkezési eseményeinek megtekintése hét nappal ezelőtt (mostantól) a felhasználó nevű fiókhoz:

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-4"></a>4. minta lekérdezés

Megtekintheti a fiók bejelentkezési eseményeit hét nappal ezelőtt a felhasználó számára, amely rossz jelszó (*0xC0000006a*) használatával próbált bejelentkezni:

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| where "0xc000006a" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-5"></a>5. minta lekérdezés

A fiók bejelentkezési eseményeinek megtekintése a fiók kizárása után a felhasználó számára, aki megpróbált bejelentkezni, miközben a fiókot kizárták (*0xC0000234*):

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| where "0xc0000234" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-6"></a>Minta lekérdezés 6

Tekintse meg a fiók bejelentkezési eseményeinek számát hét nappal ezelőtt az összes kizárt felhasználónál történt bejelentkezési kísérlet után:

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "0xc0000234" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| summarize count()
```

## <a name="audit-event-categories"></a>Naplózási események kategóriái

Az Azure AD DS biztonsági naplózása a hagyományos AD DS tartományvezérlők hagyományos auditálásával igazodik. Hibrid környezetekben újra felhasználhatja a meglévő naplózási mintákat, így az események elemzésekor ugyanezt a logikát is használhatja. A hibakereséshez vagy az elemzéshez szükséges forgatókönyvtől függően a különböző naplózási események kategóriáit kell megcélozni.

A következő naplózási események kategóriák érhetők el:

| Naplózási kategória neve | Description |
|:---|:---|
| Fiók bejelentkezése|A naplózási kísérletekkel egy tartományvezérlőn vagy egy helyi biztonsági fiókkezelő (SAM) fiók adatai hitelesíthetők.</p>A bejelentkezési és kijelentkezési szabályzat beállításai és eseményei nyomon követik az adott számítógép elérésére tett kísérleteket. Az ebben a kategóriában található beállítások és események a használt fiók-adatbázisra összpontosítanak. Ez a kategória a következő alkategóriákat tartalmazza:<ul><li>[Hitelesítő adatok érvényesítésének naplózása](/windows/security/threat-protection/auditing/audit-credential-validation)</li><li>[Kerberos hitelesítési szolgáltatás naplózása](/windows/security/threat-protection/auditing/audit-kerberos-authentication-service)</li><li>[A Kerberos szolgáltatás jegyműveleteinek naplózása](/windows/security/threat-protection/auditing/audit-kerberos-service-ticket-operations)</li><li>[Egyéb bejelentkezési/kijelentkezési események naplózása](/windows/security/threat-protection/auditing/audit-other-logonlogoff-events)</li></ul>|
| Fiókkezelés|A felhasználói és számítógépfiókok és csoportok változásainak naplózása. Ez a kategória a következő alkategóriákat tartalmazza:<ul><li>[Alkalmazáscsoport felügyeletének naplózása](/windows/security/threat-protection/auditing/audit-application-group-management)</li><li>[Számítógépes fiókok felügyeletének naplózása](/windows/security/threat-protection/auditing/audit-computer-account-management)</li><li>[Terjesztési csoportok felügyeletének naplózása](/windows/security/threat-protection/auditing/audit-distribution-group-management)</li><li>[Más fiókok felügyeletének naplózása](/windows/security/threat-protection/auditing/audit-other-account-management-events)</li><li>[Biztonsági csoportok felügyeletének naplózása](/windows/security/threat-protection/auditing/audit-security-group-management)</li><li>[Felhasználói fiókok felügyeletének naplózása](/windows/security/threat-protection/auditing/audit-user-account-management)</li></ul>|
| Részletek követése|A számítógépen lévő egyes alkalmazások és felhasználók tevékenységeinek naplózása, valamint a számítógép használatának megismerése. Ez a kategória a következő alkategóriákat tartalmazza:<ul><li>[DPAPI-tevékenység naplózása](/windows/security/threat-protection/auditing/audit-dpapi-activity)</li><li>[PNP-tevékenység naplózása](/windows/security/threat-protection/auditing/audit-pnp-activity)</li><li>[Folyamat-létrehozás naplózása](/windows/security/threat-protection/auditing/audit-process-creation)</li><li>[Folyamatmegszakítás naplózása](/windows/security/threat-protection/auditing/audit-process-termination)</li><li>[RPC események naplózása](/windows/security/threat-protection/auditing/audit-rpc-events)</li></ul>|
| Directory Services-hozzáférés|A naplózás megkísérli az objektumok elérését és módosítását Active Directory Domain Services (AD DS). Ezeket a naplózási eseményeket csak tartományvezérlőkön naplózza a rendszer. Ez a kategória a következő alkategóriákat tartalmazza:<ul><li>[Részletes könyvtárszolgáltatás-replikáció naplózása](/windows/security/threat-protection/auditing/audit-detailed-directory-service-replication)</li><li>[Könyvtárszolgáltatás hozzáférésének naplózása](/windows/security/threat-protection/auditing/audit-directory-service-access)</li><li>[Könyvtárszolgáltatás módosításainak naplózása](/windows/security/threat-protection/auditing/audit-directory-service-changes)</li><li>[Könyvtárszolgáltatás-replikáció naplózása](/windows/security/threat-protection/auditing/audit-directory-service-replication)</li></ul>|
| Logon-Logoff|A naplózás megkísérli a számítógépre való bejelentkezést interaktívan vagy hálózaton keresztül. Ezek az események hasznosak lehetnek a felhasználói tevékenységek nyomon követéséhez és a hálózati erőforrásokkal kapcsolatos lehetséges támadások azonosításához. Ez a kategória a következő alkategóriákat tartalmazza:<ul><li>[Fiókzárolás naplózása](/windows/security/threat-protection/auditing/audit-account-lockout)</li><li>[Felhasználók/eszközök jogcímeinek naplózása](/windows/security/threat-protection/auditing/audit-user-device-claims)</li><li>[IPsec kiterjesztett mód naplózása](/windows/security/threat-protection/auditing/audit-ipsec-extended-mode)</li><li>[Csoporttagság naplózása](/windows/security/threat-protection/auditing/audit-group-membership)</li><li>[IPsec fő mód naplózása](/windows/security/threat-protection/auditing/audit-ipsec-main-mode)</li><li>[IPsec gyors mód naplózása](/windows/security/threat-protection/auditing/audit-ipsec-quick-mode)</li><li>[Kijelentkezés naplózása](/windows/security/threat-protection/auditing/audit-logoff)</li><li>[Bejelentkezés naplózása](/windows/security/threat-protection/auditing/audit-logon)</li><li>[Hálózati házirend-kiszolgáló naplózása](/windows/security/threat-protection/auditing/audit-network-policy-server)</li><li>[Egyéb bejelentkezési/kijelentkezési események naplózása](/windows/security/threat-protection/auditing/audit-other-logonlogoff-events)</li><li>[Különleges bejelentkezés naplózása](/windows/security/threat-protection/auditing/audit-special-logon)</li></ul>|
|Objektum-hozzáférés| A naplózás egy hálózaton vagy számítógépen lévő adott objektumokhoz vagy típusú objektumokhoz próbál hozzáférni. Ez a kategória a következő alkategóriákat tartalmazza:<ul><li>[Létrehozott alkalmazás naplózása](/windows/security/threat-protection/auditing/audit-application-generated)</li><li>[Tanúsítványszolgáltatások naplózása](/windows/security/threat-protection/auditing/audit-certification-services)</li><li>[Részletes fájlmegosztás naplózása](/windows/security/threat-protection/auditing/audit-detailed-file-share)</li><li>[Fájlmegosztás naplózása](/windows/security/threat-protection/auditing/audit-file-share)</li><li>[Fájlrendszer naplózása](/windows/security/threat-protection/auditing/audit-file-system)</li><li>[Szűrőplatform-kapcsolat naplózása](/windows/security/threat-protection/auditing/audit-filtering-platform-connection)</li><li>[Szűrőplatform csomagvesztéseinek naplózása](/windows/security/threat-protection/auditing/audit-filtering-platform-packet-drop)</li><li>[Naplózási fogantyú kezelése](/windows/security/threat-protection/auditing/audit-handle-manipulation)</li><li>[Kernel-objektum naplózása](/windows/security/threat-protection/auditing/audit-kernel-object)</li><li>[Egyéb objektum-hozzáférési események naplózása](/windows/security/threat-protection/auditing/audit-other-object-access-events)</li><li>[Naplózási beállításjegyzék](/windows/security/threat-protection/auditing/audit-registry)</li><li>[Cserélhető tároló naplózása](/windows/security/threat-protection/auditing/audit-removable-storage)</li><li>[SAM naplózása](/windows/security/threat-protection/auditing/audit-sam)</li><li>[Központi hozzáférési házirend előkészítésének naplózása](/windows/security/threat-protection/auditing/audit-central-access-policy-staging)</li></ul>|
|Szabályzat módosítása|Egy helyi rendszer vagy hálózat fontos biztonsági házirendjeinek módosításait naplózza. A rendszergazdák általában a hálózati erőforrások biztonságossá tételéhez használják a házirendeket. A változások figyelése vagy a házirendek módosítására tett kísérletek fontos szempontot képeznek a hálózat biztonságának kezelésében. Ez a kategória a következő alkategóriákat tartalmazza:<ul><li>[Naplózási házirend-módosítások naplózása](/windows/security/threat-protection/auditing/audit-audit-policy-change)</li><li>[Hitelesítési házirend-módosítások naplózása](/windows/security/threat-protection/auditing/audit-authentication-policy-change)</li><li>[Engedélyezési házirend-módosítások naplózása](/windows/security/threat-protection/auditing/audit-authorization-policy-change)</li><li>[Szűrőplatform házirend-módosításainak naplózása](/windows/security/threat-protection/auditing/audit-filtering-platform-policy-change)</li><li>[MPSSVC szabályszintű házirend-módosítások naplózása](/windows/security/threat-protection/auditing/audit-mpssvc-rule-level-policy-change)</li><li>[Egyéb házirend-módosítás naplózása](/windows/security/threat-protection/auditing/audit-other-policy-change-events)</li></ul>|
|Jogosultságok használata| Bizonyos engedélyek használatát naplózza egy vagy több rendszeren. Ez a kategória a következő alkategóriákat tartalmazza:<ul><li>[Nem érzékeny jogosultságok használatának naplózása](/windows/security/threat-protection/auditing/audit-non-sensitive-privilege-use)</li><li>[Érzékeny jogosultságok használatának naplózása](/windows/security/threat-protection/auditing/audit-sensitive-privilege-use)</li><li>[Egyéb jogosultsághasználati események naplózása](/windows/security/threat-protection/auditing/audit-other-privilege-use-events)</li></ul>|
|Rendszer| Naplózza a rendszerszintű módosításokat egy olyan számítógépre, amely nem szerepel más kategóriákban, és amelyeknek lehetséges biztonsági következményei vannak. Ez a kategória a következő alkategóriákat tartalmazza:<ul><li>[IPsec-illesztőprogram naplózása](/windows/security/threat-protection/auditing/audit-ipsec-driver)</li><li>[Egyéb rendszeresemények naplózása](/windows/security/threat-protection/auditing/audit-other-system-events)</li><li>[Biztonsági állapotmódosítások naplózása](/windows/security/threat-protection/auditing/audit-security-state-change)</li><li>[Biztonsági rendszerbővítmény naplózása](/windows/security/threat-protection/auditing/audit-security-system-extension)</li><li>[Rendszer-integritás naplózása](/windows/security/threat-protection/auditing/audit-system-integrity)</li></ul>|

## <a name="event-ids-per-category"></a>Események azonosítói kategóriánként

 Az Azure AD DS biztonsági naplózása a következő eseményazonosítókat rögzíti, amikor az adott művelet naplózható eseményt indít el:

| Esemény kategóriájának neve | Eseményazonosító |
|:---|:---|
|Fiók bejelentkezési biztonsága|4767, 4774, 4775, 4776, 4777|
|Fiókkezelés biztonsága|4720, 4722, 4723, 4724, 4725, 4726, 4727, 4728, 4729, 4730, 4731, 4732, 4733, 4734, 4735, 4737, 4738, 4740, 4741, 4742, 4743, 4754, 4755, 4756, 4757, 4758, 4764, 4765, 4766, 4780, 4781 és 4782|
|Részletek követése biztonság|Nincsenek|
|DS-hozzáférés biztonsága|5136, 5137, 5138, 5139, 5141|
|Logon-Logoff biztonság|4624, 4625, 4634, 4647, 4648, 4672, 4675, 4964|
|Objektum-hozzáférés biztonsága|Nincsenek|
|Házirend-módosítási biztonság|4670, 4703, 4704, 4705, 4706, 4707, 4713, 4715, 4716, 4717, 4718, 4719, 4739, 4864, 4865, 4866, 4867, 4904, 4906, 4911, 4912|
|Biztonsági jogosultságok használata|4985|
|Rendszerbiztonság|4612, 4621|

## <a name="next-steps"></a>Következő lépések

A Kusto kapcsolatos konkrét információk a következő cikkekben találhatók:

* A Kusto lekérdezési nyelvének [áttekintése](/azure/kusto/query/) .
* [Kusto-oktatóanyag](/azure/kusto/query/tutorial) a lekérdezési alapismeretek megismeréséhez.
* [Példa a lekérdezésekre](/azure/kusto/query/samples) , amelyek segítenek az új módszerek megismerésében.
* Kusto [ajánlott eljárásokat](/azure/kusto/query/best-practices) a lekérdezések sikeres optimalizálása érdekében.

<!-- LINKS - Internal -->
[migrate-azure-adds]: migrate-from-classic-vnet.md