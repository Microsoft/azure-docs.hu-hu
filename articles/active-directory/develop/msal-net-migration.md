---
title: Áttelepítés MSAL.NET
titleSuffix: Microsoft identity platform
description: Ismerje meg a Microsoft Authentication Library for .NET (MSAL.NET) és az Azure AD Authentication Library for .NET (ADAL.NET) közötti különbségeket, valamint az áttelepítés MSAL.NET.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/10/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 49b5da2da72e78226db19f5d8881073577aee5b0
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575520"
---
# <a name="migrating-applications-to-msalnet"></a>Alkalmazások áttelepítése MSAL.NET

A Microsoft Authentication Library for .NET (MSAL.NET) és az Azure AD Authentication Library for .NET (ADAL.NET) az Azure AD-entitások hitelesítésére és az Azure AD-tól való jogkivonatok lekérésére szolgál. Eddig a legtöbb fejlesztő az Azure AD fejlesztői platformmal (v1.0) dolgozott az Azure AD-identitások (munkahelyi és iskolai fiókok) hitelesítéséhez az Azure AD Authentication Library (ADAL) használatával jogkivonatok lekért használatával. Az MSAL használata:

- A Microsoft identitásplatformja által használt Microsoft-identitások (Azure AD-identitások és Microsoft-fiókok, valamint közösségi és helyi fiókok Azure AD B2C) szélesebb körének hitelesítésére is van képes.
- A felhasználók a legjobb egyszeri bejelentkezési élményt kapják.
- az alkalmazás engedélyezheti a növekményes hozzájárulást, és a feltételes hozzáférés támogatása egyszerűbb
- az innovációt használja ki.

MSAL.NET Microsoft.Identity.Web az ajánlott hitelesítési kódtárak, amelyek **a Microsoft identitásplatformján használhatók.** Az új funkciók nem lesznek megvalósítva a ADAL.NET. A erőfeszítések az MSAL javítására összpontosítanak.

Ez a cikk a Microsoft Authentication Library for .NET (MSAL.NET) és az Azure AD Authentication Library for .NET (ADAL.NET) közötti különbségeket ismerteti, és segítséget nyújt az MSAL-re való áttelepítéshez.

## <a name="should-you-migrate-to-msalnet-or-to-microsoftidentityweb"></a>Ha a microsoftos vagy a MSAL.NET microsoft.identity.web webhelyre kell áttértenie

Mielőtt részletezné a MSAL.NET és ADAL.NET részleteit, ellenőrizze, hogy szeretné-e használni a MSAL.NET-t vagy egy magasabb szintű absztrakciót, például a [Microsoft.Identity.Web-t.](microsoft-identity-web.md)

Az alábbi döntési fával kapcsolatos részletekért olvassa el a Csak MSAL.NET használok? vagy magasabb szintű [absztrakciót?](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Is-MSAL.NET-right-for-me%3F)

:::image type="content" source="media/msal-net-migration/decision-diagram.png" alt-text="Blokkdiagram, amely bemutatja, hogyan választhatja ki, hogy a MSAL.NET és a Microsoft.Identity.Web webhelyet, vagy mindkettőt kell használnia a ADAL.NET":::

## <a name="differences-between-adal-and-msal-apps"></a>Az ADAL- és az MSAL-alkalmazások közötti különbségek

A legtöbb esetben az MSAL.NET a Microsoft identitásplatformját szeretné használni, amely a Microsoft hitelesítési kódtárainak legújabb generációja. A MSAL.NET jogkivonatokat szerez be az alkalmazásba Azure AD-val (munkahelyi és iskolai fiókokkal), Microsoft (személyes) fiókokkal (MSA) vagy Azure AD B2C.

Ha már ismeri az Azure AD fejlesztőknek (1.0-s verziójának) végpontját (és ADAL.NET), érdemes elolvasni a Mi a különbség a Microsoft identitásplatformján? [fejezetet.](../azuread-dev/azure-ad-endpoint-comparison.md)

Azonban továbbra is a ADAL.NET kell használnia, ha az alkalmazásnak a Active Directory összevonási szolgáltatások (AD FS) [(ADFS)](/windows-server/identity/active-directory-federation-services)korábbi verzióival kell bejelentkeznie. További információ: [ADFS-támogatás.](https://aka.ms/msal-net-adfs-support)

Az alábbi képen az egymás mellett ADAL.NET és MSAL.NET különbségeket ![ összegzi](./media/msal-compare-msaldotnet-and-adaldotnet/differences.png)

### <a name="nuget-packages-and-namespaces"></a>NuGet-csomagok és névterek

ADAL.NET [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) NuGet-csomag tartalmazza. A használnia kell a `Microsoft.IdentityModel.Clients.ActiveDirectory` névteret.

A MSAL.NET a [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) NuGet-csomagot, és a `Microsoft.Identity.Client` névteret kell használnia

### <a name="scopes-not-resources"></a>A hatókörök nem erőforrások

ADAL.NET a erőforrásokhoz szerez be jogkivonatokat, MSAL.NET pedig a hatókörök *jogkivonatát.* Az AcquireToken MSAL.NET esetében szükség van egy scopes( ) nevű `IEnumerable<string> scopes` paraméterre. Ez a paraméter a kért engedélyeket és erőforrásokat deklaráló sztringek egyszerű listája. A jól ismert hatókörök a Microsoft Graph [hatókörei.](https://docs.microsoft.com/graph/permissions-reference)

Az 1.0-s MSAL.NET is elérhető. A részleteket [lásd: Az 1.0-s alkalmazás hatókörei.](#scopes-for-a-web-api-accepting-v10-tokens)

### <a name="core-classes"></a>Alaposztályok

- ADAL.NET hitelesítésszolgáltatón keresztül az [AuthenticationContext](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AuthenticationContext:-the-connection-to-Azure-AD) tulajdonságot használja a biztonsági jogkivonat-szolgáltatással (STS) vagy az engedélyezési kiszolgálóval való kapcsolat reprezentációjaként. Ezzel szemben a MSAL.NET [ügyfélalkalmazások köré van tervezve.](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Client-Applications) Két külön osztályt biztosít: `PublicClientApplication` és `ConfidentialClientApplication`

- Jogkivonatok beszerzése: ADAL.NET és MSAL.NET azonos hitelesítési hívásokkal ( és ADAL.NET és MSAL.NET) rendelkezik, de különböző paraméterek `AcquireTokenAsync` `AcquireTokenSilentAsync` `AcquireTokenInteractive` `AcquireTokenSilent` szükségesek. Az egyik különbség az, hogy MSAL.NET már nem kell átadnia az alkalmazását minden `ClientID` AcquireTokenXX hívásban. Valójában a `ClientID` csak egyszer van beállítva a ( vagy a ) `IPublicClientApplication` `IConfidentialClientApplication` kiépítésekor.

### <a name="iaccount-not-iuser"></a>IAccount not IUser

ADAL.NET felhasználókkal. A felhasználó azonban ember vagy szoftverügynök, de birtokolhatja/birtokolhatja/felelős lehet egy vagy több fiókért a Microsoft identitásrendszerében (több Azure AD-fiók, Azure AD B2C microsoftos személyes fiók).

MSAL.NET 2.x a Fiók fogalmat határozza meg (az IAccount felületen keresztül). Ez a feltöréses változás biztosítja a megfelelő szemantikát: az a tény, hogy ugyanaz a felhasználó több fiókkal is rendelkezik, különböző Azure AD-könyvtárakban. A MSAL.NET is jobb információkat biztosítanak a vendégforgatókönyvek esetében, mivel az otthoni fiók adatait is meg kell adni.

További információ az IUser és az IAccount közötti különbségekről: [MSAL.NET 2.x.](https://aka.ms/msal-net-2-released)

### <a name="exceptions"></a>Kivételek

#### <a name="interaction-required-exceptions"></a>Az interakcióhoz szükséges kivételek

MSAL.NET konkrétabb kivételekkel rendelkezik. Ha például a csendes hitelesítés meghiúsul az ADAL-ban, az eljárás a kivételt észlelte, és keresse meg a `user_interaction_required` hibakódot:

```csharp
catch(AdalException exception)
{
 if (exception.ErrorCode == "user_interaction_required")
 {
  try
  {“try to authenticate interactively”}}
 }
}
```

Részletekért lásd a [következőt: Az ajánlott minta a jogkivonatok lekért ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-a-cached-token#recommended-pattern-to-acquire-a-token)

A MSAL.NET az `MsalUiRequiredException` [AcquireTokenSilent (Jogbeszerzési jog használata) leírásnak](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-a-cached-token)megfelelőt kap.

```csharp
catch(MsalUiRequiredException exception)
{
 try {“try to authenticate interactively”}
}
```

#### <a name="handling-claim-challenge-exceptions"></a>Jogcím-kihívást igénylő kivételek kezelése

A ADAL.NET jogcímek kivételei a következő módon vannak kezelve:

- `AdalClaimChallengeException` A egy kivétel (származtatása a osztályból), amelyet a szolgáltatás arra az esetre ad vissza, ha egy erőforrás több jogcímet igényel a felhasználótól (például kéttényezős `AdalServiceException` hitelesítés). A `Claims` tag tartalmaz néhány JSON-töredéket a jogcímekkel, amelyek várhatóak.
- A kivételt ADAL.NET nyilvános ügyfélalkalmazásnak még mindig meg kell hívnia a felülbírálást egy `AcquireTokenInteractive` jogcímparaméterrel. A `AcquireTokenInteractive` felülbírálása nem is próbálkozik a gyorsítótárral, mert nem szükséges. Ennek az az oka, hogy a gyorsítótárban lévő jogkivonat nem rendelkezik a megfelelő jogcímekkel (ellenkező esetben a nem `AdalClaimChallengeException` lett volna dobva). Ezért nincs szükség a gyorsítótárra. Vegye figyelembe, hogy az egy OBO-t használó WebAPI-ban is átveheti, míg a hívását egy nyilvános ügyfélalkalmazásban kell hívni, amely ezt `ClaimChallengeException` `AcquireTokenInteractive` a webes API-t hívja meg.
- a részletekért, beleértve a mintákat is, lásd: [Handling AdalClaimCllengeException (Az AdalClaimClaimLengeException kezelése)](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Exceptions-in-ADAL.NET#handling-adalclaimchallengeexception)

A MSAL.NET jogcímek kivételei a következő módon vannak kezelve:

- A `Claims` a felületén `MsalServiceException` található.
- Van egy `.WithClaim(claims)` metódus, amely alkalmazható a `AcquireTokenInteractive` szerkesztőre.

### <a name="supported-grants"></a>Támogatott jogosultságok

A MSAL.NET és a 2.0-s verzió végpontja még nem támogatja az összes engedélyt. Az alábbiakban egy összefoglalót látható, amely összehasonlítja a ADAL.NET MSAL-t. A NET által támogatott jogosultságok.

#### <a name="public-client-applications"></a>Nyilvános ügyfélalkalmazások

Az asztali és mobilalkalmazások ADAL.NET és MSAL.NET támogatott támogatás a következő:

Engedély | ADAL.NET | MSAL.NET
----- |----- | -----
Interaktív | [Interaktív hitelesítés](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-interactively---Public-client-application-flows) | [Jogkivonatok interaktív beszerzése a MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Acquiring-tokens-interactively)
Beépített Windows-hitelesítés | [Integrált hitelesítés Windows rendszeren (Kerberos)](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-Integrated-authentication-on-Windows-(Kerberos)) | [Integrált Windows-hitelesítés](msal-authentication-flows.md#integrated-windows-authentication)
Felhasználónév/jelszó | [Jogkivonatok beszerzése felhasználónévvel és jelszóval](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-with-username-and-password)| [Felhasználónév- és jelszóhitelesítés](msal-authentication-flows.md#usernamepassword)
Eszközkódfolyam | [Eszközprofil webböngésző nélküli eszközökhöz](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Device-profile-for-devices-without-web-browsers) | [Eszközkód-folyamat](msal-authentication-flows.md#device-code)

#### <a name="confidential-client-applications"></a>Bizalmas ügyfélalkalmazások

A webalkalmazások, webes API ADAL.NET MSAL.NET és démonalkalmazások esetében a következő támogatási ADAL.NET támogatottak:

Alkalmazás típusa | Engedély | ADAL.NET | MSAL.NET
----- | ----- | ----- | -----
Webalkalmazás, webes API, démon | Ügyfél hitelesítő adatai | [Ügyfél-hitelesítőadat-folyamatok a ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Client-credential-flows) | [Ügyfél-hitelesítőadat-folyamatok a MSAL.NET](msal-authentication-flows.md#client-credentials)
Webes API | A nevében | [Szolgáltatásokra történő hívások a felhasználó nevében a ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Service-to-service-calls-on-behalf-of-the-user) | [A nevében a MSAL.NET](msal-authentication-flows.md#on-behalf-of)
Webalkalmazás | Hitelesítési kód | [Jogkivonatok beszerzése hitelesítési kódokkal a webalkalmazásokban ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-with-authorization-codes-on-web-apps) | [Jogkivonatok beszerzése hitelesítési kódokkal a webalkalmazásokban az A MSAL.NET](msal-authentication-flows.md#authorization-code)

### <a name="cache-persistence"></a>Gyorsítótár-megőrzés

ADAL.NET segítségével kiterjesztheti a osztályt, hogy biztonságos tárolás (.NET-keretrendszer és .NET Core) nélküli platformokon megvalósítsa a kívánt adatmegőrzési funkciót a és a `TokenCache` `BeforeAccess` `BeforeWrite` metódussal. Részletekért lásd a [tokengyorsítótár szerializálásával kapcsolatos ADAL.NET.](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization)

MSAL.NET a jogkivonat-gyorsítótárat lezárt osztályba teszi, így nem lehet kibővíteni. Ezért a jogkivonat-gyorsítótár megőrzésének megvalósítása egy olyan segítőosztálynak kell lennie, amely együttműködik a lezárt jogkivonat-gyorsítótárral. Ezt az interakciót a [tokengyorsítótár szerializálását](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/token-cache-serialization)a következő MSAL.NET: .

## <a name="signification-of-the-common-authority"></a>A közös hitelesítésszolgáltató aláírásával

Ha az 1.0-s és a -hitelesítésszolgáltatót használja, lehetővé teszi a felhasználóknak, hogy bármely AAD-fiókkal jelentkezzenek be `https://login.microsoftonline.com/common` (bármely szervezet számára). Lásd: [Hitelesítésszolgáltató érvényesítése a ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AuthenticationContext:-the-connection-to-Azure-AD#authority-validation)

Ha a 2.0-s verziójában használja a hitelesítésszolgáltatót, lehetővé teszi a felhasználóknak, hogy bármely AAD-szervezettel vagy személyes Microsoft-fiókkal `https://login.microsoftonline.com/common` (MSA) jelentkezzenek be. Ha MSAL.NET AAD-fiókra szeretné korlátozni a bejelentkezést (ugyanúgy, mint a ADAL.NET), használja a következőt: `https://login.microsoftonline.com/organizations` . Részletekért tekintse meg a `authority` paramétert a [nyilvános ügyfélalkalmazásban.](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Client-Applications#publicclientapplication)

## <a name="v10-and-v20-tokens"></a>1.0-s és 2.0-s tokenek

A jogkivonatok két verzióban vannak:
- 1.0-s virtuális jogkivonatok
- 2.0-s tokenek

Az 1.0-s végpont (amelyet az ADAL használ) csak 1.0-s tokeneket bocsát ki.

Az MSAL által használt 2.0-s verziójú végpont azonban a webes API által elfogadt jogkivonat verzióját bocsátja ki. A webes API alkalmazásjegyzékének egyik tulajdonsága lehetővé teszi a fejlesztők számára, hogy kiválasztják a jogkivonat elfogadott verzióját. Lásd `accessTokenAcceptedVersion` az alkalmazásjegyzék [referenciadokumentációját.](reference-app-manifest.md)

További információ az 1.0-s és 2.0-s Azure Active Directory [jogkivonatokkal kapcsolatban](access-tokens.md)

## <a name="scopes-for-a-web-api-accepting-v10-tokens"></a>Az 1.0-s verziós jogkivonatokat elfogadó webes API hatókörei

Az OAuth2-engedélyek olyan engedélyhatókörök, amelyekhez egy 1.0-s verziós webes API-t (erőforrást) használó alkalmazás elérhetővé teszi az ügyfélalkalmazásokat. Ezek az engedélyhatókörök a jóváhagyáskor adhatók meg az ügyfélalkalmazások számára. Lásd az oauth2Permissions [alkalmazásjegyzékben Azure Active Directory szakaszát.](./reference-app-manifest.md)

### <a name="scopes-to-request-access-to-specific-oauth2-permissions-of-a-v10-application"></a>Hatókörök, amelyek egy 1.0-s alkalmazás adott OAuth2-engedélyeinek hozzáférését kérik

Ha olyan alkalmazáshoz szeretne jogkivonatokat szerezni, amelyek 1.0-s verziós jogkivonatokat fogadnak (például a Microsoft Graph API-t, azaz a et, létre kell hoznia egy kívánt erőforrás-azonosítót a kívánt https://graph.microsoft.com) OAuth2-engedéllyel az adott `scopes` erőforráshoz.

Ha például a felhasználó nevében szeretne hozzáférni egy 1.0-s verziójú webes API-hoz, amelynek az alkalmazásazonosító URI-ja , a `ResourceId` következőt kell használnia:

```csharp
var scopes = new [] { ResourceId+"/user_impersonation" };
```

Ha a MSAL.NET Azure Active Directory API Microsoft Graph () használatával szeretne olvasni és írni, a következő kódrészlethez hasonló hatókörlistát kell https://graph.microsoft.com/) létrehoznia:

```csharp
string ResourceId = "https://graph.microsoft.com/"; 
string[] scopes = { ResourceId + "Directory.Read", ResourceId + "Directory.Write" }
```

#### <a name="warning-should-you-have-one-or-two-slashes-in-the-scope-corresponding-to-a-v10-web-api"></a>Figyelmeztetés: Ha egy vagy két perjel van a hatókörben, amely egy 1.0-s verziós webes API-nak megfelelő

Ha meg szeretné írni a Azure Resource Manager API ( ) hatókörét, kérje le a következő hatókört (jegyezze fel a két https://management.core.windows.net/) perjelet).

```csharp
var scopes = new[] {"https://management.core.windows.net//user_impersonation"};
var result = await app.AcquireTokenInteractive(scopes).ExecuteAsync();

// then call the API: https://management.azure.com/subscriptions?api-version=2016-09-01
```

Ennek az az oka Resource Manager hogy a Resource Manager API perjelet vár a célközönség jogcímében ( ), majd egy perjel választja el az API nevét `aud` a hatókörtől.

Az Azure AD által használt logika a következő:
- 1.0-s v1.0-s hozzáférési jogkivonattal (az egyetlen lehetséges) ADAL- (1.0-s) végpont esetén: aud=resource
- Ha az MSAL (2.0-s végpont) hozzáférési jogkivonatot kér egy 2.0-s hitelesítési jogkivonatot elfogadó erőforráshoz, az aud=resource. Appid
- Ha az MSAL (2.0-s végpont) egy 1.0-s hozzáférési jogkivonatot elfogadó erőforráshoz kér hozzáférési jogkivonatot (ami a fenti eset), az Azure AD úgy elemezi a kívánt célközönséget a kért hatókörből, hogy mindent az utolsó perjel előtt használ, és erőforrás-azonosítóként használja. Ezért ha a https: /database.windows.net "" célközönséget vár, a \/ https://database.windows.net/ \/ /database.windows.net//.default. Lásd még a[747-es](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747)számú problémát: Az erőforrás URL-címének záró perjele nincs megadva, ami az SQL-hitelesítési hiba #747


### <a name="scopes-to-request-access-to-all-the-permissions-of-a-v10-application"></a>Az 1.0-s alkalmazás összes engedélyének hozzáférését kérelmező hatókörök

Ha például egy 1.0-s alkalmazás összes statikus hatóköre számára szeretne jogkivonatot szerezni, a következőt kell használnia:

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] { ResourceId+"/.default" };
```

### <a name="scopes-to-request-in-the-case-of-client-credential-flow--daemon-app"></a>Az ügyfél-hitelesítő adatok folyama/démonalkalmazás esetén lekért hatókörök

Ügyfél-hitelesítő adatok folyamatában a átadható hatókör is a következő lenne: `/.default` . Ez a hatókör a következőt mondja az Azure AD-nek: "az összes olyan alkalmazásszintű engedély, amelyekhez a rendszergazda hozzájárult az alkalmazásregisztrációban.

## <a name="adal-to-msal-migration"></a>Migrálás ADAL-ről MSAL-be

A ADAL.NET v2-ben. X, a frissítési jogkivonatok elérhetővé voltak téve, így az ADAL 2.x által biztosított módszerekkel gyorsítótárazva és használva megoldásokat fejleszthet a jogkivonatok `AcquireTokenByRefreshToken` használatára.
Ezek közül néhány megoldást például a következő helyzetekben használtak:
* Hosszú ideig futó szolgáltatások, amelyek műveleteket, például az irányítópultok frissítését a felhasználók nevében, míg a felhasználók már nem csatlakoznak.
* WebFarm-forgatókönyvek, amelyek lehetővé teszik az ügyfél számára, hogy az RT-t a webszolgáltatásba hozza (a gyorsítótárazás ügyféloldalon, titkosított cookie-val, és nem kiszolgálóoldalon történik)

MSAL.NET biztonsági okokból nem teszi elérhetővé a frissítési jogkivonatokat: Az MSAL kezeli a jogkivonatok frissítését.

Szerencsére MSAL.NET már rendelkezik egy OLYAN API-val, amely lehetővé teszi a korábbi frissítési jogkivonatok (az ADAL használatával beszerzett) áttelepítését `IConfidentialClientApplication` a-be:

```csharp
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

Ezzel a módszerrel meg lehet adni a korábban használt frissítési jogkivonatot a használni kíván hatókörök (erőforrások) mellett. A frissítési jogkivonat egy újra lesz cserélve, és az alkalmazásba lesz gyorsítótárazva.

Mivel ez a módszer nem tipikus forgatókönyvekhez használható, a használatával nem érhető el anélkül, hogy a metódust a -nak `IConfidentialClientApplication` átveszi. `IByRefreshToken`

Ez a kódrészlet néhány migrálási kódot mutat be egy bizalmas ügyfélalkalmazásban. `GetCachedRefreshTokenForSignedInUser` az ADAL 2.x-et kihasználó alkalmazás egy korábbi verziója által egy tárolóban tárolt frissítési jogkivonat lekérése. `GetTokenCacheForSignedInUser` deserializálja a bejelentkezett felhasználó gyorsítótárát (mivel a bizalmas ügyfélalkalmazások felhasználónként egy gyorsítótárral kell, hogy egy gyorsítótárral)

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

Az AuthenticationResult egy hozzáférési jogkivonatot és egy azonosító jogkivonatot ad vissza, amíg az új frissítési jogkivonat a gyorsítótárban van tárolva.

Ezt a módszert olyan különböző integrációs forgatókönyvekhez is használhatja, ahol rendelkezésre áll frissítési jogkivonat.

## <a name="next-steps"></a>Következő lépések

További információt a hatókörökről a Hatókörök, engedélyek és hozzájárulás a [Microsoft identitásplatformján találhat.](v2-permissions-and-consent.md)
