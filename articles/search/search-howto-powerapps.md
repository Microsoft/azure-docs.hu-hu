---
title: 'Oktatóanyag: Power apps-lekérdezés'
titleSuffix: Azure Cognitive Search
description: Részletes útmutató az Azure Cognitive Search indexhez csatlakozó, lekérdezések küldéséhez és az eredmények megjelenítéséhez kapcsolódó Power apps létrehozásához.
author: luiscabrer
manager: eladz
ms.author: luisca
ms.service: cognitive-search
ms.devlang: rest-api
ms.topic: tutorial
ms.date: 11/17/2020
ms.openlocfilehash: e8c16f02cf6b77fa54d2a19abac48e9914aa99bd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96008565"
---
# <a name="tutorial-query-a-cognitive-search-index-from-power-apps"></a>Oktatóanyag: Cognitive Search index lekérdezése a Power Appsből

Kihasználhatja a Power apps gyors alkalmazás-fejlesztési környezetét, hogy egyéni alkalmazást hozzon létre az Azure Cognitive Search kereshető tartalmához.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Kapcsolódás az Azure Cognitive Searchhoz
> * Lekérdezési kérelem beállítása
> * Eredmények megjelenítése vászon alkalmazásban

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt nyisson meg egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Előfeltételek

* [Power apps-fiók](https://make.powerapps.com)

* [Hotels –](search-get-started-portal.md) a keresési szolgáltatásban üzemeltetett minta index

* [Lekérdezési API-kulcs](search-security-api-keys.md#find-existing-keys)

## <a name="1---create-a-custom-connector"></a>1 – egyéni összekötő létrehozása

A Power apps-összekötők egy adatforrás-kapcsolatok. Ebben a lépésben létrehoz egy egyéni összekötőt, amely a felhőben található keresési indexhez csatlakozik.

1. [Jelentkezzen be](https://make.powerapps.com) a Power Apps szolgáltatásba.

1. A bal **oldalon bontsa ki az**  >  **Egyéni adatösszekötők** elemet.
 
    :::image type="content" source="./media/search-howto-powerapps/1-2-custom-connector.png" alt-text="Egyéni összekötő menü" border="true":::

1. Válassza az  **+ új egyéni összekötő** lehetőséget, majd válassza **a létrehozás üresből** lehetőséget.

    :::image type="content" source="./media/search-howto-powerapps/1-3-create-blank.png" alt-text="Létrehozás üres menüből" border="true":::

1. Adja meg az egyéni összekötő nevét (például *AzureSearchQuery*), majd kattintson a Continue ( **Folytatás**) gombra.

1. Adja meg az Általános lapon található adatokat:

   * Ikon háttérszíne (például #007ee5)
   * Leírás (például "összekötő az Azure Cognitive Search")
   * A gazdagépen meg kell adnia a keresési szolgáltatás URL-címét (például `<yourservicename>.search.windows.net` ).
   * Az alap URL-cím mezőben egyszerűen írja be a "/" értéket

    :::image type="content" source="./media/search-howto-powerapps/1-5-general-info.png" alt-text="Általános tájékoztatási párbeszéd" border="true":::

1. A biztonság lapon állítsa be az *API-kulcsot* **hitelesítési típusként**, állítsa a paraméter címkéjét és a paraméter nevét az *API-Key* értékre. A **paraméter helye** mezőben válassza a *fejléc* lehetőséget az alább látható módon.

    :::image type="content" source="./media/search-howto-powerapps/1-6-authentication-type.png" alt-text="Hitelesítés típusa beállítás" border="true":::

1. A definíciók lapon válassza az **+ új művelet** lehetőséget egy olyan művelet létrehozásához, amely lekérdezi az indexet. Adja meg a "Query" értéket az összegzéshez, valamint a művelet AZONOSÍTÓjának nevét. Adjon meg egy leírást, például *"a keresési index lekérdezése" kifejezést*.

    :::image type="content" source="./media/search-howto-powerapps/1-7-new-action.png" alt-text="Új művelet beállításai" border="true":::

1. Görgessen le. A kérelmek területen válassza a **+ Importálás mintából** gombot a keresési szolgáltatás lekérdezési kérelmének konfigurálásához:

   * Válassza ki a műveletet `GET`

   * Az URL-cím mezőben adja meg a keresési index mintájának lekérdezését (az `search=*` összes dokumentum visszaadása `$select=` lehetővé teszi a mezők kiválasztását). Az API-verziót kötelező megadni. Teljesen meg van adva egy URL-cím, amely a következőhöz hasonló lehet: `https://mydemo.search.windows.net/indexes/hotels-sample-index/docs?search=*&$select=HotelName,Description,Address/City&api-version=2020-06-30`

   * A fejlécek mezőbe írja be a következőt: `Content-Type` . Az értéket `application/json` egy későbbi lépésben kell beállítania.

     A **Power apps** az URL-címben szereplő szintaxis használatával Kinyeri a paramétereket a lekérdezésből: a keresés, a kiválasztás és az API-Version paraméterek a varázsló előrehaladása során konfigurálhatók.

       :::image type="content" source="./media/search-howto-powerapps/1-8-1-import-from-sample.png" alt-text="Importálás mintából" border="true":::

1. A kérelem automatikus kitöltéséhez kattintson az **Importálás** gombra. Fejezze be a paraméter metaadatait úgy, hogy az egyes paraméterek mellett **...** szimbólumra kattint. A **vissza** gombra kattintva térjen vissza a kérelem oldalára az egyes paraméterek frissítése után.

   :::image type="content" source="./media/search-howto-powerapps/1-8-2-import-from-sample.png" alt-text="Importálás a minta-párbeszédből" border="true":::

1. *Keresés*: `*` **alapértelmezett értékként** állítsa be a **kötelező** értéket, és állítsa a *nincs* *értékre* a **láthatóságot** . 

    :::image type="content" source="./media/search-howto-powerapps/1-10-1-parameter-metadata-search.png" alt-text="Keresési paraméter metaadatai" border="true":::

1. A *Select (kiválasztás*) beállításnál állítsa be `HotelName,Description,Address/City` az  alapértelmezett értéket, állítsa a False  ( *hamis*) **értékre**, és állítsa a *nincs* értékre.  

    :::image type="content" source="./media/search-howto-powerapps/1-10-4-parameter-metadata-select.png" alt-text="Paraméter metaadatainak kiválasztása" border="true":::

1. Az *API-Version*: beállítás értékeként állítsa be `2020-06-30` az **alapértelmezett értéket**, állítsa *igaz* értékre, és *belsőként* állítsa be a **láthatóságot** .   

    :::image type="content" source="./media/search-howto-powerapps/1-10-2-parameter-metadata-version.png" alt-text="Version paraméter metaadatai" border="true":::

1. A *Content-Type*: értéknél állítsa be a következőt: `application/json` .

1. A módosítások végrehajtása után váltson át a **hencegő szerkesztő** nézetre. A parameters (paraméterek) szakaszban a következő konfigurációnak kell megjelennie:

    ```JSON
    parameters:
      - {name: search, in: query, required: false, type: string, default: '*'}
      - {name: $select, in: query, required: false, type: string, default: 'HotelName,Description,Address/City'}
      - {name: api-version, in: query, required: true, type: string, default: '2020-06-30',
        x-ms-visibility: internal}
      - {name: Content-Type, in: header, required: false, type: string}
    ```

1. Váltson vissza a varázslóra, és térjen vissza a **3. Kérelem** lépései. Görgessen le a válasz szakaszhoz. Kattintson az **"alapértelmezett válasz hozzáadása"** lehetőségre. Ez azért fontos, mert segíteni fog a Power apps számára a válasz sémájának megismerésében. 

1. Egy minta válasz beillesztése. A minta válaszok rögzítésének egyszerű módja a Azure Portal keresési ablaka. A Search Explorerben ugyanazt a lekérdezést kell megadnia, mint a kérelemhez, de vegye fel **$Top = 2** értéket, hogy csak két dokumentumra korlátozza az eredményeket:: `search=*&$select=HotelName,Description,Address/City&$top=2` . 

   A Power apps szolgáltatásnak csak néhány eredményre van szüksége a séma észleléséhez. A varázslóba most átmásolhatja a következő választ, feltéve, hogy a Hotels-Sample-indexet használja.

    ```JSON
    {
        "@odata.context": "https://mydemo.search.windows.net/indexes('hotels-sample-index')/$metadata#docs(*)",
        "value": [
            {
                "@search.score": 1,
                "HotelName": "Arcadia Resort & Restaurant",
                "Description": "The largest year-round resort in the area offering more of everything for your vacation – at the best value!  What can you enjoy while at the resort, aside from the mile-long sandy beaches of the lake? Check out our activities sure to excite both young and young-at-heart guests. We have it all, including being named “Property of the Year” and a “Top Ten Resort” by top publications.",
                "Address": {
                    "City": "Seattle"
                }
            },
            {
                "@search.score": 1,
                "HotelName": "Travel Resort",
                "Description": "The Best Gaming Resort in the area.  With elegant rooms & suites, pool, cabanas, spa, brewery & world-class gaming.  This is the best place to play, stay & dine.",
                "Address": {
                    "City": "Albuquerque"
                }
            }
        ]
    }
    ```

    > [!TIP] 
    > A JSON-válasz megadható, ezért érdemes lehet leegyszerűsíteni a JSON-t a beillesztés előtt. A válasz sémája és formátuma fontosabb, mint maga az érték. A Description (Leírás) mező például csak az első mondatra egyszerűsített.

1. Az alapértelmezett válasz hozzáadásához kattintson az **Importálás** gombra.

1. Kattintson a jobb felső sarokban található **összekötő létrehozása** lehetőségre a definíció mentéséhez.

1. Az összekötő bezárásához kattintson a **Bezárás** gombra.

## <a name="2---test-the-connection"></a>2 – a kapcsolatok tesztelése

Az összekötő első létrehozásakor újra meg kell nyitni az egyéni összekötők listájáról, hogy tesztelni lehessen. Később, ha további frissítéseket végez, tesztelheti a varázslót a varázslóból.

Ehhez a feladathoz szüksége lesz egy [lekérdezési API-kulcsra](search-security-api-keys.md#find-existing-keys) . Minden alkalommal, amikor létrejön egy kapcsolat, függetlenül attól, hogy egy teszt futtatása vagy belefoglalása egy alkalmazásba történik, az összekötőnek szüksége van az Azure Cognitive Searchhoz való csatlakozáshoz használt lekérdezési API-kulcsra.

1. A bal szélen kattintson az **Egyéni összekötők** lehetőségre.

1. Keresse meg az összekötőt a listában (ebben az oktatóanyagban a "AzureSearchQuery").

1. Válassza ki az összekötőt, bontsa ki a műveletek listát, és válassza a **tulajdonságok megtekintése** lehetőséget.

    :::image type="content" source="./media/search-howto-powerapps/1-11-1-test-connector.png" alt-text="Tulajdonságok megtekintése" border="true":::

1. Válassza a **Szerkesztés** lehetőséget a jobb felső sarokban.

1. Válassza a **4 lehetőséget. Tesztelje** a teszt oldal megnyitását.

1. A tesztelési művelet menüben kattintson az **+ új kapcsolatok** elemre.

1. Adja meg a lekérdezési API-kulcsot. Ez egy Azure Cognitive Search-lekérdezés, amely csak olvasási hozzáférést biztosít az indexekhez. A kulcsot a Azure Portalban [találja](search-security-api-keys.md#find-existing-keys) . 

1. A műveletek területen kattintson a **tesztelési művelet** gombra. Ha sikeres, akkor egy 200 állapotot kell megjelennie, és a válasz törzsében meg kell jelennie a JSON-nek, amely leírja a keresési eredményeket.

    :::image type="content" source="./media/search-howto-powerapps/1-11-2-test-connector.png" alt-text="JSON-válasz" border="true":::

## <a name="3---visualize-results"></a>3 – eredmények megjelenítése

Ebben a lépésben hozzon létre egy energiagazdálkodási alkalmazást egy keresőmező, egy keresési gomb és egy megjelenített területen az eredményekhez. A Power app a legutóbb létrehozott egyéni összekötőhöz csatlakozik, hogy lekérje az Azure Searchból származó adatok beolvasását.

1. A bal oldalon bontsa ki az **alkalmazások**  >  **+ új alkalmazás**  >  **vászon** elemet.

    :::image type="content" source="./media/search-howto-powerapps/2-1-create-canvas.png" alt-text="Vászon alkalmazás létrehozása" border="true":::

1. Válassza ki az alkalmazás típusát. Ebben az oktatóanyagban hozzon létre egy **üres alkalmazást** a **telefonos elrendezéssel**. Ekkor megjelenik a **Power apps Studio** .

1. A Studióban válassza az **adatforrások** fület, majd kattintson az imént létrehozott új összekötőre. A mi esetünkben a *AzureSearchQuery* nevezzük. Kattintson **a kapcsolatok hozzáadása** lehetőségre.

   Adja meg a lekérdezési API-kulcsot.

    :::image type="content" source="./media/search-howto-powerapps/2-3-connect-connector.png" alt-text="összekötő összekötése" border="true":::

    Most a *AzureSearchQuery* olyan adatforrás, amely elérhető az alkalmazásból.

1. A **Beszúrás lapon** adjon hozzá néhány vezérlőt a vászonhoz.

    :::image type="content" source="./media/search-howto-powerapps/2-4-add-controls.png" alt-text="Vezérlőelemek beszúrása" border="true":::

1. Szúrja be a következő elemeket:

   * Egy "Query:" értéket tartalmazó szöveges címke
   * Szövegbeviteli elem (hívja meg a *txtQuery*, alapértelmezett érték: "*")
   * A "keresés" szöveget tartalmazó gomb 
   * Egy függőleges katalógus (meghívása *galleryResults*)

    A vászonnak a következőhöz hasonlóan kell kinéznie:

    :::image type="content" source="./media/search-howto-powerapps/2-5-controls-layout.png" alt-text="Vezérlőelemek elrendezése" border="true":::

1. Ha szeretné, hogy a **Keresés gomb** egy lekérdezést adjon ki, illessze be a következő műveletet a **OnSelect**:

    ```
    If(!IsBlank(txtQuery.Text),
        ClearCollect(azResult, AzureSearchQuery.Query({search: txtQuery.Text}).value))
    ```

   Az alábbi képernyőfelvételen a **OnSelect** művelet szerkesztőléc látható.

    :::image type="content" source="./media/search-howto-powerapps/2-6-search-button-event.png" alt-text="OnSelect gomb" border="true":::

   Ez a művelet azt eredményezi, hogy a gomb egy új, *azResult* nevű gyűjteményt fog frissíteni a keresési lekérdezés eredményével, és a *txtQuery* szövegmezőben lévő szöveget használja lekérdezési kifejezésként.

   > [!NOTE]
   > Ha a képlet szintaktikai hiba jelenik meg, akkor a "The Function" ClearCollect "függvényben érvénytelen függvények szerepelnek":
   > 
   > * Először győződjön meg arról, hogy az összekötő hivatkozása helyes. Törölje az összekötő nevét, és kezdje el beírni az összekötő nevét. Az IntelliSense a megfelelő összekötőt és műveletet javasolja.
   > 
   > * Ha a hiba továbbra is fennáll, törölje, majd hozza létre újra az összekötőt. Ha egy összekötő több példánya is van, előfordulhat, hogy az alkalmazás nem megfelelő.
   > 

1. Csatolja a függőleges katalógus vezérlőelemet az előző lépés végrehajtásakor létrehozott *azResult* -gyűjteményhez. 

   Válassza ki a katalógus vezérlőelemet, és hajtsa végre a következő műveleteket a Tulajdonságok ablaktáblán.

   * **Adatforrás** beállítása *azResult*.
   * Válasszon olyan **elrendezést** , amely az indexben található adattípuson alapul. Ebben az esetben a *címet, az alcím és a szövegtörzs* elrendezését használtuk.
   * **Szerkessze a mezőket**, és válassza ki a megjeleníteni kívánt mezőket.

    Mivel az összekötő meghatározásakor a minta eredményét adta meg, az alkalmazás tisztában van az indexben elérhető mezőkkel.
    
    :::image type="content" source="./media/search-howto-powerapps/2-7-gallery-select-fields.png" alt-text="Katalógusmezők" border="true":::   
 
1. Nyomja le az **F5** billentyűt az alkalmazás villámnézetének megtekintéséhez.  

    :::image type="content" source="./media/search-howto-powerapps/2-8-3-final.png" alt-text="A kész alkalmazás" border="true":::    

<!--     Remember that the fields can be set to calculated values.

    For the example, setting using the *"Image, Title and Subtitle"* layout and specifying the *Image* function as the concatenation of the root path for the data and the file name (for instance, `"https://mystore.blob.core.windows.net/multilang/" & ThisItem.metadata_storage_name`) will produce the result below.

    :::image type="content" source="./media/search-howto-powerapps/2-8-2-final.png" alt-text="Final app" border="true":::         -->

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha a saját előfizetésében dolgozik, érdemes az egyes projektek végén eldöntenie, hogy szüksége lesz-e még a létrehozott erőforrásokra. A továbbra is futó erőforrások költségekkel járhatnak. Az erőforrásokat törölheti egyesével, vagy az erőforráscsoport törlésével eltávolíthatja a benne lévő összes erőforrást is.

A bal oldali navigációs panelen a **minden erőforrás** vagy **erőforráscsoport** hivatkozás használatával megkeresheti és kezelheti az erőforrásokat a portálon.

Ha ingyenes szolgáltatást használ, ne feledje, hogy Ön legfeljebb három indexet, indexelő és adatforrást használhat. A portálon törölheti az egyes elemeket, hogy a korlát alatt maradjon.

## <a name="next-steps"></a>Következő lépések

A Power apps lehetővé teszi az egyéni alkalmazások gyors alkalmazásának fejlesztését. Most, hogy már tudja, hogyan csatlakozhat egy keresési indexhez, többet tudhat meg arról, hogyan hozhat létre Rich vizualizációs élményt egy egyéni Power App-alkalmazásban.

> [!div class="nextstepaction"]
> [Power apps learning-katalógus](/powerapps/learning-catalog/get-started)