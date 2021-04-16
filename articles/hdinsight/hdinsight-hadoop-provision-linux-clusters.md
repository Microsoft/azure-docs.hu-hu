---
title: Fürtök beállítása a HDInsightban Apache Hadoop, Apache Spark, Apache Kafka stb. használatával
description: Hadoop-, Kafka-, Spark-, HBase-, R Server- vagy Storm-fürtök beállítása a HDInsighthoz böngészőből, a klasszikus Azure CLI-ről, Azure PowerShell, REST-ről vagy SDK-ból.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017,seodec18, devx-track-azurecli
ms.date: 08/06/2020
ms.openlocfilehash: 3d1059ab46ff0e3722d1f6538bba61cdc4e8cb59
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482687"
---
# <a name="set-up-clusters-in-hdinsight-with-apache-hadoop-apache-spark-apache-kafka-and-more"></a>Fürtök beállítása a HDInsightban Apache Hadoop, Apache Spark, Apache Kafka stb. használatával

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Megtudhatja, hogyan állíthatja be és konfigurálhatja az Apache Hadoop, Apache Spark, Apache Kafka, Interactive Query, Apache HBase, ML Services vagy Apache Storm a HDInsightban. Azt is megtudhatja, hogyan szabhatja testre a fürtöt, és hogyan adhat hozzá biztonságot egy tartományhoz való csatlakozásukkor.

A Hadoop-fürtök több virtuális gépből (csomópontból) állnak, amelyek a tevékenységek elosztott feldolgozására használatosak. Azure HDInsight kezeli az egyes csomópontok telepítésének és konfigurációjának implementációs részleteit, így csak általános konfigurációs információkat kell adnia.

> [!IMPORTANT]  
> A HDInsight-fürt számlázása a fürt létrehozásakor kezdődik és a fürt törlésekor fejeződik be. A számlázás percalapú, ezért mindig érdemes törölni a fürtöt, ha az már nincs használatban. Megtudhatja, hogyan [törölhet fürtöt.](hdinsight-delete-cluster.md)

Ha több fürtöt használ együtt, hozzon létre egy virtuális hálózatot, és ha Spark-fürtöt használ, akkor a fürtöt is Hive Warehouse Connector. További információ: [Plan a virtual network for Azure HDInsight and](./hdinsight-plan-virtual-network-deployment.md) Integrate Apache Spark and Apache Hive with the [Hive Warehouse Connector](interactive-query/apache-hive-warehouse-connector.md).

## <a name="cluster-setup-methods"></a>Fürtbeállítási módszerek

Az alábbi táblázat a HDInsight-fürtök beállításához használható különböző módszereket mutatja be.

| A-val létrehozott fürtök | Webböngésző | Parancssor | REST API | SDK |
| --- |:---:|:---:|:---:|:---:|
| [Azure Portal](hdinsight-hadoop-create-linux-clusters-portal.md) |ââ" |&nbsp; |&nbsp; |&nbsp; |
| [Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md) |ââ" |ââ" |ââ" |ââ" |
| [Azure CLI](hdinsight-hadoop-create-linux-clusters-azure-cli.md) |&nbsp; |ââ" |&nbsp; |&nbsp; |
| [Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md) |&nbsp; |ââ" |&nbsp; |&nbsp; |
| [Curl](hdinsight-hadoop-create-linux-clusters-curl-rest.md) |&nbsp; |ââ" |ââ" |&nbsp; |
| [Azure Resource Manager-sablonok](hdinsight-hadoop-create-linux-clusters-arm-templates.md) |&nbsp; |ââ" |&nbsp; |&nbsp; |

Ez a cikk végigvezeti a Azure Portal [beállításán,](https://portal.azure.com)ahol HDInsight-fürtöt hozhat létre.

## <a name="basics"></a>Alapvető beállítások

:::image type="content" source="./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-basics-blank-fs.png" alt-text="hdinsight create options custom quick":::

### <a name="project-details"></a>Projekt részletei

[Azure Resource Manager](../azure-resource-manager/management/overview.md) az alkalmazás erőforrásait egy csoportként (más néven Azure-erőforráscsoportként) [tudja majd dolgozni.](../azure-resource-manager/management/overview.md#resource-groups) Az alkalmazás összes erőforrását egyetlen koordinált műveletben telepítheti, frissítheti, figyelheti vagy törölheti.

### <a name="cluster-details"></a>Fürt részletei

#### <a name="cluster-name"></a>Fürt neve

A HDInsight-fürtnevekre a következő korlátozások vonatkoznak:

* Megengedett karakterek: a–z, 0–9, A–Z
* Maximális hossz: 59
* Fenntartott nevek: alkalmazások
* A fürt elnevezési hatóköre az összes Azure-hoz, az összes előfizetéshez. Ezért a fürt nevének globálisan egyedinek kell lennie.
* Az első hat karakternek egyedinek kell lennie egy virtuális hálózaton belül

#### <a name="region"></a>Region

A fürt helyét nem kell explicit módon megadnia: A fürt ugyanazon a helyen található, mint az alapértelmezett tároló. A támogatott régiók listájáért  válassza a Régió legördülő listát a [HDInsight díjszabása alatt.](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409)

#### <a name="cluster-type"></a>Fürt típusa

Azure HDInsight jelenleg a következő fürttípusokat biztosítja, amelyek egyes összetevői bizonyos funkciókat biztosítanak.

> [!IMPORTANT]  
> A HDInsight-fürtök különböző típusokban érhetők el, és mindegyik egyetlen számítási feladathoz vagy technológiához használható. Nincs támogatott módszer olyan fürtök létrehozására, amelyek több típust kombinálnak, például a Stormot és a HBase-t egy fürtön. Ha a megoldáshoz több HDInsight-fürttípus között [](../virtual-network/index.yml) elterjedő technológiákra van szükség, egy Azure-beli virtuális hálózat csatlakoztathatja a szükséges fürttípusokat.

| Fürt típusa | Funkció |
| --- | --- |
| [Hadoop](hadoop/apache-hadoop-introduction.md) |Tárolt adatok kötegelt lekérdezése és elemzése |
| [HBase](hbase/apache-hbase-overview.md) |Nagy mennyiségű séma nélküli NoSQL-adat feldolgozása |
| [Interaktív lekérdezés](./interactive-query/apache-interactive-query-get-started.md) |Memóriában való gyorsítótárazás interaktív és gyorsabb Hive-lekérdezésekhez |
| [Kafka](kafka/apache-kafka-introduction.md) | Egy elosztott streamelési platform, amely valós idejű streamelési adatfolyamatokat és alkalmazásokat hoz létre |
| [ML-szolgáltatások](r-server/r-server-overview.md) |Különböző big data statisztikák, prediktív modellezés és gépi tanulási képességek |
| [Spark](spark/apache-spark-overview.md) |Memóriában történő feldolgozás, interaktív lekérdezések, mikrokötetes streamfeldolgozás |
| [Storm](storm/apache-storm-overview.md) |Valós idejű eseményfeldolgozás |

#### <a name="version"></a>Verzió

Válassza ki a HDInsight verzióját ehhez a fürthöz. További információ: [Támogatott HDInsight-verziók.](hdinsight-component-versioning.md#supported-hdinsight-versions)

### <a name="cluster-credentials"></a>Fürt hitelesítő adatai

A HDInsight-fürtök segítségével két felhasználói fiókot konfigurálhat a fürt létrehozása során:

* Fürt bejelentkezési felhasználóneve: Az alapértelmezett felhasználónév az *admin*. Az alapszintű konfigurációt használja a Azure Portal. Néha "Fürtfelhasználónak" vagy "HTTP-felhasználónak" is nevezik.
* Secure Shell- (SSH-) felhasználónév: SSH-kapcsolaton keresztül csatlakozik a fürthöz. További információ: [Az SSH használata a HDInsightban.](hdinsight-hadoop-linux-use-ssh-unix.md)

A HTTP-felhasználónévre a következő korlátozások vonatkoznak:

* Engedélyezett speciális karakterek: `_` és `@`
* Nem engedélyezett karakterek: #;."', \/ :'!*?$() {} []<>|&--=+%~^space
* Maximális hossz: 20

Az SSH-felhasználónévre a következő korlátozások vonatkoznak:

* Engedélyezett speciális karakterek: `_` és `@`
* Nem engedélyezett karakterek: #;."', \/ :'!*?$() {} []<>|&--=+%~^space
* Maximális hossz: 64
* Fenntartott nevek: hadoop, felhasználók, oozie, hive, mapred, ambari-qa, zookeeper, tez, hdfs, sqoop, yarn, hcat, ams, hbase, storm, administrator, admin, user, user1, test, user2, test1, user3, admin1, 1, 123, a, actuser, adm, admin2, aspnet, backup, console, david, guest, john, owner, root, server, sql, support, support_388945a0, sys, test2, test3, user4, user5, spark

## <a name="storage"></a>Tárolás

:::image type="content" source="./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-storage.png" alt-text="Fürttárolási beállítások: HDFS-kompatibilis végpontok":::

Bár a Hadoop helyszíni telepítése a Hadoop elosztott fájlrendszer -t (HDFS) használja a fürtön való tároláshoz, a felhőben a fürthöz csatlakoztatott tárolási végpontokat használja. A felhőalapú tárolás azt jelenti, hogy biztonságosan törölheti a számításhoz használt HDInsight-fürtöt, miközben megőrzi az adatokat.

A HDInsight-fürtök a következő tárolási lehetőségeket használhatja:

* 2. generációs Azure Data Lake Storage
* 1. generációs Azure Data Lake Storage
* Azure Storage általános célú v2
* Azure Storage általános célú v1
* Azure Storage-blokkblob (**csak másodlagos tárolóként támogatott**)

További információ a HDInsight tárolási lehetőségeiről: Compare storage options for use with Azure HDInsight clusters (A hdInsight tárolási [lehetőségeinek összehasonlítása).](hdinsight-hadoop-compare-storage-options.md)

> [!WARNING]  
> A HDInsight-fürttől eltérő helyen található további tárfiókok használata nem támogatott.

A konfigurálás során az alapértelmezett tárolási végponthoz meg kell adnia egy Azure Storage-fiók vagy -tároló blobtárolóját Data Lake Storage. Az alapértelmezett tároló alkalmazás- és rendszernaplókat tartalmaz. Igény szerint további összekapcsolt Azure Storage-fiókokat és Data Lake Storage is megadhat, amelyekhez a fürt hozzáférhet. A HDInsight-fürtnek és a függő tárfióknak ugyanazon az Azure-helyen kell lennie.

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

> [!IMPORTANT]
> A biztonságos tárterület-átvitel fürt létrehozása utáni engedélyezése hibákat eredményezhet a tárfiók használatával, és nem ajánlott. Jobb, ha új fürtöt hoz létre egy olyan tárfiókkal, amely már engedélyezve van a biztonságos átvitelhez.

> [!Note]  
> Azure HDInsight nem továbbítja, mozgatja vagy másolja automatikusan az Azure Storage-ban tárolt adatokat egyik régióból a másikba.

### <a name="metastore-settings"></a>Metaadattár beállításai

Nem kötelező Hive- vagy Apache Oozie-metaadattárat is létrehozhat. Azonban nem minden fürttípus támogatja a metaadattárat, Azure Synapse Analytics nem kompatibilisek a metaadattárokkal.

További információ: [Use external metadata stores in Azure HDInsight.](./hdinsight-use-external-metadata-stores.md)

> [!IMPORTANT]  
> Egyéni metaadattár létrehozásakor ne használjon kötőjelet, kötőjelet vagy szóközt az adatbázis nevében. Ez a fürt létrehozási folyamatának meghiúsulhat.

#### <a name="sql-database-for-hive"></a>SQL database for Hive

Ha meg szeretné őrizni a Hive-táblákat a HDInsight-fürtök törlése után, használjon egyéni metaadattárat. Ezután csatolhatja a metaadattárat egy másik HDInsight-fürthöz.

Egy HDInsight-fürtverzióhoz létrehozott HDInsight-metaadattár nem megosztható a HDInsight-fürt különböző verziói között. A HDInsight-verziók listáját lásd: [Támogatott HDInsight-verziók.](hdinsight-component-versioning.md#supported-hdinsight-versions)

> [!IMPORTANT]
> Az alapértelmezett metaadattár alapszintű 5 Azure SQL Database szintű DTU-korlátot biztosít **(nem frissíthető)**! Alapszintű tesztelési célokra alkalmas. Nagy méretű vagy éles számítási feladatok esetén javasoljuk a külső metaadattárba való áttelepítést.

#### <a name="sql-database-for-oozie"></a>SQL database for Oozie

Az Oozie használata során a teljesítmény növelése érdekében használjon egyéni metaadattárat. A metaadattára a fürt törlése után Oozie-feladatadatokhoz is hozzáférést nyújthat.

#### <a name="sql-database-for-ambari"></a>SQL database for Ambari

Az Ambari a HDInsight-fürtök figyelése, a konfigurációs módosítások, valamint a fürtkezelési információk és a feladatelőzmények tárolására használható. Az egyéni Ambari DB szolgáltatás lehetővé teszi egy új fürt üzembe helyezését és az Ambari beállítását egy Ön által felügyelt külső adatbázisban. További információ: [Egyéni Ambari DB.](./hdinsight-custom-ambari-db.md)

> [!IMPORTANT]  
> Egyéni Oozie-metaadattár nem használható újra. Ha egyéni Oozie-metaadattárat használ, meg kell adnia egy üres Azure SQL Database a HDInsight-fürt létrehozásakor.

## <a name="security--networking"></a>Biztonság és hálózat

:::image type="content" source="./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-security-networking.png" alt-text="hdinsight create options choose enterprise security package":::

### <a name="enterprise-security-package"></a>Vállalati biztonsági csomag

Hadoop-, Spark-, HBase-, Kafka- és Interactive Query fürttípusok esetében engedélyezheti a **Enterprise Security Package.** Ez a csomag lehetővé teszi egy biztonságosabb fürt beállítását az Apache Ranger használatával és a fürtszolgáltatásokkal való Azure Active Directory. További információ: [Overview of enterprise security in Azure HDInsight](./domain-joined/hdinsight-security-overview.md).

Az Enterprise biztonsági csomaggal integrálhatja a HDInsightot a Active Directory Apache Rangerrel. Az Enterprise biztonsági csomaggal több felhasználó is létre lehet hozva.

További információ a tartományhoz csatlakozott HDInsight-fürtök létrehozásáról: Tartományhoz csatlakozott [HDInsight-védőkörnyezet létrehozása.](./domain-joined/apache-domain-joined-configure-using-azure-adds.md)

### <a name="tls"></a>TLS

További információ: [Transport Layer Security](./transport-layer-security.md)

### <a name="virtual-network"></a>Virtuális hálózat

Ha a megoldáshoz több HDInsight-fürttípus között [](../virtual-network/index.yml) elterjedő technológiákra van szükség, egy Azure-beli virtuális hálózat csatlakoztathatja a szükséges fürttípusokat. Ez a konfiguráció lehetővé teszi, hogy a fürtök és a rájuk telepített kódok közvetlenül kommunikáljanak egymással.

További információ az Azure-beli virtuális hálózatok HDInsighttal való használatával kapcsolatban: Plan a virtual network for HDInsight (Virtuális hálózat megterve [a HDInsighthoz).](hdinsight-plan-virtual-network-deployment.md)

Egy Azure-beli virtuális hálózaton belül két fürttípus használatára vonatkozó példáért lásd: [Use Apache Spark Structured Streaming with Apache Kafka](hdinsight-apache-kafka-spark-structured-streaming.md). További információ a HDInsight virtuális hálózattal való használatával kapcsolatban, beleértve a virtuális hálózat konkrét konfigurációs követelményeit: Virtuális hálózat megterve [a HDInsighthoz.](hdinsight-plan-virtual-network-deployment.md)

### <a name="disk-encryption-setting"></a>Lemeztitkosítási beállítás

További információ: Ügyfél [által felügyelt kulcs lemeztitkosítása.](./disk-encryption.md)

### <a name="kafka-rest-proxy"></a>Kafka REST proxy

Ez a beállítás csak a Kafka fürttípushoz érhető el. További információ: [REST-proxy használata.](./kafka/rest-proxy.md)

### <a name="identity"></a>Identitás

További információ: [Felügyelt identitások](./hdinsight-managed-identities.md)a Azure HDInsight.

## <a name="configuration--pricing"></a>Konfiguráció és díjszabás

:::image type="content" source="./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-configuration.png" alt-text="HDInsight – válassza ki a csomópont méretét":::

A csomóponthasználatért addig kell fizetni, amíg a fürt létezik. A számlázás a fürt létrehozásakor kezdődik, és a fürt törlésekor leáll. A fürtök nem oszthatóak fel vagy nem függedhet fel.

### <a name="node-configuration"></a>Csomópont-konfiguráció

Mindegyik fürttípus saját csomópontszámmal, csomópontokkal kapcsolatos terminológiával és alapértelmezett virtuálisgép-mérettel rendelkezik. Az alábbi táblázatban az egyes csomóponttípusokhoz a csomópontok száma zárójelben van megva.

| Típus | Csomópontok | Ábra |
| --- | --- | --- |
| Hadoop |Fejcsomópont (2), munkavégző csomópont (1+) |:::image type="content" source="./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-hadoop-cluster-type-nodes.png" alt-text="HDInsight Hadoop-fürtcsomópontok" border="false"::: |
| HBase |Főkiszolgáló (2), régiókiszolgáló (1+), fő/ZooKeeper csomópont (3) |:::image type="content" source="./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-hbase-cluster-type-setup.png" alt-text="HDInsight HBase-fürttípus beállítása" border="false"::: |
| Storm |Nimbus-csomópont (2), felügyelő kiszolgáló (1+), ZooKeeper-csomópont (3) |:::image type="content" source="./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-storm-cluster-type-setup.png" alt-text="HDInsight Storm-fürttípus beállítása" border="false"::: |
| Spark |Fejcsomópont (2), munkavégző csomópont (1+), ZooKeeper-csomópont (3) (ingyenes az A1 ZooKeeper virtuális gép méretéhez) |:::image type="content" source="./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-spark-cluster-type-setup.png" alt-text="HDInsight Spark-fürttípus beállítása" border="false"::: |

További információt a [](hdinsight-supported-node-configuration.md) "What are the Hadoop components and versions in HDInsight?" (Milyen Hadoop-összetevők és -verziók vannak a HDInsightban) című cikk Alapértelmezett csomópontkonfiguráció és virtuálisgép-méretek fürtökhöz című részében található.

A HDInsight-fürtök költségét a csomópontok száma és a csomópontok virtuális gépeinek mérete határozza meg.

A különböző fürttípusok eltérő csomóponttípusokkal, csomópontszámmal és csomópontmérettel is használhatók:
* A Hadoop-fürt típusa alapértelmezés szerint:
    * Két *főcsomópont*
    * Négy *munkavégző csomópont*
* A Storm-fürt típusa alapértelmezés szerint:
    * Két *Nimbus-csomópont*
    * Három *ZooKeeper-csomópont*
    * Négy *felügyeleti csomópont*

Ha csak most próbálja ki a HDInsightot, javasoljuk, hogy használjon egy feldolgozó csomópontot. További információ a HDInsight díjszabásával kapcsolatban: [A HDInsight díjszabása.](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409)

> [!NOTE]  
> A fürt méretkorlátja Azure-előfizetésenként eltérő lehet. A [korlát növeléséhez forduljon](../azure-portal/supportability/how-to-create-azure-support-request.md) az Azure számlázási ügyfélszolgálatához.

Ha a fürt Azure Portal a fürtöt, a csomópont mérete a Konfiguráció **+ díjszabás lapon érhető** el. A portálon a különböző csomópontméretekhez kapcsolódó költségeket is láthatja.

### <a name="virtual-machine-sizes"></a>Virtuálisgép-méretek

Fürtök üzembe helyezésekor válassza ki a számítási erőforrásokat az üzembe helyezni tervben álló megoldás alapján. A HDInsight-fürtökhöz a következő virtuális gépek használhatók:

* A és D1-4 sorozatú virtuális gépek: [Általános célú Linux virtuálisgép-méretek](../virtual-machines/sizes-general.md)
* D11-14 sorozatú virtuális gép: [Memóriaoptimalált Linux virtuálisgép-méretek](../virtual-machines/sizes-memory.md)

Ha meg kell tudni, hogy milyen értéket kell használnia a virtuális gép méretének megadásához a fürt különböző HDD-k használatával való létrehozásakor, vagy ha az Azure PowerShell-t használja, tekintse meg a [HDInsight-fürtökhöz](../cloud-services/cloud-services-sizes-specs.md#size-tables)használni kívánt virtuálisgép-méreteket. Ebből a hivatkozott cikkből használja a táblák **Méret** oszlopában található értéket.

> [!IMPORTANT]  
> Ha 32 feldolgozó csomópontnál többre van szüksége egy fürtben, legalább 8 maggal és 14 GB RAM-mal rendelkező fejcsomópont-méretet kell választania.

További információ: [Virtuális gépek méretei.](../virtual-machines/sizes.md) A különböző méretek díjszabásával kapcsolatos információkért lásd: [A HDInsight díjszabása.](https://azure.microsoft.com/pricing/details/hdinsight)

### <a name="add-application"></a>Alkalmazás hozzáadása

A HDInsight-alkalmazások olyan alkalmazások, amelyeket a felhasználók egy Linux-alapú HDInsight-fürtre telepíthetnek. Használhatja a Microsoft, külső felek vagy ön által saját fejlesztésben részt vevő alkalmazások használatát. További információ: [Külső Gyártótól származó Apache Hadoop-alkalmazások telepítése a Azure HDInsight.](hdinsight-apps-install-applications.md)

A legtöbb HDInsight-alkalmazás üres élcsomóponton van telepítve.  Az üres élcsomópont egy Linux rendszerű virtuális gép, amely ugyanazokkal az ügyféleszközökkel van telepítve és konfigurálva, mint az főcsomóponton. Az élcsomópontot használhatja a fürt eléréséhez, az ügyfélalkalmazások teszteléséhez és az ügyfélalkalmazások üzemeltetéséhez. További információ: [Üres élcsomópontok használata a HDInsightban.](hdinsight-apps-use-edge-node.md)

### <a name="script-actions"></a>Szkriptműveletek

További összetevőket telepíthet, vagy testreszabhatja a fürtkonfigurációt szkriptek használatával a létrehozás során. Az ilyen szkriptek meghívása a parancsfájl-művelet használatával **történt,** amely egy konfigurációs beállítás, amely a Azure Portal, a HDInsight Windows PowerShell-parancsmagok vagy a HDInsight .NET SDK használatával használható. További információ: [HDInsight-fürt testreszabása szkript művelet használatával.](hdinsight-hadoop-customize-cluster-linux.md)

Egyes natív Java-összetevők, például az Apache Mahout és a Cascading Java Archive- (JAR-) fájlokként futtathatók a fürtön. Ezek a JAR-fájlok terjeszthetőek az Azure Storage-ba, és elküldhetőek a Hadoop-feladatküldési mechanizmusokkal a HDInsight-fürtöknek. További információ: [Apache Hadoop-feladatok programozott elküldése.](hadoop/submit-apache-hadoop-jobs-programmatically.md)

> [!NOTE]  
> Ha problémái vannak a JAR-fájlok HDInsight-fürtökön való üzembe helyezésével vagy a JAR-fájlok HDInsight-fürtökön való hívásával, lépjen kapcsolatba a [Microsoft ügyfélszolgálata.](https://azure.microsoft.com/support/options/)
>
> A HDInsight nem támogatja az egymásra épülő tevékenységeket, és nem Microsoft ügyfélszolgálata. A támogatott összetevők listáját lásd: [What's new in the cluster versions provided by HDInsight](hdinsight-component-versioning.md)(A HDInsight által biztosított fürtverziók újdonságai).

Előfordulhat, hogy a létrehozási folyamat során a következő konfigurációs fájlokat szeretné konfigurálni:

* clusterIdentity.xml
* core-site.xml
* gateway.xml
* hbase-env.xml
* hbase-site.xml
* hdfs-site.xml
* hive-env.xml
* hive-site.xml
* mapred-site
* oozie-site.xml
* oozie-env.xml
* storm-site.xml
* tez-site.xml
* webhcat-site.xml
* yarn-site.xml

További információ: [HDInsight-fürtök testreszabása a Bootstrap használatával.](hdinsight-hadoop-customize-cluster-bootstrap.md)

## <a name="next-steps"></a>Következő lépések

* [Fürt létrehozási hibáinak elhárítása Azure HDInsight](./hadoop/hdinsight-troubleshoot-cluster-creation-fails.md)
* [Mik a HDInsight, az Apache Hadoop-ökoszisztéma és a Hadoop-fürtök?](hadoop/apache-hadoop-introduction.md)
* [Az Apache Hadoop használatának első lépések a HDInsightban](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Munka a HDInsight-alapú Apache Hadoopban Windows rendszerű számítógépről](hdinsight-hadoop-windows-tools.md)
