---
title: Regisztráció és bejelentkezés beállítása SAML-identitás-szolgáltatóval
titleSuffix: Azure Active Directory B2C
description: A regisztráció és a bejelentkezés beállítása bármely SAML-identitásszolgáltató (Azure Active Directory B2C).
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/08/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 357ea903ed4bbc87717dfefc1c542722f5bd40c0
ms.sourcegitcommit: f6193c2c6ce3b4db379c3f474fdbb40c6585553b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2021
ms.locfileid: "102448404"
---
# <a name="set-up-sign-up-and-sign-in-with-saml-identity-provider-using-azure-active-directory-b2c"></a>Az SAML-identitás-szolgáltatóval való regisztráció és bejelentkezés beállítása Azure Active Directory B2C használatával

A Azure Active Directory B2C (Azure AD B2C) támogatja az SAML 2,0 identitás-szolgáltatókkal való összevonást. Ez a cikk bemutatja, hogyan engedélyezheti a bejelentkezést SAML-identitású felhasználói fiókkal, így a felhasználók bejelentkezhetnek meglévő közösségi vagy vállalati identitásokkal, például az [ADFS](identity-provider-adfs2016-custom.md) -vel és a [Salesforce](identity-provider-salesforce-saml.md).

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="scenario-overview"></a>A forgatókönyv áttekintése

A Azure AD B2C konfigurálható úgy, hogy a felhasználók a külső közösségi vagy vállalati SAML-szolgáltatók (identitásszolgáltató-EK) hitelesítő adataival jelentkezzenek be az alkalmazásba. Ha egy SAML-összevonja Azure AD B2C, akkor **a szolgáltató SAML-kérelmet indít a** SAML- **identitás szolgáltatójának**, és egy SAML-válaszra vár. A következő ábrán:

1. Az alkalmazás egy engedélyezési kérelmet kezdeményez a Azure AD B2C. Az alkalmazás lehet egy [OAuth 2,0](protocols-overview.md) vagy [OpenId Connect](openid-connect.md) alkalmazás, vagy egy [SAML](saml-service-provider.md)-szolgáltató. 
1. A Azure AD B2C bejelentkezési oldalán a felhasználó egy SAML-identitás-szolgáltatói fiókkal (például *contoso*) való bejelentkezést választja. A Azure AD B2C SAML-engedélyezési kérelmet indít el, és a bejelentkezés befejezéséhez a felhasználónak a SAML-identitás szolgáltatóját veszi át.
1. Az SAML-identitás szolgáltatója egy SAML-választ ad vissza.
1. Azure AD B2C érvényesíti az SAML-jogkivonatot, Kinyeri a jogcímeket, kiadja a saját tokenjét, és visszaviszi a felhasználót az alkalmazásba.  

![Bejelentkezés SAML-identitás-szolgáltatói folyamattal](./media/identity-provider-generic-saml/sign-in-with-saml-identity-provider-flow.png)

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="components-of-the-solution"></a>A megoldás összetevői

Ehhez a forgatókönyvhöz a következő összetevők szükségesek:

* SAML- **identitás szolgáltatója** , amely a Azure ad B2Ctól származó SAML-kérelmek fogadását, dekódolását és válaszadását teszi lehetővé.
* Nyilvánosan elérhető SAML- **metaadatok végpontja** az identitás-szolgáltató számára.
* [Azure ad B2C bérlő](tutorial-create-tenant.md).
 

## <a name="create-a-policy-key"></a>Házirend-kulcs létrehozása

A Azure AD B2C és az SAML-identitás szolgáltatója közötti megbízhatósági kapcsolat létrehozásához meg kell adnia egy érvényes X509-tanúsítványt a titkos kulccsal. Azure AD B2C aláírja az SAML-kérelmeket a tanúsítvány titkos kulcsa alapján. Az identitás-szolgáltató a tanúsítvány nyilvános kulcsával érvényesíti a kérelmet. A nyilvános kulcs a technikai profil metaadatain keresztül érhető el. Azt is megteheti, hogy manuálisan feltölti a. cer fájlt az SAML-identitás szolgáltatójának.

Az önaláírt tanúsítványok a legtöbb esetben elfogadhatók. Éles környezetekben ajánlott a hitelesítésszolgáltató által kiadott X509-tanúsítványt használni. A dokumentum későbbi részében leírtaknak megfelelően a nem éles környezetekben mindkét oldalon le lehet tiltani az SAML-aláírást.

### <a name="obtain-a-certificate"></a>Tanúsítvány beszerzése

[!INCLUDE [active-directory-b2c-create-self-signed-certificate](../../includes/active-directory-b2c-create-self-signed-certificate.md)]

### <a name="upload-the-certificate"></a>A tanúsítvány feltöltése

A tanúsítványt a Azure AD B2C bérlőben kell tárolnia.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, és válassza ki a bérlőt tartalmazó könyvtárat.
1. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Azure ad B2C**.
1. Az Áttekintés lapon válassza az **identitási élmény keretrendszert**.
1. Válassza a **szabályzat kulcsok** lehetőséget, majd kattintson a **Hozzáadás** gombra.
1. A **Beállítások** területen válassza a lehetőséget `Upload` .
1. Adja meg a szabályzat kulcsának **nevét** . Például: `SAMLSigningCert`. A rendszer automatikusan hozzáadja az előtagot a `B2C_1A_` kulcs nevéhez.
1. Tallózással keresse meg és válassza ki a tanúsítvány. pfx fájlját a titkos kulccsal.
1. Kattintson a **Létrehozás** lehetőségre.

## <a name="configure-the-saml-technical-profile"></a>Az SAML technikai profil konfigurálása

Adja meg a SAML-identitás szolgáltatóját úgy, hogy hozzáadja a **ClaimsProviders** elemhez a szabályzat bővítmény fájljában. A jogcím-szolgáltatók SAML-technikai profilt tartalmaznak, amely meghatározza, hogy mely végpontok és protokollok szükségesek az SAML-identitás-szolgáltatóval való kommunikációhoz. Az SAML technikai profillal rendelkező jogcím-szolgáltató hozzáadása:

1. Nyissa meg a *TrustFrameworkExtensions.xml*.
1. Keresse meg a **ClaimsProviders** elemet. Ha nem létezik, adja hozzá a gyökérelem elemhez.
1. Vegyen fel egy új **ClaimsProvider** a következőképpen:

    ```xml
    <ClaimsProvider>
      <Domain>Contoso.com</Domain>
      <DisplayName>Contoso</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Contoso-SAML2">
          <DisplayName>Contoso</DisplayName>
          <Description>Login with your SAML identity provider account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="PartnerEntity">https://your-AD-FS-domain/federationmetadata/2007-06/federationmetadata.xml</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="assertionSubjectName" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="first_name" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="last_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="http://schemas.microsoft.com/identity/claims/displayname" />
            <OutputClaim ClaimTypeReferenceId="email"  />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-idp"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

Frissítse a következő XML-elemeket a megfelelő értékkel:

|XML-elem  |Érték  |
|---------|---------|
|ClaimsProvider\Domain  | A [közvetlen bejelentkezéshez](direct-signin.md?pivots=b2c-custom-policy#redirect-sign-in-to-a-social-provider)használt tartománynév. Adja meg a közvetlen bejelentkezéshez használni kívánt tartománynevet. Például: *contoso.com*. |
|TechnicalProfile\DisplayName|Ez az érték a bejelentkezési képernyőjén a bejelentkezés gombján jelenik meg. Például: *contoso*. |
|Metadata\PartnerEntity|Az SAML-identitás szolgáltatójának metaadatait tartalmazó URL-cím. Vagy átmásolhatja az identitás-szolgáltató metaadatait, és hozzáadhatja azt a CDATA elemen belül `<![CDATA[Your IDP metadata]]>` .|

## <a name="map-the-claims"></a>A jogcímek leképezése

A **OutputClaims** elem tartalmazza a SAML-identitás szolgáltatója által visszaadott jogcímek listáját. Rendelje hozzá a szabályzatban definiált jogcím nevét az identitás-szolgáltatóban definiált érvényesítési névhez. A jogcímek listájának megadásához keresse meg az identitás-szolgáltatót. További információ: [jogcímek leképezése](identity-provider-generic-saml-options.md#claims-mapping).

A fenti példában a *contoso-egy saml2* tartalmazza az SAML-identitás szolgáltatója által visszaadott jogcímeket:

* A **issuerUserId** jogcím a **assertionSubjectName** jogcímhez van rendelve.
* Az **first_name** jogcím a **givenName** jogcímhez van rendelve.
* A **last_name** jogcím a **vezetéknév** jogcímere van leképezve.
* A **DisplayName** jogcím le van képezve a `http://schemas.microsoft.com/identity/claims/displayname` jogcímre.
* A név leképezése nélküli **e-mail-** jogcím.

A technikai profil az Identitáskezelő által nem visszaadott jogcímeket is visszaadja:

* Az **identityProvider** -jogcím, amely tartalmazza az identitás-szolgáltató nevét.
* A **authenticationSource** jogcím alapértelmezett **socialIdpAuthentication**-értékkel rendelkezik.
 
### <a name="add-the-saml-session-technical-profile"></a>Az SAML-munkamenet technikai profiljának hozzáadása

Ha még nem rendelkezik a `SM-Saml-idp` SAML-munkamenet technikai profiljával, vegyen fel egyet a bővítmény házirendjébe. Keresse meg a `<ClaimsProviders>` szakaszt, és adja hozzá a következő XML-kódrészletet. Ha a házirend már tartalmazza a `SM-Saml-idp` technikai profilt, ugorjon a következő lépésre. További információ: [egyszeri bejelentkezéses munkamenet-kezelés](custom-policy-reference-sso.md).

```xml
<ClaimsProvider>
  <DisplayName>Session Management</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SM-Saml-idp">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="IncludeSessionIndex">false</Item>
        <Item Key="RegisterServiceProviders">false</Item>
      </Metadata>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-create-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="configure-your-saml-identity-provider"></a>Az SAML-identitás szolgáltatójának konfigurálása

Miután konfigurálta a házirendet, konfigurálnia kell az SAML-identitás szolgáltatóját a Azure AD B2C SAML-metaadatokkal. Az SAML-metaadatok az SAML-protokollban a szabályzat, a szolgáltató konfigurációjának elérhetővé tétele érdekében használt **információk.** Meghatározza a szolgáltatások helyét, például a bejelentkezést és a kijelentkezést, a tanúsítványokat, a bejelentkezési metódusokat és egyebeket.

Mindegyik SAML-identitás szolgáltatója különböző lépésekkel rendelkezik a szolgáltató beállításához. Egyes SAML-azonosítók a Azure AD B2C metaadatokat kérik, míg másoknak manuálisan kell megadnia a metaadat-fájlt, és meg kell adnia az adatokat. Útmutatásért tekintse meg az Identitáskezelő dokumentációját.

Az alábbi példa egy Azure AD B2C technikai profil SAML-metaadatainak URL-címét jeleníti meg:

```
https://<your-tenant-name>.b2clogin.com/<your-tenant-name>.onmicrosoft.com/<your-policy>/samlp/metadata?idptp=<your-technical-profile>
```

Cserélje le a következő értékeket:

- **a** bérlő neve, például Your-Tenant.onmicrosoft.com.
- **az Ön** házirendje a szabályzat nevével. Például B2C_1A_signup_signin_adfs.
- az **Ön műszaki profilja** a SAML-identitás szolgáltatójának műszaki profiljának nevével. Például: contoso-egy SAML2.

Nyisson meg egy böngészőt, és navigáljon az URL-címre. Győződjön meg arról, hogy a helyes URL-címet adja meg, és hogy van-e hozzáférése az XML-metaadat fájlhoz.

## <a name="test-your-custom-policy"></a>Egyéni szabályzat tesztelése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza ki a **címtár + előfizetés** ikont a portál eszköztárán, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
1. A Azure Portal keresse meg és válassza a **Azure ad B2C** lehetőséget.
1. A **szabályzatok** területen válassza az **identitási élmény keretrendszere** elemet.
1. Válassza ki a függő entitás házirendjét, például: `B2C_1A_signup_signin` .
1. **Alkalmazás** esetén válasszon ki egy [korábban regisztrált](troubleshoot-custom-policies.md#troubleshoot-the-runtime)webalkalmazást. A **Válasz URL-címének** meg kell jelennie `https://jwt.ms` .
1. Kattintson a **Futtatás most** gombra.
1. A regisztrációs vagy bejelentkezési oldalon válassza a **contoso** lehetőséget a contoso-fiókkal való bejelentkezéshez.

Ha a bejelentkezési folyamat sikeres, a rendszer átirányítja a böngészőt `https://jwt.ms` , amely a Azure ad B2C által visszaadott jogkivonat tartalmát jeleníti meg.

## <a name="next-steps"></a>Következő lépések

- [SAML-identitás-szolgáltatói beállítások konfigurálása Azure Active Directory B2C](identity-provider-generic-saml-options.md)

::: zone-end
