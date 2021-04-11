---
title: Ismerje meg Apache Spark Azure Data Lake Analytics U-SQL-fejlesztőknek szóló kód fogalmait.
description: Ez a cikk Apache Spark fogalmakat ismerteti, amelyek segítenek a U-SQL-fejlesztők számára a Spark-kód fogalmait.
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.custom: Understand-apache-spark-code-concepts
ms.date: 10/15/2019
ms.openlocfilehash: 4faa48a9edb5ea157fde67e4a4f3008864342075
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105639765"
---
# <a name="understand-apache-spark-code-for-u-sql-developers"></a>A U-SQL-fejlesztők Apache Spark kódjának megismerése

Ez a szakasz magas szintű útmutatást nyújt az U-SQL-parancsfájlok Apache Spark való átalakításához.

- Ez [a két nyelv feldolgozási paradigmának összehasonlításával](#understand-the-u-sql-and-spark-language-and-processing-paradigms) kezdődik.
- A következő tippeket nyújtja:
   - [Parancsfájlok átalakítása](#transform-u-sql-scripts) , beleértve az U-SQL [sorhalmaz kifejezéseit](#transform-u-sql-rowset-expressions-and-sql-based-scalar-expressions)
   - [.NET-kód](#transform-net-code)
   - [Adattípusok](#transform-typed-values)
   - [Katalógus objektumai](#transform-u-sql-catalog-objects).

## <a name="understand-the-u-sql-and-spark-language-and-processing-paradigms"></a>Ismerje meg az U-SQL és a Spark nyelvét és a feldolgozási paradigmát

Mielőtt elkezdi áttelepíteni Azure Data Lake Analytics ' U-SQL-szkripteket a Sparkba, hasznos megérteni a két rendszer általános nyelvi és feldolgozási filozófiáit.

Az U-SQL egy olyan SQL-szerű deklaratív lekérdezési nyelv, amely adatforgalmi paradigmát használ, és lehetővé teszi a .NET-ben írt felhasználói kódok egyszerű beágyazását és méretezését (például C#), a Pythont és az R-t. A felhasználók és a bővítmények egyszerű kifejezéseket vagy felhasználó által definiált függvényeket hozhatnak létre, de megadhatják a felhasználónak, hogy az úgynevezett felhasználó által definiált operátorok implementálják az egyéni operátorokat a sorhalmaz szintű átalakítások, a kinyerések és a kimenetek írásakor.

A Spark egy kibővíthető keretrendszer, amely számos nyelvi kötést kínál a Scala, a Java, a Python, a .NET stb. esetében, ahol elsődlegesen a kódot a fenti nyelvek egyikén írja elő, hozzon létre adatabsztrakciókat a rugalmasan elosztott adatkészletek (RDD), a dataframes és az adatkészletek számára, majd használjon egy LINQ-szerű tartományi-specifikus nyelvet (DSL Emellett a dataframe és az adatkészlet absztrakt SparkSQL is biztosít deklaratív alnyelvként. A DSL két működési kategóriát biztosít, átalakításokat és műveleteket. Az adatabsztrakciók átalakításának alkalmazása nem hajtja végre az átalakítást, hanem felépíti azt a végrehajtási tervet, amelyet a rendszer egy művelettel küld el az értékeléshez (például egy ideiglenes táblába vagy fájlba, vagy az eredmény kinyomtatásával).

Így amikor egy U-SQL-szkriptet egy Spark-programba fordít, el kell döntenie, hogy melyik nyelvet szeretné használni legalább az adatkeret absztrakciójának létrehozásához (amely jelenleg a leggyakrabban használt adatabsztrakció), és hogy szeretné-e írni a deklaratív adatfolyam átalakításokat a DSL vagy a SparkSQL használatával. Összetettebb esetekben előfordulhat, hogy az U-SQL-szkriptet a Spark sorozatára kell bontania, és a Azure Batch vagy Azure Functions által megvalósított egyéb lépéseket is.

Emellett Azure Data Lake Analytics a U-SQL-t egy kiszolgáló nélküli munkahelyi szolgáltatási környezetben kínálja, miközben a Azure Databricks és az Azure HDInsight is elérhetővé kell tenni egy fürtszolgáltatást. Az alkalmazás átalakításakor figyelembe kell vennie a fürtök létrehozásának, méretezésének, skálázásának és leszerelésének következményeit is.

## <a name="transform-u-sql-scripts"></a>U-SQL-parancsfájlok átalakítása

Az U-SQL-parancsfájlok a következő feldolgozási mintát követik:

1. Az adatok beolvasása a strukturálatlan fájlokból, az `EXTRACT` utasítás, a hely vagy a fájl készletének specifikációja, valamint a beépített vagy a felhasználó által definiált kivonó és a kívánt séma alapján, illetve U-SQL-táblákból (felügyelt vagy külső táblákból) történik. A rendszer sorhalmazként jelöli meg.
2. A sorhalmaz több olyan U-SQL-utasításban van átalakítva, amely U-SQL-kifejezéseket alkalmaz a sorhalmazokra, és új sorhalmazokat állít elő.
3. Végül az eredményül kapott sorhalmazok egy fájlba kerülnek, `OUTPUT` amely a hely (ek), a beépített vagy a felhasználó által definiált vagy egy U-SQL-tábla helyét határozza meg.

A szkriptet a rendszer lustán értékeli ki, ami azt jelenti, hogy minden egyes kinyerési és átalakítási lépés egy kifejezés fastruktúrába kerül, és globálisan kiértékelt (a adatfolyam).

A Spark-programok hasonlóak ahhoz, hogy a Spark-összekötők beolvassák az adatokat, és létrehozzák a dataframes, majd alkalmazza az átalakításokat a dataframes a LINQ-Like DSL vagy SparkSQL használatával, majd az eredményt a fájlokra, az ideiglenes Spark-táblákra, néhány programozási nyelvi típusra vagy a konzolra írja.

## <a name="transform-net-code"></a>.NET-kód átalakítása

Az U-SQL Expression nyelve C#, és többféle módon méretezhető ki az egyéni .NET-kódok.

Mivel a Spark jelenleg nem támogatja natív módon a .NET-kód végrehajtását, újra kell írnia a kifejezéseket egy egyenértékű Spark, Scala, Java vagy Python kifejezésbe, vagy meg kell keresnie a .NET-kódban való meghívást. Ha a parancsfájl .NET-kódtárakat használ, a következő lehetőségek közül választhat:

- A .NET-kód lefordítása a Scala vagy a Python nyelvre.
- Ossza meg a U-SQL-szkriptet több lépésre, ahol Azure Batch folyamatokat használ a .NET-transzformációk alkalmazásához (ha elfogadható léptéket érhet el)
- A nyílt forráskódú Möbius nevű .NET nyelvi kötések használata. Ez a projekt nem támogatott állapotban van.

Ha az U-SQL-parancsfájlok nagy mennyiségű .NET-logikával rendelkezik, a Microsoft-fiókja képviselőjén keresztül további útmutatásért forduljon hozzánk.

A következő részletek a .NET-és C#-használatoknak az U-SQL-parancsfájlokban való használatának különböző eseteire vonatkoznak.

### <a name="transform-scalar-inline-u-sql-c-expressions"></a>Skaláris beágyazott U-SQL C#-kifejezések átalakítása

A U-SQL kifejezés nyelve C#. A skaláris inline U-SQL kifejezések nagy része a jobb teljesítmény érdekében natív módon valósul meg, míg a .NET-keretrendszer hívásával összetettebb kifejezéseket is végrehajthat.

A Spark saját skaláris kifejezési nyelvvel rendelkezik (vagy a DSL vagy a SparkSQL részeként), és lehetővé teszi, hogy a felhasználó által definiált függvényeknek való meghívása az üzemeltetési nyelven történjen.

Ha a U-SQL-ben skaláris kifejezések szerepelnek, először a legjobban ismert Spark skaláris kifejezést kell megtalálnia a legtöbb teljesítmény eléréséhez, majd le kell képeznie a többi kifejezést a választott Spark-üzemeltetési nyelv felhasználó által definiált függvényében.

Vegye figyelembe, hogy a .NET és a C# különböző típusú szemantikai funkciókat tartalmaz, mint a Spark üzemeltetési nyelvei és a Spark DSL. A rendszereltérések típusával kapcsolatos további részletekért lásd [alább](#transform-typed-values) .

### <a name="transform-user-defined-scalar-net-functions-and-user-defined-aggregators"></a>Felhasználó által definiált skaláris .NET-függvények és felhasználó által definiált aggregátorok átalakítása

Az U-SQL lehetővé teszi tetszőleges skaláris .NET függvények meghívását és a .NET-ben írt, felhasználó által definiált aggregatorek meghívását.

A Spark támogatja a felhasználó által definiált függvényeket és a felhasználó által definiált olyan gyűjtőket is, amelyeket a Spark DSL-és SparkSQL hívhat a legtöbb üzemeltetési nyelvén.

### <a name="transform-user-defined-operators-udos"></a>Felhasználó által definiált operátorok átalakítása (Udo)

Az U-SQL számos felhasználó által definiált operátort (Udo) kínál, mint például a kinyerő, a kiállítók, a szűkítők, a processzorok, a beszállítók és a .NET-ben (és – bizonyos mértékben a Pythonban és az R-ben) megírható kombinációk.

A Spark nem nyújt azonos bővíthetőségi modellt a kezelők számára, de bizonyos funkciókkal egyenértékű képességekkel rendelkezik.

A kivonók és a lekéréses eszközök Spark-összekötői. Sok U-SQL-kinyerő esetében egy egyenértékű összekötő található a Spark-Közösségben. Mások számára egyéni összekötőt kell írnia. Ha az U-SQL-kivonó összetett, és több .NET-kódtárat használ, érdemes lehet olyan összekötőt létrehozni a Scalaben, amely együttműködési módon hívja meg a .NET-függvénytárat, amely az adatok tényleges feldolgozását végzi. Ebben az esetben telepítenie kell a .NET Core-futtatókörnyezetet a Spark-fürtre, és győződjön meg arról, hogy a hivatkozott .NET-kódtárak a .NET Standard 2,0-kompatibilisek.

A többi U-SQL-Udo újra kell írni a felhasználó által definiált függvények és aggregátorok, valamint a szemantikailag megfelelő Spark DLS vagy SparkSQL kifejezés használatával. Egy processzor például több UDF-hívás közül VÁLASZTHAT, és olyan függvényként csomagolható, amely argumentumként dataframe, és egy dataframe ad vissza.

### <a name="transform-u-sqls-optional-libraries"></a>A U-SQL választható könyvtárainak átalakítása

Az U-SQL számos opcionális és bemutató kódtárat kínál, amelyek [Python](data-lake-analytics-u-sql-python-extensions.md)-, [R](data-lake-analytics-u-sql-r-extensions.md)-, [JSON-, XML-, AVRO-támogatást](https://github.com/Azure/usql/tree/master/Examples/DataFormats)és néhány [kognitív szolgáltatást](data-lake-analytics-u-sql-cognitive.md)biztosítanak.

A Spark saját Python-és R-integrációt, pySpark és Sparker-t kínál, valamint összekötőket biztosít a JSON, az XML és a AVRO olvasásához és írásához.

Ha át kell alakítania egy olyan parancsfájlt, amely a kognitív szolgáltatások könyvtáraira hivatkozik, javasoljuk, hogy a Microsoft-fiókja képviselőjén keresztül lépjen kapcsolatba velünk.

## <a name="transform-typed-values"></a>Beírt értékek átalakítása

Mivel az U-SQL típusa rendszer a .NET Type rendszeren alapul, és a Spark saját típusú rendszert használ, amelyet a gazdagép nyelvi kötése érint, meg kell győződnie arról, hogy a használt típusok lezárulnak, és bizonyos típusok esetén a típus tartománya, a pontosság és/vagy a skála némileg eltérő lehet. Emellett a U-SQL és a Spark `null` eltérő értékeket is kezel.

### <a name="data-types"></a>Adattípusok

A következő táblázat a Spark, a Scala és a PySpark egyenértékű típusokat tartalmazza a megadott U-SQL-típusokhoz.

| U-SQL | Spark |  Scala | PySpark |
| ------ | ------ | ------ | ------ |
|`byte`       ||||
|`sbyte`      |`ByteType` |`Byte` | `ByteType`|
|`int`        |`IntegerType` |`Int` | `IntegerType`|
|`uint`       ||||
|`long`       |`LongType` |`Long` | `LongType`|
|`ulong`      ||||
|`float`      |`FloatType` |`Float` | `FloatType`|
|`double`     |`DoubleType` |`Double` | `DoubleType`|
|`decimal`    |`DecimalType` |`java.math.BigDecimal` | `DecimalType`|
|`short`      |`ShortType` |`Short` | `ShortType`|
|`ushort`     ||||
|`char`   | |`Char`||
|`string` |`StringType` |`String` |`StringType` |
|`DateTime`   |`DateType`, `TimestampType` |`java.sql.Date`, `java.sql.Timestamp` | `DateType`, `TimestampType`|
|`bool`   |`BooleanType` |`Boolean` | `BooleanType`|
|`Guid`   ||||
|`byte[]` |`BinaryType` |`Array[Byte]` | `BinaryType`|
|`SQL.MAP<K,V>`   |`MapType(keyType, valueType, valueContainsNull)` |`scala.collection.Map` | `MapType(keyType, valueType, valueContainsNull=True)`|
|`SQL.ARRAY<T>`   |`ArrayType(elementType, containsNull)` |`scala.collection.Seq` | `ArrayType(elementType, containsNull=True)`|

További információkért lásd:

- [org. Apache. Spark. SQL. types](https://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.sql.types.package)
- [Spark SQL-és DataFrames-típusok](https://spark.apache.org/docs/latest/sql-ref-datatypes.html)
- [Scala-értékek típusai](https://www.scala-lang.org/api/current/scala/AnyVal.html)
- [pyspark. SQL. types](https://spark.apache.org/docs/2.3.1/api/python/_modules/pyspark/sql/types.html#module-pyspark.sql.types)

### <a name="treatment-of-null"></a>NULL kezelés

A Spark-ban alapértelmezés szerint a types (NULL) értékek engedélyezése a U-SQL-ben Míg a Spark lehetővé teszi, hogy egy oszlop nem null értékűként legyen meghatározva, nem kényszeríti ki a korlátozást, és [helytelen eredményt](https://medium.com/@weshoffman/apache-spark-parquet-and-troublesome-nulls-28712b06f836)eredményezhet.

A Sparkban NULL azt jelzi, hogy az érték ismeretlen. A Spark NULL érték eltér bármely értéktől, beleértve a saját magát is. A két Spark NULL érték vagy egy NULL érték és bármely más érték közötti összehasonlítás ismeretlen, mert az egyes NULL értékek értéke ismeretlen.  

Ez a viselkedés különbözik a U-SQL-től, amely a C# szemantikai sorrendjét követi, amely `null` eltér az összes értéktől, de önmagával egyenlő.  

Így a `SELECT` által használt SparkSQL-utasítás `WHERE column_name = NULL` nulla sorokat ad vissza, még akkor is, ha a-ben Null érték van `column_name` , míg az U-SQL-ben, akkor az a sor, amelyben a érték van `column_name` beállítva `null` . Hasonlóképpen, A használt Spark- `SELECT` utasítás `WHERE column_name != NULL` nulla sorokat ad vissza, akkor is, ha nem null érték szerepel a rendszerben `column_name` , míg a U-SQL esetében a nem null értékű sorokat adja vissza. Így ha azt szeretné, hogy a U-SQL null-ellenőrzési szemantikaa legyen, használja a [IsNull](https://spark.apache.org/docs/2.3.0/api/sql/index.html#isnull) és a [isnotnull](https://spark.apache.org/docs/2.3.0/api/sql/index.html#isnotnull) (vagy a DSL-egyenértékét).

## <a name="transform-u-sql-catalog-objects"></a>U-SQL-katalógus objektumainak átalakítása

Az egyik legnagyobb különbség az, hogy a U-SQL-parancsfájlok használhatják a katalógus-objektumaikat, amelyek közül sok nem rendelkezik közvetlen Spark-jogosultsággal.

A Spark támogatást nyújt a kaptár meta Store-fogalmakhoz, elsősorban adatbázisok és táblák, így az U-SQL-adatbázisok és-sémák kiképezhetők a kaptár-adatbázisokba és U-SQL-táblákat a Spark-táblákba (lásd: [u-SQL-táblákban tárolt adatok áthelyezése](understand-spark-data-formats.md#move-data-stored-in-u-sql-tables)), de nem támogatja a nézetek, a táblázat értékű függvények (TVFs), tárolt eljárások, U-SQL-szerelvények, külső adatforrások

Az U-SQL-kód objektumai, például a nézetek, a TVFs, a tárolt eljárások és a szerelvények a Sparkban, a gazdagép nyelvének függvényében és eljárási absztrakciós mechanizmusaiban (például a Python-modulok importálásán vagy a Scala functions-ben hivatkozva) is megadhatók.

Ha a U-SQL-katalógus segítségével megoszthatja az adatkezelési és a kód objektumokat a projektek és a csapatok között, akkor a megosztással egyenértékű mechanizmusokat kell használni (például a Maven megosztását a kód objektumai esetében).

## <a name="transform-u-sql-rowset-expressions-and-sql-based-scalar-expressions"></a>U-SQL sorhalmaz-kifejezések és SQL-alapú skaláris kifejezések átalakítása

Az U-SQL alapvető nyelve a sorhalmazok átalakítása, és az SQL-alapú. Az alábbi lista az U-SQL-ben kínált leggyakoribb sorhalmaz-kifejezések nem kimerítő listáját tartalmazza:

- `SELECT`/`FROM`/`WHERE`/`GROUP BY`+ Összesítések +`HAVING`/`ORDER BY`+`FETCH`
- `INNER`/`OUTER`/`CROSS`/`SEMI``JOIN`kifejezések
- `CROSS`/`OUTER``APPLY`kifejezések
- `PIVOT`/`UNPIVOT` kifejezések
- `VALUES` sorhalmaz konstruktora

- Kifejezések beállítása `UNION`/`OUTER UNION`/`INTERSECT`/`EXCEPT`

Emellett az U-SQL számos SQL-alapú skaláris kifejezést is biztosít, például:

- `OVER` ablakos kifejezések
- számos beépített aggregator és rangsorolási függvény ( `SUM` `FIRST` stb.)
- A legismertebb SQL skaláris kifejezések: `CASE` , `LIKE` , ( `NOT` ) `IN` , `AND` `OR` stb.

A Spark a legtöbb ilyen kifejezéshez egyenértékű kifejezéseket biztosít a DSL-és a SparkSQL-űrlapon. A Sparkban natív módon nem támogatott kifejezések némelyikét a natív Spark-kifejezések és a szemantikailag egyenértékű minták kombinációjával újra kell írni. Például `OUTER UNION` le kell fordítani a kivetítések és a szakszervezetek megfelelő kombinációjára.

A NULL értékek eltérő kezelésének köszönhetően a U-SQL-illesztések mindig egyeznek egy sorral, ha az összehasonlított oszlopok mindegyike NULL értéket tartalmaz, míg a Sparkban lévő illesztés nem felel meg az ilyen oszlopoknak, kivéve, ha explicit null ellenőrzéseket adnak hozzá.

## <a name="transform-other-u-sql-concepts"></a>Más U-SQL-fogalmak átalakítása

Az U-SQL számos más funkciót és fogalmat is kínál, például az összevont lekérdezéseket SQL Server adatbázisokra, paraméterekre, skaláris kifejezésekre, valamint a lambda kifejezések változóit, a rendszerváltozókat és a `OPTION` tippeket.

### <a name="federated-queries-against-sql-server-databasesexternal-tables"></a>Összevont lekérdezések SQL Server adatbázisok/külső táblák között

Az U-SQL adatforrást és külső táblákat is biztosít, valamint közvetlen lekérdezéseket Azure SQL Database. Míg a Spark nem nyújt ugyanazokat az objektum-absztrakciókat, a [Spark-összekötőt biztosít az](../azure-sql/database/spark-connector.md) SQL-adatbázisok lekérdezéséhez használható Azure SQL Databasehoz.

### <a name="u-sql-parameters-and-variables"></a>U-SQL paraméterek és változók

A paraméterek és a felhasználói változók egyenrangú fogalmakkal rendelkeznek a Sparkban és azok üzemeltetési nyelvein.

Például a Scalaban megadhat egy változót a `var` következő kulcsszóval:

```
var x = 2 * 3;
println(x)
```

A U-SQL rendszerváltozói (a-től kezdődő változók `@@` ) két kategóriába oszthatók:

- Beállítható rendszerváltozók, amelyek meghatározott értékekre állíthatók a parancsfájlok viselkedésének hatására.
- Információs rendszerváltozók, amelyek a rendszer-és a feladat-szintű információkat kérdezik le

A beállítható rendszerváltozók többsége nem rendelkezik közvetlen egyenértékűsel a Sparkban. Az információs rendszerváltozók némelyike modellezhető az információk argumentumként való átadásával a feladat végrehajtása során, mások pedig egyenértékű funkcióval rendelkezhetnek a Spark üzemeltetési nyelvén.

### <a name="u-sql-hints"></a>U-SQL-útmutatók

Az U-SQL számos szintaktikai módszert kínál a lekérdezés-optimalizáló és a végrehajtó motor számára:  

- U-SQL rendszerváltozó beállítása
- `OPTION`a sorhalmaz-kifejezéshez tartozó záradék, amely egy adatkészletet vagy egy tippet biztosít
- illesztési mutató a JOIN kifejezés szintaxisában (például `BROADCASTLEFT` )

A Spark költséghatékony lekérdezés-optimalizáló funkciója lehetővé teszi a javaslatok és a lekérdezési teljesítmény finomhangolását. Tekintse meg a vonatkozó dokumentációt.

## <a name="next-steps"></a>Következő lépések

- [A Spark-adatformátumok megismerése U-SQL-fejlesztőknek](understand-spark-data-formats.md)
- [.NET az Apache Sparkhoz](/dotnet/spark/what-is-apache-spark-dotnet)
- [Frissítse big data Analytics-megoldásait Azure Data Lake Storage Gen1ról Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-migrate-gen1-to-gen2.md)
- [Adatátalakítás a Spark-tevékenységgel Azure Data Factory](../data-factory/transform-data-using-spark.md)
- [Az adatátalakítás a Hadoop-struktúra tevékenységgel Azure Data Factory](../data-factory/transform-data-using-hadoop-hive.md)
- [Mi az az Azure HDInsight-alapú Apache Spark?](../hdinsight/spark/apache-spark-overview.md)
