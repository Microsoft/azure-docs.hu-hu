---
title: Szolgáltatások létrehozása egy Azure HDInsight Hadoop-fürtben lévő adatelemzési folyamathoz
description: Példák a Azure HDInsight Hadoop-fürtben tárolt adatszolgáltatásokat létrehozó kaptár-lekérdezésekre.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 30c0a02c2cbc11002f8e0bf0295dab91de5d0365
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96020585"
---
# <a name="create-features-for-data-in-a-hadoop-cluster-using-hive-queries"></a>Hadoop-fürtben lévő adatszolgáltatások létrehozása struktúra-lekérdezések használatával
Ebből a dokumentumból megtudhatja, hogyan hozhat létre egy Azure HDInsight Hadoop-fürtön tárolt adatszolgáltatásokat a kaptár-lekérdezések használatával. Ezek a kaptár-lekérdezések beágyazott kaptár User-Defined functions (UDF), a megadott parancsfájlokat használják.

A funkciók létrehozásához szükséges műveletek memória-igényesek lehetnek. A kaptár-lekérdezések teljesítménye az ilyen esetekben nagyobb jelentőséggel bír, és bizonyos paraméterek hangolásával javítható. A paraméterek finomhangolását a végső szakaszban tárgyaljuk.

A bemutatott lekérdezésekre jellemző példák a New York-i [taxi Trip](https://chriswhong.com/open-data/foil_nyc_taxi/) adatforgatókönyvek esetében is elérhetők a [GitHub-tárházban](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts). Ezeknek a lekérdezéseknek már van megadott adatsémája, és készen állnak a futtatásra. Az utolsó szakaszban a felhasználók által beállítható paraméterek is megtalálhatók a kaptár-lekérdezések teljesítményének növelése érdekében.

Ez a feladat a [csoportos adatelemzési folyamat (TDSP)](./index.yml)egyik lépése.

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk feltételezi, hogy rendelkezik a következővel:

* Létrehozott egy Azure Storage-fiókot. Ha útmutatásra van szüksége, tekintse meg [Az Azure Storage-fiók létrehozása](../../storage/common/storage-account-create.md) című témakört.
* Testreszabott Hadoop-fürt kiépítve a HDInsight szolgáltatással.  Ha útmutatásra van szüksége, tekintse meg [a Azure HDInsight Hadoop-fürtök testreszabása speciális elemzésekhez](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md)című témakört.
* Az adatfeltöltés Azure HDInsight Hadoop fürtök struktúrájának tábláiba történt. Ha még nem tette meg, kövesse az [adatok létrehozása és betöltése a kaptár-táblákba](move-hive-tables.md) lehetőséget, hogy először töltse fel az adatok a kaptár-táblákba.
* Engedélyezve van a távoli hozzáférés a fürthöz. Ha útmutatásra van szüksége, tekintse meg [a Hadoop-fürt fő csomópontjának elérését](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md)ismertető témakört.

## <a name="feature-generation"></a><a name="hive-featureengineering"></a>Szolgáltatás létrehozása
Ebben a szakaszban számos példát láthat arra, hogyan hozhatók létre a szolgáltatások a kaptár-lekérdezések használatával. Ha további funkciókat hozott létre, azokat oszlopként hozzáadhatja a meglévő táblához, vagy létrehozhat egy új táblát a további funkciókkal és elsődleges kulccsal, amelyek ezután az eredeti táblával is összekapcsolhatók. Az alábbi példákat mutatjuk be:

1. [Gyakoriság-alapú szolgáltatás létrehozása](#hive-frequencyfeature)
2. [A kategorikus változók kockázatai a bináris besorolásban](#hive-riskfeature)
3. [Szolgáltatások kinyerése a DateTime mezőből](#hive-datefeatures)
4. [Szolgáltatások kinyerése a szövegmezőből](#hive-textfeatures)
5. [A GPS-koordináták közötti távolság kiszámítása](#hive-gpsdistance)

### <a name="frequency-based-feature-generation"></a><a name="hive-frequencyfeature"></a>Gyakoriság-alapú szolgáltatás létrehozása
Gyakran hasznos kiszámítani egy kategorikus változó szintjeinek gyakoriságát, vagy a szintek bizonyos kombinációinak gyakoriságát több kategorikus változóból. A felhasználók a következő parancsfájl használatával számíthatják ki a gyakoriságokat:

```hiveql
select
    a.<column_name1>, a.<column_name2>, a.sub_count/sum(a.sub_count) over () as frequency
from
(
    select
        <column_name1>,<column_name2>, count(*) as sub_count
    from <databasename>.<tablename> group by <column_name1>, <column_name2>
)a
order by frequency desc;
```


### <a name="risks-of-categorical-variables-in-binary-classification"></a><a name="hive-riskfeature"></a>A kategorikus változók kockázatai a bináris besorolásban
A bináris besorolásban a nem numerikus kategorikus változókat numerikus funkciókba kell konvertálni, ha a használt modellek csak numerikus funkciókat vesznek igénybe. Ezt a konverziót úgy végezheti el, hogy az egyes nem numerikus szinteket numerikus kockázattal váltja fel. Ez a szakasz néhány általános kaptár-lekérdezést mutat be, amely egy kategorikus változó kockázati értékeit (log Odds) számítja ki.

```hiveql
set smooth_param1=1;
set smooth_param2=20;
select
    <column_name1>,<column_name2>,
    ln((sum_target+${hiveconf:smooth_param1})/(record_count-sum_target+${hiveconf:smooth_param2}-${hiveconf:smooth_param1})) as risk
from
    (
    select
        <column_nam1>, <column_name2>, sum(binary_target) as sum_target, sum(1) as record_count
    from
        (
        select
            <column_name1>, <column_name2>, if(target_column>0,1,0) as binary_target
        from <databasename>.<tablename>
        )a
    group by <column_name1>, <column_name2>
    )b
```

Ebben a példában a változók `smooth_param1` és az `smooth_param2` adatokból kiszámított kockázati értékek simítására vannak beállítva. A kockázatok az-INF és az INF közötti tartománnyal rendelkeznek. A kockázati > 0 azt jelzi, hogy a cél értéke 1, nagyobb, mint 0,5.

A kockázati tábla kiszámítását követően a felhasználók a kockázati táblához csatlakozva kockázati értékeket rendelhetnek egy táblához. A struktúra-csatlakozás lekérdezése az előző szakaszban lett megadva.

### <a name="extract-features-from-datetime-fields"></a><a name="hive-datefeatures"></a>Szolgáltatások kinyerése a DateTime mezőkből
A kaptár UDF-készletet tartalmaz a DateTime mezők feldolgozásához. A kaptárban az alapértelmezett datetime formátum: éééé-hh-nn 00:00:00 (például "1970-01-01 12:21:32"). Ez a szakasz azokat a példákat mutatja be, amelyek kinyerik a hónap napját, a hónapot egy datetime mezőből, és más példákat is, amelyek az alapértelmezett formátumtól eltérő formátumú datetime karakterláncot alakítanak ki alapértelmezett formátumban.

```hiveql
select day(<datetime field>), month(<datetime field>)
from <databasename>.<tablename>;
```

Ez a kaptár-lekérdezés feltételezi, hogy az *\<datetime field>* alapértelmezett datetime formátumban van.

Ha egy datetime mező nem az alapértelmezett formátumban van, akkor először a dátum és idő típusú mezőt kell konvertálnia a UNIX-időbélyegre, majd a UNIX-időbélyeget egy alapértelmezett formátumú datetime karakterlánccá kell alakítania. Ha a DateTime alapértelmezett formátumban van, a felhasználók a beágyazott datetime UDF alkalmazhatják a funkciók kinyeréséhez.

```hiveql
select from_unixtime(unix_timestamp(<datetime field>,'<pattern of the datetime field>'))
from <databasename>.<tablename>;
```

Ebben a lekérdezésben, ha a *\<datetime field>* minta például a *03/26/2015 12:04:39*, akkor *\<pattern of the datetime field>* a következőnek kell lennie: `'MM/dd/yyyy HH:mm:ss'` . A teszteléshez a felhasználók futtathatnak

```hiveql
select from_unixtime(unix_timestamp('05/15/2015 09:32:10','MM/dd/yyyy HH:mm:ss'))
from hivesampletable limit 1;
```

A lekérdezésben szereplő *hivesampletable* alapértelmezés szerint az összes Azure HDInsight Hadoop-fürtön előre telepítve van, amikor a fürtöket kiépítik.

### <a name="extract-features-from-text-fields"></a><a name="hive-textfeatures"></a>Szolgáltatások kinyerése szöveges mezőkből
Ha a kaptár tábla olyan szövegmezővel rendelkezik, amely szóközökből álló karakterláncot tartalmaz, a következő lekérdezés kibontja a karakterlánc hosszát és a karakterláncban szereplő szavak számát.

```hiveql
select length(<text field>) as str_len, size(split(<text field>,' ')) as word_num
from <databasename>.<tablename>;
```

### <a name="calculate-distances-between-sets-of-gps-coordinates"></a><a name="hive-gpsdistance"></a>Távolságok kiszámítása a GPS-koordináták készletei között
Az ebben a szakaszban megadott lekérdezés közvetlenül alkalmazható a New York-i taxis-adatra. A lekérdezés célja, hogy bemutassa, hogyan alkalmazhat egy beágyazott matematikai függvényt a kaptárban funkciók létrehozásához.

A lekérdezésben használt mezők a felvételi és lemorzsolódási helyeinek GPS-koordinátái, a pickup *\_ hosszúsága*, a *pickup \_ szélesség*, a *lemorzsolódási \_ hosszúság* és a *lemorzsolódási \_ szélesség*. A felvételi és lemorzsolódási koordináták közötti közvetlen távolságot kiszámító lekérdezések a következők:

```hiveql
set R=3959;
set pi=radians(180);
select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude,
    ${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
    *${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
    *cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
    /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
    +cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*
    pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance
from nyctaxi.trip
where pickup_longitude between -90 and 0
and pickup_latitude between 30 and 90
and dropoff_longitude between -90 and 0
and dropoff_latitude between 30 and 90
limit 10;
```

A két GPS-koordináták közötti távolságot kiszámító matematikai egyenletek megtalálhatók a (z) Peter Lapisu által készített <a href="http://www.movable-type.co.uk/scripts/latlong.html" target="_blank">Movable Type Scripts</a> webhelyen. Ebben a JavaScriptben a függvény `toRad()` csak *lat_or_lon* PI/180, amely a fok és a radián közötti értéket alakítja át. Itt *lat_or_lon* a szélesség vagy a hosszúság. Mivel a kaptár nem adja meg a függvényt `atan2` , de a függvényt biztosítja `atan` , a `atan2` függvényt a `atan` fenti struktúra lekérdezésében a <a href="https://en.wikipedia.org/wiki/Atan2" target="_blank">wikipedia</a>által megadott definíció használatával valósítja meg.

![Munkaterület létrehozása](./media/create-features-hive/atan2new.png)

A kaptár beágyazott UDF teljes listája megtalálható a <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-MathematicalFunctions" target="_blank">Apache Hive wiki</a> **beépített függvények** szakaszában.  

## <a name="advanced-topics-tune-hive-parameters-to-improve-query-speed"></a><a name="tuning"></a> Speciális témakörök: a struktúra paramétereinek finomhangolása a lekérdezési sebesség javítása érdekében
Előfordulhat, hogy a kaptár-fürt alapértelmezett paraméter-beállításai nem megfelelőek a kaptár-lekérdezésekhez és a lekérdezések által feldolgozott adatmennyiségekhez. Ez a szakasz azokat a paramétereket ismerteti, amelyeket a felhasználók a kaptár-lekérdezések teljesítményének javítására tudnak hangolni. A felhasználóknak az adatfeldolgozási lekérdezések előtt hozzá kell adni a paraméter-hangolási lekérdezéseket.

1. **Java-halom területe**: a nagyméretű adatkészletek csatlakoztatását, illetve a hosszú rekordok feldolgozását végző lekérdezések esetében az egyik gyakori hiba az, hogy kifogyott a **rendelkezésre álló terület** . Ezt a hibát el lehet kerülni a paraméterek *MapReduce. map. Java. dönt* és *MapReduce. Task. IO. sort. MB* és a kívánt értékek megadásával. Alább bemutatunk egy példát:
   
    ```hiveql
    set mapreduce.map.java.opts=-Xmx4096m;
    set mapreduce.task.io.sort.mb=-Xmx1024m;
    ```

    Ez a paraméter 4 GB memóriát foglal le a Java kupac területéhez, és hatékonyabbá teszi a rendezést azáltal, hogy több memóriát foglal le. Érdemes lehet lejátszani ezeket a foglalásokat, ha a rendszer feladattal kapcsolatos hibákat észlelt a heap területtel kapcsolatban.

1. **DFS-blokk mérete**: Ez a paraméter a fájlrendszer által tárolt adatmennyiség legkisebb egységét állítja be. Ha például az elosztott fájlrendszerbeli blokk mérete 128 MB, akkor a méretnél kisebb és legfeljebb 128 MB méretű adatmennyiség egyetlen blokkban tárolódik. A 128 MB-nál nagyobb adatmennyiség további blokkokat is kiosztott. 
2. A kis blokk méretének kiválasztásakor a Hadoop nagy méretűek lehetnek, mivel a név csomópontnak több kérést kell feldolgoznia, hogy megtalálja a fájlhoz tartozó megfelelő blokkot. A gigabájt (vagy nagyobb) adatmennyiség esetén ajánlott beállítás:

    ```hiveql
    set dfs.block.size=128m;
    ```

2. **Összekapcsolási művelet optimalizálása a kaptárban**: míg a Térkép/csökkentés keretrendszerben lévő csatlakozási műveletek jellemzően a csökkentési fázisban vannak, időnként a térképi fázisban (más néven "mapjoins") lévő összekapcsolások ütemezésével is nagy nyereség érhető el. Beállítás beállítása:
   
    ```hiveql
    set hive.auto.convert.join=true;
    ```

3. **A kaptárak számának megadása a struktúra számára**: míg a Hadoop lehetővé teszi a felhasználó számára a szűkítők számának megadását, a leképezések számát általában nem a felhasználó állítja be. Egy olyan trükk, amely lehetővé teszi, hogy a szám bizonyos fokú szabályozása kiválassza a Hadoop változókat *mapred. min. Split. size* és *mapred. max. Split. size* értékre, az egyes leképezési feladatok méretének meghatározásához:
   
    ```hiveql
    num_maps = max(mapred.min.split.size, min(mapred.max.split.size, dfs.block.size))
    ```
   
    Az alapértelmezett érték általában:
    
   - a *mapred. min. Split. size* értéke 0, a
   - a *mapred. max. Split. size* **hossza. max** és a 
   - az *elosztott fájlrendszer. Block. size* értéke 64 MB.

     Ahogy láthatjuk, az adatok méretének megfelelően hangolja ezeket a paramétereket "beállítás" értékre, így beállíthatja, hogy a használt leképezések száma módosítható legyen.

4. Íme néhány további **speciális lehetőség** a struktúra teljesítményének optimalizálásához. Ezekkel a beállításokkal megadhatja a leképezési és a feladatokhoz lefoglalt memóriát, és hasznos lehet a teljesítmény finomhangolásához. Ne feledje, hogy a *MapReduce. csökkentse a memóriát. MB* nem lehet nagyobb, mint a Hadoop-fürtben lévő egyes munkavégző csomópontok fizikai memóriájának mérete.
   
    ```hiveql
    set mapreduce.map.memory.mb = 2048;
    set mapreduce.reduce.memory.mb=6144;
    set mapreduce.reduce.java.opts=-Xmx8192m;
    set mapred.reduce.tasks=128;
    set mapred.tasktracker.reduce.tasks.maximum=128;
    ```