---
title: Azure Maps Szószedet | Microsoft Docs
description: A Azure Mapshoz, Location-Based szolgáltatásokhoz és a GIS-hez kapcsolódó általánosan használt kifejezések glosszáriuma.
author: rbrundritt
ms.author: richbrun
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 05071be4fc1705787a649d331613e8d9399f983f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92897177"
---
# <a name="glossary"></a>Szószedet

Az alábbi lista a Azure Maps-szolgáltatásokhoz használt általános szavakat ismerteti.

## <a name="a"></a>A

<a name="address-validation"></a>**Címek ellenőrzése**: a címek létezésének ellenőrzésének folyamata.

<a name="advanced-routing"></a>**Speciális útválasztás**: olyan szolgáltatások gyűjteménye, amelyek a közúti útválasztási adatok használatával végzik az előzetes műveleteket. például a elérhető tartományok (izokrón), a távolsági mátrixok és a kötegelt útválasztási kérelmek kiszámítása.

<a name="aerial-imagery"></a>**Légi képképek**: lásd a [műholdképeket](#satellite-imagery). 

<a name="along-a-route-search"></a>**Útvonal-keresés**: egy térbeli lekérdezés, amely egy megadott leadási idő vagy távolság alapján keresi az adatok útvonalon belüli elérését.

<a name="altitude"></a>**Magasság**: egy pont magassága vagy függőleges megemelése egy hivatkozási felület fölé. A magassági mérések egy adott hivatkozási adaton alapulnak, például az átlagos tengeri szinten. Lásd még: Jogosultságszint-emelés.

<a name="ambiguous"></a>Nem **egyértelmű**: az adatbesorolásban olyan bizonytalansági állapot áll fenn, amely akkor fordulhat elő, ha egy adott attribútumhoz legalább két érték van hozzárendelve. Ha például a "CA" helymeghatározáshoz, a rendszer két kétértelmű eredményt ad vissza: "Kanada" és "Kalifornia". A "CA" egy ország/régió és egy állapotkód a "Kanada" és "Kalifornia" esetében. 

<a name="annotation"></a>**Jegyzet**: a térképen megjelenített szöveg vagy grafika, amely információkat biztosít a felhasználónak. A jegyzet azonosíthatja vagy leírhatja egy adott Térkép entitást, általános információkat adhat meg a Térkép egy területéről, vagy magáról a térképről szolgáltathat információkat.

<a name="antimeridian"></a>**Antimeridian**: az 180 <sup>th</sup> meridián néven is ismert. Ez az a pont, ahol az-180 fok és a 180 fok hosszúsága megfelel. Ez a Prime Meridian ellentéte a világon.

<a name="application-programming-interface-api"></a>Alkalmazásprogramozási **felület (API)**: olyan specifikáció, amely lehetővé teszi, hogy a fejlesztők alkalmazásokat hozzanak létre.

<a name="api-key"></a>**API-kulcs**: Tekintse meg a [megosztott kulcsos hitelesítést](#shared-key-authentication).

<a name="area-of-interest-aoi"></a>**Érdeklődési terület (AOI)**: a Térkép vagy az adatbázis-termelés fókusz területének meghatározásához használt mérték.

<a name="asset-tracking"></a>**Eszköz nyomon követése**: egy eszköz helyének (például személy, jármű vagy más objektum) nyomon követésének folyamata.

<a name="asynchronous-request"></a>**Aszinkron kérelem**: egy olyan HTTP-kérelem, amely megnyit egy kapcsolódást, és kérést küld a kiszolgálónak, amely visszaadja az aszinkron kérelem azonosítóját, majd bezárja a kapcsolódást. A kiszolgáló továbbra is feldolgozza a kérést, és a felhasználó az azonosítót használva megtekintheti az állapotot. A kérelem feldolgozása után a felhasználó letöltheti a választ. Ez a típusú kérelem hosszú ideig futó folyamatokhoz használható.

<a name="autocomplete"></a>**Automatikus kiegészítés**: az alkalmazás egy funkciója, amely egy felhasználó begépelését jelzi. 

<a name="autosuggest"></a>Automatikus **kiegészítés: az** alkalmazás egyik funkciója, amely a felhasználó begépelésének logikai lehetőségeit Jósolja meg.

<a name="azure-location-based-services-lbs"></a>**Azure Location based Services (LBS)**: a Azure Maps korábbi neve, amikor az előzetes verzióban volt.

<a name="azure-active-directory"></a>**Azure Active Directory (Azure ad)**: az Azure ad a Microsoft felhőalapú identitás-és hozzáférés-kezelési szolgáltatása. Azure Maps Azure AD-integráció jelenleg előzetes verzióban érhető el az összes Azure Maps API-hoz. Az Azure AD támogatja az Azure szerepköralapú hozzáférés-vezérlést (Azure RBAC) a Azure Maps erőforrások részletes elérésének lehetővé tételéhez. Az Azure Maps Azure AD-integrációval kapcsolatos további tudnivalókért tekintse meg a [Azure Maps és az Azure ad](azure-maps-authentication.md) és [a hitelesítés kezelése a Azure Maps-ben](how-to-manage-authentication.md)című témakört.

<a name="azure-maps-key"></a>**Azure Maps kulcs**: Tekintse meg a [megosztott kulcsos hitelesítést](#shared-key-authentication).

## <a name="b"></a>B

<a name="base-map"></a>**Alaptérkép**: a térképes alkalmazás azon része, amely a háttér-hivatkozási információkat, például az utakat, a tereptárgyait és a politikai határokat jeleníti meg.

<a name="batch-request"></a>**Batch-kérelem**: több kérelem egyetlen kérelembe való egyesítésének folyamata.

<a name="bearing"></a>**Csapágy**: egy pont vízszintes iránya egy másik ponthoz képest. Ezt az értéket az északi értékhez viszonyítva, 0 és 360 fok közötti szögben, az óramutató járásával megegyező irányban fejezzük ki. 

<a name="boundary"></a>**Határ**: A szomszédos politikai entitásokat (például országokat/régiókat, körzeteket és tulajdonságokat) elválasztó vonal vagy sokszög. A határ egy olyan vonal, amely a fizikai funkciók, például a Rivers, a hegyek vagy a falak esetében nem követhető.

<a name="bounds"></a>**Korlátok**: lásd a [határolókeret mezőt](#bounding-box).

<a name="bounding-box"></a>**Határolókeret**: a térképen egy téglalap alakú terület ábrázolására szolgáló koordináták halmaza. 

## <a name="c"></a>C

<a name="cadastre"></a>**Kataszter**: a regisztrált földterületek és tulajdonságok nyilvántartása. Lásd még: [parcella](#parcel).

<a name="camera"></a>**Kamera**: egy interaktív térkép vezérlőelem kontextusában a kamera a térképek mezőt határozza meg. A kamera nézetablaka több térképi paraméter alapján van meghatározva: középpont, nagyítási szint, pitch, csapágy. 

<a name="centroid"></a>**Középpontját**: egy szolgáltatás geometriai középpontja. A vonal középpontját a középpont, miközben a sokszög középpontját a terület középpontja lenne.

<a name="choropleth-map"></a>**Choropleth Térkép**: egy tematikus Térkép, amelyben a területek árnyékolva vannak egy statisztikai változó mértékének arányában. Ez a statisztikai változó megjelenik a térképen. Például az egyes államokbeli állapotok határait az összes többi államra vonatkozó relatív populációjuk alapján színezheti.

<a name="concave-hull"></a>**Konkáv hajótest**: egy olyan alakzat, amely egy lehetséges homorú geometriát jelöl, amely a megadott adatkészletben lévő összes alakzatot befedi. A generált alakzat hasonló az adatcsomagoláshoz a műanyag becsomagolással, majd a fűtéssel, így nagy mértékben átível a pontok közötti, és az egyéb adatpontok felé irányuló kapcsolat.

<a name="consumption-model"></a>**Fogyasztási modell**: az a sebesség, amely meghatározza, hogy a jármű milyen sebességgel használja az üzemanyagot vagy a villamos energiát. Tekintse meg a használati [modell dokumentációját](consumption-model.md)is.

<a name="control"></a>**Vezérlő**: egy olyan önálló vagy újrafelhasználható összetevő, amely egy grafikus felhasználói felületből áll, amely az illesztőfelület viselkedési készletét határozza meg. A Térkép vezérlőelem például általában a felhasználói felület azon része, amely egy interaktív térképet tölt be.

<a name="convex-hull"></a>**Domború hajótest**: a domború hajótest olyan alakzat, amely a megadott adatkészletben lévő összes alakzatot magában foglaló, minimális domború geometriát jelöli. A generált alakzat hasonló az adathalmaz körüli rugalmas sáv becsomagolásához.

<a name="coordinate"></a>**Koordináta**: a Térkép helyének ábrázolásához használt hosszúsági és szélességi értékekből áll.

<a name="coordinate-system"></a>**Koordináta-rendszer**: egy hivatkozási keretrendszer, amely két vagy három dimenzióban határozza meg a pontok helyét.

<a name="country-code"></a>**Országkód**: egy ország/régió egyedi azonosítója az ISO-szabvány alapján. A ISO2 egy ország/régió (például USA) kétkarakteres kódja, amely ISO3 a három karakterből álló kódot jelöli (például az USA-t).

<a name="country-subdivision"></a>**Ország alcsoportja**: egy ország/régió, más néven állam vagy megye első szintű alosztálya.

<a name="country-secondary-subdivision"></a>**Ország másodlagos alosztálya**: egy ország/régió második szintű felosztása, amely gyakran más néven megye.

<a name="country-tertiary-subdivision"></a>**Ország felsőfokú alosztálya**: egy ország/régió, jellemzően egy elnevezett terület, például egy osztály harmadik szintű alcsoportja.

<a name="cross-street"></a>**Cross Street**: egy pont, ahol két vagy több utca metszi egymást.

<a name="cylindrical-projection"></a>**Hengeres kivetítés**: egy gömb vagy gömb pontjait egy tangens vagy szekáns hengerre átalakító kivetítés. A henger ekkor felülről lefelé van darabolva, és egy síkban van lelapulva.

## <a name="d"></a>T

<a name="datum"></a>**Datum**: egy mérési rendszer, egy adott felületen (horizontális Datum) vagy magasságon (függőlegesen) belüli vagy alá tartozó magasságú koordinátarendszer hivatkozási előírásai.

<a name="dbf-file"></a>**DBF-fájl**: a ALAKZATFÁJLOK (SHP)-sel együtt használt adatbázis-fájlformátum.

<a name="degree-minutes-seconds-dms"></a>**Fok perc másodperc (DMS)**: a szélesség és a hosszúság leírására szolgáló mértékegység. A fok egy kör<sup>1/360.</sup> A fok további 60 percre oszlik, és egy perc 60 másodpercre van osztva.

<a name="delaunay-triangulation"></a>**Delaunay-háromszögárfolyam**: egy olyan technika, amely összefüggő, egymást nem fedő háromszögeket hoz létre a pontok adatkészletből. Az egyes háromszögek circumscribing-köre nem tartalmaz a belső adatkészletből származó pontokat.

<a name="demographics"></a>**Demográfiai** adatok: az emberi populáció statisztikai jellemzői (például az életkor, a születési arány és a jövedelem).

<a name="destination"></a>**Cél**: egy végpont vagy hely, ahol valaki utazik.

<a name="digital-elevation-model-dem"></a>**Digitális jogosultságszint-emelési modell (DEM)**: egy felülethez kapcsolódó jogosultságszint-emelési értékek adatkészlete, amely egy adott területen rendszeres időközönként rögzített, közös adatmennyiség használatával. A DEMs jellemzően a terepi segítség ábrázolására szolgálnak.

<a name="dijkstra's-algorithm"></a>**Dijkstra algoritmusa**: egy olyan algoritmus, amely megvizsgálja a hálózat kapcsolatát, és megkeresi a lehető legrövidebb útvonalat két pont között.

<a name="distance-matrix"></a>**Távolsági mátrix**: egy olyan mátrix, amely az utazási időt és a távolsági információt tartalmazza az eredetek és a célhelyek között. 

## <a name="e"></a>E

<a name="elevation"></a>**Jogosultságszint-emelés**: egy pont vagy egy objektum függőleges távolsága a hivatkozási felület vagy az adat alatt. A hivatkozási felület általában a tengerszint szintjének minősül. A Jogosultságszint-emelés általában a föld függőleges magasságára utal.

<a name="envelope"></a>**Boríték**: lásd a [határolókeret mezőt](#bounding-box).

<a name="extended-postal-code"></a>**Kiterjesztett irányítószám**: olyan postai kód, amely további információkat tartalmazhat. Az USA-ban például a zip-kódok öt számjegyből állnak. A zip + 4 néven ismert kiterjesztett zip-kód azonban négy további számjegyet tartalmaz. Ezek a további számjegyek a földrajzi szegmensek azonosítására szolgálnak az öt számjegyből álló kézbesítési területen, például a város blokkjában, az apartmanok csoportjain vagy egy posta mezőben. Annak ismerete, hogy a földrajzi szegmensek hogyan segítik a hatékony levelezés rendezését és kézbesítését.

<a name="extent"></a>**Mérték**: lásd a [határolókeret mezőt](#bounding-box).

## <a name="f"></a>F

<a name="federated-authentication"></a>**Összevont hitelesítés**: olyan hitelesítési módszer, amely lehetővé teszi egy egyszeri bejelentkezési/hitelesítési mechanizmus használatát több webes és mobil alkalmazás között. 

<a name="feature"></a>**Szolgáltatás**: egy olyan objektum, amely a geometriát egy további metaadat-információval kombinálja. 

<a name="feature-collection"></a>**Szolgáltatás gyűjteménye**: szolgáltatás-objektumok gyűjteménye.

<a name="find-along-route"></a>**Keresés az útvonalon**: olyan térbeli lekérdezés, amely egy megadott lefoglalási időponton vagy távolságon belül található adatokra keres.

<a name="find-nearby"></a>A **közelben található**: olyan térbeli lekérdezés, amely egy pontról rögzített egyenes távolságú (például a varjú legyek).

<a name="fleet-management"></a>**Flotta-felügyelet**: a kereskedelmi járművek, például az autók, teherautók, hajók és repülőgépek felügyeletét. A flotta-felügyelet számos függvényt tartalmazhat, például a járművek finanszírozását, karbantartását, telematikai (követési és diagnosztikai), valamint az illesztőprogramok, a sebesség, az üzemanyag, valamint az állapot-és biztonsági felügyeletet. A flotta-felügyelet a vállalatok által a vállalaton belüli szállításra támaszkodó folyamat. A vállalatok minimalizálni szeretnék a kockázatokat, és csökkentik a teljes szállítási és személyzeti költségeket, miközben biztosítják a kormányzati jogszabályoknak való megfelelést.

<a name="free-flow-speed"></a>**Szabad áramlási sebesség**: az ideális körülmények között a szabad áramlási sebesség várható. Általában a sebességkorlátozás.

<a name="free-form-address"></a>**Ingyenes űrlap címe**: teljes, egyetlen szövegsorként jelölt címnek.

<a name="fuzzy-search"></a>**Fuzzy keresés**: olyan keresés, amely egy ingyenes formátumú szöveges karakterláncot vesz igénybe, amely lehet egy adott címnek vagy érdekesnek. 

## <a name="g"></a>G

<a name="geocode"></a>**Geocode**: olyan címek vagy helyek, amelyek egy olyan koordináta-ba lettek átalakítva, amely a hely térképen való megjelenítésére szolgál. 

<a name="geocoding"></a>**Helymeghatározáshoz**: más néven továbbítási helymeghatározáshoz, az a folyamat, amellyel a helyadatok címe koordinátákba kerül. 

<a name="geodesic-path"></a>**Geodéziai elérési út**: a legrövidebb útvonal egy görbe felületen két pont között. Azure Maps megjelenítéskor ez az elérési út a Mercator-vetítés miatt ívelt vonalként jelenik meg.

<a name="geofence"></a>**Geokerítésen**: egy meghatározott földrajzi régió, amely az események aktiválására használható, amikor egy eszköz belép vagy létezik a régióba.

<a name="geojson"></a>**GeoJSON**: a földrajzi vektoros adatelemek, például pontok, vonalak és sokszögek tárolására használt gyakori JSON-alapú fájlformátum. **Megjegyzés**: Azure Maps a GeoJSON kiterjesztett verzióját használja az [itt dokumentált](extend-geojson.md)módon.

<a name="geometry"></a>**Geometria**: térbeli objektumot, például pontot, vonalat vagy sokszöget jelöl.

<a name="geometrycollection"></a>**GeometryCollection**: geometriai objektumok gyűjteménye.

<a name="geopol"></a>**GeoPol**: a geopolitikai szempontból bizalmas adatokra, például a vitatott szegélyekre és a nevekre hivatkozik.

<a name="georeference"></a>**Georeference**: a földrajzi adatok vagy képek egy ismert koordináta-rendszerbe való igazításának folyamata. Ez a folyamat az adat eltolását, elforgatását, méretezését vagy eldöntését is magában foglalhatja.

<a name="georss"></a>**GeoRSS**: XML-bővítmény a térbeli adatainak RSS-hírcsatornába való felvételéhez.

<a name="gis"></a>**GIS**: a "földrajzi információs rendszer" rövidítése. A leképezési iparág leírására használt általános kifejezés.

<a name="gml"></a>**GML**: földrajzi Markup Language néven is ismert. XML-fájlkiterjesztés a térbeli adatainak tárolásához.

<a name="gps"></a>**GPS**: más néven globális helymeghatározási rendszer, amely az eszközöknek a földön való elhelyezésére használt műholdak rendszerét határozza meg. A keringő műholdak olyan jeleket továbbítanak, amelyek lehetővé teszik, hogy a GPS-vevőkészülék bárhol a földön kiszámítsa a saját helyét a trilateration-on keresztül.

<a name="gpx"></a>**GPX**: más néven GPS Exchange-formátum, amely egy, a GPS-eszközökről gyakran létrehozott XML-fájlformátum.  

<a name="great-circle-distance"></a>**Nagy** hatótávolságú távolság: a legrövidebb távolság a gömb felületén található két pont között.

<a name="greenwich-mean-time-gmt"></a>**Greenwichi középidő (GMT)**: a Prime meridián időszaka, amely a Royal Observatory-n keresztül fut Angliában.

<a name="guid"></a>**GUID**: globálisan egyedi azonosító. Egy felület, osztály, típustár, összetevő kategória vagy rekord egyedi azonosítására szolgáló sztring.

## <a name="h"></a>H

<a name="haversine-formula"></a>**Haversine Formula**: a gömb két pontja közötti nagy távolság kiszámításához használt közös egyenlet.

<a name="hd-maps"></a>**HD Maps**: a High Definition Maps néven is ismert, magas szintű hűségű közúti hálózati információk, például sáv-megjelölések, aláírások és irányjelző fények szükségesek az autonóm vezetéshez.

<a name="heading"></a>**Fejléc**: az a irány, ami mutat vagy szembesül. Lásd még: [csapágy](#heading).

<a name="heatmap"></a>**Hő**: egy adatvizualizáció, amelyben a különböző színek egy adott terület pontjainak sűrűségét jelölik. Lásd még: tematikus Térkép.

<a name="hybrid-imagery"></a>**Hibrid képek**: olyan műhold-vagy légi rendszerképek, amelyeken a közúti adatok és a címkék bekerülnek.

## <a name="i"></a>I

<a name="iana"></a>**IANA**: az internethez rendelt számok szolgáltatójának betűszója. Egy nonprofit csoport, amely felügyeli a globális IP-címek kiosztását.

<a name="isochrone"></a>**Isochrone**: a Isochrone határozza meg azt a körzetet, amelyben valaki egy adott időpontra vonatkozóan egy adott időpontban utazhat egy adott időtartamon belül. Lásd még: [elérhető tartomány](#reachable-range).

<a name="isodistance"></a>**Isodistance**: adott helyen a isochrone határozza meg azt a területét, ahol valaki egy megadott távolságon belül utazhat bármely irányban. Lásd még: [elérhető tartomány](#reachable-range).

## <a name="k"></a>K

<a name="kml"></a>**KML**: a "kulcslyuk Markup Language" néven is ismert formátum a földrajzi vektoros adatfájlok, például pontok, vonalak és sokszögek tárolására szolgáló közös XML-fájlformátum. 

## <a name="l"></a>L

<a name="landsat"></a>**Landsat**: többspektrumos, a NASA által fejlesztett, a föld képének begyűjtését biztosító műholdak. Ez a képek számos iparágban, például a mezőgazdaságban, az erdészetben és a térképészeti szolgáltatásokban használatosak.

<a name="latitude"></a>**Szélesség**: a szögletes távolság az Egyenlítőtől az Észak-vagy Dél irányában mérve.

<a name="level-of-detail"></a>**Részletességi szint**: lásd a nagyítási szintet.

<a name="lidar"></a>**Sümeg**: betűszó a könnyű észleléshez és a sokrétű használathoz. Olyan távoli érzékelési módszer, amely lézerek használatával méri a távolságokat a tükröző felületektől.

<a name="linear-interpolation"></a>**Lineáris interpoláció**: ismeretlen érték becslése az ismert értékek közötti lineáris távolság használatával.

<a name="linestring"></a>**LineString**: egy vonal ábrázolására szolgáló geometria. Más néven vonallánc. 

<a name="localization"></a>**Honosítás**: a különböző nyelvek és kultúrák támogatása.

<a name="logistics"></a>**Logisztika**: a személyek, a járművek, a készletek vagy az eszközök koordinált módon történő áthelyezésének folyamata.

<a name="longitude"></a>**Hosszúság**: a szög távolsága a Prime Meridian-től egy keleti vagy nyugati irányban.

## <a name="m"></a>M

<a name="map-tile"></a>**Térkép csempe**: egy Térkép vászon partícióját jelképező téglalap alakú ábra. További információkért lásd a [nagyítási szintek és a csempe rácsa dokumentációját](zoom-levels-and-tile-grid.md).

<a name="marker"></a>**Marker**: PIN-ként vagy gombostű is ismert, egy ikon, amely egy Térkép pont helyét jelöli.

<a name="mercator-projection"></a>**Mercator-vetítés**: egy hengeres leképezési vetület, amely a szokásos leképezési leképezést a hajózási célokra képezi, mert az állandó tanfolyam sorait, vagyis a loxodroma vonalakat jelöli, egyenes szegmensként, amely a szélességi köröket őrzi meg. Az összes strukturált térképes vetítés a Föld felszínének valódi elrendezéséhez képest torzítja a Térkép alakzatait vagy méretét. A Mercator-vetítés eltúlozza az Egyenlítőtől távol eső területeket, például úgy, hogy a kisebb területek nagyobbak lesznek a térképen a pólusok megközelítése során. 

<a name="multilinestring"></a>**MultiLineString**: a LineString objektumok gyűjteményét jelképező geometria. 

<a name="multipoint"></a>**Multipoint**: a pont objektumok gyűjteményét jelképező geometria.

<a name="multipolygon"></a>**Többsokszögű**: a sokszög objektumok gyűjteményét jelképező geometria. Ha például a Hawaii határát szeretné megjeleníteni, az egyes szigeteket sokszögtel kell megrajzolni. Így a Hawaii határa egy többsokszögű.

<a name="municipality"></a>**Település**: város vagy város. 

<a name="municipality-subdivision"></a>**Önkormányzat alosztálya**: egy önkormányzat, például egy környék vagy egy helyi név (például "Downtown") alosztálya.

## <a name="n"></a>N

<a name="navigation-bar"></a>**Navigációs sáv**: a nagyítási szint, a szurok, a forgás és az alapszintű Térkép váltásához használt Térkép vezérlőelem-készlete.

<a name="nearby-search"></a>**Közeli keresés**: olyan térbeli lekérdezés, amely egy pontról rögzített egyenes távolságú (például a varjú legyek) távolságra keres.

<a name="neutral-ground-truth"></a>**Semleges alapigazság**: egy Térkép, amely a címkét az általa képviselt régió hivatalos nyelvén jeleníti meg, és ha elérhető, helyi parancsfájlokban.

## <a name="o"></a>O

<a name="origin"></a>**Forrás**: egy kezdőpont vagy hely, ahol a felhasználó.

## <a name="p"></a>P

<a name="panning"></a>**Pásztázás**: a Térkép bármilyen irányban történő mozgatásának folyamata, miközben állandó nagyítási szintet tart fenn.

<a name="parcel"></a>**Parcella**: terület vagy tulajdonság határa.

<a name="pitch"></a>**Pitch**: a Térkép dőlésének értéke a függőlegesen, ahol a 0 a térképen egyenesen lefelé keres.

<a name="point"></a>**Pont**: a térképen egyetlen pozíciót jelölő geometria. 

<a name="points-of-interest-poi"></a>**Érdekes helyek (POI)**: egy üzleti, mérföldkő vagy közös érdekű hely.

<a name="polygon"></a>**Sokszög**: egy Térkép területét jelképező szilárd geometria. 

<a name="polyline"></a>**Vonallánc**: vonal ábrázolására szolgáló geometria. Más néven LineString. 

<a name="position"></a>**Pozíció**: egy pont hosszúsági, szélességi és magassági (x, y, z koordinátái).

<a name="post-code"></a>**Irányítószám: lásd** a [postai irányítószámot](#postal-code).

<a name="postal-code"></a>**Irányítószám**: több betű vagy szám, vagy mindkettő egy adott formátumban. A postai irányítószámot egy ország/régió postai szolgáltatása használja a földrajzi területek zónákra osztására a levelezés leegyszerűsítése érdekében.

<a name="primary-key"></a>**Elsődleges kulcs**: a Azure Maps megosztott kulcsú hitelesítéshez megadott két előfizetési kulcs első része. Lásd: [megosztott kulcsos hitelesítés](#shared-key-authentication).

<a name="prime-meridian"></a>**Prime meridián**: a hosszúság 0 fokos hosszúságú vonalát jelöli. A hosszúsági értékek általában akkor csökkennek, ha a nyugati irányba utaznak, amíg 180 fok, és a keleti irányban a-180-fokban való utazás megnövekszik. 

<a name="prj"></a>**PRJ**: egy szövegfájl, amely gyakran tartalmaz egy alakzatfájl-fájlt, amely az adathalmazban található, előre jelzett koordináta-rendszerrel kapcsolatos információkat tartalmazza.

<a name="projection"></a>**Kivetítés**: egy térképes kivetítésen alapuló tervezett koordináta-rendszer, például a transzverzális Mercator, a Albers egyenlő terület és a Robinson. Ezek lehetővé teszik a Föld gömb alakú felületének leképezését egy kétdimenziós Descartes koordináta-síkra. A tervezett koordináta-rendszereket időnként Térkép-kivetítésnek is nevezzük.

## <a name="q"></a>Q

<a name="quadkey"></a>**Quadkey**: egy quadtree-elrendezési rendszeren belüli csempéhez tartozó Base-4 cím index. További információt a [nagyítási szintek és a csempe rácsa](zoom-levels-and-tile-grid.md) dokumentációjában talál.

<a name="quadtree"></a>**Quadtree**: olyan adatstruktúra, amelyben az egyes csomópontok pontosan négy gyermeket foglalnak magukban. A Azure Mapsben használt csemperendszer olyan quadtree-struktúrát használ, mint a felhasználó egy szinten nagyítja fel az egyes Térkép csempéket négy alcsempére.  További információt a [nagyítási szintek és a csempe rácsa](zoom-levels-and-tile-grid.md) dokumentációjában talál.

<a name="queries-per-second-qps"></a>**Lekérdezések másodpercenként (QPS)**: egy adott szolgáltatás vagy platform számára elérhető lekérdezések és kérések száma egy másodpercen belül. 

## <a name="r"></a>R

<a name="radial-search"></a>**Sugárirányú keresés**: olyan térbeli lekérdezés, amely egy pontról rögzített egyenes távolságú (például a varjú legyek) távolságra keres. 

<a name="raster-data"></a>**Raszteres adatok**: olyan cellák (vagy képpontok) mátrixa, amely sorokba és oszlopokba (vagy rácsba) van rendezve, ahol minden cella olyan értéket tartalmaz, amely információkat jelképez, például a hőmérsékletet. A raszterek közé tartoznak a digitális légi fényképek, a szatellitekről, a digitális képekről és a beolvasott térképekről származó képek.

<a name="raster-layer"></a>**Raszteres réteg**: raszteres képekből álló csempe réteg.

<a name="reachable-range"></a>**Elérhető tartomány**: egy elérhető tartomány határozza meg azt a területét, ahol valaki utazhat egy adott időtartamon vagy távolságon belül, egy adott hely bármely irányában. Lásd még: [Isochrone](#isochrone) és [Isodistance](#isodistance).

<a name="remote-sensing"></a>**Távoli érzékelés**: az érzékelő adatainak távolságból való összegyűjtésének és értelmezésének folyamata.

<a name="rest-service"></a>**Rest-szolgáltatás**: a betűszó Rest áll a reprezentációs állapot átviteléhez. A REST-szolgáltatás egy URL-alapú webszolgáltatás, amely az alapszintű webes technológiára támaszkodik, a leggyakoribb módszerek a HTTP GET és a POST kérések. Az ilyen típusú szolgáltatások sokkal gyorsabbak és kisebbek, mint a hagyományos SOAP-alapú szolgáltatások.

<a name="reverse-geocode"></a>**Fordított geocode**: az a folyamat, amelynek során egy koordinátákat kell megállapítani, és meg kell határozni, hogy mely címekre vonatkozik a Térkép.

<a name="reproject"></a>**Újratervezés**: lásd az [átalakítást](#transformation).

<a name="rest-service"></a>**Rest-szolgáltatás**: betűszó a reprezentációs állapot átviteléhez. Architektúra egy decentralizált, elosztott környezetben található társak közötti információcseréhez. A REST lehetővé teszi, hogy a különböző számítógépeken lévő programok az operációs rendszertől vagy a platformtól függetlenül kommunikáljanak egymással. A szolgáltatások küldhetnek Hypertext Transfer Protocol (HTTP) kérelmet egy egységes erőforrás-lokátornak (URL), és visszakérhetik az adatforrást.

<a name="route"></a>**Route**: két vagy több helyszín közötti elérési út, amely további információkat is tartalmazhat, például a fordulópontokra vonatkozó utasításokat az útvonal mentén.

<a name="requests-per-second-rps"></a>**Kérelmek/másodperc (RPS)**: [lekérdezések másodpercenkénti száma (QPS)](#queries-per-second-qps). 

<a name="rss"></a>**RSS**: betűszó a Really Simple Syndication, Resource Description FRAMEWORK (RDF) webhelyek összefoglalásához vagy a részletes hely összegzéséhez a forrástól függően. Egy egyszerű, strukturált XML-formátum a tartalmak különböző webhelyek közötti megosztásához. Az RSS-dokumentumok olyan kulcsfontosságú metaadatokat tartalmaznak, mint például a szerző, a dátum, a cím, a rövid leírás és a Hypertext-hivatkozás. Ez az információ segít a felhasználónak (vagy egy RSS-közzétevő szolgáltatásnak) eldönteni, hogy milyen anyagokat érdemes további vizsgálatra.

## <a name="s"></a>S

<a name="satellite-imagery"></a>**Műholdképek**: a sík és a műholdak által rögzített, lefelé mutató képek.

<a name="secondary-key"></a>**Másodlagos kulcs**: a Azure Maps megosztott kulcsú hitelesítéshez megadott két előfizetési kulcs második másodperce. Lásd: [megosztott kulcsos hitelesítés](#shared-key-authentication).

<a name="shapefile-shp"></a>**Alakzatfájl (SHP)**: más néven ESRI-alakzatfájl, egy vektoros adattárolási formátum, amely a földrajzi funkciók helyét, alakját és attribútumait tárolja. A alakzatfájl a rendszer a kapcsolódó fájlok készletében tárolja.

<a name="shared-key-authentication"></a>**Megosztott kulcsos hitelesítés**: a megosztott kulcsos hitelesítés a Azure Maps fiók által generált kulcsok átadását adja meg minden egyes Azure Mapsre vonatkozó kéréssel. Ezeket a kulcsokat gyakran előfizetési kulcsoknak nevezzük. Javasoljuk, hogy a kulcsokat rendszeresen újragenerálja a biztonsághoz. Két kulcs van megadva, így a kapcsolatok egy kulccsal kezelhetők, miközben újragenerálja a másikat. A kulcsok újragenerálása után frissítenie kell minden olyan alkalmazást, amely hozzáfér ehhez a fiókhoz az új kulcsok használatához. A Azure Maps hitelesítéssel kapcsolatos további tudnivalókért tekintse meg a [Azure Maps és az Azure ad](azure-maps-authentication.md) és [a hitelesítés kezelése a Azure Maps-ben](how-to-manage-authentication.md)című témakört.

<a name="software-development-kit-sdk"></a>**Szoftverfejlesztői készlet (SDK)**: dokumentáció, mintakód és minta alkalmazások gyűjteménye, amelyek segítségével a fejlesztők API-t használhatnak alkalmazások készítéséhez.

<a name="spherical-mercator-projection"></a>**Gömb Mercator-vetítés**: lásd a [web Mercator](#web-mercator)-t. 

<a name="spatial-query"></a>**Térbeli lekérdezés**: egy olyan szolgáltatásra irányuló kérelem, amely térbeli műveletet hajt végre. Például egy kör alakú keresés vagy egy útvonalon történő keresés.

<a name="spatial-reference"></a>**Térbeli hivatkozás**: a földrajzi entitások pontos megtalálásához használt koordináta-alapú helyi, regionális vagy globális rendszer. Meghatározza azt a koordináta-rendszert, amely a Térkép-koordinátákat a valós világ helyeihez rendeli. A térbeli hivatkozások biztosítják a különböző rétegekből vagy forrásokból származó térbeli adatok integrálását a pontos megtekintéshez vagy elemzéshez. A Azure Maps a következő [EPSG használja: 3857](https://epsg.io/3857) koordináta-hivatkozási rendszer és WGS 84 a bemeneti geometriai adatokhoz.

<a name="sql-spatial"></a>**SQL térbeli**: az SQL Azureba és SQL Server 2008-es vagy újabb verzióba épített térbeli funkciókra hivatkozik. Ez a térbeli funkció olyan .NET-függvénytárként is elérhető, amely a SQL Servertól függetlenül is használható. További információt a [térbeli adatok (SQL Server) dokumentációjában](/sql/relational-databases/spatial/spatial-data-sql-server) talál.

<a name="subscription-key"></a>**Előfizetési kulcs**: lásd a [megosztott kulcsos hitelesítést](#shared-key-authentication).

<a name="synchronous-request"></a>**Szinkron kérés**: egy HTTP-kérelem megnyitja a kapcsolatokat, és megvárja a választ. A böngészők korlátozzák az oldalon elérhető egyidejű HTTP-kérelmek számát. Ha egyszerre több hosszú ideig futó szinkron kérést hajt végre, akkor ez a korlát elérhető. A rendszer késlelteti a kérelmeket, amíg az egyik másik kérelem nem fejeződött be.

## <a name="t"></a>T

<a name="telematics"></a>**Telematika**: információk küldése, fogadása és tárolása a telekommunikációs eszközökön keresztül a távoli objektumok vezérlésének hatásával együtt. 

<a name="temporal-data"></a>**Időbeli adatok**: az adatok, amelyek kifejezetten az időpontokra vagy dátumokra utalnak. Az időbeli adatkezelés diszkrét eseményekre, például villámokra is utalhat; objektumok, például vonatok mozgatása; vagy ismétlődő megfigyelések, például a forgalmi érzékelők száma.

<a name="terrain"></a>**Terep**: egy olyan terület, amely egy adott jellemzővel rendelkezik, például a homokos terepen vagy a hegyvidéki terepen.

<a name="thematic-maps"></a>**Tematikus térképek**: egy tematikus Térkép egy egyszerű Térkép, amely egy földrajzi területtel kapcsolatos témát tükröz. Az ilyen típusú leképezések gyakori forgatókönyve, hogy bizonyos mérőszámok alapján a felügyeleti régiókat, például az országokat/régiókat kell megszínezni.

<a name="tile-layer"></a>**Csempe rétege**: a Térkép csempéi (téglalap alakú szakasz) egy folytonos rétegbe való összeállításával megjelenített réteg. A csempék raszteres képcsempék vagy vektoros csempék. A raszteres csempék rétegei általában az idő előtt jelennek meg, és képként tárolódnak a kiszolgálón. A raszteres csempék rétegei nagy tárterületet használhatnak. A vektoros csempe rétegei az ügyfélalkalmazás közelében valós időben jelennek meg. Így a kiszolgálóoldali tárolási követelmények kisebbek a vektoros csempék rétegeinél.

<a name="time-zone"></a>**Időzóna**: a világ egyik régiója, amely a jogi, kereskedelmi és közösségi célokra egységes, általános időt vesz figyelembe. Az időzónák általában követik az országok/régiók és az albontások határait.

<a name="transaction"></a>**Tranzakció**: a Azure Maps tranzakciós licencelési modellt használ, ahol;

- Egy tranzakció jön létre minden 15 térképhez vagy a kért forgalmi csempéhez.
- Egy tranzakció jön létre minden API-híváshoz Azure Maps egyik szolgáltatásához. A keresés és az Útválasztás Azure Maps szolgáltatásra mutat példát.

<a name="transformation"></a>**Átalakítás**: az Adatátalakítási folyamat különböző földrajzi koordináta-rendszerek között. Előfordulhat például, hogy van néhány, az Egyesült Királyságban rögzített és a OSGB 1936 földrajzi koordináta-rendszeren alapuló információ. A Azure Maps a [EPSG: 3857](https://epsg.io/3857) koordináta hivatkozási rendszer WGS84-változatát használja. Az adatok megfelelő megjelenítéséhez az szükséges, hogy a koordinátáit az egyik rendszerről a másikra alakítsa át.

<a name="traveling-salesmen-problem-tsp"></a>**Utazási ügynökökkel kapcsolatos probléma (TK)**: egy Hamilton áramköri probléma, amelyben az üzletkötőnek meg kell találnia a leghatékonyabb módot a leállások sorozatának megkeresésére, majd vissza kell térnie a kiindulási helyhez.  

<a name="trilateration"></a>**Trilateration**: egy pont pozíciójának meghatározása a Föld felszínén, két további pontra vonatkozóan, a három pont közötti távolság mérésével.

<a name="turn-by-turn-navigation"></a>**Kapcsolja be a navigációt**: egy olyan alkalmazás, amely útvonalat ad meg az útvonal egyes lépéseihez, mivel a felhasználók a következő manővert közelítik meg.

## <a name="v"></a>V

<a name="vector-data"></a>**Vektoros** adatértékek: a pontokkal, vonalakkal vagy sokszögekkel jelölt adathalmazok koordinálása.

<a name="vector-tile"></a>**Vector csempe**: egy nyílt adatspecifikáció a térinformatikai vektoros adatok tárolásához ugyanazzal a csempe-rendszerrel, mint a Térkép vezérlőelem. Lásd még: [csempe réteg](#tile-layer).

<a name="vehicle-routing-problem-vrp"></a>**Jármű-útválasztási probléma (VRP)**: a problémák olyan csoportja, amely során a rendszer kiszámítja a járműpark megrendelt útvonalait a megkötések készletének figyelembevételével. Ezek a korlátozások magukban foglalhatják a kézbesítési időt, a több útvonal-kapacitást és az utazási időtartamra vonatkozó korlátozásokat.

<a name="voronoi-diagram"></a>**Voronoi ábra**: a területekre vagy cellákra mutató, a geometriai objektumokat tartalmazó partíciók, amelyek általában a pontok funkciói. Ezeknek a celláknak vagy sokszögeknek meg kell felelniük a Delaunay-háromszögek feltételeinek. Egy adott helyen belüli összes hely közelebb van az objektumhoz, mint a készletben lévő többi objektumhoz. A Voronoi-diagramok gyakran használják a földrajzi funkciók körét befolyásoló területeket. 

## <a name="w"></a>W

<a name="waypoint"></a>**Útpont**: az útpont egy meghatározott földrajzi hely, amelyet a hosszúság és a szélesség a navigációs célokra használ. Gyakran használják arra a pontra, amelyben valaki navigál az útvonalon.

<a name="waypoint-optimization"></a>**Útpont optimalizálás**: az útpontok egy halmazának átrendezésének folyamata az összes megadott útpont eléréséhez szükséges utazási idő vagy távolság minimálisra csökkentése érdekében. Az optimalizálás összetettségétől függően ez az optimalizálás gyakran az [Utazási ügynökök problémája](#traveling-salesmen-problem-tsp) vagy a [jármű-útválasztási probléma](#vehicle-routing-problem-vrp).

<a name="web-map-service-wms"></a>**Web Map szolgáltatás (WMS)**: a WMS egy nyílt földrajzi konzorcium (OGC) szabvány, amely a képalapú térképi szolgáltatásokat határozza meg. A WMS szolgáltatások a térképen igény szerint meghatározott területekhez biztosítanak leképezési képeket. A képek tartalmazhatnak előre megjelenített jelképeket, és a szolgáltatás által definiált különböző elnevezett stílusok egyikében is megjeleníthető.

<a name="web-mercator"></a>**Web Mercator**: más néven gömb-Mercator kivetítés. Ez a Mercator-vetítés enyhe változata, amely elsősorban a webes leképezési programokban használatos. Ugyanazokat a képleteket használja, mint a szabványos Mercator-leképezés, amelyet a kisméretű térképekhez használtak. A web Mercator azonban a gömb képleteit minden skálán használja, de a nagyméretű Mercator-térképek általában a kivetítés ellipsoidal formáját használják. Az eltérés a globális méretekben nem észlelhető, de a helyi területek térképeit kiváltva kis mértékben eltér a valódi ellipsoidal Mercator-térképektől, ugyanazon a skálán.

<a name="wgs84"></a>**WGS84**: a Térkép felszínén található térbeli koordinátákat használó állandók halmaza. A WGS84 Datum a legtöbb online leképezési szolgáltató és a GPS-eszköz által használt standard. A Azure Maps a [EPSG: 3857](https://epsg.io/3857) koordináta hivatkozási rendszer WGS84-változatát használja.

## <a name="z"></a>Z

<a name="z-coordinate"></a>**Z-koordináta**: lásd a [magasságot](#altitude). 

<a name="zip-code"></a>**Zip-kód**: lásd az [irányítószámot](#postal-code).

<a name="Zoom level"></a>**Nagyítási szint**: meghatározza a részletes részletességi szintet, valamint azt, hogy a Térkép mekkora legyen látható. Ha a 0. szintre nagyítva van, a teljes globális Térkép gyakran láthatóvá válik. A Térkép azonban a korlátozott részleteket jeleníti meg, például az ország/régió nevét, a szegélyeket és az óceán nevét. A 17. szinthez közelebbi nagyítás esetén a Térkép néhány város területét jeleníti meg, részletes információkkal. Azure Maps a legmagasabb nagyítási szint 22. További információkért lásd a [nagyítási szintek és a csempe rácsa](zoom-levels-and-tile-grid.md) dokumentációját.