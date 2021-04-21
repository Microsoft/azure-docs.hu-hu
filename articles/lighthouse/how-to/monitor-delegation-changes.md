---
title: Delegálás változásainak figyelése a kezelő bérlőben
description: Ismerje meg, hogyan figyelheti a delegálást az ügyfélbérlőktől a kezelő bérlőhöz.
ms.date: 02/18/2021
ms.topic: how-to
ms.openlocfilehash: 1a12b916fae9794d6d695191a81ec076917bda31
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814891"
---
# <a name="monitor-delegation-changes-in-your-managing-tenant"></a>Delegálás változásainak figyelése a kezelő bérlőben

Szolgáltatóként előfordulhat, hogy tudnia kell, ha az ügyfél-előfizetéseket vagy erőforráscsoportokat az [Azure Lighthouse-n](../overview.md)keresztül delegálta a bérlőjéhez, vagy ha a korábban delegált erőforrásokat eltávolítja.

A kezelő bérlőben az [Azure-tevékenységnapló](../../azure-monitor/essentials/platform-logs-overview.md) a bérlő szintjén követi nyomon a delegálást. Ez a naplózott tevékenység tartalmazza az ügyfélbérlők által hozzáadott vagy eltávolított delegálásokat.

Ez a témakör ismerteti a bérlőhöz (az összes ügyfélre vonatkozó) delegálás figyelése érdekében szükséges engedélyeket. Emellett tartalmaz egy parancsfájlmintát is, amely az adatok lekérdezésének és jelentésének egy módszerét mutatja be.

> [!IMPORTANT]
> Ezeket a lépéseket a kezelő bérlőben kell végrehajtani, nem pedig az ügyfélbérlőkben.
>
> Bár ebben a témakörben szolgáltatókra [](../concepts/enterprise.md) és ügyfelekre hivatkozunk, a több bérlőt kezelő vállalatok is használhatják ugyanazt a folyamatot.

## <a name="enable-access-to-tenant-level-data"></a>Bérlőszintű adatokhoz való hozzáférés engedélyezése

A bérlői szintű tevékenységnapló adatainak eléréséhez a fiókhoz hozzá kell rendelni a Figyelési olvasó [Azure](../../role-based-access-control/built-in-roles.md#monitoring-reader) beépített szerepkört a gyökérhatókörben (/). Ezt a hozzárendelést olyan felhasználónak kell elvégeznie, aki további emelt szintű hozzáféréssel rendelkező globális rendszergazdai szerepkörsel rendelkezik.

### <a name="elevate-access-for-a-global-administrator-account"></a>Globális rendszergazdai fiók jogosultságszint-ének megemelkedő jogosultságszintje

Ha szerepkört szeretne hozzárendelni gyökérhatókörben (/), emelt szintű hozzáféréssel rendelkező globális rendszergazdai szerepkörrel kell rendelkezik. Ezt az emelt szintű hozzáférést csak akkor kell hozzáadni, ha a szerepkör-hozzárendelést el kell távolítani, amikor végzett.

A jogosultságszint-emelés hozzáadására és eltávolítására vonatkozó részletes utasításokért lásd: Jogosultságszint emelése az összes Azure-előfizetés és [felügyeleti csoport kezeléséhez.](../../role-based-access-control/elevate-access-global-admin.md)

Miután megemeli a hozzáférési jogosultságszintet, a fiókja felhasználói hozzáférés-rendszergazdai szerepkörrel fog rendelkezik majd az Azure-ban gyökérhatókörben. Ez a szerepkör-hozzárendelés lehetővé teszi az összes erőforrás megtekintését és hozzáférés hozzárendelését a címtár bármely előfizetésében vagy felügyeleti csoportjában, valamint a szerepkör-hozzárendelések gyökérhatókörben való kiosztását.

### <a name="assign-the-monitoring-reader-role-at-root-scope"></a>A Figyelési olvasó szerepkör hozzárendelése gyökérhatókörben

Miután megemelte a hozzáférési jogosultságszintet, hozzárendelheti a megfelelő engedélyeket egy fiókhoz, hogy az lekérdezni tudja a bérlői szintű tevékenységnapló adatait. Ennek a fióknak szüksége lesz a figyelési olvasó [Azure](../../role-based-access-control/built-in-roles.md#monitoring-reader) beépített szerepkörére a felügyeleti bérlő gyökérhatókörében.

> [!IMPORTANT]
> A szerepkör-hozzárendelés gyökérhatókörben való megadása azt jelenti, hogy ugyanazok az engedélyek érvényesek a bérlő minden erőforrásán. Mivel ez a hozzáférés széles körű szintje, ezt a szerepkört hozzárendelheti egy egyszerű szolgáltatásfiókhoz, és ezzel a fiókkal [lekérdezheti az adatokat.](#use-a-service-principal-account-to-query-the-activity-log) A Figyelési olvasó szerepkört gyökérhatókörben is hozzárendelheti az egyes felhasználókhoz vagy felhasználói csoportokhoz, így azok közvetlenül a következő alkalmazásban [Azure Portal.](#view-delegation-changes-in-the-azure-portal) Ha így van, vegye figyelembe, hogy ez egy széles körű hozzáférési szint, amelyet a lehető legkisebb számú felhasználóra kell korlátozni.

A gyökérhatókör-hozzárendeléshez használja az alábbi módszerek egyikét.

#### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

New-AzRoleAssignment -SignInName <yourLoginName> -Scope "/" -RoleDefinitionName "Monitoring Reader"  -ObjectId <objectId> 
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az role assignment create --assignee 00000000-0000-0000-0000-000000000000 --role "Monitoring Reader" --scope "/"
```

### <a name="remove-elevated-access-for-the-global-administrator-account"></a>A globális rendszergazdai fiók emelt szintű hozzáférésének eltávolítása

Miután hozzárendelte a Figyelési olvasó szerepkört a kívánt fiók [](../../role-based-access-control/elevate-access-global-admin.md#remove-elevated-access) gyökérhatókörében, távolítsa el a globális rendszergazdai fiók emelt szintű hozzáférését, mivel erre a hozzáférési szintre már nincs szükség.

## <a name="view-delegation-changes-in-the-azure-portal"></a>Delegálás módosításainak megtekintése a Azure Portal

Azok a felhasználók, akik gyökérhatókörben figyelési olvasó szerepkört rendeltek hozzá, közvetlenül a felügyeleti Azure Portal.

1. Lépjen a **Saját ügyfelek oldalra,** majd a bal oldali navigációs menüben válassza a Tevékenységnapló lehetőséget. 
1. Győződjön meg arról, **hogy** a képernyő tetején található szűrőben a Címtártevékenység van kiválasztva.

Megjelenik a delegálás módosításainak listája. Kiválaszthatja az Oszlopok **szerkesztése** lehetőséget az Állapot, Eseménykategória, **Idő,** Időbélyeg, **Előfizetés,** Az erőforráscsoport, az Erőforrástípus és az  **Erőforrás** értékek elrejtéséhez.    

:::image type="content" source="../media/delegation-activity-portal.jpg" alt-text="Képernyőkép a delegálás változásairól a Azure Portal.":::

## <a name="use-a-service-principal-account-to-query-the-activity-log"></a>Szolgáltatásnév-fiók használata a tevékenységnapló lekérdezéséhez

Mivel a figyelési olvasó szerepkör gyökér szintű hozzáférése ilyen széles körű, előfordulhat, hogy egy egyszerű szolgáltatásfiókhoz szeretné hozzárendelni a szerepkört, és ezzel a fiókkal szeretne adatokat lekérdezni az alábbi szkripttel.

> [!IMPORTANT]
> Jelenleg a nagy mennyiségű delegálást végzett bérlők hibát okozhatnak az adatok lekérdezése során.

Ha szolgáltatásnév-fiókot használ a tevékenységnapló lekérdezéséhez, a következő ajánlott eljárásokat javasoljuk:

- [Hozzon létre egy](../../active-directory/develop/howto-create-service-principal-portal.md) új egyszerű szolgáltatásfiókot, amely csak ehhez a függvényhez lesz használva ahelyett, hogy ezt a szerepkört hozzárendeli egy más automatizáláshoz használt meglévő szolgáltatásnévhez.
- Győződjön meg arról, hogy ez a szolgáltatásnév nem rendelkezik hozzáféréssel egyik delegált ügyfélerőforráshoz sem.
- [Tanúsítvány használatával hitelesítse és](../../active-directory/develop/howto-create-service-principal-portal.md#authentication-two-options) [tárolja](../../key-vault/general/security-features.md)biztonságosan a Azure Key Vault.
- Korlátozza a szolgáltatásnév nevében való felhasználói jogosultságot.

Miután létrehozott egy új egyszerű szolgáltatásfiókot, amely figyelési olvasói hozzáféréssel rendelkezik a felügyeleti bérlő gyökérhatóköréhez, használhatja azt a bérlőben végzett delegálási tevékenység lekérdezésére és jelentésére.

[Ez Azure PowerShell](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/tools/monitor-delegation-changes) szkript az elmúlt 1 nap tevékenységének és jelentésének lekérdezésére használható a hozzáadott vagy eltávolított delegálásokkal (vagy sikertelen kísérletekkel). Lekérdezi [a bérlői tevékenységnapló adatait,](/rest/api/monitor/TenantActivityLogs/List) majd a következő értékeket készíti el a hozzáadott vagy eltávolított delegálások jelentéséhez:

- **DelegatedResourceId:** A delegált előfizetés vagy erőforráscsoport azonosítója
- **CustomerTenantId**: Az ügyfél bérlőazonosítója
- **CustomerSubscriptionId:** A delegált vagy a delegált erőforráscsoportot tartalmazó előfizetés-azonosító
- **CustomerDelegationStatus:** A delegált erőforrás állapotváltozása (sikeres vagy sikertelen)
- **EventTimeStamp:** A delegálás változásának naplózásának dátuma és időpontja

Az adatok lekérdezésekor tartsa szem előtt a következőt:

- Ha egyetlen üzemelő példányban több erőforráscsoport van delegálva, a rendszer minden erőforráscsoporthoz külön bejegyzést ad vissza.
- Az előző delegáláson (például az engedélystruktúra frissítésével) végrehajtott módosítások hozzáadott delegálásként lesznek naplózva.
- Ahogy fent említettük, a bérlői szintű adatok eléréséhez a fióknak a figyelési olvasó Azure beépített szerepkörével kell lennie a gyökérhatókörben (/).
- Ezeket az adatokat a saját munkafolyamatában és jelentéskészítésében használhatja. A [HTTP Data Collector API (nyilvános](../../azure-monitor/logs/data-collector-api.md) előzetes verzió) használatával például naplózhatja az Azure Monitor-adatokat [](../../azure-monitor/alerts/action-groups.md) egy REST API-ügyfélről, majd műveletcsoportok használatával értesítéseket vagy riasztásokat hozhat létre.

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Azure Lighthouse: Query Tenant Activity Log for registered/unregistered delegations for the last 1 day

$GetDate = (Get-Date).AddDays((-1))

$dateFormatForQuery = $GetDate.ToUniversalTime().ToString("yyyy-MM-ddTHH:mm:ssZ")

# Getting Azure context for the API call
$currentContext = Get-AzContext

# Fetching new token
$azureRmProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
$profileClient = [Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient]::new($azureRmProfile)
$token = $profileClient.AcquireAccessToken($currentContext.Tenant.Id)

$listOperations = @{
    Uri     = "https://management.azure.com/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&`$filter=eventTimestamp ge '$($dateFormatForQuery)'"
    Headers = @{
        Authorization  = "Bearer $($token.AccessToken)"
        'Content-Type' = 'application/json'
    }
    Method  = 'GET'
}
$list = Invoke-RestMethod @listOperations

# First link can be empty - and point to a next link (or potentially multiple pages)
# While you get more data - continue fetching and add result
while($list.nextLink){
    $list2 = Invoke-RestMethod $list.nextLink -Headers $listOperations.Headers -Method Get
    $data+=$list2.value;
    $list.nextLink = $list2.nextlink;
}

$showOperations = $data;

if ($showOperations.operationName.value -eq "Microsoft.Resources/tenants/register/action") {
    $registerOutputs = $showOperations | Where-Object -FilterScript { $_.eventName.value -eq "EndRequest" -and $_.resourceType.value -and $_.operationName.value -eq "Microsoft.Resources/tenants/register/action" }
    foreach ($registerOutput in $registerOutputs) {
        $eventDescription = $registerOutput.description | ConvertFrom-Json;
    $registerOutputdata = [pscustomobject]@{
        Event                    = "An Azure customer has registered delegated resources to your Azure tenant";
        DelegatedResourceId      = $eventDescription.delegationResourceId; 
        CustomerTenantId         = $eventDescription.subscriptionTenantId;
        CustomerSubscriptionId   = $eventDescription.subscriptionId;
        CustomerDelegationStatus = $registerOutput.status.value;
        EventTimeStamp           = $registerOutput.eventTimestamp;
        }
        $registerOutputdata | Format-List
    }
}
if ($showOperations.operationName.value -eq "Microsoft.Resources/tenants/unregister/action") {
    $unregisterOutputs = $showOperations | Where-Object -FilterScript { $_.eventName.value -eq "EndRequest" -and $_.resourceType.value -and $_.operationName.value -eq "Microsoft.Resources/tenants/unregister/action" }
    foreach ($unregisterOutput in $unregisterOutputs) {
        $eventDescription = $registerOutput.description | ConvertFrom-Json;
    $unregisterOutputdata = [pscustomobject]@{
        Event                    = "An Azure customer has unregistered delegated resources from your Azure tenant";
        DelegatedResourceId      = $eventDescription.delegationResourceId;
        CustomerTenantId         = $eventDescription.subscriptionTenantId;
        CustomerSubscriptionId   = $eventDescription.subscriptionId;
        CustomerDelegationStatus = $unregisterOutput.status.value;
        EventTimeStamp           = $unregisterOutput.eventTimestamp;
        }
        $unregisterOutputdata | Format-List
    }
}
else {
    Write-Output "No new delegation events for tenant: $($currentContext.Tenant.TenantId)"
}
```

## <a name="next-steps"></a>Következő lépések

- Ismerje meg, hogyan lehet ügyfeleket a [Azure Lighthouse.](../concepts/azure-delegated-resource-management.md)
- Tudnivalók a [Azure Monitor](../../azure-monitor/index.yml) és az [Azure-tevékenységnaplóról.](../../azure-monitor/essentials/platform-logs-overview.md)
- Tekintse át [a Tevékenységnaplók tartomány](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/workbook-activitylogs-by-domain) szerint mintamunkafüzetet, amelyből megtudhatja, hogyan jeleníthetők meg az Azure-tevékenységnaplók több előfizetésben, és hogyan szűrheti őket tartománynév alapján.
