---
title: A felhasználói felület testreszabása HTML-sablonokkal
titleSuffix: Azure AD B2C
description: Megtudhatja, hogyan szabhatja testre a felhasználói felületet a Azure Active Directory B2C-t használó alkalmazásaihoz tartozó HTML-sablonokkal.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/16/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: e694a5f6144cee65be074d05ce0015d31bfdf65e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104675825"
---
# <a name="customize-the-user-interface-with-html-templates-in-azure-active-directory-b2c"></a>A felhasználói felület testreszabása HTML-sablonokkal Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

A Azure Active Directory B2C (Azure AD B2C) felhasználói felületének védjegyezése és testreszabása az ügyfelek számára a zökkenőmentes felhasználói élményt nyújtja az alkalmazásában. Ezek a tapasztalatok közé tartozik a regisztráció, a bejelentkezés, a profil szerkesztése és a jelszó-visszaállítás. Ez a cikk bemutatja a felhasználói felület (UI) testreszabásának módszereit. 

> [!TIP]
> Ha csak a szalagcím emblémáját, háttérképét és háttérszínét szeretné módosítani a felhasználói folyamatok oldalain, kipróbálhatja a [vállalati védjegyezés](customize-ui.md) funkciót.

## <a name="custom-html-and-css-overview"></a>Egyéni HTML és CSS – áttekintés

A Azure AD B2C a kódot az ügyfél böngészőjében futtathatja, ha az [erőforrás-megosztást (CORS)](https://www.w3.org/TR/cors/)használja. Futásidőben a tartalom betöltődik a felhasználói folyamat vagy az egyéni házirendben megadott URL-címről. A felhasználói felület minden lapja betölti a tartalmat az adott laphoz megadott URL-címről. Miután betöltötte a tartalmat az URL-címről, a rendszer egyesít egy, a Azure AD B2C által beszúrt HTML-kódrészlettel, majd megjeleníti a lapot az ügyfél számára.

![Egyéni oldal tartalmának margója](./media/customize-ui-with-html/html-content-merging.png)

### <a name="custom-html-page-content"></a>Egyéni HTML-oldal tartalma

Hozzon létre egy HTML-oldalt saját arculatával az egyéni oldal tartalmának kiszolgálásához. Ez a lap lehet statikus `*.html` oldal vagy dinamikus oldal, például .net, Node.js vagy php.

Az egyéni oldal tartalma bármilyen HTML-elemet tartalmazhat, beleértve a CSS-t és a JavaScriptet is, de nem tartalmazhat olyan nem biztonságos elemeket, mint az iframe elemek. Az egyetlen szükséges elem egy div-elem, amely a `id` következőre van beállítva: `api` , például ez `<div id="api"></div>` a html-oldalon.

```html
<!DOCTYPE html>
<html>
<head>
    <title>My Product Brand Name</title>
</head>
<body>
    <div id="api"></div>
</body>
</html>
```

#### <a name="customize-the-default-azure-ad-b2c-pages"></a>Az alapértelmezett Azure AD B2C lapok testreszabása

Ahelyett, hogy az egyéni oldal tartalmait a semmiből hozza létre, testreszabhatja az Azure AD B2C's alapértelmezett oldalának tartalmát.

A következő táblázat felsorolja a Azure AD B2C által biztosított alapértelmezett oldal tartalmát. Töltse le a fájlokat, és használja kiindulási pontként a saját egyéni oldalai létrehozásához.

| Alapértelmezett lap | Leírás | Tartalom definíciójának azonosítója<br/>(csak egyéni házirend) |
|:-----------------------|:--------|-------------|
| [exception.html](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **Hiba lap**. Ez az oldal akkor jelenik meg, ha kivételt vagy hibát észlel. | *API. error* |
| [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) |  **Saját maga által érvényesített oldal**. Ezt a fájlt egyéni oldalként használhatja a közösségi fiók regisztrálása lap, a helyi fiók regisztrálása oldal, a helyi fiók bejelentkezési oldala, a jelszó alaphelyzetbe állítása és egyebek. Az űrlap különböző beviteli vezérlőket tartalmazhat, például a következőket: szövegbeviteli mező, jelszó-beviteli mező, választógomb, egyszeres kijelölés legördülő lista és többszörös kijelölés jelölőnégyzet. | *API. localaccountsignin*, API. *localaccountsignup*, *API. localaccountpasswordreset*, *API. selfasserted* |
| [multifactor-1.0.0.html](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Multi-Factor Authentication oldal**. Ezen a lapon a felhasználók megtekinthetik a telefonszámokat (szöveg vagy hang használatával) a regisztráció vagy a bejelentkezés során. | *API. phonefactor* |
| [updateprofile.html](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Profil frissítése lap**. Ez a lap egy űrlapot tartalmaz, amelyet a felhasználók frissíthetnek a profiljuk frissítéséhez. Ez az oldal hasonló a közösségi fiók regisztrációs oldalához, a jelszó-beviteli mezők kivételével. | *API. selfasserted. profileUpdate* |
| [unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **Egyesített regisztrációs vagy bejelentkezési oldal**. Ez a lap kezeli a felhasználói regisztrációt és a bejelentkezési folyamatot. A felhasználók a vállalati identitás-szolgáltatók, a közösségi identitás-szolgáltatók, például a Facebook vagy a Google +, vagy a helyi fiókok használatával használhatják a felhasználókat. | *API. signuporsignin* |

## <a name="hosting-the-page-content"></a>Az oldal tartalmának üzemeltetése

Ha saját HTML-és CSS-fájlokat használ a felhasználói felület testreszabásához, a felhasználói felület tartalmát a CORS támogató bármely nyilvánosan elérhető HTTPS-végponton üzemeltetheti. Például: [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md), [Azure app Services](../app-service/index.yml), webkiszolgálók, CDNs, AWS S3 vagy fájlmegosztó rendszerek.

## <a name="guidelines-for-using-custom-page-content"></a>Irányelvek egyéni lapok tartalmának használatához

- Használjon abszolút URL-címet, ha olyan külső erőforrásokat is tartalmaz, mint például a média, a CSS és a JavaScript-fájlok a HTML-fájlban.
- Ha a 1.2.0 vagy újabb [verzióját](page-layout.md) használja, a `data-preload="true"` HTML-címkékben lévő attribútumot hozzáadhatja a CSS és a JavaScript betöltési sorrendjének szabályozásához. A `data-preload="true"` -ben az oldal a felhasználó előtt jön létre. Ez az attribútum segít megakadályozni, hogy az oldal "vibrál" legyen a CSS-fájl előzetes betöltésével anélkül, hogy a felhasználóhoz nem tartozó HTML-fájlt kellene megjeleníteni. A következő HTML-kódrészlet a címke használatát mutatja be `data-preload` .
  ```HTML
  <link href="https://path-to-your-file/sample.css" rel="stylesheet" type="text/css" data-preload="true"/>
  ```
- Javasoljuk, hogy kezdje az alapértelmezett oldal tartalmával, és készítsen rá rá.
- Az egyéni tartalomban [JavaScripteket](javascript-and-page-layout.md) is hozzáadhat.
- A támogatott böngésző-verziók a következők:
  - Internet Explorer 11, 10 és Microsoft Edge
  - Korlátozott támogatás az Internet Explorer 9 és 8 rendszerhez
  - Google Chrome 42,0 és újabb verziók
  - Mozilla Firefox 38,0 és újabb verziók
  - Safari iOS és macOS rendszerekhez, 12-es vagy újabb verzió
- Biztonsági korlátozások miatt Azure AD B2C nem támogatja `frame` , `iframe` vagy `form` HTML-elemeket.

## <a name="localize-content"></a>Tartalom honosítása

A HTML-tartalmat honosíthatja a Azure AD B2C bérlő [nyelvi testreszabásainak](language-customization.md) engedélyezésével. A funkció engedélyezése lehetővé teszi, hogy Azure AD B2C az OpenID Connect paramétert továbbítsa a `ui_locales` végpontnak. A Content Server ezt a paramétert használhatja a nyelvspecifikus HTML-lapok megadásához.

A tartalmat a használt területi beállítás alapján különböző helyekről lehet kihúzni. A CORS-kompatibilis végponton beállíthatja, hogy a rendszer egy adott nyelvhez tartozó tartalmat működtessen. Ha a helyettesítő karaktert használja, hívja meg a megfelelőt `{Culture:RFC5646}` .

Az egyéni oldal URI-ja például az alábbihoz hasonló lehet:

```http
https://contoso.blob.core.windows.net/{Culture:RFC5646}/myHTML/unified.html
```

A lapot francia nyelven is betöltheti, ha tartalmat húz a következő helyről:

```http
https://contoso.blob.core.windows.net/fr/myHTML/unified.html
```

## <a name="custom-page-content-walkthrough"></a>Egyéni oldal tartalmi útmutatója

A folyamat áttekintése:

1. Készítse elő az egyéni oldal tartalmának (nyilvánosan elérhető, CORS-kompatibilis HTTPS-végpont) üzemeltetésére szolgáló helyet.
1. Töltse le és szabja testre az alapértelmezett oldal tartalmi fájlját, például: `unified.html` .
1. Közzéteheti az egyéni oldal tartalmát a nyilvánosan elérhető HTTPS-végponton.
1. Állítsa be a CORS a webalkalmazáshoz.
1. Irányítsa a szabályzatot az egyéni házirend-tartalom URI-ra.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

### <a name="1-create-your-html-content"></a>1. HTML-tartalom létrehozása

Hozzon létre egy egyéni oldal tartalmát a termék márkaneve nevével a címben.

1. Másolja a következő HTML-kódrészletet. Jól formázott HTML5 egy üres elemmel, amelynek neve *\<div id="api"\>\</div\>* a címkék között található *\<body\>* . Ez az elem azt jelzi, hogy hol kell beszúrni Azure AD B2C tartalmat.

   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <title>My Product Brand Name</title>
   </head>
   <body>
       <div id="api"></div>
   </body>
   </html>
   ```

1. A másolt kódrészlet beillesztése egy szövegszerkesztőbe
1. A CSS használatával a Azure AD B2C beszúrható felhasználói felületi elemeket alakíthatja át az oldalára. Az alábbi példa egy egyszerű CSS-fájlt mutat be, amely a regisztrációs beszúrt HTML-elemek beállításait is tartalmazza:

    ```css
    h1 {
      color: blue;
      text-align: center;
    }
    .intro h2 {
      text-align: center;
    }
    .entry {
      width: 400px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    .divider h2 {
      text-align: center;
    }
    .create {
      width: 400px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    ```

1.  Mentse a fájlt *customize-ui.html* néven.

> [!NOTE]
> A HTML-űrlap elemei a biztonsági korlátozások miatt törlődnek, ha login.microsoftonline.com használ. Ha HTML-űrlap elemeket szeretne használni az egyéni HTML-tartalomban, [használja a b2clogin.com](b2clogin.md).

### <a name="2-create-an-azure-blob-storage-account"></a>2. Azure Blob Storage-fiók létrehozása

Ebben a cikkben az Azure Blob Storage-t használjuk a tartalom üzemeltetéséhez. Dönthet úgy, hogy webkiszolgálón üzemelteti a tartalmat, de engedélyeznie kell a [CORS a webkiszolgálón](https://enable-cors.org/server.html).

A HTML-tartalom blob Storage-ban való üzemeltetéséhez hajtsa végre a következő lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A **központi** menüben válassza az **új**  >  **Storage**  >  **Storage-fiók** lehetőséget.
1. Válasszon egy **előfizetést** a Storage-fiókjához.
1. Hozzon létre egy **erőforráscsoportot** , vagy válasszon ki egy meglévőt.
1. Adja meg a Storage-fiók egyedi **nevét** .
1. Válassza ki a tárolási fiók **földrajzi helyét** .
1. A **telepítési modell** maradhat a **Resource Managerben**.
1. A **teljesítmény** továbbra is **standard** maradhat.
1. Változtassa meg a **fiók típusát** a **blob Storage**-ban.
1. A **replikáció** az **ra-GRS** is maradhat.
1. A **hozzáférési szint** is **megmaradhat**.
1. Válassza a **felülvizsgálat + létrehozás** lehetőséget a Storage-fiók létrehozásához.
    A telepítés befejezése után a Storage- **fiók** lap automatikusan megnyílik.

#### <a name="21-create-a-container"></a>2,1 tároló létrehozása

Ha nyilvános tárolót szeretne létrehozni a blob Storage-ban, hajtsa végre a következő lépéseket:

1. A bal oldali menüben a **blob Service** alatt válassza a **Blobok** elemet.
1. Válassza a **+ tároló** elemet.
1. A **név** mezőbe írja be a *root* értéket. A név lehet a választott név, például a *contoso*, de ebben a példában az egyszerűség kedvéért használjuk a *root* -t.
1. **Nyilvános hozzáférési szint** esetén válassza a **blob** lehetőséget, majd **az OK gombot**.
1. Válassza ki a **root** elemet az új tároló megnyitásához.

#### <a name="22-upload-your-custom-page-content-files"></a>2,2 az egyéni oldal tartalmi fájljainak feltöltése

1. Válassza a **Feltöltés** lehetőséget.
1. Válassza ki a **fájl kiválasztása** melletti mappa ikont.
1. Navigáljon a lapra, és válassza ki **customize-ui.html**, amelyet korábban hozott létre az oldal felhasználói felületének testreszabása szakaszban.
1. Ha egy almappában szeretne feltölteni, bontsa ki a **speciális** elemet, és adja meg a mappa nevét a **feltöltés mappába**.
1. Válassza a **Feltöltés** lehetőséget.
1. Válassza ki a feltöltött **customize-ui.html** blobot.
1. Az **URL-cím** szövegmező jobb oldalán válassza a **Másolás vágólapra** ikont az URL-cím vágólapra másolásához.
1. A böngészőben nyissa meg a vágólapra másolt URL-címet, és ellenőrizze, hogy a feltöltött blob elérhető-e. Ha nem érhető el, például ha `ResourceNotFound` hibát tapasztal, győződjön meg arról, hogy a tároló hozzáférési típusa **blob**.

### <a name="3-configure-cors"></a>3. a CORS konfigurálása

Az alábbi lépések végrehajtásával konfigurálja a blob Storage-t az idegen eredetű erőforrás-megosztáshoz:

1. A menüben válassza a **CORS** lehetőséget.
1. Az **engedélyezett eredetek** mezőben adja meg a következőt: `https://your-tenant-name.b2clogin.com` . Cserélje le a helyére `your-tenant-name` a Azure ad B2C bérlő nevét. Például: `https://fabrikam.b2clogin.com`. A bérlő nevének megadásakor használja az összes kisbetűt.
1. Az **engedélyezett módszerek** esetében válassza a mindkettő és a lehetőséget `GET` `OPTIONS` .
1. Az **engedélyezett fejlécek** mezőbe írjon be egy csillagot (*).
1. A közzétett **fejlécek** esetében írjon be egy csillagot (*).
1. A **Max Age** értéknél adja meg a 200 értéket.
1. Kattintson a **Mentés** gombra.

#### <a name="31-test-cors"></a>3,1 teszt CORS

Ellenőrizze, hogy készen áll-e a következő lépések végrehajtásával:

1. Ismételje meg a CORS konfigurálása lépést. Az **engedélyezett eredetek** mezőben adja meg a következőt: `https://www.test-cors.org`
1. Navigáljon a [www.test-CORS.org](https://www.test-cors.org/) 
1. A **távoli URL-cím** mezőben illessze be a HTML-fájl URL-címét. Például: `https://your-account.blob.core.windows.net/root/azure-ad-b2c/unified.html`
1. Válassza a **küldési kérelem** lehetőséget.
    Ennek az eredménynek kell lennie `XHR status: 200` . 
    Ha hibaüzenetet kap, ellenőrizze, hogy helyesek-e a CORS beállításai. Előfordulhat, hogy törölnie kell a böngésző gyorsítótárát, vagy meg kell nyitnia egy privát böngészési munkamenetet a CTRL + SHIFT + P billentyűkombináció lenyomásával.

::: zone pivot="b2c-user-flow"

### <a name="4-update-the-user-flow"></a>4. a felhasználói folyamat frissítése

1. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Azure ad B2C**.
1. Válassza a **felhasználói folyamatok** lehetőséget, majd válassza ki a *B2C_1_signupsignin1* felhasználói folyamatot.
1. Válassza **ki a lapelrendezések lehetőséget**, majd **az egyesített regisztráció vagy a bejelentkezés lapon** kattintson az **Igen** lehetőségre az **Egyéni oldal tartalmának használatához**.
1. Az **Egyéni lap URI-ja** mezőben adja meg a korábban feljegyzett *custom-ui.html* fájl URI azonosítóját.
1. A lap tetején válassza a **Mentés** lehetőséget.

### <a name="5-test-the-user-flow"></a>5. a felhasználói folyamat tesztelése

1. A Azure AD B2C-bérlőben válassza a **felhasználói folyamatok** lehetőséget, és válassza ki a *B2C_1_signupsignin1* felhasználói folyamatot.
1. A lap tetején kattintson a **felhasználói folyamat futtatása** elemre.
1. Kattintson a **felhasználói folyamat futtatása** gombra.

A létrehozott CSS-fájl alapján a következő példához hasonló oldalnak kell megjelennie:

![Webböngészőt, amely az egyéni felhasználói felületi elemekkel való regisztrációt vagy bejelentkezési oldalt jeleníti meg](./media/customize-ui-with-html/run-now.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

### <a name="4-modify-the-extensions-file"></a>4. a bővítmények fájljának módosítása

A felhasználói felület testreszabásának konfigurálásához másolja a **ContentDefinition** és annak alárendelt elemeit az alapfájlból a kiterjesztések fájlba.

1. Nyissa meg a szabályzat alapfájlját. Például: <em>`SocialAndLocalAccounts/`**`TrustFrameworkBase.xml`**</em> . Ez az alapfájl az egyéni házirend alapszintű csomagban található egyik házirend-fájl, amelyet az előfeltételben kell megszereznie az [Egyéni szabályzatok használatának első lépéseiben](./custom-policy-get-started.md).
1. Keresse meg és másolja a **ContentDefinitions** elem teljes tartalmát.
1. Nyissa meg a kiterjesztési fájlt. Például *TrustFrameworkExtensions.xml*. Keresse meg a **BuildingBlocks** elemet. Ha az elem nem létezik, adja hozzá.
1. Illessze be a **ContentDefinitions** elem teljes tartalmát, amelyet a **BuildingBlocks** elem gyermekeiként másolt.
1. Keresse meg a  `Id="api.signuporsignin"` másolt XML-fájlban található ContentDefinition elemet.
1. Módosítsa a **tartalomdefinícióban** értékét a Storage-ba feltöltött HTML-fájl URL-címére. Például: `https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html`.

    Az egyéni szabályzatnak a következő kódrészlethez hasonlóan kell kinéznie:

    ```xml
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsignin">
          <LoadUri>https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html</LoadUri>
          <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0</DataUri>
          <Metadata>
            <Item Key="DisplayName">Signin and Signup</Item>
          </Metadata>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>
    ```

1. Mentse a bővítmények fájlt.

### <a name="5-upload-and-test-your-updated-custom-policy"></a>5. a frissített egyéni szabályzat feltöltése és tesztelése

#### <a name="51-upload-the-custom-policy"></a>5,1 az egyéni szabályzat feltöltése

1. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja, majd a felső menüben válassza ki a **címtár + előfizetés** szűrőt, és válassza ki a bérlőt tartalmazó könyvtárat.
1. Keresse meg és válassza ki a **Azure ad B2C**.
1. A **szabályzatok** területen válassza az **identitási élmény keretrendszere** elemet.
1. Válassza az **egyéni házirend feltöltése** lehetőséget.
1. Töltse fel a korábban módosított kiterjesztéseket tartalmazó fájlt.

#### <a name="52-test-the-custom-policy-by-using-run-now"></a>5,2 az egyéni házirend tesztelése a **Futtatás most** használatával

1. Válassza ki a feltöltött szabályzatot, majd válassza a **Futtatás most** lehetőséget.
1. Regisztrálnia kell egy e-mail-cím használatával.

## <a name="configure-dynamic-custom-page-content-uri"></a>Dinamikus egyéni oldal tartalmi URI-ja konfigurálása

Azure AD B2C egyéni szabályzatok használatával az URL-címen vagy egy lekérdezési karakterláncban is elküldheti a paramétereket. A paraméterek a HTML-végpontnak való átadásával dinamikusan változtathatja az oldal tartalmát. Például megváltoztathatja a háttérképet az Azure AD B2C regisztrációs vagy bejelentkezési oldalán egy olyan paraméter alapján, amelyet a web- vagy mobilalkalmazásából ad át. A paraméter lehet bármely [jogcím-feloldó](claim-resolver-overview.md), például az alkalmazás azonosítója, a nyelvi azonosító vagy az egyéni lekérdezési karakterlánc paraméter, például: `campaignId` .

### <a name="sending-query-string-parameters"></a>Lekérdezési karakterlánc paramétereinek küldése

Lekérdezési karakterlánc paramétereinek küldéséhez a [függő entitás házirendjében](relyingparty.md)adjon hozzá egy `ContentDefinitionParameters` elemet az alább látható módon.

```xml
<RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <UserJourneyBehaviors>
    <ContentDefinitionParameters>
        <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
        <Parameter Name="lang">{Culture:LanguageName}</Parameter>
        <Parameter Name="appId">{OIDC:ClientId}</Parameter>
    </ContentDefinitionParameters>
    </UserJourneyBehaviors>
    ...
</RelyingParty>
```

A tartalom definíciójában módosítsa a értékét a következőre: `LoadUri` `https://<app_name>.azurewebsites.net/home/unified` . Az egyéni szabályzatnak `ContentDefinition` a következő kódrészlethez hasonlóan kell kinéznie:

```xml
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://<app_name>.azurewebsites.net/home/unified</LoadUri>
  ...
</ContentDefinition>
```

Amikor Azure AD B2C betölti a lapot, meghívja a webkiszolgáló-végpontot:

```http
https://<app_name>.azurewebsites.net/home/unified?campaignId=123&lang=fr&appId=f893d6d3-3b6d-480d-a330-1707bf80ebea
```

### <a name="dynamic-page-content-uri"></a>Dinamikus oldal tartalmi URI-ja

A tartalom a használt paraméterek alapján különböző helyekről is kihúzható. A CORS-kompatibilis végponton állítsa be a mappa szerkezetét a tartalom tárolására. Például a következő struktúrában rendezheti a tartalmakat. Gyökérmappa */mappa/nyelv/a HTML-fájlok*. Az egyéni oldal URI-ja például az alábbihoz hasonló lehet:

```xml
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://contoso.blob.core.windows.net/{Culture:LanguageName}/myHTML/unified.html</LoadUri>
  ...
</ContentDefinition>
```

A Azure AD B2C a francia nyelvhez a két betűs ISO-kódot küldi el `fr` :

```http
https://contoso.blob.core.windows.net/fr/myHTML/unified.html
```

::: zone-end

## <a name="sample-templates"></a>Példasablonok

A felhasználói felület testreszabásához itt talál példákat:

```bash
git clone https://github.com/azure-ad-b2c/html-templates
```

Ez a projekt a következő sablonokat tartalmazza:
- [Ocean Blue](https://github.com/azure-ad-b2c/html-templates/tree/main/templates/AzureBlue)
- [Szürke pala](https://github.com/azure-ad-b2c/html-templates/tree/main/templates/MSA)
- [Klasszikus](https://github.com/azure-ad-b2c/html-templates/tree/main/templates/classic)
- [Sablonerőforrások](https://github.com/azure-ad-b2c/html-templates/tree/main/templates/src)

A minta használata:

1. A tárház klónozása a helyi gépen. Válasszon egy sablon mappát `/AzureBlue` , `/MSA` vagy `/classic` .
1. Töltse fel a sablon mappájában és a mappában található összes fájlt a `/src` blob Storage-ba az előző szakaszokban leírtak szerint.
1. Ezután nyissa meg `\*.html` az egyes fájlokat a sablon mappájába. Ezután cserélje le az URL-címek összes példányát `https://login.microsoftonline.com` a 2. lépésben feltöltött URL-címre. Például:
    
    Forrás:
    ```html
    https://login.microsoftonline.com/templates/src/fonts/segoeui.WOFF
    ```

    Címzett:
    ```html
    https://your-storage-account.blob.core.windows.net/your-container/templates/src/fonts/segoeui.WOFF
    ```
    
1. Mentse a `\*.html` fájlokat, és töltse fel őket a blob Storage-ba.
1. Most módosítsa a szabályzatot, amely a korábban említett HTML-fájlra mutat.
1. Ha a hiányzó betűkészleteket, képeket vagy CSS-ket látja, tekintse át a hivatkozásokat a kiterjesztések és a \* . HTML fájlok között.

## <a name="use-company-branding-assets-in-custom-html"></a>Vállalati arculati eszközök használata egyéni HTML-kódban

Ha egyéni HTML-kódban szeretné használni a [vállalati védjegyezési](customize-ui.md#configure-company-branding) eszközöket, adja hozzá a következő címkéket a `<div id="api">` címkén kívül. A képforrást a háttérkép és a szalagcím emblémája helyettesíti.

```HTML
<img data-tenant-branding-background="true" />
<img data-tenant-branding-logo="true" alt="Company Logo" />
```

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan engedélyezheti az [ügyféloldali JavaScript-kódokat](javascript-and-page-layout.md).
