---
title: Az Azure HDInsight üzletmenet-folytonossági architektúrái
description: Ez a cikk a HDInsight különböző lehetséges üzleti folytonossági architektúráit ismerteti
keywords: Hadoop magas rendelkezésre állása
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/07/2020
ms.openlocfilehash: f74309370f1489714013344116e7feb9551fbfd5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98933453"
---
# <a name="azure-hdinsight-business-continuity-architectures"></a>Az Azure HDInsight üzletmenet-folytonossági architektúrái

Ebből a cikkből megtudhatja, milyen üzleti folytonossági architektúrákat érdemes megfontolni az Azure-HDInsight. A katasztrófa esetén a csökkentett funkciók tűréshatára egy üzleti döntés, amely az egyik alkalmazástól a következőig változik. Előfordulhat, hogy egyes alkalmazások nem lesznek elérhetők, vagy egy adott időszakra vonatkozóan kevésbé állnak rendelkezésre, vagy késleltetett feldolgozást végeznek. Más alkalmazások esetében a csökkentett funkciók elfogadhatatlanok lehetnek.

> [!NOTE]
> A cikkben bemutatott architektúrák nem teljesek. A várt Üzletmenet-folytonosság, az üzemeltetési komplexitás és a tulajdonlás díja után tervezze meg saját egyedi architektúráit.

## <a name="apache-hive-and-interactive-query"></a>Apache Hive és interaktív lekérdezés

A [kaptár replikálási v2](https://cwiki.apache.org/confluence/display/Hive/HiveReplicationv2Development#HiveReplicationv2Development-REPLSTATUS) használata a HDInsight-struktúra és az interaktív lekérdezési fürtök üzleti folytonosságához ajánlott. A replikálni kívánt önálló kaptár-fürt állandó részeit a tárolási rétegnek és a Hive-metaadattárnek kell megadnia. A kaptár-fürtök többfelhasználós forgatókönyvben Enterprise Security Package szükségük van a Azure Active Directory Domain Services és a Ranger Metaadattár.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/hive-interactive-query.png" alt-text="Struktúra és interaktív lekérdezési architektúra":::

A kaptár eseményvezérelt replikációja az elsődleges és a másodlagos fürtök között van konfigurálva. Ez két különböző fázisból áll: a rendszerindítás és a növekményes futtatások:

* A rendszerindítás replikálja a teljes kaptár-tárházat, beleértve az elsődlegesről a másodlagosra Hive-metaadattár adatokat.

* A növekményes futtatások automatizálása az elsődleges fürtön történik, a növekményes futtatások során létrehozott események pedig a másodlagos fürtön lesznek lejátszva. A másodlagos fürt felveszi az elsődleges fürtből származó eseményeket, így biztosítva, hogy a másodlagos fürt konzisztens legyen az elsődleges fürt eseményeivel a replikálás futtatása után.

A másodlagos fürt csak abban az esetben szükséges, ha a replikálás során elosztott másolatot futtat, `DistCp` de a tároló-és metaadattárak állandónak kell lennie. Dönthet úgy, hogy a replikálás előtt egy parancsfájlból álló másodlagos fürtöt indít el, majd futtatja a replikálási parancsfájlt, majd leszakítja azt a sikeres replikáció után.

A másodlagos fürt általában csak olvasható. A másodlagos fürt írható és olvasható is lehet, de ez további bonyolultságot is felhasznál, amely magában foglalja a módosítások replikálását a másodlagos fürtről az elsődleges fürtre.

### <a name="hive-event-based-replication-rpo--rto"></a>Kaptár eseményvezérelt replikálási RPO & RTO

* RPO: az adatvesztés az elsődlegesről másodlagosra történő utolsó sikeres növekményes replikálási eseményre korlátozódik.

* RTO: a hiba és a felsőbb rétegbeli tranzakciók újrakezdése és a másodlagos tranzakció utáni idő.

### <a name="apache-hive-and-interactive-query-architectures"></a>Apache Hive és interaktív lekérdezési architektúrák

#### <a name="hive-active-primary-with-on-demand-secondary"></a>Aktív elsődleges struktúra igény szerinti másodlagosként

Az *igény szerinti másodlagos* architektúrával rendelkező aktív elsődleges verzióban az alkalmazások az aktív elsődleges régióban írhatók, míg a normál műveletek során egyetlen fürt sincs kiépítve a másodlagos régióban. A másodlagos régióban található SQL-Metaadattár és-tárolás állandó, míg a HDInsight-fürt csak az ütemezett struktúra replikálásának futtatása előtt van megírt és igény szerint üzembe helyezett.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/active-primary-on-demand-secondary.png" alt-text="aktív elsődleges igény szerinti másodlagos":::

#### <a name="hive-active-primary-with-standby-secondary"></a>Elsődleges struktúra aktív, készenléti másodlagos állapottal

Egy *aktív, készenléti másodlagos állapotú aktív elsődlegesben* az alkalmazások az aktív elsődleges régióba írhatók, míg a normál működés során a (csak olvasási módban lévő) másodlagos fürtöt lefoglaló készenléti üzemmód fut. A normál műveletek során dönthet úgy, hogy kiszervezi a régió specifikus olvasási műveleteit a másodlagosra.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/active-primary-standby-secondary.png" alt-text="aktív elsődleges és készenléti másodlagos":::

A kaptár-replikációval és-kódokkal kapcsolatos további információkért tekintse meg [Apache Hive replikációt az Azure HDInsight-fürtökben](./interactive-query/apache-hive-replication.md)

## <a name="apache-spark"></a>Apache Spark

A Spark-munkaterhelések nem tartalmazhatnak kaptár-összetevőt. Ahhoz, hogy a Spark SQL-munkaterhelések lehetővé tegyék a kaptárból származó adatok olvasását és írását, a HDInsight Spark-fürtök megosztják az egyéni metaadattárak a kaptár/interaktív lekérdezési fürtökből ugyanabban a régióban Ilyen esetekben a Spark-munkaterhelések régiók közötti replikálásának kísérnie kell a struktúra metaadattárak és tárterületének replikálását is. Az ebben a szakaszban található feladatátvételi forgatókönyvek a következőkre vonatkoznak:

* [Spark SQL on sav-táblák a kaptár-összekötő (üzemeltethető webmag) használatával](./interactive-query/apache-hive-warehouse-connector.md) , egy HDInsight interaktív lekérdezési fürt használatával.
* Spark SQL-munkaterhelés a nem savas táblákon HDInsight Hadoop-fürt használatával.

Olyan helyzetekben, ahol a Spark önálló módban működik, a kiszolgált adatok és a tárolt Spark-tégelyek (Livy-feladatok esetében) az elsődleges régióból a másodlagos régióba rendszeresen replikálódnak a Azure Data Factory használatával `DistCP` .

Javasoljuk, hogy a verzió-ellenőrzési rendszerek használatával tárolja a Spark-jegyzetfüzeteket és a kódtárakat, ahol az elsődleges vagy másodlagos fürtökön egyszerűen üzembe helyezhetők. Győződjön meg arról, hogy a notebookon és a nem jegyzetfüzeten alapuló megoldások készen állnak a megfelelő adatcsatlakoztatások betöltésére az elsődleges vagy a másodlagos munkaterületen.

Ha vannak olyan ügyfél-specifikus kódtárak, amelyek az HDInsight natív módon biztosítanak, akkor azt nyomon kell követni, és rendszeresen be kell tölteni a tartalék másodlagos fürtbe.  

### <a name="apache-spark-replication-rpo--rto"></a>Apache Spark replikációs RPO & RTO

* RPO: az adatvesztés a legutóbbi sikeres növekményes replikációra (Spark és kaptár) korlátozódik az elsődlegesről a másodlagosra.

* RTO: a hiba és a felsőbb rétegbeli tranzakciók újrakezdése és a másodlagos tranzakció utáni idő.

### <a name="apache-spark-architectures"></a>Apache Spark architektúrák

#### <a name="spark-active-primary-with-on-demand-secondary"></a>Spark aktív elsődleges az igény szerinti másodlagos

Az alkalmazások az elsődleges régióban található Spark-és kaptár-fürtök olvasását és írását írják le, míg a normál műveletek során egyetlen fürt sincs kiépítve a másodlagos régióban. Az SQL Metaadattár, a kaptár Storage és a Spark Storage állandó a másodlagos régióban. A Spark-és a kaptár-fürtök parancsfájl-és üzembe helyezése igény szerint történik. A kaptár replikációja a struktúra-tároló és a kaptár metaadattárak replikálására szolgál, miközben Azure Data Factory `DistCP` az önálló Spark-tároló másolására használható. A kaptár-fürtöket a függőségi számítás miatt a struktúra összes replikálásának futtatása előtt kell telepíteni `DistCp` .

:::image type="content" source="./media/hdinsight-business-continuity-architecture/active-primary-on-demand-secondary-spark.png" alt-text="aktív elsődleges az igény szerinti másodlagos Apache Spark architektúrával":::

#### <a name="spark-active-primary-with-standby-secondary"></a>Spark aktív elsődleges és készenléti másodlagos

Az alkalmazások az elsődleges régióban található Spark-és kaptár-fürtökre olvasást és írást végeznek, a normál műveletek során pedig a másodlagos régióban lévő, írásvédett módban futtatott készenléti és a Spark-fürtök. A normál működés során dönthet úgy, hogy kiszervezi a régió specifikus struktúráját és a Spark olvasási műveleteit a másodlagosra.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/active-primary-standby-secondary-spark.png" alt-text="aktív elsődleges készenléti másodlagos Apache Spark ":::

## <a name="apache-hbase"></a>Apache HBase

A HBase exportálási és HBase replikációja a HDInsight HBase-fürtök közötti üzletmenet-folytonosságot lehetővé tévő gyakori módszer.

A HBase exportálás egy batch-replikációs folyamat, amely a HBase-exportálási segédprogramot használja az elsődleges HBase-fürtből származó táblák exportálásához az alapul szolgáló Azure Data Lake Storage 2. generációs tárolóba. Az exportált adatok ezután a másodlagos HBase-fürtből érhetők el, és olyan táblákba importálhatók, amelyeknek léteznie kell a másodlagosban. Míg a HBase-exportálás a tábla szintjének részletességét kínálja, növekményes frissítési helyzetekben az Automation-motor az egyes futtatásokban szerepeltetni kívánt növekményes sorok tartományát vezérli. További információ: [HDInsight HBase biztonsági mentés és replikálás](./hbase/apache-hbase-backup-replication.md#export-then-import).

A HBase-replikáció közel valós idejű replikációt használ a HBase-fürtök között teljesen automatizált módon. A replikáció a tábla szintjén történik. A replikáláshoz az összes tábla vagy adott táblázat is megcélozható. A HBase-replikáció végül konzisztens, ami azt jelenti, hogy az elsődleges régióban lévő táblák legutóbbi módosításait nem lehet azonnal elérhetővé tennie az összes formátumú másodlagos zónák számára. A formátumú másodlagos zónák garantáltan konzisztensek lesznek az elsődlegesekkel. A HBase replikáció két vagy több HDInsight HBase-fürt között állítható be, ha:

* Az elsődleges és a másodlagos ugyanahhoz a virtuális hálózathoz tartoznak.
* Az elsődleges és a másodlagos a különböző, azonos régióban található virtuális hálózatok.
* Az elsődleges és a másodlagos a különböző régiókban található különböző virtuális hálózatok.

További információ: [az Apache HBase-fürt replikálásának beállítása az Azure Virtual Networks](./hbase/apache-hbase-replication.md)szolgáltatásban.

A HBase-fürtök (például [a HBase mappa másolása, a](./hbase/apache-hbase-backup-replication.md#copy-the-hbase-folder)táblák és a [Pillanatképek](./hbase/apache-hbase-backup-replication.md#snapshots) [másolása](./hbase/apache-hbase-backup-replication.md#copy-tables) ) több más módon is elvégezheti a biztonsági mentéseket.

### <a name="hbase-rpo--rto"></a>HBase RPO & RTO

#### <a name="hbase-export"></a>HBase-exportálás

* RPO: az adatvesztés a másodlagos, az elsődlegestől az utolsó sikeres batch növekményes importálásra korlátozódik.
* RTO: az I/O műveletek elsődleges és újraindítási hibája közötti idő a másodlagosn.

#### <a name="hbase-replication"></a>HBase-replikáció

* RPO: az adatvesztés a másodlagosnál fogadott utolsó WalEdit-szállítmányra korlátozódik.
* RTO: az I/O műveletek elsődleges és újraindítási hibája közötti idő a másodlagosn.

### <a name="hbase-architectures"></a>HBase architektúrák

A HBase-replikáció Háromféleképpen állítható be: Leader-követő, Leader-Leader és ciklikus.

#### <a name="hbase-replication--leader--follower-model"></a>HBase-replikáció: Leader – követő modell

Ebben a régiók közötti replikációban a replikálás az elsődleges régióról a másodlagos régióra történik. Az egyirányú replikációhoz az összes tábla vagy az elsődlegesen megadott táblák azonosíthatók. A normál működés során a másodlagos fürt a saját régiójában található olvasási kérelmek kiszolgálására használható.

A másodlagos fürt normál HBase-fürtként működik, amely a saját táblázatait üzemelteti, és a regionális alkalmazások olvasását és írásait is képes kiszolgálni. A replikált táblákon vagy a másodlagos táblákon található írások azonban nem replikálódnak vissza az elsődlegesre.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/hbase-leader-follower.png" alt-text="HBase Leader-követő modell":::

#### <a name="hbase-replication--leader--leader-model"></a>HBase-replikáció: Leader – vezető modell

Ez a régiók közötti beállítás nagyon hasonlít az egyirányú beállításhoz, azzal a különbséggel, hogy a replikálás az elsődleges régió és a másodlagos régió között kétirányú módon történik. Az alkalmazások mindkét fürtöt használhatják olvasási és írási módokon, és a frissítések aszinkron módon működnek egymás között.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/hbase-leader-leader.png" alt-text="HBase Leader Leader-modell":::

#### <a name="hbase-replication-multi-region-or-cyclic"></a>HBase-replikáció: több régió vagy ciklikus

A többrégiós/ciklikus replikációs modell a HBase-replikáció kiterjesztése, és felhasználható egy globálisan redundáns HBase-architektúra létrehozására, amely több alkalmazással is rendelkezik, amelyek adott HBase-fürtöket olvasnak és írnak. A fürtöket az üzleti követelményektől függően a Leader/vezető, illetve vezető/követő különböző kombinációkban lehet beállítani.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/hbase-cyclic.png" alt-text="HBase ciklikus modell":::

## <a name="apache-kafka"></a>Apache Kafka

A régiók közötti rendelkezésre állás engedélyezéséhez a 4,0 támogatja a Kafka-MirrorMaker, amely az elsődleges Kafka-fürt másodlagos replikájának egy másik régióban való fenntartására használható HDInsight. A MirrorMaker magas szintű fogyasztói termelő párokként működik, és az elsődleges fürt egy adott témakörét használja fel, és egy azonos nevű témakörbe állítja a másodlagosban. A MirrorMaker szolgáltatással történő magas rendelkezésre állású vész-helyreállítás több fürtön történő replikálása feltételezi, hogy a termelőknek és a felhasználóknak feladatátvételt kell végezniük a replika fürtön További információ: a [MirrorMaker használata a HDInsight-beli Kafka-vel való Apache Kafka replikálásához](./kafka/apache-kafka-mirroring.md)

Attól függően, hogy milyen élettartamot indít el a replikáció, a MirrorMaker témakör replikációja különböző eltolásokat eredményezhet a forrás-és a replika témakörök között. A HDInsight Kafka-fürtök támogatják a partíciók replikálását is, amely az egyes fürtök szintjén magas rendelkezésre állású szolgáltatás.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/kafka-replication.png" alt-text="Apache Kafka replikáció":::

### <a name="apache-kafka-architectures"></a>Apache Kafka architektúrák

#### <a name="kafka-replication-active--passive"></a>Kafka-replikáció: aktív – passzív

Active-Passive telepítője lehetővé teszi az aszinkron, egyirányú tükrözést Aktívról Passzívra. A termelőknek és a fogyasztóknak ismerniük kell egy aktív és passzív fürt létezését, és készen kell lenniük a feladatátvételre a passzív állapotba, ha az aktív sikertelen. Az alábbiakban a Active-Passive telepítőjének néhány előnye és hátránya látható.

Előnyök:

* A fürtök közötti hálózati késés nem befolyásolja az aktív fürt teljesítményét.
* Az egyirányú replikáció egyszerűsége.

Hátrányok:

* A passzív fürt továbbra is kihasználatlan maradhat.
* Tervezzen összetettséget az alkalmazás-előállítókban és a fogyasztóknál a feladatátvételi ismeretek beépítésével.
* A lehetséges adatvesztés az aktív fürt meghibásodása esetén.
* Az aktív és a passzív fürtök közötti végleges konzisztencia.
* Az elsődleges Failbacks az üzenetek inkonzisztenciát okozhatnak a témakörökben.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/kafka-active-passive.png" alt-text="Aktív passzív modell Apache Kafka":::

#### <a name="kafka-replication-active--active"></a>Kafka-replikáció: aktív – aktív

A Active-Active beállítása két regionálisan elkülönített, VNet-alapú HDInsight Kafka-fürtöt foglal magában, amelyek kétirányú aszinkron replikációval rendelkeznek a MirrorMaker-mel. Ebben a kialakításban az elsődleges felhasználók által felhasznált üzeneteket a rendszer a másodlagos felhasználók számára is elérhetővé teszi, és fordítva. Az alábbiakban a Active-Active telepítőjének néhány előnye és hátránya látható.

Előnyök:

* A duplikált állapotuk miatt a feladatátvételek és a failbacks egyszerűbben végrehajthatók.

Hátrányok:

* A beállítás, a felügyelet és a figyelés összetettebb, mint az aktív-passzív.
* A körkörös replikáció problémáját meg kell oldani.  
* A kétirányú replikáció a regionális adatforgalom magasabb költségeihez vezet.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/kafka-active-active.png" alt-text="Aktív aktív modell Apache Kafka":::

## <a name="hdinsight-enterprise-security-package"></a>HDInsight Enterprise Security Package

Ezzel a beállítással engedélyezheti a többfelhasználós funkciókat mind az elsődleges, mind a másodlagos, mind az [Azure AD DS-replikák](../active-directory-domain-services/tutorial-create-replica-set.md) számára, így biztosítva, hogy a felhasználók mindkét fürtön hitelesíteni tudják magukat. A normál működés során a Ranger-házirendeket a másodlagos szolgáltatásban kell beállítani, hogy a felhasználók csak olvasási műveletekre legyenek korlátozva. Az alábbi architektúra azt ismerteti, hogyan lehet az ESP-t használó, aktív, elsődleges – készenléti másodlagos készletet beállítani.

Ranger-Metaadattár replikációja:

A Ranger Metaadattár az adathitelesítés szabályozására szolgáló Ranger-szabályzatok állandó tárolására és kiszolgálására szolgál. Javasoljuk, hogy a független Ranger-szabályzatokat az elsődleges és a másodlagos módon kezelje, és a másodlagost olvasási replikaként őrizze meg.
  
Ha a szükséges, hogy a Ranger-szabályzatok szinkronban maradjanak az elsődleges és a másodlagos között, a [Ranger Importálás/exportálás](https://cwiki.apache.org/confluence/display/RANGER/User+Guide+For+Import-Export) szolgáltatásával rendszeres időközönként biztonsági mentést készíthet, és importálhatja a Ranger-házirendeket elsődlegesről másodlagosra.

Az elsődleges és a másodlagos Ranger-házirendek replikálásával a másodlagos érték írható, ami véletlen írást eredményezhet a másodlagos adatkövetkezetlenségek eléréséhez.  

:::image type="content" source="./media/hdinsight-business-continuity-architecture/hdinsight-enterprise-security-package.png" alt-text="HDInsight Enterprise Security Package architektúra":::

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a cikkben tárgyalt elemekről, tekintse meg a következőt:

* [Az Azure HDInsight üzletmenet folytonossága](./hdinsight-business-continuity.md)
* [Azure HDInsight, magasan elérhető megoldás architektúra-esettanulmány](./hdinsight-high-availability-case-study.md)
* [Mi a Apache Hive és a HiveQL az Azure HDInsight?](./hadoop/hdinsight-use-hive.md)