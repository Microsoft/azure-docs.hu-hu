---
title: Áttelepítés a MSAL.NET
titleSuffix: Microsoft identity platform
description: Ismerje meg a .NET-hez készült Microsoft Authentication Library (MSAL.NET) és a .NET-hez készült Azure AD Authentication Library (ADAL.NET) közötti különbséget, valamint a MSAL.NET-re való áttelepítést.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6d9f178df5e5fd9d2b70b7791588cfdc0652b217
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72802781"
---
# <a name="migrating-applications-to-msalnet"></a>Alkalmazások migrálása a MSAL.NET-be

A .NET-hez készült Microsoft Authentication Library (MSAL.NET) és az Azure AD Authentication Library for .NET (ADAL.NET) is az Azure AD-entitások hitelesítésére és az Azure AD-jogkivonatok igénylésére szolgál. Eddig a legtöbb fejlesztő dolgozott együtt az Azure ad for Developers platformmal (v 1.0) az Azure AD-identitások (munkahelyi és iskolai fiókok) hitelesítéséhez az Azure AD Authentication Library (ADAL) használatával. A MSAL használata:

- a Microsoft Identity platform végpontjának használatával a Microsoft-identitások (Azure AD-identitások és Microsoft-fiókok, valamint közösségi és helyi Azure AD B2C fiókok) szélesebb körét hitelesítheti.
- a felhasználók a legjobb egyszeri bejelentkezési élményt kapják meg.
- az alkalmazás lehetővé teszi a növekményes hozzáférés engedélyezését, és egyszerűbben támogatja a feltételes hozzáférést.
- élvezheti az innováció előnyeit.

A **MSAL.net mostantól a Microsoft Identity platformmal való használatra javasolt hitelesítési függvénytár**. A ADAL.NET-on nem lesznek új funkciók implementálva. Az erőfeszítések a MSAL javítására összpontosítanak.

Ez a cikk a .NET-hez készült Microsoft Authentication Library (MSAL.NET) és a .NET-hez készült Azure AD Authentication Library (ADAL.NET) közötti különbségeket ismerteti, és segítséget nyújt a MSAL-re való Migrálás során.  

## <a name="differences-between-adal-and-msal-apps"></a>A ADAL és a MSAL alkalmazások közötti különbségek

A legtöbb esetben a MSAL.NET és a Microsoft Identity platform végpontját kívánja használni, amely a Microsoft hitelesítési kódtárainak legújabb generációja. Az MSAL.NET használatával az Azure AD-vel (munkahelyi és iskolai fiókokkal), a Microsoft-fiókokkal (MSA) és a Azure AD B2Cekkel bejelentkezett felhasználók jogkivonatait is beszerezheti az alkalmazásba. 

Ha már ismeri az Azure AD for Developers (v 1.0) végpontot (és a ADAL.NET), érdemes elolvasnia, hogy [Mi a különbség a Microsoft Identity platform (v 2.0) végpontján?](active-directory-v2-compare.md).

Azonban továbbra is szükség van a ADAL.NET használatára, ha az alkalmazásnak a [Active Directory összevonási szolgáltatások (AD FS) (ADFS)](/windows-server/identity/active-directory-federation-services)korábbi verzióival kell bejelentkeznie a felhasználókba. További információ: ADFS- [támogatás](https://aka.ms/msal-net-adfs-support).

Az alábbi kép összefoglalja a ADAL.NET és a MSAL.NET közötti különbségeket ![egymás melletti kódok](./media/msal-compare-msaldotnet-and-adaldotnet/differences.png)

### <a name="nuget-packages-and-namespaces"></a>NuGet-csomagok és-névterek

A ADAL.NET a [Microsoft. IdentityModel. clients. ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) NuGet csomagból származik. a használandó névtér `Microsoft.IdentityModel.Clients.ActiveDirectory`.

A MSAL.NET használatához hozzá kell adnia a [Microsoft. Identity. Client](https://www.nuget.org/packages/Microsoft.Identity.Client) NuGet-csomagot, és a `Microsoft.Identity.Client` névteret kell használnia.

### <a name="scopes-not-resources"></a>Hatókörök nem erőforrásai

A ADAL.NET lekéri az *erőforrásokhoz*tartozó jogkivonatokat, de a MSAL.net jogkivonatokat vásárol a *hatókörökhöz*. Számos MSAL.NET AcquireToken felülbíráláshoz hatókör (`IEnumerable<string> scopes`) nevű paraméter szükséges. Ez a paraméter a szükséges engedélyeket és erőforrásokat deklaráló karakterláncok egyszerű listája. A jól ismert hatókörök a [Microsoft Graph hatókörei](/graph/permissions-reference).

A MSAL.NET a v 1.0 erőforrásokhoz is hozzáfér. Tekintse meg a [v 1.0 alkalmazás hatókörökben](#scopes-for-a-web-api-accepting-v10-tokens)található részleteket. 

### <a name="core-classes"></a>Alapvető osztályok

- A ADAL.NET a [AuthenticationContext](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AuthenticationContext:-the-connection-to-Azure-AD) használja a biztonsági jogkivonat szolgáltatással (STS) vagy az engedélyezési kiszolgálóval való kapcsolat ábrázolására egy szolgáltatón keresztül. Éppen ellenkezőleg, a MSAL.NET az [ügyfélalkalmazások](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Client-Applications)körül van kialakítva. Két külön osztályt biztosít: `PublicClientApplication` és `ConfidentialClientApplication`

- Jogkivonatok beszerzése: a ADAL.NET és a MSAL.NET ugyanazzal a hitelesítési hívásokkal rendelkeznek (`AcquireTokenAsync` és `AcquireTokenSilentAsync` a ADAL.NET, valamint a `AcquireTokenInteractive` és `AcquireTokenSilent` a MSAL.NET), de különböző paraméterek szükségesek. Az egyik különbség az, hogy a MSAL.NET már nem kell átadni az alkalmazás `ClientID` az összes AcquireTokenXX-hívásban. Valójában a `ClientID` csak egyszer van beállítva a (`IPublicClientApplication` vagy `IConfidentialClientApplication`) összeállításakor.

### <a name="iaccount-not-iuser"></a>A IAccount nem IUser

ADAL.NET manipulált felhasználók. A felhasználó azonban egy emberi vagy egy szoftveres ügynök, de a Microsoft Identity System (több Azure AD-fiók, Azure AD B2C, Microsoft Personal accounts) egy vagy több fiókjának felelőse lehet. 

A MSAL.NET 2. x már definiálja a fiók fogalmát (az IAccount felületen keresztül). Ez a feltörési változás biztosítja a megfelelő szemantikai feltételt: az a tény, hogy ugyanaz a felhasználó több fiókkal is rendelkezhet különböző Azure AD-címtárakban. Emellett a MSAL.NET jobb információkat biztosít a vendég forgatókönyvekben, ahogy a Home Account-információk is megtalálhatók.

A IUser és a IAccount közötti különbségekkel kapcsolatos további információkért lásd: [MSAL.NET 2. x](https://aka.ms/msal-net-2-released).

### <a name="exceptions"></a>Kivételek

#### <a name="interaction-required-exceptions"></a>Interakció szükséges kivételek

A MSAL.NET explicit kivételeket tartalmaz. Ha például a csendes hitelesítés meghiúsul a ADAL, az eljárás a kivétel észlelését és a `user_interaction_required` hibakódot keresi:

```csharp
catch(AdalException exception)
{
 if (exception.ErrorCode == “user_interaction_required”)
 {
  try
  {“try to authenticate interactively”}}
 }
}
```

A ADAL.NET- [token beszerzéséhez ajánlott minta](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-a-cached-token#recommended-pattern-to-acquire-a-token) részleteinek megtekintése

A MSAL.NET használatával a `MsalUiRequiredException` a [AcquireTokenSilent](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-a-cached-token)című témakörben leírtak szerint fog megjelenni.

```csharp
catch(MsalUiRequiredException exception)
{
 try {“try to authenticate interactively”}
}
```

#### <a name="handling-claim-challenge-exceptions"></a>Jogcím-kérdésekkel foglalkozó kivételek kezelése

A ADAL.NET a következő módon kezeli a jogcímek kivételeit:

- `AdalClaimChallengeException` a szolgáltatás által kiváltott kivétel (`AdalServiceException`), ha egy erőforrás több jogcímet igényel a felhasználótól (például Kéttényezős hitelesítés). A `Claims` tag tartalmaz néhány JSON-kódrészletet a jogcímek közül, amelyek várhatóak.
- A ADAL.NET továbbra is az ezt a kivételt fogadó nyilvános ügyfélalkalmazás számára meg kell hívnia a jogcím-paraméterrel rendelkező `AcquireTokenInteractive` felülbírálást. A `AcquireTokenInteractive` felülbírálása nem is próbálja meg a gyorsítótárat, mert nem szükséges. Ennek az az oka, hogy a gyorsítótárban lévő jogkivonat nem rendelkezik a megfelelő jogcímek (ellenkező esetben a `AdalClaimChallengeException` nem lettek elvetve). Ezért nem kell megvizsgálnia a gyorsítótárat. Vegye figyelembe, hogy a `ClaimChallengeException` az OBO-t használó WebAPI lehet fogadni, míg a `AcquireTokenInteractive`t a webes API-t hívó nyilvános ügyfélalkalmazás számára kell meghívni.
- a részleteket, például a mintákat lásd: [AdalClaimChallengeException](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Exceptions-in-ADAL.NET#handling-adalclaimchallengeexception) -kezelés

A MSAL.NET a következő módon kezeli a jogcímek kivételeit:

- A `Claims` a `MsalServiceException`ba kerülnek.
- Létezik egy `.WithClaim(claims)` metódus, amely alkalmazható a `AcquireTokenInteractive` Builder szolgáltatásra. 

### <a name="supported-grants"></a>Támogatott támogatások

A MSAL.NET és a v 2.0-végponton nem minden támogatás van támogatva. A ADAL.NET és a MSAL összefoglalása az alábbiakban található. A NET által támogatott támogatások.

#### <a name="public-client-applications"></a>Nyilvános ügyfélalkalmazások

A ADAL.NET és a MSAL.NET által támogatott támogatás asztali és mobil alkalmazásokhoz

Grant | ADAL.NET | MSAL.NET
----- |----- | -----
Interaktív | [Interaktív hitelesítés](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-interactively---Public-client-application-flows) | [A jogkivonatok interaktív beszerzése a MSAL.NET-ben](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Acquiring-tokens-interactively)
Integrált Windows-hitelesítés | [Integrált hitelesítés Windows rendszeren (Kerberos)](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-Integrated-authentication-on-Windows-(Kerberos)) | [Integrált Windows-hitelesítés](msal-authentication-flows.md#integrated-windows-authentication)
Felhasználónév/jelszó | [Tokenek beszerzése felhasználónévvel és jelszóval](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-with-username-and-password)| [Felhasználónév jelszavas hitelesítés](msal-authentication-flows.md#usernamepassword)
Eszköz kódjának folyamata | [Webböngészőt nem tartalmazó eszközökhöz tartozó eszköz profilja](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Device-profile-for-devices-without-web-browsers) | [Eszköz kódjának folyamata](msal-authentication-flows.md#device-code)

#### <a name="confidential-client-applications"></a>Bizalmas ügyfélalkalmazások

A ADAL.NET és a MSAL.NET által támogatott támogatások webalkalmazásokhoz, webes API-khoz és Daemon-alkalmazásokhoz:

Alkalmazás típusa | Grant | ADAL.NET | MSAL.NET
----- | ----- | ----- | -----
Webalkalmazás, webes API, démon | Ügyfél hitelesítő adatai | [Ügyfél-hitelesítő adatok a ADAL.NET-ben](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Client-credential-flows) | [Ügyfél-hitelesítő adatok a MSAL.net-ben](msal-authentication-flows.md#client-credentials)
Webes API | A következő nevében | [A szolgáltatás és a szolgáltatás közötti hívások a felhasználó nevében a ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Service-to-service-calls-on-behalf-of-the-user) | [A MSAL.NET nevében](msal-authentication-flows.md#on-behalf-of)
Web App | Hitelesítési kód | [Jogkivonatok beszerzése engedélyezési kódokkal a Web Apps és a ADAL.NET között](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-with-authorization-codes-on-web-apps) | [Jogkivonatok beszerzése engedélyezési kódokkal a Web Apps MSAL.NET](msal-authentication-flows.md#authorization-code)

### <a name="cache-persistence"></a>Gyorsítótár-megőrzés

A ADAL.NET lehetővé teszi a `TokenCache` osztály kiterjesztését a Biztonságos tár (.NET-keretrendszer és .NET Core) nélküli platformok kívánt adatmegőrzési funkcióinak megvalósítására a `BeforeAccess`és a `BeforeWrite` módszerek használatával. Részletekért lásd: [a tokenek gyorsítótárazásának szerializálása a ADAL.net-ben](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization).

A MSAL.NET lezárt osztályba helyezi a tokent, és megszünteti a kibővítésének lehetőségét. Ezért a jogkivonat-gyorsítótár megőrzésének megvalósítását olyan segítő osztály formájában kell megadni, amely együttműködik a lezárt jogkivonat-gyorsítótárral. Ez az interakció a [jogkivonat-gyorsítótár szerializálása a MSAL.net-](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/token-cache-serialization)ben című témakörben található.

## <a name="signification-of-the-common-authority"></a>A közös hatóság jelentése

Ha a 1.0-s verzióban a https://login.microsoftonline.com/common -szolgáltatót használja, a felhasználók bármelyik HRE-fiókkal bejelentkezhetnek (bármely szervezet esetében). Lásd: [ADAL.net](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AuthenticationContext:-the-connection-to-Azure-AD#authority-validation)

Ha a https://login.microsoftonline.com/common -szolgáltatót használja a 2.0-s verzióban, lehetővé teszi a felhasználók számára, hogy bármilyen HRE-szervezettel vagy személyes Microsoft-fiókkal (MSA) jelentkezzenek be. Ha a MSAL.NET-ben korlátozni szeretné a bejelentkezést bármely HRE-fiókra (ugyanúgy, mint a ADAL.NET esetében), akkor a https://login.microsoftonline.com/organizations t kell használnia. Részletekért tekintse meg a `authority` paramétert a [nyilvános ügyfélalkalmazás alkalmazásban](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Client-Applications#publicclientapplication).

## <a name="v10-and-v20-tokens"></a>1\.0-s és v 2.0-tokenek

A jogkivonatok két verziója létezik:
- 1\.0-s verziós tokenek
- v 2.0-tokenek 

A ADAL által használt v 1.0 végpont csak v 1.0 jogkivonatokat bocsát ki.

A MSAL által használt v 2.0-végpont azonban a webes API által elfogadott token verzióját bocsátja ki. A webes API alkalmazási jegyzékfájljának egyik tulajdonsága lehetővé teszi a fejlesztők számára, hogy a jogkivonat melyik verzióját fogadják el. Tekintse meg `accessTokenAcceptedVersion` az [alkalmazás jegyzékfájljának](reference-app-manifest.md) dokumentációjában.

További információ a 1.0-s és a v 2.0-tokenekről: [Azure Active Directory hozzáférési tokenek](access-tokens.md)

## <a name="scopes-for-a-web-api-accepting-v10-tokens"></a>A webes API-k 1.0-s verziójának elfogadására szolgáló hatókörök

A OAuth2 engedélyek olyan jogosultsági hatókörök, amelyeket egy v 1.0 web API-(erőforrás-) alkalmazás tesz elérhetővé az ügyfélalkalmazások számára. A jogosultsági hatókörök az ügyfélalkalmazások számára is megadhatók a hozzájárulás során. Tekintse meg a [Azure Active Directory Application manifest](active-directory-application-manifest.md)oauth2Permissions című szakaszát.

### <a name="scopes-to-request-access-to-specific-oauth2-permissions-of-a-v10-application"></a>A v 1.0 alkalmazás adott OAuth2 engedélyeihez való hozzáférést kérő hatókörök

Ha jogkivonatokat szeretne beszerezni egy v 1.0 alkalmazás adott hatóköréhez (például a HRE gráfhoz, amely https://graph.windows.net) , akkor létre kell hoznia egy `scopes` a kívánt erőforrás-azonosítónak a kívánt OAuth2-engedéllyel való összefűzésével az adott erőforráshoz.

Ha például a felhasználó nevében egy 1.0-s verziójú webes API-t szeretne elérni, amely `ResourceId`az alkalmazás-azonosító URI-ja, a következőt kell használnia:

```csharp
var scopes = new [] {  ResourceId+"/user_impersonation"};
```

Ha a HRE Graph https://graph.windows.net/) API-val szeretné olvasni és írni a MSAL.NET-Azure Active Directory, akkor a hatókörök listáját az alábbi kódrészlethez hasonlóan kell létrehoznia:

```csharp
ResourceId = "https://graph.windows.net/";
var scopes = new [] { ResourceId + “Directory.Read”, ResourceID + “Directory.Write”}
```

#### <a name="warning-should-you-have-one-or-two-slashes-in-the-scope-corresponding-to-a-v10-web-api"></a>Figyelmeztetés: Ha egy v 1.0 webes API-nak megfelelő hatókörben egy vagy két perjel van

Ha a Azure Resource Manager API-nak megfelelő hatókört szeretné írni (https://management.core.windows.net/) , akkor a következő hatókört kell megadnia (jegyezze fel a két perjelet) 

```csharp
var scopes = new[] {"https://management.core.windows.net//user_impersonation"};
var result = await app.AcquireTokenInteractive(scopes).ExecuteAsync();

// then call the API: https://management.azure.com/subscriptions?api-version=2016-09-01
```

Ennek az az oka, hogy a Resource Manager API perjelet vár a célközönségi jogcímben (`aud`), majd egy perjelet választ az API nevének a hatókörből való elkülönítésére.

Az Azure AD által használt logika a következő:
- A ADAL (v 1.0) végponthoz egy v 1.0 hozzáférési jogkivonat (az egyetlen lehetséges), az AUD = erőforrás
- A MSAL (v 2.0-végpont) esetében Kérjen hozzáférési jogkivonatot egy v 2.0-tokent elfogadó erőforráshoz, AUD = erőforrás. AppId
- A MSAL (v 2.0 Endpoint) esetében, ha egy olyan erőforrás hozzáférési jogkivonatát kérdezi le, amely egy v 1.0 hozzáférési jogkivonatot fogad el (ez a fenti eset), az Azure AD a kért hatókörből elemezi a kívánt célközönséget, így az utolsó perjel előtt mindent megtesz, és használja erőforrás-azonosítóként. Ezért ha a https:\//database.windows.net "https://database.windows.net/ " célközönséget vár, a https:\/ /database.windows.net//.default hatókörét kell kérnie. Lásd még: #[747](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747): az erőforrás URL-címének záró perjele ki van hagyva, ami az SQL-hitelesítési hibát okozta #747


### <a name="scopes-to-request-access-to-all-the-permissions-of-a-v10-application"></a>Hatókörök egy v 1.0 alkalmazás összes engedélyéhez való hozzáférés kérelmezéséhez

Ha például jogkivonatot szeretne beszerezni egy v 1.0 alkalmazás összes statikus hatóköréhez, az egyiket a következőre kell használnia:

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

### <a name="scopes-to-request-in-the-case-of-client-credential-flow--daemon-app"></a>Ügyfél-hitelesítési folyamat/Daemon-alkalmazás esetében kérelmekre vonatkozó hatókörök

Az ügyfél-hitelesítési folyamat esetében az átadandó hatókör `/.default`is lesz. Ez a hatókör közli az Azure AD-vel: "minden olyan alkalmazás-szintű engedély, amelyet a rendszergazda beleegyezett az alkalmazás regisztrálásához.

## <a name="adal-to-msal-migration"></a>ADAL a MSAL áttelepítéséhez

A ADAL.NET v2-ben. X-ben a frissítési tokenek lehetővé tették, hogy a jogkivonatok használata körüli megoldásokat fejlesszen a gyorsítótárazással, valamint a ADAL 2. x által biztosított `AcquireTokenByRefreshToken` metódusok használatával. A megoldások némelyikét olyan forgatókönyvekben használták, mint például:
* Hosszan futó szolgáltatások, amelyek olyan műveleteket végeznek, mint például az irányítópultok frissítése a felhasználók nevében, míg a felhasználók már nem csatlakoznak. 
* Webfarm-forgatókönyvek, amelyek lehetővé teszik az ügyfél számára, hogy az RT-t a webszolgáltatásnak engedélyezze (a gyorsítótárazás az ügyféloldali, a titkosított cookie-t, és nem a kiszolgálóoldali helyet)

A MSAL.NET nem teszi elérhetővé a frissítési jogkivonatokat biztonsági okokból: a MSAL kezeli az Ön számára a frissítő tokeneket. 

Szerencsére a MSAL.NET mostantól egy olyan API-val rendelkezik, amely lehetővé teszi az előző frissítési tokenek (ADAL-ben szerzett) áttelepíthetők a `IConfidentialClientApplication`ba:

```CSharp
/// <summary>
/// Acquires an access token from an existing refresh token and stores it and the refresh token into 
/// the application user token cache, where it will be available for further AcquireTokenSilent calls.
/// This method can be used in migration to MSAL from ADAL v2 and in various integration 
/// scenarios where you have a RefreshToken available. 
/// (see https://aka.ms/msal-net-migration-adal2-msal2)
/// </summary>
/// <param name="scopes">Scope to request from the token endpoint. 
/// Setting this to null or empty will request an access token, refresh token and ID token with default scopes</param>
/// <param name="refreshToken">The refresh token from ADAL 2.x</param>
IByRefreshToken.AcquireTokenByRefreshToken(IEnumerable<string> scopes, string refreshToken);
```
 
Ezzel a módszerrel megadhatja a korábban használt frissítési jogkivonatot a kívánt hatókörökkel (erőforrásokkal) együtt. A frissítési tokent egy újat cseréli ki a rendszer, és gyorsítótárazza az alkalmazásba.  

Mivel ez a módszer nem jellemző forgatókönyvekhez készült, nem érhető el könnyen a `IConfidentialClientApplication` anélkül, hogy először a `IByRefreshToken`.

Ez a kódrészlet egy bizalmas ügyfélalkalmazás egyes áttelepítési kódját jeleníti meg. `GetCachedRefreshTokenForSignedInUser` beolvasni az alkalmazás egy korábbi verziójában tárolt frissítési tokent, amelyet a 2. x ADAL kihasznál. `GetTokenCacheForSignedInUser` deszerializálja a bejelentkezett felhasználó gyorsítótárát (mivel a bizalmas ügyfélalkalmazások felhasználónként egy gyorsítótárat kell rendelkezniük).

```csharp
TokenCache userCache = GetTokenCacheForSignedInUser();
string rt = GetCachedRefreshTokenForSignedInUser();

IConfidentialClientApplication app;
app = ConfidentialClientApplicationBuilder.Create(clientId)
 .WithAuthority(Authority)
 .WithRedirectUri(RedirectUri)
 .WithClientSecret(ClientSecret)
 .Build();
IByRefreshToken appRt = app as IByRefreshToken;
         
AuthenticationResult result = await appRt.AcquireTokenByRefreshToken(null, rt)
                                         .ExecuteAsync()
                                         .ConfigureAwait(false);
```

Ekkor megjelenik egy hozzáférési jogkivonat és egy azonosító jogkivonat, amelyet a rendszer visszaadott a AuthenticationResult, miközben az új frissítési tokent a gyorsítótárban tárolja.

Ezt a módszert olyan különböző integrációs forgatókönyvek esetében is használhatja, amelyeken elérhető frissítési jogkivonat.

## <a name="next-steps"></a>Következő lépések

A hatókörökkel [, engedélyekkel és a Microsoft Identity platform végpontjában](v2-permissions-and-consent.md) található hatókörökkel kapcsolatos további információk
