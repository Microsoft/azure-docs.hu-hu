---
title: Jogkivonatok élettartamának beállítása
titleSuffix: Microsoft identity platform
description: Megtudhatja, hogyan állíthatja be a Microsoft identitásplatformja által kiadott jogkivonatok élettartamát. Megtudhatja, hogyan kezelheti egy szervezet alapértelmezett szabályzatát, hogyan hozhat létre szabályzatot a webes bejelentkezéshez, hogyan hozhat létre szabályzatot egy webes API-t behívó natív alkalmazáshoz, és hogyan kezelheti a speciális szabályzatokat.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 04/08/2021
ms.author: ryanwi
ms.custom: aaddev, content-perf, FY21Q1
ms.reviewer: hirsin, jlu, annaba
ms.openlocfilehash: 66e9817c6d3bbcd199418b9afd78eda016c5f291
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363886"
---
# <a name="configure-token-lifetime-policies-preview"></a>Jogkivonatok élettartam-szabályzatának konfigurálása (előzetes verzió)
Megadhatja a Microsoft identitásplatformja által kiadott hozzáférési, SAML- vagy azonosító-jogkivonat élettartamát. Beállíthatja a cégen belüli összes alkalmazás jogkivonatának élettartamát több-bérlős alkalmazások (több cég) vagy munkahelyen belüli adott szolgáltatásnév esetén. További információért olvassa el a [konfigurálható jogkivonatok élettartamát.](active-directory-configurable-token-lifetimes.md)

Ebben a szakaszban egy általános szabályzatforgatókönyvet is végigveszünk, amely segíthet új szabályokat bevetni a jogkivonatok élettartamára. A példában megtudhatja, hogyan hozhat létre olyan szabályzatot, amely előírja, hogy a felhasználók gyakrabban hitelesítsék magukat a webalkalmazásban.

## <a name="get-started"></a>Bevezetés

Első lépésekként töltse le az [Azure AD PowerShell-modul legújabb nyilvános előzetes kiadását.](https://www.powershellgallery.com/packages/AzureADPreview)

Ezután futtassa a parancsot az Azure AD rendszergazdai `Connect` fiókjába való bejelentkezéshez. Futtassa ezt a parancsot minden alkalommal, amikor új munkamenetet indít el.

```powershell
Connect-AzureAD -Confirm
```

## <a name="create-a-policy-for-web-sign-in"></a>Szabályzat létrehozása webes bejelentkezéshez

Ebben a példában egy olyan szabályzatot hoz létre, amely megköveteli, hogy a felhasználók gyakrabban hitelesítsék magukat a webalkalmazásban. Ez a szabályzat a hozzáférési/azonosító jogkivonatok élettartamát a webalkalmazás szolgáltatásnévre állítja be.

1. Jogkivonat élettartam-szabályzatának létrehozása.

    Ez a szabályzat a webes bejelentkezéshez két órára állítja a hozzáférési/azonosító jogkivonat élettartamát.

    A szabályzat létrehozásához futtassa a [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) parancsmagot:

    ```powershell
    $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"02:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
    ```

    Az új szabályzatot és az **ObjectId** lekért szabályzatát a [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) parancsmag futtatásával láthatja:

    ```powershell
    Get-AzureADPolicy -Id $policy.Id
    ```

1. Rendelje hozzá a szabályzatot a szolgáltatásnévhez. A szolgáltatásnév **ObjectId-ját** is be kell szereznie.

    A [Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) parancsmaggal a szervezet összes szolgáltatásnév vagy egyetlen szolgáltatásnév látható.

    ```powershell
    # Get ID of the service principal
    $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"
    ```

    Ha már van szolgáltatásnév, futtassa az [Add-AzureADServicePrincipalPolicy](/powershell/module/azuread/add-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) parancsmagot:

    ```powershell
    # Assign policy to a service principal
    Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
    ```

## <a name="view-existing-policies-in-a-tenant"></a>Bérlőben meglévő szabályzatok megtekintése

A szervezetben létrehozott összes szabályzatot a [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) parancsmag futtatásával láthatja.  A fent felsoroltaktól eltérő, meghatározott tulajdonságértékekkel kapcsolatos eredmények a kiesése hatókörében találhatók.

```powershell
Get-AzureADPolicy -All $true
```

Az azonosított adott szabályzathoz kapcsolódó alkalmazások és szolgáltatásnévek azonosításához futtassa a [következő Get-AzureADPolicyAppliedObject](/powershell/module/azuread/get-azureadpolicyappliedobject?view=azureadps-2.0-preview&preserve-view=true) parancsmagot az **1a37dad8-5da7-4cc8-87c7-efbc0326cf20** kóddal bármely házirend-azonosításával. Ezután eldöntheti, hogy konfigurálja-e a feltételes hozzáférés bejelentkezési gyakoriságát, vagy az Azure AD alapértelmezett értékeket használja.

```powershell
Get-AzureADPolicyAppliedObject -id 1a37dad8-5da7-4cc8-87c7-efbc0326cf20
```

Ha a bérlő olyan szabályzatokkal rendelkezik, amelyek egyéni értékeket határoznak meg a frissítési és munkamenet-jogkivonatok konfigurációs tulajdonságaihoz, a Microsoft azt javasolja, hogy frissítse ezeket a szabályzatokat a fent ismertetett alapértelmezett értékekre. Ha nem történik módosítás, az Azure AD automatikusan tiszteletben fogja tenni az alapértelmezett értékeket.

### <a name="troubleshooting"></a>Hibaelhárítás
Egyes felhasználók hibát jelentettek a `Get-AzureADPolicy : The term 'Get-AzureADPolicy' is not recognized` `Get-AzureADPolicy` parancsmag futtatása után. Áthidaló megoldásként futtassa a következőt az AzureAD modul eltávolításához/újratelepítéséhez, majd telepítse az AzureADPreview modult:

```powershell
# Uninstall the AzureAD Module
UnInstall-Module AzureAD

# Re-install the AzureAD Module
Install-Module AzureAD

# Install the AzureAD Preview Module adding the -AllowClobber
Install-Module AzureADPreview -AllowClobber

Connect-AzureAD
Get-AzureADPolicy -All $true
```

## <a name="next-steps"></a>Következő lépések
Ismerje meg [az](../conditional-access/howto-conditional-access-session-lifetime.md) Azure AD feltételes hozzáférés hitelesítési munkamenet-kezelési képességeit.
