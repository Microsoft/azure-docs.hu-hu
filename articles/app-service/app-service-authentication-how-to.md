---
title: Az AuthN/AuthZ speciális használata
description: Megtudhatja, hogyan szabhatja testre a hitelesítési és engedélyezési App Service különböző forgatókönyvekhez, valamint felhasználói jogcímeket és különböző jogkivonatokat olvashat be.
ms.topic: article
ms.date: 03/29/2021
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 9335bb62e494fab50f7beadf3d7bbc423d80cf14
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775726"
---
# <a name="advanced-usage-of-authentication-and-authorization-in-azure-app-service"></a>A hitelesítés és engedélyezés speciális használata a Azure App Service

Ez a cikk bemutatja, hogyan szabhatja testre a beépített hitelesítést és engedélyezést a App Service [és](overview-authentication-authorization.md)az identitás az alkalmazásból való kezeléséhez. 

A gyors első lépésekhez tekintse meg az alábbi oktatóanyagok egyikét:

* [Oktatóanyag: Felhasználók hitelesítése és engedélyezése végpontok között az Azure App Service-ben](tutorial-auth-aad.md)
* [Az alkalmazás konfigurálása a Microsoft Identity Platform-bejelentkezés használatára](configure-authentication-provider-aad.md)
* [Az alkalmazás konfigurálása a Facebook-bejelentkezés használatára](configure-authentication-provider-facebook.md)
* [Az alkalmazás konfigurálása a Google-bejelentkezés használatára](configure-authentication-provider-google.md)
* [Az alkalmazás konfigurálása a Twitter-bejelentkezés használatára](configure-authentication-provider-twitter.md)
* [Az alkalmazás konfigurálása a bejelentkezéshez egy OpenID Connect szolgáltatóval (előzetes verzió)](configure-authentication-provider-openid-connect.md)
* [Az alkalmazás konfigurálása az Apple-bejelentkezéssel való bejelentkezésre (előzetes verzió)](configure-authentication-provider-apple.md)

## <a name="use-multiple-sign-in-providers"></a>Több bejelentkezési szolgáltató használata

A portálkonfiguráció nem kínál kulcsravezető lehetőséget több bejelentkezési szolgáltató (például a Facebook és a Twitter) felhasználóinak való bemutatni. A funkciókat azonban nem nehéz hozzáadni az alkalmazáshoz. A lépések a következők:

Először konfigurálja az összes engedélyezni **kívánt** identitásszolgáltatót Azure Portal hitelesítés/engedélyezés lapon.

A **Művelet, ha a kérelem** nincs hitelesítve beállításnál válassza a Névtelen kérések engedélyezése **(nincs művelet) lehetőséget.**

A bejelentkezési oldalon, a navigációs sávon vagy az alkalmazás bármely más helyén adjon hozzá egy bejelentkezési hivatkozást az összes engedélyezett szolgáltatóhoz ( `/.auth/login/<provider>` ). Például:

```html
<a href="/.auth/login/aad">Log in with the Microsoft Identity Platform</a>
<a href="/.auth/login/facebook">Log in with Facebook</a>
<a href="/.auth/login/google">Log in with Google</a>
<a href="/.auth/login/twitter">Log in with Twitter</a>
<a href="/.auth/login/apple">Log in with Apple</a>
```

Amikor a felhasználó az egyik hivatkozásra kattint, megnyílik a megfelelő bejelentkezési oldal, amely bejelentkezik.

Ha a bejelentkezés után átirányítja a felhasználót egy egyéni URL-címre, használja a lekérdezési sztring paramétert (nem tévesztendő össze az identitásszolgáltató konfigurációjában található `post_login_redirect_url` átirányítási URI-val). Ha például a bejelentkezés után a felhasználóhoz szeretne `/Home/Index` navigálni, használja a következő HTML-kódot:

```html
<a href="/.auth/login/<provider>?post_login_redirect_url=/Home/Index">Log in</a>
```

## <a name="validate-tokens-from-providers"></a>Szolgáltatóktól származó jogkivonatok ellenőrzése

Az ügyfél által irányított bejelentkezésben az alkalmazás manuálisan bejelentkezik a felhasználóval a szolgáltatóhoz, majd elküldi a hitelesítési jogkivonatot az App Service-nek ellenőrzés céljából (lásd a hitelesítési [folyamatot).](overview-authentication-authorization.md#authentication-flow) Ez az ellenőrzés maga nem ad hozzáférést a kívánt alkalmazás-erőforrásokhoz, de a sikeres ellenőrzés egy munkamenet-jogkivonatot ad, amely az alkalmazás erőforrásainak eléréséhez használható. 

A szolgáltatói jogkivonat érvényesítéséhez App Service alkalmazást először konfigurálni kell a kívánt szolgáltatóval. Futásidőben, miután lekéri a hitelesítési jogkivonatot a szolgáltatótól, tegye közzé a jogkivonatot a `/.auth/login/<provider>` következőn érvényesítés céljából: . Például: 

```
POST https://<appname>.azurewebsites.net/.auth/login/aad HTTP/1.1
Content-Type: application/json

{"id_token":"<token>","access_token":"<token>"}
```

A jogkivonat formátuma a szolgáltatótól függően némileg eltérő. A részletekért tekintse meg az alábbi táblázatot:

| Szolgáltató értéke | Kötelező a kérelem törzsében | Megjegyzések |
|-|-|-|
| `aad` | `{"access_token":"<access_token>"}` | |
| `microsoftaccount` | `{"access_token":"<token>"}` | A `expires_in` tulajdonság megadása nem kötelező. <br/>Amikor a jogkivonatot a Live Servicesből kéri le, mindig kérje le a `wl.basic` hatókört. |
| `google` | `{"id_token":"<id_token>"}` | A `authorization_code` tulajdonság megadása nem kötelező. Ha meg van adva, igény szerint a tulajdonság is `redirect_uri` csatolható hozzá. |
| `facebook`| `{"access_token":"<user_access_token>"}` | Érvényes [facebookos felhasználói hozzáférési jogkivonatot](https://developers.facebook.com/docs/facebook-login/access-tokens) használjon. |
| `twitter` | `{"access_token":"<access_token>", "access_token_secret":"<acces_token_secret>"}` | |
| | | |

Ha a szolgáltatói jogkivonat érvényesítése sikeresen megtörtént, az API egy et ad vissza a válasz `authenticationToken` törzsében, amely a munkamenet-jogkivonat. 

```json
{
    "authenticationToken": "...",
    "user": {
        "userId": "sid:..."
    }
}
```

Ha már rendelkezik ezzel a munkamenet-jogkivonattal, úgy férhet hozzá a védett alkalmazás erőforrásaihoz, hogy hozzáadja a fejlécet a `X-ZUMO-AUTH` HTTP-kérésekhez. Például: 

```
GET https://<appname>.azurewebsites.net/api/products/1
X-ZUMO-AUTH: <authenticationToken_value>
```

## <a name="sign-out-of-a-session"></a>Kijelentkezni egy munkamenetből

A felhasználók úgy kezdeményezhetik a kijelentkezését, hogy kérést küldenek `GET` az alkalmazás `/.auth/logout` végpontjára. A `GET` kérés a következőket teszi:

- Törli a hitelesítési cookie-kat az aktuális munkamenetből.
- Törli az aktuális felhasználó jogkivonatát a jogkivonat-tárolóból.
- A Azure Active Directory És a Google számára a kiszolgálóoldali kijelentkeztet az identitásszolgáltatón.

A következő egy egyszerű kijelentkezési hivatkozás egy webhelyen:

```html
<a href="/.auth/logout">Sign out</a>
```

Alapértelmezés szerint a sikeres kijelentkezás átirányítja az ügyfelet a `/.auth/logout/done` URL-címre. A kijelentkezés utáni átirányítási oldalt a lekérdezési paraméter hozzáadásával `post_logout_redirect_uri` módosíthatja. Például:

```
GET /.auth/logout?post_logout_redirect_uri=/index.html
```

Javasoljuk, hogy kódolja [a](https://wikipedia.org/wiki/Percent-encoding) `post_logout_redirect_uri` értékét.

Teljes URL-címek használata esetén az URL-címet vagy ugyanabban a tartományban kell üzemeltetni, vagy az alkalmazás engedélyezett külső átirányítási URL-címeként kell konfigurálni. A következő példában az átirányítás nem ugyanabban a tartományban `https://myexternalurl.com` található:

```
GET /.auth/logout?post_logout_redirect_uri=https%3A%2F%2Fmyexternalurl.com
```

Futtassa a következő parancsot a [Azure Cloud Shell:](../cloud-shell/quickstart.md)

```azurecli-interactive
az webapp auth update --name <app_name> --resource-group <group_name> --allowed-external-redirect-urls "https://myexternalurl.com"
```

## <a name="preserve-url-fragments"></a>URL-töredékek megőrzése

Miután a felhasználók bejelentkeztek az alkalmazásba, általában ugyanazon oldal ugyanazon szakaszára szeretnék átirányítani őket, például a következőre: `/wiki/Main_Page#SectionZ` . Mivel azonban [](https://wikipedia.org/wiki/Fragment_identifier) az URL-töredékek (például a ) soha nem küldve a kiszolgálónak, alapértelmezés szerint nem maradnak meg, miután az OAuth-bejelentkezés befejeződött, és visszairányított az `#SectionZ` alkalmazásba. A felhasználók így az optimálisnál rosszabb élményt tapasztalnak, amikor ismét a kívánt horgonyra kell navigálniuk. Ez a korlátozás minden kiszolgálóoldali hitelesítési megoldásra vonatkozik.

A App Service megőrizheti az OAuth-bejelentkezés url-töredékeit. Ehhez állítsa be a nevű alkalmazásbeállítást a `WEBSITE_AUTH_PRESERVE_URL_FRAGMENT` `true` beállításra. Ezt a következő paranccsal [Azure Portal,](https://portal.azure.com)vagy egyszerűen futtassa a következő parancsot a [Azure Cloud Shell:](../cloud-shell/quickstart.md)

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group <group_name> --settings WEBSITE_AUTH_PRESERVE_URL_FRAGMENT="true"
```

## <a name="access-user-claims"></a>Felhasználói jogcímek elérése

App Service speciális fejlécekkel továbbítja a felhasználói jogcímeket az alkalmazásnak. A külső kérések nem állíthatják be ezeket a fejléceket, ezért csak akkor vannak jelen, ha az App Service. Néhány példa fejlécre:

* X-MS-CLIENT-PRINCIPAL-NAME
* X-MS-CLIENT-PRINCIPAL-ID

A bármely nyelven vagy keretrendszerben megírt kód le tudja szerezni a szükséges információkat ezekről a fejlécekről. A ASP.NET 4.6-os alkalmazások esetében a **ClaimsPrincipal** automatikusan be lesz állítva a megfelelő értékekkel. ASP.NET Core azonban nem biztosít olyan hitelesítési middleware-t, amely integrálható App Service felhasználói jogcímekkel. Megkerülő megoldásért [lásd: MaximeRouiller.Azure.AppService.EasyAuth.](https://github.com/MaximRouiller/MaximeRouiller.Azure.AppService.EasyAuth)

Ha a [jogkivonat-tároló](overview-authentication-authorization.md#token-store) engedélyezve van az alkalmazás számára, a hívással további részleteket is beszerezhet a hitelesített `/.auth/me` felhasználóról. A Mobile Apps kiszolgálói SDK-k segítő metódusokat biztosítanak az adatokkal való munkához. További információ: [Az Azure Mobile Apps Node.js SDK](/previous-versions/azure/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk#howto-tables-getidentity)használata és Az Azure-hoz készült [.NET háttérkiszolgálói SDK](/previous-versions/azure/app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk#user-info)használata az Azure Mobile Apps.

## <a name="retrieve-tokens-in-app-code"></a>Jogkivonatok lekérése az alkalmazáskódban

A kiszolgálói kódból a szolgáltatóspecifikus jogkivonatokat a rendszer a kérés fejlécére injektálja, így könnyen elérheti őket. Az alábbi táblázatban a lehetséges tokenfejlécek nevei láthatóak:

| Szolgáltató | Fejlécnevek |
|-|-|
| Azure Active Directory | `X-MS-TOKEN-AAD-ID-TOKEN` <br/> `X-MS-TOKEN-AAD-ACCESS-TOKEN` <br/> `X-MS-TOKEN-AAD-EXPIRES-ON`  <br/> `X-MS-TOKEN-AAD-REFRESH-TOKEN` |
| Facebook-jogkivonat | `X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN` <br/> `X-MS-TOKEN-FACEBOOK-EXPIRES-ON` |
| Google | `X-MS-TOKEN-GOOGLE-ID-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-ACCESS-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-EXPIRES-ON` <br/> `X-MS-TOKEN-GOOGLE-REFRESH-TOKEN` |
| Twitter | `X-MS-TOKEN-TWITTER-ACCESS-TOKEN` <br/> `X-MS-TOKEN-TWITTER-ACCESS-TOKEN-SECRET` |
|||

Az ügyfélkódból (például mobilalkalmazásból vagy böngészőbeli JavaScriptből) küldjön HTTP-kérést a következőnek: ( a jogkivonat-tárolót `GET` `/.auth/me` engedélyezni kell).[](overview-authentication-authorization.md#token-store) A visszaadott JSON-nak vannak szolgáltatóspecifikus jogkivonatai.

> [!NOTE]
> A hozzáférési jogkivonatok a szolgáltatói erőforrásokhoz való hozzáférésre valók, így csak akkor állnak rendelkezésre, ha a szolgáltatót titkos ügyfélk titkos ügyfélk titkos okkal konfigurálja. A frissítési jogkivonatok lekért információkért lásd: Hozzáférési jogkivonatok frissítése.

## <a name="refresh-identity-provider-tokens"></a>Identitásszolgáltatói jogkivonatok frissítése

Amikor a szolgáltató hozzáférési jogkivonata (és nem a munkamenet-jogkivonat) [](#extend-session-token-expiration-grace-period)lejár, újra kell hitelesenie a felhasználót, mielőtt ismét használnia kellene a jogkivonatot. A jogkivonat lejáratának elkerülése érdekében hívja meg az alkalmazás `GET` `/.auth/refresh` végpontját. A rendszer App Service automatikusan frissíti a hitelesített felhasználó [](overview-authentication-authorization.md#token-store) jogkivonat-tárolójában tárolt hozzáférési jogkivonatokat. Az alkalmazás kódja által kért jogkivonatok a frissített jogkivonatokat kapják meg. Ahhoz azonban, hogy a jogkivonat-frissítés működjön, a jogkivonat-tárolónak tartalmaznia kell [a szolgáltató](https://auth0.com/learn/refresh-tokens/) frissítési jogkivonatát. A frissítési jogkivonatok lekért módját minden szolgáltató dokumentálja, de az alábbi lista röviden összefoglalja:

- **Google:** Lekérdezési sztring `access_type=offline` paraméter hozzáfűzése az `/.auth/login/google` API-híváshoz. Ha a Mobile Apps SDK-t használja, hozzáadhatja a paramétert az egyik `LogicAsync` túlterheléshez (lásd: Google Refresh [Tokens](https://developers.google.com/identity/protocols/OpenIDConnect#refresh-tokens)).
- **Facebook:** Nem biztosít frissítési jogkivonatokat. A hosszú ideig tartó jogkivonatok 60 nap múlva lejárnak (lásd a Facebook lejáratát és [a hozzáférési jogkivonatok kiterjesztését).](https://developers.facebook.com/docs/facebook-login/access-tokens/expiration-and-extension)
- **Twitter:** A hozzáférési jogkivonatok nem járnak le [(lásd: Twitter OAuth GYIK).](https://developer.twitter.com/en/docs/authentication/faq)
- **Azure Active Directory**: [https://resources.azure.com](https://resources.azure.com) A-ban tegye a következőket:
    1. Az oldal tetején válassza az **Olvasás/Írás lehetőséget.**
    2. A bal oldali böngészőben lépjen az **előfizetések** > **_\<subscription\_name_** > **resourceGroups** > ** **_> \<resource\_group\_name> _**szolgáltatók**  >  **Microsoft.Web**  >  **Sites** > **_ \<app\_name> _** > **config**  >  **authsettings**. 
    3. Kattintson a **Szerkesztés** gombra.
    4. Módosítsa a következő tulajdonságot. A helyére Azure Active Directory elérni kívánt szolgáltatás _\<app\_id>_ alkalmazásazonosítóját.

        ```json
        "additionalLoginParams": ["response_type=code id_token", "resource=<app_id>"]
        ```

    5. Kattintson a **Put (Put)** gombra. 

A szolgáltató konfigurálása után a [](#retrieve-tokens-in-app-code) frissítési jogkivonat és a hozzáférési jogkivonat lejárati ideje a jogkivonat-tárolóban található. 

Ha bármikor frissítenie kell a hozzáférési jogkivonatot, egyszerűen hívja meg a következőt `/.auth/refresh` bármely nyelven: . Az alábbi kódrészlet a jQuery használatával frissíti a hozzáférési jogkivonatokat egy JavaScript-ügyfélből.

```javascript
function refreshTokens() {
  let refreshUrl = "/.auth/refresh";
  $.ajax(refreshUrl) .done(function() {
    console.log("Token refresh completed successfully.");
  }) .fail(function() {
    console.log("Token refresh failed. See application logs for details.");
  });
}
```

Ha egy felhasználó visszavonja az alkalmazáshoz megadott engedélyeket, a hívása `/.auth/me` meghiúsulhat egy `403 Forbidden` válaszból. A hibák diagnosztizálásához ellenőrizze az alkalmazásnaplókat a részletekért.

## <a name="extend-session-token-expiration-grace-period"></a>Munkamenet-jogkivonat lejárati türelmi időszakának meghosszabbítása

A hitelesített munkamenet 8 óra után lejár. A hitelesített munkamenet lejárata után alapértelmezés szerint 72 órás türelmi időszak érvényes. Ebben a türelmi időszakban a felhasználó újbóli hitelesítése nélkül frissítheti App Service munkamenet-jogkivonatot. Ha a munkamenet-jogkivonat érvénytelenné válik, egyszerűen hívhatja a hívását, és nem kell saját maga követnie a `/.auth/refresh` jogkivonat lejáratát. Ha a 72 órás türelmi időszak már elajott, a felhasználónak újra be kell jelentkeznie egy érvényes munkamenet-jogkivonat lezétlése után.

Ha a 72 óra nem elég az Ön számára, meghosszabbíthatja ezt a lejárati időt. A lejárat hosszú időtartamra való kiterjesztése jelentős biztonsági következményekkel járhat (például ha egy hitelesítési jogkivonat kiszivárog vagy ellopják). Ezért hagyja meg az alapértelmezett 72 órát, vagy állítsa a meghosszabbítási időszakot a legkisebb értékre.

Az alapértelmezett lejárati időkeret kiterjesztéséhez futtassa a következő parancsot a [Cloud Shell.](../cloud-shell/overview.md)

```azurecli-interactive
az webapp auth update --resource-group <group_name> --name <app_name> --token-refresh-extension-hours <hours>
```

> [!NOTE]
> A türelmi időszak csak a App Service munkamenetre vonatkozik, az identitásszolgáltatók jogkivonataira nem. A lejárt szolgáltatói jogkivonatok nem lejártak türelmi időszakra. 
>

## <a name="limit-the-domain-of-sign-in-accounts"></a>A bejelentkezési fiókok tartományának korlátozása

A Microsoft-fiók és Azure Active Directory is lehetővé teszi, hogy több tartományból jelentkezzen be. A Microsoft-fiók például _outlook.com_, _live.com_ és _hotmail.com_ fiókokat. Az Azure AD bármilyen számú egyéni tartományt lehetővé tesz a bejelentkezési fiókok számára. Azonban előfordulhat, hogy fel szeretné gyorsítani a felhasználókat közvetlenül a saját márkájú Azure AD bejelentkezési oldalára (például `contoso.com` ). A bejelentkezési fiókok tartománynevének javaslathoz kövesse az alábbi lépéseket.

A fájlban lépjen a [https://resources.azure.com](https://resources.azure.com) Microsoft.Web  sites > **_\<subscription\_name_** > **resourceGroups** > ** **_>-szolgáltatókhoz a \<resource\_group\_name> _ >  **Microsoft.Web**  >  **Sites** > **_ _** > \<app\_name> **beállításokat.**  >   

Kattintson **a Szerkesztés gombra,** módosítsa a következő tulajdonságot, majd kattintson a **Put (Put) gombra.** Ne cserélje le _\<domain\_name>_ a helyére a kívánt tartományt.

```json
"additionalLoginParams": ["domain_hint=<domain_name>"]
```

Ez a beállítás hozzáfűzi `domain_hint` a lekérdezési sztring paramétert a bejelentkezési átirányítási URL-címhez. 

> [!IMPORTANT]
> Az ügyfél eltávolíthatja a paramétert az átirányítási URL-cím fogadása után, majd bejelentkezhet `domain_hint` egy másik tartománnyal. Tehát bár ez a függvény kényelmes, nem biztonsági funkció.
>

## <a name="authorize-or-deny-users"></a>Felhasználók jogosultsága vagy elutasítása

Bár App Service a legegyszerűbb hitelesítési esetet (azaz a nem hitelesített kérelmek elutasítását) is el kell látnia, az alkalmazás ennél finomabb hitelesítési viselkedést is megkövetelhet, például korlátozhatja a hozzáférést egy adott felhasználói csoportra. Bizonyos esetekben egyéni alkalmazáskódot kell írnia a bejelentkezett felhasználó hozzáférésének engedélyezése vagy megtagadása érdekében. Más esetekben a App Service vagy az identitásszolgáltató kódváltozások nélkül segíthet.

- [Kiszolgálói szint](#server-level-windows-apps-only)
- [Identitásszolgáltatói szint](#identity-provider-level)
- [Alkalmazásszint](#application-level)

### <a name="server-level-windows-apps-only"></a>Kiszolgálói szint (csak Windows-alkalmazások esetén)

Bármely Windows-alkalmazás esetén megadhatja az IIS-webkiszolgáló engedélyezési  viselkedését az IIS-Web.configszerkesztésével. A Linux-alkalmazások nem használják az IIS-t, és nem konfigurálhatóak az *Web.config.*

1. Navigáljon ide: `https://<app-name>.scm.azurewebsites.net/DebugConsole`

1. Az alkalmazásfájlok böngészőböngészőjében App Service *site/wwwroot webhelyre.* Ha egy *Web.config* nem létezik, a New File (Új fájl) lehetőség kiválasztásával **+**  >  **hozza létre.** 

1. A szerkesztéshez *Web.config* ceruza gombra. Adja hozzá a következő konfigurációs kódot, és kattintson a **Mentés gombra.** Ha *Web.config* már létezik, egyszerűen adja hozzá a `<authorization>` mindent tartalmazott elemet. Adja hozzá az elemben engedélyezni kívánt `<allow>` fiókokat.

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <configuration>
       <system.web>
          <authorization>
            <allow users="user1@contoso.com,user2@contoso.com"/>
            <deny users="*"/>
          </authorization>
       </system.web>
    </configuration>
    ```

### <a name="identity-provider-level"></a>Identitásszolgáltatói szint

Az identitásszolgáltató nyújthat bizonyos kulcsraszavas hitelesítést. Például:

- A [Azure App Service](configure-authentication-provider-aad.md)a vállalati szintű hozzáférést közvetlenül az Azure [AD-ban](../active-directory/manage-apps/what-is-access-management.md) kezelheti. Útmutatásért [lásd: How to remove a user's access to an application](../active-directory/manage-apps/methods-for-removing-user-access.md)(Felhasználó hozzáférésének eltávolítása egy alkalmazáshoz).
- A [Google](configure-authentication-provider-google.md)esetében a szervezethez [](https://cloud.google.com/resource-manager/docs/cloud-platform-resource-hierarchy#organizations) tartozó Google API-projektek konfigurálhatóak úgy, hogy csak a szervezet felhasználóinak engedélyezzenek hozzáférést (lásd a [Google **OAuth 2.0**](https://support.google.com/cloud/answer/6158849?hl=en)beállításával kapcsolatos támogatási oldalát).

### <a name="application-level"></a>Alkalmazásszint

Ha a többi szint valamelyike nem biztosítja a szükséges engedélyt, vagy ha a platformja vagy az identitásszolgáltatója nem támogatott, egyéni kódot kell írnia, amely a felhasználói jogcímek alapján engedélyezi a [felhasználókat.](#access-user-claims)

## <a name="updating-the-configuration-version"></a>A konfigurációs verzió frissítése

A hitelesítési/engedélyezési szolgáltatásnak két verziója van a felügyeleti API-nak. A V2 verzió szükséges a hitelesítéshez a Azure Portal. A V1 API-t már használó alkalmazások néhány módosítás után frissíthet a V2 verzióra. Pontosabban a titkos konfigurációt át kell áthelyezni a ragadós alkalmazásbeállításokba. Ezt automatikusan is meg lehet tenni az alkalmazás portáljának "Hitelesítés" szakaszában.

> [!WARNING]
> A V2-re való migrálás letiltja az App Service Authentication /Authorization szolgáltatás kezelését az alkalmazáshoz bizonyos ügyfeleken keresztül, például az Azure Portal, az Azure CLI és a Azure PowerShell. Ez nem fordítható meg.

A V2 API nem támogatja a Microsoft-fiók külön szolgáltatóként való létrehozását vagy szerkesztését, ahogyan azt a V1-ben is tette. Használhatja inkább az átkonvergens [Microsoft Identity Platformot](../active-directory/develop/v2-overview.md) a felhasználók Azure AD- és személyes Microsoft-fiókkal való bejelentkezésére. A V2 API-re való váltáskor a V1 Azure Active Directory konfigurálja a Microsoft Identity Platform szolgáltatóját. A V1 Microsoft-fiókszolgáltató a migrálási folyamat során is a megszokott módon működik tovább, de javasoljuk, hogy lépjen az újabb Microsoft Identity Platform-modellre. További [információ: Microsoft-fiókszolgáltatói regisztrációk](#support-for-microsoft-account-provider-registrations) támogatása.

Az automatizált migrálási folyamat alkalmazásbeállításokba áthelyezi a szolgáltatói titkos adatokat, majd a konfiguráció többi részét új formátumba konvertálja. Az automatikus migrálás használata:

1. Lépjen az alkalmazásra a portálon, és válassza a **Hitelesítés** menüelemet.
1. Ha az alkalmazás v1 modellel van konfigurálva, egy Frissítés gomb **látható.**
1. Tekintse át a leírást a megerősítési kérésben. Ha készen áll az áttelepítésre, kattintson a frissítés **elemre** a parancssorban.

### <a name="manually-managing-the-migration"></a>Az áttelepítés manuális kezelése

Az alábbi lépések lehetővé teszik az alkalmazás manuális áttelepítését a V2 API-ba, ha nem szeretné a fent említett automatikus verziót használni.

#### <a name="moving-secrets-to-application-settings"></a>Titkos kulcsok átköltöztetve az alkalmazásbeállításokba

1. Szerezze be a meglévő konfigurációt a V1 API használatával:

   ```azurecli
   # For Web Apps
   az webapp auth show -g <group_name> -n <site_name>

   # For Azure Functions
   az functionapp auth show -g <group_name> -n <site_name>
   ```

   Az eredményül kapott hasznos JSON-adatban jegyezze fel az egyes konfigurált szolgáltatókhoz használt titkos értéket:

   * AAD: `clientSecret`
   * Google: `googleClientSecret`
   * Facebook: `facebookAppSecret`
   * Twitter: `twitterConsumerSecret`
   * Microsoft-fiók: `microsoftAccountClientSecret`

   > [!IMPORTANT]
   > A titkos értékek fontos biztonsági hitelesítő adatok, és körültekintően kell kezelni őket. Ne ossza meg ezeket az értékeket, és ne őrzd meg őket egy helyi gépen.

1. Minden titkos értékhez hozzon létre egy-egy ragadós alkalmazásbeállítást. Kiválaszthatja az egyes alkalmazásbeállítások nevét. Ennek az értéknek meg kell egyeznie [](./app-service-key-vault-references.md?toc=/azure/azure-functions/toc.json) az előző lépésben megszerzett értékkel, vagy hivatkozni kell egy Key Vault titkos adatokat, amelyet ezzel az értékkel hozott létre.

   A beállítás létrehozásához használhatja a Azure Portal vagy futtathatja az alábbiak egy-egy változatát minden szolgáltatónál:

   ```azurecli
   # For Web Apps, Google example    
   az webapp config appsettings set -g <group_name> -n <site_name> --slot-settings GOOGLE_PROVIDER_AUTHENTICATION_SECRET=<value_from_previous_step>

   # For Azure Functions, Twitter example
   az functionapp config appsettings set -g <group_name> -n <site_name> --slot-settings TWITTER_PROVIDER_AUTHENTICATION_SECRET=<value_from_previous_step>
   ```

   > [!NOTE]
   > A konfiguráció alkalmazásbeállítását pontra ragadósként kell megjelölni, ami azt jelenti, hogy a pontcserélési művelet során nem fognak áthelyezni a [környezetek között.](./deploy-staging-slots.md) Ennek az az oka, hogy maga a hitelesítési konfiguráció a környezethez van kötve. 

1. Hozzon létre egy nevű új JSON-fájlt. `authsettings.json` Vegyük a korábban kapott kimenetet, és távolítsa el az összes titkos értéket. Írja a fennmaradó kimenetet a fájlba, és győződjön meg arról, hogy a fájlban nincs titkos adat. Bizonyos esetekben előfordulhat, hogy a konfiguráció üres sztringeket tartalmazó tömbökből áll. Ügyeljen rá, hogy ez ne legyen így, és ha `microsoftAccountOAuthScopes` igen, váltsa át ezt az értéket `null` értékre.

1. Adjon hozzá egy tulajdonságot, amely az egyes szolgáltatókhoz korábban létrehozott alkalmazásbeállítás `authsettings.json` nevére mutat:
 
   * AAD: `clientSecretSettingName`
   * Google: `googleClientSecretSettingName`
   * Facebook: `facebookAppSecretSettingName`
   * Twitter: `twitterConsumerSecretSettingName`
   * Microsoft-fiók: `microsoftAccountClientSecretSettingName`

   A művelet utáni példafájl a következőhöz hasonlóan néz ki, ebben az esetben csak az AAD-hez van konfigurálva:

   ```json
   {
       "id": "/subscriptions/00d563f8-5b89-4c6a-bcec-c1b9f6d607e0/resourceGroups/myresourcegroup/providers/Microsoft.Web/sites/mywebapp/config/authsettings",
       "name": "authsettings",
       "type": "Microsoft.Web/sites/config",
       "location": "Central US",
       "properties": {
           "enabled": true,
           "runtimeVersion": "~1",
           "unauthenticatedClientAction": "AllowAnonymous",
           "tokenStoreEnabled": true,
           "allowedExternalRedirectUrls": null,
           "defaultProvider": "AzureActiveDirectory",
           "clientId": "3197c8ed-2470-480a-8fae-58c25558ac9b",
           "clientSecret": "",
           "clientSecretSettingName": "MICROSOFT_IDENTITY_AUTHENTICATION_SECRET",
           "clientSecretCertificateThumbprint": null,
           "issuer": "https://sts.windows.net/0b2ef922-672a-4707-9643-9a5726eec524/",
           "allowedAudiences": [
               "https://mywebapp.azurewebsites.net"
           ],
           "additionalLoginParams": null,
           "isAadAutoProvisioned": true,
           "aadClaimsAuthorization": null,
           "googleClientId": null,
           "googleClientSecret": null,
           "googleClientSecretSettingName": null,
           "googleOAuthScopes": null,
           "facebookAppId": null,
           "facebookAppSecret": null,
           "facebookAppSecretSettingName": null,
           "facebookOAuthScopes": null,
           "gitHubClientId": null,
           "gitHubClientSecret": null,
           "gitHubClientSecretSettingName": null,
           "gitHubOAuthScopes": null,
           "twitterConsumerKey": null,
           "twitterConsumerSecret": null,
           "twitterConsumerSecretSettingName": null,
           "microsoftAccountClientId": null,
           "microsoftAccountClientSecret": null,
           "microsoftAccountClientSecretSettingName": null,
           "microsoftAccountOAuthScopes": null,
           "isAuthFromFile": "false"
       }   
   }
   ```

1. Küldje el ezt a fájlt az alkalmazás új hitelesítési/engedélyezési konfigurációjaként:

   ```azurecli
   az rest --method PUT --url "/subscriptions/<subscription_id>/resourceGroups/<group_name>/providers/Microsoft.Web/sites/<site_name>/config/authsettings?api-version=2020-06-01" --body @./authsettings.json
   ```

1. A kézmozdulat után ellenőrizze, hogy az alkalmazás továbbra is a várt módon működik-e.

1. Törölje az előző lépésekben használt fájlt.

Az alkalmazást áttelepítve alkalmazásbeállításként tárolta az identitásszolgáltatói titkos adatokat.

#### <a name="support-for-microsoft-account-provider-registrations"></a>Microsoft-fiókszolgáltatói regisztrációk támogatása

Ha a meglévő konfiguráció microsoftos fiókszolgáltatót tartalmaz, és nem tartalmaz Azure Active Directory-szolgáltatót, átkapcsolhatja a konfigurációt az Azure Active Directory-szolgáltatóra, majd végrehajthatja a migrálást. Ehhez tegye a következőket:

1. A [**Alkalmazásregisztrációk**](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) a Azure Portal, és keresse meg a Microsoft-fiókszolgáltatójához társított regisztrációt. Ez a "Személyes fiókból származó alkalmazások" fejléc alatt lehet.
1. Lépjen a regisztráció "Hitelesítés" lapjára. Az "Átirányítási URI-k" alatt egy végződésnek kell `/.auth/login/microsoftaccount/callback` lennie. Másolja ki ezt az URI-t.
1. Adjon hozzá egy új URI-t, amely megegyezik az előbb másolt URI-val, azzal a kivételt leszámítva, hogy a `/.auth/login/aad/callback` végződik. Ez lehetővé teszi a regisztráció használatát a App Service/engedélyezés konfigurációjában.
1. Lépjen az App Service hitelesítés/engedélyezés konfigurációját.
1. Gyűjtse össze a Microsoft-fiókszolgáltató konfigurációját.
1. Konfigurálja Azure Active Directory szolgáltatót a "Speciális" felügyeleti móddal, és adja meg az előző lépésben gyűjtött ügyfél-azonosítót és titkos ügyfél titkos ügyfél-értékeket. A Kiállító URL-címe mezőben használja a címet, és cserélje le a helyére a felhőalapú környezet hitelesítési végpontját (például : " globális Azure esetén), a helyére pedig a saját `<authentication-endpoint>/<tenant-id>/v2.0` *\<authentication-endpoint>* [](../active-directory/develop/authentication-national-cloud.md#azure-ad-authentication-endpoints) https://login.microsoftonline.com *\<tenant-id>* **címtár-azonosítóját (bérlőazonosítóját) írja.**
1. A konfiguráció mentése után tesztelje a bejelentkezési folyamatot úgy, hogy megnyitja a böngészőben a webhely végpontját, és befejezi a `/.auth/login/aad` bejelentkezési folyamatot.
1. Ezen a ponton sikeresen átmásolta a konfigurációt, de a meglévő Microsoft-fiókszolgáltató konfigurációja megmarad. Mielőtt eltávolítja, győződjön meg arról, hogy az alkalmazás minden része hivatkozik a Azure Active Directory szolgáltatóra bejelentkezési hivatkozásokon keresztül stb. Ellenőrizze, hogy az alkalmazás minden része a várt módon működik-e.
1. Miután ellenőrzi, hogy a dolgok működnek-e az AAD Azure Active Directory-szolgáltatóval, eltávolíthatja a Microsoft-fiókszolgáltató konfigurációját.

> [!WARNING]
> A két regisztráció az AAD-alkalmazásregisztráció [](../active-directory/develop/supported-accounts-validation.md) támogatott fióktípusának módosításával konvergálhat. Ez azonban új jóváhagyás kérését kényszeríti a Microsoft-fiók felhasználói számára, és a felhasználók identitási jogcíme struktúrája eltérő lehet, különösen az értékek megváltozása miatt, mivel új alkalmazásazonosítót `sub` használ. Ez a megközelítés csak akkor ajánlott, ha alaposan megértettük. Ehelyett várja meg a V2 API felületének két regisztrációjának támogatását.

#### <a name="switching-to-v2"></a>Váltás a V2-re

A fenti lépések után keresse meg az alkalmazást a Azure Portal. Válassza a "Hitelesítés (előzetes verzió)" szakaszt. 

Alternatív megoldásként put kérést is kérhet a helyerőforrás `config/authsettingsv2` alatt található erőforráshoz. A hasznos adatok sémája megegyezik a Konfigurálás fájl használatával [szakaszban rögzített sémával.](#config-file)

## <a name="configure-using-a-file-preview"></a><a name="config-file"> </a>Konfigurálás fájl használatával (előzetes verzió)

A hitelesítési beállítások opcionálisan konfigurálhatóak az üzemelő példány által biztosított fájllal. Erre a hitelesítés/engedélyezés bizonyos előzetes verziójú képességei App Service szükség lehet.

> [!IMPORTANT]
> Ne feledje, hogy az alkalmazás hasznos adata és ezáltal ez a fájl a környezetek között is mozoghat, mint a [tárolóhelyek esetében.](./deploy-staging-slots.md) Valószínű, hogy minden egyes tárolóhelyhez más alkalmazásregisztrációt szeretne kitűzni, és ezekben az esetekben a konfigurációs fájl használata helyett továbbra is a szabványos konfigurációs módszert kell használnia.

### <a name="enabling-file-based-configuration"></a>Fájlalapú konfiguráció engedélyezése

> [!CAUTION]
> Az előzetes verzióban a fájlalapú konfiguráció engedélyezése letiltja az App Service Authentication /Authorization szolgáltatás kezelését az alkalmazás számára bizonyos ügyfeleken keresztül, például a Azure Portal, az Azure CLI és a Azure PowerShell.

1. Hozzon létre egy új JSON-fájlt a konfigurációhoz a projekt gyökerében (a webes / függvényalkalmazásban a D:\home\site\wwwroot helyen üzembe helyezni). Töltse ki a kívánt konfigurációt a [fájlalapú konfigurációs referencia alapján.](#configuration-file-reference) Meglévő konfiguráció módosítása Azure Resource Manager, ügyeljen arra, hogy a gyűjteményben rögzített tulajdonságokat lefordítsa a `authsettings` konfigurációs fájlra.

2. Módosítsa a meglévő konfigurációt, [](../azure-resource-manager/management/overview.md) amely a(Azure Resource Manager API-kban van `Microsoft.Web/sites/<siteName>/config/authsettings` rögzíti. Ennek módosításához használhat egy Azure Resource Manager [sablont](../azure-resource-manager/templates/overview.md) vagy egy olyan eszközt, mint a [Azure Erőforrás-kezelő.](https://resources.azure.com/) Az authsettings gyűjteményben három tulajdonságot kell beállítania (és más tulajdonságokat is eltávolíthat):

    1.  Állítsa `enabled` "true" (igaz) értékre
    2.  Állítsa `isAuthFromFile` "true" (igaz) értékre
    3.  Állítsa `authFilePath` be a fájl nevét (például "auth.jsbe")

> [!NOTE]
> A formátuma `authFilePath` platformonként eltérő. Windows rendszeren a relatív és az abszolút elérési utak is támogatottak. A relatív érték ajánlott. Linux esetén jelenleg csak az abszolút elérési utak támogatottak, ezért a beállítás értéke "/home/site/wwwroot/auth.json" vagy hasonló.

A konfigurációs frissítés után a rendszer a fájl tartalma alapján határozza meg a App Service hitelesítés/engedélyezés működését. Ha bármikor vissza szeretne térni Azure Resource Manager konfigurációhoz, ezt a "false" (hamis) `isAuthFromFile` beállítással használhatja.

### <a name="configuration-file-reference"></a>Konfigurációs fájl referenciája

A konfigurációs fájlból hivatkozni kívánt titkos adatokat alkalmazásbeállításokként [kell tárolni.](./configure-common.md#configure-app-settings) A beállításoknak a kívánt nevet hatja. Csak győződjön meg arról, hogy a konfigurációs fájl hivatkozásai ugyanazt a kulcsot használják.

Az alábbi kimeríti a fájlon belüli lehetséges konfigurációs beállításokat:

```json
{
    "platform": {
        "enabled": <true|false>
    },
    "globalValidation": {
        "unauthenticatedClientAction": "RedirectToLoginPage|AllowAnonymous|Return401|Return403",
        "redirectToProvider": "<default provider alias>",
        "excludedPaths": [
            "/path1",
            "/path2"
        ]
    },
    "httpSettings": {
        "requireHttps": <true|false>,
        "routes": {
            "apiPrefix": "<api prefix>"
        },
        "forwardProxy": {
            "convention": "NoProxy|Standard|Custom",
            "customHostHeaderName": "<host header value>",
            "customProtoHeaderName": "<proto header value>"
        }
    },
    "login": {
        "routes": {
            "logoutEndpoint": "<logout endpoint>"
        },
        "tokenStore": {
            "enabled": <true|false>,
            "tokenRefreshExtensionHours": "<double>",
            "fileSystem": {
                "directory": "<directory to store the tokens in if using a file system token store (default)>"
            },
            "azureBlobStorage": {
                "sasUrlSettingName": "<app setting name containing the sas url for the Azure Blob Storage if opting to use that for a token store>"
            }
        },
        "preserveUrlFragmentsForLogins": <true|false>,
        "allowedExternalRedirectUrls": [
            "https://uri1.azurewebsites.net/",
            "https://uri2.azurewebsites.net/",
            "url_scheme_of_your_app://easyauth.callback"
        ],
        "cookieExpiration": {
            "convention": "FixedTime|IdentityDerived",
            "timeToExpiration": "<timespan>"
        },
        "nonce": {
            "validateNonce": <true|false>,
            "nonceExpirationInterval": "<timespan>"
        }
    },
    "identityProviders": {
        "azureActiveDirectory": {
            "enabled": <true|false>,
            "registration": {
                "openIdIssuer": "<issuer url>",
                "clientId": "<app id>",
                "clientSecretSettingName": "APP_SETTING_CONTAINING_AAD_SECRET",
            },
            "login": {
                "loginParameters": [
                    "paramName1=value1",
                    "paramName2=value2"
                ]
            },
            "validation": {
                "allowedAudiences": [
                    "audience1",
                    "audience2"
                ]
            }
        },
        "facebook": {
            "enabled": <true|false>,
            "registration": {
                "appId": "<app id>",
                "appSecretSettingName": "APP_SETTING_CONTAINING_FACEBOOK_SECRET"
            },
            "graphApiVersion": "v3.3",
            "login": {
                "scopes": [
                    "public_profile",
                    "email"
                ]
            },
        },
        "gitHub": {
            "enabled": <true|false>,
            "registration": {
                "clientId": "<client id>",
                "clientSecretSettingName": "APP_SETTING_CONTAINING_GITHUB_SECRET"
            },
            "login": {
                "scopes": [
                    "profile",
                    "email"
                ]
            }
        },
        "google": {
            "enabled": true,
            "registration": {
                "clientId": "<client id>",
                "clientSecretSettingName": "APP_SETTING_CONTAINING_GOOGLE_SECRET"
            },
            "login": {
                "scopes": [
                    "profile",
                    "email"
                ]
            },
            "validation": {
                "allowedAudiences": [
                    "audience1",
                    "audience2"
                ]
            }
        },
        "twitter": {
            "enabled": <true|false>,
            "registration": {
                "consumerKey": "<consumer key>",
                "consumerSecretSettingName": "APP_SETTING_CONTAINING TWITTER_CONSUMER_SECRET"
            }
        },
        "apple": {
            "enabled": <true|false>,
            "registration": {
                "clientId": "<client id>",
                "clientSecretSettingName": "APP_SETTING_CONTAINING_APPLE_SECRET"
            },
            "login": {
                "scopes": [
                    "profile",
                    "email"
                ]
            }
        },
        "openIdConnectProviders": {
            "<providerName>": {
                "enabled": <true|false>,
                "registration": {
                    "clientId": "<client id>",
                    "clientCredential": {
                        "clientSecretSettingName": "<name of app setting containing client secret>"
                    },
                    "openIdConnectConfiguration": {
                        "authorizationEndpoint": "<url specifying authorization endpoint>",
                        "tokenEndpoint": "<url specifying token endpoint>",
                        "issuer": "<url specifying issuer>",
                        "certificationUri": "<url specifying jwks endpoint>",
                        "wellKnownOpenIdConfiguration": "<url specifying .well-known/open-id-configuration endpoint - if this property is set, the other properties of this object are ignored, and authorizationEndpoint, tokenEndpoint, issuer, and certificationUri are set to the corresponding values listed at this endpoint>"
                    }
                },
                "login": {
                    "nameClaimType": "<name of claim containing name>",
                    "scopes": [
                        "openid",
                        "profile",
                        "email"
                    ],
                    "loginParameterNames": [
                        "paramName1=value1",
                        "paramName2=value2"
                    ],
                }
            },
            //...
        }
    }
}
```

## <a name="pin-your-app-to-a-specific-authentication-runtime-version"></a>Alkalmazás kitűzve egy adott hitelesítési futásidejű verzióra

Ha engedélyezi a hitelesítést/engedélyezést, a rendszer a platform középső szoftverét injektálja a HTTP-kérési folyamatba a szolgáltatás [áttekintésében leírtak szerint.](overview-authentication-authorization.md#how-it-works) Ezt a platform-közvetítőprogramot rendszeresen frissítjük új funkciókkal és fejlesztésekkel a rutinplatform-frissítések részeként. Alapértelmezés szerint a web- vagy függvényalkalmazás ennek a platformnak a legújabb verzióján fog futni. Ezek az automatikus frissítések mindig visszamenőlegesen kompatibilisek. Abban a ritka esetben azonban, amikor ez az automatikus frissítés futásidejű problémát vezet be a webes vagy a függvényalkalmazáshoz, ideiglenesen vissza lehet tért az előző, középső szoftveres verzióra. Ez a cikk azt ismerteti, hogyan lehet ideiglenesen kitűzni egy alkalmazást a hitelesítési middleware adott verziójára.

### <a name="automatic-and-manual-version-updates"></a>Automatikus és manuális verziófrissítések 

Az alkalmazást rögzítheti a platform középső szoftverének egy adott verziójára az alkalmazás `runtimeVersion` beállításával. Az alkalmazás mindig a legújabb verzión fut, kivéve, ha úgy dönt, hogy explicit módon visszatűzi egy adott verzióra. Egyszerre több verzió is támogatott lesz. Ha olyan érvénytelen verzióhoz rögzít, amely már nem támogatott, az alkalmazás a legújabb verziót fogja használni. Ha mindig a legújabb verziót futtatja, állítsa `runtimeVersion` a ~1-et. 

### <a name="view-and-update-the-current-runtime-version"></a>Az aktuális futásidejű verzió megtekintése és frissítése

Módosíthatja az alkalmazás által használt futásidejű verziót. Az alkalmazás újraindítása után az új futásidejű verziónak érvénybe kell lép. 

#### <a name="view-the-current-runtime-version"></a>Az aktuális futásidejű verzió megtekintése

A platformhitelesítési middleware aktuális verzióját az Azure CLI használatával vagy az alkalmazás egyik beépített HTTP-végpontján keresztül is megtekintheti.

##### <a name="from-the-azure-cli"></a>Az Azure CLI-ről

Az Azure CLI használatával tekintse meg az aktuális middleware verziót az [az webapp auth show paranccsal.](/cli/azure/webapp/auth#az_webapp_auth_show)

```azurecli-interactive
az webapp auth show --name <my_app_name> \
--resource-group <my_resource_group>
```

Ebben a kódban cserélje `<my_app_name>` le a helyére az alkalmazás nevét. Cserélje le `<my_resource_group>` a helyére az alkalmazás erőforráscsoportja nevét is.

A mező a `runtimeVersion` CLI-kimenetben lesz látható. Az alábbi példakimenethez fog hasonlítani, amelyet az egyértelműség kedvéért csonkoltunk: 
```output
{
  "additionalLoginParams": null,
  "allowedAudiences": null,
    ...
  "runtimeVersion": "1.3.2",
    ...
}
```

##### <a name="from-the-version-endpoint"></a>A verzióvégpontból

A /.auth/version végpontot egy alkalmazáson belül is megtekintheti, hogy az alkalmazás aktuálisan a középső szoftver aktuális verzióját futtatja. Ez az alábbi példakimenethez fog hasonlítani:
```output
{
"version": "1.3.2"
}
```

#### <a name="update-the-current-runtime-version"></a>A jelenlegi futásidejű verzió frissítése

Az Azure CLI használatával frissítheti az alkalmazás beállítását az `runtimeVersion` [az webapp auth update paranccsal.](/cli/azure/webapp/auth#az_webapp_auth_update)

```azurecli-interactive
az webapp auth update --name <my_app_name> \
--resource-group <my_resource_group> \
--runtime-version <version>
```

Cserélje `<my_app_name>` le a helyére az alkalmazás nevét. Cserélje le `<my_resource_group>` a helyére az alkalmazás erőforráscsoportja nevét is. Emellett cserélje `<version>` le a helyére az 1.x futtatás érvényes verzióját vagy `~1` a legújabb verziót. A különböző futásidejű verziók kibocsátási megjegyzéseit [itt] ( találja, hogy megállapítsa, melyik verzióra https://github.com/Azure/app-service-announcements) kell kitűzni a kódot.

Ezt a parancsot a parancs futtatásához [Azure Cloud Shell](../cloud-shell/overview.md) **előző** kódmintában a Kipróbálom használhatja. Az Azure CLI-t helyileg [is használhatja](/cli/azure/install-azure-cli) a parancs végrehajtására az [az login](/cli/azure/reference-index#az_login) végrehajtása után a bejelentkezéshez.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: Felhasználók teljes körű hitelesítése és engedélyezése](tutorial-auth-aad.md)
