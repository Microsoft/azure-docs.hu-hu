---
title: Az Adatmásolás Azure Data Lake Storage Gen2 a DistCp használatával | Microsoft Docs
description: Adatok másolása Azure Data Lake Storage Gen2ba és onnan a Apache Hadoop Distributed Copy Tool (DistCp) használatával.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 12/06/2018
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: e69a97a86a357fb36dde572f292b5cac7963d14a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95912484"
---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-azure-data-lake-storage-gen2"></a>Az DistCp használata az Azure Storage-blobok és a Azure Data Lake Storage Gen2 közötti adatmásoláshoz

Az [DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) használatával az általános célú v2 Storage-fiók és az általános célú v2 Storage-fiók közötti Adatmásolást engedélyezheti a hierarchikus névtérrel. Ez a cikk útmutatást nyújt a DistCp eszköz használatáról.

A DistCp számos parancssori paramétert biztosít, és nyomatékosan javasoljuk, hogy olvassa el ezt a cikket a használatának optimalizálása érdekében. Ez a cikk alapszintű funkciókat mutat be, miközben az adatoknak a hierarchikus névtérben engedélyezett fiókba való másolására koncentrál.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* Egy meglévő Azure Storage-fiók, amelyhez nincs engedélyezve Data Lake Storage Gen2 képesség (hierarchikus névtér).
* Egy Azure Storage-fiók, amelyen engedélyezve van a Data Lake Storage Gen2 képességek (hierarchikus névtér). A létrehozásával kapcsolatos útmutatásért lásd: [Azure Storage-fiók létrehozása](../common/storage-account-create.md)
* A Storage-fiókban a hierarchikus névtérrel létrehozott tároló.
* Egy Azure HDInsight-fürt, amely hozzáféréssel rendelkezik egy olyan Storage-fiókhoz, amelyen engedélyezve van a hierarchikus névtér funkció. Lásd: [Azure Data Lake Storage Gen2 használata az Azure HDInsight-fürtökkel](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). Győződjön meg arról, hogy engedélyezi Távoli asztal a fürt számára.

## <a name="use-distcp-from-an-hdinsight-linux-cluster"></a>DistCp használata egy HDInsight Linux-fürtből

An méretű HDInsight-fürthöz tartozik a DistCp segédprogram, amely az adatok különböző forrásokból egy HDInsight-fürtbe való másolására használható. Ha úgy állította be a HDInsight-fürtöt, hogy az Azure Blob Storaget használja, és Azure Data Lake Storage együtt, akkor a DistCp segédprogram azonnal használható az adatmásoláshoz is. Ebben a szakaszban a DistCp segédprogram használatát tekintjük át.

1. Hozzon létre egy SSH-munkamenetet a HDI-fürthöz. Lásd: [Kapcsolódás Linux-alapú HDInsight-fürthöz](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

2. Ellenőrizze, hogy el tudja-e érni meglévő általános célú v2-fiókját (a hierarchikus névtér engedélyezése nélkül).

    ```bash
    hdfs dfs –ls wasbs://<container-name>@<storage-account-name>.blob.core.windows.net/
    ```

   A kimenetnek meg kell adnia a tároló tartalmának listáját.

3. Hasonlóképpen ellenőrizze, hogy elérhető-e a Storage-fiók a fürtön engedélyezett hierarchikus névtérrel. Futtassa az alábbi parancsot:

    ```bash
    hdfs dfs -ls abfss://<container-name>@<storage-account-name>.dfs.core.windows.net/
    ```

    A kimenetnek meg kell adnia a fájlok/mappák listáját a Data Lake Storage-fiókban.

4. A DistCp használatával másolhatja át a WASB adatait egy Data Lake Storage-fiókba.

    ```bash
    hadoop distcp wasbs://<container-name>@<storage-account-name>.blob.core.windows.net/example/data/gutenberg abfss://<container-name>@<storage-account-name>.dfs.core.windows.net/myfolder
    ```

    A parancs átmásolja a **/example/Data/Gutenberg/** mappa tartalmát a blob Storage-ban a Data Lake Storage fiókban lévő **/MyFolder** .

5. Hasonlóképpen, a DistCp használatával másolhatja át Data Lake Storage fiók adatait a Blob Storageba (WASB).

    ```bash
    hadoop distcp abfss://<container-name>@<storage-account-name>.dfs.core.windows.net/myfolder wasbs://<container-name>@<storage-account-name>.blob.core.windows.net/example/data/gutenberg
    ```

    A parancs átmásolja a **/MyFolder** tartalmát a Data Lake Store fiókban a WASB **/example/Data/Gutenberg/** mappájába.

## <a name="performance-considerations-while-using-distcp"></a>Teljesítménnyel kapcsolatos megfontolások a DistCp használata során

Mivel a DistCp legalacsonyabb részletessége egyetlen fájl, az egyidejű másolatok maximális számának beállítása a legfontosabb paraméter a Data Lake Storage optimalizálásához. Az egyidejű másolatok száma egyenlő a parancssorban található mappers (**m**) paraméterek számával. Ez a paraméter határozza meg az adatmásoláshoz használt leképezések maximális számát. Az alapértelmezett érték 20.

**Példa**

```bash
hadoop distcp -m 100 wasbs://<container-name>@<storage-account-name>.blob.core.windows.net/example/data/gutenberg abfss://<container-name>@<storage-account-name>.dfs.core.windows.net/myfolder
```

### <a name="how-do-i-determine-the-number-of-mappers-to-use"></a>Hogyan határozza meg a használni kívánt leképezések számát?

Az alábbiakban olvashat némi útmutatást ezzel kapcsolatban.

* **1. lépés: a "default" szál-alkalmazási várólista számára elérhető teljes memória meghatározása** – az első lépés az "alapértelmezett" fonal-alkalmazási várólista számára elérhető memória meghatározása. Ezek az információk a fürthöz társított Ambari-portálon érhetők el. Keresse meg a FONALat, és tekintse meg a konfigurációk lapot, és tekintse meg az "alapértelmezett" alkalmazás-várólista számára elérhető fonal-memóriát. Ez a teljes rendelkezésre álló memória a DistCp-feladatokhoz (amely valójában egy MapReduce-feladatokhoz).

* **2. lépés: a leképezések számának kiszámítása** – az **m** érték egyenlő a fonalas tároló méretének hányadosával. A FONALak tárolójának mérete információi a Ambari portálon is elérhetők. Navigáljon a FONALhoz, és tekintse meg a konfigurációk lapot. Ebben az ablakban a szál tárolójának mérete jelenik meg. A leképezések (**m**) számának megérkezésére szolgáló egyenlet

    m = (a csomópontok száma * a szálak memóriája az egyes csomópontokhoz)/FONALak tárolójának mérete

**Példa**

Tegyük fel, hogy egy 4x D14v2s-fürttel rendelkezik, és 10 TB-nyi adat átvitelét kísérli meg 10 különböző mappából. Az egyes mappák különböző mennyiségű adattal rendelkeznek, és az egyes mappákban lévő fájlméretek eltérőek.

* **Összes szál memóriája**: a Ambari-portálon azt állapítja meg, hogy a szál memóriája 96 GB D14-csomóponthoz. Tehát a négy csomópontos fürthöz tartozó összes szál memóriája a következő: 

    FONAL memória = 4 * 96GB = 384GB

* **Hozzárendelések száma**: a Ambari-portálon azt állapítja meg, hogy a szál tárolójának mérete 3 072 MB a D14-fürt csomópontjaihoz. Így a leképezések száma:

    m = (4 csomópont * 96GB)/3072MB = 128 mappers

Ha más alkalmazások használják a memóriát, akkor dönthet úgy, hogy csak a fürthöz tartozó fonal-memória egy részét használja a DistCp.

### <a name="copying-large-datasets"></a>Nagyméretű adatkészletek másolása

Ha az áthelyezni kívánt adatkészlet mérete nagy (például >1 TB), vagy ha sok különböző mappával rendelkezik, érdemes lehet több DistCp-feladatot használni. Valószínűleg nincs teljesítménybeli nyereség, de kiterjeszti a feladatokat úgy, hogy ha bármilyen feladat meghiúsul, csak a teljes feladat helyett újra kell indítania az adott feladatot.

### <a name="limitations"></a>Korlátozások

* A DistCp olyan leképezéseket próbál létrehozni, amelyek mérete hasonló a teljesítmény optimalizálása érdekében. Előfordulhat, hogy a leképezések számának növelése nem mindig növeli a teljesítményt.

* A DistCp csak egy Mapper-fájlra korlátozódik. Ezért a fájlokhoz nem tartozhat több mapper. Mivel a DistCp csak egy leképezést rendelhet egy fájlhoz, ez korlátozza a nagy fájlok másolásához használható Egyidejűség mennyiségét.

* Ha kis számú nagyméretű fájllal rendelkezik, akkor a rendszer 256 MB-nyi adattömbökre osztja fel őket, hogy több potenciális párhuzamosságot biztosítson.