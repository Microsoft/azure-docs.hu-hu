---
title: Felhasználói hozzáférés kezelése Azure Active Directory B2Cban | Microsoft Docs
description: Megtudhatja, hogyan azonosíthatja a kiskorúakat, begyűjtheti a születési és az ország/régió adatait, és elvégezheti az alkalmazás használati feltételeinek elfogadását Azure AD B2C használatával.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/09/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 0ee26e7fe74d87f7b20f9a28b049b8043b376273
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102518056"
---
# <a name="manage-user-access-in-azure-active-directory-b2c"></a>Felhasználói hozzáférés kezelése Azure Active Directory B2C

Ez a cikk azt ismerteti, hogyan kezelheti az alkalmazásokhoz való felhasználói hozzáférést Azure Active Directory B2C (Azure AD B2C) használatával. A hozzáférés-kezelés az alkalmazásban az alábbiakat tartalmazza:

- A kiskorúak azonosítása és az alkalmazáshoz való felhasználói hozzáférés szabályozása.
- Szülői engedély kérése kiskorúak számára az alkalmazások használatához.
- A születési és az ország/régió adatainak összegyűjtése a felhasználóktól.
- Használati feltételek és kapuzás-hozzáférés rögzítése.

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

## <a name="control-minor-access"></a>Kisebb hozzáférés szabályozása

Az alkalmazások és a szervezetek dönthetnek úgy, hogy letiltják a kiskorúakat olyan alkalmazások és szolgáltatások használatával, amelyek nem célozzák meg a célközönséget. Alternatív megoldásként az alkalmazások és a szervezetek dönthetnek úgy is, hogy elfogadják a kiskorúakat, és ezt követően kezelik a szülői engedélyt, és lehetővé teszik az üzleti szabályok által diktált és a szabályozás által megengedett kiskorúak számára engedélyezett élményeket.

Ha a felhasználó másodlagosként van azonosítva, a felhasználói folyamat a következő három lehetőség egyikére állítható be Azure AD B2Cban:

- **Aláírt JWT id_token visszaküldése az alkalmazásnak**: a felhasználó regisztrálva van a címtárban, és egy tokent ad vissza az alkalmazásnak. Az alkalmazás ezután az üzleti szabályok alkalmazásával folytatja az alkalmazást. Előfordulhat például, hogy az alkalmazás egy szülői engedélyezési folyamattal folytatja a műveletet. Ha ezt a módszert szeretné használni, válassza a **beszerzésimennyiség** és **consentProvidedForMinor** jogcímek fogadása az alkalmazásból lehetőséget.

- **ALÁÍRATLAN JSON-token küldése az alkalmazásnak**: Azure ad B2C értesíti az alkalmazást, hogy a felhasználó kiskorú, és megadja a felhasználó szülői beleegyező állapotát. Az alkalmazás ezután az üzleti szabályok alkalmazásával folytatja az alkalmazást. Egy JSON-jogkivonat nem végez sikeres hitelesítést az alkalmazással. Az alkalmazásnak a JSON-tokenben foglalt jogcímek alapján kell feldolgoznia a nem hitelesített felhasználót, amely tartalmazhatja a **nevet**, az **e-maileket**, a **beszerzésimennyiség** és a **consentProvidedForMinor**.

- **A felhasználó letiltása**: Ha a felhasználó kiskorú, és nem adta meg a szülői engedélyt, a Azure ad B2C értesíti a felhasználót arról, hogy le vannak tiltva. Nem lett kiadva jogkivonat, a hozzáférés le van tiltva, és a felhasználói fiók nem jön létre a regisztráció során. Az értesítés megvalósításához meg kell adnia egy megfelelő HTML/CSS-tartalom lapot, amely tájékoztatja a felhasználót, és bemutatja a megfelelő beállításokat. Az alkalmazás nem igényel további műveletet az új regisztrációhoz.

## <a name="get-parental-consent"></a>Szülői engedély beszerzése

Az alkalmazásra vonatkozó szabályozástól függően előfordulhat, hogy a szülői jóváhagyást egy felnőttként ellenőrzött felhasználónak kell megadnia. A Azure AD B2C nem biztosít olyan felhasználói élményt, amely ellenőrzi egy adott életkort, és lehetővé teszi, hogy egy ellenőrzött felnőtt a szülői jóváhagyást egy kisebb értékre engedélyezze. Ezt a folyamatot az alkalmazásnak vagy egy másik szolgáltatónak kell megadnia.

A következő példa egy felhasználói folyamatot mutat be a szülői engedély összegyűjtéséhez:

1. A [Microsoft Graph API](/graph/use-the-api) -művelet a felhasználót másodlagosként azonosítja, és aláíratlan JSON-token formájában adja vissza az alkalmazás felhasználói adatát.

2. Az alkalmazás feldolgozza a JSON-tokent, és megjelenít egy képernyőt a kiskorú számára, és értesíti arról, hogy a szülői beleegyezés szükséges, és kéri a szülő online beleegyezését.

3. Azure AD B2C egy olyan bejelentkezési utat mutat be, amelyet a felhasználó a szokásos módon tud bejelentkezni, és a **legalAgeGroupClassification = "minorWithParentalConsent"** értékkel rendelkező alkalmazásnak ad ki jogkivonatot. Az alkalmazás gyűjti a szülő e-mail-címét, és ellenőrzi, hogy a szülő felnőtt-e. Ehhez megbízható forrást használ, mint például a nemzeti azonosító iroda, a licenc-ellenőrzés vagy a hitelkártya-igazolás. Ha az ellenőrzés sikeres, az alkalmazás a Azure AD B2C felhasználói folyamat használatával kéri a kiskorút a bejelentkezéshez. Ha a jóváhagyás megtagadva (például **legalAgeGroupClassification = "minorWithoutParentalConsent"**), akkor a Azure ad B2C egy JSON-tokent (nem pedig bejelentkezést) ad vissza az alkalmazásnak a jóváhagyás folyamatának újraindításához. Lehetőség van a felhasználói folyamat testreszabására úgy, hogy egy kisebb vagy egy felnőtt hozzáférjen a kiskorú fiókjához úgy, hogy regisztrációs kódot küld a kiskorú e-mail-címére vagy az e-mail-címére a rekordban.

4. Az alkalmazás lehetőséget biztosít a kiskorú számára a beleegyezés visszavonására.

5. Ha a kiskorú vagy a felnőtt visszavonja a beleegyezését, a Microsoft Graph API-val a **consentProvidedForMinor** **megtagadására** lehet szükség. Azt is megteheti, hogy az alkalmazás törli azt a kiskorút, amelynek a beleegyezését visszavonták. Lehetőség van a felhasználói folyamat testreszabására is, hogy a hitelesített kiskorú (vagy a kiskorú fiókját használó szülő) visszavonja a beleegyezést. Azure AD B2C a **consentProvidedForMinor** a **megtagadott** módon rögzíti.

A **legalAgeGroupClassification**, a **ConsentProvidedForMinor** és a **beszerzésimennyiség** szolgáltatással kapcsolatos további információkért lásd: [felhasználói erőforrás típusa](/graph/api/resources/user). További információ az egyéni attribútumokról: [Egyéni attribútumok használata a felhasználók adatainak gyűjtéséhez](user-flow-custom-attributes.md). Ha a Microsoft Graph API használatával kezeli a kiterjesztett attribútumokat, az attribútum hosszú verzióját kell használnia, például *extension_18b70cf9bb834edd8f38521c2583cd86_dateOfBirth*: *2011-01-01T00:00:00Z*.

## <a name="gather-date-of-birth-and-countryregion-data"></a>Születési dátum és ország/régió adatok összegyűjtése

Az alkalmazások a regisztráció során felhasználhatják Azure AD B2C a születési idő (DOB) és az ország/régió információinak összegyűjtését az összes felhasználótól. Ha ez az információ még nem létezik, az alkalmazás a következő hitelesítési (bejelentkezési) útvonalon kérheti le a felhasználót a felhasználótól. A felhasználók nem tudnak továbblépni a DOB és az ország/régió adatainak megadása nélkül. Azure AD B2C az információ segítségével határozza meg, hogy az adott ország vagy régió szabályozási előírásai szerint az egyes személyek kisebbnek minősülnek-e.

A testreszabott felhasználói folyamatok összegyűjthetik a DOB és az ország/régió adatait, és Azure AD B2C jogcímek átalakításával határozzák meg a **beszerzésimennyiség** , és megtartják az eredményt (vagy megőrzik a dob és az ország/régió adatait közvetlenül) a címtárban.

A következő lépések azt mutatják be, hogy milyen logikát kell kiszámítani a **beszerzésimennyiség** a felhasználó születési dátumának kiszámításához:

1. Próbálja megkeresni az országot/régiót az ország/régió kódja alapján a listában. Ha az ország/régió nem található, térjen vissza az **alapértelmezett értékre**.

2. Ha a **MinorConsent** csomópont megtalálható az ország/régió elemben:

    a. Annak a dátumnak a kiszámítása, amely szerint a felhasználónak felnőttnek kell lennie. Ha például az aktuális dátum március 14, 2015, a **MinorConsent** pedig 18, a születési dátum nem lehet későbbi, mint a 2000. március 14.

    b. A születési dátum és a tényleges születési dátum összevetése. Ha a minimális születési dátum a felhasználó születési dátuma, akkor a számítás a **korcsoport szerinti számításnak minősülő** alhálózatot adja vissza.

3. Ha a **MinorNoConsentRequired** csomópont megtalálható az ország/régió elemben, ismételje meg a 2a és a 2B lépést a **MinorNoConsentRequired** értékének használatával. A 2B kimenete **MinorNoConsentRequired** ad vissza, ha a minimális születési dátum a felhasználó születési dátuma.

4. Ha egyik számítás sem igaz értéket ad vissza, a számítás **felnőtt** értéket ad vissza.

Ha egy alkalmazás más módszerekkel megbízhatóan gyűjtött DOB vagy ország/régió adatait, az alkalmazás a Graph API használatával frissítheti a felhasználói rekordot ezekkel az adatokkal. Például:

- Ha egy felhasználó ismert felnőttnek, akkor frissítse a **beszerzésimennyiség** Directory-attribútumot **felnőtt** értékkel.
- Ha a felhasználó neve kisebb, akkor frissítse a **beszerzésimennyiség** attribútumot, és adja meg a **consentProvidedForMinor** értéket **a megfelelő** értékkel.

## <a name="minor-calculation-rules"></a>Másodlagos számítási szabályok

Az Age kapuzás két életkori értéket tartalmaz: az életkort, amelyet valaki már nem tekint kisebbnek, és azt, hogy a kiskorú gyermekeknek hogyan kell beleegyezniük. A következő táblázat felsorolja azokat a kor-szabályokat, amelyek egy kisebb és egy kisebb, a hozzájuk szükséges engedély definiálásához használatosak.

| Ország/régió | Ország/régió neve | Kisebb beleegyező életkor | Kisebb kor |
| -------------- | ------------------- | ----------------- | --------- |
| Alapértelmezett | Nincs | Nincs | 18 |
| AE | Egyesült Arab Emírségek | Nincs | 21 |
| AT | Ausztria | 14 | 18 |
| BE | Belgium | 14 | 18 |
| BG | Bulgária | 16 | 18 |
| BH | Bahrein | Nincs | 21 |
| CM | Kamerun | Nincs | 21 |
| CY | Ciprus | 16 | 18 |
| CZ | Cseh Köztársaság | 16 | 18 |
| DE | Németország | 16 | 18 |
| DK | Dánia | 16 | 18 |
| EE | Észtország | 16 | 18 |
| EG | Egyiptom | Nincs | 21 |
| ES | Spanyolország | 13 | 18 |
| JK | Franciaország | 16 | 18 |
| GB | Egyesült Királyság | 13 | 18 |
| GR | Görögország | 16 | 18 |
| HR | Horvátország | 16 | 18 |
| HU | Magyarország | 16 | 18 |
| IE | Írország | 13 | 18 |
| IT | Olaszország | 16 | 18 |
| KR | Koreai Köztársaság | 14 | 18 |
| LT | Litvánia | 16 | 18 |
| LU | Luxemburg | 16 | 18 |
| LV | Lettország | 16 | 18 |
| MT | Málta | 16 | 18 |
| NA | Namíbia | Nincs | 21 |
| NL | Hollandia | 16 | 18 |
| PL | Lengyelország | 13 | 18 |
| PT | Portugália | 16 | 18 |
| RO | Románia | 16 | 18 |
| SE | Svédország | 13 | 18 |
| SG | Szingapúr | Nincs | 21 |
| SI | Szlovénia | 16 | 18 |
| SK | Szlovákia | 16 | 18 |
| TD | Csád | Nincs | 21 |
| TH | Thaiföld | Nincs | 20 |
| TW | Tajvan | Nincs | 20 |
| USA | Egyesült Államok | 13 | 18 |



## <a name="capture-terms-of-use-agreement"></a>Használati feltételek rögzítése

Az alkalmazás fejlesztésekor általában a felhasználók beleegyezését rögzíti a használati feltételekben az alkalmazásokon belül, a nem vagy csak a kisebb, a felhasználói címtárban való részvételt illetően. Azonban lehetséges, hogy egy Azure AD B2C felhasználói folyamat használatával gyűjti össze a felhasználók használati feltételeit, korlátozhatja a hozzáférést, ha az elfogadás nem engedélyezett, és a használati feltételek jövőbeli változásainak elfogadását a legutóbbi elfogadás és a használati feltételek legújabb verziójának dátuma alapján érvényesíti.

**A használati feltételek** tartalmazhatják az adatmegosztást harmadik felekkel is. A helyi szabályozástól és az üzleti szabályoktól függően a felhasználók elfogadják mindkét feltétel összevonását, vagy engedélyezheti, hogy a felhasználó elfogadjon egy feltételt, nem pedig a másikat is.

A következő lépések bemutatják, hogyan kezelheti a használati feltételeket:

1. Rögzítse a használati feltételek elfogadását és az elfogadás dátumát a Graph API és a kiterjesztett attribútumok használatával. Ezt a beépített és egyéni felhasználói folyamatok is megteheti. Javasoljuk, hogy hozza létre és használja a **extension_termsOfUseConsentDateTime** és **extension_termsOfUseConsentVersion** attribútumokat.

2. Hozzon létre egy kötelező jelölőnégyzetet "a használati feltételek elfogadása" címkével, és jegyezze fel az eredményt a regisztráció során. Ezt a beépített és egyéni felhasználói folyamatok is megteheti.

3. Azure AD B2C a használati feltételeket és a felhasználó elfogadását tárolja. A Graph API segítségével bármely felhasználó állapotát lekérdezheti a válasz rögzítéséhez használt Extension attribútum (például olvasás **termsOfUseTestUpdateDateTime**) olvasásával. Ezt a beépített és egyéni felhasználói folyamatok is megteheti.

4. A frissített használati feltételek elfogadásának megkövetelése az elfogadás dátumát a használati feltételek legújabb verziójának dátumával összehasonlítva. A dátumokat csak egyéni felhasználói folyamat használatával lehet összehasonlítani. Használja a kiterjesztett attribútumot **extension_termsOfUseConsentDateTime**, és hasonlítsa össze az értéket a **termsOfUseTextUpdateDateTime** jogcímével. Ha az elfogadás régi, egy önérvényesített képernyő megjelenítésével kényszerítheti az új elfogadás elfogadását. Ellenkező esetben tiltsa le a hozzáférést a szabályzat logikája segítségével.

5. A frissített használati feltételek elfogadásának megkövetelése az elfogadás verziószámának összevetésével a legújabb elfogadott verziószámra. A verziószámokat csak egyéni felhasználói folyamat használatával hasonlíthatja össze. Használja a kiterjesztett attribútumot **extension_termsOfUseConsentDateTime**, és hasonlítsa össze az értéket **extension_termsOfUseConsentVersion** jogcímével. Ha az elfogadás régi, egy önérvényesített képernyő megjelenítésével kényszerítheti az új elfogadás elfogadását. Ellenkező esetben tiltsa le a hozzáférést a szabályzat logikája segítségével.

A használati feltételek elfogadását a következő esetekben rögzítheti:

- Új felhasználó regisztrálása. Megjelenik a használati feltételek, és a rendszer az elfogadási eredményt tárolja.
- A felhasználó bejelentkezik, aki korábban elfogadta a legújabb vagy az aktív használati feltételeket. A használati feltételek nem jelennek meg.
- A felhasználó bejelentkezik, aki még nem fogadta el a legújabb vagy az aktív használati feltételeket. Megjelenik a használati feltételek, és a rendszer az elfogadási eredményt tárolja.
- A felhasználó bejelentkezik, aki már elfogadta a használati feltételek egy régebbi verzióját, amely most már frissítve lett a legújabb verzióra. Megjelenik a használati feltételek, és a rendszer az elfogadási eredményt tárolja.

Az alábbi képen az ajánlott felhasználói folyamat látható:

![Az ajánlott elfogadási felhasználói folyamatot bemutató folyamatábra-diagram](./media/manage-user-access/user-flow.png)

A következő példa egy, a jogcímek használati feltételeinek megadására vonatkozó, dátumon alapuló használati feltételeket mutat be. Ha a `extension_termsOfUseConsentDateTime` jogcím régebbi, mint a `2025-01-15T00:00:00` , a `termsOfUseConsentRequired` logikai jogcím ellenőrzésével és az önérvényesített képernyő megjelenítésével kényszerítheti az új elfogadást. 

```xml
<ClaimsTransformations>
  <ClaimsTransformation Id="GetNewUserAgreeToTermsOfUseConsentDateTime" TransformationMethod="GetCurrentDateTime">
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="extension_termsOfUseConsentDateTime" TransformationClaimType="currentDateTime" />
    </OutputClaims>
  </ClaimsTransformation>
  <ClaimsTransformation Id="IsTermsOfUseConsentRequired" TransformationMethod="IsTermsOfUseConsentRequired">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="extension_termsOfUseConsentDateTime" TransformationClaimType="termsOfUseConsentDateTime" />
    </InputClaims>
    <InputParameters>
      <InputParameter Id="termsOfUseTextUpdateDateTime" DataType="dateTime" Value="2025-01-15T00:00:00" />
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="termsOfUseConsentRequired" TransformationClaimType="result" />
    </OutputClaims>
  </ClaimsTransformation>
</ClaimsTransformations>
```

A következő példa a használati feltételekre vonatkozó, a jogcímek megadására vonatkozó feltételeket ismertet. Ha a `extension_termsOfUseConsentVersion` jogcím nem egyenlő, a `V1` `termsOfUseConsentRequired` logikai jogcím ellenőrzésével és az önérvényesített képernyő megjelenítésével kényszerítheti az új elfogadást.

```xml
<ClaimsTransformations>
  <ClaimsTransformation Id="GetEmptyTermsOfUseConsentVersionForNewUser" TransformationMethod="CreateStringClaim">
    <InputParameters>
      <InputParameter Id="value" DataType="string" Value=""/>
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="extension_termsOfUseConsentVersion" TransformationClaimType="createdClaim" />
    </OutputClaims>
  </ClaimsTransformation>
  <ClaimsTransformation Id="GetNewUserAgreeToTermsOfUseConsentVersion" TransformationMethod="CreateStringClaim">
    <InputParameters>
      <InputParameter Id="value" DataType="string" Value="V1"/>
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="extension_termsOfUseConsentVersion" TransformationClaimType="createdClaim" />
    </OutputClaims>
  </ClaimsTransformation>
  <ClaimsTransformation Id="IsTermsOfUseConsentRequiredForVersion" TransformationMethod="CompareClaimToValue">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="extension_termsOfUseConsentVersion" TransformationClaimType="inputClaim1" />
    </InputClaims>
    <InputParameters>
      <InputParameter Id="compareTo" DataType="string" Value="V1" />
      <InputParameter Id="operator" DataType="string" Value="not equal" />
      <InputParameter Id="ignoreCase" DataType="string" Value="true" />
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="termsOfUseConsentRequired" TransformationClaimType="outputClaim" />
    </OutputClaims>
  </ClaimsTransformation>
</ClaimsTransformations>
```

## <a name="next-steps"></a>Következő lépések

- [Az életkor kapuzás engedélyezése a Azure ad B2Cban](age-gating.md).
- A felhasználói információk törlésével és exportálásával kapcsolatos információkért lásd: [felhasználói adatkezelés](manage-user-data.md).
- A használati feltételeket használó egyéni szabályzatok esetében tekintse meg [a B2C-IEF egyéni szabályzatot, és jelentkezzen be a "használati feltételek" üzenettel](https://github.com/azure-ad-b2c/samples/tree/master/policies/sign-in-sign-up-versioned-tou).