---
title: Annak konfigurálása, hogy a végfelhasználók miként egyeznek az Azure AD-vel az alkalmazásokkal
description: Megtudhatja, hogyan és mikor férhet hozzá a felhasználók a szervezet adataihoz hozzáférő alkalmazásokhoz.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 06/01/2020
ms.author: kenwith
ms.reviewer: arvindh, luleon, phsignor
ms.custom: contperf-fy21q2
ms.openlocfilehash: 184263badc909881f597213a222272bd675fc362
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2021
ms.locfileid: "99051974"
---
# <a name="configure-how-end-users-consent-to-applications"></a>A végfelhasználók alkalmazásokra vonatkozó hozzájárulásának konfigurálása

Az alkalmazásait a Microsoft Identity platformmal integrálva engedélyezheti a felhasználók számára, hogy munkahelyi vagy iskolai fiókjával jelentkezzenek be, és hozzáférjenek a szervezet adataihoz, és így gazdag adatvezérelt felhasználói élményt nyújtsanak.

Ahhoz, hogy egy alkalmazás hozzáférhessen a szervezet adataihoz, a felhasználónak meg kell adnia az alkalmazás engedélyeit. A különböző engedélyek különböző hozzáférési szinteket tesznek lehetővé. Alapértelmezés szerint az összes felhasználó beleegyezik abba, hogy a rendszergazdai jogosultságokat nem igénylő engedélyekkel rendelkező alkalmazások is beleegyeznek. Alapértelmezés szerint például egy felhasználó hozzájárulhat ahhoz, hogy egy alkalmazás hozzáférjen a postaládához, de nem tud beleegyezni, hogy az alkalmazás korlátlanul hozzáférhessen a szervezet összes fájljának olvasásához és írásához.

Azáltal, hogy lehetővé teszi, hogy a felhasználók alkalmazásokat adjanak az adathozzáféréshez, a felhasználók könnyen vásárolhatnak hasznos alkalmazásokat, és hatékonyan dolgozhatnak. Bizonyos helyzetekben azonban ez a konfiguráció akkor is jelenthet kockázatot, ha a rendszer nem figyeli és nem ellenőrzi körültekintően.

> [!IMPORTANT]
> Annak érdekében, hogy csökkentse a felhasználókat a szervezet adataihoz való hozzáférést biztosító rosszindulatú alkalmazások kockázatát, javasoljuk, hogy csak a [hitelesített közzétevő](../develop/publisher-verification-overview.md)által közzétett alkalmazásokhoz engedélyezze a felhasználói jóváhagyást.

## <a name="user-consent-settings"></a>Felhasználói beleegyező beállítások

Az alkalmazás-engedélyezési szabályzatok azokat a feltételeket írják le, amelyeknek teljesülniük kell ahhoz, hogy az alkalmazás jóvá lehessen hagyni. Ezek a szabályzatok tartalmazhatják a hozzáférést kérő alkalmazás feltételeit, valamint az alkalmazás által kért engedélyeket.

Ha kiválasztja, hogy mely alkalmazás-jóváhagyási szabályzatok érvényesek az összes felhasználóra vonatkozóan, megadhatja a korlátozásokat, amikor a végfelhasználók jóváhagyják az alkalmazásokat

* **Felhasználói jóváhagyás letiltása** – a felhasználók nem adhatnak engedélyeket az alkalmazásoknak. A felhasználók továbbra is bejelentkezhetnek azokba az alkalmazásokba, amelyekre korábban beleegyezett, vagy amelyeket a rendszergazdák jóváhagytak a nevükben, de nem jogosultak új engedélyekre vagy új alkalmazásokra. Csak azok a felhasználók férhetnek hozzá az új alkalmazásokhoz, akik olyan címtárbeli szerepkört adtak meg, amely belefoglalja a jóváhagyást.

* A felhasználók beleegyeznek **az ellenőrzött közzétevők vagy a szervezete alkalmazásaiba, de csak a kiválasztott engedélyek esetében** – minden felhasználó csak olyan alkalmazásokat tud jóváhagyni, amelyeket egy [ellenőrzött közzétevő](../develop/publisher-verification-overview.md) és a bérlőben regisztrált alkalmazások tettek közzé. A felhasználók csak az "alacsony hatású" besorolású engedélyekkel rendelkezhetnek. Be kell [sorolnia az engedélyeket](configure-permission-classifications.md) annak kiválasztásához, hogy a felhasználók milyen engedélyeket vehetnek igénybe.

* A felhasználók beleegyeznek **az összes** alkalmazásba – ez a beállítás lehetővé teszi, hogy minden felhasználó beleegyezést kérjen bármely alkalmazáshoz a rendszergazdai hozzájárulást nem igénylő engedélyekhez.

* **Egyéni alkalmazás-engedélyezési házirend** – a felhasználói belekötést szabályozó feltételekkel kapcsolatos további lehetőségekért [létrehozhat egyéni alkalmazás-engedélyezési szabályzatot](manage-app-consent-policies.md#create-a-custom-app-consent-policy), és beállíthatja, hogy a felhasználói beleegyezik.

# <a name="portal"></a>[Portál](#tab/azure-portal)

A felhasználói beleegyező beállítások konfigurálása a Azure Portal használatával:

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) [globális rendszergazdaként](../roles/permissions-reference.md#global-administrator--company-administrator).
1. Válassza **Azure Active Directory**  >  **vállalati alkalmazások**  >  **beleegyezett és engedélyek**  >  **felhasználói beleegyező beállításait**.
1. **Az alkalmazások felhasználói beleegyezike** területen válassza ki, hogy az összes felhasználóra vonatkozóan melyik beleegyező beállítást szeretné konfigurálni.
1. A beállítások mentéséhez kattintson a **Mentés** gombra.

:::image type="content" source="media/configure-user-consent/setting-for-all-users.png" alt-text="Felhasználói beleegyező beállítások":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Az [AzureADPreview](/powershell/azure/active-directory/install-adv2?preserve-view=true&view=azureadps-2.0-preview)legújabb Azure ad PowerShell-előnézeti modullal kiválaszthatja, hogy melyik alkalmazás-engedélyezési házirend szabályozza az alkalmazások felhasználói engedélyeit.

#### <a name="disable-user-consent"></a>Felhasználói engedély letiltása

A felhasználói engedély letiltásához állítsa be a felhasználói beleegyezett a következőre vonatkozó engedélyezési házirendeket:

  ```powershell
  Set-AzureADMSAuthorizationPolicy `
     -Id "authorizationPolicy" `
     -PermissionGrantPolicyIdsAssignedToDefaultUserRole @()
  ```

#### <a name="allow-user-consent-subject-to-an-app-consent-policy"></a>Alkalmazás-engedélyezési szabályzat hatálya alá tartozó felhasználói engedély engedélyezése

Ha engedélyezni szeretné a felhasználói hozzájárulást, válassza ki, hogy melyik alkalmazás-engedélyezési szabályzatnak kell szabályoznia a felhasználók engedélyeit az alkalmazások jóváhagyásának biztosításához:

  ```powershell
  Set-AzureADMSAuthorizationPolicy `
     -Id "authorizationPolicy" `
     -PermissionGrantPolicyIdsAssignedToDefaultUserRole @("managePermissionGrantsForSelf.{consent-policy-id}")
  ```

A helyére írja be az `{consent-policy-id}` alkalmazni kívánt szabályzat azonosítóját. Kiválaszthat egy létrehozott [egyéni alkalmazás-engedélyezési szabályzatot](manage-app-consent-policies.md#create-a-custom-app-consent-policy) , vagy a következő beépített szabályzatok közül választhat:

| ID (Azonosító) | Description |
|:---|:------------|
| Microsoft-User-default – alacsony | **A felhasználók jóváhagyásának engedélyezése az ellenőrzött közzétevők alkalmazásai számára a kiválasztott engedélyekhez**<br /> Korlátozott felhasználói jóváhagyás engedélyezése csak az ellenőrzött közzétevők és a bérlőben regisztrált alkalmazások alkalmazásai számára, és csak az "alacsony hatás" besorolású engedélyek esetében. (Ne felejtse el [besorolni az engedélyeket](configure-permission-classifications.md) annak kiválasztásához, hogy a felhasználók milyen engedélyeket adhatnak meg.) |
| Microsoft-User-default-Legacy | **Felhasználói beleegyezett alkalmazások engedélyezése**<br /> Ez a beállítás lehetővé teszi, hogy minden felhasználó beleegyezést kérjen minden olyan engedélyhez, amely nem igényel rendszergazdai hozzájárulást bármely alkalmazáshoz |
  
Ha például engedélyezni kívánja a felhasználói engedélyt a beépített szabályzattal `microsoft-user-default-low` :

```powershell
Set-AzureADMSAuthorizationPolicy `
   -Id "authorizationPolicy" `
   -PermissionGrantPolicyIdsAssignedToDefaultUserRole @("managePermissionGrantsForSelf.microsoft-user-default-low")
```

---

> [!TIP]
> [Engedélyezze a rendszergazdai hozzájárulási munkafolyamatot](configure-admin-consent-workflow.md) , amely lehetővé teszi, hogy a felhasználók a rendszergazda felülvizsgálati és jóváhagyási kérelmét kérjenek a felhasználótól, ha például a felhasználó hozzájárulása le van tiltva, vagy ha egy alkalmazás olyan engedélyeket kér, amelyet a felhasználó nem engedélyez.

## <a name="risk-based-step-up-consent"></a>Kockázatalapú lépésekre vonatkozó beleegyezett

A kockázatalapú lépésekre [vonatkozó](/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants)beleegyezikés segít csökkenteni a felhasználókat olyan kártékony alkalmazásokkal szemben, amelyek nem tesznek lehetővé jogcímeket. Ha a Microsoft egy kockázatos végfelhasználói kérést észlel, a kérelemhez "Step-up" értékre van szükség a rendszergazdai beleegyező művelethez. Ez a funkció alapértelmezés szerint engedélyezve van, de csak akkor eredményezi a viselkedést, ha engedélyezve van a végfelhasználói engedély.

Kockázatos beleegyezési kérelem észlelésekor a beleegyezés kérése üzenet jelenik meg, amely jelzi, hogy a rendszergazda jóváhagyása szükséges. Ha a [rendszergazdai hozzájárulási kérelem munkafolyamata](configure-admin-consent-workflow.md) engedélyezve van, a felhasználó a kérést egy rendszergazdának küldheti el további áttekintés céljából közvetlenül a hozzájárulási kérésből. Ha nincs engedélyezve, a következő üzenet jelenik meg:

* **AADSTS90094:** &lt; &gt; a clientAppDisplayName engedélyre van szüksége a szervezet erőforrásaihoz való hozzáféréshez, csak a rendszergazda adhat meg. Kérjen engedélyt a rendszergazdától az alkalmazáshoz, hogy használhassa azt.

Ebben az esetben a naplózási esemény a "ApplicationManagement" kategóriába kerül, az "alkalmazás beleegyezése", a "kockázatos alkalmazás észlelése" állapot oka is.

> [!IMPORTANT]
> A rendszergazdáknak a kérelmek jóváhagyása előtt körültekintően ki kell [értékelniük az összes jóváhagyási kérést](manage-consent-requests.md#evaluating-a-request-for-tenant-wide-admin-consent) , különösen akkor, ha a Microsoft kockázatot észlelt.

### <a name="disable-or-re-enable-risk-based-step-up-consent-using-powershell"></a>Kockázatalapú lépésenkénti belefoglalási engedély letiltása vagy újbóli engedélyezése a PowerShell használatával

A [AzureADPreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview)Azure ad PowerShell-előnézeti modullal letilthatja a rendszergazdai belefoglaláshoz szükséges lépéseket olyan esetekben, amikor a Microsoft észleli a kockázatokat, vagy ha korábban letiltották, újra engedélyezi.

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

    $riskBasedConsentEnabledValue = $settings.Values | ? { $_.Name -eq "BlockUserConsentForRiskyApps" }
    ```

1. A beállítások értékének megismerése:

    | Beállítás       | Típus         | Description  |
    | ------------- | ------------ | ------------ |
    | _BlockUserConsentForRiskyApps_   | Logikai |  Jelző, amely azt jelzi, hogy a felhasználó beleegyezik-e a kockázatos kérelem észlelésekor. |

1. A kívánt konfiguráció frissítési beállításainak értéke:

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

* [Felhasználói beleegyező beállítások konfigurálása](configure-user-consent.md)
* [Alkalmazás-hozzájárulási szabályzatok kezelése](manage-app-consent-policies.md)
* [Rendszergazdai engedélyezési munkafolyamat konfigurálása](configure-admin-consent-workflow.md)
* [Megtudhatja, hogyan kezelheti az alkalmazásokra vonatkozó beleegyezett, és hogyan értékelheti a hozzájárulásukat](manage-consent-requests.md)
* [Bérlőszintű rendszergazdai jóváhagyás megadása egy alkalmazáshoz](grant-admin-consent.md)
* [Engedélyek és beleegyezett a Microsoft Identity platform](../develop/v2-permissions-and-consent.md)

Segítség kérése vagy válaszok keresése a kérdéseire:
* [Azure AD a Microsoft Q&A-ben.](https://docs.microsoft.com/answers/topics/azure-active-directory.html)