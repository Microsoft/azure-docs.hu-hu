---
title: Külső metaadat-tárolók használata – Azure HDInsight
description: Külső metaadat-tárolók használata az Azure HDInsight-fürtökkel.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 08/06/2020
ms.openlocfilehash: d36c8f1f592bbe714a9e31cad8131523049f29ad
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98931357"
---
# <a name="use-external-metadata-stores-in-azure-hdinsight"></a>Külső metaadattárak használata az Azure HDInsightban

A HDInsight lehetővé teszi az adatok és a metaadatok külső adattárral való felügyeletét. Ez a funkció [Apache Hive metaadattár](#custom-metastore), [Apache Oozie Metaadattár](#apache-oozie-metastore)és [Apache Ambari adatbázishoz](#custom-ambari-db)érhető el.

A HDInsight Apache Hive metaadattár a Apache Hadoop architektúra alapvető részét képezi. A metaadattár a központi séma tárháza. A metaadattár más big data hozzáférési eszközök, például a Apache Spark, az interaktív lekérdezés (LLAP), a Presto vagy az Apache Pig használják. A HDInsight Azure SQL Database használ Hive-metaadattárként.

![HDInsight struktúra metaadat-tárolójának architektúrája](./media/hdinsight-use-external-metadata-stores/metadata-store-architecture.png)

A HDInsight-fürtökhöz kétféleképpen állíthat be metaadattár:

* [Alapértelmezett metaadattár](#default-metastore)
* [Egyéni metaadattár](#custom-metastore)

## <a name="default-metastore"></a>Alapértelmezett metaadattár

Alapértelmezés szerint a HDInsight létrehoz egy metaadattár minden fürt típussal. Ehelyett egyéni metaadattár is megadhat. Az alapértelmezett metaadattár a következő szempontokat tartalmazza:

* Nincs további díj. A HDInsight minden metaadattár létrehoz egy, a további díjak nélkül.

* Az alapértelmezett metaadattár a fürt életciklusának részét képezik. Amikor töröl egy fürtöt, a rendszer a megfelelő metaadattár és metaadatokat is törli.

* Az alapértelmezett metaadattár nem oszthatók meg más fürtökkel.

* Az alapértelmezett metaadattár csak egyszerű munkaterhelések esetén ajánlott. Olyan munkaterhelések, amelyek nem igényelnek több fürtöt, és nem kell megőrizniük a fürt életciklusán túli metaadatokat.

> [!IMPORTANT]
> Az alapértelmezett metaadattár egy **alapszintű 5 DTU-korláttal (nem frissíthető)** rendelkező Azure SQL Database biztosít. Alapvető tesztelési célokra alkalmas. Nagyméretű vagy éles számítási feladatokhoz ajánlott áttelepíteni egy külső metaadattár.

## <a name="custom-metastore"></a>Egyéni metaadattár

A HDInsight az éles fürtökhöz ajánlott egyéni metaadattárak is támogatja:

* Saját Azure SQL Database kell megadnia a metaadattár.

* A metaadattár életciklusa nem kötődik a fürtök életciklusához, így a metaadatok elvesztése nélkül hozhat létre és törölhet fürtöket. Az olyan metaadatok, mint például a struktúra sémái a HDInsight-fürt törlését és újbóli létrehozását követően is megmaradnak.

* Az egyéni metaadattár több fürtöt és fürtöt is csatlakoztathat ehhez a metaadattár. Például egyetlen metaadattár oszthatók meg az interaktív lekérdezés, a kaptár és a Spark-fürtök között a HDInsight-ben.

* Egy metaadattár (Azure SQL Database) költségeiért kell fizetnie a választott teljesítményszint alapján.

* Igény szerint méretezheti a metaadattár.

* A fürtöt és a külső metaadattár ugyanabban a régióban kell üzemeltetni.

![HDInsight-struktúra metaadatainak tárolójának használati esete](./media/hdinsight-use-external-metadata-stores/metadata-store-use-case.png)

### <a name="create-and-config-azure-sql-database-for-the-custom-metastore"></a>Az egyéni metaadattár létrehozási és konfigurációs Azure SQL Database

Hozzon létre vagy rendelkezzen meglévő Azure SQL Database a HDInsight-fürthöz tartozó egyéni Hive-metaadattár beállítása előtt.  További információ: gyors útmutató [: önálló adatbázis létrehozása Azure SQL Databaseban](../azure-sql/database/single-database-create-quickstart.md?tabs=azure-portal).

A fürt létrehozása során a HDInsight szolgáltatásnak csatlakoznia kell a külső metaadattár, és ellenőriznie kell a hitelesítő adatait. Azure SQL Database tűzfalszabályok konfigurálásával engedélyezheti az Azure-szolgáltatások és-erőforrások számára a kiszolgáló elérését. Engedélyezze ezt a beállítást a Azure Portal a **kiszolgáló tűzfalának beállítása** elem kiválasztásával. Ezután válassza a **nincs** a **nyilvános hálózati hozzáférés megtagadása** alatt lehetőséget, és **Igen** , az **Azure-szolgáltatások és-erőforrások engedélyezése alatt a kiszolgálóhoz való hozzáféréshez** Azure SQL Database. További információt az [IP-Tűzfalszabályok létrehozásával és kezelésével](../azure-sql/database/firewall-configure.md#use-the-azure-portal-to-manage-server-level-ip-firewall-rules) foglalkozó témakörben talál.

Az SQL-áruházakhoz tartozó magánhálózati végpontok csak a ResourceProviderConnection-mel létrehozott fürtökön támogatottak `outbound` . További információért tekintse meg ezt a [dokumentációt](./hdinsight-private-link.md).

![kiszolgáló tűzfalának beállítása gomb](./media/hdinsight-use-external-metadata-stores/configure-azure-sql-database-firewall1.png)

![Azure-szolgáltatások hozzáférésének engedélyezése](./media/hdinsight-use-external-metadata-stores/configure-azure-sql-database-firewall2.png)

### <a name="select-a-custom-metastore-during-cluster-creation"></a>Egyéni metaadattár kiválasztása a fürt létrehozása során

A fürtöt egy korábban létrehozott Azure SQL Databasere irányíthatja bármikor. A portálon keresztüli fürt létrehozásához a beállítás a **Storage > metaadattár beállításainál** adható meg.

![HDInsight struktúra metaadatainak tárolója Azure Portal](./media/hdinsight-use-external-metadata-stores/azure-portal-cluster-storage-metastore.png)

## <a name="hive-metastore-guidelines"></a>Hive-metaadattár irányelvek

> [!NOTE]
> Ha lehetséges, használjon egyéni metaadattár, hogy elkülönítse a számítási erőforrásokat (a futó fürtöt) és a metaadatokat (a metaadattár tárolva). Kezdje az S2-vel, amely 50 DTU és 250 GB tárterületet biztosít. Ha szűk keresztmetszet jelenik meg, az adatbázis felskálázása felfelé is elvégezhető.

* Ha több HDInsight-fürthöz is szeretne hozzáférni, használjon külön adatbázist az egyes fürtök metaadattár. Ha több HDInsight-fürtön keresztül oszt meg egy metaadattár, az azt jelenti, hogy a fürtök ugyanazokat a metaadatokat és a mögöttes felhasználói adatfájlokat használják.

* Rendszeresen készítse elő az egyéni metaadattár biztonsági mentését. Azure SQL Database automatikusan készít biztonsági másolatokat, de a biztonsági mentés megőrzési időkerete változó. További információ: az [automatikus SQL Database biztonsági mentések](../azure-sql/database/automated-backups-overview.md)ismertetése.

* Keresse meg a metaadattár és a HDInsight-fürtöt ugyanabban a régióban. Ez a konfiguráció biztosítja a legmagasabb teljesítmény és a legalacsonyabb hálózati kimenő forgalom díját.

* A metaadattár a teljesítmény és a rendelkezésre állás figyeléséhez Azure SQL Database monitorozási eszközök vagy Azure Monitor naplók használatával.

* Amikor új, újabb Azure HDInsight-verziót hoz létre egy meglévő egyéni metaadattár-adatbázison, a rendszer frissíti a metaadattár sémáját. A frissítés visszafordíthatatlan az adatbázis biztonsági másolatból való visszaállítása nélkül.

* Ha több fürtön keresztül oszt meg egy metaadattár, győződjön meg arról, hogy az összes fürt ugyanazt a HDInsight-verziót használja. A különböző kaptár-verziók eltérő metaadattár-adatbázis-sémákat használnak. Például nem oszthat meg metaadattár a kaptár 2,1 és a kaptár 3,1 verziójú fürtök között.

* A HDInsight 4,0-ben a Spark és a kaptár független katalógusokat használ a SparkSQL-vagy kaptár-táblák eléréséhez. A Spark-katalógusban a Spark által létrehozott tábla él. A kaptárak által létrehozott tábla a kaptár-katalógusban él. Ez a viselkedés eltér a HDInsight 3,6-nél, ahol a kaptár és a Spark közös katalógusa található. A HDInsight 4,0 struktúra és Spark integrációja a kaptár Warehouse-összekötőre (ÜZEMELTETHETŐ WEBMAG) támaszkodik. A ÜZEMELTETHETŐ WEBMAG a Spark és a kaptár közötti hídként működik. Tudnivalók [a kaptár-tárház összekötőről](../hdinsight/interactive-query/apache-hive-warehouse-connector.md).

* A HDInsight 4,0-ben, ha meg szeretné osztani a kaptár és a Spark közötti metaadattár, ehhez módosítsa a metaadattár. Catalog. default tulajdonságot a Spark-fürtben található struktúrára. Ezt a tulajdonságot a Ambari Advanced spark2-kaptár-site-felülbírálásban találja. Fontos tisztában lenni azzal, hogy a metaadattár megosztása csak a külső struktúra táblái esetében működik, ez nem fog működni, ha belső/felügyelt struktúra-táblákat vagy savas táblákat tartalmaz.  

## <a name="apache-oozie-metastore"></a>Apache Oozie metaadattár

Az Apache Oozie egy munkafolyamat-koordinációs rendszer, amely a Hadoop-feladatokat kezeli. A Oozie támogatja az Apache MapReduce, a Pig, a kaptár és mások Hadoop-feladatait.  A Oozie metaadattár használ a munkafolyamatok részleteinek tárolására. A Oozie használata esetén a teljesítmény növeléséhez használhatja a Azure SQL Databaset egyéni metaadattár. A metaadattár a fürt törlése után hozzáférést biztosít a Oozie-feladatok adataihoz.

A Oozie-metaadattár Azure SQL Database használatával történő létrehozásával kapcsolatos utasításokért lásd: [az Apache Oozie használata a munkafolyamatokhoz](hdinsight-use-oozie-linux-mac.md).

## <a name="custom-ambari-db"></a>Egyéni Ambari-adatbázis

Ha saját külső adatbázist szeretne használni az Apache Ambari a HDInsight-on, tekintse meg az [Egyéni Apache Ambari-adatbázist](hdinsight-custom-ambari-db.md).

## <a name="next-steps"></a>Következő lépések

* [Fürtök beállítása a HDInsightban Apache Hadoop, Apache Spark, Apache Kafka stb. használatával](./hdinsight-hadoop-provision-linux-clusters.md)