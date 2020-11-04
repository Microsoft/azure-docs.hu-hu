---
title: Műveletcsoportok létrehozása és kezelése az Azure Portalon
description: Megtudhatja, hogyan hozhat létre és kezelhet műveleti csoportokat a Azure Portalban.
author: dkamstra
ms.topic: conceptual
ms.date: 07/28/2020
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: cbe3ef7080397b135ea0fb17cd50de9b1ccfdf09
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2020
ms.locfileid: "93336119"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Műveletcsoportok létrehozása és kezelése az Azure Portalon
A műveleti csoport az Azure-előfizetés tulajdonosa által meghatározott értesítési beállítások gyűjteménye. Azure Monitor és Service Health riasztások használata műveleti csoportok segítségével értesíti a felhasználókat arról, hogy riasztást váltott ki. A különböző riasztások ugyanazt a műveleti csoportot vagy különböző műveleti csoportokat használhatják a felhasználó igényeitől függően. Egy előfizetésben akár 2 000 műveleti csoportot is beállíthat.

Ebből a cikkből megtudhatja, hogyan hozhat létre és kezelhet műveleti csoportokat a Azure Portalban.

Az egyes műveletek a következő tulajdonságokből állnak:

* **Típus** : az értesítés vagy művelet elvégezve. Ilyenek például a hanghívások, SMS-üzenetek, e-mailek küldése, vagy különböző típusú automatizált műveletek elindítása. Lásd a cikk későbbi részében található típusokat.
* **Name (név** ): a műveleti csoporton belüli egyedi azonosító.
* **Részletek** : a megfelelő részletek *típus* szerint változnak.

További információ arról, hogyan használhatók Azure Resource Manager sablonok a műveleti csoportok konfigurálásához: [Action Group Resource Manager-sablonok](./action-groups-create-resource-manager-template.md).

## <a name="create-an-action-group-by-using-the-azure-portal"></a>Műveleti csoport létrehozása a Azure Portal használatával

1. A [Azure Portal](https://portal.azure.com)keresse meg és válassza a **figyelő** elemet. A **figyelő** ablaktábla egyetlen nézetben összesíti az összes figyelési beállítást és az adatait.

1. Válassza a **riasztások** , majd a **műveletek kezelése** lehetőséget.

    ![Műveletek kezelése gomb](./media/action-groups/manage-action-groups.png)
    
1. Válassza a **műveleti csoport hozzáadása** lehetőséget, és töltse ki a megfelelő mezőket a varázsló felhasználói felületén.

    ![A "műveleti csoport hozzáadása" parancs](./media/action-groups/add-action-group.PNG)

### <a name="configure-basic-action-group-settings"></a>Alapszintű műveleti csoport beállításainak konfigurálása

A **Project details** :

Válassza ki azt az **előfizetést** és **erőforráscsoportot** , amelyben a műveleti csoportot menti.

A **példány részletei** területen:

1. Adja meg a **műveleti csoport nevét**.

1. Adja meg a **megjelenítendő nevet**. A megjelenített név a teljes műveleti csoport neve helyett használatos, ha az értesítéseket a csoport használatával küldi el a rendszer.

      ![A műveleti csoport hozzáadása párbeszédpanel](./media/action-groups/action-group-1-basics.png)


### <a name="configure-notifications"></a>Értesítések konfigurálása

1. Kattintson a **Tovább: értesítések >** gombra az **értesítések** lapra való áttéréshez, vagy válassza a képernyő felső részén található **értesítések** fület.

1. A riasztások indításakor küldendő értesítések listájának megadása. Minden értesítéshez adja meg a következőket:

    a. **Értesítés típusa** : válassza ki az elküldeni kívánt értesítés típusát. Az elérhető lehetőségek:
      * E-mail-Azure Resource Manager szerepkör – e-mail küldése az egyes előfizetési szintű ARM-szerepkörökhöz rendelt felhasználóknak.
      * E-mail/SMS/leküldés/hang – ezeket az értesítési típusokat megadott címzetteknek küldje el.
    
    b. **Név** : adjon meg egy egyedi nevet az értesítéshez.

    c. **Részletek** : a kiválasztott értesítési típus alapján írjon be egy e-mail-címet, telefonszámot stb.
    
    d. **Gyakori riasztási séma** : engedélyezheti a [közös riasztási sémát](./alerts-common-schema.md), amely lehetővé teszi, hogy egyetlen bővíthető és egységesített riasztási adattartalmat biztosítson a Azure monitor összes riasztási szolgáltatásában.

    ![Az értesítések lap](./media/action-groups/action-group-2-notifications.png)
    
### <a name="configure-actions"></a>Műveletek konfigurálása

1. Kattintson a **következőre: műveletek >** gombra a **műveletek** lapra való áthelyezéshez, vagy válassza a képernyő felső részén található **műveletek** fület.

1. Megadhatja a riasztások aktiválásakor aktiválható műveletek listáját. Minden művelethez adja meg a következőket:

    a. **Művelettípus** : válassza az Automation Runbook, az Azure Function, a ITSM, a Logic app, a Secure webhook és a webhook elemet.
    
    b. **Név** : adjon egyedi nevet a műveletnek.

    c. **Részletek** : a Művelettípus alapján adjon meg egy webhook URI-t, egy Azure-alkalmazást, egy ITSM-kapcsolat vagy egy Automation-runbook. A ITSM művelethez emellett adja meg a **munkaelemet** és a ITSM eszköz által igényelt egyéb mezőket.
    
    d. **Gyakori riasztási séma** : engedélyezheti a [közös riasztási sémát](./alerts-common-schema.md), amely lehetővé teszi, hogy egyetlen bővíthető és egységesített riasztási adattartalmat biztosítson a Azure monitor összes riasztási szolgáltatásában.
    
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

### <a name="email-azure-resource-manager-role"></a>E-mail – Azure Resource Manager-szerepkör
E-mail küldése az előfizetés szerepkörének tagjainak. A rendszer csak az **Azure ad-felhasználók** számára küld e-mailt a szerepkörhöz. Az Azure AD-csoportok és -szolgáltatásnevek nem kapják meg az e-mailt.

Az értesítő e-mailt csak az *elsődleges e-mail* -címre küldi a rendszer.

A műveleti csoportban korlátozott számú e-mail művelet lehet. Tekintse meg a [díjszabási információkat](./alerts-rate-limiting.md) ismertető cikket.

### <a name="function"></a>Függvény
Meghívja a meglévő HTTP-trigger végpontját [Azure Functionsban](../../azure-functions/functions-create-first-azure-function.md#create-a-function-app).

A műveleti csoportban korlátozott számú Function művelet lehet.

### <a name="itsm"></a>ITSM
A ITSM művelethez ITSM-kapcsolat szükséges. Megtudhatja, hogyan hozhat létre [ITSM-kapcsolatokat](./itsmc-overview.md).

A műveleti csoportban korlátozott számú ITSM művelet lehet. 

### <a name="logic-app"></a>Logikai alkalmazás
A műveleti csoportban korlátozott számú Logic app-művelet lehet.

### <a name="secure-webhook"></a>Biztonságos webhook
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
  

### <a name="voice"></a>Hang
További fontos viselkedésért tekintse meg a [díjszabási információkat](./alerts-rate-limiting.md) ismertető cikket.

A műveleti csoportban korlátozott számú hangművelet lehet.

> [!NOTE]
> Ha az Azure Portal műveleti csoport felhasználói felülete nem teszi lehetővé az ország/régió kódjának kiválasztását, akkor a hanghívások nem támogatottak az országa/régiója számára. Ha az ország/régió kódja nem érhető el, szavazással kiválaszthatja, hogy az országa/régiója a [felhasználó beszédében](https://feedback.azure.com/forums/913690-azure-monitor/suggestions/36663181-add-more-country-codes-for-sms-alerting-and-voice)van-e hozzáadva.  Addig is a megoldás, hogy a műveleti csoport meghívja a webhookot egy külső gyártótól származó hanghívási szolgáltatóra, amely támogatja az országot/régiót.  

A támogatott országok/régiók díjszabását a [Azure monitor díjszabási oldalán](https://azure.microsoft.com/pricing/details/monitor/)találja.

### <a name="webhook"></a>Webhook
A webhookok feldolgozása a következő szabályok alapján történik
- A webhook hívása legfeljebb 3 alkalommal próbálkozik.
- A rendszer újrapróbálkozik a hívással, ha nem érkezik válasz az időtúllépési időszakon belül, vagy a következő HTTP-állapotkódok egyike érkezik: 408, 429, 503 vagy 504.
- Az első hívás 10 másodpercet vár a válaszra.
- A második és a harmadik próbálkozás 30 másodpercet vár a válaszra.
- Miután a 3 kísérlet a webhook meghívására nem sikerült, a műveleti csoport 15 percig nem fogja hívni a végpontot.

Forrás IP-címtartományok
 - 13.72.19.232
 - 13.106.57.181
 - 13.106.54.3
 - 13.106.54.19
 - 13.106.38.142
 - 13.106.38.148
 - 13.106.57.196
 - 13.106.57.197
 - 52.244.68.117
 - 52.244.65.137
 - 52.183.31.0
 - 52.184.145.166
 - 51.4.138.199
 - 51.5.148.86
 - 51.5.149.19

Ha frissítéseket szeretne kapni ezen IP-címek változásairól, javasoljuk, hogy állítson be egy Service Health riasztást, amely figyeli a műveleti csoportok szolgáltatással kapcsolatos tájékoztató értesítéseket.

Előfordulhat, hogy egy műveleti csoportban korlátozott számú webhook-művelet van.

A forrás IP-címek gyakori frissítései elég időt igényelnek a webhookban. A *ActionGroup* **szolgáltatással** való használata megkönnyíti az IP-címek gyakori frissítéseinek manuális összetettségét. A fent megosztva a forrás IP-címek tartományának előtagjait a Microsoft automatikusan felügyeli a **Service tag** által felölelt módon.

#### <a name="service-tag"></a>Szolgáltatás címkéje
A szolgáltatás címkéje egy adott Azure-szolgáltatás IP-címeinek egy csoportját jelöli. A Microsoft kezeli a szolgáltatási címke által felölelt címek előtagjait, és automatikusan frissíti a szolgáltatás címkéjét a címek változásával, minimalizálva a ActionGroup vonatkozó hálózati biztonsági szabályok gyakori frissítéseinek összetettségét.

1. Azure Portal az Azure-szolgáltatások területen keresse meg a *hálózati biztonsági csoportot*.
2. Kattintson a **Hozzáadás** gombra, és hozzon létre egy hálózati biztonsági csoportot.

   1. Adja hozzá az erőforráscsoport nevét, majd adja meg a *példány részleteit*.
   1. Kattintson a **felülvizsgálat + létrehozás** elemre, majd a *Létrehozás* gombra.
   
   :::image type="content" source="media/action-groups/action-group-create-security-group.png" alt-text="Példa hálózati biztonsági csoport létrehozására."border="true":::

3. Nyissa meg az erőforráscsoportot, majd kattintson a létrehozott *hálózati biztonsági csoportra* .

    1. Válassza a *bejövő biztonsági szabályok* lehetőséget.
    1. Kattintson a **Hozzáadás** gombra.
    
    :::image type="content" source="media/action-groups/action-group-add-service-tag.png" alt-text="Példa szolgáltatási címke hozzáadására."border="true":::

4. Ekkor megnyílik egy új ablak a jobb oldali ablaktáblán.
    1.  Forrás kiválasztása: **szolgáltatás címkéje**
    1.  Forrásoldali szolgáltatás címkéje: **ActionGroup**
    1.  Kattintson a **Hozzáadás** parancsra.
    
    :::image type="content" source="media/action-groups/action-group-service-tag.png" alt-text="Példa a szolgáltatási címke hozzáadására."border="true":::

## <a name="next-steps"></a>További lépések
* További információ az [SMS-riasztás viselkedéséről](./alerts-sms-behavior.md).  
* Ismerkedjen meg [a tevékenység naplójának riasztása webhook sémával](./activity-log-alerts-webhook.md).  
* További információ a [ITSM-csatolóról](./itsmc-overview.md).
* További információ a riasztások [díjszabásának korlátozásáról](./alerts-rate-limiting.md) .
* [Tekintse át a tevékenységek naplójának riasztásait](./alerts-overview.md), és Ismerje meg, hogyan fogadhat riasztásokat.  
* Megtudhatja, hogyan [konfigurálhatja a riasztásokat, amikor egy szolgáltatás állapotáról értesítést küldenek](../../service-health/alerts-activity-log-service-notifications-portal.md).
