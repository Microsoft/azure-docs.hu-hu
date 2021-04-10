---
title: Alkalmazás-regisztrációk létrehozási korlátainak eltávolítása – Azure AD | Microsoft Docs
description: Egyéni szerepkör hozzárendelése a nem korlátozott alkalmazások regisztrációjának engedélyezéséhez az Azure AD-Active Directory
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: quickstart
ms.date: 11/05/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro, devx-track-azurepowershell
ms.collection: M365-identity-device-management
ms.openlocfilehash: 48740c52e6713735957e1ebf00e8996800eabec2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103015777"
---
# <a name="quickstart-grant-permission-to-create-unlimited-app-registrations"></a>Gyors útmutató: korlátlan alkalmazás-regisztrációk létrehozásának engedélyezése

Ebben a gyors üzembe helyezési útmutatóban létrehozhat egy egyéni szerepkört, amely korlátlan számú alkalmazás regisztrációját hozza létre, majd hozzárendeli a szerepkört egy felhasználóhoz. A hozzárendelt felhasználó ezután az Azure AD portál, az Azure AD PowerShell vagy a Microsoft Graph API használatával hozhat létre alkalmazás-regisztrációkat. A beépített alkalmazás-fejlesztői szerepkörtől eltérően ez az egyéni szerepkör korlátlan számú alkalmazás-regisztráció létrehozását teszi lehetővé. Az alkalmazás fejlesztői szerepköre biztosítja a képességet, de a létrehozott objektumok teljes száma a 250-ra van korlátozva, hogy ne verjék [a teljes címtárra kiterjedő objektum kvótáját](../enterprise-users/directory-service-limits-restrictions.md). Az Azure AD egyéni szerepköreinek létrehozásához és hozzárendeléséhez szükséges legalacsonyabb jogosultsági szintű szerepkör a Kiemelt szerepkörű rendszergazda.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="create-a-custom-role-using-the-azure-ad-portal"></a>Egyéni szerepkör létrehozása az Azure AD-portál használatával

1. Jelentkezzen be az [Azure ad felügyeleti központba](https://aad.portal.azure.com) a Kiemelt szerepkörű rendszergazda vagy a globális rendszergazdai engedélyekkel az Azure ad-szervezetben.
1. Válassza a **Azure Active Directory** lehetőséget, válassza a **szerepkörök és rendszergazdák** lehetőséget, majd válassza az **új egyéni szerepkör** lehetőséget.

    ![Szerepkörök létrehozása vagy szerkesztése a szerepkörök és rendszergazdák lapról](./media/quickstart-app-registration-limits/new-custom-role.png)

1. Az **alapvető beállítások** lapon adja meg az "alkalmazás regisztrációs létrehozója" kifejezést a szerepkör nevéhez, és "korlátlan számú alkalmazás-regisztrációt hozhat létre" a szerepkör leírásához, majd kattintson a **tovább** gombra.

    ![adja meg az egyéni szerepkör nevét és leírását az alapok lapon](./media/quickstart-app-registration-limits/basics-tab.png)

1. Az **engedélyek** lapon adja meg a "Microsoft. Directory/alkalmazások/létrehozás" kifejezést a keresőmezőbe, majd jelölje be a kívánt engedélyek melletti jelölőnégyzeteket, majd kattintson a **tovább** gombra.

    ![Egyéni szerepkör engedélyeinek kiválasztása az engedélyek lapon](./media/quickstart-app-registration-limits/permissions-tab.png)

1. A **felülvizsgálat + létrehozás** lapon tekintse át az engedélyeket, és válassza a **Létrehozás** lehetőséget.

### <a name="assign-the-role-in-the-azure-ad-portal"></a>A szerepkör kiosztása az Azure AD-portálon

1. Jelentkezzen be az [Azure ad felügyeleti központba](https://aad.portal.azure.com) a Kiemelt szerepkörű rendszergazda vagy a globális rendszergazdai engedélyekkel az Azure ad-szervezetben.
1. Válassza a **Azure Active Directory** , majd a **szerepkörök és rendszergazdák** lehetőséget.
1. Válassza ki az alkalmazás regisztrációjának létrehozója szerepkört, és válassza a **hozzárendelés hozzáadása** elemet.
1. Válassza ki a kívánt felhasználót, és kattintson a **kiválasztás** gombra a felhasználó szerepkörhöz való hozzáadásához.

Kész! Ebben a rövid útmutatóban sikeresen létrehozott egy egyéni szerepkört, amely korlátlan számú alkalmazás regisztrációját hozza létre, majd hozzárendeli a szerepkört egy felhasználóhoz.

> [!TIP]
> Ha az Azure AD-portál használatával szeretné hozzárendelni a szerepkört egy alkalmazáshoz, adja meg az alkalmazás nevét a hozzárendelési oldal keresőmező mezőjében. Az alkalmazások alapértelmezés szerint nem jelennek meg a listában, de a keresési eredmények között lesznek visszaadva.

### <a name="app-registration-permissions"></a>Alkalmazásregisztráció engedélyei

Két engedély áll rendelkezésre, amelyek lehetővé teszi az alkalmazás-regisztrációk létrehozását, amelyek mindegyike különböző viselkedést biztosít.

- Microsoft. Directory/Applications/createAsOwner: az engedély kiosztása azt eredményezi, hogy a létrehozó a létrehozott alkalmazás regisztrációjának első tulajdonosaként lesz hozzáadva, és a létrehozott alkalmazás regisztrálása a létrehozó 250 létrehozott objektum-kvótájának megfelelően fog megjelenni.
- Microsoft. Directory/alkalmazások/létrehozás: az engedély kiosztása azt eredményezi, hogy a létrehozó nem jelenik meg a létrehozott alkalmazás regisztrációjának első tulajdonosaként, és a létrehozott alkalmazás regisztrálása nem fog megjelenni a létrehozó 250 létrehozott objektum-kvótájában. Ezt az engedélyt körültekintően kell használni, mivel semmi nem akadályozza meg, hogy az alkalmazás regisztrációkat hozzon létre, amíg meg nem találja a címtár-szintű kvótát. Ha mindkét engedély hozzá van rendelve, ez az engedély elsőbbséget élvez.

## <a name="create-a-custom-role-in-azure-ad-powershell"></a>Egyéni szerepkör létrehozása az Azure AD PowerShellben

### <a name="prepare-powershell"></a>A PowerShell előkészítése

Először telepítse az Azure AD PowerShell-modult a [PowerShell-Galéria](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17). Ezután importálja az Azure AD PowerShell előzetes verzióját a következő parancs használatával:

```powershell
Import-Module -Name AzureADPreview
```

Annak ellenőrzéséhez, hogy a modul készen áll-e a használatra, a következő parancs által visszaadott verziónak kell megfelelnie az itt felsoroltak közül:

```powershell
Get-Module -Name AzureADPreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    AzureADPreview               {Add-AzureADAdministrati...}
```

### <a name="create-the-custom-role-in-azure-ad-powershell"></a>Egyéni szerepkör létrehozása az Azure AD PowerShellben

Hozzon létre egy új szerepkört a következő PowerShell-parancsfájl használatával:

```powershell

# Basic role information
$displayName = "Application Registration Creator"
$description = "Can create an unlimited number of application registrations."
$templateId = (New-Guid).Guid

# Set of permissions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/create"
    "microsoft.directory/applications/createAsOwner"
)
$rolePermissions = @{'allowedResourceActions'= $allowedResourceAction}

# Create new custom admin role
$customRole = New-AzureAdMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="assign-the-role-in-azure-ad-powershell"></a>A szerepkör kiosztása az Azure AD PowerShellben

Rendelje hozzá a szerepkört a következő PowerShell-parancsfájl használatával:

```powershell
# Get the user and role definition you want to link
$user = Get-AzureADUser -Filter "userPrincipalName eq 'Adam@contoso.com'"
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Application Registration Creator'"

# Get resource scope for assignment
$resourceScope = '/'

# Create a scoped role assignment
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope $resourceScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
```

## <a name="create-a-custom-role-in-the-microsoft-graph-api"></a>Egyéni szerepkör létrehozása a Microsoft Graph API-ban

HTTP-kérelem az egyéni szerepkör létrehozásához.

POST

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions
```

Törzs

```HTTP
{
    "description":"Can create an unlimited number of application registrations.",
    "displayName":"Application Registration Creator",
    "isEnabled":true,
    "rolePermissions":
    [
        {
            "resourceActions":
            {
                "allowedResourceActions":
                [
                    "microsoft.directory/applications/create"
                    "microsoft.directory/applications/createAsOwner"
                ]
            },
            "condition":null
        }
    ],
    "templateId":"<PROVIDE NEW GUID HERE>",
    "version":"1"
}
```

### <a name="assign-the-role-in-the-microsoft-graph-api"></a>A szerepkör kiosztása a Microsoft Graph API-ban

A szerepkör-hozzárendelés egyesíti a rendszerbiztonsági tag AZONOSÍTÓját (amely lehet egy felhasználó vagy egy egyszerű szolgáltatásnév), egy szerepkör-definíciós (szerepkör-) azonosító és egy Azure AD-erőforrás hatóköre.

Egyéni szerepkör hozzárendelésére szolgáló HTTP-kérelem.

POST

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
```

Törzs

``` HTTP
{
    "principalId":"<PROVIDE OBJECTID OF USER TO ASSIGN HERE>",
    "roleDefinitionId":"<PROVIDE OBJECTID OF ROLE DEFINITION HERE>",
    "resourceScopes":["/"]
}
```

## <a name="next-steps"></a>Következő lépések

- Nyugodtan ossza meg velünk az [Azure ad rendszergazdai szerepkörökkel foglalkozó fórumát](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
- Az Azure AD szerepkör-hozzárendelésekről további információt a [rendszergazdai szerepkörök hozzárendelése](permissions-reference.md)című témakörben talál.
- Az alapértelmezett felhasználói engedélyekkel kapcsolatos további tudnivalókért tekintse meg [az alapértelmezett vendég-és tag felhasználói engedélyek összehasonlítását](../fundamentals/users-default-permissions.md)ismertető témakört.
