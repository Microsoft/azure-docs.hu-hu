---
title: Vállalati alkalmazás elrejtése az Azure AD felhasználói felületén
description: Vállalati alkalmazás elrejtése a felhasználói élmény elől a hozzáférési panelek Azure Active Directory indító Microsoft 365 számára.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 03/25/2020
ms.author: iangithinji
ms.reviewer: kasimpso
ms.collection: M365-identity-device-management
ms.openlocfilehash: a90f3e3aeb1d68c6c6e6eeea29c04ff7880dccd3
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374199"
---
# <a name="hide-enterprise-applications-from-end-users-in-azure-active-directory"></a>Vállalati alkalmazások elrejtése a végfelhasználók elől a Azure Active Directory

Útmutatás az alkalmazások elrejtéséhez a végfelhasználók MyApps paneljére vagy Microsoft 365 indítóján. Ha egy alkalmazás rejtett, a felhasználók továbbra is engedélyekkel rendelkezik az alkalmazáshoz. 

## <a name="prerequisites"></a>Előfeltételek

alkalmazás-rendszergazda szükséges, hogy elrejtsen egy alkalmazást a MyApps panelen, és Microsoft 365 indítóját.

globális rendszergazda összes alkalmazás elrejtéséhez Microsoft 365 jogosultságok szükségesek.


## <a name="hide-an-application-from-the-end-user"></a>Alkalmazás elrejtése a végfelhasználó elől
Az alábbi lépésekkel elrejthet egy alkalmazást a MyApps panelen, és Microsoft 365 alkalmazásindítót.

1.  Jelentkezzen be a [Azure Portal](https://portal.azure.com) a címtár globális rendszergazdájaként.
2.  Válassza a **Azure Active Directory** lehetőséget.
3.  Válassza a **Vállalati alkalmazások lehetőséget.** Megnyílik **a Vállalati alkalmazások – Minden alkalmazás** panel.
4.  Az **Alkalmazás típusa alatt** válassza a Vállalati **alkalmazások** lehetőséget, ha még nincs kiválasztva.
5.  Keresse meg az elrejteni kívánt alkalmazást, és kattintson az alkalmazásra.  Megnyílik az alkalmazás áttekintése.
6.  Kattintson a **Tulajdonságok** elemre. 
7.  A **Felhasználók számára látható kérdés esetén kattintson** a Nem **gombra.**
8.  Kattintson a **Mentés** gombra.

> [!NOTE]
> Ezek az utasítások csak vállalati alkalmazásokra vonatkoznak.

## <a name="use-azure-ad-powershell-to-hide-an-application"></a>Alkalmazás elrejtése az Azure AD PowerShell használatával

Ha el szeretne rejteni egy alkalmazást a MyApps panelen, manuálisan hozzáadhatja az HideApp címkét az alkalmazás szolgáltatásnévhez. Futtassa a következő [AzureAD PowerShell-parancsokat,](/powershell/module/azuread/#service_principals) hogy az alkalmazás **Visible** tulajdonságát a Felhasználók? tulajdonságra állítsa **a No (Nem) beállításra.** 

```PowerShell
Connect-AzureAD

$objectId = "<objectId>"
$servicePrincipal = Get-AzureADServicePrincipal -ObjectId $objectId
$tags = $servicePrincipal.tags
$tags += "HideApp"
Set-AzureADServicePrincipal -ObjectId $objectId -Tags $tags
```

## <a name="hide-microsoft-365-applications-from-the-myapps-panel"></a>Alkalmazások Microsoft 365 a MyApps panelen

Az alábbi lépésekkel elrejtheti az összes Microsoft 365 a MyApps panelen. Az alkalmazások továbbra is láthatók az Office 365 portálon.

1.  Jelentkezzen be a [Azure Portal](https://portal.azure.com) a címtár globális rendszergazdájaként.
2.  Válassza a **Azure Active Directory** lehetőséget.
3.  Válassza a **Felhasználók** lehetőséget.
4.  Válassza a **Felhasználói beállítások** elemet.
5.  A **Vállalati alkalmazások alatt** kattintson a Manage how end users launch and view their applications (A végfelhasználók **alkalmazásindítási és -megtekintési mikéntének kezelése) elemre.**
6.  A Felhasználók csak az Office 365-alkalmazásokat láthatják **az Office 365 portálon,** kattintson az Igen **gombra.**
7.  Kattintson a **Mentés** gombra.

## <a name="next-steps"></a>Következő lépések
* [Az összes saját csoport](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Felhasználó vagy csoport hozzárendelése vállalati alkalmazáshoz](assign-user-or-group-access-portal.md)
* [Felhasználó- vagy csoport-hozzárendelés eltávolítása vállalati alkalmazásból](./assign-user-or-group-access-portal.md)
* [Vállalati alkalmazás nevének vagy emblémájának módosítása](./add-application-portal-configure.md)
