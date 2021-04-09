---
title: Adatfolyam-parancsfájl leképezése
description: A Data Factory adatáramlási parancsfájl-kódjának áttekintése – a nyelv mögött
author: kromerm
ms.author: nimoolen
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/15/2021
ms.openlocfilehash: 7dd58a7d4a94b832e52930f8ac6507cdd8f7a20e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100534821"
---
# <a name="data-flow-script-dfs"></a>Adatfolyam-parancsfájl (DFS)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Az adatáramlási parancsfájl (DFS) a kódolási nyelvhez hasonló, alapul szolgáló metaadatok, amely a leképezési adatfolyamban található átalakítások végrehajtásához használható. Minden átalakítást olyan tulajdonságok alkotnak, amelyek biztosítják a feladatok megfelelő futtatásához szükséges információkat. A szkript látható és szerkeszthető az ADF-ből a böngésző felhasználói felületének felső menüszalagján a "script" (szkript) gombra kattintva.

![Parancsfájl gomb](media/data-flow/scriptbutton.png "Parancsfájl gomb")

A `allowSchemaDrift: true,` forrás-átalakítás esetében például azt jelzi, hogy a szolgáltatás a forrás adatkészletből származó összes oszlopot tartalmazza, még akkor is, ha azokat nem tartalmazza a séma kivetítése.

## <a name="use-cases"></a>Használati esetek
Az elosztott fájlrendszert automatikusan a felhasználói felület állítja elő. A parancsfájl gombra kattintva megtekintheti és testreszabhatja a parancsfájlt. Parancsfájlokat az ADF felhasználói felületén kívül is létrehozhat, majd átadhatja a PowerShell-parancsmagnak. Az összetett adatfolyamatok hibakeresése során könnyebben áttekintheti a szkript kódját – a folyamatok felhasználói felületi diagramjának megjelenítése helyett.

Íme néhány példa a használati esetekre:
- A programozott módon számos olyan adatfolyamatot hoz létre, amelyek meglehetősen hasonlóak, például "kibélyegzés" típusú adatforgalom.
- A felhasználói felületen nehezen kezelhető összetett kifejezések, amelyek érvényesítési problémákat eredményeznek.
- Hibakeresés és a végrehajtás során visszaadott különféle hibák jobb megértése.

Ha egy PowerShell-lel vagy API-val használandó adatfolyam-szkriptet hoz létre, a formázott szöveget egyetlen sorba kell összecsukni. A lapokat és a sortöréseket Escape-karakterként is megtarthatja. A szöveget azonban úgy kell formázni, hogy az illeszkedjen a JSON-tulajdonsághoz. A parancsfájl-szerkesztő felhasználói felületének alján egy gomb jelenik meg, amely a parancsfájlt egyetlen sorba formázza.

![a képernyő jobb oldalán található Másolás gombot](media/data-flow/copybutton.png "a képernyő jobb oldalán található Másolás gombot")

## <a name="how-to-add-transforms"></a>Átalakítások hozzáadása
Az átalakítások hozzáadásához három alapvető lépés szükséges: az alapvető átalakítási adatok hozzáadása, a bemeneti adatfolyam átirányítása, majd a kimeneti adatfolyam átirányítása. Ez a legkönnyebb példaként látható.
Tegyük fel, hogy egy egyszerű forrással kezdi el az adatforgalom elfogadását, például az alábbihoz hasonló módon:

```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

Ha úgy dönt, hogy hozzáad egy származtatott átalakítást, először létre kell hoznia az alaptranszformáció szövegét, amely egy egyszerű kifejezéssel adja hozzá az új nagybetűs oszlopot `upperCaseTitle` :
```
derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
```

Ezután megtesszük a meglévő DFS-t, és hozzáadjuk a transzformációt:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

És most átirányítjuk a bejövő adatfolyamot úgy, hogy megismerjük, hogy melyik átalakítást szeretnénk az új átalakításra kitérni (ebben az esetben `source1` ), és a stream nevét az új átalakításra másoljuk:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

Végül azonosítjuk az új átalakítást követően szeretnénk átalakulást, és lecseréljük a bemeneti streamet (ebben az esetben `sink1` ) az új átalakítás kimeneti stream-nevével:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
deriveTransformationName sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

## <a name="dfs-fundamentals"></a>Elosztott fájlrendszer alapjai
Az elosztott fájlrendszer a csatlakoztatott átalakítások sorozatából áll, beleértve a forrásokat, a mosogatókat és számos más, az új oszlopokat, az adatszűrést, az adategyesítést és sok mást. Általában a parancsfájl egy vagy több forrással kezdődik, amelyet számos átalakítás követ, és egy vagy több mosogatóval végződik.

A források mindegyike ugyanazzal az alapszintű szerkezettel rendelkezik:
```
source(
  source properties
) ~> source_name
```

Például egy egyszerű forrás három oszloppal (movieId, title, műfaj) a következő lenne:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
```

A forrásokon kívül minden átalakításnak ugyanaz az alapszintű szerkezete:
```
name_of_incoming_stream transformation_type(
  properties
) ~> new_stream_name
```

Például egy egyszerű származtatott átalakítás, amely egy oszlopot (címet) vesz fel, és felülírja egy nagybetűs változattal, a következő:
```
source1 derive(
  title = upper(title)
) ~> derive1
```

És egy sémát nem tartalmazó fogadó egyszerűen:
```
derive1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

## <a name="script-snippets"></a>Parancsfájl-kódrészletek

A parancsfájl-kódrészletek adatáramlási parancsfájlok megosztható kódja, amelyet az adatfolyamatok közötti megosztáshoz használhat. Ebből a videóból megtudhatja, hogyan használhatók a szkriptek, és hogyan használhatja az adatfolyam-parancsfájlokat az adatáramlási diagramok mögötti szkriptek részeinek másolásához és beillesztéséhez:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tA9b]


### <a name="aggregated-summary-stats"></a>Összesített összefoglaló statisztika
Vegyen fel egy összesített átalakítást a "SummaryStats" nevű adatfolyamba, majd illessze be az alábbi kódot a parancsfájlban található összesítő függvénybe, és cserélje le a meglévő SummaryStats. Ez általános mintát biztosít az adatprofilok összegző statisztikáinak létrehozásához.

```
aggregate(each(match(true()), $$+'_NotNull' = countIf(!isNull($$)), $$ + '_Null' = countIf(isNull($$))),
        each(match(type=='double'||type=='integer'||type=='short'||type=='decimal'), $$+'_stddev' = round(stddev($$),2), $$ + '_min' = min ($$), $$ + '_max' = max($$), $$ + '_average' = round(avg($$),2), $$ + '_variance' = round(variance($$),2)),
        each(match(type=='string'), $$+'_maxLength' = max(length($$)))) ~> SummaryStats
```
Az alábbi minta használatával megszámolhatja az egyedi és a különböző sorok számát is az adataiban. Az alábbi példa beilleszthető egy olyan adatfolyamatba, amelyben a ValueDistAgg nevű összesített átalakítás szerepel. Ez a példa egy "title" nevű oszlopot használ. Ügyeljen arra, hogy a "title" értéket cserélje le az adatértékek megszerzéséhez használni kívánt karakterlánc-oszlopra.

```
aggregate(groupBy(title),
    countunique = count()) ~> ValueDistAgg
ValueDistAgg aggregate(numofunique = countIf(countunique==1),
        numofdistinct = countDistinct(title)) ~> UniqDist
```

### <a name="include-all-columns-in-an-aggregate"></a>Az összes oszlop belefoglalása egy összesítésbe
Ez egy általános összesített minta, amely bemutatja, hogyan tarthatja meg a többi oszlopot a kimeneti metaadatokban az összesítések létrehozásakor. Ebben az esetben a ```first()``` függvény használatával választjuk ki az első értéket minden olyan oszlopban, amelynek a neve nem "Movie". Ennek használatához hozzon létre egy DistinctRows nevű összesített átalakítást, majd illessze be ezt a szkriptbe a meglévő DistinctRows összesített parancsfájl fölé.

```
aggregate(groupBy(movie),
    each(match(name!='movie'), $$ = first($$))) ~> DistinctRows
```

### <a name="create-row-hash-fingerprint"></a>Sor kivonatoló ujjlenyomatának létrehozása 
Használja ezt a kódot az adatfolyam-parancsfájlban egy új származtatott oszlop létrehozásához ```DWhash``` , amely ```sha1``` három oszlop kivonatát állítja elő.

```
derive(DWhash = sha1(Name,ProductNumber,Color)) ~> DWHash
```

Az alábbi parancsfájlt is használhatja az adatfolyamban található összes oszlop használatával létrehozott sorok kivonatának létrehozásához anélkül, hogy az egyes oszlopokat kellene megadnia:

```
derive(DWhash = sha1(columns())) ~> DWHash
```

### <a name="string_agg-equivalent"></a>String_agg egyenértékű
Ez a kód úgy fog működni, mint a T-SQL ```string_agg()``` függvény, és összesíti a karakterlánc-értékeket egy tömbbe. Ezt követően a tömböt egy olyan sztringbe helyezheti, amely SQL-célhelyekkel használható.

```
source1 aggregate(groupBy(year),
    string_agg = collect(title)) ~> Aggregate1
Aggregate1 derive(string_agg = toString(string_agg)) ~> StringAgg
```

### <a name="count-number-of-updates-upserts-inserts-deletes"></a>Frissítések száma, upsert, beszúrások, törlés
Módosítási sor átalakításának használatakor érdemes lehet megszámolni a frissítések számát, a upsert, a lapkákat, és törli a módosítási sor házirendjeiből származó eredményeket. Vegyen fel egy összesített átalakítást az Alter (módosítás) sor után, és illessze be ezt az adatfolyam-parancsfájlt az összesített definícióba.

```
aggregate(updates = countIf(isUpdate(), 1),
        inserts = countIf(isInsert(), 1),
        upserts = countIf(isUpsert(), 1),
        deletes = countIf(isDelete(),1)) ~> RowCount
```

### <a name="distinct-row-using-all-columns"></a>Különálló sor az összes oszlop használatával
Ez a kódrészlet egy új összesített átalakítást ad hozzá az adatfolyamathoz, amely minden bejövő oszlopot végrehajt, és egy olyan kivonatot hoz létre, amelyet a rendszer a duplikált elemek kiiktatásához használ, majd adja meg az egyes duplikált elemek első előfordulását kimenetként. Nem kell explicit módon megadnia az oszlopokat, ezeket a rendszer automatikusan létrehozza a bejövő adatfolyamból.

```
aggregate(groupBy(mycols = sha2(256,columns())),
    each(match(true()), $$ = first($$))) ~> DistinctRows
```

### <a name="check-for-nulls-in-all-columns"></a>NULL értékek keresése az összes oszlopban
Ez egy kódrészlet, amelybe beillesztheti az adatfolyamatba, hogy a NULL értékek esetében az összes oszlopot általánosan ellenőrizzék. Ez a technika kihasználja a séma-eltolódást az összes sor összes oszlopának megkereséséhez, és egy feltételes felosztást használ a NULLával nem rendelkező sorokból való elkülönítéshez. 

```
split(contains(array(columns()),isNull(#item)),
    disjoint: false) ~> LookForNULLs@(hasNULLs, noNULLs)
```

### <a name="automap-schema-drift-with-a-select"></a>AutoMap-séma drift kijelöléssel
Ha egy meglévő adatbázis-sémát egy ismeretlen vagy dinamikus bejövő oszlopból kell betölteni, a fogadó átalakításban le kell képeznie a jobb oldali oszlopokat. Erre csak meglévő tábla betöltésekor van szükség. Adja hozzá ezt a kódrészletet a fogadó előtt, és hozzon létre egy olyan kijelölést, amely automatikusan leképezi az oszlopokat. Hagyja meg a fogadó leképezését az automatikus leképezéshez.

```
select(mapColumn(
        each(match(true()))
    ),
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> automap
```

### <a name="persist-column-data-types"></a>Az oszlopok adattípusának megőrzése
Adja hozzá ezt a parancsfájlt egy származtatott oszlop definíciójában, hogy az oszlop nevét és adattípusait az adatfolyamatból egy állandó tárolóba tárolja egy fogadó használatával.

```
derive(each(match(type=='string'), $$ = 'string'),
    each(match(type=='integer'), $$ = 'integer'),
    each(match(type=='short'), $$ = 'short'),
    each(match(type=='complex'), $$ = 'complex'),
    each(match(type=='array'), $$ = 'array'),
    each(match(type=='float'), $$ = 'float'),
    each(match(type=='date'), $$ = 'date'),
    each(match(type=='timestamp'), $$ = 'timestamp'),
    each(match(type=='boolean'), $$ = 'boolean'),
    each(match(type=='long'), $$ = 'long'),
    each(match(type=='double'), $$ = 'double')) ~> DerivedColumn1
```

### <a name="fill-down"></a>Kitöltés lefelé
Itt megtudhatja, hogyan hajthatja végre az adathalmazok közös "kitöltése" problémáját, ha NULL értékeket kíván cserélni a sorozat előző nem NULL értékének értékével. Vegye figyelembe, hogy ez a művelet negatív teljesítménybeli következményekkel járhat, mivel a teljes adathalmazon létre kell hoznia egy szintetikus ablakot egy "dummy" kategóriájú értékkel. Emellett egy érték alapján kell rendeznie a megfelelő adatsorozatot, hogy megkeresse az előző nem NULL értéket. Az alábbi kódrészlet a szintetikus kategóriát "dummy"-ként hozza létre, és egy helyettesítő kulcs alapján rendezi. Eltávolíthatja a helyettesítő kulcsot, és használhatja a saját adataihoz tartozó rendezési kulcsot is. Ez a kódrészlet feltételezi, hogy már hozzáadta a forrás-átalakítást ```source1```

```
source1 derive(dummy = 1) ~> DerivedColumn
DerivedColumn keyGenerate(output(sk as long),
    startAt: 1L) ~> SurrogateKey
SurrogateKey window(over(dummy),
    asc(sk, true),
    Rating2 = coalesce(Rating, last(Rating, true()))) ~> Window1
```

### <a name="moving-average"></a>Mozgóátlag
A mozgóátlag a Windows-transzformáció használatával nagyon könnyen megvalósítható az adatforgalomban. Az alábbi példa egy 15 napos mozgóátlagot hoz létre a Microsoft számára.

```
window(over(stocksymbol),
    asc(Date, true),
    startRowOffset: -7L,
    endRowOffset: 7L,
    FifteenDayMovingAvg = round(avg(Close),2)) ~> Window1
```

## <a name="next-steps"></a>Következő lépések

Az adatfolyamatok megismerése az [adatfolyamatok áttekintése című cikkben](concepts-data-flow-overview.md) leírtak szerint
