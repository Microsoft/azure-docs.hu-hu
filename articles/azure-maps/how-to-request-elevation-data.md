---
title: Jogosultságszint-emelési kérések igénylése a Azure Maps jogosultságszint-emelési szolgáltatással (előzetes verzió)
description: Megtudhatja, hogyan igényelhet jogosultságszint-emelési információkat a Azure Maps jogosultságszint-emelési szolgáltatással (előzetes verzió).
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: d14eda84144105bf2e04f1238284bc58a91c4c03
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98684055"
---
# <a name="request-elevation-data-using-the-azure-maps-elevation-service-preview"></a>Jogosultságszint-emelési kérések igénylése a Azure Maps jogosultságszint-emelési szolgáltatással (előzetes verzió)

> [!IMPORTANT]
> A Azure Maps jogosultságszint-emelési szolgáltatás jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A Azure Maps [jogosultságszint-emelési szolgáltatás](/rest/api/maps/elevation) API-kat biztosít a jogosultságszint-emelési adatlekérdezéshez bárhol a Föld felszínén. A mintavételes jogosultságszint-emelési adat az elérési utakon, egy meghatározott határolókereton vagy adott koordinátákon keresztül kérhető le. Azt is megteheti, hogy a [Render v2 – Get Map csempe API-](/rest/api/maps/renderv2) val lekéri a jogosultságszint-emelési adatgyűjtési formátumot. A csempék GeoTIFF raszteres formátumban lesznek továbbítva. Ez a cikk bemutatja, hogyan használhatók a Azure Maps jogosultságszint-emelési szolgáltatás és a Map csempe API a jogosultságszint-emelési kérések igényléséhez. A jogosultságszint-emelési adatkérést GeoJSON és GeoTiff formátumban is kérheti.

## <a name="prerequisites"></a>Előfeltételek

1. [Azure Maps fiók létrehozása az S1 díjszabási szinten](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Szerezzen be egy elsődleges előfizetési kulcsot](quick-demo-map-app.md#get-the-primary-key-for-your-account), más néven az elsődleges kulcsot vagy az előfizetési kulcsot.

A Azure Maps-hitelesítéssel kapcsolatos további információkért [Azure Maps a hitelesítés kezelése](how-to-manage-authentication.md)című témakörben olvashat bővebben.

Ez a cikk a [Poster](https://www.postman.com/) alkalmazást használja, de más API-fejlesztési környezetet is választhat.

## <a name="request-elevation-data-in-raster-tiled-format"></a>Jogosultságszint-emelési adatkérések a raszter mozaik formátumban

A jogosultságszint-emelési adattároló formátumában a [Render v2-Get Map csempe API-](/rest/api/maps/renderv2)t használhatja. Ha a csempe megtalálható, az API GeoTIFF-ként adja vissza a csempét. Ellenkező esetben az API a 0 értéket adja vissza. Az összes raszteres DEM-csempe a GeoID (tengerszint feletti) föld üzemmódot használja. Ebben a példában az MT-re vonatkozó jogosultságszint-emelési adatkérést fogunk kérni. Everest.

>[!TIP]
>Ha egy csempét szeretne beolvasni a globális Térkép egy adott területére, a megfelelő nagyítási szinten meg kell találnia a megfelelő csempét. Vegye figyelembe továbbá, hogy a WorldDEM a teljes globális jelentősen eltérővé váltak fedi le, de nem fedi le az óceánokat.  További információ: [nagyítási szintek és csempék rácsa](zoom-levels-and-tile-grid.md).

1. Nyissa meg a Poster alkalmazást. A Poster alkalmazás teteje közelében válassza az **új** lehetőséget. Az **új létrehozása** ablakban válassza a **gyűjtemény** elemet.  Nevezze el a gyűjteményt, és válassza a **Létrehozás** gombot.

2. A kérelem létrehozásához válassza az **új** újra lehetőséget. Az **új létrehozása** ablakban válassza a **kérelem** lehetőséget. Adja meg a kérelem **nevét** . Válassza ki az előző lépésben létrehozott gyűjteményt, majd kattintson a **Mentés** gombra.

3. Válassza a **http lekérése** módszert a Builder (szerkesztő) lapon, és adja meg a következő URL-címet a raszteres csempe igényléséhez. Ehhez a kérelemhez és a cikkben említett egyéb kérelmekhez cserélje le az `{Azure-Maps-Primary-Subscription-key}` elsődleges előfizetési kulcsát.

    ```http
    https://atlas.microsoft.com/map/tile?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=2.0&tilesetId=microsoft.dem&zoom=13&x=6074&y=3432
    ```

4. Kattintson a **Küldés** gombra. A jogosultságszint-emelési GeoTIFF formátumban tartalmazó raszteres csempét kell megkapnia. A raszteres csempén lévő nyers adattípusok mindegyikének képpont típusúnak kell lennie `float` . Az egyes képpontok értéke a tengerszint feletti magasságot jelöli.

## <a name="request-elevation-data-in-geojson-format"></a>Jogosultságszint-emelési kérések GeoJSON formátumban

A jogosultságszint-emelési szolgáltatás (előzetes verzió) API-k segítségével GeoJSON formátumban kérheti a jogosultságszint-emelési kéréseket. Ez a szakasz a három API-t mutatja be:

* [Pontok lekérdezése](/rest/api/maps/elevation/getdataforpoints)
* [Pontok utáni adatposta](/rest/api/maps/elevation/postdataforpoints)
* [Adatlekérdezés a vonallánchoz](/rest/api/maps/elevation/getdataforpolyline)
* [Adatposta a vonallánchoz](/rest/api/maps/elevation/postdataforpolyline)
* [A Határolókerethoz tartozó adat lekérése](/rest/api/maps/elevation/getdataforboundingbox)

>[!IMPORTANT]
> Ha nem lehet visszaadni egy adatvisszaadás értéket, az összes API-t visszaadja `0` .

### <a name="request-elevation-data-for-points"></a>A pontokra vonatkozó jogosultságszint-emelési kérések lekérdezése

Ebben a példában a [pontok beolvasása API](/rest/api/maps/elevation/getdataforpoints) használatával kéri a jogosultságszint-emelési adatgyűjtést. Az Everest és a Chamlang Mountains. Ezt követően a [pontok közzététele API](/rest/api/maps/elevation/postdataforpoints) -val a jogosultságszint-emelési adatkérést a két ponttal megegyezően fogja használni. Az URL-címben szereplő földrajzi szélességeknek és hosszúságoknak a WGS84 (globális geodéziai rendszer) decimális mértékben kell szerepelniük.

 >[!IMPORTANT]
 >Az URL-karakter hosszúsági korlátja miatt a 2048-as számú URL-cím hossza nem haladhatja meg a 100-es koordinátákat egy URL GET kérelemben. Ha több mint 100 koordinátákat szeretne átadni egy folyamat tagolt karakterláncának, használja a pontok utáni adatpostát.

1. A kérelem létrehozásához válassza az **új** újra lehetőséget. Az **új létrehozása** ablakban válassza a **kérelem** lehetőséget. Adja meg a kérelem **nevét** . Válassza ki az előző lépésben létrehozott gyűjteményt, majd kattintson a **Mentés** gombra.

2. Válassza a http **lekérése** módszert a Builder (szerkesztő) lapon, és adja meg a következő URL-címet. Ehhez a kérelemhez és a cikkben említett egyéb kérelmekhez cserélje le az `{Azure-Maps-Primary-Subscription-key}` elsődleges előfizetési kulcsát.

    ```http
    https://atlas.microsoft.com/elevation/point/json?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0&points=-73.998672,40.714728|150.644,-34.397
    ```

3. Kattintson a **Küldés** gombra.  A következő JSON-válasz jelenik meg:

    ```json
    {
    "data": [
        {
            "coordinate": {
                "latitude": 40.714728,
                "longitude": -73.998672
            },
            "elevationInMeter": 12.142355447638208
        },
        {
            "coordinate": {
                "latitude": -34.397,
                "longitude": 150.644
            },
            "elevationInMeter": 384.47041445517846
        }
    ]
    }
    ```

4. Most hívjuk meg a [pontok API post-adatküldési](/rest/api/maps/elevation/postdataforpoints) szolgáltatását a jogosultságszint-emelési információk ugyanarra a két pontra történő beszerzésére. Válassza a http **post** metódust a Builder (szerkesztő) lapon, és adja meg a következő URL-címet. Ehhez a kérelemhez és a cikkben említett egyéb kérelmekhez cserélje le az `{Azure-Maps-Primary-Subscription-key}` elsődleges előfizetési kulcsát.

    ```http
    https://atlas.microsoft.com/elevation/point/json?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0
    ```

5. A **post** kérelem **fejlécében** állítsa a következőre `Content-Type` : `application/json` . A **törzsben** adja meg az alábbi koordináta-pont adatait. Ha elkészült, kattintson a **Küldés** gombra.

     ```json
    [
        {
            "lon": -73.998672,
            "lat": 40.714728
        },
        {
            "lon": 150.644,
            "lat": -34.397
        }
    ]
    ```

### <a name="request-elevation-data-samples-along-a-polyline"></a>Jogosultságszint-emelési kérések lekérdezése egy vonallánc mentén

Ebben a példában a [vonalláncra vonatkozó adatgyűjtést](/rest/api/maps/elevation/getdataforpolyline) fogjuk használni, hogy öt, egymással egyenlően felhelyezett mintát igényeljen a jogosultságszint-emelési koordináták közötti egyenes vonal mentén. Az Everest és a Chamlang Mountains. Mindkét koordinátákat hosszú/lat formátumban kell megadni. Ha nem ad meg értéket a `samples` paraméterhez, a minták száma alapértelmezés szerint 10. A minták maximális száma 2 000.

Ezután a vonallánchoz tartozó adatlekérdezés használatával három, egymással egyenlő helyen lévő, a jogosultságszint-emelési adatmennyiséget tartalmazó mintát kell kérni. A minták pontos helyét a három hosszú/lat koordináta-pár átadásával fogjuk meghatározni.

Végezetül a [vonallánc API utáni Adatpostát](/rest/api/maps/elevation/postdataforpolyline) fogjuk használni, hogy megegyezzenek a jogosultságszint-emelési és a három egyenlő helyen lévő minták.

Az URL-címben szereplő földrajzi szélességeknek és hosszúságoknak a WGS84 (globális geodéziai rendszer) decimális mértékben kell szerepelniük.

 >[!IMPORTANT]
 >Az URL-karakter hosszúsági korlátja miatt a 2048-as számú URL-cím hossza nem haladhatja meg a 100-es koordinátákat egy URL GET kérelemben. Ha több mint 100 koordinátákat szeretne átadni egy folyamat tagolt karakterláncának, használja a pontok utáni adatpostát.

1. Válassza az **Új** lehetőséget. Az **új létrehozása** ablakban válassza a **kérelem** lehetőséget. Adja meg a **kérelem nevét** , és válasszon ki egy gyűjteményt. Kattintson a **Mentés** gombra.

2. Válassza a http **lekérése** módszert a Builder (szerkesztő) lapon, és adja meg a következő URL-címet. Ehhez a kérelemhez és a cikkben említett egyéb kérelmekhez cserélje le az `{Azure-Maps-Primary-Subscription-key}` elsődleges előfizetési kulcsát.

   ```http
    https://atlas.microsoft.com/elevation/line/json?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}&lines=-73.998672,40.714728|150.644,-34.397&samples=5
    ```

3. Kattintson a **Küldés** gombra.  A következő JSON-válasz jelenik meg:

    ```JSON
    {
        "data": [
            {
                "coordinate": {
                    "latitude": 40.714728,
                    "longitude": -73.998672
                },
                "elevationInMeter": 12.14236
            },
            {
                "coordinate": {
                    "latitude": 21.936796000000001,
                    "longitude": -17.838003999999998
                },
                "elevationInMeter": 0.0
            },
            {
                "coordinate": {
                    "latitude": 3.1588640000000012,
                    "longitude": 38.322664000000003
                },
                "elevationInMeter": 598.66943
            },
            {
                "coordinate": {
                    "latitude": -15.619067999999999,
                    "longitude": 94.483332000000019
                },
                "elevationInMeter": 0.0
            },
            {
                "coordinate": {
                    "latitude": -34.397,
                    "longitude": 150.644
                },
                "elevationInMeter": 384.47041
            }
        ]
    }
    ```

4. Az Everest, a Chamlang és a Jannu Mountains-hegy koordinátái között a jogosultságszint-emelési útvonal mentén három mintát fogunk kérni. A **params (paraméterek** ) szakaszban másolja a következő koordináta-tömböt a `lines` lekérdezési kulcs értékére.

    ```html
        86.9797222, 27.775|86.9252778, 27.9880556 | 88.0444444, 27.6822222
    ```

5. Módosítsa a `samples` lekérdezési kulcs értékét a következőre: `3` .  Az alábbi képen az új értékek láthatók.

     :::image type="content" source="./media/how-to-request-elevation-data/get-elevation-samples.png" alt-text="Három jogosultságszint-emelési adatminta beolvasása.":::

6. Kattintson a kék **Küldés** gombra. A következő JSON-válasz jelenik meg:

    ```json
    {
        "data": [
            {
                "coordinate": {
                    "latitude": 27.775,
                    "longitude": 86.9797222
                },
                "elevationInMeter": 7116.0348851572589
            },
            {
                "coordinate": {
                    "latitude": 27.737403546316028,
                    "longitude": 87.411180791156454
                },
                "elevationInMeter": 1798.6945512521534
            },
            {
                "coordinate": {
                    "latitude": 27.682222199999998,
                    "longitude": 88.0444444
                },
                "elevationInMeter": 7016.9372013588072
            }
        ]
    }
    ```

7. Most hívjuk a [vonallánc API utáni Adatpostát](/rest/api/maps/elevation/postdataforpolyline) , hogy megszerezzük a jogosultságszint-emelési adatgyűjtést ugyanarra a három pontra. Válassza a http **post** metódust a Builder (szerkesztő) lapon, és adja meg a következő URL-címet. Ehhez a kérelemhez és a cikkben említett egyéb kérelmekhez cserélje le az `{Azure-Maps-Primary-Subscription-key}` elsődleges előfizetési kulcsát.

    ```http
    https://atlas.microsoft.com/elevation/line/json?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}&samples=5
    ```

8. A **post** kérelem **fejlécében** állítsa a következőre `Content-Type` : `application/json` . A **törzsben** adja meg az alábbi koordináta-pont adatait. Ha elkészült, kattintson a **Küldés** gombra.

     ```json
    [
        {
            "lon": 86.9797222,
            "lat": 27.775
        },
        {
            "lon": 86.9252778,
            "lat": 27.9880556
        },
        {
            "lon": 88.0444444,
            "lat": 27.6822222
        }
    ]
    ```

### <a name="request-elevation-data-by-bounding-box"></a>Jogosultságszint-emelési adat kérése a határolókeret alapján

Most használjuk a [beolvasás a határolókeret](/rest/api/maps/elevation/getdataforboundingbox) számára lehetőséget a jogosultságszint-emelési adat az MT közelében való igényléséhez. Rainier, WA. A jogosultságszint-emelési adat a határvonalon belül egyenlően elhelyezkedő helyeken lesz visszaadva. A (2) a lat/hosszú koordináták (déli szélesség, nyugati hosszúság | északi szélesség, keleti hosszúság) által meghatározott határoló terület sorokra és oszlopokra van osztva. A határoló Box-fiók szélei két (2) és az oszlopok két (2) oszlopa között. A rendszer a sorok és oszlopok metszéspontjában létrehozott rács csúcsokra vonatkozó jogosultságszint-emeléseket adja vissza. Egyetlen kérelemben legfeljebb 2000 emelés adható vissza.

Ebben a példában a következő sorokat adjuk meg: 3 és Columns = 6. a válasz a 18 jogosultságszint-emelési értéket adja vissza. A következő ábrán a jogosultságszint-emelési értékek a délnyugati saroktól kezdődnek, majd nyugatról keletre és délről északra tartanak.  A jogosultságszint-emelési pontok számozása a visszaadott sorrendben történik.

:::image type="content" source="./media/how-to-request-elevation-data/bounding-box.png" border="false" alt-text="A határolókeret koordinátái a NE és az SE sarkoknál.":::

1. Válassza az **Új** lehetőséget. Az **új létrehozása** ablakban válassza a **kérelem** lehetőséget. Adja meg a **kérelem nevét** , és válasszon ki egy gyűjteményt. Kattintson a **Mentés** gombra.

2. Válassza a http **lekérése** módszert a Builder (szerkesztő) lapon, és adja meg a következő URL-címet. Ehhez a kérelemhez és a cikkben említett egyéb kérelmekhez cserélje le az `{Azure-Maps-Primary-Subscription-key}` elsődleges előfizetési kulcsát.

    ```http
    https://atlas.microsoft.com/elevation/lattice/json?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0&bounds=-121.66853362143818, 46.84646479863713,-121.65853362143818, 46.85646479863713&rows=2&columns=3
    ```

3. Kattintson a kék **Küldés** gombra. 18 jogosultságszint-emelési adatmintákat, egyet a rács minden csúcspontján, a válaszban adja vissza.

    ```json
    {
    "data": [
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66853362143819
            },
            "elevationInMeter": 2298.6581875651746
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66653362143819
            },
            "elevationInMeter": 2306.3980756609963
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66453362143818
            },
            "elevationInMeter": 2279.3385479564113
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66253362143819
            },
            "elevationInMeter": 2233.1549264690366
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66053362143818
            },
            "elevationInMeter": 2196.4485923541492
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.65853362143818
            },
            "elevationInMeter": 2133.1756767157253
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66853362143819
            },
            "elevationInMeter": 2345.3227848228803
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66653362143819
            },
            "elevationInMeter": 2292.2449195443587
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66453362143818
            },
            "elevationInMeter": 2270.5867788258074
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66253362143819
            },
            "elevationInMeter": 2296.8311427390604
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66053362143818
            },
            "elevationInMeter": 2266.0729430891065
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.65853362143818
            },
            "elevationInMeter": 2242.216346631234
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66853362143819
            },
            "elevationInMeter": 2378.460838833359
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66653362143819
            },
            "elevationInMeter": 2327.6761137260387
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66453362143818
            },
            "elevationInMeter": 2208.3782743402949
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66253362143819
            },
            "elevationInMeter": 2106.9526472760981
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66053362143818
            },
            "elevationInMeter": 2054.3270174034078
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.65853362143818
            },
            "elevationInMeter": 2030.6438331110671
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66853362143819
            },
            "elevationInMeter": 2318.753153399402
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66653362143819
            },
            "elevationInMeter": 2253.88875188271
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66453362143818
            },
            "elevationInMeter": 2136.6145845357587
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66253362143819
            },
            "elevationInMeter": 2073.6734467948486
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66053362143818
            },
            "elevationInMeter": 2042.994055784251
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.65853362143818
            },
            "elevationInMeter": 1988.3631481900356
        }
    ]
    }
    ```

## <a name="samples-use-elevation-service-preview-apis-in-azure-maps-control"></a>Minták: jogosultságszint-emelési szolgáltatás (előzetes verzió) API-k használata Azure Maps vezérlőben

### <a name="get-elevation-data-by-coordinate-position"></a>Jogosultságszint-emelési állapot beolvasása koordináta-pozíció alapján

Az alábbi weblap azt mutatja be, hogyan használhatja a Térkép vezérlőelemet a jogosultságszint-emelési információk egy koordináta-ponton való megjelenítéséhez. Amikor a felhasználó húzza a jelölőt, a Térkép felugró ablakban jeleníti meg a jogosultságszint-emelési adathalmazt.

<br/>

<iframe height="500" style="width:100%;" scrolling="no" title="Jogosultságszint-emelés megszerzésének helye" src="https://codepen.io/azuremaps/embed/c840b510e113ba7cb32809591d5f96a2?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
Tekintse meg a Pen <a href='https://codepen.io/azuremaps/pen/c840b510e113ba7cb32809591d5f96a2'>Get jogosultságszint-emelést a pozícióban</a> Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="get-elevation-data-by-bounding-box"></a>Jogosultságszint-emelési adat lekérése határoló mező alapján

A következő weblap azt mutatja be, hogyan használhatja a Térkép vezérlőelemet a határoló mezőben található jogosultságszint-emelési adat megjelenítéséhez. A felhasználó a határoló mezőt a `square` bal felső sarokban lévő ikonra, majd a Térkép tetszőleges pontjára rajzolva határozza meg. A Térkép vezérlőelem ezután a jobb felső sarokban található kulcsban megadott színekkel összhangban jeleníti meg a jogosultságszint-emelési kéréseket.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Jogosultságszint-emelések határoló mező alapján" src="https://codepen.io/azuremaps/embed/619c888c70089c3350a3e95d499f3e48?height=500&theme-id=default&default-tab=js,result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
A CodePen-on lévő Azure Maps () alapján megtekintheti a tollas <a href='https://codepen.io/azuremaps/pen/619c888c70089c3350a3e95d499f3e48'>emeléseket a határolókeret</a> alapján <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'></a>
</iframe>

### <a name="get-elevation-data-by-polyline-path"></a>Jogosultságszint-emelési érték lekérése a vonallánc elérési útján

A következő minta weblap azt mutatja be, hogyan használható a Térkép vezérlőelem a jogosultságszint-emelési adatelérési út mentén. A felhasználó az elérési utat úgy határozza meg `Polyline` , hogy a bal felső sarokban lévő ikonra kattint, és lerajzolja a vonalláncot a térképen. A Térkép vezérlőelem ezután a jobb felső sarokban található kulcsban megadott színekben jeleníti meg a jogosultságszint-emelési adatértékeket.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Jogosultságszint-emelési útvonal gradiense" src="https://codepen.io/azuremaps/embed/7bee08e5cb13d05cb0a11636b60f14ca?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/7bee08e5cb13d05cb0a11636b60f14ca'>emelési útvonalának átmenetét</a> Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Következő lépések

A Azure Maps jogosultságszint-emelési (előnézet) API-k további megismeréséhez lásd:

> [!div class="nextstepaction"]
> [Jogosultságszint-emelés (előzetes verzió) – az adatlekérdezés a lat hosszú koordinátáihoz](/rest/api/maps/elevation/getdataforpoints)

> [!div class="nextstepaction"]
> [Jogosultságszint-emelés (előzetes verzió) – a Határolókerethoz tartozó adat lekérése](/rest/api/maps/elevation/getdataforboundingbox)

> [!div class="nextstepaction"]
> [Jogosultságszint-emelés (előzetes verzió) – a vonalláncra vonatkozó adatlekérdezés](/rest/api/maps/elevation/getdataforpolyline)

> [!div class="nextstepaction"]
> [Render v2 – Térkép lekérése csempe](/rest/api/maps/renderv2)

A Azure Maps REST API-k teljes listájáért lásd:

> [!div class="nextstepaction"]
> [Azure Maps REST API-k](/rest/api/maps/)