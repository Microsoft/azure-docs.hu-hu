---
title: Ügyfél előkészítése az Azure Lighthouse-hoz
description: Megtudhatja, hogyan lehet egy ügyfelet Azure Lighthouse, hogy az erőforrásai a saját bérlőn keresztül érhetők el és kezelhetők az Azure által delegált erőforrás-kezelés használatával.
ms.date: 03/29/2021
ms.topic: how-to
ms.openlocfilehash: d8ad448ac022b07ecdea6b68c4544b8c955814b1
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107497965"
---
# <a name="onboard-a-customer-to-azure-lighthouse"></a>Ügyfél előkészítése az Azure Lighthouse-hoz

Ez a cikk azt ismerteti, hogy szolgáltatóként hogyan lehet ügyfelet Azure Lighthouse. Ha ezt a lehetőséget használja, az ügyfél Azure Active Directory- (Azure AD-) bérlőjéhez delegált erőforrások (előfizetések és/vagy erőforráscsoportok) az Azure által delegált erőforrás-kezelés használatával kezelhetők a saját [bérlőn keresztül.](../concepts/azure-delegated-resource-management.md)

> [!TIP]
> Bár ebben a témakörben szolgáltatókra [](../concepts/enterprise.md) és ügyfelekre hivatkozunk, a több bérlőt kezelő vállalatok ugyanazt a folyamatot Azure Lighthouse és konszolidálják a felügyeleti élményt.

A folyamatot megismételheti több ügyfél esetében is. Amikor egy megfelelő engedélyekkel rendelkező felhasználó bejelentkezik a felügyeleti bérlőbe, a felhasználó az ügyfél bérlői hatókörében engedélyezheti a felügyeleti műveletek elvégzését anélkül, hogy minden egyes ügyfélbérlőbe be kellene jelentkeznie.

Az ügyfélkapcsolatok közötti hatás nyomon követéséhez és a felismerés fogadásához társítsa az Microsoft Partner Network-azonosítóját (MPN) legalább egy olyan felhasználói fiókhoz, amely hozzáféréssel rendelkezik az egyes beléptetett előfizetéseihez. Ezt a társítást a szolgáltató bérlője számára kell elvégeznie. Javasoljuk, hogy hozzon létre egy egyszerű szolgáltatásfiókot a bérlőben, amely az MPN-azonosítóhoz van társítva, majd ezt a szolgáltatásnévvel együtt minden alkalommal, amikor Ön elővesz egy ügyfelet. További információ: Partnerazonosító csatolása a delegált erőforrások [partneri jóváírásának engedélyezéséhez.](partner-earned-credit.md)

> [!NOTE]
> Az ügyfelek alternatív módon akkor is Azure Lighthouse számára, ha felügyelt szolgáltatási ajánlatot vásárolnak (nyilvános vagy privát), amit Ön [közzétesz](publish-managed-services-offers.md)a Azure Marketplace. Használhatja az itt leírt folyamatot is az itt közzétett ajánlatokkal együtt, amelyek közzé vannak Azure Marketplace.

Az előfizetési folyamathoz a szolgáltató bérlőjéhez és az ügyfél bérlőjéhez is el kell hozni a szükséges műveleteket. Az összes lépést ebben a cikkben ismertetjük.

## <a name="gather-tenant-and-subscription-details"></a>Bérlő és előfizetés részleteinek gyűjtése

Az ügyfél bérlőinek aktív Azure-előfizetéssel kell, hogy legyen elővetve. A következőket kell tudni:

- A szolgáltató bérlőazonosítója (ahol Ön kezeli az ügyfél erőforrásait)
- Az ügyfél bérlőazonosítója (amelynek erőforrásait a szolgáltató kezeli)
- A szolgáltató által felügyelt ügyfélbérlő minden egyes előfizetéséhez (vagy a szolgáltató által felügyelt erőforráscsoport(k)t tartalmazó előfizetés-előfizetési adatokhoz.

Ha még nem rendelkezik ezekkel az azonosítóértékekkel, az alábbi módszerek egyikével lekérheti őket. Győződjön meg arról, hogy pontosan ezeket az értékeket használja az üzemelő példányban.

### <a name="azure-portal"></a>Azure Portal

A bérlőazonosítót úgy láthatja, ha a fiók nevére mutat a jobb felső sarokban, Azure Portal címtár **váltása lehetőség kiválasztásával.** A bérlőazonosító kiválasztásához és másolásához keressen rá a "Azure Active Directory"  kifejezésre a portálon, majd válassza a Tulajdonságok lehetőséget, és másolja ki a **Címtár-azonosító mezőben látható** értéket. Ha szeretné megtalálni egy ügyfélbérlő előfizetés-azonosítóját, keressen rá az „Előfizetések” kifejezésre, majd válassza ki a megfelelő előfizetés-azonosítót.

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
> Előfizetés (vagy egy vagy több erőforráscsoport az előfizetésen belül) az itt leírt folyamattal való regisztrálva lesz az adott előfizetéshez a **Microsoft.ManagedServices** erőforrás-szolgáltató.

## <a name="define-roles-and-permissions"></a>Szerepkörök és engedélyek meghatározása

Szolgáltatóként előfordulhat, hogy több feladatot szeretne elvégezni egy ügyfél számára, és különböző hatókörök eltérő hozzáférését igényli. A megfelelő beépített [Azure-szerepkörök](../../role-based-access-control/built-in-roles.md)hozzárendelése érdekében annyi engedélyt adhat meg, amennyit csak szeretne. Minden engedélyezés tartalmaz egy **principalId azonosítót,** amely egy Azure AD-felhasználóra, -csoportra vagy -szolgáltatásnévre vonatkozik a kezelő bérlőben.

> [!NOTE]
> Ha nincs explicit módon megadva, a Azure Lighthouse dokumentációjában található "felhasználóra" mutató hivatkozások egy Azure AD-felhasználóra, -csoportra vagy -szolgáltatásnévre vonatkozhatnak egy engedélyezésben.

A felügyelet egyszerűbb kezelése érdekében javasoljuk, hogy az egyes felhasználók helyett minden szerepkörhöz használja az Azure AD felhasználói csoportokat, amikor csak lehetséges. Így rugalmasan adhat hozzá vagy távolíthat el egyéni felhasználókat ahhoz a csoporthoz, amely rendelkezik hozzáféréssel, így nem kell megismételnie a felhasználói módosításokhoz szükséges be- és be- és felvételeket. A szerepköröket szolgáltatásnévhez is hozzárendelheti, ami automatizálási forgatókönyvek esetén lehet hasznos.

> [!IMPORTANT]
> Egy Azure AD-csoport engedélyeinek hozzáadásához a **Csoporttípust** Biztonságra kell **állítani.** Ez a beállítás a csoport létrehozásakor lesz kiválasztva. További információ: [Alapszintű](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)csoport létrehozása és tagok hozzáadása a Azure Active Directory.

Az engedélyek meghatározásakor mindenképpen kövesse a legkevesebb jogosultság elvét, hogy a felhasználók csak a munkájuk befejezéséhez szükséges engedélyekkel tudjanak rendelkezni. További információ a támogatott szerepkörökről és az ajánlott eljárásokról: [Bérlők, felhasználók és szerepkörök a Azure Lighthouse forgatókönyvekben.](../concepts/tenants-users-roles.md)

Az engedélyek meghatározásához minden olyan felhasználónak, felhasználói csoportnak vagy szolgáltatásnévnek az azonosítóértékét meg kell határoznia a szolgáltató bérlőben, amelyhez hozzáférést szeretne. Minden hozzárendelni kívánt beépített szerepkörhöz szüksége lesz a szerepkördefiníció-azonosítóra is. Ha még nem rendelkezik velük, lekérheti őket az alábbi parancsok futtatásával a szolgáltató bérlőjébe.

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
> Javasoljuk, hogy [](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) az ügyfél beléptetéskor rendelje hozzá a Felügyelt szolgáltatások [](remove-delegation.md) regisztráció-hozzárendelésének törlési szerepkörét, hogy a bérlő felhasználói szükség esetén később el tudja távolítani a delegáláshoz való hozzáférést. Ha ez a szerepkör nincs hozzárendelve, a delegált erőforrásokat csak az ügyfél bérlő felhasználója távolíthatja el.

## <a name="create-an-azure-resource-manager-template"></a>Sablon Azure Resource Manager létrehozása

Az ügyfél beléptetéséhez létre kell hoznia az ajánlatához egy [Azure Resource Manager](../../azure-resource-manager/index.yml)-sablont a következő információkkal. Az **mspOfferName** és **az mspOfferDescription** érték az [](view-manage-service-providers.md) ügyfél számára a szolgáltatás Szolgáltatók lapján Azure Portal.

|Mező  |Meghatározás  |
|---------|---------|
|**mspOfferName**     |A definíciót leíró név. Ez az érték jelenik meg az ügyfél számára az ajánlat címeként, és egyedi értéknek kell lennie.        |
|**mspOfferDescription**     |Az ajánlat rövid leírása (például "Contoso vm management offer").      |
|**managedByTenantId**     |Az Ön bérlőazonosítója.          |
|**Engedélyek**     |A **bérlő** felhasználóinak/csoportjainak/egyszerű szolgáltatásnevének principalId értékei, amelyek mind **principalIdDisplayName** értékkel bírnak, hogy segítsenek az ügyfélnek megérteni az engedélyezés célját, és leképezve vannak egy beépített **roleDefinitionId** értékre a hozzáférési szint megadásához.      |

A folyamathoz szükség van egy Azure Resource Manager sablonra (amely a mintatárunkban [található)](https://github.com/Azure/Azure-Lighthouse-samples/)és egy megfelelő paraméterfájlra, amely a konfigurációnak megfelelően módosítható, és meghatározza az engedélyeket.

> [!IMPORTANT]
> Az itt leírt folyamat minden egyes bevezetés alatt áll előfizetéshez külön üzembe helyezést igényel, még akkor is, ha az előfizetéseket ugyanabban az ügyfélbérlőben választja ki. Külön üzembe helyezésre is szükség van, ha több erőforráscsoportot is bevesz ugyanabban az ügyfélbérlőben különböző előfizetésekbe. Egy előfizetésen belül azonban több erőforráscsoportot is be lehet őket táblába bevetni.
>
> Külön üzemelő példányok is szükségesek, ha több ajánlat van alkalmazva ugyanazon előfizetésre (vagy egy előfizetésen belüli erőforráscsoportokra). Minden alkalmazott ajánlatnak más **mspOfferName nevet kell használnia.**

A választott sablon attól függ, hogy egy teljes előfizetést, egy erőforráscsoportot vagy több erőforráscsoportot használ egy előfizetésen belül. Emellett egy olyan sablont is biztosítunk, amely a Azure Marketplace-on közzétett felügyelt szolgáltatási ajánlatot megvásároló ügyfelek számára használható, ha inkább így szeretné előszabadni az előfizetés(eket).

|A bevetni  |A sablon Azure Resource Manager használata  |És módosítsa ezt a paraméterfájlt |
|---------|---------|---------|
|Előfizetés   |[delegatedResourceManagement.jsbe](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management/delegatedResourceManagement.json)  |[delegatedResourceManagement.parameters.jsbe](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management/delegatedResourceManagement.parameters.json)    |
|Erőforráscsoport   |[rgDelegatedResourceManagement.jsbe](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.json)  |[rgDelegatedResourceManagement.parameters.jsbe](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.parameters.json)    |
|Több erőforráscsoport egy előfizetésen belül   |[multipleRgDelegatedResourceManagement.jsbe](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.json)  |[multipleRgDelegatedResourceManagement.parameters.jsbe](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.parameters.json)    |
|Előfizetés (a szolgáltatásban közzétett ajánlat Azure Marketplace)   |[marketplaceDelegatedResourceManagement.jsbe](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.json)  |[marketplaceDelegatedResourceManagement.parameters.jsbe](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.parameters.json)    |

> [!TIP]
> Bár egy telepítésben nem lehet egy teljes felügyeleti csoportot bevetni, a házirendet a felügyeleti csoport [szintjén is telepítheti.](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-delegate-management-groups) A szabályzat a [deployIfNotExists](../../governance/policy/concepts/effects.md#deployifnotexists) hatás segítségével ellenőrzi, hogy a felügyeleti csoportban található egyes előfizetések delegálva vannak-e a megadott felügyeleti bérlőhöz, és ha nem, az Ön által megadott értékek alapján hozza létre a hozzárendelést. Ezután hozzáférhet a felügyeleti csoport összes előfizetéséhez, de különálló előfizetésként kell majd dolgoznia velük (ahelyett, hogy a teljes felügyeleti csoporton kellene műveleteket eszközlni).

Az alábbi példa egy módosítottdelegatedResourceManagement.parameters.js **mutat** be egy fájlban, amely felhasználható az előfizetések előjegyzéséhez. Az erőforráscsoport paraméterfájlja (amely az [rg-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/rg-delegated-resource-management) mappában található) hasonlóak, de egy **rgName** paramétert is tartalmaz, amely azonosítja az adott, az ön által bevetni kívánt erőforráscsoport(ak)t.

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

A fenti példában az utolsó engedélyezés hozzáad egy **principalId** azonosítót a Felhasználói hozzáférés rendszergazdája szerepkörhöz (18d7d88d-d35e-4fb5-a5c3-7773c20a72d9). A szerepkör hozzárendelésekor tartalmaznia kell a **delegatedRoleDefinitionIds** tulajdonságot és egy vagy több támogatott beépített Azure-szerepkört. Az ebben az engedélyezésben létrehozott felhasználó hozzárendelheti ezeket a szerepköröket az ügyfélbérlőben felügyelt identitásokhoz, ami a szervizelt szabályzatok telepítéséhez [szükséges.](deploy-policy-remediation.md) [](../../active-directory/managed-identities-azure-resources/overview.md)  A felhasználó támogatási incidenseket is létrehozhat. A felhasználói hozzáférés-rendszergazdai szerepkörhöz általában társított egyéb engedélyek nem érvényesek erre a **principalId azonosítóra.**

## <a name="deploy-the-azure-resource-manager-templates"></a>A sablonsablonok Azure Resource Manager üzembe helyezése

Miután frissítette a paraméterfájlt, az ügyfél bérlőjéhez egy felhasználónak üzembe kell helyeznie a Azure Resource Manager sablont a bérlőn belül. Minden egyes bevezetési előfizetéshez (vagy minden olyan előfizetéshez, amely a bevezetésre kívánt erőforráscsoportokat tartalmaz) külön üzembe helyezésre van szükség.

> [!IMPORTANT]
> Ezt az üzembe helyezést egy nem vendégfióknak kell lennie az ügyfél bérlőjében, amely rendelkezik engedéllyel (például Tulajdonos) a bevezetés alatt található előfizetéshez (vagy amely tartalmazza a bevezetés alatt található `Microsoft.Authorization/roleAssignments/write` erőforráscsoportokat). [](../../role-based-access-control/built-in-roles.md#owner) Ha olyan felhasználókat keres, akik delegálhatja az előfizetést, az ügyfél bérlőjéhez egy felhasználó kiválaszthatja az előfizetést a Azure Portal-ban, megnyithatja a Hozzáférés-vezérlést **(IAM),** és megtekintheti a Tulajdonos szerepkörű összes [felhasználót.](../../role-based-access-control/role-assignments-list-portal.md#list-owners-of-a-subscription) 
>
> Ha az előfizetést a [Felhőszolgáltató (CSP) programon](../concepts/cloud-solution-provider.md)keresztül [](/partner-center/permissions-overview#manage-commercial-transactions-in-partner-center-azure-ad-and-csp-roles) hozta létre, akkor minden olyan felhasználó végrehajthatja a telepítést, aki rendszergazdai ügynök szerepkörsel rendelkezik a szolgáltató bérlőben.

Az üzembe helyezést az alábbi Azure Portal a PowerShell vagy az Azure CLI használatával is el lehet végezve.

### <a name="azure-portal"></a>Azure Portal

1. A [GitHub-adattárban](https://github.com/Azure/Azure-Lighthouse-samples/)kattintson a használni kívánt sablon mellett látható Üzembe helyezés az **Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.
1. Adja meg az **Msp-ajánlat neve,** **az Msp-ajánlat leírása,** a **Bérlőazonosító által kezelt** és az Engedélyek **értékeit.** Ha szeretné, a  Paraméterek szerkesztése lehetőséget választva közvetlenül a paraméterfájlba is beírhatja a `mspOfferName` , , és paraméterek `mspOfferDescription` `managedbyTenantId` `authorizations` értékeit. Ne a sablon alapértelmezett értékeit használja, hanem frissítse ezeket az értékeket.
1. Válassza **az Áttekintés lehetőséget, és hozza létre** a et, majd válassza a Létrehozás **lehetőséget.**

Néhány perc múlva megjelenik egy értesítés arról, hogy az üzembe helyezés befejeződött.

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

## <a name="confirm-successful-onboarding"></a>Sikeres telepítés megerősítése

Ha egy ügyfél-előfizetés sikeresen elő lett hozva az Azure Lighthouse-hoz, a szolgáltató bérlőjébe beemelt felhasználók láthatják az előfizetést és annak erőforrásait (ha a fenti folyamaton keresztül hozzáférést kaptak hozzá, akár egyénileg, akár a megfelelő engedélyekkel rendelkező Azure AD-csoport tagjaként). Ennek ellenőrzéséhez ellenőrizze, hogy az előfizetés az alábbi módokon jelenik-e meg.  

### <a name="azure-portal"></a>Azure Portal

A szolgáltató bérlőben:

1. Lépjen a [Saját ügyfelek oldalra.](view-manage-customers.md)
2. Válassza az **Ügyfelek** lehetőséget.
3. Ellenőrizze, hogy látja-e a sablonban megadott ajánlatnévvel vagy előfizetésekkel Resource Manager előfizetéseket.

> [!IMPORTANT]
> Ahhoz, hogy a delegált előfizetés látható legyen a Saját ügyfelek csoportban, a szolgáltató bérlőjéhez a felhasználóknak Olvasó szerepkört (vagy egy másik beépített szerepkört, amely olvasói hozzáférést tartalmaz) kell biztosítaniuk az előfizetés beléptethetőjekor. [](view-manage-customers.md) [](../../role-based-access-control/built-in-roles.md#reader)

Az ügyfél bérlőben:

1. Lépjen a [Szolgáltatók lapra.](view-manage-service-providers.md)
2. Válassza a **Szolgáltatói ajánlatok** lehetőséget.
3. Ellenőrizze, hogy látja-e a sablonban megadott ajánlatnévvel vagy előfizetésekkel Resource Manager előfizetéseket.

> [!NOTE]
> Az üzembe helyezés befejezése után akár 15 percet is igénybe vehet, mielőtt a frissítések megjelennek a Azure Portal. Előfordulhat, hogy hamarabb látja a frissítéseket, ha frissíti a Azure Resource Manager-jogkivonatot a böngésző frissítéséhez, bejelentkezéshez és kijelentkezéshez vagy új jogkivonat kéréséhez.

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

# Confirm successful onboarding for Azure Lighthouse

az managedservices definition list
az managedservices assignment list
```

Ha az ügyfél létrehozása után módosításokat kell tennie, frissítheti [a delegálást.](update-delegation.md) Teljesen el is [távolíthatja a delegáláshoz való](remove-delegation.md) hozzáférést.

## <a name="troubleshooting"></a>Hibaelhárítás

Ha nem tudja sikeresen belépni az ügyfelet, vagy ha a felhasználók nem tudják elérni a delegált erőforrásokat, tekintse meg az alábbi tippeket és követelményeket, és próbálkozzon újra.

- Az érték nem lehet ugyanaz, mint az előhozott előfizetés `managedbyTenantId` bérlőazonosítója.
- Egy hatókörben nem lehet több hozzárendelés ugyanazokkal a `mspOfferName` hatókörrel.
- A **Microsoft.ManagedServices** erőforrás-szolgáltatót regisztrálni kell a delegált előfizetéshez. Ennek automatikusan meg kell történnie az üzembe helyezés során, de ha nem, [manuálisan is regisztrálhatja.](../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider)
- Az engedélyek nem tartalmazhatnak [](../../role-based-access-control/built-in-roles.md#owner) Tulajdonos beépített szerepkörű felhasználókat vagy a [DataActions beépített szerepköreit.](../../role-based-access-control/role-definitions.md#dataactions)
- A csoportokat Biztonság [**csoporttípussal**](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md#group-types) kell **létrehozni,** és nem **Microsoft 365.**
- A beágyazott csoportok hozzáférésének engedélyezése további [késleltetéssel is előfordulhat.](../..//active-directory/fundamentals/active-directory-groups-membership-azure-portal.md)
- Azok a felhasználók, akiknek meg kell tekinteniük [](../../role-based-access-control/built-in-roles.md#reader) az erőforrásokat a Azure Portal Olvasó szerepkört (vagy más beépített szerepkört, amely olvasói hozzáférést is tartalmaz).
- Az [engedélyekbe beépített Azure-szerepkörök](../../role-based-access-control/built-in-roles.md) nem tartalmazhatnak elavult szerepköröket. Ha egy beépített Azure-szerepkör elavult, az ezzel a szerepkörsel regisztrált felhasználók elveszítik a hozzáférését, és nem fognak tudni további delegálásokat is bevetni. Ennek kijavítás érdekében frissítse a sablont úgy, hogy csak a támogatott beépített szerepköröket használja, majd hajtson végre egy új üzembe helyezést.

## <a name="next-steps"></a>Következő lépések

- Ismerje meg [a bérlők közötti felügyeleti élményt.](../concepts/cross-tenant-management-experience.md)
- [Az ügyfelek megtekintéséhez és](view-manage-customers.md) kezeléséhez a saját **ügyfeleket a** Azure Portal.
- Ismerje meg, hogyan [frissítheti vagy](update-delegation.md) távolíthatja el a [delegálást.](remove-delegation.md)
