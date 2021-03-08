---
title: Jelszó-visszaállítási folyamat beállítása Azure AD B2C
titleSuffix: Azure AD B2C
description: Megtudhatja, hogyan állíthatja be a jelszó-visszaállítási folyamatot a Azure Active Directory B2Cban.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/08/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: fa34e8ea71c307b75a3f345861f8ed99d131b3fd
ms.sourcegitcommit: f6193c2c6ce3b4db379c3f474fdbb40c6585553b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2021
ms.locfileid: "102447928"
---
# <a name="set-up-a-password-reset-flow-in-azure-active-directory-b2c"></a>Jelszó-visszaállítási folyamat beállítása Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

## <a name="password-reset-flow"></a>Jelszó-visszaállítási folyamat

A [regisztrációs és bejelentkezési utazás](add-sign-up-and-sign-in-policy.md) lehetővé teszi a felhasználók számára, hogy saját jelszavukat a **jelszó elfelejtette?** hivatkozás használatával állítsa alaphelyzetbe. A jelszó-visszaállítási folyamat a következő lépésekkel jár:

1. A regisztrációs és bejelentkezési oldalon a felhasználó az **elfelejtette a jelszavát?** hivatkozásra kattint. Azure AD B2C kezdeményezi a jelszó-visszaállítási folyamatot. 
2. A felhasználó megadja és ellenőrzi az e-mail-címüket egy időzített PIN-kód megadásával.
3. A felhasználó ezután új jelszót adhat meg.

![Jelszó-visszaállítási folyamat](./media/add-password-reset-policy/password-reset-flow.png)

A jelszó-visszaállítási folyamat az Azure AD B2C helyi fiókjaira vonatkozik, amelyek [e-mail-címet](identity-provider-local.md#email-sign-in) vagy [felhasználónevet](identity-provider-local.md#username-sign-in) használnak a bejelentkezéshez jelszóval.

A felhasználók a véletlenszerű jelszavakkal Azure AD B2Cba való áttelepítését követően gyakori eljárás, hogy a felhasználók ellenőrizzék az e-mail-címüket, és az első bejelentkezéskor visszaállítsák a jelszavukat. Gyakori, hogy a felhasználó úgy kényszeríti a jelszót, hogy új jelszó megváltoztatását követően a rendszergazda megváltoztatja a jelszavát; a funkció engedélyezéséhez tekintse meg a [jelszó-visszaállítás kényszerítése](force-password-reset.md) című témakört.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="self-service-password-reset-recommended"></a>Önkiszolgáló jelszó-visszaállítás (ajánlott)

Az új jelszó-visszaállítási élmény mostantól a regisztrálási vagy bejelentkezési szabályzat részét képezi. Amikor a felhasználó kiválasztja az **elfelejtette a jelszavát?** hivatkozást, a rendszer azonnal elküldje az elfelejtett jelszó felhasználói felületének. Az alkalmazásnak többé nem kell kezelnie a [AADB2C90118 hibakódot](#password-reset-policy-legacy), és nincs szüksége külön szabályzatra a jelszó-visszaállításhoz.

::: zone pivot="b2c-user-flow"

Az önkiszolgáló jelszó-visszaállítási élmény konfigurálható a **bejelentkezéshez (ajánlott)** , illetve a regisztrációhoz **és bejelentkezéshez (ajánlott)** felhasználói folyamatokhoz. Ha nem rendelkezik ilyen felhasználói folyamattal, hozzon létre egy [bejelentkezést, és regisztrálja](add-sign-up-and-sign-in-policy.md) a felhasználói folyamatot. 

Az önkiszolgáló jelszó-visszaállítás engedélyezése a regisztrációs vagy bejelentkezési felhasználói folyamat számára:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza ki a **címtár + előfizetés** ikont a portál eszköztárán, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
1. A Azure Portal keresse meg és válassza a **Azure ad B2C** lehetőséget.
1. Válassza a **felhasználói folyamatok** lehetőséget.
1. Válassza ki a testreszabni kívánt regisztrációs vagy bejelentkezési felhasználói folyamatot (az **ajánlott** típus).
1. A bal oldali menü **Beállítások** területén válassza a **Tulajdonságok** lehetőséget.
1. A **jelszó bonyolultsága** területen válassza az **önkiszolgáló jelszó-visszaállítás** lehetőséget.
1. Kattintson a **Mentés** gombra.
1. A bal oldali menü **Testreszabás** **területén válassza a lapelrendezések lehetőséget**.
1. A lapelrendezés **verziójában** válassza a **2.1.2-current** vagy újabb lehetőséget.
1. Kattintson a **Mentés** gombra.

::: zone-end

::: zone pivot="b2c-custom-policy"

Az alábbi szakaszok azt ismertetik, hogyan adhat hozzá önkiszolgáló jelszó-felhasználói élményt egy egyéni szabályzathoz. A minta az [egyéni házirend alapszintű csomagban](./custom-policy-get-started.md)található házirend-fájlokon alapul. 

> [!TIP]
> Megtalálhatja a "regisztrálás vagy bejelentkezés a jelszó-visszaállítással" című teljes mintát a [githubon](https://github.com/azure-ad-b2c/samples/tree/master/policies/embedded-password-reset).

### <a name="indicate-a-user-selected-the-forgot-your-password-link"></a>Azt jelzi, hogy a felhasználó az elfelejtette a jelszavát? hivatkozás

Annak a szabályzatnak a jelzéséhez, amelyhez a felhasználó bejelölte az **elfelejtett jelszó?** hivatkozást, adjon meg egy logikai jogcímet. Ez a jogcím arra szolgál, hogy az elfelejtett jelszó technikai profiljába irányítsa a felhasználót. Ez a jogcím a jogkivonat számára is kiállítható, így az alkalmazás tisztában van azzal, hogy a felhasználó az elfelejtett jelszó folyamatán keresztül jelentkezett be.

A jogcímeket a jogcím- [sémában](claimsschema.md)deklarálhatja. Nyissa meg a szabályzat Extensions (bővítmények) fájlját. Például: <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .

1. Keresse meg a [BuildingBlocks](buildingblocks.md) elemet. Ha az elem nem létezik, adja hozzá.
1. Keresse meg a [ClaimsSchema](claimsschema.md) elemet. Ha az elem nem létezik, adja hozzá.
1. Adja hozzá a következő jogcímet a **ClaimsSchema** elemhez. 

```XML
<!-- 
<BuildingBlocks>
  <ClaimsSchema> -->
    <ClaimType Id="isForgotPassword">
      <DisplayName>isForgotPassword</DisplayName>
      <DataType>boolean</DataType>
      <AdminHelpText>Whether the user has selected Forgot your Password</AdminHelpText>
    </ClaimType>
  <!--
  </ClaimsSchema>
</BuildingBlocks> -->
```

### <a name="upgrade-the-page-layout-version"></a>A lapelrendezés verziójának frissítése

Lapelrendezés [verziója](contentdefinitions.md#migrating-to-page-layout) `2.1.2` az önkiszolgáló jelszó-visszaállítási folyamat engedélyezéséhez szükséges a regisztrációs vagy bejelentkezési útvonalon belül.

1. Keresse meg a [BuildingBlocks](buildingblocks.md) elemet. Ha az elem nem létezik, adja hozzá.
1. Keresse meg a [ContentDefinitions](contentdefinitions.md) elemet. Ha az elem nem létezik, adja hozzá.
1. Módosítsa a **DataURI** elemet a **ContentDefinition** elemen belül az id **API. signuporsignin** néven az alábbi ábrán látható módon.

```xml
<!-- 
<BuildingBlocks>
  <ContentDefinitions> -->
    <ContentDefinition Id="api.signuporsignin">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:2.1.2</DataUri>
    </ContentDefinition>
  <!-- 
  </ContentDefinitions>
</BuildingBlocks> -->
```

A jogcím elindításához `isForgotPassword` a rendszer egy jogcím-átalakítási technikai profilt használ. Ez a technikai profil később lesz hivatkozva. A meghívásakor a jogcím értéke a következőre lesz beállítva: `isForgotPassword` `true` . Keresse meg az `ClaimsProviders` elemet. Ha az elem nem létezik, adja hozzá. Ezután adja hozzá a következő jogcím-szolgáltatót:  

```xml
<!-- 
<ClaimsProviders> -->
  <ClaimsProvider>
    <DisplayName>Local Account</DisplayName>
    <TechnicalProfiles>
      <TechnicalProfile Id="ForgotPassword">
        <DisplayName>Forgot your password?</DisplayName>
        <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="isForgotPassword" DefaultValue="true" AlwaysUseDefaultValue="true"/>
        </OutputClaims>
      </TechnicalProfile>
      <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
        <Metadata>
          <Item Key="setting.forgotPasswordLinkOverride">ForgotPasswordExchange</Item>
        </Metadata>
      </TechnicalProfile>
    </TechnicalProfiles>
  </ClaimsProvider>
<!-- 
</ClaimsProviders> -->
```

A `SelfAsserted-LocalAccountSignin-Email` technikai profil leállítja `setting.forgotPasswordLinkOverride` a jelszó-visszaállítási jogcímek cseréjét a felhasználói úton való végrehajtáshoz. 

### <a name="add-the-password-reset-sub-journey"></a>A jelszó-visszaállítási sub-útvonal hozzáadása

Az utazás mostantól lehetővé teszi a felhasználó számára a bejelentkezést, a regisztrációt és a jelszó-visszaállítást. A felhasználói utazás jobb megszervezéséhez egy [Alútvonal](subjourneys.md) használható a jelszó-visszaállítási folyamat kezeléséhez.

A rendszer a felhasználói útvonalon hívja meg az alárendelt utat, és végrehajtja a jelszó-visszaállítási élmény a felhasználónak való továbbításának konkrét lépéseit. Használja a `Call` Sub Journey típust úgy, hogy az Alútvonal befejeződése után a vezérlést a rendszer az alútvonalat kezdeményező előkészítési lépéshez adja vissza.

Keresse meg az `SubJourneys` elemet. Ha az elem nem létezik, adja hozzá a `User Journeys` elemet a elem után. Ezután adja hozzá a következő Al-utat:

```xml
<!--
<SubJourneys>-->
  <SubJourney Id="PasswordReset" Type="Call">
    <OrchestrationSteps>
      <!-- Validate user's email address. -->
      <OrchestrationStep Order="1" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="PasswordResetUsingEmailAddressExchange" TechnicalProfileReferenceId="LocalAccountDiscoveryUsingEmailAddress" />
        </ClaimsExchanges>
      </OrchestrationStep>

      <!-- Collect and persist a new password. -->
      <OrchestrationStep Order="2" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="NewCredentials" TechnicalProfileReferenceId="LocalAccountWritePasswordUsingObjectId" />
        </ClaimsExchanges>
      </OrchestrationStep>
    </OrchestrationSteps>
  </SubJourney>
<!--
</SubJourneys>-->
```

### <a name="prepare-your-user-journey"></a>A felhasználói út előkészítése

Csatlakoztatnia kell az **elfelejtette jelszavát?** az elfelejtett jelszó alútvonalra mutató hivatkozás. Ehhez a **CombinedSignInAndSignUp** lépés **ClaimsProviderSelection** elemében lévő elfelejtett jelszó Alútvonal-azonosítóra kell hivatkoznia.

Ha nem rendelkezik a saját egyéni felhasználói útra **CombinedSignInAndSignUp** lépéssel, a következő eljárással duplikálhat egy meglévő regisztrációs vagy bejelentkezési felhasználói utat. Ellenkező esetben folytassa a következő szakasszal.

1. Nyissa meg a *TrustFrameworkBase.xml* fájlt az alapszintű csomagból.
2. A **UserJourney** elem teljes tartalmának megkeresése és másolása `Id="SignUpOrSignIn"` .
3. Nyissa meg a *TrustFrameworkExtensions.xmlt* , és keresse meg a **UserJourneys** elemet. Ha az elem nem létezik, vegyen fel egyet.
4. Hozzon létre egy gyermek elemet a **UserJourneys** elemhez a 2. lépésben másolt **UserJourney** elem teljes tartalmának beillesztésével.
5. Nevezze át a felhasználói út azonosítóját. Például: `Id="CustomSignUpSignIn"`.

### <a name="connect-the-forgot-password-link-to-the-forgot-password-sub-journey"></a>Az elfelejtett jelszó hivatkozásának csatlakoztatása az elfelejtett jelszó alútvonalhoz 

A felhasználói úton az elfelejtett jelszó alútvonala **ClaimsProviderSelection** lehet. Az elem hozzáadása összekapcsolja az **elfelejtette jelszavát?** az elfelejtett jelszó alútvonalra mutató hivatkozás.

1. A felhasználói úton keresse meg a (vagy) elemet tartalmazó előkészítési lépés elemét `Type="CombinedSignInAndSignUp"` `Type="ClaimsProviderSelection"` . Ez általában az első előkészítési lépés. A **ClaimsProviderSelections** elem azon identitás-szolgáltatók listáját tartalmazza, amelyeket a felhasználó a bejelentkezéshez használhat. Adja hozzá a következő sort:
    
    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="ForgotPasswordExchange" />
    ```

1. A következő előkészítési lépésben adjon hozzá egy **ClaimsExchange** elemet. Adja hozzá a következő sort:

    ```xml
    <ClaimsExchange Id="ForgotPasswordExchange" TechnicalProfileReferenceId="ForgotPassword" />
    ```
    
1. Adja hozzá a következő előkészítési lépést az aktuális lépés és a következő lépés között. A hozzáadott új előkészítési lépés azt ellenőrzi, hogy létezik-e a `isForgotPassword` jogcím. Ha a jogcím létezik, meghívja a [jelszó alaphelyzetbe állítására vonatkozó sub-utat](#add-the-password-reset-sub-journey). 

    ```xml
    <OrchestrationStep Order="3" Type="InvokeSubJourney">
      <Preconditions>
        <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
          <Value>isForgotPassword</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
      </Preconditions>
      <JourneyList>
        <Candidate SubJourneyReferenceId="PasswordReset" />
      </JourneyList>
    </OrchestrationStep>
    ```
    
1. Miután hozzáadta az új előkészítési lépést, a lépéseket egymás után számozza, és nem hagyhatja 1 és N közötti egész számot.

### <a name="set-the-user-journey-to-be-executed"></a>A végrehajtandó felhasználói út beállítása

Most, hogy módosította vagy létrehozta a felhasználói utat, a **függő entitás** szakaszban válassza ki azt az utat, amelyet a Azure ad B2C fog végrehajtani ehhez az egyéni házirendhez. A [RelyingParty](relyingparty.md) elemen belül keresse meg a **DefaultUserJourney** elemet. Frissítse a  **DefaultUserJourney ReferenceId** , hogy az megfeleljen annak a felhasználói ÚTNAK az azonosítójának, amelyben a **ClaimsProviderSelections** hozzáadta.

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="CustomSignUpSignIn" />
  ...
</RelyingParty>
```

### <a name="indicate-the-forgot-password-flow-to-your-app"></a>Az elfelejtett jelszó folyamatának jelzése az alkalmazáshoz

Előfordulhat, hogy az alkalmazásnak meg kell állapítania, hogy a felhasználó bejelentkezett-e az elfelejtett jelszó felhasználói folyamatán keresztül. A **isForgotPassword** jogcím egy logikai értéket tartalmaz, amely azt jelzi, hogy az alkalmazásnak eljuttatott tokenben állítható be. Ha szükséges, adja hozzá a `isForgotPassword` kimeneti jogcímeket a **függő entitás** szakaszhoz. Az alkalmazás megtekintheti a `isForgotPassword` jogcímet annak megállapításához, hogy a felhasználó visszaállítja-e a jelszavát.

```xml
<RelyingParty>
  <OutputClaims>
    ...
    <OutputClaim ClaimTypeReferenceId="isForgotPassword" DefaultValue="false" />
  </OutputClaims>
</RelyingParty>
```


### <a name="upload-the-custom-policy"></a>Egyéni szabályzat feltöltése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza ki a **címtár + előfizetés** ikont a portál eszköztárán, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
1. A Azure Portal keresse meg és válassza a **Azure ad B2C** lehetőséget.
1. A **szabályzatok** területen válassza az **identitási élmény keretrendszere** elemet.
1. Válassza az **egyéni házirend feltöltése** lehetőséget, majd töltse fel a két, a következő sorrendben módosított házirend-fájlt:
   1. A kiterjesztési szabályzat, például: `TrustFrameworkExtensions.xml` .
   2. A függő entitásra vonatkozó házirend, például: `SignUpSignIn.xml` .

::: zone-end

### <a name="test-the-password-reset-flow"></a>A jelszó-visszaállítási folyamat tesztelése

1. Válassza ki a tesztelni kívánt regisztrációs vagy bejelentkezési felhasználói folyamatot (az ajánlott típus).
1. Válassza a **felhasználói folyamat futtatása** lehetőséget.
1. Az **alkalmazás** lapon válassza ki a korábban regisztrált *webapp1* nevű webalkalmazást. A **Válasz URL-címének** meg kell jelennie `https://jwt.ms` .
1. Válassza a **felhasználói folyamat futtatása** lehetőséget.
1. A regisztrációs vagy bejelentkezési oldalon válassza a **jelszó elfelejtette?** lehetőséget.
1. Ellenőrizze a korábban létrehozott fiók e-mail-címét, majd kattintson a **Folytatás** gombra.
1. Most lehetősége van a felhasználó jelszavának módosítására. Módosítsa a jelszót, és válassza a **Folytatás** lehetőséget. A rendszer visszaküldi a tokent, `https://jwt.ms` és megjelenik Önnek.
1. A visszatérési jogkivonat jogcím-értékének bejelölése `isForgotPassword` . Ha létezik, és igaz értékre van állítva, akkor ez azt jelzi, hogy a felhasználó alaphelyzetbe állította a jelszót.

## <a name="password-reset-policy-legacy"></a>Jelszó-visszaállítási házirend (örökölt)

Ha az [önkiszolgáló jelszó-visszaállítási](#self-service-password-reset-recommended) élmény nincs engedélyezve, a hivatkozásra kattintva nem aktiválhatja automatikusan a jelszó-visszaállítás felhasználói folyamatát. Ehelyett a hibakódot a `AADB2C90118` rendszer visszaadja az alkalmazásnak. Az alkalmazásnak ezt a hibakódot úgy kell kezelnie, hogy újrainicializálja a hitelesítési könyvtárat a Azure AD B2C jelszó-visszaállítási felhasználói folyamat hitelesítéséhez.

A következő ábrán:

1. Az alkalmazásból a felhasználó a Bejelentkezés gombra kattint. Az alkalmazás elindít egy engedélyezési kérelmet, és a felhasználó Azure AD B2C a bejelentkezés befejezéséhez. Az engedélyezési kérelem megadja a regisztrációs vagy bejelentkezési szabályzat nevét, például **B2C_1_signup_signin**.
1. A felhasználó kiválasztja az **elfelejtette a jelszavát?** hivatkozást. Azure AD B2C a AADB2C90118 hibakódot adja vissza az alkalmazásnak.
1. Az alkalmazás kezeli a hibakódot, és kezdeményez egy új engedélyezési kérelmet. Az engedélyezési kérelem megadja a jelszó-visszaállítási házirend nevét, például **B2C_1_pwd_reset**.

![Örökölt jelszó-visszaállítási felhasználói folyamat](./media/add-password-reset-policy/password-reset-flow-legacy.png)

Ha példát szeretne látni, tekintse meg az [egyszerű ASP.net mintát](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI), amely bemutatja a felhasználói folyamatok összekapcsolását.

::: zone pivot="b2c-user-flow"

### <a name="create-a-password-reset-user-flow"></a>Jelszó-visszaállítási felhasználói folyamat létrehozása

Ha engedélyezni szeretné, hogy az alkalmazás felhasználói új jelszót állítsanak vissza, hozzon létre egy jelszó-visszaállítási felhasználói folyamatot.

1. A Azure AD B2C bérlő áttekintő menüjében válassza a **felhasználói folyamatok** lehetőséget, majd válassza az **új felhasználói folyamat** elemet.
1. A **felhasználói folyamat létrehozása** lapon válassza a **jelszó alaphelyzetbe állítása** lehetőséget. 
1. **A verzió kiválasztása** területen válassza az **ajánlott** lehetőséget, majd válassza a **Létrehozás** lehetőséget.
1. Adja meg a felhasználói folyamat **nevét** . Például: *passwordreset1*.
1. Az **Identity Providers** esetében engedélyezze a **jelszó alaphelyzetbe állítását e-mail-cím használatával**.
1. Az **alkalmazás jogcímei** területen válassza a **továbbiak megjelenítése** lehetőséget, majd válassza ki azokat a jogcímeket, amelyeket vissza szeretne állítani az alkalmazásnak visszaküldött engedélyezési jogkivonatokban. Válassza például a **Felhasználó objektumazonosítója** lehetőséget.
1. Válassza az **OK** lehetőséget.
1. A felhasználói folyamat hozzáadásához válassza a **Létrehozás** lehetőséget. Az *B2C_1* előtagját a rendszer automatikusan hozzáfűzi a névhez.

### <a name="test-the-user-flow"></a>A felhasználói folyamat tesztelése

1. Válassza ki a létrehozott felhasználói folyamatot az Áttekintés oldal megnyitásához, majd válassza a **felhasználói folyamat futtatása** lehetőséget.
1. Az **alkalmazás** lapon válassza ki a korábban regisztrált *webapp1* nevű webalkalmazást. A **Válasz URL-címének** meg kell jelennie `https://jwt.ms` .
1. Kattintson a **felhasználói folyamat futtatása** elemre, ellenőrizze a korábban létrehozott fiók e-mail-címét, majd kattintson a **Continue (folytatás**) gombra.
1. Most már módosíthatja a felhasználó jelszavát. Módosítsa a jelszót, és válassza a **Folytatás** lehetőséget. A rendszer visszaküldi a tokent, `https://jwt.ms` és megjelenik Önnek.

::: zone-end

::: zone pivot="b2c-custom-policy"

### <a name="create-a-password-reset-policy"></a>Új jelszó kérésére vonatkozó szabályzat létrehozása

Az egyéni házirendek olyan XML-fájlok készletei, amelyeket feltölt a Azure AD B2C bérlőre a felhasználói útvonalak definiálásához. Alapszintű csomagokat biztosítunk számos előre elkészített házirenddel, többek között a regisztrálással és a bejelentkezéssel, a jelszó-visszaállítással és a profil szerkesztési házirendjével. További információ: Ismerkedés [az egyéni szabályzatokkal Azure ad B2Cban](custom-policy-get-started.md).

::: zone-end

## <a name="next-steps"></a>Következő lépések

Állítsa be a [kényszerített jelszó-visszaállítást](force-password-reset.md).
