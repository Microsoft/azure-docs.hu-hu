---
title: Felhasználói és rendszergazdai engedélyek kezelése – Azure Active Directory | Microsoft Docs
description: Ismerje meg, hogyan tekintheti át és kezelheti az alkalmazásra vonatkozó engedélyeket az Azure AD-ben. Például visszavonhatja az alkalmazásnak biztosított összes engedélyt.
services: active-directory
author: msmimart
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 7/10/2020
ms.author: mimart
ms.reviewer: luleonpla
ms.collection: M365-identity-device-management
ms.openlocfilehash: cbd2bc7b9ccc9efe0c6fdf8de02665160667cb8f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99256932"
---
# <a name="take-action-on-overprivileged-or-suspicious-applications-in-azure-active-directory"></a>A Azure Active Directoryban lévő, nem megfelelő jogosultságokkal rendelkező vagy gyanús alkalmazásokra vonatkozó teendők

Ismerje meg, hogyan tekintheti át és kezelheti az alkalmazás engedélyeit. Ez a cikk különböző műveleteket biztosít az alkalmazás biztonságossá tételéhez a forgatókönyvnek megfelelően. Ezek a műveletek a Azure Active Directory-(Azure AD-) bérlőhöz hozzáadott összes alkalmazásra érvényesek, felhasználói vagy rendszergazdai engedélyekkel.

További információ az alkalmazásokkal való hozzájárulásról: [Azure Active Directory beleegyezési keretrendszer](../develop/consent-framework.md).

## <a name="prerequisites"></a>Előfeltételek

A következő műveletek végrehajtásához globális rendszergazdaként, alkalmazás-rendszergazdaként vagy Felhőbeli alkalmazás-rendszergazdaként kell bejelentkeznie.

Az alkalmazásokhoz való hozzáférés korlátozásához felhasználói hozzárendelést kell megkövetelni, majd hozzá kell rendelnie a felhasználókat vagy csoportokat az alkalmazáshoz.  További információ: [felhasználók és csoportok hozzárendelésének módszerei](./assign-user-or-group-access-portal.md).

Az Azure AD-portálon a műveletek végrehajtásához környezetfüggő PowerShell-parancsfájlokat érhet el.
 
1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) globális rendszergazdaként, alkalmazás-rendszergazdaként vagy Felhőbeli alkalmazás-rendszergazdaként.
2. Válassza **Azure Active Directory**  >  **vállalati alkalmazások** lehetőséget.
3. Válassza ki azt az alkalmazást, amelyhez korlátozni kívánja a hozzáférést.
4. Válassza az **Engedélyek** lehetőséget. A parancssorban válassza az **engedélyek ellenőrzése** lehetőséget.

![Képernyőkép az engedélyek áttekintése ablakról.](./media/manage-application-permissions/review-permissions.png)


## <a name="control-access-to-an-application"></a>Alkalmazáshoz való hozzáférés szabályozása

Javasoljuk, hogy a **felhasználó-hozzárendelés** beállítás bekapcsolásával korlátozza az alkalmazáshoz való hozzáférést.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) globális rendszergazdaként, alkalmazás-rendszergazdaként vagy Felhőbeli alkalmazás-rendszergazdaként.
2. Válassza **Azure Active Directory**  >  **vállalati alkalmazások** lehetőséget.
3. Válassza ki azt az alkalmazást, amelyhez korlátozni kívánja a hozzáférést.
4. Válassza a **Tulajdonságok** lehetőséget, majd állítsa az **Igen** értékre a **felhasználói követelményt** .
5. Válassza a **felhasználók és csoportok** lehetőséget, majd távolítsa el az alkalmazáshoz hozzárendelt nemkívánatos felhasználókat.
6. Felhasználók vagy csoportok társítása az alkalmazáshoz.

Igény szerint eltávolíthatja az alkalmazáshoz hozzárendelt összes felhasználót a PowerShell használatával.

## <a name="revoke-all-permissions-for-an-application"></a>Alkalmazás összes engedélyének visszavonása

A PowerShell-parancsfájl használatával visszavonja az alkalmazásnak biztosított összes engedélyt.

> [!NOTE]
> Az aktuálisan megadott engedély visszavonása nem állítja le a felhasználóktól az alkalmazáshoz való ismételt hozzájárulást. Ha le szeretné tiltani a felhasználók beleegyezését, olvassa el a [felhasználók hozzájárulásának konfigurálása az alkalmazásokhoz című témakört](configure-user-consent.md).

Igény szerint letilthatja az alkalmazást, hogy a felhasználók hozzáférhessenek az alkalmazáshoz, és hogy az alkalmazás hozzáférjen az adatokhoz.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) globális rendszergazdaként, alkalmazás-rendszergazdaként vagy Felhőbeli alkalmazás-rendszergazdaként.
2. Válassza **Azure Active Directory**  >  **vállalati alkalmazások** lehetőséget.
3. Válassza ki azt az alkalmazást, amelyhez korlátozni kívánja a hozzáférést.
4. Válassza a **Tulajdonságok** lehetőséget, majd **engedélyezze a felhasználók számára a bejelentkezést?** **nem** értékre.

## <a name="investigate-a-suspicious-application"></a>Gyanús alkalmazás vizsgálata

Javasoljuk, hogy a **felhasználó-hozzárendelés** beállítás bekapcsolásával korlátozza az alkalmazáshoz való hozzáférést. Ezután tekintse át a felhasználók és a rendszergazdák által az alkalmazás számára biztosított engedélyeket.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) globális rendszergazdaként, alkalmazás-rendszergazdaként vagy Felhőbeli alkalmazás-rendszergazdaként.
3. Válassza **Azure Active Directory**  >  **vállalati alkalmazások** lehetőséget.
5. Válassza ki azt az alkalmazást, amelyhez korlátozni kívánja a hozzáférést.
6. Válassza a **Tulajdonságok** lehetőséget, majd állítsa az **Igen** értékre a **felhasználói követelményt** .
7. Válassza az **engedélyek** lehetőséget, és tekintse át a rendszergazda és a felhasználó beleegyezéses engedélyeit.

Igény szerint a PowerShell használatával a következőket teheti:

- Távolítsa el az összes hozzárendelt felhasználót, hogy ne jelentkezzen be az alkalmazásba.
- Az alkalmazáshoz hozzáféréssel rendelkező felhasználók frissítési jogkivonatának érvénytelenítése.
- Az alkalmazás összes engedélyének visszavonása.

Vagy letilthatja az alkalmazást a felhasználók hozzáférésének letiltásához és az alkalmazás hozzáférésének leállításához az adataihoz.


## <a name="disable-a-malicious-application"></a>Rosszindulatú alkalmazás letiltása 

Javasoljuk, hogy tiltsa le az alkalmazást, hogy letiltsa a felhasználók hozzáférését, és hogy az alkalmazás hozzáférjen az adatokhoz. Ha ehelyett törli az alkalmazást, akkor a felhasználók újra megadhatják az alkalmazáshoz, és hozzáférést biztosíthatnak az adataihoz.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) globális rendszergazdaként, alkalmazás-rendszergazdaként vagy Felhőbeli alkalmazás-rendszergazdaként.
2. Válassza **Azure Active Directory**  >  **vállalati alkalmazások** lehetőséget.
3. Válassza ki azt az alkalmazást, amelyhez korlátozni kívánja a hozzáférést.
4. Válassza a **Tulajdonságok** lehetőséget, majd másolja az objektumazonosítót.

### <a name="powershell-commands"></a>PowerShell-parancsok


A szolgáltatásnév-objektum AZONOSÍTÓjának beolvasása.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) globális rendszergazdaként, alkalmazás-rendszergazdaként vagy Felhőbeli alkalmazás-rendszergazdaként.
2. Válassza **Azure Active Directory**  >  **vállalati alkalmazások** lehetőséget.
3. Válassza ki azt az alkalmazást, amelyhez korlátozni kívánja a hozzáférést.
4. Válassza a **Tulajdonságok** lehetőséget, majd másolja az objektumazonosítót.

```powershell
$sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
$sp.ObjectId
```
Az alkalmazáshoz hozzárendelt összes felhasználó eltávolítása.
 ```powershell
Connect-AzureAD

# Get Service Principal using objectId
$sp = Get-AzureADServicePrincipal -ObjectId "<ServicePrincipal objectID>"

# Get Azure AD App role assignments using objectId of the Service Principal
$assignments = Get-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId -All $true

# Remove all users and groups assigned to the application
$assignments | ForEach-Object {
    if ($_.PrincipalType -eq "User") {
        Remove-AzureADUserAppRoleAssignment -ObjectId $_.PrincipalId -AppRoleAssignmentId $_.ObjectId
    } elseif ($_.PrincipalType -eq "Group") {
        Remove-AzureADGroupAppRoleAssignment -ObjectId $_.PrincipalId -AppRoleAssignmentId $_.ObjectId
    }
}
 ```

Az alkalmazásnak biztosított engedélyek visszavonása.

```powershell
Connect-AzureAD

# Get Service Principal using objectId
$sp = Get-AzureADServicePrincipal -ObjectId "<ServicePrincipal objectID>"

# Get all delegated permissions for the service principal
$spOAuth2PermissionsGrants = Get-AzureADOAuth2PermissionGrant -All $true| Where-Object { $_.clientId -eq $sp.ObjectId }

# Remove all delegated permissions
$spOAuth2PermissionsGrants | ForEach-Object {
    Remove-AzureADOAuth2PermissionGrant -ObjectId $_.ObjectId
}

# Get all application permissions for the service principal
$spApplicationPermissions = Get-AzureADServiceAppRoleAssignedTo -ObjectId $sp.ObjectId -All $true | Where-Object { $_.PrincipalType -eq "ServicePrincipal" }

# Remove all delegated permissions
$spApplicationPermissions | ForEach-Object {
    Remove-AzureADServiceAppRoleAssignment -ObjectId $_.PrincipalId -AppRoleAssignmentId $_.objectId
}
```
A frissítési tokenek érvénytelenítése.
```powershell
Connect-AzureAD

# Get Service Principal using objectId
$sp = Get-AzureADServicePrincipal -ObjectId "<ServicePrincipal objectID>"

# Get Azure AD App role assignments using objectID of the Service Principal
$assignments = Get-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId -All $true | Where-Object {$_.PrincipalType -eq "User"}

# Revoke refresh token for all users assigned to the application
$assignments | ForEach-Object {
    Revoke-AzureADUserAllRefreshToken -ObjectId $_.PrincipalId
}
```
## <a name="next-steps"></a>Következő lépések
- [Az alkalmazások beleegyezett az alkalmazásokkal és a beleegyezikés kiértékelésével](manage-consent-requests.md)
- [Felhasználói beleegyezés konfigurálása](configure-user-consent.md)
- [Rendszergazdai engedélyezési munkafolyamat konfigurálása](configure-admin-consent-workflow.md)
