---
title: A saját fejlesztésű fejlesztői portál tesztelése
titleSuffix: Azure API Management
description: Megtudhatja, hogyan állíthat be egységteszteket és végpontok között teszteket saját maga által üzemeltetett API Management portálon.
author: dlepow
ms.author: apimpm
ms.date: 03/25/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: ab6a7aa8fc4f11c34126415379294ef1e48fa286
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741713"
---
# <a name="test-the-self-hosted-developer-portal"></a>A saját fejlesztésű fejlesztői portál tesztelése

Ez a cikk bemutatja, hogyan állíthat be egységteszteket és végpontok között teszteket saját [maga által üzemeltetett portálhoz.](developer-portal-self-host.md)

## <a name="unit-tests"></a>Egységtesztek

Az egységtesztek kis funkciókészletek érvényesítésének megközelítését biztosítják. Ez az alkalmazás más részeitől elkülönítve történik.

### <a name="example-scenario"></a>Példaforgatókönyv

Ebben a forgatókönyvben egy jelszóbeviteli vezérlőt tesztel. Csak olyan jelszavakat fogad el, amelyek legalább a következőket tartalmazzák:

- Egy betű

- Egy szám

- Egy speciális karakter
 
A követelmények érvényesítésére vonatkozó teszt így néz ki:

```typescript
const passwordInput = new PasswordInput();

passwordInput.value = "";
expect(passwordInput.isValid).to.equal(false);

passwordInput.value = "password";
expect(passwordInput.isValid).to.equal(false);

passwordInput.value = "p@ssw0rd";
expect(passwordInput.isValid.to.equal(true);
```
 
### <a name="project-structure"></a>Projektstruktúra

Gyakori, hogy egy egységtesztet az ellenőriznie kell összetevő mellett kell tartani.

```console
component.ts
component.spec.ts
```

### <a name="mock-http-requests"></a>HTTP-kérések utánzata

Vannak olyan esetek, amikor azt várja, hogy egy összetevő HTTP-kéréseket fog tenni. Az összetevőnek megfelelően kell reagálnia a különböző típusú válaszokra. Adott HTTP-válaszok szimulálására használja a következőt: `MockHttpClient` . A projekt számos más összetevője által használt felületet `HttpClient` valósítja meg.

```typescript
const httpClient = new MockHttpClient();

httpClient.mock()
    .get("/users/jane")
    .reply(200, {
        firstName: "Jane",
        lastName: "Doe"
    });
```

## <a name="end-to-end-tests"></a>Végpontok között elvégzett tesztek

A végpontok között futtatott teszt egy adott felhasználói forgatókönyvet hajt végre, pontosan olyan lépések végrehajtásával, amelyekre a felhasználónak számítania kell. Egy olyan webalkalmazásban, mint az Azure API Management fejlesztői portál, a felhasználó végiggörgeti a tartalmat, és kiválasztja a lehetőségeket bizonyos eredmények eléréséhez. 

A felhasználói navigáció replikálása érdekében használhat böngészőmanipulációs segítő kódtárakat, például a [Puppeteert.](https://github.com/puppeteer/puppeteer) Lehetővé teszi a felhasználói műveletek szimulálása és a feltételezett forgatókönyvek automatizálását. A Puppeter emellett automatikusan képernyőképeket készít az oldalakról vagy összetevőkről a teszt bármely szakaszában. Később hasonlítsa össze őket a korábbi eredményekkel az eltérések és a lehetséges regressziók összehasonlítása érdekében.

### <a name="example-scenario"></a>Példaforgatókönyv

Ebben a forgatókönyvben érvényesítenie kell egy felhasználói bejelentkezési folyamatot. Ehhez a forgatókönyvhöz a következő lépésekre lenne szükség:

1. Nyissa meg a böngészőt, és lépjen a bejelentkezési oldalra.

1. Adja meg az e-mail-címet.

1. Adja meg a jelszót.

1. Válassza **a Bejelentkezés lehetőséget.**

1. Ellenőrizze, hogy a felhasználó át lett-e irányítva a Kezdőlapra.

1. Ellenőrizze, hogy az oldalon szerepel-e **a Profil** menüpont. Ez az egyik lehetséges jelző, amely jelzi, hogy sikeresen bejelentkezett.

A teszt automatikus futtatásához hozzon létre egy szkriptet pontosan ugyanazokkal a lépésekkel:

```typescript
// 1. Open browser and navigate to the sign-in page.
const page = await browser.newPage();
await page.goto("https://contoso.com/signin");

// 2. Enter email.
await this.page.type("#email", "john.doe@contoso.com");

// 3. Enter password.
await this.page.type("#password", "p@s$w0rd");

// 4. Click Sign-in.
await this.page.click("#signin");

// 5. Verify that user got redirected to Home page.
expect(page.url()).to.equal("https://contoso.com");

// 6. Verify that the page includes the Profile menu item.
const profileMenuItem = await this.page.$("#profile");
expect(profileMenuItem).not.equals(null);
```

> [!NOTE]
> Az olyan sztringek, mint a "#email", a "#password" és a "#signin" CSS-hez hasonló választók, amelyek az oldalon található HTML-elemeket azonosítják. További információért tekintse meg a [Selectors Level 3](https://www.w3.org/TR/selectors-3/) W3C specifikációt.

### <a name="ui-component-maps"></a>Felhasználói felület összetevő-leképezések

A felhasználói folyamatok gyakran ugyanazokon az oldalakon vagy összetevőken mennek keresztül. Jó példa erre a fő webhelymenü, amely minden oldalon megjelenik. 

Hozzon létre egy felhasználói felületi összetevőtérképet, hogy elkerülje ugyanazon választók konfigurálását és frissítését minden teszthez. Lecserélheti például az előző példában található 2–6. lépést csupán két sorra:

```typescript
const signInWidget = new SigninBasicWidget(page);
await signInWidget.signInWithBasic({ email: "...", password: "..." });
```

### <a name="test-configuration"></a>Konfiguráció tesztelése

Bizonyos forgatókönyvekhez előre létrehozott adatokra vagy konfigurációra lehet szükség. Előfordulhat például, hogy automatizálni kell a felhasználói bejelentkezést közösségimédia-fiókokkal. Nehéz gyorsan vagy egyszerűen létrehozni az adatokat.

Erre a célra hozzáadhat egy speciális konfigurációs fájlt a tesztelési forgatókönyvhöz. A tesztszk szkriptek a szükséges adatokat a fájlból is kiveszik. A build- és tesztelési folyamattól függően a tesztek lekért titkos kulcsok egy elnevezett biztonságos tárolóból.

Példa egy olyanra, amely a projekt mappájában `validate.config.json` `src` lenne tárolva.

```json
{
    "environment": "validation",
    "urls": {
        "home": "https://contoso.com",
        "signin": "https://contoso.com/signin",
        "signup": "https://contoso.com/signup/"
    },
    "signin": {
        "firstName": "John",
        "lastName": "Doe",
        "credentials": {
            "basic": {
                "email": "johndoe@contoso.com",
                "password": "< password >"
            },
            "aadB2C": {
                "email": "johndoe@contoso.com",
                "password": "< password >"
            }
        }
    },
    "signup": {
        "firstName": "John",
        "lastName": "Doe",
        "credentials": {
            "basic": {
                "email": "johndoe@contoso.com",
                "password": "< password >"
            }
        }
    }
}

```

### <a name="headless-vs-normal-tests"></a>Fej nélküli és normál tesztek

A modern böngészők, például a Chrome Microsoft Edge lehetővé teszik az automatizálás futtatását fej nélküli és normál módban is. A böngésző fej nélküli módban grafikus felhasználói felület nélkül működik. Továbbra is ugyanazt az oldalt és Document Object Model (DOM) manipulálja. A böngésző felhasználói felületére általában nincs szükség a kézbesítési folyamatokban. Ebben az esetben a tesztek fej nélküli módban való futtatása nagyszerű lehetőség.

Tesztszk szkript fejlesztésekor hasznos lehet látni, hogy pontosan mi is történik a böngészőben. Ez a megfelelő időpont a normál mód használatára.

A módok közötti váltáshoz módosítsa a `headless` beállítási lehetőséget a `constants.ts` fájlban. A projekt `tests` mappájában található:

```typescript
export const LaunchOptions = {
    headless: false
};
```

Egy másik hasznos lehetőség a `slowMo` . Szünetelteti a teszt végrehajtását az egyes műveletekkel:

```typescript
export const LaunchOptions = {
    slowMo: 200 // milliseconds
};
```

## <a name="run-tests"></a>Tesztek futtatása

Ebben a projektben két beépített módon hajtunk végre teszteket:

**npm parancs**

```console
npm run test
```

**Test Explorer**

A VS Code Test Explorer bővítménye (például [a Mocha Test Explorer)](https://marketplace.visualstudio.com/items?itemName=hbenl.vscode-mocha-test-adapter)kényelmes felhasználói felülettel rendelkezik, és lehetővé teszi tesztek automatikus futtatását a forráskód minden módosításakor:

:::image type="content" source="media/developer-portal-testing/visual-studio-code-test-explorer.png" alt-text="A Code Test Explorer Visual Studio képernyőképe":::

## <a name="next-steps"></a>Következő lépések

További információ a fejlesztői portálról:

- [Az Azure API Management fejlesztői portál áttekintése](api-management-howto-developer-portal.md)

- [A fejlesztői portál saját gazdagépe](developer-portal-self-host.md)
