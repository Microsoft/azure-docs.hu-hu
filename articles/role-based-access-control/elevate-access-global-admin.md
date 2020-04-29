---
title: Hozzáférési jogosultságszint emelése az összes Azure-előfizetés és felügyeleti csoport kezeléséhez
description: Ismerteti, hogyan lehet a globális rendszergazda hozzáférését a Azure Portal vagy REST API használatával a Azure Active Directory összes előfizetésének és felügyeleti csoportjának kezeléséhez.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: bagovind
ms.assetid: b547c5a5-2da2-4372-9938-481cb962d2d6
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/17/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 18490ec7c3cfde33cef186b753e2adc809f854c6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81641365"
---
# <a name="elevate-access-to-manage-all-azure-subscriptions-and-management-groups"></a>Hozzáférési jogosultságszint emelése az összes Azure-előfizetés és felügyeleti csoport kezeléséhez

Azure Active Directory (Azure AD) globális rendszergazdájaként előfordulhat, hogy nem fér hozzá a címtár összes előfizetéséhez és felügyeleti csoportjához. Ez a cikk bemutatja, hogyan emelheti ki az összes előfizetéshez és felügyeleti csoporthoz való hozzáférést.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="why-would-you-need-to-elevate-your-access"></a>Miért kell megemelni a hozzáférést?

Ha Ön globális rendszergazda, előfordulhat, hogy a következő műveletek elvégzésére van szükség:

- Azure-előfizetéshez vagy felügyeleti csoporthoz való hozzáférés visszanyerése, ha egy felhasználó elveszítette a hozzáférést
- Azure-előfizetéshez vagy felügyeleti csoporthoz való hozzáférés engedélyezése más felhasználónak vagy saját részre
- A szervezet összes Azure-előfizetésének vagy felügyeleti csoportjának megjelenítése
- Az Azure-előfizetések és-felügyeleti csoportok elérésének engedélyezése az Automation-alkalmazások számára (például számlázási vagy naplózási alkalmazás)

## <a name="how-does-elevated-access-work"></a>Hogyan működik a emelt szintű hozzáférés?

Az Azure AD és az Azure-erőforrások egymástól függetlenül biztonságosak. Az Azure AD szerepkör-hozzárendelések nem biztosítanak hozzáférést az Azure-erőforrásokhoz, és az Azure-szerepkör-hozzárendelések nem biztosítanak hozzáférést az Azure AD-hoz. Ha azonban Ön [globális rendszergazda](../active-directory/users-groups-roles/directory-assign-admin-roles.md#company-administrator-permissions) az Azure ad-ben, saját maga is kioszthatja az összes Azure-előfizetést és felügyeleti csoportot a címtárában. Akkor használja ezt a funkciót, ha nem rendelkezik hozzáféréssel az Azure-előfizetési erőforrásokhoz, például a virtuális gépekhez vagy a Storage-fiókokhoz, és a globális rendszergazdai jogosultságot szeretné használni az erőforrásokhoz való hozzáféréshez.

Ha emeli a hozzáférést, a [felhasználói hozzáférés rendszergazdai](built-in-roles.md#user-access-administrator) szerepköre lesz hozzárendelve az Azure-ban a root scope (`/`) elemnél.Ez lehetővé teszi az összes erőforrás megtekintését és a hozzáférés hozzárendelését bármely előfizetésben vagy felügyeleti csoportban a címtárban. A felhasználói hozzáférés rendszergazdai szerepkör-hozzárendelései a Azure PowerShell, az Azure CLI vagy a REST API használatával távolíthatók el.

Ezt a emelt szintű hozzáférést csak akkor távolíthatja el, ha elvégezte a szükséges módosításokat a gyökérszintű hatókörben.

![Hozzáférési szint emelése](./media/elevate-access-global-admin/elevate-access.png)

## <a name="azure-portal"></a>Azure Portal

### <a name="elevate-access-for-a-global-administrator"></a>Globális rendszergazda hozzáférésének megemelése

Kövesse az alábbi lépéseket a globális rendszergazda hozzáférésének a Azure Portal használatával történő jogosultságszint-emeléséhez.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) vagy a [Azure Active Directory felügyeleti központba](https://aad.portal.azure.com) globális rendszergazdaként.

1. Nyissa meg **Azure Active Directory**.

1. A **kezelés**területen válassza a **Tulajdonságok**lehetőséget.

   ![Azure Active Directory tulajdonságok tulajdonságainak kiválasztása – képernyőfelvétel](./media/elevate-access-global-admin/azure-active-directory-properties.png)

1. Az **Azure-erőforrások hozzáférés-kezelés**területén állítsa a kapcsolót **Igen**értékre.

   ![Hozzáférés-kezelés az Azure-erőforrásokhoz – képernyőfelvétel](./media/elevate-access-global-admin/aad-properties-global-admin-setting.png)

   Ha a váltás az **Igen**értékre van állítva, a rendszer a felhasználói hozzáférés rendszergazdai szerepkörét az Azure RBAC a root scope (/) elemnél rendeli hozzá. Ez lehetővé teszi, hogy az ehhez az Azure AD-címtárhoz társított összes Azure-előfizetéshez és felügyeleti csoporthoz rendeljen szerepköröket. Ez a váltógomb csak a globális rendszergazdai szerepkörrel rendelkező felhasználók számára érhető el az Azure AD-ben.

   Ha a **nem**értékre állítja a kapcsolót, a rendszer eltávolítja a felhasználói fiókból az Azure RBAC felhasználói hozzáférés rendszergazdai szerepkörét. Az ehhez az Azure AD-címtárhoz társított összes Azure-előfizetéshez és felügyeleti csoporthoz már nem lehet szerepköröket hozzárendelni. Csak azokat az Azure-előfizetéseket és felügyeleti csoportokat tekintheti meg és kezelheti, amelyekhez hozzáférést kapott.

    > [!NOTE]
    > Ha [Azure ad Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-configure.md)t használ, a szerepkör-hozzárendelés inaktiválása nem változtatja meg ezt a kapcsolót a **nem**értékre. A legalacsonyabb jogosultsági szintű hozzáférés fenntartásához azt javasoljuk, hogy a szerepkör-hozzárendelés inaktiválása előtt állítsa be ezt a kapcsolót a **nem** értékre.
    
1. A beállítás mentéséhez kattintson a **Save (Mentés** ) gombra.

   Ez a beállítás nem globális tulajdonság, és csak az aktuálisan bejelentkezett felhasználóra vonatkozik. A globális rendszergazdai szerepkör összes tagjához nem lehet hozzáférni.

1. Jelentkezzen ki, majd jelentkezzen be újra, és frissítse a hozzáférését.

    Most már hozzáféréssel kell rendelkeznie a címtár összes előfizetéséhez és felügyeleti csoportjához. Amikor megtekinti a hozzáférés-vezérlés (IAM) panelt, megfigyelheti, hogy a felhasználói hozzáférés rendszergazdai szerepkört a gyökérszintű hatókörben rendelte hozzá.

   ![Előfizetési szerepkör-hozzárendelések a gyökérszintű hatókörrel – képernyőfelvétel](./media/elevate-access-global-admin/iam-root.png)

1. Végezze el a szükséges módosításokat a emelt szintű hozzáféréshez.

    A szerepkörök hozzárendelésével kapcsolatos további információkért lásd: [hozzáférés kezelése a RBAC és a Azure Portal használatával](role-assignments-portal.md). Azure AD Privileged Identity Management (PIM) használata esetén olvassa el a következő témakört: [Azure-erőforrások kezelése a PIM-ban](../active-directory/privileged-identity-management/pim-resource-roles-discover-resources.md) , illetve [Azure-erőforrás-szerepkörök kiosztása a PIM-ben](../active-directory/privileged-identity-management/pim-resource-roles-assign-roles.md).

### <a name="remove-elevated-access"></a>Emelt szintű hozzáférés eltávolítása

Az alábbi lépéseket követve távolíthatja el a felhasználói hozzáférés rendszergazdai`/`szerepkörének hozzárendelését a root scope () alkalmazásban.

1. Jelentkezzen be ugyanazzal a felhasználóval, aki a hozzáférés-kiterjesztést használta.

1. A navigációs listában kattintson a **Azure Active Directory** elemre, majd a **Tulajdonságok**elemre.

1. Az **Azure-erőforrások hozzáférés-kezelésének** beállítása a **nem**értékre vált vissza. Mivel ez egy felhasználónkénti beállítás, a hozzáférés megemeléséhez használt felhasználóként kell bejelentkeznie.

    Ha megpróbálja eltávolítani a felhasználói hozzáférés rendszergazdai szerepkörének hozzárendelését a hozzáférés-vezérlés (IAM) ablaktáblán, a következő üzenet jelenik meg. A szerepkör-hozzárendelés eltávolításához állítsa vissza a visszaváltást a **nem** értékre, vagy használja a Azure PowerShell, az Azure CLI vagy a REST API.

    ![Szerepkör-hozzárendelések eltávolítása a gyökérszintű hatókörrel](./media/elevate-access-global-admin/iam-root-remove.png)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

### <a name="list-role-assignment-at-root-scope-"></a>Szerepkör-hozzárendelés listázása a gyökérszintű hatókörben (/)

A root scope (`/`) felhasználóhoz tartozó felhasználó-hozzáférési rendszergazdai szerepkör hozzárendelésének listázásához használja a [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) parancsot.

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

Az alábbi lépéseket követve távolíthatja el a felhasználói hozzáférés rendszergazdai szerepkör-hozzárendelését önmaga`/`vagy egy másik felhasználó számára a gyökérszintű hatókörben ().

1. Jelentkezzen be olyan felhasználóként, aki el tudja távolítani a emelt szintű hozzáférést. Ez lehet ugyanaz a felhasználó, aki a hozzáférés vagy egy másik globális rendszergazda számára emelt szintű hozzáféréssel rendelkezik a gyökérszintű hatókörben.

1. A [Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment) parancs használatával távolítsa el a felhasználói hozzáférés rendszergazdai szerepkörének hozzárendelését.

    ```azurepowershell
    Remove-AzRoleAssignment -SignInName <username@example.com> `
      -RoleDefinitionName "User Access Administrator" -Scope "/"
    ```

## <a name="azure-cli"></a>Azure CLI

### <a name="list-role-assignment-at-root-scope-"></a>Szerepkör-hozzárendelés listázása a gyökérszintű hatókörben (/)

Az az [szerepkör-hozzárendelési lista](/cli/azure/role/assignment#az-role-assignment-list) parancs használatával listázhatja a felhasználó hozzáférési rendszergazdai`/`szerepkörének hozzárendelését a root scope () elemnél.

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

Az alábbi lépéseket követve távolíthatja el a felhasználói hozzáférés rendszergazdai szerepkör-hozzárendelését önmaga`/`vagy egy másik felhasználó számára a gyökérszintű hatókörben ().

1. Jelentkezzen be olyan felhasználóként, aki el tudja távolítani a emelt szintű hozzáférést. Ez lehet ugyanaz a felhasználó, aki a hozzáférés vagy egy másik globális rendszergazda számára emelt szintű hozzáféréssel rendelkezik a gyökérszintű hatókörben.

1. Az az [szerepkör-hozzárendelés törlése](/cli/azure/role/assignment#az-role-assignment-delete) paranccsal távolítsa el a felhasználói hozzáférés rendszergazdai szerepkör-hozzárendelését.

    ```azurecli
    az role assignment delete --assignee username@example.com --role "User Access Administrator" --scope "/"
    ```

## <a name="rest-api"></a>REST API

### <a name="elevate-access-for-a-global-administrator"></a>Globális rendszergazda hozzáférésének megemelése

A következő alapvető lépésekkel emelheti ki a globális rendszergazda hozzáférését a REST API használatával.

1. A REST és a `elevateAccess`Call használatával a felhasználói hozzáférés rendszergazdai szerepkört a root scope (`/`) segítségével érheti el.

   ```http
   POST https://management.azure.com/providers/Microsoft.Authorization/elevateAccess?api-version=2016-07-01
   ```

1. Hozzon létre egy [szerepkör-hozzárendelést](/rest/api/authorization/roleassignments) , amely bármely hatókörhöz társítható. Az alábbi példa a (z) {roleDefinitionID} szerepkör a root scope (`/`) szolgáltatásban való hozzárendelésének tulajdonságait mutatja be:

   ```json
   { 
     "properties": {
       "roleDefinitionId": "providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionID}",
       "principalId": "{objectID}",
       "scope": "/"
     },
     "id": "providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111",
     "type": "Microsoft.Authorization/roleAssignments",
     "name": "11111111-1111-1111-1111-111111111111"
   }
   ```

1. A felhasználói hozzáférés rendszergazdája a gyökérszintű hatókör (`/`) szerepkör-hozzárendeléseit is eltávolíthatja.

1. Távolítsa el a felhasználói hozzáférés rendszergazdai jogosultságait, amíg újra nem szükségesek.

### <a name="list-role-assignments-at-root-scope-"></a>Szerepkör-hozzárendelések listázása a gyökérszintű hatókörben (/)

Egy felhasználó összes szerepkör-hozzárendelését kilistázhatja a root scope (`/`) használatával.

- Hívja le a [Get roleAssignments](/rest/api/authorization/roleassignments/listforscope) -t, ahol a a lekérdezni kívánt szerepkör-hozzárendeléseket tartalmazó `{objectIdOfUser}` felhasználó objektumazonosító.

   ```http
   GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectIdOfUser}'
   ```

### <a name="list-deny-assignments-at-root-scope-"></a>Megtagadási hozzárendelések listázása a gyökérszintű hatókörben (/)

Egy felhasználó összes megtagadási hozzárendelését listázhatja a root scope (`/`) használatával.

- Hívja le a GET `{objectIdOfUser}` denyAssignments, ahol a a lekérdezni kívánt megtagadási hozzárendeléseket kérő felhasználó objektumazonosító.

   ```http
   GET https://management.azure.com/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter=gdprExportPrincipalId+eq+'{objectIdOfUser}'
   ```

### <a name="remove-elevated-access"></a>Emelt szintű hozzáférés eltávolítása

A hívásakor `elevateAccess`létre kell hoznia egy szerepkör-hozzárendelést a saját maga számára, hogy visszavonja ezeket a jogosultságokat, ezért el kell távolítania a felhasználói hozzáférés rendszergazdai`/`szerepkör-hozzárendelését a root scope () szolgáltatásban.

1. Hívja meg a [Get roleDefinitions](/rest/api/authorization/roledefinitions/get) , ahol `roleName` a felhasználó-hozzáférési rendszergazda határozza meg a felhasználói hozzáférés rendszergazdai szerepkörének a nevét.

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

    Mentse az azonosítót a `name` paraméterből, ebben az esetben `18d7d88d-d35e-4fb5-a5c3-7773c20a72d9`.

1. A címtár-hatókörben a címtár-rendszergazda szerepkör-hozzárendelését is fel kell sorolnia. Sorolja fel az összes hozzárendelést a címtár `principalId` hatókörében azon címtár-rendszergazda számára, aki az emelt szintű hozzáférési hívást kezdeményezte. Ez a lista a címtárban lévő összes hozzárendelést listázza a ObjectId.

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectid}'
    ```
        
    >[!NOTE] 
    >A címtár-rendszergazda nem rendelkezhet sok hozzárendeléssel, ha az előző lekérdezés túl sok hozzárendelést ad vissza, az összes hozzárendelés lekérdezése csak a címtár hatóköri szintjén végezhető el, majd szűrheti az eredményeket:`GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=atScope()`
            
1. Az előző hívások a szerepkör-hozzárendelések listáját adják vissza. Keresse meg azt a szerepkör-hozzárendelést `"/"` , amelyben `roleDefinitionId` a hatókör szerepel, és az 1. lépésben megtalált `principalId` szerepkör-név és a címtár-rendszergazda objectId. 
    
    Példa szerepkör-hozzárendelésre:
    
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
    
    Ismét mentse az azonosítót a `name` paraméterből, ebben az esetben 11111111-1111-1111-1111-111111111111.

1. Végül a szerepkör-hozzárendelési azonosító használatával távolítsa el a következő `elevateAccess`által hozzáadott hozzárendelést:

    ```http
    DELETE https://management.azure.com/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111?api-version=2015-07-01
    ```

## <a name="next-steps"></a>További lépések

- [A különböző Azure-beli szerepkörök ismertetése](rbac-and-directory-admin-roles.md)
- [Az Azure-erőforrásokhoz való hozzáférés kezelése a RBAC és a REST API használatával](role-assignments-rest.md)
