---
title: HBase-fürtök létrehozása egy Virtual Networkban – Azure
description: A HBase használatának első lépései az Azure HDInsight. Ismerje meg, hogyan hozhat létre HDInsight HBase-fürtöket az Azure Virtual Network.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/23/2019
ms.openlocfilehash: 9f179981aa39402681b4830d58a29f5b1259c7e2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98946127"
---
# <a name="create-apache-hbase-clusters-on-hdinsight-in-azure-virtual-network"></a>Apache HBase-fürtök létrehozása a HDInsight-ben az Azure-ban Virtual Network

Ismerje meg, hogyan hozhat létre Azure HDInsight Apache HBase-fürtöket egy [Azure-Virtual Network](https://azure.microsoft.com/services/virtual-network/).

A Virtual Network Integration használatával az Apache HBase-fürtök ugyanarra a virtuális hálózatra telepíthetők, mint az alkalmazások, így az alkalmazások közvetlenül kommunikálhatnak a HBase. Ez a következő előnyöket nyújtja:

* A webalkalmazás közvetlen kapcsolódása a HBase-fürt csomópontjaihoz, amely lehetővé teszi a kommunikációt a HBase Java távoli eljáráshívás (RPC) API-kon keresztül.
* Jobb teljesítmény azáltal, hogy a forgalom nem a több átjáróra és terheléselosztó-ra mutat.
* A bizalmas adatok biztonságos módon történő feldolgozásának lehetősége a nyilvános végpontok kimutatása nélkül.

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="create-apache-hbase-cluster-into-virtual-network"></a>Apache HBase-fürt létrehozása virtuális hálózatban

Ebben a szakaszban egy Linux-alapú Apache HBase-fürtöt hoz létre egy Azure-beli virtuális hálózatban található függő Azure Storage-fiókkal egy [Azure Resource Manager sablon](../../azure-resource-manager/templates/deploy-powershell.md)használatával. Más fürtök létrehozási módszereivel és a beállítások megismerésével kapcsolatban lásd: [HDInsight-fürtök létrehozása](../hdinsight-hadoop-provision-linux-clusters.md). A Apache Hadoop-fürtök HDInsight való létrehozásával kapcsolatos további információkért lásd: [Apache Hadoop-fürtök létrehozása a HDInsight-ben Azure Resource Manager sablonok használatával](../hdinsight-hadoop-create-linux-clusters-arm-templates.md)

> [!NOTE]  
> Néhány tulajdonságot a sablonban rögzítettek. Például:
>
> * Hely: USA 2. keleti **régiója**
> * **Fürt verziója**: 3.6
> * **Fürt munkavégző csomópontjainak száma**: 2
> * **Alapértelmezett Storage-fiók**: egyedi karakterlánc
> * **Virtuális hálózat neve**: CLUSTERNAME-vnet
> * **Virtuális hálózati címtartomány**: 10.0.0.0/16
> * **Alhálózat neve**: subnet1
> * **Alhálózati címtartomány**: 10.0.0.0/24
>
> `CLUSTERNAME` a a sablon használatakor megadott fürt nevével lesz lecserélve.

1. Válassza ki az alábbi képet a sablon megnyitásához a Azure Portal. A sablon az [Azure Gyorsindítás sablonjaiban](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-linux-vnet/)található.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux-vnet%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-provision-vnet/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

1. Az **Egyéni telepítés** párbeszédpanelen válassza a **Sablon szerkesztése** lehetőséget.

1. Az 165. sorban módosítsa az értéket a következőre: `Standard_A3` `Standard_A4_V2` . Kattintson a **Mentés** gombra.

1. Fejezze be a fennmaradó sablont a következő információkkal:

    |Tulajdonság |Érték |
    |---|---|
    |Előfizetés|Válasszon ki egy Azure-előfizetést, amely a HDInsight-fürt, a függő Storage-fiók és az Azure virtuális hálózat létrehozásához használatos.|
    Erőforráscsoport|Válassza az **új létrehozása** lehetőséget, és adjon meg egy új erőforráscsoport-nevet.|
    |Hely|Válasszon egy helyet az erőforráscsoportnak.|
    |Fürt neve|Adja meg a létrehozandó Hadoop-fürt nevét.|
    |Fürt bejelentkezési felhasználónevének és jelszavának megadása|Az alapértelmezett Felhasználónév a **rendszergazda**. Adja meg a jelszót.|
    |SSH-Felhasználónév és-jelszó|Az alapértelmezett Felhasználónév a **sshuser**.  Adja meg a jelszót.|

    Válassza **az Elfogadom a fenti feltételeket és a fent leírt feltételeket**.

1. Válassza a **Beszerzés** lehetőséget. Egy fürt létrehozása nagyjából 20 percet vesz igénybe. A fürt létrehozása után kiválaszthatja a fürtöt a portálon a megnyitásához.

A cikk befejezése után érdemes lehet törölni a fürtöt. A HDInsight az Azure Storage szolgáltatásban tárolja az adatokat, így biztonságosan törölhet olyan fürtöket, amelyek nincsenek használatban. Ráadásul a HDInsight-fürtök akkor is díjkötelesek, amikor éppen nincsenek használatban. Mivel a fürt költsége a sokszorosa a tároló költségeinek, gazdaságossági szempontból is ésszerű törölni a használaton kívüli fürtöket. A fürtök törlésére vonatkozó utasításokért lásd: [Apache Hadoop-fürtök kezelése a HDInsight-ben a Azure Portal használatával](../hdinsight-administer-use-portal-linux.md#delete-clusters).

Az új HBase-fürt használatának megkezdéséhez használja az első [lépések az Apache Apache Hadoop HBase használata a HDInsight-ben](./apache-hbase-tutorial-get-started-linux.md)című témakörben található eljárásokat.

## <a name="connect-to-the-apache-hbase-cluster-using-apache-hbase-java-rpc-apis"></a>Kapcsolódás az Apache HBase-fürthöz Apache HBase Java RPC API-k használatával

### <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

Hozzon létre egy infrastruktúra-szolgáltatási (IaaS) virtuális gépet ugyanabba az Azure-beli virtuális hálózatba és ugyanahhoz az alhálózatba. Az új IaaS virtuális gépek létrehozásával kapcsolatos utasításokért lásd: [Windows Server rendszerű virtuális gép létrehozása](../../virtual-machines/windows/quick-create-portal.md). A jelen dokumentum lépéseinek követése során a következő értékeket kell használnia a hálózati konfigurációhoz:

* **Virtuális hálózat**: CLUSTERNAME-vnet
* **Alhálózat**: subnet1

> [!IMPORTANT]  
> Cserélje le a `CLUSTERNAME` nevet a HDInsight-fürt előző lépésekben való létrehozásakor használt névre.

Ezeknek az értékeknek a használatával a virtuális gép ugyanahhoz a virtuális hálózathoz és alhálózathoz kerül, mint a HDInsight-fürt. Ez a konfiguráció lehetővé teszi, hogy közvetlenül kommunikáljanak egymással. Egy üres peremhálózati csomóponttal rendelkező HDInsight-fürtöt is létrehozhat. A peremhálózati csomópontot a fürt kezelésére használhatja.  További információ: [üres peremhálózati csomópontok használata a HDInsight-ben](../hdinsight-apps-use-edge-node.md).

### <a name="obtain-fully-qualified-domain-name"></a>Teljes tartománynév beszerzése

Ha Java-alkalmazás használatával távolról csatlakozik a HBase, a teljes tartománynevet (FQDN) kell használnia. Ennek megállapításához be kell szereznie a HBase-fürt kapcsolatspecifikus DNS-utótagját. Ehhez a következő módszerek egyikét használhatja:

* [Apache Ambari](https://ambari.apache.org/) -hívás létrehozása webböngésző használatával:

    Nyissa meg a következő címet: `https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts?minimal_response=true`. A DNS-utótagokat tartalmazó JSON-fájlt ad vissza.

* A Ambari webhely használata:

    1. Nyissa meg a következő címet: `https://CLUSTERNAME.azurehdinsight.net`.
    2. A felső menüben válassza a **gazdagépek** lehetőséget.

* REST-hívások a curl használatával:

    ```bash
    curl -u <username>:<password> -k https://CLUSTERNAME.azurehdinsight.net/ambari/api/v1/clusters/CLUSTERNAME.azurehdinsight.net/services/hbase/components/hbrest
    ```

A visszaadott JavaScript Object Notation-(JSON-) adatban keresse meg a "host_name" bejegyzést. Tartalmazza a fürt csomópontjainak teljes tartománynevét. Például:

```
"host_name" : "hn0-hbaseg.hjfrnszlumfuhfk4pi1guh410c.bx.internal.cloudapp.net"
```

A tartománynév a fürt nevétől kezdődő része a DNS-utótag. Például: `hjfrnszlumfuhfk4pi1guh410c.bx.internal.cloudapp.net`.

<!--
3.    Change the primary DNS suffix configuration of the virtual machine. This enables the virtual machine to automatically resolve the host name of the HBase cluster without explicit specification of the suffix. For example, the *workernode0* host name will be correctly resolved to workernode0 of the HBase cluster.

    To make the configuration change:

    1. RDP into the virtual machine.
    2. Open **Local Group Policy Editor**. The executable is gpedit.msc.
    3. Expand **Computer Configuration**, expand **Administrative Templates**, expand **Network**, and then click **DNS Client**.
    - Set **Primary DNS Suffix** to the value obtained in step 2:

        ![hdinsight.hbase.primary.dns.suffix](./media/apache-hbase-provision-vnet/hdi-primary-dns-suffix.png)
    4. Click **OK**.
    5. Reboot the virtual machine.
-->

### <a name="verify-communication-inside-virtual-network"></a>A virtuális hálózaton belüli kommunikáció ellenőrzése

Annak ellenőrzéséhez, hogy a virtuális gép tud-e kommunikálni a HBase-fürttel, használja a parancsot a `ping headnode0.<dns suffix>` virtuális gépről. Például: `ping hn0-hbaseg.hjfrnszlumfuhfk4pi1guh410c.bx.internal.cloudapp.net`.

Ha ezt az információt Java-alkalmazásokban szeretné használni, kövesse az [Apache Maven használata olyan Java-alkalmazások létrehozásához, amelyek Apache HBase-t használnak a HDInsight (Hadoop)](./apache-hbase-build-java-maven-linux.md) alkalmazással. Ha azt szeretné, hogy az alkalmazás kapcsolódjon egy távoli HBase-kiszolgálóhoz, módosítsa az ebben a példában szereplő **hbase-site.xml** fájlt, hogy a teljes tartománynevet használja a Zookeeper. Például:

```xml
<property>
    <name>hbase.zookeeper.quorum</name>
    <value>zookeeper0.<dns suffix>,zookeeper1.<dns suffix>,zookeeper2.<dns suffix></value>
</property>
```

> [!NOTE]  
> További információ az Azure-beli virtuális hálózatok névfeloldásáról, beleértve a saját DNS-kiszolgáló használatának módját: [névfeloldás (DNS)](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan hozhat létre Apache HBase-fürtöt. További információ:

* [Ismerkedés a HDInsight szolgáltatással](../hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Üres peremhálózati csomópontok használata a HDInsight](../hdinsight-apps-use-edge-node.md)
* [Apache HBase-replikálás konfigurálása a HDInsight-ben](apache-hbase-replication.md)
* [Apache Hadoop-fürtök létrehozása a HDInsight-ben](../hdinsight-hadoop-provision-linux-clusters.md)
* [Az Apache HBase használatának első lépései a HDInsight Apache Hadoop](./apache-hbase-tutorial-get-started-linux.md)
* [Virtual Network áttekintése](../../virtual-network/virtual-networks-overview.md)
