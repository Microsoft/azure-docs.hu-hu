---
title: ADAL-ügyfélalkalmazások hibáinak kezelésével kapcsolatos ajánlott eljárások | Azure
description: A ADAL-ügyfélalkalmazások hibáinak kezelésére vonatkozó útmutatást és ajánlott eljárásokat biztosít.
services: active-directory
author: rwike77
manager: CelesteDG
ms.author: ryanwi
ms.service: active-directory
ms.subservice: azuread-dev
ms.custom: aaddev
ms.topic: how-to
ms.workload: identity
ms.date: 02/27/2017
ROBOTS: NOINDEX
ms.openlocfilehash: ad5595f7eebc8feca2f00a6f95e10c547ded9529
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "85383734"
---
# <a name="error-handling-best-practices-for-azure-active-directory-authentication-library-adal-clients"></a>Hiba történt a Azure Active Directory Authentication Library-(ADAL-) ügyfelekkel kapcsolatos ajánlott eljárások kezelésében

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Ez a cikk útmutatást nyújt a fejlesztők által észlelt hibák típusáról, amikor a ADAL használatával hitelesíti a felhasználókat. A ADAL használatakor számos esetben előfordulhat, hogy a fejlesztőnek be kell lépnie és kezelnie kell a hibákat. A megfelelő hibakezelés nagy végfelhasználói élményt biztosít, és korlátozza a végfelhasználók számára a bejelentkezéshez szükséges időt.

Ebben a cikkben a ADAL által támogatott egyes platformokra vonatkozó konkrét eseteket vizsgáljuk meg, valamint azt, hogy az alkalmazás hogyan kezelje az egyes eseteket. A hiba útmutatója két szélesebb kategóriára oszlik, a ADAL API-k által biztosított jogkivonat-beszerzési minták alapján:

- **AcquireTokenSilent**: az ügyfél csendesen kísérli meg a token lekérését (nincs felhasználói felület), és sikertelen lehet, ha a ADAL sikertelen. 
- **AcquireToken**: az ügyfél megkísérelheti a csendes beszerzést, de a bejelentkezést igénylő interaktív kérelmeket is elvégezhet.

> [!TIP]
> Érdemes naplózni az összes hibát és kivételt a ADAL és az Azure AD használatakor. A naplók nem csupán hasznosak az alkalmazás általános állapotának megértéséhez, de fontosak a szélesebb körű problémák hibakeresése során is. Habár előfordulhat, hogy az alkalmazás bizonyos hibákból helyreállítható, szélesebb körű tervezési problémákra is utalhat, amelyekkel a megoldás a kód módosítását igényli. 
> 
> A jelen dokumentumban ismertetett hiba feltételeinek megvalósításakor a korábban tárgyalt okok miatt naplózni kell a hibakódot és a leírást. A naplózási kódokra vonatkozó példákat lásd a [hibák és naplózási referenciák](#error-and-logging-reference) című részben. 
>

## <a name="acquiretokensilent"></a>AcquireTokenSilent

A AcquireTokenSilent kísérletet tesz a jogkivonat beszerzésére azzal a garanciával, hogy a végfelhasználó nem lát felhasználói felületet (UI). Számos esetben előfordulhat, hogy a csendes beszerzés meghiúsul, és az interaktív kérelmek vagy egy alapértelmezett kezelő használatával kell kezelni. A következő szakaszokban ismertetjük, hogy mikor és hogyan kell alkalmazni az egyes eseteket.

Az operációs rendszer által generált hibák halmaza, ami az alkalmazásra vonatkozó hibakezelés megkövetelését okozhatja. További információ: "operációs rendszer" hibái szakasz, [hiba és naplózási hivatkozás](#error-and-logging-reference). 

### <a name="application-scenarios"></a>Alkalmazáshasználati helyzetek

- [Natív ügyfélalkalmazások](../develop/developer-glossary.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json#native-client) (iOS, Android, .net Desktop vagy Xamarin)
- [Erőforrást](../develop/developer-glossary.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json#resource-server) hívó [webes ügyfélalkalmazások](../develop/developer-glossary.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json#web-client) (.net)

### <a name="error-cases-and-actionable-steps"></a>Hibák és műveletek végrehajtható lépései

Alapvetően a AcquireTokenSilent két előfordulása van:

| Case (Eset) | Leírás |
|------|-------------|
| **1. eset**: a hiba feloldható egy interaktív bejelentkezéssel | Az érvényes tokenek hiánya miatti hibák esetén interaktív kérelemre van szükség. Pontosabban, a gyorsítótár-keresés és az érvénytelen/lejárt frissítési jogkivonat megköveteli egy AcquireToken-hívás feloldását.<br><br>Ezekben az esetekben a végfelhasználónak meg kell adnia a bejelentkezést. Az alkalmazás azonnal dönthet úgy, hogy a végfelhasználói interakciót követően (például a bejelentkezési gomb elütése) vagy egy későbbi időpontban interaktív kérést végez. A választás az alkalmazás kívánt viselkedését határozza meg.<br><br>Tekintse meg a következő szakaszban található kódot az adott esethez és a diagnosztizált hibákhoz.|
| **2. eset**: a hiba nem oldható fel interaktív bejelentkezéssel | A hálózati és átmeneti/ideiglenes hibák vagy más hibák esetén az interaktív AcquireToken-kérések nem oldják meg a problémát. A szükségtelen interaktív bejelentkezési kérések is meghiúsítják a végfelhasználókat. A ADAL automatikusan próbálkozik egyetlen Újrapróbálkozással a legtöbb hiba esetén a AcquireTokenSilent meghibásodásakor.<br><br>Az ügyfélalkalmazás egy későbbi időpontban is megkísérelheti az újrapróbálkozást, de az alkalmazás viselkedése és a kívánt végfelhasználói élmény függ. Az alkalmazás például elvégezheti a AcquireTokenSilent néhány perc múlva, vagy bizonyos végfelhasználói műveletekre adott válaszként. Az azonnali újrapróbálkozás azt eredményezi, hogy az alkalmazás szabályozva lesz, és nem kell megkísérelni.<br><br>Ha egy későbbi újrapróbálkozás meghiúsul ugyanazzal a hibával, nem jelenti azt, hogy az ügyfélnek interaktív kérelmet kell tennie a AcquireToken használatával, mivel nem oldja meg a hibát.<br><br>Tekintse meg a következő szakaszban található kódot az adott esethez és a diagnosztizált hibákhoz. |

### <a name="net"></a>.NET

A következő útmutató példákat mutat be a ADAL metódusokkal együtt: 

- acquireTokenSilentAsync(...)
- acquireTokenSilentSync(...) 
- [elavult] acquireTokenSilent (...)
- [elavult] acquireTokenByRefreshToken (...) 

A kód a következőképpen valósítható meg:

```csharp
try{
    AcquireTokenSilentAsync(…);
} 

catch (AdalSilentTokenAcquisitionException e) {
    // Exception: AdalSilentTokenAcquisitionException
    // Caused when there are no tokens in the cache or a required refresh failed. 

    // Action: Case 1, resolvable with an interactive request. 
} 

catch(AdalServiceException e) {
    // Exception: AdalServiceException 
    // Represents an error produced by the STS. 
    // e.ErrorCode contains the error code and description, which can be used for debugging. 
    // NOTE: Do not code a dependency on the contents of the error description, as it can change over time.

    // Action: Case 2, not resolvable with an interactive request.
    // Attempt retry after a timed interval or user action.
} 
    
catch (AdalException e) {
    // Exception: AdalException 
    // Represents a library exception generated by ADAL .NET. 
    // e.ErrorCode contains the error code. 

    // Action: Case 2, not resolvable with an interactive request.
    // Attempt retry after a timed interval or user action.
    // Example Error: network_not_available, default case.
}
```

### <a name="android"></a>Android

A következő útmutató példákat mutat be a ADAL metódusokkal együtt: 

- acquireTokenSilentSync(...)
- acquireTokenSilentAsync(...)
- [elavult] acquireTokenSilent (...)

A kód a következőképpen valósítható meg:

```java
// *Inside callback*
public void onError(Exception e) {

    if (e instanceof AuthenticationException) {
        // Exception: AdalException
        // Represents a library exception generated by ADAL Android.
        // Error Code: e.getCode().

        // Errors: ADALError.ERROR_SILENT_REQUEST,
        // ADALError.AUTH_REFRESH_FAILED_PROMPT_NOT_ALLOWED,
        // ADALError.INVALID_TOKEN_CACHE_ITEM
        // Description: Request failed due to no tokens in
        // cache or failed a required refresh. 

        // Action: Case 1, resolvable with an interactive request. 

        // Action: Case 2, not resolvable with an interactive request.
        // Attempt retry after a timed interval or user action.
        // Example Errors: default case,
        // DEVICE_CONNECTION_IS_NOT_AVAILABLE, 
        // BROKER_AUTHENTICATOR_ERROR_GETAUTHTOKEN,
    }
}
```

### <a name="ios"></a>iOS

A következő útmutató példákat mutat be a ADAL metódusokkal együtt: 

- acquireTokenSilentWithResource(...)

A kód a következőképpen valósítható meg:

```objc
[context acquireTokenSilentWithResource:[ARGS], completionBlock:^(ADAuthenticationResult *result) {
    if (result.status == AD_FAILED) {
        if ([error.domain isEqualToString:ADAuthenticationErrorDomain]){
            // Exception: AD_FAILED 
            // Represents a library error generated by ADAL Objective-C.
            // Error Code: result.error.code

            // Errors: AD_ERROR_SERVER_REFRESH_TOKEN_REJECTED, AD_ERROR_CACHE_NO_REFRESH_TOKEN
            // Description: No tokens in cache or failed a required token refresh failed. 
            // Action: Case 1, resolvable with an interactive request. 

            // Error: AD_ERROR_CACHE_MULTIPLE_USERS
            // Description: There was ambiguity in the silent request resulting in multiple cache items.
            // Action: Special Case, application should perform another silent request and specify the user using ADUserIdentifier. 
            // Can be caused in cases of a multi-user application. 

            // Action: Case 2, not resolvable with an interactive request.
            // Attempt retry after some time or user action.
            // Example Errors: default case,
            // AD_ERROR_CACHE_BAD_FORMAT
        }
    }
}]
```

## <a name="acquiretoken"></a>AcquireToken

A AcquireToken a jogkivonatok lekérésére használt alapértelmezett ADAL metódus. Ha szükség van a felhasználói identitásra, a AcquireToken megkísérli a token lekérését a háttérben, majd szükség esetén megjeleníti a felhasználói felületet (kivéve, ha a PromptBehavior. soha nem lett átadva). Abban az esetben, ha az alkalmazás identitása kötelező, a AcquireToken kísérletet tesz a token beszerzésére, de nem jeleníti meg a felhasználói felületet, mert nincs végfelhasználó. 

A AcquireToken hibák kezelésekor a hibakezelés a platformtól függ, és az alkalmazás által elérni próbált forgatókönyvnek kell lennie. 

Az operációs rendszer hibákat is létrehozhat, amelyek az adott alkalmazástól függő hibakezelést igényelnek. További információ: "operációs rendszer hibái" a [hiba-és naplózási referenciában](#error-and-logging-reference). 

### <a name="application-scenarios"></a>Alkalmazáshasználati helyzetek

- Natív ügyfélalkalmazások (iOS, Android, .NET Desktop vagy Xamarin)
- Erőforrás-API-t (.NET) meghívó webalkalmazások
- Egyoldalas alkalmazások (JavaScript)
- Szolgáltatások közötti alkalmazások (.NET, Java)
  - Minden forgatókönyv, beleértve a következőt:
  - Bizonyos forgatókönyvek esetén

### <a name="error-cases-and-actionable-steps-native-client-applications"></a>Hibákkal és műveletekkel kapcsolatos lépések: natív ügyfélalkalmazások

Ha natív ügyfélalkalmazás épül fel, néhány hibakezelő esetet kell figyelembe vennie, amely a hálózati problémákkal, átmeneti hibákkal és egyéb platform-specifikus hibákkal kapcsolatos. A legtöbb esetben az alkalmazásnak nem kell azonnali újrapróbálkozásokat végrehajtania, hanem várnia kell a végfelhasználói interakcióra, amely a bejelentkezést kéri. 

Vannak olyan különleges esetek, amikor egyetlen újrapróbálkozás megoldhatja a problémát. Ha például egy felhasználónak engedélyeznie kell az eszközt az eszközön, vagy a kezdeti hiba után az Azure AD Broker letöltését kell végrehajtania. 

Meghibásodás esetén előfordulhat, hogy egy alkalmazás felhasználói felülete lehetővé teszi, hogy a végfelhasználó bizonyos interakciókat hajtson végre, amely újrapróbálkozást kér. Ha például az eszköz offline hiba miatt nem sikerült, a "próbáljon újra bejelentkezni" gombra kattintva egy AcquireToken újrapróbálkozást kér, ahelyett, hogy azonnal újrapróbálkozik. 

A natív alkalmazásokban a hibák két esetben határozhatók meg:

|  |  |
|------|-------------|
| **1. eset**:<br>Nem újrapróbálkozást lehetővé tevő hiba (a legtöbb esetben) | 1. Ne próbálkozzon azonnali próbálkozással. A végfelhasználó felhasználói felületének bemutatása az újrapróbálkozást meghívó adott hiba alapján (például "próbálja meg újra bejelentkezni" vagy "az Azure AD Broker alkalmazás letöltése"). |
| **2. eset**:<br>Újrapróbálkozást lehetővé tevő hiba | 1. egyetlen újrapróbálkozást hajthat végre, mert a végfelhasználó olyan állapotba lépett, amely sikert eredményezett.<br><br>2. Ha az újrapróbálkozás sikertelen, a végfelhasználói felhasználói felületet az újrapróbálkozást meghívó adott hiba alapján ("próbálja meg újra bejelentkezni", "az Azure AD Broker alkalmazás letöltése" stb.) mutatja. |

> [!IMPORTANT]
> Ha egy felhasználói fiókot a ADAL egy csendes hívásban ad át, és a művelet meghiúsul, a következő interaktív kérelem lehetővé teszi, hogy a végfelhasználó egy másik fiókkal jelentkezzen be. Miután sikeresen AcquireToken egy felhasználói fiókot, az alkalmazásnak ellenőriznie kell, hogy a bejelentkezett felhasználó megfelel-e az alkalmazás helyi felhasználói objektumának. Az eltérés nem eredményez kivételt (kivéve az Objective C-t), de olyan esetekben kell figyelembe venni, amikor egy felhasználó a hitelesítési kérések előtt helyileg ismert (például sikertelen csendes hívás esetén).
>

#### <a name="net"></a>.NET

Az alábbi útmutató példákat tartalmaz a hibák kezelésére az összes nem csendes AcquireToken (...) együtt. ADAL metódusok, a következők *kivételével*: 

- AcquireTokenAsync(..., IClientAssertionCertification, ...)
- AcquireTokenAsync(..., ClientCredential, ...)
- AcquireTokenAsync(..., ClientAssertion, ...)
- AcquireTokenAsync (..., UserAssertion,...)   

A kód a következőképpen valósítható meg:

```csharp
try {
    AcquireTokenAsync(…);
} 
    
catch(AdalServiceException e) {
    // Exception: AdalServiceException 
    // Represents an error produced by the STS. 
    // e.ErrorCode contains the error code and description, which can be used for debugging. 
    // NOTE: Do not code a dependency on the contents of the error description, as it can change over time.
    
    // Design time consideration: Certain errors may be caused at development and exposed through this exception. 
    // Looking inside the description will give more guidance on resolving the specific issue. 

    // Action: Case 1: Non-Retryable 
    // Do not perform an immediate retry. Only retry after user action. 
    // Example Errors: default case

    } 

catch (AdalException e) {
    // Exception: AdalException 
    // Represents a library exception generated by ADAL .NET.
    // e.ErrorCode contains the error code

    // Action: Case 1, Non-Retryable 
    // Do not perform an immediate retry. Only retry after user action. 
    // Example Errors: network_not_available, default case
}
```

> [!NOTE]
> A ADAL .NET-nek további szempontja van, mivel támogatja a PromptBehavior. Never, amely a AcquireTokenSilent hasonló viselkedést tartalmaz.
>

A következő útmutató példákat mutat be a ADAL metódusokkal együtt: 

- acquireToken (..., PromptBehavior. Never)

A kód a következőképpen valósítható meg:

```csharp
    try {acquireToken(…, PromptBehavior.Never);
    } 

catch(AdalServiceException e) {
    // Exception: AdalServiceException represents 
    // Represents an error produced by the STS. 
    // e.ErrorCode contains the error code and description, which can be used for debugging. 
    // NOTE: Do not code a dependency on the contents of the error description, as it can change over time.

    // Action: Case 1: Non-Retryable 
    // Do not perform an immediate retry. Only retry after user action. 
    // Example Errors: default case

} catch (AdalException e) {
    // Error Code: e.ErrorCode == "user_interaction_required"
    // Description: user_interaction_required indicates the silent request failed 
    // in a way that's resolvable with an interactive request.
    // Action: Resolvable with an interactive request. 

    // Action: Case 1, Non-Retryable 
    // Do not perform an immediate retry. Only retry after user action. 
    // Example Errors: network_not_available, default case
}
```

#### <a name="android"></a>Android

Az alábbi útmutató példákat tartalmaz a hibák kezelésére az összes nem csendes AcquireToken (...) együtt. ADAL metódusok. 

A kód a következőképpen valósítható meg:

```java
AcquireTokenAsync(…);

// *Inside callback*
public void onError(Exception e) {
    if (e instanceof AuthenticationException) {
        // Exception: AdalException 
        // Represents a library exception generated by ADAL Android.
        // Error Code: e.getCode();

        // Error: ADALError.BROKER_APP_INSTALLATION_STARTED
        // Description: Broker app not installed, user will be prompted to download the app. 

        // Action: Case 2, Retriable Error 
        // Perform a single retry. If that fails, only try again after user action. 

        // Action: Case 1, Non-Retriable 
        // Do not perform an immediate retry. Only retry after user action. 
        // Example Errors: default case, DEVICE_CONNECTION_IS_NOT_AVAILABLE
    }
}
```

#### <a name="ios"></a>iOS

Az alábbi útmutató példákat tartalmaz a hibák kezelésére az összes nem csendes AcquireToken (...) együtt. ADAL metódusok. 

A kód a következőképpen valósítható meg:

```objc
[context acquireTokenWithResource:[ARGS], completionBlock:^(ADAuthenticationResult *result) {
    if (result.status == AD_FAILED) {
        if ([error.domain isEqualToString:ADAuthenticationErrorDomain]){
            // Exception: AD_FAILED 
            // Represents a library error generated by ADAL ObjC.
            // Error Code: result.error.code 

            // Error: AD_ERROR_SERVER_WRONG_USER
            // Description: App passed a user into ADAL and the end user signed in with a different account. 
            // Action: Case 1, Non-retriable (as is) and up to the application on how to handle this case. 
            // It can attempt a new request without specifying the user, or use UI to clarify the user account to sign in. 

            // Action: Case 1, Non-Retriable 
            // Do not perform an immediate retry. Only retry after user action. 
            // Example Errors: default case
        }
    }
}]
```

### <a name="error-cases-and-actionable-steps-web-applications-that-call-a-resource-api-net"></a>Hibákkal és műveletekkel kapcsolatos lépések: az erőforrás-API-t (.NET) meghívó webalkalmazások

Ha olyan .NET-webalkalmazást hoz létre, amely egy adott erőforráshoz tartozó engedélyezési kóddal hívja meg a tokent, az egyetlen szükséges kód az általános eset alapértelmezett kezelője. 

A következő útmutató példákat mutat be a ADAL metódusokkal együtt: 

- AcquireTokenByAuthorizationCodeAsync(...)

A kód a következőképpen valósítható meg:

```csharp
try {
    AcquireTokenByAuthorizationCodeAsync(…);
} 

catch (AdalException e) {
    // Exception: AdalException
    // Represents a library exception generated by ADAL .NET.
    // Error Code: e.ErrorCode

    // Action: Do not perform an immediate retry. Only try again after user action or wait until much later. 
    // Example Errors: default case
}
```

### <a name="error-cases-and-actionable-steps-single-page-applications-adaljs"></a>Hibák és a gyakorlatban alkalmazható lépések: egylapos alkalmazások (adal.js)

Ha adal.jst használó egylapos alkalmazást hoz létre a AcquireToken-mel, a hiba-kezelési kód hasonló egy tipikus csendes híváshoz. Különösen adal.jsban a AcquireToken soha nem jeleníti meg a felhasználói felületet. 

A sikertelen AcquireToken a következő esetekben szerepelnek:

|  |  |
|------|-------------|
| **1. eset**:<br>Feloldható interaktív kérelemmel | 1. Ha a login () nem sikerül, ne végezzen azonnali újrapróbálkozást. Csak akkor próbálkozzon újra, ha a felhasználói művelet megkérdezi az újrapróbálkozást.|
| **2. eset**:<br>Nem oldható fel egy interaktív kérelem. A hiba újrapróbálható. | 1. hajtson végre egyetlen újrapróbálkozást, mivel a végfelhasználó főkiszolgálója olyan állapotba lépett, amely sikert eredményez.<br><br>2. Ha az újrapróbálkozás sikertelen, a végfelhasználót egy, az újrapróbálkozást meghívó adott hiba alapján jeleníti meg ("próbáljon újra bejelentkezni"). |
| **3. eset**:<br>Nem oldható fel egy interaktív kérelem. A hiba nem újrapróbálható. | 1. Ne próbálkozzon azonnali próbálkozással. A végfelhasználót az újrapróbálkozást meghívó adott hiba alapján jeleníti meg ("próbáljon újra bejelentkezni"). |

A kód a következőképpen valósítható meg:

```javascript
AuthContext.acquireToken(…, function(error, errorDesc, token) {
    if (error || errorDesc) {
        // Represents any token acquisition failure that occurred. 
        // Error Code: error.indexOf("<ERROR_STRING>")

        // Errors: if (error.indexOf("interaction_required"))
        //         if (error.indexOf("login required"))
        // Description: ADAL wasn't able to silently acquire a token because of expire or fresh session. 
        // Action: Case 1, Resolvable with an interactive login() request. 

        // Error: if (error.indexOf("Token Renewal Failed")) 
        // Description: Timeout when refreshing the token.
        // Action: Case 2, Not resolvable interactively, error is retriable.
        // Perform a single retry. Only try again after user action.

        // Action: Case 3, Not resolvable interactively, error is not retriable. 
        // Do not perform an immediate retry. Only retry after user action.
        // Example Errors: default case
    }
}
```

### <a name="error-cases-and-actionable-steps-service-to-service-applications-net-only"></a>Hibákra és műveletekre vonatkozó lépések: szolgáltatások közötti alkalmazások (csak .NET esetén)

Ha AcquireToken-t használó szolgáltatás-szolgáltatás alkalmazást hoz létre, néhány kulcsfontosságú hiba miatt a kódnak kezelnie kell. Az egyetlen hiba, ha a hibát visszaküldi a hívó alkalmazásnak (az egyes esetekben), vagy újrapróbálkozási stratégiát alkalmaz. 

#### <a name="all-scenarios"></a>Az összes forgatókönyv

Az *összes* szolgáltatás-szolgáltatás alkalmazási forgatókönyvhöz, beleértve a következőket:

- Ne próbálkozzon azonnali próbálkozással. A ADAL egyetlen újrapróbálkozás kísérletet tesz lehetővé bizonyos sikertelen kérelmek esetén. 
- Csak a felhasználó vagy az alkalmazás művelete után próbálkozzon újra az Újrapróbálkozással. Egy bizonyos meghatározott intervallumban dolgozó Daemon-alkalmazásnak például várnia kell, amíg a következő intervallum újra próbálkozik.

A következő útmutató példákat mutat be a ADAL metódusokkal együtt: 

- AcquireTokenAsync(..., IClientAssertionCertification, ...)
- AcquireTokenAsync(...,ClientCredential, ...)
- AcquireTokenAsync(...,ClientAssertion, ...)
- AcquireTokenAsync(...,UserAssertion, ...)

A kód a következőképpen valósítható meg:

```csharp
try {
    AcquireTokenAsync(…);
} 

catch (AdalException e) {
    // Exception: AdalException
    // Represents a library exception generated by ADAL .NET.
    // Error Code: e.ErrorCode

    // Action: Do not perform an immediate retry. Only try again after user action (if applicable) or wait until much later. 
    // Example Errors: default case
}  
```

#### <a name="on-behalf-of-scenarios"></a>Alkalmazási helyzetek

*A* szolgáltatással való használatra vonatkozó alkalmazási forgatókönyvek esetén.

A következő útmutató példákat mutat be a ADAL metódusokkal együtt: 

- AcquireTokenAsync(..., UserAssertion, ...)

A kód a következőképpen valósítható meg:

```csharp
try {
AcquireTokenAsync(…);
} 

catch (AdalServiceException e) {
    // Exception: AdalServiceException 
    // Represents an error produced by the STS. 
    // e.ErrorCode contains the error code and description, which can be used for debugging. 
    // NOTE: Do not code a dependency on the contents of the error description, as it can change over time.

    // Error: On-Behalf-Of Error Handler
    if (e.ErrorCode == "interaction_required") {
        // Description: The client needs to perform some action due to a config from admin. 
        // Action: Capture `claims` parameter inside ex.InnerException.InnerException.
        // Generate HTTP error 403 with claims, throw back HTTP error to client.
        // Wait for client to retry. 
    }
} 
        
catch (AdalException e) {
    // Exception: AdalException 
    // Represents a library exception generated by ADAL .NET.
    // Error Code: e.ErrorCode

    // Action: Do not perform an immediate retry. Only try again after user action (if applicable) or wait until much later. 
    // Example Error: default case
}
```

Egy [teljes mintát](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca) készítettünk, amely bemutatja ezt a forgatókönyvet.

## <a name="error-and-logging-reference"></a>Hiba-és naplózási hivatkozás

### <a name="logging-personal-identifiable-information--organizational-identifiable-information"></a>Személyes azonosításra alkalmas adatok naplózása & Szervezetian azonosítható információk 
Alapértelmezés szerint a ADAL naplózása nem rögzíti vagy nem naplózza a személyes azonosításra alkalmas adatokat vagy a szervezeti azonosításra alkalmas adatokat. A könyvtár lehetővé teszi, hogy az alkalmazások fejlesztői a naplózó osztályon keresztül kapcsolják be ezt a beállítást. A személyes azonosításra alkalmas adatok vagy a szervezeti azonosításra alkalmas információk naplózásával az alkalmazás felelősséget vállal a szigorúan bizalmas adatok biztonságos kezeléséhez és a szabályozási követelmények teljesítéséhez.

### <a name="net"></a>.NET

#### <a name="adal-library-errors"></a>ADAL-függvénytár hibái

Az adott ADAL kapcsolatos hibák feltárásához az [Azure-ActiveDirectory-Library-for-DotNet adattár](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/blob/8f6d560fbede2247ec0e217a21f6929d4375dcaa/src/ADAL.PCL/Utilities/Constants.cs#L58) forráskódja a legjobb hiba-hivatkozás.

#### <a name="guidance-for-error-logging-code"></a>A hiba naplózási kódjának útmutatója

A ADAL .NET-naplózás a használt platformtól függően változik. A naplózás engedélyezéséhez tekintse meg a [naplózási wiki](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Logging-in-ADAL.Net) kódot.

### <a name="android"></a>Android

#### <a name="adal-library-errors"></a>ADAL-függvénytár hibái

Az adott ADAL kapcsolatos hibák feltárásához az [Azure-ActiveDirectory-Library-for-Android adattár](https://github.com/AzureAD/azure-activedirectory-library-for-android/blob/dev/adal/src/main/java/com/microsoft/aad/adal/ADALError.java#L33) forráskódja a legjobb hiba-hivatkozás.

#### <a name="operating-system-errors"></a>Operációs rendszer hibái

Az androidos operációsrendszer-hibák a ADAL hitelesítési kivétel keresztül érhetők el, és a "SERVER_INVALID_REQUEST" azonosíthatók, és a hibák leírásával további részletességgel is rendelkezhetnek. 

A gyakori hibák teljes listáját, valamint azt, hogy milyen lépéseket kell elvégeznie az alkalmazás vagy a végfelhasználók számára, tekintse meg a [ADAL Android wikit](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki). 

#### <a name="guidance-for-error-logging-code"></a>A hiba naplózási kódjának útmutatója

```java
// 1. Configure Logger
Logger.getInstance().setExternalLogger(new ILogger() {    
    @Override   
    public void Log(String tag, String message, String additionalMessage, LogLevel level, ADALError errorCode) { 
    // …
    // You can write this to logfile depending on level or errorcode. 
    writeToLogFile(getApplicationContext(), tag +":" + message + "-" + additionalMessage);    
    }
}

// 2. Set the log level
Logger.getInstance().setLogLevel(Logger.LogLevel.Verbose);

// By default, the `Logger` does not capture any PII or OII

// PII or OII will be logged
Logger.getInstance().setEnablePII(true);

// To STOP logging PII or OII, use the following setter
Logger.getInstance().setEnablePII(false);


// 3. Send logs to logcat.
adb logcat > "C:\logmsg\logfile.txt";
```

### <a name="ios"></a>iOS

#### <a name="adal-library-errors"></a>ADAL-függvénytár hibái

Az adott ADAL kapcsolatos hibák feltárásához az [Azure-ActiveDirectory-Library-for-ObjC adattár](https://github.com/AzureAD/azure-activedirectory-library-for-objc/blob/dev/ADAL/src/ADAuthenticationError.m#L295) forráskódja a legjobb hiba-hivatkozás.

#### <a name="operating-system-errors"></a>Operációs rendszer hibái

iOS-hibák jelentkezhetnek a bejelentkezés során, amikor a felhasználók webes nézeteket használnak, valamint a hitelesítés természetét. Ezt olyan feltételek okozhatják, mint például a TLS-hibák, az időtúllépések vagy a hálózati hibák:

- A jogosultságok megosztásához a bejelentkezések nem állandóak, és a gyorsítótár üresen jelenik meg. A következő kódrészlet a kulcstartóba való felvételével oldható fel: `[[ADAuthenticationSettings sharedInstance] setSharedCacheKeychainGroup:nil];`
- A hibák NsUrlDomain a művelet az alkalmazás logikája alapján változik. Tekintse meg a [NSURLErrorDomain dokumentációját](https://developer.apple.com/documentation/foundation/nsurlerrordomain#declarations) a kezelni kívánt példányok számára.
- Lásd: [ADAL-c általános problémák](https://github.com/AzureAD/azure-activedirectory-library-for-objc#adauthenticationerror) a ADAL Objective-c csapat által fenntartott gyakori hibák listájához.

#### <a name="guidance-for-error-logging-code"></a>A hiba naplózási kódjának útmutatója

```objc
// 1. Enable NSLogging
[ADLogger setNSLogging:YES];

// 2. Set the log level (if you want verbose)
[ADLogger setLevel:ADAL_LOG_LEVEL_VERBOSE];

// 3. Set up a callback block to simply print out
[ADLogger setLogCallBack:^(ADAL_LOG_LEVEL logLevel, NSString *message, NSString *additionalInformation, NSInteger errorCode, NSDictionary *userInfo) {
     NSString* log = [NSString stringWithFormat:@"%@ %@", message, additionalInformation];    
     NSLog(@"%@", log);
}];
```

### <a name="guidance-for-error-logging-code---javascript"></a>Útmutató a hiba-naplózási kódokhoz – JavaScript 

```javascript
0: Error1: Warning2: Info3: Verbose
window.Logging = {
    level: 3,
    log: function (message) {
        console.log(message);
    }
};
```

## <a name="related-content"></a>Kapcsolódó tartalom

* [Azure AD-hitelesítési kódtárak][AAD-Auth-Libraries]
* [Az Azure AD hitelesítési forgatókönyvei][AAD-Auth-Scenarios]
* [Alkalmazások integrálása a Azure Active Directory][AAD-Integrating-Apps]

A következő megjegyzések szakasz segítségével visszajelzést adhat, és segítheti a tartalom pontosítását és kialakítását.

[![A "Bejelentkezés Microsoft-fiókkal" gomb megjelenítése][AAD-Sign-In]][AAD-Sign-In]
<!--Reference style links -->

[AAD-Auth-Libraries]: ./active-directory-authentication-libraries.md
[AAD-Auth-Scenarios]:v1-authentication-scenarios.md
[AAD-Integrating-Apps]:../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json
[AZURE-portal]: https://portal.azure.com

<!--Image references-->
[AAD-Sign-In]:./media/active-directory-devhowto-multi-tenant-overview/sign-in-with-microsoft-light.png

