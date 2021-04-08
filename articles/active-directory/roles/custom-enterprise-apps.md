---
title: Egyéni szerepkörök létrehozása vállalati alkalmazások kezeléséhez Azure Active Directory
description: Egyéni Azure AD-szerepkörök létrehozása és társítása a vállalati alkalmazások eléréséhez Azure Active Directory
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 11/04/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1a76e2d37e9dcdd285a8608fdbfd715bfb834eb8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103467748"
---
# <a name="create-custom-roles-to-manage-enterprise-apps-in-azure-active-directory"></a>Egyéni szerepkörök létrehozása vállalati alkalmazások kezeléséhez Azure Active Directory

Ez a cikk azt ismerteti, hogyan lehet egyéni szerepkört létrehozni a Azure Active Directory (Azure AD) felhasználói és csoportjai számára a vállalati alkalmazás-hozzárendelések kezeléséhez szükséges engedélyekkel. A szerepkörök hozzárendeléseinek elemeihez, valamint a kifejezések, például altípusok, engedélyek és tulajdonságértékek jelentéséhez tekintse meg az [Egyéni szerepkörök áttekintése](custom-overview.md)című témakört.

## <a name="enterprise-app-role-permissions"></a>Vállalati alkalmazás szerepkör engedélyei

Ebben a cikkben két nagyvállalati alkalmazásra vonatkozó engedély szerepel. Az összes példa az Update (frissítés) engedélyt használja.

* A felhasználói és csoportos hozzárendelések hatókörben való olvasásához adja meg az `microsoft.directory/servicePrincipals/appRoleAssignedTo/read` engedélyt
* A felhasználói és csoportos hozzárendelések hatókörben való kezeléséhez adja meg az `microsoft.directory/servicePrincipals/appRoleAssignedTo/update` engedélyt

A frissítési engedély megadása azt eredményezi, hogy a megbízott felügyeli a felhasználók és csoportok hozzárendelését a vállalati alkalmazásokhoz. A felhasználói és/vagy csoportos hozzárendelések hatóköre megadható egyetlen alkalmazás számára, vagy az összes alkalmazás számára engedélyezett. Ha az adott szervezetre kiterjedő szinten biztosított, a megbízott az összes alkalmazás hozzárendeléseit kezelheti. Ha az alkalmazás szintjén történik, a megbízott csak a megadott alkalmazás hozzárendeléseit tudja kezelni.

A frissítési engedély megadása két lépésben történik:

1. Egyéni szerepkör létrehozása engedélyekkel `microsoft.directory/servicePrincipals/appRoleAssignedTo/update`
1. Felhasználók vagy csoportok jogosultságának megadása a felhasználói és csoportos hozzárendelések vállalati alkalmazásokhoz való kezeléséhez. Ez az az érték, amikor a hatókört a szervezetre kiterjedő szintre vagy egyetlen alkalmazásra állíthatja be.

## <a name="use-the-azure-ad-admin-center"></a>Az Azure AD felügyeleti központ használata

### <a name="create-a-new-custom-role"></a>Új egyéni szerepkör létrehozása

>[!NOTE]
> Az egyéni szerepköröket a szervezetre kiterjedő szinten hozzák létre és kezelik, és csak a szervezet áttekintő oldaláról érhetők el.

1. Jelentkezzen be az [Azure ad felügyeleti központba](https://aad.portal.azure.com) a Kiemelt szerepkörű rendszergazda vagy a szervezet globális rendszergazdai engedélyeivel.
1. Válassza a **Azure Active Directory** lehetőséget, válassza a **szerepkörök és rendszergazdák** lehetőséget, majd válassza az **új egyéni szerepkör** lehetőséget.

    ![Új egyéni szerepkör hozzáadása az Azure AD szerepköreinek listájából](./media/custom-enterprise-apps/new-custom-role.png)

1. Az **alapok** lapon adja meg a "felhasználói és csoportos hozzárendelések kezelése" nevet a szerepkörhöz, valamint a "felhasználói és csoportos hozzárendelések kezelésének engedélyezése" jogosultságot a szerepkör leírásához, majd kattintson a **tovább** gombra.

    ![Adja meg az egyéni szerepkör nevét és leírását](./media/custom-enterprise-apps/role-name-and-description.png)

1. Az **engedélyek** lapon írja be a "Microsoft. Directory/ServicePrincipals/appRoleAssignedTo/Update" kifejezést a keresőmezőbe, majd jelölje be a kívánt engedélyek melletti jelölőnégyzeteket, majd kattintson a **tovább** gombra.

    ![Az engedélyek hozzáadása az egyéni szerepkörhöz](./media/custom-enterprise-apps/role-custom-permissions.png)

1. A **felülvizsgálat + létrehozás** lapon tekintse át az engedélyeket, és válassza a **Létrehozás** lehetőséget.

    ![Most létrehozhat egy egyéni szerepkört](./media/custom-enterprise-apps/role-custom-create.png)

### <a name="assign-the-role-to-a-user-using-the-azure-ad-portal"></a>A szerepkör kiosztása egy felhasználónak az Azure AD-portál használatával

1. Jelentkezzen be az [Azure ad felügyeleti központba](https://aad.portal.azure.com) a Kiemelt szerepkörű rendszergazda szerepkör engedélyeivel.
1. Válassza a **Azure Active Directory** , majd a **szerepkörök és rendszergazdák** lehetőséget.
1. Válassza ki a **felhasználók és csoportok hozzárendelései szerepkör kezeléséhez szükséges engedélyeket** .

    ![Szerepkörök és rendszergazdák megnyitása és az egyéni szerepkör keresése](./media/custom-enterprise-apps/select-custom-role.png)

1. Válassza a **hozzárendelés hozzáadása** lehetőséget, válassza ki a kívánt felhasználót, majd kattintson a **kiválasztás** elemre, és adja hozzá a szerepkör-hozzárendelést a felhasználóhoz.

    ![Hozzárendelés hozzáadása az egyéni szerepkörhöz a felhasználó számára](./media/custom-enterprise-apps/assign-user-to-role.png)

#### <a name="assignment-tips"></a>Hozzárendelési tippek

* Ha engedélyeket szeretne adni a engedményesek számára a felhasználók és a csoportok hozzáférésének kezeléséhez az összes vállalati alkalmazáshoz, a szervezethez tartozó Azure AD **áttekintő** lapján a szervezeti szintű **szerepkörök és rendszergazdák** listájából kezdheti meg.
* Ha engedélyeket szeretne adni a engedményesek számára a felhasználók és a csoportok hozzáférésének kezeléséhez egy adott vállalati alkalmazáshoz, lépjen az alkalmazásba az Azure AD-ben, és nyissa meg az alkalmazás **szerepkörök és rendszergazdák** listájában. Válassza ki az új egyéni szerepkört, és fejezze be a felhasználó vagy csoport hozzárendelését. A engedményesek csak az adott alkalmazás esetében kezelheti a felhasználókat és a csoportokhoz való hozzáférést.
* Az egyéni szerepkör-hozzárendelés teszteléséhez jelentkezzen be meghatalmazottként, és nyisson meg egy alkalmazás **felhasználóinak és csoportjainak** lapját annak ellenőrzéséhez, hogy engedélyezve van-e a **felhasználó hozzáadása** lehetőség.

    ![A felhasználói engedélyek ellenőrzése](./media/custom-enterprise-apps/verify-user-permissions.png)

## <a name="use-azure-ad-powershell"></a>Az Azure AD PowerShell használata

További részletekért lásd: [Egyéni szerepkör létrehozása és társítása](custom-create.md) [az erőforrás-hatókörhöz a PowerShell használatával](custom-assign-powershell.md).

Először telepítse az Azure AD PowerShell-modult [a PowerShell-Galéria](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17). Ezután importálja az Azure AD PowerShell előzetes verzióját a következő parancs használatával:

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

### <a name="create-a-custom-role"></a>Egyéni szerepkör létrehozása

Hozzon létre egy új szerepkört a következő PowerShell-parancsfájl használatával:

```PowerShell
# Basic role information
$description = "Manage user and group assignments"
$displayName = "Can manage user and group assignments for Applications"
$templateId = (New-Guid).Guid

# Set of permissions to grant
$allowedResourceAction =@( "microsoft.directory/servicePrincipals/appRoleAssignedTo/update")
$resourceActions = @{'allowedResourceActions'= $allowedResourceAction}
$rolePermission = @{'resourceActions' = $resourceActions}
$rolePermissions = $rolePermission

# Create new custom admin role
$customRole = New-AzureADMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="assign-the-custom-role"></a>Az egyéni szerepkör kiosztása

Rendelje hozzá a szerepkört a PowerShell-parancsfájl használatával.

```powershell
PowerShell
# Basic role information

$description = "Manage user and group assignments"
$displayName = "Can manage user and group assignments for Applications"
$templateId = (New-Guid).Guid

# Set of permissions to grant
$allowedResourceAction =
@(
    "microsoft.directory/servicePrincipals/appRoleAssignedTo/update"
)
$resourceActions = @{'allowedResourceActions'= $allowedResourceAction}
$rolePermission = @{'resourceActions' = $resourceActions}
$rolePermissions = $rolePermission

# Create new custom role
$customRole = New-AzureAdRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

## <a name="use-the-microsoft-graph-api"></a>A Microsoft Graph API használata

Hozzon létre egy egyéni szerepkört a Microsoft Graph API megadott példájának használatával. További részletekért lásd: [Egyéni szerepkör létrehozása és társítása](custom-create.md) és [Egyéni rendszergazdai szerepkörök társítása a Microsoft Graph API használatával](custom-assign-graph.md).

HTTP-kérelem az egyéni szerepkör létrehozásához.

```HTTP
POST
https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitionsIsEnabled $true
{
    "description":"Can manage user and group assignments for Applications.",
    "displayName":" Manage user and group assignments",
    "isEnabled":true,
    "rolePermissions":
    [
        {
            "resourceActions":
            {
                "allowedResourceActions":
                [
                    "microsoft.directory/servicePrincipals/appRoleAssignedTo/update"
                ]
            },
            "condition":null
        }
    ],
    "templateId":"<PROVIDE NEW GUID HERE>",
    "version":"1"
}
```

### <a name="assign-the-custom-role-using-microsoft-graph-api"></a>Egyéni szerepkör kiosztása Microsoft Graph API használatával

A szerepkör-hozzárendelés egyesíti a rendszerbiztonsági tag AZONOSÍTÓját (amely lehet egy felhasználó vagy egy egyszerű szolgáltatásnév), egy szerepkör-definíciós azonosító és egy Azure AD-erőforrás hatóköre. A szerepkör-hozzárendelés elemeivel kapcsolatos további információkért tekintse meg az [Egyéni szerepkörök áttekintése](custom-overview.md) című témakört.

Egyéni szerepkör hozzárendelésére szolgáló HTTP-kérelem.

```HTTP
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments

{
    "principalId":"<PROVIDE OBJECTID OF USER TO ASSIGN HERE>",
    "roleDefinitionId":"<PROVIDE OBJECTID OF ROLE DEFINITION HERE>",
    "resourceScopes":["/"]
}
```

## <a name="next-steps"></a>Következő lépések

* [A vállalati alkalmazások számára elérhető egyéni szerepkör-engedélyek megismerése](custom-enterprise-app-permissions.md)
