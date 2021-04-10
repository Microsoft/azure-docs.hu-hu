---
title: Azure AD B2C (MSAL Android) | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg, hogy milyen szempontokat kell figyelembe venni, amikor az Androidhoz készült Microsoft Authentication Library (MSAL) használatával Azure AD B2Ct használ. Android
services: active-directory
author: iambmelt
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 9/18/2019
ms.author: brianmel
ms.reviewer: rapong
ms.custom: aaddev
ms.openlocfilehash: 1a9b9481d0b4086505bbfd3c2cd654ce228d1ae2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101688875"
---
# <a name="use-msal-for-android-with-b2c"></a>Az Android rendszerhez készült MSAL használata B2C-vel

A Microsoft Authentication Library (MSAL) lehetővé teszi az alkalmazások fejlesztői számára, hogy [Azure Active Directory B2C (Azure ad B2C)](../../active-directory-b2c/index.yml)használatával hitelesítsék a felhasználókat a közösségi és helyi identitásokkal. Azure AD B2C egy Identitáskezelés szolgáltatás. Ezzel testreszabhatja és szabályozhatja, hogy az ügyfelek hogyan regisztrálhatnak, jelentkezhetnek be és kezelhetik a profiljaikat, amikor az alkalmazásaikat használják.

## <a name="choosing-a-compatible-authorization_user_agent"></a>Kompatibilis authorization_user_agent kiválasztása
A B2C Identity Management rendszer számos közösségi fiókkal (például Google, Facebook, Twitter és Amazon) támogatja a hitelesítést. Ha azt tervezi, hogy támogatja az ilyen típusú fiókokat az alkalmazásban, akkor azt javasoljuk, hogy a MSAL nyilvános ügyfélalkalmazás a vagy a érték használatára konfigurálja a `DEFAULT` `BROWSER` jegyzékfájlt, [`authorization_user_agent`](msal-configuration.md#authorization_user_agent) mivel a korlátozások miatt a Webview-alapú hitelesítés bizonyos külső identitás-szolgáltatókkal való használatát tiltja.

## <a name="configure-known-authorities-and-redirect-uri"></a>Ismert hatóságok és átirányítási URI konfigurálása

Az Android rendszerhez készült MSAL-ben a B2C-szabályzatok (felhasználói útvonalak) önálló hatóságokként vannak konfigurálva.

Olyan B2C-alkalmazás, amely két házirenddel rendelkezik:
- Regisztráció/bejelentkezés
    * Úgynevezett `B2C_1_SISOPolicy`
- Profil szerkesztése
    * Úgynevezett `B2C_1_EditProfile`

Az alkalmazás konfigurációs fájlja kettőt deklarál `authorities` . Egyet az egyes szabályzatokhoz. Az `type` egyes hatóságok tulajdonsága `B2C` .

>Megjegyzés: a `account_mode` -t a B2C-alkalmazások esetében **többszörösre** kell beállítani. Tekintse meg a dokumentációt, ahol további információkat talál a [több fiókkal rendelkező nyilvános ügyfélalkalmazások alkalmazásáról](./single-multi-account.md#multiple-account-public-client-application).

### `app/src/main/res/raw/msal_config.json`

```json
{
  "client_id": "<your_client_id_here>",
  "redirect_uri": "<your_redirect_uri_here>",
  "account_mode" : "MULTIPLE",
  "authorization_user_agent" : "DEFAULT",
  "authorities": [
    {
      "type": "B2C",
      "authority_url": "https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_SISOPolicy/",
      "default": true
    },
    {
      "type": "B2C",
      "authority_url": "https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_EditProfile/"
    }
  ]
}
```

A `redirect_uri` -t regisztrálni kell az alkalmazás konfigurációjában, valamint a-ben az  `AndroidManifest.xml` átirányítás támogatásához az [engedélyezési kód engedélyezése folyamat](../../active-directory-b2c/authorization-code-flow.md)során.

## <a name="initialize-ipublicclientapplication"></a>IPublicClientApplication inicializálása

`IPublicClientApplication` a egy gyári metódussal jön létre, amely lehetővé teszi az alkalmazás konfigurációjának aszinkron elemzését.

```java
PublicClientApplication.createMultipleAccountPublicClientApplication(
    context, // Your application Context
    R.raw.msal_config, // Id of app JSON config
    new IPublicClientApplication.ApplicationCreatedListener() {
        @Override
        public void onCreated(IMultipleAccountPublicClientApplication pca) {
            // Application has been initialized.
        }

        @Override
        public void onError(MsalException exception) {
            // Application could not be created.
            // Check Exception message for details.
        }
    }
);
```

## <a name="interactively-acquire-a-token"></a>Jogkivonat interaktív beszerzése

Ha a tokent interaktív módon szeretné beszerezni a MSAL, hozzon létre egy `AcquireTokenParameters` példányt, és adja meg a `acquireToken` metódusnak. Az alábbi jogkivonat-kérelem a `default` szolgáltatót használja.

```java
IMultipleAccountPublicClientApplication pca = ...; // Initialization not shown

AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(activity)
    .withScopes(Arrays.asList("https://contoso.onmicrosoft.com/contosob2c/read")) // Provide your registered scope here
    .withPrompt(Prompt.LOGIN)
    .callback(new AuthenticationCallback() {
        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            // Token request was successful, inspect the result
        }

        @Override
        public void onError(MsalException exception) {
            // Token request was unsuccessful, inspect the exception
        }

        @Override
        public void onCancel() {
            // The user cancelled the flow
        }
    }).build();

pca.acquireToken(parameters);
```

## <a name="silently-renew-a-token"></a>Jogkivonat megújítása csendes módban

Ha a tokent a MSAL-mel csendben szeretné beszerezni, hozzon létre egy `AcquireTokenSilentParameters` példányt, és adja meg a `acquireTokenSilentAsync` metódusnak. A `acquireToken` metódussal ellentétben a `authority` rendszernek meg kell adni a token csendes beszerzését.

```java
IMultipleAccountPublicClientApplication pca = ...; // Initialization not shown
AcquireTokenSilentParameters parameters = new AcquireTokenSilentParameters.Builder()
    .withScopes(Arrays.asList("https://contoso.onmicrosoft.com/contosob2c/read")) // Provide your registered scope here
    .forAccount(account)
    // Select a configured authority (policy), mandatory for silent auth requests
    .fromAuthority("https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_SISOPolicy/")
    .callback(new SilentAuthenticationCallback() {
        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            // Token request was successful, inspect the result
        }

        @Override
        public void onError(MsalException exception) {
            // Token request was unsuccessful, inspect the exception
        }
    })
    .build();

pca.acquireTokenSilentAsync(parameters);
```

## <a name="specify-a-policy"></a>Házirend meghatározása

Mivel a B2C-szabályzatok külön hatóságokként jelennek meg, az alapértelmezetttől eltérő házirendet nem kell megadnia egy `fromAuthority` záradék létrehozásával `acquireToken` vagy paraméterekkel való megadásával `acquireTokenSilent` .  Például:

```java
AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(activity)
    .withScopes(Arrays.asList("https://contoso.onmicrosoft.com/contosob2c/read")) // Provide your registered scope here
    .withPrompt(Prompt.LOGIN)
    .callback(...) // provide callback here
    .fromAuthority("<url_of_policy_defined_in_configuration_json>")
    .build();
```

## <a name="handle-password-change-policies"></a>Jelszó-módosítási házirendek kezelése

A helyi fiók regisztrálása vagy bejelentkezési felhasználói folyamata egy "**elfelejtett jelszót**" mutat be hivatkozással. Ha erre a hivatkozásra kattint, nem indítja el automatikusan a jelszó-visszaállítás felhasználói folyamatát.

Ehelyett a hibakódot a `AADB2C90118` rendszer visszaadja az alkalmazásnak. Az alkalmazásnak ezt a hibakódot egy adott felhasználói folyamat futtatásával kell kezelnie, amely alaphelyzetbe állítja a jelszót.

Ha a jelszó-visszaállítási hibakódot szeretné elfogni, a következő implementáció használható a rendszerben `AuthenticationCallback` :

```java
new AuthenticationCallback() {

    @Override
    public void onSuccess(IAuthenticationResult authenticationResult) {
        // ..
    }

    @Override
    public void onError(MsalException exception) {
        final String B2C_PASSWORD_CHANGE = "AADB2C90118";

        if (exception.getMessage().contains(B2C_PASSWORD_CHANGE)) {
            // invoke password reset flow
        }
    }

    @Override
    public void onCancel() {
        // ..
    }
}
```

## <a name="use-iauthenticationresult"></a>IAuthenticationResult használata

A jogkivonat sikeres beszerzése egy `IAuthenticationResult` objektumot eredményez. A hozzáférési jogkivonatot, a felhasználói jogcímeket és a metaadatokat tartalmazza.

### <a name="get-the-access-token-and-related-properties"></a>Hozzáférési jogkivonat és a kapcsolódó tulajdonságok beszerzése

```java
// Get the raw bearer token
String accessToken = authenticationResult.getAccessToken();

// Get the scopes included in the access token
String[] accessTokenScopes = authenticationResult.getScope();

// Gets the access token's expiry
Date expiry = authenticationResult.getExpiresOn();

// Get the tenant for which this access token was issued
String tenantId = authenticationResult.getTenantId();
```

### <a name="get-the-authorized-account"></a>A hitelesítő fiók beszerzése

```java
// Get the account from the result
IAccount account = authenticationResult.getAccount();

// Get the id of this account - note for B2C, the policy name is a part of the id
String id = account.getId();

// Get the IdToken Claims
//
// For more information about B2C token claims, see reference documentation
// https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-tokens
Map<String, ?> claims = account.getClaims();

// Get the 'preferred_username' claim through a convenience function
String username = account.getUsername();

// Get the tenant id (tid) claim through a convenience function
String tenantId = account.getTenantId();
```

### <a name="idtoken-claims"></a>IdToken jogcímek

A IdToken visszaadott jogcímeket a biztonsági jogkivonat szolgáltatás (STS) tölti fel, nem pedig a MSAL. A használt identitásszolgáltató függően előfordulhat, hogy egyes jogcímek hiányoznak. Egyes IDP jelenleg nem nyújtják be a `preferred_username` jogcímet. Mivel ezt a jogcímet a MSAL használja a gyorsítótárazáshoz, a `MISSING FROM THE TOKEN RESPONSE` rendszer helyőrző értéket használ a helyén. A B2C-IdToken jogcímeivel kapcsolatos további információkért lásd: [a jogkivonatok áttekintése Azure Active Directory B2Cban](../../active-directory-b2c/tokens-overview.md#claims).

## <a name="managing-accounts-and-policies"></a>Fiókok és házirendek kezelése

A B2C az egyes szabályzatokat külön szolgáltatóként kezeli. Így a hozzáférési tokenek, a frissítési tokenek és az egyes szabályzatok által visszaadott azonosító tokenek nem módosíthatók. Ez azt jelenti, hogy az egyes házirendek egy külön objektumot adnak vissza `IAccount` , amelynek tokenjét nem lehet más szabályzatok meghívására használni.

Mindegyik szabályzat `IAccount` minden felhasználóhoz hozzáadja a gyorsítótárat. Ha egy felhasználó bejelentkezik egy alkalmazásba, és két házirendet hív meg, akkor két `IAccount` OK lesz. Ha el szeretné távolítani ezt a felhasználót a gyorsítótárból, minden szabályzatot meg kell hívnia `removeAccount()` .

Amikor megújítja a Szabályzathoz tartozó jogkivonatokat `acquireTokenSilent` , megadhatja, `IAccount` hogy a szabályzat korábbi hívásai milyen értéket adtak vissza  `AcquireTokenSilentParameters` . Egy másik házirend által visszaadott fiók megadása hibát eredményez.

## <a name="next-steps"></a>Következő lépések

További információ a Azure Active Directory B2Cról (Azure AD B2C) a [mi Azure Active Directory B2C?](../../active-directory-b2c/overview.md)
