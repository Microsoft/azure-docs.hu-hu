---
title: Azure AD B2C és MSAL.NET
titleSuffix: Microsoft identity platform
description: Megfontolandó szempontok a .NET-hez készült Microsoft Authentication Library (MSAL.NET) használatával való Azure AD B2C használatához.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2020
ms.author: jeferrie
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: b28454e9b60654541d4f62ec1d8455b30cfc2906
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2021
ms.locfileid: "99580827"
---
# <a name="use-msalnet-to-sign-in-users-with-social-identities"></a>A MSAL.NET használata a felhasználók közösségi identitásokkal való bejelentkezéséhez

A MSAL.NET használatával közösségi identitásokkal jelentkezhet be a felhasználókba [Azure Active Directory B2C (Azure ad B2C)](../../active-directory-b2c/overview.md)használatával. Azure AD B2C a szabályzatok fogalma köré épül fel. A MSAL.NET-ben egy szabályzatot kell megadnia, amely egy szolgáltatót biztosít.

- A nyilvános ügyfélalkalmazás létrehozásakor a házirend részeként határozza meg a szabályzatot.
- Ha szabályzatot szeretne alkalmazni, hívja meg `AcquireTokenInteractive` a paramétert tartalmazó felülbírálást `authority` .

Ez a cikk a 3. x MSAL.NET vonatkozik. A 2. x MSAL.NET Azure AD B2C tekintse meg a [MSAL 2. x verziójában](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-Specifics-MSAL-2.x) a MSAL.net wikiben a githubon.

## <a name="authority-for-an-azure-ad-b2c-tenant-and-policy"></a>Azure AD B2C bérlő és házirend szolgáltatója

A Azure AD B2C szolgáltatói formátuma a következő: `https://{azureADB2CHostname}/tfp/{tenant}/{policyName}`

- `azureADB2CHostname` – A Azure AD B2C bérlő és a gazdagép neve. Például: *contosob2c.b2clogin.com*.
- `tenant` – A Azure AD B2C bérlő tartománynevét vagy könyvtárát (bérlői AZONOSÍTÓját). Például: *contosob2c.onmicrosoft.com* vagy GUID.
- `policyName` – Az alkalmazandó felhasználói folyamat vagy egyéni szabályzat neve. Például egy regisztrálási vagy bejelentkezési szabályzat, például *b2c_1_susi*.

A Azure AD B2C-hatóságokkal kapcsolatos további információkért lásd: [átirányítási URL-címek beállítása b2clogin.com](../../active-directory-b2c/b2clogin.md).

## <a name="instantiating-the-application"></a>Az alkalmazás példányának példánya

Adja meg a szolgáltatót az `WithB2CAuthority()` Application objektum létrehozásakor:

```csharp
// Azure AD B2C Coordinates
public static string Tenant = "fabrikamb2c.onmicrosoft.com";
public static string AzureADB2CHostname = "fabrikamb2c.b2clogin.com";
public static string ClientID = "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6";
public static string PolicySignUpSignIn = "b2c_1_susi";
public static string PolicyEditProfile = "b2c_1_edit_profile";
public static string PolicyResetPassword = "b2c_1_reset";

public static string AuthorityBase = $"https://{AzureADB2CHostname}/tfp/{Tenant}/";
public static string Authority = $"{AuthorityBase}{PolicySignUpSignIn}";
public static string AuthorityEditProfile = $"{AuthorityBase}{PolicyEditProfile}";
public static string AuthorityPasswordReset = $"{AuthorityBase}{PolicyResetPassword}";

application = PublicClientApplicationBuilder.Create(ClientID)
               .WithB2CAuthority(Authority)
               .Build();
```

## <a name="acquire-a-token-to-apply-a-policy"></a>Jogkivonat beszerzése házirend alkalmazásához

Egy Azure AD B2C által védett API-hoz egy nyilvános ügyfélalkalmazás jogkivonatának beszerzéséhez a felülbírálásokat egy szolgáltatóval kell használni:

```csharp
IEnumerable<IAccount> accounts = await application.GetAccountsAsync();
AuthenticationResult ar = await application.AcquireTokenInteractive(scopes)
                                           .WithAccount(GetAccountByPolicy(accounts, policy))
                                           .WithParentActivityOrWindow(ParentActivityOrWindow)
                                           .ExecuteAsync();
```

Az előző kódrészletben:

- `policy` egy olyan karakterlánc, amely tartalmazza a Azure AD B2C felhasználói folyamat vagy az egyéni házirend nevét (például: `PolicySignUpSignIn` ).
- `ParentActivityOrWindow` az Androidhoz (a tevékenységhez) szükséges, és nem kötelező más platformokhoz, amelyek támogatják a szülő felhasználói felületet, például a Windowst a Microsoft Windows rendszeren és az iOS-UIViewController. További információ a felhasználói felületi párbeszédpanelről: [WithParentActivityOrWindow](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Acquiring-tokens-interactively#withparentactivityorwindow) a MSAL wikin.
- `GetAccountByPolicy(IEnumerable<IAccount>, string)` egy olyan metódus, amely megkeresi az adott szabályzathoz tartozó fiókot. Például:

  ```csharp
  private IAccount GetAccountByPolicy(IEnumerable<IAccount> accounts, string policy)
  {
      foreach (var account in accounts)
      {
          string userIdentifier = account.HomeAccountId.ObjectId.Split('.')[0];
          if (userIdentifier.EndsWith(policy.ToLower()))
              return account;
      }
      return null;
  }
  ```

A felhasználói folyamat vagy az egyéni házirend alkalmazása (például a felhasználó szerkesztési profiljának vagy a jelszó alaphelyzetbe állításának engedélyezése) jelenleg a meghívásával történik `AcquireTokenInteractive` . Ebben a két házirendben nem használja a visszaadott jogkivonat/hitelesítési eredményt.

## <a name="profile-edit-policies"></a>Profil szerkesztése házirendek

Annak engedélyezéséhez, hogy a felhasználók bejelentkezzenek a közösségi identitásba, majd szerkesszék a profilt, alkalmazza a profil szerkesztése Azure AD B2C.

Ezt úgy teheti `AcquireTokenInteractive` meg, ha meghívja a szolgáltatót az adott szabályzathoz. Mivel a felhasználó már be van jelentkezve, és aktív cookie-munkamenettel rendelkezik, a használatával `Prompt.NoPrompt` megakadályozhatja a fiók kijelölési párbeszédpanelének megjelenítését.

```csharp
private async void EditProfileButton_Click(object sender, RoutedEventArgs e)
{
    IEnumerable<IAccount> accounts = await app.GetAccountsAsync();
    try
    {
        var authResult = await app.AcquireToken(scopes:App.ApiScopes)
                            .WithAccount(GetUserByPolicy(accounts, App.PolicyEditProfile)),
                            .WithPrompt(Prompt.NoPrompt),
                            .WithB2CAuthority(App.AuthorityEditProfile)
                            .ExecuteAsync();
        DisplayBasicTokenInfo(authResult);
    }
    catch
    {
    }
}
```

## <a name="resource-owner-password-credentials-ropc"></a>Erőforrás-tulajdonos jelszavának hitelesítő adatai (ROPC)

A ROPC folyamattal kapcsolatos további információkért lásd: [Bejelentkezés erőforrás-tulajdonosi jelszó hitelesítő adataival](v2-oauth-ropc.md).

A ROPC folyamat **nem ajánlott** , mert a felhasználó jelszavának megkérdezése nem biztonságos. A problémával kapcsolatos további információkért lásd: [Mi a megoldás a jelszavak növekvő problémájára?](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/).

A Felhasználónév/jelszó használatával ROPC-folyamatokban több dolgot is feláldozhat:

- A modern identitás alapvető alapelvei: a jelszó megoldható vagy visszajátszható, mert a közös titkos kulcs elfogása lehetséges. Definíció szerint a ROPC nem kompatibilis a jelszóval nem rendelkező folyamatokkal.
- Az MFA-t igénylő felhasználóknak nem lehet bejelentkezniük (mivel nincs interakció).
- A felhasználók nem tudják használni az egyszeri bejelentkezést (SSO).

### <a name="configure-the-ropc-flow-in-azure-ad-b2c"></a>A ROPC folyamat konfigurálása Azure AD B2C

A Azure AD B2C-bérlőben hozzon létre egy új felhasználói folyamatot, és válassza a **Bejelentkezés a ROPC használatával** lehetőséget a felhasználói folyamat ROPC engedélyezéséhez. További információ: [az erőforrás-tulajdonos jelszava hitelesítő adatainak konfigurálása](../../active-directory-b2c/add-ropc-policy.md).

`IPublicClientApplication` a `AcquireTokenByUsernamePassword` metódust tartalmazza:

```csharp
AcquireTokenByUsernamePassword(
            IEnumerable<string> scopes,
            string username,
            SecureString password)
```

Ez `AcquireTokenByUsernamePassword` a metódus a következő paramétereket veszi figyelembe:

- Azok a *hatókörök* , amelyekhez hozzáférési tokent kell beszerezni.
- Egy *Felhasználónév*.
- A felhasználó SecureString *jelszava* .

### <a name="limitations-of-the-ropc-flow"></a>A ROPC folyamat korlátai

A ROPC folyamat **csak a helyi fiókok esetében működik**, ahol a felhasználók e-mail-cím vagy Felhasználónév használatával regisztráltak Azure ad B2C. Ez a folyamat nem működik, ha a Azure AD B2C (Facebook, Google stb.) által támogatott külső egyesítő.

## <a name="google-auth-and-embedded-webview"></a>Google-hitelesítés és beágyazott webnézet

Ha a Google-t identitás-szolgáltatóként használja, javasoljuk, hogy a rendszerböngészőt használja, mivel a Google nem engedélyezi a [hitelesítést a beágyazott webnézetekben](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html). Jelenleg `login.microsoftonline.com` egy megbízható szolgáltató a Google-szel, és a beágyazott webnézettel fog működni. A `b2clogin.com` Google azonban nem megbízható hatóság, így a felhasználók nem fognak tudni hitelesíteni.

Ha változnak, a [probléma](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/688) frissítését is biztosítjuk.

## <a name="token-caching-in-msalnet"></a>Token gyorsítótárazása a MSAL.NET-ben

### <a name="known-issue-with-azure-ad-b2c"></a>Ismert probléma a Azure AD B2C

A MSAL.NET támogatja a [jogkivonat-gyorsítótárat](/dotnet/api/microsoft.identity.client.tokencache). A jogkivonat-gyorsítótárazási kulcs az identitás-szolgáltató (identitásszolgáltató) által visszaadott jogcímek alapján történik.

A MSAL.NET jelenleg két jogcímet igényel a jogkivonat-gyorsítótár kulcsának létrehozásához:

- `tid` (az Azure AD-bérlő azonosítója)
- `preferred_username`

Előfordulhat, hogy mindkét jogcím hiányzik Azure AD B2C forgatókönyvekben, mert nem minden közösségi identitás-szolgáltató (Facebook, Google és mások) visszaadja azokat a jogkivonatokban, amelyeket visszaadnak Azure AD B2Cnak.

Egy ilyen forgatókönyv tünete az, hogy a MSAL.NET akkor adja vissza, `Missing from the token response` Amikor a `preferred_username` Azure ad B2C által kiállított jogkivonatokban eléri a jogcím értékét. A MSAL a `Missing from the token response` értéket használja a `preferred_username` gyorsítótárak közötti kompatibilitás fenntartásához a kódtárak között.

### <a name="workarounds"></a>Kerülő megoldások

#### <a name="mitigation-for-missing-tenant-id"></a>Hiányzó bérlői azonosító enyhítése

A javasolt Áthidaló megoldás a korábban leírt [szabályzat gyorsítótárazásának](#acquire-a-token-to-apply-a-policy) használata.

Másik lehetőségként használhatja a `tid` jogcímet, ha [egyéni házirendeket](../../active-directory-b2c/custom-policy-get-started.md) használ a Azure ad B2Cban. Az egyéni szabályzatok a [jogcím-átalakítás](../../active-directory-b2c/claims-transformation-technical-profile.md)használatával további jogcímeket adhatnak vissza az alkalmazáshoz.

#### <a name="mitigation-for-missing-from-the-token-response"></a>A "hiányzó jogkivonat-válasz" megoldásának enyhítése

Az egyik lehetőség a jogcím használata a `name` helyett `preferred_username` . Ha a `name` jogcímet Azure ad B2C által kiállított azonosító jogkivonatokban szeretné felvenni, válassza a **megjelenítendő név** lehetőséget a felhasználói folyamat konfigurálásakor.

A felhasználói folyamatok által visszaadott jogcímek meghatározásával kapcsolatos további információkért lásd [: oktatóanyag: felhasználói folyamatok létrehozása Azure ad B2Cban](../../active-directory-b2c/tutorial-create-user-flows.md).

## <a name="next-steps"></a>Következő lépések

A jogkivonatok interaktív módon való beszerzésével kapcsolatban további részleteket a következő példában talál: Azure AD B2C alkalmazások MSAL.NET.

| Sample | Platform | Description|
|------ | -------- | -----------|
|[Active-Directory-B2C-xamarin-Native](https://github.com/Azure-Samples/active-directory-b2c-xamarin-native) | Xamarin iOS, Xamarin Android, UWP | A MSAL.NET-t használó Xamarin-alkalmazások a felhasználókat a Azure AD B2C használatával hitelesítik, majd a visszaadott tokenekkel rendelkező webes API-kat férnek hozzá.|
