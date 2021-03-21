---
title: Azure Data Lake Storage Gen1 – teljesítmény finombeállítása
description: Ismerje meg, hogyan használható az összes rendelkezésre álló átviteli sebesség Azure Data Lake Storage Gen1 a legjobb teljesítmény eléréséhez a lehető legtöbb olvasást és írást kell végrehajtania.
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 06/30/2017
ms.author: twooley
ms.openlocfilehash: c7f16dd9ea450185893164e10928c7022d6ab5a6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97724680"
---
# <a name="tune-azure-data-lake-storage-gen1-for-performance"></a>Azure Data Lake Storage Gen1 teljesítményének hangolása

A Data Lake Storage Gen1 nagy átviteli sebességet biztosít az I/O-intenzív elemzésekhez és az adatáthelyezéshez. Data Lake Storage Gen1 az összes rendelkezésre álló átviteli sebesség – a másodpercenként olvasható vagy írható adatok mennyisége – fontos a legjobb teljesítmény érdekében. Ez úgy érhető el, hogy a lehető legtöbb olvasási és írási műveletet végrehajtja.

![Data Lake Storage Gen1 teljesítmény](./media/data-lake-store-performance-tuning-guidance/throughput.png)

A Data Lake Storage Gen1 méretezheti, hogy az összes elemzési forgatókönyv esetében elérhető legyen a szükséges átviteli sebesség. Alapértelmezés szerint egy Data Lake Storage Gen1 fiók automatikusan elegendő átviteli sebességet biztosít, hogy megfeleljen a használati esetek széles kategóriájának. Azokban az esetekben, amikor az ügyfelek az alapértelmezett korláton futnak, a Data Lake Storage Gen1 fiók úgy konfigurálható, hogy a Microsoft támogatási szolgálatával további átviteli sebességet biztosítson.

## <a name="data-ingestion"></a>Adatfeldolgozás

Amikor adatfeldolgozást végez egy forrásoldali rendszerből Data Lake Storage Gen1ba, fontos figyelembe venni, hogy a forrás hardver, a forrás hálózati hardver és a Data Lake Storage Gen1 hálózati kapcsolata a szűk keresztmetszet.

![Diagram, amely azt mutatja, hogy a forrás hardver, a forrás hálózati hardver és a Data Lake Storage Gen1 hálózati kapcsolat lehet a szűk keresztmetszet.](./media/data-lake-store-performance-tuning-guidance/bottleneck.png)

Fontos, hogy az adatok áthelyezését ne befolyásolja ezek a tényezők.

### <a name="source-hardware"></a>Forrás hardver

Függetlenül attól, hogy helyszíni gépeket vagy virtuális gépeket használ az Azure-ban, gondosan válassza ki a megfelelő hardvert. A forrásoldali lemezes hardverek esetében inkább az SSD-ket használja a HDD-k számára, és gyorsabb orsókkal válassza a lemez hardverét. A forrásoldali hálózati hardverek esetében a lehető leggyorsabb hálózati adaptereket használhatja. Az Azure-ban olyan Azure D14 virtuális gépeket ajánlunk, amelyek megfelelő teljesítményű lemezzel és hálózati hardverrel rendelkeznek.

### <a name="network-connectivity-to-data-lake-storage-gen1"></a>Data Lake Storage Gen1 hálózati kapcsolat

A forrásadatok és a Data Lake Storage Gen1 közötti hálózati kapcsolat esetenként szűk keresztmetszetet jelenthet. Ha a forrásadatok helyszíniek, érdemes lehet dedikált hivatkozást használni az [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) . Ha a forrásadatok az Azure-ban találhatók, akkor a teljesítmény akkor a legjobb, ha az adat ugyanabban az Azure-régióban található, mint a Data Lake Storage Gen1 fiók.

### <a name="configure-data-ingestion-tools-for-maximum-parallelization"></a>Adatfeldolgozási eszközök konfigurálása a maximális párhuzamos

Miután megoldotta a forrás hardver és a hálózati kapcsolat szűk keresztmetszeteit, készen áll a betöltési eszközök konfigurálására. A következő táblázat összefoglalja a számos népszerű betöltési eszköz főbb beállításait, és részletes teljesítmény-finomhangolási cikkeket biztosít számukra. Ha többet szeretne megtudni a forgatókönyvhöz használt eszközről, tekintse meg ezt a [cikket](./data-lake-store-data-scenarios.md).

| Eszköz          | Beállítások | További részletek                                                                 |
|--------------------|------------------------------------------------------|------------------------------|
| PowerShell       | PerFileThreadCount, ConcurrentFileCount | [Hivatkozás](./data-lake-store-get-started-powershell.md) |
| AdlCopy    | Azure Data Lake Analytics egységek | [Hivatkozás](./data-lake-store-copy-data-azure-storage-blob.md#performance-considerations-for-using-adlcopy)         |
| DistCp            | -m (Mapper) | [Hivatkozás](./data-lake-store-copy-data-wasb-distcp.md#performance-considerations-while-using-distcp)                             |
| Azure Data Factory| parallelCopies | [Hivatkozás](../data-factory/copy-activity-performance.md)                          |
| Sqoop           | FS. Azure. Block. size,-m (Mapper) | [Hivatkozás](/archive/blogs/shanyu/performance-tuning-for-hdinsight-storm-and-microsoft-azure-eventhubs)        |

## <a name="structure-your-data-set"></a>Az adathalmaz szerkezete

Ha az adat tárolása Data Lake Storage Gen1 történik, a fájl mérete, a fájlok száma és a mappa szerkezete befolyásolja a teljesítményt. A következő szakasz az ezekben a területeken ajánlott eljárásokat ismerteti.

### <a name="file-size"></a>Fájlméret

Az elemzési motorok (például a HDInsight és a Azure Data Lake Analytics) általában Egyfájlos terheléssel rendelkeznek. Ha az adatait sok kis fájlként tárolja, ez negatívan befolyásolhatja a teljesítményt.

Általánosságban megszervezheti az adatait nagyobb méretű fájlokba a jobb teljesítmény érdekében. Az adatkészletek az 256 MB vagy nagyobb méretű fájlokba rendezhetők. Bizonyos esetekben, például képekben és bináris adatszolgáltatásokban nem lehet őket párhuzamosan feldolgozni. Ezekben az esetekben javasoljuk, hogy az egyes fájlokat 2 GB alá tartsa.

Időnként az adatfolyamatok korlátozott mértékben szabályozzák a sok kis fájlból álló nyers adatmennyiséget. Azt javasoljuk, hogy egy "főzési" folyamattal rendelkezzen, amely nagyobb fájlokat generál az alárendelt alkalmazásokhoz.

### <a name="organize-time-series-data-in-folders"></a>Idősorozat-adattárolás mappákban történő rendszerezése

A kaptár-és ADLA számítási feladataihoz az idősoros adatok partícióinak metszése segíthet néhány lekérdezésnek az adatok egy részhalmazát olvasni, ami javítja a teljesítményt.

Azok a folyamatok, amelyek idősoros adatot töltenek be, gyakran a fájlok és mappák strukturált elnevezésével helyezik el a fájljaikat. A következő gyakori példa a dátum: *\dataset\yyyy\mm\dd\ datafile_YYYY_MM_DD. TSV* alapján strukturált adatok esetében látható.

Figyelje meg, hogy a DateTime információ a mappák és a fájlnév között is megjelenik.

A dátum és idő esetében a következő gyakori minta: *\dataset\yyyy\mm\dd\hh\mm\ datafile_YYYY_MM_DD_HH_mm. TSV*.

A mappa és a fájl szervezetének megválasztásakor a nagyobb fájlméretet és az egyes mappákban lévő fájlok ésszerű számú fájljának optimalizálását is érdemes megtenni.

## <a name="optimize-io-intensive-jobs-on-hadoop-and-spark-workloads-on-hdinsight"></a>Nagy I/O-igényű feladatok optimalizálása a Hadoop-és Spark-munkaterheléseken a HDInsight

A feladatok a következő három kategóriába sorolhatók:

* **CPU-igényes.** Ezeknek a feladatoknak hosszú számítási ideje van a minimális I/O-időpontokban. Ilyenek például a gépi tanulás és a természetes nyelvi feldolgozási feladatok.
* **Memória-igényes.** Ezek a feladatok sok memóriát használnak. Ilyenek például a PageRank és a valós idejű elemzési feladatok.
* **Nagy I/O-igényű.** Ezek a feladatok a legtöbb időt töltik az I/O-műveletek során. Gyakori példa egy olyan másolási művelet, amely csak olvasási és írási műveleteket végez. A többi példa olyan adat-előkészítési feladatokat is tartalmaz, amelyek számos adatot olvasnak, elvégez némi adatátalakítást, majd visszaírja az adatot a tárolóba.

Az alábbi útmutatás csak az I/O-igényes feladatokra vonatkozik.

### <a name="general-considerations-for-an-hdinsight-cluster"></a>HDInsight-fürt általános szempontjai

* **HDInsight-verziók.** A legjobb teljesítmény érdekében használja a HDInsight legújabb kiadását.
* **Régiók.** Helyezze a Data Lake Storage Gen1 fiókot a HDInsight-fürttel megegyező régióban.

An méretű HDInsight-fürt két fő csomópontból és néhány feldolgozói csomópontból áll. Minden munkavégző csomópont adott számú magot és memóriát biztosít, amelyet a virtuális gép típusa határoz meg. A feladatok futtatásakor a fonal a tárolók létrehozásához rendelkezésre álló memória és magok kiosztására szolgáló erőforrás-tárgyaló. Minden tároló futtatja a feladat végrehajtásához szükséges feladatokat. A tárolók párhuzamosan futnak a feladatok gyors feldolgozásához. Ezért a teljesítmény javítása a lehető legtöbb párhuzamos tároló futtatásával történik.

Egy HDInsight-fürtben három réteg található, amelyek úgy állíthatók be, hogy növeljük a tárolók számát és az összes elérhető átviteli sebességet.

* **Fizikai réteg**
* **FONAL réteg**
* **Munkaterhelés réteg**

### <a name="physical-layer"></a>Fizikai réteg

**Futtasson több csomóponttal és/vagy nagyobb méretű virtuális géppel rendelkező fürtöt.** Egy nagyobb fürt lehetővé teszi, hogy több FONALas tárolót futtasson az alábbi képen látható módon.

![A több SZÁLat tartalmazó tárolók használatát bemutató diagram.](./media/data-lake-store-performance-tuning-guidance/VM.png)

**Használjon nagyobb hálózati sávszélességű virtuális gépeket.** A hálózati sávszélesség mennyisége szűk keresztmetszetet jelenthet, ha a Data Lake Storage Gen1 átviteli sebességnél kevesebb hálózati sávszélesség van. A különböző virtuális gépek eltérő hálózati sávszélességet fognak tartalmazni. Válasszon olyan virtuálisgép-típust, amely a legnagyobb lehetséges hálózati sávszélességgel rendelkezik.

### <a name="yarn-layer"></a>FONAL réteg

**Használjon kisebb FONALas tárolókat.** Csökkentse az egyes FONALak tárolóinak méretét, hogy több tárolót hozzon létre azonos mennyiségű erőforrással.

![A kisebb FONALas tárolók használatát bemutató diagram.](./media/data-lake-store-performance-tuning-guidance/small-containers.png)

A számítási feladattól függően mindig szükség van egy minimálisan szükséges szál-tároló méretre. Ha túl kis tárolót választ, a feladatok memórián kívüli problémákba fognak futni. Általában a FONALas tárolók nem lehetnek 1 GB-nál kisebbek. Ez általában a 3 GB-os FONALas tárolók esetében látható. Bizonyos munkaterhelések esetén nagyobb méretű FONALas tárolók szükségesek.

**Növelje a magok számát a szálak tárolóján.** Növelje az egyes tárolók számára lefoglalt magok számát, hogy növelje az egyes tárolókban futó párhuzamos feladatok számát. Ez olyan alkalmazások esetében működik, mint a Spark, amelyek tárolón több feladatot futtatnak. Az olyan alkalmazások esetében, mint például a kaptár, amely minden tárolóban egyetlen szálat futtat, jobb, ha több tárolót szeretne, és nem több magot.

### <a name="workload-layer"></a>Munkaterhelés réteg

**Az összes rendelkezésre álló tároló használata.** Állítsa be, hogy a tevékenységek száma egyenlő vagy nagyobb legyen, mint a rendelkezésre álló tárolók száma, hogy az összes erőforrás használatban legyen.

![Az összes rendelkezésre álló tároló használatát bemutató diagram.](./media/data-lake-store-performance-tuning-guidance/use-containers.png)

**A sikertelen feladatok költségesek.** Ha az egyes feladatok nagy mennyiségű adattal dolgoznak fel, akkor a feladat meghibásodása költséges újrapróbálkozást eredményez. Ezért érdemes több feladatot létrehozni, amelyek mindegyike kis mennyiségű adat feldolgozását dolgozza fel.

A fenti általános irányelvek mellett minden alkalmazás különböző paraméterekkel rendelkezik az adott alkalmazás finomhangolásához. Az alábbi táblázat néhány paramétert és hivatkozást tartalmaz, amelyekkel megkezdheti az egyes alkalmazások teljesítményének finomhangolását.

| Számítási feladat               | A feladatok beállítására szolgáló paraméter                                                         |
|--------------------|-------------------------------------------------------------------------------------|
| [Spark on HDInsight](data-lake-store-performance-tuning-spark.md)  | <ul><li>NUM-végrehajtók</li><li>Végrehajtó – memória</li><li>Végrehajtó – magok</li></ul> |
| [Struktúra a HDInsight](data-lake-store-performance-tuning-hive.md)    | <ul><li>kaptár. TEZ. Container. size</li></ul>         |
| [MapReduce a HDInsight](data-lake-store-performance-tuning-mapreduce.md)            | <ul><li>MapReduce. map. Memory</li><li>MapReduce. job. Maps</li><li>MapReduce. csökkentse a memóriát</li><li>MapReduce. job. csökkenti</li></ul> |
| [Storm on HDInsight](data-lake-store-performance-tuning-storm.md)| <ul><li>Munkavégző folyamatok száma</li><li>Kiöntő végrehajtó példányainak száma</li><li>A bolt végrehajtó példányainak száma </li><li>Kiöntő feladatok száma</li><li>Bolti feladatok száma</li></ul>|

## <a name="see-also"></a>Lásd még

* [A Azure Data Lake Storage Gen1 áttekintése](data-lake-store-overview.md)
* [Ismerkedés az Azure Data Lake Analytics szolgáltatással](../data-lake-analytics/data-lake-analytics-get-started-portal.md)