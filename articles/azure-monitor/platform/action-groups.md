---
title: Műveletcsoportok létrehozása és felügyelete az Azure Portalon
description: Megtudhatja, hogyan hozhat létre és kezelhet műveleti csoportokat a Azure Portalban.
author: dkamstra
ms.topic: conceptual
ms.date: 01/28/2021
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: 08cf66edaa67ab1853a3b246afb9364b431445c6
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2021
ms.locfileid: "99055110"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Műveletcsoportok létrehozása és felügyelete az Azure Portalon
A műveleti csoport az Azure-előfizetés tulajdonosa által meghatározott értesítési beállítások gyűjteménye. Azure Monitor és Service Health riasztások használata műveleti csoportok segítségével értesíti a felhasználókat arról, hogy riasztást váltott ki. A különböző riasztások ugyanazt a műveleti csoportot vagy különböző műveleti csoportokat használhatják a felhasználó igényeitől függően. 

Ebből a cikkből megtudhatja, hogyan hozhat létre és kezelhet műveleti csoportokat a Azure Portalban.

Az egyes műveletek a következő tulajdonságokből állnak:

* **Típus**: az értesítés vagy művelet elvégezve. Ilyenek például a hanghívások, SMS-üzenetek, e-mailek küldése, vagy különböző típusú automatizált műveletek elindítása. Lásd a cikk későbbi részében található típusokat.
* **Name (név**): a műveleti csoporton belüli egyedi azonosító.
* **Részletek**: a megfelelő részletek *típus* szerint változnak.

További információ arról, hogyan használhatók Azure Resource Manager sablonok a műveleti csoportok konfigurálásához: [Action Group Resource Manager-sablonok](./action-groups-create-resource-manager-template.md).

## <a name="create-an-action-group-by-using-the-azure-portal"></a>Műveleti csoport létrehozása a Azure Portal használatával

1. A [Azure Portal](https://portal.azure.com)keresse meg és válassza a **figyelő** elemet. A **figyelő** ablaktábla egyetlen nézetben összesíti az összes figyelési beállítást és az adatait.

1. Válassza a **riasztások**, majd a **műveletek kezelése** lehetőséget.

    ![Műveletek kezelése gomb](./media/action-groups/manage-action-groups.png)
    
1. Válassza a **műveleti csoport hozzáadása** lehetőséget, és töltse ki a megfelelő mezőket a varázsló felhasználói felületén.

    ![A "műveleti csoport hozzáadása" parancs](./media/action-groups/add-action-group.PNG)

### <a name="configure-basic-action-group-settings"></a>Alapszintű műveleti csoport beállításainak konfigurálása

A **Project details**:

Válassza ki azt az **előfizetést** és **erőforráscsoportot** , amelyben a műveleti csoportot menti.

A **példány részletei** területen:

1. Adja meg a **műveleti csoport nevét**.

1. Adja meg a **megjelenítendő nevet**. A megjelenített név a teljes műveleti csoport neve helyett használatos, ha az értesítéseket a csoport használatával küldi el a rendszer.

      ![A műveleti csoport hozzáadása párbeszédpanel](./media/action-groups/action-group-1-basics.png)


### <a name="configure-notifications"></a>Értesítések konfigurálása

1. Kattintson a **Tovább: értesítések >** gombra az **értesítések** lapra való áttéréshez, vagy válassza a képernyő felső részén található **értesítések** fület.

1. A riasztások indításakor küldendő értesítések listájának megadása. Minden értesítéshez adja meg a következőket:

    a. **Értesítés típusa**: válassza ki az elküldeni kívánt értesítés típusát. Az elérhető lehetőségek:
      * E-mail-Azure Resource Manager szerepkör – e-mail küldése az egyes előfizetési szintű ARM-szerepkörökhöz rendelt felhasználóknak.
      * E-mail/SMS/leküldés/hang – ezeket az értesítési típusokat megadott címzetteknek küldje el.
    
    b. **Név**: adjon meg egy egyedi nevet az értesítéshez.

    c. **Részletek**: a kiválasztott értesítési típus alapján írjon be egy e-mail-címet, telefonszámot stb.
    
    d. **Gyakori riasztási séma**: engedélyezheti a [közös riasztási sémát](./alerts-common-schema.md), amely lehetővé teszi, hogy egyetlen bővíthető és egységesített riasztási adattartalmat biztosítson a Azure monitor összes riasztási szolgáltatásában.

    ![Az értesítések lap](./media/action-groups/action-group-2-notifications.png)
    
### <a name="configure-actions"></a>Műveletek konfigurálása

1. Kattintson a **következőre: műveletek >** gombra a **műveletek** lapra való áthelyezéshez, vagy válassza a képernyő felső részén található **műveletek** fület.

1. Megadhatja a riasztások aktiválásakor aktiválható műveletek listáját. Minden művelethez adja meg a következőket:

    a. **Művelettípus**: válassza az Automation Runbook, az Azure Function, a ITSM, a Logic app, a Secure webhook és a webhook elemet.
    
    b. **Név**: adjon egyedi nevet a műveletnek.

    c. **Részletek**: a Művelettípus alapján adjon meg egy webhook URI-t, egy Azure-alkalmazást, egy ITSM-kapcsolat vagy egy Automation-runbook. A ITSM művelethez emellett adja meg a **munkaelemet** és a ITSM eszköz által igényelt egyéb mezőket.
    
    d. **Gyakori riasztási séma**: engedélyezheti a [közös riasztási sémát](./alerts-common-schema.md), amely lehetővé teszi, hogy egyetlen bővíthető és egységesített riasztási adattartalmat biztosítson a Azure monitor összes riasztási szolgáltatásában.
    
    ![A műveletek lap](./media/action-groups/action-group-3-actions.png)

### <a name="create-the-action-group"></a>A műveleti csoport létrehozása

1. Ha kívánja, végignézheti a **Címkék** lapot is. Ez lehetővé teszi a kulcs/érték párok hozzárendelését a kategorizáláshoz tartozó műveleti csoportba, és bármely Azure-erőforrás számára elérhető funkciót.

    ![A címkék lap](./media/action-groups/action-group-4-tags.png)
    
1. A beállítások áttekintéséhez kattintson a **Felülvizsgálat + létrehozás** elemre. Ezzel gyorsan érvényesítheti a bemeneteket, így meggyőződhet arról, hogy az összes szükséges mező ki van választva. Ha valami hibádzik, az ezen a ponton megjelenik. Miután áttekintette a beállításokat, kattintson a **Létrehozás** elemre a műveleti csoport kiépítéséhez.
    
    ![A felülvizsgálat + Létrehozás lap](./media/action-groups/action-group-5-review.png)

> [!NOTE]
> Ha olyan műveletet konfigurál, amely e-mailben vagy SMS-ben értesíti a személyeket, a rendszer megerősítést küld, amely jelzi, hogy hozzá lettek adva a műveleti csoporthoz.

## <a name="manage-your-action-groups"></a>A műveleti csoportok kezelése

A műveleti csoport létrehozása után megtekintheti a **műveleti csoportokat** a **figyelés** ablaktábla **riasztások** kezdőlapján a **műveletek kezelése** lehetőség kiválasztásával. Válassza ki a kezelni kívánt műveleti csoportot:

* Műveletek hozzáadása, szerkesztése vagy eltávolítása.
* A műveleti csoport törlése.

## <a name="action-specific-information"></a>Műveletre vonatkozó információk

> [!NOTE]
> Az alábbi elemeken megtekintheti az [előfizetési szolgáltatás korlátozásait](../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-monitor-limits) a numerikus korlátok figyeléséhez.  

### <a name="automation-runbook"></a>Automation Runbook
Tekintse meg az [Azure-előfizetési szolgáltatási](../../azure-resource-manager/management/azure-subscription-service-limits.md) korlátokat a Runbook-adattartalomra vonatkozó korlátozásokkal kapcsolatban.

A műveleti csoportban korlátozott számú Runbook művelet lehet. 

### <a name="azure-app-push-notifications"></a>Azure-alkalmazás leküldéses értesítései
Előfordulhat, hogy egy műveleti csoportban korlátozott számú Azure-alkalmazási művelet van.

### <a name="email"></a>E-mail
A rendszer e-maileket küld a következő e-mail-címekről. Győződjön meg arról, hogy az e-mail-szűrés megfelelően van konfigurálva
- azure-noreply@microsoft.com
- azureemail-noreply@microsoft.com
- alerts-noreply@mail.windowsazure.com

A műveleti csoportban korlátozott számú e-mail művelet lehet. Tekintse meg a [díjszabási információkat](./alerts-rate-limiting.md) ismertető cikket.

### <a name="email-azure-resource-manager-role"></a>Azure Resource Manager-szerepkör küldése e-mailben
E-mail küldése az előfizetés szerepkörének tagjainak. A rendszer csak az **Azure ad-felhasználók** számára küld e-mailt a szerepkörhöz. Az Azure AD-csoportok és -szolgáltatásnevek nem kapják meg az e-mailt.

Az értesítő e-mailt csak az *elsődleges e-mail* -címre küldi a rendszer.

Ha nem kap értesítéseket az *elsődleges e-mail-címére*, akkor a következő lépéseket teheti meg:

1. Azure Portal nyissa meg a *Active Directory*.
2. Kattintson a minden felhasználó elemre (a bal oldali ablaktáblában), és megjelenik a felhasználók listája (a jobb oldali ablaktáblában).
3. Válassza ki azt a felhasználót, amelynek az *elsődleges e-mail-* adatait szeretné áttekinteni.

  :::image type="content" source="media/action-groups/active-directory-user-profile.png" alt-text="Példa a felhasználói profil áttekintésére."border="true":::

4. Ha a felhasználói profilban a kapcsolattartási adatok területen az "E-mail" lap üres, akkor kattintson a felül található *Szerkesztés* gombra, és adja hozzá az *elsődleges e-mailt* , és nyomja meg a *Mentés* gombot a felső részen.

  :::image type="content" source="media/action-groups/active-directory-add-primary-email.png" alt-text="Példa az elsődleges e-mailek hozzáadására."border="true":::

A műveleti csoportban korlátozott számú e-mail művelet lehet. Tekintse meg a [díjszabási információkat](./alerts-rate-limiting.md) ismertető cikket.

### <a name="function"></a>Függvény
Meghívja a meglévő HTTP-trigger végpontját [Azure Functionsban](../../azure-functions/functions-get-started.md).

A műveleti csoportban korlátozott számú Function művelet lehet.

### <a name="itsm"></a>ITSM
A ITSM művelethez ITSM-kapcsolat szükséges. Megtudhatja, hogyan hozhat létre [ITSM-kapcsolatokat](./itsmc-overview.md).

A műveleti csoportban korlátozott számú ITSM művelet lehet. 

### <a name="logic-app"></a>Logikai alkalmazás
A műveleti csoportban korlátozott számú Logic app-művelet lehet.

### <a name="secure-webhook"></a>Biztonságos webhook

> [!NOTE]
> A webhook művelet végrehajtásához a cél webhook-végpontnak nem kell megadnia a riasztás részleteit a sikeres működéshez, vagy képesnek kell lennie a POST művelet részeként megadott riasztási környezeti információk elemzésére. Ha a webhook-végpont nem tudja önállóan kezelni a riasztási környezet információit, használhat egy olyan megoldást is, mint a [Logic app-művelet](./action-groups-logic-app.md) a riasztási környezet adatainak egyéni manipulációja érdekében, hogy megfeleljen a webhook várt adatformátumának.
> A felhasználónak a webhook szolgáltatásnév **tulajdonosának** kell lennie annak érdekében, hogy a biztonság ne legyen megsértve. Mivel bármely Azure-ügyfél hozzáférhet a portálon keresztül az összes objektum-azonosítóhoz, a tulajdonos ellenőrzése nélkül bárki felveheti a biztonságos webhookot a saját műveleti csoportjához az Azure Monitor riasztási értesítéseire, amely megsérti a biztonságot.

A műveleti csoportok webhook művelettel kihasználhatja a Azure Active Directory előnyeit a műveleti csoport és a védett webes API (webhook-végpont) közötti kapcsolat biztonságossá tételéhez. A funkció kihasználásának általános munkafolyamata alább olvasható. Az Azure AD-alkalmazások és-szolgáltatások áttekintését lásd: [Microsoft Identity platform (v 2.0) – áttekintés](../../active-directory/develop/v2-overview.md).

1. Hozzon létre egy Azure AD-alkalmazást a védett webes API-hoz. Tekintse meg a [védett webes API: alkalmazás regisztrációja](../../active-directory/develop/scenario-protected-web-api-app-registration.md)című témakört.
    - Beállíthatja, hogy a védett API-t [egy Daemon-alkalmazás hívja](../../active-directory/develop/scenario-protected-web-api-app-registration.md#if-your-web-api-is-called-by-a-daemon-app)meg.
    
2. Engedélyezze a műveleti csoportokat az Azure AD-alkalmazás használatához.

    > [!NOTE]
    > A szkript végrehajtásához az [Azure ad alkalmazás-rendszergazda szerepkör](../../active-directory/roles/permissions-reference.md#available-roles) tagjának kell lennie.
    
    - Módosítsa a PowerShell-parancsfájl Connect-AzureAD hívását az Azure AD-bérlő AZONOSÍTÓjának használatára.
    - Módosítsa a PowerShell-parancsfájl változóját $myAzureADApplicationObjectId az Azure AD-alkalmazás Object ID-azonosítójának használatára.
    - Futtassa a módosított parancsfájlt.
    
3. Konfigurálja a műveleti csoport biztonságos webhook műveletét.
    - Másolja a $myApp. ObjectId értéket a parancsfájlból, és adja meg a webhook műveleti definíciójának Application Object ID mezőjében.
    
    ![Biztonságos webhook művelet](./media/action-groups/action-groups-secure-webhook.png)

#### <a name="secure-webhook-powershell-script"></a>Webhook PowerShell-parancsfájl biztonságossá tétele

```PowerShell
Connect-AzureAD -TenantId "<provide your Azure AD tenant ID here>"
    
# This is your Azure AD Application's ObjectId. 
$myAzureADApplicationObjectId = "<the Object Id of your Azure AD Application>"
    
# This is the Action Groups Azure AD AppId
$actionGroupsAppId = "461e8683-5575-4561-ac7f-899cc907d62a"
    
# This is the name of the new role we will add to your Azure AD Application
$actionGroupRoleName = "ActionGroupsSecureWebhook"
    
# Create an application role of given name and description
Function CreateAppRole([string] $Name, [string] $Description)
{
    $appRole = New-Object Microsoft.Open.AzureAD.Model.AppRole
    $appRole.AllowedMemberTypes = New-Object System.Collections.Generic.List[string]
    $appRole.AllowedMemberTypes.Add("Application");
    $appRole.DisplayName = $Name
    $appRole.Id = New-Guid
    $appRole.IsEnabled = $true
    $appRole.Description = $Description
    $appRole.Value = $Name;
    return $appRole
}
    
# Get my Azure AD Application, it's roles and service principal
$myApp = Get-AzureADApplication -ObjectId $myAzureADApplicationObjectId
$myAppRoles = $myApp.AppRoles
$actionGroupsSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $actionGroupsAppId + "'")

Write-Host "App Roles before addition of new role.."
Write-Host $myAppRoles
    
# Create the role if it doesn't exist
if ($myAppRoles -match "ActionGroupsSecureWebhook")
{
    Write-Host "The Action Groups role is already defined.`n"
}
else
{
    $myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")
    
    # Add our new role to the Azure AD Application
    $newRole = CreateAppRole -Name $actionGroupRoleName -Description "This is a role for Action Groups to join"
    $myAppRoles.Add($newRole)
    Set-AzureADApplication -ObjectId $myApp.ObjectId -AppRoles $myAppRoles
}
    
# Create the service principal if it doesn't exist
if ($actionGroupsSP -match "AzNS AAD Webhook")
{
    Write-Host "The Service principal is already defined.`n"
}
else
{
    # Create a service principal for the Action Groups Azure AD Application and add it to the role
    $actionGroupsSP = New-AzureADServicePrincipal -AppId $actionGroupsAppId
}
    
New-AzureADServiceAppRoleAssignment -Id $myApp.AppRoles[0].Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $actionGroupsSP.ObjectId -PrincipalId $actionGroupsSP.ObjectId
    
Write-Host "My Azure AD Application ($myApp.ObjectId): " + $myApp.ObjectId
Write-Host "My Azure AD Application's Roles"
Write-Host $myApp.AppRoles
```

### <a name="sms"></a>SMS
További fontos információk: az információk és az [SMS-riasztások viselkedésének](./alerts-sms-behavior.md) [korlátozása](./alerts-rate-limiting.md) . 

A műveleti csoportban korlátozott számú SMS-művelet lehet.

> [!NOTE]
> Ha az Azure Portal műveleti csoport felhasználói felülete nem teszi lehetővé az ország/régió kódjának kiválasztását, akkor az országa/régiója nem támogatja az SMS-t.  Ha az ország/régió kódja nem érhető el, szavazással kiválaszthatja, hogy az országa/régiója a [felhasználó beszédében](https://feedback.azure.com/forums/913690-azure-monitor/suggestions/36663181-add-more-country-codes-for-sms-alerting-and-voice)van-e hozzáadva. Addig is a megoldás, hogy a műveleti csoport meghívja a webhookot egy harmadik féltől származó SMS-szolgáltatóra, amely támogatja az országot/régiót.  

A támogatott országok/régiók díjszabását a [Azure monitor díjszabási oldalán](https://azure.microsoft.com/pricing/details/monitor/)találja.

**Azon országok listája, amelyeken az SMS-értesítés támogatott**

| Országkód | Ország neve |
|:---|:---|
| 61 | Ausztrália |
| 43 | Ausztria |
| 32 | Belgium |
| 55 | Brazília |
| 1 |Kanada |
| 56 | Chile |
| 86 | Kína |
| 420 | Cseh Köztársaság |
| 45 | Dánia |
| 372 | Észtország |
| 358 | Finnország |
| 33 | Franciaország |
| 49 | Németország |
| 852 | Hongkong |
| 91 | India |
| 353 | Írország |
| 972 | Izrael |
| 39 | Olaszország |
| 81 | Japán |
| 352 | Luxemburg |
| 60 | Malajzia |
| 52 | Mexikó |
| 31 | Hollandia |
| 64 | Új-Zéland |
| 47 | Norvégia |
| 351 | Portugália |
| 1 | Puerto Rico |
| 40 | Románia |
| 65 | Szingapúr |
| 27 | Dél-afrikai Köztársaság |
| 82 | Dél-Korea |
| 34 | Spanyolország |
| 41 | Svájc |
| 886 | Tajvan |
| 44 | Egyesült Királyság |
| 1 | Egyesült Államok |

### <a name="voice"></a>Hang
További fontos viselkedésért tekintse meg a [díjszabási információkat](./alerts-rate-limiting.md) ismertető cikket.

A műveleti csoportban korlátozott számú hangművelet lehet.

> [!NOTE]
> Ha az Azure Portal műveleti csoport felhasználói felülete nem teszi lehetővé az ország/régió kódjának kiválasztását, akkor a hanghívások nem támogatottak az országa/régiója számára. Ha az ország/régió kódja nem érhető el, szavazással kiválaszthatja, hogy az országa/régiója a [felhasználó beszédében](https://feedback.azure.com/forums/913690-azure-monitor/suggestions/36663181-add-more-country-codes-for-sms-alerting-and-voice)van-e hozzáadva.  Addig is a megoldás, hogy a műveleti csoport meghívja a webhookot egy külső gyártótól származó hanghívási szolgáltatóra, amely támogatja az országot/régiót.  
> Csak a Azure Portal műveleti csoportban jelenleg támogatott országkód a következő: + 1 (Egyesült Államok). 

A támogatott országok/régiók díjszabását a [Azure monitor díjszabási oldalán](https://azure.microsoft.com/pricing/details/monitor/)találja.

### <a name="webhook"></a>Webhook

> [!NOTE]
> A webhook művelet végrehajtásához a cél webhook-végpontnak nem kell megadnia a riasztás részleteit a sikeres működéshez, vagy képesnek kell lennie a POST művelet részeként megadott riasztási környezeti információk elemzésére. Ha a webhook-végpont nem tudja önállóan kezelni a riasztási környezet információit, használhat egy olyan megoldást is, mint a [Logic app-művelet](./action-groups-logic-app.md) a riasztási környezet adatainak egyéni manipulációja érdekében, hogy megfeleljen a webhook várt adatformátumának.

A webhookok feldolgozása a következő szabályok alapján történik
- A webhook hívása legfeljebb 3 alkalommal próbálkozik.
- A rendszer újrapróbálkozik a hívással, ha nem érkezik válasz az időtúllépési időszakon belül, vagy a következő HTTP-állapotkódok egyike érkezik: 408, 429, 503 vagy 504.
- Az első hívás 10 másodpercet vár a válaszra.
- A második és a harmadik próbálkozás 30 másodpercet vár a válaszra.
- Miután a 3 kísérlet a webhook meghívására nem sikerült, a műveleti csoport 15 percig nem fogja hívni a végpontot.

Tekintse meg a [műveleti csoport IP](../app/ip-addresses.md) -címeit a forrás IP-címtartományok számára.


## <a name="next-steps"></a>Következő lépések
* További információ az [SMS-riasztás viselkedéséről](./alerts-sms-behavior.md).  
* Ismerkedjen meg [a tevékenység naplójának riasztása webhook sémával](./activity-log-alerts-webhook.md).  
* További információ a [ITSM-csatolóról](./itsmc-overview.md).
* További információ a riasztások [díjszabásának korlátozásáról](./alerts-rate-limiting.md) .
* [Tekintse át a tevékenységek naplójának riasztásait](./alerts-overview.md), és Ismerje meg, hogyan fogadhat riasztásokat.  
* Megtudhatja, hogyan [konfigurálhatja a riasztásokat, amikor egy szolgáltatás állapotáról értesítést küldenek](../../service-health/alerts-activity-log-service-notifications-portal.md).
