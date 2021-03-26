---
title: Alapértelmezett Hive-metaadattár migrálása külső metaadattár az Azure HDInsight
description: Alapértelmezett Hive-metaadattár migrálása külső metaadattár az Azure HDInsight
author: kevxmsft
ms.author: kevx
ms.reviewer: ''
ms.service: hdinsight
ms.topic: how-to
ms.date: 11/4/2020
ms.openlocfilehash: 4a0258d5e448c59baa1cd63e98058fe7116a8485
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105566115"
---
# <a name="migrate-default-hive-metastore-db-to-external-metastore-db"></a>Alapértelmezett Hive-metaadattár adatbázis migrálása külső metaadattár-ADATBÁZISba

Ez a cikk bemutatja, hogyan telepítheti át a metaadatokat egy [alapértelmezett metaadattár-adatbázisból](../hdinsight-use-external-metadata-stores.md#default-metastore) a kaptárra egy külső SQL Database a HDInsight. 

## <a name="why-migrate-to-external-metastore-db"></a>Miért érdemes áttérni a külső metaadattár-ADATBÁZISra

* Az alapértelmezett metaadattár-adatbázis az alapszintű SKU-ra korlátozódik, és nem tudja kezelni a termelési méretezési feladatokat.

* A külső metaadattár adatbázis lehetővé teszi az ügyfél számára, hogy horizontálisan méretezheti a kaptár számítási erőforrásait úgy, hogy új HDInsight-fürtöket ad hozzá, amelyek ugyanazt a metaadattár

* A HDInsight 3,6 és 4,0 közötti áttelepítéshez a struktúra-séma verziójának frissítése előtt kötelező áttelepíteni a metaadatokat a külső metaadattár-ADATBÁZISba. Lásd: [munkaterhelések áttelepítése a HDInsight 3,6-ről a HDInsight 4,0-](./apache-hive-migrate-workloads.md)ra.

Mivel az alapértelmezett metaadattár-adatbázis korlátozott számítási kapacitással rendelkezik, a metaadatok áttelepítése során ajánlott alacsony kihasználtságot kihasználni a fürt többi feladatához.

A forrás-és a célként megadott adatbázisok ugyanazt a HDInsight-verziót és ugyanazokat a Storage-fiókokat kell használniuk. Ha 3,6 és 4,0 között frissíti a HDInsight-verziókat, először hajtsa végre a jelen cikkben ismertetett lépéseket. Ezután kövesse a hivatalos [frissítés lépéseit](./apache-hive-migrate-workloads.md).

## <a name="prerequisites"></a>Előfeltételek

[Azure Data Lake Storage Gen1](../overview-data-lake-storage-gen1.md)használata esetén a kaptár-táblák helyei valószínűleg a fürt HDFS-beállításaitól függenek Azure Data Lake Storage Gen1. Futtassa a következő parancsfájl-műveletet, hogy ezeket a tárolóhelyeket más fürtökön is elvégezze. Lásd: [parancsfájl-művelet egy futó fürthöz](../hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster).

A művelet hasonló a symlinkek teljes elérési úttal való lecseréléséhez.

|Tulajdonság | Érték |
|---|---|
|Bash-parancsfájl URI-ja|`https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/hive-adl-expand-location-v01.sh`|
|Csomópont típusa (i)|Head|
|Paraméterek|""|

## <a name="migrate-with-exportimport-using-sqlpackage"></a>Migrálás exportálással/importálással a sqlpackage használatával

An méretű HDInsight a csak az 2020-10-15-as számú létrehozott fürtöt támogatja az SQL-Exportálás/-Importálás a kaptár alapértelmezett metaadattár-ADATBÁZISának használatával `sqlpackage` .

1. Telepítse a [sqlpackage](/sql/tools/sqlpackage-download#get-sqlpackage-net-core-for-linux) a fürtre.

2. A következő parancs végrehajtásával exportálja az alapértelmezett metaadattár-adatbázist a BACPAC-fájlba.

    ```bash
    wget "https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/hive_metastore_tool.py"
    SQLPACKAGE_FILE='/home/sshuser/sqlpackage/sqlpackage'  # replace with sqlpackage location
    TARGET_FILE='hive.bacpac'
    sudo python hive_metastore_tool.py --sqlpackagefile $SQLPACKAGE_FILE --targetfile $TARGET_FILE
    ```

3. Mentse a BACPAC fájlt. Az alábbiakban egy lehetőség látható.

    ```bash
    hdfs dfs -mkdir -p /bacpacs
    hdfs dfs -put $TARGET_FILE /bacpacs/
    ```

4. Importálja a BACPAC fájlt egy új adatbázisba az [itt](../../azure-sql/database/database-import.md)felsorolt lépésekkel.

5. Az új adatbázis készen áll arra, hogy [külső metaadattár adatbázisként konfigurálja az új HDInsight-fürtön](../hdinsight-use-external-metadata-stores.md#select-a-custom-metastore-during-cluster-creation).

## <a name="migrate-using-hive-script"></a>Migrálás a kaptár parancsfájl használatával

Az 2020-10-15 előtt létrehozott fürtök nem támogatják az alapértelmezett metaadattár-adatbázis exportálását/importálását.

Ilyen fürtök esetén kövesse az útmutató a [kaptárak másolása a Storage-fiókok között](./hive-migration-across-storage-accounts.md)című témakört, amely egy [külső Hive-metaadattár db](../hdinsight-use-external-metadata-stores.md#select-a-custom-metastore-during-cluster-creation)-vel rendelkező második fürtöt használ. A második fürt ugyanazt a Storage-fiókot használhatja, de új alapértelmezett fájlrendszert kell használnia.

### <a name="option-to-shallow-copy"></a>"Sekély" másolási lehetőség
A tárterület-használat megduplázódik, ha a fenti útmutató alapján a táblázatok "Deep" típusúnak lesznek másolva. A forrás Storage-tárolóban manuálisan kell megtisztítani az adatforrást.
Ehelyett "sekély" másolatot készíthetünk a táblákról, ha nem tranzakciós. A HDInsight 3,6 összes kaptár-táblázata alapértelmezés szerint nem tranzakciós, de csak a külső táblák nem tranzakciós a HDInsight 4,0-ben. A tranzakciós táblákat mélyen kell másolni. A nem tranzakciós táblázatok sekély másolásához kövesse az alábbi lépéseket:

1. Futtassa a [Hive-ddls.sh](https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/hive-ddls.sh) parancsfájlt a forrás-fürt elsődleges átjárócsomóponthoz, hogy minden struktúra-táblához létrehozza a DDL-t.
2. A DDL egy nevű helyi struktúra-parancsfájlba íródik `/tmp/hdi_hive_ddls.hql` . Hajtsa végre ezt a külső Hive-metaadattár adatbázist használó cél fürtön.

## <a name="verify-that-all-hive-tables-are-imported"></a>Annak ellenőrzése, hogy minden kaptár-tábla importálva van-e

A következő parancs egy SQL-lekérdezést használ a metaadattár DB-ben az összes struktúra-tábla és az adatterületek kinyomtatásához. Hasonlítsa össze az új és a régi fürtök közötti kimeneteket annak ellenőrzéséhez, hogy nem hiányoznak-e táblák az új metaadattár-ADATBÁZISból.

```bash
SCRIPT_FNAME='hive_metastore_tool.py'
SCRIPT="/tmp/$SCRIPT_FNAME"
wget -O "$SCRIPT" "https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/$SCRIPT_FNAME"
OUTPUT_FILE='/tmp/hivetables.csv'
QUERY="SELECT DBS.NAME, TBLS.TBL_NAME, SDS.LOCATION FROM SDS, TBLS, DBS WHERE TBLS.SD_ID = SDS.SD_ID AND TBLS.DB_ID = DBS.DB_ID ORDER BY DBS.NAME, TBLS.TBL_NAME ASC;"
sudo python "$SCRIPT" --query "$QUERY" > $OUTPUT_FILE
```

## <a name="further-reading"></a>További információ

* [Munkaterhelések áttelepíthetők a HDInsight 3,6 – 4,0](./apache-hive-migrate-workloads.md)
* [Struktúra-munkaterhelések áttelepítése a Storage-fiókok között](./hive-migration-across-storage-accounts.md)
* [Kapcsolódás a HDInsight-hez](../hadoop/connect-install-beeline.md)
* [Hiba az engedélyek létrehozásakor – tábla létrehozása](./interactive-query-troubleshoot-permission-error-create-table.md)