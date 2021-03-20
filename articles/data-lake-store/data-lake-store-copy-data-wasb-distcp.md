---
title: Adatok másolása a WASB-ba és a Azure Data Lake Storage Gen1 a DistCp használatával
description: Adatok másolása az Azure Storage-blobokból és az Azure Data Lake Storage Gen1ba az DistCp eszköz használatával
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 01/03/2020
ms.author: twooley
ms.openlocfilehash: c608f357eb1eff9fd36e583b98d26250a71cb923
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "85515673"
---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-azure-data-lake-storage-gen1"></a>Az DistCp használata az Azure Storage-blobok és a Azure Data Lake Storage Gen1 közötti adatmásoláshoz

> [!div class="op_single_selector"]
> * [A DistCp használata](data-lake-store-copy-data-wasb-distcp.md)
> * [Az AdlCopy használata](data-lake-store-copy-data-azure-storage-blob.md)
>
>

Ha olyan HDInsight-fürttel rendelkezik, amely Azure Data Lake Storage Gen1hoz fér hozzá, használhatja a Hadoop ökoszisztéma-eszközöket, például a DistCp-t, hogy egy HDInsight-fürt tárterületére (WASB) másolja az adatait egy Data Lake Storage Gen1-fiókba. Ez a cikk bemutatja, hogyan használhatja a DistCp eszközt.

## <a name="prerequisites"></a>Előfeltételek

* **Egy Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* **Egy Azure Data Lake Storage Gen1-fiók**. A létrehozásával kapcsolatos útmutatásért tekintse meg a [Azure Data Lake Storage Gen1 első lépései](data-lake-store-get-started-portal.md)című témakört.
* **Azure HDInsight-fürt** Data Lake Storage Gen1 fiókhoz való hozzáféréssel. Lásd: [HDInsight-fürt létrehozása Data Lake Storage Gen1sal](data-lake-store-hdinsight-hadoop-use-portal.md). Győződjön meg arról, hogy engedélyezi Távoli asztal a fürt számára.

## <a name="use-distcp-from-an-hdinsight-linux-cluster"></a>DistCp használata egy HDInsight Linux-fürtből

An méretű HDInsight-fürthöz tartozik a DistCp eszköz, amely különböző forrásokból származó adatok egy HDInsight-fürtbe való másolására használható. Ha úgy állította be a HDInsight-fürtöt, hogy a Data Lake Storage Gen1 további tárterületként használja, akkor a DistCp használatával az adatok átmásolhatók egy Data Lake Storage Gen1-fiókba és onnan. Ebben a szakaszban a DistCp eszköz használatát tekintjük át.

1. Az asztalról az SSH használatával csatlakozhat a fürthöz. Lásd: [Kapcsolódás Linux-alapú HDInsight-fürthöz](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md). Futtassa a parancsokat az SSH-parancssorból.

1. Ellenőrizze, hogy el tudja-e érni az Azure Storage-blobokat (WASB). Futtassa az alábbi parancsot:

   ```
   hdfs dfs –ls wasb://<container_name>@<storage_account_name>.blob.core.windows.net/
   ```

   A kimenet a Storage-blobban található tartalmak listáját tartalmazza.

1. Hasonlóképpen ellenőrizze, hogy el tudja-e érni a Data Lake Storage Gen1 fiókot a fürtből. Futtassa az alábbi parancsot:

   ```
   hdfs dfs -ls adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/
   ```

    A kimenet a Data Lake Storage Gen1 fiókban található fájlok és mappák listáját tartalmazza.

1. A DistCp használatával másolhatja át a WASB adatait egy Data Lake Storage Gen1-fiókba.

   ```
   hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder
   ```

    A parancs átmásolja a **/example/Data/Gutenberg/** mappa tartalmát a WASB-ben a Data Lake Storage Gen1-fiókban lévő **/MyFolder** -be.

1. Hasonlóképpen, a DistCp használatával másolhatja az adatok egy Data Lake Storage Gen1-fiókból a WASB.

   ```
   hadoop distcp adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg
   ```

    A parancs átmásolja a **/MyFolder** tartalmát a Data Lake Storage Gen1 fiókban a WASB **/example/Data/Gutenberg/** mappájába.

## <a name="performance-considerations-while-using-distcp"></a>Teljesítménnyel kapcsolatos megfontolások a DistCp használata során

Mivel a DistCp eszköz legalacsonyabb részletessége egyetlen fájl, az egyidejű másolatok maximális számának beállítása a legfontosabb paraméter a Data Lake Storage Gen1 optimalizálásához. Az egyidejű másolatok számát beállíthatja a parancssorban lévő mappers (am) paraméterek számának beállításával. Ez a paraméter határozza meg az adatmásoláshoz használt leképezések maximális számát. Az alapértelmezett érték 20.

Példa:

```
 hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder -m 100
```

### <a name="how-to-determine-the-number-of-mappers-to-use"></a>A használandó leképezések számának meghatározása

Az alábbiakban olvashat némi útmutatást ezzel kapcsolatban.

* **1. lépés: a fonal teljes memóriájának meghatározása** – az első lépés a DistCp-feladatot futtató fürt számára elérhető fonal-memória meghatározása. Ezek az információk a fürthöz társított Ambari-portálon érhetők el. Keresse meg a FONALat, és tekintse meg a **konfigurációk** lapot a szál memóriájának megtekintéséhez. A FONALak teljes memóriájának lekéréséhez szorozza meg a szál memóriáját a fürtben található csomópontok számával.

* **2. lépés: a leképezések számának kiszámítása** – az **m** érték egyenlő a fonalas tároló méretének hányadosával. A FONALak tárolójának mérete információ a Ambari-portálon is elérhető. Navigáljon a FONALhoz, és tekintse meg a **konfigurációk** lapot. Ebben az ablakban a szál tárolójának mérete jelenik meg. A leképezések (**m**) számának megérkezésére szolgáló egyenlet a következő:

   `m = (number of nodes * YARN memory for each node) / YARN container size`

Példa:

Tegyük fel, hogy négy D14v2s-csomóponttal rendelkezik a fürtben, és 10 TB-nyi adat átvitelét szeretné 10 különböző mappából. Az egyes mappák különböző mennyiségű adattal rendelkeznek, és az egyes mappákban lévő fájlméretek eltérőek.

* Összes szál memóriája – a Ambari-portálon azt állapítja meg, hogy a szál memóriája 96 GB D14-csomóponthoz. Tehát a négy csomópontos fürthöz tartozó összes szál memóriája a következő:

   `YARN memory = 4 * 96GB = 384GB`

* Hozzárendelések száma – a Ambari-portálon azt állapítja meg, hogy a szál tárolójának mérete 3072 D14 fürtcsomópont esetén. Így a leképezések száma a következőket teszi:

   `m = (4 nodes * 96GB) / 3072MB = 128 mappers`

Ha más alkalmazások használják a memóriát, dönthet úgy, hogy csak a fürt DistCp tartozó szál-memóriájának egy részét használja.

### <a name="copying-large-datasets"></a>Nagyméretű adatkészletek másolása

Ha az áthelyezni kívánt adatkészlet mérete nagy (például > 1 TB), vagy ha sok különböző mappával rendelkezik, érdemes lehet több DistCp-feladatot használni. Valószínűleg nincs teljesítménybeli nyereség, de kiterjeszti a feladatokat, hogy ha bármilyen feladat meghiúsul, akkor a teljes feladat helyett csak az adott feladatot kell újraindítani.

### <a name="limitations"></a>Korlátozások

* A DistCp olyan leképezéseket próbál létrehozni, amelyek mérete hasonló a teljesítmény optimalizálása érdekében. Előfordulhat, hogy a leképezések számának növelése nem mindig növeli a teljesítményt.

* A DistCp csak egy Mapper-fájlra korlátozódik. Ezért a fájlokhoz nem tartozhat több mapper. Mivel a DistCp csak egy leképezést rendelhet egy fájlhoz, ez korlátozza a nagyméretű fájlok másolásához használható Egyidejűség mennyiségét.

* Ha kis számú nagyméretű fájllal rendelkezik, ossza fel őket 256 MB-nyi adattömbökbe, hogy több potenciális párhuzamosságot biztosítson.

* Ha Azure Blob Storage-fiókból másol, a másolási feladatot a blob Storage oldalán is szabályozhatja. Ez csökkenti a másolási feladatok teljesítményét. Ha többet szeretne megtudni az Azure Blob Storage korlátairól, tekintse meg az Azure Storage korlátozásait az [Azure-előfizetés és a szolgáltatás korlátainál](../azure-resource-manager/management/azure-subscription-service-limits.md).

## <a name="see-also"></a>Lásd még

* [Adatok másolása az Azure Storage-blobokból a Data Lake Storage Gen1ba](data-lake-store-copy-data-azure-storage-blob.md)
* [Az adatok védelme az 1. generációs Data Lake Storage-ban](data-lake-store-secure-data.md)
* [Azure Data Lake Analytics használata a Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Az Azure HDInsight használata Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
