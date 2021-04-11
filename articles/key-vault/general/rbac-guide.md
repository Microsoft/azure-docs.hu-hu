---
title: Engedélyek megadása az alkalmazásoknak az Azure Key Vault Azure RBAC történő eléréséhez | Microsoft Docs
description: Ismerje meg, hogyan biztosíthat hozzáférést a kulcsokhoz, a titkokhoz és a tanúsítványokhoz az Azure szerepköralapú hozzáférés-vezérlés használatával.
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 8/30/2020
ms.author: mbaldwin
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 216df0d128e0557345db8f82f6010e1ef681593c
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105968781"
---
# <a name="provide-access-to-key-vault-keys-certificates-and-secrets-with-an-azure-role-based-access-control"></a>Hozzáférés biztosítása Key Vault kulcsokhoz, tanúsítványokhoz és titkokhoz egy Azure szerepköralapú hozzáférés-vezérléssel

> [!NOTE]
> Key Vault **erőforrás-szolgáltató** két erőforrástípust támogat: tárolókat és **felügyelt HSM**. A jelen cikkben leírt **hozzáférés-vezérlés** csak a tárolók esetében érvényes. A felügyelt HSM hozzáférés-vezérléséről a [felügyelt HSM hozzáférés-vezérlés](../managed-hsm/access-control.md)című témakörben olvashat bővebben.

Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) egy [Azure Resource Managerra](../../azure-resource-manager/management/overview.md) épülő engedélyezési rendszer, amely az Azure-erőforrások részletes hozzáférés-kezelését teszi lehetővé.

Az Azure RBAC lehetővé teszi a felhasználók számára a kulcsok, titkos kulcsok és tanúsítványok engedélyeinek kezelését. Egy helyet biztosít a minden kulcstartó összes engedélyének kezeléséhez. 

Az Azure RBAC modellje lehetővé teszi a különböző hatóköri szintekre vonatkozó engedélyek beállítását: felügyeleti csoport, előfizetés, erőforráscsoport vagy egyedi erőforrás.  Az Azure RBAC a Key Vault számára is lehetővé teszi, hogy külön engedélyekkel rendelkezzenek az egyes kulcsokra, titkokra és tanúsítványokra vonatkozóan

További információ: [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../../role-based-access-control/overview.md).

## <a name="best-practices-for-individual-keys-secrets-and-certificates"></a>Ajánlott eljárások az egyes kulcsokhoz, titkokhoz és tanúsítványokhoz

Javasoljuk, hogy a tárolót egy alkalmazás/környezet (fejlesztés, üzem előtti és éles üzem) alapján használja.

Az egyes kulcsokat, titkokat és tanúsítványokat csak bizonyos esetekben kell használni:

-   Többrétegű alkalmazások, amelyeknek külön kell elkülöníteniük a hozzáférés-vezérlést a rétegek között

-   Egyéni titok megosztása több alkalmazás között

További információ a Azure Key Vault felügyeleti irányelvekről:

- [Azure Key Vault biztonsági áttekintés](security-overview.md)
- [Azure Key Vault szolgáltatási korlátok](service-limits.md)

## <a name="azure-built-in-roles-for-key-vault-data-plane-operations"></a>Az Azure beépített szerepkörei Key Vault adatsík-műveletekhez
> [!NOTE]
> `Key Vault Contributor` a szerepkör a felügyeleti sík-műveletekhez használható a kulcstartók kezeléséhez. Nem teszi lehetővé a kulcsok, titkos kódok és tanúsítványok elérését.

| Beépített szerepkör | Leírás | ID (Azonosító) |
| --- | --- | --- |
| Key Vault rendszergazda| Minden adatsík művelet végrehajtása a kulcstartón és az abban található összes objektumon, beleértve a tanúsítványokat, a kulcsokat és a titkos kulcsokat is. A Key Vault-erőforrások nem kezelhetők, illetve nem kezelhetők a szerepkör-hozzárendelések. Csak az "Azure szerepköralapú hozzáférés-vezérlés" engedélyezési modellt használó kulcstartók esetében működik. | 00482a5a-887f-4fb3-b363-3b7fe8e74483 |
| Key Vault Certificates Officer | Hajtson végre bármilyen műveletet a Key Vault tanúsítványán, kivéve a kezelési engedélyeket. Csak az "Azure szerepköralapú hozzáférés-vezérlés" engedélyezési modellt használó kulcstartók esetében működik. | a4417e6f-fecd-4de8-b567-7b0420556985 |
| Key Vault titkosítási igazgató | Hajtson végre bármilyen műveletet a kulcstartó kulcsain, kivéve a kezelési engedélyeket. Csak az "Azure szerepköralapú hozzáférés-vezérlés" engedélyezési modellt használó kulcstartók esetében működik. | 14b46e9e-c2b7-41b4-b07b-48a6ebf60603 |
| Titkosítási szolgáltatás titkosítása Key Vault | A kulcsok metaadatainak olvasása, valamint a becsomagolási/kicsomagolási műveletek végrehajtása. Csak az "Azure szerepköralapú hozzáférés-vezérlés" engedélyezési modellt használó kulcstartók esetében működik. | e147488a-f6f5-4113-8e2d-b22465e65bf6 |
| Key Vault kriptográfiai felhasználó  | Titkosítási műveletek végrehajtása kulcsok használatával. Csak az "Azure szerepköralapú hozzáférés-vezérlés" engedélyezési modellt használó kulcstartók esetében működik. | 12338af0-0e69-4776-bea7-57ae8d297424 |
| Key Vault olvasó | A kulcstartók és a hozzá tartozó tanúsítványok, kulcsok és titkos kódok metaadatainak olvasása. Nem lehet olvasni a bizalmas értékeket, például a titkos tartalmat vagy a kulcs anyagát. Csak az "Azure szerepköralapú hozzáférés-vezérlés" engedélyezési modellt használó kulcstartók esetében működik. | 21090545-7ca7-4776-b22c-e363652d74d2 |
| Key Vault Secrets Officer| Hajtson végre bármilyen műveletet a Key Vault titkos kulcsain, kivéve a kezelési engedélyeket. Csak az "Azure szerepköralapú hozzáférés-vezérlés" engedélyezési modellt használó kulcstartók esetében működik. | b86a8fe4-44ce-4948-aee5-eccb2c155cd7 |
| Key Vault Secrets-felhasználó | Titkos tartalom olvasása. Csak az "Azure szerepköralapú hozzáférés-vezérlés" engedélyezési modellt használó kulcstartók esetében működik. | 4633458b-17de-408a-b874-0445c86b69e6 |

Az Azure beépített szerepkör-definíciókkal kapcsolatos további információkért lásd: [Az Azure beépített szerepkörei](../../role-based-access-control/built-in-roles.md).

## <a name="using-azure-rbac-secret-key-and-certificate-permissions-with-key-vault"></a>Az Azure RBAC titkos, kulcs-és tanúsítvány-engedélyeinek használata a Key Vault

A Key Vault új Azure RBAC-engedélyezési modellje alternatívát biztosít a tároló hozzáférési házirendjének engedélyeihez. 

### <a name="prerequisites"></a>Előfeltételek

A szerepkör-hozzárendelések hozzáadásához a következőket kell tennie:

- Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- `Microsoft.Authorization/roleAssignments/write` és `Microsoft.Authorization/roleAssignments/delete` engedélyek, például a [felhasználói hozzáférés rendszergazdája](../../role-based-access-control/built-in-roles.md#user-access-administrator) vagy a [tulajdonos](../../role-based-access-control/built-in-roles.md#owner)

### <a name="enable-azure-rbac-permissions-on-key-vault"></a>Azure RBAC engedélyek engedélyezése Key Vault

> [!NOTE]
> Az engedélyezési modell módosításához a [tulajdonosi](../../role-based-access-control/built-in-roles.md#owner) és a [felhasználói hozzáférés-rendszergazdai](../../role-based-access-control/built-in-roles.md#user-access-administrator) szerepkörök részét képező "Microsoft. Authorization/roleAssignments/Write" engedély szükséges. A klasszikus előfizetés-rendszergazdai szerepkörök, például a "szolgáltatás rendszergazdája" és a "társ-rendszergazda" nem támogatottak.

1.  Azure RBAC engedélyek engedélyezése az új kulcstartón:

    ![Azure RBAC engedélyeinek engedélyezése – új tároló](../media/rbac/image-1.png)

2.  Azure RBAC engedélyek engedélyezése a meglévő kulcstartón:

    ![Azure RBAC engedélyeinek engedélyezése – meglévő tár](../media/rbac/image-2.png)

> [!IMPORTANT]
> Az Azure RBAC engedélyezési modell beállítása érvényteleníti az összes hozzáférési szabályzat engedélyét. Kiesést okozhat, ha az egyenértékű Azure-szerepköröket nem rendeli hozzá.

### <a name="assign-role"></a>Szerepkör hozzárendelése

> [!Note]
> Ajánlott az egyedi szerepkör-azonosítót használni a szkriptek szerepkör neve helyett. Ezért ha egy szerepkört átneveznek, a parancsfájlok továbbra is működni fognak. A jelen dokumentum-szerepkör neve csak olvashatóság érdekében használatos.

A szerepkör-hozzárendelés létrehozásához futtassa a következő parancsot:

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

A Azure Portal az Azure szerepkör-hozzárendelések képernyő a hozzáférés-vezérlés (IAM) lapon lévő összes erőforráshoz elérhető.

![Szerepkör-hozzárendelés – (IAM) lap](../media/rbac/image-3.png)

### <a name="resource-group-scope-role-assignment"></a>Erőforráscsoport-hatókör szerepkör-hozzárendelése

1.  Nyissa meg a Key Vault erőforráscsoportot.
    ![Szerepkör-hozzárendelés – erőforráscsoport](../media/rbac/image-4.png)

2.  Kattintson a hozzáférés-vezérlés (IAM) \> szerepkör-hozzárendelés hozzáadása elemre. \>

3.  Key Vault olvasó szerepkör létrehozása az aktuális felhasználó számára "Key Vault Reader"

    ![Szerepkör-erőforráscsoport hozzáadása](../media/rbac/image-5.png)

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

A fenti szerepkör-hozzárendelés lehetővé teszi a Key Vault-objektumok listázását a Key vaultban.

### <a name="key-vault-scope-role-assignment"></a>Key Vault hatókör-szerepkör hozzárendelése

1. Ugrás a Key Vault \> hozzáférés-vezérlés (iam) lapra

2. Kattintson a Hozzáadás-szerepkör-hozzárendelés hozzáadása elemre. \>

3. A Key Secrets Officer "Key Vault Secrets Officer" szerepkör létrehozása az aktuális felhasználó számára.

    ![Szerepkör-hozzárendelés – Key Vault](../media/rbac/image-6.png)

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

Miután létrehozta a fenti szerepkör-hozzárendelést, létrehozhat/frissíthet/törölhet titkokat.

4. Hozzon létre új titkot (titkok \> + Létrehozás/importálás) a titkos szint szerepkör-hozzárendelésének teszteléséhez.

    ![Szerepkör-Key Vault hozzáadása](../media/rbac/image-7.png)

### <a name="secret-scope-role-assignment"></a>Titkos hatókör szerepkör-hozzárendelése

1. Nyisson meg egy korábban létrehozott titkot, figyelje az Áttekintés és hozzáférés-vezérlés (IAM) 

2. Kattintson a hozzáférés-vezérlés (IAM) fülre

    ![Szerepkör-hozzárendelés – titok](../media/rbac/image-8.png)

3. Az aktuális felhasználóhoz tartozó "Key Vault Secrets Officer" nevű kulcsfontosságú titkokat hozhatja létre, ugyanúgy, mint a Key Vault.

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
> A böngészők a szerepkör-hozzárendelések eltávolítása után gyorsítótárazást és az oldal frissítését igénylik.<br>
> A szerepkör-hozzárendelések frissítésének engedélyezése több percet is igénybe vehet

1. Új titok hozzáadásának ellenőrzése a Key Vault szintjén a "Key Vault Secrets Officer" szerepkör nélkül.

Lépjen a Key Vault hozzáférés-vezérlés (IAM) lapra, és távolítsa el a "Key Vault Secrets Officer" szerepkör-hozzárendelést ehhez az erőforráshoz.

![Hozzárendelés eltávolítása – Key Vault](../media/rbac/image-9.png)

Navigáljon a korábban létrehozott titkos kulcshoz. Az összes titkos tulajdonságot láthatja.

![Titkos nézet hozzáféréssel](../media/rbac/image-10.png)

Új titok létrehozása (titkok \> + Létrehozás/importálás) az alábbi hibaüzenet jelenik meg:

   ![Új titkos kód létrehozása](../media/rbac/image-11.png)

2.  A titkos szerkesztés ellenőrzése a titkos szint "Key Vault Secret Officer" szerepköre nélkül.

-   Lépjen a korábban létrehozott titkos Access Control (IAM) lapra, és távolítsa el a "Key Vault Secrets Officer" szerepkör-hozzárendelést ehhez az erőforráshoz.

-   Navigáljon a korábban létrehozott titkos kulcshoz. Láthatja a titkos kulcs tulajdonságait.

   ![Titkos nézet hozzáférés nélkül](../media/rbac/image-12.png)

3. Titkos kulcsok ellenőrzése a Key Vault szintjén olvasó szerepkör nélkül.

-   Nyissa meg a Key Vault erőforráscsoport hozzáférés-vezérlés (IAM) lapját, és távolítsa el a "Key Vault olvasó" szerepkör-hozzárendelést.

-   A Key Vault Secrets (titkos kulcsok) lapján navigáljon az alábbi hibaüzenet jelenik meg:

   ![Titkos kód lap – hiba](../media/rbac/image-13.png)

### <a name="creating-custom-roles"></a>Egyéni szerepkörök létrehozása 

[az szerepkör-definíció létrehozása parancs](/cli/azure/role/definition#az-role-definition-create)

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

Az egyéni szerepkörök létrehozásáról további információt a következő témakörben talál:

[Egyéni Azure-szerepkörök](../../role-based-access-control/custom-roles.md)

## <a name="known-limits-and-performance"></a>Ismert korlátok és teljesítmény

-   2000 Azure szerepkör-hozzárendelések/előfizetés

-   Szerepkör-hozzárendelések késése: az aktuálisan várt teljesítménynél akár 10 600 percet is igénybe vehet, miután a szerepkör-hozzárendelések módosultak az alkalmazáshoz.

## <a name="learn-more"></a>Tudjon meg többet

- [Az Azure RBAC áttekintése](../../role-based-access-control/overview.md)
- [Egyéni szerepkörök oktatóanyaga](../../role-based-access-control/tutorial-custom-role-cli.md)