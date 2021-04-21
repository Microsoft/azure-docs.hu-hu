---
title: Hozzáférési jogosultságszint emelése az összes Azure-előfizetés és felügyeleti csoport kezeléséhez
description: Ez a cikk azt ismerteti, hogyan lehet megemelni egy globális rendszergazda jogosultságszint-megemelt hozzáférését a Azure Active Directory előfizetések és felügyeleti csoportok kezeléséhez a Azure Portal vagy REST API.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 06/09/2020
ms.author: rolyon
ms.openlocfilehash: 37d50c030a2b426cb3e9af57afb899b7fab68388
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778470"
---
# <a name="elevate-access-to-manage-all-azure-subscriptions-and-management-groups"></a>Hozzáférési jogosultságszint emelése az összes Azure-előfizetés és felügyeleti csoport kezeléséhez

Az Azure AD Azure Active Directory rendszergazdájaként előfordulhat, hogy nem rendelkezik hozzáféréssel a címtárban található összes előfizetéshez és felügyeleti csoporthoz. Ez a cikk bemutatja, hogyan emelheti a hozzáférési jogosultságszintet az összes előfizetésre és felügyeleti csoportra.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="why-would-you-need-to-elevate-your-access"></a>Miért kell megemelni a hozzáférési jogosultságszintet?

Ha Ön globális rendszergazda, előfordulhat, hogy a következő műveleteket szeretné megtenni:

- Azure-előfizetéshez vagy felügyeleti csoporthoz való hozzáférés visszaszerzése, ha egy felhasználó nem rendelkezik hozzáféréssel
- Azure-előfizetéshez vagy felügyeleti csoporthoz való hozzáférés engedélyezése más felhasználónak vagy saját részre
- Egy szervezet összes Azure-előfizetésének vagy felügyeleti csoportának a see all Azure subscriptions or management groups (Az összes Azure-előfizetés vagy felügyeleti csoport)
- Automatizálási alkalmazás (például számlázási vagy naplózási alkalmazás) hozzáférésének engedélyezése az összes Azure-előfizetéshez vagy felügyeleti csoporthoz

## <a name="how-does-elevated-access-work"></a>Hogyan működik az emelt szintű hozzáférés?

Az Azure AD és az Azure-erőforrások egymástól függetlenek. Ez azt jelenti, hogy az Azure AD-beli szerepkör-hozzárendelések nem kiosztással kiosztást az Azure-erőforrásokhoz, az Azure-beli szerepkör-hozzárendelések pedig nem kiosztást az Azure AD-hez. Ha azonban Ön globális rendszergazda [az](../active-directory/roles/permissions-reference.md#global-administrator) Azure AD-ban, saját maga is hozzárendelhet hozzáférést a címtárban található összes Azure-előfizetéshez és felügyeleti csoporthoz. Akkor használja ezt a képességet, ha nem fér hozzá az Azure-előfizetés erőforrásaihoz, például virtuális gépekhez vagy tárfiókokhoz, és globális rendszergazdai jogosultságát szeretné használni az erőforrásokhoz való hozzáféréshez.

Ha megemeli a hozzáférési jogosultságszintet, a rendszer a Felhasználói hozzáférés rendszergazdája szerepkört rendeli hozzá az Azure-ban gyökérhatókörben ( [](built-in-roles.md#user-access-administrator) `/` ).Ez lehetővé teszi az összes erőforrás megtekintését és hozzáférés hozzárendelését a címtár bármely előfizetésében vagy felügyeleti csoportjában. A felhasználói hozzáférés-rendszergazdai szerepkör-hozzárendelések a Azure PowerShell, az Azure CLI vagy a REST API.

Ezt az emelt szintű hozzáférést akkor távolítsa el, ha végrehajtotta a gyökérhatókörben szükséges módosításokat.

![Hozzáférési szint emelése](./media/elevate-access-global-admin/elevate-access.png)

## <a name="azure-portal"></a>Azure Portal

### <a name="elevate-access-for-a-global-administrator"></a>Globális rendszergazdai jogosultságszint-emelkedés

Kövesse az alábbi lépéseket egy globális rendszergazda hozzáférési jogosultságszint-ének a Azure Portal.

1. Jelentkezzen be az Azure Portal felügyeleti [Azure Active Directory](https://portal.azure.com) [felügyeleti központba](https://aad.portal.azure.com) globális rendszergazdaként.

    Ha a globális rendszergazdai szerepkört Azure AD Privileged Identity Management, [aktiválja a globális rendszergazdai szerepkör-hozzárendelést.](../active-directory/privileged-identity-management/pim-how-to-activate-role.md)

1. Nyissa meg **Azure Active Directory.**

1. A **Kezelés** területen válassza a **Tulajdonságok** elemet.

   ![A tulajdonságok tulajdonságainak Azure Active Directory – képernyőkép](./media/elevate-access-global-admin/azure-active-directory-properties.png)

1. Az **Azure-erőforrásokhoz való hozzáférés kezelése alatt** állítsa a kapcsolót Igen **beállításra.**

   ![Azure-erőforrások hozzáférés-kezelése – képernyőkép](./media/elevate-access-global-admin/aad-properties-global-admin-setting.png)

   Ha a váltógombot Igenre állítva van beállítva, a rendszer a Felhasználói hozzáférés rendszergazdája szerepkört rendeli hozzá az Azure RBAC-hez gyökérhatókörben (/). Ez engedélyezi a szerepkörök hozzárendelését az Ehhez az Azure AD-címtárhoz társított összes Azure-előfizetésben és felügyeleti csoportban. Ez a váltógomb csak olyan felhasználók számára érhető el, akik globális rendszergazdai szerepkörhöz vannak hozzárendelve az Azure AD-ban.

   Ha a váltógombot Nem, akkor az Azure RBAC felhasználói hozzáférés-rendszergazdai szerepköre el lesz távolítva a felhasználói fiókból. Már nem rendelhet szerepköröket az ehhez az Azure AD-címtárhoz társított összes Azure-előfizetéshez és felügyeleti csoporthoz. Csak azok az Azure-előfizetések és felügyeleti csoportok nézete és kezelése, amelyekhez hozzáférést kapott.

    > [!NOTE]
    > Ha a következőt [használja Privileged Identity Management](../active-directory/privileged-identity-management/pim-configure.md)a szerepkör-hozzárendelés inaktiválásával nem módosítja az **Azure-erőforrások** hozzáférés-kezelési szolgáltatását a Nem **értékre.** A legkevesebb emelt szintű hozzáférés fenntartása érdekében javasoljuk,  hogy a szerepkör-hozzárendelés inaktiválása előtt állítsa a Nem kapcsolót a beállításra.
    
1. Kattintson **a Mentés** gombra a beállítás mentéshez.

   Ez a beállítás nem globális tulajdonság, és csak az aktuálisan bejelentkezett felhasználóra vonatkozik. A globális rendszergazdai szerepkör összes tagjához nem lehet megemelni a jogosultságszintet.

1. Jelentkezzen ki, majd jelentkezzen be újra a hozzáférés frissítéshez.

    Most már hozzáféréssel kell rendelkezik a címtárban található összes előfizetéshez és felügyeleti csoporthoz. A Hozzáférés-vezérlés (IAM) panel megtekintésekor figyelje meg, hogy a felhasználói hozzáférés rendszergazdája szerepkört rendelte hozzá a gyökér szintű hatókörben.

   ![Előfizetési szerepkör-hozzárendelések gyökérhatókörrel – képernyőkép](./media/elevate-access-global-admin/iam-root.png)

1. A szükséges módosításokat emelt szintű hozzáféréssel kell eltennünk.

    További információ a szerepkörök hozzárendelésről: [Azure-szerepkörök hozzárendelése a Azure Portal.](role-assignments-portal.md) Ha erőforrás-kezelőt Privileged Identity Management azure-erőforrások [felderítése](../active-directory/privileged-identity-management/pim-resource-roles-discover-resources.md) az Azure-erőforrás-szerepkörök kezeléséhez vagy [hozzárendeléséhez.](../active-directory/privileged-identity-management/pim-resource-roles-assign-roles.md)

1. Az emelt szintű hozzáférés eltávolításához hajtsa végre a következő szakaszban található lépéseket.

### <a name="remove-elevated-access"></a>Emelt szintű hozzáférés eltávolítása

A Felhasználói hozzáférés-rendszergazda szerepkör-hozzárendelés gyökérhatókörben () való eltávolításához `/` kövesse az alábbi lépéseket.

1. Jelentkezzen be ugyanazként a felhasználóval, aki a hozzáférési jogosultságszint megemeltét használta.

1. A navigációs listában kattintson **a** Azure Active Directory majd a Tulajdonságok **elemre.**

1. Állítsa az **Azure-erőforrások hozzáférés-kezelési beállítását** a Nem **beállításra.** Mivel ez egy felhasználónkénti beállítás, ugyanazként a felhasználóval kell bejelentkeznie, mint a hozzáférési jogosultságszint megemelésében.

    Ha megpróbálja eltávolítani a Felhasználói hozzáférés-rendszergazda szerepkör-hozzárendelést a Hozzáférés-vezérlés (IAM) panelen, a következő üzenet jelenik meg. A szerepkör-hozzárendelés eltávolításához állítsa vissza a  váltógombot a Nem, vagy használja Azure PowerShell, az Azure CLI vagy a REST API.

    ![Szerepkör-hozzárendelések eltávolítása gyökérhatókörrel](./media/elevate-access-global-admin/iam-root-remove.png)

1. Jelentkezzen ki globális rendszergazdaként.

    Ha a globális rendszergazdai szerepkört Privileged Identity Management, inaktiválja a globális rendszergazdai szerepkör-hozzárendelést.

    > [!NOTE]
    > Ha a következőt [használja: , Privileged Identity Management](../active-directory/privileged-identity-management/pim-configure.md)szerepkör-hozzárendelés inaktiválásával nem módosítja az **Azure-erőforrások** hozzáférés-kezelési szolgáltatását a Nem **értékre.** A legkevesebb emelt szintű hozzáférés fenntartása érdekében javasoljuk,  hogy a szerepkör-hozzárendelés inaktiválása előtt állítsa a Nem kapcsolót.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

### <a name="list-role-assignment-at-root-scope-"></a>Szerepkör-hozzárendelés felsorolása gyökérhatókörben (/)

A felhasználói hozzáférés-rendszergazda szerepkör-hozzárendelésének felsorolásához használja a `/` [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) parancsot a gyökérhatókörű () felhasználóhoz.

```azurepowershell
Get-AzRoleAssignment | where {$_.RoleDefinitionName -eq "User Access Administrator" `
  -and $_.SignInName -eq "<username@example.com>" -and $_.Scope -eq "/"}
```

```Example
RoleAssignmentId   : /providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111
Scope              : /
DisplayName        : username
SignInName         : username@example.com
RoleDefinitionName : User Access Administrator
RoleDefinitionId   : 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9
ObjectId           : 22222222-2222-2222-2222-222222222222
ObjectType         : User
CanDelegate        : False
```

### <a name="remove-elevated-access"></a>Emelt szintű hozzáférés eltávolítása

A felhasználói hozzáférés-rendszergazda szerepkör-hozzárendelésének eltávolításához kövesse az alábbi lépéseket saját maga vagy egy másik, gyökérhatókörű felhasználó `/` () számára.

1. Jelentkezzen be olyan felhasználóként, aki eltávolíthatja az emelt szintű hozzáférést. Ez lehet ugyanaz a felhasználó, aki a hozzáférési jogosultságszint megemelt vagy egy másik, emelt szintű hozzáféréssel rendelkező globális rendszergazda a gyökérhatókörben.

1. A [Remove-AzRoleAssignment paranccsal](/powershell/module/az.resources/remove-azroleassignment) távolítsa el a Felhasználói hozzáférés rendszergazdája szerepkör-hozzárendelést.

    ```azurepowershell
    Remove-AzRoleAssignment -SignInName <username@example.com> `
      -RoleDefinitionName "User Access Administrator" -Scope "/"
    ```

## <a name="azure-cli"></a>Azure CLI

### <a name="elevate-access-for-a-global-administrator"></a>Globális rendszergazda jogosultságszint-ének megemelkedő jogosultságszintje

Az alábbi alapvető lépésekkel emelheti a globális rendszergazdai jogosultságszintet az Azure CLI használatával.

1. Az [az rest paranccsal](/cli/azure/reference-index#az_rest) hívja meg a végpontot, amely a Felhasználói hozzáférés rendszergazdája szerepkört biztosítja `elevateAccess` a gyökérhatókörben ( `/` ).

    ```azurecli
    az rest --method post --url "/providers/Microsoft.Authorization/elevateAccess?api-version=2016-07-01"
    ```

1. A szükséges módosításokat emelt szintű hozzáféréssel kell eltennünk.

    További információ a szerepkörök hozzárendelésről: [Azure-szerepkörök hozzárendelése az Azure CLI használatával.](role-assignments-cli.md)

1. Hajtsa végre egy későbbi szakaszban a lépéseket az emelt szintű hozzáférés eltávolításához.

### <a name="list-role-assignment-at-root-scope-"></a>Szerepkör-hozzárendelések felsorolása gyökérhatókörben (/)

A felhasználói hozzáférés-rendszergazda szerepkör-hozzárendelésének felsorolásához használja az az role assignment list parancsot a gyökérhatókörű `/` () [felhasználóhoz.](/cli/azure/role/assignment#az_role_assignment_list)

```azurecli
az role assignment list --role "User Access Administrator" --scope "/"
```

```Example
[
  {
    "canDelegate": null,
    "id": "/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111",
    "name": "11111111-1111-1111-1111-111111111111",
    "principalId": "22222222-2222-2222-2222-222222222222",
    "principalName": "username@example.com",
    "principalType": "User",
    "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
    "roleDefinitionName": "User Access Administrator",
    "scope": "/",
    "type": "Microsoft.Authorization/roleAssignments"
  }
]

```

### <a name="remove-elevated-access"></a>Emelt szintű hozzáférés eltávolítása

A felhasználói hozzáférés-rendszergazda szerepkör-hozzárendelésének eltávolításához kövesse az alábbi lépéseket saját maga vagy egy másik, gyökérhatókörű felhasználó `/` () számára.

1. Jelentkezzen be olyan felhasználóként, aki eltávolíthatja az emelt szintű hozzáférést. Ez lehet ugyanaz a felhasználó, aki a hozzáférési jogosultságszint megemelt vagy egy másik, emelt szintű hozzáféréssel rendelkező globális rendszergazda a gyökérhatókörben.

1. Az [az role assignment delete paranccsal](/cli/azure/role/assignment#az_role_assignment_delete) távolítsa el a Felhasználói hozzáférés rendszergazdája szerepkör-hozzárendelést.

    ```azurecli
    az role assignment delete --assignee username@example.com --role "User Access Administrator" --scope "/"
    ```

## <a name="rest-api"></a>REST API

### <a name="elevate-access-for-a-global-administrator"></a>Globális rendszergazda jogosultságszint-ének megemelkedő jogosultságszintje

Az alábbi alapvető lépésekkel emelheti a globális rendszergazdai jogosultságszintet a REST API.

1. A REST használatával hívja meg a parancsot, amely a Felhasználói hozzáférés rendszergazdája szerepkört `elevateAccess` biztosítja a gyökérhatókörben ( `/` ).

   ```http
   POST https://management.azure.com/providers/Microsoft.Authorization/elevateAccess?api-version=2016-07-01
   ```

1. A szükséges módosításokat emelt szintű hozzáféréssel kell eltennünk.

    További információ a szerepkörök hozzárendelésről: [Azure-szerepkörök hozzárendelése a REST API.](role-assignments-rest.md)

1. Hajtsa végre egy későbbi szakaszban az emelt szintű hozzáférés eltávolításához szükséges lépéseket.

### <a name="list-role-assignments-at-root-scope-"></a>Szerepkör-hozzárendelések felsorolása gyökérhatókörben (/)

A felhasználók összes szerepkör-hozzárendelését a gyökérhatókörben () listába `/` sorolhatja.

- A [GET roleAssignments](/rest/api/authorization/roleassignments/listforscope) hívása, ahol annak a felhasználónak az objektumazonosítója, akinek a szerepkör-hozzárendelését `{objectIdOfUser}` le szeretné hívni.

   ```http
   GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectIdOfUser}'
   ```

### <a name="list-deny-assignments-at-root-scope-"></a>Megtagadás-hozzárendelések felsorolása gyökérhatókörben (/)

A felhasználók összes megtagadási hozzárendelését a gyökérhatókörben () listába `/` sorolhatja.

- A GET denyAssignments hívása, ahol annak a felhasználónak az objektumazonosítója, akinek a megtagadási hozzárendelését `{objectIdOfUser}` le szeretné kapni.

   ```http
   GET https://management.azure.com/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter=gdprExportPrincipalId+eq+'{objectIdOfUser}'
   ```

### <a name="remove-elevated-access"></a>Emelt szintű hozzáférés eltávolítása

A hívatáskor szerepkör-hozzárendelést hoz létre saját maga számára, így a jogosultságok visszavonásához el kell távolítania a felhasználói hozzáférés-rendszergazda szerepkör-hozzárendelést saját maga számára a gyökérhatókörben `elevateAccess` ( `/` ).

1. Hívja [meg a GET roleDefinitions](/rest/api/authorization/roledefinitions/get) parancsot, ahol a Felhasználói hozzáférés rendszergazdája érték megegyezik a Felhasználói hozzáférés rendszergazdája szerepkör névazonosítójának `roleName` meghatározásához.

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter=roleName+eq+'User Access Administrator'
    ```

    ```json
    {
      "value": [
        {
          "properties": {
      "roleName": "User Access Administrator",
      "type": "BuiltInRole",
      "description": "Lets you manage user access to Azure resources.",
      "assignableScopes": [
        "/"
      ],
      "permissions": [
        {
          "actions": [
            "*/read",
            "Microsoft.Authorization/*",
            "Microsoft.Support/*"
          ],
          "notActions": []
        }
      ],
      "createdOn": "0001-01-01T08:00:00.0000000Z",
      "updatedOn": "2016-05-31T23:14:04.6964687Z",
      "createdBy": null,
      "updatedBy": null
          },
          "id": "/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
          "type": "Microsoft.Authorization/roleDefinitions",
          "name": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9"
        }
      ],
      "nextLink": null
    }
    ```

    Mentse az azonosítót a `name` paraméterből, ebben az esetben a `18d7d88d-d35e-4fb5-a5c3-7773c20a72d9` következőből: .

1. A címtár-rendszergazda szerepkör-hozzárendelését a címtár hatókörében is fel kell sorolni. Sorolja fel a jogosultságszint-kiosztást hívó címtár-rendszergazda címtárhatókörében `principalId` található összes hozzárendelést. Ez felsorolja az objectid könyvtárában található összes hozzárendelést.

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectid}'
    ```
        
    >[!NOTE] 
    >A címtár-rendszergazdák nem kaphatnak sok hozzárendelést, ha az előző lekérdezés túl sok hozzárendelést ad vissza, akkor az összes hozzárendelést lekérdezheti csak a címtár hatókörének szintjén, majd szűrheti az eredményeket: `GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=atScope()`
            
1. Az előző hívások a szerepkör-hozzárendelések listáját ják vissza. Keresse meg azt a szerepkör-hozzárendelést, ahol a hatókör található, és a végződik az 1. lépésben talált szerepkörnév-azonosítóval, és egyezik a címtár-rendszergazda `"/"` `roleDefinitionId` `principalId` objectId azonosítójával. 
    
    Szerepkör-hozzárendelési minta:
    
    ```json
    {
      "value": [
        {
          "properties": {
            "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
            "principalId": "{objectID}",
            "scope": "/",
            "createdOn": "2016-08-17T19:21:16.3422480Z",
            "updatedOn": "2016-08-17T19:21:16.3422480Z",
            "createdBy": "22222222-2222-2222-2222-222222222222",
            "updatedBy": "22222222-2222-2222-2222-222222222222"
          },
          "id": "/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111",
          "type": "Microsoft.Authorization/roleAssignments",
          "name": "11111111-1111-1111-1111-111111111111"
        }
      ],
      "nextLink": null
    }
    ```
    
    Ezúttal is mentse az azonosítót a paraméterből, amely ebben az esetben `name` a 1111111-1111-1111-1111-1111111111111111.

1. Végül távolítsa el a által hozzáadott hozzárendelést a szerepkör-hozzárendelés `elevateAccess` azonosítójával:

    ```http
    DELETE https://management.azure.com/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111?api-version=2015-07-01
    ```

## <a name="next-steps"></a>Következő lépések

- [A különböző szerepkörök ismertetése](rbac-and-directory-admin-roles.md)
- [Azure-szerepkörök hozzárendelése a REST API](role-assignments-rest.md)
