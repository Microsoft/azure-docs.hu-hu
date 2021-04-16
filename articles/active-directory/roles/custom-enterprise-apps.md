---
title: Egyéni szerepkörök létrehozása vállalati alkalmazások kezeléséhez a Azure Active Directory
description: Egyéni Azure AD-szerepkörök létrehozása és hozzárendelése vállalati alkalmazások hozzáféréséhez a Azure Active Directory
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 04/14/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a7c04afe76ced0abf40abf8e30362005fb269172
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534713"
---
# <a name="create-custom-roles-to-manage-enterprise-apps-in-azure-active-directory"></a>Egyéni szerepkörök létrehozása vállalati alkalmazások kezeléséhez a Azure Active Directory

Ez a cikk bemutatja, hogyan hozhat létre egyéni szerepkört a felhasználók és csoportok vállalati alkalmazás-hozzárendeléseinek kezeléséhez szükséges engedélyekkel a Azure Active Directory (Azure AD) szolgáltatásban. A szerepkör-hozzárendelések elemeit, valamint az olyan kifejezések jelentését, mint az altípus, az engedély és a tulajdonságkészlet, lásd az egyéni szerepkörök [áttekintését.](custom-overview.md)

## <a name="enterprise-app-role-permissions"></a>Vállalati alkalmazásszerepk szerepkör engedélyei

Ebben a cikkben két vállalati alkalmazásengedélyt tárgyalunk. Minden példa a frissítési engedélyt használja.

* A hatókör felhasználói és csoport-hozzárendeléseinek olvasásához adja meg az `microsoft.directory/servicePrincipals/appRoleAssignedTo/read` engedélyt
* A felhasználói és csoport-hozzárendelések hatókörben való kezeléséhez adja meg az `microsoft.directory/servicePrincipals/appRoleAssignedTo/update` engedélyt

A frissítési engedély megadása azt teszi lehetővé, hogy a hozzárendelt kezelni tudja a felhasználók és csoportok vállalati alkalmazásokhoz való hozzárendelését. A felhasználói és/vagy csoport-hozzárendelések hatóköre adható meg egyetlen alkalmazáshoz, vagy minden alkalmazáshoz. Ha szervezeti szintű jogosultságot kap, a hozzárendelő minden alkalmazás hozzárendelését kezelheti. Ha alkalmazásszinten készült, a hozzárendelő csak a megadott alkalmazás hozzárendelését tudja kezelni.

A frissítési engedély megadása két lépésben történik:

1. Egyéni szerepkör létrehozása engedéllyel `microsoft.directory/servicePrincipals/appRoleAssignedTo/update`
1. Engedélyeket adhat a felhasználóknak vagy csoportoknak a vállalati alkalmazásokhoz való felhasználói és csoport-hozzárendelések kezeléséhez. Itt állíthatja be a hatókört a teljes szervezetre kiterjedően vagy egyetlen alkalmazásra.

## <a name="use-the-azure-ad-admin-center"></a>Az Azure AD felügyeleti központ használata

### <a name="create-a-new-custom-role"></a>Új egyéni szerepkör létrehozása

>[!NOTE]
> Az egyéni szerepkörök létrehozása és kezelése a teljes szervezet szintjén lehetséges, és csak a szervezet Áttekintés oldalán érhetők el.

1. Jelentkezzen be az [Azure AD felügyeleti központba](https://aad.portal.azure.com) kiemelt szerepkör-rendszergazdai vagy globális rendszergazdai engedélyekkel a szervezetben.
1. Válassza **Azure Active Directory** lehetőséget, válassza a **Szerepkörök és rendszergazdák,** majd az Új egyéni szerepkör **lehetőséget.**

    ![Új egyéni szerepkör hozzáadása az Azure AD szerepkörlistából](./media/custom-enterprise-apps/new-custom-role.png)

1. Az Alapvető **beállítások** lapon adja meg a "Felhasználó- és csoport-hozzárendelések kezelése" nevet a szerepkör neveként, az Engedélyek megadása a felhasználó- és csoport-hozzárendelések kezeléséhez lehetőséget a szerepkör leírásához, majd válassza a Tovább **lehetőséget.**

    ![Adja meg az egyéni szerepkör nevét és leírását](./media/custom-enterprise-apps/role-name-and-description.png)

1. Az **Engedélyek lapon** írja be a "microsoft.directory/servicePrincipals/appRoleAssignedTo/update" szövegmezőt a keresőmezőbe, majd jelölje be a kívánt engedélyek melletti jelölőnégyzeteket, majd kattintson a Tovább **gombra.**

    ![Engedélyek hozzáadása az egyéni szerepkörhöz](./media/custom-enterprise-apps/role-custom-permissions.png)

1. Az Áttekintés **+ létrehozás lapon** tekintse át az engedélyeket, és válassza a Létrehozás **lehetőséget.**

    ![Most már létrehozhatja az egyéni szerepkört](./media/custom-enterprise-apps/role-custom-create.png)

### <a name="assign-the-role-to-a-user-using-the-azure-ad-portal"></a>Szerepkör hozzárendelése egy felhasználóhoz az Azure AD portál használatával

1. Jelentkezzen be az [Azure AD felügyeleti központba](https://aad.portal.azure.com) kiemelt szerepkör rendszergazdai szerepkör engedélyekkel.
1. Válassza **a Azure Active Directory,** majd a **Szerepkörök és rendszergazdák lehetőséget.**
1. Válassza az **Engedélyek megadása a felhasználó- és csoport-hozzárendelések kezeléséhez szerepkört.**

    ![Nyissa meg a Szerepkörök és rendszergazdák et, és keresse meg az egyéni szerepkört](./media/custom-enterprise-apps/select-custom-role.png)

1. Válassza **a Hozzárendelés hozzáadása** lehetőséget, válassza  ki a kívánt felhasználót, majd kattintson a Kijelölés gombra a szerepkör-hozzárendelés hozzáadásához.

    ![Egyéni szerepkör hozzárendelésének hozzáadása a felhasználóhoz](./media/custom-enterprise-apps/assign-user-to-role.png)

#### <a name="assignment-tips"></a>Hozzárendelési tippek

* Ha engedélyeket szeretne rendelni a felhasználók és csoportok hozzáférésének kezeléséhez minden vállalati alkalmazáshoz a teljes szervezetben, kezdje a szervezeti szintű Szerepkörök és rendszergazdák listájával, amely a szervezet Azure **AD** áttekintési oldalán található. 
* Ha engedélyeket szeretne hozzárendelni egy adott vállalati alkalmazás felhasználóinak és csoportelérésének kezeléséhez, nyissa  meg az alkalmazást az Azure AD-ban, és nyissa meg az alkalmazást az alkalmazás Szerepkörök és rendszergazdák listájában. Válassza ki az új egyéni szerepkört, és töltse ki a felhasználó- vagy csoport-hozzárendelést. A hozzárendelések csak az adott alkalmazás felhasználóit és csoportelérését kezelhetik.
* Az egyéni szerepkör-hozzárendelés teszteléséhez jelentkezzen be hozzárendeltként,  és nyissa meg az  alkalmazás Felhasználók és csoportok lapját annak ellenőrzéséhez, hogy a Felhasználó hozzáadása lehetőség engedélyezve van-e.

    ![A felhasználói engedélyek ellenőrzése](./media/custom-enterprise-apps/verify-user-permissions.png)

## <a name="use-azure-ad-powershell"></a>Az Azure AD PowerShell használata

További részletekért lásd: Egyéni szerepkör létrehozása és [hozzárendelése és](custom-create.md) Egyéni szerepkörök hozzárendelése erőforrás-hatókörrel [a PowerShell használatával.](custom-assign-powershell.md)

Először telepítse az Azure AD PowerShell-modult a [PowerShell-galéria.](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17) Ezután importálja az Azure AD PowerShell előzetes modult a következő paranccsal:

```powershell
Import-Module -Name AzureADPreview
```

Annak ellenőrzéséhez, hogy a modul készen áll-e a használatra, a következő parancs által visszaadott verziónak meg kell egyeznie az itt felsorolt verzióval:

```powershell
Get-Module -Name AzureADPreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    AzureADPreview               {Add-AzureADAdministrati...}
```

### <a name="create-a-custom-role"></a>Egyéni szerepkör létrehozása

Hozzon létre egy új szerepkört a következő PowerShell-szkript használatával:

```PowerShell
# Basic role information
$description = "Manage user and group assignments"
$displayName = "Can manage user and group assignments for Applications"
$templateId = (New-Guid).Guid

# Set of permissions to grant
$allowedResourceAction = @("microsoft.directory/servicePrincipals/appRoleAssignedTo/update")
$resourceActions = @{'allowedResourceActions'= $allowedResourceAction}
$rolePermission = @{'resourceActions' = $resourceActions}
$rolePermissions = $rolePermission

# Create new custom admin role
$customRole = New-AzureADMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="assign-the-custom-role"></a>Az egyéni szerepkör hozzárendelése

Rendelje hozzá a szerepkört ezzel a PowerShell-szkript használatával.

```powershell
# Get the user and role definition you want to link
$user = Get-AzureADUser -Filter "userPrincipalName eq 'chandra@example.com'"
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Manage user and group assignments'"

# Get app registration and construct resource scope for assignment.
$appRegistration = Get-AzureADApplication -Filter "displayName eq 'My Filter Photos'"
$resourceScope = '/' + $appRegistration.objectId

# Create a scoped role assignment
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope $resourceScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
```

## <a name="use-the-microsoft-graph-api"></a>A Microsoft Graph API használata

Hozzon létre egy egyéni szerepkört a Microsoft Graph API-ban megadott példával. További részletekért lásd: Egyéni szerepkör létrehozása és [hozzárendelése és](custom-create.md) Egyéni rendszergazdai szerepkörök hozzárendelése [a Microsoft Graph API használatával.](custom-assign-graph.md)

HTTP-kérés az egyéni szerepkör létrehozásához.

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

### <a name="assign-the-custom-role-using-microsoft-graph-api"></a>Az egyéni szerepkör hozzárendelése az Microsoft Graph API-val

A szerepkör-hozzárendelés kombinálja a rendszerbiztonsági tag azonosítóját (amely lehet felhasználó vagy szolgáltatásnév), egy szerepkör-definíció azonosítója és egy Azure AD-erőforráshatókör. A szerepkör-hozzárendelések elemeivel kapcsolatos további információkért lásd az egyéni [szerepkörök áttekintését.](custom-overview.md)

HTTP-kérés egyéni szerepkör hozzárendeléshez.

```HTTP
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments

{
    "principalId":"<PROVIDE OBJECTID OF USER TO ASSIGN HERE>",
    "roleDefinitionId":"<PROVIDE OBJECTID OF ROLE DEFINITION HERE>",
    "resourceScopes":["/"]
}
```

## <a name="next-steps"></a>Következő lépések

* [Megismerheti a vállalati alkalmazásokhoz elérhető egyéni szerepkör-engedélyeket](custom-enterprise-app-permissions.md)
