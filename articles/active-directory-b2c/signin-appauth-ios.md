---
title: AppAuth használata iOS-alkalmazásokban
titleSuffix: Azure AD B2C
description: AppAuth-t használó iOS-alkalmazás létrehozása a Azure Active Directory B2C a felhasználói identitások kezeléséhez és a felhasználók hitelesítéséhez.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/30/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6064bd2c62922abea44508b8bf6cdfa3e7ecbc92
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94953304"
---
# <a name="azure-ad-b2c-sign-in-using-an-ios-application"></a>Azure AD B2C: bejelentkezés iOS-alkalmazás használatával

A Microsoft identitásplatformja nyílt szabványokat, többek között OAuth2-t és OpenID Connectet használ. A nyílt szabványú protokoll használatával további fejlesztői lehetőségek közül választhat, amikor kijelöl egy könyvtárat a szolgáltatásokkal való integrációhoz. Ezt a bemutatót és másokat, például a Microsoft Identity platformhoz kapcsolódó alkalmazások írásához segítséget nyújt a fejlesztőknek. [A RFC6749 OAuth2 SPECT](https://tools.ietf.org/html/rfc6749) megvalósító kódtárak többsége képes csatlakozni a Microsoft Identity platformhoz.

> [!WARNING]
> A Microsoft nem biztosít javításokat harmadik féltől származó tárak számára, és nem végzett felülvizsgálatot ezen könyvtárakon. Ez a minta egy AppAuth nevű harmadik féltől származó könyvtárat használ, amely a Azure AD B2C segítségével alapszintű forgatókönyvekben való kompatibilitásra lett tesztelve. A problémákat és a szolgáltatási kérelmeket a könyvtár nyílt forráskódú projektjeire kell irányítani. További információkért tekintse meg [ezt a cikket](../active-directory/develop/reference-v2-libraries.md).
>
>

Ha még nem ismeri a OAuth2 vagy az OpenID Connectet, akkor előfordulhat, hogy a minta konfigurációjának nagy része nem lenne értelme. Ebben az esetben javasoljuk, hogy olvassa el [a protokoll áttekintését, amelyet itt talál](protocols-overview.md).

## <a name="get-an-azure-ad-b2c-directory"></a>Az Azure AD B2C-címtár beszerzése
Az Azure AD B2C használatához létre kell hoznia egy címtárat vagy bérlőt. A címtár minden felhasználó, alkalmazás, csoport és egyéb tároló. Ha még nem tette meg, [hozzon létre most egy B2C-címtárat](tutorial-create-tenant.md), mielőtt továbblépne.

## <a name="create-an-application"></a>Alkalmazás létrehozása

Ezután regisztráljon egy alkalmazást a Azure AD B2C-bérlőben. Ez biztosítja az Azure AD számára az alkalmazással való biztonságos kommunikációhoz szükséges információkat.

[!INCLUDE [active-directory-b2c-appreg-native](../../includes/active-directory-b2c-appreg-native.md)]

Jegyezze fel az **alkalmazás (ügyfél) azonosítóját** egy későbbi lépésben való használatra.

Az egyéni átirányítási URI-t is rögzítheti egy későbbi lépésben való használatra. Például: `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`.

## <a name="create-your-user-flows"></a>Felhasználói folyamatok létrehozása
Azure AD B2C minden felhasználói élményt egy [felhasználói folyamat](user-flow-overview.md)definiál. Ez az alkalmazás egyetlen identitási élményt tartalmaz: egy kombinált bejelentkezést és egy regisztrációt. A felhasználói folyamat létrehozásakor ügyeljen arra, hogy:

* A **regisztrálási attribútumok** területen válassza ki az attribútum **megjelenítendő nevét**.  Más attribútumok is kiválaszthatók.
* Az **alkalmazás jogcímei** területen válassza ki a jogcímek **megjelenítendő nevét** és a **felhasználó objektumának azonosítóját**. Más jogcímeket is kijelölhet.
* A létrehozás után másolja az egyes felhasználói folyamatok **nevét** . A felhasználói folyamat neve `b2c_1_` a felhasználói folyamat mentésekor előtaggal van ellátva.  A felhasználói folyamat nevét később kell megadnia.

Miután létrehozta a felhasználói folyamatokat, készen áll az alkalmazás létrehozására.

## <a name="download-the-sample-code"></a>A mintakód letöltése
Olyan munkamintaot kaptunk, amely a AppAuth-t használja Azure AD B2C [a githubon](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c). Letöltheti a kódot, és futtathatja. Ha saját Azure AD B2C bérlőt szeretne használni, kövesse a [readme.MD](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c/blob/master/README.md)utasításait.

Ezt a mintát a [githubon lévő iOS AppAuth projekt](https://github.com/openid/AppAuth-iOS)readme utasításai követték. A minta és a könyvtár működésével kapcsolatos további részletekért lásd a AppAuth README fájlt a GitHubon.

## <a name="modifying-your-app-to-use-azure-ad-b2c-with-appauth"></a>Az alkalmazás módosítása Azure AD B2C használatára a AppAuth

> [!NOTE]
> A AppAuth az iOS 7-es vagy újabb verzióját támogatja.  Ahhoz azonban, hogy támogassa a közösségi bejelentkezéseket a Google-on, SFSafariViewController van szükség, amelyhez iOS 9-es vagy újabb verzió szükséges.
>

### <a name="configuration"></a>Konfiguráció

Az Azure AD B2Cval való kommunikációt az engedélyezési végpont és a jogkivonat-végpont URI-k megadásával állíthatja be.  Az URI-k létrehozásához a következő információk szükségesek:
* Bérlő azonosítója (például contoso.onmicrosoft.com)
* Felhasználói folyamat neve (például B2C \_ 1 \_ SignUpIn)

A jogkivonat-végpont URI-ja a bérlői \_ azonosító és a szabályzat \_ nevének a következő URL-címben való lecserélésével hozható létre:

```objc
static NSString *const tokenEndpoint = @"https://<Tenant_name>.b2clogin.com/te/<Tenant_ID>/<Policy_Name>/oauth2/v2.0/token";
```

Az engedélyezési végpont URI-ja a bérlői \_ azonosító és a szabályzat nevének a \_ következő URL-címben való lecserélésével hozható létre:

```objc
static NSString *const authorizationEndpoint = @"https://<Tenant_name>.b2clogin.com/te/<Tenant_ID>/<Policy_Name>/oauth2/v2.0/authorize";
```

Futtassa a következő kódot a AuthorizationServiceConfiguration objektum létrehozásához:

```objc
OIDServiceConfiguration *configuration =
    [[OIDServiceConfiguration alloc] initWithAuthorizationEndpoint:authorizationEndpoint tokenEndpoint:tokenEndpoint];
// now we are ready to perform the auth request...
```

### <a name="authorizing"></a>Engedélyezése

Az engedélyezési szolgáltatás konfigurációjának konfigurálása vagy beolvasása után egy engedélyezési kérelem is létrehozható. A kérelem létrehozásához a következő információk szükségesek:

* Az ügyfél-azonosító (alkalmazás azonosítója), amelyet korábban rögzített. Például: `00000000-0000-0000-0000-000000000000`.
* A korábban rögzített egyéni átirányítási URI. Például: `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`.

[Az alkalmazás regisztrálásakor](#create-an-application)mindkét elemet el kell menteni.

```objc
OIDAuthorizationRequest *request =
    [[OIDAuthorizationRequest alloc] initWithConfiguration:configuration
                                                  clientId:kClientId
                                                    scopes:@[OIDScopeOpenID, OIDScopeProfile]
                                               redirectURL:[NSURL URLWithString:kRedirectUri]
                                              responseType:OIDResponseTypeCode
                                      additionalParameters:nil];

AppDelegate *appDelegate = (AppDelegate *)[UIApplication sharedApplication].delegate;
appDelegate.currentAuthorizationFlow =
    [OIDAuthState authStateByPresentingAuthorizationRequest:request
                                   presentingViewController:self
                                                   callback:^(OIDAuthState *_Nullable authState, NSError *_Nullable error) {
        if (authState) {
            NSLog(@"Got authorization tokens. Access token: %@", authState.lastTokenResponse.accessToken);
            [self setAuthState:authState];
        } else {
            NSLog(@"Authorization error: %@", [error localizedDescription]);
            [self setAuthState:nil];
        }
    }];
```

Ha úgy szeretné beállítani az alkalmazást, hogy az az egyéni sémával kezelje az átirányítást az URI-ra, frissítenie kell az "URL-sémák" listáját az info. plist fájlban:
* Nyissa meg az info. plist fájlt.
* Vigye az egérmutatót egy olyan sorra, mint a "köteg operációs rendszer típusa", majd kattintson a \+ szimbólumra.
* Nevezze át az új sor URL-típusait.
* Kattintson az "URL-típusok" bal oldalán található nyílra a fa megnyitásához.
* Kattintson a "0. elem" bal oldalán található nyílra a fa megnyitásához.
* Nevezze át a 0. tétel alá tartozó első tételt az "URL-sémák" értékre.
* Kattintson az "URL-sémák" bal oldalán található nyílra a fa megnyitásához.
* Az "érték" oszlopban van egy üres mező, amely a "0. elem" bal oldalán található az "URL-sémák" alatt.  Állítsa be az értéket az alkalmazás egyedi sémájára.  Az értéknek meg kell egyeznie a redirectURL a OIDAuthorizationRequest objektum létrehozásakor használt sémával.  A mintában a "com. onmicrosoft. fabrikamb2c. exampleapp" séma van használatban.

Tekintse át az [AppAuth útmutatót](https://openid.github.io/AppAuth-iOS/) , amely bemutatja, hogyan végezheti el a folyamat hátralévő részét. Ha gyorsan kell megkezdenie egy működő alkalmazás használatát, tekintse meg [a mintát](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c). A saját Azure AD B2C konfigurációjának megadásához kövesse a [readme.MD](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c/blob/master/README.md) lépéseit.

Mindig nyitottak vagyunk visszajelzésre és javaslatokra! Ha problémája merül fel a jelen cikkben vagy a tartalom javítására vonatkozó javaslatokkal, a lap alján értékeljük a visszajelzését. A szolgáltatási kérelmek esetében adja hozzá őket a [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c).