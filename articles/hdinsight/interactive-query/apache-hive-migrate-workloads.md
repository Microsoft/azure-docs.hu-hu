---
title: Az Azure HDInsight 3,6 kaptár számítási feladatait áttelepítheti HDInsight 4,0
description: Megtudhatja, hogyan telepítheti át Apache Hive számítási feladatait a 3,6-es HDInsight a HDInsight 4,0-ra.
author: kevxmsft
ms.author: kevx
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 11/4/2020
ms.openlocfilehash: b13e8e088eff95071247a53ad1a4a18879f94053
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101742194"
---
# <a name="migrate-azure-hdinsight-36-hive-workloads-to-hdinsight-40"></a>Az Azure HDInsight 3,6 kaptár számítási feladatait áttelepítheti HDInsight 4,0

A HDInsight 4,0 számos előnnyel jár a HDInsight 3,6. Az alábbiakban [áttekintjük a HDInsight 4,0 újdonságait](../hdinsight-version-release.md).

Ez a cikk a kaptárak számítási feladatainak 3,6 – 4,0 HDInsight való átirányításának lépéseit ismerteti, beleértve a következőket:

* Hive-metaadattár másolás és séma frissítése
* Biztonságos Migrálás a savas kompatibilitáshoz
* A struktúra biztonsági házirendjeinek megőrzése

Az új és a régi HDInsight-fürtöknek hozzáféréssel kell rendelkezniük ugyanahhoz a Storage-fiókhoz.

A kaptár-táblák új Storage-fiókba való áttelepítését külön lépésként kell elvégezni. Lásd: a [struktúra áttelepítése a Storage-fiókok között](./hive-migration-across-storage-accounts.md).

## <a name="steps-to-upgrade"></a>A frissítés lépései

### <a name="1-prepare-the-data"></a>1. az adatgyűjtés előkészítése

* A HDInsight 3,6 alapértelmezés szerint nem támogatja a savas táblákat. Ha a savas táblák jelen vannak, azonban a "MAJOR" tömörítést kell futtatni rajtuk. A tömörítéssel kapcsolatos részletekért tekintse meg a [kaptár nyelvi útmutatóját](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-AlterTable/Partition/Compact) .

* [Azure Data Lake Storage Gen1](../overview-data-lake-storage-gen1.md)használata esetén a kaptár-tábla helyei valószínűleg a fürt HDFS-beállításaitól függenek. Futtassa a következő parancsfájl-műveletet, hogy ezeket a tárolóhelyeket más fürtökön is elvégezze. Lásd: [parancsfájl-művelet egy futó fürthöz](../hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster).

    |Tulajdonság | Érték |
    |---|---|
    |Bash-parancsfájl URI-ja|`https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/hive-adl-expand-location-v01.sh`|
    |Csomópont típusa (i)|Head|
    |Paraméterek||

### <a name="2-copy-the-sql-database"></a>2. az SQL-adatbázis másolása

* Ha a fürt alapértelmezett Hive-metaadattár használ, kövesse ezt az [útmutatót](./hive-default-metastore-export-import.md) a metaadatok külső metaadattár való exportálásához. Ezután hozzon létre egy másolatot a külső metaadattár a frissítéshez.

* Ha a fürt külső Hive-metaadattár használ, hozzon létre egy másolatot. A lehetőségek közé tartozik az [Exportálás/Importálás](../../azure-sql/database/database-export.md) és [az időponthoz való visszaállítás](../../azure-sql/database/recovery-using-backups.md#point-in-time-restore).

### <a name="3-upgrade-the-metastore-schema"></a>3. a metaadattár séma frissítése

Ez a lépés a [`Hive Schema Tool`](https://cwiki.apache.org/confluence/display/Hive/Hive+Schema+Tool) HDInsight 4,0-es verziójával frissíti a metaadattár sémát.

> [!Warning]
> Ez a lépés nem vonható vissza. Ezt csak a metaadattár egy példányán futtassa.

1. Hozzon létre egy ideiglenes HDInsight 4,0-fürtöt az 4,0-struktúra eléréséhez `schematool` . Ehhez a lépéshez használhatja az [alapértelmezett Hive-metaadattár](../hdinsight-use-external-metadata-stores.md#default-metastore) .

1. A HDInsight 4,0-fürtről futtassa a következőt `schematool` : a cél HDInsight 3,6 metaadattár:

    ```sh
    SERVER='servername.database.windows.net'  # replace with your SQL Server
    DATABASE='database'  # replace with your 3.6 metastore SQL Database
    USERNAME='username'  # replace with your 3.6 metastore username
    PASSWORD='password'  # replace with your 3.6 metastore password
    STACK_VERSION=$(hdp-select status hive-server2 | awk '{ print $3; }')
    /usr/hdp/$STACK_VERSION/hive/bin/schematool -upgradeSchema -url "jdbc:sqlserver://$SERVER;databaseName=$DATABASE;trustServerCertificate=false;encrypt=true;hostNameInCertificate=*.database.windows.net;" -userName "$USERNAME" -passWord "$PASSWORD" -dbType "mssql" --verbose
    ```

    > [!NOTE]
    > Ez a segédprogram ügyfél használatával `beeline` hajtja végre az SQL-parancsfájlokat a alkalmazásban `/usr/hdp/$STACK_VERSION/hive/scripts/metastore/upgrade/mssql/upgrade-*.mssql.sql` .
    >
    > A parancsfájlok SQL-szintaxisa nem feltétlenül kompatibilis más ügyféleszközök. Az [Azure Portalon](../../azure-sql/database/connect-query-portal.md) az [SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) és a Query Editor használatához például `GO` minden parancs után kulcsszó szükséges.
    >
    > Ha az erőforrás kapacitása vagy a tranzakció időtúllépése miatt bármelyik parancsfájl meghibásodik, a SQL Database vertikális felskálázása.

1. Ellenőrizze a végleges verziót a lekérdezéssel `select schema_version from dbo.version` .

    A kimenetnek egyeznie kell a következő bash-parancsnak a HDInsight 4,0-fürtből.

    ```bash
    grep . /usr/hdp/$(hdp-select --version)/hive/scripts/metastore/upgrade/mssql/upgrade.order.mssql | tail -n1 | rev | cut -d'-' -f1 | rev
    ```

1. Törölje az ideiglenes HDInsight 4,0-fürtöt.

### <a name="4-deploy-a-new-hdinsight-40-cluster"></a>4. új HDInsight 4,0-fürt üzembe helyezése

Hozzon létre egy új HDInsight 4,0-fürtöt, és [válassza ki a frissített Hive-metaadattár](../hdinsight-use-external-metadata-stores.md#select-a-custom-metastore-during-cluster-creation) és ugyanazokat a Storage-fiókokat.

* Az új fürthöz nem szükséges azonos alapértelmezett fájlrendszer.

* Ha a metaadattár több Storage-fiókban található táblákat tartalmaz, ezeket a táblák eléréséhez hozzá kell adnia ezeket a Storage-fiókokat az új fürthöz. Lásd: [további Storage-fiókok hozzáadása a HDInsight](../hdinsight-hadoop-add-storage.md).

* Ha a kaptár-feladatok tárolási megközelíthetetlenség miatt sikertelenek, ellenőrizze, hogy a tábla helye a fürthöz hozzáadott Storage-fiókban található-e.

    A táblázat helyének azonosításához használja a következő struktúra-parancsot:

    ```sql
    SHOW CREATE TABLE ([db_name.]table_name|view_name);
    ```

### <a name="5-convert-tables-for-acid-compliance"></a>5. a táblák konvertálása a savas megfelelőséghez

A felügyelt tábláknak sav-kompatibilisnek kell lenniük a HDInsight 4,0-es verziójában. `strictmanagedmigration`A HDInsight 4,0 futtatásával a nem savas felügyelt táblákat a tulajdonsággal rendelkező külső táblákra konvertálhatja `'external.table.purge'='true'` . Végrehajtás a átjárócsomóponthoz:

```bash
sudo su - hive
STACK_VERSION=$(hdp-select status hive-server2 | awk '{ print $3; }')
/usr/hdp/$STACK_VERSION/hive/bin/hive --config /etc/hive/conf --service strictmanagedmigration --hiveconf hive.strict.managed.tables=true -m automatic --modifyManagedTables
```

## <a name="secure-hive-across-hdinsight-versions"></a>Biztonsági struktúra HDInsight-verziókon keresztül

A HDInsight opcionálisan integrálható Azure Active Directory az HDInsight Enterprise Security Package (ESP) használatával. Az ESP a Kerberos és az Apache Ranger használatával kezeli a fürtön belüli adott erőforrások engedélyeit. A HDInsight 3,6-es struktúrában üzembe helyezett Ranger-házirendek a következő lépésekkel telepíthetők át a HDInsight 4,0-re:

1. Navigáljon a Ranger Service Manager panelre a HDInsight 3,6-fürtben.
2. Navigáljon a **struktúra** nevű szabályzathoz, és exportálja a szabályzatot egy JSON-fájlba.
3. Győződjön meg arról, hogy az exportált házirend JSON-ban hivatkozott összes felhasználó létezik az új fürtben. Ha a felhasználó a szabályzat JSON-ban szerepel, de nem létezik az új fürtben, adja hozzá a felhasználót az új fürthöz, vagy távolítsa el a hivatkozást a szabályzatból.
4. Navigáljon a **Ranger Service Manager** panelre a HDInsight 4,0-fürtben.
5. Navigáljon a **kaptár** nevű szabályzathoz, és importálja a Ranger Policy JSON-t a 2. lépésből.

## <a name="hive-changes-in-hdinsight-40-that-may-require-application-changes"></a>A HDInsight 4,0-es verziójának változása szükségessé teheti az alkalmazások módosítását

* A metaadattár a Spark és a kaptár között a savas táblákhoz való megosztásához tekintse meg a [további konfigurációt a kaptár Warehouse Connector használatával](./apache-hive-warehouse-connector.md) .

* A HDInsight 4,0 [Storage-alapú hitelesítést](https://cwiki.apache.org/confluence/display/Hive/Storage+Based+Authorization+in+the+Metastore+Server)használ. Ha módosítja a fájl engedélyeit, vagy más felhasználóként hoz létre mappákat, mint a kaptár, a rendszer valószínűleg a tárolási engedélyek alapján észleli a struktúra hibáit. A javításhoz engedélyezze `rw-` a hozzáférést a felhasználóhoz. Lásd: [HDFS-engedélyek útmutatója](https://hadoop.apache.org/docs/r2.7.1/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html).

* `HiveCLI` helyébe a `Beeline` .

További változásokért tekintse meg a [HDInsight 4,0 közleményt](../hdinsight-version-release.md) .

## <a name="further-reading"></a>További információ

* [HDInsight 4,0 közlemény](../hdinsight-version-release.md)
* [HDInsight 4,0 Deep Dive](https://azure.microsoft.com/blog/deep-dive-into-azure-hdinsight-4-0/)
* [3. struktúra – savas táblák](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.1.0/using-hiveql/content/hive_3_internals.html)
