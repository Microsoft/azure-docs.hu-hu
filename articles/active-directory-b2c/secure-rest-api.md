---
title: Biztonságos Rest-szolgáltatás a Azure AD B2C
titleSuffix: Azure AD B2C
description: Az egyéni REST API jogcímek cseréjének biztonságossá tétele a Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: f6907db7f6e53247a8f2fc0042e8c8e6b081dbd3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97516374"
---
# <a name="secure-your-restful-services"></a>A REST-szolgáltatások biztonságossá tétele 

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

REST API Azure AD B2C felhasználói úton való integrálásakor az REST API-végpontot hitelesítéssel kell védetté tenni. Ez biztosítja, hogy csak a megfelelő hitelesítő adatokkal rendelkező szolgáltatások (például a Azure AD B2C) hívásokat hajtson végre a REST API-végponton.

Megtudhatja, hogyan integrálhat egy REST APIt a Azure AD B2C felhasználói úton a [felhasználói bevitel ellenőrzése](custom-policy-rest-api-claims-validation.md) és [REST API a jogcímek egyéni házirendekbe való hozzáadásával](custom-policy-rest-api-claims-exchange.md) .

Ebből a cikkből megtudhatja, hogyan védheti a REST APIt a HTTP Basic, az ügyféltanúsítvány vagy a OAuth2 hitelesítés használatával. 

## <a name="prerequisites"></a>Előfeltételek

Hajtsa végre a következő útmutató lépéseinek egyikét:

- [Integrálja REST API jogcímek cseréjét a Azure ad B2C felhasználói úton a felhasználói bevitel érvényesítéséhez](custom-policy-rest-api-claims-validation.md).
- [Jogcímek REST API hozzáadása egyéni házirendekhez](custom-policy-rest-api-claims-exchange.md)

## <a name="http-basic-authentication"></a>Egyszerű HTTP-hitelesítés

Az egyszerű HTTP-hitelesítés az [RFC 2617](https://tools.ietf.org/html/rfc2617)-ben van meghatározva. Az alapszintű hitelesítés a következőképpen működik: Azure AD B2C egy HTTP-kérelmet küld az ügyfél hitelesítő adataival az engedélyezési fejlécben. A hitelesítő adatokat a rendszer Base64 kódolású karakterláncként (név: jelszó) formázza.  

### <a name="add-rest-api-username-and-password-policy-keys"></a>REST API Felhasználónév és jelszó házirend kulcsainak hozzáadása

Ha egy REST API műszaki profilt HTTP alapszintű hitelesítéssel szeretne konfigurálni, hozza létre a következő titkosítási kulcsokat a Felhasználónév és a jelszó tárolásához:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, és válassza ki a Azure ad B2C könyvtárat.
1. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Azure ad B2C**.
1. Az Áttekintés lapon válassza az **identitási élmény keretrendszert**.
1. Válassza a **szabályzat kulcsok** lehetőséget, majd kattintson a **Hozzáadás** gombra.
1. A **Beállítások** lapon válassza a **manuális** lehetőséget.
1. A **név** mezőbe írja be a következőt: **RestApiUsername**.
    Lehet, hogy az előtag *B2C_1A_* automatikusan hozzá lesz adva.
1. A **titok** mezőbe írja be a REST API felhasználónevet.
1. A **kulcshasználat** beállításnál válassza a **titkosítás** lehetőséget.
1. Válassza a **Létrehozás** lehetőséget.
1. Válassza újra a **házirend-kulcsok** elemet.
1. Válassza a **Hozzáadás** lehetőséget.
1. A **Beállítások** lapon válassza a **manuális** lehetőséget.
1. A **név** mezőbe írja be a következőt: **RestApiPassword**.
    Lehet, hogy az előtag *B2C_1A_* automatikusan hozzá lesz adva.
1. A **titok** mezőbe írja be a REST API jelszót.
1. A **kulcshasználat** beállításnál válassza a **titkosítás** lehetőséget.
1. Válassza a **Létrehozás** lehetőséget.

### <a name="configure-your-rest-api-technical-profile-to-use-http-basic-authentication"></a>A REST API műszaki profil konfigurálása HTTP alapszintű hitelesítés használatára

A szükséges kulcsok létrehozása után konfigurálja a REST API technikai profil metaadatait a hitelesítő adatokra való hivatkozáshoz.

1. A munkakönyvtárában nyissa meg a kiterjesztési házirend fájlját (TrustFrameworkExtensions.xml).
1. Keresse meg a REST API technikai profilt. Például: `REST-ValidateProfile` vagy `REST-GetProfile` .
1. Keresse meg az `<Metadata>` elemet.
1. Módosítsa a *AuthenticationType* a következőre: `Basic` .
1. Módosítsa a *AllowInsecureAuthInProduction* a következőre: `false` .
1. Közvetlenül a záró `</Metadata>` elem után adja hozzá a következő XML-kódrészletet:
    ```xml
    <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_RestApiUsername" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_RestApiPassword" />
    </CryptographicKeys>
    ```

Az alábbi példa egy olyan REST-alapú műszaki profilt mutat be, amely a HTTP alapszintű hitelesítéssel van konfigurálva:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">Basic</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_RestApiUsername" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_RestApiPassword" />
      </CryptographicKeys>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="https-client-certificate-authentication"></a>HTTPS-ügyféltanúsítvány hitelesítése

Az ügyféltanúsítvány-alapú hitelesítés kölcsönös tanúsítványalapú hitelesítés, amelyben az ügyfél (Azure AD B2C) biztosítja az ügyféltanúsítványt a kiszolgálónak az identitásának igazolásához. Ez az SSL-kézfogás részeként történik. A REST API szolgáltatáshoz csak olyan szolgáltatások férhetnek hozzá, amelyek megfelelő tanúsítvánnyal rendelkeznek (például Azure AD B2C). Az ügyféltanúsítvány egy X. 509 digitális tanúsítvány. Éles környezetekben a tanúsítványt egy hitelesítésszolgáltatónak kell aláírnia.

### <a name="prepare-a-self-signed-certificate-optional"></a>Önaláírt tanúsítvány előkészítése (nem kötelező)

Nem éles környezetekben, ha még nem rendelkezik tanúsítvánnyal, önaláírt tanúsítványt is használhat. Windows rendszeren a PowerShell [New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) parancsmag segítségével hozhatja elő a tanúsítványt.

1. Futtassa ezt a PowerShell-parancsot egy önaláírt tanúsítvány létrehozásához. Módosítsa az `-Subject` argumentumot megfelelően az alkalmazáshoz, és Azure ad B2C a bérlő nevét. A dátumot úgy is beállíthatja `-NotAfter` , hogy a tanúsítványhoz eltérő lejáratot határozzon meg.
    ```powershell
    New-SelfSignedCertificate `
        -KeyExportPolicy Exportable `
        -Subject "CN=yourappname.yourtenant.onmicrosoft.com" `
        -KeyAlgorithm RSA `
        -KeyLength 2048 `
        -KeyUsage DigitalSignature `
        -NotAfter (Get-Date).AddMonths(12) `
        -CertStoreLocation "Cert:\CurrentUser\My"
    ```    
1. Nyissa meg a **felhasználói tanúsítványok kezelése**  >  **aktuális felhasználói**  >  **személyes**  >  **tanúsítványok**  >  *yourappname.yourtenant.onmicrosoft.com*.
1. Válassza ki a tanúsítványt > **művelet**  >  **minden feladat**  >  **Exportálás** elemet.
1. Válassza az **Igen**  >  **tovább** lehetőséget  >  **, majd exportálja a titkos kulcsot a**  >  **Tovább gombra**.
1. Fogadja el az **exportálási** fájlformátum alapértelmezett értékeit.
1. Adja meg a tanúsítványhoz tartozó jelszót.

### <a name="add-a-client-certificate-policy-key"></a>Ügyféltanúsítvány-házirend kulcsának hozzáadása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, és válassza ki a Azure ad B2C könyvtárat.
1. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Azure ad B2C**.
1. Az Áttekintés lapon válassza az **identitási élmény keretrendszert**.
1. Válassza a **szabályzat kulcsok** lehetőséget, majd kattintson a **Hozzáadás** gombra.
1. A **Beállítások** mezőben válassza a **feltöltés** lehetőséget.
1. A **név** mezőbe írja be a következőt: **RestApiClientCertificate**.
    A rendszer automatikusan hozzáadja a *B2C_1A_* előtagot.
1. A **fájlfeltöltés** mezőben válassza ki a tanúsítvány. pfx fájlját, amely titkos kulccsal rendelkezik.
1. A **jelszó** mezőbe írja be a tanúsítvány jelszavát.
1. Válassza a **Létrehozás** lehetőséget.

### <a name="configure-your-rest-api-technical-profile-to-use-client-certificate-authentication"></a>Az REST API műszaki profil konfigurálása az ügyféltanúsítvány-alapú hitelesítés használatára

A szükséges kulcs létrehozása után konfigurálja a REST API technikai profil metaadatait az ügyféltanúsítvány hivatkozására.

1. A munkakönyvtárában nyissa meg a kiterjesztési házirend fájlját (TrustFrameworkExtensions.xml).
1. Keresse meg a REST API technikai profilt. Például: `REST-ValidateProfile` vagy `REST-GetProfile` .
1. Keresse meg az `<Metadata>` elemet.
1. Módosítsa a *AuthenticationType* a következőre: `ClientCertificate` .
1. Módosítsa a *AllowInsecureAuthInProduction* a következőre: `false` .
1. Közvetlenül a záró `</Metadata>` elem után adja hozzá a következő XML-kódrészletet:
    ```xml
    <CryptographicKeys>
       <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_RestApiClientCertificate" />
    </CryptographicKeys>
    ```

A következő példa egy HTTP-ügyféltanúsítvány használatára konfigurált REST-alapú műszaki profilt mutat be:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">ClientCertificate</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_RestApiClientCertificate" />
      </CryptographicKeys>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="oauth2-bearer-authentication"></a>OAuth2-tulajdonos hitelesítése 

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

A tulajdonosi jogkivonat hitelesítése a [OAuth 2.0 engedélyezési keretrendszerében van definiálva: tulajdonosi jogkivonat használata (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). A tulajdonosi jogkivonat-hitelesítésben Azure AD B2C egy HTTP-kérelmet küld az engedélyezési fejlécben található jogkivonattal.

```http
Authorization: Bearer <token>
```

A tulajdonosi jogkivonat egy átlátszatlan karakterlánc. Ez lehet egy JWT hozzáférési token vagy bármely olyan karakterlánc, amelyet a REST API az engedélyezési fejlécben Azure AD B2C küldésére vár. Azure AD B2C a következő típusokat támogatja:

- **Tulajdonosi jogkivonat**. Ahhoz, hogy el tudja küldeni a tulajdonosi jogkivonatot a REST-technikai profilban, a szabályzatnak először be kell kérnie a tulajdonosi jogkivonatot, majd azt a REST technikai profilban kell használnia.  
- **Statikus tulajdonosi jogkivonat**. Ezt a módszert akkor használja, ha a REST API hosszú távú hozzáférési tokent ad ki. Ha statikus tulajdonosi jogkivonatot szeretne használni, hozzon létre egy házirend-kulcsot, és készítsen hivatkozást a REST technikai profilból a házirend-kulcsra. 


## <a name="using-oauth2-bearer"></a>A OAuth2-tulajdonos használata  

A következő lépések bemutatják, hogyan használhatók az ügyfél hitelesítő adatai egy tulajdonosi jogkivonat beszerzéséhez, és hogyan adhatók át az REST API-hívások engedélyezési fejlécébe.  

### <a name="define-a-claim-to-store-the-bearer-token"></a>Jogcím megadása a tulajdonosi jogkivonat tárolásához

A jogcím a Azure AD B2C szabályzat végrehajtása során ideiglenes adattárolást biztosít. A [jogcím-séma](claimsschema.md) az a hely, ahol deklarálja a jogcímeket. A hozzáférési jogkivonatot a későbbiekben használni kívánt jogcímben kell tárolni. 

1. Nyissa meg a szabályzat Extensions (bővítmények) fájlját. Például: <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .
1. Keresse meg a [BuildingBlocks](buildingblocks.md) elemet. Ha az elem nem létezik, adja hozzá.
1. Keresse meg a [ClaimsSchema](claimsschema.md) elemet. Ha az elem nem létezik, adja hozzá.
1. Adja hozzá a következő jogcímeket a **ClaimsSchema** elemhez.  

```xml
<ClaimType Id="bearerToken">
  <DisplayName>Bearer token</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="grant_type">
  <DisplayName>Grant type</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="scope">
  <DisplayName>scope</DisplayName>
  <DataType>string</DataType>
</ClaimType>
```

### <a name="acquiring-an-access-token"></a>Hozzáférési jogkivonat beszerzése 

A hozzáférési jogkivonatot többféleképpen is beszerezheti: Ha [egy összevont identitás-szolgáltatótól](idp-pass-through-user-flow.md)szerzi be REST API a hozzáférési jogkivonatot, egy [ROPC-folyamat](../active-directory/develop/v2-oauth-ropc.md)használatával vagy az [ügyfél hitelesítő adatainak](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md)használatával.  

Az alábbi példa egy REST API technikai profilt használ az Azure AD jogkivonat-végpontra irányuló kérelem elvégzésére az ügyfél HTTP alapszintű hitelesítésként átadott hitelesítő adatainak használatával. Az Azure AD-ben való konfigurálásról a [Microsoft Identity platform és a OAuth 2,0 ügyfél-hitelesítő adatok folyamata](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md)című cikkben olvashat. Előfordulhat, hogy módosítania kell ezt az identitás-szolgáltatóval való kapcsolatra. 

A ServiceUrl cserélje le a-bérlő-nevet az Azure AD-bérlő nevére. Tekintse meg a [Rest műszaki profil](restful-technical-profile.md) referenciáját az összes elérhető lehetőségről.

```xml
<TechnicalProfile Id="SecureREST-AccessToken">
  <DisplayName></DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://login.microsoftonline.com/your-tenant-name.onmicrosoft.com/oauth2/v2.0/token</Item>
    <Item Key="AuthenticationType">Basic</Item>
     <Item Key="SendClaimsIn">Form</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_SecureRESTClientId" />
    <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_SecureRESTClientSecret" />
  </CryptographicKeys>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="grant_type" DefaultValue="client_credentials" />
    <InputClaim ClaimTypeReferenceId="scope" DefaultValue="https://secureb2cfunction.azurewebsites.net/.default" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="bearerToken" PartnerClaimType="access_token" />
  </OutputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

### <a name="change-the-rest-technical-profile-to-use-bearer-token-authentication"></a>A REST technikai profil módosítása a tulajdonosi jogkivonat hitelesítésének használatára

Ha a tulajdonosi jogkivonat hitelesítését szeretné támogatni az egyéni házirendben, módosítsa a REST API technikai profilt a következővel:

1. A munkakönyvtárában nyissa meg az *TrustFrameworkExtensions.xml* -bővítmény házirend-fájlját.
1. Keresse meg a-t `<TechnicalProfile>` tartalmazó csomópontot `Id="REST-API-SignUp"` .
1. Keresse meg az `<Metadata>` elemet.
1. Módosítsa a *AuthenticationType* a *tulajdonosra*, a következőképpen:
    ```xml
    <Item Key="AuthenticationType">Bearer</Item>
    ```
1. Módosítsa vagy adja hozzá a *UseClaimAsBearerToken* a *bearerToken*-hez az alábbiak szerint. A *bearerToken* annak a jogcímnek a neve, amelynek a tulajdonosi jogkivonatát beolvassa a rendszer (a kimeneti jogcímeből `SecureREST-AccessToken` ).

    ```xml
    <Item Key="UseClaimAsBearerToken">bearerToken</Item>
    ```
    
1. Győződjön meg arról, hogy a fent használt jogcímet bemeneti jogcímként adja hozzá:

    ```xml
    <InputClaim ClaimTypeReferenceId="bearerToken"/>
    ```    

A fenti kódrészletek hozzáadása után a technikai profilnak a következő XML-kódhoz hasonlóan kell kinéznie:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">Bearer</Item>
        <Item Key="UseClaimAsBearerToken">bearerToken</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="bearerToken"/>
      </InputClaims>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="using-a-static-oauth2-bearer"></a>Statikus OAuth2-tulajdonos használata 

### <a name="add-the-oauth2-bearer-token-policy-key"></a>Adja hozzá a OAuth2 tulajdonosi jogkivonat-házirendjének kulcsát

Ha egy REST API műszaki profilt OAuth2 tulajdonosi jogkivonattal szeretne konfigurálni, szerezze be a hozzáférési jogkivonatot a REST API tulajdonosától. Ezután hozza létre a következő titkosítási kulcsot a tulajdonosi jogkivonat tárolásához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, és válassza ki a Azure ad B2C könyvtárat.
1. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Azure ad B2C**.
1. Az Áttekintés lapon válassza az **identitási élmény keretrendszert**.
1. Válassza a **szabályzat kulcsok** lehetőséget, majd kattintson a **Hozzáadás** gombra.
1. A **Beállítások** területen válassza a lehetőséget `Manual` .
1. Adja meg a szabályzat kulcsának **nevét** . Például: `RestApiBearerToken`. A rendszer automatikusan hozzáadja az előtagot a `B2C_1A_` kulcs nevéhez.
1. A **Secret (titkos kulcs**) mezőben adja meg a korábban rögzített ügyfél-titkot.
1. A **kulcshasználat** beállításnál válassza a elemet `Encryption` .
1. Válassza a **Létrehozás** lehetőséget.

### <a name="configure-your-rest-api-technical-profile-to-use-the-bearer-token-policy-key"></a>A REST API technikai profiljának konfigurálása a tulajdonosi jogkivonat-házirend kulcsának használatára

A szükséges kulcs létrehozása után konfigurálja a REST API technikai profil metaadatait a tulajdonosi jogkivonatra való hivatkozáshoz.

1. A munkakönyvtárában nyissa meg a kiterjesztési házirend fájlját (TrustFrameworkExtensions.xml).
1. Keresse meg a REST API technikai profilt. Például: `REST-ValidateProfile` vagy `REST-GetProfile` .
1. Keresse meg az `<Metadata>` elemet.
1. Módosítsa a *AuthenticationType* a következőre: `Bearer` .
1. Módosítsa a *AllowInsecureAuthInProduction* a következőre: `false` .
1. Közvetlenül a záró `</Metadata>` elem után adja hozzá a következő XML-kódrészletet:
    ```xml
    <CryptographicKeys>
       <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_RestApiBearerToken" />
    </CryptographicKeys>
    ```

A következő példa egy olyan REST-alapú műszaki profilt mutat be, amely a tulajdonosi jogkivonat-hitelesítéssel van konfigurálva:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">Bearer</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_RestApiBearerToken" />
      </CryptographicKeys>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="api-key-authentication"></a>API-kulcs hitelesítése

Az API-kulcs egy olyan egyedi azonosító, amely egy felhasználó hitelesítésére szolgál REST API végpont eléréséhez. A kulcsot egy egyéni HTTP-fejlécben kell elküldeni. A [Azure FUNCTIONS http-trigger](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) például a `x-functions-key` http-fejlécet használja a kérelmező azonosítására.  

### <a name="add-api-key-policy-keys"></a>API-kulcsokra vonatkozó házirend-kulcsok hozzáadása

REST API műszaki profil API-kulcsos hitelesítéssel való konfigurálásához hozza létre a következő titkosítási kulcsot az API-kulcs tárolásához:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, és válassza ki a Azure ad B2C könyvtárat.
1. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Azure ad B2C**.
1. Az Áttekintés lapon válassza az **identitási élmény keretrendszert**.
1. Válassza a **szabályzat kulcsok** lehetőséget, majd kattintson a **Hozzáadás** gombra.
1. A **Beállítások** lapon válassza a **manuális** lehetőséget.
1. A **név** mezőbe írja be a következőt: **RestApiKey**.
    Lehet, hogy az előtag *B2C_1A_* automatikusan hozzá lesz adva.
1. A **titok** mezőbe írja be a REST API kulcsot.
1. A **kulcshasználat** beállításnál válassza a **titkosítás** lehetőséget.
1. Válassza a **Létrehozás** lehetőséget.


### <a name="configure-your-rest-api-technical-profile-to-use-api-key-authentication"></a>Az REST API műszaki profil konfigurálása az API-kulcsos hitelesítés használatára

A szükséges kulcs létrehozása után konfigurálja a REST API technikai profil metaadatait a hitelesítő adatokra való hivatkozáshoz.

1. A munkakönyvtárában nyissa meg a kiterjesztési házirend fájlját (TrustFrameworkExtensions.xml).
1. Keresse meg a REST API technikai profilt. Például: `REST-ValidateProfile` vagy `REST-GetProfile` .
1. Keresse meg az `<Metadata>` elemet.
1. Módosítsa a *AuthenticationType* a következőre: `ApiKeyHeader` .
1. Módosítsa a *AllowInsecureAuthInProduction* a következőre: `false` .
1. Közvetlenül a záró `</Metadata>` elem után adja hozzá a következő XML-kódrészletet:
    ```xml
    <CryptographicKeys>
        <Key Id="x-functions-key" StorageReferenceId="B2C_1A_RestApiKey" />
    </CryptographicKeys>
    ```

A titkosítási kulcs **azonosítója** a HTTP-fejlécet határozza meg. Ebben a példában az API-kulcsot **x-functions-Key-** ként küldjük el.

Az alábbi példa egy olyan REST-alapú műszaki profilt mutat be, amely az API-kulcsos hitelesítéssel rendelkező Azure-függvény meghívására van konfigurálva:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">ApiKeyHeader</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="x-functions-key" StorageReferenceId="B2C_1A_RestApiKey" />
      </CryptographicKeys>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="next-steps"></a>Következő lépések

- További információ az IEF-referenciában található [Rest technikai profil](restful-technical-profile.md) elemről.