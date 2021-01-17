---
title: Bejelentkezés beállítása Azure AD-szervezetekhez
titleSuffix: Azure AD B2C
description: Be kell állítania a bejelentkezést egy adott Azure Active Directory szervezet számára a Azure Active Directory B2Cban.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/15/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit, project-no-code
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 2b640730bac410136ef8fdd4ea8e0261f68a3284
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2021
ms.locfileid: "98538137"
---
# <a name="set-up-sign-in-for-a-specific-azure-active-directory-organization-in-azure-active-directory-b2c"></a>Bejelentkezés beállítása egy adott Azure Active Directory szervezet számára Azure Active Directory B2C

Ez a cikk bemutatja, hogyan engedélyezheti a bejelentkezést egy adott Azure AD-szervezet felhasználóinak a Azure AD B2C felhasználói folyamatával.

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="register-an-azure-ad-app"></a>Azure AD-alkalmazás regisztrálása

Ha engedélyezni szeretné a bejelentkezést az Azure AD-fiókkal rendelkező felhasználók számára egy adott Azure AD-szervezetből, Azure Active Directory B2C (Azure AD B2C), létre kell hoznia egy alkalmazást a [Azure Portal](https://portal.azure.com). További információ: [alkalmazás regisztrálása a Microsoft Identity platformmal](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app).

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Győződjön meg arról, hogy a szervezeti Azure AD-bérlőt (például contoso.com) tartalmazó könyvtárat használja. Válassza ki a **címtár + előfizetés szűrőt** a felső menüben, majd válassza ki azt a könyvtárat, amely tartalmazza az Azure ad-bérlőt.
1. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Alkalmazásregisztrációk**.
1. Válassza az **új regisztráció** lehetőséget.
1. Adja meg az alkalmazás **nevét** . Például: `Azure AD B2C App`.
1. Fogadja el a fiókok alapértelmezett kiválasztását ebben a **szervezeti könyvtárban csak** ehhez az alkalmazáshoz.
1. Az **átirányítási URI** esetében fogadja el a **web** értékét, és írja be az alábbi URL-címet minden kisbetűvel, ahol a `your-B2C-tenant-name` helyére a Azure ad B2C bérlő neve kerül.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Például: `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`.

1. Válassza a **Regisztráció** lehetőséget. Jegyezze fel az **alkalmazás (ügyfél) azonosítóját** egy későbbi lépésben való használatra.
1. Válassza a **tanúsítványok & titkok** lehetőséget, majd válassza az **új ügyfél titka** lehetőséget.
1. Adja meg a titok **leírását** , válasszon ki egy lejáratot, majd kattintson a **Hozzáadás** gombra. Jegyezze fel a titok **értékét** egy későbbi lépésben való használatra.

### <a name="configuring-optional-claims"></a>Választható jogcímek konfigurálása

Ha a `family_name` és a `given_name` jogcímeket az Azure ad-től szeretné beszerezni, az alkalmazáshoz a Azure Portal felhasználói felületén vagy az alkalmazás-jegyzékfájlban konfigurálhat választható jogcímeket is. További információkért lásd: [opcionális jogcímek megadása az Azure ad-alkalmazáshoz](../active-directory/develop/active-directory-optional-claims.md).

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Keresse meg és válassza ki az **Azure Active Directoryt**.
1. A **kezelés** szakaszban válassza a **Alkalmazásregisztrációk** lehetőséget.
1. Válassza ki azt az alkalmazást, amelyhez választható jogcímeket szeretne konfigurálni a listában.
1. A **kezelés** szakaszban válassza a **jogkivonat-konfiguráció** elemet.
1. Válassza a **választható jogcím hozzáadása** lehetőséget.
1. A **jogkivonat típusa** beállításnál válassza az **azonosító** lehetőséget.
1. Válassza ki a hozzáadandó választható jogcímeket, `family_name` és `given_name` .
1. Kattintson a **Hozzáadás** parancsra.

::: zone pivot="b2c-user-flow"

## <a name="configure-azure-ad-as-an-identity-provider"></a>Az Azure AD konfigurálása identitás-szolgáltatóként

1. Győződjön meg arról, hogy a Azure AD B2C bérlőt tartalmazó könyvtárat használja. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, és válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
1. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Azure ad B2C**.
1. Válassza az **identitás-szolgáltatók**, majd az **új OpenID Connect Provider** lehetőséget.
1. Adjon meg egy **nevet**. Adja meg például a *contoso Azure ad*-t.
1. A **metaadatok URL-címéhez** adja meg az alábbi URL-címet, `{tenant}` amely az Azure ad-bérlő tartománynevét helyettesíti:

    ```
    https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration
    ```

    Például: `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration`.
    Például: `https://login.microsoftonline.com/contoso.com/v2.0/.well-known/openid-configuration`.

1. Az **ügyfél-azonosító** mezőben adja meg a korábban rögzített alkalmazás azonosítóját.
1. Az **ügyfél titkos kulcsa** mezőben adja meg a korábban rögzített ügyfél-titkot.
1. A **hatókörben** adja meg a következőt: `openid profile` .
1. Hagyja meg a **Válasz típusa** és a **válasz mód** alapértelmezett értékeit.
1. Választható A **tartományhoz tartozó tipp** esetében adja meg a következőt: `contoso.com` . További információ: [közvetlen bejelentkezés beállítása Azure Active Directory B2C használatával](direct-signin.md#redirect-sign-in-to-a-social-provider).
1. Az **Identity Provider jogcímek leképezése** területen válassza ki a következő jogcímeket:

    - **Felhasználói azonosító**: *OID*
    - **Megjelenítendő név**: *név*
    - **Utónév**: *given_name*
    - **Vezetéknév**: *family_name*
    - **E-mail**: *preferred_username*

1. Válassza a **Mentés** lehetőséget.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>Házirend-kulcs létrehozása

A Azure AD B2C bérlőben létrehozott alkalmazás-kulcsot kell tárolnia.

1. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja. Válassza ki a **címtár + előfizetés szűrőt** a felső menüben, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
1. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Azure ad B2C**.
1. A **szabályzatok** területen válassza az **identitási élmény keretrendszere** elemet.
1. Válassza a **szabályzat kulcsok** lehetőséget, majd kattintson a **Hozzáadás** gombra.
1. A **Beállítások** területen válassza a lehetőséget `Manual` .
1. Adja meg a szabályzat kulcsának **nevét** . Például: `ContosoAppSecret`.  A rendszer `B2C_1A_` automatikusan hozzáadja az előtagot a kulcs neveként a létrehozáskor, ezért a következő szakaszban található XML-ben szereplő hivatkozás a *B2C_1A_ContosoAppSecret*.
1. A **Secret (titkos kulcs**) mezőben adja meg a korábban feljegyzett ügyfél-titkot.
1. A **kulcshasználat** beállításnál válassza a elemet `Signature` .
1. Válassza a **Létrehozás** lehetőséget.

## <a name="add-a-claims-provider"></a>Jogcím-szolgáltató hozzáadása

Ha azt szeretné, hogy a felhasználók az Azure AD használatával jelentkezzenek be, az Azure AD-t olyan jogcím-szolgáltatóként kell megadnia, amely Azure AD B2C tud kommunikálni egy végponton keresztül. A végpont olyan jogcímeket biztosít, amelyeket a Azure AD B2C használ annak ellenőrzéséhez, hogy egy adott felhasználó hitelesítve van-e.

Az Azure AD-t jogcím-szolgáltatóként is megadhatja, ha hozzáadja az Azure AD-t az **ClaimsProvider** elemhez a szabályzat bővítmény fájljában.

1. Nyissa meg a *TrustFrameworkExtensions.xml* fájlt.
2. Keresse meg a **ClaimsProviders** elemet. Ha nem létezik, adja hozzá a gyökérelem elemhez.
3. Vegyen fel egy új **ClaimsProvider** a következőképpen:
    ```xml
    <ClaimsProvider>
      <Domain>Contoso</Domain>
      <DisplayName>Login using Contoso</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="OIDC-Contoso">
          <DisplayName>Contoso Employee</DisplayName>
          <Description>Login with your Contoso account</Description>
          <Protocol Name="OpenIdConnect"/>
          <Metadata>
            <Item Key="METADATA">https://login.microsoftonline.com/tenant-name.onmicrosoft.com/v2.0/.well-known/openid-configuration</Item>
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid profile</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_ContosoAppSecret"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="oid"/>
            <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. A **ClaimsProvider** elem alatt frissítse a **tartomány** értékét egy olyan egyedi értékre, amelyet a más identitás-szolgáltatóktól való megkülönböztetéshez használhat. Például: `Contoso`. A tartomány végén nem kerül be a érték `.com` .
5. A **ClaimsProvider** elem alatt frissítse a **DisplayName** értékét a jogcím-szolgáltató rövid nevére. Ez az érték jelenleg nincs használatban.

### <a name="update-the-technical-profile"></a>A technikai profil frissítése

Az Azure AD-végponttól kapott jogkivonat lekéréséhez meg kell határoznia azokat a protokollokat, amelyeket az Azure AD B2C az Azure AD-vel való kommunikációhoz használnia kell. Ez a **ClaimsProvider** **kivonatjogcím** elemén belül történik.

1. Frissítse a **kivonatjogcím** elem azonosítóját. Ezzel az AZONOSÍTÓval hivatkozhat erre a műszaki profilra a szabályzat más részeiről, például: `OIDC-Contoso` .
1. A **DisplayName** értékének frissítése. Ez az érték a bejelentkezési képernyőjén a bejelentkezés gombján jelenik meg.
1. A **Leírás** értékének frissítése.
1. Az Azure AD az OpenID Connect protokollt használja, ezért győződjön meg arról, hogy a **protokoll** értéke `OpenIdConnect` .
1. Állítsa be a **metaadatok** értékét a értékre `https://login.microsoftonline.com/tenant-name.onmicrosoft.com/v2.0/.well-known/openid-configuration` , ahol `tenant-name` az az Azure ad-bérlő neve. Például: `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration`
1. **Client_id** beállítása az alkalmazás-azonosítóhoz az alkalmazás regisztrációja során.
1. A **CryptographicKeys** alatt frissítse a **StorageReferenceId** értékét a korábban létrehozott házirend-kulcs nevére. Például: `B2C_1A_ContosoAppSecret`.

### <a name="upload-the-extension-file-for-verification"></a>A bővítmény fájljának feltöltése ellenőrzéshez

Most úgy konfigurálta a házirendet, hogy Azure AD B2C tudja, hogyan kommunikálhat az Azure AD-címtárral. Próbálja megismételni a szabályzat kiterjesztési fájljának feltöltését, hogy megbizonyosodjon róla, hogy eddig nincs probléma.

1. A Azure AD B2C-bérlő **Egyéni házirendek** lapján válassza a **házirend feltöltése** lehetőséget.
1. **Ha létezik, engedélyezze a házirend felülírását**, majd keresse meg és válassza ki a *TrustFrameworkExtensions.xml* fájlt.
1. Kattintson a **Feltöltés** gombra.

## <a name="register-the-claims-provider"></a>A jogcím-szolgáltató regisztrálása

Ezen a ponton az identitás-szolgáltató beállítása megtörtént, de a regisztrációs és bejelentkezési oldalakon még nem érhető el. Ha elérhetővé szeretné tenni, hozzon létre egy másolatot egy meglévő sablon felhasználói utazásról, majd módosítsa úgy, hogy az Azure AD Identity Provider is legyen:

1. Nyissa meg a *TrustFrameworkBase.xml* fájlt az alapszintű csomagból.
1. A **UserJourney** elem teljes tartalmának megkeresése és másolása `Id="SignUpOrSignIn"` .
1. Nyissa meg a *TrustFrameworkExtensions.xmlt* , és keresse meg a **UserJourneys** elemet. Ha az elem nem létezik, vegyen fel egyet.
1. Illessze be a **UserJourney** elem teljes tartalmát, amelyet a **UserJourneys** elem gyermekeiként másolt.
1. Nevezze át a felhasználói út AZONOSÍTÓját. Például: `SignUpSignInContoso`.

### <a name="display-the-button"></a>A gomb megjelenítése

A **ClaimsProviderSelection** elem hasonló a regisztrációs vagy bejelentkezési oldalon található Identity Provider gombhoz. Ha hozzáad egy **ClaimsProviderSelection** elemet az Azure ad-hoz, egy új gomb jelenik meg, amikor a felhasználó az oldalon landol.

1. Keresse meg a **OrchestrationStep** elemet, amely `Order="1"` a *TrustFrameworkExtensions.xmlban* létrehozott felhasználói útra vonatkozik.
1. A **ClaimsProviderSelections** területen adja hozzá a következő elemet. Állítsa a **TargetClaimsExchangeId** értékét egy megfelelő értékre, például `ContosoExchange` :

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Gomb csatolása egy művelethez

Most, hogy van egy gomb a helyén, össze kell kapcsolni egy művelettel. Ebben az esetben a művelet Azure AD B2C az Azure AD-vel való kommunikációhoz a jogkivonat fogadása céljából. Csatolja a gombot egy művelethez az Azure AD-jogcímek szolgáltatójának technikai profiljának összekapcsolásával:

1. Keresse meg a felhasználói útra kiterjedő **OrchestrationStep** `Order="2"` .
1. Adja hozzá a következő **ClaimsExchange** elemet, és győződjön meg arról, hogy ugyanazt az értéket használja a **TargetClaimsExchangeId** használt **azonosítóhoz** :

    ```xml
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="OIDC-Contoso" />
    ```

    Frissítse a **TechnicalProfileReferenceId** értékét a korábban létrehozott műszaki profil **azonosítójával** . Például: `OIDC-Contoso`.

1. Mentse a *TrustFrameworkExtensions.xml* fájlt, és töltse fel újra az ellenőrzéshez.

::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="add-azure-ad-identity-provider-to-a-user-flow"></a>Azure AD Identity Provider hozzáadása felhasználói folyamathoz 

1. A Azure AD B2C-bérlőben válassza a **felhasználói folyamatok** lehetőséget.
1. Kattintson arra a felhasználói folyamatra, amelyhez hozzá szeretné adni az Azure AD-identitás szolgáltatóját.
1. A **közösségi identitás-szolgáltatók** területen válassza a **contoso Azure ad** elemet.
1. Válassza a **Mentés** lehetőséget.
1. A szabályzat teszteléséhez válassza a **felhasználói folyamat futtatása** lehetőséget.
1. Az **alkalmazás** lapon válassza ki a korábban regisztrált *testapp1* nevű webalkalmazást. A **Válasz URL-címének** meg kell jelennie `https://jwt.ms` .
1. Kattintson a **felhasználói folyamat futtatása** elemre.

::: zone-end

::: zone pivot="b2c-custom-policy"


## <a name="update-and-test-the-relying-party-file"></a>A függő entitás fájljának frissítése és tesztelése

Frissítse a függő entitás (RP) fájlját, amely kezdeményezi a létrehozott felhasználói utat.

1. Készítsen másolatot *SignUpOrSignIn.xml* a munkakönyvtárában, és nevezze át. Nevezze át például *SignUpSignInContoso.xmlra*.
1. Nyissa meg az új fájlt, és frissítse a **PolicyId** attribútum értékét a **TrustFrameworkPolicy** egyedi értékkel. Például: `SignUpSignInContoso`.
1. Frissítse a **PublicPolicyUri** értékét a szabályzat URI azonosítójának értékével. Például: `http://contoso.com/B2C_1A_signup_signin_contoso`.
1. Frissítse a **ReferenceId** attribútum értékét a **DefaultUserJourney** -ben, hogy megfeleljen a korábban létrehozott felhasználói út azonosítójának. Például: *SignUpSignInContoso*.
1. Mentse a módosításokat, és töltse fel a fájlt.
1. Az **Egyéni házirendek** területen válassza ki az új szabályzatot a listában.
1. Az **alkalmazás kiválasztása** legördülő menüben válassza ki a korábban létrehozott Azure ad B2C alkalmazást. Például: *testapp1*.
1. Másolja a **Futtatás most végpontot** , és nyissa meg egy privát böngészőablakban, például: Incognito mód a Google Chrome-ban vagy egy InPrivate-ablak a Microsoft Edge-ben. A privát böngészőablak megnyitása lehetővé teszi a teljes felhasználói út tesztelését, ha nem használja a jelenleg gyorsítótárazott Azure AD-beli hitelesítő adatokat.
1. Válassza ki az Azure AD-bejelentkezés gombot, például a *contoso Employee* lehetőséget, majd adja meg az Azure ad szervezeti bérlő felhasználójának hitelesítő adatait. A rendszer megkéri, hogy engedélyezze az alkalmazást, majd adja meg a profil adatait.

Ha a bejelentkezési folyamat sikeres, a böngésző átirányítja a `https://jwt.ms` -ra, amely megjeleníti a Azure ad B2C által visszaadott jogkivonat tartalmát.

## <a name="next-steps"></a>További lépések

Egyéni szabályzatok használatakor előfordulhat, hogy további információra van szüksége, amikor a fejlesztés során hibaelhárítást végez a házirendben.

A problémák diagnosztizálásához ideiglenesen a "fejlesztői módba" helyezheti a szabályzatot, és összegyűjtheti a naplókat az Azure Application Insights használatával. Ismerje meg, hogyan történik a [Azure Active Directory B2C: naplók gyűjtése](troubleshoot-with-application-insights.md).

::: zone-end