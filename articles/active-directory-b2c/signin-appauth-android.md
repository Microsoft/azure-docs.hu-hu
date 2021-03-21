---
title: Token beszerzése Android-alkalmazásokban
titleSuffix: Azure AD B2C
description: AppAuth-t használó Android-alkalmazás létrehozása a Azure Active Directory B2C a felhasználói identitások kezeléséhez és a felhasználók hitelesítéséhez.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/12/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: acbd2918bd311cec1c27018763ad10771d779d85
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94953321"
---
# <a name="sign-in-using-an-android-application-in-azure-active-directory-b2c"></a>Bejelentkezés Android-alkalmazással Azure Active Directory B2C

A Microsoft identitásplatformja nyílt szabványokat, többek között OAuth2-t és OpenID Connectet használ. Ezek a szabványok lehetővé teszik bármilyen olyan kódtár használatát, amelyet integrálni szeretne az Azure Active Directory B2C-be. Ha más kódtárakat szeretne használni, a következőhöz hasonló bemutatót is használhat, amely bemutatja, hogyan konfigurálhat harmadik féltől származó kódtárat a Microsoft Identity platformhoz való kapcsolódáshoz. [A RFC6749 OAuth2 specifikációt](https://tools.ietf.org/html/rfc6749) megvalósító könyvtárak többsége a Microsoft Identity platformhoz tud csatlakozni.

> [!WARNING]
> A Microsoft nem biztosít javításokat harmadik féltől származó kódtárak számára, és nem végezte el a könyvtárak áttekintését. Ez a minta egy AppAuth nevű harmadik féltől származó könyvtárat használ, amely az Azure AD B2C segítségével alapszintű forgatókönyvekben való kompatibilitásra lett tesztelve. A problémákat és a szolgáltatási kérelmeket a könyvtár nyílt forráskódú projektjeire kell irányítani. További információt [ebben a cikkben](../active-directory/develop/reference-v2-libraries.md) talál.
>
>

Ha csak most ismerkedik az OAuth2 vagy az OpenID Connect használatával, előfordulhat, hogy nem fogja tökéletesen érteni a konfigurációs lépéseket. Ebben az esetben javasoljuk, hogy olvassa el [a protokoll áttekintését, amelyet itt talál](protocols-overview.md).

## <a name="get-an-azure-ad-b2c-directory"></a>Az Azure AD B2C-címtár beszerzése

Az Azure AD B2C használatához létre kell hoznia egy címtárat vagy bérlőt. A címtárban tárolhatja az összes felhasználót, alkalmazást, csoportot és más elemeket. Ha még nem tette meg, [hozzon létre most egy B2C-címtárat](tutorial-create-tenant.md), mielőtt továbblépne.

## <a name="create-an-application"></a>Alkalmazás létrehozása

Ezután regisztráljon egy alkalmazást a Azure AD B2C-bérlőben. Ez biztosítja az Azure AD számára az alkalmazással való biztonságos kommunikációhoz szükséges információkat.

[!INCLUDE [active-directory-b2c-appreg-native](../../includes/active-directory-b2c-appreg-native.md)]

Jegyezze fel az **alkalmazás (ügyfél) azonosítóját** egy későbbi lépésben való használatra.

Az egyéni átirányítási URI-t is rögzítheti egy későbbi lépésben való használatra. Például: `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`.

## <a name="create-your-user-flows"></a>Felhasználói folyamatok létrehozása

Azure AD B2C minden felhasználói élményt egy [felhasználói folyamat](user-flow-overview.md)definiál, amely az Azure ad viselkedését vezérlő szabályzatok összessége. Az alkalmazáshoz be kell jelentkeznie és regisztrálnia kell a felhasználói folyamatot. A felhasználói folyamat létrehozásakor ügyeljen arra, hogy:

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
* Felhasználói folyamat neve (pl. B2C \_ 1 \_ SignUpIn)

Ha úgy dönt, hogy automatikusan felderíti az engedélyezési és jogkivonat-végponti URI-ket, le kell kérnie az adatokat a felderítési URI-ból. A felderítési URI a `<tenant-id>` és a következő URL-címben való lecserélése után hozható létre `<policy-name>` :

```java
String mDiscoveryURI = "https://<tenant-name>.b2clogin.com/<tenant-id>/<policy-name>/v2.0/.well-known/openid-configuration";
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

Ahelyett, hogy a felderítést az engedélyezési és jogkivonat-végponti URI-k beszerzéséhez használja, explicit módon megadhatja azokat az `<tenant-id>` `<policy-name>` alábbi URL-címekre:

```java
String mAuthEndpoint = "https://<tenant-name>.b2clogin.com/<tenant-id>/<policy-name>/oauth2/v2.0/authorize";

String mTokenEndpoint = "https://<tenant-name>.b2clogin.com/<tenant-id>/<policy-name>/oauth2/v2.0/token";
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