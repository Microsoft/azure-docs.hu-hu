---
title: Csoporttulajdonosi jóváhagyás konfigurálása arra, hogy az alkalmazások hozzáférjenek a csoportadatokhoz az Azure AD használatával
description: Megtudhatja, hogyan kezelheti, hogy a csoport- és csoporttulajdonosok engedélyt kaphatnak-e olyan alkalmazásokhoz, amelyek hozzáférhetnek a csoport vagy a csapat adataihoz.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 05/19/2020
ms.author: iangithinji
ms.reviewer: arvindh, luleon, phsignor
ms.custom: contperf-fy21q2
ms.openlocfilehash: be28148aacf270f2f3cfabad4cbd5f03afa05d3b
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374420"
---
# <a name="configure-group-owner-consent-to-apps-accessing-group-data"></a>Csoporttulajdonosi hozzájárulás konfigurálása a csoportadatokhoz hozzáférő alkalmazásokhoz

A csoport- és csoporttulajdonosok engedélyezhetik az alkalmazások , például a külső szállítók által közzétett alkalmazások számára a szervezet egy csoporthoz társított adatainak hozzáférését. Egy Microsoft Teams-csoport tulajdonosa például engedélyezheti egy alkalmazásnak a csapat összes Teams-üzenetének olvasását, vagy a csoport tagjainak alapvető profilját. További [információ: Erőforrás-specifikus jóváhagyás a Microsoft Teamsben.](/microsoftteams/resource-specific-consent)

## <a name="manage-group-owner-consent-to-apps"></a>Csoporttulajdonos alkalmazásokhoz való hozzájárulásának kezelése

Beállíthatja, hogy mely felhasználók járulhatnak hozzá a csoportok vagy csapatok adataihoz hozzáférő alkalmazásokhoz, vagy letilthatja ezt az összes felhasználó számára.

# <a name="portal"></a>[Portál](#tab/azure-portal)

Kövesse az alábbi lépéseket a csoporttulajdonosi jóváhagyás kezeléséhez a csoportadatokhoz hozzáférő alkalmazások számára:

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) globális [rendszergazdaként.](../roles/permissions-reference.md#global-administrator)
2. Válassza **Azure Active Directory** Vállalati alkalmazások Hozzájárulás és engedélyek  >    >    >  **Felhasználói hozzájárulási beállítások lehetőséget.**
3. A **Csoporttulajdonosi hozzájárulás az adatokhoz hozzáférő alkalmazásokhoz** alatt válassza az engedélyezni kívánt lehetőséget.
4. A **beállítások mentéséhez** válassza a Mentés lehetőséget.

Ebben a példában minden csoporttulajdonos engedélyt ad arra, hogy az alkalmazások hozzáférjenek a csoportok adataihoz:

:::image type="content" source="media/configure-user-consent-groups/group-owner-consent.png" alt-text="Csoporttulajdonosi hozzájárulási beállítások":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Az Azure AD PowerShell előzetes verziójú modulját [(AzureADPreview)](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview)használva engedélyezheti vagy letilthatja, hogy a csoporttulajdonosok engedélyt adjanak a céges adatokhoz hozzáférő alkalmazásoknak a saját csoportjaikra.

1. Győződjön meg arról, hogy az [AzureADPreview modult](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview) használja. Ez a lépés akkor fontos, ha az [AzureAD](/powershell/module/azuread/) modult és az [AzureADPreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview) modult is telepítette.

    ```powershell
    Remove-Module AzureAD
    Import-Module AzureADPreview
    ```

1. Csatlakozzon az Azure AD PowerShellhez.

   ```powershell
   Connect-AzureAD
   ```

1. Lekéri a consent **policy settings (Hozzájárulási szabályzat beállításai)** címtárbeállítások aktuális értékét a bérlőben. Ehhez ellenőriznie kell, hogy létrejöttek-e a szolgáltatás címtárbeállítási, és ha nem, használja a megfelelő címtárbeállítási sablon értékeit.

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

1. A beállításértékek. Két beállításérték határozza meg, hogy mely felhasználók engedélyeznék, hogy egy alkalmazás hozzáférjen a csoport adataihoz:

    | Beállítás       | Típus         | Leírás  |
    | ------------- | ------------ | ------------ |
    | _EnableGroupSpecificConsent_   | Logikai | Jelző, amely jelzi, hogy a csoportok tulajdonosai adhatnak-e csoportspecifikus engedélyeket. |
    | _ConstrainGroupSpecificConsentToMembersOfGroupId_ | Guid | Ha _az EnableGroupSpecificConsent_ értéke "True", és ez az érték egy csoport objektumazonosítója, az azonosított csoport tagjai csoportspecifikus engedélyeket adhatnak a saját csoportjaiknak. |

1. Frissítse a kívánt konfiguráció beállításértékét:

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

* [Felhasználói hozzájárulási beállítások konfigurálása](configure-user-consent.md)
* [A rendszergazdai jóváhagyás munkafolyamatának konfigurálása](configure-admin-consent-workflow.md)
* [Útmutató az alkalmazásokhoz való hozzájárulás kezeléséhez és a hozzájárulási kérelmek kiértékeléséhez](manage-consent-requests.md)
* [Bérlőszintű rendszergazdai jóváhagyás megadása egy alkalmazáshoz](grant-admin-consent.md)
* [Engedélyek és jóváhagyás a Microsoft identitásplatformján](../develop/v2-permissions-and-consent.md)

Segítségért vagy a kérdéseire adott válaszokért:
* [Azure AD a Microsoft Q&A-n ](/answers/topics/azure-active-directory.html)