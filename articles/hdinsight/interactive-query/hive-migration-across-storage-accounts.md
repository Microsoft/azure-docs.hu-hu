---
title: A struktúra munkaterhelésének áttelepítése új fiókba az Azure Storage-ban
description: A struktúra munkaterhelésének áttelepítése új fiókba az Azure Storage-ban
author: kevxmsft
ms.author: kevx
ms.reviewer: ''
ms.service: hdinsight
ms.topic: how-to
ms.date: 12/11/2020
ms.openlocfilehash: 0d62bebddb7751c168ba2e487b2391a40bbc6e67
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101746104"
---
# <a name="hive-workload-migration-to-new-account-in-azure-storage"></a>A struktúra munkaterhelésének áttelepítése új fiókba az Azure Storage-ban

Megtudhatja, hogyan lehet parancsfájl-műveletek használatával a HDInsight-ben lévő tárolási fiókokba másolni a kaptár-táblákat. Ez akkor lehet hasznos, ha áttelepíti a-t [`Azure Data Lake Storage Gen2`](../hdinsight-hadoop-use-data-lake-storage-gen2.md) .

Ha manuálisan szeretne átmásolni egy különálló struktúra-táblázatot a 4,0-es HDInsight, tekintse meg az [Exportálás/Importálás struktúrát](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ImportExport).

## <a name="prerequisites"></a>Előfeltételek

* Új HDInsight-fürt a következő konfigurációkkal:
  * Az alapértelmezett fájlrendszer a célként megadott Storage-fiók. Lásd: [Az Azure Storage használata az Azure HDInsight-fürtökkel](../hdinsight-hadoop-use-blob-storage.md).
  * A fürt verziószámának meg kell egyeznie a forrás-fürttel.
  * Egy új külső Hive-metaadattár adatbázist használ. Lásd: [külső metaadat-tárolók használata](../hdinsight-use-external-metadata-stores.md#select-a-custom-metastore-during-cluster-creation).
* Egy olyan Storage-fiók, amely az eredeti és az új fürtök számára is elérhető. Lásd: [további Storage-fiókok hozzáadása a HDInsight](../hdinsight-hadoop-add-storage.md) és a [tárolási típusokhoz és szolgáltatásokhoz](../hdinsight-hadoop-compare-storage-options.md#storage-types-and-features) az engedélyezett másodlagos tárolási típusoknál.

    Íme néhány lehetőség:
  * Adja hozzá a célként megadott Storage-fiókot az eredeti fürthöz.
  * Adja hozzá az eredeti Storage-fiókot az új fürthöz.
  * Adjon hozzá egy köztes Storage-fiókot az eredeti és az új fürtökhöz is.

## <a name="how-it-works"></a>Működés

Egy parancsfájl-művelet futtatásával exportálhatja a kaptár táblákat az eredeti fürtről egy megadott HDFS könyvtárba. Lásd: [parancsfájl-művelet egy futó fürthöz](../hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster).

Ezután egy másik parancsfájl-műveletet fogunk futtatni az új fürtön a kaptár-tábláknak a HDFS könyvtárból történő importálásához.

A szkript újra létrehozza a táblákat az új fürt alapértelmezett fájlrendszerében. A natív táblák a Storage-ban is másolják az adattárakat. A nem natív táblák csak definíció alapján lesznek átmásolva. A nem natív táblák részleteit a [kaptár Storage kezelői](https://cwiki.apache.org/confluence/display/Hive/StorageHandlers) című szakaszban tekintheti meg.

A rendszer megőrzi a struktúra-tárház könyvtárában nem szereplő külső táblák elérési útját. A többi tábla a célként megadott fürt alapértelmezett struktúrájának elérési útjára fog másolni. Lásd: struktúra tulajdonságai `hive.metastore.warehouse.external.dir` és `hive.metastore.warehouse.dir` .

A parancsfájlok nem őrzik meg az egyéni fájlok engedélyeit a célként megadott fürtben.

> [!NOTE]
>
> Ez az útmutató támogatja a struktúra-adatbázisokhoz,-táblákhoz és-partíciókhoz kapcsolódó metaadat-objektumok másolását. Más metaadat-objektumokat manuálisan kell újból létrehozni.
>
> * A esetében a `Views` kaptár a `SHOW VIEWS` HDInsight 4,0-es számú kaptár 2.2.0-as parancsát támogatja. `SHOW CREATE TABLE`A nézet definíciójának használata. A kaptár korábbi verziói esetében a metaadattár SQL-adatbázis lekérdezése a nézetek megjelenítéséhez.
> * A alkalmazásban a, a `Materialized Views` és a parancsokat használja `SHOW MATERIALIZED VIEWS` `DESCRIBE FORMATTED` `CREATE MATERIALIZED VIEW` . A részletekért tekintse meg a [lényeges nézeteket](https://cwiki.apache.org/confluence/display/Hive/Materialized+views) .
> * A esetében, amely a `Constraints` HDInsight 4,0-as struktúra-2.1.0 támogatott, a használatával `DESCRIBE EXTENDED` megkötéseket listázhat a táblához, és `ALTER TABLE` a használatával korlátozásokat adhat hozzá. A részletekért lásd: az [ALTER TABLE megkötései](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-AlterTableConstraints) .

## <a name="copy-hive-tables"></a>Struktúra-táblák másolása

1. Alkalmazza az "export" szkript műveletét az eredeti fürtön a következő mezőkkel.

    Ezzel létrehozza és végrehajtja a köztes struktúra-parancsfájlokat. A rendszer a megadott értékre menti `<hdfs-export-path>` .

    Szükség esetén a használatával `--run-script=false` testreszabhatja őket a manuális végrehajtás előtt.

    |Tulajdonság | Érték |
    |---|---|
    |Bash-parancsfájl URI-ja|`https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/export-hive-data-v01.sh`|
    |Csomópont típusa (i)|Head|
    |Paraméterek|`<hdfs-export-path>` `--run-script`|

    ```sh
    usage: generate Hive export and import scripts and export Hive data to specified HDFS path
           [--run-script={true,false}]
           hdfs-export-path

    positional arguments:

        hdfs-export-path      remote HDFS directory to write export data to

    optional arguments:
        --run-script={true,false}
                            whether to execute the generated Hive export script
                            (default: true)
    ```

2. Az Exportálás sikeres befejezését követően alkalmazza az "Importálás" parancsfájl-műveletet az új fürtön a következő mezőkkel.

    |Tulajdonság | Érték |
    |---|---|
    |Bash-parancsfájl URI-ja|`https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/import-hive-data-v01.sh`|
    |Csomópont típusa (i)|Head|
    |Paraméterek|`<hdfs-export-path>`|

    ```sh
    usage: download Hive import script from specified HDFS path and execute it
           hdfs-export-path

    positional arguments:

      hdfs-export-path      remote HDFS directory to download Hive import script from

    ```

## <a name="verification"></a>Ellenőrzés

Töltse le és futtassa a parancsfájlt root felhasználóként [`hive_contents.sh`](https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/hive_contents.sh) az egyes fürtök elsődleges csomópontján, és hasonlítsa össze a kimeneti fájl tartalmát `/tmp/hive_contents.out` . Lásd: [Kapcsolódás HDInsight (Apache Hadoop) SSH használatával](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="cleanup-additional-storage-usage"></a>További tárterület-használat tisztítása

A tárterület-áttelepítés befejezése és ellenőrzése után a megadott HDFS exportálási útvonalon törölheti az adatkészletet.

A beállítás az HDFS parancs használata `hdfs dfs -rm -R` .

## <a name="option-reduce-additional-storage-usage"></a>Lehetőség: további tárterület-használat csökkentése

Az exportálási parancsfájl művelete valószínűleg megduplázza a tárterület használatát a struktúra miatt. Azonban lehetséges a további tárterület-használat korlátozása manuálisan, egy adatbázis vagy egy tábla egyszerre történő áttelepítésével.

1. Itt adhatja meg `--run-script=false` , hogy ki kell-e hagyni a létrehozott struktúra-parancsfájl végrehajtását. A struktúra exportálási és importálási parancsfájljai továbbra is az exportálási útvonalra lesznek mentve.

2. Az exportálási és importálási parancsfájlok adatbázis-adatbázis vagy táblázat szerinti létrehozása, valamint az összes áttelepített adatbázis vagy tábla után az exportálási útvonal manuális törlése.

## <a name="next-steps"></a>Következő lépések

* [Azure Data Lake Storage Gen2](../hdinsight-hadoop-use-data-lake-storage-gen2.md)
* [Külső metaadat-tárolók használata](../hdinsight-use-external-metadata-stores.md#select-a-custom-metastore-during-cluster-creation)
* [Tárolási típusok és szolgáltatások](../hdinsight-hadoop-compare-storage-options.md#storage-types-and-features)
* [Parancsfájl művelete futó fürthöz](../hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster)
