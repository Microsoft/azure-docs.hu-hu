---
title: Hibák és kivételek kezelése a MSAL4J-ben
titleSuffix: Microsoft identity platform
description: Megtudhatja, hogyan kezelheti a hibákat és kivételeket, a feltételes hozzáférési jogcímeket, és próbálkozzon újra a MSAL4J-alkalmazásokban.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/27/2020
ms.author: marsma
ms.reviewer: saeeda, nacanuma
ms.custom: aaddev
ms.openlocfilehash: 8563804a736c37acc9a96eb4a186933507f34200
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98760666"
---
# <a name="handle-errors-and-exceptions-in-msal-for-java"></a>Hibák és kivételek kezelése a Javához készült MSAL-ben

[!INCLUDE [Active directory error handling introduction](../../../includes/active-directory-develop-error-handling-introduction.md)]

## <a name="error-handling-in-msal-for-java"></a>Hibakezelés a MSAL for Java esetében

A Java-MSAL háromféle kivétel létezik: `MsalClientException` , `MsalServiceException` , és; az összes, `MsalInteractionRequiredException` amely az-tól származik `MsalException` .

- `MsalClientException` akkor következik be, amikor olyan hiba fordul elő, amely helyi a könyvtár vagy az eszköz számára.
- `MsalServiceException` akkor következik be, amikor a biztonságos jogkivonat szolgáltatás (STS) hibaüzenetet ad vissza, vagy más hálózati hiba történik.
- `MsalInteractionRequiredException` akkor fordul elő, ha a sikeres hitelesítéshez felhasználói felületi interakció szükséges.

### <a name="msalserviceexception"></a>MsalServiceException

`MsalServiceException` a kérésekben visszaadott HTTP-fejlécek közzététele az STS-ben. Hozzáférés a `MsalServiceException.headers()`

### <a name="msalinteractionrequiredexception"></a>MsalInteractionRequiredException

A MSAL for Java által visszaadott általános állapotkódok egyike a hívásakor `AcquireTokenSilently()` `InvalidGrantError` . Ez azt jelenti, hogy a hitelesítési jogkivonat kiállítása előtt további felhasználói beavatkozásra van szükség. Az alkalmazásnak újra meg kell hívnia a hitelesítési könyvtárat, de interaktív módban a `AuthorizationCodeParameters` nyilvános ügyfélalkalmazások küldésével vagy használatával `DeviceCodeParameters` .

Az idő nagy részében `AcquireTokenSilently` , amikor a művelet meghiúsul, az azért van, mert a jogkivonat-gyorsítótár nem rendelkezik a kérelemnek megfelelő jogkivonattal. A hozzáférési tokenek egy órán belül lejárnak, és egy `AcquireTokenSilently` frissítési jogkivonat alapján megpróbálnak újat szerezni. A OAuth2 szempontjából ez a jogkivonat-folyamat frissítése. Ez a folyamat több okból is meghiúsulhat, például ha a bérlői rendszergazda szigorúbb bejelentkezési házirendeket konfigurál.

A hibát okozó bizonyos feltételek egyszerűen feloldhatók a felhasználók számára. Előfordulhat például, hogy el kell fogadniuk a használati feltételeket, vagy a kérés nem teljesíthető az aktuális konfigurációval, mert a gépnek egy adott vállalati hálózathoz kell csatlakoznia.

A MSAL egy mezőt tesz elérhetővé `reason` , amelynek segítségével jobb felhasználói élményt biztosíthat. Előfordulhat például, hogy a `reason` mező arra utasítja a felhasználót, hogy a jelszavuk lejárt, vagy hogy meg kell adnia az egyes erőforrások használatának beleegyezikét. A támogatott értékek az enumerálás részét képezik  `InteractionRequiredExceptionReason` :

| Ok | Értelmezés | Ajánlott kezelési |
|---------|-----------|-----------------------------|
| `BasicAction` | A feltételt a felhasználói interakció feloldható az interaktív hitelesítési folyamat során. | Hívás `acquireToken` interaktív paraméterekkel. |
| `AdditionalAction` | A feltételt az interaktív hitelesítési folyamaton kívüli rendszerrel folytatott további javító interakciók is megoldhatók. | Az `acquireToken` interaktív paraméterek megadásával megjeleníthető egy üzenet, amely leírja, hogy milyen javító műveletet kell végrehajtania. A hívó alkalmazás dönthet úgy, hogy elrejti azokat a folyamatokat, amelyek további műveletet igényelnek, ha a felhasználó nem valószínű, hogy befejezi a javító műveletet. |
| `MessageOnly` | A feltétel jelenleg nem oldható fel. Interaktív hitelesítési folyamat elindítása a feltételt bemutató üzenet megjelenítéséhez. | `acquireToken`Az interaktív paraméterek megadásával megjeleníthető egy üzenet, amely ismerteti a feltételt. `acquireToken` a `UserCanceled` hibaüzenetet ad vissza, miután a felhasználó beolvassa az üzenetet, és bezárja az ablakot. Az alkalmazás dönthet úgy, hogy elrejti az üzenetet eredményező folyamatokat, ha a felhasználó nem valószínű, hogy kihasználja az üzenetet. |
| `ConsentRequired`| A felhasználói beleegyezés hiányzik vagy vissza lett vonva. |`acquireToken`Az interaktív paraméterek megadásával megadhatja, hogy a felhasználó beleegyezik. |
| `UserPasswordExpired` | A felhasználó jelszava lejárt. | Hívjon `acquireToken` interaktív paraméterrel, hogy a felhasználó alaphelyzetbe állíthatja a jelszavát. |
| `None` |  További részletek. A feltételt a felhasználói interakció feloldható az interaktív hitelesítési folyamat során. | Hívás `acquireToken` interaktív paraméterekkel. |

### <a name="code-example"></a>Példa programkódra

```java
        IAuthenticationResult result;
        try {
            PublicClientApplication application = PublicClientApplication
                    .builder("clientId")
                    .b2cAuthority("authority")
                    .build();

            SilentParameters parameters = SilentParameters
                    .builder(Collections.singleton("scope"))
                    .build();

            result = application.acquireTokenSilently(parameters).join();
        }
        catch (Exception ex){
            if(ex instanceof MsalInteractionRequiredException){
                // AcquireToken by either AuthorizationCodeParameters or DeviceCodeParameters
            } else{
                // Log and handle exception accordingly
            }
        }
```

[!INCLUDE [Active directory error handling claims challenges](../../../includes/active-directory-develop-error-handling-claims-challenges.md)]

[!INCLUDE [Active directory error handling retries](../../../includes/active-directory-develop-error-handling-retries.md)]

## <a name="next-steps"></a>Következő lépések

A hibák diagnosztizálásához és hibakereséséhez érdemes lehet engedélyezni a [MSAL-ban való naplózást a Javához](msal-logging-java.md) .
