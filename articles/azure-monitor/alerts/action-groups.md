---
title: Műveletcsoportok létrehozása és felügyelete az Azure Portalon
description: Megtudhatja, hogyan hozhat létre és kezelhet műveletcsoportokat a Azure Portal.
author: dkamstra
ms.topic: conceptual
ms.date: 04/07/2021
ms.author: dukek
ms.openlocfilehash: 1486415c5d225163dd2b2c7e79cd008ad0a76588
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107514869"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Műveletcsoportok létrehozása és felügyelete az Azure Portalon
A műveletcsoport az Azure-előfizetés tulajdonosa által meghatározott értesítési beállítások gyűjteménye. Azure Monitor és Service Health riasztások műveletcsoportokkal értesítik a felhasználókat arról, hogy egy riasztás aktiválódott. A különböző riasztások a felhasználói követelményektől függően ugyanazt a műveletcsoportot vagy különböző műveletcsoportokat is használhatnak. 

Ez a cikk bemutatja, hogyan hozhat létre és kezelhet műveletcsoportokat a Azure Portal.

Minden művelet a következő tulajdonságokból áll:

* **Típus:** A végrehajtott értesítés vagy művelet. Ilyenek például a hanghívások, az SMS-ek és az e-mailek küldése; vagy különböző típusú automatizált műveleteket aktiválhat. A típusokról a cikk egy későbbi, későbbi, cikkében olvashat.
* **Név:** A műveletcsoporton belüli egyedi azonosító.
* **Részletek:** A típusonként eltérő megfelelő *részletek.*

További információ a műveletcsoportok Azure Resource Manager sablonok használatával való konfigurálásról: [Műveletcsoport Resource Manager sablonok.](./action-groups-create-resource-manager-template.md)

## <a name="create-an-action-group-by-using-the-azure-portal"></a>Műveletcsoport létrehozása a Azure Portal

1. A [Azure Portal](https://portal.azure.com)keresse meg és válassza a **Figyelás lehetőséget.** A **Figyelés** panel egyetlen nézetben összesíteni fogja az összes figyelési beállítást és adatot.

1. Válassza **a Riasztások,** majd a **Műveletek kezelése lehetőséget.**

    ![Műveletek kezelése gomb](./media/action-groups/manage-action-groups.png)
    
1. Válassza **a Műveletcsoport hozzáadása lehetőséget,** és töltse ki a megfelelő mezőket a varázslóban.

    ![A "Műveletcsoport hozzáadása" parancs](./media/action-groups/add-action-group.PNG)

### <a name="configure-basic-action-group-settings"></a>Alapszintű műveletcsoport-beállítások konfigurálása

A **Projekt részletei alatt:**

Válassza ki **azt az előfizetést** **és** erőforráscsoportot, amelyben a műveletcsoport mentve van.

A **Példány részletei alatt:**

1. Adjon meg **egy műveletcsoport-nevet.**

1. Adjon meg **egy megjelenítendő nevet.** Amikor ezzel a csoporttal küld értesítéseket, a rendszer a megjelenített nevet használja a műveletcsoport teljes neve helyett.

      ![A Műveletcsoport hozzáadása" párbeszédpanel](./media/action-groups/action-group-1-basics.png)


### <a name="configure-notifications"></a>Értesítések konfigurálása

1. Kattintson **a Tovább: Értesítések >** gombra az  Értesítések lapra lépéshez, vagy válassza az Értesítések lapot a képernyő tetején. 

1. Definiálja a riasztások aktivált értesítési listáját. Minden értesítéshez adja meg a következőket:

    a. **Értesítés típusa:** Válassza ki a küldeni kívánt értesítés típusát. Az elérhető lehetőségek:
      * E-Azure Resource Manager szerepkör – E-mail küldése bizonyos előfizetés-szintű ARM-szerepkörökhöz rendelt felhasználóknak.
      * E-mail/SMS/Leküldéses/Hang – Ezeket az értesítéstípusokat adott címzetteknek küldheti el.
    
    b. **Név:** Adjon meg egy egyedi nevet az értesítésnek.

    c. **Részletek:** A kiválasztott értesítéstípus alapján adjon meg egy e-mail-címet, egy telefonszámot stb.
    
    d. **Gyakori riasztási séma:** Engedélyezheti [](./alerts-common-schema.md)a gyakori riasztási sémát, amely egyetlen, a riasztási szolgáltatásban lévő összes riasztási szolgáltatásban egyetlen, egységesített hasznos Azure Monitor.

    ![Az Értesítések lap](./media/action-groups/action-group-2-notifications.png)
    
### <a name="configure-actions"></a>Műveletek konfigurálása

1. Kattintson **a Tovább: Műveletek >** gombra  a Műveletek lapra lépéshez, vagy válassza a Műveletek lapot a képernyő tetején. 

1. Definiálja a riasztás aktivált műveletlistát. Minden művelethez adja meg a következőket:

    a. **Művelet típusa:** Válassza ki az Automation-runbookot, az Azure-függvényt, az ITSM-et, a logikai alkalmazást, a biztonságos webhookot és a webhookot.
    
    b. **Név:** Adjon meg egy egyedi nevet a művelethez.

    c. **Részletek:** A művelet típusától függően adjon meg egy webhook URI-t, egy Azure-alkalmazást, egy ITSM-kapcsolatot vagy egy Automation-runbookot. Az ITSM-művelethez adja meg továbbá a **Munkaelemet** és az ITSM-eszköz által megkövetelt egyéb mezőket.
    
    d. **Gyakori riasztási séma:** Engedélyezheti [](./alerts-common-schema.md)a gyakori riasztási sémát, amely egyetlen, a riasztási szolgáltatásban lévő összes riasztási szolgáltatásban egyetlen, egységesített hasznos Azure Monitor.
    
    ![A Műveletek lap](./media/action-groups/action-group-3-actions.png)

### <a name="create-the-action-group"></a>A műveletcsoport létrehozása

1. Ha kívánja, végignézheti a **Címkék** lapot is. Ez lehetővé teszi kulcs/érték párok társítását a műveletcsoporthoz a kategorizáláshoz, és bármely Azure-erőforráshoz elérhető szolgáltatás.

    ![A Címkék lap](./media/action-groups/action-group-4-tags.png)
    
1. A beállítások áttekintéséhez kattintson a **Felülvizsgálat + létrehozás** elemre. Ezzel gyorsan érvényesíti a bemeneteket, hogy biztosan az összes kötelező mező ki legyen választva. Ha valami hibádzik, az ezen a ponton megjelenik. A beállítások áttekintése után kattintson a Létrehozás **gombra** a műveletcsoport létrehozásához.
    
    ![Az Áttekintés és létrehozás lap](./media/action-groups/action-group-5-review.png)

> [!NOTE]
> Amikor úgy konfigurál egy műveletet, hogy e-mailben vagy SMS-ben értesítsen egy személyt, a műveletcsoporthoz való hozzáadásról megerősítést kap.

## <a name="manage-your-action-groups"></a>A műveletcsoportok kezelése

Miután létrehozott egy műveletcsoportot,  a Műveletcsoportok megtekintéséhez válassza a Műveletek kezelése lehetőséget a Figyelés panel **Riasztások** **kezdőlapján.**  Válassza ki a kezelni kívánt műveletcsoportot:

* Műveletek hozzáadása, szerkesztése vagy eltávolítása.
* Törölje a műveletcsoportot.

## <a name="action-specific-information"></a>Műveletspecifikus információk

> [!NOTE]
> Az [alábbi elemekre vonatkozó](../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-monitor-limits) numerikus korlátokért lásd: Subscription Service Limits for Monitoring (Előfizetési szolgáltatási korlátok a monitorozáshoz).  

### <a name="automation-runbook"></a>Automation Runbook
A Runbook hasznos adatokra vonatkozó korlátozásokkal kapcsolatban tekintse meg az [Azure-előfizetés](../../azure-resource-manager/management/azure-subscription-service-limits.md) szolgáltatási korlátait.

Egy műveletcsoportban korlátozott számú Runbook-művelet lehet. 

### <a name="azure-app-push-notifications"></a>Azure-alkalmazás leküldéses értesítései
Engedélyezze a leküldéses értesítéseket a Azure mobile app [a](https://azure.microsoft.com/features/azure-portal/mobile-app/) fiókazonosítóként használt e-mail-cím meg Azure mobile app.

Egy műveletcsoportban korlátozott számú Azure-alkalmazásműveletet használhat.

### <a name="email"></a>E-mail
Az e-mailek a következő e-mail-címekről lesznek elküldve. Győződjön meg arról, hogy az e-mail-szűrés megfelelően van konfigurálva
- azure-noreply@microsoft.com
- azureemail-noreply@microsoft.com
- alerts-noreply@mail.windowsazure.com

A műveletcsoportban korlátozott számú e-mail-művelet lehet. Lásd a [sebességkorlátozásról tájékoztató](./alerts-rate-limiting.md) cikket.

### <a name="email-azure-resource-manager-role"></a>Azure Resource Manager-szerepkör küldése e-mailben
Küldjön e-mailt az előfizetés szerepkörének tagjainak. A rendszer csak a szerepkör **Azure AD-felhasználói** tagjainak küld e-mailt. Az Azure AD-csoportok és -szolgáltatásnevek nem kapják meg az e-mailt.

A rendszer csak az elsődleges e-mail-címre küld *értesítő e-mailt.*

Ha nem kap értesítéseket az elsődleges *e-mail-címére,* próbálkozzon a következő lépésekkel:

1. A Azure Portal *a* Active Directory.
2. Kattintson a Minden felhasználó elemre (a bal oldali panelen), és megjelenik a felhasználók listája (a jobb oldali panelen).
3. Válassza ki azt a felhasználót, aki számára meg szeretné vizsgálni az *elsődleges e-mail-információkat.*

  :::image type="content" source="media/action-groups/active-directory-user-profile.png" alt-text="Példa a felhasználói profil áttekintésre." border="true":::

4. Ha az "E-mail" lap üres a Kapcsolattartási adatok alatt a  Felhasználói profil  alatt, kattintson felül a Szerkesztés gombra, adja hozzá az elsődleges e-mail-címét, és kattintson felül a Mentés gombra. 

  :::image type="content" source="media/action-groups/active-directory-add-primary-email.png" alt-text="Példa az elsődleges e-mailek hozzáadására." border="true":::

A műveletcsoportban korlátozott számú e-mail-művelet lehet. Lásd a [sebességkorlátozásról tájékoztató](./alerts-rate-limiting.md) cikket.

Az *E-mail ARM-szerepkör beállításakor* meg kell győződni arról, hogy az alábbi 3 feltétel teljesül:

1. A szerepkörhöz rendelt entitás típusának **"Felhasználó"** típusúnak kell lennie.
2. A hozzárendelést az előfizetés szintjén **kell** tenni.
3. A felhasználónak konfigurálnia kell egy e-mailt az **AAD-profiljában.** 


### <a name="function"></a>Függvény
Egy meglévő HTTP-eseményindító végpontját hívja meg a [Azure Functions.](../../azure-functions/functions-get-started.md) A kérések kezeléshez a végpontnak kell kezelnie a HTTP POST-műveleteket.

Előfordulhat, hogy egy műveletcsoportban korlátozott számú függvényművelet van.

### <a name="itsm"></a>ITSM
Az ITSM-művelethez ITSM-kapcsolat szükséges. Útmutató [ITSM-kapcsolat létrehozásához.](./itsmc-overview.md)

Egy műveletcsoportban korlátozott számú ITSM-művelet lehet. 

### <a name="logic-app"></a>Logikai alkalmazás
Egy műveletcsoportban korlátozott számú logikaialkalmazás-művelet lehet.

### <a name="secure-webhook"></a>Biztonságos webhook
A Műveletcsoportok biztonságos webhookja művelet lehetővé teszi, hogy kihasználja az Azure Active Directory előnyeit a műveletcsoport és a védett webes API (webhook-végpont) közötti kapcsolat védelméhez. A funkció előnyeinek kihasználása teljes munkafolyamatát az alábbiakban ismertetjük. Az Azure AD-alkalmazások és -szolgáltatásnévk áttekintését lásd: [A Microsoft identitásplatformjának (v2.0) áttekintése.](../../active-directory/develop/v2-overview.md)

> [!NOTE]
> A webhookművelet használatához a cél webhook végpontjának vagy nem szükséges a riasztás részleteinek a sikeres működéséhez, vagy képes a POST művelet részeként megadott riasztáskörnyezeti információknak a végrehajtásához. Ha [a](./action-groups-logic-app.md) webhook végpontja nem tudja egyedül kezelni a riasztási környezet adatait, egy logikai alkalmazáshoz hasonló megoldást használhat a riasztási környezet adatainak egyéni kezeléséhez a webhook várt adatformátumának megfelelően.

1. Hozzon létre egy Azure AD-alkalmazást a védett webes API-hoz. Lásd: [Védett webes API: Alkalmazásregisztráció.](../../active-directory/develop/scenario-protected-web-api-app-registration.md)
    - Konfigurálja a védett API-t úgy, hogy [egy démonalkalmazás hívja meg.](../../active-directory/develop/scenario-protected-web-api-app-registration.md#if-your-web-api-is-called-by-a-daemon-app)
    
2. Engedélyezze a műveletcsoportok számára az Azure AD-alkalmazás használatát.

    > [!NOTE]
    > A szkript végrehajtásához [az Azure AD](../../active-directory/roles/permissions-reference.md#all-roles) alkalmazás-rendszergazdai szerepkörének kell lennie.
    
    - Módosítsa a PowerShell-szkript Connect-AzureAD az Azure AD-bérlőazonosító használatára.
    - Módosítsa a PowerShell-szkript változóját úgy$myAzureADApplicationObjectId hogy az Azure AD-alkalmazás objektumazonosítóját használja.
    - Futtassa a módosított szkriptet.
    
3. Konfigurálja a Műveletcsoport biztonságos webhook műveletét.
    - Másolja ki a $myApp.ObjectId értéket a szkriptből, és adja meg a Webhook-művelet definíciójának Application Object ID (Alkalmazásobjektum-azonosító) mezőjében.
    
    ![Biztonságos webhook művelet](./media/action-groups/action-groups-secure-webhook.png)

#### <a name="secure-webhook-powershell-script"></a>Biztonságos webhook PowerShell-szkript

```PowerShell
Connect-AzureAD -TenantId "<provide your Azure AD tenant ID here>"
    
# This is your Azure AD Application's ObjectId. 
$myAzureADApplicationObjectId = "<the Object ID of your Azure AD Application>"
    
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
    
Write-Host "My Azure AD Application (ObjectId): " + $myApp.ObjectId
Write-Host "My Azure AD Application's Roles"
Write-Host $myApp.AppRoles
```

### <a name="sms"></a>SMS
További fontos információkért tekintse meg a [sebességkorlátozási információkat](./alerts-rate-limiting.md) és [az SMS-riasztások](./alerts-sms-behavior.md) viselkedését. 

Előfordulhat, hogy egy műveletcsoportban korlátozott számú SMS-művelet van.

> [!NOTE]
> Ha a Azure Portal felhasználói felülete nem teszi lehetővé az ország/régió kód kiválasztását, akkor az SMS nem támogatott az ön országa/régiója esetén.  Ha az ország-/régiókód nem érhető el, szavazhat, hogy az országa/régiója felhasználói hangon [legyen hozzáadva.](https://feedback.azure.com/forums/913690-azure-monitor/suggestions/36663181-add-more-country-codes-for-sms-alerting-and-voice) Addig is meg kell dolgozni, hogy a műveletcsoport hívja meg a webhookot egy külső SMS-szolgáltatóhoz, amely az Ön országában/régiójában nyújt támogatást.  

A támogatott országok/régiók díjszabását a következő [díjszabási Azure Monitor sorolja fel:](https://azure.microsoft.com/pricing/details/monitor/).

**Azon országok listája, ahol az SMS-értesítések támogatottak**

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
További fontos [viselkedést a sebességkorlátozásról](./alerts-rate-limiting.md) tájékoztató cikkben talál.

Előfordulhat, hogy egy műveletcsoportban korlátozott számú hangművelet van.

> [!NOTE]
> Ha a Azure Portal felhasználói felülete nem teszi lehetővé az ország/régió kód kiválasztását, akkor a hanghívások nem támogatottak az ön országa/régiója esetén. Ha az ország-/régiókód nem érhető el, szavazhat, hogy az országa/régiója felhasználói hangon [legyen hozzáadva.](https://feedback.azure.com/forums/913690-azure-monitor/suggestions/36663181-add-more-country-codes-for-sms-alerting-and-voice)  Addig is meg kell dolgozni, hogy a műveletcsoport hívja meg a webhookot egy külső hanghívás-szolgáltatóhoz, amely támogatást nyújt az ön országában/régiójában.  
> A Hangértesítés műveletcsoportban jelenleg csak a Azure Portal támogatott országkód +1(Egyesült Államok). 

A támogatott országok/régiók díjszabását a következő [díjszabási Azure Monitor sorolja fel:](https://azure.microsoft.com/pricing/details/monitor/).

### <a name="webhook"></a>Webhook

> [!NOTE]
> A webhookművelet használatához a cél webhook végpontjának vagy nincs szüksége a riasztás részleteire a sikeres működéshez, vagy képes a POST művelet részeként megadott riasztáskörnyezet-információk végrehajtásához. Ha [a](./action-groups-logic-app.md) webhook-végpont nem tudja egyedül kezelni a riasztási környezet adatait, a logikai alkalmazáshoz hasonló megoldást használhat a riasztási környezet adatainak egyéni manipulálása érdekében, hogy az megfeleljen a webhook várt adatformátumának.

A webhookok feldolgozása az alábbi szabályokkal
- A rendszer legfeljebb 3 alkalommal kísérel meg webhookhívást.
- A hívás akkor lesz újraküldve, ha a válasz nem érkezik meg az időkorláton belül, vagy a következő HTTP-állapotkódok valamelyike lesz visszaadva: 408, 429, 503 vagy 504.
- Az első hívás 10 másodpercet vár a válaszra.
- A második és a harmadik kísérlet 30 másodpercet vár a válaszra.
- Miután a webhook hívásának 3. kísérlete meghiúsult, egyetlen műveletcsoport sem hívja meg a végpontot 15 percig.

A forrás [IP-címtartományokat lásd: Műveletcsoport](../app/ip-addresses.md) IP-címei.


## <a name="next-steps"></a>Következő lépések
* További információ az [SMS-riasztások viselkedéséről.](./alerts-sms-behavior.md)  
* A [tevékenységnapló-riasztás webhooksémának ismertetése.](./activity-log-alerts-webhook.md)  
* További információ a [ITSM-csatoló.](./itsmc-overview.md)
* További információ a [riasztások sebességkorlátozásról.](./alerts-rate-limiting.md)
* Áttekintheti [a tevékenységnapló-riasztásokat,](./alerts-overview.md)és megtudhatja, hogyan kaphat riasztásokat.  
* Ismerje meg, hogyan [konfigurálhatja a riasztásokat, amikor szolgáltatás állapotértesítést küld.](../../service-health/alerts-activity-log-service-notifications-portal.md)
