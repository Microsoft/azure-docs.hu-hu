---
title: Office 365 felügyeleti megoldás az Azure-ban
description: Ez a cikk részletesen ismerteti az Office 365-megoldás konfigurálását és használatát az Azure-ban.  A Azure Monitorban létrehozott Office 365-rekordok részletes leírását tartalmazza.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/30/2020
ms.openlocfilehash: be5409f7130f0e79b77871a2657609e8ddb76e36
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101728637"
---
# <a name="office-365-management-solution-in-azure-preview"></a>Office 365 felügyeleti megoldás az Azure-ban (előzetes verzió)

![Office 365 embléma](media/solution-office-365/icon.png)

> [!IMPORTANT]
> ## <a name="solution-update"></a>Megoldás frissítése
> Ezt a megoldást az [Office 365](../../sentinel/connect-office-365.md) általánosan elérhető megoldás váltotta fel az [Azure Sentinel](../../sentinel/overview.md) és az [Azure ad jelentéskészítési és figyelési megoldásában](../../active-directory/reports-monitoring/plan-monitoring-and-reporting.md). Együttesen a korábbi Azure Monitor Office 365-megoldás frissített verzióját nyújtják, amely továbbfejlesztett konfigurációs élményt biztosít. Továbbra is használhatja a meglévő megoldást 2020. október 31-ig.
> 
> Az Azure Sentinel egy Felhőbeli natív biztonsági információ-és eseménykezelő megoldás, amely betölti a naplókat, és további SIEM-funkciókat biztosít, beleértve az észleléseket, a nyomozást, a vadászatot és a gépi tanulásra épülő elemzéseket. Az Azure Sentinel használatával mostantól az Office 365 SharePoint-tevékenységek és az Exchange felügyeleti naplók betöltése is elérhetővé válik.
> 
> Az Azure AD jelentéskészítés átfogóbb áttekintést nyújt az Azure AD-tevékenységekből származó naplókról a környezetében, beleértve a bejelentkezési eseményeket, a naplózási eseményeket és a címtárban történt módosításokat. Az Azure AD-naplók összekapcsolásához használhatja az [Azure Sentinel Azure ad-összekötőt](../../sentinel/connect-azure-active-directory.md) , vagy konfigurálhatja az [Azure ad-naplók integrációját Azure monitor](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md). 
>
> Az Azure AD-napló gyűjteménye Azure Monitor díjszabás alá esik.  További információért tekintse meg a [Azure monitor díjszabását](https://azure.microsoft.com/pricing/details/monitor/) .
>
> Az Azure Sentinel Office 365 megoldás használata:
> 1. Az Office 365 Connector használata az Azure Sentinelben hatással van a munkaterület díjszabására. További információ: az [Azure Sentinel díjszabása](https://azure.microsoft.com/pricing/details/azure-sentinel/).
> 2. Ha már használja a Azure Monitor Office 365 megoldást, először el kell távolítania azt az [Eltávolítás szakaszban](#uninstall)található parancsfájl használatával.
> 3. [Engedélyezze az Azure Sentinel-megoldást](../../sentinel/quickstart-onboard.md) a munkaterületen.
> 4. Nyissa meg az Azure Sentinel **adatösszekötők** lapját, és engedélyezze az **Office 365** -összekötőt.
>
> ## <a name="frequently-asked-questions"></a>Gyakori kérdések
> 
> ### <a name="q-is-it-possible-to-on-board-the-office-365-azure-monitor-solution-between-now-and-october-31"></a>K: van lehetőség arra, hogy az Office 365 Azure Monitor megoldást most és október 31-ig is felhasználja?
> Nem, a Azure Monitor Office 365-megoldás bevezetési parancsfájljai már nem érhetők el. A megoldás október 31-ig el lesz távolítva.
> 
> ### <a name="q-will-the-tables-and-schemas-be-changed"></a>K: a táblák és sémák módosulnak?
> A **OfficeActivity** -tábla neve és sémája ugyanaz marad, mint az aktuális megoldásban. Továbbra is használhatja az új megoldásban ugyanazokat a lekérdezéseket, az Azure AD-adataira hivatkozó lekérdezések kivételével.
> 
> Az új [Azure ad jelentéskészítési és figyelési megoldás](../../active-directory/reports-monitoring/plan-monitoring-and-reporting.md) naplói bekerülnek a [SigninLogs](../../active-directory/reports-monitoring/concept-sign-ins.md) és a [AuditLogs](../../active-directory/reports-monitoring/concept-audit-logs.md) táblákba a **OfficeActivity** helyett. További információ: az Azure [ad-naplók elemzése](../../active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics.md), amely az Azure Sentinel és a Azure monitor felhasználók számára is fontos.
> 
> A következő minták a **OfficeActivity** és a **SigninLogs** lekérdezések átalakítására szolgálnak:
> 
> **Sikertelen bejelentkezések lekérdezése felhasználónként:**
> 
> ```Kusto
> OfficeActivity
> | where TimeGenerated >= ago(1d) 
> | where OfficeWorkload == "AzureActiveDirectory"                      
> | where Operation == 'UserLoginFailed'
> | summarize count() by UserId    
> ```
> 
> ```Kusto
> SigninLogs
> | where ConditionalAccessStatus == "failure" or ConditionalAccessStatus == "notApplied"
> | summarize count() by UserDisplayName
> ```
> 
> **Azure AD-műveletek megtekintése:**
> 
> ```Kusto
> OfficeActivity
> | where OfficeWorkload =~ "AzureActiveDirectory"
> | sort by TimeGenerated desc
> | summarize AggregatedValue = count() by Operation
> ```
> 
> ```Kusto
> AuditLogs
> | summarize count() by OperationName
> ```
> 
> ### <a name="q-how-can-i-on-board-azure-sentinel"></a>K: Hogyan lehet a fedélzeten az Azure Sentinel?
> Az Azure Sentinel egy olyan megoldás, amelyet engedélyezheti új vagy meglévő Log Analytics munkaterületen. További információ: [Az Azure Sentinel helyszíni dokumentációja](../../sentinel/quickstart-onboard.md).
>
> ### <a name="q-do-i-need-azure-sentinel-to-connect-the-azure-ad-logs"></a>K: Szükségem van az Azure Sentinelre az Azure AD-naplók összekapcsolásához?
> Konfigurálhatja az [Azure ad-naplók integrációját Azure monitor](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md), amely nem kapcsolódik az Azure Sentinel megoldáshoz. Az Azure Sentinel natív összekötőt és beépített tartalmat biztosít az Azure AD-naplók számára. További információkért tekintse meg az alábbi, a beépített biztonsággal kapcsolatos tartalomra vonatkozó kérdést.
>
> ###    <a name="q-what-are-the-differences-when-connecting-azure-ad-logs-from-azure-sentinel-and-azure-monitor"></a>K: Milyen különbségek vannak az Azure AD-naplók Azure Sentinel-és Azure Monitor-beli csatlakoztatásakor?
> Az Azure Sentinel és a Azure Monitor ugyanazon [Azure ad-jelentéskészítési és-figyelési megoldás](../../active-directory/reports-monitoring/plan-monitoring-and-reporting.md)alapján csatlakozik az Azure ad-naplókhoz. Az Azure Sentinel egy kattintással elérhető natív összekötőt biztosít, amely ugyanazokat az adatokat köti össze, és figyelési információkat biztosít.
>
> ###    <a name="q-what-do-i-need-to-change-when-moving-to-the-new-azure-ad-reporting-and-monitoring-tables"></a>K: mit kell módosítani az új Azure AD jelentéskészítési és figyelési táblázatokra való áttéréskor?
> Az Azure AD-adatokat használó összes lekérdezést, beleértve a riasztások, az irányítópultok és az Office 365 Azure AD-adatokat használó összes tartalmat, újra létre kell hozni az új táblák használatával.
>
> Az Azure Sentinel és az Azure AD beépített tartalmakat biztosít, amelyeket az Azure AD jelentéskészítési és figyelési megoldásra való áttéréshez használhat. További információkért tekintse meg a következő, a beépített biztonsággal kapcsolatos tartalomra vonatkozó kérdést, valamint a [Azure monitor munkafüzetek használatát Azure Active Directory jelentésekhez](../../active-directory/reports-monitoring/howto-use-azure-monitor-workbooks.md). 
>
> ### <a name="q-how-i-can-use-the-azure-sentinel-out-of-the-box-security-oriented-content"></a>K: Hogyan használhatom az Azure Sentinel out-of-the-Box biztonsági irányultságú tartalmat?
> Az Azure Sentinel beépített, biztonsági szintű irányítópultokat, egyéni riasztási lekérdezéseket, vadászati lekérdezéseket, nyomozást és automatizált reagálási képességeket biztosít az Office 365 és az Azure AD-naplók alapján. További információért tekintse meg az Azure Sentinel GitHubot és oktatóanyagokat:
>
> - [Fenyegetések észlelése](../../sentinel/tutorial-detect-threats-built-in.md)
> - [Egyéni elemzési szabályok létrehozása gyanús fenyegetések észleléséhez](../../sentinel/tutorial-detect-threats-custom.md)
> - [Adatok monitorozása](../../sentinel/tutorial-monitor-your-data.md)
> - [Incidensek vizsgálata az Azure Sentinellel](../../sentinel/tutorial-investigate-cases.md)
> - [Fenyegetésre adott automatikus válaszok beállítása az Azure Sentinelben](../../sentinel/tutorial-respond-threats-playbook.md)
> - [Azure Sentinel GitHub-Közösség](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks)
> 
> ### <a name="q-does-azure-sentinel-provide-additional-connectors-as-part-of-the-solution"></a>K: az Azure Sentinel további összekötőket biztosít a megoldás részeként?
> Igen, lásd: [Az Azure Sentinel összekapcsolási adatforrásai](../../sentinel/connect-data-sources.md).
> 
> ###    <a name="q-what-will-happen-on-october-31-do-i-need-to-offboard-beforehand"></a>K: mi fog történni október 31-én? Előre kell regisztrációjának megszüntetésére szolgáló?
> 
> - Nem fogja tudni fogadni az **Office 365** -megoldás adatait. A megoldás el lesz távolítva a munkaterületről, és a továbbiakban nem lesz elérhető a piactéren.
> - Az Azure Sentinel-ügyfelek esetében a Log Analytics munkaterület megoldás **Office 365** szerepelni fognak az Azure Sentinel **SecurityInsights** megoldásban.
> - Ha október 31-ig nem regisztrációjának megszüntetésére szolgáló a megoldást, az adatai automatikusan le lesznek választva, és a **OfficeActivity** tábla törlődik. Még így is visszaállíthatja a táblázatot, ha engedélyezi az Office 365-összekötőt az Azure Sentinelben, az alábbiakban leírtak szerint.
> 
> ### <a name="q-will-my-data-transfer-to-the-new-solution"></a>K: az adatok átkerülnek az új megoldásba?
> Igen. Ha eltávolítja az **Office 365** -megoldást a munkaterületről, az adatai átmenetileg elérhetetlenné válnak, mert a séma el lesz távolítva. Ha engedélyezi az új **Office 365** -összekötőt az Azure sentinelben, a séma vissza lesz állítva a munkaterületre, és a már összegyűjtött adatok elérhetővé válnak. 
 

Az Office 365 felügyeleti megoldás lehetővé teszi az Office 365-környezet figyelését Azure Monitorban.

- Az Office 365-fiókok felhasználói tevékenységeinek monitorozásával elemezheti a használati mintákat, valamint azonosíthatja a viselkedési trendeket. Kinyerheti például bizonyos használati forgatókönyveket, például a szervezeten kívül megosztott fájlokat vagy a legnépszerűbb SharePoint-webhelyeket.
- Rendszergazdai tevékenységek figyelése a konfigurációs változások és a magas jogosultsági szintű műveletek nyomon követéséhez.
- A nem kívánt felhasználói viselkedés észlelése és vizsgálata, amely testreszabható a szervezeti igényeknek megfelelően.
- Auditálás és megfelelőség bemutatása. Például figyelheti a fájl-hozzáférési műveleteket a bizalmas fájlokon, ami segíthet a naplózási és megfelelőségi folyamatokban.
- Hajtsa végre a működés közbeni hibaelhárítást a szervezete Office 365-tevékenység adataihoz tartozó [lekérdezések](../logs/log-query-overview.md) használatával.


## <a name="uninstall"></a>Eltávolítás

Az Office 365 felügyeleti megoldás eltávolításához használja a [felügyeleti megoldás eltávolítása](solutions.md#remove-a-monitoring-solution)című eljárást. Ez nem állítja le az Office 365-ből gyűjtött adatokat a Azure Monitor mégis. Kövesse az alábbi eljárást az Office 365 lemondásához és az adatok gyűjtésének leállításához.

1. Mentse a következő parancsfájlt *office365_unsubscribe.ps1ként*.

    ```powershell
    param (
        [Parameter(Mandatory=$True)][string]$WorkspaceName,
        [Parameter(Mandatory=$True)][string]$ResourceGroupName,
        [Parameter(Mandatory=$True)][string]$SubscriptionId,
        [Parameter(Mandatory=$True)][string]$OfficeTennantId
    )
    $line='#-------------------------------------------------------------------------------------------------------------------------------------------------------------------------'
    
    $line
    IF ($Subscription -eq $null)
        {Login-AzAccount -ErrorAction Stop}
    $Subscription = (Select-AzSubscription -SubscriptionId $($SubscriptionId) -ErrorAction Stop)
    $Subscription
    $option = [System.StringSplitOptions]::RemoveEmptyEntries 
    $Workspace = (Set-AzOperationalInsightsWorkspace -Name $($WorkspaceName) -ResourceGroupName $($ResourceGroupName) -ErrorAction Stop)
    $Workspace
    $WorkspaceLocation= $Workspace.Location
    
    # Client ID for Azure PowerShell
    $clientId = "1950a258-227b-4e31-a9cf-717495945fc2"
    # Set redirect URI for Azure PowerShell
    $redirectUri = "urn:ietf:wg:oauth:2.0:oob"
    $domain='login.microsoftonline.com'
    $adTenant =  $Subscription[0].Tenant.Id
    $authority = "https://login.windows.net/$adTenant";
    $ARMResource ="https://management.azure.com/";
    $xms_client_tenant_Id ='55b65fb5-b825-43b5-8972-c8b6875867c1'
    
    switch ($WorkspaceLocation) {
           "USGov Virginia" { 
                             $domain='login.microsoftonline.us';
                              $authority = "https://login.microsoftonline.us/$adTenant";
                              $ARMResource ="https://management.usgovcloudapi.net/"; break} # US Gov Virginia
           default {
                    $domain='login.microsoftonline.com'; 
                    $authority = "https://login.windows.net/$adTenant";
                    $ARMResource ="https://management.azure.com/";break} 
                    }
    
    Function RESTAPI-Auth { 
    
    $global:SubscriptionID = $Subscription.SubscriptionId
    # Set Resource URI to Azure Service Management API
    $resourceAppIdURIARM=$ARMResource;
    # Authenticate and Acquire Token 
    # Create Authentication Context tied to Azure AD Tenant
    $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $authority
    # Acquire token
    $platformParameters = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.PlatformParameters" -ArgumentList "Auto"
    $global:authResultARM = $authContext.AcquireTokenAsync($resourceAppIdURIARM, $clientId, $redirectUri, $platformParameters)
    $global:authResultARM.Wait()
    $authHeader = $global:authResultARM.Result.CreateAuthorizationHeader()
    $authHeader
    }
    
    Function Office-UnSubscribe-Call{
    
    #----------------------------------------------------------------------------------------------------------------------------------------------
    $authHeader = $global:authResultARM.Result.CreateAuthorizationHeader()
    $ResourceName = "https://manage.office.com"
    $SubscriptionId   = $Subscription[0].Subscription.Id
    $OfficeAPIUrl = $ARMResource + 'subscriptions/' + $SubscriptionId + '/resourceGroups/' + $ResourceGroupName + '/providers/Microsoft.OperationalInsights/workspaces/' + $WorkspaceName + '/datasources/office365datasources_'  + $SubscriptionId + $OfficeTennantId + '?api-version=2015-11-01-preview'
    
    $Officeparams = @{
        ContentType = 'application/json'
        Headers = @{
        'Authorization'="$($authHeader)"
        'x-ms-client-tenant-id'=$xms_client_tenant_Id
        'Content-Type' = 'application/json'
        }
        Method = 'Delete'
        URI = $OfficeAPIUrl
      }
    
    $officeresponse = Invoke-WebRequest @Officeparams 
    $officeresponse
    
    }
    
    #GetDetails 
    RESTAPI-Auth -ErrorAction Stop
    Office-UnSubscribe-Call -ErrorAction Stop
    ```

2. Futtassa a szkriptet a következő paranccsal:

    ```
    .\office365_unsubscribe.ps1 -WorkspaceName <Log Analytics workspace name> -ResourceGroupName <Resource Group name> -SubscriptionId <Subscription ID> -OfficeTennantID <Tenant ID> 
    ```

    Példa:

    ```powershell
    .\office365_unsubscribe.ps1 -WorkspaceName MyWorkspace -ResourceGroupName MyResourceGroup -SubscriptionId '60b79d74-f4e4-4867-b631-yyyyyyyyyyyy' -OfficeTennantID 'ce4464f8-a172-4dcf-b675-xxxxxxxxxxxx'
    ```

A rendszer kérni fogja a hitelesítő adatokat. Adja meg a Log Analytics munkaterület hitelesítő adatait.

## <a name="data-collection"></a>Adatgyűjtés

Az adatok kezdetben való gyűjtése igénybe vehet néhány órát. A gyűjtés megkezdése után az Office 365 egy [webhook-értesítést](/office/office-365-management-api/office-365-management-activity-api-reference#receiving-notifications) küld a részletes adatokkal, hogy Azure monitor minden alkalommal, amikor létrejön egy rekord. Ez a rekord a kézhezvételt követően néhány percen belül Azure Monitor érhető el.

## <a name="using-the-solution"></a>A megoldás használata

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

Amikor hozzáadja az Office 365-megoldást a Log Analytics munkaterülethez, az **office 365** csempe hozzá lesz adva az irányítópulthoz. Ez a csempe a környezetben jelenleg elérhető számítógépek számát és grafikus ábrázolását jeleníti meg, valamint a frissítési megfelelőségi állapotukat.<br><br>
![Office 365 összefoglaló csempe](media/solution-office-365/tile.png)  

Kattintson az **office 365** csempére az **Office 365** irányítópultjának megnyitásához.

![Office 365 irányítópult](media/solution-office-365/dashboard.png)  

Az irányítópulton az alábbi táblázatban felsorolt oszlopok találhatóak. Minden oszlop felsorolja az első tíz riasztást az oszlopnak a megadott hatókörhöz és időtartományhoz tartozó feltételeinek megfelelő számokkal. Futtathat egy naplóbeli keresést, amely a teljes listát az oszlop alján található az összes megjelenítése elemre kattintva vagy az oszlop fejlécére kattintva jeleníti meg.

| Oszlop | Leírás |
|:--|:--|
| Üzemeltetés | Az összes figyelt Office 365-előfizetés aktív felhasználóival kapcsolatos információkat nyújt. Megtekintheti az idő múlásával zajló tevékenységek számát is.
| Exchange | Megjeleníti az Exchange Server-tevékenységek részletezését, például Add-Mailbox engedélyt vagy a set-Mailbox műveletet. |
| SharePoint | Megjeleníti a felhasználók által a SharePoint-dokumentumokon végrehajtott legfontosabb tevékenységeket. Ha ezen a csempén végez részletezést, a Keresés lap megjeleníti a tevékenységek részleteit, például a célként megadott dokumentumot és a tevékenység helyét. Egy fájlhoz hozzáférő esemény esetében például megtekintheti az elérni kívánt dokumentumot, a hozzá tartozó fióknevet és IP-címet. |
| Azure Active Directory | Magában foglalja a leggyakoribb felhasználói tevékenységeket, például a felhasználói jelszavak visszaállítását és a bejelentkezési kísérleteket. A részletezés során látni fogja a tevékenységek részleteit, például az eredmény állapotát. Ez általában akkor hasznos, ha a Azure Active Directory gyanús tevékenységeket szeretne figyelni. |




## <a name="azure-monitor-log-records"></a>Naplóbejegyzések Azure Monitor

Az Office 365-megoldás által Azure Monitor Log Analytics munkaterületen létrehozott összes rekord **típusa** **OfficeActivity**.  A **OfficeWorkload** tulajdonság határozza meg, hogy melyik Office 365-szolgáltatás a rekord a-Exchange, a AzureActiveDirectory, a SharePoint vagy a OneDrive kifejezésre hivatkozik.  A **rekordtípus** tulajdonság meghatározza a művelet típusát.  A tulajdonságok minden egyes Művelettípus esetében eltérőek lesznek, és az alábbi táblázatokban jelennek meg.

### <a name="common-properties"></a>Közös tulajdonságok

A következő tulajdonságok minden Office 365-rekordnál közösek.

| Tulajdonság | Leírás |
|:--- |:--- |
| Típus | *OfficeActivity* |
| Ügyfélip | Az esemény naplózásakor használt eszköz IP-címe. Az IP-cím IPv4 vagy IPv6 formátumban jelenik meg. |
| OfficeWorkload | Az Office 365 szolgáltatás, amelyre a rekord hivatkozik.<br><br>AzureActiveDirectory<br>Exchange<br>SharePoint|
| Művelet | A felhasználói vagy rendszergazdai tevékenység neve.  |
| OrganizationId (Szervezeti azonosító) | A szervezet Office 365-bérlője GUID azonosítója. Ez az érték mindig ugyanaz lesz a szervezetnél, függetlenül attól, hogy melyik Office 365-szolgáltatásban történik. |
| RecordType (Rekordtípus) | A végrehajtott művelet típusa. |
| ResultStatus (Eredmény állapota) | Azt jelzi, hogy a (Művelet tulajdonságban megadott) művelet sikeres volt-e. A lehetséges értékek sikeresek, PartiallySucceeded vagy sikertelenek. Exchange-rendszergazdai tevékenység esetén az érték igaz vagy hamis. |
| UserId (Felhasználóazonosító) | A naplózni kívánt műveletet végrehajtó felhasználó UPN-neve (egyszerű felhasználónév); például: my_name@my_domain_name . Vegye figyelembe, hogy a rendszerfiókok (például SHAREPOINT\system vagy NTAUTHORITY\SYSTEM) által végzett tevékenységekre vonatkozó rekordok is szerepelnek. | 
| UserKey (Felhasználói kulcs) | A felhasználóazonosító tulajdonságban azonosított felhasználó alternatív azonosítója.  Ez a tulajdonság például a Passport egyedi azonosítójával (PUID) van feltöltve a felhasználók által a SharePointban, a vállalati OneDrive és az Exchange-ben végrehajtott eseményekhez. Ez a tulajdonság a felhasználóazonosító tulajdonsággal megegyező értéket is megadhat a más szolgáltatásokban és a rendszerfiókokban végrehajtott eseményekben előforduló események esetében.|
| UserType (Felhasználótípus) | A műveletet végrehajtó felhasználó típusa.<br><br>Rendszergazda<br>Alkalmazás<br>DcAdmin<br>Szabályos<br>Fenntartva<br>Szolgáltatásnév<br>Rendszer |


### <a name="azure-active-directory-base"></a>Azure Active Directory alapja

A következő tulajdonságok minden Azure Active Directory rekordnál közösek.

| Tulajdonság | Leírás |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType (Rekordtípus)     | AzureActiveDirectory |
| AzureActiveDirectory_EventType | Az Azure AD-esemény típusa. |
| Extendedproperties példányt paraméterként | Az Azure AD-esemény kiterjesztett tulajdonságai. |


### <a name="azure-active-directory-account-logon"></a>Azure Active Directory fiók bejelentkezése

Ezek a rekordok akkor jönnek létre, amikor egy Active Directory felhasználó megpróbál bejelentkezni.

| Tulajdonság | Leírás |
|:--- |:--- |
| `OfficeWorkload` | AzureActiveDirectory |
| `RecordType`     | AzureActiveDirectoryAccountLogon |
| `Application` | Az alkalmazás, amely elindítja a fiók bejelentkezési eseményét, például: Office 15. |
| `Client` | A fiók bejelentkezési eseményéhez használt ügyféleszközök, eszköz operációs rendszer és eszköz böngésző adatai. |
| `LoginStatus` | Ez a tulajdonság a OrgIdLogon. LoginStatus közvetlenül származik. A különböző érdekes bejelentkezési hibák leképezése a riasztási algoritmusokkal végezhető el. |
| `UserDomain` | A bérlői azonosító információi (TII). | 


### <a name="azure-active-directory"></a>Azure Active Directory

Ezek a rekordok akkor jönnek létre, amikor módosítást vagy kiegészítést végeznek Azure Active Directory objektumokhoz.

| Tulajdonság | Leírás |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType (Rekordtípus)     | AzureActiveDirectory |
| AADTarget | A felhasználó, aki a műveletet (a művelet tulajdonsága azonosítja) elvégezte. |
| Színész | Az a felhasználó vagy szolgáltatásnév, amely végrehajtotta a műveletet. |
| ActorContextId | Annak a szervezetnek a GUID azonosítója, amelyhez a szereplő tartozik. |
| ActorIpAddress | A szereplő IP-címe IPV4-vagy IPV6-cím formátumban. |
| InterSystemsId | A GUID-azonosító, amely az Office 365 szolgáltatásban található összetevők közötti műveleteket nyomon követi. |
| IntraSystemId |     A Azure Active Directory által generált GUID a művelet nyomon követéséhez. |
| SupportTicketId | Az ügyfél-támogatási jegy azonosítója a művelethez "működés közbeni" helyzetekben. |
| TargetContextId | Annak a szervezetnek a GUID azonosítója, amelyhez a megcélozott felhasználó tartozik. |


### <a name="data-center-security"></a>Adatközpont biztonsága

Ezeket a rekordokat az adatközpont biztonsági naplózási adatai alapján hozza létre a rendszer.  

| Tulajdonság | Leírás |
|:--- |:--- |
| EffectiveOrganization | Annak a bérlőnek a neve, amelyhez a Jogosultságszint-emelés vagy a parancsmag irányult. |
| ElevationApprovedTime | A Jogosultságszint-emelés jóváhagyásának időbélyegzője. |
| ElevationApprover | A Microsoft Manager neve. |
| ElevationDuration | Az az időtartam, ameddig a Jogosultságszint-emelés aktív volt. |
| ElevationRequestId |     A jogosultságszint-emelési kérelem egyedi azonosítója. |
| ElevationRole | A jogosultságszint-emelést kérő szerepkör. |
| ElevationTime | A Jogosultságszint-emelés kezdési időpontja. |
| Start_Time | A parancsmag végrehajtásának kezdési időpontja. |


### <a name="exchange-admin"></a>Exchange-rendszergazda

Ezek a rekordok akkor jönnek létre, amikor módosításokat végeznek az Exchange-konfigurációban.

| Tulajdonság | Leírás |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType (Rekordtípus)     | ExchangeAdmin |
| ExternalAccess |     Megadja, hogy a parancsmagot a szervezet egy felhasználója, a Microsoft Datacenter személyzete vagy egy adatközpont-szolgáltatásfiók vagy egy meghatalmazott rendszergazda futtatta-e. A False érték azt jelzi, hogy a parancsmagot a szervezet egy személye futtatta. Az igaz érték azt jelzi, hogy a parancsmagot az adatközpont munkatársai, egy adatközpont-szolgáltatásfiók vagy meghatalmazott rendszergazda futtatták. |
| ModifiedObjectResolvedName |     Ez a parancsmag által módosított objektum felhasználóbarát neve. Ezt csak akkor naplózza a rendszer, ha a parancsmag módosítja az objektumot. |
| OrganizationName | A bérlő neve. |
| OriginatingServer | Annak a kiszolgálónak a neve, amelyből a parancsmag végre lett hajtva. |
| Paraméterek | Az Operations tulajdonságban azonosított parancsmaggal használt összes paraméter neve és értéke. |


### <a name="exchange-mailbox"></a>Exchange-postaláda

Ezek a rekordok akkor jönnek létre, amikor az Exchange-postaládák módosításai vagy kiegészítései történnek.

| Tulajdonság | Leírás |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType (Rekordtípus)     | ExchangeItem |
| ClientInfoString | A művelet végrehajtásához használt e-mail-ügyféllel kapcsolatos információk, például a böngésző verziószáma, az Outlook verziója és a mobileszköz-információ. |
| Client_IPAddress | Annak az eszköznek az IP-címe, amelyet a művelet naplózásakor használt. Az IP-cím IPv4 vagy IPv6 formátumban jelenik meg. |
| ClientMachineName | Az Outlook-ügyfelet futtató számítógépnév. |
| ClientProcessName | A postaláda elérésére használt e-mail ügyfélprogram. |
| ClientVersion | Az e-mail ügyfélprogram verziója. |
| InternalLogonType | Belső használatra fenntartva. |
| Logon_Type | Megadja a postaládához hozzáférő felhasználó típusát, és végrehajtotta a naplózott műveletet. |
| LogonUserDisplayName |     A műveletet végrehajtó felhasználó felhasználóbarát neve. |
| LogonUserSid | A műveletet végrehajtó felhasználó biztonsági azonosítója. |
| MailboxGuid | Az elérni kívánt postaláda Exchange GUID azonosítója. |
| MailboxOwnerMasterAccountSid | A postaláda-tulajdonos fiókjának fő fiókja SID. |
| MailboxOwnerSid | A postaláda tulajdonosának biztonsági azonosítója. |
| MailboxOwnerUPN | Az elérni kívánt postaládát birtokló személy e-mail-címe. |


### <a name="exchange-mailbox-audit"></a>Exchange-postaláda naplózása

Ezek a rekordok a postaláda-naplózási bejegyzések létrehozásakor jönnek létre.

| Tulajdonság | Leírás |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType (Rekordtípus)     | ExchangeItem |
| Elem | Azt az elemeket jelöli, amelyen a műveletet elvégezték | 
| SendAsUserMailboxGuid | Azon postaláda Exchange GUID azonosítója, amelyet a rendszer az e-mailek küldésére használt. |
| SendAsUserSmtp | A megszemélyesíteni kívánt felhasználó SMTP-címe. |
| SendonBehalfOfUserMailboxGuid | Azon postaláda Exchange GUID azonosítója, amelyet a rendszer a levél nevében küldött e-mailek küldéséhez. |
| SendOnBehalfOfUserSmtp | Annak a felhasználónak az SMTP-címe, akinek az e-mail-címét elküldi. |


### <a name="exchange-mailbox-audit-group"></a>Exchange postaláda-naplózási csoport

Ezek a rekordok akkor jönnek létre, amikor változtatásokat vagy kiegészítéseket végeznek az Exchange-csoportokban.

| Tulajdonság | Leírás |
|:--- |:--- |
| OfficeWorkload | Exchange |
| OfficeWorkload | ExchangeItemGroup |
| AffectedItems | Információk a csoport egyes elemeiről. |
| CrossMailboxOperations | Azt jelzi, hogy a művelet több postaládát is érintett-e. |
| DestMailboxId | Csak akkor állítható be, ha a CrossMailboxOperations paraméter értéke TRUE (igaz). Megadja a cél postaláda GUID azonosítóját. |
| DestMailboxOwnerMasterAccountSid | Csak akkor állítható be, ha a CrossMailboxOperations paraméter értéke TRUE (igaz). Meghatározza a postaláda tulajdonosának fő fiókjának SID-azonosítóját. |
| DestMailboxOwnerSid | Csak akkor állítható be, ha a CrossMailboxOperations paraméter értéke TRUE (igaz). Megadja a cél postaláda SID-azonosítóját. |
| DestMailboxOwnerUPN | Csak akkor állítható be, ha a CrossMailboxOperations paraméter értéke TRUE (igaz). Megadja a cél postaláda tulajdonosának egyszerű felhasználónevét. |
| DestFolder | A célmappa olyan műveletek esetén, mint például az áthelyezés. |
| Mappa | Az a mappa, ahol az elemek csoportja található. |
| Mappák |     A műveletben érintett forrás mappákkal kapcsolatos információk; például ha a mappák elem ki van választva, majd törölve lett. |


### <a name="sharepoint-base"></a>SharePoint-alap

Ezek a tulajdonságok az összes SharePoint-rekord esetében azonosak.

| Tulajdonság | Leírás |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| EventSource | Azt azonosítja, hogy egy esemény történt a SharePointban. A lehetséges értékek a következők: SharePoint vagy ObjectModel. |
| ItemType | Az elért vagy módosított objektum típusa. Az objektumok típusairól a ItemType táblázatban talál további információt. |
| MachineDomainInfo | Az eszköz-szinkronizálási műveletekkel kapcsolatos információk. Ezek az adatok csak akkor jelennek meg, ha szerepelnek a kérelemben. |
| MachineId |     Az eszköz-szinkronizálási műveletekkel kapcsolatos információk. Ezek az adatok csak akkor jelennek meg, ha szerepelnek a kérelemben. |
| Site_ | Annak a helynek a GUID azonosítója, ahol a felhasználó által elért fájl vagy mappa található. |
| Source_Name | Az a entitás, amely kiváltotta a naplózott műveletet. A lehetséges értékek a következők: SharePoint vagy ObjectModel. |
| UserAgent | A felhasználó ügyfelének vagy böngészőjének adatai. Ezt az információt az ügyfél vagy a böngésző biztosít. |


### <a name="sharepoint-schema"></a>SharePoint-séma

Ezek a rekordok akkor jönnek létre, amikor konfigurációs módosításokat végeznek a SharePointban.

| Tulajdonság | Leírás |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| CustomEvent | Nem kötelező karakterlánc az egyéni eseményekhez. |
| Event_Data |     Nem kötelező adattartalom az egyéni eseményekhez. |
| ModifiedProperties | A tulajdonságot a felügyeleti események tartalmazzák, például a felhasználók hozzáadása egy helyhez vagy egy webhelycsoport-felügyeleti csoport tagjaként. A tulajdonság tartalmazza a módosított tulajdonság nevét (például a hely felügyeleti csoportját), a módosított tulajdonság új értékét (például a hely rendszergazdájaként hozzáadott felhasználót) és a módosított objektum előző értékét. |


### <a name="sharepoint-file-operations"></a>SharePoint-fájl műveletei

Ezek a rekordok a SharePoint-fájl műveleteire adott válaszként jönnek létre.

| Tulajdonság | Leírás |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePointFileOperation |
| DestinationFileExtension | A másolt vagy áthelyezett fájl kiterjesztése. Ez a tulajdonság csak a FileCopied és a FileMoved eseményekhez jelenik meg. |
| DestinationFileName | A másolt vagy áthelyezett fájl neve. Ez a tulajdonság csak a FileCopied és a FileMoved eseményekhez jelenik meg. |
| DestinationRelativeUrl | Annak a célmappanek az URL-címe, ahová a fájl másolása vagy áthelyezése történik. A SiteURL, a DestinationRelativeURL és a DestinationFileName paraméterek értékeinek kombinációja megegyezik a ObjectID tulajdonság értékével, amely a másolt fájl teljes elérési útja. Ez a tulajdonság csak a FileCopied és a FileMoved eseményekhez jelenik meg. |
| SharingType | A felhasználóhoz hozzárendelt megosztási engedélyek típusa, amelyekkel az erőforrás meg lett osztva. Ezt a felhasználót a UserSharedWith paraméter azonosítja. |
| Site_Url | Annak a helynek az URL-címe, ahol a felhasználó által elért fájl vagy mappa található. |
| SourceFileExtension | A felhasználó által elért fájl kiterjesztése. Ez a tulajdonság üres, ha az elérni kívánt objektum egy mappa. |
| SourceFileName |     A felhasználó által elért fájl vagy mappa neve. |
| SourceRelativeUrl | A felhasználó által elért fájlt tartalmazó mappa URL-címe. A SiteURL, a SourceRelativeURL és a SourceFileName paraméterek értékeinek kombinációja megegyezik a ObjectID tulajdonság értékével, amely a felhasználó által elért fájl teljes elérési útja. |
| UserSharedWith |     Az a felhasználó, aki az erőforrást megosztotta. |




## <a name="sample-log-queries"></a>Példák a naplók lekérdezésére

A következő táblázat példákat tartalmaz a megoldás által gyűjtött frissítési rekordok lekérdezésére.

| Lekérdezés | Description |
| --- | --- |
|Az Office 365-előfizetés összes műveletének száma |OfficeActivity &#124; összegzések száma () művelet szerint |
|SharePoint-webhelyek használata|OfficeActivity &#124;, ahol a OfficeWorkload = ~ "SharePoint" &#124; összesítések száma () SiteUrl szerint \| rendezve|
|Fájl-hozzáférési műveletek felhasználói típus szerint | OfficeActivity &#124; összegzések száma () UserType szerint |
|Külső műveletek figyelése az Exchange-ben|OfficeActivity &#124;, ahol OfficeWorkload = ~ "Exchange" és ExternalAccess = = True|



## <a name="next-steps"></a>Következő lépések

* A részletes frissítési információk megtekintéséhez használja [a Azure monitor a naplózási lekérdezéseket](../logs/log-query-overview.md) .
* [Saját irányítópultok létrehozásával](../visualize/tutorial-logs-dashboards.md) megjelenítheti kedvenc Office 365 keresési lekérdezéseit.
* [Riasztásokat hozhat létre](../alerts/alerts-overview.md) , amelyekkel proaktívan értesítheti a fontos Office 365-tevékenységeket.  
