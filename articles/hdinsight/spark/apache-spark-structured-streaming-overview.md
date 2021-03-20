---
title: Spark strukturált streaming az Azure HDInsight
description: Spark strukturált adatfolyam-alkalmazások használata a HDInsight Spark-fürtökön.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/24/2019
ms.openlocfilehash: 9f92007c271da5b6d2cb8db6c3904a62b114e7c2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98929492"
---
# <a name="overview-of-apache-spark-structured-streaming"></a>Strukturált streaming Apache Spark áttekintése

[Apache Spark](https://spark.apache.org/) A strukturált streaming lehetővé teszi méretezhető, nagy átviteli sebességű, hibatűrő alkalmazások megvalósítását az adatfolyamok feldolgozásához. A strukturált adatfolyam a Spark SQL-motorra épül, és a Spark SQL-adatkeretek és-adatkészletek szerkezeteit fejleszti, így ugyanúgy írhat adatfolyam-lekérdezéseket, mint például a Batch-lekérdezések írására.  

A strukturált adatfolyam-alkalmazások HDInsight Spark-fürtökön futnak, és a [Apache Kafka](https://kafka.apache.org/), a TCP-szoftvercsatorna (hibakeresési célú), az Azure Storage vagy a Azure Data Lake Storage adatátviteli szolgáltatásához kapcsolódnak. Az utóbbi két lehetőség, amely a külső tárolási szolgáltatásokra támaszkodik, lehetővé teszi a tároláshoz hozzáadott új fájlok megtekintését, valamint a tartalom feldolgozását úgy, mintha az adatfolyamot továbbították volna.

A strukturált adatfolyam egy hosszú ideig futó lekérdezést hoz létre, amely során a rendszer a bemeneti adatokra alkalmazza a műveleteket, például a kijelölést, a kivetítést, az összesítést, az ablakot, és összekapcsolja a streaming DataFrame a Reference DataFrames. Ezután az eredményeket a file Storage (Azure Storage Blobs vagy Data Lake Storage) vagy bármely adattár számára egyéni kód (például SQL Database vagy Power BI) használatával kell kiadnia. A strukturált streaming emellett kimenetet biztosít a konzol számára a helyi hibakereséshez és a memóriában tárolt táblákhoz, így a hibakereséshez generált adatokat láthatja a HDInsight-ben.

![Stream feldolgozása a HDInsight és a Spark strukturált streaming segítségével](./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming.png)

> [!NOTE]  
> A Spark strukturált streaming a Spark streaminget (DStreams) váltja fel. A folyamatos átvitel érdekében a strukturált streaming a fejlesztéseket és a karbantartást is megkapja, míg a DStreams csak karbantartási módba kerül. A strukturált adatfolyam jelenleg nem a DStreams, hanem az általa támogatott források és nyelők számára, ezért a követelmények kiértékelésével kiválaszthatja a megfelelő Spark stream-feldolgozási lehetőséget.

## <a name="streams-as-tables"></a>Adatfolyamok táblázatként

A Spark strukturált streaming olyan adatstreamet jelöl, amely nem megfelelő mélységű, azaz a tábla folyamatosan növekszik, ahogy az új adat érkezik. Ezt a *bemeneti táblázatot* egy hosszan futó lekérdezés folyamatosan dolgozza fel, és egy *kimeneti táblába* küldi az eredményeket:

![Strukturált adatfolyam-koncepció](./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming-concept.png)

A strukturált adatfolyamban az adatok beérkeznek a rendszerbe, és azonnal bekerülnek egy bemeneti táblába. Olyan lekérdezéseket írhat (a DataFrame és az adatkészlet API-k használatával), amelyek a bemeneti táblán műveleteket hajtanak végre. A lekérdezés kimenete egy másik táblát, az *eredmények táblát* eredményezi. A Results (eredmények) tábla a lekérdezés eredményét tartalmazza, amelyből egy külső adattárra, például egy, a kapcsolódó adatbázisra vonatkozó adatok rajzolása végezhető el. Az adatok bemeneti táblából való feldolgozásának időzítését az *aktiválási intervallum* vezérli. Alapértelmezés szerint az trigger intervalluma nulla, így a strukturált adatfolyam a megérkezése után azonnal feldolgozza az adatfeldolgozást. A gyakorlatban ez azt jelenti, hogy amint a strukturált adatfolyam feldolgozása befejeződött az előző lekérdezés futtatásával, egy másik feldolgozást indít el az újonnan fogadott összes adattal. Az indítást beállíthatja úgy, hogy intervallumban fusson, hogy a folyamatos átviteli adatkötegek feldolgozása időalapú kötegekben történjen.

Az eredmények táblázatában szereplő adatokat csak a lekérdezés legutóbbi feldolgozása óta új adatokat (*hozzáfűzési mód*) tartalmazhatják, vagy ha a tábla minden új adattal frissül, így a tábla tartalmazza az összes kimeneti adatokat, mivel az adatfolyam-lekérdezés megkezdődött (*teljes mód*).

### <a name="append-mode"></a>Hozzáfűzési mód

A hozzáfűzési módban csak az eredményeket tartalmazó táblázatba felvett sorok jelennek meg az eredmények táblában, és a külső tárolóba íródnak. A legegyszerűbb lekérdezés például csak az összes adatot másolja át a bemeneti táblából az eredmények táblába változatlanul. Minden alkalommal, amikor az aktiválási időköz eltelik, az új adatfeldolgozás történik, és az új adatmennyiséget jelképező sorok megjelennek az eredmények táblázatban.

Vegyünk például egy olyan forgatókönyvet, amelyben telemetria dolgoz fel a hőmérséklet-érzékelőkből, például egy termosztátból. Tegyük fel, hogy az első eseményindító feldolgozott egy eseményt az 1. számú, 95 fokos hőmérséklet-olvasási értékű 00:01-es időpontban. A lekérdezés első triggerében csak a 00:01 időponttal rendelkező sor jelenik meg az eredmények táblázatban. Ha egy másik esemény érkezésekor 00:02, az egyetlen új sor az a sor, amely a 00:02-as időpontot jelöli, így az eredmények tábla csak ezt az egy sort fogja tartalmazni.

![Strukturált adatfolyam-hozzáfűzési mód](./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming-append-mode.png)

Hozzáfűzési mód használatakor a lekérdezés kivetítéseket alkalmaz (kijelöli a hozzá tartozó oszlopokat), a szűrést (csak bizonyos feltételeknek megfelelő sorok kiválasztását) vagy a csatlakozást (az adatok kibővítését egy statikus keresési táblázatból származó adatokkal). A hozzáfűzési mód lehetővé teszi, hogy csak a megfelelő új adatokat küldje el a külső tárterületre.

### <a name="complete-mode"></a>Teljes mód

Vegye figyelembe ugyanezt a forgatókönyvet, ezúttal a teljes üzemmódot használva. Teljes módban a teljes kimeneti tábla frissül minden triggeren, így a tábla nem csupán a legutóbbi trigger-futtatásból, hanem minden futtatásból is tartalmaz adatokat. A teljes módot használhatja az adatoknak a bemeneti táblából a Results táblába való másolásának megváltoztatásához. Minden aktivált futtatáskor az új eredmény sorok az összes korábbi sorral együtt jelennek meg. A kimeneti eredmények táblázata végül a lekérdezés kezdete óta gyűjtött összes összegyűjtött adatokat tárolja, és végül elfogyott a memória. A teljes üzemmód olyan összesített lekérdezésekkel használható, amelyek valamilyen módon összegzik a bejövő adatokat, így minden triggernél új összegzéssel frissülnek az eredmények táblázata.

Tegyük fel, hogy eddig öt másodperc van a már feldolgozott adatmennyiség, és ideje feldolgozni az adatot a hatodik másodpercben. A bemeneti tábla a 00:01-as és a 00:03-os időponthoz tartozó eseményeket tartalmazza. A példában szereplő lekérdezés célja, hogy öt másodpercenként adja meg az eszköz átlagos hőmérsékletét. A lekérdezés megvalósítása olyan összesítést alkalmaz, amely az egyes 5 másodperces ablakban lévő összes értéket végrehajtja, a hőmérsékletet, és egy sort hoz létre az adott intervallumban mért átlagos hőmérséklethez képest. Az első 5 másodperces ablak végén két rekordok létezik: (00:01, 1, 95) és (00:03, 1, 98). Tehát a 00:00-00:05-es ablak esetében az Összesítés egy olyan rekordot állít elő, amely a 96,5 fok átlagos hőmérsékletét eredményezi. A következő 5 másodperces ablakban csak egy adatpont van a 00:06-es időpontban, így az eredményül kapott átlagos hőmérséklet 98 fok. Az 00:10-es időpontban a teljes mód használata esetén a Results tábla a Windows 00:00-00:05 és a 00:05-00:10 sorait is tartalmazza, mivel a lekérdezés az összes összesített sort megjeleníti, nem csak az újakat. Ezért az eredmények táblázata továbbra is nő, ahogy az új ablakok hozzáadódnak.

![Strukturált folyamatos átvitel teljes módja](./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming-complete-mode.png)

Nem minden teljes üzemmódot használó lekérdezés fogja eredményezni, hogy a tábla határok nélkül nő.  Vegye figyelembe az előző példában, hogy a hőmérsékletet nem a Time-ablak átlaga alapján, hanem az eszköz azonosítója alapján átlagosan. Az eredmény tábla rögzített számú sort tartalmaz (egy eszközönként), az eszköz átlagos hőmérsékletét az eszköztől kapott összes adatpontra vonatkozóan. Az új hőmérsékletek fogadásakor a rendszer frissíti az eredményeket tartalmazó táblázatot, hogy a táblázatban szereplő átlagok mindig naprakészek legyenek.

## <a name="components-of-a-spark-structured-streaming-application"></a>A Spark strukturált adatfolyam-alkalmazás összetevői

Egy egyszerű példa lekérdezés a hőmérséklet-beolvasásokat óránkénti Windows-alapú összesítéssel összegzi. Ebben az esetben a rendszer az Azure Storage-ban található JSON-fájlokban tárolja az adatfájlokat (a HDInsight-fürt alapértelmezett tárolóként van csatolva):

```json
{"time":1469501107,"temp":"95"}
{"time":1469501147,"temp":"95"}
{"time":1469501202,"temp":"95"}
{"time":1469501219,"temp":"95"}
{"time":1469501225,"temp":"95"}
```

Ezeket a JSON-fájlokat a `temps` HDInsight-fürt tárolója alatti almappában tárolja a rendszer.

### <a name="define-the-input-source"></a>A bemeneti forrás megadása

Először állítson be egy DataFrame, amely leírja az adatforrást, valamint a forrás által igényelt beállításokat. Ez a példa az Azure Storage-ban található JSON-fájlokból származik, és egy sémát alkalmaz rájuk olvasási időpontban.

```sql
import org.apache.spark.sql.types._
import org.apache.spark.sql.functions._

//Cluster-local path to the folder containing the JSON files
val inputPath = "/temps/" 

//Define the schema of the JSON files as having the "time" of type TimeStamp and the "temp" field of type String
val jsonSchema = new StructType().add("time", TimestampType).add("temp", StringType)

//Create a Streaming DataFrame by calling readStream and configuring it with the schema and path
val streamingInputDF = spark.readStream.schema(jsonSchema).json(inputPath)
``` 

#### <a name="apply-the-query"></a>A lekérdezés alkalmazása

Ezután alkalmazzon egy olyan lekérdezést, amely a kívánt műveleteket tartalmazza a folyamatos átviteli DataFrame. Ebben az esetben egy összesítés csoportosítja az összes sort az 1 órás Windowsba, majd kiszámítja az 1 órás időszak minimális, átlagos és maximális hőmérsékletét.

```sql
val streamingAggDF = streamingInputDF.groupBy(window($"time", "1 hour")).agg(min($"temp"), avg($"temp"), max($"temp"))
```

### <a name="define-the-output-sink"></a>A kimeneti fogadó megadása

Ezután adja meg az egyes triggerek intervallumában az eredmények táblához hozzáadott sorok célját. Ebben a példában az összes sort egy memóriában lévő táblába `temps` helyezi, amelyet később a SparkSQL-sel lehet lekérdezni. A teljes kimeneti mód biztosítja, hogy minden Windows-sor minden alkalommal kimenetet végezzen.

```sql
val streamingOutDF = streamingAggDF.writeStream.format("memory").queryName("temps").outputMode("complete")
``` 

### <a name="start-the-query"></a>A lekérdezés elindítása

Indítsa el az adatfolyam-lekérdezést, és futtassa a parancsot, amíg meg nem érkezik a megszakítási jel.

```sql
val query = streamingOutDF.start() 
``` 

### <a name="view-the-results"></a>Az eredmények megtekintése

Amíg a lekérdezés fut, ugyanabban a SparkSession futtathat egy SparkSQL-lekérdezést azon a `temps` táblán, amelyen a lekérdezési eredményeket tárolják.

```sql
select * from temps
```

A lekérdezés a következőhöz hasonló eredményeket eredményez:

| ablak |  minimális (Temp) | átlag (Temp) | Max (Temp) |
| --- | --- | --- | --- |
|{u'start ': u ' 2016-07-26T02:00:00.000 Z ', u'end '... |    95 |    95,231579 | 99 |
|{u'start ': u ' 2016-07-26T03:00:00.000 Z ', u'end '...  |95 |   96,023048 | 99 |
|{u'start ': u ' 2016-07-26T04:00:00.000 Z ', u'end '...  |95 |   96,797133 | 99 |
|{u'start ': u ' 2016-07-26T05:00:00.000 Z ', u'end '...  |95 |   96,984639 | 99 |
|{u'start ': u ' 2016-07-26T06:00:00.000 Z ', u'end '...  |95 |   97,014749 | 99 |
|{u'start ': u ' 2016-07-26T07:00:00.000 Z ', u'end '...  |95 |   96,980971 | 99 |
|{u'start ': u ' 2016-07-26T08:00:00.000 Z ', u'end '...  |95 |   96,965997 | 99 |  

A Spark strukturált stream API-val, valamint az általa támogatott bemeneti adatforrásokkal, műveletekkel és kimeneti tárolókkal kapcsolatos részletekért lásd: [Apache Spark strukturált streaming programozási útmutató](https://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html).

## <a name="checkpointing-and-write-ahead-logs"></a>Ellenőrzőpontok és írási naplók

A rugalmasság és a hibatűrés biztosításához a strukturált adatfolyam-ellenőrzés az *ellenőrzőponton* alapul, hogy a stream feldolgozása továbbra is zavartalan maradjon, még a csomópont meghibásodása esetén is. A HDInsight-ben a Spark az Azure Storage vagy a Data Lake Storage használatával hoz létre ellenőrzőpontokat a tartós tároláshoz. Ezek az ellenőrzőpontok az adatfolyam-lekérdezés előrehaladási adatait tárolják. A strukturált adatfolyam továbbá egy *írási idejű naplót* (Wal) használ. A WAL rögzíti a beérkezett, de a lekérdezés által még nem feldolgozott adatot. Ha hiba történik, és a rendszer újraindította a feldolgozást a WAL-ből, a forrástól kapott események nem vesznek el.

## <a name="deploying-spark-streaming-applications"></a>Spark streaming-alkalmazások üzembe helyezése

Általában egy Spark streaming-alkalmazást készít egy JAR-fájlba, majd üzembe helyezheti a Spark on HDInsight. ehhez másolja a JAR-fájlt a HDInsight-fürthöz csatolt alapértelmezett tárolóba. Az alkalmazást a fürtön elérhető [Apache Livy](https://livy.incubator.apache.org/) REST API-k használatával kezdheti meg a post művelettel. A POST törzse tartalmaz egy JSON-dokumentumot, amely megadja a JAR elérési útját, annak az osztálynak a nevét, amelynek a fő metódusa definiálja és futtatja a folyamatos átviteli alkalmazást, valamint opcionálisan a feladathoz tartozó erőforrás-követelményeket (például a végrehajtók, a memória és a magok számát), valamint az alkalmazás kódjához szükséges konfigurációs beállításokat.

![Spark streaming-alkalmazás üzembe helyezése](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-livy.png)

Az összes alkalmazás állapota a GET kérelemmel is ellenőrizhető egy LIVY-végponton. Végül a LIVY-végpontra vonatkozó TÖRLÉSi kérelem kibocsátásával leállíthatja a futó alkalmazást. A LIVY API-val kapcsolatos részletekért lásd: [távoli feladatok Apache LIVY](apache-spark-livy-rest-interface.md)

## <a name="next-steps"></a>Következő lépések

* [Apache Spark-fürt létrehozása a HDInsight-ben](../hdinsight-hadoop-create-linux-clusters-portal.md)
* [Apache Spark strukturált streaming programozási útmutató](https://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html)
* [Apache Spark feladatok távoli elindítása az Apache LIVY](apache-spark-livy-rest-interface.md)
