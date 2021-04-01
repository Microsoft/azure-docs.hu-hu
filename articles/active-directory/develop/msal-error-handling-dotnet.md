---
title: Hibák és kivételek kezelése az MSAL.NET-ben
titleSuffix: Microsoft identity platform
description: Megtudhatja, hogyan kezelheti a hibákat és a kivételeket, a feltételes hozzáférési jogcímeket és az újrapróbálkozásokat a MSAL.NET.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/26/2020
ms.author: marsma
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 565acd745ba5d7fdec71f306d3851e599838f7d9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99584044"
---
# <a name="handle-errors-and-exceptions-in-msalnet"></a>Hibák és kivételek kezelése az MSAL.NET-ben

[!INCLUDE [Active directory error handling introduction](../../../includes/active-directory-develop-error-handling-introduction.md)]

## <a name="error-handling-in-msalnet"></a>Hibakezelés a MSAL.NET

A .NET-kivételek feldolgozásakor használhatja a kivétel típusát és a `ErrorCode` tagot a kivételek megkülönböztetésére. `ErrorCode` az értékek [MsalError](/dotnet/api/microsoft.identity.client.msalerror)típusú állandók.

Tekintse meg a [MsalClientException](/dotnet/api/microsoft.identity.client.msalexception), a [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception)és a [MsalUIRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception)mezőit is.

Ha a [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception) eldobása megtörtént, próbálja meg a [hitelesítési és engedélyezési hibakódokat, és](reference-aadsts-error-codes.md) ellenőrizze, hogy a kód szerepel-e itt.

### <a name="common-net-exceptions"></a>Gyakori .NET-kivételek

Íme a gyakori kivételek, amelyek felmerülhetnek, és néhány lehetséges megoldás:  

| Kivétel | Hibakód | Kockázatcsökkentés|
| --- | --- | --- |
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception) | AADSTS65001: a felhasználó vagy a rendszergazda nem egyezett bele a (z) {appName} nevű, {appId} AZONOSÍTÓJÚ alkalmazás használatára. Interaktív engedélyezési kérelem küldése ehhez a felhasználóhoz és erőforráshoz.| Először kapjon felhasználói beleegyező engedélyt. Ha nem használja a .NET Core-ot (amely nem rendelkezik webes felhasználói felülettel), hívja a (csak egyszer) hívást `AcquireTokeninteractive` . Ha a .NET Core-ot használja, vagy nem szeretné elvégezni `AcquireTokenInteractive` a használatát, a felhasználó megkeresheti az URL-címet a beleegyezés megadásához: `https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read` . hívás `AcquireTokenInteractive` : `app.AcquireTokenInteractive(scopes).WithAccount(account).WithClaims(ex.Claims).ExecuteAsync();`|
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception) | AADSTS50079: a felhasználónak a [többtényezős hitelesítés (MFA)](../authentication/concept-mfa-howitworks.md)használatára van szüksége.| Nincs megoldás. Ha az MFA konfigurálva van a bérlőhöz, és Azure Active Directory (HRE) úgy dönt, hogy kikényszeríti azt, térjen vissza egy interaktív folyamathoz, például: `AcquireTokenInteractive` vagy `AcquireTokenByDeviceCode` .|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception) |AADSTS90010: a Grant típus nem támogatott a */gyakori hibák* -vagy */consumers* -végpontokon. Használja a */Organizations* vagy a bérlő-specifikus végpontot. A */gyakori hibák* használta.| Ahogy az az Azure AD-ban is szerepel, a szolgáltatónak Bérlővel vagy egyéb */Organizations* kell rendelkeznie.|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception) | AADSTS70002: a kérelem törzsének tartalmaznia kell a következő paramétert: `client_secret or client_assertion` .| Ez a kivétel akkor fordulhat elő, ha az alkalmazása nincs nyilvános ügyfélalkalmazásként regisztrálva az Azure AD-ben. A Azure Portal szerkessze az alkalmazás jegyzékfájlját, és állítsa a következőre: `allowPublicClient` `true` . |
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception)| `unknown_user Message`: Nem azonosítható a bejelentkezett felhasználó| A függvénytár nem tudta lekérdezni az aktuálisan bejelentkezett Windows-felhasználót, vagy a felhasználó nem AD vagy az Azure AD-hez csatlakoztatott (a munkahelyhez csatlakoztatott felhasználók nem támogatottak). 1. megoldás: a UWP győződjön meg arról, hogy az alkalmazás a következő képességekkel rendelkezik: vállalati hitelesítés, magánhálózat (ügyfél és kiszolgáló), felhasználói fiók adatai. 2. megoldás: saját logikát implementálhat a Felhasználónév lekéréséhez (például john@contoso.com ), és használhatja a `AcquireTokenByIntegratedWindowsAuth` felhasználónevet tartalmazó űrlapot.|
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception)|integrated_windows_auth_not_supported_managed_user| Ez a módszer Active Directory (AD) által közzétett protokollra támaszkodik. Ha egy felhasználó az Azure AD-ben az AD-támogatás nélkül lett létrehozva ("felügyelt" felhasználó), ez a metódus sikertelen lesz. Az AD-ben létrehozott és az Azure AD által támogatott ("összevont" felhasználók) felhasználók számára előnyös lehet a nem interaktív hitelesítési módszer. Enyhítés: használjon interaktív hitelesítést.|

### `MsalUiRequiredException`

A MSAL.NET által visszaadott általános állapotkódok egyike a hívásakor `AcquireTokenSilent()` `MsalError.InvalidGrantError` . Ez az állapotkód azt jelenti, hogy az alkalmazásnak újra meg kell hívnia a hitelesítési függvénytárat, de interaktív módban (AcquireTokenInteractive vagy AcquireTokenByDeviceCodeFlow a nyilvános ügyfélalkalmazások esetében a webalkalmazásokban van kihívás). Ennek az az oka, hogy a hitelesítési jogkivonat kiállítása előtt további felhasználói beavatkozásra van szükség.

Az idő nagy részében `AcquireTokenSilent` , amikor a művelet meghiúsul, az azért van, mert a jogkivonat-gyorsítótár nem rendelkezik a kérelemnek megfelelő jogkivonatokkal. A hozzáférési tokenek 1 órán belül lejárnak, és egy `AcquireTokenSilent` frissítési jogkivonat alapján megpróbálnak beolvasni egy újat (OAuth2 kifejezésekben ez a "refresh token" folyamat). Ez a folyamat több okból is meghiúsulhat, például ha a bérlői rendszergazda szigorúbb bejelentkezési házirendeket konfigurál. 

A beavatkozás célja, hogy a felhasználó műveletet hajtson végre. Bizonyos feltételek egyszerűen feloldhatók a felhasználók számára (például elfogadják a használati feltételeket egyetlen kattintással), és néhány nem oldható fel a jelenlegi konfigurációval (például a szóban forgó gépnek egy adott vállalati hálózathoz kell csatlakoznia). Némi segítség a felhasználónak a többtényezős hitelesítés beállításában, vagy a Microsoft Authenticator telepítését az eszközön.

### <a name="msaluirequiredexception-classification-enumeration"></a>`MsalUiRequiredException` besorolások enumerálása

A MSAL egy mezőt tesz elérhetővé `Classification` , amelynek segítségével jobb felhasználói élményt biztosíthat. Tegyük fel például, hogy a felhasználótól a jelszavuk lejárt, vagy hogy meg kell adnia az egyes erőforrások használatának beleegyezikét. A támogatott értékek az enumerálás részét képezik `UiRequiredExceptionClassification` :

| Osztályozás    | Értelmezés           | Ajánlott kezelési |
|-------------------|-------------------|----------------------|
| BasicAction | A feltételt a felhasználói interakció feloldható az interaktív hitelesítési folyamat során. | A AcquireTokenInteractively () hívása. |
| AdditionalAction | A feltételt az interaktív hitelesítési folyamaton kívül további javító interakciók is feloldhatók a rendszerrel. | Hívja meg a AcquireTokenInteractively () egy olyan üzenet megjelenítéséhez, amely ismerteti a javító műveletet. A hívó alkalmazás dönthet úgy, hogy elrejti a additional_actiont igénylő folyamatokat, ha a felhasználó nem valószínű, hogy elvégzi a javító műveletet. |
| MessageOnly      | A feltétel jelenleg nem oldható fel. Az interaktív hitelesítési folyamat elindításakor megjelenik egy üzenet, amely ismerteti a feltételt. | A AcquireTokenInteractively () hívásával megjeleníthető egy üzenet, amely ismerteti a feltételt. A AcquireTokenInteractively () UserCanceled-hibát ad vissza, miután a felhasználó beolvasta az üzenetet, és bezárja az ablakot. A hívó alkalmazás dönthet úgy, hogy elrejti a message_only eredményező folyamatokat, ha a felhasználó nem valószínű, hogy kihasználja az üzenetet.|
| ConsentRequired  | A felhasználói beleegyezés hiányzik vagy vissza lett vonva. | A AcquireTokenInteractively () hívásával adja meg a felhasználót a beleegyezés megadásához. |
| UserPasswordExpired | A felhasználó jelszava lejárt. | Hívja meg a AcquireTokenInteractively (), hogy a felhasználó alaphelyzetbe állíthatja a jelszavát. |
| PromptNeverFailed| Az interaktív hitelesítés a következő paraméterrel lett meghívva: prompt = soha, kényszerítve a MSAL, hogy a böngésző cookie-kra támaszkodjon, és ne jelenjen meg a böngésző. Ez nem sikerült. | A AcquireTokenInteractively () hívása prompt nélkül. None |
| AcquireTokenSilentFailed | A MSAL SDK nem rendelkezik elegendő információval a tokennek a gyorsítótárból való beolvasásához. Ennek oka az lehet, hogy a gyorsítótárban nincsenek tokenek, vagy nem található a fiók. A hibaüzenet további részleteket tartalmaz.  | A AcquireTokenInteractively () hívása. |
| Nincsenek    | További részletek nincsenek megadva. A feltételt a felhasználói interakció feloldható az interaktív hitelesítési folyamat során. | A AcquireTokenInteractively () hívása. |

## <a name="net-code-example"></a>.NET-kód – példa

```csharp
AuthenticationResult res;
try
{
 res = await application.AcquireTokenSilent(scopes, account)
        .ExecuteAsync();
}
catch (MsalUiRequiredException ex) when (ex.ErrorCode == MsalError.InvalidGrantError)
{
 switch (ex.Classification)
 {
  case UiRequiredExceptionClassification.None:
   break;
  case UiRequiredExceptionClassification.MessageOnly:
  // You might want to call AcquireTokenInteractive(). Azure AD will show a message
  // that explains the condition. AcquireTokenInteractively() will return UserCanceled error
  // after the user reads the message and closes the window. The calling application may choose
  // to hide features or data that result in message_only if the user is unlikely to benefit 
  // from the message
  try
  {
      res = await application.AcquireTokenInteractive(scopes).ExecuteAsync();
  }
  catch (MsalClientException ex2) when (ex2.ErrorCode == MsalError.AuthenticationCanceledError)
  {
   // Do nothing. The user has seen the message
  }
  break;

  case UiRequiredExceptionClassification.BasicAction:
  // Call AcquireTokenInteractive() so that the user can, for instance accept terms
  // and conditions

  case UiRequiredExceptionClassification.AdditionalAction:
  // You might want to call AcquireTokenInteractive() to show a message that explains the remedial action. 
  // The calling application may choose to hide flows that require additional_action if the user 
  // is unlikely to complete the remedial action (even if this means a degraded experience)

  case UiRequiredExceptionClassification.ConsentRequired:
  // Call AcquireTokenInteractive() for user to give consent.
  
  case UiRequiredExceptionClassification.UserPasswordExpired:
  // Call AcquireTokenInteractive() so that user can reset their password
  
  case UiRequiredExceptionClassification.PromptNeverFailed:
  // You used WithPrompt(Prompt.Never) and this failed
  
  case UiRequiredExceptionClassification.AcquireTokenSilentFailed:
  default:
  // May be resolved by user interaction during the interactive authentication flow.
  res = await application.AcquireTokenInteractive(scopes)
                         .ExecuteAsync(); break;
 }
}
```
[!INCLUDE [Active directory error handling claims challenges](../../../includes/active-directory-develop-error-handling-claims-challenges.md)]

Ha a MSAL.NET feltételes hozzáférést igénylő API-t hív meg, az alkalmazásnak kezelnie kell a jogcímek kivételeit. Ez olyan [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception) fog megjelenni, ahol a [jogcím](/dotnet/api/microsoft.identity.client.msalserviceexception.claims) tulajdonság nem üres.

A jogcím-kihívás kezeléséhez az `.WithClaim()` osztály metódusát kell használnia `PublicClientApplicationBuilder` .

[!INCLUDE [Active directory error handling retries](../../../includes/active-directory-develop-error-handling-retries.md)]

### <a name="http-error-codes-500-600"></a>HTTP-hibakódok 500-600

A MSAL.NET egy egyszerű újrapróbálkozási mechanizmust valósít meg a 500-600-es HTTP-hibakódok miatti hibák esetén.

[MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception) felületek `System.Net.Http.Headers.HttpResponseHeaders` tulajdonságként `namedHeaders` . A hibakód további információkat is használhat az alkalmazások megbízhatóságának növelése érdekében. A leírt esetben használhatja a `RetryAfterproperty` (típus `RetryConditionHeaderValue` ) és a számítást, ha újra próbálkozik.

Íme egy példa egy Daemon-alkalmazásra, amely az ügyfél hitelesítő adatait használja. Ezt bármely, a jogkivonat beszerzésére szolgáló metódushoz igazíthatja.

```csharp

bool retry = false;
do
{
    TimeSpan? delay;
    try
    {
         result = await publicClientApplication.AcquireTokenForClient(scopes, account).ExecuteAsync();
    }
    catch (MsalServiceException serviceException)
    {
         if (serviceException.ErrorCode == "temporarily_unavailable")
         {
             RetryConditionHeaderValue retryAfter = serviceException.Headers.RetryAfter;
             if (retryAfter.Delta.HasValue)
             {
                 delay = retryAfter.Delta;
             }
             else if (retryAfter.Date.HasValue)
             {
                 delay = retryAfter.Date.Value.Offset;
             }
         }
    }
    // . . .
    if (delay.HasValue)
    {
        Thread.Sleep((int)delay.Value.TotalMilliseconds); // sleep or other
        retry = true;
    }
} while (retry);
```

## <a name="next-steps"></a>Következő lépések

A hibák diagnosztizálásához és hibakereséséhez érdemes lehet engedélyezni [a naplózást a MSAL.net-ben](msal-logging-dotnet.md) .
