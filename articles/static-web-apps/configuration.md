---
title: Az Azure statikus Web Apps konfigurálása
description: Ismerje meg az útvonalak konfigurálását, a biztonsági szabályok betartatását és az Azure statikus Web Apps globális beállításait.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: cshoe
ms.openlocfilehash: b6779de0203246a60bdfa60ea110a0f0d5f26ff3
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2021
ms.locfileid: "106106449"
---
# <a name="configure-azure-static-web-apps"></a>Az Azure statikus Web Apps konfigurálása

Az Azure statikus Web Apps konfigurációja a fájl _staticwebapp.config.js_ van definiálva, amely a következő beállításokat vezérli:

- Útválasztás
- Hitelesítés
- Engedélyezés
- Tartalék szabályok
- HTTP-válasz felülbírálásai
- Globális HTTP-fejléc definíciói
- Egyéni MIME-típusok

## <a name="file-location"></a>Fájl helye

A _staticwebapp.config.jsbekapcsolásának_ ajánlott helye a `app_location` [munkafolyamati fájlban](./github-actions-workflow.md)beállított mappában található. Előfordulhat azonban, hogy a fájlt az alkalmazás forráskódjának mappájába tartozó tetszőleges helyre helyezi.

A részletekért tekintse meg a [példa konfigurációs](#example-configuration-file) fájlját.

> [!IMPORTANT]
> Ha létezik _staticwebapp.config.js_ , a rendszer figyelmen kívül hagyja a [fájl _routes.jsét_](./routes.md) .

## <a name="routes"></a>Útvonalak

Az útválasztási szabályok lehetővé teszik az alkalmazáshoz való hozzáférést lehetővé tevő URL-címek mintájának meghatározását a weben. Az útvonalak az útválasztási szabályok tömbje vannak meghatározva. Példaként tekintse meg a [konfigurációs fájlt](#example-configuration-file) a használati példákhoz.

- A szabályok a tömbben megjelenő sorrendben lesznek végrehajtva `routes` .
- A szabály kiértékelése leáll az első egyezés – az útválasztási szabályok nincsenek összefűzve.
- Teljes hozzáférése van az egyéni szerepkörök neveihez.
  - Létezik néhány beépített szerepkör, amely tartalmazza a és a nevet [`anonymous`](./authentication-authorization.md) [`authenticated`](./authentication-authorization.md) .

Az Útválasztás jelentős mértékben átfedésben van a hitelesítéssel (a felhasználó azonosításával) és az engedélyezéssel (a felhasználói képességek kiosztásával) kapcsolatos fogalmakkal. Ügyeljen rá, hogy olvassa el a [hitelesítési és engedélyezési](authentication-authorization.md) útmutatót a jelen cikk mellett.

A statikus tartalom alapértelmezett fájlja a *index.html* fájl.

## <a name="defining-routes"></a>Útvonalak meghatározása

Minden szabály egy útvonal mintából áll, valamint egy vagy több választható szabály-tulajdonsággal. Példaként tekintse meg a [konfigurációs fájlt](#example-configuration-file) a használati példákhoz.

| Szabály tulajdonsága  | Kötelező | Alapértelmezett érték | Megjegyzés                                                      |
| -------------- | -------- | ------------- | ------------------------------------------------------------ |
| `route`        | Yes      | n.a.          | A hívó által kért útvonal-minta.<ul><li>A [helyettesítő karakterek](#wildcards) az útvonal-elérési utak végén támogatottak.<ul><li>Például az útvonal _rendszergazdája/ \*_ a _rendszergazdai_ elérési úton található bármely útvonalra illeszkedik.</ul></ul>|
| `rewrite`        | No       | n.a.          | Meghatározza a kérelemből visszaadott fájlt vagy elérési utat.<ul><li>Kölcsönösen kizárható egy `redirect` szabályhoz<li>Az Újraírási szabályok nem változtatják meg a böngésző helyét.<li>Az értékeknek az alkalmazás gyökeréhez viszonyítva kell lenniük</ul>  |
| `redirect`        | No       | n.a.          | Meghatározza a kérelem fájl-vagy elérésiút-átirányítási célját.<ul><li>Kölcsönösen kizárható egy `rewrite` szabályhoz.<li>Az átirányítási szabályok megváltoztatják a böngésző helyét.<li>Az alapértelmezett válasz kódja a [`302`](https://developer.mozilla.org/docs/Web/HTTP/Status/302) (ideiglenes átirányítás), de felülbírálható egy [`301`](https://developer.mozilla.org/docs/Web/HTTP/Status/301) (állandó átirányítás).</ul> |
| `allowedRoles` | No       | névtelen     | Az útvonal eléréséhez szükséges szerepkör-nevek listáját határozza meg. <ul><li>Érvényes karakterek a következők:,, `a-z` `A-Z` `0-9` és `_` .<li>A beépített szerepkör az összes nem [`anonymous`](./authentication-authorization.md) hitelesített felhasználóra vonatkozik<li>A beépített szerepkör [`authenticated`](./authentication-authorization.md) minden bejelentkezett felhasználóra vonatkozik.<li>A felhasználóknak legalább egy szerepkörhöz kell tartoznia.<li>A szerepköröket _vagy_ azok alapján kell egyeztetni.<ul><li>Ha egy felhasználó a felsorolt szerepkörök valamelyikében szerepel, akkor a rendszer hozzáférést biztosít.</ul><li>Az egyes felhasználók a szerepkörökhöz vannak társítva a [meghívásokon](authentication-authorization.md)keresztül.</ul> |
| `headers`<a id="route-headers"></a> | No | n.a. | A válaszhoz hozzáadott [HTTP-fejlécek](https://developer.mozilla.org/docs/Web/HTTP/Headers) készlete. <ul><li>Az útvonal-specifikus fejlécek felülbírálják, [`globalHeaders`](#global-headers) Ha az útválasztási fejléc ugyanaz, mint a globális fejléc a válaszban.<li>Egy fejléc eltávolításához állítsa az értéket egy üres sztringre.</ul> |
| `statusCode`   | No       | `200`, `301` vagy `302` átirányításhoz | A válasz [http-állapotkódot](https://developer.mozilla.org/docs/Web/HTTP/Status) . |
| `methods` | No | Minden metódus | Az útvonalnak megfelelő kérelem-metódusok listája. Az elérhető módszerek a következők:,,,,,,, `GET` `HEAD` `POST` `PUT` `DELETE` `CONNECT` `OPTIONS` `TRACE` és `PATCH` . |

Minden tulajdonságnak konkrét célja van a kérelem/válasz folyamatban.

| Cél | Tulajdonságok |
|---|---|
| Útvonalak egyeztetése | `route`, `methods` |
| Engedélyezés az útvonal egyeztetése után | `allowedRoles` |
| Folyamat egy szabály egyeztetése és engedélyezése után | `rewrite` (kérelem módosítása) <br><br>`redirect`, `headers` , `statusCode` (válasz módosítása) |

## <a name="securing-routes-with-roles"></a>Útvonalak biztonságossá tétele szerepkörökkel

Az útvonalak egy vagy több szerepkörnek a szabály tömbbe való felvételével vannak biztosítva `allowedRoles` , és a felhasználók [meghívón](./authentication-authorization.md)keresztül egyéni szerepkörökhöz vannak társítva. Példaként tekintse meg a [konfigurációs fájlt](#example-configuration-file) a használati példákhoz.

Alapértelmezés szerint minden felhasználó a beépített `anonymous` szerepkörhöz tartozik, és az összes bejelentkezett felhasználó tagja a `authenticated` szerepkörnek.

Ha például csak a hitelesített felhasználókra szeretné korlátozni az útvonalat, adja hozzá a beépített `authenticated` szerepkört a `allowedRoles` tömbhöz.

```json
{
  "route": "/profile",
  "allowedRoles": ["authenticated"]
}
```

Szükség szerint új szerepköröket hozhat létre a `allowedRoles` tömbben. Ha csak a rendszergazdákra szeretné korlátozni az útvonalat, a tömbben definiálhatja saját szerepkörét `administrator` `allowedRoles` .

```json
{
  "route": "/admin",
  "allowedRoles": ["administrator"]
}
```

- Teljes hozzáférése van a szerepkörök neveihez; nincsenek lista, amelyeknek a szerepköreinek be kell tartaniuk.
- Az egyes felhasználók a szerepkörökhöz vannak társítva a [meghívásokon](authentication-authorization.md)keresztül.

## <a name="wildcards"></a>Helyettesítő karakterek

A helyettesítő karakterrel megadott szabályok megfelelnek az adott útvonal összes kérelmének, csak az elérési út végén támogatottak, és a fájlkiterjesztés alapján is szűrhetők. Példaként tekintse meg a [konfigurációs fájlt](#example-configuration-file) a használati példákhoz.

Ha például egy naptári alkalmazás útvonalait szeretné megvalósítani, akkor a _Naptár_ útvonala alá tartozó összes URL-címet újraírhatja egyetlen fájl kiszolgálásához.

```json
{
  "route": "/calendar/*",
  "rewrite": "/calendar.html"
}
```

A _calendar.html_ fájl ezután az ügyféloldali útválasztás használatával más nézetet is kiszolgálhat az URL-változatok, például, `/calendar/january/1` `/calendar/2020` és `/calendar/overview` .

A helyettesítő karakteres egyezéseket fájlkiterjesztés alapján is szűrheti. Ha például olyan szabályt szeretne felvenni, amely csak egy adott elérési úton lévő HTML-fájlokra illeszkedik, a következő szabályt hozhatja létre:

```json
{
  "route": "/articles/*.html",
  "headers" : {
    "Cache-Control": "public, max-age=604800, immutable"
  }
}
```

Ha több fájlkiterjesztés alapján szeretne szűrni, a beállításokat kapcsos zárójelek közé helyezi, ahogy az ebben a példában is látható:

```json
{
  "route": "/images/thumbnails/*.{png,jpg,gif}",
  "headers": {
    "Cache-Control": "public, max-age=604800, immutable"
  }
}
```

A helyettesítő karakteres útvonalak gyakori használati esetei a következők:

- Egy adott fájl kiszolgálása egy teljes elérésiút-mintára
- Különböző HTTP-metódusok leképezése egy teljes elérésiút-mintára
- Hitelesítési és engedélyezési szabályok érvényesítése
- Speciális gyorsítótárazási szabályok implementálása

## <a name="fallback-routes"></a>Tartalék útvonalak

Az egyoldalas alkalmazások gyakran az ügyféloldali útválasztásra támaszkodnak. Ezek az ügyféloldali útválasztási szabályok frissítik a böngésző ablakának helyét anélkül, hogy kéréseket kellene visszaküldeni a kiszolgálónak. Ha frissíti az oldalt, vagy közvetlenül az ügyféloldali útválasztási szabályok által létrehozott URL-címekre navigál, a megfelelő HTML-lap kiszolgálása érdekében kiszolgálóoldali tartalék útvonal szükséges (amely általában a _index.html_ az ügyféloldali alkalmazáshoz).

Az alkalmazás úgy konfigurálható, hogy olyan szabályokat használjon, amelyek a tartalék útvonalat implementálják, ahogyan az a következő példában látható, amely egy olyan elérési utat használ, amely a fájl szűrőjét használja:

```json
{
  "navigationFallback": {
    "rewrite": "/index.html",
    "exclude": ["/images/*.{png,jpg,gif}", "/css/*"]
  }
}
```

Az alábbi példa a fájl struktúráját mutatja be, a következő eredmények lehetségesek ezzel a szabállyal.

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

| Kérelmek... | visszatérési érték... | az állapottal... |
| --- | --- | --- |
| *körülbelül* | A */index.html* fájl | `200` |
| */images/logo.png* | A képfájl  | `200` |
| */images/icon.svg* | A */index.html* fájl – mivel az *SVG* -fájlkiterjesztés nem szerepel a `/images/*.{png,jpg,gif}` szűrőben   | `200` |
| */images/unknown.png* | A fájl nem található hiba  | `404` |
| */css/unknown.css* | A fájl nem található hiba  | `404` |
| */css/global.css* | A stíluslap fájl | `200` |
| A */images* -vagy */CSS* -mappákon kívüli egyéb fájlok | A */index.html* fájl | `200` |

## <a name="global-headers"></a>Globális fejlécek

A `globalHeaders` szakasz az egyes válaszokra alkalmazott [HTTP-fejléceket](https://developer.mozilla.org/docs/Web/HTTP/Headers) tartalmazza, hacsak nem bírálja felül az [útvonal fejlécének](#route-headers) szabályát, ellenkező esetben az útvonal és a globális fejlécek fejléceit is visszaadja a rendszer.

Példaként tekintse meg a [konfigurációs fájlt](#example-configuration-file) a használati példákhoz.

A fejlécek eltávolításához állítsa az értéket üres sztringre ( `""` ).

A globális fejlécek gyakori használati esetei többek között a következők:

- Egyéni gyorsítótárszabályok
- Biztonsági házirendek betartatása
- Kódolási beállítások

## <a name="response-overrides"></a>Válasz felülbírálásai

A `responseOverrides` szakasz lehetőséget biztosít egyéni válasz definiálására, ha a kiszolgáló egyébként hibakódot ad vissza. Példaként tekintse meg a [konfigurációs fájlt](#example-configuration-file) a használati példákhoz.

A felülbíráláshoz a következő HTTP-kódok érhetők el:

| Állapotkód | Értelmezés | Lehetséges ok |
| --- | --- | --- |
| [400](https://developer.mozilla.org/docs/Web/HTTP/Status/400) | Hibás kérelem | Érvénytelen meghívó hivatkozás |
| [401](https://developer.mozilla.org/docs/Web/HTTP/Status/401) | Nem engedélyezett | Korlátozott lapokra irányuló kérelem hitelesítés nélkül |
| [403](https://developer.mozilla.org/docs/Web/HTTP/Status/403) | Forbidden |<ul><li>A felhasználó be van jelentkezve, de nem rendelkezik a lap megtekintéséhez szükséges szerepkörökkel.<li>A felhasználó be van jelentkezve, de a futtatókörnyezet nem tudja beolvasni a felhasználói adatokat az identitás jogcímeiből.<li>Túl sok felhasználó jelentkezett be a webhelyre egyéni szerepkörökkel, ezért a futtatókörnyezet nem tud bejelentkezni a felhasználóba.</ul> |
| [404](https://developer.mozilla.org/docs/Web/HTTP/Status/404) | Nem található | A fájl nem található |

A következő példa azt mutatja be, hogyan bírálható felül egy hibakód.

```json
{
    "responseOverrides": {
        "400" : {
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

## <a name="example-configuration-file"></a>Példa konfigurációs fájlra

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
            "methods": [ "GET" ],
            "allowedRoles": ["registeredusers"]
        },
        {
            "route": "/api/*",
            "methods": [ "PUT", "POST", "PATCH", "DELETE" ],
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
        "400" : {
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

A fenti konfiguráció alapján tekintse át a következő forgatókönyveket.

| Kérelmek... | eredmények... |
| --- | --- |
| _/Profile_ | A hitelesített felhasználók kiszolgálják a _/profile/index.html_ fájlt. A nem hitelesített felhasználókat a rendszer átirányítja a _belépéshez_. |
| _/admin_ | A _rendszergazdai_ szerepkörben lévő hitelesített felhasználók a _/Admin/index.html_ fájlt szolgálják ki. A _rendszergazdai_ szerepkörben nem szereplő hitelesített felhasználók az `403` <sup>1</sup>. hibát szolgálják ki. A nem hitelesített felhasználókat a rendszer átirányítja a _belépéshez_. |
| _/logo.png_ | A rendszerképet egy egyéni gyorsítótárazási szabállyal szolgálja ki, ahol a maximális életkor valamivel több mint 182 nap (15 770 000 másodperc). |
| _/api/admin_ | `GET` a _registeredusers_ szerepkörben hitelesített felhasználóktól érkező kéréseket a rendszer ELKÜLDI az API-nak. A _registeredusers_ szerepkörben nem szereplő hitelesített felhasználók és a nem hitelesített felhasználók hibát szolgáltatnak `401` .<br/><br/>`POST`, `PUT` , `PATCH` és `DELETE` a _rendszergazdai_ szerepkörben lévő hitelesített felhasználóktól érkező kéréseket a rendszer elküldi az API-nak. A _rendszergazdai_ szerepkörben nem szereplő hitelesített felhasználók és a nem hitelesített felhasználók hibát szolgáltatnak `401` . |
| _/customers/contoso_ | Azok a hitelesített felhasználók, akik a _rendszergazdához_ vagy az _ügyfelekhez tartozó \_ contoso_ -szerepkörökhöz tartoznak, a _/Customers/contoso/index.html_ fájlt kézbesítik. A _rendszergazda_ vagy az _ügyfelek \_ contoso_ szerepkörrel nem rendelkező hitelesített felhasználók az `403` <sup>1</sup>. hibát szolgálják ki. A nem hitelesített felhasználókat a rendszer átirányítja a _belépéshez_. |
| _/Belépés View_ | A nem hitelesített felhasználók a GitHub használatával hitelesíthetők. |
| _/.auth/login/twitter_ | Mivel az útválasztási szabály letiltja a Twitter-engedélyezést, a rendszer `404` visszaadja a hibát, amely visszatérhet a Serving _/index.html_ `200` állapot kódjával. |
| _/logout_ | A felhasználók ki vannak jelentkezve bármely hitelesítési szolgáltatóból. |
| _/calendar/2021/01_ | A böngésző a _/calendar.html_ fájlt kézbesíti. |
| _/specials_ | A böngésző véglegesen átirányítja a _/Deals_. |
| _/data.jsbekapcsolva_ | A fájl a MIME- `text/json` típussal szolgált. |
| _/About_ vagy bármely olyan mappa, amely megfelel az ügyféloldali útválasztási mintáknak | A _/index.html-_ fájl `200` állapotkódot szolgál. |
| Nem létező fájl a _/images/_ mappában | `404`Hiba. |

<sup>1</sup> egyéni hibaüzenetet adhat meg egy [Válasz felülbírálási szabály](#response-overrides)használatával.

## <a name="restrictions"></a>Korlátozások

A fájl _staticwebapps.config.js_ a következő korlátozások vonatkoznak.

- A maximális fájlméret 100 KB
- Legfeljebb 50 különböző szerepkör

Az általános korlátozásokról és korlátozásokról a [kvóták című cikkben](quotas.md) olvashat.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Hitelesítés és engedélyezés beállítása](authentication-authorization.md)
