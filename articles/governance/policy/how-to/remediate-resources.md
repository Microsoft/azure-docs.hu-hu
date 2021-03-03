---
title: Nem megfelelő erőforrások szervizelése
description: Ez az útmutató végigvezeti az olyan erőforrások szervizelésén, amelyek nem felelnek meg a Azure Policy szabályzatának.
ms.date: 02/17/2021
ms.topic: how-to
ms.openlocfilehash: e567bedf48393a36215c1ac3f3d11f467ae7badd
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101742228"
---
# <a name="remediate-non-compliant-resources-with-azure-policy"></a>Nem megfelelő erőforrások szervizelése Azure Policy

Azok az erőforrások, amelyek nem felelnek meg a **deployIfNotExists** vagy a házirend- **módosítási** szabályzatoknak, **szervizeléssel** kompatibilis állapotba helyezhetők. A szervizelést arra utasítja Azure Policy, hogy futtassa a **deployIfNotExists** hatást vagy a hozzárendelt házirend **módosítási műveleteit** a meglévő erőforrásokon és előfizetéseken, függetlenül attól, hogy a hozzárendelés egy felügyeleti csoportra, egy előfizetésre, egy erőforráscsoport vagy egy adott erőforrásra vonatkozik-e. Ez a cikk azokat a lépéseket mutatja be, amelyekkel megismerheti és elvégezheti a szervizelést Azure Policy.

## <a name="how-remediation-security-works"></a>A Szervizelési biztonság működése

Ha Azure Policy futtatja a sablont a **deployIfNotExists** házirend-definíciójában, akkor azt [felügyelt identitás](../../../active-directory/managed-identities-azure-resources/overview.md)használatával végzi el.
Azure Policy létrehoz egy felügyelt identitást az egyes hozzárendelésekhez, de a felügyelt identitást megadó szerepkörökkel kapcsolatos részletekkel kell rendelkeznie. Ha a felügyelt identitásból hiányzik egy szerepkör, a házirend vagy egy kezdeményezés hozzárendelése során hiba jelenik meg. A portál használatakor Azure Policy automatikusan megadja a felügyelt identitást a felsorolt szerepköröknek a hozzárendelés indításakor. Az SDK használatakor a szerepköröket manuálisan kell megadni a felügyelt identitásnak. A felügyelt identitás _helye_ nem befolyásolja a műveletét Azure Policy.

:::image type="content" source="../media/remediate-resources/missing-role.png" alt-text="Képernyőkép egy deployIfNotExists szabályzatról, amelyből hiányzik egy meghatározott engedély a felügyelt identitáson." border="false":::

> [!IMPORTANT]
> A következő helyzetekben a hozzárendelés felügyelt identitásának [manuálisan kell megadnia a hozzáférést](#manually-configure-the-managed-identity) , vagy a Szervizelési telepítés sikertelen lesz:
>
> - Ha a hozzárendelést SDK-val hozták létre
> - Ha egy **deployIfNotExists** vagy **módosítás** által módosított erőforrás kívül esik a szabályzat-hozzárendelés hatókörén
> - Ha a sablon a szabályzat-hozzárendelés hatókörén kívüli erőforrásokhoz fér hozzá a tulajdonságokhoz

## <a name="configure-policy-definition"></a>Házirend-definíció konfigurálása

Első lépésként meg kell határozni azokat a szerepköröket, amelyek **deployIfNotExists** és **módosítása** szükséges a házirend-definícióban a tartalmazott sablon tartalmának sikeres telepítéséhez. A **részletek** tulajdonságban adjon hozzá egy **roleDefinitionIds** tulajdonságot. Ez a tulajdonság olyan karakterláncok tömbje, amelyek megfelelnek a környezet szerepköreinek. Teljes példaként tekintse meg a [deployIfNotExists példát](../concepts/effects.md#deployifnotexists-example) vagy a [példák módosításait](../concepts/effects.md#modify-examples).

```json
"details": {
    ...
    "roleDefinitionIds": [
        "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
        "/providers/Microsoft.Authorization/roleDefinitions/{builtinroleGUID}"
    ]
}
```

A **roleDefinitionIds** tulajdonság a teljes erőforrás-azonosítót használja, és nem veszi igénybe a szerepkör rövid **roleName** . A "közreműködő" szerepkör AZONOSÍTÓjának lekéréséhez a környezetben használja a következő kódot:

```azurecli-interactive
az role definition list --name 'Contributor'
```

## <a name="manually-configure-the-managed-identity"></a>Felügyelt identitás manuális konfigurálása

Ha a portál használatával hoz létre hozzárendelést, Azure Policy mindkét esetben létrehoz egy felügyelt identitást, és megadja a **roleDefinitionIds**-ben definiált szerepköröket. A következő feltételekben manuálisan kell végrehajtani a felügyelt identitás létrehozásához és az engedélyek hozzárendeléséhez szükséges lépéseket:

- Az SDK használata közben (például Azure PowerShell)
- Ha a sablon módosítja a hozzárendelési Hatókörön kívüli erőforrást
- Ha a sablon a hozzárendelési Hatókörön kívüli erőforrást olvas be

### <a name="create-managed-identity-with-powershell"></a>Felügyelt identitás létrehozása a PowerShell-lel

Ha felügyelt identitást szeretne létrehozni a szabályzat hozzárendelése során, a **helyet** definiálni és **AssignIdentity** kell használni. A következő példa beolvassa a beépített házirend definícióját, amely az **SQL db transzparens adattitkosítását telepíti**, beállítja a célként megadott erőforráscsoportot, majd létrehozza a hozzárendelést.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the built-in "Deploy SQL DB transparent data encryption" policy definition
$policyDef = Get-AzPolicyDefinition -Id '/providers/Microsoft.Authorization/policyDefinitions/86a912f6-9a06-4e26-b447-11b16ba8659f'

# Get the reference to the resource group
$resourceGroup = Get-AzResourceGroup -Name 'MyResourceGroup'

# Create the assignment using the -Location and -AssignIdentity properties
$assignment = New-AzPolicyAssignment -Name 'sqlDbTDE' -DisplayName 'Deploy SQL DB transparent data encryption' -Scope $resourceGroup.ResourceId -PolicyDefinition $policyDef -Location 'westus' -AssignIdentity
```

A `$assignment` változó mostantól tartalmazza a felügyelt identitás elsődleges azonosítóját, valamint a szabályzat-hozzárendelés létrehozásakor visszaadott szabványos értékeket. Ez a használatával érhető el `$assignment.Identity.PrincipalId` .

### <a name="grant-defined-roles-with-powershell"></a>Definiált szerepkörök megadása a PowerShell-lel

Az új felügyelt identitásnak a Azure Active Directoryon keresztül kell végrehajtania a replikálást, mielőtt a szükséges szerepköröket megadhatja. A replikáció befejeződése után a következő példa megismétli a RoleDefinitionIds tartozó házirend-definíciót, `$policyDef` és a [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) használatával adja meg a szerepkörök új felügyelt identitását. 

```azurepowershell-interactive
# Use the $policyDef to get to the roleDefinitionIds array
$roleDefinitionIds = $policyDef.Properties.policyRule.then.details.roleDefinitionIds

if ($roleDefinitionIds.Count -gt 0)
{
    $roleDefinitionIds | ForEach-Object {
        $roleDefId = $_.Split("/") | Select-Object -Last 1
        New-AzRoleAssignment -Scope $resourceGroup.ResourceId -ObjectId $assignment.Identity.PrincipalId -RoleDefinitionId $roleDefId
    }
}
```

### <a name="grant-defined-roles-through-portal"></a>Definiált szerepkörök megadása a portálon keresztül

A hozzárendelések felügyelt identitását kétféleképpen lehet megadnia a portál használatával, a **hozzáférés-vezérlés (iam)** használatával, vagy a házirend vagy a kezdeményezés hozzárendelésének szerkesztésével, és a **Mentés gombra** kattintva.

Ha szerepkört szeretne hozzáadni a hozzárendelés felügyelt identitásához, kövesse az alábbi lépéseket:

1. Indítsa el a Azure Policy szolgáltatást a Azure Portal a **minden szolgáltatás** lehetőség kiválasztásával, majd a **szabályzat** keresésével és kiválasztásával.

1. Válassza ki a **Hozzárendelések** elemet az Azure Policy oldal bal oldalán.

1. Keresse meg a felügyelt identitással rendelkező hozzárendelést, és válassza ki a nevet.

1. Keresse meg a **hozzárendelés-azonosító** tulajdonságot a szerkesztés oldalon. A hozzárendelés azonosítója a következőhöz hasonló lesz:

   ```output
   /subscriptions/{subscriptionId}/resourceGroups/PolicyTarget/providers/Microsoft.Authorization/policyAssignments/2802056bfc094dfb95d4d7a5
   ```

   A felügyelt identitás neve a hozzárendelési erőforrás AZONOSÍTÓjának utolsó része, amely `2802056bfc094dfb95d4d7a5` ebben a példában szerepel. Másolja a hozzárendelési erőforrás-azonosító ezen részét.

1. Navigáljon az erőforráshoz vagy az erőforrások szülő-tárolóhoz (erőforráscsoport, előfizetés, felügyeleti csoport), amelyhez manuálisan hozzá kell adni a szerepkör-definíciót.

1. Válassza a **hozzáférés-vezérlés (iam)** hivatkozást az erőforrások lapon, majd válassza a **+ szerepkör-hozzárendelés hozzáadása** elemet a hozzáférés-vezérlési oldal tetején.

1. Válassza ki a megfelelő szerepkört, amely megfelel egy **roleDefinitionIds** a házirend-definícióban.
   Az "Azure AD-felhasználó, csoport vagy alkalmazás" alapértelmezett értékének beállításához hagyja a **hozzáférés-hozzárendelést** . A **kiválasztás** mezőben illessze be vagy írja be a korábban megjelenő hozzárendelési erőforrás-azonosító részét. Ha a keresés befejeződött, jelölje ki az azonos nevű objektumot az azonosító kiválasztásához, majd válassza a **Mentés** lehetőséget.

## <a name="create-a-remediation-task"></a>Szervizelési feladat létrehozása

### <a name="create-a-remediation-task-through-portal"></a>Szervizelési feladat létrehozása a portálon keresztül

Az értékelés során a szabályzat-hozzárendelés **deployIfNotExists** vagy a hatások **módosítása** határozza meg, hogy nem megfelelő erőforrások vagy előfizetések találhatók-e. Ha nem megfelelő erőforrások vagy előfizetések találhatók, a részleteket a **szervizelés** lapon találja. A nem megfelelő erőforrásokkal vagy előfizetésekkel rendelkező szabályzatok listája mellett egy **szervizelési feladat** is aktiválható.
Ez a beállítás azt eredményezi, hogy a központi telepítést a **deployIfNotExists** sablonból vagy a **módosítási** műveletekkel hozza létre.

**Szervizelési feladat** létrehozásához kövesse az alábbi lépéseket:

1. Indítsa el a Azure Policy szolgáltatást a Azure Portal a **minden szolgáltatás** lehetőség kiválasztásával, majd a **szabályzat** keresésével és kiválasztásával.

   :::image type="content" source="../media/remediate-resources/search-policy.png" alt-text="Képernyőkép a szabályzatok kereséséről az összes szolgáltatásban." border="false":::

1. Válassza a **szervizelés** elemet a Azure Policy lap bal oldalán.

   :::image type="content" source="../media/remediate-resources/select-remediation.png" alt-text="Képernyőfelvétel a szabályzat lap szervizelési csomópontról." border="false":::

1. A nem megfelelő erőforrásokkal rendelkező szabályzat-hozzárendelések minden **deployIfNotExists** és **módosítása** a lap és az adattábla **szervizeléséhez** szükséges. Válasszon olyan házirendet, amelynek erőforrásai nem megfelelőek. Megnyílik az **új szervizelési feladat** lap.

   > [!NOTE]
   > A **szervizelési feladat** lap megnyitásának másik módja, ha megkeresi és kiválasztja a szabályzatot a **megfelelőség** lapon, majd kiválasztja a **szervizelési feladat létrehozása** gombot.

1. Az **új szervizelési feladat** lapon a **hatóköri** ellipszisek használatával szűrheti a kijavítani kívánt erőforrásokat, hogy kiválassza a gyermek erőforrásokat, amelyekről a szabályzat hozzá van rendelve (beleértve az egyes erőforrás-objektumokat is). Emellett az erőforrások további szűréséhez használja a **helyszínek** legördülő listát. Csak a táblázatban felsorolt erőforrások lesznek szervizelve.

   :::image type="content" source="../media/remediate-resources/select-resources.png" alt-text="Képernyőkép a Szervizelési csomópontról és a szervizeléshez szükséges erőforrások rácsáról." border="false":::

1. Indítsa el a Szervizelési feladatot az erőforrások szűrése után a **szervizelés** lehetőség kiválasztásával. A szabályzat megfelelősége lap megnyílik a **szervizelési feladatok** lapra, hogy megjelenjen a feladatok állapota. A Szervizelési feladat által létrehozott központi telepítések azonnal megkezdődik.

   :::image type="content" source="../media/remediate-resources/task-progress.png" alt-text="Képernyőfelvétel a Szervizelési feladatok lapról, valamint a meglévő szervizelési feladatok előrehaladásáról." border="false":::

1. Válassza ki a **szervizelési feladatot** a szabályzat megfelelősége lapon a folyamat részleteinek megismeréséhez. A feladathoz használt szűrés megjelenik a szervizelt erőforrások listájával együtt.

1. A **szervizelési feladat** lapon kattintson a jobb gombbal egy erőforrásra a Szervizelési feladat központi telepítésének vagy erőforrásának megtekintéséhez. A sor végén válassza a **kapcsolódó események** lehetőséget a részletek, például a hibaüzenetek megjelenítéséhez.

   :::image type="content" source="../media/remediate-resources/resource-task-context-menu.png" alt-text="Képernyőkép a Szervizelési feladat lap erőforrásának helyi menüjéről." border="false":::

A **szervizelési feladattal** üzembe helyezett erőforrások hozzáadódnak a szabályzat megfelelősége lap **telepített erőforrások** lapjához.

### <a name="create-a-remediation-task-through-azure-cli"></a>Szervizelési feladat létrehozása az Azure CLI-n keresztül

Ha az Azure CLI-vel szeretne **szervizelési feladatot** létrehozni, használja a `az policy remediation` parancsokat. Cserélje le az `{subscriptionId}` előfizetése azonosítóját és a `{myAssignmentId}` **deployIfNotExists** , vagy **módosítsa** a szabályzat-hozzárendelés azonosítóját.

```azurecli-interactive
# Login first with az login if not using Cloud Shell

# Create a remediation for a specific assignment
az policy remediation create --name myRemediation --policy-assignment '/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/{myAssignmentId}'
```

További szervizelési parancsokért és példákért tekintse meg az az [Policy szervizelési](/cli/azure/policy/remediation) parancsokat.

### <a name="create-a-remediation-task-through-azure-powershell"></a>Szervizelési feladat létrehozása Azure PowerShell használatával

Ha Azure PowerShell használatával szeretne **szervizelési feladatot** létrehozni, használja a `Start-AzPolicyRemediation` parancsokat. Cserélje le az `{subscriptionId}` előfizetése azonosítóját és a `{myAssignmentId}` **deployIfNotExists** , vagy **módosítsa** a szabályzat-hozzárendelés azonosítóját.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Create a remediation for a specific assignment
Start-AzPolicyRemediation -Name 'myRemedation' -PolicyAssignmentId '/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/{myAssignmentId}'
```

Más szervizelési parancsmagok és példák esetében tekintse meg az az [. PolicyInsights](/powershell/module/az.policyinsights/#policy_insights) modult.

### <a name="create-a-remediation-task-during-policy-assignment-in-the-azure-portal"></a>Szervizelési feladat létrehozása a házirend-hozzárendelés során a Azure Portalban

A Szervizelési feladatok zökkenőmentes létrehozásához a Azure Portal a házirend-hozzárendelés során. Ha a hozzárendelni kívánt házirend-definíció **deployIfNotExists** vagy **módosítási** hatás, a **szervizelés** lapon lévő varázsló egy _szervizelési feladat létrehozása_ lehetőséget kínál. Ha ez a beállítás be van jelölve, a rendszer a szabályzat-hozzárendeléssel megegyező időpontban létrehoz egy szervizelési feladatot.

## <a name="next-steps"></a>Következő lépések

- Tekintse át a példákat [Azure Policy mintákon](../samples/index.md).
- Tekintse meg az [Azure szabályzatdefiníciók struktúrája](../concepts/definition-structure.md) szakaszt.
- A [Szabályzatok hatásainak ismertetése](../concepts/effects.md).
- Megtudhatja, hogyan [hozhat létre programozott módon házirendeket](programmatically-create.md).
- Ismerje meg, hogyan [kérheti le a megfelelőségi információkat](get-compliance-data.md).
- Tekintse át, hogy a felügyeleti csoport hogyan [rendezi az erőforrásokat az Azure felügyeleti csoportjaival](../../management-groups/overview.md).
