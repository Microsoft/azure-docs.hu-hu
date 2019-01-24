---
title: Android-alkalmazás használatával az Azure Active Directory B2C egy token beszerzése |} A Microsoft Docs
description: Ebből a cikkből megtudhatja, hogyan hozhat létre Android-alkalmazás, amely az Azure Active Directory B2C az AppAuth használatával felhasználói identitásokat kezelhet és hitelesítheti a felhasználókat.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 8a9f6e52f589ab68c3bf8b394d6875082bc0ddf1
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2019
ms.locfileid: "54845792"
---
# <a name="sign-in-using-an-android-application-in-azure-active-directory-b2c"></a>Jelentkezzen be az Azure Active Directory B2C egy Android-alkalmazás használatával

A Microsoft identitásplatformja nyílt szabványokat, többek között OAuth2-t és OpenID Connectet használ. Ezeknek a szabványoknak való integrálása az Azure Active Directory B2C szeretné bármilyen típusú kódtárat teszi lehetővé. Segítséget a könyvtárak használatához, az alábbihoz hasonló bemutató segítségével bemutatják, hogyan lehet csatlakozni a Microsoft identity platform 3. fél szalagtárak konfigurálása. Használó legtöbb kódtár [RFC6749 OAuth2 specifikációt](https://tools.ietf.org/html/rfc6749) csatlakozhat a Microsoft Identity platform.

> [!WARNING]
> A Microsoft nem biztosít javításokat a 3. fél tárak, és nem tett át ezeket a kódtárakat. Ez a minta egy 3. fél kódtár, hogy tesztelték az AppAuth nevű alapszintű forgatókönyvekben kompatibilitás érdekében az Azure AD B2C-vel használ. Problémák és a funkciókérések legyen átirányítva a tár nyílt forráskódú projekt. Lásd: [Ez a cikk](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries) további információt.  
>
>

Ha csak most ismerkedik az OAuth2 vagy az OpenID Connect használatával, előfordulhat, hogy nem fogja tökéletesen érteni a konfigurációs lépéseket. Ebben az esetben javasoljuk, hogy olvassa el [a protokoll áttekintését, amelyet itt talál](active-directory-b2c-reference-protocols.md).

## <a name="get-an-azure-ad-b2c-directory"></a>Az Azure AD B2C-címtár beszerzése

Az Azure AD B2C használatához létre kell hoznia egy címtárat vagy bérlőt. A címtárban tárolhatja az összes felhasználót, alkalmazást, csoportot és más elemeket. Ha még nem tette meg, [hozzon létre most egy B2C-címtárat](tutorial-create-tenant.md), mielőtt továbblépne.

## <a name="create-an-application"></a>Alkalmazás létrehozása

A következő lépésben hozzon létre egy alkalmazást a B2C-címtárban. Ez biztosítja az alkalmazással történő biztonságos kommunikációhoz szükséges információkat az Azure AD számára. Mobilalkalmazás létrehozása, hajtsa végre a [ezek az utasítások](active-directory-b2c-app-registration.md). Ügyeljen arra, hogy:

* Például egy **natív ügyfél** az alkalmazásban.
* Másolja az alkalmazáshoz rendelt **alkalmazásazonosítót**. Ez később lesz szüksége.
* Állítsa be egy natív ügyfél **átirányítási URI-t** (pl. com.onmicrosoft.fabrikamb2c.exampleapp://oauth/redirect). Később erre is szüksége lesz.

## <a name="create-your-user-flows"></a>A felhasználói folyamatok létrehozása

Az Azure AD B2C felhasználói élményeket által meghatározott egy [felhasználói folyamat](active-directory-b2c-reference-policies.md), azaz az Azure AD működését szabályozó házirendjei. Az alkalmazás használatához a bejelentkezési és regisztrációs felhasználói folyamata. Ha a felhasználói folyamatot hoz létre, ügyeljen arra, hogy:

* Válassza ki a **megjelenítendő név** a felhasználói folyamat előfizetési attribútumaként.
* Válassza ki a **megjelenítendő név** és **Objektumazonosító** alkalmazásjogcímek minden felhasználó flow-ban. Kiválaszthat egyéb jogcímeket is.
* Másolás a **neve** az egyes felhasználói folyamatok létrehozása után. A névnek a következő előtaggal kell rendelkeznie: `b2c_1_`.  A felhasználói interakciósorozat neve később szüksége lesz.

A felhasználói folyamatok létrehozását követően készen áll az alkalmazás elkészítésére.

## <a name="download-the-sample-code"></a>A mintakód letöltése

Biztosítunk egy mintát, amely az AppAuth használja az Azure AD B2C-vel [a Githubon](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c). Töltse le a kódot, és futtathatja. Gyorsan megkezdheti a saját Azure AD B2C konfiguráció használatával a következő témakör utasításait követve saját alkalmazással az [README.md](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c/blob/master/README.md).

A minta a minta által biztosított módosítás [az AppAuth](https://openid.github.io/AppAuth-Android/). Tekintse meg a lap további információ az AppAuth és annak szolgáltatásait.

## <a name="modifying-your-app-to-use-azure-ad-b2c-with-appauth"></a>Az alkalmazás Azure AD B2C használata az AppAuth módosítása

> [!NOTE]
> Az AppAuth támogatja az Android API 16 (Jellybean) vagy újabb verzió. Javasoljuk, hogy használja az API 23 vagy újabb verzió.
>

### <a name="configuration"></a>Konfiguráció

Kommunikációs konfigurálhatja az Azure AD B2C-vel, vagy adja meg a felderítés URI-t vagy az engedélyezési végpont és a jogkivonat-végpont URI-k meghatározásával. Mindkét esetben szüksége lesz a következő információkat:

* Bérlő azonosítója (Példa: contoso.onmicrosoft.com)
* Felhasználói interakciósorozat neve (Példa: B2C\_1\_SignUpIn)

Ha automatikus észleléséhez, az engedélyezési és jogkivonat-végpont URI-k, szüksége lesz az adatok beolvasása a felderítésből URI. A felderítést, és cserélje le a bérlő URI-t hozhat létre\_Azonosítóját és a szabályzat\_az alábbi URL-cím neve:

```java
String mDiscoveryURI = "https://<Tenant_name>.b2clogin.com/<Tenant_ID>/v2.0/.well-known/openid-configuration?p=<Policy_Name>";
```

Ezután az engedélyezési és jogkivonat-végpont URI-k megszerzésére és AuthorizationServiceConfiguration objektum létrehozása a következő futtatásával:

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

Felderítési juthat hozzá az engedélyezési és jogkivonat-végpont URI-k helyett azt is beállíthatja azokat explicit módon a bérlő lecserélésével\_Azonosítóját és a szabályzat\_neve az URL-címet az alábbi:

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

### <a name="authorizing"></a>Engedélyezés folyamatban

Miután konfigurálása, vagy egy engedélyezési szolgáltatás konfigurációjának lekérése, egy engedélyezési kérést lehet létrehozni. A kérelem létrehozásához szüksége lesz a következő információkat:

* Ügyfél-azonosító (pl. 00000000-0000-0000-0000-000000000000)
* Átirányítási URI-t egy egyéni sémával (pl. com.onmicrosoft.fabrikamb2c.exampleapp://oauthredirect)

Mindkettőt kell a rendszer mentette, amikor [regisztrálja az alkalmazást](#create-an-application).

```java
AuthorizationRequest req = new AuthorizationRequest.Builder(
    config,
    clientId,
    ResponseTypeValues.CODE,
    redirectUri)
    .build();
```

Tekintse meg a [az AppAuth útmutató](https://openid.github.io/AppAuth-Android/) való fejezze be a folyamatot. Ha a használatának gyors megkezdése egy működő alkalmazást van szüksége, tekintse meg az [ebben a mintában](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c). Kövesse a [README.md](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c/blob/master/README.md) , adja meg a saját Azure AD B2C konfiguráció.

