---
title: Expression Builder az adatforgalom leképezéséhez
description: Kifejezések létrehozása a Azure Data Factory az adatfolyamok leképezése a Expression Builder használatával
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/04/2021
ms.openlocfilehash: 753f201fbde5d9e7100b6e257f8dc79e4462d7b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99584923"
---
# <a name="build-expressions-in-mapping-data-flow"></a>Kifejezések létrehozása a leképezési adatfolyamban

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A leképezési adatfolyamban számos átalakítási tulajdonságot kifejezésként kell megadni. Ezek a kifejezések olyan oszlop-értékekkel, paraméterekkel, függvényekkel, operátorokkal és literálokból állnak, amelyek futási időben kiértékelik a Spark adattípust. Az adatforgalom leképezése egy dedikált felülettel rendelkezik, amelynek célja, hogy segítse Önt a **kifejezés-szerkesztő** elnevezésű kifejezések létrehozásában. Az  [IntelliSense](/visualstudio/ide/using-intellisense) -kódok kiemeléséhez, a szintaxis ellenőrzéséhez és az autocomplete értékhez való kihasználása lehetővé teszi, hogy a Kifejezésszerkesztő megkönnyítse az adatforgalom kiépítését. Ez a cikk azt ismerteti, hogy miként használható a Kifejezésszerkesztő az üzleti logikájának hatékony felépítéséhez.

![Expression Builder](media/data-flow/expresion-builder.png "Kifejezésszerkesztő")

## <a name="open-expression-builder"></a>Expression Builder megnyitása

A Kifejezésszerkesztő megnyitásához több belépési pont is van. Ezek mind függenek az adatfolyam-átalakítás adott környezetének. A leggyakoribb használati eset olyan átalakításokban [van, mint](data-flow-aggregate.md) a [származtatott oszlop](data-flow-derived-column.md) , és a felhasználók az adatáramlás kifejezésének nyelve alapján hoznak létre vagy frissíthetnek oszlopokat. A Kifejezésszerkesztő megnyitható úgy, hogy kiválasztja az oszlopok listájának **megnyitásához a kifejezés-szerkesztőt** . Az oszlop környezetére is kattinthat, és közvetlenül a kifejezéshez is megnyithatja a Kifejezésszerkesztő kifejezést.

![Nyitó Expression Builder származtatása](media/data-flow/open-expression-builder-derive.png "Nyitó Expression Builder származtatása")

Bizonyos átalakítások, mint például a [szűrő](data-flow-filter.md), a kék kifejezés szövegmezőre kattintva megnyílik a Kifejezésszerkesztő. 

![Kék kifejezés mező](media/data-flow/expressionbox.png "Kék kifejezés mező")

Ha egyező vagy csoportosított oszlopokra hivatkozik, akkor egy kifejezés kinyerheti az oszlopokból származó értékeket. Kifejezés létrehozásához válassza a **számított oszlop** lehetőséget.

![Számított oszlop beállítás](media/data-flow/computedcolumn.png "Számított oszlop beállítás")

Azokban az esetekben, amikor egy kifejezés vagy literális érték érvényes bemenet, válassza a **dinamikus tartalom hozzáadása** lehetőséget, ha olyan kifejezést szeretne létrehozni, amely kiértékeli a konstans értéket.

![Dinamikus tartalom hozzáadása lehetőség](media/data-flow/add-dynamic-content.png "Dinamikus tartalom hozzáadása lehetőség")

## <a name="expression-elements"></a>Kifejezés elemei

Az adatfolyamatok leképezése során a kifejezések oszlop-, paraméterek, függvények, helyi változók, operátorok és literálok állhatnak. A kifejezéseknek egy Spark-adattípusra kell kiértékelniük, például string, Boolean vagy Integer értékre.

![Kifejezés elemei](media/data-flow/expression-elements.png "Kifejezés elemei")

### <a name="functions"></a>Functions

Az adatfolyamatok leképezése beépített függvényekkel és operátorokkal rendelkezik, amelyek kifejezésekben használhatók. Az elérhető függvények listáját a [leképezési adatfolyam nyelvi referenciája](data-flow-expression-functions.md)című témakörben tekintheti meg.

#### <a name="address-array-indexes"></a>Címterület-indexek

Ha olyan oszlopokat vagy függvényeket ad meg, amelyek tömb típusokat adnak vissza, a szögletes zárójelek ([]) segítségével férnek hozzá egy adott elemhez. Ha az index nem létezik, a kifejezés a NULL értéket adja vissza.

![Expression Builder-tömb](media/data-flow/expression-array.png "Kifejezési adatelőnézet")

> [!IMPORTANT]
> Az adatfolyamatok leképezése során a tömbök egy-alapúak, ami az első elemre hivatkozik az index alapján. Például a myArray [1] egy "myArray" nevű tömb első elemét fogja elérni.

### <a name="input-schema"></a>Bemeneti séma

Ha az adatfolyam bármely forrásában definiált sémát használ, az oszlopokat név szerint is hivatkozhat számos kifejezésben. Ha séma-eltolódást használ, explicit módon hivatkozhat oszlopokra az `byName()` vagy `byNames()` függvények vagy egyezés használatával.

#### <a name="column-names-with-special-characters"></a>Oszlopok nevei speciális karakterekkel

Ha speciális karaktereket vagy szóközöket tartalmazó oszlopnevek vannak, a nevet kapcsos zárójelekkel kell megadnia, hogy azok egy kifejezésben legyenek hivatkozva.

```{[dbo].this_is my complex name$$$}```

### <a name="parameters"></a>Paraméterek

A paraméterek olyan értékek, amelyeket a rendszer a folyamat futási idején átadott adatfolyamként továbbít. Egy paraméterre való hivatkozáshoz kattintson a paraméterre a **kifejezés elemek** nézetében, vagy hivatkozzon a neve előtt egy Dollar-jelre. Egy Parameter1 nevű paramétert például a következőre hivatkozhat: `$parameter1` . További információ: parameterizing- [leképezési adatforgalom](parameters-data-flow.md).

### <a name="cached-lookup"></a>Gyorsítótárazott keresés

A gyorsítótárazott keresések lehetővé teszik a gyorsítótárazott fogadó kimenetének beágyazott keresését. Az egyes gyűjtők esetében két függvény használható, `lookup()` és `outputs()` . A függvények hivatkozásának szintaxisa a következő: `cacheSinkName#functionName()` . További információ: [gyorsítótár](data-flow-sink.md#cache-sink)-tárolók.

`lookup()` a jelenlegi átalakításban lévő egyező oszlopok paraméterként való beolvasása, és egy összetett oszlop visszaadása, amely megegyezik a gyorsítótár-fogadó fő oszlopával egyező sorral. A visszaadott összetett oszlop a gyorsítótár-fogadóban leképezett egyes oszlopok aloszlopát tartalmazza. Ha például egy hibakódot tartalmazó gyorsítótár-tárolót kapott `errorCodeCache` , amely a kóddal és egy nevű oszloppal rendelkezik `Message` . A hívás `errorCodeCache#lookup(errorCode).Message` visszaadja az átadott kódnak megfelelő üzenetet. 

`outputs()` nem fogad paramétereket, és a teljes gyorsítótár-fogadót összetett oszlopok tömbje adja vissza. Ez nem hívható meg, ha kulcs oszlopai vannak megadva a fogadóban, és csak akkor használhatók, ha kis számú sor van a gyorsítótár-fogadóban. Gyakori használati eset a növekményes kulcs maximális értékének hozzáfűzése. Ha egy gyorsítótárazott, egyetlen aggregált sor `CacheMaxKey` tartalmaz egy oszlopot `MaxKey` , a meghívásával hivatkozhat az első értékre `CacheMaxKey#outputs()[1].MaxKey` .

![Gyorsítótárazott keresés](media/data-flow/cached-lookup-example.png "Gyorsítótárazott keresés")

### <a name="locals"></a>Figyelőpontjaival

Ha több oszlop között osztja meg a logikát, vagy szeretné felosztják a logikát, létrehozhat egy helyi column\. belül Ha egy helyi hivatkozásra szeretne hivatkozni, kattintson a helyi **elemre a kifejezés elemei** nézetben, vagy hivatkozzon rá a neve előtt található kettőspontra. Például egy Helyi1 nevű helyi hivatkozásra a következő hivatkozik: `:local1` . További információ a helyiekről a [származtatott oszlop dokumentációjában olvasható](data-flow-derived-column.md#locals).

## <a name="preview-expression-results"></a>Előnézet kifejezés eredményei

Ha a [hibakeresési mód](concepts-data-flow-debug-mode.md) be van kapcsolva, interaktív módon használhatja a hibakeresési fürtöt, hogy megtekintse a kifejezés kiértékelését. Válassza az adatelőnézet elem melletti **frissítés** lehetőséget az adatelőnézet eredményeinek frissítéséhez. Az egyes sorok kimenetét láthatja a bemeneti oszlopokban.

![Folyamatban lévő előzetes verzió](media/data-flow/preview-expression.png "Kifejezési adatelőnézet")

## <a name="string-interpolation"></a>Karakterlánc-interpoláció

A kifejezési elemeket használó hosszú karakterláncok létrehozásakor a karakterlánc-interpolációval egyszerűen hozhat létre összetett karakterlánc-logikát. A karakterlánc-interpoláció elkerüli a karakterláncok összefűzésének széles körű használatát, ha paraméterek szerepelnek a lekérdezési karakterláncokban. Az idézőjelek között idézőjelek közé kell foglalni a literális karakterláncot a kifejezésekkel együtt. Kifejezési függvények, oszlopok és paraméterek is megadhatók. A kifejezés szintaxisának használatához kapcsos zárójelek közé kell tenni,

Néhány példa a karakterlánc-interpolációra:

* ```"My favorite movie is {iif(instr(title,', The')>0,"The {split(title,', The')[1]}",title)}"```

* ```"select * from {$tablename} where orderyear > {$year}"```

* ```"Total cost with sales tax is {round(totalcost * 1.08,2)}"```

* ```"{:playerName} is a {:playerRating} player"```

> [!NOTE]
> Ha karakterlánc-interpolációs szintaxist használ az SQL-forrás lekérdezésekben, a lekérdezési karakterláncnak egyetlen sorban kell lennie, "/n" nélkül.

## <a name="commenting-expressions"></a>Megjegyzések kifejezései

Megjegyzéseket fűzhet a kifejezésekhez egy egysoros és többsoros Megjegyzés szintaxisának használatával.

Az alábbi példák érvényes megjegyzéseket tartalmaznak:

* ```/* This is my comment */```

* ```/* This is a```
* ```multi-line comment */```

Ha a kifejezés tetején egy megjegyzést helyez el, az átalakítás szövegmezőben megjelenik az átalakítási kifejezések dokumentálása.

![Megjegyzés az átalakítás szövegmezőben](media/data-flow/comment-expression.png "Megjegyzések")

## <a name="regular-expressions"></a>Reguláris kifejezések

Számos kifejezés nyelvi függvénye reguláris kifejezési szintaxist használ. Reguláris kifejezések használata esetén az Expression Build Escape-karakterként próbálja értelmezni a fordított perjelet ( \\ ). Ha a reguláris kifejezésben fordított perjeleket használ, zárja be a teljes regexet a aposztrófokkal () metódusba, \` vagy használjon dupla fordított perjelet.

Aposztrófokkal használó példa:

```
regex_replace('100 and 200', `(\d+)`, 'digits')
```

Dupla perjelet használó példa:

```
regex_replace('100 and 200', '(\\d+)', 'digits')
```

## <a name="keyboard-shortcuts"></a>Billentyűparancsok

Az alábbi lista a Expression Builder-ben elérhető parancsikonokat tartalmazza. Kifejezések létrehozásakor a legtöbb IntelliSense-parancsikon elérhető.

* CTRL + K CTRL + C: Megjegyzés teljes sor.
* CTRL + K CTRL + U: Megjegyzés visszatartása.
* F1: adja meg a szerkesztő súgójának utasításait.
* ALT + lefelé nyíl gomb: az aktuális sor lejjebb helyezése.
* ALT + fel nyíl gomb: az aktuális sor feljebb helyezése.
* CTRL + SZÓKÖZ: környezetfüggő súgó megjelenítése.

## <a name="commonly-used-expressions"></a>Gyakran használt kifejezések

### <a name="convert-to-dates-or-timestamps"></a>Konvertálás dátumokra vagy időbélyegekre

Ha karakterlánc-konstansokat szeretne szerepeltetni az időbélyeg-kimenetben, csomagolja be a konverzióját ```toString()``` .

```toString(toTimestamp('12/31/2016T00:12:00', 'MM/dd/yyyy\'T\'HH:mm:ss'), 'MM/dd /yyyy\'T\'HH:mm:ss')```

Az ezredmásodpercek időpontról dátumra vagy időbélyegre való konvertálásához használja a következőt: `toTimestamp(<number of milliseconds>)` . Ha az idő másodpercben érkezik, szorozza meg a 1 000-as értékkel.

```toTimestamp(1574127407*1000l)```

Az előző kifejezés végén lévő "l" záró érték hosszú típusra, beágyazott szintaxisként való átalakítást jelent.

### <a name="find-time-from-epoch-or-unix-time"></a>Idő megkeresése az EPOCH vagy a Unix idő alapján

toLong (currentTimestamp ()-toTimestamp (' 1970-01-01 00:00:00.000 ', ' éééé-hh-nn óó: PP: mm. ÉER) *) * 1000

## <a name="next-steps"></a>Következő lépések

[Az Adatátalakítási kifejezések létrehozásának megkezdése](data-flow-expression-functions.md)
