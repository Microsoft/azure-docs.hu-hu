---
title: 'Gyors útmutató: felhasználói bejelentkezés hozzáadása egy Node.js webalkalmazáshoz | Azure'
titleSuffix: Microsoft identity platform
description: Ebből a rövid útmutatóból megtudhatja, hogyan valósítható meg a hitelesítés egy Node.js webalkalmazásban az OpenID Connect használatával.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/28/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET, devx-track-js
ms.openlocfilehash: fec130ff02d9796fb9a6cea7a831bbdcb25cb1ed
ms.sourcegitcommit: 2dd0932ba9925b6d8e3be34822cc389cade21b0d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2021
ms.locfileid: "99225630"
---
# <a name="quickstart-add-sign-in-using-openid-connect-to-a-nodejs-web-app"></a>Rövid útmutató: bejelentkezés hozzáadása az OpenID használatával Node.js webalkalmazáshoz

Ebben a rövid útmutatóban egy Code-mintát tölt le és futtat, amely bemutatja, hogyan állítható be az OpenID Connect-hitelesítés a Node.js Express használatával létrehozott webalkalmazásokban. A minta úgy van kialakítva, hogy bármilyen platformon fusson.

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/en/download/).

## <a name="register-your-application"></a>Az alkalmazás regisztrálása

1. Jelentkezzen be a <a href="https://portal.azure.com/" target="_blank">Azure Portalba <span class="docon docon-navigate-external x-hidden-focus"></span> </a>.
1. Ha több bérlőhöz fér hozzá, a felső menüben a **könyvtár + előfizetés** szűrő használatával :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: válassza ki azt a bérlőt, amelyben regisztrálni kíván egy alkalmazást.
1. Keresse meg és válassza ki az **Azure Active Directoryt**.
1. A **kezelés** területen válassza a **Alkalmazásregisztrációk**  >  **új regisztráció** lehetőséget.
1. Adja meg az alkalmazás **nevét** , például: `MyWebApp` . Előfordulhat, hogy az alkalmazás felhasználói láthatják ezt a nevet, és később is megváltoztathatók.
1. A **támogatott fióktípus** szakaszban válassza a fiókok lehetőséget a **szervezeti címtárban és a személyes Microsoft-fiókokban (például Skype, Xbox, Outlook.com)**.

    Ha egynél több átirányítási URI van, akkor az alkalmazás sikeres létrehozása után később adja hozzá ezeket a **hitelesítés** lapról.

1. Az alkalmazás létrehozásához válassza a **regisztráció** lehetőséget.
1. Az alkalmazás **Áttekintés** lapján keresse meg az **alkalmazás (ügyfél) azonosító** értékét, és jegyezze fel később. Erre az értékre szüksége lesz ahhoz, hogy az alkalmazást később konfigurálja a projektben.
1. A **kezelés** területen válassza a **hitelesítés** lehetőséget.
1. Válassza **a platform**  >  **web** hozzáadása lehetőséget. 
1. Az **átirányítási URI** -k szakaszban adja meg a értéket `http://localhost:3000/auth/openid/return` .
1. Adja meg az **előtérben kijelentkezési URL-címet** `https://localhost:3000` .
1. Az **implicit engedélyezési és hibrid folyamatok** szakaszban válassza az **azonosító tokenek** lehetőséget, mivel ez a minta megköveteli, hogy az [implicit engedélyezési folyamat](./v2-oauth2-implicit-grant-flow.md) engedélyezze a bejelentkezést a felhasználó számára.
1. Válassza a **Konfigurálás** lehetőséget.
1. A **kezelés** területen válassza a **tanúsítványok & titkos kulcsok**  >  **új ügyfél titka** lehetőséget.
1. Adja meg a kulcs leírását (például az alkalmazás titkos kulcsaként).
1. Válassza ki a kulcs időtartamát **1 év vagy 2 év között,** vagy **Soha ne járjon le**.
1. Válassza a **Hozzáadás** elemet. Ekkor megjelenik a kulcs értéke. Másolja a kulcs értékét, és mentse biztonságos helyre későbbi használatra.


## <a name="download-the-sample-application-and-modules"></a>A minta alkalmazás és modulok letöltése

Ezután a minta-tárház klónozása és a NPM-modulok telepítése.

A rendszerhéjból vagy parancssorból:

`$ git clone git@github.com:AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git`

vagy

`$ git clone https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git`

A projekt gyökérkönyvtárában futtassa a következő parancsot:

`$ npm install`

## <a name="configure-the-application"></a>Az alkalmazás konfigurálása

Adja meg a paramétereket a `exports.creds` config.js utasítás szerint.

* Frissítse a `<tenant_name>` - `exports.identityMetadata` t az Azure ad-bérlő neve \* . onmicrosoft.com formátumban.
* Frissítse `exports.clientID` az alkalmazás-regisztráció során feljegyzett alkalmazás-azonosítóval.
* Frissítse `exports.clientSecret` az alkalmazás-regisztráció során feljegyzett alkalmazási titokkal.
* Frissítse `exports.redirectUrl` az alkalmazás regisztrációja során feljegyzett átirányítási URI-val.

**Az üzemi alkalmazások opcionális konfigurációja:**

* `exports.destroySessionUrl`Ha másikat szeretne használni, config.js frissítsen `post_logout_redirect_uri` .

* `exports.useMongoDBSessionStore`Ha a [mongoDB](https://www.mongodb.com) -t vagy más [kompatibilis munkamenet-tárolókat](https://github.com/expressjs/session#compatible-session-stores)szeretne használni, állítsa be config.js értéke TRUE (igaz) értékre.
A példában szereplő alapértelmezett munkamenet-tároló `express-session` . Az alapértelmezett munkamenet-tároló nem alkalmas az éles környezetben való használatra.

* Frissítsen `exports.databaseUri` , ha a mongoDB munkamenet-áruházat és egy másik adatbázis-URI-t szeretne használni.

* Frissítés `exports.mongoDBSessionMaxAge` . Itt adhatja meg, hogy mennyi ideig szeretné megőrizni a munkamenetet a mongoDB. Az egység másodperc (ek).

## <a name="build-and-run-the-application"></a>Az alkalmazás fordítása és futtatása

Indítsa el a mongoDB szolgáltatást. Ha ebben az alkalmazásban a mongoDB munkamenet-tárolót használja, először [telepítenie](http://www.mongodb.org/) kell a mongoDB-t, és előbb el kell indítania a szolgáltatást. Ha az alapértelmezett munkamenet-tárolót használja, akkor kihagyhatja ezt a lépést.

Futtassa az alkalmazást a parancssorból a következő parancs használatával.

```
$ node app.js
```

**Nehezen érthető a kiszolgáló kimenete?:** Ennek a `bunyan` mintának a naplózására használjuk. A konzol nem fog sok értelmet adni Önnek, ha nem is telepíti a Bunyan-t, és nem futtatja a fentieket, hanem a Bunyan bináris fájlon keresztül átadja a kiszolgálót:

```
$ npm install -g bunyan

$ node app.js | bunyan
```

### <a name="youre-done"></a>Ennyi az egész!

A kiszolgáló sikeresen fut `http://localhost:3000` .

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Következő lépések
További információ a Microsoft Identity platform által támogatott Web App-forgatókönyvről:
> [!div class="nextstepaction"]
> [Felhasználói forgatókönyvet használó webalkalmazás](scenario-web-app-sign-user-overview.md)