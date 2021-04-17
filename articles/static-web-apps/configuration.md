---
title: Az Azure Static Web Apps
description: Megtudhatja, hogyan konfigurálhatja az útvonalakat, kényszeríti a biztonsági szabályokat és a globális beállításokat a Azure Static Web Apps.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 04/09/2021
ms.author: cshoe
ms.openlocfilehash: 9494bcc9941491bbb82c6a948dce720cb9e51424
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502283"
---
# <a name="configure-azure-static-web-apps"></a>Az Azure Static Web Apps

A Azure Static Web Apps fájlstaticwebapp.config.js _meg,_ amely a következő beállításokat szabályozza:

- Útválasztás
- Hitelesítés
- Engedélyezés
- Tartalék szabályok
- HTTP-válasz felülbírálásai
- Globális HTTP-fejlécdefiníciók
- Egyéni MIME-típusok

## <a name="file-location"></a>Fájl helye

A fájl ajánlottstaticwebapp.config.js _a_ munkafolyamat-fájlban beállított `app_location` [mappában található.](./github-actions-workflow.md) A fájl azonban az alkalmazás forráskódmappában található bármely helyen elhelyezhető.

A [részletekért tekintse](#example-configuration-file) meg a konfigurációs példafájlt.

> [!IMPORTANT]
> A [ _routes.jsnem veszi_](./routes.md) figyelembe, ha létezikstaticwebapp.config.jsa _fájlban._

## <a name="routes"></a>Útvonalak

Az útvonalszabályok lehetővé teszik az alkalmazás webes hozzáférését engedélyező URL-címek mintáját. Az útvonalak útválasztási szabályok tömbjeként vannak definiálva. Példákat [a példa konfigurációs fájlban](#example-configuration-file) talál.

- A szabályok akkor is definiálva `routes` vannak a tömbben, ha csak egy útvonala van.
- A szabályok a sorrendben vannak végrehajtva, ahogy a tömbben `routes` megjelennek.
- A szabály kiértékelése az első egyezéskor leáll – az útválasztási szabályok nincsenek összefűve.
- Teljes körűen szabályozhatja az egyéni szerepkörneveket.
  - Van néhány beépített szerepkörnév, amelyek közé tartozik a [`anonymous`](./authentication-authorization.md) és [`authenticated`](./authentication-authorization.md) a .

Az útválasztás jelentősen átfedésben van a hitelesítéssel (a felhasználó azonosításával) és az engedélyezéssel (képességek hozzárendelése a felhasználóhoz) kapcsolatos fogalmakkal. Mindenképpen olvassa el a hitelesítési [és engedélyezési](authentication-authorization.md) útmutatót és ezt a cikket.

A statikus tartalom alapértelmezett fájlja aindex.htm _l_ fájl.

## <a name="defining-routes"></a>Útvonalak definiálása

Minden szabály egy útvonalmintát, valamint egy vagy több választható szabálytulajdonságból áll. Az útválasztási szabályok a tömbben vannak `routes` definiálva. Példákat [a példa konfigurációs fájlban](#example-configuration-file) talál.

| Szabálytulajdonság                       | Kötelező | Alapértelmezett érték                        | Megjegyzés                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| ----------------------------------- | -------- | ------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `route`                             | Igen      | n.a.                                  | A hívó által kért útvonalminta.<ul><li>[A helyettesítő karakterek](#wildcards) az útvonal végén támogatottak.<ul><li>Az útvonal _rendszergazdája/ \*_ például a rendszergazdai útvonalon található _bármely útvonalra illeszkedik._</ul></ul>                                                                                                                                                                                                                                                                                                                                                                                                        |
| `rewrite`                           | Nem       | n.a.                                  | Meghatározza a kérés által visszaadott fájlt vagy elérési utat.<ul><li>Kölcsönösen kizár egy `redirect` szabályt<li>Az átírási szabályok nem változtatják meg a böngésző helyét.<li>Az értékeknek az alkalmazás gyökeréhez képest relatívnak kell lennie</ul>                                                                                                                                                                                                                                                                                                                                                                                                      |
| `redirect`                          | Nem       | n.a.                                  | Meghatározza a kérés fájl- vagy elérésiút-átirányítási célját.<ul><li>A szabályok kölcsönösen `rewrite` kizárják egymást.<li>Az átirányítási szabályok módosítják a böngésző helyét.<li>Az alapértelmezett válaszkód egy (ideiglenes átirányítás), de felülbírálható [`302`](https://developer.mozilla.org/docs/Web/HTTP/Status/302) egy [`301`](https://developer.mozilla.org/docs/Web/HTTP/Status/301) (állandó átirányítással).</ul>                                                                                                                                                                                                              |
| `allowedRoles`                      | Nem       | Névtelen                            | Meghatározza az útvonal eléréséhez szükséges szerepkörnevek listáját. <ul><li>Érvényes karakterek: `a-z` , `A-Z` , és `0-9` `_` .<li>A beépített szerepkör () az összes nem hitelesített [`anonymous`](./authentication-authorization.md) felhasználóra vonatkozik<li>A beépített szerepkör minden bejelentkezett [`authenticated`](./authentication-authorization.md) felhasználóra vonatkozik.<li>A felhasználóknak legalább egy szerepkörhöz kell tartozni.<li>A szerepkörök egy VAGY alapján _vannak megfeleltetve._<ul><li>Ha egy felhasználó a felsorolt szerepkörök valamelyikében szerepel, akkor a rendszer hozzáférést biztosít.</ul><li>Az egyes felhasználók meghívásokkal vannak [szerepkörökhöz társítva.](authentication-authorization.md)</ul> |
| `headers`<a id="route-headers"></a> | Nem       | n.a.                                  | A [válaszhoz hozzáadott](https://developer.mozilla.org/docs/Web/HTTP/Headers) HTTP-fejlécek készlete. <ul><li>Az útvonalspecifikus fejlécek felülírják, ha az útvonalspecifikus fejléc megegyezik a válaszban szereplő globális [`globalHeaders`](#global-headers) fejléccel.<li>Fejléc eltávolításához állítsa az értéket üres sztringre.</ul>                                                                                                                                                                                                                                                                                          |
| `statusCode`                        | Nem       | `200`, `301` vagy `302` átirányítások | A [válasz HTTP-állapotkódja.](https://developer.mozilla.org/docs/Web/HTTP/Status)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| `methods`                           | Nem       | Minden metódus                          | Az útvonalnak megfelelő kérési metódusok listája. Elérhető metódusok: `GET` , , , , , , , , `HEAD` és `POST` `PUT` `DELETE` `CONNECT` `OPTIONS` `TRACE` `PATCH` .                                                                                                                                                                                                                                                                                                                                                                                                                                                             |

Minden tulajdonság egy adott céllal rendelkezik a kérés/válasz folyamatban.

| Cél                                        | Tulajdonságok                                                                                   |
| ---------------------------------------------- | -------------------------------------------------------------------------------------------- |
| Útvonalak egyeztetése                                   | `route`, `methods`                                                                           |
| Engedély egy útvonal egyeztetése után             | `allowedRoles`                                                                               |
| Feldolgozás egy szabály egyeztetése és az engedélyezettek után | `rewrite` (módosítja a kérést) <br><br>`redirect`, `headers` , `statusCode` (módosítja a választ) |

## <a name="securing-routes-with-roles"></a>Útvonalak biztonságossá tétele szerepkörökkel

Az útvonalakat egy vagy több szerepkörnév szabálytömbjére való felvételével biztosítjuk, és a felhasználók egyéni szerepkörökhöz vannak társítva `allowedRoles` [meghívásokkal.](./authentication-authorization.md) Példákat [a példa konfigurációs fájlban](#example-configuration-file) talál.

Alapértelmezés szerint minden felhasználó a beépített szerepkörhöz tartozik, és minden bejelentkezett felhasználó `anonymous` a szerepkör `authenticated` tagja.

Ha például csak a hitelesített felhasználókra szeretne korlátozni egy útvonalat, adja hozzá a beépített szerepkört a `authenticated` `allowedRoles` tömbhöz.

```json
{
  "route": "/profile",
  "allowedRoles": ["authenticated"]
}
```

A tömbben szükség szerint hozhat létre új `allowedRoles` szerepköröket. Ha egy útvonalat csak rendszergazdákra korlátoz, definiálhatja a saját, nevű szerepkörét a `administrator` `allowedRoles` tömbben.

```json
{
  "route": "/admin",
  "allowedRoles": ["administrator"]
}
```

- Teljes körűen szabályozhatja a szerepkörneveket; nincs olyan lista, amelyhez a szerepköröknek meg kell felelniük.
- Az egyes felhasználók meghívásokkal vannak [szerepkörökhöz társítva.](authentication-authorization.md)

## <a name="wildcards"></a>Helyettesítő karakterek

A helyettesítő karakterekre vonatkozó szabályok egy útvonalmintában található összes kérésre vonatkoznak, csak az elérési út végén támogatottak, és fájlkiterjesztéssel szűrhetők. Példákat [a példa konfigurációs fájlban](#example-configuration-file) talál.

Egy naptáralkalmazás útvonalának implementálására például átírhatja a naptár útvonala alá eső összes URL-t, hogy egyetlen fájlt szolgáljon ki. 

```json
{
  "route": "/calendar/*",
  "rewrite": "/calendar.html"
}
```

A _calendar.html_ fájl ezután ügyféloldali útválasztással más nézetet tud kiszolgálni az URL-változatok számára, például a , a és a `/calendar/january/1` `/calendar/2020` `/calendar/overview` .

A helyettesítő karakteres egyezéseket fájlkiterjesztés alapján szűrheti. Ha például olyan szabályt szeretne hozzáadni, amely csak egy adott elérési úton található HTML-fájlokra illeszkedik, a következő szabályt hozhatja létre:

```json
{
  "route": "/articles/*.html",
  "headers": {
    "Cache-Control": "public, max-age=604800, immutable"
  }
}
```

Több fájlkiterjesztés szűréséhez a lehetőségeket kapcsos zárójelek közé kell foglalni, ahogy az alábbi példában látható:

```json
{
  "route": "/images/thumbnails/*.{png,jpg,gif}",
  "headers": {
    "Cache-Control": "public, max-age=604800, immutable"
  }
}
```

A helyettesítő karakteres útvonalak gyakori használatieset-esete a következő:

- Adott fájl kiszolgálása egy teljes elérésiút-mintához
- Különböző HTTP-metódusok leképezése egy teljes elérésiút-mintára
- Hitelesítési és engedélyezési szabályok kényszerítése
- Specializált gyorsítótárazás szabályainak megvalósítása

## <a name="fallback-routes"></a>Tartalék útvonalak

Az egyoldalas alkalmazások gyakran ügyféloldali útválasztásra támaszkodnak. Ezek az ügyféloldali útválasztási szabályok anélkül frissítik a böngésző ablakának helyét, hogy kéréseket adnak vissza a kiszolgálónak. Ha frissíti az oldalt, vagy közvetlenül az ügyféloldali útválasztási szabályok által létrehozott URL-címekre navigál, kiszolgálóoldali tartalék útvonalra van szükség a megfelelő HTML-oldal (amely általában az ügyféloldali alkalmazás _index.html-_ útvonala).

Az alkalmazást konfigurálhatja úgy, hogy olyan szabályokat használjon, amelyek tartalék útvonalat valósítanak meg az alábbi példában látható módon, amely helyettesítő karaktereket használ fájlszűrővel:

```json
{
  "navigationFallback": {
    "rewrite": "/index.html",
    "exclude": ["/images/*.{png,jpg,gif}", "/css/*"]
  }
}
```

Az alábbi példa fájlstruktúra a következő eredményeket teszi lehetővé ezzel a s szabálysal.

```files
├── images
│   ├── logo.png
│   ├── headshot.jpg
│   └── screenshot.gif
│
├── css
│   └── global.css
│
└── index.html
```

| Kérések a...                                         | Visszatér...                                                                                                    | a következő állapottal... |
| ------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------- | ------------------ |
| _/about/_                                              | A _/index.htmfájl_                                                                                        | `200`              |
| _/images/logo.png_                                     | A képfájl                                                                                                | `200`              |
| _/images/icon.svg_                                     | A _/index.html_ fájl – mivel az _svg_ fájlkiterjesztés nem szerepel a `/images/*.{png,jpg,gif}` szűrőben | `200`              |
| _/images/unknown.png_                                  | A fájl nem található hiba                                                                                          | `404`              |
| _/css/unknown.css_                                     | A fájl nem található hiba                                                                                          | `404`              |
| _/css/global.css_                                      | A stíluslap fájlja                                                                                           | `200`              |
| Az _/images_ vagy a _/css_ mappán kívüli egyéb fájlok | A _/index.htmfájl_                                                                                        | `200`              |

## <a name="global-headers"></a>Globális fejlécek

A szakasz az egyes válaszokra alkalmazott HTTP-fejléceket tartalmaz, kivéve, ha egy útvonalfejléc-szabály felülbírálja őket, ellenkező esetben a rendszer az útvonal mindkét fejlécének unióját adja `globalHeaders` vissza. [](https://developer.mozilla.org/docs/Web/HTTP/Headers) [](#route-headers)

Példákat [a példa konfigurációs fájlban](#example-configuration-file) talál.

Fejléc eltávolításához állítsa be az értéket egy üres sztringre ( `""` ).

Néhány gyakori eset a globális fejlécek esetén:

- Egyéni gyorsítótárszabályok
- Biztonsági szabályzatok érvényesítése
- Kódolási beállítások

## <a name="response-overrides"></a>Válasz-felülbírálások

A szakasz lehetőséget biztosít egyéni válasz meghatározására, ha a kiszolgáló egyébként `responseOverrides` hibakódot adna vissza. Példákat [a példa konfigurációs fájlban](#example-configuration-file) talál.

A következő HTTP-kódok felülbírálhatóak:

| Állapotkód                                                   | Értelmezés      | Lehetséges ok                                                                                                                                                                                                                                                                                     |
| ------------------------------------------------------------- | ------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [400](https://developer.mozilla.org/docs/Web/HTTP/Status/400) | Hibás kérés  | Érvénytelen meghívási hivatkozás                                                                                                                                                                                                                                                                            |
| [401](https://developer.mozilla.org/docs/Web/HTTP/Status/401) | Nem engedélyezett | Korlátozott oldalakra vonatkozó kérés hitelesítés nélkül                                                                                                                                                                                                                                                  |
| [403](https://developer.mozilla.org/docs/Web/HTTP/Status/403) | Forbidden    | <ul><li>A felhasználó be van jelentkezve, de nem rendelkezik az oldal megtekintéséhez szükséges szerepkörökkel.<li>A felhasználó be van jelentkezve, de a futásidő nem tudja lehozni a felhasználói adatokat az identitás jogcímeikből.<li>Túl sok felhasználó jelentkezett be a webhelyre egyéni szerepkörökkel, ezért a futásidő nem tud bejelentkezni a felhasználóba.</ul> |
| [404](https://developer.mozilla.org/docs/Web/HTTP/Status/404) | Nem található    | A fájl nem található                                                                                                                                                                                                                                                                                     |

Az alábbi példakonfiguráció bemutatja, hogyan bírálható felül egy hibakód.

```json
{
  "responseOverrides": {
    "400": {
      "rewrite": "/invalid-invitation-error.html",
      "statusCode": 200
    },
    "401": {
      "statusCode": 302,
      "redirect": "/login"
    },
    "403": {
      "rewrite": "/custom-forbidden-page.html",
      "statusCode": 200
    },
    "404": {
      "rewrite": "/custom-404.html",
      "statusCode": 200
    }
  }
}
```

## <a name="example-configuration-file"></a>Konfigurációs példafájl

```json
{
  "routes": [
    {
      "route": "/profile",
      "allowedRoles": ["authenticated"]
    },
    {
      "route": "/admin/*",
      "allowedRoles": ["administrator"]
    },
    {
      "route": "/images/*",
      "headers": {
        "cache-control": "must-revalidate, max-age=15770000"
      }
    },
    {
      "route": "/api/*",
      "methods": ["GET"],
      "allowedRoles": ["registeredusers"]
    },
    {
      "route": "/api/*",
      "methods": ["PUT", "POST", "PATCH", "DELETE"],
      "allowedRoles": ["administrator"]
    },
    {
      "route": "/api/*",
      "allowedRoles": ["authenticated"]
    },
    {
      "route": "/customers/contoso",
      "allowedRoles": ["administrator", "customers_contoso"]
    },
    {
      "route": "/login",
      "rewrite": "/.auth/login/github"
    },
    {
      "route": "/.auth/login/twitter",
      "statusCode": 404
    },
    {
      "route": "/logout",
      "redirect": "/.auth/logout"
    },
    {
      "route": "/calendar/*",
      "rewrite": "/calendar.html"
    },
    {
      "route": "/specials",
      "redirect": "/deals",
      "statusCode": 301
    }
  ],
  "navigationFallback": {
    "rewrite": "index.html",
    "exclude": ["/images/*.{png,jpg,gif}", "/css/*"]
  },
  "responseOverrides": {
    "400": {
      "rewrite": "/invalid-invitation-error.html"
    },
    "401": {
      "redirect": "/login",
      "statusCode": 302
    },
    "403": {
      "rewrite": "/custom-forbidden-page.html"
    },
    "404": {
      "rewrite": "/404.html"
    }
  },
  "globalHeaders": {
    "content-security-policy": "default-src https: 'unsafe-eval' 'unsafe-inline'; object-src 'none'"
  },
  "mimeTypes": {
    ".json": "text/json"
  }
}
```

A fenti konfiguráció alapján tekintse át az alábbi forgatókönyveket.

| Kérések a...                                                    | a következőt...                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| ----------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| _/profile_                                                        | A hitelesített felhasználók számára a _/profile/index.html fájl szolgál_ ki. A nem hitelesített felhasználókat a rendszer a _/login webhelyre irányítja át._                                                                                                                                                                                                                                                                                                                              |
| _/admin/_                                                         | A rendszergazdai szerepkörben a hitelesített _felhasználók_ számára a _/admin/index.html fájl szolgál._ A rendszergazdai szerepkörben nem rendelkező hitelesített _felhasználók_ `403` <sup>1-es hibaüzenetet kapnak.</sup> A nem hitelesített felhasználókat a rendszer a _/login webhelyre irányítja át._                                                                                                                                                                                                          |
| _/logo.png_                                                       | A rendszerképet egy egyéni gyorsítótárszabály alapján szolgálja ki, ahol a maximális életkor kicsivel több mint 182 nap (15 770 000 másodperc).                                                                                                                                                                                                                                                                                                                                   |
| _/api/admin_                                                      | `GET`A regisztrált felhasználók szerepkörben  hitelesített felhasználóktól származó kéréseket a rendszer elküldi az API-nak. A nem regisztrált  felhasználók és a nem hitelesített felhasználók hibaüzenetet `401` kapnak.<br/><br/>`POST`A rendszergazdai szerepkörben a hitelesített felhasználók , , és kérései `PUT` `PATCH` az `DELETE` API-nak vannak elküldve.  A rendszergazdai szerepkör  nélküli hitelesített felhasználók és a nem hitelesített felhasználók hibaüzenetet `401` kapnak. |
| _/customers/contoso_                                              | Azok a hitelesített felhasználók,  akik  rendszergazdai vagy customers_contoso szerepkörhöz tartoznak, a _/customers/contoso/index.html fájlt kapják_ meg. A rendszergazdai szerepkörben vagy szerepkörben _nem customers_contoso_ hitelesített felhasználók  `403` <sup>1-es hibaüzenetet kapnak.</sup> A nem hitelesített felhasználókat a rendszer a _/login webhelyre irányítja át._                                                                                                                            |
| _/login_                                                          | A nem hitelesített felhasználóknak hitelesítenie kell magukat a GitHubbal.                                                                                                                                                                                                                                                                                                                                                                             |
| _/.auth/login/twitter_                                            | Mivel az útválasztási szabály letiltja a Twitter-hitelesítést, a rendszer hibát ad vissza, amely a `404` _/index.html_ állapotkóddal való `200` kiszolgálására vonatkozik.                                                                                                                                                                                                                                                                                     |
| _/logout_                                                         | A felhasználók kijelentkeznek bármely hitelesítésszolgáltatóból.                                                                                                                                                                                                                                                                                                                                                                                          |
| _/calendar/2021/01_                                               | A böngésző a _/calendar.html fájlt szolgálja_ ki.                                                                                                                                                                                                                                                                                                                                                                                              |
| _/specials (speciális)_                                                       | A böngésző véglegesen át lesz irányítva a _/deals webhelyre._                                                                                                                                                                                                                                                                                                                                                                                            |
| _/data.jsbe_                                                      | A MIME típussal `text/json` kiszolgált fájl.                                                                                                                                                                                                                                                                                                                                                                                               |
| _/about_, vagy bármely olyan mappa, amely megfelel az ügyféloldali útválasztási mintáknak | Az _/index.html_ fájlt egy állapotkód `200` szolgálja ki.                                                                                                                                                                                                                                                                                                                                                                                    |
| Egy nem létező fájl az _/images/ mappában_                     | Egy `404` hiba.                                                                                                                                                                                                                                                                                                                                                                                                                                |

<sup>1</sup> Egyéni hibaoldalt is meg lehet adni egy [válasz-felülbírálási s szabály használatával.](#response-overrides)

## <a name="restrictions"></a>Korlátozások

A fájlonstaticwebapps.config.js _korlátozások vonatkoznak._

- A maximális fájlméret 100 KB
- Legfeljebb 50 különböző szerepkör

Az általános [korlátozásokat és](quotas.md) korlátozásokat lásd a Kvóták cikkben.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Hitelesítés és engedélyezés beállítása](authentication-authorization.md)
