---
title: Engedélyek besorolásának konfigurálása az Azure AD-vel
description: Megtudhatja, hogyan kezelheti a delegált engedélyek besorolásait.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 06/01/2020
ms.author: phsignor
ms.reviewer: arvindh, luleon, phsignor
ms.openlocfilehash: 0add5001e2e62e26d448b06927210c14f17729f1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91804313"
---
# <a name="configure-permission-classifications"></a>Engedélyosztályozások konfigurálása

Az engedélyek besorolása lehetővé teszi annak a meghatározását, hogy a különböző engedélyek milyen hatással vannak a szervezet szabályzatai és a kockázatértékelések alapján. Az engedélyezési házirendekben szereplő engedélyek besorolásával például azonosíthatja azokat az engedélyeket, amelyeket a felhasználók jóváhagynak.

## <a name="manage-permission-classifications"></a>Engedélyek besorolásának kezelése

Jelenleg csak az "alacsony hatású" engedélyek besorolása támogatott. Csak a rendszergazdai jogosultságokat nem igénylő delegált engedélyek besorolása "alacsony hatás" lehet.

> [!TIP]
> Az alapszintű bejelentkezéshez szükséges minimális engedélyek a következők:,, `openid` `profile` `email` `User.Read` és `offline_access` , amelyek minden delegált engedély a Microsoft Graph. Ezekkel az engedélyekkel az alkalmazás beolvashatja a bejelentkezett felhasználó teljes profiljának részleteit, és akkor is karbantarthatja ezt a hozzáférést, ha a felhasználó már nem használja az alkalmazást.

# <a name="portal"></a>[Portál](#tab/azure-portal)

Az alábbi lépéseket követve osztályozhatja az engedélyeket a Azure Portal használatával:

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) [globális rendszergazdaként](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator).
1. Válassza **Azure Active Directory**  >  **vállalati alkalmazások**  >  **hozzájárulása és engedélyek**  >  **besorolása**lehetőséget.
1. Válassza az **engedélyek hozzáadása** lehetőséget, hogy egy másik engedélyt "alacsony hatásként" minősítse.
1. Válassza ki az API-t, majd válassza ki a delegált engedélyeket (ka) t.

Ebben a példában az egyszeri bejelentkezéshez minimálisan szükséges engedélyt soroltuk be:

:::image type="content" source="media/configure-permission-classifications/permission-classifications.png" alt-text="Engedélyek besorolása":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Az engedélyek besorolásához használhatja a legújabb Azure AD PowerShell előzetes [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true)-modult is. Az engedélyek besorolása az API **ServicePrincipal** objektumán van konfigurálva, amely közzéteszi az engedélyeket.

#### <a name="list-the-current-permission-classifications-for-an-api"></a>Egy API aktuális engedélyezési besorolásának listázása

1. Az API **ServicePrincipal** objektumának beolvasása. Itt beolvasjuk a Microsoft Graph API ServicePrincipal objektumát:

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. Olvassa el az API delegált jogosultsági besorolását:

   ```powershell
   Get-AzureADMSServicePrincipalDelegatedPermissionClassification `
       -ServicePrincipalId $api.ObjectId | Format-Table Id, PermissionName, Classification
   ```

#### <a name="classify-a-permission-as-low-impact"></a>Engedélyek osztályozása "kis hatás"

1. Az API **ServicePrincipal** objektumának beolvasása. Itt beolvasjuk a Microsoft Graph API ServicePrincipal objektumát:

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. Keresse meg a minősíteni kívánt delegált engedélyt:

   ```powershell
   $delegatedPermission = $api.OAuth2Permissions | Where-Object { $_.Value -eq "User.ReadBasic.All" }
   ```

1. Állítsa be az engedélyek besorolását az engedély neve és azonosítója alapján:

   ```powershell
   Add-AzureADMSServicePrincipalDelegatedPermissionClassification `
      -ServicePrincipalId $api.ObjectId `
      -PermissionId $delegatedPermission.Id `
      -PermissionName $delegatedPermission.Value `
      -Classification "low"
   ```

#### <a name="remove-a-delegated-permission-classification"></a>Delegált engedélyek besorolásának eltávolítása

1. Az API **ServicePrincipal** objektumának beolvasása. Itt beolvasjuk a Microsoft Graph API ServicePrincipal objektumát:

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. Keresse meg az eltávolítani kívánt delegált engedélyek besorolását:

   ```powershell
   $classifications = Get-AzureADMSServicePrincipalDelegatedPermissionClassification `
       -ServicePrincipalId $api.ObjectId
   $classificationToRemove = $classifications | Where-Object {$_.PermissionName -eq "User.ReadBasic.All"}
   ```

1. Az engedélyek besorolásának törlése:

   ```powershell
   Remove-AzureADMSServicePrincipalDelegatedPermissionClassification `
       -ServicePrincipalId $api.ObjectId `
       -Id $classificationToRemove.Id
   ```

---

## <a name="next-steps"></a>Következő lépések

További tudnivalók:

* [Felhasználói beleegyező beállítások konfigurálása](configure-user-consent.md)
* [Rendszergazdai engedélyezési munkafolyamat konfigurálása](configure-admin-consent-workflow.md)
* [Megtudhatja, hogyan kezelheti az alkalmazásokra vonatkozó beleegyezett, és hogyan értékelheti a hozzájárulásukat](manage-consent-requests.md)
* [Bérlőszintű rendszergazdai jóváhagyás megadása egy alkalmazáshoz](grant-admin-consent.md)
* [Engedélyek és beleegyezett a Microsoft Identity platform](../develop/active-directory-v2-scopes.md)

Segítség kérése vagy válaszok keresése a kérdéseire:
* [Azure AD a StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
