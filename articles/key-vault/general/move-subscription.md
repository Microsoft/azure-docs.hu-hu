---
title: A tár másik előfizetésre való áthelyezése Azure Key Vault | Microsoft Docs
description: Útmutató a kulcstartó egy másik előfizetéshez való áthelyezéséhez.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 05/05/2020
ms.author: mbaldwin
Customer intent: As a key vault administrator, I want to move my vault to another subscription.
ms.openlocfilehash: a84627b2b426385d21ad72b85780db86e7dd5ee3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101096081"
---
# <a name="moving-an-azure-key-vault-to-another-subscription"></a>Egy Azure-kulcstartó áthelyezése egy másik előfizetésbe

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Áttekintés

> [!IMPORTANT]
> **Ha egy kulcstartót másik előfizetésre helyez át, a rendszer a környezetét is megváltoztatja.**
> Ügyeljen rá, hogy megértse a változás hatását, és figyelmesen kövesse a cikk útmutatását, mielőtt a Key Vault új előfizetésre való áthelyezését döntene.
> Ha felügyelt szolgáltatásbeli identitásokat (MSI-t) használ, olvassa el a dokumentum végén található áthelyezés utáni utasításokat. 

A [Azure Key Vault](overview.md) automatikusan az alapértelmezett [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) bérlői azonosítóhoz van kötve ahhoz az előfizetéshez, amelyben létrehozták. Az előfizetéshez társított bérlői azonosítót az [útmutató](../../active-directory/fundamentals/active-directory-how-to-find-tenant.md)követésével érheti el. Az összes hozzáférési házirend bejegyzése és szerepkör-hozzárendelése ehhez a bérlői AZONOSÍTÓhoz is kötődik.  Ha áthelyezi az Azure-előfizetését az A bérlőtől a B bérlőhöz, a meglévő kulcstartók elérhetetlenné válnak a B bérlőhöz tartozó egyszerű szolgáltatásnév (felhasználók és alkalmazások) számára. A probléma megoldásához a következőket kell tennie:

* Módosítsa az előfizetésben lévő összes meglévő kulcstartóhoz tartozó bérlői azonosítót a B bérlőre.
* Törölnie kell minden meglévő hozzáférésiszabályzat-bejegyzést.
* Adja hozzá a B bérlőhöz társított új hozzáférési szabályzatok bejegyzéseit.

További információ a Azure Key Vault és a Azure Active Directoryról:
- [Információk az Azure Key Vaultról](overview.md)
- [Mi az Azure Active Directory?](../../active-directory/fundamentals/active-directory-whatis.md)
- [A bérlőazonosító megkeresése](../../active-directory/fundamentals/active-directory-how-to-find-tenant.md)

## <a name="limitations"></a>Korlátozások

> [!IMPORTANT]
> **A lemez titkosításához használt kulcstartók nem helyezhetők át** Ha a Key vaultot a virtuális gép lemezes titkosításával használja, a kulcstároló nem helyezhető át másik erőforráscsoporthoz vagy előfizetésbe, amíg a lemez titkosítása engedélyezve van. A Key Vault új erőforráscsoporthoz vagy előfizetésbe való áthelyezése előtt le kell tiltania a lemez titkosítását. 

Egyes egyszerű szolgáltatások (felhasználók és alkalmazások) egy adott bérlőhöz vannak kötve. Ha áthelyezi a kulcstartót egy másik bérlőn lévő előfizetésbe, akkor előfordulhat, hogy nem fogja tudni visszaállítani az adott egyszerű szolgáltatáshoz való hozzáférést. Győződjön meg arról, hogy az összes alapvető szolgáltatásnév létezik abban a bérlőben, ahová a kulcstartót helyezi át.

## <a name="prerequisites"></a>Előfeltételek

* [Közreműködői](../../role-based-access-control/built-in-roles.md#contributor) szintű hozzáférés vagy magasabb a jelenlegi előfizetéshez, ahol a kulcstartó létezik. Szerepkört a [Azure Portal](../../role-based-access-control/role-assignments-portal.md), az [Azure CLI](../../role-based-access-control/role-assignments-cli.md)vagy a [PowerShell](../../role-based-access-control/role-assignments-powershell.md)használatával rendelhet hozzá.
* [Közreműködői](../../role-based-access-control/built-in-roles.md#contributor) szintű hozzáférés vagy magasabb ahhoz az előfizetéshez, ahová át szeretné helyezni a kulcstartót. Szerepkört a [Azure Portal](../../role-based-access-control/role-assignments-portal.md), az [Azure CLI](../../role-based-access-control/role-assignments-cli.md)vagy a [PowerShell](../../role-based-access-control/role-assignments-powershell.md)használatával rendelhet hozzá.
* Egy erőforráscsoport az új előfizetésben. A [Azure Portal](../../azure-resource-manager/management/manage-resource-groups-portal.md), a [PowerShell](../../azure-resource-manager/management/manage-resource-groups-powershell.md)vagy az [Azure CLI](../../azure-resource-manager/management/manage-resource-groups-cli.md)használatával hozhat létre egyet.

A meglévő szerepköröket a [Azure Portal](../../role-based-access-control/role-assignments-list-portal.md), a [PowerShell](../../role-based-access-control/role-assignments-list-powershell.md), az [Azure CLI](../../role-based-access-control/role-assignments-list-cli.md)vagy a [REST API](../../role-based-access-control/role-assignments-list-rest.md)használatával lehet megtekinteni.


## <a name="moving-a-key-vault-to-a-new-subscription"></a>Kulcstartó áthelyezése új előfizetésre

1. Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.
2. Navigáljon a [kulcstartóhoz](overview.md)
3. Kattintson az "áttekintés" fülre
4. Válassza az "áthelyezés" gombot
5. Válassza a "áthelyezés másik előfizetésre" lehetőséget a legördülő listából.
6. Válassza ki azt az erőforráscsoportot, amelyben át szeretné helyezni a kulcstartót
7. Az erőforrások áthelyezésével kapcsolatos figyelmeztetés nyugtázása
8. Válassza az OK gobot.

## <a name="additional-steps-when-subscription-is-in-a-new-tenant"></a>További lépések az előfizetés új bérlőben való létrehozásakor

Ha áthelyezte a kulcstartót egy új bérlő egyik előfizetéséhez, manuálisan kell frissítenie a bérlő AZONOSÍTÓját, és el kell távolítania a régi hozzáférési házirendeket és a szerepkör-hozzárendeléseket. Ezek a lépések a PowerShell és az Azure CLI-ben című témakörben találhatók. Ha a PowerShellt használja, előfordulhat, hogy az alábbi dokumentált Clear-AzContext parancsot kell futtatnia, hogy az aktuálisan kiválasztott hatókörön kívül is megjelenjenek az erőforrások. 

### <a name="update-tenant-id-in-a-key-vault"></a>A bérlő AZONOSÍTÓjának frissítése a Key vaultban

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
> Ha Key Vault az [Azure RBAC](../../role-based-access-control/overview.md) engedély modelljét használja. A Key Vault szerepkör-hozzárendeléseket is el kell távolítania. A szerepkör-hozzárendeléseket az [Azure Portal](../../role-based-access-control/role-assignments-portal.md), az [Azure CLI](../../role-based-access-control/role-assignments-cli.md)vagy a [PowerShell](../../role-based-access-control/role-assignments-powershell.md)használatával távolíthatja el. 

Most, hogy a tároló hozzá van rendelve a megfelelő bérlői AZONOSÍTÓhoz és a régi hozzáférési házirend bejegyzéseihez, vagy a szerepkör-hozzárendelések el lettek távolítva, új hozzáférési szabályzat-bejegyzéseket vagy szerepkör-hozzárendeléseket

A házirendek hozzárendelésével kapcsolatban lásd:
- [Hozzáférési szabályzat kiosztása a portál használatával](assign-access-policy-portal.md)
- [Hozzáférési szabályzat kiosztása az Azure CLI-vel](assign-access-policy-cli.md)
- [Hozzáférési szabályzat kiosztása a PowerShell használatával](assign-access-policy-powershell.md)

A szerepkör-hozzárendelések hozzáadásával kapcsolatban lásd:
- [Azure-szerepkörök kiosztása a Azure Portal használatával](../../role-based-access-control/role-assignments-portal.md)
- [Azure-szerepkörök kiosztása az Azure CLI-vel](../../role-based-access-control/role-assignments-cli.md)
- [Azure-szerepkörök kiosztása a PowerShell használatával](../../role-based-access-control/role-assignments-powershell.md)


### <a name="update-managed-identities"></a>Felügyelt identitások frissítése

Ha teljes előfizetést továbbít, és felügyelt identitást használ az Azure-erőforrásokhoz, frissítenie kell azt az új Azure Active Directory bérlőre is. A felügyelt identitásokkal kapcsolatos további információkért [tekintse át a felügyelt identitások áttekintése](../../active-directory/managed-identities-azure-resources/overview.md)című témakört.

Ha felügyelt identitást használ, az identitást is frissítenie kell, mert a régi identitás többé nem lesz a megfelelő Azure Active Directory bérlőn. A probléma megoldásához tekintse meg a következő dokumentumokat. 

* [MSI frissítése](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)
* [Előfizetés átszállítása új könyvtárba](../../role-based-access-control/transfer-subscription.md)

## <a name="next-steps"></a>Következő lépések

- További információ a [kulcsokról, a titkokról és a tanúsítványokról](about-keys-secrets-certificates.md)
- Elméleti információk, beleértve a Key Vault naplók értelmezését: [Key Vault naplózás](logging.md)
- [Key Vault fejlesztői útmutató](../general/developers-guide.md)
- [A Key Vault biztonságossá tétele](secure-your-key-vault.md)
- [Azure Key Vault tűzfalak és virtuális hálózatok konfigurálása](network-security.md)