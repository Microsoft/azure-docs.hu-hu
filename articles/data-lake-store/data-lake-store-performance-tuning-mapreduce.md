---
title: Azure Data Lake Storage Gen1 Performance tuning – MapReduce
description: Ismerkedjen meg az Azure Data Lake Storage Gen1 MapReduce teljesítményének finomhangolásával, beleértve a paramétereket, az útmutatást, a számításokat és a korlátozásokat.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 12/19/2016
ms.author: twooley
ms.openlocfilehash: 2549413241e422fb1e0e5e1f079c287e0b7cf005
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97723728"
---
# <a name="performance-tuning-guidance-for-mapreduce-on-hdinsight-and-azure-data-lake-storage-gen1"></a>Teljesítmény-finomhangolási útmutató a HDInsight és Azure Data Lake Storage Gen1 MapReduce

## <a name="prerequisites"></a>Előfeltételek

* **Egy Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* **Egy Azure Data Lake Storage Gen1-fiók**. A létrehozásával kapcsolatos útmutatásért tekintse meg az Ismerkedés [a Azure Data Lake Storage Gen1rel](data-lake-store-get-started-portal.md) című témakört.
* **Azure HDInsight-fürt** Data Lake Storage Gen1 fiókhoz való hozzáféréssel. Lásd: [HDInsight-fürt létrehozása Data Lake Storage Gen1sal](data-lake-store-hdinsight-hadoop-use-portal.md). Győződjön meg arról, hogy engedélyezi Távoli asztal a fürt számára.
* **A MapReduce használata a HDInsight-on**. További információ: [MapReduce használata a Hadoop on HDInsight](../hdinsight/hadoop/hdinsight-use-mapreduce.md)
* **Tekintse át a Data Lake Storage Gen1 teljesítmény-hangolási irányelveit**. Az általános teljesítménnyel kapcsolatos fogalmakat lásd: [Data Lake Storage Gen1 teljesítmény-finomhangolási útmutató](./data-lake-store-performance-tuning-guidance.md)

## <a name="parameters"></a>Paraméterek

A MapReduce-feladatok futtatásakor itt láthatók a legfontosabb paraméterek, amelyeket konfigurálhat a Data Lake Storage Gen1 teljesítményének növeléséhez:

|Paraméter      | Leírás  |
|---------|---------|
|`Mapreduce.map.memory.mb`  |  Az egyes leképezések számára lefoglalható memória mennyisége.  |
|`Mapreduce.job.maps`     |  Feladat-hozzárendelési feladatok száma.  |
|`Mapreduce.reduce.memory.mb`     |  Az egyes szűkítők számára lefoglalható memória mennyisége.  |
|`Mapreduce.job.reduces`    |   A feladatok számának csökkentése feladat alapján.  |

### <a name="mapreducemapmemory--mapreducereducememory"></a>MapReduce. map. Memory/MapReduce. csökkentse a memóriát

Módosítsa ezt a számot attól függően, hogy mennyi memóriát igényel a Térkép és/vagy a feladat csökkentése. A és a Ambari alapértelmezett értékeit `mapreduce.map.memory` `mapreduce.reduce.memory` a fonal konfigurációján keresztül tekintheti meg. A Ambari-ben navigáljon a fonal lapra, és tekintse meg a **konfigurációk** lapot. Ekkor megjelenik a szál memóriája.

### <a name="mapreducejobmaps--mapreducejobreduces"></a>MapReduce. job. Maps/MapReduce. job. csökkentse

Ez határozza meg a létrehozandó leképezések vagy szűkítők maximális számát. A felosztások száma határozza meg, hogy a rendszer hány leképezést hoz létre a MapReduce feladatokhoz. Ezért előfordulhat, hogy kevesebb leképezést kap, mint amennyit kért, ha a kért leképezések száma kevesebb.

## <a name="guidance"></a>Útmutató

### <a name="step-1-determine-number-of-jobs-running"></a>1. lépés: a futó feladatok számának meghatározása

Alapértelmezés szerint a MapReduce a teljes fürtöt fogja használni a feladatokhoz. A fürt kevesebb, mint a rendelkezésre álló tárolók használatával kevesebb leképezést használ. A jelen dokumentumban szereplő útmutatás azt feltételezi, hogy az alkalmazás az egyetlen, a fürtön futó alkalmazás.

### <a name="step-2-set-mapreducemapmemorymapreducereducememory"></a>2. lépés: állítsa be a MapReduce. map. Memory/MapReduce. csökkentse a memóriát

A térképhez és a tevékenységek csökkentéséhez használt memória mérete az adott feladattól függ. Ha a párhuzamosságot szeretné bővíteni, csökkentheti a memória méretét. Az egyidejűleg futó feladatok száma a tárolók számától függ. Ha csökkenti a memória mennyiségét a leképező vagy a csökkentő memóriában, több tárolót is létrehozhat, amelyek lehetővé teszik, hogy egyidejűleg több leképezést vagy szűkítőt futtasson. A túl sok memória mennyiségének csökkentése miatt előfordulhat, hogy bizonyos folyamatok elfogynak a memóriából. Ha a feladatok futtatásakor egy halom hibaüzenetet kap, növelje a memóriát a leképező vagy a szűkítő alapján. Vegye figyelembe, hogy a további tárolók hozzáadásával további terhelések is megadhatók, ami potenciálisan csökkentheti a teljesítményt. Egy másik alternatíva, hogy több memóriát kell használnia egy olyan fürt használatával, amely nagyobb mennyiségű memóriával rendelkezik, vagy növeli a fürt csomópontjainak számát. Több memória használata több tároló használatát teszi lehetővé, ami nagyobb párhuzamosságot jelent.

### <a name="step-3-determine-total-yarn-memory"></a>3. lépés: a FONALak teljes memóriájának meghatározása

A MapReduce. job. Maps/MapReduce. job. reakcióhoz. Ez az információ a Ambari-ben érhető el. Navigáljon a FONALhoz, és tekintse meg a **konfigurációk** lapot. Ebben az ablakban a szál memóriája jelenik meg. Szorozza meg a szál memóriáját a fürtben lévő csomópontok számával a teljes fonal memóriájának lekéréséhez.

`Total YARN memory = nodes * YARN memory per node`

Ha üres fürtöt használ, a memória a fürt teljes FONALának memóriája lehet. Ha más alkalmazások használják a memóriát, akkor úgy is dönthet, hogy csak a fürt memóriájának egy részét használja. ehhez csökkentse a használni kívánt tárolók számát.

### <a name="step-4-calculate-number-of-yarn-containers"></a>4. lépés: a FONALas tárolók számának kiszámítása

A FONALas tárolók a feladatokhoz rendelkezésre álló Egyidejűség mennyiségét írják le. A fonal teljes memóriájának és osztásának elvégzése a MapReduce. map. Memory használatával.

`# of YARN containers = total YARN memory / mapreduce.map.memory`

### <a name="step-5-set-mapreducejobmapsmapreducejobreduces"></a>5. lépés: állítsa be a MapReduce. job. Maps/MapReduce. job. csökkenti

Állítsa be a MapReduce. job. Maps/MapReduce. job parancsot, és csökkentse legalább a rendelkezésre álló tárolók számát. További kísérletet tehet a mappers és a szűkítők számának növelésével, hogy megtudja, jobb teljesítményt érhet-e el. Ne feledje, hogy a további leképezések további terheléssel fognak rendelkezni, így túl sok Mapper is csökkentheti a teljesítményt.

A CPU-ütemezés és a CPU-elkülönítés alapértelmezés szerint ki van kapcsolva, így a szálak tárolóinak száma korlátozott a memóriában.

## <a name="example-calculation"></a>Példa a számításra

Tegyük fel, hogy jelenleg 8 D14-csomópontból álló fürttel rendelkezik, és egy I/O-igényes feladatot szeretne futtatni. A következő számításokat kell végrehajtania:

### <a name="step-1-determine-number-of-jobs-running"></a>1. lépés: a futó feladatok számának meghatározása

A példánkban feltételezzük, hogy a feladatoknak csak egy fut a feladata.

### <a name="step-2-set-mapreducemapmemorymapreducereducememory"></a>2. lépés: állítsa be a MapReduce. map. Memory/MapReduce. csökkentse a memóriát

Példánkban nagy I/O-igényű feladatot futtat, és úgy dönt, hogy a térképi feladatokhoz 3 GB memória elegendő.

`mapreduce.map.memory = 3GB`

### <a name="step-3-determine-total-yarn-memory"></a>3. lépés: a FONALak teljes memóriájának meghatározása

`total memory from the cluster is 8 nodes * 96GB of YARN memory for a D14 = 768GB`

### <a name="step-4-calculate--of-yarn-containers"></a>4. lépés: a FONALas tárolók számának kiszámítása

`# of YARN containers = 768 GB of available memory / 3 GB of memory = 256`

### <a name="step-5-set-mapreducejobmapsmapreducejobreduces"></a>5. lépés: állítsa be a MapReduce. job. Maps/MapReduce. job. csökkenti

`mapreduce.map.jobs = 256`

## <a name="limitations"></a>Korlátozások

**Data Lake Storage Gen1 szabályozás**

Több-bérlős szolgáltatásként Data Lake Storage Gen1 beállítja a fiók szintű sávszélesség korlátozásait. Ha eléri ezeket a korlátozásokat, megkezdheti a feladat hibáinak megtekintését. Ez a feladat naplófájljaiban lévő szabályozási hibák megfigyelésével azonosítható. Ha a feladatokhoz több sávszélességre van szüksége, vegye fel velünk a kapcsolatot.

Annak vizsgálatához, hogy a rendszer leszabályozza-e a szabályozást, engedélyeznie kell a hibakeresési naplózást az ügyfél oldalán. A következőképpen teheti meg:

1. Helyezze a következő tulajdonságot a log4j tulajdonságaiba a Ambari > FONALat > config > Advanced fonál-log4j: log4j. Logger. com. microsoft. Azure. datalake. Store = DEBUG

2. A konfiguráció érvénybe léptetéséhez indítsa újra az összes csomópontot/szolgáltatást.

3. Ha leszabályozást végez, a HTTP 429 hibakód jelenik meg a fonal naplófájljában. A fonal naplófájlja a/tmp/ &lt; felhasználói &gt; /yarn.log található.

## <a name="examples-to-run"></a>Futtatási példák

Ha szeretné bemutatni, hogy a MapReduce hogyan fut a Data Lake Storage Gen1on, az alábbiakban egy olyan mintakód található, amely egy fürtön futott a következő beállításokkal:

* 16 csomópontos D14v2
* Hadoop-fürt, amely HDI 3,6-t futtat

A kiindulási pontnál Íme néhány példa a MapReduce Teragen, a Terasort és a Teravalidate futtatására szolgáló parancsokra. Ezeket a parancsokat az erőforrások alapján módosíthatja.

### <a name="teragen"></a>Teragen

```
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 10000000000 adl://example/data/1TB-sort-input
```

### <a name="terasort"></a>Terasort

```
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 -Dmapreduce.job.reduces=512 -Dmapreduce.reduce.memory.mb=3072 adl://example/data/1TB-sort-input adl://example/data/1TB-sort-output
```

### <a name="teravalidate"></a>Teravalidate

```
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapreduce.job.maps=512 -Dmapreduce.map.memory.mb=3072 adl://example/data/1TB-sort-output adl://example/data/1TB-sort-validate
```