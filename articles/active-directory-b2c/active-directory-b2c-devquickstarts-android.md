---
title: Token beszerzése Android-alkalmazás használatával Azure Active Directory B2Cban | Microsoft Docs
description: Ez a cikk bemutatja, hogyan hozhat létre olyan Android-alkalmazást, amely a AppAuth-t használja Azure Active Directory B2C a felhasználói identitások kezeléséhez és a felhasználók hitelesítéséhez.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 28b1c3622ca449b0ce539937369fe43bd1d508ee
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73468954"
---
# <a name="sign-in-using-an-android-application-in-azure-active-directory-b2c"></a>Bejelentkezés Android-alkalmazással Azure Active Directory B2C

A Microsoft identitásplatformja nyílt szabványokat, többek között OAuth2-t és OpenID Connectet használ. Ezek a szabványok lehetővé teszik bármely olyan kódtár kihasználása, amelyet integrálni szeretne Azure Active Directory B2Cokkal. Ha más kódtárakat szeretne használni, a következőhöz hasonló bemutatót is használhat, amely bemutatja, hogyan konfigurálhat harmadik féltől származó kódtárat a Microsoft Identity platformhoz való kapcsolódáshoz. [A RFC6749 OAuth2 specifikációt](https://tools.ietf.org/html/rfc6749) megvalósító könyvtárak többsége a Microsoft Identity platformhoz tud csatlakozni.

> [!WARNING]
> A Microsoft nem biztosít javításokat harmadik féltől származó kódtárak számára, és nem végezte el a könyvtárak áttekintését. Ez a minta egy AppAuth nevű harmadik féltől származó könyvtárat használ, amely az Azure AD B2C segítségével alapszintű forgatókönyvekben való kompatibilitásra lett tesztelve. A problémákat és a szolgáltatási kérelmeket a könyvtár nyílt forráskódú projektjeire kell irányítani. További információt [ebben a cikkben](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries) talál.
>
>

Ha csak most ismerkedik az OAuth2 vagy az OpenID Connect használatával, előfordulhat, hogy nem fogja tökéletesen érteni a konfigurációs lépéseket. Ebben az esetben javasoljuk, hogy olvassa el [a protokoll áttekintését, amelyet itt talál](active-directory-b2c-reference-protocols.md).

## <a name="get-an-azure-ad-b2c-directory"></a>Az Azure AD B2C-címtár beszerzése

Az Azure AD B2C használatához létre kell hoznia egy címtárat vagy bérlőt. A címtárban tárolhatja az összes felhasználót, alkalmazást, csoportot és más elemeket. Ha még nem tette meg, [hozzon létre most egy B2C-címtárat](tutorial-create-tenant.md), mielőtt továbblépne.

## <a name="create-an-application"></a>Alkalmazás létrehozása

Ezután regisztráljon egy alkalmazást a Azure AD B2C-bérlőben. Ez biztosítja az Azure AD számára az alkalmazással való biztonságos kommunikációhoz szükséges információkat.

[!INCLUDE [active-directory-b2c-appreg-native](../../includes/active-directory-b2c-appreg-native.md)]

Jegyezze fel az **alkalmazás (ügyfél) azonosítóját** egy későbbi lépésben való használatra.

Az egyéni átirányítási URI-t is rögzítheti egy későbbi lépésben való használatra. Például: `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`.

## <a name="create-your-user-flows"></a>Felhasználói folyamatok létrehozása

Azure AD B2C minden felhasználói élményt egy [felhasználói folyamat](active-directory-b2c-reference-policies.md)definiál, amely az Azure ad viselkedését vezérlő szabályzatok összessége. Az alkalmazáshoz be kell jelentkeznie és regisztrálnia kell a felhasználói folyamatot. A felhasználói folyamat létrehozásakor ügyeljen arra, hogy:

* Válassza ki a **megjelenítendő nevet** regisztrációs attribútumként a felhasználói folyamatban.
* Válassza ki a **megjelenítendő nevet** és az **objektumazonosító** -alkalmazás jogcímeit minden felhasználói folyamatban. Ezenfelül más jogcímeket is használhat.
* A létrehozás után másolja az egyes felhasználói folyamatok **nevét** . A névnek a következő előtaggal kell rendelkeznie: `b2c_1_`.  A felhasználói folyamat nevét később kell megadnia.

Miután létrehozta a felhasználói folyamatokat, készen áll az alkalmazás létrehozására.

## <a name="download-the-sample-code"></a>A mintakód letöltése

Olyan munkamintaot kaptunk, amely a AppAuth-t használja Azure AD B2C [a githubon](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c). Letöltheti a kódot, és futtathatja. A [readme.MD](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c/blob/master/README.md)utasításait követve gyorsan megkezdheti saját alkalmazásának használatát a saját Azure ad B2C konfigurációjának használatával.

A minta a [AppAuth](https://openid.github.io/AppAuth-Android/)által megadott minta módosítása. A AppAuth és annak funkcióival kapcsolatos további információkért tekintse meg a lapjait.

## <a name="modifying-your-app-to-use-azure-ad-b2c-with-appauth"></a>Az alkalmazás módosítása Azure AD B2C használatára a AppAuth

> [!NOTE]
> A AppAuth az Android API 16 (Jellybean) és újabb verzióit támogatja. Javasoljuk, hogy a 23. és újabb API-t használja.
>

### <a name="configuration"></a>Konfiguráció

A Azure AD B2Choz való kommunikáció konfigurálásához adja meg a felderítési URI-t, vagy adja meg az engedélyezési végpontot és a jogkivonat-végpont URI-JÁT is. Mindkét esetben a következő információkra lesz szüksége:

* Bérlő azonosítója (pl. contoso.onmicrosoft.com)
* Felhasználói folyamat neve (pl. B2C\_1\_SignUpIn)

Ha úgy dönt, hogy automatikusan felderíti az engedélyezési és jogkivonat-végponti URI-ket, le kell kérnie az adatokat a felderítési URI-ból. A felderítési URI a bérlői\_AZONOSÍTÓjának és a házirend\_nevének a következő URL-címben való lecserélésével hozható létre:

```java
String mDiscoveryURI = "https://<Tenant_name>.b2clogin.com/<Tenant_ID>/v2.0/.well-known/openid-configuration?p=<Policy_Name>";
```

Ezután megvásárolhatja az engedélyezési és jogkivonat-végponti URI-ket, és létrehozhat egy AuthorizationServiceConfiguration-objektumot a következő parancs futtatásával:

```java
final Uri issuerUri = Uri.parse(mDiscoveryURI);
AuthorizationServiceConfiguration config;

AuthorizationServiceConfiguration.fetchFromIssuer(
    issuerUri,
    new RetrieveConfigurationCallback() {
      @Override public void onFetchConfigurationCompleted(
          @Nullable AuthorizationServiceConfiguration serviceConfiguration,
          @Nullable AuthorizationException ex) {
        if (ex != null) {
            Log.w(TAG, "Failed to retrieve configuration for " + issuerUri, ex);
        } else {
            // service configuration retrieved, proceed to authorization...
        }
      }
  });
```

Ahelyett, hogy a felderítést az engedélyezési és jogkivonat-végponti URI-k beszerzésére használja, explicit módon megadhatja azokat a bérlő\_AZONOSÍTÓjának és a házirend\_nevének a következő URL-címben való lecserélésével:

```java
String mAuthEndpoint = "https://<Tenant_name>.b2clogin.com/<Tenant_ID>/oauth2/v2.0/authorize?p=<Policy_Name>";

String mTokenEndpoint = "https://<Tenant_name>.b2clogin.com/<Tenant_ID>/oauth2/v2.0/token?p=<Policy_Name>";
```

Futtassa a következő kódot a AuthorizationServiceConfiguration objektum létrehozásához:

```java
AuthorizationServiceConfiguration config =
        new AuthorizationServiceConfiguration(name, mAuthEndpoint, mTokenEndpoint);

// perform the auth request...
```

### <a name="authorizing"></a>Engedélyezése

Az engedélyezési szolgáltatás konfigurációjának konfigurálása vagy beolvasása után egy engedélyezési kérelem is létrehozható. A kérelem létrehozásához a következő információkra lesz szüksége:

* Az ügyfél-azonosító (alkalmazás azonosítója), amelyet korábban rögzített. Például: `00000000-0000-0000-0000-000000000000`.
* A korábban rögzített egyéni átirányítási URI. Például: `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`.

[Az alkalmazás regisztrálásakor](#create-an-application)mindkét elemet el kell menteni.

```java
AuthorizationRequest req = new AuthorizationRequest.Builder(
    config,
    clientId,
    ResponseTypeValues.CODE,
    redirectUri)
    .build();
```

A folyamat további részének végrehajtásához tekintse meg a [AppAuth útmutatót](https://openid.github.io/AppAuth-Android/) . Ha gyorsan kell megkezdenie egy működő alkalmazást, tekintse meg a [mintát](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c). A saját Azure AD B2C konfigurációjának megadásához kövesse a [readme.MD](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c/blob/master/README.md) lépéseit.
