---
title: Alkalmazás-hozzájárulási szabályzatok kezelése az Azure AD-ban
description: Megtudhatja, hogyan kezelheti a beépített és az egyéni alkalmazás-hozzájárulási szabályzatokat annak szabályozására, hogy mikor adható meg a hozzájárulás.
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
ms.openlocfilehash: 44299fadd17d1acfa292dd88bd57c8be4a44be36
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375695"
---
# <a name="manage-app-consent-policies"></a>Alkalmazás-hozzájárulási szabályzatok kezelése

Az Azure AD PowerShell használatával megtekintheti és kezelheti az alkalmazás-hozzájárulási szabályzatokat.

Az alkalmazás-hozzájárulási szabályzat egy vagy több "tartalmaz" feltételkészletből és nulla vagy több "kizárási" feltételkészletből áll. Ahhoz, hogy egy esemény figyelembe legyen véve  egy alkalmazás-hozzájárulási szabályzatban, annak meg kell egyeznie legalább egy "tartalmaz" feltételkészletével, és nem egyeznie kell a  "kizárási" feltételkészletekkel.

Minden feltételkészlet több feltételből áll. Ahhoz, hogy egy esemény  megfeleljen egy feltételkészletnek, a feltételkészlet összes feltételének teljesülnie kell.

Az alkalmazás-hozzájárulási szabályzatok, amelyekben az azonosító a "microsoft-" szótól kezdődik, beépített szabályzatok. Ezen beépített szabályzatok némelyike a meglévő beépített címtár-szerepkörökben használatos. Az alkalmazás-hozzájárulási szabályzat például azokat a feltételeket írja le, amelyek esetén az alkalmazás-rendszergazdai és a felhőalkalmazás-rendszergazdai szerepkörök engedélyt adhatnak a `microsoft-application-admin` bérlői szintű rendszergazdai jóváhagyásra. Beépített szabályzatok használhatók az egyéni címtár-szerepkörökben és a felhasználói hozzájárulási beállítások konfigurálása érdekében, de nem szerkeszthetők vagy törölhetők.

## <a name="pre-requisites"></a>Előfeltételek

1. Győződjön meg arról, hogy az [AzureADPreview modult](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview) használja. Ez a lépés akkor fontos, ha az [AzureAD](/powershell/module/azuread/) modult és az [AzureADPreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview) modult is telepítette.

    ```powershell
    Remove-Module AzureAD -ErrorAction SilentlyContinue
    Import-Module AzureADPreview
    ```

1. Csatlakozzon az Azure AD PowerShellhez.

   ```powershell
   Connect-AzureAD
   ```

## <a name="list-existing-app-consent-policies"></a>Meglévő alkalmazás-hozzájárulási szabályzatok felsorolása

Először is ismerkedjen meg a szervezet meglévő alkalmazás-hozzájárulási szabályzatával:

1. Az összes alkalmazás-hozzájárulási szabályzat felsorolása:

   ```powershell
   Get-AzureADMSPermissionGrantPolicy | ft Id, DisplayName, Description
   ```

1. Tekintse meg egy szabályzat "tartalmazza" feltételkészleteit:

    ```powershell
    Get-AzureADMSPermissionGrantConditionSet -PolicyId "microsoft-application-admin" `
                                             -ConditionSetType "includes"
    ```

1. Tekintse meg a "kihagy" feltételkészleteket:

    ```powershell
    Get-AzureADMSPermissionGrantConditionSet -PolicyId "microsoft-application-admin" `
                                             -ConditionSetType "excludes"
    ```

## <a name="create-a-custom-app-consent-policy"></a>Egyéni alkalmazás-hozzájárulási szabályzat létrehozása

Kövesse az alábbi lépéseket egy egyéni alkalmazás-hozzájárulási szabályzat létrehozásához:

1. Hozzon létre egy új üres alkalmazás-hozzájárulási szabályzatot.

   ```powershell
   New-AzureADMSPermissionGrantPolicy `
       -Id "my-custom-policy" `
       -DisplayName "My first custom consent policy" `
       -Description "This is a sample custom app consent policy."
   ```

1. "Includes" feltételkészletek hozzáadása.

   ```powershell
   # Include delegated permissions classified "low", for apps from verified publishers
   New-AzureADMSPermissionGrantConditionSet `
       -PolicyId "my-custom-policy" `
       -ConditionSetType "includes" `
       -PermissionType "delegated" `
       -PermissionClassification "low" `
       -ClientApplicationsFromVerifiedPublisherOnly $true
   ```

   Ismételje meg ezt a lépést további "include" feltételkészletek hozzáadásához.

1. Igény szerint hozzáadhat "kizár" feltételkészleteket is.

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

   Ismételje meg ezt a lépést további "kizárási" feltételkészletek hozzáadásához.

Az alkalmazás-hozzájárulási szabályzat létrehozása után engedélyezheti, hogy a felhasználói [jóváhagyásra](configure-user-consent.md?tabs=azure-powershell#allow-user-consent-subject-to-an-app-consent-policy) ez a szabályzat vonatkozik.

## <a name="delete-a-custom-app-consent-policy"></a>Egyéni alkalmazás-hozzájárulási szabályzat törlése

1. Az alábbiakban az egyéni alkalmazás-hozzájárulási szabályzatok törlését mutatjuk be. **Ez a művelet nem vonható vissza.**

   ```powershell
   Remove-AzureADMSPermissionGrantPolicy -Id "my-custom-policy"
   ```

> [!WARNING]
> A törölt alkalmazás-hozzájárulási szabályzatok nem állíthatók vissza. Ha véletlenül töröl egy egyéni alkalmazás-hozzájárulási szabályzatot, újra létre kell hoznia a szabályzatot.

---

### <a name="supported-conditions"></a>Támogatott feltételek

Az alábbi táblázat az alkalmazás-hozzájárulási szabályzatok támogatott feltételeinek listáját tartalmazza.

| Feltétel | Leírás|
|:---------------|:----------|
| PermissionClassification (Engedélyosztályosítás) | A [megadott engedély](configure-permission-classifications.md) besorolása, vagy "mind", hogy megfeleljen az engedélyek besorolásának (beleértve a nem besorolt engedélyeket is). Az alapértelmezett érték az "összes". |
| PermissionType (Engedélytípus) | A megadott engedély engedélytípusa. Az alkalmazásengedélyeket (például alkalmazás-szerepköröket) vagy a delegált engedélyeket "delegáltként" használhatja. <br><br>**Megjegyzés:** A "delegatedUserConsentable" érték olyan delegált engedélyeket jelez, amelyeket az API-közzétevő nem konfigurált rendszergazdai jóváhagyásra – ez az érték használható a beépített engedély-megadó szabályzatban, de nem használható egyéni engedélyengedély-hozzáférési szabályzatban. Kötelező. |
| ResourceApplication (Erőforrás-alkalmazás) | Annak **az erőforrás-alkalmazásnak** (például AZ API-nak) az AppId-ja, amelyhez az engedélyt megadták, vagy az "any" (bármely) egyezése bármely erőforrás-alkalmazással vagy API-val. Az alapértelmezett érték az "any". |
| Engedélyek | Az adott engedélyekkel egyező engedélyek listája, vagy egy olyan lista, amely egyetlen "all" értéket ad meg az engedélyeknek való megfeleltetve. Az alapértelmezett érték az "összes". <ul><li>A delegált engedélyekkel rendelkezők az API ServicePrincipal **objektumának OAuth2Permissions** tulajdonságában találhatók.</li><li>Az alkalmazásengedélyek az API ServicePrincipal **objektumÁnak AppRoles** tulajdonságában találhatók.</li></ol> |
| ClientApplicationIds (ClientApplicationIds) | Az **ügyfélalkalmazások alkalmazásazonosító-értékeinek** listája, vagy egy olyan lista, amely egyetlen "all" értéket tartalmaz az ügyfélalkalmazások egyeztetéséhez. Az alapértelmezett érték az "összes". |
| ClientApplicationTenantIds (ClientApplicationTenantIds) | Azon bérlői Azure Active Directory, amelyekben az ügyfélalkalmazás regisztrálva van, vagy egy olyan lista, amelynek egyetlen értéke "all", és amely megfelel a bármely bérlőben regisztrált ügyfélalkalmazásnak. Az alapértelmezett érték az "összes". |
| ClientApplicationPublisherIds | Az ügyfélalkalmazás ellenőrzött közzétevőinek Microsoft Partner Network [](../develop/publisher-verification-overview.md) (MPN)-inek listája, vagy egy olyan lista, amely egyetlen "all" értékkel egyezik meg bármely közzétevőtől származó ügyfélalkalmazásokkal. Az alapértelmezett érték az "összes". |
| ClientApplicationsFromVerifiedPublisherOnly (ÜgyfélalkalmazásokFromVerifiedPublisherOnly) | Állítsa a `$true` beállítását úgy, hogy csak ellenőrzött közzétevővel [egyező ügyfélalkalmazások esetén egyezést állítson be.](../develop/publisher-verification-overview.md) Állítsa a beállítását úgy, hogy bármely ügyfélalkalmazásban egyezést állítson be, még akkor is, ha nem rendelkezik `$false` ellenőrzött közzétevővel. Az alapértelmezett szint a `$false`. |

## <a name="next-steps"></a>Következő lépések

További tudnivalók:

* [Felhasználói hozzájárulási beállítások konfigurálása](configure-user-consent.md)
* [A rendszergazdai jóváhagyás munkafolyamatának konfigurálása](configure-admin-consent-workflow.md)
* [Útmutató az alkalmazásokhoz való hozzájárulás kezeléséhez és a hozzájárulási kérelmek kiértékeléséhez](manage-consent-requests.md)
* [Bérlőszintű rendszergazdai jóváhagyás megadása egy alkalmazáshoz](grant-admin-consent.md)
* [Engedélyek és jóváhagyás a Microsoft identitásplatformján](../develop/v2-permissions-and-consent.md)

Segítségért vagy a kérdéseire adott válaszokért:
* [Azure AD a Microsoft Q&A-n](/answers/products/)