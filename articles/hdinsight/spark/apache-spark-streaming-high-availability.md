---
title: Magasan elérhető Spark streaming-feladatok a FONALban – Azure HDInsight
description: Apache Spark streaming beállítása magas rendelkezésre állású forgatókönyvhöz az Azure HDInsight
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/29/2019
ms.openlocfilehash: 602fa1cab71a797dd25aca263e0c6a9f2aa616bb
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104870228"
---
# <a name="create-high-availability-apache-spark-streaming-jobs-with-yarn"></a>Magas rendelkezésre állású Apache Spark folyamatos átviteli feladatok létrehozása a FONALral

[Apache Spark](https://spark.apache.org/) A streaming lehetővé teszi, hogy méretezhető, nagy átviteli sebességű és hibatűrő alkalmazásokat implementáljon az adatfolyamok feldolgozásához. A Spark streaming-alkalmazásokat HDInsight Spark-fürtökön különböző típusú adatforrásokhoz, például az Azure Event Hubs, az Azure IoT Hub, az [Apache Kafka](https://kafka.apache.org/), az [Apache Flume](https://flume.apache.org/), a Twitterhez, a [ZeroMQ](http://zeromq.org/), a RAW TCP-aljzatokhoz, vagy a [Apache Hadoop HDFS](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html) -fájlrendszer módosításának figyelésével is összekapcsolhatja. A Spark streaming támogatja a hibatűrést azzal a garanciával, hogy minden adott eseményt pontosan egyszer dolgoz fel, még csomópont meghibásodása esetén is.

A Spark streaming olyan hosszan futó feladatokat hoz létre, amelyekben az adatok átalakítását el tudja végezni, majd az eredményeket elküldheti a fájlrendszer, az adatbázisok, az irányítópultok és a konzol számára. A Spark streaming az adatok mikro-kötegeit dolgozza fel, és először egy adott időintervallumban gyűjti össze az események kötegét. Ezt követően a köteg feldolgozásra és kimenetre lesz küldve. A Batch-időintervallumok általában egy másodperc töredékében vannak meghatározva.

:::image type="content" source="./media/apache-spark-streaming-high-availability/apache-spark-streaming.png" alt-text="Spark streaming" border="false":::

## <a name="dstreams"></a>DStreams

A Spark streaming az *diszkretizált stream* (DStream) használatával folyamatos adatfolyamot jelent. Ez a DStream olyan bemeneti forrásokból is létrehozható, mint a Event Hubs vagy a Kafka, vagy egy másik DStream átalakítások alkalmazásával. Ha egy esemény érkezik a Spark streaming-alkalmazásba, az eseményt megbízható módon tárolja a rendszer. Ez azt is megtörténik, hogy a rendszer replikálja az eseményre vonatkozó adatmennyiséget, így több csomópontnak is van egy másolata. Ez biztosítja, hogy egyetlen csomópont meghibásodása ne okozza az esemény elvesztését.

A Spark Core *rugalmasan elosztott adatkészleteket* (RDD-ket) használ. A RDD a fürt több csomópontja között osztja el az adatelosztást, ahol az egyes csomópontok általában a legjobb teljesítmény érdekében teljes mértékben megőrzik az adataikat a memóriában. Mindegyik RDD a Batch-intervallumban összegyűjtött eseményeket jelöli. Ha a Batch-intervallum eltelik, a Spark streaming egy új RDD hoz létre, amely az adott intervallumban lévő összes adathalmazt tartalmazza. Ezt a folyamatos RDD-készletet egy DStream gyűjti. A Spark streaming-alkalmazások feldolgozzák az egyes kötegek RDD tárolt adategységeket.

:::image type="content" source="./media/apache-spark-streaming-high-availability/apache-spark-dstream.png" alt-text="Spark DStream" border="false":::

## <a name="spark-structured-streaming-jobs"></a>Spark strukturált streaming-feladatok

A Spark strukturált adatfolyamot a Spark 2,0-ben vezették be analitikus motorként, amely strukturált adatátvitelt használ. A Spark strukturált streaming a SparkSQL batching Engine API-kat használja. A Spark streaminghez hasonlóan a Spark strukturált streaming a folyamatosan érkező, a mikro-kötegekben tárolt adatokra vonatkozó számításokat is futtatja. A Spark Structured streaming az adatok streamjét egy korlátlan számú bemeneti táblázatként jelöli. Vagyis a bemeneti tábla folyamatosan növekszik, ahogy új adatok érkeznek. Ezt a bemeneti táblázatot egy hosszan futó lekérdezés folyamatosan dolgozza fel, és az eredményeket egy kimeneti táblába írja a rendszer.

:::image type="content" source="./media/apache-spark-streaming-high-availability/structured-streaming.png" alt-text="Spark strukturált streaming" border="false":::

A strukturált adatfolyamban az adatok beérkeznek a rendszerbe, és azonnal bekerülnek a bemeneti táblába. A bemeneti táblán műveleteket végrehajtó lekérdezések írhatók. A lekérdezés kimenete egy másik, a Results (eredmények) tábla nevű táblát eredményez. A Results (eredmények) tábla a lekérdezés eredményét tartalmazza, amelyből megrajzolhatja az adatok küldését egy külső adattárba, például a kapcsolódó adatbázisba. Az *aktiválási intervallum* beállítja az időzítést, hogy mikor dolgozza fel az adatokat a bemeneti táblából. Alapértelmezés szerint a strukturált adatfolyam a megérkezése után azonnal feldolgozza az adatfeldolgozást. Azt is beállíthatja, hogy az triggert a rendszer a hosszú időintervallumon belül futtassa, így a folyamatos átviteli adatkötegekben dolgozza fel az adatfolyam-továbbítási időt. Előfordulhat, hogy az eredmények táblázatában lévő adatokat a rendszer minden alkalommal frissíti, amikor új adatokat tartalmaz, így az az összes kimeneti adatokat magában foglalja az adatfolyam-lekérdezés megkezdése óta (*teljes mód*), vagy csak a lekérdezés legutóbbi feldolgozása óta új adatokat tartalmazza (*hozzáfűzési mód*).

## <a name="create-fault-tolerant-spark-streaming-jobs"></a>Hibatűrő Spark streaming-feladatok létrehozása

Ha a Spark streaming-feladatokhoz egy magasan elérhető környezetet szeretne létrehozni, akkor a helyreállításhoz az egyes feladatokat a meghibásodások esetén. Ezek az önjavító feladatok hibatűrők.

A RDD számos olyan tulajdonsággal rendelkeznek, amelyek támogatják a nagy rendelkezésre állású és hibatűrő Spark streaming-feladatokat:

* A RDD-ben tárolt bemeneti adatok kötegei a DStream automatikusan replikálódnak a memóriába a hibatűrés érdekében.
* A munkavégző meghibásodás miatt elvesztett adatok újraszámítása a különböző munkavégzők replikált bemeneti adataiból lehetséges, feltéve, hogy ezek a feldolgozó csomópontok elérhetők.
* A gyors hibajavítás egy másodpercen belül megtörténhet, mivel a hibák/csellengõ helyreállítása a memóriában történt számításokon keresztül történik.

### <a name="exactly-once-semantics-with-spark-streaming"></a>Pontosan egyszer szemantika a Spark Streamingtel

Ha olyan alkalmazást szeretne létrehozni, amely az egyes eseményeket egyszer (és csak egyszer) dolgozza fel, gondolja át, hogy az összes rendszerpontnál a hiba után újrainduljon a probléma, és hogy miként lehet elkerülni az adatvesztést. Pontosan egyszer a szemantika megköveteli, hogy egyetlen ponton se vesszenek el az adatvesztés, és hogy az üzenetek feldolgozása újrainduljon, függetlenül attól, hogy hol történik a hiba. Lásd: [Spark streaming-feladatok létrehozása pontosan egyszer az események feldolgozásával](apache-spark-streaming-exactly-once.md).

## <a name="spark-streaming-and-apache-hadoop-yarn"></a>Spark streaming és Apache Hadoop fonal

A HDInsight a fürt működését *egy másik erőforrás-tárgyaló* (fonal) koordinálja. A Spark streaming magas rendelkezésre állásának megtervezése magában foglalja a Spark streaming technikáit, valamint a fonal-összetevőket is.  Alább látható egy példa a FONALat használó konfigurációra.

:::image type="content" source="./media/apache-spark-streaming-high-availability/hdi-yarn-architecture.png" alt-text="FONAL architektúrája" border="false":::

A következő szakaszok a konfigurációra vonatkozó tervezési szempontokat ismertetik.

### <a name="plan-for-failures"></a>A hibák megtervezése

A magas rendelkezésre állású szál konfigurációjának létrehozásához meg kell terveznie egy lehetséges végrehajtót vagy illesztőprogram-hibát. Egyes Spark streaming-feladatok olyan adatgaranciára vonatkozó követelményeket is tartalmaznak, amelyeknek további konfigurálásra és beállításra van szükségük. Előfordulhat például, hogy egy streaming-alkalmazás üzleti követelménye nulla adatvesztési garanciát jelent, amely a gazda-vagy HDInsight-fürtön előforduló hiba miatt fordul elő.

Ha egy **végrehajtó** meghibásodik, a rendszer automatikusan újraindítja a feladatokat és a fogadókat, így nincs szükség konfigurációs módosításra.

Ha azonban egy **illesztőprogram** meghibásodik, akkor az összes hozzá tartozó végrehajtó meghibásodik, és az összes fogadott blokk és számítási eredmény elvész. Az illesztőprogram meghibásodása esetén a *DStream ellenőrzőpontok* használata a [Spark streaming-feladatok létrehozása pontosan egyszeri esemény feldolgozásával](apache-spark-streaming-exactly-once.md#use-checkpoints-for-drivers)című témakörben leírtak szerint. A DStream ellenőrzőpontok rendszeres időközönként elmentik a DStreams *irányított aciklikus gráfját* (Dag) a hibatűrő tárolóba, például az Azure Storage-ba.  Az ellenőrzőpontok lehetővé teszik a Spark Structured streaming számára a sikertelen illesztőprogram újraindítását az ellenőrzőpont információi alapján.  Ez az illesztőprogram-újraindítás elindítja az új végrehajtókat, és újraindítja a fogadókat is.

Az DStream ellenőrzőpontokkal rendelkező illesztőprogramok helyreállítása:

* Konfigurálja az automatikus illesztőprogram-újraindítást a SZÁLon a konfigurációs beállítással `yarn.resourcemanager.am.max-attempts` .
* Egy HDFS-kompatibilis fájlrendszerben állítson be ellenőrzőpont-könyvtárat `streamingContext.checkpoint(hdfsDirectory)` .
* A forráskód újrastrukturálása a helyreállításhoz szükséges ellenőrzőpontok használatához, például:

    ```scala
        def creatingFunc() : StreamingContext = {
            val context = new StreamingContext(...)
            val lines = KafkaUtils.createStream(...)
            val words = lines.flatMap(...)
            ...
            context.checkpoint(hdfsDir)
        }

        val context = StreamingContext.getOrCreate(hdfsDir, creatingFunc)
        context.start()
    ```

* Állítsa be az elveszett adat-helyreállítást úgy, hogy engedélyezi a Write-Ahead naplót (WAL) `sparkConf.set("spark.streaming.receiver.writeAheadLog.enable","true")` és letiltja a memórián belüli replikációt a bemeneti DStreams `StorageLevel.MEMORY_AND_DISK_SER` .

Ha az ellenőrzőpontok és a WAL + megbízható fogadók használatával szeretne összefoglalni, akkor a "legalább egyszer" adathelyreállítást tud biztosítani:

* Pontosan egyszer, amíg a kapott adatokat nem veszik el, a kimenetek pedig idempotens vagy tranzakciós.
* Pontosan egyszer, az új Kafka közvetlen megközelítésével, amely a Kafka-t replikált naplóként használja a fogadók vagy a WALs helyett.

### <a name="typical-concerns-for-high-availability"></a>A magas rendelkezésre állással kapcsolatos jellemző információk

* A kötegelt feladatoknál nehezebb figyelni a folyamatos átviteli feladatokat. A Spark streaming-feladatok általában hosszan futnak, és a FONALak nem összesítik a naplókat, amíg a feladat be nem fejeződik.  A Spark-ellenőrzőpontok elvesznek az alkalmazás vagy a Spark frissítése során, és a frissítés során törölnie kell az ellenőrzőpont-könyvtárat.

* A FONALas fürt üzemmódjának konfigurálása az illesztőprogramok futtatásához, még akkor is, ha az ügyfél meghibásodik. Az illesztőprogramok automatikus újraindításának beállítása:

    ```
    spark.yarn.maxAppAttempts = 2
    spark.yarn.am.attemptFailuresValidityInterval=1h
    ```

* A Spark és a Spark streaming felhasználói felülete konfigurálható metrikai rendszerrel rendelkezik. További kódtárakat is használhat, például a grafit/Grafana az irányítópult-metrikák letöltéséhez, például a "NUM Records által feldolgozott", a "memória/GC-használat az illesztőprogram & végrehajtók", a "teljes késés", a "fürt kihasználtsága" és így tovább. A strukturált streaming 2,1-es vagy újabb verziójában `StreamingQueryListener` további mérőszámokat is gyűjthet.

* A hosszú ideig futó feladatokat érdemes szegmentálni.  Amikor egy Spark streaming-alkalmazást küld a fürtnek, meg kell határozni a szál várólistáját, amelyben a feladatot futtatni kell. A [fonalak kapacitása ütemező](https://hadoop.apache.org/docs/stable/hadoop-yarn/hadoop-yarn-site/CapacityScheduler.html) használatával hosszan futó feladatokat küldhet el külön várólistákra.

* Állítsa le a folyamatos átviteli alkalmazást. Ha az eltolások ismertek, és az összes alkalmazás állapota külsőleg van tárolva, akkor programozott módon állíthatja le a folyamatos átviteli alkalmazást a megfelelő helyen. Az egyik módszer a "Thread hookok" használata a Sparkban, ha a külső jelzőt minden *n* másodpercben ellenőrzi. A HDFS-ben létrehozott *jelölő fájlt* is használhat az alkalmazás indításakor, majd eltávolítja, ha le szeretné állítani. A jelölő fájl megközelítéséhez használjon egy különálló szálat a Spark-alkalmazásban, amely a következőhöz hasonló kódot hív meg:

    ```scala
    streamingContext.stop(stopSparkContext = true, stopGracefully = true)
    // to be able to recover on restart, store all offsets in an external database
    ```

## <a name="next-steps"></a>Következő lépések

* [Apache Spark streaming – áttekintés](apache-spark-streaming-overview.md)
* [Apache Spark streaming-feladatok létrehozása pontosan egyszer az események feldolgozásával](apache-spark-streaming-exactly-once.md)
* [Hosszú ideig futó Apache Spark streaming-feladatok a FONALon](https://mkuthan.github.io/blog/2016/09/30/spark-streaming-on-yarn/)
* [Strukturált streaming: hibatűrő szemantika](https://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html#fault-tolerance-semantics)
* [Diszkretizált Streams: A skálázható adatfolyamok feldolgozására szolgáló Fault-Tolerant modell](https://www2.eecs.berkeley.edu/Pubs/TechRpts/2012/EECS-2012-259.pdf)
