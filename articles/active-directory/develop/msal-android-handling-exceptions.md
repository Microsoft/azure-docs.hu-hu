---
title: Hibák és kivételek (MSAL Android) | Azure
titleSuffix: Microsoft identity platform
description: Megtudhatja, hogyan kezelheti a hibákat és a kivételeket, a feltételes hozzáférést és a jogcímeket a MSAL Android-alkalmazásaiban.
services: active-directory
author: hamiltonha
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: troubleshooting
ms.workload: identity
ms.date: 08/07/2020
ms.author: hahamil
ms.reviewer: marsma
ms.openlocfilehash: 4185206e92a78c2684ba1690f03700ef2532cc5e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98761399"
---
# <a name="handle-exceptions-and-errors-in-msal-for-android"></a>Kivételek és hibák kezelése az Android rendszerhez készült MSAL-ben

A Microsoft Authentication Library (MSAL) kivételei arra szolgálnak, hogy segítsenek az alkalmazások fejlesztőinek az alkalmazásuk hibakeresésében. A kivételek üzenetei nincsenek honosítva.

A kivételek és hibák feldolgozásakor használhatja a kivétel típusát és a hibakódot a kivételek megkülönböztetése érdekében.  A hibakódok listáját lásd: [hitelesítési és engedélyezési hibakódok](reference-aadsts-error-codes.md).

A bejelentkezési folyamat során hibák merülhetnek fel a beleegyezések, a feltételes hozzáférés (MFA, eszközkezelés, hely-alapú korlátozások), a jogkivonat kiállítása és a visszaváltás, valamint a felhasználói tulajdonságok miatt.


|Hiba osztály | Ok/hiba karakterlánca| Kezelés |
|-----------|------------|----------------|
|`MsalUiRequiredException`| <ul><li>`INVALID_GRANT`: A hozzáférési jogkivonat beváltásához használt frissítési token érvénytelen, lejárt vagy visszavont. Ezt a kivételt a jelszó módosítása okozhatja. </li><li>`NO_TOKENS_FOUND`: A hozzáférési jogkivonat nem létezik, és nem található frissítési jogkivonat a hozzáférési jogkivonat beváltásához.</li> <li>Lépésre van szükség<ul><li>MFA</li><li>Hiányzó jogcímek</li></ul></li><li>A feltételes hozzáférés letiltotta (például a [hitelesítés-átvitelszervező](./msal-android-single-sign-on.md) telepítése szükséges)</li><li>`NO_ACCOUNT_FOUND`: Nem érhető el fiók a gyorsítótárban a csendes hitelesítéshez.</li></ul> |Kérje meg `acquireToken()` , hogy kérje meg a felhasználót, hogy adja meg felhasználónevét és jelszavát, és lehet, hogy beleegyezik, és elvégzi a többtényezős hitelesítést.|
|`MsalDeclinedScopeException`|<ul><li>`DECLINED_SCOPE`: A felhasználó vagy a kiszolgáló nem fogadta el az összes hatókört. Előfordulhat, hogy a kiszolgáló elutasítja a hatókört, ha a kért hatókör nem támogatott, nem ismerhető fel, vagy egy adott fiók esetében nem támogatott. </li></ul>| A fejlesztőnek el kell döntenie, hogy folytatja-e a hitelesítést a megadott hatókörökkel, vagy befejezi a hitelesítési folyamatot. Lehetőség a beszerzési jogkivonat kérésének újbóli elküldésére csak a megadott hatókörök esetében, és olyan tippeket nyújt, amelyeknek az átadásával `silentParametersForGrantedScopes` és meghívásával engedélyeket adtak meg `acquireTokenSilent` . |
|`MsalServiceException`|<ul><li>`INVALID_REQUEST`: A kérelemből hiányzik egy kötelező paraméter, érvénytelen paramétert tartalmaz, a paraméter többször is szerepel, vagy más módon helytelen formátumú. </li><li>`SERVICE_NOT_AVAILABLE`: 500/503/506 hibakódot jelöl a szolgáltatás leállása miatt. </li><li>`UNAUTHORIZED_REQUEST`: Az ügyfél nem jogosult engedélyezési kód igénylésére.</li><li>`ACCESS_DENIED`: Az erőforrás-tulajdonos vagy az engedélyezési kiszolgáló megtagadta a kérelmet.</li><li>`INVALID_INSTANCE`: az `AuthorityMetadata` Érvényesítés nem sikerült</li><li>`UNKNOWN_ERROR`: A kiszolgálóra irányuló kérelem meghiúsult, de nem történt hiba, és visszakerül `error_description` a szolgáltatásból.</li><ul>| Ez a kivételi osztály a szolgáltatással folytatott kommunikáció során fellépő hibákat jeleníti meg az engedélyezés vagy a jogkivonat-végpontok közül. A MSAL beolvassa a hibát, és error_description a kiszolgáló válasza alapján. Ezek a hibák általában a kód vagy az alkalmazás-regisztrációs portálon az alkalmazások konfigurációinak kijavításával oldhatók meg. A szolgáltatás leállása ritkán képes kiváltani ezt a figyelmeztetést, ami csak a szolgáltatás helyreállítására való várakozással enyhíthető.  |
|`MsalClientException`|<ul><li> `MULTIPLE_MATCHING_TOKENS_DETECTED`: Több gyorsítótár-bejegyzés található, és az SDK nem tudja azonosítani a megfelelő hozzáférési vagy frissítési tokent a gyorsítótárból. Ez a kivétel általában az SDK-ban a jogkivonatok tárolására szolgáló hibát jelzi, vagy ha a szolgáltató nincs megadva a csendes kérelemben, és több egyező token található. </li><li>`DEVICE_NETWORK_NOT_AVAILABLE`: Nem érhető el aktív hálózat az eszközön. </li><li>`JSON_PARSE_FAILURE`: Az SDK nem tudta elemezni a JSON formátumot.</li><li>`IO_ERROR`: `IOException` történt, lehet eszköz-vagy hálózati hiba. </li><li>`MALFORMED_URL`: Az URL-cím helytelen formátumú. Az hitelesítési kérelem, a hitelesítésszolgáltató vagy az átirányítási URI létrehozása valószínűleg okozta. </li><li>`UNSUPPORTED_ENCODING`: Az eszköz nem támogatja a kódolást. </li><li>`NO_SUCH_ALGORITHM`: Az [PKCE](https://tools.ietf.org/html/rfc7636) -kihívás létrehozásához használt algoritmus nem támogatott. </li><li>`INVALID_JWT`: `JWT` a kiszolgáló által visszaadott érték érvénytelen vagy üres vagy helytelen formátumú. </li><li>`STATE_MISMATCH`: Az engedélyezési válasz állapota nem felelt meg az engedélyezési kérelemben szereplő állapotnak. Az engedélyezési kérelmek esetében az SDK ellenőrzi, hogy az átirányítási és a kérésben elküldött állapotot a rendszer visszaadja-e. </li><li>`UNSUPPORTED_URL`: Nem támogatott URL-cím, nem hajtható végre az ADFS-szolgáltató ellenőrzése. </li><li> `AUTHORITY_VALIDATION_NOT_SUPPORTED`: A hatóság hitelesítése nem támogatott. Az SDK támogatja a B2C-hatóságokat, de nem támogatja a B2C-hatóság érvényesítését. Csak a jól ismert gazdagép lesz támogatott. </li><li>`CHROME_NOT_INSTALLED`: A Chrome nincs telepítve az eszközön. Az SDK a Chrome egyéni lapját használja az engedélyezési kérelmekhez, ha elérhető, és vissza fog térni a Chrome böngészőhöz. </li><li>`USER_MISMATCH`: A beszerzési jogkivonat-kérelemben megadott felhasználó nem egyezik meg a kiszolgáló által visszaadott felhasználóval.</li></ul>|Ez a kivételi osztály a könyvtár helyi általános hibáit jelöli. Ezeket a kivételeket a kérelem helyesbítésével lehet kezelni.|
|`MsalUserCancelException`|<ul><li>`USER_CANCELED`: A felhasználó kezdeményezte az interaktív folyamatot, és a tokenek fogadása előtt visszavonta a kérést. </li></ul>||
|`MsalArgumentException`|<ul><li>`ILLEGAL_ARGUMENT_ERROR_CODE`</li><li>`AUTHORITY_REQUIRED_FOR_SILENT`: A szolgáltatót meg kell adni `acquireTokenSilent` .</li></ul>|Ezeket a hibákat a fejlesztő kijavítási argumentumai és az interaktív hitelesítés, a befejezési visszahívás, a hatókörök, valamint az érvényes AZONOSÍTÓval rendelkező fiók megadásával csökkentheti.|


## <a name="catching-errors"></a>Kifogási hibák

A következő kódrészlet egy példát mutat be a csendes hívások esetén előforduló hibákra `acquireToken` .

```java
/**
 * Callback used in for silent acquireToken calls.
 */
private SilentAuthenticationCallback getAuthSilentCallback() {
    return new SilentAuthenticationCallback() {

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            Log.d(TAG, "Successfully authenticated");

            /* Successfully got a token, use it to call a protected resource - MSGraph */
            callGraphAPI(authenticationResult);
        }

        @Override
        public void onError(MsalException exception) {
            /* Failed to acquireToken */
            Log.d(TAG, "Authentication failed: " + exception.toString());
            displayError(exception);

            if (exception instanceof MsalClientException) {
                /* Exception inside MSAL, more info inside MsalError.java */
            } else if (exception instanceof MsalServiceException) {
                /* Exception when communicating with the STS, likely config issue */
            } else if (exception instanceof MsalUiRequiredException) {
                /* Tokens expired or no session, retry with interactive */
            }
        }
    };
}
```

## <a name="next-steps"></a>Következő lépések

További információ az [Android rendszerhez készült MSAL való bejelentkezésről](msal-logging-android.md).