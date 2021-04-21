---
title: Oktatóanyag a BioCatch konfigurálás Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: 'Oktatóanyag: Azure Active Directory B2C BioCatch segítségével a kockázatos és rosszindulatú felhasználók azonosításához'
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/20/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: f15cc294a0d3d930548d7d9bdf1d05b552b60fa5
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107819386"
---
# <a name="tutorial-configure-biocatch-with-azure-active-directory-b2c"></a>Oktatóanyag: A BioCatch konfigurálása Azure Active Directory B2C

Ebből a minta oktatóanyagból megtudhatja, hogyan integrálhatja az Azure Active Directory (AD) B2C-hitelesítést a [BioCatchtal](https://www.biocatch.com/) az ügyfélidentitás- és hozzáférés-kezelési (TUDM) biztonsági rendszer további bővítéséhez. A BioCatch elemzi a felhasználók fizikai és kognitív digitális viselkedését, hogy olyan elemzéseket hozzon létre, amelyek megkülönböztetik a megbízható ügyfeleket és a kiberbűnözéseket.

## <a name="prerequisites"></a>Előfeltételek

A első lépésekhez a következőre lesz szüksége:

- Azure-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes fiókot [is kaphat.](https://azure.microsoft.com/free/)

- [Egy Azure AD B2C,](tutorial-create-tenant.md) amely az Azure-előfizetéséhez van kapcsolva.

- Egy [BioCatch-fiók.](https://www.biocatch.com/contact-us)

## <a name="scenario-description"></a>Forgatókönyv leírása

A BioCatch integrációja a következő összetevőket tartalmazza:

- **Webalkalmazás vagy webszolgáltatás** – A felhasználó először erre a webszolgáltatásra keres rá. Ez a webszolgáltatás példányosít egy egyedi ügyfél-munkamenet-azonosítót, amely a BioCatch számára van elküldve. Az ügyfél munkamenet-azonosítója ezután azonnal megkezdi a felhasználói viselkedés jellemzőinek továbbítását a BioCatch számára.

- **Egy metódus –** Elküldi az egyedi ügyfél-munkamenet-azonosítót a Azure AD B2C. A megadott példában JavaScript használatával adhatja meg az értéket egy rejtett HTML-mezőben.

- **Egyéni Azure AD B2C** felhasználói felület – Elrejt egy HTML-mezőt a JavaScript ügyfél-munkamenet-azonosítójának bemenetéhez, ha a fenti metódust használja

- **Azure AD B2C szabályzat létrehozása**

  - Jogcím formájában veszi át az egyéni ügyfél-munkamenet azonosítóját a felhasználói felületről. Ez egy önkiszolgáló technikai profillal érhető el

  - Integrálható a BioCatchkel egy REST API jogcímszolgáltatón keresztül, és átadja az ügyfél munkamenet-azonosítóját a BioCatch platformnak

  - A BioCatch több egyéni jogcímet ad vissza, hogy az egyéni szabályzat logikája ezután a megfelelőt

  - Egy userjourney, amely kiértékeli a visszaadott jogcímet, például a munkamenet kockázatát, és feltételesen végrehajt egy műveletet, például meghívja a többtényezős hitelesítést (MFA).

![A bio catch architektúra ábrája.](media/partner-biocatch/biocatch-architecture-diagram.png)

| Lépés  | Description |
|:---|:-----------------------|
|1a     | A felhasználó böngészi a webszolgáltatást. A webszolgáltatás ezután HTML-, CSS- vagy JavaScript-értékeket ad vissza, és konfigurálja a BioCatch JavaScript SDK betöltését. Az ügyféloldali JavaScript konfigurálja/beállítja a BioCatch SDK ügyfél-munkamenet-azonosítóját. A webszolgáltatás előre konfigurálhatja az ügyfél-munkamenet azonosítóját, és elküldheti az ügyfélnek.        |
|1b    |  Konfigurálja a példányos BioCatch JavaScript SDK-t a BioCatch platformhoz. Azonnal megkezdheti a felhasználói viselkedés jellemzőinek elküldését a BioCatchnak az ügyféleszközről az 1a lépés ügyfél-munkamenet-azonosítójának használatával.    |
|2     |  A felhasználó megpróbál bejelentkezni/bejelentkezni, és a rendszer átirányítja a Azure AD B2C.      |
|3a    | A userjourney része egy önkiszolgáló jogcímprovider, amely bemenetként az ügyfél-munkamenet azonosítóját veszi fel. Ez a mező el van rejtve a képernyőn. A JavaScripttel a munkamenet-azonosítót használhatja a mezőben. Kattintson *a tovább gombra* a regisztráció/bejelentkezés folytatásához.|
|3b     | A rendszer elküldi az ügyfél-munkamenet azonosítóját a BioCatch platformra a kockázati pontszám meghatározásához. |
|3c     | A BioCatch információkat ad vissza a munkamenetről, például a kockázati pontszámot, és javaslatot tesz arra, hogy mit kell tenni – engedélyezni vagy letiltani |
|3d    |A userjourney feltételes ellenőrző lépéssel rendelkezik, amely a visszaadott jogcímek alapján működik|
| 4   | A feltételes ellenőrzés eredménye alapján a rendszer egy olyan műveletet hív meg, mint például az *MFA* lépése|
|5    | Amikor a felhasználó először eléri a webszolgáltatás oldalát, a webszolgáltatás az ügyfél-munkamenet azonosítójával lekérdezheti a BioCatch API-t a kockázati pontszám és a munkamenet-információk valós idejű meghatározásához. |

## <a name="onboard-with-biocatch"></a>A BioCatch onboard (A BioCatch- és a biocatch-

Lépjen kapcsolatba a [BioCatch-rel,](https://www.biocatch.com/contact-us) és hozzon létre egy fiókot.

## <a name="configure-the-custom-ui"></a>Az egyéni felhasználói felület konfigurálása

Javasoljuk, hogy rejtse el az ügyfél-munkamenet azonosítója mezőt. A mező elrejtését CSS, JavaScript vagy bármely más módszer használatával használhatja. Tesztelési célból felfedheti a mezőt. A JavaScript például a következő ként elrejti a bemeneti mezőt:

```
document.getElementById("clientSessionId").style.display = 'none';
```

## <a name="configure--azure-ad-b2c-identity-experience-framework-policies"></a>A Azure AD B2C Identity Experience Framework konfigurálása

1. Konfigurálja a kezdeti [egyéni szabályzatkonfigurációt.](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started)

2. Hozzon létre egy új fájlt, amely a bővítményfájlból örököl.

    ```
    <BasePolicy> 

        <TenantId>tenant.onmicrosoft.com</TenantId> 

        <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId> 

      </BasePolicy> 
    ```

3. Hozzon létre egy hivatkozást az egyéni felhasználói felületre a beviteli mező elrejtéséhez a BuildingBlocks erőforrás alatt.

    ```
    <ContentDefinitions> 

        <ContentDefinition Id="api.selfasserted"> 

            <LoadUri>https://domain.com/path/to/selfAsserted.cshtml</LoadUri> 

            <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.0</DataUri> 

          </ContentDefinition> 

        </ContentDefinitions>
    ```

4. Adja hozzá a következő jogcímeket a BuildingBlocks erőforráshoz.

    ```
    <ClaimsSchema> 

          <ClaimType Id="riskLevel"> 

            <DisplayName>Session risk level</DisplayName> 

            <DataType>string</DataType>       

          </ClaimType> 

          <ClaimType Id="score"> 

            <DisplayName>Session risk score</DisplayName> 

            <DataType>int</DataType>       

          </ClaimType> 

          <ClaimType Id="clientSessionId"> 

            <DisplayName>The ID of the client session</DisplayName> 

            <DataType>string</DataType> 

            <UserInputType>TextBox</UserInputType> 

          </ClaimType> 

    <ClaimsSchema> 
    ```

5. Konfigurálja az önkiszolgáló jogcímszolgáltatót az ügyfél-munkamenet azonosítója mezőhöz.

    ```
    <ClaimsProvider> 

          <DisplayName>Client Session ID Claims Provider</DisplayName> 

          <TechnicalProfiles> 

            <TechnicalProfile Id="login-NonInteractive-clientSessionId"> 

              <DisplayName>Client Session ID TP</DisplayName> 

              <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" /> 

              <Metadata> 

                <Item Key="ContentDefinitionReferenceId">api.selfasserted</Item> 

              </Metadata> 

              <CryptographicKeys> 

                <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" /> 

              </CryptographicKeys> 

            <!—Claim we created earlier --> 

              <OutputClaims> 

                <OutputClaim ClaimTypeReferenceId="clientSessionId" Required="false" DefaultValue="100"/> 

              </OutputClaims> 

            <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" /> 

            </TechnicalProfile> 

          </TechnicalProfiles> 

        </ClaimsProvider> 
    ```

6. Konfigurálja REST API BioCatch-hez a jogcímszolgáltatót. 

    ```
    <TechnicalProfile Id="BioCatch-API-GETSCORE"> 

          <DisplayName>Technical profile for BioCatch API to return session information</DisplayName> 

          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" /> 

          <Metadata> 

            <Item Key="ServiceUrl">https://biocatch-url.com/api/v6/score?customerID=<customerid>&amp;action=getScore&amp;uuid=<uuid>&amp;customerSessionID={clientSessionId}&amp;solution=ATO&amp;activtyType=<activity_type>&amp;brand=<brand></Item> 

            <Item Key="SendClaimsIn">Url</Item> 

            <Item Key="IncludeClaimResolvingInClaimsHandling">true</Item> 

            <!-- Set AuthenticationType to Basic or ClientCertificate in production environments --> 

            <Item Key="AuthenticationType">None</Item> 

            <!-- REMOVE the following line in production environments --> 

            <Item Key="AllowInsecureAuthInProduction">true</Item> 

          </Metadata> 

          <InputClaims> 

            <InputClaim ClaimTypeReferenceId="clientsessionId" /> 

          </InputClaims> 

          <OutputClaims> 

            <OutputClaim ClaimTypeReferenceId="riskLevel" /> 

            <OutputClaim ClaimTypeReferenceId="score" /> 

          </OutputClaims> 

          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" /> 

        </TechnicalProfile> 

      </TechnicalProfiles>
    ```

    > [!Note]
    > A BioCatch meg fogja adni a konfigurálni kívánt URL-címet, ügyfél-azonosítót és egyedi felhasználói azonosítót (uuID). Az ügyfél SessionID jogcíme lekérdezési karakterlánc paraméterként lesz átadva a BioCatch számára. Kiválaszthatja a tevékenység típusát, például a *MAKE_PAYMENT.*

7. Konfigurálja a userjourney felhasználót; kövesse a példát

   1. A clientSessionID lekért jogcíme

   1. A munkamenet-információk lehívása a BioCatch API-val

   1. Ha a visszaadott *jogcímkockázat* értéke *alacsony,* hagyja ki az MFA lépését, különben kényszerítenie kell a felhasználói MFA-t

    ```
    <OrchestrationStep Order="8" Type="ClaimsExchange"> 

              <ClaimsExchanges> 

                <ClaimsExchange Id="clientSessionIdInput" TechnicalProfileReferenceId="login-NonInteractive-clientSessionId" /> 

              </ClaimsExchanges> 

            </OrchestrationStep> 

            <OrchestrationStep Order="9" Type="ClaimsExchange"> 

              <ClaimsExchanges> 

                <ClaimsExchange Id="BcGetScore" TechnicalProfileReferenceId=" BioCatch-API-GETSCORE" /> 

              </ClaimsExchanges> 

            </OrchestrationStep> 

            <OrchestrationStep Order="10" Type="ClaimsExchange"> 

              <Preconditions> 

                <Precondition Type="ClaimEquals" ExecuteActionsIf="true"> 

                  <Value>riskLevel</Value> 

                  <Value>LOW</Value> 

                  <Action>SkipThisOrchestrationStep</Action> 

                </Precondition> 

              </Preconditions> 

              <ClaimsExchanges> 

                <ClaimsExchange Id="PhoneFactor-Verify" TechnicalProfileReferenceId="PhoneFactor-InputOrVerify" /> 

              </ClaimsExchanges>  

    ```

8. Konfigurálás függő fél konfigurációja alapján (nem kötelező)

    Hasznos, ha a BioCatch által visszaadott adatokat jogcímként adja át az alkalmazásnak a jogkivonatban, pontosabban a kockázati *szint és* a *pontszám értékeként.*

    ```
    <RelyingParty> 

        <DefaultUserJourney ReferenceId="SignUpOrSignInMfa" /> 

        <UserJourneyBehaviors> 

          <SingleSignOn Scope="Tenant" KeepAliveInDays="30" /> 

          <SessionExpiryType>Absolute</SessionExpiryType> 

          <SessionExpiryInSeconds>1200</SessionExpiryInSeconds> 

          <ScriptExecution>Allow</ScriptExecution> 

        </UserJourneyBehaviors> 

        <TechnicalProfile Id="PolicyProfile"> 

          <DisplayName>PolicyProfile</DisplayName> 

          <Protocol Name="OpenIdConnect" /> 

          <OutputClaims> 

            <OutputClaim ClaimTypeReferenceId="displayName" /> 

            <OutputClaim ClaimTypeReferenceId="givenName" /> 

            <OutputClaim ClaimTypeReferenceId="surname" /> 

            <OutputClaim ClaimTypeReferenceId="email" /> 

            <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" /> 

            <OutputClaim ClaimTypeReferenceId="identityProvider" />                 

            <OutputClaim ClaimTypeReferenceId="riskLevel" /> 

            <OutputClaim ClaimTypeReferenceId="score" /> 

            <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" /> 

          </OutputClaims> 

          <SubjectNamingInfo ClaimType="sub" /> 

        </TechnicalProfile> 

      </RelyingParty> 

    ```

## <a name="integrate-with-azure-ad-b2c"></a>Integrálás az Azure AD B2C szolgáltatással

Az alábbi lépéseket követve adja hozzá a szabályzatfájlokat a Azure AD B2C

1. Jelentkezzen be a  [**Azure Portal**](https://portal.azure.com/) globális rendszergazdájaként a Azure AD B2C bérlőjébe.

2. Győződjön meg arról, hogy azt a címtárat használja, amely a Azure AD B2C található. A **felső menüben** válassza a Címtár és előfizetés szűrő lehetőséget, majd válassza ki a   bérlőt tartalmazó címtárat.

3. A **bal felső sarokban** válassza a Minden szolgáltatás Azure Portal, majd válassza a   Azure AD B2C.

4. Lépjen a **Azure AD B2C**   >  **Identity Experience Framework**

3. Töltse fel az összes szabályzatfájlt a bérlőjébe.

## <a name="test-the-solution"></a>A megoldás tesztelése

1. [Regisztrál egy próbaalkalmazást, amely átirányítja a JWT.MS](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-register-applications?tabs=app-reg-ga)  

2. A **Identity Experience Framework** alatt válassza ki a létrehozott szabályzatot

3. A szabályzat ablakában válassza ki a JWT.MS, majd válassza a **Futtatás most lehetőséget**

4. A regisztráció folyamatának végigmenve hozzon létre egy fiókot. A biztonsági JWT.MS a riskLevel és a pontszám 2-edikének kell lennie. Kövesse a példát.  

    ```
    { 

      "typ": "JWT", 

      "alg": "RS256", 

      "kid": "_keyid" 

    }.{ 

      "exp": 1615872580, 

      "nbf": 1615868980, 

      "ver": "1.0", 

      "iss": "https://tenant.b2clogin.com/12345678-1234-1234-1234-123456789012/v2.0/", 

      "sub": "12345678-1234-1234-1234-123456789012", 

      "aud": "12345678-1234-1234-1234-123456789012", 

      "acr": "b2c_1a_signup_signin_biocatch_policy", 

      "nonce": "defaultNonce", 

      "iat": 1615868980, 

      "auth_time": 1615868980, 

      "name": "John Smith", 

      "email": "john.smith@contoso.com", 

      "given_name": "John", 

      "family_name": "Smith", 

      "riskLevel": "LOW", 

      "score": 275, 

      "tid": "12345678-1234-1234-1234-123456789012" 

    }.[Signature]  

    ```

## <a name="additional-resources"></a>További források

- [Egyéni szabályzatok az Azure AD B2C-ben](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Az egyéni szabályzatok első lépések a Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)
