---
title: Munkamenet-viselkedés konfigurálása – Azure Active Directory B2C | Microsoft Docs
description: Megtudhatja, hogyan konfigurálhatja a munkamenet viselkedését Azure Active Directory B2Cban.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/04/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 3a3cdb93ee4cbf4a2e15540b9daf78b6c231d393
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104579739"
---
# <a name="configure-session-behavior-in-azure-active-directory-b2c"></a>Munkamenet viselkedésének konfigurálása az Azure Active Directory B2C-ben

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Az egyszeri bejelentkezés (SSO) biztonsági és kényelmi funkciókat biztosít, amikor a felhasználók bejelentkeznek Azure Active Directory B2C (Azure AD B2C) alkalmazásokban. Ez a cikk a Azure AD B2Cben használt egyszeri bejelentkezési módszereket ismerteti, és segít kiválasztani a legmegfelelőbb egyszeri bejelentkezési módszert a szabályzat konfigurálása során.

Egyszeri bejelentkezés esetén a felhasználók egyetlen fiókkal jelentkeznek be, és több alkalmazáshoz is hozzáférhetnek. Az alkalmazás lehet web-, mobil-vagy egyoldalas alkalmazás, függetlenül a platformtól vagy a tartománynévtől.

Amikor a felhasználó először bejelentkezik egy alkalmazásba, Azure AD B2C megőrzi a cookie-alapú munkamenetet. További hitelesítési kérelmek esetén Azure AD B2C beolvassa és érvényesíti a cookie-alapú munkamenetet, és a felhasználó értesítése nélkül kiadja a hozzáférési jogkivonatot. Ha a cookie-alapú munkamenet lejár vagy érvénytelenné válik, a rendszer felszólítja a felhasználót, hogy jelentkezzen be újra.  

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="azure-ad-b2c-session-overview"></a>Azure AD B2C-munkamenet áttekintése

A Azure AD B2C való integráció három típusú SSO-munkamenetet foglal magában:

- **Azure ad B2C** – a Azure ad B2C által felügyelt munkamenet
- **Összevont identitás-szolgáltató** – az identitás-szolgáltató által kezelt munkamenet, például Facebook, Salesforce vagy Microsoft-fiók
- Web-, mobil-vagy egyoldalas alkalmazás által felügyelt **alkalmazás** -munkamenet

![SSO-munkamenet](media/session-behavior/sso-session-types.png)

### <a name="azure-ad-b2c-session"></a>Azure AD B2C-munkamenet 

Amikor egy felhasználó sikeresen hitelesít egy helyi vagy közösségi fiókkal, a Azure AD B2C egy cookie-alapú munkamenetet tárol a felhasználó böngészőjében. A cookie-t a Azure AD B2C bérlői tartomány neve alatt tárolja, például: `https://contoso.b2clogin.com` .

Ha egy felhasználó először összevont fiókkal jelentkezik be, majd a munkamenet ideje (élettartam vagy TTL) bejelentkezik egy adott alkalmazásba vagy egy másik alkalmazásba, Azure AD B2C megpróbál új hozzáférési jogkivonatot beszerezni az összevont identitás-szolgáltatótól. Ha az összevont identitás-szolgáltató munkamenete lejárt vagy érvénytelen, az összevont identitás szolgáltatója kéri a felhasználót a hitelesítő adataik megadására. Ha a munkamenet továbbra is aktív (vagy ha a felhasználó egy összevont fiók helyett helyi fiókkal jelentkezett be), a Azure AD B2C engedélyezi a felhasználó számára, és megszünteti a további kéréseket.

Konfigurálhatja a munkamenet viselkedését, beleértve a munkamenet ÉLETTARTAMát, valamint azt, hogy a Azure AD B2C hogyan osztja meg a munkamenetet a házirendek és az alkalmazások között.

### <a name="federated-identity-provider-session"></a>Összevont identitás-szolgáltatói munkamenet

A közösségi vagy vállalati identitás-szolgáltató a saját munkamenetét kezeli. A cookie-t az Identity Provider tartományneve (például `https://login.salesforce.com` ) tárolja. Azure AD B2C nem szabályozza az összevont identitás-szolgáltatói munkamenetet. Ehelyett a munkamenet viselkedését az összevont identitás-szolgáltató határozza meg. 

Vegyük példaként a következő esetet:

1. A felhasználók bejelentkeznek a Facebookon, hogy ellenőrizzék a hírcsatornát.
2. Később a felhasználó megnyitja az alkalmazást, és elindítja a bejelentkezési folyamatot. Az alkalmazás átirányítja a felhasználót, hogy Azure AD B2C a bejelentkezési folyamat befejezéséhez.
3. A Azure AD B2C regisztrációs vagy bejelentkezési oldalán a felhasználó úgy dönt, hogy bejelentkezik Facebook-fiókjával. A rendszer átirányítja a felhasználót a Facebook-ra. Ha a Facebookban aktív munkamenet van, a rendszer nem kéri a felhasználót, hogy adja meg hitelesítő adatait, és a rendszer azonnal átirányítja a Facebook-tokenbe Azure AD B2C.

### <a name="application-session"></a>Alkalmazás-munkamenet

A web-, mobil-vagy egyoldalas alkalmazások védelmét OAuth-hozzáférés, azonosító jogkivonatok vagy SAML-tokenek védik. Amikor egy felhasználó egy védett erőforráshoz próbál hozzáférni az alkalmazásban, az alkalmazás ellenőrzi, hogy van-e aktív munkamenet az alkalmazás oldalán. Ha nincs alkalmazás-munkamenet, vagy a munkamenet lejárt, akkor az alkalmazás beAzure AD B2C a bejelentkezési oldalra.

Az alkalmazás-munkamenet lehet az alkalmazás tartományának neve alatt tárolt cookie-alapú munkamenet, például: `https://contoso.com` . A mobil alkalmazások eltérő módon tárolhatják a munkamenetet, de hasonló megközelítést alkalmaznak.

## <a name="configure-azure-ad-b2c-session-behavior"></a>Azure AD B2C munkamenet viselkedésének konfigurálása

Beállíthatja a Azure AD B2C munkamenet viselkedését, beleértve a következőket:

- **Webalkalmazás-munkamenet élettartama (perc)** – a sikeres hitelesítés után az Azure ad B2C munkamenet-cookie-t a felhasználó böngészőjében tárolja a rendszer. A munkamenet élettartamát akár 24 óráig is beállíthatja.

- **Webalkalmazás-munkamenet időtúllépése** – azt jelzi, hogy a munkamenetet a munkamenet élettartama beállítás vagy a bejelentkezett maradás (KMSI) beállítás hogyan hosszabbítja meg.
  - **Gördülő** – azt jelzi, hogy a munkamenet minden alkalommal ki van-e terjesztve, amikor a felhasználó elvégez egy cookie-alapú hitelesítést (alapértelmezett).
  - **Absolute** – azt jelzi, hogy a felhasználónak a megadott időszak után újra hitelesítenie kell magát.

- **Egyszeri bejelentkezés konfigurálása** – a Azure ad B2C munkamenet a következő hatókörökkel konfigurálható:
  - **Bérlő** – ez a beállítás az alapértelmezett érték. Ezzel a beállítással a B2C-bérlőben több alkalmazás és felhasználói folyamat is megoszthatja ugyanazt a felhasználói munkamenetet. Ha például egy felhasználó bejelentkezik egy alkalmazásba, a felhasználó zökkenőmentesen is bejelentkezhet egy másikba, amikor hozzáfér.
  - **Alkalmazás** – ez a beállítás lehetővé teszi, hogy a felhasználói munkamenetet kizárólag egy alkalmazáshoz, más alkalmazásoktól függetlenül kezelje. Használhatja például ezt a beállítást, ha azt szeretné, hogy a felhasználó bejelentkezzen a contoso gyógyszertárba, függetlenül attól, hogy a felhasználó már be van-e jelentkezve a contoso-élelmiszerbe.
  - **Házirend** – ez a beállítás lehetővé teszi a felhasználói munkamenetek kizárólag felhasználói folyamatokhoz való fenntartását, az azt használó alkalmazásoktól függetlenül. Ha például a felhasználó már bejelentkezett, és elvégezte a többtényezős hitelesítés (MFA) lépését, a felhasználó több alkalmazás nagyobb biztonságú részeihez is hozzáférhet, ha a munkamenet a felhasználói folyamathoz kötődik, nem jár le.
  - **Letiltva** – ez a beállítás arra kényszeríti a felhasználót, hogy a házirend minden végrehajtásakor a teljes felhasználói folyamaton keresztül fusson.
- **Tartsa meg a bejelentkezett (KMSI)** – a munkamenet élettartamát állandó cookie használatával terjeszti ki. Ha ez a funkció engedélyezve van, és a felhasználó kiválasztja, a munkamenet aktív marad még a felhasználó bezárása után is, és újra megnyitja a böngészőt. A munkamenetet csak akkor vonja vissza a rendszer, ha a felhasználó kijelentkezik. A KMSI funkció csak helyi fiókkal történő bejelentkezésre vonatkozik. A KMSI funkció elsőbbséget élvez a munkamenet élettartama felett.

::: zone pivot="b2c-user-flow"

A munkamenet viselkedésének konfigurálása:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja, majd a felső menüben válassza ki a **címtár + előfizetés** szűrőt, és válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőt tartalmazza.
1. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Azure ad B2C**.
1. Válassza a **felhasználói folyamatok** lehetőséget.
1. Nyissa meg a korábban létrehozott felhasználói folyamatot.
1. Válassza ki a **Tulajdonságok** elemet.
1. Konfigurálja a **webalkalmazás-munkamenet élettartamát (perc)**, a **webalkalmazás-munkamenet időkorlátját**, az **egyszeri bejelentkezés konfigurációját**, és szükség szerint adja **meg az azonosító tokent a kijelentkezési kérésekben** .
1. Kattintson a **Mentés** gombra.

::: zone-end

::: zone pivot="b2c-custom-policy"

A munkamenet-viselkedés és az SSO-konfigurációk módosításához adjon hozzá egy **UserJourneyBehaviors** elemet a [RelyingParty](relyingparty.md) elemen belül.  A **UserJourneyBehaviors** elemnek azonnal követnie kell a **DefaultUserJourney**. A **UserJourneyBehavors** elemnek a következő példához hasonlóan kell kinéznie:

```xml
<UserJourneyBehaviors>
   <SingleSignOn Scope="Application" />
   <SessionExpiryType>Absolute</SessionExpiryType>
   <SessionExpiryInSeconds>86400</SessionExpiryInSeconds>
</UserJourneyBehaviors>
```
::: zone-end

## <a name="enable-keep-me-signed-in-kmsi"></a>Bejelentkezés megtartásának engedélyezése (KMSI)

Engedélyezheti a KMSI szolgáltatást a webes és a natív alkalmazások felhasználói számára, akik helyi fiókkal rendelkeznek a Azure AD B2C könyvtárban. Ha engedélyezi a szolgáltatást, a felhasználók dönthetnek úgy, hogy bejelentkezve maradnak, így a munkamenet aktív marad a böngésző bezárása után. Ezután újra megnyithatja a böngészőt anélkül, hogy meg kellene adnia a felhasználónevét és jelszavát. A rendszer visszavonja ezt a hozzáférést, amikor egy felhasználó kijelentkezik.

![Példa a bejelentkezési bejelentkezési oldalára, amely egy bejelentkezve marad jelölőnégyzetet jelenít meg](./media/session-behavior/keep-me-signed-in.png)


::: zone pivot="b2c-user-flow"

A KMSI az egyéni felhasználói folyamat szintjén konfigurálható. A felhasználói folyamatok KMSI engedélyezése előtt vegye figyelembe a következőket:

- A KMSI csak a regisztráció és a bejelentkezés (bejelentkezett), a bejelentkezés és a profil szerkesztési felhasználói folyamatainak **ajánlott** verziói esetén támogatott. Ha jelenleg a felhasználói folyamatok **standard** vagy **Legacy Preview-v2** verziója van, és engedélyezni szeretné a KMSI, akkor a felhasználói folyamatok új, **javasolt** verzióit kell létrehoznia.
- A KMSI nem támogatott a jelszó-visszaállítási vagy a regisztrációs felhasználói folyamatok esetében.
- Ha engedélyezni szeretné a KMSI a bérlő összes alkalmazásához, javasoljuk, hogy engedélyezze a KMSI a bérlő összes felhasználói folyamata számára. Mivel egy felhasználó több házirenddel is megtekinthető egy munkamenet során, lehetséges, hogy előfordulhat, hogy a KMSI engedélyezése nem engedélyezett, ami eltávolítja a KMSI cookie-t a munkamenetből.
- A KMSI nem szabad engedélyezni a nyilvános számítógépeken.

### <a name="configure-kmsi-for-a-user-flow"></a>Felhasználói folyamat KMSI konfigurálása

A KMSI engedélyezése a felhasználói folyamat számára:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja. Válassza ki a **címtár + előfizetés**   szűrőt a felső menüben, és válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
3. Válassza ki az **összes szolgáltatást** a   Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Azure ad B2C**.
4. Válassza a **felhasználói folyamatok (szabályzatok)** lehetőséget.
5. Nyissa meg a korábban létrehozott felhasználói folyamatot.
6. Válassza a **Tulajdonságok** lehetőséget.

7. A  **munkamenet viselkedése** alatt jelölje be **a bejelentkezett munkamenet megtartása lehetőségnél az engedélyezés** lehetőséget. A **munkamenet (nap) bejelentkezve tartása** mellett adjon meg egy 1 és 90 közötti értéket, amely meghatározza, hogy a munkamenet hány napig maradhat nyitva.


   ![A bejelentkezett munkamenet fenntartásának engedélyezése](media/session-behavior/enable-keep-me-signed-in.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

A felhasználók nem engedélyezhetik ezt a lehetőséget a nyilvános számítógépeken.

### <a name="configure-the-page-identifier"></a>Az oldal azonosítójának konfigurálása

A KMSI engedélyezéséhez állítsa a Content definition `DataUri` elemet az [oldal-azonosítóra](contentdefinitions.md#datauri) `unifiedssp` és az [oldal Version](page-layout.md) *1.1.0* vagy újabb verzióra.

1. Nyissa meg a szabályzat fájlkiterjesztés-fájlját. Például: <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> . Ez a kiterjesztési fájl az egyéni házirend alapszintű csomagban található egyik házirend-fájl, amelyet az előfeltételben kell megszereznie az [Egyéni szabályzatok használatának első lépéseiben](custom-policy-get-started.md).
1. Keresse meg a **BuildingBlocks** elemet. Ha az elem nem létezik, adja hozzá.
1. Adja hozzá a **ContentDefinitions** elemet a szabályzat **BuildingBlocks** eleméhez.

    Az egyéni szabályzatnak a következő kódrészlethez hasonlóan kell kinéznie:

    ```xml
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsignin">
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0</DataUri>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>
    ```

### <a name="add-the-metadata-to-the-self-asserted-technical-profile"></a>Metaadatok hozzáadása az önérvényesített technikai profilhoz

Ha a KMSI jelölőnégyzetet fel szeretné venni a regisztrációs és bejelentkezési oldalra, állítsa a `setting.enableRememberMe` metaadatokat igaz értékre. Bírálja felül a SelfAsserted-LocalAccountSignin-e-mailes technikai profilokat a bővítmény fájljában.

1. Keresse meg a ClaimsProviders elemet. Ha az elem nem létezik, adja hozzá.
1. Adja hozzá a következő jogcím-szolgáltatót a ClaimsProviders elemhez:

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
      <Metadata>
        <Item Key="setting.enableRememberMe">True</Item>
      </Metadata>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

1. Mentse a bővítmények fájlt.

### <a name="configure-a-relying-party-file"></a>Függő entitás fájljának konfigurálása

Frissítse a függő entitás (RP) fájlját, amely kezdeményezi a létrehozott felhasználói utat. A keepAliveInDays paraméter lehetővé teszi annak konfigurálását, hogy mennyi ideig maradjon a bejelentkezve maradó (KMSI) munkamenet-cookie. Ha például az értéket 30 értékre állítja, akkor a KMSI-munkamenet cookie-je 30 napig marad. Az érték tartománya 1 és 90 nap közé esik.

1. Nyissa meg az egyéni házirend-fájlt. Például *SignUpOrSignin.xml*.
1. Ha még nem létezik, adjon hozzá egy `<UserJourneyBehaviors>` alárendelt csomópontot a `<RelyingParty>` csomóponthoz. `<DefaultUserJourney ReferenceId="User journey Id" />`A szolgáltatásnak közvetlenül a következő után kell elhelyezkednie, például: `<DefaultUserJourney ReferenceId="SignUpOrSignIn" />` .
1. Adja hozzá a következő csomópontot az elem gyermekének `<UserJourneyBehaviors>` .

    ```xml
    <UserJourneyBehaviors>
      <SingleSignOn Scope="Tenant" KeepAliveInDays="30" />
      <SessionExpiryType>Absolute</SessionExpiryType>
      <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
    </UserJourneyBehaviors>
    ```

Azt javasoljuk, hogy a SessionExpiryInSeconds értékét rövid időtartamra (1200 másodpercre) állítsa be, míg a KeepAliveInDays értéke viszonylag hosszú (30 nap) lehet, ahogy az alábbi példában látható:

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <UserJourneyBehaviors>
    <SingleSignOn Scope="Tenant" KeepAliveInDays="30" />
    <SessionExpiryType>Absolute</SessionExpiryType>
    <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
  </UserJourneyBehaviors>
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="displayName" />
      <OutputClaim ClaimTypeReferenceId="givenName" />
      <OutputClaim ClaimTypeReferenceId="surname" />
      <OutputClaim ClaimTypeReferenceId="email" />
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="identityProvider" />
      <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

::: zone-end

## <a name="sign-out"></a>Kijelentkezés

Ha ki szeretné írni a felhasználót az alkalmazásból, nem elég az alkalmazás cookie-jait törölni, vagy más módon leállítani a munkamenetet a felhasználóval. A kijelentkezéshez át kell irányítani a felhasználót a Azure AD B2C. Ellenkező esetben előfordulhat, hogy a felhasználó újra hitelesíteni tudja az alkalmazásait anélkül, hogy újra be kellene írnia a hitelesítő adatait.

Kijelentkezési kérelem esetén Azure AD B2C:

1. A Azure AD B2C cookie-alapú munkamenet érvénytelenítése.
::: zone pivot="b2c-user-flow"
2. Kísérlet az összevont identitás-szolgáltatókból való kijelentkezésre
::: zone-end
::: zone pivot="b2c-custom-policy"
3. Kísérlet az összevont identitás-szolgáltatókból való kijelentkezésre:
   - OpenId Connect – ha az identitás-szolgáltató jól ismert konfigurációs végpontja megad egy `end_session_endpoint` helyet. A kijelentkezési kérelem nem adja át a `id_token_hint` paramétert. Ha az összevont identitás szolgáltatója ehhez a paraméterhez szükséges, a kijelentkezési kérelem sikertelen lesz.
   - OAuth2 – ha az [Identity Provider metaadatai](oauth2-technical-profile.md#metadata) tartalmazzák a `end_session_endpoint` helyet.
   - SAML – ha az [Identity Provider metaadatai](identity-provider-generic-saml.md) tartalmazzák a `SingleLogoutService` helyet.
4. Opcionálisan kijelentkezhet más alkalmazásokból is. További információ: az [egyszeri kijelentkezési](#single-sign-out) szakasz.

> [!NOTE]
> A kijelentkezés az összevont identitás-szolgáltatókról letiltható, ha az identitás-szolgáltató technikai profiljának metaadatait a értékre állítja `SingleLogoutEnabled` `false` .
::: zone-end

A kijelentkezési szolgáltatás törli a felhasználó egyszeri bejelentkezési állapotát Azure AD B2C, de előfordulhat, hogy nem írja alá a felhasználót a közösségi identitás-szolgáltatói munkamenetből. Ha a felhasználó ugyanazt az identitás-szolgáltatót választja egy későbbi bejelentkezés során, akkor a hitelesítő adatok megadása nélkül is újrahitelesíthetők. Ha a felhasználó ki szeretne jelentkezni az alkalmazásból, nem feltétlenül jelenti azt, hogy ki szeretné jelentkezni a Facebook-fiókjából. Ha azonban helyi fiókokat használ, a felhasználó munkamenete megfelelően végződik.

::: zone pivot="b2c-custom-policy"

### <a name="single-sign-out"></a>Egyszeri kijelentkezés 

Amikor átirányítja a felhasználót a Azure AD B2C kijelentkezési végpontra (a OAuth2 és az SAML protokollok esetében egyaránt), Azure AD B2C törli a felhasználó munkamenetét a böngészőből. Előfordulhat azonban, hogy a felhasználó továbbra is be van jelentkezve más alkalmazásokba, amelyek az Azure AD B2C-t használják a hitelesítéshez. Ha engedélyezni szeretné, hogy az alkalmazások egyidejűleg írják alá a felhasználót, Azure AD B2C egy HTTP GET kérelmet küld az összes olyan alkalmazás regisztrálásához, `LogoutUrl` amelyhez a felhasználó jelenleg be van jelentkezve.

Az alkalmazásoknak válaszolnia kell erre a kérelemre a felhasználót azonosító munkamenetek törlésével és a válasz visszaadásával `200` . Ha az alkalmazásban szeretné támogatni az egyszeri kijelentkezést, az alkalmazás kódjában végre kell hajtania egy `LogoutUrl` alkalmazást. 

Az egyszeri kijelentkezés támogatásához a token kiállítói technikai profiljainak mind a JWT, mind az SAML esetében meg kell adniuk a következőket:

- A protokoll neve, például `<Protocol Name="OpenIdConnect" />`
- A munkamenet technikai profiljára mutató hivatkozás, például: `UseTechnicalProfileForSessionManagement ReferenceId="SM-OAuth-issuer" />` .

Az alábbi példa a JWT és az SAML-jogkivonat kiállítóit mutatja be egyszeri kijelentkezéssel:

```xml
<ClaimsProvider>
  <DisplayName>Local Account SignIn</DisplayName>
  <TechnicalProfiles>
    <!-- JWT Token Issuer -->
    <TechnicalProfile Id="JwtIssuer">
      <DisplayName>JWT token Issuer</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <OutputTokenFormat>JWT</OutputTokenFormat>
      ...    
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-OAuth-issuer" />
    </TechnicalProfile>

    <!-- Session management technical profile for OIDC based tokens -->
    <TechnicalProfile Id="SM-OAuth-issuer">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.OAuthSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    </TechnicalProfile>

    <!--SAML token issuer-->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>SAML token issuer</DisplayName>
      <Protocol Name="SAML2" />
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      ...
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-issuer" />
    </TechnicalProfile>

    <!-- Session management technical profile for SAML based tokens -->
    <TechnicalProfile Id="SM-Saml-issuer">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

::: zone-end

### <a name="secure-your-logout-redirect"></a>A kijelentkezési átirányítás biztonságossá tétele

A kijelentkezés után a rendszer átirányítja a felhasználót a paraméterben megadott URI-ra `post_logout_redirect_uri` , az alkalmazáshoz megadott válasz URL-címektől függetlenül. Ha azonban érvényes `id_token_hint` értéket ad át, és a **kijelentkezési kérelmekhez szükséges azonosító jogkivonat** be van kapcsolva, Azure ad B2C ellenőrzi, hogy az adott `post_logout_redirect_uri` alkalmazás beállított átirányítási URI azonosítóinak egyike megfelel-e az átirányítás végrehajtása előtt. Ha nincs beállítva egyező válasz URL-cím az alkalmazáshoz, a rendszer hibaüzenetet jelenít meg, és a felhasználót nem irányítja át. 

::: zone pivot="b2c-user-flow"

AZONOSÍTÓ jogkivonat megkövetelése a kijelentkezési kérelmekben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja, majd a felső menüben válassza ki a **címtár + előfizetés** szűrőt, és válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőt tartalmazza.
1. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Azure ad B2C**.
1. Válassza a **felhasználói folyamatok** lehetőséget.
1. Nyissa meg a korábban létrehozott felhasználói folyamatot.
1. Válassza ki a **Tulajdonságok** elemet.
1. Engedélyezze az **azonosító jogkivonat megkövetelése lehetőséget a kijelentkezési kérelmekben**.
1. Térjen vissza a  **Azure ad B2Cra**.
1. Válassza a **Alkalmazásregisztrációk** lehetőséget, majd válassza ki az alkalmazást.
1. Válassza a **Hitelesítés** lehetőséget.
1. A **kijelentkezési URL-cím** szövegmezőbe írja be a kijelentkezési ÁTirányítási URI-t, majd kattintson a **Mentés** gombra.

::: zone-end

::: zone pivot="b2c-custom-policy"

Ha azonosító tokent szeretne megadni a kijelentkezési kérelmekben, adjon hozzá egy **UserJourneyBehaviors** elemet a [RelyingParty](relyingparty.md) elemen belül. Ezután állítsa be a **SingleSignon** elem **EnforceIdTokenHintOnLogout** a következőre: `true` . A **UserJourneyBehaviors** elemnek a következő példához hasonlóan kell kinéznie:

```xml
<UserJourneyBehaviors>
  <SingleSignOn Scope="Tenant" EnforceIdTokenHintOnLogout="true"/>
</UserJourneyBehaviors>
```

::: zone-end

Az alkalmazás kijelentkezési URL-címének konfigurálása:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja, majd a felső menüben válassza ki a **címtár + előfizetés** szűrőt, és válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőt tartalmazza.
1. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Azure ad B2C**.
1. Válassza a **Alkalmazásregisztrációk** lehetőséget, majd válassza ki az alkalmazást.
1. Válassza a **Hitelesítés** lehetőséget.
1. A **kijelentkezési URL-cím** szövegmezőbe írja be a kijelentkezési ÁTirányítási URI-t, majd kattintson a **Mentés** gombra.

## <a name="next-steps"></a>Következő lépések

- Megtudhatja, hogyan [konfigurálhat jogkivonatokat a Azure ad B2Cban](configure-tokens.md).
