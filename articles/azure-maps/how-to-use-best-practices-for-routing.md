---
title: Ajánlott eljárások Azure Maps Route Service Microsoft Azure Maps-ben
description: Megtudhatja, hogyan irányíthatja a járműveket Microsoft Azure Maps Route Service használatával.
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/02/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 8174529def5e3924086e49f36c225f07a4da2648
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2021
ms.locfileid: "99051651"
---
# <a name="best-practices-for-azure-maps-route-service"></a>Ajánlott eljárások Azure Maps Route Service-hez

Azure Maps [Route Service](/rest/api/maps/route) Route Directions és Route Matrix API-k segítségével kiszámíthatja az egyes kért útvonalak becsült érkezési idejét (ETA). Az útválasztási API-k olyan tényezőket vesznek figyelembe, mint például a valós idejű forgalmi információk és a korábbi forgalmi adatok, például a hét kért napjának és a nap időpontjának a szokásos országúti sebessége. Az API-k a legrövidebb vagy leggyorsabb útvonalakat adják vissza több célhelyre egyszerre, sorrendben vagy optimalizált sorrendben, az idő vagy a távolság alapján. A felhasználók speciális útvonalakat és részleteket is igényelhetnek a túrázók, a kerékpárosok és a kereskedelmi járművek, például a teherautók számára. Ebben a cikkben megosztjuk az ajánlott eljárásokat a Azure Maps [Route Service](/rest/api/maps/route)meghívásához, és megismerheti a következőket:

 * Válasszon az útvonalterv API-k és a mátrix-útválasztási API közül
 * A korábbi és várható utazási idők kérése valós idejű és korábbi forgalmi adatok alapján
 * Az útvonal részleteit, például az időt és a távolságot, a teljes útvonal és az útvonal minden egyes szakasza esetén
 * Kereskedelmi jármű, például egy teherautó kérésének útvonala
 * Forgalmi információk kérése az útvonalon, például a dzsemek és az autópályadíj-információk
 * Egy vagy több leállításból (útpontok) álló útvonal igénylése
 * Egy vagy több leállás útvonalának optimalizálása a legjobb megrendelés megszerzéséhez az egyes Leállítás (útpont) megkereséséhez
 * Alternatív útvonalak optimalizálása támogató pontok használatával. Például alternatív útvonalakat is kínálunk, amelyek egy elektromos járművet betöltő állomásnak adnak át.
 * A [Route Service](/rest/api/maps/route) használata a Azure Maps web SDK-val

## <a name="prerequisites"></a>Előfeltételek

1. [Azure Maps fiók létrehozása](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Szerezzen be egy elsődleges előfizetési kulcsot](quick-demo-map-app.md#get-the-primary-key-for-your-account), más néven az elsődleges kulcsot vagy az előfizetési kulcsot.

A Route Service lefedettségével kapcsolatos további információkért tekintse meg az [Útválasztás lefedettségét](routing-coverage.md).

Ez a cikk a [Poster alkalmazást](https://www.postman.com/downloads/) használja a REST-hívások létrehozásához, de bármelyik API-fejlesztési környezetet kiválaszthatja.

## <a name="choose-between-route-directions-and-matrix-routing"></a>Útvonali irányok és mátrix-útválasztás közötti választás

A Route Directions API-k olyan utasításokat adnak vissza, mint az útvonal elérési ideje és koordinátái. Az útvonal-mátrix API lehetővé teszi, hogy kiszámítsa az utazási időt és a távolságot a forrás-és célhelyek által meghatározott útvonalak halmaza számára. A Matrix API minden egyes forrás esetében kiszámítja az Útválasztás (utazási idő és távolság) összegét az adott forrástól az összes megadott célra. Az API-k mindegyike lehetővé teszi olyan paraméterek megadását, mint például a kívánt indulási idő, az érkezés időpontja és a jármű típusa, például autó vagy teherautó. Ennek megfelelően minden valós idejű vagy prediktív forgalmi adatokat a legoptimálisabb útvonalak visszaadására használnak.

Ha a forgatókönyve a következő, érdemes megfontolnia a Route Directions API-k hívását:

* Kérje meg a legrövidebb vagy leggyorsabb vezetési útvonalat két vagy több ismert hely között, hogy pontos érkezési időt kapjon a szállítási járművek számára.
* Részletes útvonal-útmutatás kérése, beleértve az útvonal geometriáját, az útvonalak megjelenítéséhez a térképen
* Az ügyfelek helyeinek listája alapján a lehető legrövidebb útvonalat kell kiszámítani az egyes ügyfelek helyének megkereséséhez és a forráshoz való visszatéréshez. Ezt a forgatókönyvet gyakran az utazási ügynök problémájának nevezzük. Egy kérelemben akár 150 Útpontot (leáll) is megadhat.
* A lekérdezéseket a Route Directions batch API-ba csak egyetlen API-hívással küldheti el.

Ha a forgatókönyve a következő, érdemes megfontolnia a mátrix útválasztási API meghívását:

* Kiszámítja az utazási időt vagy a távolságot az egyes originek és célhelyek között. Tegyük fel például, hogy 12 illesztőprogramja van, és meg kell keresnie a legközelebbi elérhető illesztőprogramot, amely az étteremből származó élelmiszer-szállítást szeretné felvenni.
* A lehetséges útvonalakat a tényleges utazási távolság vagy idő szerint rendezheti. A mátrix API csak az utazási időpontokat és a távolságokat adja vissza az egyes forrás-és rendeltetési kombinációk esetében.
* Az utazási idő vagy távolságok alapján. Például a vállalata rendelkezik 50-alkalmazottakkal, megkeresheti az irodájában 20 perces autóúton belül élő alkalmazottakat.

Íme egy összehasonlítás az Útvonalterv és a mátrix API-k bizonyos képességeinek megjelenítéséhez:

| Azure Maps API | A kérelemben szereplő lekérdezések maximális száma | Területek elkerülése | Tehergépkocsi-és elektromos járművek útválasztása | A fordulópontok és az utazási ügynök optimalizálása | Támogató pontok |
| :--------------: |  :--------------: |  :--------------: | :--------------: | :--------------: | :--------------: |
| Útvonal irányának beolvasása | 1 | | ✔ | ✔ | |
| Útvonal irányának elküldése | 1 | ✔ | ✔ | ✔ | ✔ |
| Útvonal-utasítások kötegének közzététele | 700 | | ✔ | ✔ | |
| Útvonal-mátrix küldése | 700 | | ✔ | | |

Ha többet szeretne megtudni az elektromos járművek útválasztási képességeiről, tekintse meg a következő oktatóanyagot, amely bemutatja, hogyan [irányíthatók az elektromos járművek Azure Notebooks a Python használatával](tutorial-ev-routing.md).

## <a name="request-historic-and-real-time-data"></a>Korábbi és valós idejű adatkérés

Alapértelmezés szerint az útvonal-szolgáltatás azt feltételezi, hogy az utazási mód egy autó, a távozási idő pedig most. Az útvonalat a valós idejű forgalmi feltételek alapján adja vissza, kivéve, ha egy útvonal-számítási kérelem másként nem határoz meg. Rögzített időfüggő forgalmi korlátozások, például a "bal oldali fordulatok nem engedélyezettek a 4:00 PM – 6:00 PM" között, és az útválasztási motor veszi figyelembe. A közúti bezárások (például a Vázszerkezeti) csak akkor lesznek figyelembe véve, ha kifejezetten olyan útvonalat kér, amely figyelmen kívül hagyja az aktuális élő forgalmat. Az aktuális forgalom figyelmen kívül hagyásához állítsa `traffic` az `false` API-kérelemre.

Az útvonal számítási **travelTimeInSeconds** értéke a forgalom miatti késleltetést is tartalmazza. A szolgáltatás a jelenlegi és a korábbi időpontra vonatkozó adatértékek kihasználása alapján jön létre, amikor az indulási idő most értékre van állítva. Ha a jövőben az indulási idő be van állítva, az API-k a korábbi adatértékek alapján előre jelzett utazási időt adnak vissza.

Ha a kérelemben a **computeTravelTimeFor = all** paramétert adja meg, akkor a válasz összefoglaló eleme a következő mezőket fogja tartalmazni, beleértve a korábbi forgalmi feltételeket:

| Elem | Leírás|
| :--- | :--- |
| noTrafficTravelTimeInSeconds | A becsült utazási idő, ha a forgalmi körülmények miatt nem jár késés az útvonalon, például a torlódás miatt |
| historicTrafficTravelTimeInSeconds | Becsült utazási idő kiszámítása időfüggő korábbi forgalmi adatok alapján |
| liveTrafficIncidentsTravelTimeInSeconds | Becsült utazási idő a valós idejű adatátviteli sebesség alapján |

A következő szakaszok bemutatják, hogyan lehet hívásokat kezdeményezni az útválasztási API-kkal a tárgyalt paraméterek használatával.

### <a name="sample-query"></a>Mintalekérdezés

Az indulási idő alatti első példában az írás időpontjában a jövőre van állítva.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&query=51.368752,-0.118332:51.385426,-0.128929&travelMode=car&traffic=true&departAt=2025-03-29T08:00:20&computeTravelTimeFor=all
```

A válasz egy összegző elemet tartalmaz, amely az alábbihoz hasonló. Mivel az indulási idő a jövőre van állítva, a **trafficDelayInSeconds** értéke nulla. A **travelTimeInSeconds** értékének kiszámítása időfüggő korábbi forgalmi adatok alapján történik. Így ebben az esetben a **travelTimeInSeconds** érték egyenlő a **historicTrafficTravelTimeInSeconds** értékkel.

```json
"summary": {
    "lengthInMeters": 2131,
    "travelTimeInSeconds": 248,
    "trafficDelayInSeconds": 0,
    "departureTime": "2025-03-29T08:00:20Z",
    "arrivalTime": "2025-03-29T08:04:28Z",
    "noTrafficTravelTimeInSeconds": 225,
    "historicTrafficTravelTimeInSeconds": 248,
    "liveTrafficIncidentsTravelTimeInSeconds": 248
},
```

### <a name="sample-query"></a>Mintalekérdezés

Az alábbi második példában egy valós idejű útválasztási kérelemünk van, ahol a távozás időpontja jelenleg. Az URL-címben nincs explicit módon megadva, mert ez az alapértelmezett érték.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&query=47.6422356,-122.1389797:47.6641142,-122.3011268&travelMode=car&traffic=true&computeTravelTimeFor=all
```

A válasz egy összegzést tartalmaz, az alábbi ábrán látható módon. A torlódások miatt a **trafficDelaysInSeconds** értéke nagyobb nullánál. A **historicTrafficTravelTimeInSeconds** is nagyobb.

```json
"summary": {
    "lengthInMeters": 16637, 
    "travelTimeInSeconds": 2905, 
    "trafficDelayInSeconds": 1604, 
    "departureTime": "2020-02-28T01:00:20+00:00",
    "arrivalTime": "2020-02-28T01:48:45+00:00", 
    "noTrafficTravelTimeInSeconds": 872, 
    "historicTrafficTravelTimeInSeconds": 1976, 
    "liveTrafficIncidentsTravelTimeInSeconds": 2905 
},
```

## <a name="request-route-and-leg-details"></a>A kérés útvonala és a láb részletei

Alapértelmezés szerint az útvonal-szolgáltatás a koordináták tömbjét fogja visszaadni. A válasz az elérési utat tartalmazó koordinátákat fogja tartalmazni egy nevű listában `points` . Az útvonal válasza az útvonal elejétől és az eltelt időponttól számított távolságot is tartalmazza. Ezek az értékek a teljes útvonal átlagos sebességének kiszámítására használhatók.

Az alábbi képen az `points` elem látható.

![Pontok elem](media/how-to-use-best-practices-for-routing/points-list-is-hidden-img.png)

Bontsa ki az `point` elemet az elérési út koordinátáinak megjelenítéséhez:

![Kibontott pontok elem](media/how-to-use-best-practices-for-routing/points-list-img.png)

Az útválasztási utasítások API-jai különböző, a **instructionsType** paraméter megadásával felhasználható utasítások formátumait támogatják. Az egyszerű számítógép-feldolgozásra vonatkozó utasítások formázásához használja a **instructionsType = kódolt** lehetőséget. Használja a **instructionsType = Tagged** utasítást a felhasználó szövegének megjelenítéséhez. Emellett az utasítások szövegként is formázhatók, ahol az utasítások egyes elemei meg vannak jelölve, az utasítás pedig speciális formázással jelenik meg. További információkért tekintse meg a [támogatott utasításkészlet-típusok listáját](/rest/api/maps/route/postroutedirections#routeinstructionstype).

Ha a rendszer utasításokat kér, a válasz egy új elemet ad vissza `guidance` . Az `guidance` elem két információt tartalmaz: lépésenkénti utasítások és összegzett utasítások.

![Utasítások típusa](media/how-to-use-best-practices-for-routing/instructions-type-img.png)

Az `instructions` elem megtartja az utazás lépésenkénti irányát, és az `instructionGroups` összegzett utasításokkal rendelkezik. Az egyes utasítások összegzése az utazás egy olyan szegmensét fedi le, amely több utat is képes kimutatni. Az API-k egy útvonal szakaszának részleteit adhatják vissza. ilyenek például a forgalmi dugók koordináta-tartománya vagy a forgalom aktuális sebessége.

![Kapcsolja be az utasításokat](media/how-to-use-best-practices-for-routing/instructions-turn-by-turn-img.png)

![Összegzett utasítások](media/how-to-use-best-practices-for-routing/instructions-summary-img.png)

## <a name="request-a-route-for-a-commercial-vehicle"></a>Kereskedelmi jármű útvonalának igénylése

Azure Maps útválasztási API-k támogatják a kereskedelmi járművek útválasztását, a kereskedelmi teherautók útválasztását is beleértve. Az API-k meghatározott korlátokat határoznak meg. Például: a jármű magassága és súlya, valamint ha a jármű veszélyes rakományt szállít. Ha például egy jármű gyúlékony, az útválasztási motor elkerüli a közelében lévő bizonyos alagutakat.

### <a name="sample-query"></a>Mintalekérdezés

Az alábbi minta-kérelem egy kereskedelmi teherautó útvonalát kérdezi le. A kamion az 1. osztályba tartozó veszélyes hulladékot hordozza.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&vehicleWidth=2&vehicleHeight=2&vehicleCommercial=true&vehicleLoadType=USHazmatClass1&travelMode=truck&instructionsType=text&query=51.368752,-0.118332:41.385426,-0.128929
```

Az Route API olyan irányokat ad vissza, amelyek a kamion és a veszélyes hulladék méreteinek megfelelően vannak elhelyezve. Az útvonalra vonatkozó utasításokat az elem kibontásával érheti el `guidance` .

![1. osztályú hazwaste rendelkező kamion](media/how-to-use-best-practices-for-routing/truck-with-hazwaste-img.png)

### <a name="sample-query"></a>Mintalekérdezés

Ha megváltoztatja az USA HAZMAT osztályát, a fenti lekérdezésből eltérő útvonalat fog eredményezni a változáshoz.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&vehicleWidth=2&vehicleHeight=2&vehicleCommercial=true&vehicleLoadType=USHazmatClass9&travelMode=truck&instructionsType=text&query=51.368752,-0.118332:41.385426,-0.128929
```

Az alábbi válasz egy 9. osztályú veszélyes anyagot hordozó teherautóra mutat, amely kevésbé veszélyes, mint az 1. osztályba tartozó veszélyes anyagok. Ha kibontja az `guidance` elemet az utasítások olvasásához, megfigyelheti, hogy az irányok nem egyeznek. Az 1. osztályú veszélyes anyagokhoz több útvonalra vonatkozó útmutatást is talál.



![Tehergépkocsi, 9. osztályú hazwaste](media/how-to-use-best-practices-for-routing/truck-with-hazwaste9-img.png)



## <a name="request-traffic-information-along-a-route"></a>Forgalmi adatok kérése az útvonal mentén

A Azure Maps Route Direction API-kkal a fejlesztők az egyes szakaszok típusait is megadhatják a `sectionType` kérelemben szereplő paraméterrel együtt. Megkérheti például az egyes forgalmi Jam-szegmensek sebességére vonatkozó információkat. Tekintse meg a [sectionType-kulcs értékeinek listáját](/rest/api/maps/route/getroutedirections#sectiontype) , és ismerkedjen meg a kért különféle részletekkel.

### <a name="sample-query"></a>Mintalekérdezés

A következő lekérdezés állítja be a `sectionType` -t `traffic` . A Seattle-ből San Diego-ra irányuló forgalmi adatokat tartalmazó szakaszt kér.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&sectionType=traffic&query=47.6062,-122.3321:32.7157,-117.1611
```

A válasz azokat a szakaszokat tartalmazza, amelyek alkalmasak a forgalomra a megadott koordináták mentén.

![Forgalmi csoportok](media/how-to-use-best-practices-for-routing/traffic-section-type-img.png)

Ezzel a beállítással az alábbi képen látható módon színezheti a pontokat a térkép megjelenítéséhez: 

![A térképen megjelenített színes csoportok](media/how-to-use-best-practices-for-routing/show-traffic-sections-img.png)

## <a name="calculate-and-optimize-a-multi-stop-route"></a>Több megállóval rendelkező útvonal kiszámítása és optimalizálása

Azure Maps jelenleg két útvonal-optimalizálási formát biztosít:

* Optimalizálás a kért útvonal típusa alapján, az útpontok sorrendjének módosítása nélkül. Itt megtalálhatja a [támogatott útvonal-típusokat](/rest/api/maps/route/postroutedirections#routetype)

* Az utazási ügynök optimalizálása, amely megváltoztatja az iránypontok sorrendjét, hogy a lehető legjobb sorrendben keresse meg az egyes leállításokat

A többszörös leállítási útválasztáshoz legfeljebb 150 útpont adható meg egyetlen útvonal-kérelemben. A kezdő és záró koordináta-helyszínek megegyeznek, ahogy az az eset, amikor egy oda-és visszaút is van. Azonban legalább egy további Útpontot meg kell adnia az útvonal kiszámításához. Az útpontok a forrás és a cél koordinátái között adhatók hozzá a lekérdezéshez.

Ha optimalizálni szeretné a legmegfelelőbb sorrendet a megadott útpontok megtekintéséhez, akkor a **computeBestOrder = True** értéket kell megadnia. Ezt a forgatókönyvet az utazási ügynök optimalizálási problémájának is nevezzük.

### <a name="sample-query"></a>Mintalekérdezés

A következő lekérdezés hat útpontok elérési útját kéri le, a `computeBestOrder` paraméter értéke pedig `false` . Ez a paraméter alapértelmezett értéke is `computeBestOrder` .

```http
https://atlas.microsoft.com/route/directions/json?api-version=1.0&subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&computeBestOrder=false&query=47.606544,-122.336502:47.759892,-122.204821:47.670682,-122.120415:47.480133,-122.213369:47.615556,-122.193689:47.676508,-122.206054:47.495472,-122.360861
```

A válasz az elérési út 140 851 méteres hosszát írja le, és az elérési út 9 991 másodpercet vesz igénybe.

![Nem optimalizált válasz](media/how-to-use-best-practices-for-routing/non-optimized-response-img.png)

Az alábbi képen látható a lekérdezésből származó elérési út. Ez az elérési út egyetlen lehetséges útvonal. Nem az optimális elérési út az idő vagy a távolság alapján.

![Nem optimalizált rendszerkép](media/how-to-use-best-practices-for-routing/non-optimized-image-img.png)



Az útvonal útpont sorrendje a következő: 0, 1, 2, 3, 4, 5 és 6.

### <a name="sample-query"></a>Mintalekérdezés

A következő lekérdezés ugyanazt a hat fordulópontot kéri le, ahogy a fenti mintában. Ezúttal a `computeBestOrder` paraméter értéke `true` (az utazó ügynök optimalizálása).

```http
https://atlas.microsoft.com/route/directions/json?api-version=1.0&subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&computeBestOrder=true&query=47.606544,-122.336502:47.759892,-122.204821:47.670682,-122.120415:47.480133,-122.213369:47.615556,-122.193689:47.676508,-122.206054:47.495472,-122.360861
```

A válasz az elérési út 91 814 méteres hosszát írja le, és az elérési út 7 797 másodpercet vesz igénybe. Az utazási távolság és az utazási idő is alacsonyabb, mert az API az optimalizált útvonalat adta vissza.

![Optimalizált válasz](media/how-to-use-best-practices-for-routing/optimized-response-img.png)

Az alábbi képen látható a lekérdezésből származó elérési út.

![Optimalizált rendszerkép](media/how-to-use-best-practices-for-routing/optimized-image-img.png)

Az optimális útvonal a következő útpont sorrendtel rendelkezik: 0, 5, 1, 2, 4, 3 és 6.

>[!TIP]
> Az útválasztási szolgáltatás optimalizált útpontok-sorrendi adatai az indexek készletét biztosítják. Ezek kizárják a forrás és a cél indexeket. Ezeket az értékeket 1 értékkel kell növelni a forráshoz. Ezután adja hozzá a célhelyet a végéhez a teljes rendezett útpont lista lekéréséhez.

## <a name="calculate-and-bias-alternative-routes-using-supporting-points"></a>Alternatív útvonalak kiszámítása és torzítása a támogatási pontok használatával

Előfordulhat, hogy olyan helyzetekre van szüksége, amelyekben a viszonyítási útvonal nulla vagy több alternatív útvonalának kiszámításához egy útvonalat szeretne létrehozni. Előfordulhat például, hogy meg szeretné jeleníteni az ügyfelek alternatív útvonalait, amelyek átadják a kiskereskedelmi áruházat. Ebben az esetben egy helyet kell megtorzítani a támogatási pontok használatával. Itt láthatók a helyek torzításának lépései:

1. Útvonal kiszámításának módja, és az útvonal válaszának lekérése
2. Használja az útvonal elérési útját, hogy megkeresse a kívánt helyszíneket az útvonal elérési útja mentén vagy annak közelében. Használhatja például Azure Maps [Point of érdekes API](/rest/api/maps/search/getsearchpoi) -t, vagy lekérdezheti saját adatait az adatbázisban.  
3. A helyszínek sorrendje az útvonal elejétől mért távolság alapján
4. Adja hozzá ezeket a helyeket a támogatási pontokként egy új útvonal-kérelemben az [útvonal-utasítások API](/rest/api/maps/route/postroutedirections)-hoz. A támogatási pontokkal kapcsolatos további tudnivalókért tekintse meg az [Route Directions API dokumentációját](/rest/api/maps/route/postroutedirections#supportingpoints). 

Az útvonalterv [utáni API](/rest/api/maps/route/postroutedirections)meghívásakor megadhatja a minimális eltérési időt vagy a távolsági megkötéseket, valamint a támogatási pontokat. Akkor használja ezeket a paramétereket, ha alternatív útvonalakat szeretne ajánlani, de az utazási időt is korlátozni szeretné. Ha ezeket a korlátozásokat használják, az alternatív útvonalak az adott időpontra vagy távolságra vonatkozóan a kezdőponttól érkező hivatkozási útvonalat fogják követni. Ez azt jelenti, hogy a többi útvonal a megadott megkötések alapján eltér a hivatkozási útvonaltól.

Az alábbi kép egy példát mutat be alternatív útvonalak megjelenítésére az idő és a távolság megadott eltérési korlátaival.

![Alternatív útvonalak](media/how-to-use-best-practices-for-routing/alternative-routes-img.png)

## <a name="use-the-routing-service-in-a-web-app"></a>Az útválasztási szolgáltatás használata egy webalkalmazásban

A Azure Maps web SDK egy [szolgáltatási modult](/javascript/api/azure-maps-rest/)biztosít. Ez a modul egy segítő könyvtár, amely megkönnyíti a Azure Maps REST API-k használatát a web-és Node.js-alkalmazásokban JavaScript vagy írógéppel használatával. A szolgáltatás modul a visszaadott útvonalak megjelenítéséhez használható a térképen. A modul automatikusan meghatározza, hogy melyik API-t használja a GET és a POST kérésekhez.

## <a name="next-steps"></a>Következő lépések

További információt a következő témakörben talál:

> [!div class="nextstepaction"]
> [Azure Maps Route szolgáltatás](/rest/api/maps/route)

> [!div class="nextstepaction"]
> [A szolgáltatás moduljának használata](./how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Útvonal megjelenítése a térképen](./map-route.md)

> [!div class="nextstepaction"]
> [Azure Maps NPM-csomag](https://www.npmjs.com/package/azure-maps-rest  )
