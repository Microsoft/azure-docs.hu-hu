---
title: Restful-szolgáltatás biztonságossá Azure AD B2C
titleSuffix: Azure AD B2C
description: Biztonságossá kell REST API egyéni jogcímek cseréjét a Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/19/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 462d69a8bde0dec2689ac30620276b5bcd335410
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717692"
---
# <a name="secure-your-restful-services"></a>A RESTful-szolgáltatások biztonságossá tere 

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Amikor egy REST API integrál egy Azure AD B2C folyamatba, hitelesítéssel kell védenie REST API végpontot. Ez biztosítja, hogy csak a megfelelő hitelesítő adatokkal (például Azure AD B2C) kapcsolatos szolgáltatások kezdeményezni tudjanak hívásokat a REST API végpontra.

Megtudhatja, hogyan integrálható egy REST API a felhasználói Azure AD B2C [](custom-policy-rest-api-claims-validation.md) a felhasználói bevitel ellenőrzése és a Jogcímek cseréje egyéni szabályzatokkal REST API [cikkekbe.](custom-policy-rest-api-claims-exchange.md)

Ez a cikk bemutatja, hogyan védheti meg REST API http alapszintű HTTP-, ügyfél-tanúsítvány- vagy OAuth2-hitelesítéssel. 

## <a name="prerequisites"></a>Előfeltételek

Kövesse az alábbi "Útmutató" útmutatók valamelyikében található lépéseket:

- [Integrálja REST API jogcímek cseréjét](custom-policy-rest-api-claims-validation.md)a Azure AD B2C felhasználói úton a felhasználói bevitel ellenőrzéséhez.
- [Új REST API hozzáadása egyéni szabályzatokhoz](custom-policy-rest-api-claims-exchange.md)

## <a name="http-basic-authentication"></a>ALAPszintű HTTP-hitelesítés

Az alapszintű HTTP-hitelesítés az [RFC 2617-ben van meghatározva.](https://tools.ietf.org/html/rfc2617) Az alapszintű hitelesítés a következőképpen működik: Azure AD B2C http-kérést küld az engedélyezési fejlécben található ügyfél-hitelesítő adatokkal. A hitelesítő adatok a "name:password" base64 kódolású sztringként vannak formázva.  

### <a name="add-rest-api-username-and-password-policy-keys"></a>Felhasználónév REST API jelszó házirendkulcsok hozzáadása

Ha alapszintű HTTP REST API hitelesítéssel konfigurál egy technikai profilt, hozza létre a következő titkosítási kulcsokat a felhasználónév és a jelszó tárolására:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. Győződjön meg arról, hogy a saját bérlőt tartalmazó könyvtárat Azure AD B2C használja. Válassza ki **a Címtár és előfizetés** szűrőt a felső menüben, és válassza ki Azure AD B2C címtárat.
1. Válassza **a bal felső** sarokban található Minden szolgáltatás Azure Portal, majd keresse meg és válassza a **Azure AD B2C.**
1. Az Áttekintés lapon válassza **a** Identity Experience Framework.
1. Válassza **a Házirendkulcsok,** majd a Hozzáadás **lehetőséget.**
1. A **Beállítások beállításhoz** válassza a **Manuális lehetőséget.**
1. A **Név mezőbe** írja be a **következőt: RestApiUsername.**
    Az *előtag B2C_1A_* automatikusan hozzáadható.
1. A Titkos **listában** adja meg a REST API felhasználónevet.
1. A **Kulcshasználat beállításhoz** válassza a **Titkosítás lehetőséget.**
1. Válassza a **Létrehozás** lehetőséget.
1. Válassza **ismét a Szabályzatkulcsok** lehetőséget.
1. Válassza a **Hozzáadás** lehetőséget.
1. A **Beállítások beállításhoz** válassza a **Manuális lehetőséget.**
1. A **Név mezőbe** írja be a **következőt: RestApiPassword.**
    Az *előtag B2C_1A_* automatikusan hozzáadható.
1. A Titkos **listában** adja meg a REST API jelszót.
1. A **Kulcshasználat beállításhoz** válassza a **Titkosítás lehetőséget.**
1. Válassza a **Létrehozás** lehetőséget.

### <a name="configure-your-rest-api-technical-profile-to-use-http-basic-authentication"></a>A technikai REST API konfigurálása alapszintű HTTP-hitelesítés használatára

A szükséges kulcsok létrehozása után konfigurálja a REST API profil metaadatait a hitelesítő adatokra való hivatkozáshoz.

1. A munkakönyvtárban nyissa meg a bővítmény házirendfájlját (TrustFrameworkExtensions.xml).
1. Keresse meg a REST API profilt. Például : `REST-ValidateProfile` vagy `REST-GetProfile` .
1. Keresse meg az `<Metadata>` elemet.
1. Módosítsa az *AuthenticationType tulajdonságot a következőre:* `Basic` .
1. Módosítsa az *AllowInsecureAuthInProduction et a következőre:* `false` .
1. Közvetlenül a záró elem `</Metadata>` után adja hozzá a következő XML-kódrészletet:
    ```xml
    <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_RestApiUsername" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_RestApiPassword" />
    </CryptographicKeys>
    ```

Az alábbiakban egy egyszerű HTTP-hitelesítéssel konfigurált RESTful technikai profilra mutatunk példát:

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

## <a name="https-client-certificate-authentication"></a>HTTPS-ügyfél tanúsítványának hitelesítése

Az ügyfél-tanúsítványos hitelesítés kölcsönös tanúsítványalapú hitelesítés, amelyben az ügyfél Azure AD B2C az ügyfél tanúsítványát biztosítja a kiszolgálónak identitásának bizonyítására. Ez az SSL-kézfogás részeként történik. Csak a megfelelő tanúsítványokkal (például Azure AD B2C) rendelkezik hozzáféréssel a REST API szolgáltatáshoz. Az ügyfél tanúsítványa egy X.509 digitális tanúsítvány. Éles környezetben azt egy hitelesítésszolgáltatónak kell aláírni.

### <a name="prepare-a-self-signed-certificate-optional"></a>Önaírt tanúsítvány előkészítése (nem kötelező)

Nem éles környezetekben, ha még nem rendelkezik tanúsítvánnyal, használhat önaírt tanúsítványt. Windows rendszeren a PowerShell [New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) parancsmagja segítségével hozhat létre tanúsítványt.

1. Hajtsa végre ezt a PowerShell-parancsot egy önaírt tanúsítvány létrehozásához. Módosítsa az argumentumot az alkalmazásnak megfelelően, és `-Subject` Azure AD B2C bérlő nevét. A dátumot módosíthatja `-NotAfter` is, hogy más lejárati időt adjon meg a tanúsítványhoz.
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
1. Nyissa **meg a Felhasználói tanúsítványok kezelése** Aktuális  >  **felhasználói**  >  **személyes** tanúsítványok a  >  **yourappname.yourtenant.onmicrosoft.com.**  >  
1. Válassza ki a tanúsítványt, >  >  **Művelet Minden feladat**  >  **exportálása lehetőséget.**
1. Válassza **az Igen**  >  **Következő**  >  **Igen, exportálja a titkos kulcsot Tovább**  >  lehetőséget.
1. Fogadja el az Alapértelmezett **fájlformátum exportálása beállítást.**
1. Adjon meg egy jelszót a tanúsítványhoz.

### <a name="add-a-client-certificate-policy-key"></a>Ügyfél-tanúsítvány házirendkulcsának hozzáadása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. Győződjön meg arról, hogy azt a címtárat használja, amely a Azure AD B2C található. Válassza ki **a Címtár és előfizetés** szűrőt a felső menüben, és válassza ki Azure AD B2C címtárat.
1. Válassza **a bal felső** sarokban található Minden szolgáltatás Azure Portal, majd keresse meg és válassza a **Azure AD B2C.**
1. Az Áttekintés lapon válassza **a** Identity Experience Framework.
1. Válassza **a Házirendkulcsok,** majd a Hozzáadás **lehetőséget.**
1. A Beállítások **mezőben** válassza a Feltöltés **lehetőséget.**
1. A Név **mezőbe írja** be a **Következőt: RestApiClientCertificate.**
    Az *előtag B2C_1A_* automatikusan hozzá lesz adva.
1. A Fájl **feltöltése mezőben** válassza ki a tanúsítvány .pfx fájlját egy titkos kulccsal.
1. A Jelszó **mezőbe** írja be a tanúsítvány jelszavát.
1. Válassza a **Létrehozás** lehetőséget.

### <a name="configure-your-rest-api-technical-profile-to-use-client-certificate-authentication"></a>A REST API konfigurálása az ügyfél-tanúsítvány hitelesítésének használatára

A szükséges kulcs létrehozása után konfigurálja a REST API profil metaadatait az ügyfél-tanúsítványra való hivatkozáshoz.

1. A munkakönyvtárban nyissa meg a bővítmény házirendfájlját (TrustFrameworkExtensions.xml).
1. Keresse meg a REST API profilt. Például : `REST-ValidateProfile` vagy `REST-GetProfile` .
1. Keresse meg az `<Metadata>` elemet.
1. Módosítsa az *AuthenticationType tulajdonságot a következőre:* `ClientCertificate` .
1. Módosítsa az *AllowInsecureAuthInProduction et a következőre:* `false` .
1. Közvetlenül a záró elem után adja hozzá a `</Metadata>` következő XML-kódrészletet:
    ```xml
    <CryptographicKeys>
       <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_RestApiClientCertificate" />
    </CryptographicKeys>
    ```

Az alábbiakban egy HTTP-ügyfél tanúsítvánnyal konfigurált RESTful technikai profilra mutatunk példát:

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

## <a name="oauth2-bearer-authentication"></a>OAuth2-alapú bearer hitelesítés 

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

A bearer token hitelesítése az [OAuth2.0 engedélyezési keretrendszerben van meghatározva: Bearer Token Usage (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). A jogkivonatok hitelesítése során a Azure AD B2C http-kérést küld egy jogkivonattal az engedélyezési fejlécben.

```http
Authorization: Bearer <token>
```

A bearer token egy átlátszatlan sztring. Ez lehet egy JWT hozzáférési jogkivonat vagy bármilyen sztring, REST API, Azure AD B2C az engedélyezési fejlécben kell küldenie. Azure AD B2C a következő típusokat támogatja:

- **Bearer token**. Ahhoz, hogy a tulajdonosi jogkivonatot el tudja küldeni a Restful technikai profilban, a szabályzatnak először be kell szereznie a tulajdonosi jogkivonatot, majd fel kell használnia azt a RESTful technikai profiljában.  
- **Statikus bearer jogkivonat.** Akkor használja ezt a REST API, ha a felhasználó hosszú távú hozzáférési jogkivonatot ad ki. Statikus tulajdonosi jogkivonat létrehozásához hozzon létre egy szabályzatkulcsot, és hivatkozhat a RESTful technikai profiljából a szabályzatkulcsra. 


## <a name="using-oauth2-bearer"></a>Az OAuth2 Bearer használata  

A következő lépések bemutatják, hogyan használhatók az ügyfél-hitelesítő adatok a bearer token beszerzéséhez és a hitelesítő adatoknak az engedélyezési fejlécbe való REST API során.  

### <a name="define-a-claim-to-store-the-bearer-token"></a>Jogcím meghatározása a bearer token tárolására

A jogcím ideiglenes adattárolást biztosít egy Azure AD B2C végrehajtása során. A [jogcímek sémája](claimsschema.md) az a hely, ahol deklarálja a jogcímeket. A hozzáférési jogkivonatot egy jogcímben kell tárolni, hogy később használni lehet. 

1. Nyissa meg a szabályzat bővítményfájlját. Például: <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .
1. Keresse meg a [BuildingBlocks](buildingblocks.md) elemet. Ha a elem nem létezik, adja hozzá.
1. Keresse meg [a ClaimsSchema](claimsschema.md) elemet. Ha a elem nem létezik, adja hozzá.
1. Adja hozzá a következő jogcímeket a **ClaimsSchema elemhez.**  

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

A hozzáférési jogkivonatot többféleképpen is beszerezheti: [](idp-pass-through-user-flow.md)egy összevont identitásszolgáltatótól való beszerzéséhez hívja meg a REST API-t, amely egy hozzáférési jogkivonatot ad vissza egy [ROPC-folyamattal,](../active-directory/develop/v2-oauth-ropc.md)vagy az ügyfél hitelesítőadat-folyamatával. [](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md) Az ügyfél-hitelesítő adatok folyamatát általában olyan kiszolgálók közötti interakciókhoz használják, amelyeknek a háttérben kell futniuk anélkül, hogy azonnali interakciót kell alkalmazniuk a felhasználóval.

#### <a name="acquiring-an-azure-ad-access-token"></a>Azure AD hozzáférési jogkivonat beszerzése 

Az alábbi példa egy REST API profillal kér az Azure AD-jogkivonat végpontjára az alapszintű HTTP-hitelesítésként átadott ügyfél-hitelesítő adatokkal. További információ: [A Microsoft identitásplatformja és az OAuth 2.0](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md)ügyfél-hitelesítő adatok folyamat. 

Az Azure AD hozzáférési jogkivonatának megszerzéséhez hozzon létre egy alkalmazást az Azure AD-bérlőben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza ki **a Címtár és előfizetés** szűrőt a felső menüben, majd válassza ki az Azure AD-bérlőt tartalmazó címtárat.
1. A bal oldali menüben válassza **a** Azure Active Directory lehetőséget. Vagy válassza a **Minden szolgáltatás lehetőséget,** és keresse meg és válassza a **Azure Active Directory.**
1. Válassza **Alkalmazásregisztrációk** lehetőséget, majd válassza az Új **regisztráció lehetőséget.**
1. Adja **meg** az alkalmazás nevét. Például: *Client_Credentials_Auth_app.*
1. A **Támogatott fióktípusok alatt válassza** a Csak a szervezeti **címtárban található fiókok lehetőséget.**
1. Válassza a **Regisztráció** lehetőséget.
2. Jegyezd fel **az alkalmazás (ügyfél) azonosítóját.** 


Az ügyfél-hitelesítő adatok folyamatához létre kell hoznia egy titkos alkalmazást. A titkos ügyféltitkot alkalmazásjelszónak is nevezik. Az alkalmazás a titkos okkal szerez hozzáférési jogkivonatot.

1. A Azure AD B2C **– Alkalmazásregisztrációk** lapon válassza ki a létrehozott alkalmazást, például a *Client_Credentials_Auth_app.*
1. A bal oldali menü Kezelés **menüjében válassza** a Tanúsítványok és **titkos & lehetőséget.**
1. Válassza az **Új titkos ügyfélkód** lehetőséget.
1. A Leírás mezőben adja meg az ügyfél titkos **ának leírását.** Például *clientsecret1*.
1. A **Lejárat alatt** válassza ki azt az időtartamot, amelyre érvényes a titkos kód, majd válassza a Hozzáadás **lehetőséget.**
1. Rögzítse a titkos kód **értékét** az ügyfélalkalmazás kódban való használatra. Ez a titkos érték a lap elhagyás után többé nem jelenik meg. Ezt az értéket használhatja az alkalmazás kódban titkos alkalmazáskódként.

#### <a name="create-azure-ad-b2c-policy-keys"></a>Szabályzatkulcsok Azure AD B2C létrehozása

Tárolnia kell az ügyfél-azonosítót és a titkos ügyfél titkos adatokat, amelyet korábban rögzített a Azure AD B2C bérlőben.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Győződjön meg arról, hogy a saját bérlőt tartalmazó Azure AD B2C használja. Válassza ki **a Címtár és előfizetés** szűrőt a felső menüben, és válassza ki a bérlőt tartalmazó címtárat.
3. Válassza **a Bal felső** sarokban található Minden szolgáltatás Azure Portal, majd keresse meg és válassza a **Azure AD B2C.**
4. Az Áttekintés lapon válassza **a** Identity Experience Framework.
5. Válassza **a Házirendkulcsok,** majd a Hozzáadás **lehetőséget.**
6. A **Beállítások beállításhoz** válassza a `Manual` lehetőséget.
7. Adja meg a **szabályzatkulcs** nevét `SecureRESTClientId` (). A rendszer automatikusan hozzáadja az előtagot `B2C_1A_` a kulcs nevéhez.
8. A **Titkos kódban** adja meg a korábban feljegyzett ügyfél-azonosítót.
9. A **Kulcshasználat mezőben** válassza a `Signature` lehetőséget.
10. Kattintson a **Létrehozás** lehetőségre.
11. Hozzon létre egy másik szabályzatkulcsot a következő beállításokkal:
    -   **Név:** `SecureRESTClientSecret` .
    -   **Titkos:** adja meg az ügyfél korábban rögzített titkos adatokat

A ServiceUrl helyett cserélje le a your-tenant-name helyére az Azure AD-bérlő nevét. Az összes elérhető lehetőségért tekintse meg a [RESTful műszaki profil](restful-technical-profile.md) referenciáját.

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
    <InputClaim ClaimTypeReferenceId="scope" DefaultValue="https://graph.microsoft.com/.default" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="bearerToken" PartnerClaimType="access_token" />
  </OutputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

### <a name="change-the-rest-technical-profile-to-use-bearer-token-authentication"></a>A REST technikai profiljának módosítása a jogkivonat-alapú hitelesítés használatára

A bearer tokenek egyéni szabályzatban való hitelesítésének támogatásához módosítsa a REST API a következővel:

1. A munkakönyvtárban nyissa  meg aTrustFrameworkExtensions.xmlszabályzatfájlját.
1. Keresse meg a `<TechnicalProfile>` csomópontot, amely tartalmazza a `Id="REST-API-SignUp"` et.
1. Keresse meg az `<Metadata>` elemet.
1. Módosítsa *az AuthenticationType tulajdonságot* *Bearer* típusra a következőképpen:
    ```xml
    <Item Key="AuthenticationType">Bearer</Item>
    ```
1. Módosítsa vagy adja hozzá a *UseClaimAsBearerToken et* *a bearerTokenhez* az alábbiak szerint. A *bearerToken annak* a jogcímnek a neve, amelyből a rendszer lekéri a bearer tokent (a parancsból származó kimeneti `SecureREST-AccessToken` jogcímet).

    ```xml
    <Item Key="UseClaimAsBearerToken">bearerToken</Item>
    ```
    
1. Győződjön meg arról, hogy hozzáadta a fent használt jogcímet bemeneti jogcímként:

    ```xml
    <InputClaim ClaimTypeReferenceId="bearerToken"/>
    ```    

A fenti kódrészletek hozzáadása után a technikai profilnak az alábbi XML-kódhoz kell hasonlítanunk:

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

## <a name="using-a-static-oauth2-bearer"></a>Statikus OAuth2-bearer használata 

### <a name="add-the-oauth2-bearer-token-policy-key"></a>Az OAuth2 bearer token szabályzatkulcsának hozzáadása

Ha egy technikai REST API OAuth2 tulajdonosi jogkivonattal kell konfigurálnia, szerezze be a hozzáférési jogkivonatot a REST API tulajdonosától. Ezután hozza létre a következő titkosítási kulcsot a szolgáltatói jogkivonat tárolására.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. Győződjön meg arról, hogy a saját bérlőt tartalmazó könyvtárat Azure AD B2C használja. Válassza ki **a Címtár és előfizetés** szűrőt a felső menüben, és válassza ki Azure AD B2C címtárat.
1. Válassza **a bal felső** sarokban található Minden szolgáltatás Azure Portal, majd keresse meg és válassza a **Azure AD B2C.**
1. Az Áttekintés lapon válassza **a** Identity Experience Framework.
1. Válassza **a Házirendkulcsok,** majd a Hozzáadás **lehetőséget.**
1. A **Beállítások beállításhoz** válassza a `Manual` lehetőséget.
1. Adja meg a **szabályzatkulcs** nevét. Például: `RestApiBearerToken`. A rendszer automatikusan hozzáadja az előtagot `B2C_1A_` a kulcs nevéhez.
1. A **Secret (Titkos)** szövegben adja meg a korábban feljegyzett titkos ügyfél titkos adatokat.
1. A **Kulcshasználat mezőben** válassza a `Encryption` lehetőséget.
1. Válassza a **Létrehozás** lehetőséget.

### <a name="configure-your-rest-api-technical-profile-to-use-the-bearer-token-policy-key"></a>A REST API konfigurálása a bearer token szabályzatkulcs használatára

A szükséges kulcs létrehozása után konfigurálja a REST API a technikai profil metaadatait a bearer tokenre való hivatkozáshoz.

1. A munkakönyvtárban nyissa meg a bővítmény szabályzatfájlját (TrustFrameworkExtensions.xml).
1. Keresse meg a REST API profilt. Például: `REST-ValidateProfile` vagy `REST-GetProfile` .
1. Keresse meg az `<Metadata>` elemet.
1. Módosítsa az *AuthenticationType tulajdonságot a következőre:* `Bearer` .
1. Módosítsa az *AllowInsecureAuthInProduction et a következőre:* `false` .
1. Közvetlenül a záró elem `</Metadata>` után adja hozzá a következő XML-kódrészletet:
    ```xml
    <CryptographicKeys>
       <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_RestApiBearerToken" />
    </CryptographicKeys>
    ```

Az alábbiakban egy példát mutatunk be egy tulajdonosi jogkivonat-hitelesítéssel konfigurált RESTful technikai profilra:

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

## <a name="api-key-authentication"></a>API-kulcsos hitelesítés

Az API-kulcs egy egyedi azonosító, amely egy felhasználó hitelesítésére használható egy REST API végpont eléréséhez. A kulcsot a rendszer egy egyéni HTTP-fejlécben küldi el. A http Azure Functions [eseményindító](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) például a `x-functions-key` HTTP-fejlécet használja a kérelmező azonosításához.  

### <a name="add-api-key-policy-keys"></a>API-kulcs szabályzatkulcsok hozzáadása

Ha egy technikai REST API API-kulcsos hitelesítéssel konfigurál, hozza létre a következő titkosítási kulcsot az API-kulcs tárolására:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. Győződjön meg arról, hogy azt a címtárat használja, amely a Azure AD B2C található. Válassza ki **a Címtár és előfizetés** szűrőt a felső menüben, és válassza ki Azure AD B2C címtárat.
1. Válassza **a bal felső** sarokban található Minden szolgáltatás Azure Portal, majd keresse meg és válassza a **Azure AD B2C.**
1. Az Áttekintés lapon válassza **a** Identity Experience Framework.
1. Válassza **a Házirendkulcsok,** majd a Hozzáadás **lehetőséget.**
1. A **Beállítások beállításhoz** válassza a **Manuális lehetőséget.**
1. A **Név mezőbe írja** be a **Következőt: RestApiKey.**
    Az *előtag B2C_1A_* automatikusan hozzáadható.
1. A Titkos **kulcs mezőbe** írja be a REST API kulcsot.
1. A **Kulcshasználat beállításhoz** válassza a **Titkosítás lehetőséget.**
1. Válassza a **Létrehozás** lehetőséget.


### <a name="configure-your-rest-api-technical-profile-to-use-api-key-authentication"></a>A technikai REST API konfigurálása AZ API-kulcsos hitelesítés használatára

A szükséges kulcs létrehozása után konfigurálja a REST API profil metaadatait a hitelesítő adatokra való hivatkozáshoz.

1. A munkakönyvtárban nyissa meg a bővítmény házirendfájlját (TrustFrameworkExtensions.xml).
1. Keresse meg a REST API profilt. Például: `REST-ValidateProfile` vagy `REST-GetProfile` .
1. Keresse meg az `<Metadata>` elemet.
1. Módosítsa az *AuthenticationType tulajdonságot a következőre:* `ApiKeyHeader` .
1. Módosítsa az *AllowInsecureAuthInProduction et a következőre:* `false` .
1. Közvetlenül a záró elem `</Metadata>` után adja hozzá a következő XML-kódrészletet:
    ```xml
    <CryptographicKeys>
        <Key Id="x-functions-key" StorageReferenceId="B2C_1A_RestApiKey" />
    </CryptographicKeys>
    ```

A **titkosítási** kulcs azonosítója határozza meg a HTTP-fejlécet. Ebben a példában az API-kulcsot **x-functions-key-ként küldjük el.**

Az alábbi példában egy API-kulcsos hitelesítést használó Azure-függvény hívásához konfigurált RESTful technikai profilt mutatunk be:

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

- További információ az [IEF-referencia Restful technikai profil](restful-technical-profile.md) elemről.
