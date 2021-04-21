---
title: Engedélyek megadása az alkalmazásoknak egy Azure-kulcstartó Azure RBAC-kulcstartóval való | Microsoft Docs
description: Megtudhatja, hogyan biztosíthatja a hozzáférést a kulcsokhoz, titkos kulcsokhoz és tanúsítványokhoz az Azure szerepköralapú hozzáférés-vezérlésének használatával.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 04/15/2021
ms.author: mbaldwin
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 9806327c7393c3ba1fdab09acfb5545d6026e5cf
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107818461"
---
# <a name="provide-access-to-key-vault-keys-certificates-and-secrets-with-an-azure-role-based-access-control"></a>Hozzáférést biztosít Key Vault kulcsokhoz, tanúsítványokhoz és titkos kulcsokhoz egy Azure-beli szerepköralapú hozzáférés-vezérléssel

> [!NOTE]
> Key Vault erőforrás-szolgáltató két erőforrástípust **támogat:** tárolókat és felügyelt **HSM-eket.** A jelen cikkben ismertetett hozzáférés-vezérlés csak a **tárolókra vonatkozik.** További információ a felügyelt HSM hozzáférés-vezérlésről: Managed HSM access control (Felügyelt [HSM hozzáférés-vezérlés).](../managed-hsm/access-control.md)

Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) [](../../azure-resource-manager/management/overview.md) egy olyan engedélyezési rendszer, amely Azure Resource Manager, és részletes hozzáférés-vezérlést biztosít az Azure-erőforrásokhoz.

Az Azure RBAC lehetővé teszi a felhasználók számára a kulcsok, titkos kulcsok és tanúsítványok engedélyeinek kezelését. Egyetlen helyen kezelheti az összes kulcstartó összes engedélyét. 

Az Azure RBAC-modell lehetővé teszi engedélyek beállítását különböző hatókörszintek esetében: felügyeleti csoport, előfizetés, erőforráscsoport vagy egyéni erőforrások.  A Kulcstartóhoz való Azure RBAC emellett különálló engedélyeket is biztosít az egyes kulcsokhoz, titkos kulcsokhoz és tanúsítványokhoz

További információ: [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC).](../../role-based-access-control/overview.md)

## <a name="best-practices-for-individual-keys-secrets-and-certificates"></a>Ajánlott eljárások az egyes kulcsokhoz, titkos kulcsokhoz és tanúsítványokhoz

Azt javasoljuk, hogy környezetenként (fejlesztés, éles üzem előtti és éles környezet) alkalmazásonként használjon tárolót.

Az egyéni kulcsokra, titkos kulcsokra és tanúsítványokra vonatkozó engedélyeket csak bizonyos forgatókönyvekhez szabad használni:

-   Többrétegű alkalmazások, amelyek a hozzáférés-vezérlést külön kell választani a rétegek között

-   Egyedi titkos adatok megosztása több alkalmazás között

További információ a Azure Key Vault irányelvekről:

- [Azure Key Vault biztonsági funkciók](security-features.md)
- [Azure Key Vault szolgáltatási korlátok](service-limits.md)

## <a name="azure-built-in-roles-for-key-vault-data-plane-operations"></a>Az Azure beépített szerepkörei Key Vault adatsík-műveletekhez
> [!NOTE]
> `Key Vault Contributor` A szerepkör a felügyeleti síkon a kulcstartók kezeléséhez szükséges műveletekhez szükséges. Nem engedélyezi a kulcsokhoz, titkos kulcsokhoz és tanúsítványokhoz való hozzáférést.

| Beépített szerepkör | Leírás | ID (Azonosító) |
| --- | --- | --- |
| Key Vault rendszergazda| Minden adatsíkműveletet végrehajt egy kulcstartón és a benne lévő összes objektumon, beleértve a tanúsítványokat, kulcsokat és titkos kulcsokat. A Key Vault-erőforrások és a szerepkör-hozzárendelések nem kezelhetők. Csak olyan kulcstartók esetében működik, amelyek az "Azure szerepköralapú hozzáférés-vezérlés" engedélymodellt használják. | 00482a5a-887f-4fb3-b363-3b7fe8e74483 |
| Key Vault tanúsítvány-felelős | Az engedélyek kezelése kivételével minden műveletet végrehajt a kulcstartó tanúsítványán. Csak olyan kulcstartók esetében működik, amelyek az "Azure szerepköralapú hozzáférés-vezérlés" engedélymodellt használják. | a4417e6f-fecd-4de8-b567-7b0420556985 |
| Key Vault kriptográfiai igazgató | Az engedélyek kezelése kivételével bármilyen műveletet végrehajt a kulcstartó kulcsán. Csak olyan kulcstartók esetében működik, amelyek az "Azure szerepköralapú hozzáférés-vezérlés" engedélymodellt használják. | 14b46e9e-c2b7-41b4-b07b-48a6ebf60603 |
| Key Vault titkosítási szolgáltatás titkosítási felhasználója | Beolvassa a kulcsok metaadatait, és burkoló/kicsomagol műveleteket hajt végre. Csak olyan kulcstartók esetében működik, amelyek az "Azure szerepköralapú hozzáférés-vezérlés" engedélymodellt használják. | e147488a-f6f5-4113-8e2d-b22465e65bf6 |
| Key Vault kriptográfiai felhasználó  | Titkosítási műveletek végrehajtása kulcsokkal. Csak olyan kulcstartók esetében működik, amelyek az "Azure szerepköralapú hozzáférés-vezérlés" engedélymodellt használják. | 12338af0-0e69-4776-bea7-57ae8d297424 |
| Key Vault Olvasó | Kulcstartók, tanúsítványok, kulcsok és titkos kulcsok metaadatainak olvasása. Nem olvashatja a bizalmas értékeket, például a titkos adatokat vagy a kulcsanyagokat. Csak olyan kulcstartók esetében működik, amelyek az "Azure szerepköralapú hozzáférés-vezérlés" engedélymodellt használják. | 21090545-7ca7-4776-b22c-e363652d74d2 |
| Key Vault titkos kulcsokért felelős igazgató| Az engedélyek kezelése kivételével bármilyen műveletet végrehajt a kulcstartó titkos kulcsán. Csak olyan kulcstartók esetében működik, amelyek az "Azure szerepköralapú hozzáférés-vezérlés" engedélymodellt használják. | b86a8fe4-44ce-4948-aee5-eccb2c155cd7 |
| Key Vault titkos kulcsok felhasználója | Titkos fájlok tartalmának olvasása. Csak olyan kulcstartók esetében működik, amelyek az "Azure szerepköralapú hozzáférés-vezérlés" engedélymodellt használják. | 4633458b-17de-408a-b874-0445c86b69e6 |

További információ az Azure beépített szerepkör-definícióiról: [Azure beépített szerepkörök.](../../role-based-access-control/built-in-roles.md)

## <a name="using-azure-rbac-secret-key-and-certificate-permissions-with-key-vault"></a>Az Azure RBAC titkos kulcs-, kulcs- és tanúsítványengedélyeinek használata Key Vault

A kulcstartó új Azure RBAC-engedélymodellje a tároló-hozzáférési szabályzat engedélymodellje alternatívájaként szolgál. 

### <a name="prerequisites"></a>Előfeltételek

Szerepkör-hozzárendelések hozzáadásához a következővel kell lennie:

- Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- `Microsoft.Authorization/roleAssignments/write` és `Microsoft.Authorization/roleAssignments/delete` engedélyek, például felhasználói [hozzáférés rendszergazdája vagy](../../role-based-access-control/built-in-roles.md#user-access-administrator) [tulajdonosa](../../role-based-access-control/built-in-roles.md#owner)

### <a name="enable-azure-rbac-permissions-on-key-vault"></a>Azure RBAC-engedélyek engedélyezése a Key Vault

> [!NOTE]
> Az engedélymodell módosításához "Microsoft.Authorization/roleAssignments/write" engedélyre van szükség, amely a [Tulajdonos](../../role-based-access-control/built-in-roles.md#owner) és a Felhasználói hozzáférés rendszergazdája szerepkörök [része.](../../role-based-access-control/built-in-roles.md#user-access-administrator) Az olyan klasszikus előfizetés-rendszergazdai szerepkörök, mint a "Szolgáltatás-rendszergazda" és a "Társadi rendszergazda" nem támogatottak.

1.  Engedélyezze az Azure RBAC-engedélyeket az új kulcstartón:

    ![Azure RBAC-engedélyek engedélyezése – új tároló](../media/rbac/image-1.png)

2.  Azure RBAC-engedélyek engedélyezése meglévő kulcstartón:

    ![Azure RBAC-engedélyek engedélyezése – meglévő tároló](../media/rbac/image-2.png)

> [!IMPORTANT]
> Az Azure RBAC-engedélymodell beállítása érvényteleníti az összes hozzáférési szabályzat engedélyét. Ez kimaradást okozhat, ha nincs hozzárendelve egyenértékű Azure-szerepkör.

### <a name="assign-role"></a>Szerepkör hozzárendelése

> [!Note]
> Javasoljuk, hogy a szkriptek szerepkörneve helyett az egyedi szerepkör-azonosítót használja. Ezért ha átnevez egy szerepkört, a szkriptek továbbra is működni fog. Ebben a dokumentumban a szerepkör neve csak az olvashatóság érdekében használatos.

Szerepkör-hozzárendelés létrehozásához futtassa a következő parancsot:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --scope <scope>
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
#Assign by User Principal Name
New-AzRoleAssignment -RoleDefinitionName <role_name> -SignInName <assignee_upn> -Scope <scope>

#Assign by Service Principal ApplicationId
New-AzRoleAssignment -RoleDefinitionName Reader -ApplicationId <applicationId> -Scope <scope>
```
---

A Azure Portal Az Azure szerepkör-hozzárendelések képernyője minden erőforrás számára elérhető a Hozzáférés-vezérlés (IAM) lapon.

![Szerepkör-hozzárendelés – (IAM) lap](../media/rbac/image-3.png)

### <a name="resource-group-scope-role-assignment"></a>Erőforráscsoport hatókörének szerepkör-hozzárendelése

1.  Ugrás a Key Vault-erőforráscsoportra.
    ![Szerepkör-hozzárendelés – erőforráscsoport](../media/rbac/image-4.png)

2.  Kattintson a Hozzáférés-vezérlés (IAM) \> szerepkör-hozzárendelés hozzáadása \> elemre.

3.  "Key Vault olvasói szerepkör "Key Vault olvasó" létrehozása az aktuális felhasználóhoz

    ![Szerepkör hozzáadása – erőforráscsoport](../media/rbac/image-5.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az role assignment create --role "Key Vault Reader" --assignee {i.e user@microsoft.com} --scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
#Assign by User Principal Name
New-AzRoleAssignment -RoleDefinitionName 'Key Vault Reader' -SignInName {i.e user@microsoft.com} -Scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}

#Assign by Service Principal ApplicationId
New-AzRoleAssignment -RoleDefinitionName 'Key Vault Reader' -ApplicationId {i.e 8ee5237a-816b-4a72-b605-446970e5f156} -Scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}
```
---

A fenti szerepkör-hozzárendelés lehetővé teszi a Kulcstartó-objektumok listába sorolása a Key Vaultban.

### <a name="key-vault-scope-role-assignment"></a>Key Vault hatókör szerepkör-hozzárendelése

1. Ugrás a Key Vault \> (IAM) lapra

2. Kattintson a Szerepkör-hozzárendelés hozzáadása \> gombra Hozzáadás

3. Hozzon létre "Key Vault titkoskulcs-felelős" szerepkört az aktuális felhasználó számára.

    ![Szerepkör-hozzárendelés – kulcstartó](../media/rbac/image-6.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az role assignment create --role "Key Vault Secrets Officer" --assignee {i.e jalichwa@microsoft.com} --scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}/providers/Microsoft.KeyVault/vaults/{key-vault-name}
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
#Assign by User Principal Name
New-AzRoleAssignment -RoleDefinitionName 'Key Vault Secrets Officer' -SignInName {i.e user@microsoft.com} -Scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}/providers/Microsoft.KeyVault/vaults/{key-vault-name}

#Assign by Service Principal ApplicationId
New-AzRoleAssignment -RoleDefinitionName 'Key Vault Secrets Officer' -ApplicationId {i.e 8ee5237a-816b-4a72-b605-446970e5f156} -Scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}/providers/Microsoft.KeyVault/vaults/{key-vault-name}
```
---

A fenti szerepkör-hozzárendelés létrehozása után létrehozhat/frissíthet/törölhet titkos adatokat.

4. Hozzon létre egy új titkos adatokat (Titkos kulcsok \> +Létrehozás/Importálás) a titkos szintű szerepkör-hozzárendelés tesztelésére.

    ![Szerepkör hozzáadása – Kulcstartó](../media/rbac/image-7.png)

### <a name="secret-scope-role-assignment"></a>Titkos hatókör szerepkör-hozzárendelése

1. Nyissa meg a korábban létrehozott titkos kulcsok valamelyikét, és figyelje meg az Áttekintés és hozzáférés-vezérlés (IAM) et 

2. Kattintson a Hozzáférés-vezérlés (IAM) fülre

    ![Szerepkör-hozzárendelés – titkos](../media/rbac/image-8.png)

3. Hozzon létre egy "Key Vault titkoskulcs-felelős" szerepkört az aktuális felhasználó számára, ugyanúgy, mint a fenti Key Vault.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az role assignment create --role "Key Vault Secrets Officer" --assignee {i.e user@microsoft.com} --scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}/providers/Microsoft.KeyVault/vaults/{key-vault-name}/secrets/RBACSecret
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
#Assign by User Principal Name
New-AzRoleAssignment -RoleDefinitionName 'Key Vault Secrets Officer' -SignInName {i.e user@microsoft.com} -Scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}/providers/Microsoft.KeyVault/vaults/{key-vault-name}/secrets/RBACSecret

#Assign by Service Principal ApplicationId
New-AzRoleAssignment -RoleDefinitionName 'Key Vault Secrets Officer' -ApplicationId {i.e 8ee5237a-816b-4a72-b605-446970e5f156} -Scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}/providers/Microsoft.KeyVault/vaults/{key-vault-name}/secrets/RBACSecret
```
---

### <a name="test-and-verify"></a>Tesztelés és ellenőrzés

> [!NOTE]
> A böngészők gyorsítótárazást használnak, és a szerepkör-hozzárendelések eltávolítása után oldalfrissítésre van szükség.<br>
> A szerepkör-hozzárendelések frissítésének engedélyezése több percig

1. Ellenőrizze az új titkos kulcstartói Key Vault nélküli hozzáadását a Key Vault szintjén.

A Key Vault Hozzáférés-vezérlés (IAM) lapján távolítsa el a "titkos Key Vault felelős" szerepkör-hozzárendelést ehhez az erőforráshoz.

![Hozzárendelés eltávolítása – Key Vault](../media/rbac/image-9.png)

Lépjen a korábban létrehozott titkos titokra. Láthatja az összes titkos tulajdonságokkal.

![Titkos elérésű nézet](../media/rbac/image-10.png)

Az új titkos kód létrehozása (Titkos kulcsok \> +Létrehozás/Importálás) az alábbi hibát mutatja:

   ![Új titkos adat létrehozása](../media/rbac/image-11.png)

2.  A titkos gombra való Key Vault "titkos Key Vault" szerepkör nélkül, titkos szintű ellenőrzés.

-   Ugrás a korábban létrehozott titkos Access Control (IAM) lapra, és távolítsa el a "titkos Key Vault felelős" szerepkör-hozzárendelést ehhez az erőforráshoz.

-   Lépjen a korábban létrehozott titkos titokra. Láthatja a titkos tulajdonságokat.

   ![Titkos elérésű nézet hozzáférés nélkül](../media/rbac/image-12.png)

3. Ellenőrizze a titkos kulcsok olvasását olvasói szerepkör nélkül a Key Vault szintjén.

-   A Key Vault erőforráscsoport hozzáférés-vezérlés (IAM) lapján távolítsa el az "Olvasó Key Vault" szerepkör-hozzárendelést.

-   A Key Vault Titkos kulcsok lapra navigálva az alábbi hibaüzenetnek kell mutatnia:

   ![Titkoskód lap – hiba](../media/rbac/image-13.png)

### <a name="creating-custom-roles"></a>Egyéni szerepkörök létrehozása 

[az role definition create parancs](/cli/azure/role/definition#az_role_definition_create)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az role definition create --role-definition '{ \
   "Name": "Backup Keys Operator", \
   "Description": "Perform key backup/restore operations", \
    "Actions": [ 
    ], \
    "DataActions": [ \
        "Microsoft.KeyVault/vaults/keys/read ", \
        "Microsoft.KeyVault/vaults/keys/backup/action", \
         "Microsoft.KeyVault/vaults/keys/restore/action" \
    ], \
    "NotDataActions": [ 
   ], \
    "AssignableScopes": ["/subscriptions/{subscriptionId}"] \
}'
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
$roleDefinition = @"
{ 
   "Name": "Backup Keys Operator", 
   "Description": "Perform key backup/restore operations", 
    "Actions": [ 
    ], 
    "DataActions": [ 
        "Microsoft.KeyVault/vaults/keys/read ", 
        "Microsoft.KeyVault/vaults/keys/backup/action", 
         "Microsoft.KeyVault/vaults/keys/restore/action" 
    ], 
    "NotDataActions": [ 
   ], 
    "AssignableScopes": ["/subscriptions/{subscriptionId}"] 
}
"@

$roleDefinition | Out-File role.json

New-AzRoleDefinition -InputFile role.json
```
---

További információ az egyéni szerepkörök létrehozásáról:

[Egyéni Azure-szerepkörök](../../role-based-access-control/custom-roles.md)

## <a name="known-limits-and-performance"></a>Ismert korlátok és teljesítmény

-   2000 Azure-beli szerepkör-hozzárendelés előfizetésenként

-   Szerepkör-hozzárendelések késése: a jelenlegi várt teljesítmény esetén akár 10 percet (600 másodpercet) is igénybe fog venni a szerepkör-hozzárendelések a szerepkör-hozzárendelések alkalmazása után

## <a name="learn-more"></a>Tudjon meg többet

- [Az Azure RBAC áttekintése](../../role-based-access-control/overview.md)
- [Egyéni szerepkörök – oktatóanyag](../../role-based-access-control/tutorial-custom-role-cli.md)