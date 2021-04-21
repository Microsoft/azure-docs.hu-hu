---
title: Azure Key Vault tároló másik előfizetésbe való | Microsoft Docs
description: Útmutató a kulcstartó másik előfizetésbe való áthelyezéséhez.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 05/05/2020
ms.author: mbaldwin
ms.openlocfilehash: 1a1cd8c051f9e04c09ef2986805873d8e7fea54e
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107817629"
---
# <a name="moving-an-azure-key-vault-to-another-subscription"></a>Egy Azure-kulcstartó áthelyezése egy másik előfizetésbe

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Áttekintés

> [!IMPORTANT]
> **Ha egy kulcstartót egy másik előfizetésbe költöztet, az a környezetben is meg fog változni.**
> Mielőtt úgy dönt, hogy áthelyezi a kulcstartót egy új előfizetésbe, győződjön meg arról, hogy megértette ennek a változásnak a hatását, és kövesse az ebben a cikkben olvasható útmutatást.
> Ha felügyeltszolgáltatás-identitásokat (MSI) használ, olvassa el az áthelyezés utáni utasításokat a dokumentum végén. 

[Azure Key Vault](overview.md) a rendszer automatikusan ahhoz az előfizetéshez [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) alapértelmezett bérlőazonosítóhoz kötődik, amelyben létre van hozva. Az előfizetéséhez társított bérlőazonosítót ebben az útmutatóban [találja.](../../active-directory/fundamentals/active-directory-how-to-find-tenant.md) A hozzáférési szabályzat minden bejegyzése és szerepkör-hozzárendelése ehhez a bérlőazonosítóhoz is kötődik.  Ha áthelyezi Azure-előfizetését az A bérlőről a B bérlőre, a meglévő kulcstartók nem lesznek elérhetőek a B bérlőben lévő szolgáltatásnév (felhasználók és alkalmazások) számára. A probléma megoldásához a következőt kell megoldania:

* Módosítsa az előfizetésben lévő összes meglévő kulcstartóhoz társított bérlőazonosítót a B bérlőre.
* Törölnie kell minden meglévő hozzáférésiszabályzat-bejegyzést.
* Adjon hozzá a B bérlőhöz társított új hozzáférési szabályzatbejegyzéseket.

További információ a Azure Key Vault és Azure Active Directory:
- [Információk az Azure Key Vaultról](overview.md)
- [Mi az az Azure Active Directory?](../../active-directory/fundamentals/active-directory-whatis.md)
- [A bérlőazonosító megkeresése](../../active-directory/fundamentals/active-directory-how-to-find-tenant.md)

## <a name="limitations"></a>Korlátozások

> [!IMPORTANT]
> **A lemeztitkosításhoz használt kulcstartók nem mozgathatók** Ha a kulcstartót lemeztitkosítással használja egy virtuális géphez, a kulcstartó nem áthelyezhető másik erőforráscsoportba vagy előfizetésbe, amíg a lemeztitkosítás engedélyezve van. A kulcstartó új erőforráscsoportba vagy előfizetésbe való áthelyezéséhez le kell tiltania a lemeztitkosítást. 

Egyes szolgáltatásnév (felhasználók és alkalmazások) egy adott bérlőhöz van kötve. Ha a kulcstartót egy másik bérlő egyik előfizetésébe költözteti, akkor lehet, hogy nem tudja visszaállítani egy adott szolgáltatásnévhez való hozzáférést. Ellenőrizze, hogy minden alapvető szolgáltatásnév létezik-e abban a bérlőben, ahová a kulcstartót átköltözti.

## <a name="prerequisites"></a>Előfeltételek

* [Közreműködői](../../role-based-access-control/built-in-roles.md#contributor) szintű hozzáférés vagy magasabb szintű hozzáférés ahhoz az előfizetéshez, amelyben a kulcstartó található. A szerepköröket a Azure Portal, [az Azure CLI](../../role-based-access-control/role-assignments-cli.md)vagy [a](../../role-based-access-control/role-assignments-portal.md) [PowerShell használatával rendelheti hozzá.](../../role-based-access-control/role-assignments-powershell.md)
* [Közreműködői](../../role-based-access-control/built-in-roles.md#contributor) szintű hozzáférés vagy magasabb szintű hozzáférés ahhoz az előfizetéshez, ahová a kulcstartót át szeretné áthelyezni. A szerepköröket a Azure Portal, [az Azure CLI](../../role-based-access-control/role-assignments-cli.md)vagy [a](../../role-based-access-control/role-assignments-portal.md) [PowerShell használatával rendelheti hozzá.](../../role-based-access-control/role-assignments-powershell.md)
* Egy erőforráscsoport az új előfizetésben. Létrehozhat egyet a [Azure Portal,](../../azure-resource-manager/management/manage-resource-groups-portal.md) [a PowerShell](../../azure-resource-manager/management/manage-resource-groups-powershell.md)vagy az [Azure CLI használatával.](../../azure-resource-manager/management/manage-resource-groups-cli.md)

A meglévő szerepköröket az [Azure Portal,](../../role-based-access-control/role-assignments-list-portal.md) [a PowerShell,](../../role-based-access-control/role-assignments-list-powershell.md) [az Azure CLI](../../role-based-access-control/role-assignments-list-cli.md)vagy a [REST API.](../../role-based-access-control/role-assignments-list-rest.md)


## <a name="moving-a-key-vault-to-a-new-subscription"></a>Kulcstartó átköltöztet egy új előfizetésbe

1. Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.
2. Lépjen a [kulcstartóhoz](overview.md)
3. Kattintson az "Áttekintés" lapra
4. Válassza az "Áthelyezés" gombot
5. A legördülő menüben válassza az "Áthelyezés másik előfizetésre" lehetőséget
6. Válassza ki azt az erőforráscsoportot, ahová a kulcstartót át szeretné áthelyezni
7. Az erőforrások áthelyezésével kapcsolatos figyelmeztetés nyugtázása
8. Válassza az OK gobot.

## <a name="additional-steps-when-subscription-is-in-a-new-tenant"></a>További lépések, ha az előfizetés egy új bérlőben található

Ha a kulcstartót egy új bérlő egyik előfizetésébe költözte, manuálisan kell frissítenie a bérlőazonosítót, és el kell távolítania a régi hozzáférési szabályzatokat és szerepkör-hozzárendeléseket. Az alábbi oktatóanyagokkal ezeket a lépéseket a PowerShellben és az Azure CLI-ban is megtudhatja. Ha PowerShellt használ, előfordulhat, hogy futtatnia kell Clear-AzContext alább dokumentált Clear-AzContext, hogy az aktuálisan kiválasztott hatókörben kívüli erőforrásokat is látható legyen. 

### <a name="update-tenant-id-in-a-key-vault"></a>Bérlőazonosító frissítése egy kulcstartóban

```azurepowershell
Select-AzSubscription -SubscriptionId <your-subscriptionId>                # Select your Azure Subscription
$vaultResourceId = (Get-AzKeyVault -VaultName myvault).ResourceId          # Get your key vault's Resource ID 
$vault = Get-AzResource -ResourceId $vaultResourceId -ExpandProperties     # Get the properties for your key vault
$vault.Properties.TenantId = (Get-AzContext).Tenant.TenantId               # Change the Tenant that your key vault resides in
$vault.Properties.AccessPolicies = @()                                     # Access policies can be updated with real
                                                                           # applications/users/rights so that it does not need to be                             # done after this whole activity. Here we are not setting 
                                                                           # any access policies. 
Set-AzResource -ResourceId $vaultResourceId -Properties $vault.Properties  # Modifies the key vault's properties.

Clear-AzContext                                                            #Clear the context from PowerShell
Connect-AzAccount                                                          #Log in again to confirm you have the correct tenant id
````

```azurecli
az account set -s <your-subscriptionId>                                    # Select your Azure Subscription
tenantId=$(az account show --query tenantId)                               # Get your tenantId
az keyvault update -n myvault --remove Properties.accessPolicies           # Remove the access policies
az keyvault update -n myvault --set Properties.tenantId=$tenantId          # Update the key vault tenantId
```
### <a name="update-access-policies-and-role-assignments"></a>Hozzáférési szabályzatok és szerepkör-hozzárendelések frissítése

> [!NOTE]
> Ha Key Vault Azure [RBAC-engedélymodellt](../../role-based-access-control/overview.md) használ. A Kulcstartó szerepkör-hozzárendeléseket is el kell távolítania. A szerepkör-hozzárendeléseket az [Azure Portal,](../../role-based-access-control/role-assignments-portal.md)az [Azure CLI](../../role-based-access-control/role-assignments-cli.md)vagy a PowerShell használatával [távolíthatja el.](../../role-based-access-control/role-assignments-powershell.md) 

Most, hogy a tároló a megfelelő bérlőazonosítóhoz van társítva, és a régi hozzáférési szabályzat bejegyzései vagy szerepkör-hozzárendelései el vannak távolítva, állítsa be az új hozzáférési szabályzat bejegyzéseit vagy szerepkör-hozzárendeléseit.

A szabályzatok hozzárendelését lásd:
- [Hozzáférési szabályzat hozzárendelése a Portál használatával](assign-access-policy-portal.md)
- [Hozzáférési szabályzat hozzárendelése az Azure CLI használatával](assign-access-policy-cli.md)
- [Hozzáférési szabályzat hozzárendelése a PowerShell használatával](assign-access-policy-powershell.md)

A szerepkör-hozzárendelések hozzáadásáról lásd:
- [Azure-szerepkörök hozzárendelése a Azure Portal](../../role-based-access-control/role-assignments-portal.md)
- [Azure-szerepkörök hozzárendelése az Azure CLI használatával](../../role-based-access-control/role-assignments-cli.md)
- [Azure-szerepkörök hozzárendelése a PowerShell használatával](../../role-based-access-control/role-assignments-powershell.md)


### <a name="update-managed-identities"></a>Felügyelt identitások frissítése

Ha teljes előfizetést ad át, és felügyelt identitást használ az Azure-erőforrásokhoz, akkor azt is frissítenie kell az új Azure Active Directory bérlőre. A felügyelt identitásokkal kapcsolatos további információkért, [a felügyelt identitások áttekintését ismertető témakörben talál.](../../active-directory/managed-identities-azure-resources/overview.md)

Ha felügyelt identitást használ, akkor frissítenie kell az identitást is, mert a régi identitás már nem lesz a megfelelő Azure Active Directory bérlőben. A probléma megoldásához tekintse meg az alábbi dokumentumokat. 

* [Az MSI frissítése](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)
* [Előfizetés átvitele új címtárba](../../role-based-access-control/transfer-subscription.md)

## <a name="next-steps"></a>Következő lépések

- További információ a [kulcsokról, titkos kulcsokról és tanúsítványokról](about-keys-secrets-certificates.md)
- Fogalmi információkért, beleértve a naplók Key Vault értelmezését, lásd: Key Vault [naplózás](logging.md)
- [Key Vault fejlesztői útmutató](../general/developers-guide.md)
- [Azure Key Vault biztonsági funkciók](security-features.md)
- [Tűzfalak Azure Key Vault virtuális hálózatok konfigurálása](network-security.md)