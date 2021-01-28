---
title: Fürtök beállítása a HDInsightban Apache Hadoop, Apache Spark, Apache Kafka stb. használatával
description: Hadoop-, Kafka-, Spark-, HBase-, R Server-vagy Storm-fürtöket állíthat be a HDInsight böngészőből, a klasszikus Azure CLI-ből, a Azure PowerShell, a REST-ból vagy az SDK-ból.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017,seodec18
ms.date: 08/06/2020
ms.openlocfilehash: 4e81ccb541b188fedfefd150233082e9cb1f8c22
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98945550"
---
# <a name="set-up-clusters-in-hdinsight-with-apache-hadoop-apache-spark-apache-kafka-and-more"></a>Fürtök beállítása a HDInsightban Apache Hadoop, Apache Spark, Apache Kafka stb. használatával

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Megtudhatja, hogyan állíthatja be és konfigurálhatja Apache Hadoop, Apache Spark, Apache Kafka, interaktív lekérdezés, Apache HBase, ML szolgáltatások vagy Apache Storm a HDInsight-ben. Azt is megtudhatja, hogyan szabhatja testre a fürtöket, és hogyan adhat hozzá a biztonsághoz egy tartományhoz való csatlakoztatással.

A Hadoop-fürtök több virtuális gépről (csomópontból) állnak, amelyek a feladatok elosztott feldolgozásához használatosak. Az Azure HDInsight az egyes csomópontok telepítésének és konfigurálásának megvalósítási részleteit kezeli, így csak általános konfigurációs adatokat kell megadnia.

> [!IMPORTANT]  
> A HDInsight-fürt számlázása a fürt létrehozásakor kezdődik és a fürt törlésekor fejeződik be. A számlázás percalapú, ezért mindig érdemes törölni a fürtöt, ha az már nincs használatban. További információ a [fürtök törléséről.](hdinsight-delete-cluster.md)

Ha egyszerre több fürtöt használ, létre kell hoznia egy virtuális hálózatot, és ha Spark-fürtöt használ, akkor a méhkas Warehouse-összekötőt is érdemes használnia. További információ: [virtuális hálózat megtervezése az Azure HDInsight](./hdinsight-plan-virtual-network-deployment.md) és [a Apache Spark és Apache Hive integrálása a kaptár Warehouse-összekötővel](interactive-query/apache-hive-warehouse-connector.md).

## <a name="cluster-setup-methods"></a>Fürt beállítási módszerei

A következő táblázat a HDInsight-fürtök beállításához használható különböző módszereket mutatja be.

| A-val létrehozott fürtök | Webböngésző | Parancssor | REST API | SDK |
| --- |:---:|:---:|:---:|:---:|
| [Azure Portal](hdinsight-hadoop-create-linux-clusters-portal.md) |✔ |&nbsp; |&nbsp; |&nbsp; |
| [Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md) |✔ |✔ |✔ |✔ |
| [Azure CLI](hdinsight-hadoop-create-linux-clusters-azure-cli.md) |&nbsp; |✔ |&nbsp; |&nbsp; |
| [Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md) |&nbsp; |✔ |&nbsp; |&nbsp; |
| [cURL](hdinsight-hadoop-create-linux-clusters-curl-rest.md) |&nbsp; |✔ |✔ |&nbsp; |
| [Azure Resource Manager-sablonok](hdinsight-hadoop-create-linux-clusters-arm-templates.md) |&nbsp; |✔ |&nbsp; |&nbsp; |

Ez a cikk végigvezeti a telepítőn a [Azure Portalon](https://portal.azure.com), ahol HDInsight-fürtöt hozhat létre.

## <a name="basics"></a>Alapbeállítások

![hdinsight-létrehozási beállítások egyéni gyors](./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-basics-blank-fs.png)

### <a name="project-details"></a>Projekt részletei

[Azure Resource Manager](../azure-resource-manager/management/overview.md) segít az alkalmazásban lévő erőforrásokkal való munkavégzésben, amely Azure- [erőforráscsoport](../azure-resource-manager/management/overview.md#resource-groups)néven is ismert. Az alkalmazás összes erőforrását egyetlen összehangolt műveletben telepítheti, frissítheti, figyelheti vagy törölheti.

### <a name="cluster-details"></a>Fürt részletei

#### <a name="cluster-name"></a>Fürt neve

A HDInsight-fürtök neve a következő korlátozásokkal rendelkezhet:

* Engedélyezett karakterek: a-z, 0-9, A-Z
* Maximális hossz: 59
* Fenntartott nevek: alkalmazások
* A fürt elnevezési hatóköre az összes Azure-ra kiterjed az összes előfizetésben. Így a fürt nevének egyedinek kell lennie világszerte.
* Az első hat karakternek egyedinek kell lennie a virtuális hálózaton belül

#### <a name="region"></a>Régió

Nem kell explicit módon megadnia a fürt helyét: a fürt az alapértelmezett tárolóval megegyező helyen található. A támogatott régiók listájának megtekintéséhez válassza a [HDInsight díjszabása](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409) **régió** legördülő listát.

#### <a name="cluster-type"></a>Fürt típusa

Az Azure HDInsight jelenleg a következő típusú fürtöket biztosítja, amelyek mindegyike tartalmaz bizonyos funkciókat biztosító összetevők készletét.

> [!IMPORTANT]  
> A HDInsight-fürtök különböző típusokban érhetők el, amelyek mindegyike egyetlen munkaterheléshez vagy technológiához használható. Nincs támogatott módszer a több típust egyesítő fürt létrehozására, például a Storm és a HBase egy fürtön. Ha a megoldás olyan technológiákat igényel, amelyek több HDInsight között oszlanak el, az Azure-beli [virtuális hálózatok](../virtual-network/index.yml) kapcsolódhatnak a szükséges fürtökhöz.

| Fürt típusa | Funkció |
| --- | --- |
| [Hadoop](hadoop/apache-hadoop-introduction.md) |A tárolt adatmennyiségek kötegelt lekérdezése és elemzése |
| [HBase](hbase/apache-hbase-overview.md) |Nagy mennyiségű séma nélküli, NoSQL-adatbázis feldolgozása |
| [Interaktív lekérdezés](./interactive-query/apache-interactive-query-get-started.md) |Memóriában történő gyorsítótárazás az interaktív és gyorsabb kaptár-lekérdezésekhez |
| [Kafka](kafka/apache-kafka-introduction.md) | Elosztott streaming platform, amely valós idejű adatfolyam-továbbítási adatcsatornák és alkalmazások létrehozására használható |
| [ML-szolgáltatások](r-server/r-server-overview.md) |Különböző big data statisztikák, prediktív modellezés és gépi tanulási képességek |
| [Spark](spark/apache-spark-overview.md) |Memóriabeli feldolgozás, interaktív lekérdezések, mikro-köteg adatfolyam-feldolgozás |
| [Storm](storm/apache-storm-overview.md) |Valós idejű eseményfeldolgozás |

#### <a name="version"></a>Verzió

Válassza ki a fürthöz tartozó HDInsight verzióját. További információ: [támogatott HDInsight-verziók](hdinsight-component-versioning.md#supported-hdinsight-versions).

### <a name="cluster-credentials"></a>Fürt hitelesítő adatai

A HDInsight-fürtök esetében két felhasználói fiókot is beállíthat a fürt létrehozása során:

* Fürt bejelentkezési felhasználóneve: az alapértelmezett Felhasználónév a *rendszergazda*. A Azure Portal alapszintű konfigurációját használja. Néha "fürt felhasználó" vagy "HTTP-felhasználó" néven is ismert.
* Secure Shell-(SSH-) Felhasználónév: a fürt SSH-n keresztüli kapcsolódásához használatos. További információ: az [SSH használata a HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

A HTTP-Felhasználónév a következő korlátozásokkal rendelkezik:

* Engedélyezett speciális karakterek: `_` és `@`
* Nem engedélyezett karakterek: #;. "", \/ : "! *? $ () {} [] <>|&--= +% ~ ^ Space
* Maximális hossz: 20

Az SSH-Felhasználónév a következő korlátozásokkal rendelkezik:

* Engedélyezett speciális karakterek: `_` és `@`
* Nem engedélyezett karakterek: #;. "", \/ : "! *? $ () {} [] <>|&--= +% ~ ^ Space
* Maximális hossz: 64
* Fenntartott nevek: Hadoop, felhasználók, oozie, struktúra, mapred, ambari-QA, Zookeeper, TEZ, hdfs, sqoop, fonal, hcat, AMS, hbase, Storm, Administrator, admin, felhasználó, Felhasználó1, test, Felhasználó2, test1, user3, Rendszergazda1, 1, 123, a, actuser, adm, admin2, ASPNET, Backup, konzol, David, vendég, János, tulajdonos, gyökér, kiszolgáló, SQL, támogatás, support_388945a0, sys, teszt2, test3, user4, user5, Spark

## <a name="storage"></a>Tárolás

![Fürt tárolási beállításai: HDFS-kompatibilis végpontok](./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-storage.png)

Bár a Hadoop helyszíni telepítése a elosztott fájlrendszer Hadoop (HDFS) használja a fürtön lévő tároláshoz, a felhőben a fürthöz csatlakozó tárolási végpontokat használja. A felhőalapú tárolás eszközzel biztonságosan törölheti a számításhoz használt HDInsight-fürtöket, miközben továbbra is megőrizheti az adatait.

A HDInsight-fürtök a következő tárolási lehetőségeket használhatják:

* 2. generációs Azure Data Lake Storage
* 1. generációs Azure Data Lake Storage
* Azure Storage általános célú v2
* Azure Storage általános célú v1
* Azure Storage Block-blob (**csak másodlagos tárolóként támogatott**)

További információ a tárolási lehetőségekről a HDInsight használatával: a [tárolási lehetőségek összehasonlítása az Azure HDInsight-fürtökkel való használatra](hdinsight-hadoop-compare-storage-options.md).

> [!WARNING]  
> A HDInsight-fürttől eltérő helyen található további Storage-fiók használata nem támogatott.

A konfiguráció során az alapértelmezett tárolási végponthoz meg kell adnia egy Azure Storage-fiók vagy Data Lake Storage blob-tárolóját. Az alapértelmezett tároló alkalmazás-és rendszernaplókat tartalmaz. Megadhat további társított Azure Storage-fiókokat és Data Lake Storage fiókokat is, amelyeket a fürt elérhet. A HDInsight-fürtnek és a függő tárolási fiókoknak ugyanabban az Azure-helyen kell lenniük.

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

> [!IMPORTANT]
> A Biztonságos tár átvitelének engedélyezése a fürt létrehozása után hibákat eredményezhet a Storage-fiókkal, és nem ajánlott. Jobb megoldás, ha olyan Storage-fiókkal szeretne új fürtöt létrehozni, amelyen már engedélyezve van a biztonságos átvitel.

> [!Note]  
> Az Azure HDInsight nem továbbítja automatikusan az Azure Storage-ban tárolt adatait az egyik régióból a másikba.

### <a name="metastore-settings"></a>Metaadattár-beállítások

Létrehozhat opcionális struktúrát vagy Apache Oozie metaadattárak is. Azonban nem minden típusú fürt támogatja a metaadattárak, és az Azure szinapszis Analytics nem kompatibilis a metaadattárak.

További információ: [külső metaadat-tárolók használata az Azure HDInsight-ben](./hdinsight-use-external-metadata-stores.md).

> [!IMPORTANT]  
> Egyéni metaadattár létrehozásakor ne használjon kötőjeleket, kötőjeleket és szóközöket az adatbázis nevében. Ennek hatására a fürt létrehozási folyamata sikertelen lehet.

#### <a name="sql-database-for-hive"></a>SQL Database for kaptár

Ha a HDInsight-fürt törlése után szeretné megőrizni a kaptár-táblákat, egyéni metaadattár használjon. Ezután a metaadattár egy másik HDInsight-fürthöz is csatolhatja.

Az egyik HDInsight-metaadattár létrehozott rendszerértékek nem oszthatók meg különböző HDInsight-fürt verziói között. An méretű HDInsight A HDInsight-verziók listáját lásd: [támogatott HDInsight-verziók](hdinsight-component-versioning.md#supported-hdinsight-versions).

> [!IMPORTANT]
> Az alapértelmezett metaadattár egy **alapszintű 5 DTU-korláttal (nem frissíthető)** rendelkező Azure SQL Database biztosít. Alapvető tesztelési célokra alkalmas. Nagyméretű vagy éles számítási feladatokhoz ajánlott áttelepíteni egy külső metaadattár.

#### <a name="sql-database-for-oozie"></a>SQL Database for Oozie

A Oozie használatakor a teljesítmény növeléséhez használjon egyéni metaadattár. A metaadattár a fürt törlése után is biztosíthat hozzáférést a Oozie-feladatok adataihoz.

#### <a name="sql-database-for-ambari"></a>SQL Database for Ambari

A Ambari a HDInsight-fürtök figyelésére, a konfiguráció módosítására, valamint a fürt felügyeleti információinak és a feladatok előzményeinek tárolására szolgál. Az egyéni Ambari adatbázis-szolgáltatás lehetővé teszi, hogy új fürtöt helyezzen üzembe, és beállítsa a Ambari egy olyan külső adatbázisban, amelyet Ön felügyel. További információ: [Custom AMBARI db](./hdinsight-custom-ambari-db.md).

> [!IMPORTANT]  
> Egyéni Oozie-metaadattár nem használható fel újra. Ha egyéni Oozie-metaadattár szeretne használni, üres Azure SQL Database kell megadnia a HDInsight-fürt létrehozásakor.

## <a name="security--networking"></a>Biztonság és hálózatkezelés

![hdinsight-létrehozási beállítások válassza a vállalati biztonsági csomag lehetőséget](./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-security-networking.png)

### <a name="enterprise-security-package"></a>Vállalati biztonsági csomag

A Hadoop, a Spark, a HBase, a Kafka és az interaktív lekérdezési fürt típusainál engedélyezheti a **Enterprise Security Package**. Ez a csomag lehetővé teszi, hogy az Apache Ranger használatával biztonságosabb fürtöt lehessen beállítani, és integrálja Azure Active Directory. További információ: [a vállalati biztonság áttekintése az Azure HDInsight-ben](./domain-joined/hdinsight-security-overview.md).

A nagyvállalati biztonsági csomag lehetővé teszi a HDInsight integrálását Active Directory és Apache Ranger használatával. A vállalati biztonsági csomag használatával több felhasználó is létrehozható.

A tartományhoz csatlakoztatott HDInsight-fürtök létrehozásával kapcsolatos további információkért lásd: [tartományhoz csatlakoztatott HDInsight homokozó környezet létrehozása](./domain-joined/apache-domain-joined-configure-using-azure-adds.md).

### <a name="tls"></a>TLS

További információ: [Transport Layer Security](./transport-layer-security.md)

### <a name="virtual-network"></a>Virtuális hálózat

Ha a megoldás olyan technológiákat igényel, amelyek több HDInsight között oszlanak el, az Azure-beli [virtuális hálózatok](../virtual-network/index.yml) kapcsolódhatnak a szükséges fürtökhöz. Ez a konfiguráció lehetővé teszi, hogy a fürtök és a rájuk telepített kódok közvetlenül kommunikálhassanak egymással.

További információ az Azure-beli virtuális hálózatok HDInsight használatával történő használatáról: [virtuális hálózat megtervezése HDInsight számára](hdinsight-plan-virtual-network-deployment.md).

Az Azure-beli virtuális hálózaton belüli két fürt típusának használatával kapcsolatban lásd: [Apache Spark strukturált adatfolyam használata Apache Kafka használatával](hdinsight-apache-kafka-spark-structured-streaming.md). További információ a HDInsight virtuális hálózattal történő használatáról, beleértve a virtuális hálózat speciális konfigurációs követelményeit: [virtuális hálózat megtervezése HDInsight számára](hdinsight-plan-virtual-network-deployment.md).

### <a name="disk-encryption-setting"></a>Lemez titkosításának beállítása

További információ: [ügyfél által felügyelt kulcs lemezének titkosítása](./disk-encryption.md).

### <a name="kafka-rest-proxy"></a>Kafka REST proxy

Ez a beállítás csak a Kafka típusú fürtök esetében érhető el. További információ: [Rest proxy használata](./kafka/rest-proxy.md).

### <a name="identity"></a>Identitás

További információ: [felügyelt identitások az Azure HDInsight-ben](./hdinsight-managed-identities.md).

## <a name="configuration--pricing"></a>Konfiguráció és díjszabás

![HDInsight válassza ki a csomópont méretét](./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-configuration.png)

A csomópont-használatért díjat számítunk fel, amíg a fürt létezik. A számlázás akkor kezdődik, amikor létrejön egy fürt, és leáll, amikor a fürt törlődik. A fürtöket nem lehet kiosztani vagy lefoglalni.

### <a name="node-configuration"></a>Csomópont-konfiguráció

Mindegyik fürt típusa saját számú csomóponttal, csomópontok terminológiával és alapértelmezett virtuálisgép-mérettel rendelkezik. Az alábbi táblázatban az egyes csomópont-típusok csomópontjainak száma zárójelben van.

| Típus | Csomópontok | Ábra |
| --- | --- | --- |
| Hadoop |Fő csomópont (2), munkavégző csomópont (1 +) |![HDInsight Hadoop](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-hadoop-cluster-type-nodes.png) |
| HBase |Head Server (2), régió-kiszolgáló (1 +), fő/ZooKeeper csomópont (3) |![HDInsight HBase-fürt típusának beállítása](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-hbase-cluster-type-setup.png) |
| Storm |Nimbus csomópont (2), felügyeleti kiszolgáló (1 +), ZooKeeper csomópont (3) |![HDInsight Storm-fürt típusának beállítása](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-storm-cluster-type-setup.png) |
| Spark |Fő csomópont (2), munkavégző csomópont (1 +), ZooKeeper-csomópont (3) (az A1-es ZooKeeper VM-mérethez ingyenes) |![HDInsight Spark-fürt típusának beállítása](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-spark-cluster-type-setup.png) |

További információ: az [alapértelmezett csomópont-konfiguráció és a fürtök virtuálisgép-mérete](hdinsight-component-versioning.md#default-node-configuration-and-virtual-machine-sizes-for-clusters) a "mi a Hadoop-összetevők és-verziók a HDInsight-ben?"

A HDInsight-fürtök költségeit a csomópontok száma és a csomópontok virtuális gépei mérete határozza meg.

A különböző típusú fürtök eltérő csomópont-típusokkal, csomópont-számokkal és csomópont-méretekkel rendelkeznek:
* Hadoop-fürt típusának alapértelmezett értéke:
    * Két *fő csomópont*  
    * Négy *munkavégző csomópont*
* Storm-fürt típusa alapértelmezett:
    * Két *Nimbus-csomópont*
    * Három *ZooKeeper-csomópont*
    * Négy *felügyeleti csomópont*

Ha csak kipróbálja a HDInsight, javasoljuk, hogy használjon egy feldolgozói csomópontot. További információ a HDInsight díjszabásáról: [HDInsight díjszabása](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

> [!NOTE]  
> A fürt méretének korlátozása az Azure-előfizetések között változhat. A korlát növeléséhez vegye fel a kapcsolatot az [Azure számlázási támogatási szolgálatával](../azure-portal/supportability/how-to-create-azure-support-request.md) .

Ha a Azure Portal a fürt konfigurálására használja, a csomópont mérete a **konfiguráció + díjszabás** lapon érhető el. A portálon megtekintheti a különböző csomópont-méretekhez kapcsolódó költségeket is.

### <a name="virtual-machine-sizes"></a>Virtuálisgép-méretek

Fürtök telepítésekor a telepítendő megoldás alapján válassza a számítási erőforrások lehetőséget. A következő virtuális gépek használatosak a HDInsight-fürtökhöz:

* A és D1-4 sorozatú virtuális gépek: [általános célú LINUXOS VM-méretek](../virtual-machines/sizes-general.md)
* D11-14 sorozatú virtuális gép: [memóriára optimalizált LINUXOS VM-méretek](../virtual-machines/sizes-memory.md)

Annak megállapításához, hogy milyen értéket kell használnia a virtuális gép méretének megadásához a különböző SDK-k használatával vagy a Azure PowerShell használata közben, lásd: [HDInsight-fürtökhöz használandó](../cloud-services/cloud-services-sizes-specs.md#size-tables)virtuálisgép-méretek. Ebből a csatolt cikkből a táblázatok **méret** oszlopában található értéket használja.

> [!IMPORTANT]  
> Ha a fürtben több mint 32 feldolgozó csomópontra van szüksége, ki kell választania egy fő csomópont-méretet legalább 8 maggal és 14 GB RAM-mal.

További információ: [virtuális gépek méretei](../virtual-machines/sizes.md). További információ a különböző méretek díjszabásáról: [HDInsight díjszabása](https://azure.microsoft.com/pricing/details/hdinsight).

### <a name="add-application"></a>Alkalmazás hozzáadása

A HDInsight-alkalmazások olyan alkalmazások, amelyeket a felhasználók egy Linux-alapú HDInsight-fürtre telepíthetnek. Használhatja a Microsoft vagy harmadik felek által biztosított alkalmazásokat, illetve saját maga is fejlesztheti azokat. További információ: [külső féltől származó Apache Hadoop alkalmazások telepítése az Azure HDInsight](hdinsight-apps-install-applications.md).

A HDInsight alkalmazások többsége üres peremhálózati csomópontra van telepítve.  Az üres peremhálózati csomópont egy Linux rendszerű virtuális gép, amelyen ugyanaz az ügyféleszközök van telepítve és konfigurálva, mint a fő csomópontban. Használhatja a peremhálózati csomópontot a fürt eléréséhez, az ügyfélalkalmazások teszteléséhez és az ügyfélalkalmazások üzemeltetéséhez. További információ: [üres peremhálózati csomópontok használata a HDInsight-ben](hdinsight-apps-use-edge-node.md).

### <a name="script-actions"></a>Parancsfájlok műveletei

További összetevőket is telepíthet, vagy testre szabhatja a fürt konfigurációját parancsfájlok használatával a létrehozás során. Az ilyen parancsfájlokat parancsfájl- **művelettel** hívja meg, amely a Azure Portal, a HDInsight Windows PowerShell-parancsmagok vagy a HDINSIGHT .net SDK használatával használható konfigurációs lehetőség. További információ: a [HDInsight-fürt testreszabása parancsfájl-művelettel](hdinsight-hadoop-customize-cluster-linux.md).

Egyes natív Java-összetevők, például az Apache Mahout és a kaszkádolt, Java Archive (JAR) fájlokként futtathatók a fürtön. Ezek a JAR-fájlok eloszthatók az Azure Storage-ba, és a Hadoop-feladatok beküldési mechanizmusaival HDInsight-fürtökbe elküldve További információt a [Apache Hadoop feladatok programozott beküldése](hadoop/submit-apache-hadoop-jobs-programmatically.md)című témakörben talál.

> [!NOTE]  
> Ha problémába ütközik a JAR-fájlok HDInsight-fürtökre való telepítésekor vagy JAR-fájlok HDInsight-fürtökön való meghívásakor, forduljon a [Microsoft ügyfélszolgálatahoz](https://azure.microsoft.com/support/options/).
>
> A HDInsight nem támogatja a kaszkádolt használatát, és nem jogosult Microsoft ügyfélszolgálatara. A támogatott összetevők listáját a [HDInsight által biztosított fürtcsomópontok újdonságai](hdinsight-component-versioning.md)című témakörben tekintheti meg.

Időnként a következő konfigurációs fájlokat szeretné konfigurálni a létrehozási folyamat során:

* clusterIdentity.xml
* core-site.xml
* gateway.xml
* hbase-env.xml
* hbase-site.xml
* hdfs-site.xml
* hive-env.xml
* hive-site.xml
* mapred – hely
* oozie-site.xml
* oozie-env.xml
* storm-site.xml
* tez-site.xml
* webhcat-site.xml
* yarn-site.xml

További információ: HDInsight- [fürtök testreszabása a bootstrap használatával](hdinsight-hadoop-customize-cluster-bootstrap.md).

## <a name="next-steps"></a>Következő lépések

* [Fürtök létrehozásával kapcsolatos hibák elhárítása az Azure HDInsight](./hadoop/hdinsight-troubleshoot-cluster-creation-fails.md)
* [Mik azok a HDInsight, a Apache Hadoop ökoszisztémák és a Hadoop-fürtök?](hadoop/apache-hadoop-introduction.md)
* [A Apache Hadoop használatának első lépései a HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Munkahelyi Apache Hadoop HDInsight Windows rendszerű SZÁMÍTÓGÉPRŐL](hdinsight-hadoop-windows-tools.md)