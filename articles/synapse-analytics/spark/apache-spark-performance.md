---
title: A Spark-feladatok optimalizálása a teljesítmény érdekében
description: Ez a cikk az Azure szinapszis Analytics Apache Sparkának bevezetését ismerteti.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: b94ece73d5f9dc9b8343e45fb1f616599b9a1c1f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96450923"
---
# <a name="optimize-apache-spark-jobs-in-azure-synapse-analytics"></a>Apache Spark feladatok optimalizálása az Azure szinapszis Analyticsben

Megtudhatja, hogyan optimalizálhat egy [Apache Spark](https://spark.apache.org/) fürtkonfiguráció az adott számítási feladathoz.  A leggyakoribb kihívás a memória leterheltsége, amelynek okai lehetnek a nem megfelelő konfigurációk (különösen a nem megfelelő méretű végrehajtók), a hosszan futó műveletek, valamint a Descartes-műveleteket eredményező feladatok. Felgyorsíthatja a feladatokat a megfelelő gyorsítótárazással, és engedélyezheti az [adatok eldöntését](#optimize-joins-and-shuffles). A legjobb teljesítmény érdekében figyelje és tekintse át a hosszú ideig futó és az erőforrás-igényes Spark-feladatok végrehajtását.

A következő szakaszok ismertetik a Spark-feladatok gyakori optimalizálásait és javaslatait.

## <a name="choose-the-data-abstraction"></a>Válassza ki az adatabsztrakciót

A korábbi Spark-verziók a RDD és az absztrakt adatokat, a Spark 1,3-es és a 1,6-es verzióját használták a DataFrames és adatkészletek használatára. Vegye figyelembe a következő relatív Érdemeiket:

* **Adatkeretek**
  * Legjobb választás a legtöbb esetben.
  * Lekérdezési optimalizálást biztosít a katalizátoron keresztül.
  * Teljes fázisú programkódok létrehozása.
  * Közvetlen memória-hozzáférés.
  * Alacsony terhelésű gyűjtés (GC) terhelése.
  * Nem fejlesztőként, mint adatkészletek, mivel nincsenek fordítási idejű ellenőrzések vagy tartományi objektumok programozása.
* **Adatkészletek**
  * Olyan összetett ETL-folyamatokban jó, ahol a teljesítményre gyakorolt hatás elfogadható.
  * Nem jó olyan összesítésekben, ahol a teljesítményre gyakorolt hatás jelentős lehet.
  * Lekérdezési optimalizálást biztosít a katalizátoron keresztül.
  * Fejlesztőknek – a tartományi objektumok programozása és a fordítási idő ellenőrzésének biztosításával.
  * Szerializálási/deszerializálási terhelés hozzáadására szolgál.
  * Magas GC-terhelés.
  * A teljes fázisú programkódok létrehozásának megszakítása.
* **RDD-k**
  * Nem kell RDD használnia, hacsak nem kell új egyéni RDD létrehoznia.
  * Nincs lekérdezés optimalizálása a katalizátoron keresztül.
  * Nincs egész fázist generáló kód.
  * Magas GC-terhelés.
  * A Spark 1. x örökölt API-kat kell használnia.

## <a name="use-optimal-data-format"></a>Optimális adatformátum használata

A Spark számos formátumot támogat, például a CSV-t, a JSON-t, az XML-t, a parketta, az ork és a Avro. A Spark bővíthető a külső adatforrásokkal rendelkező több formátum támogatásához – további információ: [Apache Spark csomagok](https://spark-packages.org).

A teljesítmény legjobb formátuma a Parquet és a *Snappy Compression*, amely az alapértelmezett a Spark 2. x verzióban. A Parquet oszlopos formátumban tárolja az adatok, és a Spark-ban is nagyon optimalizált. Emellett a *Snappy Compression* is nagyobb fájlokat eredményezhet, mint a gzip-tömörítést. A fájlok felosztható jellege miatt a rendszer gyorsabban kibontja a kibontást.

## <a name="use-the-cache"></a>Gyorsítótár használata

A Spark saját natív gyorsítótárazási mechanizmusokat biztosít, amelyek különböző módszerekkel használhatók, például:, `.persist()` `.cache()` és `CACHE TABLE` . Ez a natív gyorsítótárazás a kis adatkészletek és az ETL-folyamatok esetében érvényes, ahol a közbenső eredmények gyorsítótárazására van szükség. A Spark natív gyorsítótárazás azonban jelenleg nem működik megfelelően a particionálással, mivel a gyorsítótárazott táblák nem őrzik meg a particionálási adatmennyiséget.

## <a name="use-memory-efficiently"></a>A memória hatékony használata

A Spark úgy működik, hogy a memóriába helyezi az adatok mennyiségét, így a memória erőforrásainak kezelése kulcsfontosságú szempont a Spark-feladatok végrehajtásának optimalizálásához.  Több módszer is alkalmazható a fürt memóriájának hatékony használatára.

* A particionálási stratégiában a kisebb adatpartíciók és a fiókok mérete, típusai és eloszlása is előnyben részesített.
* Az alapértelmezett Java-szerializálás helyett vegye figyelembe az újabb, hatékonyabb [Kryo-adatszerializálást](https://github.com/EsotericSoftware/kryo).
* A Spark konfigurációs beállításainak figyelése és finomhangolása.

A következő képen látható a Spark-memória szerkezete és néhány kulcsfontosságú végrehajtói memória paraméter.

### <a name="spark-memory-considerations"></a>A Spark memória szempontjai

Apache Spark az Azure Szinapszisban FONALat [Apache HADOOP fonalat](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html)használ, a fonal pedig az egyes Spark-csomópontokon lévő összes tároló által felhasznált memória maximális számát szabályozza.  A következő ábrán a legfontosabb objektumok és azok kapcsolatai láthatók.

![A fonal Spark memóriájának kezelése](./media/apache-spark-perf/apache-yarn-spark-memory.png)

A "memórián kívüli" üzenetek megoldásához próbálkozzon a következővel:

* Tekintse át a DAG felügyeletének véletlenszerű működését. Csökkentse a leképezési és a partíciós (vagy bucketize) adatforrások közötti adatmennyiséget, maximalizálja az egyetlen véletlenszerű sorrendet, és csökkentse a továbbított adatmennyiséget.
* Inkább a `ReduceByKey` rögzített memória korlátja `GroupByKey` , amely az összesítéseket, az ablakokat és más függvényeket tartalmaz, de a memória korlátja korlátozott.
* A rendszer inkább a `TreeReduce` végrehajtók vagy a partíciók több munkáját hajtja végre a (z `Reduce` ) rendszeren, amely az illesztőprogramon végzett összes munkát végzi.
* Az alsó szintű RDD-objektumok helyett használja a DataFrames.
* Hozzon létre olyan ComplexTypes, amelyek műveleteket (például "Top N"), különböző összesítéseket vagy ablakkezelő műveleteket ágyaznak be.

## <a name="optimize-data-serialization"></a>Az adatszerializálás optimalizálása

A Spark-feladatok terjesztése megtörténik, ezért a megfelelő adatszerializálás fontos a legjobb teljesítmény érdekében.  A Spark két szerializálási lehetőséggel rendelkezik:

* A Java-szerializálás az alapértelmezett.
* A Kryo szerializálása egy újabb formátum, amely gyorsabb és kompakt szerializálást eredményezhet a Javánál.  A Kryo használatához regisztrálnia kell az osztályokat a programban, és még nem támogatja az összes szerializálható típust.

## <a name="use-bucketing"></a>Gyűjtés használata

A gyűjtő az adatparticionáláshoz hasonló, de az egyes gyűjtők nem csupán egy oszlop értékét tárolhatják. A gyűjtő nagy mennyiségű (több millió vagy több) értékben, például termékazonosítóban is működik. A gyűjtőt a sor gyűjtő kulcsának kivonatolásával határozzuk meg. A gyűjtő táblák egyedi optimalizációkat biztosítanak, mert metaadatokat tárolnak a gyűjtők és a rendezésük módjával kapcsolatban.

Bizonyos speciális gyűjtő funkciók a következők:

* Lekérdezés optimalizálása a meta-adatok gyűjtője alapján.
* Optimalizált összesítések.
* Optimalizált illesztések.

Egyszerre használhatja a particionálást és a gyűjtőt.

## <a name="optimize-joins-and-shuffles"></a>A csatlakozás és a véletlen sorrend optimalizálása

Ha lassú feladatokkal van összekapcsolva vagy shuffle, az OK valószínűleg az *adatok eldöntése*, ami a feladat adataiban található aszimmetria. A térképes feladatok például 20 másodpercet is igénybe vehetnek, de egy olyan feladatot futtatnak, ahol az adatok csatlakoztatva vannak, vagy a csoszogott órákat vesz igénybe. Az adatdöntés kijavításához a teljes kulcsot kell megállapítania, vagy egy *elkülönített sót* kell használnia a kulcsok csak néhány részhalmaza számára. Ha izolált sót használ, érdemes tovább szűrnie, hogy elkülönítse a sós kulcsok részhalmazát a térképi illesztésekben. Egy másik lehetőség egy gyűjtő oszlop bevezetése és a gyűjtők előzetes összesítése.

A lassú illesztéseket okozó másik tényező lehet az illesztés típusa. Alapértelmezés szerint a Spark az `SortMerge` illesztés típusát használja. Az ilyen típusú illesztések leginkább nagy adatkészletekhez használhatók, de más számítási szempontból költségesek, mert először a bal és a jobb oldalon kell rendezni az adatok egyesítését.

Az `Broadcast` illesztés a kisebb adatkészletekhez ideális, vagy ha az illesztés egyik oldala sokkal kisebb, mint a másik oldal. Az ilyen típusú illesztések az egyik oldalról az összes végrehajtóra mutatnak, ezért általában több memóriát igényelnek a szórások számára.

Az illesztési típust beállíthatja a konfigurációban, vagy beállíthatja az `spark.sql.autoBroadcastJoinThreshold` illesztési mutatót a DataFrame API-k ( `dataframe.join(broadcast(df2))` ) használatával.

```scala
// Option 1
spark.conf.set("spark.sql.autoBroadcastJoinThreshold", 1*1024*1024*1024)

// Option 2
val df1 = spark.table("FactTableA")
val df2 = spark.table("dimMP")
df1.join(broadcast(df2), Seq("PK")).
    createOrReplaceTempView("V_JOIN")

sql("SELECT col1, col2 FROM V_JOIN")
```

Ha gyűjtő táblákat használ, akkor egy harmadik illesztési típussal rendelkezik, amelyhez `Merge` csatlakozik. Egy megfelelően előre particionált és előre rendezett adatkészlet kihagyja a költséges rendezési szakaszt egy `SortMerge` illesztésből.

Az illesztések sorrendje, különösen az összetettebb lekérdezésekben. Kezdje a legszelektívebb illesztésekkel. Emellett olyan illesztéseket is helyezhet át, amelyek a sorok számát a lehetséges összesítések után növelhetik.

A Descartes-féle illesztések párhuzamosságának kezeléséhez beágyazott struktúrákat, ablakokat adhat hozzá, és lehet, hogy kihagy egy vagy több lépést a Spark-feladatokban.

### <a name="select-the-correct-executor-size"></a>A megfelelő végrehajtó méretének kiválasztása

A végrehajtó konfigurációjának meghatározásakor vegye figyelembe a Java Garbage Collection (GC) terhelését.

* A végrehajtó méretének csökkentésére szolgáló tényezők:
  * Csökkentse a 32 GB alatti halom méretét a GC terhelésének < 10%-os megtartásához.
  * Csökkentse a magok számát, hogy a GC terhelése < 10% legyen.

* A végrehajtó méretének növelésére szolgáló tényezők:
  * A végrehajtók közötti kommunikációs terhelés csökkentése.
  * Csökkentse a nyitott kapcsolatok számát a végrehajtók (N2) között nagyobb fürtökön (>100 végrehajtók).
  * Növelje a nagy mennyiségű memóriát, hogy megfeleljen a memória-igényes feladatok számára.
  * Nem kötelező: csökkentse a végrehajtó memória terhelését.
  * Nem kötelező: növelje a kihasználtságot és a párhuzamosságot a processzor túllépésével.

Általános ökölszabály a végrehajtó méretének kiválasztásakor:

* Indítson el 30 GB-ot végrehajtóként, és terjesszen rendelkezésre álló gépi magokat.
* Növelje a végrehajtó magok számát a nagyobb fürtöknél (> 100 végrehajtók).
* A méretet a próbaverziós futtatások és az előző tényezők, például a GC terhelése alapján módosíthatja.

Az egyidejű lekérdezések futtatásakor vegye figyelembe a következőket:

* Indítson el 30 GB-ot végrehajtóként és minden gépi magot.
* Több párhuzamos Spark-alkalmazás létrehozása a processzor túllépésével (körülbelül 30%-os késéssel).
* Lekérdezések terjesztése párhuzamos alkalmazások között.
* A méretet a próbaverziós futtatások és az előző tényezők, például a GC terhelése alapján módosíthatja.

A lekérdezés teljesítményének figyelése kiugró vagy egyéb teljesítménnyel kapcsolatos problémák esetén, az Idősor nézet, az SQL Graph, a feladatok statisztikái és így tovább. Előfordulhat, hogy a végrehajtók közül egy vagy több lassabb, mint a többi, és a feladatok végrehajtása sokkal hosszabb ideig tart. Ez gyakran előfordul a nagyobb fürtökön (> 30 csomópont). Ebben az esetben a munkát nagyobb számú feladatra osztja fel, így az ütemező kompenzálhatja a lassú feladatokat. 

Például legalább kétszer annyi feladatnak kell lennie, mint a végrehajtó magok száma az alkalmazásban. A feladatokhoz tartozó spekulatív végrehajtást is engedélyezheti `conf: spark.speculation = true` .

## <a name="optimize-job-execution"></a>A feladat-végrehajtás optimalizálása

* Szükség szerint gyorsítótárazza, például ha kétszer használja az adatkészletet, majd gyorsítótárazza.
* Szórási változók az összes végrehajtóra. A változók csak egyszer szerializáltak, ami gyorsabb keresést eredményez.
* Használja a szál készletét az illesztőprogramon, ami gyorsabb műveletet eredményez számos feladathoz.

A Spark 2. x lekérdezési teljesítménye a Wolfram motor, amely a teljes fázisú programkódok generálásának függvénye. Bizonyos esetekben előfordulhat, hogy a teljes fázisú kód létrehozása le lesz tiltva. 

Ha például nem megváltoztathatatlan típust ( `string` ) használ az összesítési kifejezésben, a () helyett a következő `SortAggregate` jelenik meg: `HashAggregate` . Például a jobb teljesítmény érdekében próbálkozzon a következőkkel, majd engedélyezze újra a kód generálását:

```sql
MAX(AMOUNT) -> MAX(cast(AMOUNT as DOUBLE))
```

## <a name="next-steps"></a>Következő lépések

- [Hangolás Apache Spark](https://spark.apache.org/docs/2.4.5/tuning.html)
- [A Apache Spark-feladatok tényleges finomhangolása](https://www.slideshare.net/ilganeli/how-to-actually-tune-your-spark-jobs-so-they-work)
- [Kryo szerializálás](https://github.com/EsotericSoftware/kryo)
