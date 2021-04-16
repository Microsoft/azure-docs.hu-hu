---
title: Felhasználói és rendszergazdai engedélyek kezelése – Azure Active Directory | Microsoft Docs
description: Ismerje meg, hogyan lehet áttekintni és kezelni az alkalmazás engedélyeinek az Azure AD-ban való kezelését. Visszavonhatja például az alkalmazásnak megadott összes engedélyt.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 7/10/2020
ms.author: iangithinji
ms.reviewer: luleonpla
ms.collection: M365-identity-device-management
ms.openlocfilehash: bcd137030e4e1f3e88f47ec5ba78b3bde08fe068
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107373978"
---
# <a name="take-action-on-overprivileged-or-suspicious-applications-in-azure-active-directory"></a>Művelet a túlprivilegizált vagy gyanús alkalmazásokon a Azure Active Directory

Útmutató az alkalmazásengedélyek áttekintéséhez és kezeléséhez. Ez a cikk a forgatókönyvnek megfelelően különböző műveleteket tartalmaz az alkalmazás biztonságának biztosítása érdekében. Ezek a műveletek minden olyan alkalmazásra vonatkoznak, amely felhasználói vagy rendszergazdai jóváhagyással Azure Active Directory hozzá az Azure AD-bérlőhöz.

Az alkalmazásokhoz való hozzájárulással kapcsolatos további információkért lásd: Azure Active Directory [hozzájárulási keretrendszer.](../develop/consent-framework.md)

## <a name="prerequisites"></a>Előfeltételek

A következő műveletekhez globális rendszergazdaként, alkalmazás-rendszergazdaként vagy felhőalkalmazás-rendszergazdaként kell bejelentkeznie.

Az alkalmazásokhoz való hozzáférés korlátozásához felhasználói hozzárendelésre van szükség, majd felhasználókat vagy csoportokat kell hozzárendelni az alkalmazáshoz.  További információ: Felhasználók és csoportok [hozzárendelésének módszerei.](./assign-user-or-group-access-portal.md)

Az Azure AD-portálon környezeti PowerShell-szkripteket kaphat a műveletek végrehajtásához.
 
1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) globális rendszergazdaként, alkalmazás-rendszergazdaként vagy felhőalkalmazás-rendszergazdaként.
2. Válassza **Azure Active Directory**  >  **Vállalati alkalmazások lehetőséget.**
3. Válassza ki azt az alkalmazást, amely számára korlátozni szeretné a hozzáférést.
4. Válassza az **Engedélyek** lehetőséget. A parancssávon válassza az **Engedélyek áttekintése lehetőséget.**

![Az Engedélyek áttekintése ablak képernyőképe.](./media/manage-application-permissions/review-permissions.png)


## <a name="control-access-to-an-application"></a>Alkalmazáshoz való hozzáférés szabályozása

Javasoljuk, hogy korlátozza az alkalmazáshoz való hozzáférést a Felhasználó-hozzárendelés **beállítás bekapcsolásával.**

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) globális rendszergazdaként, alkalmazás-rendszergazdaként vagy felhőalkalmazás-rendszergazdaként.
2. Válassza **Azure Active Directory**  >  **vállalati alkalmazások lehetőséget.**
3. Válassza ki azt az alkalmazást, amely számára korlátozni szeretné a hozzáférést.
4. Válassza **a Tulajdonságok** lehetőséget, majd állítsa a Felhasználói **követelményt Yes** **(Igen) beállításra.**
5. Válassza **a Felhasználó és csoportok** lehetőséget, majd távolítsa el az alkalmazáshoz rendelt nemkívánatos felhasználókat.
6. Felhasználók vagy csoportok hozzárendelése az alkalmazáshoz.

A PowerShell használatával az alkalmazáshoz rendelt összes felhasználót is eltávolíthatja.

## <a name="revoke-all-permissions-for-an-application"></a>Egy alkalmazás összes engedélyének visszavonása

A PowerShell-szkript használata visszavonja az alkalmazáshoz megadott összes engedélyt.

> [!NOTE]
> Az aktuálisan megadott engedély visszavonása nem fogja megakadályozni, hogy a felhasználók újra hozzájárulnak az alkalmazáshoz. Ha szeretné letiltani a felhasználók beleegyezését, olvassa el a következőt: Configure how users consent to applications (Annak konfigurálása, hogy a [felhasználók hogyan járulnak hozzá az alkalmazásokhoz).](configure-user-consent.md)

Ha szeretné, letilthatja az alkalmazást, így a felhasználók nem férhetnek hozzá az alkalmazáshoz, és nem is férhetnek hozzá az adataihoz.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) globális rendszergazdaként, alkalmazás-rendszergazdaként vagy felhőalkalmazás-rendszergazdaként.
2. Válassza **Azure Active Directory**  >  **vállalati alkalmazások lehetőséget.**
3. Válassza ki azt az alkalmazást, amely számára korlátozni szeretné a hozzáférést.
4. Válassza **a Tulajdonságok** lehetőséget, majd állítsa a Felhasználók **bejelentkeznek?** beállítást Nem **lehetőségre.**

## <a name="investigate-a-suspicious-application"></a>Gyanús alkalmazás kivizsgálása

Javasoljuk, hogy korlátozza az alkalmazáshoz való hozzáférést a Felhasználó-hozzárendelés **beállítás bekapcsolásával.** Ezután tekintse át a felhasználók és rendszergazdák által az alkalmazásnak megadott engedélyeket.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) globális rendszergazdaként, alkalmazás-rendszergazdaként vagy felhőalkalmazás-rendszergazdaként.
3. Válassza **Azure Active Directory**  >  **vállalati alkalmazások lehetőséget.**
5. Válassza ki azt az alkalmazást, amely számára korlátozni szeretné a hozzáférést.
6. Válassza **a Tulajdonságok** lehetőséget, majd állítsa a Felhasználói követelmény **beállítását** Igen **lehetőségre.**
7. Válassza **az Engedélyek lehetőséget,** és tekintse át a rendszergazdai és a felhasználó által megadott engedélyeket.

A PowerShell használatával a következőt is használhatja:

- Távolítsa el az összes hozzárendelt felhasználót, hogy ne jelentkeztethető be az alkalmazásba.
- Érvényteleníti az alkalmazáshoz hozzáférő felhasználók frissítési jogkivonatát.
- Vonja vissza az alkalmazás összes engedélyét.

Vagy letilthatja az alkalmazást a felhasználók hozzáférésének letiltásához és az adatokhoz való hozzáférésének letiltásához.


## <a name="disable-a-malicious-application"></a>Rosszindulatú alkalmazás letiltása 

Javasoljuk, hogy tiltsa le az alkalmazást, hogy letiltsa a felhasználók hozzáférését, és megakadályozza, hogy az alkalmazás hozzáférjen az adataihoz. Ha ehelyett törli az alkalmazást, a felhasználók újra beleegyeznek az alkalmazásba, és hozzáférést adhatnak az adatokhoz.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) globális rendszergazdaként, alkalmazás-rendszergazdaként vagy felhőalkalmazás-rendszergazdaként.
2. Válassza **Azure Active Directory**  >  **vállalati alkalmazások lehetőséget.**
3. Válassza ki azt az alkalmazást, amely számára korlátozni szeretné a hozzáférést.
4. Válassza **a Tulajdonságok** lehetőséget, majd másolja ki az objektumazonosítót.

### <a name="powershell-commands"></a>PowerShell-parancsok


A szolgáltatásnév objektumazonosítójának lekérése.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) globális rendszergazdaként, alkalmazás-rendszergazdaként vagy felhőalkalmazás-rendszergazdaként.
2. Válassza **Azure Active Directory**  >  **Vállalati alkalmazások lehetőséget.**
3. Válassza ki azt az alkalmazást, amely számára korlátozni szeretné a hozzáférést.
4. Válassza **a Tulajdonságok** lehetőséget, majd másolja ki az objektumazonosítót.

```powershell
$sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
$sp.ObjectId
```
Távolítsa el az alkalmazáshoz rendelt összes felhasználót.
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

Vonja vissza az alkalmazásnak megadott engedélyeket.

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
Érvényteleníti a frissítési jogkivonatokat.
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
- [Az alkalmazásokhoz való hozzájárulás kezelése és a hozzájárulási kérelem kiértékelése](manage-consent-requests.md)
- [Felhasználói beleegyezés konfigurálása](configure-user-consent.md)
- [Rendszergazdai jóváhagyás munkafolyamatának konfigurálása](configure-admin-consent-workflow.md)
