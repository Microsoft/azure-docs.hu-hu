---
title: Vállalati alkalmazások felhasználói bejelentkezésének letiltása az Azure AD-ban
description: Vállalati alkalmazás letiltása, hogy egyetlen felhasználó se jelentkeztethet be a Azure Active Directory
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 04/12/2019
ms.author: iangithinji
ms.reviewer: asteen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: b9af580ef8b3691a5f188ac15069dda00a5f5802
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374250"
---
# <a name="disable-user-sign-ins-for-an-enterprise-app-in-azure-active-directory"></a>Vállalati alkalmazások felhasználói bejelentkezésének letiltása a Azure Active Directory

A vállalati alkalmazások egyszerűen letilthatóak, így egyetlen felhasználó sem tud bejelentkezni az Azure Active Directory (Azure AD) szolgáltatásba. A vállalati alkalmazás kezeléséhez megfelelő engedélyekre van szüksége. A címtár globális rendszergazdának kell lennie.

## <a name="how-do-i-disable-user-sign-ins"></a>Hogyan a felhasználói bejelentkezéseket?

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) egy olyan fiókkal, amely a címtár globális rendszergazdája.
1. Válassza **a Minden szolgáltatás** lehetőséget, Azure Active Directory a szövegmezőbe, majd válassza az Enter **billentyűt.** 
1. A **Azure Active Directory**  -   **__ panelen_*(azaz*** a felügyeletben található címtár Azure AD panelje) válassza a _ Vállalati alkalmazások lehetőséget.
1. A Vállalati **alkalmazások – Minden alkalmazás panelen** megjelenik a felügyelheti alkalmazások listája. Jelöljön ki egy alkalmazást.
1. Az ***alkalmazásnév** _ panelen (azaz a címben a kiválasztott alkalmazás nevével jelölt panelen) válassza a _*Properties** lehetőséget.
1. Az ***alkalmazásnév** _ - _ *Tulajdonságok** panelen válassza a **Nem** lehetőséget, ha a felhasználók bejelentkeznek? beállításnál válassza a Nem **lehetőséget.**
1. Válassza a **Mentés** parancsot.

## <a name="use-azure-ad-powershell-to-disable-an-unlisted-app"></a>Nem listázó alkalmazás letiltása az Azure AD PowerShell használatával

Ha ismeri egy olyan alkalmazás AppId-ját, amely nem jelenik meg a Vállalati alkalmazások listájában (például azért, mert törölte az alkalmazást vagy a szolgáltatásnév még nem lett létrehozva, mert az alkalmazást a Microsoft előzetesen engedélyezte), manuálisan létrehozhatja az alkalmazás szolgáltatását, majd letilthatja az [AzureAD PowerShell-parancsmag használatával.](/powershell/module/azuread/New-AzureADServicePrincipal)

```PowerShell
# The AppId of the app to be disabled
$appId = "{AppId}"

# Check if a service principal already exists for the app
$servicePrincipal = Get-AzureADServicePrincipal -Filter "appId eq '$appId'"
if ($servicePrincipal) {
    # Service principal exists already, disable it
    Set-AzureADServicePrincipal -ObjectId $servicePrincipal.ObjectId -AccountEnabled $false
} else {
    # Service principal does not yet exist, create it and disable it at the same time
    $servicePrincipal = New-AzureADServicePrincipal -AppId $appId -AccountEnabled $false
}
```

## <a name="next-steps"></a>Következő lépések

* [Az összes saját csoport](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Felhasználó vagy csoport hozzárendelése vállalati alkalmazáshoz](assign-user-or-group-access-portal.md)
* [Felhasználó- vagy csoport-hozzárendelés eltávolítása vállalati alkalmazásból](./assign-user-or-group-access-portal.md)
* [Vállalati alkalmazás nevének vagy emblémájának módosítása](./add-application-portal-configure.md)
