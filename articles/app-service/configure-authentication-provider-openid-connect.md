---
title: OpenID Connect-szolgáltató konfigurálása (előzetes verzió)
description: Megtudhatja, hogyan konfigurálhat egy OpenID Connect-szolgáltatót App Service vagy Azure Functions alkalmazáshoz tartozó identitás-szolgáltatóként.
ms.topic: article
ms.date: 07/08/2020
ms.reviewer: mahender
ms.openlocfilehash: e8112f2dc20175e81cfa8388440b2d9aef6a419c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "90983878"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-login-using-an-openid-connect-provider-preview"></a>Az App Service- vagy az Azure Functions-alkalmazás konfigurálása OpenID Connect-szolgáltatóval való bejelentkezéshez (előzetes verzió)

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Ez a cikk bemutatja, hogyan konfigurálhatja Azure App Service vagy Azure Functions az [OpenID Connect specifikációjának](https://openid.net/connect/)megfelelő egyéni hitelesítési szolgáltató használatára. Az OpenID Connect (OIDC) a számos Identity Provider (IDP) által használt iparági szabvány. Nem kell megismernie a specifikáció részleteit ahhoz, hogy az alkalmazást egy Híves IDENTITÁSSZOLGÁLTATÓ használatára konfigurálja.

Az alkalmazás beállítható úgy, hogy egy vagy több OIDC-szolgáltatót használjon. Mindegyiknek egyedi nevet kell adni a konfigurációban, és csak egy lehet az alapértelmezett átirányítási célként szolgálni.

> [!CAUTION]
> Az OpenID Connect-szolgáltató engedélyezése letilthatja az alkalmazás App Service hitelesítési/engedélyezési funkciójának kezelését bizonyos ügyfeleken, például a Azure Portal, az Azure CLI és a Azure PowerShell használatával. A funkció egy új API-felületre támaszkodik, amely az előzetes verzióban még nem szerepel a felügyeleti élményekben.

## <a name="register-your-application-with-the-identity-provider"></a><a name="register"> </a>Az alkalmazás regisztrálása az identitás-szolgáltatónál

A szolgáltató megköveteli, hogy regisztrálja az alkalmazás adatait. Tekintse meg az adott szolgáltatóra vonatkozó utasításokat. Össze kell gyűjtenie az alkalmazáshoz tartozó **ügyfél-azonosítót** és az **ügyfél titkos kulcsát** .

> [!IMPORTANT]
> Az alkalmazás titkos kulcsa egy fontos biztonsági hitelesítő adat. Ezt a titkos kódot Ne ossza meg senkivel, vagy Ossza szét egy ügyfélalkalmazás alkalmazásán belül.
>

> [!NOTE]
> Egyes szolgáltatók további lépéseket igényelhetnek a konfigurációjuk és az általuk megadott értékek használatával. Például az Apple olyan titkos kulcsot biztosít, amely nem szerepel a OIDC-ügyfél titkos kulcsában, és ehelyett olyan JWT kell használnia, amely az alkalmazás konfigurációjában megadott titokként van kezelve (lásd a [Bejelentkezés az Apple-](https://developer.apple.com/documentation/sign_in_with_apple/generate_and_validate_tokens)mel című témakör "az ügyfél titkának létrehozása" című szakaszát).
>

Adja hozzá az ügyfél titkát [alkalmazási beállításként](./configure-common.md#configure-app-settings) az alkalmazáshoz, a kívánt beállítás neve alapján. Később jegyezze fel ezt a nevet.

Emellett szüksége lesz az OpenID Connect metaadatainak a szolgáltatóhoz. Ezt gyakran egy [konfigurációs metaadat-dokumentum](https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderConfig)teszi elérhetővé, amely a szolgáltató kiállítói URL-címének utótagja `/.well-known/openid-configuration` . A konfigurációs URL-cím összegyűjtése.

Ha nem tud konfigurációs metaadat-dokumentumot használni, külön kell összegyűjtenie a következő értékeket:

- A kiállító URL-címe (néha látható `issuer` )
- A [OAuth 2,0 engedélyezési végpontja](https://tools.ietf.org/html/rfc6749#section-3.1) (esetenként a `authorization_endpoint` következőként látható)
- A [OAuth 2,0 jogkivonat-végpont](https://tools.ietf.org/html/rfc6749#section-3.2) (néha látható `token_endpoint` )
- A [OAuth 2,0 JSON Webkulcs-készlet](https://tools.ietf.org/html/rfc8414#section-2) dokumentumának URL-címe (néha látható `jwks_uri` )

## <a name="add-provider-information-to-your-application"></a><a name="configure"> </a>Szolgáltatói adatok hozzáadása az alkalmazáshoz

> [!NOTE]
> A szükséges konfiguráció új API-formátumban van, jelenleg csak a [fájl alapú konfiguráció (előzetes verzió)](.\app-service-authentication-how-to.md#config-file)támogatja. Az alábbi lépéseket kell követnie egy ilyen fájl használatával.

Ez a szakasz végigvezeti a konfiguráció frissítésén, hogy tartalmazza az új IDENTITÁSSZOLGÁLTATÓ. Egy példa a konfigurációra.

1. Az `identityProviders` objektumon belül adjon hozzá egy `openIdConnectProviders` objektumot, ha még nem létezik ilyen.
1. Az `openIdConnectProviders` objektumon belül adjon hozzá egy kulcsot az új szolgáltatóhoz. Ez egy felhasználóbarát név, amely a szolgáltatóra hivatkozik a konfiguráció többi részén. Ha például azt szeretné, hogy az összes kérelem hitelesítése ezzel a szolgáltatóval megtörténjen, a "RedirectToLoginPage" értékre kell állítani, `globalValidation.unauthenticatedClientAction` és `redirectToProvider` erre a rövid névre kell beállítani.
1. Rendeljen hozzá egy objektumot a kulcshoz egy `registration` objektumon belül, és opcionálisan egy `login` objektumot:
    
    ```json
    "myCustomIDP" : {
       "registration" : {},
       "login": {
             "nameClaimType": "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name",
             "scope": [],
             "loginParameterNames": [],
       }
    }
    ```

1. A regisztrációs objektumban állítsa be a értéket `clientId` a begyűjtött ügyfél-azonosítóra, állítsa be `clientCredential.secretSettingName` annak az alkalmazásnak a nevét, ahol az ügyfél titkát tárolja, és hozzon létre egy `openIdConnectConfiguration` objektumot:

    ```json
    "registration": {
      "clientId": "bd96cf8a-3f2b-4806-b180-d3c5fd11a2be",
      "clientCredential": {
         "secretSettingName": "IDP_CLIENT_SECRET"
      },
      "openIdConnectConfiguration" : {}
    }
    ```

1. Az `openIdConnectConfiguration` objektumon belül adja meg a korábban összegyűjtött OpenID Connect-metaadatokat. Ennek két lehetősége van: az összegyűjtött információk alapján:

    - Állítsa a `wellKnownOpenIdConfiguration` tulajdonságot a korábban összegyűjtött konfigurációs metaadatok URL-címére.
    - Azt is megteheti, hogy az alábbi négy egyedi értéket adja meg:
        - Beállítás `issuer` a kiállító URL-címére
        - Beállítás `authorizationEndpoint` az engedélyezési végpontra
        - Beállítás `tokenEndpoint` a jogkivonat-végpontra
        - Beállítás a `certificationUri` JSON Webkulcs-készlet dokumentumának URL-címére

    Ez a két lehetőség kölcsönösen kizárható.

Ha ezt a konfigurációt beállította, készen áll az OpenID Connect-szolgáltató használatára az alkalmazásban való hitelesítéshez.

A konfiguráció például az alábbihoz hasonló lehet (az Apple-be való bejelentkezéssel példaként, ahol a APPLE_GENERATED_CLIENT_SECRET-beállítás az [Apple-dokumentáció](https://developer.apple.com/documentation/sign_in_with_apple/generate_and_validate_tokens)alapján generált JWT mutat):

```json
{
    "platform": {
        "enabled": true
    },
    "globalValidation": {
        "redirectToProvider": "apple",
        "unauthenticatedClientAction": "RedirectToLoginPage"
    },
    "identityProviders": {
        "openIdConnectProviders": {
            "apple": {
                "registration": {
                    "clientId": "com.contoso.example.client",
                    "clientCredential": {
                        "secretSettingName": "APPLE_GENERATED_CLIENT_SECRET"
                    },
                    "openIdConnectConfiguration": {
                        "wellKnownOpenIdConfiguration": "https://appleid.apple.com/.well-known/openid-configuration"
                    }
                },
                "login": {
                    "nameClaimType": "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name",
                    "scope": [],
                    "loginParameterNames": []
                }
            }
        }
    },
    "login": {
        "tokenStore": {
            "enabled": true
        }
    }     
}
```

## <a name="next-steps"></a><a name="related-content"> </a>Következő lépések

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]
