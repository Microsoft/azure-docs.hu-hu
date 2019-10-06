---
title: Szabályzatok megfelelőségi állapotának beolvasása
description: Az Azure házirend értékelések és hatások határozza meg a megfelelőség. Ismerje meg, hogyan kérheti le a megfelelőségi adatait.
author: DCtheGeek
ms.author: dacoulte
ms.date: 02/01/2019
ms.topic: conceptual
ms.service: azure-policy
ms.openlocfilehash: ff50619d7b3d5bc803e8ee8d9e4cbf4389a4191f
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/06/2019
ms.locfileid: "71978087"
---
# <a name="get-compliance-data-of-azure-resources"></a>Azure-erőforrások megfelelőségi információk beolvasása

Egyik legnagyobb előnye az Azure Policy egy insight- és vezérlőket biztosít egy adott előfizetés erőforrások vagy [felügyeleti csoport](../../management-groups/overview.md) előfizetések. Ez a vezérlő megakadályozza az erőforrások a megfelelő helyen létrehozott kényszerítése általános és következetes címke használatot, például számos különböző módon lehet érvényesíteni, vagy naplózási meglévő erőforrások, a szükséges konfigurációk és beállítások. A Azure Policy az összes esetben létrehozta az adatait, így megismerheti a környezet megfelelőségi állapotát.

Többféleképpen is lehet a megfelelőségi adatokat a szabályzat és a kezdeményezési hozzárendelések által generált eléréséhez:

- Használatával a [Azure Portalon](#portal)
- Keresztül [parancssori](#command-line) scripting

Mielőtt megnézzük a jelentés a megfelelőségi módszereket, tekintsük át megfelelőségi adatok frissítésekor és a gyakoriság és a egy kiértékelési ciklusa kiváltó események.

> [!WARNING]
> Ha a megfelelőségi állapotot **nem regisztráltként**jelenti, ellenőrizze, hogy a **Microsoft. PolicyInsights** erőforrás-szolgáltató regisztrálva van-e, és hogy a felhasználó rendelkezik-e a megfelelő szerepköralapú hozzáférés-vezérlési (RBAC) engedélyekkel a RBAC című témakörben leírtak szerint. [ Azure Policy](../overview.md#rbac-permissions-in-azure-policy).

## <a name="evaluation-triggers"></a>Értékelés eseményindítók

A befejezett kiértékelési ciklusa eredményeit érhetők el a `Microsoft.PolicyInsights` erőforrás-szolgáltató `PolicyStates` és `PolicyEvents` műveletek. Az Azure Policy-információk REST API működésével kapcsolatos további információkért tekintse meg az [Azure Policy](/rest/api/policy-insights/)elemzése című témakört.

Hozzárendelt házirendeket és kezdeményezések értékelések történt különféle események eredményeként fordulhat elő:

- Egy szabályzatot vagy kezdeményezést újonnan hozzárendel egy hatókörhöz. A hozzárendelés a alkalmazni lehessen a megadott hatókörhöz körülbelül 30 percet vesz igénybe. Az alkalmazása után az újonnan hozzárendelt szabályzatot vagy kezdeményezést vagy a szabályzat által használt hatása attól függően, hogy hatókörön belüli erőforrások megkezdi a kiértékelési ciklusa, vagy kezdeményezés, erőforrások vannak megjelölve megfelelő vagy nem megfelelő. Egy nagy szabályzatot vagy kezdeményezést erőforrások nagy hatókörének értékelni időt vehet igénybe. Mint ilyen nem érkezik nincs előre definiált elvárás, ha a kiértékelési ciklusa fog befejeződni. Ha kész, a portálon és az SDK-k frissített megfelelőségi eredmények érhetők el.

- Egy szabályzatot vagy kezdeményezést már hozzá van rendelve egy hatókör frissül. A kiértékelési ciklusa és az időzítési ebben a forgatókönyvben ugyanúgy történik, mint egy új hozzárendelést egy hatókörhöz.

- Egy erőforrás üzembe van helyezve, az erőforrás-kezelő, REST, Azure CLI-vel vagy az Azure PowerShell-hozzárendelés egy hatókörhöz. Ebben a forgatókönyvben a hatás esemény (hozzáfűzés, naplózása, megtagadása, üzembe helyezése) és a megfelelő állapotát az egyes erőforrások számára elérhetővé válik, a portálon és az SDK-k körülbelül 15 perc múlva. Ez az esemény nem okozhat más erőforrások értékelése.

- Standard szintű megfelelőségi kiértékelési ciklusa. Miután 24 óránként automatikusan újraértékeli a hozzárendeléseket. Egy nagy szabályzatot vagy kezdeményezést, számos erőforrás időt vehet igénybe, így nem előre definiált elvárás, ha az értékelési ciklusát fog befejeződni. Ha kész, a portálon és az SDK-k frissített megfelelőségi eredmények érhetők el.

- A [vendég-konfiguráció](../concepts/guest-configuration.md) erőforrás-szolgáltatóját egy felügyelt erőforrás megfelelőségi részleteivel frissíti.

- Igény szerinti vizsgálat

### <a name="on-demand-evaluation-scan"></a>Igény szerinti-értékelési vizsgálat

Egy értékelési vizsgálatának egy előfizetést vagy egy erőforráscsoport elindítható a REST API-hívással. Ez a vizsgálat a egy aszinkron folyamat. A vizsgálat elindítása a REST-végpont nem mint ilyen, várjon, amíg a vizsgálat teljes válaszolni. Ehelyett a kért kiértékelése állapotának lekérdezése egy URI-t biztosít.

Minden REST API URI tartalmaz olyan változókat, amelyeket le kell cserélnie saját értékekre:

- `{YourRG}` – Cserélje le az erőforráscsoport nevét
- `{subscriptionId}` – Cserélje le az előfizetése azonosítójára

A vizsgálat erőforrások értékelése támogatja az egy előfizetésben, vagy egy erőforráscsoportban. A REST API-val hatókörére vizsgálat indítása **POST** parancsának használatával a következő URI struktúrák:

- Előfizetés

  ```http
  POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/triggerEvaluation?api-version=2018-07-01-preview
  ```

- Erőforráscsoport

  ```http
  POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{YourRG}/providers/Microsoft.PolicyInsights/policyStates/latest/triggerEvaluation?api-version=2018-07-01-preview
  ```

A hívás eredménye egy **202-es elfogadva** állapotát. Tartalmazza a válasz fejléce van egy **hely** tulajdonság a következő formátumban:

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/asyncOperationResults/{ResourceContainerGUID}?api-version=2018-07-01-preview
```

`{ResourceContainerGUID}` a kért hatókörrel statikusan jön. A hatókör már fut egy igény szerinti vizsgálat, ha új vizsgálatot nincs elindítva. Ehelyett az új kérés van megadva azonos `{ResourceContainerGUID}` **hely** állapot URI. REST API-t **első** parancsot a **helye** URI-t adja vissza egy **202-es elfogadva** közben az értékelés folyamatban. Az értékelési vizsgálat befejezése után adja vissza egy **200 OK** állapotát. A szervezet egy befejezett ellenőrzési egy JSON-választ a állapota:

```json
{
    "status": "Succeeded"
}
```

## <a name="how-compliance-works"></a>Megfelelőségi működése

A hozzárendelés, van egy erőforrás **nem megfelelő** Ha nem követi a szabályzatot vagy kezdeményezést szabályokat.
Az alábbi táblázat a különböző házirend hatások működnek a feltételek kiértékelésével a az eredményül kapott megfelelőségi állapot:

| Erőforrás állapota | Következmény | Szabályzat-kiértékelés | Megfelelőségi állapot |
| --- | --- | --- | --- |
| Létezik | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | True (Igaz) | Nem megfelelő |
| Létezik | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | False (Hamis) | Megfelelő |
| Új | Naplózás, AuditIfNotExist\* | True (Igaz) | Nem megfelelő |
| Új | Naplózás, AuditIfNotExist\* | False (Hamis) | Megfelelő |

\* Az Append, a DeployIfNotExist és az AuditIfNotExist hatás esetében az IF utasításnak TRUE értéket kell visszaadnia.
Emellett a létezési feltételnek FALSE értéket kell visszaadnia ahhoz, hogy a szabályzat nem megfelelőnek minősüljön. TRUE érték esetén az IF feltétel kiváltja a vonatkozó erőforrások létezési feltételének kiértékelését.

Vegyük például, hogy rendelkezik egy erőforráscsoport – ContsoRG, néhány azokat a tárfiókokat (vörös színnel) jelennek meg a nyilvános hálózatok.

![A nyilvános hálózatok vannak kitéve Storage-fiókok](../media/getting-compliance-data/resource-group01.png)

Ebben a példában kell legyen óvatos az olyan biztonsági kockázatokat. Most, hogy a létrehozott szabályzat-hozzárendelés, a rendszer kiértékeli az ContosoRG erőforráscsoportban lévő összes storage-fiók. Azt naplózza, hogy a három nem megfelelő tárfiókok, az állapotot, és ezért módosítása **nem megfelelő.**

![Nem megfelelő tárfiókokat naplózza](../media/getting-compliance-data/resource-group03.png)

Mellett **megfelelő** és **nem megfelelő**, szabályzatok és az erőforrások három egyéb állapota van:

- **Ütköző**: Az ütköző szabályok két vagy több szabályzatot tartalmaznak. Ha például két szabályzat hozzáfűzése a különböző értékkel azonos címkével.
- **Nem indult el**: A kiértékelési ciklus nem indult el a házirendhez vagy az erőforráshoz.
- **Nincs regisztrálva**: A Azure Policy erőforrás-szolgáltató nincs regisztrálva, vagy a bejelentkezett fióknak nincs engedélye a megfelelőségi információk olvasásához.

Azure Policy a definíció **típus** és **név** mezőjét használja annak megállapítására, hogy az erőforrás egyezik-e. Az erőforrás megegyezik, azt alkalmazható számít, és állapota vagy **megfelelő** vagy **nem megfelelő**. Ha bármelyik **típus** vagy **neve** nem az egyetlen tulajdonság a definícióban összes erőforrás tekinti a alkalmazni, és értékeli ki.

Határozza meg a megfelelőség százalékos értékkel való osztásának **megfelelő** erőforrásokat _erőforrások teljes_.
_Erőforrások teljes_ összege típusúként van definiálva a **megfelelő**, **nem megfelelő**, és **ütköző** erőforrásokat. Az általános megfelelőségi szám, a különböző erőforrások, amelyek összege **megfelelő** elosztja az összes különböző erőforrás. Az alábbi képen vannak 20 különböző erőforrások, amelyek alkalmazhatók, és csak az egyiket **nem megfelelő**. A teljes erőforrás-megfelelőség 95 %-os (19 20).

![Példa a megfelelőségi oldal szabályzatoknak való megfelelőségére](../media/getting-compliance-data/simple-compliance.png)

## <a name="portal"></a>Portál

Az Azure Portalon bemutatja egy grafikus felületet jelenítenek meg, és a környezet megfelelőségi állapotát ismertetése. Az a **házirend** lapon a **áttekintése** beállítás részletes adatokat biztosít a megfelelőségi szabályzatok és a kezdeményezések elérhető hatóköreit. A megfelelőségi állapotok és száma felhasználónként hozzárendelés együtt diagramját, az elmúlt hét napban megfelelőségi tartalmazza. A **megfelelőségi** lap tartalmaz szinte ugyanezeket az adatokat (kivéve a diagramot), de további szűrési és rendezési lehetőségeket biztosít.

![Azure Policy megfelelőségi lap – példa](../media/getting-compliance-data/compliance-page.png)

Egy szabályzatot vagy kezdeményezést több hatókörhöz is rendelhető, mert a tábla minden hozzárendelés és-definíciót, amely hozzá volt rendelve típusát a hatókör magában foglalja. A nem megfelelő erőforrások és a nem megfelelő szabályzatok egyes hozzárendelések száma is biztosítja. Kattintson egy szabályzatot vagy kezdeményezést a tábla a biztosít, hogy adott hozzárendelés megfelelőségi alaposabban.

![Azure Policy megfelelőségi részletek oldalának példája](../media/getting-compliance-data/compliance-details.png)

Az erőforrások listájában a **erőforrás megfelelőségi** lapon a meglévő erőforrások, az aktuális hozzárendelés értékelési állapotát jeleníti meg. A lapon alapértelmezés szerint a **nem megfelelő**, de szűrésével.
Események (hozzáfűzés, naplózása, megtagadása, üzembe helyezése) váltott erőforrás létrehozására vonatkozó kérelem alatt látható a **események** fülre.

![Azure Policy megfelelőségi események – példa](../media/getting-compliance-data/compliance-events.png)

Kattintson a jobb gombbal a sor az esemény gyűjtse össze a további részleteket a, és válassza ki, szeretné **Tevékenységnaplók megjelenítése**. A tevékenységnapló oldalon megnyílik, és a Keresés az események és az a hozzárendelés részleteit megjelenítő előre szűrt. A tevékenységnapló további kontextus és eseményekből információkat biztosít.

![Azure Policy megfelelőségi tevékenység naplójának példája](../media/getting-compliance-data/compliance-activitylog.png)

### <a name="understand-non-compliance"></a>A nem megfelelőség megismerése

<a name="change-history-preview"></a>

Ha egy erőforrás úgy van meghatározva, hogy **nem megfelelőnek**minősül, számos lehetséges oka lehet. Annak megállapításához, hogy az erőforrás **nem megfelelő-** e, vagy hogy megkeresse a felelős változást, tekintse [meg a nem megfelelőség megállapítása](./determine-non-compliance.md)című témakört.

## <a name="command-line"></a>Parancssor

A REST API-val lekérheti a portálon elérhető ugyanazokat az információkat (beleértve az [ARMClient](https://github.com/projectkudu/ARMClient)) vagy az Azure PowerShell-lel. A REST API részletes ismertetését lásd: [Azure Policy](/rest/api/policy-insights/) -információk áttekintése. A REST API-referencia lapok ", próbálja meg" egy zöld gombot rendelkezik minden művelet, amely lehetővé teszi, hogy próbálja ki a jobb oldalon, a böngészőben.

Az alábbi példák az Azure PowerShell használatához hozhatnak létre egy hitelesítési tokent ebben a példában kóddal. Ezután cserélje le a $restUri a példákban egy JSON-objektumot, majd a program értelmezni tudja beolvasni a karakterláncot.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

$azContext = Get-AzContext
$azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
$profileClient = New-Object -TypeName Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient -ArgumentList ($azProfile)
$token = $profileClient.AcquireAccessToken($azContext.Subscription.TenantId)
$authHeader = @{
    'Content-Type'='application/json'
    'Authorization'='Bearer ' + $token.AccessToken
}

# Define the REST API to communicate with
# Use double quotes for $restUri as some endpoints take strings passed in single quotes
$restUri = "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/summarize?api-version=2018-04-04"

# Invoke the REST API
$response = Invoke-RestMethod -Uri $restUri -Method POST -Headers $authHeader

# View the response object (as JSON)
$response
```

### <a name="summarize-results"></a>Összegzés eredményei

A REST API-val összegzési tároló, a definition vagy a hozzárendelés alapján végezheti el. Íme egy példa az előfizetés szintjén az Azure Policy Insight összefoglalása az [előfizetéshez](/rest/api/policy-insights/policystates/summarizeforsubscription):

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/summarize?api-version=2018-04-04
```

A kimenet az előfizetés foglalja össze. Az alábbi példa kimenetében az összesített megfelelőségi vannak **value.results.nonCompliantResources** és **value.results.nonCompliantPolicies**. A kérelem további adatait, például az egyes hozzárendelést a nem megfelelő számokat és a helydefiníció adatait minden hozzárendelés esetében végrehajtott biztosít. A hierarchia minden egyes csoportházirend-objektum biztosít egy **queryResultsUri** , amely további részleteket lekérni azon a szinten használható.

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#summary",
    "@odata.count": 1,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#summary/$entity",
        "results": {
            "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false",
            "nonCompliantResources": 15,
            "nonCompliantPolicies": 1
        },
        "policyAssignments": [{
            "policyAssignmentId": "/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77",
            "policySetDefinitionId": "",
            "results": {
                "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77'",
                "nonCompliantResources": 15,
                "nonCompliantPolicies": 1
            },
            "policyDefinitions": [{
                "policyDefinitionReferenceId": "",
                "policyDefinitionId": "/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "effect": "deny",
                "results": {
                    "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'",
                    "nonCompliantResources": 15
                }
            }]
        }]
    }]
}
```

### <a name="query-for-resources"></a>Erőforrások lekérdezése

A fentiek példában **value.policyAssignments.policyDefinitions.results.queryResultsUri** egy minta URI-t biztosít a számára egy adott szabályzatdefiníciót minden nem megfelelő erőforrások. Megnézzük a **$filter** , IsCompliant értéke (eq) FALSE, PolicyAssignmentId van megadva a szabályzat-definíció, majd a PolicyDefinitionId magát. Többek között a PolicyAssignmentId szűrő az az oka az oka, hogy a PolicyDefinitionId sikerült több szabályzat vagy más hatóköröknek a kezdeményezési hozzárendelések szerepel. A PolicyAssignmentId, mind a PolicyDefinitionId megadásával biztosak lehetünk abban a számíthatunk eredmények explicit. Korábban a PolicyStates használtuk **legújabb**, amely automatikusan beállítja egy **a** és **való** időkeretnél az elmúlt 24 órában.

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'
```

Az alábbi példaválasz kivonatosan egyetlen nem kompatibilis erőforrás van vágva. A válaszok részletes adatait az erőforrás, a szabályzatot vagy kezdeményezést és a hozzárendelés több adatra is rendelkezik. Figyelje meg, hogy is láthatja, milyen hozzárendelés paraméterek lettek átadva a szabályzat-definíció.

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
    "@odata.count": 15,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
        "timestamp": "2018-05-19T04:41:09Z",
        "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/rg-tags/providers/Microsoft.Compute/virtualMachines/linux",
        "policyAssignmentId": "/subscriptions/{subscriptionId}/resourceGroups/rg-tags/providers/Microsoft.Authorization/policyAssignments/37ce239ae4304622914f0c77",
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
        "effectiveParameters": "",
        "isCompliant": false,
        "subscriptionId": "{subscriptionId}",
        "resourceType": "/Microsoft.Compute/virtualMachines",
        "resourceLocation": "westus2",
        "resourceGroup": "RG-Tags",
        "resourceTags": "tbd",
        "policyAssignmentName": "37ce239ae4304622914f0c77",
        "policyAssignmentOwner": "tbd",
        "policyAssignmentParameters": "{\"tagName\":{\"value\":\"costCenter\"},\"tagValue\":{\"value\":\"Contoso-Test\"}}",
        "policyAssignmentScope": "/subscriptions/{subscriptionId}/resourceGroups/RG-Tags",
        "policyDefinitionName": "1e30110a-5ceb-460c-a204-c1c3969c6d62",
        "policyDefinitionAction": "deny",
        "policyDefinitionCategory": "tbd",
        "policySetDefinitionId": "",
        "policySetDefinitionName": "",
        "policySetDefinitionOwner": "",
        "policySetDefinitionCategory": "",
        "policySetDefinitionParameters": "",
        "managementGroupIds": "",
        "policyDefinitionReferenceId": ""
    }]
}
```

### <a name="view-events"></a>Események megtekintése

Amikor az erőforrás létrehozásakor vagy frissítésekor, egy házirend kiértékelésének eredménye jön létre. Eredmények az úgynevezett _házirend-események_. Legutóbbi házirend-események az előfizetéshez tartozó megtekintéséhez használja a következő URI azonosítót.

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/default/queryResults?api-version=2018-04-04
```

Az eredmények a következő példához hasonlók:

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/$metadata#default",
    "@odata.count": 1,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/$metadata#default/$entity",
        "NumAuditEvents": 16
    }]
}
```

A házirend-események lekérdezésével kapcsolatos további információkért tekintse meg a [Azure Policy Events](/rest/api/policy-insights/policyevents) Reference című cikket.

### <a name="azure-powershell"></a>Azure PowerShell

A Azure Policy Azure PowerShell modulja a PowerShell-galéria az az [. PolicyInsights](https://www.powershellgallery.com/packages/Az.PolicyInsights)néven érhető el.
A PowerShellGet használata esetén telepítheti a modul használatával `Install-Module -Name Az.PolicyInsights` (Ellenőrizze, hogy a legújabb [Azure PowerShell-lel](/powershell/azure/install-az-ps) telepítve van):

```azurepowershell-interactive
# Install from PowerShell Gallery via PowerShellGet
Install-Module -Name Az.PolicyInsights

# Import the downloaded module
Import-Module Az.PolicyInsights

# Login with Connect-AzAccount if not using Cloud Shell
Connect-AzAccount
```

A modul a következő parancsmagokkal rendelkezik:

- `Get-AzPolicyStateSummary`
- `Get-AzPolicyState`
- `Get-AzPolicyEvent`
- `Get-AzPolicyRemediation`
- `Remove-AzPolicyRemediation`
- `Start-AzPolicyRemediation`
- `Stop-AzPolicyRemediation`

Példa: Az állapot összegzésének beolvasása a legfelső szintű hozzárendelt szabályzathoz a legmagasabb számú nem megfelelő erőforrással.

```azurepowershell-interactive
PS> Get-AzPolicyStateSummary -Top 1

NonCompliantResources : 15
NonCompliantPolicies  : 1
PolicyAssignments     : {/subscriptions/{subscriptionId}/resourcegroups/RG-Tags/providers/micros
                        oft.authorization/policyassignments/37ce239ae4304622914f0c77}
```

Példa: Az állapot rekordjának beolvasása a legutóbb kiértékelt erőforráshoz (alapértelmezés szerint az időbélyegző csökkenő sorrendben történik).

```azurepowershell-interactive
PS> Get-AzPolicyState -Top 1

Timestamp                  : 5/22/2018 3:47:34 PM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/networkInterfaces/linux316
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
IsCompliant                : False
SubscriptionId             : {subscriptionId}
ResourceType               : /Microsoft.Network/networkInterfaces
ResourceLocation           : westus2
ResourceGroup              : RG-Tags
ResourceTags               : tbd
PolicyAssignmentName       : 37ce239ae4304622914f0c77
PolicyAssignmentOwner      : tbd
PolicyAssignmentParameters : {"tagName":{"value":"costCenter"},"tagValue":{"value":"Contoso-Test"}}
PolicyAssignmentScope      : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags
PolicyDefinitionName       : 1e30110a-5ceb-460c-a204-c1c3969c6d62
PolicyDefinitionAction     : deny
PolicyDefinitionCategory   : tbd
```

Példa: A nem megfelelő virtuális hálózati erőforrások adatainak beolvasása.

```azurepowershell-interactive
PS> Get-AzPolicyState -Filter "ResourceType eq '/Microsoft.Network/virtualNetworks'"

Timestamp                  : 5/22/2018 4:02:20 PM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/virtualNetworks/RG-Tags-vnet
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
IsCompliant                : False
SubscriptionId             : {subscriptionId}
ResourceType               : /Microsoft.Network/virtualNetworks
ResourceLocation           : westus2
ResourceGroup              : RG-Tags
ResourceTags               : tbd
PolicyAssignmentName       : 37ce239ae4304622914f0c77
PolicyAssignmentOwner      : tbd
PolicyAssignmentParameters : {"tagName":{"value":"costCenter"},"tagValue":{"value":"Contoso-Test"}}
PolicyAssignmentScope      : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags
PolicyDefinitionName       : 1e30110a-5ceb-460c-a204-c1c3969c6d62
PolicyDefinitionAction     : deny
PolicyDefinitionCategory   : tbd
```

Példa: Olyan nem megfelelő virtuális hálózati erőforrásokhoz kapcsolódó események beolvasása, amelyek egy adott dátum után következtek be.

```azurepowershell-interactive
PS> Get-AzPolicyEvent -Filter "ResourceType eq '/Microsoft.Network/virtualNetworks'" -From '2018-05-19'

Timestamp                  : 5/19/2018 5:18:53 AM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/virtualNetworks/RG-Tags-vnet
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
IsCompliant                : False
SubscriptionId             : {subscriptionId}
ResourceType               : /Microsoft.Network/virtualNetworks
ResourceLocation           : eastus
ResourceGroup              : RG-Tags
ResourceTags               : tbd
PolicyAssignmentName       : 37ce239ae4304622914f0c77
PolicyAssignmentOwner      : tbd
PolicyAssignmentParameters : {"tagName":{"value":"costCenter"},"tagValue":{"value":"Contoso-Test"}}
PolicyAssignmentScope      : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags
PolicyDefinitionName       : 1e30110a-5ceb-460c-a204-c1c3969c6d62
PolicyDefinitionAction     : deny
PolicyDefinitionCategory   : tbd
TenantId                   : {tenantId}
PrincipalOid               : {principalOid}
```

A **PrincipalOid** mező segítségével egy adott felhasználó lekérése az Azure PowerShell-parancsmaggal `Get-AzADUser`. Cserélje le **{principalOid}** a válasz az előző példából kap.

```azurepowershell-interactive
PS> (Get-AzADUser -ObjectId {principalOid}).DisplayName
Trent Baker
```

## <a name="azure-monitor-logs"></a>Azure Monitor-naplók

Ha [log Analytics munkaterülettel](../../../log-analytics/log-analytics-overview.md) rendelkezik, és az előfizetéshez kötött [Activity Log Analytics megoldásból](../../../azure-monitor/platform/activity-log-collect.md) `AzureActivity` van társítva, akkor a kiértékelési ciklusban az egyszerű Kusto lekérdezések és a `AzureActivity` tábla használatával is megtekintheti a nem megfelelőségi eredményeket. Azure Monitor naplók részleteivel a riasztások úgy konfigurálhatók, hogy megfigyeljék a nem megfelelőséget.


![Megfelelőség Azure Policy Azure Monitor naplók használatával](../media/getting-compliance-data/compliance-loganalytics.png)

## <a name="next-steps"></a>További lépések

- Tekintse át a példákat [Azure Policy mintákon](../samples/index.md).
- Tekintse meg az [Azure szabályzatdefiníciók struktúrája](../concepts/definition-structure.md) szakaszt.
- A [Szabályzatok hatásainak ismertetése](../concepts/effects.md).
- Megtudhatja, hogyan [hozhat létre programozott módon házirendeket](programmatically-create.md).
- Ismerje meg, hogyan javíthatja a [nem megfelelő erőforrásokat](remediate-resources.md).
- Tekintse át, hogy a felügyeleti csoport hogyan [rendezi az erőforrásokat az Azure felügyeleti csoportjaival](../../management-groups/overview.md).