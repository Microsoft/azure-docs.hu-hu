---
title: Egyéni adathalmazok renderelése raszteres térképeken | Microsoft Azure térképek
description: Megtudhatja, hogyan adhat hozzá pushpins, címkéket és geometriai alakzatokat egy raszteres térképhez. Tekintse meg, hogyan használható a statikus rendszerkép szolgáltatás a Azure Maps erre a célra.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 5c70835c11bafb3fd06645ba51099b33d1eb6149
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96906080"
---
# <a name="render-custom-data-on-a-raster-map"></a>Egyéni adathalmazok renderelése egy raszteres térképen

Ez a cikk azt ismerteti, hogyan használható a [statikus képszolgáltatás](/rest/api/maps/render/getmapimage)a képösszeállítási funkcióval a raszteres térképeken található átfedések engedélyezéséhez. A képösszeállítás lehetővé teszi a raszteres csempék visszaszerzését, további adatokkal (például egyéni pushpins, címkékkel és geometriai átfedésekkel).

Az egyéni pushpins, címkék és geometriai átfedések megjelenítéséhez használhatja a Poster alkalmazást. Azure Maps [adatszolgáltatási API-kat](/rest/api/maps/data) használhat az átfedések tárolására és megjelenítésére.

> [!Tip]
> Gyakran sokkal költséghatékonyabb, hogy a Azure Maps web SDK használatával egy egyszerű Térkép jelenjen meg egy weboldalon, mint a statikus képszolgáltatás használata. A web SDK leképezési csempéket használ, kivéve, ha a felhasználó serpenyőben és nagyítja a térképet, gyakran csak egy tranzakció töredékét eredményezi a leképezések terhelése alapján. Vegye figyelembe, hogy a Azure Maps web SDK-val lehetőség van a pásztázás és a nagyítás letiltására. Emellett a Azure Maps web SDK az adatvizualizációs lehetőségek széles választékát kínálja, mint a statikus Térkép webszolgáltatás.  

## <a name="prerequisites"></a>Előfeltételek

### <a name="create-an-azure-maps-account"></a>Azure Maps-fiók létrehozása

A cikkben ismertetett eljárások elvégzéséhez először létre kell hoznia egy Azure Maps fiókot, és le kell kérnie a Maps-fiók kulcsát. Kövesse a [fiók létrehozása](quick-demo-map-app.md#create-an-azure-maps-account) egy Azure Maps fiók előfizetésének létrehozásához című témakör utasításait, és kövesse az [elsődleges kulcs lekérése](quick-demo-map-app.md#get-the-primary-key-for-your-account) a fiók elsődleges kulcsának lekérése című szakasz lépéseit. A Azure Maps-hitelesítéssel kapcsolatos további információkért lásd: a [Azure Maps hitelesítés kezelése](./how-to-manage-authentication.md).


## <a name="render-pushpins-with-labels-and-a-custom-image"></a>Pushpins megjelenítése címkékkel és egyéni képpel

> [!Note]
> Az ebben a szakaszban szereplő eljáráshoz Azure Maps-fiók szükséges a S0 vagy S1 díjszabási szinten.

A Azure Maps fiók S0 szintje csak a paraméter egyetlen példányát támogatja `pins` . Ez lehetővé teszi, hogy legfeljebb öt pushpins jelenítse meg az URL-kérelemben megadott egyéni rendszerkép használatával.

A pushpins címkékkel és egyéni képpel történő megjelenítéséhez hajtsa végre a következő lépéseket:

1. Hozzon létre egy gyűjteményt, amelyben tárolni szeretné a kérelmeket. A Poster alkalmazásban válassza az **új** lehetőséget. Az **új létrehozása** ablakban válassza a **gyűjtemény** elemet. Nevezze el a gyűjteményt, és válassza a **Létrehozás** gombot. 

2. A kérelem létrehozásához válassza az **új** újra lehetőséget. Az **új létrehozása** ablakban válassza a **kérelem** lehetőséget. Adja meg a pushpins tartozó **kérelem nevét** . Válassza ki az előző lépésben létrehozott gyűjteményt, amely a kérés mentési helye. Ezután válassza a **Mentés** lehetőséget.
    
    ![Kérelem létrehozása a Poster-ban](./media/how-to-render-custom-data/postman-new.png)

3. Válassza a HTTP beolvasása metódust a Builder (szerkesztő) lapon, majd a GET kérelem létrehozásához adja meg a következő URL-címet.

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.98,%2040.77&pins=custom%7Cla15+50%7Cls12%7Clc003b61%7C%7C%27CentralPark%27-73.9657974+40.781971%7C%7Chttps%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FAzureMapsCodeSamples%2Fmaster%2FAzureMapsCodeSamples%2FCommon%2Fimages%2Ficons%2Fylw-pushpin.png
    ```
    Az eredményül kapott rendszerkép:

    ![Egy címkével rendelkező egyéni gombostű](./media/how-to-render-custom-data/render-pins.png)


## <a name="get-data-from-azure-maps-data-storage"></a>Adatok beolvasása Azure Maps adattárolóból

> [!Note]
> Az ebben a szakaszban ismertetett eljáráshoz egy Azure Maps fiókra van szükség az S1 díjszabási szinten.

Az elérési utat és a rögzítési hely adatait az [Adatfeltöltő API](/rest/api/maps/data/uploadpreview)használatával is beszerezheti. Az elérési út és a PIN-kód adatok feltöltéséhez kövesse az alábbi lépéseket.

1. A Poster alkalmazásban nyisson meg egy új lapot az előző szakaszban létrehozott gyűjteményben. Válassza a HTTP POST metódust a Builder (szerkesztő) lapon, és adja meg a következő URL-címet a POST-kérelem elvégzéséhez:

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```

2. A **Paraméterek** lapon adja meg a következő kulcs/érték párokat, amelyek a post kérelem URL-címéhez használatosak. Cserélje le az `subscription-key` értéket a Azure Maps előfizetési kulcsára.
    
    ![A Poster kulcs/érték paraméterei](./media/how-to-render-custom-data/postman-key-vals.png)

3. A **törzs** lapon válassza ki a nyers bemeneti formátumot, és a legördülő listából válassza a JSON lehetőséget. Adja meg ezt a JSON-t a feltöltött adatként:
    
    ```JSON
    {
      "type": "FeatureCollection",
      "features": [
        {
          "type": "Feature",
          "properties": {},
          "geometry": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  -73.98235,
                  40.76799
                ],
                [
                  -73.95785,
                  40.80044
                ],
                [
                  -73.94928,
                  40.7968
                ],
                [
                  -73.97317,
                  40.76437
                ],
                [
                  -73.98235,
                  40.76799
                ]
              ]
            ]
          }
        },
        {
          "type": "Feature",
          "properties": {},
          "geometry": {
            "type": "LineString",
            "coordinates": [
              [
                -73.97624731063843,
                40.76560773817073
              ],
              [
                -73.97914409637451,
                40.766826609362575
              ],
              [
                -73.98513078689575,
                40.7585866048861
              ]
            ]
          }
        }
      ]
    }
    ```

4. Válassza a **Küldés** lehetőséget, és tekintse át a válasz fejlécét. Sikeres kérelem esetén a hely fejléce tartalmazza az állapot URI-JÁT a feltöltési kérelem aktuális állapotának vizsgálatához. Az állapot URI-ja a következő formátumú lesz:.  

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0
   ```

5. Másolja az állapot-URI-t, és fűzze hozzá az előfizetés-kulcs paramétert a Azure Maps fiók előfizetési kulcsának értékéhez. Használja ugyanazt a fiók-előfizetési kulcsot, amelyet az adatok feltöltéséhez használt. Az állapot URI-formátumának az alábbihoz hasonlóan kell kinéznie:

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

6. A udId beszerzéséhez nyisson meg egy új lapot a Poster alkalmazásban. Válassza a HTTP-módszer beolvasása lehetőséget a szerkesztő lapon. Hozzon igénybe egy GET-kérelmet az állapot URI-n. Ha az adatok feltöltése sikeres volt, egy udId fog kapni a válasz törzsében. Másolja a udId.

   ```JSON
   {
      "udid" : "{udId}"
   }
   ```

7. Használja az `udId` Adatfeltöltő API által fogadott értéket a funkciók megjelenítéséhez a térképen. Ehhez nyisson meg egy új fület az előző szakaszban létrehozott gyűjteményben. Jelölje be a HTTP lekérése metódust a szerkesztő lapon, cserélje le az {előfizetés-Key} és {udId} értéket az értékekkel, és írja be ezt az URL-címet a GET kérelem elvégzéséhez:

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.96682739257812%2C40.78119135317995&pins=default|la-35+50|ls12|lc003C62|co9B2F15||'Times Square'-73.98516297340393 40.758781646381024|'Central Park'-73.96682739257812 40.78119135317995&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.30||udid-{udId}
    ```

    A válasz képe:

    ![Adatok beolvasása Azure Maps adattárolóból](./media/how-to-render-custom-data/uploaded-path.png)

## <a name="render-a-polygon-with-color-and-opacity"></a>Sokszög megjelenítése színnel és opacitással

> [!Note]
> Az ebben a szakaszban ismertetett eljáráshoz egy Azure Maps fiókra van szükség az S1 díjszabási szinten.


A sokszög megjelenését a [path paraméterrel](/rest/api/maps/render/getmapimage#uri-parameters)módosíthatja a stílus-módosítók használatával.

1. A Poster alkalmazásban nyisson meg egy új fület a korábban létrehozott gyűjteményben. Jelölje be a HTTP lekérése metódust a szerkesztő lapon, és adja meg a következő URL-címet a GET kérelem konfigurálásához a sokszög színének és opacitásának megjelenítéséhez:
    
    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&sku=S1&zoom=14&height=500&Width=500&center=-74.040701, 40.698666&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.50||-74.03995513916016 40.70090237454063|-74.04082417488098 40.70028420372218|-74.04113531112671 40.70049568385827|-74.04298067092896 40.69899904076542|-74.04271245002747 40.69879568992435|-74.04367804527283 40.6980961582905|-74.04364585876465 40.698055487620714|-74.04368877410889 40.698022951066996|-74.04168248176573 40.696444909137|-74.03901100158691 40.69837271818651|-74.03824925422668 40.69837271818651|-74.03809905052185 40.69903971085914|-74.03771281242369 40.699340668780984|-74.03940796852112 40.70058515602143|-74.03948307037354 40.70052821920425|-74.03995513916016 40.70090237454063
    &subscription-key={subscription-key}
    ```

    A válasz képe:

    ![Átlátszatlan sokszög megjelenítése](./media/how-to-render-custom-data/opaque-polygon.png)


## <a name="render-a-circle-and-pushpins-with-custom-labels"></a>Kör-és pushpins megjelenítése egyéni címkékkel

> [!Note]
> Az ebben a szakaszban ismertetett eljáráshoz egy Azure Maps fiókra van szükség az S1 díjszabási szinten.


A PIN-kódok megjelenését a stílus-módosítók hozzáadásával módosíthatja. Ha például a pushpins és a hozzájuk tartozó címkéket nagyobb vagy kisebb értékre szeretné tenni, használja a `sc` "skála stílusa" módosítót. Ez a módosító egy nullánál nagyobb értéket vesz fel. Az 1 érték a standard skála. Az 1 értéknél nagyobb értékek esetén a PIN-kód nagyobb lesz, és az 1-nél kisebb értékek kisebbek lesznek. A stílus-módosítókkal kapcsolatos további információkért lásd a [statikus képszolgáltatás elérési útja paramétereit](/rest/api/maps/render/getmapimage#uri-parameters).


A kör-és pushpins egyéni címkékkel történő megjelenítéséhez kövesse az alábbi lépéseket:

1. A Poster alkalmazásban nyisson meg egy új fület a korábban létrehozott gyűjteményben. Válassza a HTTP beolvasása metódust a szerkesztő lapon, és írja be ezt az URL-címet a GET kérelem elvégzéséhez:

    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&zoom=14&height=700&Width=700&center=-122.13230609893799,47.64599069048016&path=lcFF0000|lw2|la0.60|ra1000||-122.13230609893799 47.64599069048016&pins=default|la15+50|al0.66|lc003C62|co002D62||'Microsoft Corporate Headquarters'-122.14131832122801  47.64690503939462|'Microsoft Visitor Center'-122.136828 47.642224|'Microsoft Conference Center'-122.12552547454833 47.642940335653996|'Microsoft The Commons'-122.13687658309935  47.64452336193245&subscription-key={subscription-key}
    ```

    A válasz képe:

    ![Kör megjelenítése egyéni pushpins](./media/how-to-render-custom-data/circle-custom-pins.png)

2. Ha módosítani szeretné a pushpins színét az utolsó lépéssel, módosítsa a "co" stílus-módosítót. Nézze meg `pins=default|la15+50|al0.66|lc003C62|co002D62|` , hogy az aktuális szín #002D62ként lesz megadva a CSS-ben. Tegyük fel, hogy módosítani szeretné #41d42ara. Írja be az új színértéket a "co" megadása után, a következőhöz hasonló módon: `pins=default|la15+50|al0.66|lc003C62|co41D42A|` . Új GET-kérés készítése:

    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&zoom=14&height=700&Width=700&center=-122.13230609893799,47.64599069048016&path=lcFF0000|lw2|la0.60|ra1000||-122.13230609893799 47.64599069048016&pins=default|la15+50|al0.66|lc003C62|co41D42A||'Microsoft Corporate Headquarters'-122.14131832122801  47.64690503939462|'Microsoft Visitor Center'-122.136828 47.642224|'Microsoft Conference Center'-122.12552547454833 47.642940335653996|'Microsoft The Commons'-122.13687658309935  47.64452336193245&subscription-key={subscription-key}
    ```

    A PIN-kód színeinek módosítása után a válasz képe:

    ![Kör megjelenítése frissített pushpins](./media/how-to-render-custom-data/circle-updated-pins.png)

Hasonlóképpen megváltoztathatja, hozzáadhatja és eltávolíthatja a többi stílus-módosítót is.

## <a name="next-steps"></a>Következő lépések


* Ismerkedjen meg a [Azure Maps Map RENDSZERKÉP API](/rest/api/maps/render/getmapimage) dokumentációjának használatával.
* Ha többet szeretne megtudni a Azure Maps adatszolgáltatásról (előzetes verzió), tekintse meg a [szolgáltatás dokumentációját](/rest/api/maps/data).