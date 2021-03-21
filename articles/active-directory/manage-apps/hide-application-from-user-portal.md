---
title: Vállalati alkalmazás elrejtése a felhasználó Azure AD-beli felhasználói felületéről
description: Vállalati alkalmazások elrejtése Azure Active Directory hozzáférési panelek vagy Microsoft 365-indítók felhasználói felületéről.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 03/25/2020
ms.author: kenwith
ms.reviewer: kasimpso
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8469b48b92f3f9a645a0c05441e6c1943b02e16f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99258880"
---
# <a name="hide-enterprise-applications-from-end-users-in-azure-active-directory"></a>Vállalati alkalmazások elrejtése a Azure Active Directoryban lévő végfelhasználók számára

Útmutató a végfelhasználók MyApps paneljén vagy Microsoft 365 Indítóban található alkalmazások elrejtéséhez. Ha egy alkalmazás rejtve van, a felhasználók továbbra is rendelkeznek engedéllyel az alkalmazáshoz. 

## <a name="prerequisites"></a>Előfeltételek

Alkalmazás-rendszergazdai jogosultságok szükségesek az alkalmazások elrejtéséhez a MyApps panelen és a Microsoft 365 Indítóban.

Az összes Microsoft 365 alkalmazás elrejtéséhez globális rendszergazdai jogosultságok szükségesek.


## <a name="hide-an-application-from-the-end-user"></a>Alkalmazás elrejtése a végfelhasználótól
A következő lépésekkel rejtheti el az alkalmazást a MyApps panelen, és Microsoft 365 az Application Launcher alkalmazást.

1.  Jelentkezzen be a [Azure Portalra](https://portal.azure.com) globális rendszergazdaként a címtárában.
2.  Válassza a **Azure Active Directory** lehetőséget.
3.  Válassza a **vállalati alkalmazások** lehetőséget. A **vállalati alkalmazások – minden alkalmazás** panel nyílik meg.
4.  Az **alkalmazás típusa** területen válassza a **vállalati alkalmazások** lehetőséget, ha még nincs kiválasztva.
5.  Keresse meg az elrejteni kívánt alkalmazást, majd kattintson az alkalmazásra.  Megnyílik az alkalmazás áttekintése.
6.  Kattintson a **Tulajdonságok** elemre. 
7.  A **látható felhasználók számára?** kérdés, kattintson a **nem** gombra.
8.  Kattintson a **Mentés** gombra.

> [!NOTE]
> Ezek az utasítások csak a vállalati alkalmazásokra érvényesek.

## <a name="use-azure-ad-powershell-to-hide-an-application"></a>Alkalmazások elrejtése az Azure AD PowerShell használatával

Ha el szeretné rejteni egy alkalmazást a MyApps panelen, manuálisan is hozzáadhatja az HideApp címkét az alkalmazás egyszerű szolgáltatásához. Futtassa az alábbi [AzureAD PowerShell](/powershell/module/azuread/#service_principals) -parancsokat, hogy az alkalmazás **látható legyen a felhasználók számára?** tulajdonság értéke **nem**. 

```PowerShell
Connect-AzureAD

$objectId = "<objectId>"
$servicePrincipal = Get-AzureADServicePrincipal -ObjectId $objectId
$tags = $servicePrincipal.tags
$tags += "HideApp"
Set-AzureADServicePrincipal -ObjectId $objectId -Tags $tags
```

## <a name="hide-microsoft-365-applications-from-the-myapps-panel"></a>Microsoft 365 alkalmazások elrejtése a MyApps panelen

A következő lépésekkel elrejtheti az összes Microsoft 365 alkalmazást a MyApps panelen. Az alkalmazások továbbra is láthatók az Office 365-portálon.

1.  Jelentkezzen be a [Azure Portalra](https://portal.azure.com) globális rendszergazdaként a címtárában.
2.  Válassza a **Azure Active Directory** lehetőséget.
3.  Válassza a **Felhasználók** lehetőséget.
4.  Válassza a **Felhasználói beállítások** elemet.
5.  A **vállalati alkalmazások** területen kattintson **a kezelés a végfelhasználók általi indítás és az alkalmazások megtekintése** elemre.
6.  A **felhasználók csak az office 365-alkalmazásokat látják az office 365-portálon**, kattintson az **Igen** gombra.
7.  Kattintson a **Mentés** gombra.

## <a name="next-steps"></a>Következő lépések
* [Összes saját csoport megjelenítése](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Felhasználó vagy csoport társítása vállalati alkalmazáshoz](assign-user-or-group-access-portal.md)
* [Felhasználó vagy csoport hozzárendelésének eltávolítása vállalati alkalmazásból](./assign-user-or-group-access-portal.md)
* [Vállalati alkalmazás nevének vagy emblémájának módosítása](./add-application-portal-configure.md)
