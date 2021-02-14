---
title: Oktatóanyag – webszolgáltatások migrálása a Google Maps szolgáltatásból | Microsoft Azure térképek
description: Útmutató webszolgáltatások áttelepítéséhez a Google Maps szolgáltatásból Microsoft Azure Mapsbe
author: rbrundritt
ms.author: richbrun
ms.date: 08/19/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 04bbb0465f26e82d247c53e7393c4476b9d36fde
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100386849"
---
# <a name="tutorial-migrate-web-service-from-google-maps"></a>Oktatóanyag: webszolgáltatás migrálása a Google Mapsből

Az Azure és a Google Maps egyaránt hozzáférést biztosít a térbeli API-khoz a REST-alapú webszolgáltatásokon keresztül. Ezeknek a platformoknak a API-felületei hasonló funkciókat hajtanak végre. Azonban mindegyik különböző elnevezési konvenciókat és válasz objektumokat használ.

Az oktatóanyag során a következőket fogja elsajátítani:

> [!div class="checklist"]
> * Helymeghatározáshoz továbbítása és sztornírozása
> * Hasznos helyek keresése
> * Útvonalak és irányok kiszámítása
> * Térkép rendszerképének beolvasása
> * Távolsági mátrix kiszámítása
> * Időzóna részleteinek beolvasása

Emellett a következőket is megismerheti:

> [!div class="checklist"]
> * Azure Maps REST szolgáltatás a Google Maps webszolgáltatásból való Migrálás során
> * Tippek a Azure Maps-szolgáltatások legtöbbje számára
> * Egyéb kapcsolódó Azure Maps-szolgáltatások betekintése

A táblázat megjeleníti a Azure Maps Service API-kat, amelyek hasonló funkcióval rendelkeznek a felsorolt Google Maps Service API-khoz.

| Google Maps szolgáltatás API | Azure Maps Service API                                                                      |
|-------------------------|---------------------------------------------------------------------------------------------|
| Útmutatás              | [Útvonal](/rest/api/maps/route)                                     |                         
| Távolsági mátrix         | [Útvonal-mátrix](/rest/api/maps/route/postroutematrixpreview)       |                         
| Geokódolás               | [Search](/rest/api/maps/search)                                   |                         
| Helyek keresése           | [Search](/rest/api/maps/search)                                   |                         
| Automatikus kiegészítés      | [Search](/rest/api/maps/search)                                   |                         
| Elérési út            | Lásd: [útvonalak és utasítások kiszámítása](#calculate-routes-and-directions) szakasz.            
| Sebességkorlátozások            | Lásd: [koordináta szakasz fordított geocode](#reverse-geocode-a-coordinate) .                  
| Statikus Térkép              | [Megjelenítés](/rest/api/maps/render/getmapimage)                       |                         
| Időzóna               | [Időzóna](/rest/api/maps/timezone)                              |                         
| Jogosultságszint               | [Jogosultságszint-emelés (előzetes verzió)](/rest/api/maps/elevation)                   |                         |

A következő szolgáltatási API-k jelenleg nem érhetők el Azure Mapsban:

- Térinformatikai-Azure Maps rendelkezik egy térinformatikai szolgáltatással, de IP-címet biztosít a helyadatok számára, de jelenleg nem támogatja a Cell Tower vagy a Wi-Fi-háromszögárfolyam-használatot.
- Helyek részletei és fényképek – a telefonszámok és a webhely URL-címe a Azure Maps Search API-ban érhető el.
- Térkép URL-címei
- Legközelebbi utak – ez elérhető a web SDK-val, ahogy az [itt](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Basic%20snap%20to%20road%20logic)látható, de jelenleg nem érhető el szolgáltatásként.
- Statikus utcai nézet

Azure Maps számos további REST-webszolgáltatással rendelkezik, amelyek érdekesek lehetnek:

- [Térbeli műveletek](/rest/api/maps/spatial): összetett térbeli számítások és műveletek (például geokerítések) kiszervezése szolgáltatáshoz.
- [Forgalom](/rest/api/maps/traffic): a valós idejű forgalom és az incidensek adatforgalmának elérése.

## <a name="prerequisites"></a>Előfeltételek

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/).
2. [Azure Maps fiók létrehozása](quick-demo-map-app.md#create-an-azure-maps-account)
3. [Szerezzen be egy elsődleges előfizetési kulcsot](quick-demo-map-app.md#get-the-primary-key-for-your-account), más néven az elsődleges kulcsot vagy az előfizetési kulcsot. A Azure Maps-hitelesítéssel kapcsolatos további információkért lásd: a [Azure Maps hitelesítés kezelése](how-to-manage-authentication.md).

## <a name="geocoding-addresses"></a>Helymeghatározáshoz-címek

A helymeghatározáshoz a címek koordinátabe alakításának folyamata. Az "1 Microsoft Way, Redmond, WA" érték például a következőre konvertálja a hosszúságot:-122,1298, szélesség: 47,64005. Ezt követően a koordináták különböző típusú célokra használhatók, például a Térkép középpontba helyezi a jelölőt.

A Azure Maps számos módszert biztosít a helymeghatározáshoz-címekhez:

- [**Szabad formátumú helymeghatározáshoz**](/rest/api/maps/search/getsearchaddress): egyetlen címnek megfelelő karakterláncot kell megadni, és azonnal fel kell dolgozni a kérést. az "1 Microsoft Way, Redmond, WA" egy példa egyetlen címtartomány-karakterláncra. Ez az API akkor javasolt, ha gyorsan kell geocode az egyes címeket.
- [**Strukturált helymeghatározáshoz**](/rest/api/maps/search/getsearchaddressstructured): egyetlen cím részeit adja meg, például az utca nevét, a várost, az országot/régiót és az irányítószámot, és azonnal feldolgozza a kérést. Ez az API akkor javasolt, ha gyorsan kell geocode az egyes címeket, és az adatai már az egyes címekre is bekerülnek.
- [**Batch-helymeghatározáshoz**](/rest/api/maps/search/postsearchaddressbatchpreview): hozzon létre egy legfeljebb 10 000 címet tartalmazó kérelmet, és egy adott időszakban dolgozza fel azokat. A rendszer az összes címet párhuzamosan geokódolva a kiszolgálón, és amikor elkészült, a teljes eredményhalmazt letöltheti. Ez a nagyméretű adatkészletek helymeghatározáshoz ajánlott.
- [**Fuzzy keresés**](/rest/api/maps/search/getsearchfuzzy): ez az API ötvözi a helymeghatározáshoz a keresett ponttal. Ez az API egy szabad formátumú karakterláncot vesz igénybe. Ez a karakterlánc lehet a címe, a hely, a mérföldkő, a hasznos hely vagy az érdeklődési pont kategóriája. Ez az API közel valós időben dolgozza fel a kérelmet. Ez az API olyan alkalmazások esetében ajánlott, amelyekben a felhasználók egy adott szövegmezőben keresik meg a címeket vagy érdekes pontokat.
- [**Fuzzy batch-keresés**](/rest/api/maps/search/postsearchfuzzybatchpreview): hozzon létre egy olyan kérést, amely legfeljebb 10 000 címet, helyet, tereptárgyat vagy érdeklődési pontot tartalmaz, és egy adott időszakban feldolgozható. Az összes adat párhuzamosan lesz feldolgozva a kiszolgálón, és ha elkészült, a teljes eredményhalmaz letölthető.

A következő táblázat kereszthivatkozásokat hivatkozik a Google Maps API-paraméterekre a Azure Maps hasonló API-paramétereivel.

| Google Maps API-paraméter | Hasonló Azure Maps API-paraméter  |
|---------------------------|--------------------------------------|
| `address`                   | `query`                            |
| `bounds`                    | `topLeft` és `btmRight`           |
| `components`                | `streetNumber`<br/>`streetName`<br/>`crossStreet`<br/>`postalCode`<br/>`municipality` – város/város<br/>`municipalitySubdivision` – szomszédság, Sub/Super City<br/>`countrySubdivision` -állam vagy megye<br/>`countrySecondarySubdivision` -County<br/>`countryTertiarySubdivision` -District<br/>`countryCode` -két betű ország/régió kódja |
| `key`                       | `subscription-key` – Lásd még a [hitelesítés Azure Maps](azure-maps-authentication.md) a dokumentációt. |
| `language`                  | `language` – Lásd a [támogatott nyelvek](supported-languages.md) dokumentációját.  |
| `region`                    | `countrySet`                       |

A Search szolgáltatás használatának példáját [itt](how-to-search-for-address.md)találja. Mindenképpen tekintse át [az ajánlott eljárásokat a kereséshez](how-to-use-best-practices-for-search.md).

> [!TIP]
> A helymeghatározáshoz és a fuzzy keresési API-k a `&typeahead=true` kérelem URL-címéhez való hozzáadásával automatikus kiegészítési módban is használhatók. Ez azt közli a kiszolgálóval, hogy a bemeneti szöveg valószínűleg részleges, és a keresés a prediktív módba lép.

## <a name="reverse-geocode-a-coordinate"></a>Koordináta fordított geocode

A fordított helymeghatározáshoz az a folyamat, amellyel a földrajzi koordinátákat egy hozzávetőleges címmé alakíthatja. Koordináták a "hosszúság:-122,1298, Latitude: 47,64005" értékre váltás "1 Microsoft Way, Redmond, WA" értékűre.

Azure Maps több fordított helymeghatározáshoz módszert biztosít:

- [**Fordított geocoder**](/rest/api/maps/search/getsearchaddressreverse): egyetlen földrajzi koordináta megadásával lekérheti a koordinátanak megfelelő hozzávetőleges címeket. Feldolgozza a kérést közel valós időben.
- [**Cross Street fordított geocoder**](/rest/api/maps/search/getsearchaddressreversecrossstreet): egyetlen földrajzi koordinátat határozhat meg a közeli Cross Street-információk eléréséhez és a kérés azonnali feldolgozásához. Előfordulhat például, hogy a következő Cross Streets, az első Ave és a Main St.
- [**Fordított geocoder**](/rest/api/maps/search/postsearchaddressreversebatchpreview): hozzon létre egy legfeljebb 10 000 koordinátákat tartalmazó kérelmet, amely egy adott időszakban feldolgozható. A rendszer minden adatfeldolgozást párhuzamosan dolgoz fel a kiszolgálón. A kérelem befejezésekor letöltheti a teljes eredményt.

Ez a táblázat a Google Maps API paramétereit a Azure Maps hasonló API-paramétereivel hivatkozik.

| Google Maps API-paraméter   | Hasonló Azure Maps API-paraméter   |
|-----------------------------|---------------------------------------|
| `key`                       | `subscription-key` – Lásd még a [hitelesítés Azure Maps](azure-maps-authentication.md) a dokumentációt. |
| `language`                  | `language` – Lásd a [támogatott nyelvek](supported-languages.md) dokumentációját.  |
| `latlng`                    | `query`  |
| `location_type`             | *N/A*     |
| `result_type`               | `entityType`    |

Tekintse át [az ajánlott eljárásokat a kereséshez](how-to-use-best-practices-for-search.md).

A Azure Maps fordított helymeghatározáshoz API további funkciókkal rendelkezik, amelyek nem érhetők el a Google Maps szolgáltatásban. Ezek a funkciók hasznosak lehetnek az alkalmazással való integrációhoz, amikor az alkalmazást áttelepíti:

* Sebességkorlátozó-adatlekérdezés
* Közúti használati információk beolvasása: helyi út, artériás, korlátozott hozzáférés, rámpa stb.
* Az utca azon oldalának lekérése, amelyen a koordináta található

## <a name="search-for-points-of-interest"></a>Hasznos helyek keresése

A helyek keresési API-ját a Google Maps szolgáltatásban keresheti meg. Ez az API három különböző módszert biztosít a fontos helyek kereséséhez:

* **Hely keresése a következő szövegből:** Megkeresi az érdeklődési pontot a neve, címe vagy telefonszáma alapján.
* **Közeli keresés**: megkeresi azokat az érdeklődési pontokat, amelyek egy adott távolságon belül vannak.
* **Szöveges keresés:** Megkeresi a helyeket egy szabad formátumú szöveggel, amely az érdeklődési pont és a hely adatait is tartalmazza. Például: "pizza a New York-ban" vagy "éttermek a Main St közelében".

Azure Maps számos keresési API-t biztosít a hasznos helyek számára:

- [**POI-keresés**](/rest/api/maps/search/getsearchpoi): érdeklődési pontok keresése név alapján. Például: "Starbucks".
- [**POI-kategória keresése**](/rest/api/maps/search/getsearchpoicategory): az érdeklődési pontok kategória szerint kereshetők. Például: "étterem".
- [**Közeli keresés**](/rest/api/maps/search/getsearchnearby): megkeresi azokat az érdeklődési pontokat, amelyek egy adott távolságon belül vannak.
- [**Fuzzy keresés**](/rest/api/maps/search/getsearchfuzzy): ez az API ötvözi a helymeghatározáshoz a keresett ponttal. Ez az API egy olyan szabad formátumú karakterláncot vesz igénybe, amely lehet egy lakcím, egy hely, egy mérföldkő, egy érdekes hely vagy egy érdekes pont. Közel valós időben dolgozza fel a kérelmet. Ez az API olyan alkalmazások esetében ajánlott, amelyekben a felhasználók egy adott szövegmezőben keresik meg a címeket vagy érdekes pontokat.
- [**Keresés a geometrián belül**](/rest/api/maps/search/postsearchinsidegeometry): a megadott geometrián belüli érdeklődési pontok keresése. Keressen például egy érdekes pontot egy sokszögen belül.
- [**Keresés az útvonal mentén**](/rest/api/maps/search/postsearchalongroute): keressen olyan pontokat, amelyek egy adott útvonal útvonalán találhatók.
- [**Fuzzy batch-keresés**](/rest/api/maps/search/postsearchfuzzybatchpreview): hozzon létre legfeljebb 10 000 címet, helyet, tereptárgyat vagy érdeklődési pontot tartalmazó kérelmet. A kérelem feldolgozása egy adott időszakon belül megtörtént. A rendszer minden adatfeldolgozást párhuzamosan dolgoz fel a kiszolgálón. Ha a kérés befejezi a feldolgozást, letöltheti az eredmény teljes készletét.

Jelenleg Azure Maps nem rendelkezik hasonló API-val a Google Maps-beli szöveges keresési API-hoz.

> [!TIP]
> A POI Search, a POI kategóriájú keresés és a fuzzy Search API-k automatikus kiegészítési módban is használhatók `&typeahead=true` a kérelem URL-címéhez való hozzáadással. Ez azt közli a kiszolgálóval, hogy a bemeneti szöveg valószínűleg részleges. Az API a keresést prediktív módban hajtja végre.

Tekintse át a keresési dokumentációval [kapcsolatos ajánlott eljárásokat](how-to-use-best-practices-for-search.md) .

### <a name="find-place-from-text"></a>Hely keresése a szövegből

Az érdeklődési pontok kereséséhez használja az Azure Maps [POI Search](/rest/api/maps/search/getsearchpoi) és a [fuzzy Search](/rest/api/maps/search/getsearchfuzzy) kifejezést.

A táblázat a Google Maps API paramétereit a hasonló Azure Maps API-paraméterekkel hivatkozik.

| Google Maps API-paraméter | Hasonló Azure Maps API-paraméter |
|---------------------------|-------------------------------------|
| `fields`                  | *N/A*                               |
| `input`                   | `query`                             |
| `inputtype`               | *N/A*                               |
| `key`                     | `subscription-key` – Lásd még a [hitelesítés Azure Maps](azure-maps-authentication.md) a dokumentációt. |
| `language`                | `language` – Lásd a [támogatott nyelvek](supported-languages.md) dokumentációját.  |
| `locationbias`            | `lat``lon`és`radius`<br/>`topLeft` és `btmRight`<br/>`countrySet`  |

### <a name="nearby-search"></a>Közeli keresés

A [közeli keresési](/rest/api/maps/search/getsearchnearby) API-val lekérheti a közeli érdeklődési pontokat a Azure maps.

A táblázat a Google Maps API paramétereit jeleníti meg az összehasonlítható Azure Maps API-paraméterekkel.

| Google Maps API-paraméter | Hasonló Azure Maps API-paraméter  |
|---------------------------|--------------------------------------|
| `key`                       | `subscription-key` – Lásd még a [hitelesítés Azure Maps](azure-maps-authentication.md) a dokumentációt. |
| `keyword`                   | `categorySet` és `brandSet`        |
| `language`                  | `language` – Lásd a [támogatott nyelvek](supported-languages.md) dokumentációját.  |
| `location`                  | `lat` és `lon`                     |
| `maxprice`                  | *N/A*                               |
| `minprice`                  | *N/A*                               |
| `name`                      | `categorySet` és `brandSet`        |
| `opennow`                   | *N/A*                               |
| `pagetoken`                 | `ofs` és `limit`                   |
| `radius`                    | `radius`                            |
| `rankby`                    | *N/A*                               |
| `type`                      | `categorySet –` Lásd a [támogatott keresési kategóriák](supported-search-categories.md) dokumentációját.   |

## <a name="calculate-routes-and-directions"></a>Útvonalak és irányok kiszámítása

Útvonalak és irányok kiszámítása Azure Maps használatával. Azure Maps a Google Maps útválasztási szolgáltatása számos különböző funkciót tartalmaz, például:

* Érkezési és távozási idő.
* Valós idejű és prediktív forgalmi útvonalak.
* Különböző szállítási módok. Például:, vezetés, gyaloglás, kerékpározás.

> [!NOTE]
> Azure Maps az összes Útpontot koordinálni kell. Először a címeket kell geokódolva.

A Azure Maps útválasztási szolgáltatás a következő API-kat biztosítja az útvonalak kiszámításához:

- [**Útvonal kiszámítása**](/rest/api/maps/route/getroutedirections): kiszámítja az útvonalat, és azonnal feldolgozza a kérést. Ez az API a GET és a POST kérelmeket is támogatja. A POST kérések használata nagy számú útpont megadása esetén ajánlott, vagy ha sok útvonal-beállítást használ, hogy az URL-cím kérése ne legyen túl hosszú, és problémákat okozzon. A Azure Maps utáni útvonal iránya olyan lehetőséggel is rendelkezhet, amely több ezer [támogató pontot](/rest/api/maps/route/postroutedirections#supportingpoints) is igénybe vehet, és ezek használatával újra létrehozhatja a logikai útvonal elérési útját (a központhoz illesztés). 
- [**Batch Route**](/rest/api/maps/route/postroutedirectionsbatchpreview): hozzon létre egy legfeljebb 1 000 útválasztási kérelmet tartalmazó kérelmet, és egy adott időszakban dolgozza fel azokat. Az összes adat párhuzamosan lesz feldolgozva a kiszolgálón, és ha elkészült, a teljes eredményhalmaz letölthető.
- [* * Mobilitási szolgáltatások (előzetes verzió) * *](/rest/api/maps/mobility): útvonalak és irányok kiszámítása a nyilvános átvitel használatával.

A táblázat a Google Maps API paramétereit a Azure Maps hasonló API-paramétereivel hivatkozik.

| Google Maps API-paraméter    | Hasonló Azure Maps API-paraméter  |
|------------------------------|--------------------------------------|
| `alternatives`                 | `maxAlternatives`                  |
| `arrival_time`                | `arriveAt`                          |
| `avoid`                        | `avoid`                            |
| `departure_time`              | `departAt`                          |
| `destination`                  | `query` – Koordináták formátumban `"lat0,lon0:lat1,lon1…."`  |
| `key`                          | `subscription-key` – Lásd még a [hitelesítés Azure Maps](azure-maps-authentication.md) a dokumentációt. |
| `language`                     | `language` – Lásd a [támogatott nyelvek](supported-languages.md) dokumentációját.   |
| `mode`                         | `travelMode`                       |
| `optimize`                     | `computeBestOrder`                 |
| `origin`                       | `query`                            |
| `region`                       | *N/A* – ez a funkció helymeghatározáshoz kapcsolatos. Használja a *countrySet* paramétert a Azure Maps helymeghatározáshoz API használatakor.  |
| `traffic_model`               | *N/A* – csak azt adhatja meg, hogy a forgalmi adatokat a *Traffic* paraméterrel kell-e használni. |
| `transit_mode`                | Lásd: a [mobilitási szolgáltatások (előzetes verzió) dokumentációja](/rest/api/maps/mobility) |
| `transit_routing_preference` | Lásd: a [mobilitási szolgáltatások (előzetes verzió) dokumentációja](/rest/api/maps/mobility) |
| `units`                        | *N/A* – Azure Maps csak a metrikus rendszer használatát használja.  |
| `waypoints`                    | `query`                            |

> [!TIP]
> Alapértelmezés szerint a Azure Maps Route API csak összegzést ad vissza. A függvény a távolságot és az időpontokat, valamint az útvonal útvonalának koordinátáit adja vissza. Használja a `instructionsType` paramétert a lépésenkénti utasítások lekéréséhez. A paraméter használatával pedig `routeRepresentation` kiszűrheti az összefoglalás és az útvonal elérési útját.

Azure Maps Routing API további funkciókkal rendelkezik, amelyek nem érhetők el a Google Maps szolgáltatásban. Az alkalmazás áttelepítésekor érdemes lehet ezeket a funkciókat használni, ha hasznosnak találja őket.

* Az útvonal típusának támogatása: legrövidebb, leggyorsabb, trilling és a legtöbb üzemanyag-takarékos.
* További utazási módok támogatása: busz, motorkerékpár, taxi, tehergépkocsi és Furgon.
* 150-es útpontok támogatása.
* Több utazási idő kiszámítása egyetlen kérelemben; korábbi forgalom, élő forgalom, forgalom nélkül.
* Kerülje a további utak típusát: telekocsi utak, nem burkolt utak, már használatban lévő utak.
* A kerülni kívánt egyéni területek meghatározása.
* Korlátozza a jogosultságszint-emelést, amelyet az útvonal felemel.
* Útvonal a motor előírásai alapján. A motor specifikációi alapján kiszámítja az égési vagy az elektromos járművek útvonalát, valamint a fennmaradó üzemanyagot vagy díjat.
* A kereskedelmi jármű útvonalának paramétereinek támogatása. Ilyenek például a járművek méretei, súlyozása, Axel-száma és a rakomány típusa.
* A jármű maximális sebességének meghatározása.

Ezen kívül a Azure Maps útvonal-szolgáltatása támogatja az [útválasztásos tartományok kiszámítását](/rest/api/maps/route/getrouterange). Az átirányítható tartományok kiszámítása izokrón néven is ismert. Magában foglalja egy olyan sokszög előállítását, amely egy olyan területre mutat, amely egy adott kezdőponttól bármilyen irányban átutazható. Az összes megadott időtartam vagy üzemanyag vagy díj alapján.

Tekintse át az útválasztási dokumentációval [kapcsolatos ajánlott eljárásokat](how-to-use-best-practices-for-routing.md) .

## <a name="retrieve-a-map-image"></a>Térkép rendszerképének beolvasása

A Azure Maps egy API-t biztosít a statikus Térkép képének megjelenítéséhez. A [Térkép képmegjelenítési](/rest/api/maps/render/getmapimagerytile) API-ját Azure Maps a Google Maps statikus Térkép API-ját hasonlítja össze.

> [!NOTE]
> Azure Maps a középpontot, az összes jelölőt és az elérési út helyét a "hosszúság, szélesség" formátumban kell koordinálni. Míg a Google Maps a "szélesség, hosszúság" formátumot használja. Először a címeket kell geokódolva.

A táblázat a Google Maps API paramétereit a Azure Maps hasonló API-paramétereivel hivatkozik.

| Google Maps API-paraméter | Hasonló Azure Maps API-paraméter  |
|---------------------------|--------------------------------------|
| `center`                    | `center`                           |
| `format`                    | `format` – az URL elérési útjának részeként van megadva. Jelenleg csak a PNG támogatott. |
| `key`                       | `subscription-key` – Lásd még a [hitelesítés Azure Maps](azure-maps-authentication.md) a dokumentációt. |
| `language`                  | `language` – Lásd a [támogatott nyelvek](supported-languages.md) dokumentációját.  |
| `maptype`                   | `layer` és `style` – lásd a [Térkép támogatott stílusainak](supported-map-styles.md) dokumentációját. |
| `markers`                   | `pins`                             |
| `path`                      | `path`                             |
| `region`                    | *N/A* – ez egy helymeghatározáshoz-hez kapcsolódó szolgáltatás. Használja a `countrySet` paramétert, ha a Azure Maps HELYMEGHATÁROZÁSHOZ API-t használja.  |
| `scale`                     | *N/A*                              |
| `size`                      | `width` és `height` – akár 8192x8192 is lehet. |
| `style`                     | *N/A*                              |
| `visible`                   | *N/A*                              |
| `zoom`                      | `zoom`                             |

> [!NOTE]
> A Azure Maps csempe rendszerében a csempék a Google Maps-ben használt Térkép csempék méretének kétszerese. Mivel a nagyítási szint értéke Azure Maps egy nagyítási szint jelenik meg Azure Maps a Google Maps-hez képest. Ahhoz, hogy kompenzálja ezt a különbséget, az áttelepíteni kívánt kérések nagyítási szintjének csökkentése.

További információ: [útmutató a Térkép rendszerkép-renderelési API-hoz](how-to-render-custom-data.md).

Egy statikus térképi rendszerkép létrehozásán kívül a Azure Maps Render szolgáltatás lehetővé teszi a térképes csempék közvetlen elérését raszteres (PNG) és vektoros formátumban:

- [**Térkép csempe**](/rest/api/maps/render/getmaptile): a RASZTER (PNG) és a vektor csempék lekérése az alaptérképekhez (utak, határok, háttér).
- [**Térkép képének csempe**](/rest/api/maps/render/getmapimagerytile): antenna-és műholdas képcsempék lekérése.

> [!TIP]
> Számos Google Maps-alkalmazás, amely néhány éve az interaktív térképi élményről a statikus térképi képekre váltott át. Ez költségmegtakarítási módszerként történt. A Azure Maps általában költséghatékonyabb, hogy az interaktív térkép vezérlőelemet használja a web SDK-ban. Az interaktív térkép vezérlési díjai a csempék betöltésének száma alapján jelennek meg. Azure Maps a csempék nagy méretűek. Gyakran csak néhány csempét kell létrehoznia, hogy újra létrehozza ugyanazt a leképezési nézetet statikus térképként. A Térkép csempéit a böngésző automatikusan gyorsítótárazza. Így az interaktív térkép vezérlőelem gyakran generál egy tranzakció egy részét, amikor egy statikus Térkép nézetet készít elő. A pásztázás és a nagyítás több csempét is betölt; a Térkép vezérlőelemben azonban lehetőség van a viselkedés letiltására. Az interaktív térkép vezérlőelem sokkal több vizualizációs lehetőséget is biztosít, mint a statikus Térkép-szolgáltatások.

### <a name="marker-url-parameter-format-comparison"></a>Jelölő URL-paraméter formátumának összehasonlítása

**Előtte: Google Maps**

Adja hozzá a jelölőket az `markers` URL-cím paraméterének használatával. A `markers` paraméter egy stílust és a térképen megjeleníteni kívánt helyszínek listáját veszi figyelembe az alábbi ábrán látható módon:

```text
&markers=markerStyles|markerLocation1|markerLocation2|...
```

További stílusok hozzáadásához használja a `markers` paramétereket az URL-címhez egy másik stílussal és hellyel.

A "szélesség, hosszúság" formátumban válassza a jelölők helyét.

Adja hozzá a jelölő stílusát a `optionName:value` formátumhoz, több stílust pedig pipe () karakterekkel elválasztva, \| például "optionName1: érték1 \| optionName2: Érték2". Megjegyzés: a beállítások nevei és értékei kettősponttal vannak elválasztva (:). A Google Maps-ben a következő stílusú stílusok használhatók a Style jelölők számára:

* `color` – Az alapértelmezett jelölő ikonjának színe. Lehet egy 24 bites hexadecimális szín ( `0xrrggbb` ) vagy a következő értékek egyike:,,,, `black` `brown` `green` `purple` `yellow` ,,, `blue` `gray` `orange` , `red` , `white` .
* `label` – Egy nagybetűs alfanumerikus karakter, amely az ikon tetején jelenik meg.
* `size` – A jelölő mérete. Lehet `tiny` , `mid` , vagy `small` .

A Google Maps egyéni ikonjaihoz a következő stílusú beállításokat használhatja:

* `anchor` – Meghatározza, hogyan igazíthatja az ikont a koordinátahoz. Lehet egy képpont (x, y) érték vagy a következő értékek egyike: ,,,, `top` `bottom` `left` `right` `center` , `topleft` , `topright` , `bottomleft` , vagy `bottomright` .
* `icon` – Az ikon képére mutató URL-cím.

Tegyük fel például, hogy egy piros, közepes méretű jelölőt adunk a térképhez a földrajzi hosszúság:-110, szélesség: 45:

```text
&markers=color:red|size:mid|45,-110
```

![Google Maps-jelölő](media/migrate-google-maps-web-services/google-maps-marker.png)

**Utána: Azure Maps**

Adja hozzá a jelölőket egy statikus Térkép képhez a paraméter megadásával `pins` az URL-címben. A Google Mapshöz hasonlóan a paraméterben is megadunk egy stílust és a helyszínek listáját. A paraméter többször is megadható a `pins` különböző stílusú jelölők támogatásához.

```text
&pins=iconType|pinStyles||pinLocation1|pinLocation2|...
```

Ha további stílusokat szeretne használni, adjon hozzá további `pins` paramétereket az URL-címhez egy másik stílussal és hellyel.

Azure Maps a PIN-kód helyét "hosszúsági szélesség" formátumban kell megadni. A Google Maps a "szélesség, hosszúság" formátumot használja. Egy szóköz, nem pedig vessző, elkülöníti a hosszúságot és a szélességet Azure Maps formátumban.

A `iconType` létrehozandó PIN-kód típusát adja meg. A következő értékeket veheti fel:

* `default` – Az alapértelmezett PIN-kód ikon.
* `none` – A ikon nem jelenik meg, csak a feliratok lesznek megjelenítve.
* `custom` – Meghatározza, hogy egyéni ikont kell használni. Az ikon képére mutató URL-címet a rendszer a `pins` PIN-kód helye információinak lejárta után is hozzáadhatja a paraméter végéhez.
* `{udid}` – Egyedi Adatazonosító (UDID) a Azure Maps adattárolási platformon tárolt ikonhoz.

Adja hozzá a PIN-kód stílusát a `optionNameValue` formátumhoz. Több stílust is elkülönít a pipe ( \| ) karakterekkel. Példa: `iconType|optionName1Value1|optionName2Value2`. A beállítások nevei és értékei nincsenek elválasztva. A következő stílus-beállítási neveket használja a Style jelölőknek:

* `al` – A jelölő opacitását (alfa) adja meg. 0 és 1 közötti számot adjon meg.
* `an` – Megadja a PIN-kód horgonyát. X és y képpont értékeket az "x y" formátumban kell megadni.
* `co` – A PIN-kód színe. 24 bites hexadecimális színt válasszon a következőhöz `000000` : `FFFFFF` .
* `la` – Megadja a felirat horgonyát. X és y képpont értékeket az "x y" formátumban kell megadni.
* `lc` – A címke színe. 24 bites hexadecimális színt válasszon a következőhöz `000000` : `FFFFFF` .
* `ls` – A felirat mérete képpontban megadva. Nullánál nagyobb számot adjon meg.
* `ro` – Az ikon elforgatására szolgáló fokban megadott érték. Válasszon egy-360 és 360 közötti számot.
* `sc` – A PIN-kód ikonjának méretezési értéke. Nullánál nagyobb számot adjon meg.

Az egyes PIN-kódokhoz tartozó feliratok értékének megadása. Ez a megközelítés hatékonyabb, mint egyetlen címke értékének alkalmazása a helyszínek listáján szereplő összes jelölőre. A címke értéke több karakterből álló karakterlánc lehet. A sztringet egyetlen idézőjelek közé csomagolva biztosíthatja, hogy ne legyen helytelen a stílus vagy a hely értéke.

Vegyünk fel egy vörös ( `FF0000` ) alapértelmezett ikont, amely a "Space tű" címkével van ellátva, és az alábbi pozícióban van (15 50). Az ikon a következő hosszúságú:-122,349300, szélesség: 47,620180:

```text
&pins=default|coFF0000|la15 50||'Space Needle' -122.349300 47.620180
```

![Azure Maps jelölő](media/migrate-google-maps-web-services/azure-maps-marker.png)

Vegyen fel három PIN-értéket az "1", "2" és "3" címke értékkel:

```text
&pins=default||'1'-122 45|'2'-119.5 43.2|'3'-121.67 47.12
```

![Több jelölő Azure Maps](media/migrate-google-maps-web-services/azure-maps-multiple-markers.png)

### <a name="path-url-parameter-format-comparison"></a>Elérési út URL-paraméterének formátumának összehasonlítása

**Előtte: Google Maps**

Adja hozzá a vonalakat és a sokszögeket egy statikus térképi képhez az `path` URL-cím paraméterének használatával. A `path` paraméter egy stílust és a térképen megjeleníteni kívánt helyszínek listáját veszi figyelembe az alábbi ábrán látható módon:

```text
&path=pathStyles|pathLocation1|pathLocation2|...
```

További stílusok hozzáadásával további paramétereket adhat hozzá `path` az URL-címhez más stílussal és különböző helyszínekkel.

Az elérési utak helye a `latitude1,longitude1|latitude2,longitude2|…` formátummal van megadva. Az elérési utak kódolása vagy a pontok címeinek tárolására használható.

Adja hozzá a Path stílusokat a `optionName:value` formátumhoz, és válassza el a több stílust a pipe ( \| ) karakterekkel. És a választható nevek és értékek kettősponttal (:). A következőhöz hasonló: `optionName1:value1|optionName2:value2` . A következő stílus-beállítási nevek használhatók a Google Maps-útvonalak stílusához:

* `color` – Az elérési út vagy a sokszög körvonalának színe Egy 24 bites hexadecimális szín ( `0xrrggbb` ), a 32 bites hexadecimális szín ( `0xrrggbbbaa` ) vagy a következő értékek egyike lehet: fekete, barna, zöld, lila, sárga, kék, szürke, narancssárga, piros, fehér.
* `fillColor` – A görbe kitöltéséhez használandó szín (sokszög). Egy 24 bites hexadecimális szín ( `0xrrggbb` ), a 32 bites hexadecimális szín ( `0xrrggbbbaa` ) vagy a következő értékek egyike lehet: fekete, barna, zöld, lila, sárga, kék, szürke, narancssárga, piros, fehér.
* `geodesic` – Azt jelzi, hogy az elérési útnak olyan vonalnak kell-e lennie, amely követi a föld görbületét.
* `weight` – Az elérési út vonalvastagsága képpontban megadva.

Adja hozzá a piros vonal opacitását és a képpont vastagságát a koordináták közötti térképhez az URL paraméterben. Az alábbi példában a vonal 50%-os opacitással és négy képpont vastagsággal rendelkezik. A koordináták a következők:-110, szélesség: 45 és hosszúság:-100, szélesség: 50.

```text
&path=color:0xFF000088|weight:4|45,-110|50,-100
```

![Google Maps-vonallánc](media/migrate-google-maps-web-services/google-maps-polyline.png)

**Utána: Azure Maps**

Sorok és sokszögek hozzáadása statikus térképi képhez a `path` paraméter az URL-címben való megadásával. A Google Mapshöz hasonlóan az ebben a paraméterben szereplő helyszínek is megtalálhatók. A paraméter többszöri megadásával `path` több kör, vonal és sokszög jeleníthető meg különböző stílusokkal.

```text
&path=pathStyles||pathLocation1|pathLocation2|...
```

Az elérési utak helyeinél Azure Maps megköveteli, hogy a koordináták "hosszúsági szélesség" formátumúak legyenek. A Google Maps a "szélesség, hosszúság" formátumot használja. Egy szóköz, nem pedig vessző, elkülöníti a hosszúságot és a szélességet Azure Maps formátumban. A Azure Maps nem támogatja a pontok kódolására szolgáló elérési utakat vagy címeket. Az [itt](how-to-render-custom-data.md#get-data-from-azure-maps-data-storage)leírtak szerint töltsön fel nagyobb adatkészleteket GeoJSON-fájlként a Azure Maps ADATtároló API-ba.

Adja hozzá a Path stílusokat a `optionNameValue` formátumhoz. Több stílust is elválaszthat a pipe ( \| ) karakterekkel, például a következőképpen `optionName1Value1|optionName2Value2` . A beállítások nevei és értékei nincsenek elválasztva. A következő stílus-beállítási neveket használja a Azure Maps stílusának elérési útjaihoz:

* `fa` – A sokszögek renderelése során használt kitöltési szín opacitása (alpha). 0 és 1 közötti számot adjon meg.
* `fc` – A sokszög területének megjelenítéséhez használt kitöltési szín.
* `la` – A vonalak és a sokszögek körvonalainak megjelenítéséhez használt vonal színopacitása (alpha). 0 és 1 közötti számot adjon meg.
* `lc` – A vonalak megjelenítéséhez és a sokszögek körvonalához használt vonal színe.
* `lw` – A vonal szélessége képpontban megadva.
* `ra` – A körök sugarát adja meg méterben.

Adja hozzá a piros vonal fedettségét és a képpont vastagságát a koordináták között az URL paraméterben. Az alábbi példában a vonal 50%-os opacitással és négy képpont vastagsággal rendelkezik. A koordináták értékei a következők: hosszúság:-110, szélesség: 45 és hosszúság:-100, szélesség: 50.

```text
&path=lcFF0000|la.5|lw4||-110 45|-100 50
```

![Azure Maps vonallánc](media/migrate-google-maps-web-services/azure-maps-polyline.png)

## <a name="calculate-a-distance-matrix"></a>Távolsági mátrix kiszámítása

Azure Maps a távolsági mátrix API-t biztosítja. Ezzel az API-val kiszámíthatja az utazási időt és a távolságokat a különböző tárolóhelyek között, a távolsági mátrixtal együtt. A Google Maps szolgáltatásban a távolsági mátrix API-hoz hasonló.

- [**Útvonal-mátrix**](/rest/api/maps/route/postroutematrixpreview): aszinkron módon számítja ki az utazási időpontokat és a távolságokat az egyes eredetek és célhelyek esetében. A legfeljebb 700 cellát támogat. Az eredetek száma megszorozva a célhelyek számával. Ennek a korlátozásnak a szem előtt tartva a lehetséges mátrix-dimenziók például a következők: 700x1, 50x10, 10x10, 28x25, 10x70.

> [!NOTE]
> A távolsági mátrix API-ra irányuló kérés csak POST-kéréssel hozható létre a kérelem törzsében, a forrás-és a célként megadott információval. Emellett Azure Maps az összes eredetet és célhelyet koordinálni kell. Először a címeket kell geokódolva.

Ez a táblázat az összehasonlítható Azure Maps API-paraméterekkel hivatkozik a Google Maps API-paraméterekre.

| Google Maps API-paraméter      | Hasonló Azure Maps API-paraméter  |
|--------------------------------|--------------------------------------|
| `arrivial_time`                | `arriveAt`                           |
| `avoid`                        | `avoid`                              |
| `depature_time`                | `departAt`                           |
| `destinations`                 | `destination` – a POST kérelem törzsében válassza a GeoJSON. |
| `key`                          | `subscription-key` – Lásd még a [hitelesítés Azure Maps](azure-maps-authentication.md) a dokumentációt. |
| `language`                     | `language` – Lásd a [támogatott nyelvek](supported-languages.md) dokumentációját.  |
| `mode`                         | `travelMode`                         |
| `origins`                      | `origins` – a POST kérelem törzsében válassza a GeoJSON.  |
| `region`                       | *N/A* – ez a funkció helymeghatározáshoz kapcsolatos. Használja a `countrySet` paramétert, ha a Azure Maps HELYMEGHATÁROZÁSHOZ API-t használja. |
| `traffic_model`                | *N/A* – csak azt adhatja meg, hogy a forgalmi adatokat a paraméterrel kell-e használni `traffic` . |
| `transit_mode`                 | *N/A – az* átvitelen alapuló távolsági mátrixok jelenleg nem támogatottak.  |
| `transit_routing_preference`   | *N/A – az* átvitelen alapuló távolsági mátrixok jelenleg nem támogatottak.  |
| `units`                        | *N/A* – Azure Maps csak a metrikus rendszer használatát használja. |

> [!TIP]
> A Azure Maps útválasztási API-ban elérhető összes speciális útválasztási lehetőség támogatott a Azure Maps távolsági mátrix API-ban. A speciális útválasztási lehetőségek a következők: Tehergépkocsi-útválasztás, motor-specifikációk stb.

Tekintse át az útválasztási dokumentációval [kapcsolatos ajánlott eljárásokat](how-to-use-best-practices-for-routing.md) .

## <a name="get-a-time-zone"></a>Időzóna beolvasása

A Azure Maps egy API-t biztosít a koordináta időzónájának lekéréséhez. A Azure Maps időzóna API hasonló az időzóna API-hoz a Google Maps szolgáltatásban:

- [**Időzóna koordináta szerint**](/rest/api/maps/timezone/gettimezonebycoordinates): megadási koordináta és a koordináta időzóna-adatainak fogadása.

Ez a táblázat a Google Maps API paramétereit a Azure Maps hasonló API-paramétereivel hivatkozik.

| Google Maps API-paraméter | Hasonló Azure Maps API-paraméter   |
|---------------------------|---------------------------------------|
| `key`                       | `subscription-key` – Lásd még a [hitelesítés Azure Maps](azure-maps-authentication.md) a dokumentációt.       |
| `language`                  | `language` – Lásd a [támogatott nyelvek](supported-languages.md) dokumentációját.    |
| `location`                  | `query`             |
| `timestamp`                 | `timeStamp`         |

Ezen az API-on kívül a Azure Maps számos időzóna API-t biztosít. Ezek az API-k az időzóna neve vagy azonosítói alapján alakítják át az időt:

- [**Időzóna azonosító szerint**](/rest/api/maps/timezone/gettimezonebyid): a megadott IANA Időzóna-azonosító aktuális, korábbi és jövőbeli időzóna-információit adja vissza.
- [**Időzóna enumerálása IANA**](/rest/api/maps/timezone/gettimezoneenumiana): az IANA időzóna-azonosítók teljes listáját adja vissza. Az IANA szolgáltatás frissítései egy napon belül megjelennek a rendszeren.
- [**Időzóna enumerálása Windows**](/rest/api/maps/timezone/gettimezoneenumwindows): a Windows időzóna-azonosítók teljes listáját adja vissza.
- [**Időzóna – IANA-verzió**](/rest/api/maps/timezone/gettimezoneianaversion): az Azure Maps által használt aktuális IANA-verziószámot adja vissza.
- [**Időzóna-ablakok az IANA számára**](/rest/api/maps/timezone/gettimezonewindowstoiana): egy megfelelő IANA-azonosítót ad vissza, amely érvényes Windows időzóna-azonosítót adott meg. Egy Windows-AZONOSÍTÓhoz több IANA-azonosító is visszaküldhető.

## <a name="client-libraries"></a>Ügyfélkódtárak

A Azure Maps a következő programozási nyelvekhez biztosít ügyféloldali kódtárakat:

* JavaScript, írógéppel, Node.js – [dokumentációs](how-to-use-services-module.md) \| [NPM csomag](https://www.npmjs.com/package/azure-maps-rest)

Ezek a nyílt forráskódú ügyféloldali kódtárak más programozási nyelveken is elérhetők:

* .NET Standard 2,0 – [GitHub Project](https://github.com/perfahlen/AzureMapsRestServices) \| [NuGet csomag](https://www.nuget.org/packages/AzureMapsRestToolkit/)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Nincs kitakarítható erőforrás.

## <a name="next-steps"></a>Következő lépések

További információ a Azure Maps REST-szolgáltatásokról:

> [!div class="nextstepaction"]
> [Ajánlott keresési eljárások](how-to-use-best-practices-for-search.md)
