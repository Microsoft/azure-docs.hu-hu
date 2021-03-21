---
title: Tokenek élettartamának beállítása
titleSuffix: Microsoft identity platform
description: Megtudhatja, hogyan állíthatja be a Microsoft Identity platform által kiállított jogkivonatok élettartamát. Ismerje meg, hogyan kezelheti a szervezet alapértelmezett házirendjét, hogyan hozhat létre webes bejelentkezésre vonatkozó házirendet, hogyan hozhat létre házirendet a webes API-t meghívó natív alkalmazásokhoz, és hogyan kezelheti a speciális házirendeket.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 02/01/2021
ms.author: ryanwi
ms.custom: aaddev, content-perf, FY21Q1
ms.reviewer: hirsin, jlu, annaba
ms.openlocfilehash: 3ec94543a53e3e5b7709801de8f4cf1dde3fc3d9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99428115"
---
# <a name="configure-token-lifetime-policies-preview"></a>Jogkivonat élettartamára vonatkozó szabályzatok konfigurálása (előzetes verzió)
Megadhatja a Microsoft Identity platform által kiadott hozzáférés, SAML vagy azonosító jogkivonatok élettartamát. Beállíthatja a cégen belüli összes alkalmazás jogkivonatának élettartamát több-bérlős alkalmazások (több cég) vagy munkahelyen belüli adott szolgáltatásnév esetén. További információért olvassa el a [konfigurálható jogkivonat élettartamait](active-directory-configurable-token-lifetimes.md).

Ebben a szakaszban egy olyan általános házirend-forgatókönyvet ismertetünk, amely segíthet új szabályok bevezetésében a jogkivonat élettartama tekintetében. A példában megtudhatja, hogyan hozhat létre olyan szabályzatot, amely megköveteli, hogy a felhasználók gyakrabban hitelesítsék magukat a webalkalmazásban.

## <a name="get-started"></a>Bevezetés
A kezdéshez hajtsa végre a következő lépéseket:

1. Töltse le a legújabb [Azure ad PowerShell-modul nyilvános előzetes kiadását](https://www.powershellgallery.com/packages/AzureADPreview).
1. A parancs futtatásával `Connect` Jelentkezzen be az Azure ad-rendszergazdai fiókjába. Futtassa ezt a parancsot minden alkalommal, amikor új munkamenetet indít el.

    ```powershell
    Connect-AzureAD -Confirm
    ```

1. A szervezetben létrehozott összes házirend megtekintéséhez futtassa a [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) parancsmagot.  A fent felsorolt alapértelmezett értékektől eltérő, meghatározott tulajdonságértékeket használó eredmények a nyugdíjazás hatókörében vannak.

    ```powershell
    Get-AzureADPolicy -All $true
    ```

1. Ha szeretné megtekinteni, hogy mely alkalmazások és szolgáltatások vannak összekapcsolva egy adott házirenddel, akkor a következő [Get-AzureADPolicyAppliedObject](/powershell/module/azuread/get-azureadpolicyappliedobject?view=azureadps-2.0-preview&preserve-view=true) parancsmagot kell lecserélnie a **1a37dad8-5da7-4cc8-87c7-efbc0326cf20** bármely házirend-azonosítóval való lecserélésével. Ezután eldöntheti, hogy konfigurálja-e a feltételes hozzáférés bejelentkezési gyakoriságát, vagy az Azure AD alapértelmezett értékeivel marad-e.

    ```powershell
    Get-AzureADPolicyAppliedObject -id 1a37dad8-5da7-4cc8-87c7-efbc0326cf20
    ```

Ha a bérlő rendelkezik olyan házirendekkel, amelyek egyéni értékeket határoznak meg a frissítési és a munkamenet-jogkivonat konfigurációs tulajdonságaihoz, a Microsoft javasolja, hogy frissítse ezeket a házirendeket a fent ismertetett alapértékeket tükröző értékekre. Ha nem végez módosítást, az Azure AD automatikusan tiszteletben tartja az alapértelmezett értékeket.

## <a name="create-a-policy-for-web-sign-in"></a>Házirend létrehozása webes bejelentkezéshez

Ebben a példában olyan házirendet hoz létre, amely megköveteli, hogy a felhasználók gyakrabban hitelesítsék magukat a webalkalmazásban. Ezzel a szabályzattal állítható be a hozzáférési/azonosító tokenek élettartama, valamint a többtényezős munkamenet-tokenek maximális kora a webalkalmazás egyszerű szolgáltatásnév számára.

1. Hozzon létre egy jogkivonat-élettartam-szabályzatot.

    Ez a házirend a webes bejelentkezéshez megadja a hozzáférési/azonosító jogkivonat élettartamát és az egytényezős munkamenet-token maximális életkorát két óráig.

    1. A szabályzat létrehozásához futtassa a [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) parancsmagot:

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"02:00:00","MaxAgeSessionSingleFactor":"02:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    1. Az új szabályzat megtekintéséhez és a szabályzat **ObjectId** lekéréséhez futtassa a [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) parancsmagot:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Rendelje hozzá a szabályzatot az egyszerű szolgáltatáshoz. Az egyszerű szolgáltatásnév **ObjectId** is le kell kérnie.

    1. A [Get-azureadserviceprincipal parancsmagot](/powershell/module/azuread/get-azureadserviceprincipal) parancsmaggal tekintheti meg az összes szervezet egyszerű szolgáltatását vagy egy egyszerű szolgáltatásnevet.
        ```powershell
        # Get ID of the service principal
        $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"
        ```

    1. Ha rendelkezik az egyszerű szolgáltatással, futtassa az [Add-AzureADServicePrincipalPolicy](/powershell/module/azuread/add-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) parancsmagot:
        ```powershell
        # Assign policy to a service principal
        Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
        ```

## <a name="create-token-lifetime-policies-for-refresh-and-session-tokens"></a>Jogkivonat élettartamára vonatkozó szabályzatok létrehozása frissítési és munkamenet-tokenekhez
> [!IMPORTANT]
> 2021. január 30-ig nem konfigurálhatja a frissítési és a munkamenet-jogkivonat élettartamát. Azure Active Directory már nem veszi figyelembe a frissítési és a munkamenet-jogkivonat konfigurációját a meglévő házirendekben.  A meglévő tokenek lejártát követően kiadott új jogkivonatok mostantól az [alapértelmezett konfigurációra](active-directory-configurable-token-lifetimes.md#configurable-token-lifetime-properties-after-the-retirement)vannak beállítva. Továbbra is konfigurálhatja a hozzáférés, az SAML és az azonosító token élettartamát a frissítés és a munkamenet-jogkivonat konfigurációjának kivonása után.
>
> A meglévő jogkivonat élettartama nem változik. A lejárat után a rendszer egy új jogkivonatot ad ki az alapértelmezett érték alapján.
>
> Ha továbbra is meg kell határoznia azt az időtartamot, ameddig a felhasználónak újra be kell jelentkeznie, konfigurálnia kell a bejelentkezési gyakoriságot a feltételes hozzáférésben. Ha többet szeretne megtudni a feltételes hozzáférésről, olvassa el a [hitelesítési munkamenet-kezelés konfigurálása feltételes hozzáféréssel című szakaszt](../conditional-access/howto-conditional-access-session-lifetime.md).

### <a name="manage-an-organizations-default-policy"></a>A szervezet alapértelmezett házirendjének kezelése
Ebben a példában olyan házirendet hoz létre, amely lehetővé teszi, hogy a felhasználók a teljes szervezeten belül ritkábban jelentkezzenek be. Ehhez hozzon létre egy jogkivonat-élettartam-szabályzatot az egytényezős frissítési tokenekhez, amelyet a rendszer a szervezeten belül alkalmaz. A szabályzatot a szervezet minden alkalmazására, valamint minden olyan egyszerű szolgáltatásnév alkalmazza, amely még nem rendelkezik házirend-beállítással.

1. Hozzon létre egy jogkivonat-élettartam-szabályzatot.

    1. Állítsa az egytényezős frissítési tokent a "visszavonás visszavonása" értékre. A jogkivonat nem jár le, amíg vissza nem vonja a hozzáférést. Hozza létre a következő szabályzat-definíciót:

        ```powershell
        @('{
            "TokenLifetimePolicy":
            {
                "Version":1,
                "MaxAgeSingleFactor":"until-revoked"
            }
        }')
        ```

    1. A szabályzat létrehozásához futtassa a [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) parancsmagot:

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    1. A szóköz eltávolításához futtassa a [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) parancsmagot:

        ```powershell
        Get-AzureADPolicy -id | set-azureadpolicy -Definition @($((Get-AzureADPolicy -id ).Replace(" ","")))
        ```

    1. Az új szabályzat megtekintéséhez és a szabályzat **ObjectId** beszerzéséhez futtassa a következő parancsot:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Frissítse a szabályzatot.

    Dönthet úgy is, hogy az ebben a példában megadott első szabályzat nem annyira szigorú, mint a szolgáltatás. Ha úgy szeretné beállítani az egytényezős frissítési tokent, hogy két nap múlva lejárjon, futtassa a következő parancsot:

    ```powershell
    Set-AzureADPolicy -Id $policy.Id -DisplayName $policy.DisplayName -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"2.00:00:00"}}')
    ```

### <a name="create-a-policy-for-a-native-app-that-calls-a-web-api"></a>Szabályzat létrehozása egy webes API-t meghívó natív alkalmazáshoz
Ebben a példában olyan házirendet hoz létre, amely megköveteli, hogy a felhasználóknak ritkábban kell hitelesíteniük magukat. A szabályzat emellett meghosszabbítja azt az időtartamot is, ameddig a felhasználó inaktív lehet, mielőtt a felhasználónak újra hitelesítenie kell magát. A szabályzatot a rendszer a webes API-ra alkalmazza. Ha a natív alkalmazás erőforrásként kéri a webes API-t, a rendszer alkalmazza ezt a házirendet.

1. Hozzon létre egy jogkivonat-élettartam-szabályzatot.

    1. Ha szigorú szabályzatot szeretne létrehozni egy webes API-hoz, futtassa a [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) parancsmagot:

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"30.00:00:00","MaxAgeMultiFactor":"until-revoked","MaxAgeSingleFactor":"180.00:00:00"}}') -DisplayName "WebApiDefaultPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    1. Az új szabályzat megtekintéséhez futtassa a következő parancsot:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Rendelje hozzá a szabályzatot a webes API-hoz. Emellett le kell kérnie az alkalmazás **ObjectId** is. Használja a [Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) parancsmagot az alkalmazás **ObjectId** megkereséséhez, vagy használja a [Azure Portal](https://portal.azure.com/).

    Szerezze be az alkalmazás **ObjectId** , és rendelje hozzá a szabályzatot:

    ```powershell
    # Get the application
    $app = Get-AzureADApplication -Filter "DisplayName eq 'Fourth Coffee Web API'"

    # Assign the policy to your web API.
    Add-AzureADApplicationPolicy -Id $app.ObjectId -RefObjectId $policy.Id
    ```

### <a name="manage-an-advanced-policy"></a>Speciális szabályzat kezelése
Ebben a példában néhány szabályzatot hoz létre a prioritási rendszer működésének megismeréséhez. Azt is megtudhatja, hogyan kezelhet több objektumra alkalmazott házirendeket.

1. Hozzon létre egy jogkivonat-élettartam-szabályzatot.

    1. Ha a szervezet alapértelmezett házirendjét szeretné létrehozni, amely az egytényezős frissítési token élettartamát 30 napra állítja be, futtassa a [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) parancsmagot:

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"30.00:00:00"}}') -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    1. Az új szabályzat megjelenítéséhez futtassa a [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) parancsmagot:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Rendelje hozzá a szabályzatot egy egyszerű szolgáltatáshoz.

    Most már rendelkezik egy szabályzattal, amely a teljes szervezetre vonatkozik. Előfordulhat, hogy meg szeretné őrizni ezt a 30 napos házirendet egy adott egyszerű szolgáltatásnév esetében, de a szervezet alapértelmezett házirendjét a "visszavonás visszavonása" felső korlátra kell módosítania.

    1. A szervezet összes szolgáltatásának megtekintéséhez használja a [Get-azureadserviceprincipal parancsmagot](/powershell/module/azuread/get-azureadserviceprincipal) parancsmagot.

    1. Ha rendelkezik az egyszerű szolgáltatással, futtassa az [Add-AzureADServicePrincipalPolicy](/powershell/module/azuread/add-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) parancsmagot:

        ```powershell
        # Get ID of the service principal
        $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"

        # Assign policy to a service principal
        Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
        ```

1. A jelző beállítása false (hamis) értékre `IsOrganizationDefault` :

    ```powershell
    Set-AzureADPolicy -Id $policy.Id -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $false
    ```

1. Új szervezet alapértelmezett házirendjének létrehozása:

    ```powershell
    New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "ComplexPolicyScenarioTwo" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
    ```

    Most már rendelkezik a szolgáltatáshoz tartozó eredeti házirenddel, és az új szabályzat beállítása a szervezet alapértelmezett házirendje. Fontos megjegyezni, hogy az egyszerű szolgáltatásokra alkalmazott szabályzatok elsőbbséget élveznek a szervezet alapértelmezett házirendjeivel szemben.

## <a name="next-steps"></a>Következő lépések
Ismerje meg az Azure AD feltételes hozzáférésének [hitelesítési munkamenet-kezelési képességeit](../conditional-access/howto-conditional-access-session-lifetime.md) .
