---
title: Az Azure HDInsight 3,6 kaptár számítási feladatait áttelepítheti HDInsight 4,0
description: Megtudhatja, hogyan telepítheti át Apache Hive számítási feladatait a 3,6-es HDInsight a HDInsight 4,0-ra.
author: msft-tacox
ms.author: tacox
ms.service: hdinsight
ms.topic: how-to
ms.date: 11/13/2019
ms.openlocfilehash: 93dc565055c6eb413a0c277a9891e5fcfab50345
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98941356"
---
# <a name="migrate-azure-hdinsight-36-hive-workloads-to-hdinsight-40"></a>Az Azure HDInsight 3,6 kaptár számítási feladatait áttelepítheti HDInsight 4,0

Ez a dokumentum bemutatja, hogyan telepítheti át Apache Hive és LLAP számítási feladatait a HDInsight 3,6 HDInsight 4,0-es verzióra. A HDInsight 4,0 újabb kaptár-és LLAP funkciókat biztosít, mint például a jelentős nézetek és a lekérdezési eredmények gyorsítótárazása. Ha a számítási feladatokat a HDInsight 4,0-re telepíti át, a 3. struktúra számos újabb funkcióját használhatja, amelyek nem érhetők el a HDInsight 3,6-on.

Ez a cikk a következő témákat tárgyalja:

* A kaptár metaadatainak áttelepítése a HDInsight 4,0-re
* SAVAS és nem savas táblák biztonságos áttelepítése
* A struktúra biztonsági házirendjének megőrzése HDInsight-verziókon keresztül
* Lekérdezés végrehajtása és hibakeresése a HDInsight 3,6 és a HDInsight 4,0 között

A kaptár egyik előnye, hogy lehetővé teszi a metaadatok exportálását egy külső adatbázisba (ez a struktúra Metaadattár néven ismert). A **Metaadattár struktúra** feladata a táblák statisztikáinak tárolása, beleértve a tábla tárolási helyét, az oszlopnevek és a tábla indexét. A HDInsight 3,6 és a HDInsight 4,0 különböző metaadattár sémákat igényel, és nem oszthat meg egyetlen metaadattár. A Hive-metaadattár biztonságos verziófrissítésének ajánlott módja, ha az aktuális éles környezetben nem az eredeti példányt frissíti. Ez a dokumentum megköveteli, hogy az eredeti és az új fürtök hozzáférhessenek ugyanahhoz a Storage-fiókhoz. Ezért nem fedi le az adatáttelepítést egy másik régióba.

## <a name="migrate-from-external-metastore"></a>Migrálás külső metaadattár

### <a name="1-run-major-compaction-on-acid-tables-in-hdinsight-36"></a>1. a nagy tömörítés futtatása savas táblákon a HDInsight 3,6

A HDInsight 3,6 és a HDInsight 4,0 ACID táblázatok eltérően értelmezik a savas különbözeteket. Az áttelepítés előtt csak a "MAJOR" tömörítést kell végrehajtani az 3,6-fürt minden egyes savas táblájánál. A tömörítéssel kapcsolatos részletekért tekintse meg a [kaptár nyelvi útmutatóját](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-AlterTable/Partition/Compact) .

### <a name="2-copy-sql-database"></a>2. az SQL Database másolása
Hozzon létre egy új másolatot a külső metaadattár. Ha külső metaadattár használ, az egyik biztonságos és egyszerű módszer a metaadattár másolására, hogy a függvény használatával egy másik néven [állítsa vissza az adatbázist](../../azure-sql/database/recovery-using-backups.md#point-in-time-restore) `RESTORE` .  További információ a külső metaadattár HDInsight-fürthöz való csatolásáról: [külső metaadat-tárolók használata az Azure HDInsight](../hdinsight-use-external-metadata-stores.md) .

### <a name="3-upgrade-metastore-schema"></a>3. a metaadattár séma frissítése
A metaadattár **másolásának** befejeződése után futtasson egy [séma-frissítési parancsfájlt a meglévő](../hdinsight-hadoop-customize-cluster-linux.md) HDInsight 3,6-fürtön az új metaadattár a kaptár 3 sémára való frissítéséhez. (Ehhez a lépéshez nem szükséges, hogy az új metaadattár csatlakozni lehessen egy fürthöz.) Ez lehetővé teszi, hogy az adatbázis HDInsight 4,0 metaadattár legyen csatolva.

Használja az alábbi táblázatban szereplő értékeket. Cserélje le a `SQLSERVERNAME DATABASENAME USERNAME PASSWORD` értéket a Hive-metaadattár **Másolás** megfelelő értékeire, szóközzel elválasztva. Ne adja meg a ". database.windows.net" kifejezést az SQL Server nevének megadásakor.

|Tulajdonság | Érték |
|---|---|
|Parancsfájl típusa|– Egyéni|
|Name|Struktúra frissítése|
|Bash-parancsfájl URI-ja|`https://hdiconfigactions.blob.core.windows.net/hivemetastoreschemaupgrade/launch-schema-upgrade.sh`|
|Csomópont típusa (i)|Head|
|Paraméterek|SQLSERVERNAME DATABASENAME FELHASZNÁLÓNÉV JELSZAVA|

> [!Warning]  
> A HDInsight 3,6 metaadat-sémát a HDInsight 4,0 sémába konvertáló frissítés nem vonható vissza.

A frissítés ellenőrzéséhez futtassa a következő SQL-lekérdezést az adatbázison:

```sql
select * from dbo.version
```

### <a name="4-deploy-a-new-hdinsight-40-cluster"></a>4. új HDInsight 4,0-fürt üzembe helyezése

1. A frissített metaadattár határozza meg az új fürt Hive-metaadattár.

1. A táblákból származó tényleges adatok azonban nem érhetők el, amíg a fürt nem fér hozzá a szükséges Storage-fiókokhoz.
Győződjön meg arról, hogy a HDInsight 3,6-fürtben a kaptár Tables Storage-fiókok az új HDInsight 4,0-fürt elsődleges vagy másodlagos tárolási fiókjaiként vannak megadva.
További információ a Storage-fiókok HDInsight-fürtökhöz való hozzáadásáról: [további Storage-fiókok hozzáadása a HDInsight](../hdinsight-hadoop-add-storage.md).

### <a name="5-complete-migration-with-a-post-upgrade-tool-in-hdinsight-40"></a>5. a HDInsight 4,0-es frissítés utáni eszközzel végzett áttelepítés befejezése

A felügyelt tábláknak a HDInsight 4,0-es, alapértelmezés szerint sav-kompatibilisnek kell lenniük. Miután elvégezte a metaadattár áttelepítését, futtasson egy verziófrissítés utáni eszközt, hogy a korábban nem savas felügyelt táblák kompatibilisek legyenek a HDInsight 4,0-fürttel. Ez az eszköz a következő átalakítást fogja alkalmazni:

|3,6 |4,0 |
|---|---|
|Külső táblák|Külső táblák|
|Nem savas felügyelt táblák|Külső táblák a következő tulajdonsággal: "External. table. Purge" = "true"|
|SAVAS felügyelt táblák|SAVAS felügyelt táblák|

Hajtsa végre a kaptár frissítés utáni eszközét az HDInsight 4,0-fürtön az SSH-rendszerhéj használatával:

1. Csatlakozzon a fürt átjárócsomóponthoz SSH használatával. Útmutatásért lásd: [Kapcsolódás a HDInsight az SSH használatával](../hdinsight-hadoop-linux-use-ssh-unix.md)
1. Bejelentkezési rendszerhéj megnyitása kaptár-felhasználóként a futtatásával `sudo su - hive`
1. Futtassa a következő parancsot a rendszerhéjból.

    ```bash
    STACK_VERSION=$(hdp-select status hive-server2 | awk '{ print $3; }')
    /usr/hdp/$STACK_VERSION/hive/bin/hive --config /etc/hive/conf --service  strictmanagedmigration --hiveconf hive.strict.managed.tables=true -m automatic --modifyManagedTables
    ```

Az eszköz befejeződése után a kaptár-tárház készen áll a HDInsight 4,0-re.

## <a name="migrate-from-internal-metastore"></a>Áttelepítés belső metaadattár

Ha a HDInsight 3,6-fürt belső Hive-metaadattár használ, kövesse az alábbi lépéseket egy parancsfájl futtatásához, amely a metaadattár származó objektumazonosítók exportálására szolgáló kaptár-lekérdezéseket hoz létre.

A HDInsight 3,6-es és 4,0-es fürtöknek ugyanazt a Storage-fiókot kell használniuk.

> [!NOTE]
>
> * A savas táblák esetében a tábla alatti adatmennyiség új másolatát fogja létrehozni.
>
> * Ez a parancsfájl csak a kaptár-adatbázisok,-táblák és-partíciók áttelepítését támogatja. Más metaadat-objektumokat, például nézeteket, UDF és táblákra vonatkozó korlátozásokat manuálisan kell másolni.
>
> * A szkript befejezése után feltételezhető, hogy a régi fürtöt a rendszer többé nem fogja használni a parancsfájlban hivatkozott táblák vagy adatbázisok eléréséhez.
>
> * Az összes felügyelt tábla tranzakciós lesz a HDInsight 4,0-ben. Ha szeretné, megtarthatja a tábla nem tranzakciós beállításait úgy, hogy az adatexportálást egy külső táblába exportálja a "External. table. Purge" = "true" tulajdonsággal. Példa:
>
>    ```SQL
>    create table tablename_backup like tablename;
>    insert overwrite table tablename_backup select * from tablename;
>    create external table tablename_tmp like tablename;
>    insert overwrite table tablename_tmp select * from tablename;
>    alter table tablename_tmp set tblproperties('external.table.purge'='true');
>    drop table tablename;
>    alter table tablename_tmp rename to tablename;
>    ```

1. Kapcsolódjon a HDInsight 3,6-fürthöz egy [Secure Shell-(SSH-) ügyfél](../hdinsight-hadoop-linux-use-ssh-unix.md)használatával.

1. Az Open SSH-munkamenetből töltse le a következő parancsfájlt egy **alltables. HQL** nevű fájl létrehozásához.

    ```bash
    wget https://hdiconfigactions.blob.core.windows.net/hivemetastoreschemaupgrade/exporthive_hdi_3_6.sh
    chmod 755 exporthive_hdi_3_6.sh
    ```

    * Az ESP nélküli normál HDInsight-fürtök esetében egyszerűen hajtsa végre a végrehajtást `exporthive_hdi_3_6.sh` .

    * Az ESP-vel rendelkező fürtök esetében a kinit parancsot és az argumentumok módosítása: futtassa a következőt, és határozza meg az Azure AD-felhasználó teljes kaptár-engedélyekkel rendelkező felhasználó-és TARTOMÁNYát.

        ```bash
        USER="USER"  # replace USER
        DOMAIN="DOMAIN"  # replace DOMAIN
        DOMAIN_UPPER=$(printf "%s" "$DOMAIN" | awk '{ print toupper($0) }')
        kinit "$USER@$DOMAIN_UPPER"
        ```

        ```bash
        hn0=$(grep hn0- /etc/hosts | xargs | cut -d' ' -f4)
        BEE_CMD="beeline -u 'jdbc:hive2://$hn0:10001/default;principal=hive/_HOST@$DOMAIN_UPPER;auth-kerberos;transportMode=http' -n "$USER@$DOMAIN" --showHeader=false --silent=true --outputformat=tsv2 -e"
        ./exporthive_hdi_3_6.sh "$BEE_CMD"
        ```

1. Lépjen ki az SSH-munkamenetből. Ezután adjon meg egy scp-parancsot a **alltables. HQL** helyi letöltéséhez.

    ```bash
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:alltables.hql c:/hdi
    ```

1. Töltse fel a **alltables. HQL** az *új* HDInsight-fürtre.

    ```bash
    scp c:/hdi/alltables.hql sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/home/sshuser/
    ```

1. Ezután használja az SSH-t az *új* HDInsight 4,0-fürthöz való kapcsolódáshoz. Futtassa a következő kódot egy SSH-munkamenetből a fürtre:

    ESP nélkül:

    ```bash
    beeline -u "jdbc:hive2://localhost:10001/;transportMode=http" -f alltables.hql
    ```

    ESP-vel:

    ```bash
    USER="USER"  # replace USER
    DOMAIN="DOMAIN"  # replace DOMAIN
    DOMAIN_UPPER=$(printf "%s" "$DOMAIN" | awk '{ print toupper($0) }')
    kinit "$USER@$DOMAIN_UPPER"
    ```

    ```bash
    hn0=$(grep hn0- /etc/hosts | xargs | cut -d' ' -f4)
    beeline -u "jdbc:hive2://$hn0:10001/default;principal=hive/_HOST@$DOMAIN_UPPER;auth-kerberos;transportMode=http" -n "$USER@$DOMAIN" -f alltables.hql
    ```

A külső metaadattár áttelepítésére szolgáló verziófrissítési eszköz itt nem érvényes, mivel a HDInsight 3,6 nem savas felügyelt táblái a HDInsight 4,0-ben savas felügyelt táblákra konvertálhatók.

> [!Important]  
> A HDInsight 4,0 felügyelt táblái (beleértve a 3,6-ból áttelepített táblákat is) más szolgáltatásokkal vagy alkalmazásokkal nem érhetők el, beleértve a HDInsight 3,6-fürtöket.

## <a name="secure-hive-across-hdinsight-versions"></a>Biztonsági struktúra HDInsight-verziókon keresztül

A HDInsight 3,6 óta a HDInsight a HDInsight Enterprise Security Package (ESP) használatával integrálódik a Azure Active Directoryba. Az ESP a Kerberos és az Apache Ranger használatával kezeli a fürtön belüli adott erőforrások engedélyeit. A HDInsight 3,6-es struktúrában üzembe helyezett Ranger-házirendek a következő lépésekkel telepíthetők át a HDInsight 4,0-re:

1. Navigáljon a Ranger Service Manager panelre a HDInsight 3,6-fürtben.
2. Navigáljon a **struktúra** nevű szabályzathoz, és exportálja a szabályzatot egy JSON-fájlba.
3. Győződjön meg arról, hogy az exportált házirend JSON-ban hivatkozott összes felhasználó létezik az új fürtben. Ha a felhasználó a szabályzat JSON-ban szerepel, de nem létezik az új fürtben, adja hozzá a felhasználót az új fürthöz, vagy távolítsa el a hivatkozást a szabályzatból.
4. Navigáljon a **Ranger Service Manager** panelre a HDInsight 4,0-fürtben.
5. Navigáljon a **kaptár** nevű szabályzathoz, és importálja a Ranger Policy JSON-t a 2. lépésből.

## <a name="check-compatibility-and-modify-codes-as-needed-in-test-app"></a>Ellenőrizze a kompatibilitást, és szükség szerint módosítsa a kódokat az alkalmazás teszteléséhez.

A munkaterhelések, például a meglévő programok és lekérdezések áttelepítésekor ellenőrizze a kibocsátási megjegyzéseket és a dokumentációt, és szükség szerint alkalmazza a módosításokat. Ha a HDInsight 3,6-fürt megosztott Sparkot és Hive-metaadattár használ, akkor a [kaptár-tárház összekötőjét használó további konfigurációra](./apache-hive-warehouse-connector.md) van szükség.

## <a name="deploy-new-app-for-production"></a>Új alkalmazás üzembe helyezése éles környezetben

Az új fürtre való váltáshoz például telepíthet egy új ügyfélalkalmazás alkalmazást, és használhatja azt új éles környezetként, vagy frissítheti meglévő ügyfélalkalmazásját, és átválthat a HDInsight 4,0-re.

## <a name="switch-hdinsight-40-to-the-production"></a>HDInsight 4,0 váltása az éles környezetbe

Ha a tesztelés során eltérések jöttek létre a metaadattár, előbb frissítenie kell a változtatásokat a váltás előtt. Ebben az esetben exportálhatja & importálhatja a metaadattár, majd újra elvégezheti a frissítést.

## <a name="remove-the-old-production"></a>A régi éles környezet eltávolítása

Miután meggyőződött arról, hogy a kiadás elkészült és teljes mértékben működőképes, eltávolíthatja a 3,6-es és a korábbi metaadattár-verziót. A környezet törlése előtt győződjön meg arról, hogy minden migrálva van.

## <a name="query-execution-across-hdinsight-versions"></a>Lekérdezés végrehajtása HDInsight-verziókon keresztül

A HDInsight 3,6-fürtön belül két módon hajtható végre a kaptár/LLAP lekérdezések. A HiveCLI parancssori felületet biztosít, a [TEZ nézet/struktúra nézet](../hadoop/apache-hadoop-use-hive-ambari-view.md) pedig grafikus felhasználói felületen alapuló munkafolyamatot biztosít.

A HDInsight 4,0-ben a HiveCLI lecserélte a Beeline elemre. A TEZ nézet/struktúra nézet egy GUI-alapú munkafolyamatot biztosít. A HiveCLI egy takarékossági ügyfél az 1. Hiveserver, a Beeline pedig egy JDBC-ügyfél, amely hozzáférést biztosít a 2. Hiveserver. A Beeline használatával bármely más JDBC-kompatibilis adatbázis-végponthoz csatlakozhat. A Beeline a 4,0-es HDInsight-on keresztül érhető el anélkül, hogy telepítésre lenne szükség.

## <a name="next-steps"></a>Következő lépések

* [HDInsight 4,0 közlemény](../hdinsight-version-release.md)
* [HDInsight 4,0 Deep Dive](https://azure.microsoft.com/blog/deep-dive-into-azure-hdinsight-4-0/)
* [3. struktúra – savas táblák](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.1.0/using-hiveql/content/hive_3_internals.html)