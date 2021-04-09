---
title: Hely keresése Azure Maps Search Services használatával
description: További információ a Azure Maps Search szolgáltatásról. Tekintse meg, hogyan használhatók ezen API-k a helymeghatározáshoz, a fordított helymeghatározáshoz, a zavaros keresések és a fordított keresztek közötti keresésekhez.
author: anastasia-ms
ms.author: v-stharr
ms.date: 01/19/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: dddf56edf2037d87a28589a59834db32f8d04a4c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98598362"
---
# <a name="search-for-a-location-using-azure-maps-search-services"></a>Hely keresése Azure Maps Search Services használatával

A [Azure Maps Search Service](/rest/api/maps/search) olyan REST API-k készlete, amelyek segítségével a fejlesztők megkereshetik a címeket, a helyeket és az üzleti listákat név, kategória és egyéb földrajzi információk alapján. A hagyományos helymeghatározáshoz-támogatás mellett a szolgáltatások a földrajzi szélesség és a hosszúságok alapján is visszafordítják a címeket és a geocode. A keresés által visszaadott szélességi és hosszúsági értékek más Azure Maps szolgáltatásokban, például [útvonal](/rest/api/maps/route) -és [időjárási](/rest/api/maps/weather) szolgáltatásokban is használhatók paraméterként.


Ebből a cikkből megtudhatja, hogyan:

* A [keresési címek API]( https://docs.microsoft.com/rest/api/maps/search/getsearchaddress)-val egy adott címnek (geocode) a szélességi és hosszúsági koordinátáit kéri le.
* Keressen egy címeket vagy érdekes pontot (POI) a [fuzzy Search API](/rest/api/maps/search/getsearchfuzzy)használatával.
* [Fordított címtartomány-kereséssel](/rest/api/maps/search/getsearchaddressreverse) fordítsa le a koordináta helyét az utca címeként.
* A koordinációs helyet egy emberi, érthető Cross Street-re fordíthatja, ha a [keresési címeket fordított Cross Street API](/rest/api/maps/search/getsearchaddressreversecrossstreet)-t használja.  Ez leggyakrabban olyan alkalmazások nyomon követéséhez szükséges, amelyek egy eszközről vagy eszközről érkező GPS-hírcsatornát fogadnak, és szeretnék tudni, hol található a koordináta.

## <a name="prerequisites"></a>Előfeltételek

1. [Azure Maps fiók létrehozása](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Szerezzen be egy elsődleges előfizetési kulcsot](quick-demo-map-app.md#get-the-primary-key-for-your-account), más néven az elsődleges kulcsot vagy az előfizetési kulcsot.

Ez az oktatóanyag a [Poster](https://www.postman.com/) alkalmazást használja, de más API-fejlesztési környezetet is választhat.

## <a name="request-latitude-and-longitude-for-an-address-geocoding"></a>Címek szélességének és hosszúságának kérése (helymeghatározáshoz)

Ebben a példában a Azure Maps [keresési címek API](/rest/api/maps/search/getsearchaddress) -t használjuk a címek szélességi és hosszúsági koordinátákba való átalakításához. Ezt a folyamatot *helymeghatározáshoz* is nevezik. A koordináták visszaadásán kívül a válasz részletes cím tulajdonságokat is ad vissza, például az utcát, az irányítószámot, a helyhatóságot és az ország/régió adatait.

>[!TIP]
>Ha a geocode címei vannak, a [keresési cím KÖTEGELT API](/rest/api/maps/search/postsearchaddressbatch) használatával egyetlen API-hívásban küldheti el a lekérdezések kötegét.

1. Nyissa meg a Poster alkalmazást. A Poster alkalmazás teteje közelében válassza az **új** lehetőséget. Az **új létrehozása** ablakban válassza a **gyűjtemény** elemet.  Nevezze el a gyűjteményt, és válassza a **Létrehozás** gombot. Ezt a gyűjteményt a jelen dokumentum többi példájában is használhatja.

2. A kérelem létrehozásához válassza az **új** újra lehetőséget. Az **új létrehozása** ablakban válassza a **kérelem** lehetőséget. Adja meg a kérelem **nevét** . Válassza ki az előző lépésben létrehozott gyűjteményt, majd kattintson a **Mentés** gombra.

3. Válassza a http **lekérése** módszert a Builder (szerkesztő) lapon, és adja meg a következő URL-címet. Ebben a kérésben egy adott címeket keresünk: `400 Braod St, Seattle, WA 98109` . Ehhez a kérelemhez és a cikkben említett egyéb kérelmekhez cserélje le az `{Azure-Maps-Primary-Subscription-key}` elsődleges előfizetési kulcsát.

    ```http
    https://atlas.microsoft.com/search/address/json?&subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0&language=en-US&query=400 Broad St, Seattle, WA 98109
    ```

4. Kattintson a kék **Küldés** gombra. A válasz törzse egyetlen helyhez tartozó adategységeket fog tartalmazni.

5. Most egy olyan címen fogunk keresni, amely egynél több lehetséges hellyel rendelkezik. A **params (paraméterek** ) szakaszban módosítsa a kulcsot a következőre: `query` `400 Broad, Seattle` . Kattintson a kék **Küldés** gombra.

    :::image type="content" source="./media/how-to-search-for-address/search-address.png" alt-text="Keresés a címben":::

6. Ezután próbálja meg beállítani a `query` kulcsot `400 Broa` .

7. Kattintson a **Küldés** gombra. Láthatja, hogy a válasz több országból származó válaszokat is tartalmaz. Ahhoz, hogy a felhasználók a megfelelő területen geobias az eredményeket, mindig adja hozzá a kéréshez a lehető legtöbb helyet.

## <a name="using-fuzzy-search-api"></a>A fuzzy Search API használata

A Azure Maps [fuzzy Search API](/rest/api/maps/search/getsearchfuzzy) támogatja a standard egysoros és a szabad formátumú kereséseket. Javasoljuk, hogy használja a Azure Maps Search fuzzy API-t, ha nem tudja, hogy a felhasználó bemeneti típusa keresési kérelem.  A lekérdezés bemenete lehet teljes vagy részleges címe. Ez lehet egy érdekes (POI) token, például a POI neve, a POI-kategória vagy a márka neve. Továbbá a keresési eredmények relevanciájának javítása érdekében a lekérdezési eredményeket a koordináta helye és a sugár is korlátozhatja, vagy egy határolókeret definiálásával.

>[!TIP]
>A legtöbb keresési lekérdezés alapértelmezett értéke maxFuzzyLevel = 1 a teljesítmény eléréséhez és a szokatlan eredmények csökkentése érdekében. A bizonytalanság szintje a `maxFuzzyLevel` vagy paraméterrel állítható be `minFuzzyLevel` . További információ a `maxFuzzyLevel` és az összes választható paraméter teljes listájáról: [intelligens keresési URI-paraméterek](/rest/api/maps/search/getsearchfuzzy#uri-parameters)

### <a name="search-for-an-address-using-fuzzy-search"></a>Címek keresése a fuzzy Search használatával

Ebben a példában a alkalmazásban a teljes világ keresésére használjuk a fuzzy Search kifejezést `pizza` .  Ezután bemutatjuk, hogyan kereshet egy adott ország hatókörén. Végezetül megmutatjuk, hogyan használhatók a koordináta-helyek és a RADIUS a keresés hatókörére egy adott területen, valamint a visszaadott eredmények számának korlátozása.

>[!IMPORTANT]
>Ahhoz, hogy a felhasználók a megfelelő területen geobias az eredményeket, mindig adja hozzá a lehető legtöbb helyet. További információ: [ajánlott eljárások a kereséshez](how-to-use-best-practices-for-search.md#geobiased-search-results).

1. Nyissa meg a Poster alkalmazást, kattintson az **új** elemre, majd válassza a **kérelem** lehetőséget. Adja meg a kérelem **nevét** . Válassza ki az előző szakaszban létrehozott gyűjteményt, vagy hozzon létre egy újat, majd válassza a **Mentés** lehetőséget.

2. Válassza a http **lekérése** módszert a Builder (szerkesztő) lapon, és adja meg a következő URL-címet. Ehhez a kérelemhez és a cikkben említett egyéb kérelmekhez cserélje le az `{Azure-Maps-Primary-Subscription-key}` elsődleges előfizetési kulcsát.

    ```http
   https://atlas.microsoft.com/search/fuzzy/json?&api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}&language=en-US&query=pizza
    ```

    >[!NOTE]
    >Az URL-cím alatt található _JSON_ -attribútum meghatározza a válasz formátumát. Ez a cikk a JSON-t használja a könnyű használat és az olvashatóság érdekében. Más támogatott formátumok kereséséhez tekintse meg a `format` paraméter definícióját az [URI-paraméter dokumentációjában](/rest/api/maps/search/getsearchfuzzy#uri-parameters).

3. Kattintson a **Küldés** gombra, és tekintse át a válasz törzsét.

    A "pizza" nem egyértelmű lekérdezési karakterlánca a "pizza" és az "étterem" kategóriákban 10 hasznos [eredményt](/rest/api/maps/search/getsearchpoi#searchpoiresponse) adott vissza (POI). Minden eredmény olyan adatokat tartalmaz, mint például az utca címe, a szélesség és a hosszúság, a port megtekintése és a belépési pontok a helyhez. Az eredmények mostantól megváltozhatnak ehhez a lekérdezéshez, és nincsenek a hivatkozási helyekhez kötve.
  
    A következő lépésben a `countrySet` paraméterrel csak azokat az országokat/régiókat fogjuk megadni, amelyeknek az alkalmazásának lefedettségre van szüksége. A támogatott országok/régiók teljes listájáért tekintse meg a [Keresés a lefedettséggel](./geocoding-coverage.md)foglalkozó témakört.

4. Az alapértelmezett viselkedés a teljes világon való keresés, ami felesleges eredményeket ad vissza. Ezután a pizzát csak a Egyesült Államok fogjuk keresni. Adja hozzá a `countrySet` kulcsot a **Paraméterek** szakaszhoz, és állítsa be a értékét a következőre: `US` . A `countrySet` kulcs beállítása, hogy az `US` eredményeket a Egyesült Államokhoz kösse.

    :::image type="content" source="./media/how-to-search-for-address/search-fuzzy-country.png" alt-text="Pizza keresése a Egyesült Államokban":::

    Az eredmények mostantól az országkód szerint vannak kötve, és a lekérdezés a Egyesült Államok pizzás éttermeit adja vissza.

5. Ha még több célzott keresést szeretne kapni, megkeresheti a lat./Lon. hatókörét. koordináta pár. Ebben a példában a lat./Lon. fogjuk használni. a Seattle Space tű. Mivel csak a 400-es sugarú RADIUS-n belüli eredményeket szeretnénk visszaadni, hozzáadjuk a `radius` paramétert. Emellett hozzáadjuk a `limit` paramétert az eredményeknek az öt legközelebbi pizza-helyre való korlátozásához.

    A **params (paraméterek** ) szakaszban adja hozzá a következő kulcs/érték párokat:

     | Kulcs | Érték |
    |-----|------------|
    | Lat | 47,620525 |
    | Lon | – 122,349274 |
    | RADIUS- | 400 |
    | korlát | 5|

6. Kattintson a **Küldés** gombra. A válasz a Seattle Space tű közelében található pizza-éttermek eredményeit is tartalmazza.

## <a name="search-for-a-street-address-using-reverse-address-search"></a>Házszám keresése fordított címek használatával

A Azure Maps [keresési cím fordított API]( https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) lefordítja a koordinátákat az emberi olvasási utcai címekre. Ezt az API-t gyakran használják olyan alkalmazások számára, amelyek GPS-hírcsatornákat használnak, és meghatározott koordináta-pontokon szeretnének felderíteni a címeket.

>[!IMPORTANT]
>Ahhoz, hogy a felhasználók a megfelelő területen geobias az eredményeket, mindig adja hozzá a lehető legtöbb helyet. További információ: [ajánlott eljárások a kereséshez](how-to-use-best-practices-for-search.md#geobiased-search-results).

>[!TIP]
>Ha a geocode több koordináta-hellyel rendelkezik, a [post Search reverse batch API](/rest/api/maps/search/postsearchaddressreversebatch) használatával egyetlen API-hívással küldhet le lekérdezési köteget.

Ebben a példában az elérhető választható paraméterek közül néhányat visszafordítunk. A választható paraméterek teljes listájáért lásd: [fordított keresési paraméterek](/rest/api/maps/search/getsearchaddressreverse#uri-parameters).

1. A Poster alkalmazásban kattintson az **új** elemre, majd válassza a **kérelem** lehetőséget. Adja meg a kérelem **nevét** . Válassza ki az első szakaszban létrehozott gyűjteményt, vagy hozzon létre egy újat, majd válassza a **Mentés** lehetőséget.

2. Válassza a http **lekérése** módszert a Builder (szerkesztő) lapon, és adja meg a következő URL-címet. Ehhez a kérelemhez és a cikkben említett egyéb kérelmekhez cserélje le az `{Azure-Maps-Primary-Subscription-key}` elsődleges előfizetési kulcsát. A kérelemnek a következő URL-címhez hasonlóan kell kinéznie:

    ```http
    https://atlas.microsoft.com/search/address/reverse/json?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}&language=en-US&query=47.591180,-122.332700&number=1
    ```

3. Kattintson a **Küldés** gombra, és tekintse át a válasz törzsét. Egy lekérdezési eredménynek kell megjelennie. A válasz tartalmazza a Safeco mező legfontosabb információit.
  
4. Most hozzáadjuk a következő kulcs/érték párokat a params ( **Paraméterek** ) szakaszhoz:

    | Kulcs | Érték | Válaszok
    |-----|------------|------|
    | szám | 1 |A válasz tartalmazhat az utca oldalát (balra/jobbra), valamint a szám eltolási pozícióját is.|
    | returnSpeedLimit | true | A címen lévő sebességkorlátozás visszaadását adja vissza.|
    | returnRoadUse | true | A következő címen adja vissza a közúti használat típusait:. Az összes lehetséges közúti használati típushoz lásd: [országúti használat típusai](/rest/api/maps/search/getsearchaddressreverse#uri-parameters).|
    | returnMatchType | true| A egyezés típusát adja vissza. A lehetséges értékekért lásd: [keresési eredmények fordított címe](/rest/api/maps/search/getsearchaddressreverse#searchaddressreverseresult)

   :::image type="content" source="./media/how-to-search-for-address/search-reverse.png" alt-text="Keresés fordított.":::

5. Kattintson a **Küldés** gombra, és tekintse át a válasz törzsét.

6. Ezután adja hozzá a `entityType` kulcsot, és állítsa be a értékét a következőre: `Municipality` . A `entityType` kulcs felülbírálja a `returnMatchType` kulcsot az előző lépésben. Emellett el kell távolítani `returnSpeedLimit` a-t és a-t is, `returnRoadUse` mivel adatokat kérnek a településről.  Az összes lehetséges entitás típusával kapcsolatban lásd: [entitások típusai](/rest/api/maps/search/getsearchaddressreverse#entitytype).

    :::image type="content" source="./media/how-to-search-for-address/search-reverse-entity-type.png" alt-text="Keresés fordított entityType.":::

7. Kattintson a **Küldés** gombra. Hasonlítsa össze az eredményeket az 5. lépésben visszaadott eredményekkel.  Mivel a kért entitás típusa már megtörtént `municipality` , a válasz nem tartalmazza az utca címe információit. Emellett a visszaadott `geometryId` érték a határ sokszög kérésére is használható Azure Maps Get [Search sokszög API](/rest/api/maps/search/getsearchpolygon)-val.

>[!TIP]
>Ha további információkat szeretne megtudni ezekről a paraméterekről, valamint a másokkal kapcsolatos további információkért lásd a [fordított keresési paraméterek szakaszt](/rest/api/maps/search/getsearchaddressreverse#uri-parameters).

## <a name="search-for-cross-street-using-reverse-address-cross-street-search"></a>A Cross Street keresése fordított címekkel Cross Street Search használatával

Ebben a példában a címek koordinátái alapján keresünk egy kereszt utcát.

1. A Poster alkalmazásban kattintson az **új** elemre, majd válassza a **kérelem** lehetőséget. Adja meg a kérelem **nevét** . Válassza ki az első szakaszban létrehozott gyűjteményt, vagy hozzon létre egy újat, majd válassza a **Mentés** lehetőséget.

2. Válassza a http **lekérése** módszert a Builder (szerkesztő) lapon, és adja meg a következő URL-címet. Ehhez a kérelemhez és a cikkben említett egyéb kérelmekhez cserélje le az `{Azure-Maps-Primary-Subscription-key}` elsődleges előfizetési kulcsát. A kérelemnek a következő URL-címhez hasonlóan kell kinéznie:
  
    ```http
   https://atlas.microsoft.com/search/address/reverse/crossstreet/json?&api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}&language=en-US&query=47.591180,-122.332700
    ```

    :::image type="content" source="./media/how-to-search-for-address/search-address-cross.png" alt-text="Keresés a Cross Streeten.":::
  
3. Kattintson a **Küldés** gombra, és tekintse át a válasz törzsét. Figyelje meg, hogy a válasz tartalmazza a `crossStreet` értékét `South Atlantic Street` .

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Azure Maps Search Service REST API](/rest/api/maps/search)

> [!div class="nextstepaction"]
> [Azure Maps Search Service ajánlott eljárások](how-to-use-best-practices-for-search.md)