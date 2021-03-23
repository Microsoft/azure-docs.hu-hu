---
title: Azure Maps web SDK – ajánlott eljárások | Microsoft Azure térképek
description: Tippek & trükkök a Azure Maps web SDK használatának optimalizálásához.
author: rbrundritt
ms.author: richbrun
ms.date: 3/22/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: ba351053c876c31d945ec7e4127a5caebd6a71ce
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104878040"
---
# <a name="azure-maps-web-sdk-best-practices"></a>Azure Maps web SDK – ajánlott eljárások

Ez a dokumentum a Azure Maps web SDK ajánlott eljárásaira koncentrál, azonban az ajánlott eljárások és optimalizálások számos más Azure Maps SDK-ra alkalmazhatók.

A Azure Maps web SDK számos különböző módon biztosítja a nagyméretű térbeli adatkészletek megjelenítésének hatékony vászonját. Bizonyos esetekben az adatok többféleképpen is elvégezhetők, de az adathalmaz méretétől és a kívánt funkcióktól függően az egyik módszer jobban elvégezhető, mint a többi. Ez a cikk az ajánlott eljárásokat és tippeket és trükköket mutatja be a teljesítmény maximalizálásához és zökkenőmentes felhasználói élmény létrehozásához.

Általánosságban elmondható, hogy a Térkép teljesítményének növelésekor a rétegek és a források számának csökkentését, valamint az adathalmazok és a megjelenítési stílusok összetettségét keresi.

## <a name="security-basics"></a>Biztonsági alapok

Az alkalmazás egyetlen legfontosabb része a biztonsága. Ha az alkalmazás nem biztonságos, a hacker bármilyen alkalmazást tönkreteheti, függetlenül attól, hogy milyen jó a felhasználói élmény. Az alábbiakban néhány tippet találhat a Azure Maps alkalmazás biztonságának fenntartásához. Az Azure használatakor ügyeljen arra, hogy megismerkedjen az Ön számára elérhető biztonsági eszközökkel. Tekintse meg ezt a dokumentumot az [Azure Security bevezetéséhez](https://docs.microsoft.com/azure/security/fundamentals/overview).

> [!IMPORTANT]
> Azure Maps két hitelesítési módszert biztosít.
> * Előfizetés-kulcs alapú hitelesítés
> * Azure Active Directory hitelesítés minden üzemi alkalmazásban Azure Active Directory használ.
> Az előfizetések kulcs alapú hitelesítése egyszerű, és a legtöbb leképezési platform a legegyszerűbb módszer a platform használatának a számlázási célokra való mérésére. Ez azonban nem biztonságos hitelesítési forma, és csak helyileg használható az alkalmazások fejlesztésekor. Egyes platformok lehetővé teszik, hogy korlátozza, hogy mely IP-címek és/vagy HTTP-hivatkozók legyenek a kérelmekben, de ezek az információk könnyen megoldhatók. Ha előfizetési kulcsokat használ, ne felejtse el [rendszeresen forgatni őket](how-to-manage-authentication.md#manage-and-rotate-shared-keys).
> A Azure Active Directory egy nagyvállalati identitás-szolgáltatás, amely számos különböző alkalmazás-forgatókönyvhöz biztosít biztonsági funkciókat és beállításokat. A Microsoft azt javasolja, hogy a Azure Mapst használó összes éles alkalmazás Azure Active Directory hitelesítéshez.
> További információ a [hitelesítés kezeléséről a Azure Maps](how-to-manage-authentication.md) ebben a dokumentumban.

### <a name="secure-your-private-data"></a>Személyes adatai védelme

Ha az Azure Maps interaktív térképi SDK-hoz hozzáadott adatbevitelt, a rendszer helyileg jeleníti meg a végfelhasználó eszközén, és semmilyen okból soha nem küldi vissza az internetre.

Ha az alkalmazás olyan adatfeltöltést végez, amely nem lehet nyilvánosan elérhető, győződjön meg róla, hogy az adatai biztonságos helyen vannak tárolva, és hogy maga az alkalmazás zárolva van, és csak a kívánt felhasználók számára érhető el. Ha a fenti lépések bármelyikét kihagyja, a jogosulatlan személyek hozzáférhetnek ezekhez az adatkezelési lehetőségekhez. Azure Active Directory segíthet a zárolás leállításában.

Ez az oktatóanyag a [Azure app Service-on futó webalkalmazás hitelesítésének hozzáadását](https://docs.microsoft.com/azure/app-service/scenario-secure-app-authentication-app-service) ismerteti.

### <a name="use-the-latest-versions-of-azure-maps"></a>A Azure Maps legújabb verzióinak használata

A Azure Maps SDK-k rendszeres biztonsági tesztelést végeznek, valamint az SDK-k által használható külső függőségi kódtárakat is. Minden ismert biztonsági probléma időben rögzített, és éles környezetben is megjelent. Ha az alkalmazás a Azure Maps web SDK üzemeltetett verziójának legújabb főverziójára mutat, automatikusan megkapja az összes olyan alverzió-frissítést, amely tartalmazni fogja a biztonsággal kapcsolatos javításokat.

Ha a Azure Maps web SDK-t a NPM modulon keresztül futtatja, ügyeljen arra, hogy a kalap (^) szimbólumot a fájlban lévő Azure Maps NPM-csomag verziószámával együtt használja, `package.json` hogy mindig a legújabb alverzióra mutasson.

```json
"dependencies": {
  "azure-maps-control": "^2.0.30"
}
```

## <a name="optimize-initial-map-load"></a>A kezdeti Térkép terhelésének optimalizálása

Egy weblap betöltését követően az egyik első dolog, amit el szeretne végezni, a lehető leghamarabb elkezdi a renderelést, hogy a felhasználó ne legyen üres képernyő.

### <a name="watch-the-maps-ready-event"></a>A Maps Ready esemény megtekintése

Hasonlóképpen, amikor a Térkép kezdetben betöltődik, a lehető leghamarabb be kell töltenie az adatgyűjtést, így a felhasználó nem keres üres térképet. Mivel a Térkép aszinkron módon tölti be az erőforrásokat, meg kell várnia, amíg a Térkép készen áll a működésére, mielőtt a saját adatait is fel kellene használni. Két eseményt várhat, egy `load` eseményt és egy `ready` eseményt. A betöltési esemény akkor indul el, ha a Térkép befejezte a Térkép kezdeti nézetének teljes betöltését, és minden Térkép csempe betöltődött. A Ready (kész) esemény akkor indul el, amikor a minimális leképezési erőforrások szükségesek a térképen való interakció megkezdéséhez. A Ready esemény gyakran tüzet okozhat a betöltési esemény időpontjában, ami lehetővé teszi, hogy hamarabb megkezdje az adatai betöltését a térképre.

### <a name="lazy-load-the-azure-maps-web-sdk"></a>Lusta betöltés a Azure Maps web SDK

Ha a Térkép nem szükséges azonnal, akkor a Azure Maps web SDK-t csak akkor töltse be, ha szükséges. Ezzel a művelettel késleltetheti a Azure Maps web SDK által használt JavaScript-és CSS-fájlok betöltését, amíg szükséges. Gyakori eset, ha a Térkép betöltődik egy lapon vagy egy olyan menü-panelen, amely nem jelenik meg az oldal betöltésén.
Az alábbi mintakód bemutatja, hogyan késleltetheti a Azure Maps web SDK betöltését a gomb lenyomása után.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="A Térkép lusta betöltése" src="https://codepen.io/azuremaps/embed/vYEeyOv?height=500&theme-id=default&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Lásd a tollat <a href='https://codepen.io/azuremaps/pen/vYEeyOv'>lusta</a> Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) a <a href='https://codepen.io'>CodePen</a>-on.
</iframe>

### <a name="add-a-placeholder-for-the-map"></a>Helyőrző hozzáadása a térképhez

Ha a Térkép a hálózati korlátozások vagy az alkalmazás más prioritásai miatt betöltési időt vesz igénybe, érdemes lehet egy kis háttérképet hozzáadni a térképhez a `div` Térkép helyőrzőként. A betöltéskor a Térkép üresen jelenik `div` meg.

### <a name="set-initial-map-style-and-camera-options-on-initialization"></a>Kezdeti Térkép stílusa és a kamera beállításainak megadása az inicializáláskor

Gyakran az alkalmazások egy adott helyre vagy stílusba szeretnék betölteni a leképezést. Néha a fejlesztők megvárhatják a Térkép betöltését (vagy megvárni az `ready` eseményt), majd a `setCemer` `setStyle` Térkép vagy funkcióját használják. Ez általában hosszabb időt vesz igénybe a kívánt kezdeti térképi nézethez képest, mivel a rendszer alapértelmezés szerint több erőforrást is betölt, mielőtt a kívánt térképi nézethez szükséges erőforrások betöltődik. Jobb megoldás, ha az inicializáláskor a kívánt térképi kamerát és a stílus beállításait a térképre helyezi át.

## <a name="optimize-data-sources"></a>Adatforrások optimalizálása

A web SDK két adatforrással rendelkezik,

* **GeoJSON forrás**: Ez a `DataSource` osztály a nyers helyadatok helyi GeoJSON-formátumban való kezelése. Kis-és közepes adatkészletekhez jó (több százezer funkció).
* **Vektoros csempe forrása**: ismert a `VectorTileSource` osztályban, betölti az aktuális leképezési nézethez tartozó vektor csempéket a Maps csempe rendszer alapján. Ideális megoldás a nagy adathalmazok számára (több millió vagy több milliárd funkció).

### <a name="use-tile-based-solutions-for-large-datasets"></a>Csempe-alapú megoldások használata nagyméretű adatkészletekhez

Ha több millió funkciót tartalmazó nagyobb adatkészletekkel dolgozik, az optimális teljesítmény elérésének ajánlott módja az, hogy egy kiszolgálóoldali megoldással, például a vektoros vagy raszteres képcsempe szolgáltatással tegye elérhetővé az adatokat.  
A vektoros csempék úgy vannak optimalizálva, hogy csak a csempe fókusz területére kivágott geometriai adatok betöltésére legyenek kitöltve.

A [Azure Maps létrehozó platform](creator-indoor-maps.md) lehetővé teszi az adatlekérdezést a vektoros csempék formátumában. Más adatformátumok olyan eszközöket is használhatnak, mint a [Tippecanoe](https://github.com/mapbox/tippecanoe) , vagy az ezen a lapon található számos [erőforrás lista](https://github.com/mapbox/awesome-vector-tiles)egyike.

Létrehozhat egy egyéni szolgáltatást is, amely raszteres képcsempeként jeleníti meg az adatkészleteket, és betölti az adatokat a Map SDK TileLayer osztályának használatával. Ez kivételes teljesítményt nyújt, mivel a Térkép csak néhány tucat rendszerkép betöltését és kezelését igényli. A raszteres csempék használata azonban bizonyos korlátozásokkal jár, mivel a nyers adatai helyileg nem érhetők el. Egy másodlagos szolgáltatásra gyakran szükség van bármilyen interakciós tapasztalat kikapcsolására, például megtudhatja, milyen formára kattint a felhasználó. Emellett a raszteres csempék fájlmérete gyakran nagyobb, mint egy általánosított és nagyítási szintű optimalizált geometriát tartalmazó tömörített vektoros csempe.

További információ az adatforrásokról az adatforrás [létrehozása](create-data-source-web-sdk.md) dokumentumban.

### <a name="combine-multiple-datasets-into-a-single-vector-tile-source"></a>Több adatkészlet egyesítése egyetlen vektoros csempe-forrásba

Minél kevesebb adatforrást kell kezelnie a térképen, annál gyorsabban dolgozhat az összes megjeleníthető funkció. Különösen, ha a csempék forrásaként két vektoros forrást egyesít, a csempék beolvasására szolgáló HTTP-kérések számát, a teljes adatmennyiség pedig valamivel kisebb lesz, mivel csak egy fájl fejléce van.

Egy vektoros csempe-forrás több adatkészletének kombinálásával egy eszköz, például a [Tippecanoe](https://github.com/mapbox/tippecanoe)segítségével is elérhető. Az adatkészletek kombinálhatók egyetlen szolgáltatás-gyűjteménybe, vagy külön rétegben, a forrás-rétegként ismert vektoros csempén belül. Amikor egy vektoros csempe forrását egy renderelési réteghez csatlakoztatja, megadhatja azt a forrásoldali réteget, amely a réteggel megjeleníteni kívánt adatforrásokat tartalmazza.

### <a name="reduce-the-number-of-canvas-refreshes-due-to-data-updates"></a>Az adatfrissítések miatt csökkentse a vászon frissítéseinak számát

Az osztályok többféleképpen is `DataSource` hozzáadhatók vagy frissíthetők. Az alábbiakban felsoroljuk a különböző módszereket és néhány megfontolandó szempontot a megfelelő teljesítmény biztosítása érdekében.

* Az adatforrások `add` függvény használatával egy vagy több szolgáltatás adható hozzá egy adatforráshoz. Minden alkalommal, amikor ez a függvény meghívásra kerül, egy térképi vászon frissítését indítja el. Ha sok funkciót ad hozzá, kombinálja őket egy tömb-vagy szolgáltatás-gyűjteményben, és ezt a függvényt egyszer kell átadnia, és nem kell az adathalmaz fölé kapcsolni, és a függvényt az egyes szolgáltatásokhoz hívnia.
* Az adatforrások függvény az adatforrás `setShapes` összes alakzatának felülírására használható. A motorháztető alatt összekapcsolja az adatforrásokat `clear` és a `add` függvényeket, és egyetlen térképi vászon-frissítést végez a kettő helyett, ami sokkal gyorsabb. Ezt akkor használja, ha egy adatforrás összes adatforrását szeretné frissíteni.
* Az adatforrások `importDataFromUrl` funkció használatával GeoJSON-fájlokat tölthet be egy adatforrásba egy URL-címen keresztül. Az adat letöltése után a rendszer átadja az adatforrások `add` függvénynek. Ha a GeoJSON fájl egy másik tartományban található, akkor győződjön meg arról, hogy a másik tartomány támogatja a tartományok közötti kérelmeket (CORs). Ha nem veszi figyelembe az adatok másolását a tartomány egy helyi fájljába, vagy olyan proxykiszolgálót hoz létre, amelynek engedélyezve van a CORs. Ha a fájl mérete nagy, érdemes lehet átkonvertálni egy vektoros csempét tartalmazó forrásra.
* Ha a szolgáltatások a osztályba vannak becsomagolva `Shape` , a `addProperty` , az `setCoordinates` és `setProperties` az alakzat függvényei mindegyike egy frissítést indít el az adatforrásban, és a Térkép vászon frissítése is frissül. Az adatforrások és a függvények által visszaadott összes funkció `getShapes` `getShapeById` automatikusan be van csomagolva az `Shape` osztályba. Ha több alakzatot szeretne frissíteni, a rendszer gyorsabban átalakítja a JSON-t az adatforrások `toJson` függvénnyel, szerkeszti a GeoJSON, majd átadja ezeket az adatforrások `setShapes` függvénynek.

### <a name="avoid-calling-the-data-sources-clear-function-unnecessarily"></a>Ne hívja meg az adatforrások törlési funkcióját szükségtelenül

Az osztály Clear függvényének meghívásával a `DataSource` térképi vászon frissítése is megtörtént. Ha a `clear` függvény többször is meghívva van egy sorban, késés léphet fel, amíg a Térkép megvárja az egyes frissítések betöltését.

Gyakori forgatókönyv, ahol ez gyakran előfordul az alkalmazásokban, amikor egy alkalmazás törli az adatforrást, letölti az új adatforrást, törli az adatforrást, majd hozzáadja az új adatforráshoz. A kívánt felhasználói élménytől függően a következő alternatívák jobbak lennének.

* Az új adat letöltése előtt törölje az adatforrásokat, majd adja át az új adatforrásokat `add` vagy `setShapes` függvényeket. Ha ez az egyetlen adathalmaz a térképen, a Térkép üres lesz, amíg az új adatfájl letöltése megtörténik.
* Töltse le az új adatforrásokat, majd továbbítsa azt az adatforrások `setShapes` függvénynek. Ezzel lecseréli a térképen lévő összes adatelemet.

### <a name="remove-unused-features-and-properties"></a>Nem használt szolgáltatások és tulajdonságok eltávolítása

Ha az adatkészlet olyan szolgáltatásokat tartalmaz, amelyeket nem fog használni az alkalmazásban, távolítsa el őket. Hasonlóképpen távolítsa el a nem szükséges szolgáltatások tulajdonságait. Ennek számos előnye van:

* Csökkenti a letölteni kívánt adatmennyiséget.
* Csökkenti azoknak a szolgáltatásoknak a számát, amelyeket az adatmegjelenítéskor kell összeállítani.
* Időnként megkönnyítheti vagy eltávolíthatja az adatvezérelt kifejezéseket és szűrőket, ami azt jelenti, hogy a renderelési időben kevesebb feldolgozásra van szükség.

Ha a funkciók sok tulajdonsággal vagy tartalommal rendelkeznek, sokkal nagyobb teljesítményre van szükség, hogy korlátozza, hogy az adatforráshoz hozzáadott adatok csak a rendereléshez szükségesek, és hogy a további tulajdonságok vagy tartalmak igény szerinti lekéréséhez külön módszer vagy szolgáltatás legyen. Ha például egy egyszerű Térkép jeleníti meg a helyszíneket a térképen, amikor rákattintanak egy csomó részletes tartalomra. Ha úgy szeretné használni az adatvezérelt stílust, hogy testreszabja a helyeknek a térképen való megjelenítését, csak az adatforráshoz szükséges tulajdonságokat kell betölteni. Ha meg szeretné jeleníteni a részletes tartalmat, használja a szolgáltatás AZONOSÍTÓját a további tartalom külön való lekéréséhez. Ha a tartalom a kiszolgálóoldali helyen található, akkor a szolgáltatás aszinkron módon kérhető le, ami drasztikusan csökkenti a Térkép első betöltéséhez szükséges adatmennyiséget.

Emellett a funkciók koordinátáiban szereplő jelentős számjegyek számának csökkentése jelentősen csökkentheti az adatméretet. Nem ritka, hogy a koordináták 12 vagy több tizedesjegyet tartalmaznak; a hat tizedesjegy pontossága azonban körülbelül 0,1 méter, ami gyakran pontosabb, mint a koordináta által reprezentált hely (például beltéri építési elrendezések használata esetén 6 tizedesjegyet ajánlott használni). Ha több mint hat tizedesjegyet észlel, valószínűleg nem fog különbséget tenni az adatmegjelenítésben, és csak a felhasználónak kell további adatletöltést kiadnia további kedvezmény nélkül.

Az alábbi lista a [GeoJSON-adatmennyiséggel kapcsolatos hasznos eszközöket](https://github.com/tmcw/awesome-geojson)tartalmazza.

### <a name="use-a-separate-data-source-for-rapidly-changing-data"></a>A gyorsan változó adatforrások használata egy külön adatforrás használatával

Időnként szükség van a térképen lévő információk gyors frissítésére olyan dolgok esetében, mint például a folyamatos átviteli vagy animálási funkciók élő frissítéseinek megjelenítése. Amikor egy adatforrás frissül, a renderelési motor az adatforrás összes funkcióját átveszi és megjeleníti. A statikus adatoknak a gyorsan változó adatokból különböző adatforrásokra való elválasztása jelentősen csökkentheti az egyes frissítésekben az adatforráshoz újra megjelenített szolgáltatások számát, és javítja az általános teljesítményt.

Ha élő adattal rendelkező vektoros csempéket használ, a frissítések támogatásának egyszerű módja a `expires` Válasz fejlécének használata. Alapértelmezés szerint a vektoros csempe forrás-vagy raszteres csempéje automatikusan újratölti a csempéket a `expires` dátum után. A Térkép forgalmi és incidens csempéi ezt a funkciót használják annak biztosítására, hogy a térképen megjelenjenek a friss valós idejű adatforgalmi adatok. Ezt a funkciót letilthatja a Maps szolgáltatás beállításával `refreshExpiredTiles` `false` .

### <a name="adjust-the-buffer-and-tolerance-options-in-geojson-data-sources"></a>A puffer és a tolerancia beállításainak módosítása a GeoJSON-adatforrásokban

Az `DataSource` osztály a nyers helyadatok a vektoros csempévé alakítása az on-the-fly megjelenítéshez. Ezek a helyi vektoros csempék a csempék területének egy kis pufferével leképezik a nyers adatmennyiséget, így biztosítva a mozaikok közötti zökkenőmentes renderelést. Minél kisebb a `buffer` lehetőség, annál kevesebb átfedésben lévő adat tárolódik a helyi vektor csempén, és a jobb teljesítmény, azonban minél nagyobb a renderelési összetevők változása. Próbálja meg ezt a beállítást, hogy a teljesítmény megfelelő kombinációja legyen a minimális renderelési összetevőkkel.

Az `DataSource` osztályban is van egy olyan `tolerance` lehetőség, amely a Douglas-Peucker egyszerűsítési algoritmust használja a geometriák renderelési célú feloldásának csökkentése érdekében. A tolerancia értékének növelése csökkenti a geometriák felbontását, és javítja a teljesítményt. Ezzel a beállítással a geometriai megoldás és a teljesítmény megfelelő kombinációja olvasható be az adatkészlethez.

### <a name="set-the-max-zoom-option-of-geojson-data-sources"></a>GeoJSON-adatforrások maximális nagyítási beállításának beállítása

Az `DataSource` osztály a nyers helyadatok a vektoros csempévé alakítása az on-the-fly megjelenítéshez. Alapértelmezés szerint ez csak a 18. nagyítási szintig lesz végrehajtva, ekkor a nagyításkor a rendszer a 18. nagyítási szinthez generált csempéből származó adatokból fog mintát venni. Ez a legtöbb olyan adathalmaz esetében jól működik, amelyeknek nagy felbontással kell rendelkezniük, ha ezeken a szinteken nagyítanak. Ha azonban olyan adatkészletekkel dolgozik, amelyeket nagyobb valószínűséggel lehet megtekinteni, ha többre nagyít, például az állam vagy a tartomány sokszögének megtekintésekor, az adatforrás beállítását `minZoom` kisebb értékre állítja be, például `12` csökkenti a számítást, a helyi csempék létrehozását, valamint az adatforrás által használt memóriát, és növeli a teljesítményt.

### <a name="minimize-geojson-response"></a>GeoJSON-válasz csökkentése

Ha egy szolgáltatáson keresztül vagy egy GeoJSON töltik le a kiszolgálóról, akkor ügyeljen arra, hogy az adatok a szükségesnél nagyobb méretűek legyenek.

### <a name="access-raw-geojson-using-a-url"></a>Nyers GeoJSON elérése URL-cím használatával

A JavaScripten belüli GeoJSON-objektumok is tárolhatók, azonban ez nagy mennyiségű memóriát használ a példányok számára az objektumhoz létrehozott változóban, és az adatforrás-példányt, amely egy különálló webes feldolgozón belül van kezelve. A GeoJSON elérhetővé teheti az alkalmazáshoz egy URL-cím használatával, az adatforrás pedig az adatforrások webes feldolgozója számára egyetlen másolatát fogja betölteni.  

## <a name="optimize-rendering-layers"></a>Renderelési rétegek optimalizálása

Az Azure Maps számos különböző réteget biztosít az adatleképezések megjelenítéséhez. Számos optimalizálással kihasználhatja ezeket a rétegeket a forgatókönyvhöz, így növelheti a teljesítményt és a teljes felhasználói élményt.

### <a name="create-layers-once-and-reuse-them"></a>Rétegek létrehozása egyszer és újbóli felhasználása

A Azure Maps web SDK úgy döntött, hogy adatvezérelt. Az adatforrások bekerülnek az adatforrásokba, amelyek ezután a renderelési rétegekhez kapcsolódnak. Ha módosítani szeretné a térképen lévő adatforrást, frissítse az adatforrásban lévő adatforrást, vagy módosítsa a stílus beállításait egy rétegen. Ez gyakran sokkal gyorsabb, mint az eltávolítás, majd a rétegek újbóli létrehozása, amikor módosul a változás.

### <a name="consider-bubble-layer-over-symbol-layer"></a>Buborék rétegének megvizsgálása

A buborék réteg a Térkép soraiként jeleníti meg a pontokat, és az adatvezérelt kifejezéssel egyszerűen elvégezhető a sugár és a szín stílusa. Mivel a kör egy egyszerű alakzat, amellyel a WebGL rajzolható, a renderelési motor sokkal gyorsabban jeleníthető meg, mint egy szimbólum réteg, amely egy kép betöltését és megjelenítését teszi lehetővé. A két renderelési réteg teljesítménybeli eltérése a több tízezer pont renderelése esetén észlelhető.

### <a name="use-html-markers-and-popups-sparingly"></a>A HTML-jelölők és az előugró ablakok használata takarékosan

A WebGL for rendering, HTML-jelölők és előugró ablakok esetében Azure Maps a legtöbb réteggel ellentétben a rendereléshez hagyományos DOM-elemeket használunk. Így a további HTML-jelölők és a felugró ablakok hozzáadott egy oldalt, annál több DOM-elem van. Néhány száz HTML jelölő vagy felugró ablak hozzáadása után a teljesítmény csökkenhet. Nagyobb adatkészletek esetén vegye fontolóra az adatcsoportosítást vagy egy szimbólum vagy egy buborék réteg használatát. Az előugró ablakok esetében egy közös stratégia egy előugró ablak létrehozása és újbóli felhasználása a tartalom és a pozíció frissítésével az alábbi példában látható módon:

<br/>

<iframe height='500' scrolling='no' title='Felugró ablak újrahasználata több PIN-kód használatával' src='//codepen.io/azuremaps/embed/rQbjvK/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a tollat a CodePen-on Azure Maps () által <a href='https://codepen.io/azuremaps/pen/rQbjvK/'>több PIN</a> -kód használatával <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'></a>
</iframe>

Ez azt jelzi, hogy ha csak néhány pontot kell megjeleníteni a térképen, a HTML-jelölők egyszerűsége előnyben részesített lehet. Emellett a HTML-jelölők könnyedén áthelyezhetők, ha szükséges.

### <a name="combine-layers"></a>Rétegek egyesítése

A Térkép több száz réteget is képes megjeleníteni, azonban minél több réteg van, annál több időbe telik a jelenet renderelése. Egy stratégia a rétegek számának csökkentéséhez a hasonló stílusokkal rendelkező rétegek és az [adatvezérelt stílusok](data-driven-style-expressions-web-sdk.md)használatával kombinálható.

Vegyünk például egy olyan adatkészletet, amelyben az összes szolgáltatás rendelkezik egy `isHealthy` tulajdonsággal, amelynek értéke `true` vagy `false` . Ha olyan buborék réteget hoz létre, amely ezen tulajdonság alapján különböző színes buborékok megjelenítését teszi lehetővé, több módon is elvégezheti ezt az alábbiak szerint, a legtöbbet a legtöbbet teljesítve.

* Ossza fel az adatforrásokat két adatforrásra az `isHealthy` érték alapján, és csatolja az egyes adatforrásokhoz rögzített színnel rendelkező buborék réteget.
* Helyezze az összes információt egyetlen adatforrásba, és hozzon létre két buborék réteget egy nehezen kódolt szín beállítással és egy, a tulajdonság alapján szűrővel `isHealthy` . 
* Az összes adatforrást egyetlen adatforrásba helyezze, hozzon létre egy egybuborékos réteget a `case` Color beállításhoz a tulajdonság alapján `isHealthy` . Itt látható egy mintakód, amely ezt mutatja be.

```javascript
var layer = new atlas.layer.BubbleLayer(source, null, {
    color: [
        'case',

        //Get the 'isHealthy' property from the feature.
        ['get', 'isHealthy'],

        //If true, make the color 'green'. 
        'green',

        //If false, make the color red.
        'red'
    ]
});
```

### <a name="create-smooth-symbol-layer-animations"></a>Sima szimbólumú rétegbeli animációk létrehozása

A szimbólumok rétegeinél alapértelmezés szerint engedélyezve van az ütközések észlelése. Az ütközés észlelése célja annak biztosítása, hogy a két szimbólum ne legyen átfedésben. Egy szimbólum réteg ikon-és szöveges beállításai két lehetőség közül választhatnak.

* `allowOverlap` -Megadja, hogy látható-e a szimbólum, ha az ütközik más szimbólumokkal.
* `ignorePlacement` -Megadja, hogy a többi szimbólumnak van-e ütközése a szimbólummal.

Mindkét beállítás alapértelmezés szerint be van állítva `false` . Egy szimbólum animálásakor az ütközés-észlelési számítások az animáció minden egyes keretén futnak, ami lelassíthatja az animációt, és kevesebb folyadékot fog keresni. Az animáció kisimításához állítsa be ezeket a beállításokat a következőre: `true` .

A következő mintakód egy egyszerű módszert mutat be egy szimbólum réteg animálására.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Szimbólum rétegének animációja" src="https://codepen.io/azuremaps/embed/oNgGzRd?height=500&theme-id=default&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/oNgGzRd'>szimbólumának rétegének animációját</a> Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) használatával a <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="specify-zoom-level-range"></a>Nagyítási szint tartományának meghatározása

Ha az adatai megfelelnek a következő feltételek egyikének, ügyeljen arra, hogy a réteg minimális és maximális nagyítási szintjét adja meg, hogy a renderelési motor kihagyható legyen a nagyítási szint tartományán kívül.

* Ha az adatok egy vektoros csempéből származnak, a különböző adattípusok esetében gyakran csak a nagyítási szintek széles köre érhető el.
* Ha olyan csempe réteget használ, amely nem tartalmaz csempéket a 0 és 24 közötti nagyításhoz, és azt szeretné, hogy csak a csempéket tartalmazó szintek legyenek leképezve, és ne próbálja meg kitölteni a hiányzó csempéket más nagyítási szint csempéi között.
* Ha csak bizonyos nagyítási szinteken szeretné megjeleníteni a réteget.
Minden réteg rendelkezik egy `minZoom` és `maxZoom` lehetőséggel, ahol a réteg a jelen logikán alapuló nagyítási szintek között jelenik meg ` maxZoom > zoom >= minZoom` .

**Példa**

```javascript
//Only render this layer between zoom levels 1 and 9. 
var layer = new atlas.layer.BubbleLayer(dataSource, null, {
    minZoom: 1,
    maxZoom: 10
});
```

### <a name="specify-tile-layer-bounds-and-source-zoom-range"></a>Csempe-réteg határainak és a forrás nagyítási tartományának meghatározása

Alapértelmezés szerint a csempe rétegei az egész világon betöltik a csempéket. Ha azonban a csempe szolgáltatásnak csak egy bizonyos terület csempéi vannak, akkor a Térkép a területen kívüli csempéket próbál betölteni. Ha ez történik, az egyes csempék iránti kérést a rendszer megvárja, és megvárja a választ, amely letilthatja a Térkép többi kérelmét, és így lelassíthatja más rétegek megjelenítését. A csempe réteg határainak megadásával a Térkép csak a határolókeretban található csempéket kéri le. Továbbá, ha a csempe rétege csak bizonyos nagyítási szintek között érhető el, a minimális és a maximális forrás nagyítást is ugyanezen okból kell megadni.

**Példa**

```javascript
var tileLayer = new atlas.layer.TileLayer({
    tileUrl: 'myTileServer/{z}/{y}/{x}.png',
    bounds: [-101.065, 14.01, -80.538, 35.176],
    minSourceZoom: 1,
    maxSourceZoom: 10
});
```

### <a name="use-a-blank-map-style-when-base-map-not-visible"></a>Üres leképezési stílus használata, ha a kiindulási Térkép nem látható

Ha olyan réteget helyez el a térképen, amely teljesen lefedi az alaptérképt, érdemes lehet a leképezési stílust úgy beállítani, hogy `blank` `blank_accessible` az alap Térkép ne legyen megjelenítve. Ez egy gyakori forgatókönyv, amely akkor fordul elő, ha a teljes Globe-csempe átfedésben van, és az alaptérkép felett nincs opacitás vagy átlátszó terület.

### <a name="smoothly-animate-image-or-tile-layers"></a>Képek és csempék zökkenőmentes animálása

Ha a térképen a képek vagy csempék sorozatán keresztül szeretne animálni. Gyakran gyorsabb, ha egy réteget hoz létre minden képhez vagy csempe réteghez, és módosítani szeretné az opacitást, mint az egyes animációs kereteken található egyetlen réteg forrásának frissítéséhez. Egy réteg elrejtéséhez állítsa az opacitást nullára, és jelenítse meg az új réteget úgy, hogy az opacitása nullánál nagyobb értékre van állítva, sokkal gyorsabb, mint a rétegben lévő forrás frissítése. Azt is megteheti, hogy a rétegek láthatósága is beállítható, de ügyeljen arra, hogy a réteg elhalványulása nullára legyen állítva, ellenkező esetben pedig animálni fogja a réteget a megjelenítésekor, ami egy villogási hatást eredményez, mivel az előző réteget el kellene rejteni az új réteg láthatóvá tétele előtt.

### <a name="tweak-symbol-layer-collision-detection-logic"></a>Csípés szimbólum rétegének ütközés-észlelési logikája

A szimbólum réteg két olyan lehetőséggel rendelkezik, amelyek mind a ikonhoz, mind pedig a szöveghez használhatók `allowOverlap` `ignorePlacement` . Ez a két beállítás azt határozza meg, hogy egy szimbólum ikonja vagy szövege átfedésben legyen vagy átfedésben van-e. Ha ezek a értékre vannak beállítva `false` , a szimbólum réteg kiszámítja az egyes pontok megjelenítését, hogy ellenőrizze, hogy az ütközik-e a rétegben már megjelenített szimbólummal, és ha igen, nem fogja megjeleníteni az ütköző szimbólumot. Ez jó a Térkép zsúfoltságának csökkentése és a megjelenített objektumok számának csökkentése érdekében. Ha ezeket a beállításokat a értékre állítja `false` , az ütközés észlelési logikája ki lesz hagyva, és az összes szimbólum megjelenik a térképen. Ha ezt a lehetőséget választja, a teljesítmény és a felhasználói élmény legjobb kombinációját érheti el.

### <a name="cluster-large-point-data-sets"></a>Fürt nagy pont adatkészletei

Nagy mennyiségű adatpont használata esetén előfordulhat, hogy bizonyos nagyítási szinteken a pontok számos átfedésben vannak, és csak részlegesen láthatók, ha egyáltalán van. A fürtözési folyamat olyan csoportosítási pontokat mutat be, amelyek egymáshoz kapcsolódnak, és egyetlen fürtözött pontként jelölik őket. Ahogy a felhasználó a térképen nagyítja a-t, a fürtök egymás után bontják az egyes pontokat. Ez jelentősen csökkentheti a megjeleníteni kívánt adatmennyiséget, így a Térkép kevésbé zsúfolt, és javíthatja a teljesítményt. Az `DataSource` osztály az adatcsoportosítási lehetőségeket helyileg is tartalmazhatja. Emellett számos vektoros csempét létrehozó eszköz is rendelkezik fürtözési lehetőségekkel.

Emellett növelje a fürt sugarának méretét a teljesítmény javítása érdekében. Minél nagyobb a fürt sugara, annál kisebb a fürtözött pontok nyomon követése és renderelése.
További információ a [fürtözési pont adatdokumentumáról](clustering-point-data-web-sdk.md)

### <a name="use-weighted-clustered-heat-maps"></a>Súlyozott fürtözött hő térképek használata

A hő-Térkép réteg több tízezer adatpontot képes megjeleníteni. Nagyobb adatkészletek esetében érdemes lehet engedélyezni a fürtözést az adatforráson, és egy kis méretű fürtöt használni, és a Clusters `point_count` tulajdonságot a magassági Térkép súlyozása alapján felhasználni. Ha a fürt sugara csak néhány képpont méretű, a megjelenített hő-Térképben kevés a vizuális különbség. A nagyobb méretű fürt sugara nagyobb teljesítményt nyújt, de csökkentheti a megjelenített hő-Térkép felbontását.

```javascript
var layer = new atlas.layer.HeatMapLayer(source, null, {
   weight: ['get', 'point_count']
});
```

További információ a [jelen dokumentumban található fürtözési és Heat Maps](clustering-point-data-web-sdk.md #clustering-and-the-heat-maps-layer) szolgáltatásról

### <a name="keep-image-resources-small"></a>Képerőforrások kis méretének megőrzése

A képek a Maps-kép sprite-ban adhatók meg, amelyek egy sokszög rétegben vagy mintázatban lévő ikonokat ábrázolnak. Ezeket a képeket kis méretűre állíthatja, hogy minimálisra csökkentse a letölteni kívánt adatok mennyiségét, valamint azt, hogy a Maps-kép sprite-ban mennyi tárterületet vesz fel. Ha olyan szimbólum réteget használ, amely a kapcsoló használatával méretezi az ikont `size` , használjon olyan képet, amely a tervnek a térképen való megjelenítéséhez szükséges maximális méretet adja meg, és ne legyen nagyobb. Ez biztosítja, hogy az ikon nagy felbontással legyen megjelenítve, és minimalizálja az általa használt erőforrásokat. Emellett az SVG is használható kisebb Fájlformátumként az egyszerű ikonos képekhez.

## <a name="optimize-expressions"></a>Kifejezések optimalizálása

Az [adatvezérelt stílusú kifejezések](data-driven-style-expressions-web-sdk.md) nagy rugalmasságot és hatékonyságot biztosítanak a térképen való szűréshez és formázáshoz. A kifejezések többféleképpen is optimalizálható. Íme, néhány tipp.

### <a name="reduce-the-complexity-of-filters"></a>A szűrők összetettségének csökkentése

Az adatforrásban lévő összes adatra szűri a rendszer, és ellenőrizze, hogy az egyes szűrők megfelelnek-e a szűrő logikájának. Ha a szűrők összetettek, ez teljesítménnyel kapcsolatos problémákat okozhat. Néhány lehetséges stratégia a következőkhöz:

* Ha vektoros csempéket használ, bontsa az adatforrást különböző rétegekre.
* Ha az osztályt használja, az adatforrásokat `DataSource` különálló adatforrásokra bontja. Próbálja meg kiegyensúlyozni az adatforrások számát a szűrő bonyolultságával. Túl sok adatforrás is okozhat teljesítménnyel kapcsolatos problémákat, ezért előfordulhat, hogy valamilyen tesztelést kell végeznie, hogy megtudja, mi a legmegfelelőbb a forgatókönyvhöz.
* Ha egy réteghez összetett szűrőt használ, érdemes lehet több réteget használni stílus kifejezésekkel a szűrő összetettségének csökkentése érdekében. Ne hozzon létre több réteget a hardcoded stílussal, ha a stílus kifejezések nagy számú rétegként használhatók, és a teljesítménnyel kapcsolatos problémákat is okozhatnak.

### <a name="make-sure-expressions-dont-produce-errors"></a>Győződjön meg arról, hogy a kifejezések nem eredményeznek hibákat

A kifejezéseket gyakran arra használják, hogy programkódot állítson elő számítások vagy logikai műveletek végrehajtásához a renderelési időben. Ugyanúgy, mint a kód a többi alkalmazásban, győződjön meg arról, hogy a számítások és a logikai ész, és nem hajlamosak a hibákra. A kifejezések hibái a kifejezés kiértékelésével kapcsolatos problémákat okoznak, ami csökkentheti a teljesítményt és a rendereléssel kapcsolatos problémákat.

Az egyik gyakori hiba, hogy szem előtt tartja, hogy olyan kifejezés található, amely egy olyan szolgáltatás tulajdonságra támaszkodik, amely nem létezik az összes szolgáltatásban. Az alábbi kód például egy kifejezés használatával állítja be egy buborékdiagram szín tulajdonságát `myColor` egy szolgáltatás tulajdonságára.

```javascript
var layer = new atlas.layer.BubbleLayer(source, null, {
    color: ['get', 'myColor']
});
```

A fenti kód akkor fog működni, ha az adatforrás összes funkciója rendelkezik `myColor` tulajdonsággal, és a tulajdonság értéke egy szín. Előfordulhat, hogy ez nem jelent problémát, ha az adatforráson belül teljes mértékben felügyeli az adatforrást, és biztos lehet benne, hogy egyes funkciók esetében érvényes szín lesz a `myColor` tulajdonságban. Ez azt mondta, hogy a kód a hibáktól való biztonsága érdekében egy `case` kifejezés használható a `has` kifejezéssel annak ellenőrzését, hogy a szolgáltatás rendelkezik-e a `myColor` tulajdonsággal. Ha igen, a `to-color` Type kifejezéssel a tulajdonság értékének a színre való konvertálása is használható. Ha a szín érvénytelen, a rendszer egy tartalék színt is felhasználhat. A következő kód bemutatja, hogyan teheti ezt meg, és a zöldre állítja a tartalék színt.

```javascript
var layer = new atlas.layer.BubbleLayer(source, null, {
    color: [
        'case',

        //Check to see if the feature has a 'myColor' property.
        ['has', 'myColor'],

        //If true, try validating that 'myColor' value is a color, or fallback to 'green'. 
        ['to-color', ['get', 'myColor'], 'green'],

        //If false, return a fallback value.
        'green'
    ]
});
```

### <a name="order-boolean-expressions-from-most-specific-to-least-specific"></a>Logikai kifejezések megrendelése a legpontosabbtól a legkevésbé konkrétig

Ha több feltételes tesztet tartalmazó logikai kifejezéseket használ, a feltételes teszteket a legpontosabban a legkevésbé meghatározottra kell rendelni. Ezzel az első feltételnek csökkentenie kell a második feltétel által vizsgálandó adatmennyiséget, így csökkentve a végrehajtandó feltételes tesztek teljes számát.

### <a name="simplify-expressions"></a>Egyszerűbb kifejezések

A kifejezések lehetnek hatékonyak és esetenként bonyolultak. Minél egyszerűbb a kifejezés, annál gyorsabban lesz kiértékelve. Ha például egyszerű összehasonlításra van szükség, egy kifejezés, például `['==', ['get', 'category'], 'restaurant']` jobb lenne, mint például a Match kifejezés használata `['match', ['get', 'category'], 'restaurant', true, false]` . Ebben az esetben, ha az ellenőrizendő tulajdonság egy logikai érték, a `get` kifejezés még egyszerűbb lesz `['get','isRestaurant']` .

## <a name="web-sdk-troubleshooting"></a>Web SDK – hibaelhárítás

A következő tippek a Azure Maps web SDK-val való fejlesztés során felmerülő gyakori problémák hibakeresését tárgyalják.

**Miért nem jelenik meg a Térkép a webes vezérlő betöltésekor?**

Tegye a következőket:

* Győződjön meg arról, hogy hozzáadta a további hitelesítési beállításokat a térképhez. Ha ez nem történik meg, a Térkép üres vászonra töltődik be, mivel nem tud hozzáférni az alapszintű térképi adatokhoz hitelesítés nélkül, és a 401 hibák a böngésző fejlesztői eszközeinek hálózat lapján jelennek meg.
* Győződjön meg arról, hogy van internetkapcsolata.
* A böngésző fejlesztői eszközeivel kapcsolatos hibákért keresse meg a konzolt. Bizonyos hibák miatt előfordulhat, hogy a Térkép nem jelenik meg. Az alkalmazás hibakeresése.
* Győződjön meg arról, hogy [támogatott böngészőt](supported-browsers.md)használ.

**Az összes adat a világ másik oldalán jelenik meg, mi történik?**
Az Azure Maps SDK-k a térinformatikai iparág szabványos formátumával összhangban lévő koordinátákat, más néven pozíciókat is mutatnak `[longitude, latitude]` . Ugyanez a formátum is a koordináták definiálása a GeoJSON-sémában. a Azure Maps SDK-k által használt alapvető adatformátum. Ha az adatok a világ másik részén jelennek meg, akkor a rendszer a koordináták és a pozíció adatainak fordított hosszúsági és szélességi értékei miatt legvalószínűbb.

**Miért jelenik meg a HTML-jelölők helytelen helyen a webes vezérlőelemben?**

Ellenőrizhető dolgok:

* Ha egyéni tartalmat használ a jelölőhöz, ellenőrizze, hogy `anchor` helyesek-e a és a `pixelOffset` beállítások. Alapértelmezés szerint a tartalom alsó középpontja a Térkép pozíciójának megfelelően van igazítva.
* Győződjön meg arról, hogy a Azure Maps CSS-fájlja be van töltve.
* Vizsgálja meg a HTML-jelölő DOM-elemét, és ellenőrizze, hogy az alkalmazásból származó összes CSS-fájl hozzá van-e fűzve a jelölőhöz, és hatással van-e a helyére

**Miért nem megfelelő helyen jelenik meg az ikonok vagy a szöveg a szimbólum rétegben?**
Győződjön meg arról, hogy a `anchor` és a `offset` Beállítások megfelelően vannak konfigurálva ahhoz, hogy illeszkedjenek a kép vagy szöveg azon részéhez, amelyet össze szeretne hangolni a Térkép koordinátáival.
Ha a szimbólum csak a Térkép elforgatásakor érhető el, jelölje be a `rotationAlignment` beállítást. Alapértelmezés szerint a rendszer a térképek nézetablakmal elforgatja a szimbólumokat, hogy azok a felhasználó számára is megjelenjenek. A forgatókönyvtől függően azonban érdemes lehet a szimbólumot a Térkép tájolásának megfelelően zárolni. Állítsa be `rotationAlignment` ezt a beállítást `’map’` .
Ha a szimbólum csak akkor érhető el, ha a Térkép fel van helyezve/megdöntött, jelölje be a `pitchAlignment` beállítást. Alapértelmezés szerint a szimbólumok a térképek nézetablakban maradnak, mivel a Térkép feldobásra vagy döntésre kerül. Azonban előfordulhat, hogy a forgatókönyvtől függően érdemes lehet a szimbólumot zárolni a Térkép pályáján. Állítsa be `pitchAlignment` ezt a beállítást `’map’` .

**Miért nem jelenik meg az adataim a térképen?**

Ellenőrizhető dolgok:

* A böngésző fejlesztői eszközeinél keresse meg a konzolt a hibákért.
* Győződjön meg arról, hogy az adatforrás létre lett hozva, és hozzá lett adva a térképhez, valamint arról, hogy az adatforrás egy renderelési réteghez van csatlakoztatva, amely szintén hozzá lett adva a térképhez.
* Vegyen fel töréspontokat a kódban, és haladjon végig annak biztosításához, hogy az adatforráshoz hozzáadott adatforrások és az adatforrás és a rétegek a hibák nélkül kerüljenek a térképbe.
* Próbálja meg eltávolítani az adatvezérelt kifejezéseket a renderelési rétegből. Lehetséges, hogy az egyik ilyen hibával járhat, ami okozza a problémát.

**Használhatom a Azure Maps web SDK-t egy munkapéldányos iframe-ben?**

Igen. Vegye figyelembe, hogy a [Safari olyan hibát tartalmaz](https://bugs.webkit.org/show_bug.cgi?id=170075) , amely megakadályozza a munkavégzők webes feldolgozók futtatását, ami a Azure Maps web SDK követelménye. A megoldás a `"allow-same-origin"` címkét hozzáadja az IFRAME sandbox tulajdonságához.

## <a name="get-support"></a>Támogatás kérése

A következő módokon kérheti le a Azure Maps támogatását a probléma függvényében.

**Hogyan jelentést egy adatproblémáról vagy egy címről?**

Azure Maps tartalmaz egy adatvisszajelzési eszközt, amelyben az adathibákat jelenteni és követni lehet. [https://feedback.azuremaps.com/](https://feedback.azuremaps.com/) Minden egyes elküldött probléma egyedi URL-címet hoz létre, amellyel nyomon követheti az adatprobléma előrehaladását. Az adatprobléma megoldásához szükséges idő a probléma típusától és a módosítás helyességének egyszerű megváltoztatásával függ. A rögzítést követően a renderelési szolgáltatás a heti frissítésben fogja látni a frissítést, míg más szolgáltatások, például a helymeghatározáshoz és az Útválasztás a havi frissítésben jelennek meg. Az adatprobléma jelentésének részletes utasításait ebben a [dokumentumban](how-to-use-feedback-tool.md)találhatja meg.

**Hogyan hibát jelent egy szolgáltatásban vagy API-ban?**

https://azure.com/support

**Hol kaphatok technikai segítséget a Azure Maps?**

Ha a Power BI Azure Maps vizualizációhoz kapcsolódik: az https://powerbi.microsoft.com/support/ összes többi Azure Maps szolgáltatás esetében: https://azure.com/support vagy a fejlesztői fórumok: https://docs.microsoft.com/answers/topics/azure-maps.html

**Hogyan a szolgáltatásra vonatkozó kérést?**

Hozzon igénybe egy funkciót a felhasználói hangalapú oldalunkon: https://feedback.azure.com/forums/909172-azure-maps

## <a name="next-steps"></a>Következő lépések

A következő cikkekben további tippeket talál az alkalmazás felhasználói felületének fejlesztéséhez.

> [!div class="nextstepaction"]
> [Az alkalmazás elérhetővé tétele](map-accessibility.md)

További információ a Azure Maps és a térinformatikai iparág által használt terminológiáról.

> [!div class="nextstepaction"]
> [Azure Maps-szószedet](glossary.md)
