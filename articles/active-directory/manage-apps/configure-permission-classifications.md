---
title: Engedélybesorolások konfigurálása az Azure AD-val
description: Útmutató a delegált engedélybesorolások kezeléséhez.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 06/01/2020
ms.author: iangithinji
ms.reviewer: arvindh, luleon, phsignor
ms.custom: contperf-fy21q2
ms.openlocfilehash: be58f5cd18d32302d1e92f00afb7d7e0aae09410
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374471"
---
# <a name="configure-permission-classifications"></a>Engedélyosztályozások konfigurálása

Az engedélybesorolások lehetővé teszik annak azonosítását, hogy a különböző engedélyek milyen hatással vannak a szervezet szabályzatai és kockázatértékelései alapján. A hozzájárulási szabályzatok engedélybesorolásai segítségével például azonosíthatja azokat az engedélyeket, amelyekhez a felhasználók engedélyt kaphatnak.

## <a name="manage-permission-classifications"></a>Engedélybesorolások kezelése

Jelenleg csak az "Alacsony hatás" engedélybesorolás támogatott. Csak a rendszergazdai jóváhagyást nem igénylő delegált engedélyeket lehet "Alacsony hatásúként" besorolni.

> [!TIP]
> Az alapszintű bejelentkezéshez minimálisan szükséges engedélyek: , , és , amelyek mind delegált engedélyek a `openid` `profile` `email` `User.Read` `offline_access` Microsoft Graph. Ezekkel az engedélyekkel az alkalmazás elolvashatja a bejelentkezett felhasználó teljes profiladatokat, és akkor is fenntarthatja ezt a hozzáférést, ha a felhasználó már nem használja az alkalmazást.

# <a name="portal"></a>[Portál](#tab/azure-portal)

A következő lépésekkel osztályozhatja az engedélyeket a Azure Portal:

1. Jelentkezzen be [](https://portal.azure.com) a Azure Portal globális [](../roles/permissions-reference.md#global-administrator)rendszergazdaként, [](../roles/permissions-reference.md#application-administrator)alkalmazás-rendszergazdaként vagy [felhőalkalmazás-rendszergazdaként](../roles/permissions-reference.md#cloud-application-administrator)
1. Válassza **Azure Active Directory**  >  **Vállalati alkalmazások Hozzájárulás** és  >  **engedélyek**  >  **Engedélybesorolások lehetőséget.**
1. Válassza **az Engedélyek hozzáadása lehetőséget** egy másik engedély "Alacsony hatás" besorolásához.
1. Válassza ki az API-t, majd válassza ki a delegált engedélyeket.

Ebben a példában az egyszeri bejelentkezéshez szükséges minimális engedélykészletet soroljuk be:

:::image type="content" source="media/configure-permission-classifications/permission-classifications.png" alt-text="Engedélybesorolások":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Az engedélyek besorolásához használhatja az Azure AD PowerShell előzetes verzió legújabb modulját, az [AzureADPreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview)modult. Az engedélybesorolások az engedélyeket közzétesző API **ServicePrincipal** objektumán vannak konfigurálva.

#### <a name="list-the-current-permission-classifications-for-an-api"></a>Egy API aktuális engedélybesorolásának felsorolása

1. Az API **ServicePrincipal** objektumának lekérése. Itt lekérjük a ServicePrincipal objektumot a Microsoft Graph API-hoz:

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. Olvassa el az API delegált engedélybesorolását:

   ```powershell
   Get-AzureADMSServicePrincipalDelegatedPermissionClassification `
       -ServicePrincipalId $api.ObjectId | Format-Table Id, PermissionName, Classification
   ```

#### <a name="classify-a-permission-as-low-impact"></a>Engedélyek besorolása "Alacsony hatás" besorolásra

1. Az API **ServicePrincipal** objektumának lekérése. Itt a ServicePrincipal objektumot kérjük le a Microsoft Graph API-hoz:

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. Keresse meg azt a delegált engedélyt, amelybe a besorolást szeretné elvégezni:

   ```powershell
   $delegatedPermission = $api.OAuth2Permissions | Where-Object { $_.Value -eq "User.ReadBasic.All" }
   ```

1. Állítsa be az engedélybesorolást az engedély nevével és azonosítójával:

   ```powershell
   Add-AzureADMSServicePrincipalDelegatedPermissionClassification `
      -ServicePrincipalId $api.ObjectId `
      -PermissionId $delegatedPermission.Id `
      -PermissionName $delegatedPermission.Value `
      -Classification "low"
   ```

#### <a name="remove-a-delegated-permission-classification"></a>Delegált engedélybesorolás eltávolítása

1. Az API **ServicePrincipal** objektumának lekérése. Itt lekérjük a ServicePrincipal objektumot a Microsoft Graph API-hoz:

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. Keresse meg az eltávolítani kívánt delegált engedélybesorolást:

   ```powershell
   $classifications = Get-AzureADMSServicePrincipalDelegatedPermissionClassification `
       -ServicePrincipalId $api.ObjectId
   $classificationToRemove = $classifications | Where-Object {$_.PermissionName -eq "User.ReadBasic.All"}
   ```

1. Törölje az engedélybesorolást:

   ```powershell
   Remove-AzureADMSServicePrincipalDelegatedPermissionClassification `
       -ServicePrincipalId $api.ObjectId `
       -Id $classificationToRemove.Id
   ```

---

## <a name="next-steps"></a>Következő lépések

További tudnivalók:

* [Felhasználói hozzájárulási beállítások konfigurálása](configure-user-consent.md)
* [A rendszergazdai jóváhagyás munkafolyamatának konfigurálása](configure-admin-consent-workflow.md)
* [Útmutató az alkalmazásokhoz való hozzájárulás kezeléséhez és a hozzájárulási kérelmek kiértékeléséhez](manage-consent-requests.md)
* [Bérlőszintű rendszergazdai jóváhagyás megadása egy alkalmazáshoz](grant-admin-consent.md)
* [Engedélyek és jóváhagyás a Microsoft identitásplatformján](../develop/v2-permissions-and-consent.md)

Segítségért vagy a kérdéseire adott válaszokért:
* [Azure AD a Microsoft Q&A-n](/answers/topics/azure-active-directory.html)