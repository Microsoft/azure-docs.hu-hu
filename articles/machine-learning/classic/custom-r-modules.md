---
title: 'ML Studio (klasszikus): létrehozás & egyéni R-modulok üzembe helyezése – Azure'
description: Ismerje meg, hogyan hozhat létre és helyezhet üzembe egyéni R-modulokat ML Studio (klasszikus).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 11/29/2017
ms.openlocfilehash: d44f2cfa72bd53b01da073fca31ca698eb42720d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100520476"
---
# <a name="define-custom-r-modules-for-machine-learning-studio-classic"></a>Egyéni R-modulok definiálása a Machine Learning Studiohoz (klasszikus)

**a következőkre vonatkozik:** ![ A következőre vonatkozik:. ](../../../includes/media/aml-applies-to-skus/yes.png) A Machine Learning Studio (klasszikus) ![ nem vonatkozik a következőre:.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  

Ez a témakör bemutatja, hogyan hozhat létre és helyezhet üzembe egyéni R studiót (klasszikus). Elmagyarázza, hogy milyen egyéni R-modulokat és milyen fájlokat használnak ezek definiálásához. Bemutatja, hogyan hozhat létre olyan fájlokat, amelyek meghatározzák a modult, és hogyan regisztrálhatják a modult az üzembe helyezéshez egy Machine Learning munkaterületen. Az egyéni modul definíciójában használt elemeket és attribútumokat a rendszer részletesebben ismerteti. A kiegészítő funkciók és a fájlok használata, valamint a több kimenet is megbeszélve. 

Az **Egyéni modul** egy felhasználó által definiált modul, amely feltölthető a munkaterületre, és Azure Machine learning Studio (klasszikus) kísérlet részeként is végrehajtható. Az **Egyéni r-modul** egy egyéni modul, amely egy felhasználó által definiált r-függvényt hajt végre. Az **R** olyan statisztikai számítástechnikai és grafikai programozási nyelv, amelyet a statisztikusok és az adatszakértők széles körben használnak az algoritmusok megvalósításához. Jelenleg az egyéni modulok csak az egyedüli nyelvet támogatják, a további nyelvek támogatása pedig későbbi kiadásokra van ütemezve.

Az egyéni modulok az **első osztályú állapottal** rendelkeznek Azure Machine learning Studio (klasszikus) abban az értelemben, hogy ugyanúgy használhatók, mint bármely más modul. A közzétett kísérletekben vagy vizualizációkban található más modulokkal is végrehajthatók. Ön szabályozhatja a modul által megvalósított algoritmust, a használandó bemeneti és kimeneti portokat, a modellezési paramétereket és a különböző futásidejű viselkedéseket. Az egyéni modulokat tartalmazó kísérletek könnyen megoszthatók a Azure AI Gallery is.

## <a name="files-in-a-custom-r-module"></a>Egyéni R-modul fájljai
Egy egyéni R modult egy. zip fájl definiál, amely legalább két fájlt tartalmaz:

* A modul által elérhető R-függvényt megvalósító **forrásfájl**
* Az egyéni modul felületét ismertető **XML-definíciós fájl**

További kiegészítő fájlok is szerepelhetnek a. zip fájlban, amely az egyéni modulból elérhető funkciókat biztosít. Ezt a beállítást az **XML-definíciós fájl** hivatkozási szakaszának **argumentumok** részében találja, a gyors üzembe helyezési példát követve.

## <a name="quickstart-example-define-package-and-register-a-custom-r-module"></a>Gyors üzembe helyezési példa: egyéni R-modul definiálása, becsomagolása és regisztrálása
Ez a példa azt szemlélteti, hogyan lehet létrehozni egy egyéni R modul által igényelt fájlokat, csomagolja őket egy zip-fájlba, majd regisztrálja a modult a Machine Learning munkaterületen. A zip-csomag és a minta fájljai letölthetők a [letöltés CustomAddRows.zip fájlból](https://go.microsoft.com/fwlink/?LinkID=524916&clcid=0x409).

## <a name="the-source-file"></a>A forrásfájl
Vegyünk például egy olyan **egyéni sorok hozzáadása** modult, amely módosítja a sorok **hozzáadása** modul standard implementációját, amely két adatkészletből (adatkeretből) való összefűzéshez használatos. A standard **Add sorok** modul hozzáfűzi a második bemeneti adatkészlet sorait az első bemeneti adatkészlet végéhez az `rbind` algoritmus használatával. A testreszabott `CustomAddRows` függvény Hasonlóképpen két adatkészletet fogad el, de egy logikai swap paramétert is elfogad további bemenetként. Ha a swap paraméter **hamis** értékre van beállítva, akkor ugyanazt az adatkészletet adja vissza, mint a normál implementáció. Ha azonban a swap paraméter értéke **true (igaz**), a függvény hozzáfűzi az első bemeneti adatkészlet sorait a második adatkészlet végéhez. Az `CustomAddRows` **egyéni sorok hozzáadása** modul által elérhető r-függvény megvalósítását tartalmazó CustomAddRows. R fájl a következő R-kóddal rendelkezik.

```r
CustomAddRows <- function(dataset1, dataset2, swap=FALSE) 
{
    if (swap)
    {
        return (rbind(dataset2, dataset1));
    }
    else
    {
        return (rbind(dataset1, dataset2));
    } 
} 
```

### <a name="the-xml-definition-file"></a>Az XML-definíciós fájl
Ahhoz, hogy ez `CustomAddRows` a függvény elérhető legyen a Azure Machine learning Studio (klasszikus) modulként, létre kell hoznia egy XML-definíciós fájlt, amely meghatározza, hogy az **egyéni sorok hozzáadása** modul hogyan nézzen és viselkedjen. 

```xml
<!-- Defined a module using an R Script -->
<Module name="Custom Add Rows">
    <Owner>Microsoft Corporation</Owner>
    <Description>Appends one dataset to another. Dataset 2 is concatenated to Dataset 1 when Swap is FALSE, and vice versa when Swap is TRUE.</Description>

<!-- Specify the base language, script file and R function to use for this module. -->        
    <Language name="R" 
        sourceFile="CustomAddRows.R" 
        entryPoint="CustomAddRows" />  

<!-- Define module input and output ports -->
<!-- Note: The values of the id attributes in the Input and Arg elements must match the parameter names in the R Function CustomAddRows defined in CustomAddRows.R. -->
    <Ports>
        <Input id="dataset1" name="Dataset 1" type="DataTable">
            <Description>First input dataset</Description>
        </Input>
        <Input id="dataset2" name="Dataset 2" type="DataTable">
            <Description>Second input dataset</Description>
        </Input>
        <Output id="dataset" name="Dataset" type="DataTable">
            <Description>The combined dataset</Description>
        </Output>
    </Ports>

<!-- Define module parameters -->
    <Arguments>
        <Arg id="swap" name="Swap" type="bool" >
            <Description>Swap input datasets.</Description>
        </Arg>
    </Arguments>
</Module>
```

Fontos megjegyezni, hogy az XML-fájlban szereplő **bemeneti** és **ARG** elemek **azonosító** attribútumainak értékének meg kell egyeznie a CustomAddRows. r fájlban található r-kód Function paraméterének nevével pontosan: (*DataSet1 elemet*, *dataset2* és *swap* a példában). Hasonlóképpen, a **nyelvi** elem **BelépésiPont** attribútumának értékének meg kell egyeznie a függvény nevével az R-szkriptben pontosan: (a példában a *CustomAddRows* ). 

Ezzel szemben a **kimeneti** elem **ID** attribútuma nem felel meg az R-szkriptben szereplő változóknak. Ha egynél több kimenetre van szükség, egyszerűen visszaállíthat egy listát az R-függvényből, amelynek *a sorrendje megegyezik* a **kimeneti** elemek XML-fájlban való deklarált eredményeivel.

### <a name="package-and-register-the-module"></a>A modul becsomagolása és regisztrálása
Mentse a két fájlt *CustomAddRows. R* néven, majd *CustomAddRows.xml* , majd a két fájlt egy *CustomAddRows.zip* fájlba.

Ha regisztrálni szeretné őket a Machine Learning munkaterületen, lépjen a munkaterületre Azure Machine Learning Studio (klasszikus) területen, kattintson az **+ új** gombra az alján, és válassza a **modul-> a zip-csomagból** lehetőséget az új **egyéni sorok hozzáadása** modul feltöltéséhez.

![Zip feltöltése](./media/custom-r-modules/upload-from-zip-package.png)

Az **egyéni sorok hozzáadása** modul most már készen áll a Machine learning kísérletek elérésére.

## <a name="elements-in-the-xml-definition-file"></a>Az XML-definíciós fájl elemei
### <a name="module-elements"></a>Modul elemei
A **Module** elem egy egyéni modul definiálására használható az XML-fájlban. Több modul is definiálható egy XML-fájlban több **modul** elem használatával. A munkaterület minden moduljának egyedi névvel kell rendelkeznie. Regisztráljon egy egyéni modult ugyanazzal a névvel, mint egy meglévő egyéni modult, és lecseréli a meglévő modult az újat. Az egyéni modulok azonban a meglévő Azure Machine Learning Studio (klasszikus) modul nevével megegyező névvel regisztrálhatók. Ha igen, a modul paletta **Egyéni** kategóriájában jelennek meg.

```xml
<Module name="Custom Add Rows" isDeterministic="false"> 
    <Owner>Microsoft Corporation</Owner>
    <Description>Appends one dataset to another...</Description>/> 
```

A **modul** elemen belül két további választható elemet is megadhat:

* a modulba ágyazott **tulajdonosi** elem  
* a **modul** gyors súgójában megjelenő szöveg, amely a Machine learning felhasználói felületén található modul fölé mutat.

A karakterek korlátozásai a modul elemeiben:

* A **Module** elem **Name** attribútumának értéke nem lehet hosszabb 64 karakternél. 
* A **Leírás** elem tartalma nem lehet hosszabb 128 karakternél.
* A **tulajdonos** elem tartalma nem lehet hosszabb 32 karakternél.

A modulok eredményei lehetnek determinisztikus vagy determinált. * * alapértelmezés szerint az összes modul determinisztikus tekintendő. Ez azt eredményezi, hogy a bemeneti paraméterek és adatok változatlanul eltérő készletét kell visszaadnia, és a modulnak ugyanazt az eredményt kell visszaadnia, vagy eacRAND. Ennek a viselkedésnek a miatt a Azure Machine Learning Studio (klasszikus) csak determinisztikus jelölésű modulokat futtat, ha a paraméter vagy a bemeneti adatok módosultak. A gyorsítótárazott eredmények visszaadása a kísérletek sokkal gyorsabb végrehajtását is lehetővé teszi.

Vannak olyan függvények, amelyek determinált, például a RAND vagy egy függvény, amely az aktuális dátumot vagy időpontot adja vissza. Ha a modul determinált függvényt használ, megadhatja, hogy a modul nem determinisztikus, ha a választható **isDeterministic** attribútumot **hamis** értékre állítja. Ez azt biztosítja, hogy a modul a kísérlet futtatásakor újra fut, még akkor is, ha a modul bemenete és paraméterei nem változtak. 

### <a name="language-definition"></a>Nyelvi definíció
Az XML-definíciós fájl **nyelvi** eleme az egyéni modul nyelvének megadására szolgál. Jelenleg az R az egyetlen támogatott nyelv. A **sourceFile** attribútum értékének meg kell egyeznie azon R-fájl nevével, amely a modul futásakor hívni kívánt függvényt tartalmazza. Ennek a fájlnak a zip-csomag részeként kell szerepelnie. A **BelépésiPont** attribútum értéke a hívott függvény neve, és meg kell egyeznie a forrásfájl által definiált érvényes függvénnyel.

```xml
<Language name="R" sourceFile="CustomAddRows.R" entryPoint="CustomAddRows" />
```

### <a name="ports"></a>Portok
Az egyéni modul bemeneti és kimeneti portjai az XML-definíciós fájl **portok** szakaszának alárendelt elemeiben vannak megadva. Ezeknek az elemeknek a sorrendje határozza meg a felhasználók által tapasztalt elrendezést (UX). Az XML-fájl **portok** elemében felsorolt első gyermek **bemenet** vagy **kimenet** a Machine learning UX bal szélső bemeneti portja lesz.
Előfordulhat, hogy az egyes bemeneti és kimeneti portok opcionális **leírási** alárendelt elemmel rendelkeznek, amely megadja az egérmutatónak a Machine learning felhasználói felületen lévő port fölé helyezésekor megjelenő szöveget.

**Portok szabályai**:

* A **bemeneti és a kimeneti portok** maximális száma 8.

### <a name="input-elements"></a>Bemeneti elemek
A bemeneti portok lehetővé teszik, hogy adatokat továbbítson az R-függvénynek és a munkaterületnek. A bemeneti portok által támogatott **adattípusok** a következők: 

**DataTable:** Ezt a típust a rendszer adat. keretként továbbítja az R-függvénynek. Valójában minden olyan típust (például CSV-fájlt vagy ARFF-fájlt), amelyet a Machine Learning támogat, és amelyek kompatibilisek a **DataTable adattáblával** , automatikusan átváltanak egy adatkeretbe. 

```xml
<Input id="dataset1" name="Input 1" type="DataTable" isOptional="false">
    <Description>Input Dataset 1</Description>
</Input>
```

Az egyes **DataTable** bemeneti portokhoz társított **ID** attribútumnak egyedi értékkel kell rendelkeznie, és ennek az értéknek meg kell egyeznie az R-függvény megfelelő megnevezett paraméterével.
Azok a nem kötelező **DataTable** portok, amelyeket nem a kísérlet bemenetként továbbítanak, az R-függvénynek **Null** értéket adtak át, és a választható zip-portok figyelmen kívül lesznek hagyva, ha a bemenet nincs csatlakoztatva. A **isOptional** attribútum nem kötelező a **DataTable** és a **zip** típushoz, és alapértelmezés szerint *hamis* .

**Zip:** Az egyéni modulok bemenetként is elfogadhatják a ZIP-fájlokat. Ezt a bemenetet a függvény R Working könyvtárába csomagoljuk

```xml
<Input id="zippedData" name="Zip Input" type="Zip" IsOptional="false">
    <Description>Zip files to be extracted to the R working directory.</Description>
</Input>
```

Az egyéni R-modulok esetében a zip-portok AZONOSÍTÓjának nem kell egyeznie az R függvény egyetlen paraméterével sem. Ennek az az oka, hogy a rendszer automatikusan Kinyeri a zip-fájlt az R Working könyvtárba.

**Bemeneti szabályok:**

* A **bemeneti** elem **ID** attribútumának értékének érvényes R-változó névnek kell lennie.
* A **bemeneti** elem **ID** attribútumának értéke nem lehet hosszabb 64 karakternél.
* A **bemeneti** elem **Name** attribútumának értéke nem lehet hosszabb 64 karakternél.
* A **Leírás** elem tartalma nem lehet hosszabb 128 karakternél.
* A **bemeneti** elem **Type** attribútumának értéke csak *zip* vagy *DataTable* lehet.
* A **bemeneti** elem **isOptional** attribútumának értéke nem kötelező (és alapértelmezés szerint *hamis* , ha nincs megadva); Ha azonban meg van adva, akkor *igaz* vagy *hamis értéknek* kell lennie.

### <a name="output-elements"></a>Kimeneti elemek
**Szabványos kimeneti portok:** A kimeneti portok az R-függvény visszatérési értékeire vannak leképezve, amelyet aztán a későbbi modulok használhatnak. A *DataTable* az egyetlen, jelenleg támogatott szabványos kimeneti port. (A *tanulók* és az *átalakítások* támogatása hamarosan megtörténik.) A *DataTable* kimenet a következőképpen van definiálva:

```xml
<Output id="dataset" name="Dataset" type="DataTable">
    <Description>Combined dataset</Description>
</Output>
```

Az egyéni R-modulok kimenetei esetében az **ID** attribútum értékének nem kell megfelelnie az R-parancsfájlban található semmit, de egyedinek kell lennie. Egyetlen modul kimenete esetén az R függvény visszatérési értékének egy *adat. frame típusúnak* kell lennie. A támogatott adattípusok egynél több objektumának exportálásához meg kell adni a megfelelő kimeneti portokat az XML-definíciós fájlban, és az objektumokat listaként kell megadni. A kimeneti objektumok a bal és jobb oldali kimeneti portokhoz vannak rendelve, ami azt jelzi, hogy az objektumok milyen sorrendben kerülnek a visszaadott listára.

Ha például módosítani kívánja az **egyéni sorok hozzáadása** modult, hogy az az eredeti két adatkészletet, a *DataSet1 elemet* és a *dataset2* az új csatlakoztatott adatkészlet, az *adatkészlet*(egy rendelésben, balról jobbra, a következőt adja meg: *adatkészlet*, *DataSet1 elemet*, *dataset2*), akkor a következőképpen határozza meg a kimeneti portokat a CustomAddRows.xml fájlban:

```xml
<Ports> 
    <Output id="dataset" name="Dataset Out" type="DataTable"> 
        <Description>New Dataset</Description> 
    </Output> 
    <Output id="dataset1_out" name="Dataset 1 Out" type="DataTable"> 
        <Description>First Dataset</Description> 
    </Output> 
    <Output id="dataset2_out" name="Dataset 2 Out" type="DataTable"> 
        <Description>Second Dataset</Description> 
    </Output> 
    <Input id="dataset1" name="Dataset 1" type="DataTable"> 
        <Description>First Input Table</Description>
    </Input> 
    <Input id="dataset2" name="Dataset 2" type="DataTable"> 
        <Description>Second Input Table</Description> 
    </Input> 
</Ports> 
```

A listában szereplő objektumok listájának visszaadása a "CustomAddRows. R" megfelelő sorrendjében:

```r
CustomAddRows <- function(dataset1, dataset2, swap=FALSE) { 
    if (swap) { dataset <- rbind(dataset2, dataset1)) } 
    else { dataset <- rbind(dataset1, dataset2)) 
    } 
    return (list(dataset, dataset1, dataset2)) 
} 
```

**Vizualizáció kimenete:** Megadhat egy *vizualizáció* típusú kimeneti portot is, amely megjeleníti az R Graphics eszköz és a konzol kimenetének kimenetét. Ez a port nem része az R-függvény kimenetének, és nem zavarja a többi kimeneti port típusának sorrendjét. Ha vizualizációs portot szeretne hozzáadni az egyéni modulokhoz, adjon hozzá egy **kimeneti** elemet a **Type** attribútumhoz a *vizualizáció* értékével:

```xml
<Output id="deviceOutput" name="View Port" type="Visualization">
    <Description>View the R console graphics device output.</Description>
</Output>
```

**Kimeneti szabályok:**

* A **kimeneti** elem **ID** attribútumának értékének érvényes R-változó névnek kell lennie.
* A **kimeneti** elem **ID** attribútumának értéke nem lehet hosszabb 32 karakternél.
* A **kimeneti** elem **Name** attribútumának értéke nem lehet hosszabb 64 karakternél.
* A **kimeneti** elem **Type** attribútumának értékének a *vizualizációnak* kell lennie.

### <a name="arguments"></a>Argumentumok
További adatok adhatók át az R függvénynek az **argumentumok** elemben definiált modul-paramétereken keresztül. Ezek a paraméterek a Machine Learning felhasználói felület jobb szélső tulajdonságok paneljén jelennek meg, ha a modul ki van választva. Az argumentumok bármelyike támogatott típus lehet, vagy szükség esetén egyéni enumerálást is létrehozhat. A **portok** elemeihez hasonlóan az **argumentumok** elemei opcionális **leírási** elemmel is rendelkezhetnek, amely meghatározza azt a szöveget, amely akkor jelenik meg, amikor az egérmutatót a paraméter neve fölé viszi.
Egy modul nem kötelező tulajdonságai, például a defaultValue, a minValue és a maxValue bármely argumentumként hozzáadhatók attribútumként egy **tulajdonság** elemhez. A **Properties** elem érvényes tulajdonságai az argumentum típusától függenek, és a következő szakaszban szereplő támogatott argumentum-típusokkal vannak leírva. Az **"igaz"** értékre beállított **isOptional** tulajdonsággal rendelkező argumentumok nem igénylik, hogy a felhasználó értéket adjon meg. Ha nincs megadva érték az argumentumhoz, akkor a rendszer nem adja át az argumentumot a belépési pont függvénynek. A beléptetési pont függvény argumentumait explicit módon kell kezelni a függvénynek, például a NULL értékhez rendelt alapértelmezett értéket a belépési pont függvény definíciójában. Ha a felhasználó értéket ad meg, a választható argumentumok csak a többi argumentum korlátozását (például min vagy max) fogják érvényesíteni.
A bemenetekhez és kimenetekhez hasonlóan elengedhetetlen, hogy mindegyik paraméterhez egyedi azonosító értékek legyenek társítva. A gyors üzembe helyezési példában a kapcsolódó azonosító/paraméter *felcserélve*.

### <a name="arg-element"></a>ARG-elem
A modul paramétereit az XML-definíciós fájl **argumentumok** szakaszának **ARG** gyermek eleme alapján definiáljuk. A **portok** szakasz alárendelt elemeihez hasonlóan az **argumentumok** szakaszban szereplő paraméterek sorrendje határozza meg az UX-ben észlelt elrendezést. A paraméterek a felhasználói felületen felülről lefelé jelennek meg, ugyanabban a sorrendben, ahogyan az XML-fájlban vannak meghatározva. A paraméterek Machine Learning által támogatott típusok itt láthatók. 

**int** – egész szám (32 bites) típusú paraméter.

```xml
<Arg id="intValue1" name="Int Param" type="int">
    <Properties min="0" max="100" default="0" />
    <Description>Integer Parameter</Description>
</Arg>
```

* *Választható tulajdonságok*: **min**., **Max**., **alapértelmezett** és **isOptional**

**Double** – egy Double Type paraméter.

```xml
<Arg id="doubleValue1" name="Double Param" type="double">
    <Properties min="0.000" max="0.999" default="0.3" />
    <Description>Double Parameter</Description>
</Arg>
```

* *Választható tulajdonságok*: **min**., **Max**., **alapértelmezett** és **isOptional**

**bool** – egy logikai paraméter, amelyet az UX egy jelölőnégyzete jelöl.

```xml
<Arg id="boolValue1" name="Boolean Param" type="bool">
    <Properties default="true" />
    <Description>Boolean Parameter</Description>
</Arg>
```

* *Választható tulajdonságok*: **default** -false, ha nincs beállítva

**String**: standard sztring

```xml
<Arg id="stringValue1" name="My string Param" type="string">
    <Properties isOptional="true" />
    <Description>String Parameter 1</Description>
</Arg>    
```

* Nem *kötelező tulajdonságok*: **alapértelmezett** és **isOptional**

**ColumnPicker**: oszlop kiválasztási paramétere. Ez a típus az UX-ben oszlop-kiválasztó jelenik meg. Itt  megadhatja annak a portnak az azonosítóját, amelyből az oszlopok ki vannak választva, ahol a célként megadott portnak *DataTable* típusúnak kell lennie. Az oszlop kijelölésének eredményét átadja az R függvénynek a kijelölt oszlopnevek nevét tartalmazó sztringek listájaként. 

```xml
<Arg id="colset" name="Column set" type="ColumnPicker">      
    <Properties portId="datasetIn1" allowedTypes="Numeric" default="NumericAll"/>
    <Description>Column set</Description>
</Arg>
```

* *Szükséges tulajdonságok*: **PortId** – a *DataTable* típusú bemeneti elem azonosítójának felel meg.
* Nem *kötelező tulajdonságok*:
  
  * **allowedTypes** – a kiválasztható oszlopok típusát szűri. Az érvényes értékek a következők: 
    
    * Numerikus
    * Logikai érték
    * Kategorikus
    * Sztring
    * Címke
    * Szolgáltatás
    * Pontszám
    * Mind
  * **alapértelmezett** – az oszlop választójának alapértelmezett értékei a következők: 
    
    * Nincsenek
    * NumericFeature
    * NumericLabel
    * NumericScore
    * Numerikus
    * BooleanFeature
    * BooleanLabel
    * BooleanScore
    * BooleanAll
    * CategoricalFeature
    * CategoricalLabel
    * CategoricalScore
    * CategoricalAll
    * StringFeature
    * StringLabel
    * StringScore
    * StringAll
    * AllLabel
    * AllFeature
    * AllScore
    * Mind

**Legördülő menü**: felhasználó által megadott enumerálás (legördülő lista). A legördülő elemek a **Tulajdonságok** elemen belül, egy **Item** elem használatával vannak megadva. Az egyes **elemek** **azonosítójának** egyedinek és érvényes R-változónak kell lennie. Az **elemek** **nevének** értéke a megjelenő szövegként és az R-függvénynek átadott értékként szolgál.

```xml
<Arg id="color" name="Color" type="DropDown">
    <Properties default="red">
        <Item id="red" name="Red Value"/>
        <Item id="green" name="Green Value"/>
        <Item id="blue" name="Blue Value"/>
    </Properties>
    <Description>Select a color.</Description>
</Arg>    
```

* Nem *kötelező tulajdonságok*:
  * **alapértelmezett** – az alapértelmezett tulajdonság értékének meg kell egyeznie az **elem** egyik elemének azonosító értékével.

### <a name="auxiliary-files"></a>Kiegészítő fájlok
Az egyéni modul ZIP-fájljába helyezett összes fájl elérhető lesz a végrehajtás ideje alatt. A rendszer megőrzi a szükséges címtár-struktúrákat. Ez azt jelenti, hogy a fájl beszerzése a helyi és a Azure Machine Learning Studio (klasszikus) végrehajtással azonos módon működik. 

> [!NOTE]
> Figyelje meg, hogy minden fájl "src" könyvtárba van kibontva, így minden elérési útnak "src/" előtaggal kell rendelkeznie.
> 
> 

Tegyük fel például, hogy el kívánja távolítani a NAs-ból származó összes sort az adatkészletből, és el is távolítja az ismétlődő sorokat, mielőtt a CustomAddRows, és már írt egy R-függvényt, amely egy RemoveDupNARows. R fájlban található.

```r
RemoveDupNARows <- function(dataFrame) {
    #Remove Duplicate Rows:
    dataFrame <- unique(dataFrame)
    #Remove Rows with NAs:
    finalDataFrame <- dataFrame[complete.cases(dataFrame),]
    return(finalDataFrame)
}
```

A RemoveDupNARows. R kiegészítő fájlt a CustomAddRows függvényben is elvégezheti:

```r
CustomAddRows <- function(dataset1, dataset2, swap=FALSE) {
    source("src/RemoveDupNARows.R")
        if (swap) { 
            dataset <- rbind(dataset2, dataset1))
        } else { 
            dataset <- rbind(dataset1, dataset2)) 
        } 
    dataset <- removeDupNARows(dataset)
    return (dataset)
}
```

Ezután töltsön fel egy "CustomAddRows. R", "CustomAddRows.xml" és "RemoveDupNARows. R" nevű zip-fájlt egyéni R-modulként.

## <a name="execution-environment"></a>Végrehajtási környezet
Az R-szkript végrehajtási környezete ugyanazt az R-verziót használja, mint az r **szkript végrehajtása** modul, és ugyanazokat az alapértelmezett csomagokat használhatja. Az egyéni modulhoz további R-csomagokat is hozzáadhat, ha azokat az egyéni modul ZIP-csomagjába helyezi. Csak töltse be őket az R-szkriptbe úgy, hogy a saját R-környezetében lenne. 

**A végrehajtási környezet korlátai** a következők:

* Nem állandó fájlrendszer: az egyéni modul futtatásakor megírt fájlok nem maradnak meg ugyanazon modul több futtatásán keresztül.
* Nincs hálózati hozzáférés