---
title: A felhasználói felület testreszabása HTML-sablonokkal
titleSuffix: Azure AD B2C
description: Megtudhatja, hogyan szabhatja testre a felhasználói felületet HTML-sablonokkal a felhasználói felületet használó Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/19/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 8f9f6dc1abd08c5e53f3d44a8f6ec1b3e20786ed
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717440"
---
# <a name="customize-the-user-interface-with-html-templates-in-azure-active-directory-b2c"></a>A felhasználói felület testreszabása HTML-sablonokkal a Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Az ügyfelek számára Azure Active Directory B2C (Azure AD B2C) által látható felhasználói felület védjegyezése és testreszabása segít zökkenőmentes felhasználói élményt nyújtani az alkalmazásban. Ilyen szolgáltatás például a regisztráció, a bejelentkezés, a profilszerkesztés és a jelszó-visszaállítás. Ez a cikk bemutatja a felhasználói felület (UI) testreszabásának módszereit. 

> [!TIP]
> Ha csak a szalagcím emblémáját, a háttérképet és a felhasználói folyamatoldalak háttérszínét szeretné módosítani, kipróbálhatja a Vállalati arculat [funkciót.](customize-ui.md)

## <a name="custom-html-and-css-overview"></a>Egyéni HTML és CSS – áttekintés

Azure AD B2C kódot futtat az ügyfél böngészőjében az [Eredetközi erőforrás-megosztás (CORS) használatával.](https://www.w3.org/TR/cors/) Futásidőben a rendszer betölti a tartalmat egy, a felhasználói folyamatban vagy egyéni szabályzatban megadott URL-címről. A felhasználói felület minden oldala betölti annak tartalmát az adott oldalhoz megadott URL-címből. Miután betöltötte a tartalmat az URL-címről, a rendszer egyesíti azt egy, a Azure AD B2C által beszúrt HTML-töredéktel, majd az oldal megjelenik az ügyfél számára.

![Egyéni oldaltartalom margója](./media/customize-ui-with-html/html-content-merging.png)

### <a name="custom-html-page-content"></a>Egyéni HTML-oldal tartalma

Hozzon létre egy HTML-oldalt saját védjegyezéssel az egyéni oldal tartalmának kiszolgálására. Ez az oldal lehet statikus oldal, vagy dinamikus oldal, például `*.html` .NET, Node.js PHP.

Az egyéni oldal tartalma bármilyen HTML-elemet tartalmazhat, beleértve a CSS-t és a JavaScriptet is, de nem tartalmazhat nem biztonságos elemeket, például iframe-eket. Az egyetlen kötelező elem egy div elem, amely beállítása `id` , például ez a `api` `<div id="api"></div>` HTML-oldalon belül.

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

#### <a name="customize-the-default-azure-ad-b2c-pages"></a>Az alapértelmezett lap Azure AD B2C testreszabása

Az egyéni oldal tartalmának létrehozása helyett testreszabhatja a Azure AD B2C alapértelmezett oldaltartalmat.

A következő táblázatban a szolgáltatás által biztosított alapértelmezett oldaltartalom Azure AD B2C. Töltse le a fájlokat, és használja őket kiindulási pontként saját egyéni lapok létrehozásához.

| Alapértelmezett oldal | Description | Tartalomdefiníció azonosítója<br/>(csak egyéni szabályzatok esetén) |
|:-----------------------|:--------|-------------|
| [exception.html](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **Hibalap:**. Ez a lap kivétel vagy hiba esetén jelenik meg. | *api.error* |
| [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) |  **Önfeltételes oldal.** Ezt a fájlt egyéni oldaltartalomként használhatja közösségi fiókok bejelentkezési oldalához, helyi fiók-bejelentkezési oldalhoz, helyi fiók bejelentkezési oldalához, jelszó-visszaállításhoz stb. Az űrlap különböző beviteli vezérlőket tartalmazhat, például szövegbeviteli mezőt, jelszóbeviteli mezőt, választógombot, egyszeres kijelöléses legördülő jelölőnégyzeteket és több jelölőnégyzetet. | *api.localaccountsignin*, *api.localaccountsignup*, *api.localaccountpasswordreset*, *api.selfasserted* |
| [multifactor-1.0.0.html](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Többtényezős hitelesítés lap.** Ezen az oldalon a felhasználók ellenőrizhetik telefonszámukat (szöveg vagy hang használatával) a regisztráció vagy a bejelentkezés során. | *api.phonefactor* |
| [updateprofile.html](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Profilfrissítési oldal.** Ez az oldal egy űrlapot tartalmaz, amelyhez a felhasználók hozzáférhetnek a profiljuk frissítéséhez. Ez az oldal hasonló a közösségi fiókok bejelentkezési oldalához, kivéve a jelszóbeviteli mezőket. | *api.selfasserted.profileupdate* |
| [unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **Egyesített regisztráció vagy** bejelentkezési oldal. Ez az oldal kezeli a felhasználó regisztrációját és bejelentkezési folyamatát. A felhasználók használhatnak vállalati identitásszolgáltatókat, közösségi identitásszolgáltatókat, például a Facebookot vagy a Google+-t, vagy helyi fiókokat. | *api.signuporsignin* |

## <a name="hosting-the-page-content"></a>A lap tartalmának üzemeltetése

Ha saját HTML- és CSS-fájlokat használ a felhasználói felület testreszabásához, a felhasználói felület tartalmát bármely, a CORS-t támogató, nyilvánosan elérhető HTTPS-végponton kell elosztani. Ilyen például az [Azure Blob Storage,](../storage/blobs/storage-blobs-introduction.md)az [Azure App Services,](../app-service/index.yml)webkiszolgálók, CDN-ek, AWS S3 vagy fájlmegosztási rendszerek.

## <a name="guidelines-for-using-custom-page-content"></a>Irányelvek egyéni oldaltartalmakhoz

- Abszolút URL-címet használjon, ha külső erőforrásokat, például média-, CSS- és JavaScript-fájlokat foglal bele a HTML-fájlba.
- Az [oldalelrendezés](page-layout.md) 1.2.0-s vagy újabb verziójának használatával hozzáadhatja az attribútumot a HTML-címkékhez a CSS és a JavaScript betöltési sorrendjének `data-preload="true"` szabályozására. A `data-preload="true"` alkalmazással a lap a felhasználó számára való megjelenik előtt lesz felépítve. Ez az attribútum segít megakadályozni, hogy az oldal "őzése" a CSS-fájl előzetes betöltése nélkül, a felhasználó számára megjelenő stílus nélküli HTML-kód nélkül megjelenik. Az alábbi HTML-kódrészlet a címke használatát `data-preload` mutatja be.
  ```HTML
  <link href="https://path-to-your-file/sample.css" rel="stylesheet" type="text/css" data-preload="true"/>
  ```
- Javasoljuk, hogy kezdje az alapértelmezett oldaltartalommal, és építsen rá.
- Egyéni [tartalmaiba JavaScriptet](javascript-and-page-layout.md) is foglalhat.
- A támogatott böngészőverziók a következőek:
  - Internet Explorer 11, 10 és Microsoft Edge
  - Korlátozott támogatás a 9 Internet Explorer 8-as és 8-as
  - Google Chrome 42.0 és magasabb
  - Mozilla Firefox 38.0 és magasabb verziók
  - Safari iOS és macOS rendszerekhez, 12-es vagy újabb verzió
- Biztonsági korlátozások miatt a Azure AD B2C nem támogatja a `frame` `iframe` , vagy `form` HTML-elemeket.

## <a name="localize-content"></a>Tartalom honosizálása

A HTML-tartalmat úgy honosíthatja, hogy [engedélyezi](language-customization.md) a nyelv testreszabását a Azure AD B2C bérlőben. A funkció engedélyezésével Azure AD B2C a OpenID Connect a `ui_locales` végpontra. A tartalomkiszolgáló ezzel a paraméterrel biztosít nyelvspecifikus HTML-oldalakat.

A tartalom különböző helyekről is lekért adatok a használt területi adatok alapján. A CORS-kompatibilis végponton be kell állítania egy mappastruktúrát, amely meghatározott nyelveken fog tartalmat gazdagépként beállítani. Ha a helyettesítő karakter értéket használja, a megfelelőt fogja `{Culture:RFC5646}` hívni.

Az egyéni oldal URI-ja például a következő lehet:

```http
https://contoso.blob.core.windows.net/{Culture:RFC5646}/myHTML/unified.html
```

Az oldalt francia nyelven is betöltheti, ha a tartalmakat a következő ről húzza le:

```http
https://contoso.blob.core.windows.net/fr/myHTML/unified.html
```

## <a name="custom-page-content-walkthrough"></a>Egyéni oldal tartalmának bemutatója

A folyamat áttekintése:

1. Készítsen elő egy helyet az egyéni oldal tartalmának (egy nyilvánosan elérhető, CORS-kompatibilis HTTPS-végpont) számára.
1. Letölthet és testreszabhat egy alapértelmezett oldaltartalomfájlt, `unified.html` például: .
1. Tegye közzé az egyéni oldal tartalmát a nyilvánosan elérhető HTTPS-végponton.
1. Az eredetközi erőforrás-megosztás (CORS) beállítása a webalkalmazáshoz.
1. Mutasson a szabályzatra az egyéni szabályzat tartalmának URI-ján.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

### <a name="1-create-your-html-content"></a>1. HTML-tartalom létrehozása

Hozzon létre egy egyéni oldaltartalmat a termék márkanevének címében.

1. Másolja ki az alábbi HTML-kódrészletet. Jól formázott HTML5, amely a címkékben található üres nevű *\<div id="api"\>\</div\>* elemmel *\<body\>* rendelkezik. Ez az elem jelzi, Azure AD B2C tartalom hová lesz beszúrva.

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

1. Illessze be a másolt kódrészletet egy szövegszerkesztőbe
1. A CSS használatával stílust használhat az oldalba Azure AD B2C beszúrt felhasználói felületi elemekhez. Az alábbi példa egy egyszerű CSS-fájlt mutat be, amely a regisztrációba injektált HTML-elemek beállításait is tartalmazza:

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

1.  Mentse a fájlt a *következőcustomize-ui.htm: .*

> [!NOTE]
> A HTML-űrlapelemek biztonsági korlátozások miatt el lesznek távolítva, ha login.microsoftonline.com. Ha HTML-űrlapelemeket szeretne használni az egyéni HTML-tartalomban, használja a [b2clogin.com.](b2clogin.md)

### <a name="2-create-an-azure-blob-storage-account"></a>2. Azure Blob Storage-fiók létrehozása

Ebben a cikkben az Azure Blob Storage-et használjuk a tartalom tárolására. Dönthet úgy, hogy egy webkiszolgálón szeretné a tartalmakat, de engedélyeznie kell a CORS-t a [webkiszolgálón.](https://enable-cors.org/server.html)

A HTML-tartalom Blob Storage-ban való tárolásához hajtsa végre a következő lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A központi **menüben** válassza az **Új**  >  **Tárfiók**  >  **lehetőséget.**
1. Válasszon ki **egy előfizetést** a tárfiókhoz.
1. Hozzon **létre egy erőforráscsoportot,** vagy válasszon ki egy meglévőt.
1. Adjon meg egy egyedi **nevet** a tárfiók számára.
1. Válassza ki **a tárfiók** földrajzi helyét.
1. **Az üzembe helyezési** modell továbbra **is Resource Manager.**
1. **A teljesítmény** továbbra is **Standard maradhat.**
1. Módosítsa **a Fiók fajtáját** **Blob Storage-fiókra.**
1. **A replikáció** **ra-GRS maradhat.**
1. **A hozzáférési szint** továbbra is **hot (gyors elérésű) maradhat.**
1. A **tárfiók létrehozásához válassza** az Áttekintés + létrehozás lehetőséget.
    Az üzembe helyezés befejezése után a **Tárfiók** lap automatikusan megnyílik.

#### <a name="21-create-a-container"></a>2.1 Tároló létrehozása

Nyilvános tároló Blob Storage-ban való létrehozásához hajtsa végre a következő lépéseket:

1. A **Blob service** menüben válassza a **Blobok lehetőséget.**
1. Válassza **a +Tároló lehetőséget.**
1. A **Name (Név) alatt** adja meg a root *(gyökér) nevet.* A név lehet egy választott név, például *contoso*  , de ebben a példában az egyszerűség kedvéért a gyökért használjuk.
1. A **Nyilvános hozzáférés szint beállításhoz** válassza a **Blob** lehetőséget, majd az **OK lehetőséget.**
1. Az **új tároló megnyitásához** válassza a root lehetőséget.

#### <a name="22-upload-your-custom-page-content-files"></a>2.2 Az egyéni oldaltartalomfájlok feltöltése

1. Válassza a **Feltöltés** lehetőséget.
1. Válassza a Fájl kiválasztása melletti mappa **ikont.**
1. Keresse meg és válassza **customize-ui.htma** lap felhasználói felületének testreszabása szakaszban korábban létrehozott 3. 000 000 000 000 000 000,
1. Ha egy almappába szeretne feltölteni, bontsa ki a **Speciális** gombra, és adja meg a mappa nevét a **Feltöltés mappába részbe.**
1. Válassza a **Feltöltés** lehetőséget.
1. Válassza ki **customize-ui.htmfeltöltött blobot.**
1. Az **URL-cím** szövegmező jobb  gombja mellett válassza a Másolás a vágólapra ikont, hogy az URL-címet a vágólapra másolja.
1. A webböngészőben lépjen a kimásott URL-címre, és ellenőrizze, hogy a feltöltött blob elérhető-e. Ha például hibába ütközik, ellenőrizze, hogy a tároló hozzáférési típusa blobra `ResourceNotFound` **van-e állítva.**

### <a name="3-configure-cors"></a>3. A CORS konfigurálása

Konfigurálja a Blob Storage-et az eredetközi erőforrás-megosztáshoz az alábbi lépések elvégzésével:

1. A menüben válassza a **CORS lehetőséget.**
1. Az **Engedélyezett eredetek esetében adja** meg a et. `https://your-tenant-name.b2clogin.com` Cserélje le a helyére a Azure AD B2C `your-tenant-name` nevét. Például: `https://fabrikam.b2clogin.com`. A bérlő nevének megadásakor használjon minden kisbetűt.
1. Az **Allowed Methods (Engedélyezett metódusok) mezőben** válassza ki a és `GET` a lehetőséget `OPTIONS` is.
1. Az **Engedélyezett fejlécek mezőben** adjon meg egy csillagot (*).
1. Az Exposed Headers (Elérhető **fejlécek) mezőben** írjon be egy csillag (*) karaktert.
1. A **Maximális életkor értéknél** adja meg a 200-as értékeket.
1. Kattintson a **Mentés** gombra.

#### <a name="31-test-cors"></a>3.1 CORS-teszt

A következő lépések elvégzésével ellenőrizze, hogy készen áll-e:

1. Ismételje meg a CORS konfigurálás lépését. Az **Engedélyezett eredetek esetében adja** meg a `https://www.test-cors.org`
1. Lépjen a [www.test-cors.org](https://www.test-cors.org/) 
1. A Távoli **URL mezőben** illessze be a HTML-fájl URL-címét. Például: `https://your-account.blob.core.windows.net/root/azure-ad-b2c/unified.html`
1. Válassza a **Kérés küldése lehetőséget.**
    Az eredménynek a következőnek kell `XHR status: 200` lennie: . 
    Ha hibaüzenetet kap, ellenőrizze, hogy a CORS-beállítások helyesek-e. Szükség lehet a böngésző gyorsítótárának kiürítése vagy egy privát böngészési munkamenet megnyitására a Ctrl+Shift+P billentyűkombináció lenyomásával.

::: zone pivot="b2c-user-flow"

### <a name="4-update-the-user-flow"></a>4. A felhasználói folyamat frissítése

1. Válassza **a bal felső** sarokban található Minden szolgáltatás Azure Portal, majd keresse meg és válassza a **Azure AD B2C.**
1. Válassza **a Felhasználói folyamatok** lehetőséget, majd B2C_1_signupsignin1 felhasználói folyamatot. 
1. Válassza **az Oldalelrendezések** lehetőséget, majd az Egyesített regisztráció vagy bejelentkezési oldal alatt kattintson az **Igen** gombra az Egyéni oldaltartalom **használata beállításnál.** 
1. Az **Egyéni oldal URI-jában** adja meg a *korábbancustom-ui.html* fájl URI-ját.
1. Az oldal tetején válassza a Mentés **lehetőséget.**

### <a name="5-test-the-user-flow"></a>5. A felhasználói folyamat tesztelése

1. A saját Azure AD B2C válassza a **Felhasználói folyamatok lehetőséget,** majd válassza ki *B2C_1_signupsignin1* felhasználói folyamatot.
1. Az oldal tetején kattintson a **Felhasználói folyamat futtatása elemre.**
1. Kattintson a **Felhasználói folyamat futtatása gombra.**

Az alábbi példához hasonló oldalnak kell jelen lennie, a létrehozott CSS-fájl alapján középre ékelő elemekkel:

![A felhasználói felület egyéni elemeit megjelenítő regisztrációt vagy bejelentkezési oldalt megjelenítő webböngésző](./media/customize-ui-with-html/run-now.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

### <a name="4-modify-the-extensions-file"></a>4. A bővítményfájl módosítása

A felhasználói felület testreszabásának konfigurálását a **ContentDefinition** és annak gyermekelemei az alapfájlból a bővítményfájlba másolja.

1. Nyissa meg a szabályzat alapfájlját. Például: <em>`SocialAndLocalAccounts/`**`TrustFrameworkBase.xml`**</em> . Ez az alapfájl az egyéni szabályzat-kezdőcsomag egyik szabályzatfájlja, amelyet az Egyéni szabályzatok – első lépések előfeltételében [kell beszereznie.](./tutorial-create-user-flows.md?pivots=b2c-custom-policy)
1. Keresse meg és másolja ki a **ContentDefinitions elem teljes** tartalmát.
1. Nyissa meg a bővítményfájlt. Például: *TrustFrameworkExtensions.xml.* Keresse meg a **BuildingBlocks** elemet. Ha a elem nem létezik, adja hozzá.
1. Illessze be a **ContentDefinitions** elem teljes tartalmát, amit a **BuildingBlocks elem gyermekeként másolt** ki.
1. Keresse meg a **contentDefinition elemet,** amely a kimásott `Id="api.signuporsignin"` XML-fájlban található.
1. Módosítsa a **LoadUri** értékét a tárolóba feltöltött HTML-fájl URL-címére. Például: `https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html`.

    Az egyéni szabályzatnak az alábbi kódrészlethez hasonlónak kell lennie:

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

1. Mentse a bővítményfájlt.

### <a name="5-upload-and-test-your-updated-custom-policy"></a>5. A frissített egyéni szabályzat feltöltése és tesztelése

#### <a name="51-upload-the-custom-policy"></a>5.1 Az egyéni szabályzat feltöltése

1. Győződjön meg arról, hogy az Azure AD B2C-bérlőt tartalmazó címtárat használja. A felső menüben válassza a Címtár **és** előfizetés szűrőt, és válassza ki a bérlőt tartalmazó címtárat.
1. Keresse meg és válassza a **Azure AD B2C.**
1. A **Szabályzatok alatt** **válassza** a Identity Experience Framework lehetőséget.
1. Válassza az **Egyéni szabályzat feltöltése lehetőséget.**
1. Töltse fel a korábban módosított kiterjesztésfájlt.

#### <a name="52-test-the-custom-policy-by-using-run-now"></a>5.2 Az egyéni szabályzat tesztelése a **Futtatás most használatával**

1. Válassza ki a feltöltött szabályzatot, majd válassza a **Futtatás most lehetőséget.**
1. A regisztrációhoz e-mail-címet kell használnia.

## <a name="configure-dynamic-custom-page-content-uri"></a>Dinamikus egyéni oldaltartalom URI-ének konfigurálása

Egyéni Azure AD B2C használatával paramétert küldhet az URL-címben, vagy egy lekérdezési sztringet. A paraméterek a HTML-végpontnak való átadásával dinamikusan változtathatja az oldal tartalmát. Például megváltoztathatja a háttérképet az Azure AD B2C regisztrációs vagy bejelentkezési oldalán egy olyan paraméter alapján, amelyet a web- vagy mobilalkalmazásából ad át. A paraméter bármilyen jogcímfel feloldó [lehet,](claim-resolver-overview.md)például az alkalmazásazonosító, a nyelvazonosító vagy az egyéni lekérdezési sztring paraméter, például `campaignId` .

### <a name="sending-query-string-parameters"></a>Lekérdezési sztring paramétereinek küldése

A lekérdezési sztring paramétereinek elküldéhez a [függő](relyingparty.md)fél szabályzatában adjon hozzá egy elemet az alább `ContentDefinitionParameters` látható módon.

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

A tartalomdefinícióban módosítsa a értékét `LoadUri` `https://<app_name>.azurewebsites.net/home/unified` értékre. Az egyéni `ContentDefinition` szabályzatnak az alábbi kódrészlethez hasonlónak kell lennie:

```xml
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://<app_name>.azurewebsites.net/home/unified</LoadUri>
  ...
</ContentDefinition>
```

Amikor Azure AD B2C betölti az oldalt, hívást tesz a webkiszolgáló végpontjára:

```http
https://<app_name>.azurewebsites.net/home/unified?campaignId=123&lang=fr&appId=f893d6d3-3b6d-480d-a330-1707bf80ebea
```

### <a name="dynamic-page-content-uri"></a>Dinamikus oldaltartalom URI-ja

A használt paraméterek alapján a tartalmak különböző helyekről is lekértek. A CORS-kompatibilis végponton állítson be egy mappastruktúrát a tartalom gazdagépeként. A tartalmat például a következő struktúrában rendszerezheti. *Gyökérmappa/mappa nyelvenként/a HTML-fájlokban.* Az egyéni oldal URI-ja például a következő lehet:

```xml
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://contoso.blob.core.windows.net/{Culture:LanguageName}/myHTML/unified.html</LoadUri>
  ...
</ContentDefinition>
```

Azure AD B2C a kétbetűs ISO-kódot küldi el a nyelvhez, `fr` a francia nyelvhez:

```http
https://contoso.blob.core.windows.net/fr/myHTML/unified.html
```

::: zone-end

## <a name="sample-templates"></a>Példasablonok

A felhasználói felület testreszabásához szükséges mintasablonokat itt találja:

```bash
git clone https://github.com/azure-ad-b2c/html-templates
```

Ez a projekt a következő sablonokat tartalmazza:
- [Ocean Blue](https://github.com/azure-ad-b2c/html-templates/tree/main/templates/AzureBlue)
- [Slate Gray](https://github.com/azure-ad-b2c/html-templates/tree/main/templates/MSA)
- [Klasszikus](https://github.com/azure-ad-b2c/html-templates/tree/main/templates/classic)
- [Sablonerőforrások](https://github.com/azure-ad-b2c/html-templates/tree/main/templates/src)

A minta használata:

1. Klónozza a helyi gépen található repo-t. Válasszon egy , `/AzureBlue` `/MSA` vagy sablonmappát. `/classic`
1. Töltse fel a sablon mappájában és mappájában található összes fájlt a Blob Storage-be az előző `/src` szakaszokban leírtak szerint.
1. Ezután nyissa meg a `\*.html` sablon mappájában lévő összes fájlt. Ezután cserélje le az URL-címek összes példányát a `https://login.microsoftonline.com` 2. lépésben feltöltött URL-címre. Például:
    
    Forrás:
    ```html
    https://login.microsoftonline.com/templates/src/fonts/segoeui.WOFF
    ```

    Címzett:
    ```html
    https://your-storage-account.blob.core.windows.net/your-container/templates/src/fonts/segoeui.WOFF
    ```
    
1. Mentse a `\*.html` fájlokat, és töltse fel őket a Blob Storage-be.
1. Most módosítsa a szabályzatot, és mutasson a HTML-fájlra a korábban említettek szerint.
1. Ha hiányzó betűtípusokat, képeket vagy CSS-eket lát, ellenőrizze a hivatkozásokat a bővítmény-szabályzatban és a `\*.html` fájlokban.

## <a name="use-company-branding-assets-in-custom-html"></a>Vállalati arculat eszközeinek használata egyéni HTML-ben

Ha egyéni [HTML-ben](customize-ui.md#configure-company-branding) szeretne vállalati védjegyezést használni, adja hozzá a következő címkéket a `<div id="api">` címkén kívül. A rendszer lecseréli a kép forrását a háttérkép és a szalagcímembléma jelére.

```HTML
<img data-tenant-branding-background="true" />
<img data-tenant-branding-logo="true" alt="Company Logo" />
```

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan engedélyezheti [az ügyféloldali JavaScript-kódot.](javascript-and-page-layout.md)
