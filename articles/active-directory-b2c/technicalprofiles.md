---
title: Technikai profilok
titleSuffix: Azure AD B2C
description: A Azure Active Directory B2C egyéni házirendjének TechnicalProfiles elemének megadásához.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/04/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: bcff1ffd574db910c3206d82e4da0e9428db788f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102631795"
---
# <a name="technical-profiles"></a>Technikai profilok

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

A *technikai profil* olyan keretrendszert biztosít, amely beépített mechanizmussal rendelkezik a különböző típusú felekkel való kommunikációhoz. A technikai profilok használatával kommunikálhat a Azure Active Directory B2C (Azure AD B2C) Bérlővel egy felhasználó létrehozásához vagy felhasználói profil beolvasásához. A technikai profil önérvényesíthető úgy, hogy lehetővé tegye a felhasználóval való interakciót. A technikai profil például összegyűjtheti a felhasználó hitelesítő adatait a bejelentkezéshez, majd megjelenítheti a regisztrációs lapot vagy a jelszó-visszaállítási lapot.

## <a name="types-of-technical-profiles"></a>Technikai profilok típusai

A technikai profil a következő típusú forgatókönyveket teszi lehetővé:

- [Application Insights](analytics-with-application-insights.md): az esemény-adatokat [Application Insightsba](../azure-monitor/app/app-insights-overview.md)küldi.
- [Azure ad](active-directory-technical-profile.md): támogatást nyújt a Azure ad B2C felhasználói felügyelethez.
- [Azure ad többtényezős hitelesítés](multi-factor-auth-technical-profile.md): támogatást nyújt a telefonszámok ellenőrzéséhez az Azure ad többtényezős hitelesítés használatával.
- [Jogcím-átalakítás](claims-transformation-technical-profile.md): meghívja a kimeneti jogcímek átalakítását a jogcímek értékeinek módosításához, a jogcímek érvényesítéséhez, illetve a kimeneti jogcímek alapértelmezett értékének megadásához.
- [Azonosító jogkivonat-emlékeztető](id-token-hint.md): ellenőrzi a `id_token_hint` JWT jogkivonat-aláírást, a kiállító nevét és a jogkivonat célközönségét, és kibontja a jogcímet a bejövő jogkivonatból.
- [JWT jogkivonat kiállítója](jwt-issuer-technical-profile.md): kibocsátja a függő entitás alkalmazásnak visszaadott JWT-tokent.
- [OAuth1](oauth1-technical-profile.md): összevonás bármely OAuth 1,0 protokoll-identitás-szolgáltatóval.
- [OAuth2](oauth2-technical-profile.md): összevonás bármely OAuth 2,0 protokoll-identitás-szolgáltatóval.
- [Egyszeri jelszó](one-time-password-technical-profile.md): az egyszeri jelszó generálásának és ellenőrzésének felügyeletét teszi lehetővé.
- [OpenID Connect](openid-connect-technical-profile.md): összevonás bármely OpenID Connect protokoll identitás-szolgáltatóval.
- [Telefonos faktor](phone-factor-technical-profile.md): támogatja a telefonszámok regisztrálását és ellenőrzését.
- [Rest-szolgáltató](restful-technical-profile.md): olyan REST API szolgáltatásokat hív meg, mint például a felhasználói bevitel ellenőrzése, a felhasználói adatok gazdagítása vagy az üzletági alkalmazásokkal való integráció.
- [SAML-identitás szolgáltatója](identity-provider-generic-saml.md): összevonás bármely SAML protokoll identitás-szolgáltatóval.
- [SAML-jogkivonat kiállítója](saml-service-provider.md): a függő entitás alkalmazásának visszaadott SAML-tokent bocsát ki.
- [Önérvényesített](self-asserted-technical-profile.md): a felhasználóval való interakció. Például gyűjti a felhasználó hitelesítő adatait a bejelentkezéshez, a regisztrációs oldal megjelenítéséhez vagy a jelszó alaphelyzetbe állításához.
- [Munkamenet-kezelés](custom-policy-reference-sso.md): a különböző típusú munkameneteket kezeli.

## <a name="technical-profile-flow"></a>Technikai profil folyamata

A műszaki profilok minden típusa ugyanazzal a fogalommal rendelkezik. Először a bemeneti jogcímek beolvasásával és a jogcím-átalakítások futtatásával kezdődnek. Ezután kommunikálnak a konfigurált féllel, például egy identitás-szolgáltatóval, REST APItal vagy az Azure AD Directory-szolgáltatásokkal. A folyamat befejezése után a technikai profil visszaadja a kimeneti jogcímeket, és a kimeneti jogcímek átalakítását is futtathatja. A következő ábra a technikai profilban hivatkozott átalakítások és leképezések feldolgozását mutatja be. A jogcímek átalakításának végrehajtása után a rendszer azonnal tárolja a kimeneti jogcímeket a jogcímek táskájában, függetlenül attól, hogy a technikai profil milyen fél által kommunikál.

![A műszaki profil folyamatát ábrázoló diagram.](./media/technical-profiles/technical-profile-flow.png)

1. **Egyszeri bejelentkezéses (SSO) munkamenet-kezelés**: a technikai profil munkamenet-állapotát az egyszeri bejelentkezéses [munkamenet-kezelés](custom-policy-reference-sso.md)használatával állítja vissza.
1. **Bemeneti jogcímek átalakítása**: a technikai profil elindítása előtt Azure ad B2C futtatja a bemeneti [jogcímek átalakítását](claimstransformations.md).
1. **Bemeneti jogcímek**: a jogcímeket a technikai profilhoz használt jogcímek táskájában kell kiválasztani.
1. **Technikai profil végrehajtása**: a technikai profil a jogcímeket a konfigurált fél számára cseréli. Például:
    - A bejelentkezés befejezéséhez átirányítja a felhasználót az identitás-szolgáltatóhoz. A sikeres bejelentkezés után a felhasználó visszatér, és a technikai profil végrehajtása folytatódik.
    - Meghívja a REST API a paraméterek Szabályzattípushoz való elküldésekor és az adatok OutputClaims való lekérése közben.
    - Létrehozza vagy frissíti a felhasználói fiókot.
    - A többtényezős hitelesítés SMS-üzenetének küldése és ellenőrzése.
1. **Ellenőrzési technikai profilok**: az [önérvényesített technikai profil](self-asserted-technical-profile.md) érvényesítő [technikai](validation-technical-profile.md) profilokat hívhat meg, hogy érvényesítse a felhasználó által megadható adatforrásokat.
1. **Kimeneti jogcímek**: a jogcímeket a rendszer visszaadja a jogcímek táskájának. Ezeket a jogcímeket használhatja a következő előkészítési lépésekben vagy a kimeneti jogcímek átalakításában.
1. **Kimeneti jogcímek átalakításai**: a technikai profil befejeződése után Azure ad B2C a kimeneti [jogcímek átalakítását](claimstransformations.md)futtatja.
1. **SSO-munkamenetek kezelése**: az [egyszeri bejelentkezéses munkamenet-kezelés](custom-policy-reference-sso.md)használatával megőrzi a technikai profilhoz tartozó adataikat a munkamenetben.

A **TechnicalProfiles** elem a jogcím-szolgáltató által támogatott technikai profilok készletét tartalmazza. Minden jogcím-szolgáltatónak rendelkeznie kell legalább egy technikai profillal. A technikai profil határozza meg a végpontokat és a jogcím-szolgáltatóval való kommunikációhoz szükséges protokollokat. A jogcím-szolgáltató több technikai profillal is rendelkezhet.

```xml
<ClaimsProvider>
  <DisplayName>Display name</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Technical profile identifier">
      <DisplayName>Display name of technical profile</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        ...
      </Metadata>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

A **kivonatjogcím** elem a következő attribútumot tartalmazza:

| Attribútum | Kötelező | Leírás |
|---------|---------|---------|
| Id | Yes | A technikai profil egyedi azonosítója. A technikai profil hivatkozhat erre az azonosítóra a házirend fájljának más elemeiről. Ilyenek például a **OrchestrationSteps** és a **ValidationTechnicalProfile**. |

A **kivonatjogcím** elem a következő elemeket tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| Tartomány | 0:1 | A technikai profil tartományneve. Ha például a technikai profil megadja a Facebook-identitás szolgáltatóját, a tartománynév Facebook.com. |
| DisplayName | 1:1 | A technikai profil megjelenítendő neve. |
| Leírás | 0:1 | A technikai profil leírása. |
| Protokoll | 1:1 | A másik féllel folytatott kommunikációhoz használt protokoll. |
| Metaadatok | 0:1 | Kulcsok és értékek halmaza, amelyek a technikai profil viselkedését vezérlik. |
| InputTokenFormat | 0:1 | A bemeneti jogkivonat formátuma. A lehetséges értékek:,, `JSON` `JWT` `SAML11` vagy `SAML2` . Az `JWT` érték az IETF-specifikációnak megfelelő JSON web tokent jelöl. Az `SAML11` érték egy SAML 1,1 biztonsági jogkivonatot képvisel az Oasis-specifikáció alapján. Az `SAML2` érték egy SAML 2,0 biztonsági jogkivonatot képvisel az Oasis-specifikáció alapján. |
| OutputTokenFormat | 0:1 | A kimeneti token formátuma. A lehetséges értékek:,, `JSON` `JWT` `SAML11` vagy `SAML2` . |
| CryptographicKeys | 0:1 | A technikai profilban használt titkosítási kulcsok listája. |
| InputClaimsTransformations | 0:1 | A jogcímek átalakítására korábban meghatározott hivatkozások listája, amelyeket végre kell hajtani ahhoz, hogy a jogcímeket a jogcím-szolgáltató vagy a függő entitás megkapja. |
| Szabályzattípushoz | 0:1 | Azoknak a korábban meghatározott hivatkozásoknak a listája, amelyeket a technikai profilban bemenetként vesznek fel. |
| PersistedClaims | 0:1 | Azoknak a korábban meghatározott hivatkozásoknak a listája, amelyek a technikai profil által megőrzött jogcím-típusokra mutatnak. |
| DisplayClaims | 0:1 | A korábban meghatározott, az önellenőrzött [technikai profil](self-asserted-technical-profile.md)által megjelenített jogcím-típusokra mutató hivatkozásokat tartalmazó lista. A DisplayClaims szolgáltatás jelenleg előzetes verzióban érhető el. |
| OutputClaims | 0:1 | A korábban meghatározott, a technikai profilban kimenetként kapott jogcím-típusokra mutató hivatkozások listája. |
| OutputClaimsTransformations | 0:1 | A korábban meghatározott, a jogcím-átalakításokra mutató hivatkozásokat tartalmazó lista, amelyet a jogcím-szolgáltatótól érkező jogcímek fogadása után kell végrehajtani. |
| ValidationTechnicalProfiles | 0: n | Azon egyéb műszaki profilokra mutató hivatkozások listája, amelyeket a technikai profil az ellenőrzési célokra használ. További információ: [Validation Technical Profile](validation-technical-profile.md).|
| SubjectNamingInfo | 0:1 | A tulajdonos nevének olyan jogkivonatokban való előállítását szabályozza, amelyekben a tulajdonos neve külön van megadva a jogcímek között. Ilyenek például a OAuth vagy az SAML. |
| IncludeInSso | 0:1 | Azt határozza meg, hogy ennek a technikai profilnak a használata esetén kell-e SSO-viselkedést alkalmazni a munkamenetre, vagy inkább explicit interakcióra Ez az elem csak az érvényesítési technikai profilban használt SelfAsserted-profilokban érvényes. A lehetséges értékek a következők: `true` (alapértelmezett) vagy `false` . |
| IncludeClaimsFromTechnicalProfile | 0:1 | Egy olyan műszaki profil azonosítója, amelyből hozzá kívánja adni az összes bemeneti és kimeneti jogcímet ehhez a technikai profilhoz. A hivatkozott technikai profilt ugyanabban a házirend-fájlban kell definiálni. |
| IncludeTechnicalProfile |0:1 | Egy műszaki profil azonosítója, amelyből a technikai profilba felvenni kívánt összes adat bekerül. |
| UseTechnicalProfileForSessionManagement | 0:1 | A munkamenet-kezeléshez használt másik technikai profil. |
|EnabledForUserJourneys| 0:1 |Azt szabályozza, hogy a technikai profilt egy felhasználói úton hajtják végre. |

## <a name="protocol"></a>Protokoll

A **protokoll** elem meghatározza a másik féllel folytatott kommunikációhoz használandó protokollt. A **protokoll** elem a következő attribútumokat tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Név | Yes | A technikai profil részeként használt Azure AD B2C által támogatott érvényes protokoll neve. A lehetséges értékek:,,,, `OAuth1` `OAuth2` `SAML2` `OpenIdConnect` `Proprietary` vagy `None` . |
| Kezelő | No | Ha a protokoll neve értékre van állítva, a a `Proprietary` protokoll kezelőjének meghatározásához a Azure ad B2C által használt szerelvény nevét adja meg. |

## <a name="metadata"></a>Metaadatok

A **metaadat** elem egy adott protokollhoz tartozó konfigurációs beállításokat tartalmazza. A támogatott metaadatok listáját a [technikai profil](#types-of-technical-profiles) megfelelő specifikációja tartalmazza. A **metaadat** -elemek a következő elemet tartalmazzák:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| Elem | 0: n | A technikai profilhoz kapcsolódó metaadatok. Az egyes technikai profilok különböző metaadat-elemekből állnak. További információ: technikai profilok típusai szakasz. |

### <a name="item"></a>Elem

A **metadata** elem **Item** eleme a következő attribútumot tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Kulcs | Yes | A metaadat-kulcs. A metaadat-elemek listájának megtekintéséhez tekintse meg az egyes [műszaki profilok típusait](#types-of-technical-profiles) . |

Az alábbi példa a [OAuth2-technikai profilhoz](oauth2-technical-profile.md#metadata)kapcsolódó metaadatok használatát szemlélteti.

```xml
<TechnicalProfile Id="Facebook-OAUTH">
  ...
  <Metadata>
    <Item Key="ProviderName">facebook</Item>
    <Item Key="authorization_endpoint">https://www.facebook.com/dialog/oauth</Item>
    <Item Key="AccessTokenEndpoint">https://graph.facebook.com/oauth/access_token</Item>
    <Item Key="HttpBinding">GET</Item>
    <Item Key="UsePolicyInRedirectUri">0</Item>
    ...
  </Metadata>
  ...
</TechnicalProfile>
```

Az alábbi példa a [REST API technikai profilhoz](restful-technical-profile.md#metadata)tartozó metaadatok használatát szemlélteti.

```xml
<TechnicalProfile Id="REST-Validate-Email">
  ...
  <Metadata>
    <Item Key="ServiceUrl">https://api.sendgrid.com/v3/mail/send</Item>
    <Item Key="AuthenticationType">Bearer</Item>
    <Item Key="SendClaimsIn">Body</Item>
    ...
  </Metadata>
  ...
</TechnicalProfile>
```

## <a name="cryptographic-keys"></a>Titkosítási kulcsok

Ha megbízhatósági kapcsolatot szeretne létesíteni a szolgáltatással együtt, Azure AD B2C a titkokat és tanúsítványokat a [szabályzat kulcsainak](policy-keys-overview.md)formájában tárolja. A technikai profil végrehajtása során Azure AD B2C lekéri a titkosítási kulcsokat Azure AD B2C szabályzat kulcsaiból. Ezután a Azure AD B2C a kulcsokkal megbízhatósági kapcsolatot létesít, illetve titkosíthatja vagy aláírhatja a jogkivonatot. Ezek a megbízhatósági kapcsolatok a következőkből állnak:

- Összevonás [OAuth1](oauth1-technical-profile.md#cryptographic-keys), [OAuth2](oauth2-technical-profile.md#cryptographic-keys)és [SAML](identity-provider-generic-saml.md) -identitással.
- A [REST API-szolgáltatásokkal](secure-rest-api.md)létesített kapcsolatok biztonságossá tétele.
- A [JWT](jwt-issuer-technical-profile.md#cryptographic-keys) és az [SAML](saml-service-provider.md) -tokenek aláírása és titkosítása.

A **CryptographicKeys** elem a következő elemet tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| Kulcs | 1: n | A technikai profilban használt titkosítási kulcs. |

### <a name="key"></a>Kulcs

A **Key** elem a következő attribútumot tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Id | No | Egy adott kulcspár egyedi azonosítója, amelyet a rendszer a házirend fájljának más elemeiből hivatkozik. |
| StorageReferenceId | Yes | A termékazonosító más elemeitől hivatkozott tárolási kulcstároló-tárolók egyike. |

## <a name="input-claims-transformations"></a>Bemeneti jogcím-átalakítások

A **InputClaimsTransformations** elem olyan bemeneti jogcím-átalakítási elemek gyűjteményét is tartalmazhatja, amelyek a bemeneti jogcímek módosításához vagy újak létrehozásához használatosak.

A jogcím-átalakítási gyűjtemény korábbi jogcím-átalakításának kimeneti jogcímei a következő bemeneti jogcímek átalakításának bemeneti jogcímei lehetnek. Ily módon a jogcím-átalakítások sorozatából állhat, amelyek egymástól függenek.

A **InputClaimsTransformations** elem a következő elemet tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| InputClaimsTransformation | 1: n | Egy jogcím-átalakítás azonosítója, amelyet el kell végezni, mielőtt a rendszer a jogcímeket elküldi a jogcím-szolgáltatónak vagy a függő entitásnak. A jogcímek átalakításával módosíthatók a meglévő ClaimsSchema-jogcímek, vagy újak is létrehozhatók. |

### <a name="inputclaimstransformation"></a>InputClaimsTransformation

A **InputClaimsTransformation** elem a következő attribútumot tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| ReferenceId | Yes | Egy jogcímek átalakításának azonosítója, amely már definiálva van a házirend fájljában vagy a szülő házirend fájljában. |

A következő műszaki profilok a **CreateOtherMailsFromEmail** jogcím-átalakításra hivatkoznak. A jogcím-átalakítási szolgáltatás hozzáadja a `email` jogcím értékét a `otherMails` gyűjteményhez, mielőtt megőrzi az adattárat.

```xml
<TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
  ...
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="CreateOtherMailsFromEmail" />
  </InputClaimsTransformations>
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="alternativeSecurityId" />
    <PersistedClaim ClaimTypeReferenceId="userPrincipalName" />
    <PersistedClaim ClaimTypeReferenceId="mailNickName" DefaultValue="unknown" />
    <PersistedClaim ClaimTypeReferenceId="displayName" DefaultValue="unknown" />
    <PersistedClaim ClaimTypeReferenceId="otherMails" />
    <PersistedClaim ClaimTypeReferenceId="givenName" />
    <PersistedClaim ClaimTypeReferenceId="surname" />
  </PersistedClaims>
  ...
</TechnicalProfile>
```

## <a name="input-claims"></a>Bemeneti jogcímek

A **szabályzattípushoz** elem felveszi a jogcímeket a technikai profilhoz használt jogcímek táskából. Egy [önérvényesített technikai profil](self-asserted-technical-profile.md) például a bemeneti jogcímek használatával előre feltölti a felhasználó által biztosított kimeneti jogcímeket. A REST API műszaki profilok a bemeneti jogcímek használatával küldenek bemeneti paramétereket a REST API végpontnak. Az Azure AD egy bemeneti jogcímet használ egyedi azonosítóként egy fiók olvasásához, frissítéséhez vagy törléséhez.

A **szabályzattípushoz** elem a következő elemet tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| InputClaim | 1: n | Egy várt bemeneti jogcím típusa. |

### <a name="inputclaim"></a>InputClaim

A **InputClaim** elem a következő attribútumokat tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Yes | Jogcím típusának azonosítója A jogcím már definiálva van a házirend fájl vagy a szülő házirend fájl jogcím-séma szakaszában. |
| DefaultValue | No | A jogcím létrehozásához használt alapértelmezett érték, ha a ClaimTypeReferenceId által jelzett jogcím nem létezik, így az eredményül kapott jogcím a technikai profil InputClaim elemeként is használható. |
| PartnerClaimType | No | Azon külső partner jogcím-típusának azonosítója, amelyhez a megadott házirend-jogcím típusa le van képezve. Ha nincs megadva a PartnerClaimType attribútum, a rendszer a megadott házirend-jogcím típusát az azonos nevű partner jogcím-típushoz rendeli. Akkor használja ezt a tulajdonságot, ha a jogcím típusának neve eltér a másik féltől. Ilyen eset például, ha az első jogcím neve *givenName*, míg a partner egy *first_name* nevű jogcímet használ. |

## <a name="display-claims"></a>Jogcímek megjelenítése

A **DisplayClaims** elem tartalmazza a képernyőn megjeleníteni kívánt jogcímek listáját, hogy adatokat gyűjtsön a felhasználótól. A megjelenítési jogcímek gyűjteménybe belefoglalhatja a [jogcím típusára](claimsschema.md) vagy a létrehozott [megjelenítési vezérlőelemre](display-controls.md) mutató hivatkozást is.

- A jogcím típusa a képernyőn megjelenő jogcímre mutató hivatkozás.
  - Ha szeretné kényszeríteni a felhasználót, hogy adjon meg egy értéket egy adott jogcím számára, állítsa a **DisplayClaim** elem **kötelező** attribútumát a következőre: `true` .
  - A megjelenítési jogcímek értékének előre való feltöltéséhez használja a korábban leírt bemeneti jogcímeket. Előfordulhat, hogy az elem tartalmaz egy alapértelmezett értéket is.
  - A **DisplayClaims** -gyűjtemény **claimType** elemének a **UserInputType** elemet kell beállítania a Azure ad B2C által támogatott összes felhasználói bemeneti típusra. Ilyenek például a következők: `TextBox` vagy `DropdownSingleSelect` .
- A megjelenítési vezérlő egy olyan felhasználói felületi elem, amely speciális funkciókkal rendelkezik, és a Azure AD B2C háttérrendszer-szolgáltatással kommunikál. Lehetővé teszi a felhasználó számára, hogy műveleteket hajtson végre az oldalon, amely a háttérbeli érvényesítési technikai profilt hívja meg. Ilyen például az e-mail-cím, telefonszám vagy ügyfél-hűségi szám ellenőrzése.

A **DisplayClaims** elemeinek sorrendje határozza meg, hogy a Azure ad B2C milyen sorrendben jelenítse meg a jogcímeket a képernyőn.

A **DisplayClaims** elem a következő elemet tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| DisplayClaim | 1: n | Egy várt bemeneti jogcím típusa. |

### <a name="displayclaim"></a>DisplayClaim

A **DisplayClaim** elem a következő attribútumokat tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | No | Egy jogcím-típus azonosítója már definiálva van a ClaimsSchema szakaszban a házirend fájl vagy a szülő házirend fájljában. |
| DisplayControlReferenceId | No | A ClaimsSchema szakaszban már definiált [megjelenítési vezérlő](display-controls.md) azonosítója a házirend fájljában vagy a szülő házirend fájljában. |
| Kötelező | No | Azt jelzi, hogy szükséges-e a megjelenítési jogcím. |

Az alábbi példa azt szemlélteti, hogyan használhatók a megjelenítési jogcímek és a megjelenítési vezérlők egy önérvényesített technikai profilban.

![Képernyőkép, amely egy önjelölt technikai profilt mutat be a megjelenítési jogcímekkel.](./media/technical-profiles/display-claims.png)

A következő technikai profilban:

- Az első megjelenítési jogcím egy hivatkozást tartalmaz a `emailVerificationControl` megjelenítési vezérlőelemre, amely összegyűjti és ellenőrzi az e-mail-címet.
- Az ötödik megjelenítési jogcím hivatkozik a `phoneVerificationControl` megjelenítési vezérlőelemre, amely egy telefonszámot gyűjt és ellenőriz.
- A többi megjelenítési jogcímek a felhasználótól begyűjtött ClaimType elemek.

```xml
<TechnicalProfile Id="Id">
  <DisplayClaims>
    <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
    <DisplayClaim DisplayControlReferenceId="phoneVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="newPassword" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
  </DisplayClaims>
</TechnicalProfile>
```

## <a name="persisted-claims"></a>Megőrzött jogcímek

A **PersistedClaims** elem tartalmazza az összes olyan értéket, amelyet egy Azure ad-beli [műszaki profilnak](active-directory-technical-profile.md) meg kell őriznie a szabályzat és az Azure ad-attribútum neve [ClaimsSchema](claimsschema.md) szakaszában már definiált jogcím-típus között.

A jogcím neve az [Azure ad attribútum](user-profile-attributes.md) neve, kivéve, ha meg van adva a **PartnerClaimType** attribútum, amely tartalmazza az Azure ad-attribútum nevét.

A **PersistedClaims** elem a következő elemet tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| PersistedClaim | 1: n | A megmaradó jogcím típusa. |

### <a name="persistedclaim"></a>PersistedClaim

A **PersistedClaim** elem a következő attribútumokat tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Yes | Egy jogcím-típus azonosítója már definiálva van a ClaimsSchema szakaszban a házirend fájl vagy a szülő házirend fájljában. |
| DefaultValue | No | A jogcím létrehozásához használandó alapértelmezett érték, ha a jogcím nem létezik. |
| PartnerClaimType | No | Azon külső partner jogcím-típusának azonosítója, amelyhez a megadott házirend-jogcím típusa le van képezve. Ha nincs megadva a PartnerClaimType attribútum, a rendszer a megadott házirend-jogcím típusát az azonos nevű partner jogcím-típushoz rendeli. Akkor használja ezt a tulajdonságot, ha a jogcím típusának neve eltér a másik féltől. Ilyen eset például, ha az első jogcím neve *givenName*, míg a partner egy *first_name* nevű jogcímet használ. |

A következő példában a **HRE-UserWriteUsingLogonEmail** technikai profil vagy az [alapszintű csomag](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccounts), amely új helyi fiókot hoz létre, megőrzi a következő jogcímeket:

```xml
<PersistedClaims>
  <PersistedClaim ClaimTypeReferenceId="email" PartnerClaimType="signInNames.emailAddress" />
  <PersistedClaim ClaimTypeReferenceId="newPassword" PartnerClaimType="password"/>
  <PersistedClaim ClaimTypeReferenceId="displayName" DefaultValue="unknown" />
  <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration" />
  <PersistedClaim ClaimTypeReferenceId="givenName" />
  <PersistedClaim ClaimTypeReferenceId="surname" />
</PersistedClaims>
```

## <a name="output-claims"></a>Kimeneti jogcímek

A **OutputClaims** elem a technikai profil befejezése után visszaadott jogcímek gyűjteménye. Ezeket a jogcímeket használhatja a következő előkészítési lépésekben vagy a kimeneti jogcímek átalakításában. A **OutputClaims** elem a következő elemet tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| OutputClaim | 1: n | A várt kimeneti jogcím típusa. |

### <a name="outputclaim"></a>OutputClaim

A **OutputClaim** elem a következő attribútumokat tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Yes | Egy jogcím-típus azonosítója már definiálva van a ClaimsSchema szakaszban a házirend fájl vagy a szülő házirend fájljában. |
| DefaultValue | No | A jogcím létrehozásához használandó alapértelmezett érték, ha a jogcím nem létezik. |
|AlwaysUseDefaultValue |No |Az alapértelmezett érték használatának kényszerítése. |
| PartnerClaimType | No | Azon külső partner jogcím-típusának azonosítója, amelyhez a megadott házirend-jogcím típusa le van képezve. Ha nincs megadva a partner jogcím Type attribútuma, a rendszer a megadott házirend-jogcím típusát az azonos nevű partner jogcím-típushoz rendeli. Akkor használja ezt a tulajdonságot, ha a jogcím típusának neve eltér a másik féltől. Ilyen eset például, ha az első jogcím neve *givenName*, míg a partner egy *first_name* nevű jogcímet használ. |

## <a name="output-claims-transformations"></a>Kimeneti jogcímek átalakítása

A **OutputClaimsTransformations** elem **OutputClaimsTransformation** -elemek gyűjteményét is tartalmazhatja. A kimeneti jogcímek átalakításával módosíthatók a kimeneti jogcímek, vagy újakat hozhatnak fel. A végrehajtás után a kimeneti jogcímek vissza lesznek helyezve a jogcím-táskába. Ezeket a jogcímeket használhatja a következő előkészítési lépésekben.

A jogcím-átalakítási gyűjtemény korábbi jogcím-átalakításának kimeneti jogcímei a következő bemeneti jogcímek átalakításának bemeneti jogcímei lehetnek. Ily módon a jogcím-átalakítások sorozatából állhat, amelyek egymástól függenek.

A **OutputClaimsTransformations** elem a következő elemet tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| OutputClaimsTransformation | 1: n | A jogcímek átalakításának a jogcím-szolgáltatóba vagy a függő entitásba való elküldése előtt végrehajtandó azonosítók. A jogcímek átalakításával módosíthatók a meglévő ClaimsSchema-jogcímek, vagy újak is létrehozhatók. |

### <a name="outputclaimstransformation"></a>OutputClaimsTransformation

A **OutputClaimsTransformation** elem a következő attribútumot tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| ReferenceId | Yes | Egy jogcímek átalakításának azonosítója, amely már definiálva van a házirend fájljában vagy a szülő házirend fájljában. |

A következő műszaki profil a AssertAccountEnabledIsTrue jogcím-átalakításra hivatkozik annak kiértékeléséhez, hogy a fiók engedélyezve van-e, vagy sem a `accountEnabled` jogcímnek a címtárból való elolvasása után

```xml
<TechnicalProfile Id="AAD-UserReadUsingEmailAddress">
  ...
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
    <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="accountEnabled" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
    <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
  </OutputClaims>
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertAccountEnabledIsTrue" />
  </OutputClaimsTransformations>
  ...
</TechnicalProfile>
```

## <a name="validation-technical-profiles"></a>Ellenőrzési technikai profilok

Az érvényesítési technikai profil a kimeneti jogcímek érvényesítésére szolgál egy [önérvényesített technikai profilban](self-asserted-technical-profile.md#validation-technical-profiles). Az érvényesítési műszaki profil bármely protokollból, például az [Azure ad](active-directory-technical-profile.md) -ből vagy egy [Rest APIból](restful-technical-profile.md)származó általános műszaki profil. Az érvényesítési technikai profil visszaadja a kimeneti jogcímeket, vagy visszaadja a hibakódot. A hibaüzenet jelenik meg a felhasználó számára a képernyőn, amely lehetővé teszi a felhasználó számára az újrapróbálkozást.

Az alábbi ábra azt szemlélteti, hogyan használja a Azure AD B2C egy érvényesítési technikai profilt a felhasználói hitelesítő adatok ellenőrzéséhez.

![Az érvényesítési technikai profil folyamatát ábrázoló diagram.](./media/technical-profiles/validation-technical-profile.png)

A **ValidationTechnicalProfiles** elem a következő elemet tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| ValidationTechnicalProfile | 1: n | A felhasználható műszaki profilok azonosítói a hivatkozó technikai profil kimeneti jogcímeinek némelyikét vagy mindegyikét ellenőrzik. A hivatkozott technikai profil összes bemeneti jogcímének szerepelnie kell a hivatkozó technikai profil kimeneti jogcímeiben. |

### <a name="validationtechnicalprofile"></a>ValidationTechnicalProfile

A **ValidationTechnicalProfile** elem a következő attribútumot tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| ReferenceId | Yes | A házirend-fájlban vagy a szülő-házirend fájlban már definiált technikai profil azonosítója. |

## <a name="subjectnaminginfo"></a>SubjectNamingInfo

A **SubjectNamingInfo** elem a jogkivonatokban használt tulajdonos nevét határozza meg egy [függő entitás házirendjében](relyingparty.md#subjectnaminginfo). A **SubjectNamingInfo** elem a következő attribútumot tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| ClaimType | Yes | Egy jogcím-típus azonosítója már definiálva van a ClaimsSchema szakaszban a házirend fájljában. |

## <a name="include-technical-profile"></a>Technikai profil belefoglalása

A műszaki profilok tartalmazhatnak egy másik technikai profilt a beállítások módosításához vagy új funkciók hozzáadásához. A **IncludeTechnicalProfile** elem arra a közös műszaki profilra hivatkozik, amelyből a technikai profil származik. A házirend-elemek redundancia és összetettsége csökkentése érdekében használja a befoglalást, ha több technikai profillal rendelkezik, amelyek osztoznak az alapvető elemeken. Használjon közös technikai profilt a közös konfigurációval együtt, valamint olyan konkrét feladattal kapcsolatos technikai profilokat, amelyek tartalmazzák a közös technikai profilt.

Tegyük fel, hogy van egy [REST API technikai profilja](restful-technical-profile.md) egyetlen végponttal, ahol különböző típusú jogcímeket kell elküldenie különböző forgatókönyvekhez. Hozzon létre egy közös technikai profilt a megosztott funkciókkal, például a REST API végpont URI-JÁT, a metaadatokat, a hitelesítési típust és a titkosítási kulcsokat. Hozzon létre konkrét feladattal kapcsolatos technikai profilokat, amelyek tartalmazzák a közös technikai profilt. Ezután adja hozzá a bemeneti és a kimeneti jogcímeket, vagy írja felül az adott technikai profilhoz tartozó REST API végponti URI-t.

A **IncludeTechnicalProfile** elem a következő attribútumot tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| ReferenceId | Yes | A házirend-fájlban vagy a szülő-házirend fájlban már definiált technikai profil azonosítója. |

Az alábbi példa a felvétel használatát szemlélteti:

- **REST-API-Common**: az alapszintű konfigurációval rendelkező közös műszaki profil.
- **Rest-ValidateProfile**: a **REST-API-Common** műszaki profilt tartalmazza, és megadja a bemeneti és a kimeneti jogcímeket.
- **Rest-UpdateProfile**: magában foglalja a **REST-API-Common** műszaki profilt, megadja a bemeneti jogcímeket, és felülírja a `ServiceUrl` metaadatokat.

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-API-Common">
      <DisplayName>Base REST API configuration</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity</Item>
        <Item Key="AuthenticationType">Basic</Item>
        <Item Key="SendClaimsIn">Body</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_B2cRestClientId" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_B2cRestClientSecret" />
      </CryptographicKeys>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>

    <TechnicalProfile Id="REST-ValidateProfile">
      <DisplayName>Validate the account and return promo code</DisplayName>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="objectId" />
        <InputClaim ClaimTypeReferenceId="email" />
        <InputClaim ClaimTypeReferenceId="userLanguage" PartnerClaimType="lang" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="promoCode" />
      </OutputClaims>
      <IncludeTechnicalProfile ReferenceId="REST-API-Common" />
    </TechnicalProfile>

    <TechnicalProfile Id="REST-UpdateProfile">
       <Metadata>
        <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/update</Item>
      </Metadata>
      <DisplayName>Update the user profile</DisplayName>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="objectId" />
        <InputClaim ClaimTypeReferenceId="email" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="REST-API-Common" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

### <a name="multilevel-inclusion"></a>Többszintű felvétel

A technikai profil egyetlen technikai profilt is tartalmazhat. A bevonási szintek száma nincs korlátozva. Például a **HRE-UserReadUsingAlternativeSecurityId-Error** technikai profil tartalmazza a **HRE-UserReadUsingAlternativeSecurityId**. Ez a technikai profil a `RaiseErrorIfClaimsPrincipalDoesNotExist` metaadatokat állítja be, `true` és hibát jelez, ha egy közösségi fiók nem létezik a címtárban. **HRE-UserReadUsingAlternativeSecurityId – a hiba** felülbírálja ezt a viselkedést, és letiltja a hibaüzenetet.

```xml
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId-NoError">
  <Metadata>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">false</Item>
  </Metadata>
  <IncludeTechnicalProfile ReferenceId="AAD-UserReadUsingAlternativeSecurityId" />
</TechnicalProfile>
```

**HRE – a UserReadUsingAlternativeSecurityId** tartalmazza a `AAD-Common` technikai profilt.

```xml
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="Operation">Read</Item>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
    <Item Key="UserMessageIfClaimsPrincipalDoesNotExist">User does not exist. Please sign up before you can sign in.</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityId" PartnerClaimType="alternativeSecurityId" Required="true" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
    <OutputClaim ClaimTypeReferenceId="givenName" />
    <OutputClaim ClaimTypeReferenceId="surname" />
  </OutputClaims>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

Mind a **HRE-UserReadUsingAlternativeSecurityId** , mind a **HRE-UserReadUsingAlternativeSecurityId** nem adja meg a szükséges **protokoll** elemet, mert az a **HRE-Common** Technical profilban van megadva.

```xml
<TechnicalProfile Id="AAD-Common">
  <DisplayName>Azure Active Directory</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
</TechnicalProfile>
```

## <a name="use-technical-profile-for-session-management"></a>A munkamenet-kezelés technikai profiljának használata

A **UseTechnicalProfileForSessionManagement** elem az [SSO-munkamenet technikai profiljára](custom-policy-reference-sso.md)hivatkozik. A **UseTechnicalProfileForSessionManagement** elem a következő attribútumot tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| ReferenceId | Yes | A házirend-fájlban vagy a szülő-házirend fájlban már definiált technikai profil azonosítója. |

## <a name="enabled-for-user-journeys"></a>Felhasználói utazások engedélyezése

A felhasználói utazás [ClaimsProviderSelections](userjourneys.md#claims-provider-selection) a jogcím-szolgáltató kiválasztási lehetőségeinek listáját és sorrendjét határozza meg. A **EnabledForUserJourneys** elemmel szűrheti, hogy melyik jogcím-szolgáltató érhető el a felhasználó számára. A **EnabledForUserJourneys** elem a következő értékek egyikét tartalmazza:

- **Mindig**: a technikai profilt hajtja végre.
- **Soha**: kihagyja a technikai profilt.
- **OnClaimsExistence**: csak akkor hajtható végre, ha a technikai profilban megadott jogcím létezik.
- **OnItemExistenceInStringCollectionClaim**: csak akkor hajtható végre, ha egy karakterlánc-gyűjteményi jogcímben szerepel egy tétel.
- **OnItemAbsenceInStringCollectionClaim**: csak akkor hajtható végre, ha egy objektum nem létezik karakterlánc-gyűjtési jogcímben.

A **OnClaimsExistence**, a **OnItemExistenceInStringCollectionClaim** vagy a **OnItemAbsenceInStringCollectionClaim** használatával a következő metaadatokat kell megadnia:

- **ClaimTypeOnWhichToEnable**: a jogcím azon típusát adja meg, amelyet ki kell értékelni.
- **ClaimValueOnWhichToEnable**: az összehasonlítandó értéket adja meg.

A következő technikai profilt csak akkor hajtja végre a rendszer, ha a **identityProviders** karakterlánc-gyűjtemény a következők értékét tartalmazza `facebook.com` :

```xml
<TechnicalProfile Id="UnLink-Facebook-OAUTH">
  <DisplayName>Unlink Facebook</DisplayName>
...
    <Metadata>
      <Item Key="ClaimTypeOnWhichToEnable">identityProviders</Item>
      <Item Key="ClaimValueOnWhichToEnable">facebook.com</Item>
    </Metadata>
...
  <EnabledForUserJourneys>OnItemExistenceInStringCollectionClaim</EnabledForUserJourneys>
</TechnicalProfile>
```
