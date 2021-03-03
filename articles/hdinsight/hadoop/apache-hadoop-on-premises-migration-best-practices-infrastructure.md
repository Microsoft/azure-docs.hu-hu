---
title: 'Infrastruktúra: helyszíni Apache Hadoop az Azure HDInsight'
description: Ismerje meg a helyszíni Hadoop-fürtök Azure HDInsight való áttelepítésére vonatkozó ajánlott eljárásokat.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: 309053c2d7a0f9482016f1bd83e0c61dcd31bec5
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101740664"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---infrastructure-best-practices"></a>Helyszíni Apache Hadoop-fürtök migrálása az Azure HDInsight-infrastruktúrára – ajánlott eljárások

Ez a cikk ajánlásokat nyújt az Azure HDInsight-fürtök infrastruktúrájának kezeléséhez. Egy sorozat része, amely ajánlott eljárásokat biztosít a helyszíni Apache Hadoop rendszerek Azure HDInsight való áttelepítésének segítésére.

## <a name="plan-for-hdinsight-cluster-capacity"></a>A HDInsight-fürt kapacitásának megtervezése

A HDInsight-fürt kapacitásának megtervezéséhez szükséges legfontosabb lehetőségek a következők:

**Régió**  
Az Azure-régió meghatározza, hogy a fürt hol van fizikailag kiépítve. Az olvasási és írási késés csökkentése érdekében a fürtnek ugyanabban a régióban kell lennie, mint az adatokat.

**Tárolási hely és méret**  
Az alapértelmezett tárolónak ugyanabban a régióban kell lennie, mint a fürtnek. 48 csomópontos fürt esetén ajánlott 4 – 8 Storage-fiók használata. Bár lehetséges, hogy a teljes tárterület már elegendő, az egyes Storage-fiókok további hálózati sávszélességet biztosítanak a számítási csomópontok számára. Ha több Storage-fiók van, akkor minden egyes Storage-fiókhoz használjon véletlenszerű nevet, előtag nélkül. A véletlenszerű elnevezések célja, hogy csökkentse a tárolási torlódások (szabályozás) és az összes fiók általános módú meghibásodásának esélyét. A jobb teljesítmény érdekében csak egy tárolót használjon Storage-fiókkal.

**VM-méret és-típus (mostantól támogatja a G sorozatot)**  
Minden egyes fürt típusa csomópont típusú, és mindegyik csomópont típusa speciális beállításokkal rendelkezik a virtuálisgép-mérethez és-típushoz. A virtuális gép méretét és típusát a CPU feldolgozási teljesítmény, a RAM mérete és a hálózati késés határozza meg. A virtuális gépek optimális méretének és típusának meghatározásához szimulált számítási feladatok használhatók.

**Munkavégző csomópontok száma**  
A feldolgozó csomópontok kezdeti száma a szimulált munkaterhelések használatával határozható meg. A fürt később is méretezhető, ha további munkavégző csomópontokat ad hozzá a maximális terhelési igények kielégítéséhez. A fürt később is méretezhető, ha a további munkavégző csomópontok nem szükségesek.

További információt a [HDInsight-fürtök kapacitásának megtervezése](../hdinsight-capacity-planning.md)című cikkben talál.

## <a name="use-recommended-virtual-machine-type-for-cluster"></a>Ajánlott virtuálisgép-típus használata a fürthöz

Az egyes HDInsight-fürtökhöz ajánlott virtuálisgép-típusokat az [alapértelmezett csomópont-konfiguráció és a virtuális gépek méretei](../hdinsight-supported-node-configuration.md) című témakörben talál.

## <a name="check-hadoop-components-availability-in-hdinsight"></a>Hadoop-összetevők rendelkezésre állásának keresése a HDInsight-ben

Minden HDInsight-verzió a Hadoop Eco-System összetevők egy halmazának Felhőbeli eloszlása. A HDInsight összes összetevőjével és jelenlegi verziójával kapcsolatos részletekért tekintse meg a [HDInsight összetevő verziószámozását](../hdinsight-component-versioning.md) .

Az Apache Ambari felhasználói felületén vagy a Ambari REST API segítségével is megtekintheti a Hadoop összetevőit és verzióit a HDInsight-ben.

A helyszíni fürtökben elérhető, de nem a HDInsight-fürtök részét képező alkalmazások vagy összetevők hozzáadhatók egy peremhálózati csomóponton vagy egy olyan virtuális gépen, amely ugyanabban a VNet található, mint a HDInsight-fürt. Az Azure HDInsight nem elérhető külső gyártótól származó Hadoop alkalmazás a HDInsight-fürt "alkalmazások" lehetőségével telepíthető. Az egyéni Hadoop alkalmazások a HDInsight-fürtökön a "parancsfájl-műveletek" használatával telepíthetők. A következő táblázat néhány gyakori alkalmazást és azok HDInsight-integrációs lehetőségeit sorolja fel:

|**Alkalmazás**|**Integráció**
|---|---|
|Légáramlás|IaaS vagy HDInsight Edge-csomópont
|Alluxio|IaaS  
|Arcadia|IaaS 
|Atlaszi|Nincs (csak HDP)
|Datameer|HDInsight Edge-csomópont
|Datastax (Cassandra)|IaaS (alternatív CosmosDB az Azure-on)
|DataTorrent|IaaS 
|Drill|IaaS 
|Ignite|IaaS
|Jethro|IaaS 
|Mapador|IaaS 
|Mongo|IaaS (alternatív CosmosDB az Azure-on)
|NiFi|IaaS 
|Presto|IaaS vagy HDInsight Edge-csomópont
|Python 2|PaaS 
|Python 3|PaaS 
|R|PaaS 
|SAS|IaaS 
|Vertica|IaaS (alternatív SQLDW az Azure-on)
|Tableau|IaaS 
|Vízvonal|HDInsight Edge-csomópont
|StreamSets|HDInsight Edge 
|Palantír|IaaS 
|Sailpoint|IaaS 

További információ: [Apache Hadoop különböző HDInsight-verziókban elérhető összetevők](../hdinsight-component-versioning.md) .

## <a name="customize-hdinsight-clusters-using-script-actions"></a>HDInsight-fürtök testreszabása parancsfájl-műveletek használatával

A HDInsight egy **parancsfájl-műveletnek** nevezett fürtkonfiguráció-metódust biztosít. A parancsfájl művelet olyan bash-parancsfájl, amely egy HDInsight-fürt csomópontjain fut, és további összetevők telepítéséhez és a konfigurációs beállítások módosításához használható.

A parancsfájl-műveleteket olyan URI-n kell tárolni, amely elérhető a HDInsight-fürtből. A fürtök létrehozása során vagy után is használhatók, és csak bizonyos csomópontok esetében korlátozhatók.

A parancsfájl egy alkalommal megtartható vagy végrehajtható. A megőrzött parancsfájlok a fürthöz a skálázási műveletekkel hozzáadott új munkavégző csomópontok testreszabására szolgálnak. A megőrzött parancsfájlok a skálázási műveletek végrehajtásakor is alkalmazhatják a módosításokat egy másik csomópont-típusra, például egy fő csomópontra.

A HDInsight előre megírt parancsfájlokat biztosít a következő összetevők telepítéséhez a HDInsight-fürtökön:

- Azure Storage-fiók hozzáadása
- A Hue telepítése
- A Presto telepítése
- A Solr telepítése
- A Giraph telepítése
- Struktúra-kódtárak előzetes betöltése
- Mono telepítése vagy frissítése

> [!Note]  
> A HDInsight nem biztosít közvetlen támogatást a parancsfájl-műveletek használatával telepített egyéni Hadoop összetevőkhöz vagy összetevőkhöz.

A parancsfájlok műveletei az Azure Marketplace-en is HDInsight-alkalmazásként közzétehetők.

További információért tekintse át a következő cikkeket:

- [Külső gyártótól származó Apache Hadoop alkalmazások telepítése a HDInsight-on](../hdinsight-apps-install-applications.md)
- [HDInsight-fürtök testreszabása parancsfájl-műveletek használatával](../hdinsight-hadoop-customize-cluster-linux.md)
- [HDInsight-alkalmazás közzététele az Azure Marketplace-en](../hdinsight-apps-publish-applications.md)

## <a name="customize-hdinsight-configs-using-bootstrap"></a>HDInsight-konfigurációk testreszabása a bootstrap használatával

A konfigurációs fájlokban lévő konfigurációk módosításai, például a `core-site.xml` , `hive-site.xml` és a `oozie-env.xml` bootstrap használatával hozhatók létre. A következő szkript egy példa a PowerShell az [Module](/powershell/azure/new-azureps-module-az) parancsmag [New-AzHDInsightClusterConfig](/powershell/module/az.hdinsight/new-azhdinsightcluster)használatával:

```powershell
# hive-site.xml configuration
$hiveConfigValues = @{"hive.metastore.client.socket.timeout"="90"}

$config = New—AzHDInsightClusterConfig '
    | Set—AzHDInsightDefaultStorage
    —StorageAccountName "$defaultStorageAccountName.blob. core.windows.net" `
    —StorageAccountKey "defaultStorageAccountKey " `
    | Add—AzHDInsightConfigValues `
        —HiveSite $hiveConfigValues

New—AzHDInsightCluster `
    —ResourceGroupName $existingResourceGroupName `
    —Cluster-Name $clusterName `
    —Location $location `
    —ClusterSizeInNodes $clusterSizeInNodes `
    —ClusterType Hadoop `
    —OSType Linux `
    —Version "3.6" `
    —HttpCredential $httpCredential `
    —Config $config
```

További információ: [HDInsight-fürtök testreszabása a bootstrap használatával](../hdinsight-hadoop-customize-cluster-bootstrap.md).  Lásd még: [HDInsight-fürtök kezelése az Apache Ambari REST API használatával](../hdinsight-hadoop-manage-ambari-rest-api.md).

## <a name="access-client-tools-from-hdinsight-hadoop-cluster-edge-nodes"></a>A HDInsight Hadoop-fürt peremhálózati csomópontjainak elérése az ügyféleszközök számára

Az üres peremhálózati csomópont egy Linux rendszerű virtuális gép, amely ugyanazokkal az eszközökkel van telepítve és konfigurálva, mint a fő csomópontok, de nem fut Hadoop-szolgáltatás. A peremhálózati csomópont a következő célokra használható:

- a fürt elérése
- ügyfélalkalmazások tesztelése
- ügyfélalkalmazások üzemeltetése

Az Edge-csomópontok létrehozhatók és törölhetők a Azure Portalon keresztül, és a fürt létrehozásakor vagy után is használhatók. Az Edge-csomópont létrehozása után az SSH használatával kapcsolódhat a peremhálózati csomóponthoz, és az ügyféleszközök futtatásával érheti el a Hadoop-fürtöt a HDInsight-ben. Az Edge Node SSH-végpontja `<EdgeNodeName>.<ClusterName>-ssh.azurehdinsight.net:22` .


További információkért tekintse meg a következő cikket: [üres peremhálózati csomópontok használata Apache Hadoop fürtökön a HDInsight-ben](../hdinsight-apps-use-edge-node.md).


## <a name="use-scale-up-and-scale-down-feature-of-clusters"></a>Fürtök vertikális felskálázásával és leskálázásával kapcsolatos funkciójának használata

A HDInsight rugalmasságot biztosít azáltal, hogy lehetővé teszi a fürtökben lévő munkavégző csomópontok számának vertikális felskálázását és méretezését. Ez a funkció lehetővé teszi, hogy a fürtöket órák vagy hétvégék után, az üzleti igényeknek megfelelően bővítse. További információkért lásd:

* [HDInsight-fürtök méretezése](../hdinsight-scaling-best-practices.md).
* [Fürtök méretezése](../hdinsight-administer-use-portal-linux.md#scale-clusters).

## <a name="use-hdinsight-with-azure-virtual-network"></a>A HDInsight használata az Azure Virtual Network

Az Azure Virtual Networks lehetővé teszi az Azure-erőforrások (például az Azure Virtual Machines) biztonságos kommunikációját egymással, az internettel és a helyszíni hálózatokkal a hálózati forgalom szűrésével és útválasztásával.

Az Azure Virtual Network és a HDInsight használata a következő forgatókönyveket teszi lehetővé:

- Csatlakozás a HDInsight közvetlenül egy helyszíni hálózatról.
- HDInsight csatlakoztatása az adattárakhoz egy Azure-beli virtuális hálózaton.
- Közvetlenül az interneten keresztül nyilvánosan nem elérhető Hadoop-szolgáltatásokhoz férhet hozzá. Például: Kafka API-k vagy a HBase Java API.

A HDInsight új vagy meglévő Azure-Virtual Networkhoz is hozzáadhatók. Ha a HDInsight bekerül egy meglévő Virtual Networkba, a meglévő hálózati biztonsági csoportokat és a felhasználó által megadott útvonalakat frissíteni kell, hogy az Azure-adatközpont [több IP-címéhez](../hdinsight-management-ip-addresses.md) való korlátlan hozzáférést engedélyezzen. Ügyeljen arra is, hogy ne tiltsa le a HDInsight-szolgáltatások által használt [portokra](../control-network-traffic.md#required-ports)irányuló forgalmat.

> [!Note]  
> A HDInsight jelenleg nem támogatja a kényszerített bújtatást. A kényszerített bújtatás egy olyan alhálózat-beállítás, amely az eszközre irányuló kimenő internetes forgalmat ellenőrzés és naplózás céljából kényszeríti. Távolítsa el a kényszerített bújtatást, mielőtt telepítené a HDInsight egy alhálózatba, vagy hozzon létre egy új alhálózatot a HDInsight. A HDInsight emellett nem támogatja a kimenő hálózati kapcsolatok korlátozását.

További információért tekintse át a következő cikkeket:

- [Azure Virtual-Networks – áttekintés](../../virtual-network/virtual-networks-overview.md)
- [Azure HDInsight kiterjesztése Azure virtuális hálózat használatával](../hdinsight-plan-virtual-network-deployment.md)

## <a name="securely-connect-to-azure-services-with-azure-virtual-network-service-endpoints"></a>Biztonságos kapcsolódás az Azure-szolgáltatásokhoz az Azure Virtual Network Service-végpontokkal

A HDInsight támogatja a [virtuális hálózati szolgáltatás-végpontokat](../../virtual-network/virtual-network-service-endpoints-overview.md), amelyek lehetővé teszik az Azure Blob Storage, Azure Data Lake Storage Gen2, Cosmos db és SQL-adatbázisokhoz való biztonságos kapcsolódást. A szolgáltatás-végpont Azure HDInsight való engedélyezésével a forgalom a biztonságos útvonalon halad át az Azure-adatközponton belül. Ezzel a fokozott biztonsággal a hálózati rétegben zárolhatja big data Storage-fiókjait a megadott virtuális hálózatokra (virtuális hálózatok), és továbbra is zökkenőmentesen használhatja a HDInsight-fürtöket az adateléréshez és a feldolgozáshoz.

További információért tekintse át a következő cikkeket:

- [Virtuális hálózati szolgáltatásvégpontok](../../virtual-network/virtual-network-service-endpoints-overview.md)
- [A HDInsight biztonság növelése a szolgáltatási végpontokkal](https://azure.microsoft.com/blog/enhance-hdinsight-security-with-service-endpoints/)

## <a name="connect-hdinsight-to-the-on-premises-network"></a>HDInsight összekapcsolása a helyszíni hálózattal

A HDInsight Azure-beli virtuális hálózatok és VPN-átjáró használatával csatlakoztathatók a helyszíni hálózathoz. A kapcsolat létesítéséhez a következő lépések használhatók:

- HDInsight használata olyan Azure-Virtual Network, amely a helyszíni hálózathoz kapcsolódik.
- Konfigurálja a DNS-névfeloldást a virtuális hálózat és a helyszíni hálózat között.
- Konfigurálja a hálózati biztonsági csoportokat vagy a felhasználó által megadott útvonalakat (UDR) a hálózati forgalom szabályozásához.

További információ: a [HDInsight összekapcsolása a helyszíni hálózattal](../connect-on-premises-network.md) .

## <a name="next-steps"></a>Következő lépések

Olvassa el a következő cikket ebben a sorozatban: [a helyszíni tárolásra vonatkozó ajánlott eljárásokat a Azure HDInsight Hadoop áttelepítéshez](apache-hadoop-on-premises-migration-best-practices-storage.md).