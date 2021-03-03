---
title: Csoport tulajdonosi engedélyének konfigurálása a csoportok adataihoz való hozzáféréshez az Azure AD használatával
description: Ismerje meg, hogy a csoport-és csapattagok hogyan adhatnak hozzá olyan alkalmazásokat, amelyeknek hozzáférésük lesz a csoporthoz vagy a csapathoz.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 05/19/2020
ms.author: kenwith
ms.reviewer: arvindh, luleon, phsignor
ms.custom: contperf-fy21q2
ms.openlocfilehash: 24b2b8ee6398ef1306aee59b5d7ca7525284f96b
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101644547"
---
# <a name="configure-group-owner-consent-to-apps-accessing-group-data"></a>A csoportra vonatkozó adatokhoz hozzáférő alkalmazások csoportbeli tulajdonosi engedélyének konfigurálása

A csoport-és csapat tulajdonosai engedélyezhetik az alkalmazások, például a külső gyártók által közzétett alkalmazások számára, hogy hozzáférjenek a szervezet adataihoz a csoporthoz társítva. A Microsoft Teams csapatának tulajdonosa például lehetővé teheti az alkalmazások számára, hogy beolvassák a csapat összes csapatának üzenetét, vagy felsorolják a csoport tagjainak alapszintű profilját. További információ: [erőforrás-specifikus beleegyezett a Microsoft Teams-ben](/microsoftteams/resource-specific-consent) .

## <a name="manage-group-owner-consent-to-apps"></a>Csoport tulajdonosi engedélyének kezelése az alkalmazásokhoz

Beállíthatja, hogy mely felhasználók férjenek hozzá a csoportjaihoz vagy a csapatához tartozó adatokhoz, vagy letilthatja ezt az összes felhasználó számára.

# <a name="portal"></a>[Portál](#tab/azure-portal)

Az alábbi lépéseket követve kezelheti a csoport tulajdonosának a csoportra vonatkozó adatokhoz való hozzáférését:

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) [globális rendszergazdaként](../roles/permissions-reference.md#global-administrator).
2. Válassza **Azure Active Directory**  >  **vállalati alkalmazások**  >  **beleegyezett és engedélyek**  >  **felhasználói beleegyező beállításait**.
3. Az **adatokhoz hozzáférő alkalmazások csoport tulajdonosi engedélye** területen válassza ki az engedélyezni kívánt beállítást.
4. A beállítások mentéséhez kattintson a **Mentés** gombra.

Ebben a példában minden csoport tulajdonosa jogosult a csoportok adatokhoz hozzáférő alkalmazásokhoz való hozzáférésre:

:::image type="content" source="media/configure-user-consent-groups/group-owner-consent.png" alt-text="Csoport tulajdonosának engedélyezési beállításai":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A [AzureADPreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview)Azure ad PowerShell-előnézeti moduljának használatával engedélyezheti vagy letilthatja a csoport tulajdonosai számára, hogy a szervezet adataihoz hozzáférjenek a saját csoportok számára.

1. Győződjön meg arról, hogy a [AzureADPreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview) modult használja. Ez a lépés akkor fontos, ha a [AzureAD](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0) modult és a [AzureADPreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview) modult is telepítette.

    ```powershell
    Remove-Module AzureAD
    Import-Module AzureADPreview
    ```

1. Kapcsolódjon az Azure AD PowerShell-hez.

   ```powershell
   Connect-AzureAD
   ```

1. Az aktuális érték beolvasása a bérlőben a **beleegyező házirend-beállítások** címtár-beállításainál. Ehhez ellenőrizni kell, hogy a szolgáltatáshoz tartozó címtár-beállítások létre lettek-e hozva, és ha nem, akkor a megfelelő címtár-beállítási sablon értékeit használja.

    ```powershell
    $consentSettingsTemplateId = "dffd5d46-495d-40a9-8e21-954ff55e198a" # Consent Policy Settings
    $settings = Get-AzureADDirectorySetting -All $true | Where-Object { $_.TemplateId -eq $consentSettingsTemplateId }

    if (-not $settings) {
        $template = Get-AzureADDirectorySettingTemplate -Id $consentSettingsTemplateId
        $settings = $template.CreateDirectorySetting()
    }

    $enabledValue = $settings.Values | ? { $_.Name -eq "EnableGroupSpecificConsent" }
    $limitedToValue = $settings.Values | ? { $_.Name -eq "ConstrainGroupSpecificConsentToMembersOfGroupId" }
    ```

1. A beállítás értékeinek megismerése. Két beállítási érték határozza meg, hogy mely felhasználók számára engedélyezhető, hogy egy alkalmazás hozzáférhessen a csoport adatait:

    | Beállítás       | Típus         | Leírás  |
    | ------------- | ------------ | ------------ |
    | _EnableGroupSpecificConsent_   | Logikai | Jelző, amely azt jelzi, hogy a csoportok tulajdonosai jogosultak-e a csoportra vonatkozó engedélyek megadására. |
    | _ConstrainGroupSpecificConsentToMembersOfGroupId_ | Guid | Ha a _EnableGroupSpecificConsent_ értéke "true" (igaz), és ez az érték egy csoport objektumazonosító, akkor az azonosított csoport tagjai jogosultak a csoportokra vonatkozó engedélyek megadására a saját maguknak. |

1. Módosítsa a kívánt konfiguráció beállításait:

    ```powershell
    # Disable group-specific consent entirely
    $enabledValue.Value = "False"
    $limitedToValue.Value = ""
    ```

    ```powershell
    # Enable group-specific consent for all users
    $enabledValue.Value = "True"
    $limitedToValue.Value = ""
    ```

    ```powershell
    # Enable group-specific consent for users in a given group
    $enabledValue.Value = "True"
    $limitedToValue.Value = "{group-object-id}"
    ```

1. Mentse a beállításokat.

    ```powershell
    if ($settings.Id) {
        # Update an existing directory settings
        Set-AzureADDirectorySetting -Id $settings.Id -DirectorySetting $settings
    } else {
        # Create a new directory settings to override the default setting 
        New-AzureADDirectorySetting -DirectorySetting $settings
    }
    ```

---

## <a name="next-steps"></a>Következő lépések

További tudnivalók:

* [Felhasználói beleegyező beállítások konfigurálása](configure-user-consent.md)
* [Rendszergazdai engedélyezési munkafolyamat konfigurálása](configure-admin-consent-workflow.md)
* [Megtudhatja, hogyan kezelheti az alkalmazásokra vonatkozó beleegyezett, és hogyan értékelheti a hozzájárulásukat](manage-consent-requests.md)
* [Bérlőszintű rendszergazdai jóváhagyás megadása egy alkalmazáshoz](grant-admin-consent.md)
* [Engedélyek és beleegyezett a Microsoft Identity platform](../develop/v2-permissions-and-consent.md)

Segítség kérése vagy válaszok keresése a kérdéseire:
* [Azure AD a Microsoft Q&A ](/answers/topics/azure-active-directory.html)