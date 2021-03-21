---
title: Útvonalak az Azure statikus Web Apps
description: Tudnivalók a háttérbeli útválasztási szabályokról és az útvonalak szerepkörökkel való biztonságossá tételéről.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: 5cbcbcf8914a663a6d039abecd6a4488eaf677b2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101739644"
---
# <a name="routes-in-azure-static-web-apps-preview"></a>Útvonalak az Azure statikus Web Apps előzetes verziójában

> [!IMPORTANT]
> A fájl *routes.jsban* definiált funkció már elavult, és jobban megvalósítható az Azure statikus Web Apps [konfigurációs fájljában](./configuration.md#routes).

Az Azure statikus Web Apps útválasztása meghatározza a háttér-útválasztási szabályokat és a statikus tartalom és<sup>az API-</sup>k engedélyezési viselkedését. A szabályok a fájlban lévő _routes.js_ szabályok tömbje vannak meghatározva.

- A fájl _routes.jsjának_ léteznie kell az alkalmazás Build-összetevő mappájának gyökerében.
- A szabályok a tömbben megjelenő sorrendben lesznek végrehajtva `routes` .
- A szabály kiértékelése az első egyeztetéskor leáll. Az útválasztási szabályok nincsenek összefűzve.
- A szerepkörök a fájl _routes.jsban_ vannak definiálva, és a felhasználók [meghívón](authentication-authorization.md)keresztül vannak társítva a szerepkörökhöz.
- Teljes hozzáférése van a szerepkörök neveihez.

Az Útválasztás témaköre jelentősen átfedésben van a hitelesítési és engedélyezési fogalmakkal. Ügyeljen rá, hogy olvassa el a [hitelesítési és engedélyezési](authentication-authorization.md) útmutatót a jelen cikk mellett.

A részletekért tekintse meg a [példában szereplő útvonal-fájlt](#example-route-file) .

## <a name="location"></a>Hely

> [!IMPORTANT]
> A fájl *routes.jsban* definiált funkció már elavult, és jobban megvalósítható az Azure statikus Web Apps [konfigurációs fájljában](./configuration.md#routes).

A fájl _routes.jsjának_ léteznie kell az alkalmazás Build-összetevő mappájának gyökerében. Ha a webalkalmazás olyan Build lépést tartalmaz, amely egy adott mappából a Build-összetevő mappájába másolt létrehozott fájlokat, akkor a fájl _routes.jsának_ léteznie kell az adott mappában.

A következő táblázat felsorolja a megfelelő helyet, amellyel a _routes.jsa_ fájl számos előtér-keretrendszerhez és-könyvtárhoz elhelyezhető.

|Keretrendszer/könyvtár | Hely  |
|---------|----------|
| Angular | _kapcsolatobjektumok_   |
| React   | _public_  |
| Svelte  | _public_   |
| Vue     | _public_ |
| Blazor  | _wwwroot_ |

A fenti táblázat csak néhány, az Azure statikus Web Appsával kompatibilis keretrendszert és könyvtárat képvisel. További információért lásd az [előtér-keretrendszerek és-kódtárak konfigurálását](./front-end-frameworks.md) ismertető témakört.

## <a name="defining-routes"></a>Útvonalak meghatározása

> [!IMPORTANT]
> A fájl *routes.jsban* definiált funkció már elavult, és jobban megvalósítható az Azure statikus Web Apps [konfigurációs fájljában](./configuration.md#routes).

Az útvonalak a (z) _routes.js_ fájlon vannak definiálva az útválasztási szabályok tömbje a `routes` tulajdonságon. Minden szabály egy útvonal mintából áll, valamint egy vagy több választható szabály-tulajdonsággal. A használati példákat lásd a [példaként szolgáló útválasztási fájlban](#example-route-file) .

| Szabály tulajdonsága  | Kötelező | Alapértelmezett érték | Megjegyzés                                                      |
| -------------- | -------- | ------------- | ------------------------------------------------------------ |
| `route`        | Yes      | n.a.          | A hívó által kért útvonal-minta.<ul><li>A [helyettesítő karakterek](#wildcards) az útvonal-elérési utak végén támogatottak. Például az útvonal _rendszergazdája/ \*_ a _rendszergazdai_ elérési úton található bármely útvonalra illeszkedik.<li>Az útvonal alapértelmezett fájlja _index.html_.</ul>|
| `serve`        | No       | n.a.          | Meghatározza a kérelemből visszaadott fájlt vagy elérési utat. A fájl elérési útja és neve eltérő lehet a kért elérési útról. Ha `serve` nincs megadva érték, a rendszer a kért elérési utat használja. A querystring paraméterek nem támogatottak; `serve` az értékeknek tényleges fájlokra kell mutatniuk.  |
| `allowedRoles` | No       | névtelen     | A szerepkörök neveinek tömbje. <ul><li>Érvényes karakterek a következők:,, `a-z` `A-Z` `0-9` és `_` .<li>A beépített szerepkör minden nem `anonymous` hitelesített felhasználóra érvényes.<li>A beépített szerepkör `authenticated` minden bejelentkezett felhasználóra érvényes.<li>A felhasználóknak legalább egy szerepkörhöz kell tartoznia.<li>A szerepköröket _vagy_ azok alapján kell egyeztetni. Ha egy felhasználó a felsorolt szerepkörök valamelyikében szerepel, akkor a rendszer hozzáférést biztosít.<li>Az egyes felhasználók a szerepkörökhöz vannak társítva a [meghívásokon](authentication-authorization.md)keresztül.</ul> |
| `statusCode`   | No       | 200           | A kérelem [http-állapotkód](https://wikipedia.org/wiki/List_of_HTTP_status_codes) -válasza. |

## <a name="securing-routes-with-roles"></a>Útvonalak biztonságossá tétele szerepkörökkel

> [!IMPORTANT]
> A fájl *routes.jsban* definiált funkció már elavult, és jobban megvalósítható az Azure statikus Web Apps [konfigurációs fájljában](./configuration.md#routes).

Az útvonalak egy vagy több szerepkörnek a szabály tömbbe való felvételével biztonságosak `allowedRoles` . A használati példákat lásd a [példaként szolgáló útválasztási fájlban](#example-route-file) .

Alapértelmezés szerint minden felhasználó a beépített `anonymous` szerepkörhöz tartozik, és az összes bejelentkezett felhasználó tagja a `authenticated` szerepkörnek. Ha például csak a hitelesített felhasználókra szeretné korlátozni az útvonalat, adja hozzá a beépített `authenticated` szerepkört a `allowedRoles` tömbhöz.

```json
{
  "route": "/profile",
  "allowedRoles": ["authenticated"]
}
```

Szükség szerint új szerepköröket hozhat létre a `allowedRoles` tömbben. Az útvonalak csak rendszergazdákra való korlátozásához definiálhat egy `administrator` szerepkört a `allowedRoles` tömbben.

```json
{
  "route": "/admin",
  "allowedRoles": ["administrator"]
}
```

- Teljes hozzáférése van a szerepkörök neveihez; nincsenek lista, amelyeknek a szerepköreinek be kell tartaniuk.
- Az egyes felhasználók a szerepkörökhöz vannak társítva a [meghívásokon](authentication-authorization.md)keresztül.

## <a name="wildcards"></a>Helyettesítő karakterek

> [!IMPORTANT]
> A fájl *routes.jsban* definiált funkció már elavult, és jobban megvalósítható az Azure statikus Web Apps [konfigurációs fájljában](./configuration.md#routes).

A helyettesítő karakteres szabályok az adott útvonal mintájában lévő összes kérelemnek megfelelnek. Ha meghatároz egy `serve` értéket a szabályban, a megnevezett fájl vagy elérési út válaszként szolgál.

A naptári alkalmazások útvonalának megvalósításához például leképezheti a _naptári_ útvonal alá tartozó összes URL-címet egyetlen fájl kiszolgálásához.

```json
{
  "route": "/calendar/*",
  "serve": "/calendar.html"
}
```

A _calendar.html_ fájl ezután az ügyféloldali útválasztás használatával más nézetet is kiszolgálhat az URL-változatok, például, `/calendar/january/1` `/calendar/2020` és `/calendar/overview` .

Az útvonalakat helyettesítő karakterekkel is biztonságossá teheti. A következő _példában a rendszergazdai elérési_ úton kért összes fájlhoz egy hitelesített felhasználónak kell tartoznia, aki tagja a _rendszergazda_ szerepkörnek.

```json
{
  "route": "/admin/*",
  "allowedRoles": ["administrator"]
}
```

> [!NOTE]
> A kiszolgált fájl által hivatkozott fájlokra vonatkozó kérelmeket a rendszer csak a hitelesítési ellenőrzésekhez értékeli. A helyettesítő karakteres elérési úton lévő CSS-fájlokra irányuló kérések például a CSS-fájl, nem pedig a fájl definíciója `serve` . A CSS-fájl mindaddig rendelkezésre áll, amíg a felhasználó megfelel a szükséges hitelesítési követelményeknek.

## <a name="fallback-routes"></a>Tartalék útvonalak

> [!IMPORTANT]
> A fájl *routes.jsban* definiált funkció már elavult, és jobban megvalósítható az Azure statikus Web Apps [konfigurációs fájljában](./configuration.md#routes).

Egyoldalas alkalmazások, függetlenül attól, hogy az előtér-JavaScript-keretrendszerek vagy-kódtárak vagy webszerelvény-platformok, például a Blazer használják-e, gyakran az ügyféloldali útválasztásra támaszkodnak a webalkalmazások navigációs felületén. Ezek az ügyféloldali útválasztási szabályok frissítik a böngésző ablakának helyét anélkül, hogy kéréseket kellene visszaküldeni a kiszolgálónak. Ha frissíti az oldalt, vagy közvetlenül az ügyféloldali útválasztási szabályok által létrehozott helyekre navigál, a megfelelő HTML-oldal kiszolgálásához kiszolgálóoldali tartalék útvonal szükséges.

A következő példában egy közös tartalék útvonal látható:

```json
{
  "routes": [
    {
      "route": "/*",
      "serve": "/index.html",
      "statusCode": 200
    }
  ]
}
```

Az útválasztási szabályokban a tartalék útvonalnak szerepelnie kell az utolsóként, mivel a korábban meghatározott szabályok által nem észlelt összes kérést kigyűjti.

## <a name="redirects"></a>Átirányítja

> [!IMPORTANT]
> A fájl *routes.jsban* definiált funkció már elavult, és jobban megvalósítható az Azure statikus Web Apps [konfigurációs fájljában](./configuration.md#routes).

Az [301](https://en.wikipedia.org/wiki/HTTP_301) -es és a [302](https://en.wikipedia.org/wiki/HTTP_302) -es HTTP-állapotkódok használatával átirányíthatja a kérelmeket az egyik útvonalról a másikra.

A következő szabály például létrehoz egy 301-es átirányítást _old-page.html_ -ről _new-page.html_-re.

```json
{
  "route": "/old-page.html",
  "serve": "/new-page.html",
  "statusCode": 301
}
```

Az átirányítások olyan elérési utakkal is működnek, amelyek nem határoznak meg külön fájlokat.

```json
{
  "route": "/about-us",
  "serve": "/about",
  "statusCode": 301
}
```

## <a name="custom-error-pages"></a>Egyéni hibalapok

> [!IMPORTANT]
> A fájl *routes.jsban* definiált funkció már elavult, és jobban megvalósítható az Azure statikus Web Apps [konfigurációs fájljában](./configuration.md#routes).

Előfordulhat, hogy a felhasználók számos különböző szituációba ütköznek, ami hibát okozhat. A tömb használatával egyéni felhasználói élményt adhat meg a `platformErrorOverrides` hibákra reagálva. Tekintse át a [példaként szolgáló útválasztási fájlt](#example-route-file) a tömb elhelyezéséhez a fájl _routes.js_ .

> [!NOTE]
> Ha egy kérelem a platform felülbírálási szintjére kerül, az útválasztási szabályok nem futnak újra.

A következő táblázat az elérhető platform-hibák felülbírálásait sorolja fel:

| Hiba típusa  | HTTP-állapotkód | Leírás |
|---------|---------|---------|
| `NotFound` | 404  | Nem található lap a kiszolgálón. |
| `Unauthenticated` | 401 | A felhasználó nincs bejelentkezve egy [hitelesítési szolgáltatóval](authentication-authorization.md). |
| `Unauthorized_InsufficientUserInformation` | 401 | A felhasználó fiókja a hitelesítési szolgáltatón nincs konfigurálva a szükséges adatvédelemre. Ez a hiba olyan helyzetekben fordulhat elő, mint amikor az alkalmazás megkéri a hitelesítő szolgáltatót a felhasználó e-mail-címére, de a felhasználó úgy döntött, hogy korlátozza a hozzáférést az e-mail-címre. |
| `Unauthorized_InvalidInvitationLink` | 401 | Egy meghívás lejárt, vagy a felhasználó egy másik címzett számára létrehozott meghívó hivatkozást követett.  |
| `Unauthorized_MissingRoles` | 401 | A felhasználó nem tagja egy szükséges szerepkörnek. |
| `Unauthorized_TooManyUsers` | 401 | A hely elérte a felhasználók maximális számát, és a kiszolgáló korlátozza a további kiegészítéseket. Ez a hiba az ügyfél számára érhető el, mert nincs korlátozva a létrehozható [meghívások](authentication-authorization.md) száma, és egyes felhasználók soha nem fogadják el a meghívót.|
| `Unauthorized_Unknown` | 401 | Ismeretlen hiba történt a felhasználó hitelesítése közben. Ennek a hibának az egyik oka az lehet, hogy a felhasználó nem ismerhető fel, mert nem engedélyezte az alkalmazáshoz való hozzájárulásukat.|

## <a name="custom-mime-types"></a>Egyéni MIME-típusok

> [!IMPORTANT]
> A fájl *routes.jsban* definiált funkció már elavult, és jobban megvalósítható az Azure statikus Web Apps [konfigurációs fájljában](./configuration.md#routes).

A `mimeTypes` tömbvel megegyező szinten felsorolt objektum `routes` lehetővé teszi a [MIME-típusok](https://developer.mozilla.org/docs/Web/HTTP/Basics_of_HTTP/MIME_types/Common_types) kiterjesztésekkel való társítását.

```json
{
    "routes": [],
    "mimeTypes": {
        "custom": "text/html"
    }
}
```

A fenti példában a kiterjesztéssel rendelkező összes fájl `.custom` a MIME-típussal lesz kézbesítve `text/html` .

A következő szempontok fontosak a MIME-típusok használatakor:

- A kulcsok értéke nem lehet null vagy üres, vagy 50 karakternél hosszabb.
- Az értékek nem lehetnek null értékűek vagy üresek, vagy több mint 1000 karakterből állhatnak.

> [!NOTE]
> A statikus Web Apps megérti a Blazer-alkalmazásokat és a WASM és a DLL-fájlok várható MIME-típusait, ezért nem szükséges leképezéseket hozzáadni ezekhez.

## <a name="default-headers"></a>Alapértelmezett fejlécek

> [!IMPORTANT]
> A fájl *routes.jsban* definiált funkció már elavult, és jobban megvalósítható az Azure statikus Web Apps [konfigurációs fájljában](./configuration.md#routes).

A `defaultHeaders` tömbvel megegyező szinten felsorolt objektum `routes` lehetővé teszi a [Válasz fejlécek](https://developer.mozilla.org/docs/Web/HTTP/Headers)hozzáadását, módosítását és eltávolítását.

Egy fejléc értékének megadása vagy módosítása a fejlécet adja meg vagy módosítja. Ha üres értéket ad meg, azzal eltávolítja a fejlécet az ügyfélnek.

```json
{
    "routes": [],
    "defaultHeaders": {
      "content-security-policy": "default-src https: 'unsafe-eval' 'unsafe-inline'; object-src 'none'",
      "cache-control": "must-revalidate, max-age=6000",
      "x-dns-prefetch-control": ""
    }
}
```

A fenti példában egy új fejléc kerül `content-security-policy` hozzáadásra, a `cache-control` módosítja a kiszolgáló alapértelmezett értékét, és a `x-dns-prefetch-control` fejléc el lesz távolítva.

A következő szempontok fontosak a fejlécek használatakor:

- A kulcsok nem lehetnek null értékűek vagy üresek.
- Null vagy üres érték esetén a rendszer eltávolítja a fejlécet a feldolgozásból.
- A kulcsok vagy az értékek nem haladhatják meg a 8 000 karaktert.
- A definiált fejlécek minden kérelemmel együtt lesznek kézbesítve.
- A _routes.jsban_ definiált fejlécek csak statikus tartalomra vonatkoznak. A függvény kódjában testreszabhatja az API-végpontok válaszának fejléceit.

## <a name="example-route-file"></a>Példa az útválasztási fájlra

> [!IMPORTANT]
> A fájl *routes.jsban* definiált funkció már elavult, és jobban megvalósítható az Azure statikus Web Apps [konfigurációs fájljában](./configuration.md#routes).

Az alábbi példa bemutatja, hogyan hozhat létre a statikus tartalomra és API-kra vonatkozó útválasztási szabályokat a fájl _routes.js_ . Egyes útvonalak a [ _/.auth_ rendszermappáját](authentication-authorization.md) használják, amely hozzáfér a hitelesítéssel kapcsolatos végpontokhoz.

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
      "route": "/api/admin",
      "allowedRoles": ["administrator"]
    },
    {
      "route": "/customers/contoso",
      "allowedRoles": ["administrator", "customers_contoso"]
    },
    {
      "route": "/login",
      "serve": "/.auth/login/github"
    },
    {
      "route": "/.auth/login/twitter",
      "statusCode": "404"
    },
    {
      "route": "/logout",
      "serve": "/.auth/logout"
    },
    {
      "route": "/calendar/*",
      "serve": "/calendar.html"
    },
    {
      "route": "/specials",
      "serve": "/deals",
      "statusCode": 301
    }
  ],
  "platformErrorOverrides": [
    {
      "errorType": "NotFound",
      "serve": "/custom-404.html"
    },
    {
      "errorType": "Unauthenticated",
      "statusCode": "302",
      "serve": "/login"
    }
  ],
  "defaultHeaders": {
    "content-security-policy": "default-src https: 'unsafe-eval' 'unsafe-inline'; object-src 'none'"
  },
  "mimeTypes": {
      "custom": "text/html"
  }
}
```

Az alábbi példák azt írják le, mi történik, ha egy kérelem megfelel egy szabálynak.

| Kérelmek... | Eredmény... |
|--|--|--|
| _/Profile_ | A hitelesített felhasználók kiszolgálják a _/profile/index.html_ fájlt. Nem hitelesített felhasználók átirányítva a _belépési_ értékre. |
| _/admin/reports_ | A _rendszergazdák_ szerepkörben lévő hitelesített felhasználók a _/Admin/Reports/index.html_ fájlban lesznek kézbesítve. A _rendszergazdák_ szerepkörben nem szereplő hitelesített felhasználók a <sup>2</sup>. 401 hibát szolgálják fel. Nem hitelesített felhasználók átirányítva a _belépési_ értékre. |
| _/api/admin_ | A _rendszergazdák_ szerepkörbe tartozó hitelesített felhasználóktól érkező kéréseket a rendszer ELKÜLDI az API-nak. A _rendszergazdák_ szerepkörbe nem tartozó hitelesített felhasználók és a nem hitelesített felhasználók 401-es hibát szolgáltatnak. |
| _/customers/contoso_ | Azok a hitelesített felhasználók, akik a _rendszergazdákhoz_ vagy az _ügyfelekhez tartozó \_ contoso_ -szerepkörökhöz tartoznak, a _/Customers/contoso/index.html_ <sup>2</sup>. fájlba kerülnek. A _rendszergazdák_ vagy az _ügyfelek \_ contoso_ szerepköreiben nem szereplő hitelesített felhasználók 401-es hibát szolgáltatnak. Nem hitelesített felhasználók átirányítva a _belépési_ értékre. |
| _/Belépés View_ | A nem hitelesített felhasználók a GitHub használatával hitelesíthetők. |
| _/.auth/login/twitter_ | A Twitter-engedélyezés le van tiltva. A kiszolgáló 404-as hibával válaszol. |
| _/logout_ | A felhasználók ki vannak jelentkezve bármely hitelesítési szolgáltatóból. |
| _/calendar/2020/01_ | A böngésző a _/calendar.html_ fájlt kézbesíti. |
| _/specials_ | A böngésző átirányítja a _/Deals_. |
| _/unknown-folder_ | A _/custom-404.html_ fájl kézbesítve. |
| `.custom`Kiterjesztésű fájlok | A `text/html` MIME-típussal szolgál |

Az összes válasz tartalmazza a `content-security-policy` fejléceket, amelyeknek értéke `default-src https: 'unsafe-eval' 'unsafe-inline'; object-src 'none'` .

<sup>1</sup> az API functions esetében az útválasztási szabályok csak az [átirányításokat](#redirects) támogatják, és [a szerepkörökkel biztosítják az útvonalak védelmét](#securing-routes-with-roles).

<sup>2</sup> egyéni hibaüzenetet is megadhat egy szabály definiálásával `Unauthorized_MissingRoles` a `platformErrorOverrides` tömbben.

## <a name="restrictions"></a>Korlátozások

- A fájl _routes.jsa_ nem lehet nagyobb, mint 100 kb
- A fájl _routes.js_ legfeljebb 50 különböző szerepkört támogat

Az általános korlátozásokról és korlátozásokról a [kvóták című cikkben](quotas.md) olvashat.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Hitelesítés és engedélyezés beállítása](authentication-authorization.md)
