---
title: Delegálási változások figyelése a kezelési bérlőben
description: Megtudhatja, hogyan figyelheti a delegálási tevékenységeket az ügyfelek bérlői számára a felügyeleti bérlőbe.
ms.date: 02/18/2021
ms.topic: how-to
ms.openlocfilehash: 8bd9e89039c114f3d1088df44198fe00c69bbf82
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103199061"
---
# <a name="monitor-delegation-changes-in-your-managing-tenant"></a>Delegálási változások figyelése a kezelési bérlőben

Szolgáltatóként érdemes lehet tájékoztatni, ha az ügyfél-előfizetések vagy erőforráscsoportok delegálása a bérlőn keresztül történik az [Azure világítótoronyban](../overview.md), vagy ha a korábban delegált erőforrások törlődnek.

A bérlő kezelése során az [Azure-tevékenység naplója](../../azure-monitor/essentials/platform-logs-overview.md) a bérlői szinten nyomon követi a delegálási tevékenységet. Ez a naplózott tevékenység magában foglalja az ügyfelek bérlői által hozzáadott vagy eltávolított delegálásokat.

Ez a témakör ismerteti azokat az engedélyeket, amelyek szükségesek a delegálási tevékenységek figyeléséhez a bérlőre (az összes ügyfél között). Egy olyan minta parancsfájlt is tartalmaz, amely az adott adatlekérdezésre és jelentéskészítésre szolgáló egyik metódust jeleníti meg.

> [!IMPORTANT]
> Az összes fenti lépést a kezelő bérlőn kell végrehajtani, nem pedig az ügyfél bérlői.
>
> Bár a jelen témakörben a szolgáltatók és az ügyfelekre is hivatkozunk, a [több bérlőt kezelő vállalatok](../concepts/enterprise.md) ugyanazt a folyamatot használhatják.

## <a name="enable-access-to-tenant-level-data"></a>Bérlői szintű adatelérés engedélyezése

A bérlői szintű tevékenység naplófájljainak eléréséhez a fióknak hozzá kell rendelnie a [monitoring Reader](../../role-based-access-control/built-in-roles.md#monitoring-reader) Azure beépített szerepkörét a root scope (/) elemnél. Ezt a hozzárendelést egy globális rendszergazdai szerepkörrel rendelkező felhasználónak kell végrehajtania, amely további emelt szintű hozzáféréssel rendelkezik.

### <a name="elevate-access-for-a-global-administrator-account"></a>Globális rendszergazdai fiók hozzáférésének megemelése

Ahhoz, hogy szerepkört rendeljen a root (/) hatókörhöz, a globális rendszergazdai szerepkört emelt szintű hozzáféréssel kell rendelkeznie. Ezt a emelt szintű hozzáférést csak akkor kell hozzáadni, ha a szerepkör-hozzárendelést el kell végeznie, majd el kell távolítani, ha elkészült.

A Jogosultságszint-emelés hozzáadásával és eltávolításával kapcsolatos részletes utasításokért lásd: [jogosultságszint-emelési hozzáférés az összes Azure-előfizetés és-felügyeleti csoport kezeléséhez](../../role-based-access-control/elevate-access-global-admin.md).

Miután megemelte a hozzáférést, a fiókja felhasználói hozzáférés rendszergazdai szerepkörrel fog rendelkezni az Azure-ban a gyökérszintű hatókörben. Ez a szerepkör-hozzárendelés lehetővé teszi az összes erőforrás megtekintését és a hozzáférés hozzárendelését a címtárban található bármely előfizetésben vagy felügyeleti csoportban, valamint a szerepkör-hozzárendeléseket a gyökérszintű hatókörben.

### <a name="assign-the-monitoring-reader-role-at-root-scope"></a>A figyelési olvasó szerepkör kiosztása a gyökérszintű hatókörben

Miután megemelte a hozzáférést, hozzárendelheti a megfelelő engedélyeket egy fiókhoz, hogy lekérdezze a bérlői szintű tevékenység naplójának adatait. Ennek a fióknak rendelkeznie kell a [figyelési olvasó](../../role-based-access-control/built-in-roles.md#monitoring-reader) Azure beépített szerepkörrel, amelyet a felügyeleti bérlő legfelső szintű hatókörében lehet hozzárendelni.

> [!IMPORTANT]
> A szerepkör-hozzárendelés gyökérszintű hatókörben való megadása azt jelenti, hogy ugyanazok az engedélyek lesznek érvényesek a bérlő minden erőforrására. Mivel ez egy széles körű hozzáférés, érdemes lehet [ezt a szerepkört hozzárendelni egy egyszerű szolgáltatásnév-fiókhoz, és a fiók használatával lekérdezni](#use-a-service-principal-account-to-query-the-activity-log)azokat. A figyelési olvasó szerepkört a root hatókörben is hozzárendelheti az egyes felhasználókhoz vagy a felhasználói csoportokhoz, így [közvetlenül a Azure Portal tekinthetik meg a delegálási adatokat](#view-delegation-changes-in-the-azure-portal). Ha így tesz, vegye figyelembe, hogy ez egy széles körű hozzáférés, amelyet a lehető legkevesebb felhasználó számára kell korlátozni.

A gyökér hatókör-hozzárendelésének végrehajtásához használja a következő módszerek egyikét.

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

Miután hozzárendelte a figyelési olvasó szerepkört a root hatókörben a kívánt fiókhoz, mindenképpen [távolítsa el a globális rendszergazdai fiók emelt szintű hozzáférését](../../role-based-access-control/elevate-access-global-admin.md#remove-elevated-access) , mert ez a hozzáférési szint többé nem lesz szükséges.

## <a name="view-delegation-changes-in-the-azure-portal"></a>Delegálási változások megtekintése a Azure Portalban

Azok a felhasználók, akik a felügyeleti olvasó szerepkörhöz lettek rendelve a gyökérszintű hatókörben, közvetlenül a Azure Portalban tekinthetik meg a delegálási módosításokat.

1. Lépjen a **saját ügyfelek** oldalra, majd válassza a **műveletnapló** lehetőséget a bal oldali navigációs menüből.
1. Győződjön meg arról, hogy a **címtár tevékenység** van kiválasztva a képernyő felső részén található szűrőben.

Ekkor megjelenik a delegálási változások listája. Az **Oszlopok szerkesztése** lehetőség kiválasztásával megjelenítheti vagy elrejtheti az **állapotot**, az **események kategóriáját**, az **időt**, az **időbélyeget**, az **előfizetést**, az **esemény által kezdeményezett, az** **erőforráscsoport**, az **erőforrástípus** és az **erőforrás** értékét.

:::image type="content" source="../media/delegation-activity-portal.jpg" alt-text="Képernyőkép a Azure Portal delegálási változásairól.":::

## <a name="use-a-service-principal-account-to-query-the-activity-log"></a>Egyszerű szolgáltatás fiók használata a műveletnapló lekérdezéséhez

Mivel a figyelési olvasó szerepkör a gyökér hatókörében egy ilyen széles körű hozzáférés, érdemes lehet a szerepkört hozzárendelni egy egyszerű szolgáltatásnév-fiókhoz, és az alábbi parancsfájl használatával az adatlekérdezéshez használni a fiókot.

> [!IMPORTANT]
> A nagy mennyiségű delegálási tevékenységgel rendelkező bérlők jelenleg hibákba ütköznek az adatlekérdezés során.

Ha egyszerű szolgáltatásnevet használ a műveletnapló lekérdezéséhez, javasoljuk a következő ajánlott eljárásokat:

- [Hozzon létre egy új egyszerű szolgáltatásnevet](../../active-directory/develop/howto-create-service-principal-portal.md) , amelyet csak ehhez a függvényhez kell használni, ahelyett, hogy ezt a szerepkört egy másik automatizáláshoz használt meglévő egyszerű szolgáltatáshoz rendeli.
- Győződjön meg arról, hogy ez az egyszerű szolgáltatás nem rendelkezik hozzáféréssel a delegált ügyfelek erőforrásaihoz.
- [Tanúsítvány használata](../../active-directory/develop/howto-create-service-principal-portal.md#authentication-two-options) az Azure Key Vault biztonságos hitelesítéséhez és [tárolásához](../../key-vault/general/security-overview.md).
- Korlátozza azokat a felhasználókat, akik hozzáféréssel rendelkeznek az egyszerű szolgáltatásnév nevében.

Miután létrehozott egy új egyszerű szolgáltatásnevet, amely figyeli az olvasó hozzáférését a felügyeleti bérlő legfelső szintű hatóköréhez, használhatja a delegálási tevékenység lekérdezésére és jelentésére a bérlőben.

[Ezzel a Azure PowerShell parancsfájllal](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/tools/monitor-delegation-changes) lekérdezheti a tevékenységek és jelentések elmúlt 1 napját a hozzáadott vagy eltávolított delegálásokra vonatkozóan (vagy sikertelen próbálkozások esetén). Lekérdezi a [bérlői tevékenység naplójának](/rest/api/monitor/TenantActivityLogs/List) adatait, majd létrehozza a következő értékeket a hozzáadott vagy eltávolított delegálások jelentéséhez:

- **DelegatedResourceId**: a delegált előfizetés vagy erőforráscsoport azonosítója
- **CustomerTenantId**: az ügyfél BÉRLŐi azonosítója
- **CustomerSubscriptionId**: a delegált vagy a delegált erőforráscsoportot tartalmazó előfizetés azonosítója
- **CustomerDelegationStatus**: a delegált erőforrás állapotának változása (sikeres vagy sikertelen)
- **EventTimeStamp**: a delegálás módosításának dátuma és időpontja

Az adatlekérdezés során vegye figyelembe a következőket:

- Ha több erőforráscsoport van delegálva egyetlen központi telepítésben, minden erőforráscsoport külön bejegyzést ad vissza.
- Az előző delegáláson végrehajtott módosítások (például az engedélyezési struktúra frissítése) bekerülnek egy hozzáadott delegálásba.
- A fentiekben leírtaknak megfelelően a fióknak rendelkeznie kell az Azure beépített felügyeleti szerepkörrel a root scope (/) használatával ahhoz, hogy hozzáférjen ehhez a bérlői szintű adathoz.
- Ezeket az adatait saját munkafolyamataiban és jelentéseiben is használhatja. Használhatja például a http-adatgyűjtő API-t [(nyilvános előzetes verzió)](../../azure-monitor/logs/data-collector-api.md) az adatok REST API-ügyfélből Azure monitor való naplózásához, majd a [műveleti csoportok](../../azure-monitor/alerts/action-groups.md) használatával értesítéseket vagy riasztásokat hozhat létre.

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

- Ismerje meg, hogyan hozhatja be az ügyfeleket az [Azure lighthouseba](../concepts/azure-delegated-resource-management.md).
- Ismerje meg a [Azure monitor](../../azure-monitor/index.yml) és az [Azure-tevékenység naplóját](../../azure-monitor/essentials/platform-logs-overview.md).
- Tekintse át a [tevékenységek naplói a tartományi](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/workbook-activitylogs-by-domain) minta munkafüzetben című témakört, amelyből megtudhatja, hogyan jelenítheti meg az Azure-tevékenységek naplóit az előfizetések között a tartománynév alapján történő
