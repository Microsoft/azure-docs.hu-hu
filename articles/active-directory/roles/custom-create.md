---
title: Egyéni szerepkörök létrehozása az Azure AD szerepköralapú hozzáférés-vezérlésében | Microsoft Docs
description: Egyéni Azure AD-szerepköröket hozhat létre és rendelhet hozzá erőforrás-hatókörrel Azure Active Directory erőforrásokhoz.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 01/05/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e0e1543f18c18c7fdf97c39f35ba38ded658392
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103007817"
---
# <a name="create-and-assign-a-custom-role-in-azure-active-directory"></a>Egyéni szerepkör létrehozása és társítása Azure Active Directory

Ez a cikk bemutatja, hogyan hozhat létre új egyéni szerepköröket a Azure Active Directoryban (Azure AD). Az egyéni szerepkörök alapjaival kapcsolatban tekintse meg az [Egyéni szerepkörök áttekintése](custom-overview.md)című témakört. A szerepkör csak a címtár szintű hatókörben vagy az alkalmazás regisztrációs erőforrásának hatókörében rendelhető hozzá.

Az egyéni szerepkörök az Azure AD Áttekintés lapjának [szerepkörök és rendszergazdák](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators) lapján hozhatók létre.

## <a name="create-a-role-in-the-azure-portal"></a>Szerepkör létrehozása a Azure Portalban

### <a name="create-a-new-custom-role-to-grant-access-to-manage-app-registrations"></a>Új egyéni szerepkör létrehozása az alkalmazások regisztrálásához való hozzáférés biztosításához

1. Jelentkezzen be az [Azure ad felügyeleti központba](https://aad.portal.azure.com) a Kiemelt szerepkörű rendszergazda vagy a globális rendszergazdai engedélyekkel az Azure ad-szervezetben.
1. Válassza ki **Azure Active Directory**  >  **szerepkörök és rendszergazdák**  >  **új egyéni szerepkört**.

   ![Szerepkörök létrehozása vagy szerkesztése a szerepkörök és rendszergazdák lapról](./media/custom-create/new-custom-role.png)

1. Az **alapok** lapon adja meg a szerepkör nevét és leírását, majd kattintson a **tovább** gombra.

   ![adja meg az egyéni szerepkör nevét és leírását az alapok lapon](./media/custom-create/basics-tab.png)

1. Az **engedélyek** lapon válassza ki az alkalmazás-regisztrációk alapszintű tulajdonságainak és hitelesítő adatainak kezeléséhez szükséges engedélyeket. Az egyes engedélyek részletes ismertetését lásd: [alkalmazás-regisztrációs altípusok és engedélyek a Azure Active Directory](custom-available-permissions.md).
   1. Először írja be a "hitelesítő adatok" kifejezést a keresősáv mezőbe, és válassza ki az `microsoft.directory/applications/credentials/update` engedélyt.

      ![Egyéni szerepkör engedélyeinek kiválasztása az engedélyek lapon](./media/custom-create/permissions-tab.png)

   1. Ezután írja be az "alapszintű" kifejezést a keresősávba, válassza ki az `microsoft.directory/applications/basic/update` engedélyt, majd kattintson a **tovább** gombra.
1. A **felülvizsgálat + létrehozás** lapon tekintse át az engedélyeket, és válassza a **Létrehozás** lehetőséget.

Az egyéni szerepkör megjelenik a Hozzárendelendő elérhető szerepkörök listájában.

## <a name="create-a-role-using-powershell"></a>Szerepkör létrehozása a PowerShell használatával

### <a name="prepare-powershell"></a>A PowerShell előkészítése

Először [le kell töltenie az Azure ad Preview PowerShell-modult](https://www.powershellgallery.com/packages/AzureADPreview).

Az Azure AD PowerShell-modul telepítéséhez használja az alábbi parancsokat:

``` PowerShell
Install-Module -Name AzureADPreview 
Import-Module -Name AzureADPreview 
```

A következő parancs használatával ellenőrizheti, hogy a modul használatra kész-e:

``` PowerShell
Get-Module -Name AzureADPreview 

  ModuleType Version      Name                         ExportedCommands 
  ---------- ---------    ----                         ---------------- 
  Binary     2.0.0.115    AzureADPreview               {Add-AzureADAdministrati...} 
```

### <a name="connect-to-azure"></a>Csatlakozás az Azure szolgáltatáshoz

Azure Active Directoryhoz való kapcsolódáshoz használja a következő parancsot:

``` PowerShell
Connect-AzureAD
```

### <a name="create-the-custom-role"></a>Az egyéni szerepkör létrehozása

Hozzon létre egy új szerepkört a következő PowerShell-parancsfájl használatával:

``` PowerShell
# Basic role information
$displayName = "Application Support Administrator"
$description = "Can manage basic aspects of application registrations."
$templateId = (New-Guid).Guid
 
# Set of permissions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/basic/update",
    "microsoft.directory/applications/credentials/update"
)
$rolePermissions = @{'allowedResourceActions'= $allowedResourceAction}
 
# Create new custom admin role
$customAdmin = New-AzureADMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="assign-the-custom-role-using-azure-ad-powershell"></a>Egyéni szerepkör kiosztása az Azure AD PowerShell használatával

Rendelje hozzá a szerepkört az alábbi PowerShell-parancsfájl használatával:

``` PowerShell
# Get the user and role definition you want to link
$user = Get-AzureADUser -Filter "userPrincipalName eq 'cburl@f128.info'"
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Application Support Administrator'"

# Get app registration and construct resource scope for assignment.
$appRegistration = Get-AzureADApplication -Filter "displayName eq 'f/128 Filter Photos'"
$resourceScope = '/' + $appRegistration.objectId

# Create a scoped role assignment
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope $resourceScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
```

## <a name="create-a-role-with-graph-api"></a>Szerepkör létrehozása a Graph API

1. Hozza létre a szerepkör-definíciót.

    HTTP-kérelem egyéni szerepkör-definíció létrehozásához.

    POST

    ``` HTTP
    https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions
    ```

    Törzs

    ``` HTTP
    {
       "description": "Can manage basic aspects of application registrations.",
       "displayName": "Application Support Administrator",
       "isEnabled": true,
       "templateId": "<GUID>",
       "rolePermissions": [
           {
               "allowedResourceActions": [
                   "microsoft.directory/applications/basic/update",
                   "microsoft.directory/applications/credentials/update"
               ]
           }
       ]
    }
    ```

    > [!Note]
    > Az egy `"templateId": "GUID"` opcionális paraméter, amelyet a rendszer a követelménytől függően elküldhet a törzsben. Ha több különböző egyéni szerepkört kell létrehoznia közös paraméterekkel, érdemes létrehoznia egy sablont, és meg kell határoznia egy `templateId` értéket. `templateId`A PowerShell-parancsmag használatával előre létrehozhat egy értéket `(New-Guid).Guid` . 

1. Hozza létre a szerepkör-hozzárendelést.

    HTTP-kérelem egyéni szerepkör-definíció létrehozásához.

    POST

    ``` HTTP
    https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
    ```

    Törzs

    ``` HTTP
   {
       "principalId":"<GUID OF USER>",
       "roleDefinitionId":"<GUID OF ROLE DEFINITION>",
       "resourceScope":"/<GUID OF APPLICATION REGISTRATION>"
   }
    ```

## <a name="assign-a-custom-role-scoped-to-a-resource"></a>Erőforráshoz tartozó egyéni szerepkör társítása

A beépített szerepkörökhöz hasonlóan az egyéni szerepkörök alapértelmezés szerint az alapértelmezett szervezeti szintű hatókörben vannak hozzárendelve, hogy hozzáférési engedélyeket adjanak a szervezet összes alkalmazás-regisztrációja felett. A beépített szerepköröktől eltérően azonban egyéni szerepkörök is hozzárendelhetők egyetlen Azure AD-erőforrás hatóköréhez. Ez lehetővé teszi, hogy a felhasználónak engedélyt kapjon egy alkalmazás hitelesítő adatainak és alapvető tulajdonságainak frissítésére anélkül, hogy egy második egyéni szerepkört kellene létrehoznia.

1. Jelentkezzen be az [Azure ad felügyeleti központba](https://aad.portal.azure.com) az alkalmazás fejlesztői engedélyeivel az Azure ad-szervezetben.
1. Válassza az **Alkalmazásregisztrációk** lehetőséget.
1. Válassza ki azt az alkalmazás-regisztrációt, amelyhez hozzáférést kíván adni a kezeléshez. Előfordulhat, hogy az **összes alkalmazás** lehetőséget kell választania az Azure ad-szervezetben az alkalmazások regisztrálásának teljes listájának megtekintéséhez.

    ![Válassza ki az alkalmazás regisztrációját erőforrás-hatókörként a szerepkör-hozzárendeléshez](./media/custom-create/appreg-all-apps.png)

1. Az alkalmazás regisztrálása területen válassza a **szerepkörök és rendszergazdák** lehetőséget. Ha még nem hozott létre ilyet, az utasítások az [előző eljárásban](#create-a-new-custom-role-to-grant-access-to-manage-app-registrations)találhatók.

1. Válassza ki a szerepkört a **hozzárendelések** lap megnyitásához.
1. Felhasználó hozzáadásához válassza a **hozzárendelés hozzáadása** lehetőséget. A felhasználó csak a kiválasztott alkalmazás regisztrációja után kap engedélyeket.

## <a name="next-steps"></a>Következő lépések

- Nyugodtan ossza meg velünk az [Azure ad rendszergazdai szerepkörökkel foglalkozó fórumát](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
- A szerepkörökkel és a rendszergazdai szerepkör-hozzárendeléssel kapcsolatos további információkért lásd: [rendszergazdai szerepkörök hozzárendelése](permissions-reference.md).
- Az alapértelmezett felhasználói engedélyek összehasonlítását lásd: a [vendég és a tag alapértelmezett felhasználói engedélyeinek összehasonlítása](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2froles%2fcontext%2fugr-context).