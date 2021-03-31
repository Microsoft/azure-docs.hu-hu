---
title: Engedélyek az Azure Active Directoryban | Microsoft Docs
description: Megismerheti az Azure Active Directoryban lévő engedélyeket és azok használatának módját.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: jesakowi
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 2b85115d905cb6a7eb7c6aed64a4834425d2f1d7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92366394"
---
# <a name="permissions-and-consent-in-the-azure-active-directory-v10-endpoint"></a>Engedélyek és belefoglalt engedély a Azure Active Directory v 1.0-végponton

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Az Azure Active Directory (Azure AD) jelentősen igénybe veszi az OAuth és az OpenID Connect (OIDC) folyamatok engedélyeit is. Amikor az alkalmazás hozzáférési jogkivonatot szerez az Azure AD-tól, a hozzáférési jogkivonat olyan jogcímeket foglal magába, amelyek leírják az alkalmazás által adott erőforrással kapcsolatban használt engedélyeket.

Az *engedélyek*– más néven *hatókörök*– lehetővé teszik az erőforrás egyszerű engedélyezését, mert az erőforrásnak csak azt kell ellenőriznie, hogy a jogkivonat tartalmazza-e a megfelelő engedélyeket az alkalmazás által meghívott API-hoz.

## <a name="types-of-permissions"></a>Engedélytípusok

Az Azure AD kétféle engedélyt határoz meg:

* **Delegált engedélyek** – Olyan alkalmazások használják, amelyeken bejelentkezett felhasználó szerepel. Ezekben az alkalmazásokban a felhasználó vagy egy rendszergazda elfogadja az alkalmazás által kért engedélyeket, és az alkalmazáshoz engedély van delegálva, amely szerint az alkalmazás a bejelentkezett felhasználóként viselkedik, amikor API-t hív. Az API-tól függően előfordulhat, hogy a felhasználó nem tud közvetlenül hozzájárulni az API-hoz, és ehelyett a [rendszergazdának meg kell adnia a "rendszergazdai jóváhagyást"](../develop/howto-convert-app-to-be-multi-tenant.md).
* **Alkalmazásengedélyek** – Olyan alkalmazások használják, amelyek bejelentkezett felhasználó nélkül futnak, például háttérszolgáltatásokként vagy démonokként futó alkalmazások. Az alkalmazás engedélyeit csak a [rendszergazdák](../develop/v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant) vehetik igénybe, mivel általában hatékonyak, és lehetővé teszik az adathozzáférést a felhasználói határok között, illetve olyanokat, amelyek egyébként a rendszergazdákra korlátozódnak. Azok a felhasználók, akik az erőforrás-alkalmazás tulajdonosaként vannak definiálva (azaz az engedélyeket közzétevő API), a saját API-k számára is jogosultak az alkalmazás engedélyeinek megadására.

A hatályos engedélyek olyan engedélyek, amelyekkel az alkalmazás rendelkezik, amikor kéréseket küld egy API-nak. 

* Delegált jogosultságok esetén az alkalmazás hatályos engedélyei az alkalmazás számára (hozzájárulással) megadott delegált engedélyek és a jelenleg bejelentkezett felhasználó jogosultságainak legalacsonyabb szintű metszetei. Az alkalmazásnak soha nem lehet több jogosultsága, mint a bejelentkezett felhasználónak. A cégeken belül a bejelentkezett felhasználó jogosultságait szabályzat vagy egy vagy több rendszergazdai szerepkör tagsága határozhatja meg. Ha meg szeretné tudni, hogy mely rendszergazdai szerepkörök jogosultak a delegált engedélyekre, tekintse meg az [Azure ad-beli rendszergazdai szerepkörre vonatkozó engedélyeket](../roles/permissions-reference.md).
    Tegyük fel például, hogy az alkalmazás a `User.ReadWrite.All` delegált engedélyt kapta meg a Microsoft Graphban. Ez az engedély névlegesen ad engedélyt az alkalmazás számára egy cégben lévő összes felhasználó profiljának olvasásához és frissítéséhez. Ha a bejelentkezett felhasználó egy globális rendszergazda, az alkalmazás frissíteni tudja a cégben lévő összes felhasználó profilját. Ha azonban a bejelentkezett felhasználó nem rendszergazdai szerepkörben van, az alkalmazás csak a bejelentkezett felhasználó profilját tudja frissíteni. Nem tudja frissíteni a cégben lévő többi felhasználó profilját, mert az a felhasználó nem rendelkezik ilyen jogosultságokkal, akinek a nevében eljár.
* Az alkalmazásengedélyek esetén az alkalmazás hatályos engedélyeinek köre az engedély által sugallt jogosultságok teljes szintje. A `User.ReadWrite.All` alkalmazásengedéllyel rendelkező alkalmazás például a cégben lévő összes felhasználó profilját frissítheti.

## <a name="permission-attributes"></a>Engedélyek attribútumai
Az Azure AD-ben az engedélyek rendelkeznek néhány tulajdonsággal, amelyek segítenek a felhasználóknak, a rendszergazdáknak és az alkalmazásfejlesztőknek annak tájékozott eldöntésében, hogy az engedély mihez nyújt hozzáférést.

> [!NOTE]
> Az Azure Portalon vagy a PowerShellben tekintheti meg az Azure AD-alkalmazások vagy a szolgáltatásnevek által elérhetővé tett engedélyeket. Ezzel a szkripttel megtekintheti a Microsoft Graph által elérhetővé tett engedélyeket.
> ```powershell
> Connect-AzureAD
> 
> # Get OAuth2 Permissions/delegated permissions
> (Get-AzureADServicePrincipal -filter "DisplayName eq 'Microsoft Graph'").OAuth2Permissions
> 
> # Get App roles/application permissions
> (Get-AzureADServicePrincipal -filter "DisplayName eq 'Microsoft Graph'").AppRoles
> ```

| Tulajdonság neve | Leírás | Példa |
| --- | --- | --- |
| `ID` | Ez az engedélyt egyedi módon azonosító GUID-érték. | 570282fd-fa5c-430d-a7fd-fc8dc98a9dca |
| `IsEnabled` | Jelzi, hogy ez az engedély használható-e. | true |
| `Type` | Jelzi, hogy ez az engedély a felhasználó vagy a rendszergazda jóváhagyását igényli-e. | User |
| `AdminConsentDescription` | Ez a rendszergazdai jóváhagyás használata esetén a rendszergazdák számára megjelenő leírás. | Lehetővé teszi, hogy az alkalmazás elolvassa a felhasználók postafiókjában lévő e-maileket. |
| `AdminConsentDisplayName` | Ez a rendszergazdai jóváhagyás használata esetén a rendszergazdák számára megjelenő rövid név. | Felhasználók levelezésének olvasása |
| `UserConsentDescription` | Ez a felhasználói jóváhagyás használata esetén a felhasználók számára megjelenő leírás. |  Lehetővé teszi, hogy az alkalmazás elolvassa a postafiókjában lévő e-maileket. |
| `UserConsentDisplayName` | A felhasználói jóváhagyás használata esetén a felhasználók számára megjelenő rövid név. | Levelezés olvasása |
| `Value` | Az OAuth 2.0 engedélyezési folyamatok során az engedély azonosítására használt sztring. `Value` az alkalmazásazonosító URI-sztringjével is kombinálható a teljes engedélynév kialakítása érdekében. | `Mail.Read` |

## <a name="types-of-consent"></a>Jóváhagyástípusok

Az Azure AD-ben lévő alkalmazások a jóváhagyásokra támaszkodnak a szükséges erőforrások vagy API-k elérése érdekében. Különböző típusú jóváhagyások léteznek, amelyeket az alkalmazásnak esetleg ismernie kell a sikeres működés érdekében. Az engedélyek meghatározásakor azt is tudnia kell, hogyan fognak hozzáférni a felhasználók az alkalmazáshoz vagy az API-hoz.

* **Statikus felhasználói jóváhagyás** – Automatikusan történik az [OAuth 2.0 engedélyezési folyamat](v1-protocols-oauth-code.md#request-an-authorization-code) során, amikor megadja az erőforrást, amellyel az alkalmazás kommunikálni szeretne. Statikus felhasználói jóváhagyás használata esetén az alkalmazáshoz már korábban meg kellett adni az összes szükséges engedélyt az Azure Portalon, az alkalmazás konfigurációjában. Ha a felhasználó (vagy helyzettől függően a rendszergazda) nem adta a jóváhagyását ehhez az alkalmazáshoz, akkor az Azure AD megkéri a felhasználót, hogy most egyezzen bele. 

    További információ az API-k statikus készletének hozzáférését kérő Azure AD-alkalmazások regisztrálásáról.
* **Dinamikus felhasználói jóváhagyás** – Ez a v2 Azure AD alkalmazásmodell egyik funkciója. Ebben az esetben az alkalmazás a [v2-alkalmazások OAuth 2.0 engedélyezési folyamatához](../develop/v2-permissions-and-consent.md#requesting-individual-user-consent) szükséges engedélyeket kér. Ha a felhasználó még nem hagyta jóvá, a rendszer most kéri erre. [További információ a dinamikus jóváhagyásról](./azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent).

    > [!IMPORTANT]
    > A dinamikus jóváhagyás kényelmes lehet, de nagy kihívást jelent a rendszergazdai jóváhagyást igénylő engedélyek számára, mert a rendszergazdai jóváhagyás használatakor ezek az engedélyek ismeretlenek a jóváhagyáskor. Ha rendszergazdai jogosultsági szintű engedélyekre van szüksége, vagy ha az alkalmazás dinamikus beleegyezik, akkor regisztrálnia kell az összes engedélyt a Azure Portal (nem csak a rendszergazdai jogosultságokat igénylő engedélyek részhalmaza). Ez lehetővé teszi a bérlői rendszergazdák számára, hogy az összes felhasználó nevében hozzájárulásukat adjanak.
  
* **Rendszergazdai jóváhagyás** – Akkor szükséges, amikor az alkalmazásnak hozzáférésre van szüksége bizonyos magas szintű jogosultságokkal rendelkező engedélyekhez. A rendszergazdai jóváhagyás biztosítja, hogy a rendszergazdák további vezérlőkkel rendelkezzenek, mielőtt engedélyeznék, hogy az alkalmazások vagy felhasználók magas jogosultságú adatokat érjenek el a cégben. [További információ a rendszergazdai jóváhagyás megadásáról](../develop/v2-permissions-and-consent.md#using-the-admin-consent-endpoint).

## <a name="best-practices"></a>Ajánlott eljárások

### <a name="client-best-practices"></a>Ajánlott eljárások az ügyfélhez

- Csak olyan engedélyeket kérjen, amelyekre szüksége van az alkalmazásának. A túl sok engedéllyel rendelkező alkalmazások biztonságának sérülése esetén nagy a felhasználói adatok felfedésének kockázata.
- Az alkalmazása által támogatott forgatókönyv alapján választhat a delegált engedélyek és az alkalmazásengedélyek között.
    - Ha a hívást egy felhasználó nevében végzi el, mindig delegált engedélyeket használjon.
    - Csak akkor használjon alkalmazásengedélyeket, ha az alkalmazás nem interaktív, és nem végez hívásokat egy konkrét felhasználó nevében. Az alkalmazásengedélyek kiemelt jogosultságokkal rendelkeznek, alkalmazásuk csak akkor ajánlott, ha feltétlenül szükséges.
- Ha v2.0-végponton alapuló alkalmazást használ, mindig úgy állítsa be a statikus engedélyeket (amelyek az alkalmazásregisztrációban vannak megadva), hogy azok legyenek a futásidőben kért dinamikus engedélyek (amelyek a kódban vannak megadva, és lekérdezési paraméterként lesznek továbbküldve az engedélyezési kérelemben) kibővítései, hogy az olyan forgatókönyvek, mint a rendszergazdai jóváhagyás, megfelelően működhessenek.

### <a name="resourceapi-best-practices"></a>Erőforrás/API – ajánlott eljárások

- Az API-kat elérhetővé tévő erőforrásoknak az általuk védett adatokra vagy műveletekre jellemző engedélyeket kell meghatározniuk. Az ajánlott eljárás követésével biztosítható, hogy az ügyfelek ne számukra felesleges adatok hozzáférésére szóló engedélyeket kapjanak, és hogy a felhasználók jól ismerjék, milyen adatok hozzáférését hagyják jóvá.
- Az erőforrásoknak külön kell meghatározniuk a `Read` és a `ReadWrite` engedélyeket.
- Az erőforrásoknak `Admin` engedélyekként kell jelölniük az összes olyan engedélyt, amelyek felhasználói határokon keresztül engedélyezik az adatok hozzáférését.
- Az erőforrásoknak követniük kell a `Subject.Permission[.Modifier]` elnevezési mintát, ahol:
  - `Subject` megfelel az elérhető adattípusnak
  - `Permission` megfelel annak a műveletnek, amelyet a felhasználó az adott adatmennyiségre vonatkozóan igénybe vehet
  - `Modifier` egy másik engedély szakterületének leírására van szükség
    
    Például:
  - Mail.Read – Lehetővé teszi, hogy a felhasználók elolvassák a leveleket.
  - Mail.ReadWrite – Lehetővé teszi, hogy a felhasználók elolvassák a leveleket és leveleket írjanak.
  - Mail.ReadWrite.All – Lehetővé teszi, hogy egy rendszergazda vagy felhasználó elérje a cégben lévő összes levelet.