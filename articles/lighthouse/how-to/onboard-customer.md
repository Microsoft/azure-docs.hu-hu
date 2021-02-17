---
title: Ügyfél előkészítése az Azure Lighthouse-hoz
description: Ismerje meg, hogyan végezheti el az ügyfelek Azure világítótoronyba való bevezetését, így az erőforrásaik a saját bérlőn keresztül érhetők el és kezelhetők az Azure-beli delegált erőforrás-kezelés használatával.
ms.date: 02/16/2021
ms.topic: how-to
ms.openlocfilehash: 4487dd82b30e14f9db2001dc10f7437a53e745f3
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100556103"
---
# <a name="onboard-a-customer-to-azure-lighthouse"></a>Ügyfél előkészítése az Azure Lighthouse-hoz

Ez a cikk azt ismerteti, hogy Ön, mint szolgáltató, hogyan helyezhet üzembe egy ügyfelet az Azure Lighthouse szolgáltatásban. Ha így tesz, a delegált erőforrások (előfizetések és/vagy erőforráscsoportok) az ügyfél Azure Active Directory (Azure AD) bérlője a saját bérlőn keresztül felügyelhető az Azure-beli [delegált erőforrás-kezelés](../concepts/azure-delegated-resource-management.md)használatával.

> [!TIP]
> Bár a jelen témakörben a szolgáltatók és az ügyfelek számára is hivatkozunk, a [több bérlőt kezelő vállalatok](../concepts/enterprise.md) ugyanazt a folyamatot használhatják az Azure Lighthouse beállításához és a kezelési élmény megszilárdításához.

A bevezetési folyamat több ügyfél számára is megismételhető. Ha a megfelelő engedélyekkel rendelkező felhasználó bejelentkezik a kezelő bérlőbe, a felhasználó az ügyfél-kihelyezés hatókörén belül jogosult a felügyeleti műveletek végrehajtására, anélkül, hogy be kellene jelentkeznie minden egyes ügyfél-bérlőre.

Ha nyomon szeretné követni az ügyfelek bevonásait és az elismerést, társítsa a Microsoft Partner Network (MPN) AZONOSÍTÓját legalább egy olyan felhasználói fiókkal, amely hozzáfér a beérkező előfizetésekhez. Ezt a társítást a szolgáltatói bérlőben kell végrehajtania. Azt javasoljuk, hogy hozzon létre egy egyszerű szolgáltatásnevet a bérlőben, amely az MPN-AZONOSÍTÓhoz van társítva, majd az adott egyszerű szolgáltatást is beleértve minden alkalommal, amikor előkészíti az ügyfelet. További információ: [a partner azonosítójának összekapcsolása a partner által a delegált erőforrásokon kapott jóváírás engedélyezéséhez](partner-earned-credit.md).

> [!NOTE]
> Az Azure világítótoronyban az ügyfelek felhasználhatják az Azure-világítótornyot, amikor az [Azure Marketplace](publish-managed-services-offers.md)-en közzétett felügyelt szolgáltatási ajánlatot (nyilvános vagy privát) vásárolnak. Az itt ismertetett bevezetési folyamatot az Azure Marketplace-en közzétett ajánlatokkal együtt is használhatja.

A bevezetési folyamathoz a szolgáltató bérlője és az ügyfél bérlője között végrehajtandó műveletek szükségesek. A fenti lépéseket a cikk ismerteti.

## <a name="gather-tenant-and-subscription-details"></a>Bérlői és előfizetési adatok összegyűjtése

Az ügyfél bérlője számára aktív Azure-előfizetéssel kell rendelkeznie. Ismernie kell a következőket:

- A szolgáltató bérlője bérlői azonosítója (ahol az ügyfél erőforrásait fogja kezelni)
- Az ügyfél bérlője bérlői azonosítója (amelynek erőforrásai a szolgáltató által felügyelt erőforrások lesznek)
- Az ügyfél bérlője által felügyelt minden egyes előfizetéshez tartozó előfizetési azonosítók (vagy a szolgáltató által felügyelt erőforráscsoport (oka) t tartalmazza).

Ha még nem rendelkezik ezekkel az azonosító értékekkel, a következő módokon kérheti le őket. Ügyeljen rá, hogy ezeket a pontos értékeket használja az üzemelő példányban.

### <a name="azure-portal"></a>Azure Portal

A bérlő AZONOSÍTÓját a Azure Portal jobb felső sarkában lévő fiók neve fölé helyezve, vagy a **könyvtár váltása** lehetőség kiválasztásával lehet látni. A bérlői azonosító kiválasztásához és másolásához keressen a "Azure Active Directory" kifejezésre a portálon, majd válassza a **Tulajdonságok** lehetőséget, és másolja ki a **címtár-azonosító** mezőben megjelenő értéket. Az előfizetés ügyfél-bérlőben történő megkereséséhez keressen rá az "előfizetések" kifejezésre, majd válassza ki a megfelelő előfizetés-azonosítót.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

Select-AzSubscription <subscriptionId>
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az account set --subscription <subscriptionId/name>
az account show
```

> [!NOTE]
> Ha egy előfizetést (vagy egy vagy több, az előfizetésen belüli erőforráscsoportot) készít az itt leírt eljárással, a **Microsoft. ManagedServices** erőforrás-szolgáltató regisztrálva lesz az adott előfizetéshez.

## <a name="define-roles-and-permissions"></a>Szerepkörök és engedélyek definiálása

Szolgáltatóként több feladatot is el lehet végezni egyetlen ügyfél számára, eltérő hozzáférésre van szükség a különböző hatókörökhöz. A megfelelő [Azure beépített szerepkörök](../../role-based-access-control/built-in-roles.md)hozzárendeléséhez annyi jogosultságot adhat meg, amennyire csak szüksége van. Minden engedély tartalmaz egy **principalId** , amely egy Azure ad-felhasználóra,-csoportra vagy egy egyszerű szolgáltatásnévre hivatkozik a bérlői felügyeletben.

> [!NOTE]
> Ha explicit módon meg van adva, az Azure Lighthouse dokumentációjában lévő "user" kifejezésre mutató hivatkozások egy adott Azure AD-felhasználóra, csoportra vagy egyszerű szolgáltatásra vonatkoznak az engedélyekben.

A felügyelet egyszerűbbé tételéhez ajánlott az egyes szerepkörökhöz tartozó Azure AD-felhasználói csoportokat használni, nem pedig az egyes felhasználókra. Ez rugalmasságot biztosít az egyes felhasználók hozzáadásához vagy eltávolításához a hozzáféréssel rendelkező csoport számára, így nem kell megismételni a bevezetési folyamatot a felhasználói módosítások elvégzéséhez. Szerepköröket is hozzárendelhet egy egyszerű szolgáltatáshoz, ami automatizálható az automatizálási forgatókönyvek esetében.

> [!IMPORTANT]
> Az Azure AD-csoport engedélyeinek hozzáadásához a **csoport típusát** **biztonsági** értékre kell állítani. Ez a beállítás a csoport létrehozásakor van kiválasztva. További információkért lásd: [alapszintű csoport létrehozása és Tagok hozzáadása Azure Active Directory használatával](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

Az engedélyek meghatározásakor ügyeljen arra, hogy kövesse a legalacsonyabb jogosultsági szint elvét, hogy a felhasználók csak a feladataik elvégzéséhez szükséges engedélyekkel rendelkezzenek. További információ a támogatott szerepkörökről és az ajánlott eljárásokról: [bérlők, felhasználók és szerepkörök az Azure Lighthouse-forgatókönyvekben](../concepts/tenants-users-roles.md).

Az engedélyek definiálásához ismernie kell az egyes felhasználók, felhasználói csoportok vagy egyszerű szolgáltatásnév azonosító értékeit abban a szolgáltatói bérlőn, amelyhez hozzáférést szeretne biztosítani. A hozzárendelni kívánt beépített szerepkörökhöz is szüksége lesz a szerepkör-definíciós AZONOSÍTÓra. Ha még nem rendelkezik velük, lekérheti őket az alábbi parancsok futtatásával a szolgáltatói bérlőn belül.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# To retrieve the objectId for an Azure AD group
(Get-AzADGroup -DisplayName '<yourGroupName>').id

# To retrieve the objectId for an Azure AD user
(Get-AzADUser -UserPrincipalName '<yourUPN>').id

# To retrieve the objectId for an SPN
(Get-AzADApplication -DisplayName '<appDisplayName>' | Get-AzADServicePrincipal).Id

# To retrieve role definition IDs
(Get-AzRoleDefinition -Name '<roleName>').id
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# To retrieve the objectId for an Azure AD group
az ad group list --query "[?displayName == '<yourGroupName>'].objectId" --output tsv

# To retrieve the objectId for an Azure AD user
az ad user show --id "<yourUPN>" --query "objectId" --output tsv

# To retrieve the objectId for an SPN
az ad sp list --query "[?displayName == '<spDisplayName>'].objectId" --output tsv

# To retrieve role definition IDs
az role definition list --name "<roleName>" | grep name
```

> [!TIP]
> Javasoljuk, hogy a [felügyelt szolgáltatások regisztrációs hozzárendelésének törlési szerepkörét](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) a felhasználó bevezetéséhez rendelje hozzá, hogy a bérlő felhasználói szükség esetén később is [el tudják távolítani a delegáláshoz való hozzáférést](remove-delegation.md) . Ha ez a szerepkör nincs hozzárendelve, a delegált erőforrásokat csak egy felhasználó távolíthatja el az ügyfél bérlője számára.

## <a name="create-an-azure-resource-manager-template"></a>Azure Resource Manager sablon létrehozása

Az ügyfél beléptetéséhez létre kell hoznia egy [Azure Resource Manager](../../azure-resource-manager/index.yml) sablont az ajánlatához a következő információkkal. A **mspOfferName** és a **mspOfferDescription** értékek láthatók lesznek az ügyfél számára a Azure Portal [szolgáltató lapján](view-manage-service-providers.md) .

|Mező  |Meghatározás  |
|---------|---------|
|**mspOfferName**     |A definíciót leíró név. Ez az érték jelenik meg az ügyfél számára az ajánlat címének, és egyedi értéknek kell lennie.        |
|**mspOfferDescription**     |Az ajánlat rövid leírása (például "contoso VM Management ajánlat").      |
|**managedByTenantId**     |A bérlő azonosítója.          |
|**engedélyek**     |A bérlőből származó felhasználók/csoportok/SPN- **principalId** értékei **, amelyek segítségével** az ügyfelek megismerhetik az engedélyezés célját, és egy beépített **roleDefinitionId** értékre vannak leképezve, hogy megadják a hozzáférési szintet.      |

A bevezetési folyamathoz szükség van egy Azure Resource Manager sablonra (a [mintákat](https://github.com/Azure/Azure-Lighthouse-samples/)tartalmazó tárházban), valamint egy megfelelő, a konfigurációnak megfelelően módosított paramétereket tartalmazó fájlra és az engedélyek megadására.

> [!IMPORTANT]
> Az itt leírt folyamat külön üzembe helyezést igényel minden előfizetéshez, még akkor is, ha az előfizetések ugyanabban az ügyfél-bérlőben vannak bevezetésben. A különálló központi telepítések akkor is szükségesek, ha több erőforráscsoportot is előkészít ugyanazon ügyfél bérlője különböző előfizetéseken belül. Egy adott előfizetésen belül több erőforráscsoport bevezetését azonban egyetlen központi telepítésben is elvégezheti.
>
> Külön központi telepítések is szükségesek ahhoz, hogy több ajánlat is alkalmazható legyen ugyanarra az előfizetésre (vagy az előfizetésen belüli erőforráscsoportok). Minden egyes alkalmazásnak eltérő **mspOfferName** kell használnia.

A választott sablon attól függ, hogy teljes előfizetést, erőforráscsoportot vagy több erőforráscsoportot készít elő egy előfizetésen belül. Egy olyan sablont is biztosítunk, amely az Azure Marketplace-en közzétett, felügyelt szolgáltatási ajánlatot megvásárló ügyfelek számára is felhasználható, ha így szeretne előfizetni.

|A beléptetéshez  |Azure Resource Manager sablon használata  |A paraméter fájljának módosítása |
|---------|---------|---------|
|Előfizetés   |[delegatedResourceManagement.jsbekapcsolva](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management/delegatedResourceManagement.json)  |[delegatedResourceManagement.parameters.jsbekapcsolva](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management/delegatedResourceManagement.parameters.json)    |
|Erőforráscsoport   |[rgDelegatedResourceManagement.jsbekapcsolva](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.json)  |[rgDelegatedResourceManagement.parameters.jsbekapcsolva](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.parameters.json)    |
|Több erőforráscsoport egy előfizetésen belül   |[multipleRgDelegatedResourceManagement.jsbekapcsolva](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.json)  |[multipleRgDelegatedResourceManagement.parameters.jsbekapcsolva](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.parameters.json)    |
|Előfizetés (az Azure Marketplace-en közzétett ajánlat használata esetén)   |[marketplaceDelegatedResourceManagement.jsbekapcsolva](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.json)  |[marketplaceDelegatedResourceManagement.parameters.jsbekapcsolva](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.parameters.json)    |

> [!TIP]
> Noha egyetlen központi telepítésben nem lehet teljes felügyeleti csoportot bevezetni, [a felügyeleti csoport szintjén telepítheti a szabályzatot](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-delegate-management-groups). A szabályzat azt vizsgálja, hogy a felügyeleti csoporton belüli egyes előfizetések delegálása megtörtént-e a megadott kezelési bérlőre, és ha nem, akkor az Ön által megadott értékek alapján hozza létre a hozzárendelést.

Az alábbi példa egy olyan módosított **delegatedResourceManagement.parameters.jst** mutat be, amely az előfizetés előkészítéséhez használható. Az erőforráscsoport-paraméter fájljai (az [RG-delegált erőforrás-kezelő](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/rg-delegated-resource-management) mappában találhatók) hasonlóak, de tartalmaznak egy **rgName** paramétert is a bevezetéshez megadott erőforráscsoport (ok) azonosításához.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mspOfferName": {
            "value": "Fabrikam Managed Services - Interstellar"
        },
        "mspOfferDescription": {
            "value": "Fabrikam Managed Services - Interstellar"
        },
        "managedByTenantId": {
            "value": "df4602a3-920c-435f-98c4-49ff031b9ef6"
        },
        "authorizations": {
            "value": [
                {
                    "principalId": "0019bcfb-6d35-48c1-a491-a701cf73b419",
                    "principalIdDisplayName": "Tier 1 Support",
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
                },
                {
                    "principalId": "0019bcfb-6d35-48c1-a491-a701cf73b419",
                    "principalIdDisplayName": "Tier 1 Support",
                    "roleDefinitionId": "36243c78-bf99-498c-9df9-86d9f8d28608"
                },
                {
                    "principalId": "0afd8497-7bff-4873-a7ff-b19a6b7b332c",
                    "principalIdDisplayName": "Tier 2 Support",
                    "roleDefinitionId": "acdd72a7-3385-48ef-bd42-f606fba81ae7"
                },
                {
                    "principalId": "9fe47fff-5655-4779-b726-2cf02b07c7c7",
                    "principalIdDisplayName": "Service Automation Account",
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
                },
                {
                    "principalId": "3kl47fff-5655-4779-b726-2cf02b05c7c4",
                    "principalIdDisplayName": "Policy Automation Account",
                    "roleDefinitionId": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
                    "delegatedRoleDefinitionIds": [
                        "b24988ac-6180-42a0-ab88-20f7382dd24c",
                        "92aaf0da-9dab-42b6-94a3-d43ce8d16293"
                    ]
                }
            ]
        }
    }
}
```

A fenti példában szereplő utolsó engedély egy **principalId** hoz létre a felhasználói hozzáférés rendszergazdai szerepkörrel (18d7d88d-d35e-4fb5-a5c3-7773c20a72d9). A szerepkör kiosztásakor meg kell adnia a **delegatedRoleDefinitionIds** tulajdonságot, és egy vagy több támogatott Azure beépített szerepkört. Az ebben az engedélyezésben létrehozott felhasználó hozzá tudja rendelni ezeket a szerepköröket a [felügyelt identitásokhoz](../../active-directory/managed-identities-azure-resources/overview.md) az ügyfél bérlője számára, amely a [szervizelhető házirendek telepítéséhez](deploy-policy-remediation.md)szükséges.  A felhasználó támogatási incidenseket is létrehozhat. A felhasználói hozzáférés rendszergazdai szerepkörhöz általában nem társított egyéb engedélyek lesznek érvényesek erre a **principalId**.

## <a name="deploy-the-azure-resource-manager-templates"></a>A Azure Resource Manager-sablonok üzembe helyezése

A paramétert tartalmazó fájl frissítése után az ügyfél bérlője a Azure Resource Manager sablont a bérlőn belül kell telepítenie. Külön üzembe helyezésre van szükség minden olyan előfizetéshez, amelyet be szeretne készíteni a bevezetéshez (vagy minden olyan előfizetéshez, amely a bevezetéshez használni kívánt erőforráscsoportokat tartalmaz).

> [!IMPORTANT]
> Ezt az üzembe helyezést egy olyan nem vendég fiókkal kell végrehajtani az ügyfél bérlője számára, akinek van szerepköre a `Microsoft.Authorization/roleAssignments/write` (z) előfizetésben (vagy amely tartalmazza az előkészítés alatt álló erőforráscsoportokat) a [tulajdonossal](../../role-based-access-control/built-in-roles.md#owner). Az előfizetést delegáló felhasználók megkereséséhez az ügyfél bérlője számára kiválaszthatja az előfizetést a Azure Portalban, megnyithatja a **hozzáférés-vezérlés (iam)** lehetőséget, és [megtekintheti a tulajdonosi szerepkörrel rendelkező összes felhasználót](../../role-based-access-control/role-assignments-list-portal.md#list-owners-of-a-subscription). 
>
> Ha az előfizetés a [Cloud Solution Provider (CSP) programon](../concepts/cloud-solution-provider.md)keresztül lett létrehozva, akkor minden olyan felhasználó, aki rendelkezik [rendszergazdai ügynök](/partner-center/permissions-overview#manage-commercial-transactions-in-partner-center-azure-ad-and-csp-roles) szerepkörrel a szolgáltatói bérlőben, végrehajthatja a telepítést.

Az üzembe helyezés a Azure Portal a PowerShell vagy az Azure CLI használatával végezhető el, az alább látható módon.

### <a name="azure-portal"></a>Azure Portal

1. A [GitHub](https://github.com/Azure/Azure-Lighthouse-samples/)-tárházban válassza az **üzembe helyezés az Azure** -ban gombot, amely a használni kívánt sablon mellett látható. A sablon az Azure Portalon fog megnyílni.
1. Adja meg az **MSP-ajánlat neve**, az **MSP-ajánlat leírása**, a **bérlői azonosító** és a **jogosultságok** által kezelt értékeket. Ha szeretné, a **Paraméterek szerkesztése** lehetőség kiválasztásával megadhatja a,,, `mspOfferName` `mspOfferDescription` `managedbyTenantId` és `authorizations` közvetlenül a paraméter fájljának értékét. Ne felejtse el frissíteni ezeket az értékeket a sablon alapértelmezett értékeinek használata helyett.
1. Válassza a **felülvizsgálat és létrehozás**, majd a **Létrehozás** lehetőséget.

Néhány perc elteltével megjelenik egy értesítés arról, hogy a telepítés befejeződött.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Deploy Azure Resource Manager template using template and parameter file locally
New-AzSubscriptionDeployment -Name <deploymentName> `
                 -Location <AzureRegion> `
                 -TemplateFile <pathToTemplateFile> `
                 -TemplateParameterFile <pathToParameterFile> `
                 -Verbose

# Deploy Azure Resource Manager template that is located externally
New-AzSubscriptionDeployment -Name <deploymentName> `
                 -Location <AzureRegion> `
                 -TemplateUri <templateUri> `
                 -TemplateParameterUri <parameterUri> `
                 -Verbose
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# Deploy Azure Resource Manager template using template and parameter file locally
az deployment sub create --name <deploymentName> \
                         --location <AzureRegion> \
                         --template-file <pathToTemplateFile> \
                         --parameters <parameters/parameterFile> \
                         --verbose

# Deploy external Azure Resource Manager template, with local parameter file
az deployment sub create --name <deploymentName> \
                         --location <AzureRegion> \
                         --template-uri <templateUri> \
                         --parameters <parameterFile> \
                         --verbose
```

## <a name="confirm-successful-onboarding"></a>Sikeres előkészítés megerősítése

Ha az ügyfél-előfizetés sikeresen bekerült az Azure Lighthouse-be, a szolgáltató bérlője felhasználói megtekinthetik az előfizetést és annak erőforrásait (ha a fenti eljáráson keresztül kaptak hozzáférést a hozzáféréshez, vagy egy Azure AD-csoport tagjaként a megfelelő engedélyekkel). Ennek megerősítéséhez győződjön meg arról, hogy az előfizetés az alábbi módszerek egyikével jelenik meg.  

### <a name="azure-portal"></a>Azure Portal

A szolgáltató bérlője:

1. Navigáljon a [saját ügyfelek oldalra](view-manage-customers.md).
2. Válassza az **Ügyfelek** lehetőséget.
3. Győződjön meg arról, hogy az előfizetés (ok) a Resource Manager-sablonban megadott ajánlat nevével jelenik meg.

> [!IMPORTANT]
> Ha szeretné megtekinteni a delegált előfizetést az [ügyfeleken](view-manage-customers.md), a szolgáltató bérlője felhasználóinak meg kell adni az [olvasó](../../role-based-access-control/built-in-roles.md#reader) szerepkört (vagy egy másik beépített szerepkört, amely olvasói hozzáférést is tartalmaz) az előfizetés előállítása során.

Az ügyfél bérlője:

1. Navigáljon a szolgáltatók [lapra](view-manage-service-providers.md).
2. Válassza a **Szolgáltatói ajánlatok** lehetőséget.
3. Győződjön meg arról, hogy az előfizetés (ok) a Resource Manager-sablonban megadott ajánlat nevével jelenik meg.

> [!NOTE]
> A telepítés befejezése után akár 15 percet is igénybe vehet, mielőtt a frissítések megjelennek a Azure Portalban. Előfordulhat, hogy hamarabb megtekintheti a frissítéseket, ha frissíti a Azure Resource Manager tokent a böngésző frissítésével, beléptetéssel vagy új token kérésével.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

Get-AzContext

# Confirm successful onboarding for Azure Lighthouse

Get-AzManagedServicesDefinition
Get-AzManagedServicesAssignment
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az account list
```

Ha módosításokat kell végeznie az ügyfél beléptetése után, akkor [frissítheti a delegálást](update-delegation.md). [A delegáláshoz való hozzáférést teljesen el is távolíthatja](remove-delegation.md) .

## <a name="troubleshooting"></a>Hibaelhárítás

Ha nem tudja sikeresen bevezetni az ügyfelet, vagy ha a felhasználók nem tudnak hozzáférni a delegált erőforrásokhoz, tekintse meg az alábbi tippeket és követelményeket, és próbálkozzon újra.

- Az `managedbyTenantId` érték nem egyezhet meg az előfizetéshez tartozó bérlői azonosítóval.
- Ugyanahhoz a hatókörhöz nem tartozhat több hozzárendelés `mspOfferName` .
- A delegált előfizetéshez regisztrálni kell a **Microsoft. ManagedServices** erőforrás-szolgáltatót. Ez automatikusan megtörténik az üzembe helyezés során, de ha nem, akkor [manuálisan is regisztrálhatja](../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider).
- Az engedélyek nem tartalmazhatnak olyan felhasználókat, akik a [tulajdonos](../../role-based-access-control/built-in-roles.md#owner) beépített szerepkörével vagy a [DataActions](../../role-based-access-control/role-definitions.md#dataactions)-mel rendelkező beépített szerepkörökkel rendelkeznek.
- A csoportokat úgy kell létrehozni, hogy a [**csoport típusa**](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md#group-types) legyen **biztonsági** , és ne **Microsoft 365**.
- A [beágyazott csoportok](../..//active-directory/fundamentals/active-directory-groups-membership-azure-portal.md)hozzáférésének engedélyezése előtt további késleltetés is lehetséges.
- Azok a felhasználók, akiknek a Azure Portal erőforrásainak meg kell tekinteniük az [olvasó](../../role-based-access-control/built-in-roles.md#reader) szerepkört (vagy egy másik beépített szerepkört, amely olvasói hozzáférést is tartalmaz).
- Az engedélyezésekben foglalt [Azure beépített szerepkörök](../../role-based-access-control/built-in-roles.md) nem tartalmazhatnak elavult szerepköröket. Ha egy Azure beépített szerepkör elavulttá válik, a szerepkörbe tartozó összes felhasználó elveszíti a hozzáférést, és nem fog tudni további delegálásokat bevezetni. A probléma megoldásához frissítse a sablont csak a támogatott beépített szerepkörök használatára, majd hajtson végre egy új központi telepítést.

## <a name="next-steps"></a>Következő lépések

- További információ a [bérlők közötti felügyeleti élményekről](../concepts/cross-tenant-management-experience.md).
- [Megtekintheti és kezelheti az ügyfeleket](view-manage-customers.md) a Azure Portalban lévő **ügyfelekkel** .
- Megtudhatja, hogyan [frissítheti](update-delegation.md) vagy [távolíthatja el](remove-delegation.md) a delegálást.
