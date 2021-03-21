---
title: Alkalmazás-engedélyezési szabályzatok kezelése az Azure AD-ben
description: Megtudhatja, hogyan kezelheti a belefoglalt és az egyéni alkalmazás-engedélyezési szabályzatokat a beleegyező engedélyek megadásának szabályozásához.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 06/01/2020
ms.author: kenwith
ms.reviewer: arvindh, luleon, phsignor
ms.custom: contperf-fy21q2
ms.openlocfilehash: 9c269e2ab37a08e48eedd3ee468080a382f9a8e3
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102558729"
---
# <a name="manage-app-consent-policies"></a>Alkalmazás-hozzájárulási szabályzatok kezelése

Az Azure AD PowerShell használatával megtekintheti és kezelheti az alkalmazás-engedélyezési szabályzatokat.

Az alkalmazás-engedélyezési szabályzat egy vagy több "include", illetve nulla vagy több "kizárás" típusú készletből áll. Ahhoz, hogy egy esemény figyelembe legyen véve egy alkalmazás-engedélyezési szabályzatban, meg kell egyeznie *legalább* egy "belefoglalt" feltételsel, és *nem egyezhet meg a "* kizárások" állapottal.

Mindegyik feltétel több feltételből áll. Ahhoz, hogy egy esemény megfeleljen egy adott feltételnek, a feltételben megadott *összes* feltételnek teljesülnie kell.

Azok az alkalmazás-engedélyezési házirendek, amelyekben az azonosító a "Microsoft-" előtaggal kezdődik, beépített szabályzatok. Ezen beépített szabályzatok némelyikét a meglévő beépített címtár-szerepkörök használják. Az alkalmazás `microsoft-application-admin` -jóváhagyásra vonatkozó házirend ismerteti például azokat a feltételeket, amelyek mellett az alkalmazás-rendszergazda és a felhőalapú alkalmazás rendszergazdai szerepkörei jogosultak a bérlői szintű rendszergazdai jóváhagyásra. A beépített szabályzatok egyéni címtárbeli szerepkörökben és a felhasználói engedélyezési beállítások konfigurálásához használhatók, de nem szerkeszthetők és nem törölhetők.

## <a name="pre-requisites"></a>Előfeltételek

1. Győződjön meg arról, hogy a [AzureADPreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview) modult használja. Ez a lépés akkor fontos, ha a [AzureAD](/powershell/module/azuread/) modult és a [AzureADPreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview) modult is telepítette.

    ```powershell
    Remove-Module AzureAD -ErrorAction SilentlyContinue
    Import-Module AzureADPreview
    ```

1. Kapcsolódjon az Azure AD PowerShell-hez.

   ```powershell
   Connect-AzureAD
   ```

## <a name="list-existing-app-consent-policies"></a>Meglévő alkalmazás-engedélyezési szabályzatok listázása

Érdemes elsajátítani a meglévő alkalmazás-engedélyezési szabályzatok megismerését a szervezetben:

1. Az összes alkalmazás-engedélyezési szabályzat listázása:

   ```powershell
   Get-AzureADMSPermissionGrantPolicy | ft Id, DisplayName, Description
   ```

1. Tekintse meg a szabályzat "include" (belefoglalt) készleteit:

    ```powershell
    Get-AzureADMSPermissionGrantConditionSet -PolicyId "microsoft-application-admin" `
                                             -ConditionSetType "includes"
    ```

1. Tekintse meg a "kizárások" feltételt:

    ```powershell
    Get-AzureADMSPermissionGrantConditionSet -PolicyId "microsoft-application-admin" `
                                             -ConditionSetType "excludes"
    ```

## <a name="create-a-custom-app-consent-policy"></a>Egyéni alkalmazás-engedélyezési szabályzat létrehozása

Az alábbi lépéseket követve hozhat létre egyéni alkalmazás-engedélyezési szabályzatot:

1. Hozzon létre egy új, üres alkalmazás-engedélyezési szabályzatot.

   ```powershell
   New-AzureADMSPermissionGrantPolicy `
       -Id "my-custom-policy" `
       -DisplayName "My first custom consent policy" `
       -Description "This is a sample custom app consent policy."
   ```

1. Adja hozzá a "include" feltételt.

   ```powershell
   # Include delegated permissions classified "low", for apps from verified publishers
   New-AzureADMSPermissionGrantConditionSet `
       -PolicyId "my-custom-policy" `
       -ConditionSetType "includes" `
       -PermissionType "delegated" `
       -PermissionClassification "low" `
       -ClientApplicationsFromVerifiedPublisherOnly $true
   ```

   Ismételje meg ezt a lépést további "Belefoglalás" állapotok hozzáadásához.

1. Opcionálisan vegye fel a "kizárások" feltételt.

   ```powershell
   # Retrieve the service principal for the Azure Management API
   $azureApi = Get-AzureADServicePrincipal -Filter "servicePrincipalNames/any(n:n eq 'https://management.azure.com/')"

   # Exclude delegated permissions for the Azure Management API
   New-AzureADMSPermissionGrantConditionSet `
       -PolicyId "my-custom-policy" `
       -ConditionSetType "excludes" `
       -PermissionType "delegated" `
       -ResourceApplication $azureApi.AppId
   ```

   Ismételje meg ezt a lépést további "kizárás" állapotok hozzáadásához.

Miután létrehozta az alkalmazáshoz való beleegyezett szabályzatot, [engedélyezheti a felhasználó beleegyezik](configure-user-consent.md?tabs=azure-powershell#allow-user-consent-subject-to-an-app-consent-policy) a szabályzat hatálya alá.

## <a name="delete-a-custom-app-consent-policy"></a>Egyéni alkalmazás-engedélyezési szabályzat törlése

1. Az alábbiakban bemutatjuk, hogyan törölhet egyéni alkalmazás-engedélyezési szabályzatot. **Ez a művelet nem vonható vissza.**

   ```powershell
   Remove-AzureADMSPermissionGrantPolicy -Id "my-custom-policy"
   ```

> [!WARNING]
> A törölt alkalmazás-engedélyezési házirendek nem állíthatók vissza. Ha véletlenül töröl egy egyéni alkalmazás-engedélyezési szabályzatot, akkor újra létre kell hoznia a szabályzatot.

---

### <a name="supported-conditions"></a>Támogatott feltételek

Az alábbi táblázat az alkalmazás-engedélyezési házirendek támogatott feltételeinek listáját tartalmazza.

| Feltétel | Description|
|:---------------|:----------|
| PermissionClassification | Az engedélyek [besorolása](configure-permission-classifications.md) a megadott engedélyhez, vagy az "all", hogy egyezzen az engedélyek besorolásával (beleértve a nem besorolt engedélyeket is). Az alapértelmezett érték az "all". |
| PermissionType | A megadott engedély engedélyezési típusa. Használja az "alkalmazás" jogosultságot az alkalmazás engedélyeihez (például az alkalmazás szerepköreihez) vagy a delegált engedélyekhez. <br><br>**Megjegyzés**: a "delegatedUserConsentable" érték olyan delegált engedélyeket jelöl, amelyeket nem konfiguráltak az API-közzétevő a rendszergazdai hozzájárulás megköveteléséhez – ez az érték a beépített engedélyezési házirendekben használható, de nem használható egyéni engedélyek megadására szolgáló szabályzatokban. Kötelező. |
| ResourceApplication | Az erőforrás-alkalmazás **AppID** (például az API-t), amelyhez engedély van megadva, vagy bármely erőforrás-alkalmazással vagy API-val való egyeztetéshez. Az alapértelmezett érték az "any". |
| Engedélyek | A megfelelő engedélyekhez tartozó engedélyezési azonosítók listája, vagy egy olyan lista, amely egyetlen "all" értékkel rendelkezik, hogy minden engedélyhez illeszkedjen. Az alapértelmezett érték az "all". <ul><li>A delegált engedélyek azonosítói az API ServicePrincipal objektumának **OAuth2Permissions** tulajdonságában találhatók.</li><li>Az alkalmazás engedélyeinek azonosítói az API ServicePrincipal objektumának **AppRoles** tulajdonságában találhatók.</li></ol> |
| ClientApplicationIds | Azon **AppID** -értékek listája, amelyek megfelelnek az ügyfél-alkalmazásoknak, vagy egy olyan lista, amely egyetlen "all" értékkel rendelkezik az ügyfélalkalmazás egyeztetéséhez. Az alapértelmezett érték az "all". |
| ClientApplicationTenantIds | Azon Azure Active Directory bérlői azonosítók listája, amelyekben az ügyfélalkalmazás regisztrálva van, vagy egyetlen "all" értékű lista, amely megfelel a bármely bérlőben regisztrált ügyfélalkalmazások. Az alapértelmezett érték az "all". |
| ClientApplicationPublisherIds | Az ügyfélalkalmazás [ellenőrzött közzétevői](../develop/publisher-verification-overview.md) számára készült Microsoft Partner Network (MPN) azonosítók listája, vagy egyetlen "all" értékű lista, amely az ügyfélalkalmazások bármelyik közzétevőtől való egyeztetésére használható. Az alapértelmezett érték az "all". |
| ClientApplicationsFromVerifiedPublisherOnly | A beállítás értéke `$true` csak az [ellenőrzött közzétevővel](../develop/publisher-verification-overview.md)rendelkező ügyfélalkalmazások egyeztetése. Úgy állítsa be, `$false` hogy minden ügyfélalkalmazás egyezzen, még akkor is, ha nem rendelkezik ellenőrzött közzétevővel. Az alapértelmezett szint a `$false`. |

## <a name="next-steps"></a>Következő lépések

További tudnivalók:

* [Felhasználói beleegyező beállítások konfigurálása](configure-user-consent.md)
* [Rendszergazdai engedélyezési munkafolyamat konfigurálása](configure-admin-consent-workflow.md)
* [Megtudhatja, hogyan kezelheti az alkalmazásokra vonatkozó beleegyezett, és hogyan értékelheti a hozzájárulásukat](manage-consent-requests.md)
* [Bérlőszintű rendszergazdai jóváhagyás megadása egy alkalmazáshoz](grant-admin-consent.md)
* [Engedélyek és beleegyezett a Microsoft Identity platform](../develop/v2-permissions-and-consent.md)

Segítség kérése vagy válaszok keresése a kérdéseire:
* [Azure AD a Microsoft Q&A](/answers/products/)