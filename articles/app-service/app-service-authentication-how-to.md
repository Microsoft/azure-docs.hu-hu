---
title: A AuthN/AuthZ speciális használata
description: Megtudhatja, hogyan szabhatja testre a hitelesítési és engedélyezési funkciót App Service különböző forgatókönyvek esetén, valamint felhasználói jogcímeket és különböző jogkivonatokat kaphat.
ms.topic: article
ms.date: 07/08/2020
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: fc2916cbccc21262467533b0b497b14f4f4b941c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105034877"
---
# <a name="advanced-usage-of-authentication-and-authorization-in-azure-app-service"></a>A hitelesítés és az engedélyezés speciális használata Azure App Service

Ez a cikk bemutatja, hogyan szabhatja testre a [app Service beépített hitelesítését és engedélyezését](overview-authentication-authorization.md), és hogyan kezelheti az alkalmazás identitását. 

A gyors kezdéshez tekintse meg a következő oktatóanyagok egyikét:

* [Oktatóanyag: Felhasználók hitelesítése és engedélyezése végpontok között az Azure App Service-ben](tutorial-auth-aad.md)
* [Az alkalmazás konfigurálása az Azure Active Directory-bejelentkezés használatára](configure-authentication-provider-aad.md)
* [Az alkalmazás konfigurálása a Facebook-bejelentkezés használatára](configure-authentication-provider-facebook.md)
* [Az alkalmazás konfigurálása a Google-bejelentkezés használatára](configure-authentication-provider-google.md)
* [Az alkalmazás konfigurálása a Microsoft-fiókbejelentkezés használatára](configure-authentication-provider-microsoft.md)
* [Az alkalmazás konfigurálása a Twitter-bejelentkezés használatára](configure-authentication-provider-twitter.md)
* [Alkalmazás konfigurálása OpenID Connect-szolgáltató használatával történő bejelentkezéshez (előzetes verzió)](configure-authentication-provider-openid-connect.md)
* [Az alkalmazás konfigurálása az Apple-be való bejelentkezésre (előzetes verzió)](configure-authentication-provider-apple.md)

## <a name="use-multiple-sign-in-providers"></a>Több bejelentkezési szolgáltató használata

A portál konfigurációja nem biztosít kulcsrakész módot arra, hogy több bejelentkezési szolgáltatót nyújtson be a felhasználók számára (például Facebook és Twitter). Azonban nem nehéz felvenni a funkciót az alkalmazáshoz. A lépéseket a következőképpen vázoljuk fel:

Először a Azure Portal **hitelesítés/engedélyezés** lapján konfigurálja az összes engedélyezni kívánt identitás-szolgáltatót.

**Ha a kérelem nem hitelesítve van**, válassza a **Névtelen kérelmek engedélyezése (nincs művelet)** lehetőséget.

A bejelentkezési oldalon vagy a navigációs sávon vagy az alkalmazás bármely más helyén vegyen fel egy bejelentkezési hivatkozást az összes engedélyezett szolgáltatóhoz ( `/.auth/login/<provider>` ). Például:

```html
<a href="/.auth/login/aad">Log in with Azure AD</a>
<a href="/.auth/login/microsoftaccount">Log in with Microsoft Account</a>
<a href="/.auth/login/facebook">Log in with Facebook</a>
<a href="/.auth/login/google">Log in with Google</a>
<a href="/.auth/login/twitter">Log in with Twitter</a>
<a href="/.auth/login/apple">Log in with Apple</a>
```

Amikor a felhasználó rákattint az egyik hivatkozásra, megnyílik a megfelelő bejelentkezési oldal, amely bejelentkezik a felhasználóba.

Ha át szeretné irányítani a felhasználói bejelentkezést egy egyéni URL-címre, használja a `post_login_redirect_url` lekérdezési karakterlánc paramétert (a rendszer nem tévesztendő össze az identitás-szolgáltatói konfigurációban található átirányítási URI azonosítóval). Ha például a felhasználót a bejelentkezés után szeretné navigálni `/Home/Index` , használja a következő HTML-kódot:

```html
<a href="/.auth/login/<provider>?post_login_redirect_url=/Home/Index">Log in</a>
```

## <a name="validate-tokens-from-providers"></a>Jogkivonatok érvényesítése a szolgáltatóktól

Az ügyfél által irányított bejelentkezés során az alkalmazás manuálisan bejelentkezik a felhasználónak a szolgáltatóba, majd elküldi a hitelesítési jogkivonatot az ellenőrzéshez App Service (lásd: [hitelesítési folyamat](overview-authentication-authorization.md#authentication-flow)). Ez az érvényesítés önmagában nem biztosít hozzáférést a kívánt alkalmazás-erőforrásokhoz, de sikeres ellenőrzés esetén egy munkamenet-tokent ad meg, amely az alkalmazás erőforrásainak elérésére használható. 

A szolgáltatói jogkivonat érvényesítéséhez a App Service alkalmazást először konfigurálni kell a kívánt szolgáltatóval. Futásidőben a szolgáltatótól kapott hitelesítési jogkivonat lekérése után a tokent az `/.auth/login/<provider>` érvényesítéshez tegye közzé. Például: 

```
POST https://<appname>.azurewebsites.net/.auth/login/aad HTTP/1.1
Content-Type: application/json

{"id_token":"<token>","access_token":"<token>"}
```

A jogkivonat formátuma a szolgáltatótól függően némileg eltér. A részletekért tekintse meg az alábbi táblázatot:

| Szolgáltató értéke | A kérelem törzsében kötelező | Megjegyzések |
|-|-|-|
| `aad` | `{"access_token":"<access_token>"}` | |
| `microsoftaccount` | `{"access_token":"<token>"}` | A `expires_in` tulajdonság megadása nem kötelező. <br/>Ha az élő szolgáltatásokból kér jogkivonatot, mindig kérje a `wl.basic` hatókört. |
| `google` | `{"id_token":"<id_token>"}` | A `authorization_code` tulajdonság megadása nem kötelező. Ha meg van adva, azt is elvégezheti a `redirect_uri` tulajdonsággal együtt. |
| `facebook`| `{"access_token":"<user_access_token>"}` | Érvényes [felhasználói hozzáférési tokent](https://developers.facebook.com/docs/facebook-login/access-tokens) használhat a Facebook-ból. |
| `twitter` | `{"access_token":"<access_token>", "access_token_secret":"<acces_token_secret>"}` | |
| | | |

Ha a szolgáltatói jogkivonat ellenőrzése sikeresen megtörtént, az API a `authenticationToken` Válasz törzsében, azaz a munkamenet-jogkivonatban tér vissza. 

```json
{
    "authenticationToken": "...",
    "user": {
        "userId": "sid:..."
    }
}
```

Ha ezzel a munkamenet-jogkivonattal rendelkezik, a védett alkalmazás erőforrásaihoz a `X-ZUMO-AUTH` fejlécet a http-kérelmekhez hozzáadva érheti el. Például: 

```
GET https://<appname>.azurewebsites.net/api/products/1
X-ZUMO-AUTH: <authenticationToken_value>
```

## <a name="sign-out-of-a-session"></a>Kijelentkezés a munkamenetből

A felhasználók elindíthatnak egy kijelentkezést `GET` az alkalmazás végpontjának küldött kérelem elküldésével `/.auth/logout` . A `GET` kérelem a következő műveleteket végzi el:

- Törli a hitelesítési cookie-kat az aktuális munkamenetből.
- Törli az aktuális felhasználó jogkivonatait a jogkivonat-tárolóból.
- A Azure Active Directory és a Google esetében a kiszolgálóoldali kijelentkezést hajt végre az identitás-szolgáltatón.

A következő egy egyszerű kijelentkezési hivatkozás egy webhelyen:

```html
<a href="/.auth/logout">Sign out</a>
```

Alapértelmezés szerint a sikeres kijelentkezés az URL-címre irányítja át az ügyfelet `/.auth/logout/done` . A kijelentkezési átirányítási lapot a lekérdezési paraméter hozzáadásával módosíthatja `post_logout_redirect_uri` . Például:

```
GET /.auth/logout?post_logout_redirect_uri=/index.html
```

Javasoljuk, hogy [kódolja](https://wikipedia.org/wiki/Percent-encoding) a értékét `post_logout_redirect_uri` .

Teljes URL-címek használatakor az URL-címnek vagy ugyanabban a tartományban kell lennie, vagy az alkalmazás számára engedélyezett külső átirányítási URL-címnek kell lennie. A következő példában a `https://myexternalurl.com` nem ugyanabban a tartományban üzemeltetett átirányítás:

```
GET /.auth/logout?post_logout_redirect_uri=https%3A%2F%2Fmyexternalurl.com
```

Futtassa a következő parancsot a [Azure Cloud Shellban](../cloud-shell/quickstart.md):

```azurecli-interactive
az webapp auth update --name <app_name> --resource-group <group_name> --allowed-external-redirect-urls "https://myexternalurl.com"
```

## <a name="preserve-url-fragments"></a>URL-töredékek megőrzése

Miután a felhasználók bejelentkeznek az alkalmazásba, általában ugyanarra a lapra szeretnének átirányítani, például: `/wiki/Main_Page#SectionZ` . Mivel azonban a rendszer soha nem küldi el a kiszolgálónak az [URL-töredékeket](https://wikipedia.org/wiki/Fragment_identifier) (például `#SectionZ` ), a rendszer alapértelmezés szerint nem őrzi meg őket a OAuth bejelentkezés befejezése után, és visszairányítja az alkalmazásba. A felhasználók ezt követően optimális élményt kapnak, amikor újra kell navigálni a kívánt horgonyhoz. Ez a korlátozás az összes kiszolgálóoldali hitelesítési megoldásra vonatkozik.

App Service hitelesítésnél megőrizheti az URL-töredékeket a OAuth-bejelentkezés során. Ehhez állítson be egy nevű alkalmazást `WEBSITE_AUTH_PRESERVE_URL_FRAGMENT` `true` . Ezt megteheti a [Azure Portalban](https://portal.azure.com), vagy egyszerűen futtathatja a következő parancsot a [Azure Cloud Shellban](../cloud-shell/quickstart.md):

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group <group_name> --settings WEBSITE_AUTH_PRESERVE_URL_FRAGMENT="true"
```

## <a name="access-user-claims"></a>Felhasználói jogcímek elérése

A App Service speciális fejlécek használatával átadja az alkalmazásnak a felhasználói jogcímeket. A külső kérelmek nem állíthatják be ezeket a fejléceket, ezért csak akkor jelennek meg, ha App Service. A fejlécek például a következők:

* X-MS-CLIENT-PRINCIPAL-NAME
* X-MS-CLIENT-PRINCIPAL-ID

A bármely nyelven vagy keretrendszerben írt kód a fenti fejlécek által igényelt információkat kérheti le. A ASP.NET 4,6 alkalmazások esetében a **ClaimsPrincipal** automatikusan a megfelelő értékekkel van beállítva. ASP.NET Core azonban nem biztosít olyan hitelesítési middleware-t, amely App Service felhasználói jogcímekbe integrálódik. Megkerülő megoldásért tekintse meg a következőt: [MaximeRouiller. Azure. AppService. EasyAuth](https://github.com/MaximRouiller/MaximeRouiller.Azure.AppService.EasyAuth).

Ha a [jogkivonat-tároló](overview-authentication-authorization.md#token-store) engedélyezve van az alkalmazáshoz, a meghívásával további részleteket is megtudhat a hitelesített felhasználóról `/.auth/me` . Az Mobile Apps Server SDK-k segítő módszereket biztosítanak az adatkezeléshez. További információ: [az azure Mobile Apps Node.js SDK használata](/previous-versions/azure/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk#howto-tables-getidentity), valamint az Azure-hoz [készült .net backend Server SDK-val való együttműködés. Mobile apps](/previous-versions/azure/app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk#user-info).

## <a name="retrieve-tokens-in-app-code"></a>Jogkivonatok beolvasása az alkalmazás kódjában

A kiszolgáló kódjából a szolgáltatóra jellemző jogkivonatokat a rendszer a kérelem fejlécébe helyezi, így könnyen elérheti őket. A következő táblázat a lehetséges tokenek fejlécének neveit tartalmazza:

| Szolgáltató | Fejléc neve |
|-|-|
| Azure Active Directory | `X-MS-TOKEN-AAD-ID-TOKEN` <br/> `X-MS-TOKEN-AAD-ACCESS-TOKEN` <br/> `X-MS-TOKEN-AAD-EXPIRES-ON`  <br/> `X-MS-TOKEN-AAD-REFRESH-TOKEN` |
| Facebook-token | `X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN` <br/> `X-MS-TOKEN-FACEBOOK-EXPIRES-ON` |
| Google | `X-MS-TOKEN-GOOGLE-ID-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-ACCESS-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-EXPIRES-ON` <br/> `X-MS-TOKEN-GOOGLE-REFRESH-TOKEN` |
| Microsoft-fiók | `X-MS-TOKEN-MICROSOFTACCOUNT-ACCESS-TOKEN` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-EXPIRES-ON` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-AUTHENTICATION-TOKEN` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-REFRESH-TOKEN` |
| Twitter | `X-MS-TOKEN-TWITTER-ACCESS-TOKEN` <br/> `X-MS-TOKEN-TWITTER-ACCESS-TOKEN-SECRET` |
|||

Az ügyfél kódjából (például egy mobil alkalmazásból vagy egy böngészőből származó JavaScriptből) küldjön HTTP- `GET` kérelmet a `/.auth/me` (jogkivonat-[tárolónak](overview-authentication-authorization.md#token-store) engedélyezve kell lennie). A visszaadott JSON a szolgáltatóra jellemző jogkivonatokat tartalmaz.

> [!NOTE]
> A hozzáférési tokenek a szolgáltatói erőforrások eléréséhez szükségesek, így csak akkor jelennek meg, ha a szolgáltatót egy ügyfél titkos kulcsával konfigurálja. A frissítési tokenek beszerzésével kapcsolatban lásd: hozzáférési tokenek frissítése.

## <a name="refresh-identity-provider-tokens"></a>Identitás-szolgáltatói tokenek frissítése

Ha a szolgáltató hozzáférési jogkivonata (nem a [munkamenet jogkivonata](#extend-session-token-expiration-grace-period)) lejár, újra hitelesítenie kell a felhasználót, mielőtt újra felhasználja a tokent. A jogkivonat lejáratának elkerüléséhez `GET` hívja meg az `/.auth/refresh` alkalmazás végpontját. A híváskor App Service automatikusan frissíti a hitelesített felhasználó hozzáférési jogkivonatait a [jogkivonat-tárolóban](overview-authentication-authorization.md#token-store) . Az alkalmazás kódjának a jogkivonatokra vonatkozó későbbi kérelmei megkapják a frissített jogkivonatokat. Ahhoz azonban, hogy a jogkivonat-frissítés működjön, a jogkivonat-tárolónak tartalmaznia kell a szolgáltató [frissítési jogkivonatait](https://auth0.com/learn/refresh-tokens/) . A frissítési tokenek beszerzésének módját az egyes szolgáltatók dokumentálják, de a következő lista egy rövid összefoglalás:

- **Google**: fűzze hozzá a `access_type=offline` lekérdezési karakterlánc paramétert az `/.auth/login/google` API-híváshoz. Ha a Mobile Apps SDK-t használja, hozzáadhatja a paramétert a `LogicAsync` túlterhelések egyikéhez (lásd: [Google frissítési jogkivonatok](https://developers.google.com/identity/protocols/OpenIDConnect#refresh-tokens)).
- **Facebook**: nem biztosít frissítési jogkivonatokat. A hosszú élettartamú tokenek 60 nap múlva lejárnak (lásd [a Facebook lejáratát és a hozzáférési tokenek kiterjesztését](https://developers.facebook.com/docs/facebook-login/access-tokens/expiration-and-extension)).
- **Twitter**: a hozzáférési tokenek nem járnak le (lásd: [Twitter OAuth – gyakori kérdések](https://developer.twitter.com/en/docs/authentication/faq)).
- **Microsoft-fiók**: a [Microsoft-fiók hitelesítési beállításainak konfigurálásakor](configure-authentication-provider-microsoft.md)válassza ki a `wl.offline_access` hatókört.
- **Azure Active Directory**: a-ben [https://resources.azure.com](https://resources.azure.com) végezze el a következő lépéseket:
    1. Az oldal tetején válassza az **írás/írás** lehetőséget.
    2. A bal oldali böngészőben navigáljon az **előfizetések** > * *_\<subscription\_name_** > **resourceGroups** > * *_ \<resource\_group\_name> _* * > **szolgáltatók**  >  **Microsoft. Web**  >  **Sites** > * *_ \<app\_name> _ * * > **config**  >  **authsettings elemre**. 
    3. Kattintson a **Szerkesztés** gombra.
    4. Módosítsa a következő tulajdonságot. Cserélje le az helyére az _\<app\_id>_ elérni kívánt szolgáltatás Azure Active Directory alkalmazás-azonosítóját.

        ```json
        "additionalLoginParams": ["response_type=code id_token", "resource=<app_id>"]
        ```

    5. Kattintson a **put** elemre. 

Miután konfigurálta a szolgáltatót, [megkeresheti a frissítési jogkivonatot és a hozzáférési token lejárati idejét](#retrieve-tokens-in-app-code) a jogkivonat-tárolóban. 

Ha bármikor frissíteni szeretné a hozzáférési jogkivonatot, csak `/.auth/refresh` tetszőleges nyelven hívhat meg. A következő kódrészlet a jQuery használatával frissíti a hozzáférési jogkivonatait egy JavaScript-ügyfélről.

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

Ha a felhasználó visszavonja az alkalmazás számára biztosított engedélyeket, a hívása `/.auth/me` sikertelen lehet `403 Forbidden` . A hibák diagnosztizálásához az alkalmazás naplófájljaiban tájékozódhat a részletekről.

## <a name="extend-session-token-expiration-grace-period"></a>Munkamenet-jogkivonat lejárati türelmi időszakának kiterjesztése

A hitelesített munkamenet 8 óra elteltével lejár. A hitelesített munkamenet lejárta után a rendszer alapértelmezés szerint 72 órás türelmi időszakot használ. A türelmi időszakon belül lehetősége van a munkamenet-jogkivonat frissítésére App Service a felhasználó ismételt hitelesítése nélkül. Csak akkor hívható meg `/.auth/refresh` , ha a munkamenet-jogkivonat érvénytelenné válik, és nem kell nyomon követnie a jogkivonat lejáratát. Ha a 72 órás türelmi időszak lejár, a felhasználónak újra be kell jelentkeznie, hogy érvényes munkamenet-tokent kapjon.

Ha a 72 órán belül nincs elég idő, kiterjesztheti ezt a lejárati időszakot. Ha hosszabb időn keresztül kiterjeszti a lejáratot, jelentős biztonsági következményekkel járhat (például egy hitelesítési jogkivonat kiszivárgásakor vagy ellopásakor). Ezért hagyja meg az alapértelmezett 72 órát, vagy állítsa a kiterjesztési időszakot a legkisebb értékre.

Az alapértelmezett lejárati időszak meghosszabbításához futtassa a következő parancsot a [Cloud Shellban](../cloud-shell/overview.md).

```azurecli-interactive
az webapp auth update --resource-group <group_name> --name <app_name> --token-refresh-extension-hours <hours>
```

> [!NOTE]
> A türelmi időszak csak a App Service hitelesített munkamenetre vonatkozik, nem az Identitáskezelő jogkivonatára. A lejárt szolgáltatói jogkivonatok esetében nincs türelmi időszak. 
>

## <a name="limit-the-domain-of-sign-in-accounts"></a>Bejelentkezési fiókok tartományának korlátozása

A Microsoft-fiók és a Azure Active Directory egyaránt lehetővé teszi több tartományból való bejelentkezést. A Microsoft-fiók például lehetővé teszi a _Outlook.com_, a _live.com_ és a _hotmail.com_ fiók használatát. Az Azure AD lehetővé teszi a bejelentkezési fiókok tetszőleges számú egyéni tartományának használatát. Előfordulhat azonban, hogy a felhasználóknak azonnal fel kell gyorsítania a saját márkás Azure AD bejelentkezési oldalát (például `contoso.com` ). Ha a bejelentkezési fiókok tartománynevét szeretné javasolni, kövesse az alábbi lépéseket.

A alkalmazásban [https://resources.azure.com](https://resources.azure.com) navigáljon az **előfizetések** > * *_\<subscription\_name_** > **resourceGroups** > * *_ \<resource\_group\_name> _* * > **szolgáltatók**  >  **Microsoft. Web**  >  **Sites** > * *_ \<app\_name> _ * * > **config**  >  **authsettings elemre**. 

Kattintson a **Szerkesztés** gombra, módosítsa a következő tulajdonságot, majd kattintson a **put** elemre. Ne felejtse el lecserélni _\<domain\_name>_ a kívánt tartományra.

```json
"additionalLoginParams": ["domain_hint=<domain_name>"]
```

Ezzel a beállítással a rendszer hozzáfűzi a `domain_hint` lekérdezési karakterlánc paramétert a bejelentkezési átirányítás URL-címéhez. 

> [!IMPORTANT]
> Lehetséges, hogy az ügyfél el szeretné távolítani a `domain_hint` paramétert az átirányítási URL-cím fogadása után, majd egy másik tartományba való bejelentkezést. Így amíg ez a függvény kényelmes, nem biztonsági funkció.
>

## <a name="authorize-or-deny-users"></a>Felhasználók engedélyezése vagy megtagadása

Míg App Service gondoskodik a legegyszerűbb engedélyezési esetről (azaz a nem hitelesített kérelmek elutasításáról), az alkalmazás részletesebb engedélyezési viselkedést igényelhet, például csak egy adott felhasználói csoportra korlátozhatja a hozzáférést. Bizonyos esetekben egyéni programkódot kell írnia a bejelentkezett felhasználó hozzáférésének engedélyezéséhez vagy megtagadásához. Más esetekben előfordulhat, hogy a App Service vagy az Ön személyazonosság-szolgáltatója a kód módosítása nélkül tud segítséget nyújtani.

- [Kiszolgáló szintje](#server-level-windows-apps-only)
- [Identitás-szolgáltató szintje](#identity-provider-level)
- [Alkalmazás szintje](#application-level)

### <a name="server-level-windows-apps-only"></a>Kiszolgáló szintje (csak Windows-alkalmazások esetén)

Bármely Windows-alkalmazás esetében a *Web.config* fájl szerkesztésével megadhatja az IIS-webkiszolgáló engedélyezési viselkedését. A Linux-alkalmazások nem használják az IIS-t, és nem konfigurálhatók *Web.configon* keresztül.

1. Navigáljon ide: `https://<app-name>.scm.azurewebsites.net/DebugConsole`

1. A App Service-fájlok böngésző-tallózójában navigáljon a *hely/wwwroot* elemre. Ha a *Web.config* nem létezik, hozza létre az **+**  >  **új fájl** kiválasztásával. 

1. Válassza ki a ceruzát *Web.config* a szerkesztéshez. Adja hozzá a következő konfigurációs kódot, és kattintson a **Mentés** gombra. Ha *Web.config* már létezik, egyszerűen adja hozzá a `<authorization>` elemet a benne található összes elemhez. Adja hozzá azokat a fiókokat, amelyeket engedélyezni szeretne a `<allow>` elemben.

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

### <a name="identity-provider-level"></a>Identitás-szolgáltató szintje

Az identitás-szolgáltató bizonyos kulcsrakész engedélyezést is biztosíthat. Például:

- [Azure app Service](configure-authentication-provider-aad.md)esetében a [vállalati szintű hozzáférés](../active-directory/manage-apps/what-is-access-management.md) közvetlenül az Azure ad-ben is kezelhető. Útmutatásért lásd: [felhasználó hozzáférésének eltávolítása egy alkalmazáshoz](../active-directory/manage-apps/methods-for-removing-user-access.md).
- A [Google](configure-authentication-provider-google.md)-ban a [szervezethez](https://cloud.google.com/resource-manager/docs/cloud-platform-resource-hierarchy#organizations) tartozó Google API-projektek konfigurálhatók úgy, hogy csak a szervezet felhasználói számára engedélyezzenek hozzáférést (lásd: a [Google **OAuth 2,0** -támogatás beállítása oldal](https://support.google.com/cloud/answer/6158849?hl=en)).

### <a name="application-level"></a>Alkalmazás szintje

Ha a többi szint valamelyike nem rendelkezik a szükséges engedélyekkel, vagy ha a platform vagy az identitás szolgáltatója nem támogatott, egyéni kódot kell írnia a felhasználók engedélyezéséhez a [felhasználói jogcímek](#access-user-claims)alapján.

## <a name="updating-the-configuration-version-preview"></a>A konfiguráció verziójának frissítése (előzetes verzió)

A felügyeleti API két verziója létezik a hitelesítés/engedélyezés szolgáltatáshoz. Az előnézeti v2 verziója szükséges a (z) "hitelesítés (előzetes verzió)" felhasználói felületéhez a Azure Portal. Egy alkalmazás, amely már a v1 API-t használja, a v2 verzióra frissíthet néhány módosítás után. A titkos konfigurációt a tárolóhely-Sticky alkalmazás beállításaiba kell áthelyezni. A Microsoft-fiók szolgáltatójának konfigurálása jelenleg nem támogatott a v2-ben.

> [!WARNING]
> A v2 előzetes verzióra való Migrálás letilthatja az alkalmazás App Service hitelesítési/engedélyezési funkciójának kezelését egyes ügyfeleken, például a Azure Portal, az Azure CLI és a Azure PowerShell meglévő felhasználói felületén. Ez nem vonható vissza. Az előzetes verzió ideje alatt a termelési számítási feladatok áttelepítése nem javasolt vagy nem támogatott. Csak kövesse az ebben a szakaszban ismertetett lépéseket az alkalmazások teszteléséhez.

### <a name="moving-secrets-to-application-settings"></a>A titkok áthelyezése az alkalmazás beállításaiba

1. Szerezze be meglévő konfigurációját a v1 API használatával:

   ```azurecli
   # For Web Apps
   az webapp auth show -g <group_name> -n <site_name>

   # For Azure Functions
   az functionapp auth show -g <group_name> -n <site_name>
   ```

   Az eredményül kapott JSON-adattartalomban jegyezze fel az egyes konfigurált szolgáltatóhoz használt titkos értéket:

   * HRE `clientSecret`
   * Google `googleClientSecret`
   * Facebook `facebookAppSecret`
   * Twitter `twitterConsumerSecret`
   * Microsoft-fiók: `microsoftAccountClientSecret`

   > [!IMPORTANT]
   > A titkos értékek fontos biztonsági hitelesítő adatok, és körültekintően kell kezelni őket. Ne ossza meg ezeket az értékeket, és ne maradjon meg a helyi gépen.

1. Tárolóhely-Sticky Alkalmazásbeállítások létrehozása minden titkos értékhez. Megadhatja az egyes Alkalmazásbeállítások nevét. Az értéknek egyeznie kell azzal, amit az előző lépésben kapott, vagy az adott értékkel létrehozott [Key Vault titokra hivatkozik](./app-service-key-vault-references.md?toc=/azure/azure-functions/toc.json) .

   A beállítás létrehozásához használja a Azure Portal, vagy futtassa a következő variációját minden szolgáltatónál:

   ```azurecli
   # For Web Apps, Google example    
   az webapp config appsettings set -g <group_name> -n <site_name> --slot-settings GOOGLE_PROVIDER_AUTHENTICATION_SECRET=<value_from_previous_step>

   # For Azure Functions, Twitter example
   az functionapp config appsettings set -g <group_name> -n <site_name> --slot-settings TWITTER_PROVIDER_AUTHENTICATION_SECRET=<value_from_previous_step>
   ```

   > [!NOTE]
   > Ennek a konfigurációnak az alkalmazási beállításait tárolóhelyként kell megjelölni, ami azt jelenti, hogy a rendszer nem helyezi át a környezetek között egy [tárolóhely-swap művelet](./deploy-staging-slots.md)során. Ennek az az oka, hogy maga a hitelesítési konfiguráció a környezethez van kötve. 

1. Hozzon létre egy nevű új JSON-fájlt `authsettings.json` . Végezze el a korábban kapott kimenetet, és távolítsa el az összes titkos értéket. Írja a fennmaradó kimenetet a fájlba, és ügyeljen arra, hogy a rendszer ne tartalmazza a titkos kulcsot. Bizonyos esetekben előfordulhat, hogy a konfiguráció üres karakterláncokat tartalmazó tömbökkel rendelkezik. Győződjön meg arról `microsoftAccountOAuthScopes` , hogy a nem, és ha igen, ezt az értéket állítsa a következőre: `null` .

1. Adjon hozzá egy olyan tulajdonságot, `authsettings.json` amely az egyes szolgáltatóknál korábban létrehozott alkalmazás-beállítási névre mutat:
 
   * HRE `clientSecretSettingName`
   * Google `googleClientSecretSettingName`
   * Facebook `facebookAppSecretSettingName`
   * Twitter `twitterConsumerSecretSettingName`
   * Microsoft-fiók: `microsoftAccountClientSecretSettingName`

   A művelet után egy példa a következőhöz hasonlóan néz ki: ebben az esetben csak a HRE van konfigurálva:

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

1. A fájl elküldése az alkalmazás új hitelesítési/engedélyezési konfigurációjához:

   ```azurecli
   az rest --method PUT --url "/subscriptions/<subscription_id>/resourceGroups/<group_name>/providers/Microsoft.Web/sites/<site_name>/config/authsettings?api-version=2020-06-01" --body @./authsettings.json
   ```

1. Ellenőrizze, hogy az alkalmazás továbbra is a várt módon működik-e a kézmozdulat után.

1. Törölje az előző lépésekben használt fájlt.

Ekkor áttelepítette az alkalmazást, hogy az identitás-szolgáltatói titkokat az alkalmazás beállításainak megfelelően tárolja.

### <a name="support-for-microsoft-account-registrations"></a>Microsoft-fiók regisztrációk támogatása

A v2 API jelenleg nem támogatja a Microsoft-fiókot eltérő szolgáltatóként. Ehelyett az átszervezett [Microsoft Identity platformot](../active-directory/develop/v2-overview.md) használja a személyes Microsoft-fiókkal rendelkező felhasználók bejelentkezésére. A v2 API-ra való áttéréskor a v1 Azure Active Directory konfiguráció a Microsoft Identity platform szolgáltatójának konfigurálására szolgál.

Ha a meglévő konfigurációja Microsoft-fiókot tartalmaz, és nem tartalmaz Azure Active Directory szolgáltatót, átválthatja a konfigurációt a Azure Active Directory szolgáltatóra, majd elvégezheti az áttelepítést. Ehhez tegye a következőket:

1. Nyissa meg [**Alkalmazásregisztrációk**](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) a Azure Portalban, és keresse meg a Microsoft-fiókjához társított regisztrációt. Előfordulhat, hogy a "személyes fiókból származó alkalmazások" fejléc alatt található.
1. Navigáljon a "hitelesítés" lapra a regisztrációhoz. Az "URI-k átirányítása" alatt egy bejegyzést kell megjelennie `/.auth/login/microsoftaccount/callback` . Másolja ezt az URI-t.
1. Vegyen fel egy olyan új URI-t, amely megfelel az imént másolt elemnek, kivéve, ha az már véget ért `/.auth/login/aad/callback` . Ez lehetővé teszi a regisztráció használatát a App Service hitelesítés/engedélyezés konfigurációjában.
1. Navigáljon a App Service hitelesítés/engedélyezési konfigurációhoz az alkalmazáshoz.
1. Gyűjtse össze a Microsoft-fiók szolgáltatójának konfigurációját.
1. Konfigurálja a Azure Active Directory szolgáltatót a "speciális" felügyeleti mód használatával, és adja meg az előző lépésben összegyűjtött ügyfél-azonosítót és az ügyfél titkos kulcsának értékeit. A kibocsátó URL-címéhez használja a use (használat) `<authentication-endpoint>/<tenant-id>/v2.0` kifejezést, és cserélje le a (z) és a (z) helyére *\<authentication-endpoint>* a [felhőalapú környezet hitelesítési végpontját](../active-directory/develop/authentication-national-cloud.md#azure-ad-authentication-endpoints) (például https://login.microsoftonline.com : "" a globális Azure-hoz) *\<tenant-id>* . 
1. Miután mentette a konfigurációt, tesztelje a bejelentkezési folyamatot úgy, hogy a böngészőben navigál a `/.auth/login/aad` webhelyen található végponthoz, és befejezi a bejelentkezési folyamatot.
1. Ezen a ponton sikeresen átmásolta a konfigurációt, de a Microsoft-fiókok meglévő konfigurációja továbbra is fennáll. Mielőtt eltávolítja, győződjön meg arról, hogy az alkalmazás összes része hivatkozik a Azure Active Directory szolgáltatóra a bejelentkezési hivatkozások használatával stb. Ellenőrizze, hogy az alkalmazás összes része a várt módon működik-e.
1. Ha ellenőrizte, hogy a dolgok a HRE Azure Active Directory szolgáltatón keresztül működnek, eltávolíthatja a Microsoft-fiók szolgáltatójának konfigurációját.

Előfordulhat, hogy egyes alkalmazások már külön regisztrációval rendelkeznek Azure Active Directory és a Microsoft-fiókhoz. Ezek az alkalmazások jelenleg nem telepíthetők át. 

> [!WARNING]
> A két regisztráció megszervezése a HRE-alkalmazás által [támogatott fióktípus](../active-directory/develop/supported-accounts-validation.md) módosításával lehetséges. Ez azonban a Microsoft-fiókok felhasználói számára új beleegyezés-kérést kényszerít, és a felhasználók identitási jogcímei eltérőek lehetnek a szerkezetben, `sub` különösen az értékek módosítása óta, mivel új alkalmazás-azonosító van használatban. Ez a megközelítés nem ajánlott, ha alaposan megértette. Ehelyett várnia kell a két regisztráció támogatását a v2 API felületén.

### <a name="switching-to-v2"></a>Váltás a v2-re

A fenti lépések elvégzése után navigáljon az alkalmazáshoz a Azure Portal. Válassza a "hitelesítés (előzetes verzió)" szakaszt. 

Azt is megteheti, hogy egy PUT-kérelmet helyez el az `config/authsettingsv2` erőforráshoz a hely erőforrása alatt. A hasznos adatok sémája ugyanaz, mint a [fájl használata](#config-file) szakaszban rögzített konfiguráció.

## <a name="configure-using-a-file-preview"></a><a name="config-file"> </a>Konfigurálás fájl használatával (előzetes verzió)

Az Auth beállításai opcionálisan konfigurálhatók az üzemelő példány által biztosított fájlon keresztül is. Erre szükség lehet a App Service hitelesítés/engedélyezés bizonyos előzetes verziójú képességeitől.

> [!IMPORTANT]
> Ne feledje, hogy az alkalmazás hasznos, ezért ez a fájl a környezetek között mozog, a [tárolóhelyek](./deploy-staging-slots.md)esetében. Valószínű, hogy egy másik, az egyes tárolóhelyekre rögzített alkalmazás-regisztrációt szeretne használni, és ezekben az esetekben a konfigurációs fájl használata helyett továbbra is a szabványos konfigurációs módszert kell használnia.

### <a name="enabling-file-based-configuration"></a>A fájl alapú konfiguráció engedélyezése

> [!CAUTION]
> Az előzetes verzióban a fájl alapú konfiguráció engedélyezésével letilthatja az alkalmazás App Service hitelesítési/engedélyezési funkciójának kezelését bizonyos ügyfeleken, például a Azure Portal, az Azure CLI és a Azure PowerShell használatával.

1. Hozzon létre egy új JSON-fájlt a konfigurációhoz a projekt gyökérkönyvtárában (a web/Function alkalmazás D:\home\site\wwwroot üzembe helyezése). Adja meg a kívánt konfigurációt a [fájl alapú konfigurációs hivatkozás](#configuration-file-reference)alapján. Meglévő Azure Resource Manager konfigurációjának módosításakor ügyeljen arra, hogy a gyűjteményben rögzített tulajdonságokat a `authsettings` konfigurációs fájlba fordítsa.

2. Módosítsa a meglévő konfigurációt, amely a [Azure Resource Manager](../azure-resource-manager/management/overview.md) API-ban van rögzítve `Microsoft.Web/sites/<siteName>/config/authsettings` . Ennek módosításához használhat [Azure Resource Manager sablont](../azure-resource-manager/templates/overview.md) vagy eszközt, például [Azure erőforrás-kezelőt](https://resources.azure.com/). A authsettings elemre gyűjteményben három tulajdonságot kell megadnia (és el is távolíthatja másokat):

    1.  Beállítás értéke `enabled` "true"
    2.  Beállítás értéke `isAuthFromFile` "true"
    3.  Állítsa a `authFilePath` fájl nevére (például "auth.json")

> [!NOTE]
> A `authFilePath` platformok közötti változó formátuma. Windows rendszeren a relatív és az abszolút elérési út is támogatott. A relatív érték használata javasolt. A Linux esetében jelenleg csak az abszolút elérési utak támogatottak, így a beállítás értéke "/Home/site/wwwroot/auth.json" vagy hasonló lehet.

Ha elvégezte ezt a konfigurációs frissítést, a rendszer a fájl tartalmát fogja használni App Service hitelesítés/engedélyezés viselkedésének meghatározásához az adott helyen. Ha bármikor vissza szeretne térni Azure Resource Manager konfigurációhoz, a `isAuthFromFile` "false" (hamis) értékre állíthatja vissza.

### <a name="configuration-file-reference"></a>Konfigurációs fájl leírása

A konfigurációs fájlra hivatkozó összes titkot [alkalmazási beállításokként](./configure-common.md#configure-app-settings)kell tárolni. Megadhatja a beállításokat, amelyeket szeretne. Győződjön meg arról, hogy a konfigurációs fájl hivatkozásai ugyanazokat a kulcsokat használják.

Az alábbi kimeríti a fájl lehetséges konfigurációs beállításait:

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

## <a name="pin-your-app-to-a-specific-authentication-runtime-version"></a>Alkalmazás rögzítése egy adott hitelesítési futtatókörnyezet-verzióra

A hitelesítés/engedélyezés engedélyezésekor a platform middleware-t a [szolgáltatás áttekintése](overview-authentication-authorization.md#how-it-works)című témakörben leírtak szerint fecskendezik be a http-kérési folyamatba. Ezt a platformot rendszeresen frissítik a platform legújabb frissítéseinek részeként új funkciókkal és újdonságokkal. Alapértelmezés szerint a web vagy Function alkalmazás a platform middleware legújabb verziójában fog futni. Ezek az automatikus frissítések mindig visszafelé kompatibilisek. Azonban abban a ritka esetben, ha ez az automatikus frissítés futásidejű problémát jelent a web vagy Function alkalmazás számára, átmenetileg visszaállíthatja az előző middleware-verziót. Ez a cikk azt ismerteti, hogyan lehet ideiglenesen PIN-kódot rögzíteni az alkalmazások számára a hitelesítési middleware egy adott verziójához.

### <a name="automatic-and-manual-version-updates"></a>Az automatikus és a manuális verzió frissítései 

Az alkalmazást az alkalmazás egy beállításának beállításával rögzítheti a platform middleware egy adott verziójához `runtimeVersion` . Az alkalmazás mindig a legújabb verzión fut, kivéve, ha úgy dönt, hogy kifejezetten egy adott verzióra szeretné rögzíteni. Egyszerre csak néhány támogatott verzió érhető el. Ha egy érvénytelen, már nem támogatott verzióra rögzít, az alkalmazás a legújabb verziót fogja használni. A legújabb verzió mindig futtatásához állítsa a következőt: `runtimeVersion` ~ 1. 

### <a name="view-and-update-the-current-runtime-version"></a>Az aktuális futtatókörnyezet verziójának megtekintése és frissítése

Módosíthatja az alkalmazás által használt futtatókörnyezet verzióját. Az új futtatókörnyezet verziója az alkalmazás újraindítása után lép érvénybe. 

#### <a name="view-the-current-runtime-version"></a>Az aktuális futtatókörnyezet verziójának megtekintése

Megtekintheti a platform-hitelesítési middleware aktuális verzióját az Azure CLI használatával vagy az alkalmazás beépített HTTP-végpontjának egyikével.

##### <a name="from-the-azure-cli"></a>Az Azure CLI-ből

Az Azure CLI használatával tekintse meg az aktuális middleware-verziót az az [WebApp Auth show](/cli/azure/webapp/auth#az-webapp-auth-show) paranccsal.

```azurecli-interactive
az webapp auth show --name <my_app_name> \
--resource-group <my_resource_group>
```

Ebben a kódban cserélje le az `<my_app_name>` alkalmazást az alkalmazás nevére. Az `<my_resource_group>` alkalmazáshoz tartozó erőforráscsoport nevét is cserélje le.

Ekkor megjelenik a `runtimeVersion` CLI kimenetben lévő mező. A következő példához hasonló kimenetet fog hasonlítani, amelyet az egyértelműség érdekében csonkolt: 
```output
{
  "additionalLoginParams": null,
  "allowedAudiences": null,
    ...
  "runtimeVersion": "1.3.2",
    ...
}
```

##### <a name="from-the-version-endpoint"></a>A verzió végpontján

Egy alkalmazás/.auth/Version-végpontját is elérheti, ha meg szeretné tekinteni az alkalmazás által futó aktuális middleware-verziót. A következő példához hasonló kimenetet fog kinézni:
```output
{
"version": "1.3.2"
}
```

#### <a name="update-the-current-runtime-version"></a>Az aktuális futtatókörnyezet verziójának frissítése

Az Azure CLI használatával az az `runtimeVersion` [WebApp Auth Update](/cli/azure/webapp/auth#az-webapp-auth-update) paranccsal frissítheti az alkalmazás beállításait.

```azurecli-interactive
az webapp auth update --name <my_app_name> \
--resource-group <my_resource_group> \
--runtime-version <version>
```

A helyére írja `<my_app_name>` be az alkalmazás nevét. Az `<my_resource_group>` alkalmazáshoz tartozó erőforráscsoport nevét is cserélje le. Továbbá cserélje le az értékét az `<version>` 1. x futtatókörnyezet érvényes verziójára vagy `~1` a legújabb verzióra. A kibocsátási megjegyzéseket a különböző futtatókörnyezet-verziókban találja ([ide]) (a https://github.com/Azure/app-service-announcements) rögzítéshez használt verzió meghatározásához.

Ezt a parancsot a [Azure Cloud Shell](../cloud-shell/overview.md) futtathatja, ha az előző kódrészletben a **kipróbálás** lehetőséget választja. Az [Azure CLI helyi](/cli/azure/install-azure-cli) használatával is végrehajthatja ezt a parancsot az [az login (bejelentkezés](/cli/azure/reference-index#az-login) ) parancs végrehajtása után.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: Felhasználók teljes körű hitelesítése és engedélyezése](tutorial-auth-aad.md)
