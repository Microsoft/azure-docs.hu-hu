---
title: Azure Maps rajz-átalakítási hibák és figyelmeztetések
description: Ismerje meg az átalakítási hibákat és a figyelmeztetéseket, amelyek a Azure Maps átalakítási szolgáltatás használata során esetlegesen teljesülnek. Néhány példa a hibák és a figyelmeztetések megoldásával kapcsolatos javaslatok elolvasására.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philMea
ms.openlocfilehash: cecc19f0984ce1801d50e5cbda73e98a01e2825b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96906216"
---
# <a name="drawing-conversion-errors-and-warnings"></a>Rajz átalakítási hibái és figyelmeztetései

A [Azure Maps konverziós szolgáltatás](/rest/api/maps/conversion) lehetővé teszi a feltöltött rajzok leképezési adatként való átalakítását. A rajzolási csomagoknak meg kell felelniük a [rajzolási csomagok követelményeinek](drawing-requirements.md). Ha egy vagy több követelmény nem teljesül, az átalakítási szolgáltatás hibákat vagy figyelmeztetéseket ad vissza. Ez a cikk az átalakítási hibát és a figyelmeztetési kódokat sorolja fel, és javaslatokat tesz a megoldására. Emellett néhány példát is tartalmaz a rajzok számára, amelyek hatására az átalakítási szolgáltatás visszaadja ezeket a kódokat.

Az átalakítási szolgáltatás akkor fog sikerülni, ha van valamilyen konverziós figyelmeztetés. Javasoljuk azonban, hogy tekintse át és oldja fel az összes figyelmeztetést. Egy figyelmeztetés a konverzió egy részét figyelmen kívül hagyta vagy automatikusan rögzítették. A figyelmeztetések feloldása hibát okozhat az utóbbi folyamatokban.

## <a name="general-warnings"></a>Általános figyelmeztetések

### <a name="geometrywarning"></a>**geometryWarning**

#### <a name="description-for-geometrywarning"></a>*A geometryWarning leírása*

Egy **geometryWarning** akkor következik be, amikor a rajz érvénytelen entitást tartalmaz. Érvénytelen entitás egy olyan entitás, amely nem felel meg a geometriai megkötéseknek. Érvénytelen entitás például egy önmetsző sokszög vagy egy nem zárt vonallánc egy olyan rétegben, amely csak a zárt geometriát támogatja.

A konverziós szolgáltatás nem tud létrehozni leképezési szolgáltatást érvénytelen entitásból, hanem figyelmen kívül hagyja azt.

#### <a name="examples-for-geometrywarning"></a>*Példák a geometryWarning*

* Az alábbi két kép az önmetsző sokszögek példáit mutatja be.

     ![Példa egy önmetsző sokszögre, például egy.](./media/drawing-conversion-error-codes/geometry-warning-1.png)

     ![Példa egy önmetsző sokszögre, például kettő.](./media/drawing-conversion-error-codes/geometry-warning-2.png)

* Alább látható egy olyan rendszerkép, amely nem lezárt vonalláncot mutat be. Tegyük fel, hogy a réteg csak a zárt geometriát támogatja.

    ![Nem zárt vonallánc – példa](./media/drawing-conversion-error-codes/geometry-warning-3.png)

#### <a name="how-to-fix-geometrywarning"></a>*A geometryWarning javítása*

Vizsgálja meg az egyes entitások **geometryWarning** , és ellenőrizze, hogy az a geometriai korlátokat követi-e.

### <a name="unexpectedgeometryinlayer"></a>**unexpectedGeometryInLayer**

#### <a name="description-for-unexpectedgeometryinlayer"></a>*A unexpectedGeometryInLayer leírása*

**UnexpectedGeometryInLayer** figyelmeztetés jelenik meg, ha a rajz olyan geometriát tartalmaz, amely nem kompatibilis az adott réteg várt geometriájának típusával. Ha az átalakítási szolgáltatás **unexpectedGeometryInLayer** figyelmeztetést ad vissza, a rendszer figyelmen kívül hagyja ezt a geometriát.

#### <a name="example-for-unexpectedgeometryinlayer"></a>*Példa unexpectedGeometryInLayer*

Az alábbi képen egy nem lezárt vonallánc látható. Tegyük fel, hogy a réteg csak a zárt geometriát támogatja.

![Nem zárt vonallánc – példa](./media/drawing-conversion-error-codes/geometry-warning-3.png)

#### <a name="how-to-fix-unexpectedgeometryinlayer"></a>*A unexpectedGeometryInLayer javítása*

Vizsgálja meg az egyes **unexpectedGeometryInLayer** kapcsolatos figyelmeztetéseket, és helyezze át a nem kompatibilis geometriát egy kompatibilis rétegbe. Ha nem kompatibilis a többi réteggel, azt el kell távolítani.

### <a name="unsupportedfeaturerepresentation"></a>**unsupportedFeatureRepresentation**

#### <a name="description-for-unsupportedfeaturerepresentation"></a>*A unsupportedFeatureRepresentation leírása*

A **unsupportedFeatureRepresentation** figyelmeztetés akkor fordul elő, ha a rajz nem támogatott entitást tartalmaz.

#### <a name="example-for-unsupportedfeaturerepresentation"></a>*Példa unsupportedFeatureRepresentation*

Az alábbi képen egy nem támogatott entitás típusa jelenik meg többsoros szöveges objektumként egy felirat rétegben.
  
![Példa többsoros szöveges objektumra a címke rétegén](./media/drawing-conversion-error-codes/multi-line.png)

#### <a name="how-to-fix-unsupportedfeaturerepresentation"></a>*A unsupportedFeatureRepresentation javítása*

Győződjön meg arról, hogy a DWG-fájlok csak a támogatott entitások típusait tartalmazzák. A támogatott típusok listáját a rajzeszközökre [vonatkozó követelmények című szakaszban](drawing-requirements.md#drawing-package-requirements)találja.

### <a name="automaticrepairperformed"></a>**automaticRepairPerformed**

#### <a name="description-for-automaticrepairperformed"></a>*A automaticRepairPerformed leírása*

A **automaticRepairPerformed** figyelmeztető üzenet jelenik meg, ha az átalakítási szolgáltatás automatikusan kijavítja az érvénytelen geometriát.

#### <a name="examples-for-automaticrepairperformed"></a>*Példák a automaticRepairPerformed*

* Az alábbi képen azt láthatja, hogy a konverziós szolgáltatás hogyan javította egy önmetsző sokszöget az érvényes geometriában.

    ![Példa egy önmetsző sokszög javítására](./media/drawing-conversion-error-codes/automatic-repair-1.png)

* Az alábbi képen látható, hogy az átalakítási szolgáltatás hogyan zárolta a nem zárt vonallánc első és utolsó csúcspontját egy zárt vonallánc létrehozásához, ahol az első és az utolsó csúcspont kevesebb, mint 1 mm.  

    ![Egy csattant vonallánc példája](./media/drawing-conversion-error-codes/automatic-repair-2.png)

* Az alábbi ábrán egy olyan réteg látható, amely csak zárt vonalláncokat támogat, a konverziós szolgáltatás több nem lezárt vonalláncot javított. A nem bezárt vonalláncok figyelmen kívül hagyása érdekében a szolgáltatás egyetlen zárt Vonalláncba kombinálja őket.

    ![Nem lezárt vonalláncok – például egyetlen zárt Vonalláncba egyesítve](./media/drawing-conversion-error-codes/automatic-repair-3.png)

#### <a name="how-to-fix-automaticrepairperformed"></a>*A automaticRepairPerformed javítása*

Egy **automaticRepairPerformed** figyelmeztetés kijavításához végezze el a következő műveleteket:

1. Vizsgálja meg az összes figyelmeztetés geometriáját és az adott figyelmeztetés szövegét.
2. Annak megállapítása, hogy az automatikus javítás helyes-e.
3. Ha a javítás helyes, folytassa a művelettel. Ellenkező esetben nyissa meg a tervezési fájlt, és manuálisan javítsa a figyelmeztetést.

>[!TIP]
>Ha egy figyelmeztetést szeretne letiltani a jövőben, módosítsa az eredeti rajzot úgy, hogy az eredeti rajz megfeleljen a javított rajznak.

## <a name="manifest-warnings"></a>Jegyzékfájl figyelmeztetései

### <a name="redundantattribution"></a>**redundantAttribution**

#### <a name="description-for-redundantattribution"></a>*A redundantAttribution leírása*

A **redundantAttribution** figyelmeztetés akkor fordul elő, ha a jegyzékfájl redundáns vagy ütköző objektumot tartalmaz.

#### <a name="examples-for-redundantattribution"></a>*Példák a redundantAttribution*

* Az alábbi JSON-kódrészlet két vagy több `unitProperties` objektumot tartalmaz `name` .

    ```json
    "unitProperties": [
        {
            "unitName": "L1-100",
            "categoryName": "room.office"
        },
        {
            "unitName": "L1-101",
            "categoryName": "room.office"
        },
        {
            "unitName": "L1-101",
            "categoryName": "room.office"
        }
    ]
    ```

* Az alábbi JSON-kódrészletben legalább két `zoneProperties` objektumnak azonosnak kell lennie `name` .

    ```json
     "zoneProperties": [
        {
            "zoneName": "Assembly Area 1",
            "categoryName": "zone.assembly"
        },
        {
            "zoneName": "Assembly Area 2",
            "categoryName": "zone.assembly"
        },
        {
            "zoneName": "Assembly Area 2",
            "categoryName": "zone.assembly"
        }
    ```

#### <a name="how-to-fix-redundantattribution"></a>*A redundantAttribution javítása*

A **redundantAttribution* figyelmeztetés kijavításához távolítsa el a redundáns vagy ütköző objektum tulajdonságait.

### <a name="manifestwarning"></a>**manifestWarning**

#### <a name="description-for-manifestwarning"></a>*A manifestWarning leírása*

Egy **manifestWarning** akkor következik be, amikor a jegyzékfájl olyan unitProperties-vagy zoneProperties-objektumokat tartalmaz, amelyek nem használhatók a konverzió során.

#### <a name="examples-for-manifestwarning"></a>*Példák a manifestWarning*

* A jegyzékfájl olyan objektumot tartalmaz, amelynek `unitProperties` `unitName` nem megfelelő címkéje van egy `unitLabel` rétegben.

* A jegyzékfájl olyan objektumot tartalmaz, amelynek `zoneProperties` `zoneName` nem megfelelő címkéje van egy `zoneLabel` rétegben.

#### <a name="how-to-fix-manifestwarning"></a>*A manifestWarning javítása*

Egy **manifestWarning** kijavításához távolítsa el a nem használt `unitProperties` vagy az `zoneProperties` objektumot a jegyzékfájlból, vagy adjon hozzá egy egység/zóna címkét a rajzhoz, hogy a tulajdonságok objektum a konverzió során legyen használatban.

## <a name="wall-warnings"></a>Fali figyelmeztetések

### <a name="walloutsidelevel"></a>**wallOutsideLevel**

#### <a name="description-for-walloutsidelevel"></a>*A wallOutsideLevel leírása*

A **wallOutsideLevel** figyelmeztetés akkor következik be, amikor a rajz a szint körvonalának határain kívül egy fal-geometriát tartalmaz.

#### <a name="example-for-walloutsidelevel"></a>*Példa wallOutsideLevel*

* Az alábbi képen egy belső fal látható, vörös színnel, a sárga szint határán kívül.

    ![Példa belső falra a szint határán kívül](./media/drawing-conversion-error-codes/wall-outside-level.png)

* Az alábbi képen egy külső fal látható vörös színnel, a sárga szint határán kívül.

    ![Külső fal – példa a szint határán kívül](./media/drawing-conversion-error-codes/wall-outside-level-exterior.png)

#### <a name="how-to-fix-walloutsidelevel"></a>*A wallOutsideLevel javítása*

A **wallOutsideLevel** figyelmeztetés kijavításához bontsa ki a szint geometriáját az összes fal belefoglalásához. Vagy módosítsa a fal határait úgy, hogy illeszkedjenek a szint határán belülre.

## <a name="unit-warnings"></a>Egységre vonatkozó figyelmeztetések

### <a name="unitoutsidelevel"></a>**unitOutsideLevel**

#### <a name="description-for-unitoutsidelevel"></a>*A unitOutsideLevel leírása*

**UnitOutsideLevel** figyelmeztető üzenet jelenik meg, ha a rajz egység geometriát tartalmaz a szint körvonalának határain kívül.

#### <a name="example-for-unitoutsidelevel"></a>*Példa unitOutsideLevel*

 A következő ábrán az egység geometriája piros színnel meghaladja a sárga szintű határ határait.

 ![A szint határát meghaladó egység – példa](./media/drawing-conversion-error-codes/unit-outside-level.png)

#### <a name="how-to-fix-unitoutsidelevel"></a>*A unitOutsideLevel javítása*

A **unitOutsideLevel** figyelmeztetés kijavításához bontsa ki a szint határt, hogy minden egység szerepeljen. Vagy módosítsa az egység geometriáját úgy, hogy az illeszkedjen a szint határain belül.

### <a name="partiallyoverlappingunit"></a>**partiallyOverlappingUnit**

#### <a name="description-for-partiallyoverlappingunit"></a>*A partiallyOverlappingUnit leírása*

**PartiallyOverlappingUnit** figyelmeztetés jelenik meg, ha a rajz egy egység geometriát tartalmaz, amely részben átfedésben van egy másik egység geometriáján. A konverziós szolgáltatás figyelmen kívül hagyja az átfedésben lévő egységeket.

#### <a name="example-scenarios-partiallyoverlappingunit"></a>*Példák partiallyOverlappingUnit*

Az alábbi képen az átfedésben lévő egység piros színnel van kiemelve. `UNIT110` és `HALLWAY` a rendszer elveti.

![Átfedésben lévő egységek – példa](./media/drawing-conversion-error-codes/partially-overlapping-unit.png)

#### <a name="how-to-fix-partiallyoverlappingunit"></a>*A partiallyOverlappingUnit javítása*

Egy **partiallyOverlappingUnit** figyelmeztetés kijavításához dolgozza fel az egyes részben átfedésben lévő egységeket úgy, hogy ne legyenek átfedésben más egységekkel.

## <a name="door-warnings"></a>Ajtóra vonatkozó figyelmeztetések

### <a name="dooroutsidelevel"></a>**doorOutsideLevel**

#### <a name="description-for-dooroutsidelevel"></a>*A doorOutsideLevel leírása*

**DoorOutsideLevel** figyelmeztető üzenet jelenik meg, ha a rajz a szint geometriájának határain kívüli ajtó geometriát tartalmaz.

#### <a name="example-for-dooroutsidelevel"></a>*Példa doorOutsideLevel*

Az alábbi képen az ajtó geometriája piros színnel van kiemelve, átfedésben van a sárga szint határával.

![Egy szint határán átfedő ajtó – példa](./media/drawing-conversion-error-codes/door-outside-level.png)

#### <a name="how-to-fix-dooroutsidelevel"></a>*A doorOutsideLevel javítása*

Egy **doorOutsideLevel** figyelmeztetés kijavításához rajzolja újra az ajtót a geometriában, hogy az a szint határain belül legyen.

## <a name="zone-warnings"></a>Zónákra vonatkozó figyelmeztetések

### <a name="zonewarning"></a>**zoneWarning**

#### <a name="description-for-zonewarning"></a>*A zoneWarning leírása*

A **zoneWarning** akkor következik be, amikor egy zóna nem tartalmaz címkét. A konverziós szolgáltatás elveti a nem Label. l nevű zónát.

#### <a name="example-for-zonewarning"></a>*Példa zoneWarning*

Az alábbi képen egy olyan zóna látható, amely nem tartalmaz címkét.

![Egy zóna például nem tartalmaz címkét](./media/drawing-conversion-error-codes/zone-warning.png)

#### <a name="how-to-fix-zonewarning"></a>*A zoneWarning javítása*

Egy **zoneWarning** kijavításához ellenőrizze, hogy minden zónának van-e egyetlen címkéje.

## <a name="label-warnings"></a>Feliratok figyelmeztetései

### <a name="labelwarning"></a>*labelWarning*

#### <a name="description-for-labelwarning"></a>*A labelWarning leírása*

A **labelWarning** akkor következik be, amikor a rajz kétértelmű vagy ellentmondásos címkéket tartalmaz.

Egy **labelWarning** a következő okok közül egy vagy több miatt fordul elő:

* Egy egység címkéje nincs egységben.
* Egy zóna címkéje nincs egyetlen zónában sem.
* A zóna címkéje két vagy több zónán belül van.

#### <a name="example-for-labelwarning"></a>*Példa labelWarning*

Az alábbi képen egy címke látható két zónán belül.

![Két zónán belüli címke – példa ](./media/drawing-conversion-error-codes/label-warning.png)

#### <a name="how-to-fix-labelwarning"></a>*A labelWarning javítása*

A **labelWarning** javításához a következőket kell tennie:

* Minden egység felirata egységben van.
* Az összes zóna felirata zónán belül van.
* Minden zóna felirata egy és csak egy zónában található.

## <a name="drawing-package-errors"></a>Rajzolási csomagok hibái

### <a name="invalidarchiveformat"></a>**invalidArchiveFormat**

#### <a name="description-for-invalidarchiveformat"></a>*A invalidArchiveFormat leírása*

**InvalidArchiveFormat** hiba történik, ha a rajzfájl érvénytelen archiválási formátumban van, például: gzip vagy 7-Zip. Csak a ZIP-archívum formátuma támogatott.

**InvalidArchiveFormat** -hiba akkor is előfordul, ha a zip-archívum üres.

#### <a name="how-to-fix-invalidarchiveformat"></a>*A invalidArchiveFormat javítása*

A **invalidArchiveFormat** hibáinak kijavításához ellenőrizze a következőket:

* Az archív fájl neve a _. zip_ fájlban végződik.
* A ZIP-archívumban adatok szerepelnek.
* Megnyithatja a ZIP-archívumot.

### <a name="invaliduserdata"></a>**invalidUserData**

#### <a name="description-for-invaliduserdata"></a>*A invalidUserData leírása*

**InvalidUserData** hiba történik, ha a konverziós szolgáltatás nem tud beolvasni egy felhasználói adatobjektumot a tárolóból.

#### <a name="example-scenario-for-invaliduserdata"></a>*InvalidUserData-forgatókönyv – példa*

Nem megfelelő paraméterrel próbált meg feltölteni egy rajzfájl-csomagot `udid` .

#### <a name="how-to-fix-invaliduserdata"></a>*A invalidUserData javítása*

A **invalidUserData** hibáinak kijavításához ellenőrizze a következőket:

* Megfelelőt adott meg `udid` a feltöltött csomaghoz.
* A Azure Maps létrehozója (előzetes verzió) engedélyezve van a rajzfájl feltöltéséhez használt Azure Maps-fiókhoz.
* Az átalakítási szolgáltatás API-kérelme tartalmazza az előfizetési kulcsot arra a Azure Maps fiókra, amelyet a rajzfájl feltöltéséhez használt.

### <a name="dwgerror"></a>**dwgError**

#### <a name="description-for-dwgerror"></a>*A dwgError leírása*

**DwgError** , ha a rajzfájl egy vagy több DWG-fájllal kapcsolatos problémát tartalmaz a feltöltött ZIP-archívumban.

A **dwgError** akkor következik be, amikor a RAJZFÁJL olyan DWG-fájlt tartalmaz, amely nem nyitható meg, mert érvénytelen vagy sérült.

* A DWG-fájlok nem érvényes AutoCAD DWG fájlformátum-rajzok.
* Egy DWG-fájl sérült.
* A DWG-fájlok szerepelnek a _manifest.js_ fájlban, de a zip-archívumból hiányoznak.

#### <a name="how-to-fix-dwgerror"></a>*A dwgError javítása*

A **dwgError** kijavításához tekintse meg a _manifest.jsa_ fájlban ellenőrizze, hogy:

* A ZIP-archívumban található összes DWG-fájl érvényes AutoCAD DWG formátumú rajzok, amelyek mindegyikét az AutoCADben nyitják meg. Távolítsa el vagy javítsa ki az összes érvénytelen rajzot.
* A _manifest.js_  található DWG-fájlok listája megegyezik a ZIP-archívumban található DWG-fájlokkal.

## <a name="manifest-errors"></a>Jegyzékfájl-hibák

### <a name="invalidjsonformat"></a>**invalidJsonFormat**

#### <a name="description-for-invalidjsonformat"></a>A invalidJsonFormat leírása

**InvalidJsonFormat** hiba történik, ha a fájl _manifest.js_ nem olvasható.

A _manifest.json_file JSON-formázás vagy szintaktikai hiba miatt nem olvashatók. További információ a JSON formátumáról és szintaxisáról: [JavaScript Object Notation (JSON) adatcsere formátuma](https://tools.ietf.org/html/rfc7159)

#### <a name="how-to-fix-invalidjsonformat"></a>*A invalidJsonFormat javítása*

A **invalidJsonFormat** hibáinak kijavításához használjon JSON-kisegítő hibát a JSON-hibák észleléséhez és megoldásához.

### <a name="missingrequiredfield"></a>**missingRequiredField**

#### <a name="description-for-missingrequiredfield"></a>*A missingRequiredField leírása*

**MissingRequiredField** hiba történik, ha a _manifest.js_ fájlból hiányzik a szükséges adatérték.

#### <a name="how-to-fix-missingrequiredfield"></a>*A missingRequiredField javítása*

A **missingRequiredField** hibáinak kijavításához ellenőrizze, hogy a jegyzékfájl tartalmazza-e az összes szükséges tulajdonságot. A szükséges jegyzékfájl-objektumok teljes listájáért tekintse meg a következő [szakaszt a rajzolási csomag követelményeiben: manifest](drawing-requirements.md#manifest-file-requirements)  

### <a name="missingmanifest"></a>**missingManifest**

#### <a name="description-for-missingmanifest"></a>*A missingManifest leírása*

A **missingManifest** hiba akkor fordul elő, ha a fájl _manifest.js_ hiányzik a zip-archívumból.

A **missingManifest** hiba a következő okok közül egy vagy több miatt fordul elő:

* A fájl _manifest.js_ hibásan van írva.
* Hiányzik a _manifest.js_ .
* A _manifest.js_ nem a zip-archívum gyökérkönyvtárában található.

#### <a name="how-to-fix-missingmanifest"></a>*A missingManifest javítása*

A **missingManifest** hibáinak kijavításához ellenőrizze, hogy az archívumban van-e egy _manifest.js_ nevű fájl a zip-archívum legfelső szintjén.

### <a name="conflict"></a>**Ütközés**

#### <a name="description-for-conflict"></a>*Ütközés leírása*

Az **ütközési** hiba akkor fordul elő, ha a fájl _manifest.js_ ütköző információt tartalmaz.

#### <a name="example-scenario-for-conflict"></a>*Példa ütközésre*

Az átalakítási szolgáltatás **ütközési** hibát ad vissza, ha egynél több szint van definiálva ugyanazzal a szintű sorszámmal. A következő JSON-kódrészlet két, azonos sorszámmal definiált szintet mutat be.

```JSON
"buildingLevels":
{
    "levels": [
        {
            "levelName": "Ground",
            "ordinal": 0,
            "filename": "./Level_0.dwg"
        },
        {
            "levelName": "Parking",
            "ordinal": 0,
            "filename": "./Level_P.dwg"
        }
    ]
}
```

#### <a name="how-to-fix-conflict"></a>*Az ütközés elhárítása*

Az **ütközési** hibák elhárításához vizsgálja _meg amanifest.jst_ , és távolítsa el az ütköző információkat.

### <a name="invalidgeoreference"></a>**invalidGeoreference**

#### <a name="description-for-invalidgeoreference"></a>*A invalidGeoreference leírása*

A **invalidGeoreference** hiba akkor fordul elő, ha a fájl _manifest.js_ érvénytelen Georeference tartalmaz.

A **invalidGeoreference** hiba a következő okok közül egy vagy több miatt fordul elő:

* A felhasználó olyan szélességi vagy hosszúsági értéket georeferencing, amely kívül esik a megengedett tartományon.
* A felhasználónak olyan georeferencing kell lennie, amely kívül esik a megengedett tartományon.

#### <a name="example-scenario-for-invalidgeoreference"></a>*InvalidGeoreference-forgatókönyv – példa*

Az alábbi JSON-kódrészletben a szélesség meghaladja a felső korlátot.

```json
"georeference"
{
    "lat": 88.0,
    "lon": -122.132600,
    "angle": 0
},
```

#### <a name="how-to-fix-invalidgeoreference"></a>*A invalidGeoreference javítása*

A **invalidGeoreference** hibáinak kijavításához ellenőrizze, hogy a georeferens értékek tartományon belül vannak-e.

>[!IMPORTANT]
>A GeoJSON a koordináták sorrendje a hosszúság és a szélesség. Ha nem a megfelelő sorrendet használja, előfordulhat, hogy véletlenül olyan szélességi vagy hosszúsági értéket hivatkozik, amely kívül esik a megengedett tartományon.

## <a name="wall-errors"></a>Fali hibák

### <a name="wallerror"></a>**wallError**

#### <a name="description-for-wallerror"></a>*A wallError leírása*

A **wallError** akkor következik be, amikor a rajz hibát tartalmaz, miközben egy fali funkciót próbál létrehozni.

#### <a name="example-scenario-for-wallerror"></a>*WallError-forgatókönyv – példa*

Az alábbi képen egy olyan fal-funkció látható, amely nem fedi át az egységeket.

![Példa a fali funkcióra, amely nem fedi át az egységeket](./media/drawing-conversion-error-codes/wall-error.png)

#### <a name="how-to-fix-wallerror"></a>*A wallError javítása*

A **wallError** hibáinak kijavításához újra kell rajzolni a falatot, hogy az átfedésben legyen legalább egy egységgel. Vagy hozzon létre egy új egységet, amely átfedésben van a falon.

## <a name="vertical-penetration-errors"></a>Vertikális behatolási hibák

### <a name="verticalpenetrationerror"></a>**verticalPenetrationError**

#### <a name="description-for-verticalpenetrationerror"></a>*A verticalPenetrationError leírása*

A **verticalPenetrationError** akkor következik be, amikor a rajz nem egyértelmű vertikális behatolási funkciót tartalmaz.

A **verticalPenetrationError** a következő okok közül egy vagy több miatt fordul elő:

* A rajz egy vertikális behatolási területet tartalmaz, amely nem fedi át az átfedésben lévő vertikális behatolási területeket az alatta vagy alatta lévő bármilyen szinten.
* A rajzfájl egy olyan szintet tartalmaz, amely két vagy több vertikális bevezetési funkcióval rendelkezik, amelyek mindegyike átfedésben van egy vertikális bevezetési funkcióval egy másik szinten közvetlenül felett vagy felett.

#### <a name="example-scenario-for-verticalpenetrationerror"></a>*VerticalPenetrationError-forgatókönyv – példa*

Az alábbi képen egy vertikális behatolási terület látható, amely nem fedi át az átfedésben lévő vertikális behatolási területeket a fenti vagy alatti szinteken.

![Példa függőleges behatolásra 1](./media/drawing-conversion-error-codes/vrt-2.png)

Az alábbi képen egy vertikális behatolási hely látható, amely átfedésben van egy szomszédos szinten lévő több vertikális bevezetési résszel.

![Példa függőleges behatolásra 2](./media/drawing-conversion-error-codes/vrt-1.png)

#### <a name="how-to-fix-verticalpenetrationerror"></a>A verticalPenetrationError javítása

A **verticalPenetrationError** hibáinak kijavításához olvassa el a vertikális bevezetési funkció használata a [rajzolási csomag követelményei](drawing-requirements.md) című cikket.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [A Azure Maps Rajzolási hibák megjelenítésének használata](drawing-error-visualizer.md)

> [!div class="nextstepaction"]
> [Creator (előzetes verzió) beltéri leképezéshez](creator-indoor-maps.md)