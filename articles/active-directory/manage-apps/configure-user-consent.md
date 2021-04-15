---
title: A végfelhasználók alkalmazásokra vonatkozó hozzájárulásának konfigurálása az Azure AD-vel
description: Megtudhatja, hogyan és mikor lehet kezelni, hogy a felhasználók hogyan és mikor járulnak hozzá olyan alkalmazásokhoz, amelyek hozzáférhetnek a szervezet adataihoz.
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
ms.openlocfilehash: 95a651f6201c9f60500c9191821edb7eb76b8535
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374437"
---
# <a name="configure-how-end-users-consent-to-applications"></a>A végfelhasználók alkalmazásokra vonatkozó hozzájárulásának konfigurálása

Alkalmazásait integrálhatja a Microsoft identitásplatformmal, így a felhasználók bejelentkezhetnek munkahelyi vagy iskolai fiókjukkal, és hozzáférhetnek a szervezeti adatokhoz, így gazdag adatvezérelt felhasználói élményt kínálnak.

Ahhoz, hogy egy alkalmazás hozzáfér a szervezet adataihoz, a felhasználónak engedélyeket kell adnunk az alkalmazásnak erre. A különböző engedélyek különböző hozzáférési szinteket engedik. Alapértelmezés szerint minden felhasználó engedélyt ad az alkalmazásoknak olyan engedélyekhez való hozzájárulására, amelyekhez nincs szükség rendszergazdai jóváhagyásra. A felhasználók például alapértelmezés szerint engedélyezhetik, hogy az alkalmazások hozzáférjenek a postaládájukhoz, de nem engedélyezhetik, hogy egy alkalmazás a szervezet összes fájljának olvasson és írjon.

Azáltal, hogy a felhasználók hozzáférést adhatnak az alkalmazásoknak az adatokhoz, a felhasználók könnyen hasznos alkalmazásokat szerezhetnek be, és hatékonyan dolgozhatnak. Bizonyos helyzetekben azonban ez a konfiguráció kockázatot jelenthet, ha nem figyelik és nem irányítják körültekintően.

> [!IMPORTANT]
> Annak érdekében, hogy csökkentse annak kockázatát, hogy rosszindulatú alkalmazások kísérelik megtévesztni a felhasználókat a szervezeti adatokhoz való hozzáférés megadásában, javasoljuk, hogy csak az ellenőrzött közzétevő által közzétett alkalmazásokhoz engedélyezze a felhasználói [jóváhagyást.](../develop/publisher-verification-overview.md)

## <a name="user-consent-settings"></a>Felhasználói hozzájárulási beállítások

Az alkalmazás-hozzájárulási szabályzatok azokat a feltételeket írják le, amelyeket az alkalmazás beleegyezése előtt meg kell adni. Ezek a szabályzatok a hozzáférést kérő alkalmazásra vonatkozó feltételeket, valamint az alkalmazás által kért engedélyeket is tartalmazhatnak.

Ha kiválasztja, hogy mely alkalmazás-hozzájárulási szabályzatok vonatkoznak az összes felhasználóra, korlátokat állíthat be arra, hogy mikor adhatnak engedélyt a végfelhasználók az alkalmazásoknak, és mikor kell rendszergazdai felülvizsgálatot és jóváhagyást kérniük:

* **Felhasználói hozzájárulás letiltása –** A felhasználók nem adhatnak engedélyeket az alkalmazásoknak. A felhasználók továbbra is bejelentkezhetnek az olyan alkalmazásokba, amelyekhez korábban hozzájárultak, vagy amelyeket a rendszergazdák a nevükben engedik, de nem kaphatnak engedélyt az új engedélyek vagy az új alkalmazások saját kezű kezű hozzájárulására. Csak azok a felhasználók fognak hozzájárulni az új alkalmazásokhoz, akik címtárbeli szerepkört kaptak, amely a hozzájárulás megadásának engedélyével rendelkezik.

* A felhasználók engedélyt kaphatnak az ellenőrzött közzétevőktől vagy a szervezettől származó **alkalmazásokhoz,** de csak ön által kiválasztott engedélyek esetén – Minden felhasználó csak olyan alkalmazásokat hagyhat el, amelyek közzétette egy ellenőrzött közzétevő és [a](../develop/publisher-verification-overview.md) bérlőben regisztrált alkalmazások. A felhasználók csak az Ön által "alacsony hatásúként" besorolt engedélyekhez járulnak hozzá. Az [engedélyeket osztályoznia](configure-permission-classifications.md) kell annak kiválasztásához, hogy a felhasználók mely engedélyekhez járulhatnak hozzá.

* **A felhasználók minden alkalmazáshoz** engedélyt kaphatnak – Ez a beállítás lehetővé teszi, hogy minden felhasználó elfogadja az olyan engedélyeket, amelyekhez nincs szükség rendszergazdai jóváhagyásra, bármely alkalmazáshoz.

* **Egyéni alkalmazás-hozzájárulási** szabályzat – A felhasználói jóváhagyásra vonatkozó [](manage-app-consent-policies.md#create-a-custom-app-consent-policy)feltételek további beállításaihoz létrehozhat egyéni alkalmazás-hozzájárulási szabályzatot, és konfigurálhatja őket a felhasználói hozzájárulás létrehozására.

# <a name="portal"></a>[Portál](#tab/azure-portal)

A felhasználói hozzájárulási beállítások konfigurálása a Azure Portal:

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) globális [rendszergazdaként.](../roles/permissions-reference.md#global-administrator)
1. Válassza **Azure Active Directory** Vállalati alkalmazások Hozzájárulás és engedélyek  >    >    >  **Felhasználói hozzájárulási beállítások lehetőséget.**
1. A **Felhasználói jóváhagyás az alkalmazásokhoz alatt** válassza ki az összes felhasználó számára konfigurálni kívánt hozzájárulási beállítást.
1. A **beállítások mentéséhez** válassza a Mentés lehetőséget.

:::image type="content" source="media/configure-user-consent/setting-for-all-users.png" alt-text="Felhasználói hozzájárulási beállítások":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Az Azure AD PowerShell előzetes verziójának legújabb modulját [(AzureADPreview)](/powershell/azure/active-directory/install-adv2?preserve-view=true&view=azureadps-2.0-preview)használva kiválaszthatja, hogy mely alkalmazás-hozzájárulási szabályzat szabályozza az alkalmazások felhasználói hozzájárulását.

#### <a name="disable-user-consent"></a>Felhasználói jóváhagyás letiltása

A felhasználói jóváhagyás letiltásához állítsa be a felhasználói jóváhagyást szabályozó hozzájárulási szabályzatokat üresen:

  ```powershell
  Set-AzureADMSAuthorizationPolicy `
     -Id "authorizationPolicy" `
     -PermissionGrantPolicyIdsAssignedToDefaultUserRole @()
  ```

#### <a name="allow-user-consent-subject-to-an-app-consent-policy"></a>Az alkalmazás-hozzájárulási szabályzat hatálya alá tartozó felhasználói jóváhagyás engedélyezése

A felhasználói jóváhagyás engedélyezése érdekében válassza ki, hogy melyik alkalmazás-hozzájárulási szabályzat szabályozza a felhasználók engedélyét az alkalmazásokhoz való hozzájárulás megadásához:

  ```powershell
  Set-AzureADMSAuthorizationPolicy `
     -Id "authorizationPolicy" `
     -PermissionGrantPolicyIdsAssignedToDefaultUserRole @("managePermissionGrantsForSelf.{consent-policy-id}")
  ```

Cserélje le a helyére annak a szabályzatnak az `{consent-policy-id}` azonosítóját, amit alkalmazni szeretne. Választhat egy ön által [létrehozott egyéni](manage-app-consent-policies.md#create-a-custom-app-consent-policy) alkalmazás-hozzájárulási szabályzatot, vagy választhat az alábbi beépített szabályzatok közül:

| ID (Azonosító) | Leírás |
|:---|:------------|
| microsoft-user-default-low | **Felhasználói jóváhagyás engedélyezése az ellenőrzött közzétevőktől származó alkalmazásokhoz a kiválasztott engedélyekhez**<br /> Csak az ellenőrzött közzétevőktől és a bérlőben regisztrált alkalmazásoktól származó korlátozott felhasználói jóváhagyást engedélyezze, és csak az "Alacsony hatás" besorolású engedélyekhez. (Ne felejtse el [](configure-permission-classifications.md) besorolni az engedélyeket annak kiválasztásához, hogy a felhasználók mely engedélyekhez járulhatnak hozzá.) |
| microsoft-user-default-legacy | **Felhasználói jóváhagyás engedélyezése alkalmazásokhoz**<br /> Ez a beállítás lehetővé teszi, hogy minden felhasználó elfogadja az olyan engedélyeket, amelyekhez nincs szükség rendszergazdai jóváhagyásra, bármely alkalmazáshoz |
  
Például a beépített szabályzat hatálya alá tartozó felhasználói jóváhagyás `microsoft-user-default-low` engedélyezéséhez:

```powershell
Set-AzureADMSAuthorizationPolicy `
   -Id "authorizationPolicy" `
   -PermissionGrantPolicyIdsAssignedToDefaultUserRole @("managePermissionGrantsForSelf.microsoft-user-default-low")
```

---

> [!TIP]
> [](configure-admin-consent-workflow.md) A rendszergazdai hozzájárulási munkafolyamat engedélyezésével a felhasználók kérhetik egy olyan alkalmazás rendszergazdai jóváhagyását és jóváhagyását, amelyhez a felhasználó nem járulhat hozzá – például ha a felhasználói jóváhagyás le van tiltva, vagy ha egy alkalmazás olyan engedélyeket kér, amelyek megadására a felhasználó nem jogosult.

## <a name="risk-based-step-up-consent"></a>Kockázatalapú, felfelé való lépéshez szükséges hozzájárulás

A kockázatalapú, felfelé irányuló hozzájárulás segít csökkenteni a felhasználók kitettségét az olyan rosszindulatú alkalmazásoknak, amelyek hamis hozzájárulási [kérelmeket hoznak.](/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants) Ha a Microsoft kockázatos végfelhasználói hozzájárulási kérelmet észlel, a kérelemhez a rendszergazdai hozzájárulás "lépésére" lesz szükség. Ez a funkció alapértelmezés szerint engedélyezve van, de csak akkor eredményez viselkedésváltozást, ha a végfelhasználói jóváhagyás engedélyezve van.

Kockázatos hozzájárulási kérelem észlelésekor a hozzájárulási kérés egy üzenetet jelenít meg, amely jelzi, hogy rendszergazdai jóváhagyásra van szükség. Ha a [rendszergazdai jóváhagyás kérésének munkafolyamata](configure-admin-consent-workflow.md) engedélyezve van, a felhasználó közvetlenül a hozzájárulási kérésből elküldheti a kérést egy rendszergazdának további ellenőrzésre. Ha nincs engedélyezve, a következő üzenet jelenik meg:

* **AADSTS90094:** &lt; A clientAppDisplayName tulajdonságnak engedélyre van szüksége a szervezetben csak egy rendszergazda által &gt; megadó erőforrások eléréséhez. Kérjen engedélyt a rendszergazdától az alkalmazáshoz, hogy használhassa azt.

Ebben az esetben a rendszer naplózza a naplózási eseményeket az "ApplicationManagement" kategóriával, a "Hozzájárulás az alkalmazáshoz" tevékenységtípussal és a "Kockázatos alkalmazás észlelve" állapot okával.

> [!IMPORTANT]
> A rendszergazdáknak alaposan [ki kell](manage-consent-requests.md#evaluating-a-request-for-tenant-wide-admin-consent) értékelniük az összes hozzájárulási kérelmet a kérelem jóváhagyása előtt, különösen akkor, ha a Microsoft kockázatot észlelt.

### <a name="disable-or-re-enable-risk-based-step-up-consent-using-powershell"></a>Kockázatalapú, részletes hozzájárulás letiltása vagy újra engedélyezése a PowerShell használatával

Az Azure AD PowerShell előzetes verziójának moduljával [(AzureADPreview)](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview)letilthatja a rendszergazdai jóváhagyásra vonatkozó lépést olyan esetekben, amikor a Microsoft kockázat észlelését észleli, vagy újra engedélyezheti, ha korábban letiltották.

1. Győződjön meg arról, hogy az [AzureADPreview modult](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview) használja. Ez a lépés akkor fontos, ha telepítette az [AzureAD](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0) modult és az [AzureADPreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview) modult is).

    ```powershell
    Remove-Module AzureAD
    Import-Module AzureADPreview
    ```

1. Csatlakozás az Azure AD PowerShellhez.

   ```powershell
   Connect-AzureAD
   ```

1. A hozzájárulási szabályzat beállításai címtárbeállítás **aktuális értékének lekérése** a bérlőben. Ehhez ellenőriznie kell, hogy létrejöttek-e a szolgáltatás címtárbeállítási, és ha nem, használja a megfelelő címtárbeállítási sablon értékeit.

    ```powershell
    $consentSettingsTemplateId = "dffd5d46-495d-40a9-8e21-954ff55e198a" # Consent Policy Settings
    $settings = Get-AzureADDirectorySetting -All $true | Where-Object { $_.TemplateId -eq $consentSettingsTemplateId }

    if (-not $settings) {
        $template = Get-AzureADDirectorySettingTemplate -Id $consentSettingsTemplateId
        $settings = $template.CreateDirectorySetting()
    }

    $riskBasedConsentEnabledValue = $settings.Values | ? { $_.Name -eq "BlockUserConsentForRiskyApps" }
    ```

1. A beállítási értékeket a következő okkal értheti meg:

    | Beállítás       | Típus         | Leírás  |
    | ------------- | ------------ | ------------ |
    | _BlockUserConsentForRiskyApps_   | Logikai |  Jelölő, amely jelzi, hogy a rendszer letiltja-e a felhasználói jóváhagyást kockázatos kérés észlelésekor. |

1. Frissítse a kívánt konfiguráció beállításértékét:

    ```powershell
    # Disable risk-based step-up consent entirely
    $riskBasedConsentEnabledValue.Value = "False"
    ```

    ```powershell
    # Re-enable risk-based step-up consent, if disabled previously
    $riskBasedConsentEnabledValue.Value = "True"
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

## <a name="next-steps"></a>Következő lépések

További tudnivalók:

* [Felhasználói hozzájárulási beállítások konfigurálása](configure-user-consent.md)
* [Alkalmazás-hozzájárulási szabályzatok kezelése](manage-app-consent-policies.md)
* [A rendszergazdai jóváhagyás munkafolyamatának konfigurálása](configure-admin-consent-workflow.md)
* [Útmutató az alkalmazásokhoz való hozzájárulás kezeléséhez és a hozzájárulási kérelmek kiértékeléséhez](manage-consent-requests.md)
* [Bérlőszintű rendszergazdai jóváhagyás megadása egy alkalmazáshoz](grant-admin-consent.md)
* [Engedélyek és jóváhagyás a Microsoft identitásplatformján](../develop/v2-permissions-and-consent.md)

Segítségért vagy a kérdéseire adott válaszokért:
* [Azure AD a Microsoft Q&A-n.](/answers/topics/azure-active-directory.html)