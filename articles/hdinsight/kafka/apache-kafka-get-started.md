---
title: A HDInsight Apache Kafka beállítása a Azure Portal-gyors útmutató használatával
description: Ebben a rövid útmutatóban megismerheti, hogyan hozhat létre Apache Kafka-fürtöt az Azure HDInsightban az Azure Portal használatával. A Kafka-témakörökről, -előfizetőkről és -fogyasztókról is olvashat.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: mvc
ms.topic: quickstart
ms.date: 06/12/2019
ms.openlocfilehash: b66306de6b2afa1e39a91ba3b3981aec4b440e1a
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2019
ms.locfileid: "71123589"
---
# <a name="quickstart-create-apache-kafka-cluster-in-azure-hdinsight-using-azure-portal"></a>Gyors útmutató: Apache Kafka-fürt létrehozása az Azure HDInsight Azure Portal használatával

Az Apache Kafka egy nyílt forráskódú, elosztott adatstreamelési platform. Sokszor használják üzenetközvetítőként, mivel a közzétételi-feliratkozási üzenetsorokhoz hasonló funkciókat kínál. 

Ebben a rövid útmutatóban megismerheti, hogyan hozhat létre [Apache Kafka](https://kafka.apache.org)-fürtöt az Azure Portal használatával. Azt is megtudhatja, hogyan küldhet és fogadhat üzeneteket a mellékelt segédprogramokkal az Apache Kafka segítségével.

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

Az Apache Kafka API csak az ugyanazon virtuális hálózaton belüli erőforrások számára érhető el. Ebben a rövid útmutatóban közvetlenül éri el a fürtöt SSH-val. Ha más szolgáltatásokat, hálózatokat vagy virtuális gépeket szeretne csatlakoztatni az Apache Kafkához, először létre kell hoznia egy virtuális hálózatot, majd létre kell hoznia a hálózaton belüli erőforrásokat. További információt a [Csatlakozás az Apache Kafkához virtuális hálózattal](apache-kafka-connect-vpn-gateway.md) című dokumentumban találhat.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Egy SSH-ügyfél. További információ: [Kapcsolódás HDInsight (Apache Hadoop) SSH használatával](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-an-apache-kafka-cluster"></a>Apache Kafka-fürt létrehozása

Egy Apache Kafka on HDInsight-fürt létrehozásához kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. A bal oldali menüben navigáljon a **+ erőforrás** > létrehozása**elemzési** > **HDInsight**elemre.

    ![Erőforrás-HDInsight Azure Portal létrehozása](./media/apache-kafka-get-started/create-hdinsight-cluster.png)

3. Az **Alapvető beállítások** képernyőn adja meg vagy válassza ki a következő információkat:

    | Beállítás | Érték |
    | --- | --- |
    | Fürt neve | A HDInsight-fürt egyedi neve. |
    | Előfizetés | Válassza ki előfizetését. |

   Válassza ki a __Fürt típusát__ a **Fürtkonfiguráció** megjelenítéséhez.

   ![A HDInsight-beli Apache Kafka-fürt alapszintű konfigurálása](./media/apache-kafka-get-started/custom-basics-kafka1.png)

4. A __fürtkonfiguráció__területen válassza ki a következő értékeket:

    | Beállítás | Érték |
    | --- | --- |
    | Fürt típusa | Kafka |
    | Version | Kafka 1.1.0 (HDI 3.6) |

    Válassza a **kijelölés** lehetőséget a fürt típusának beállításainak mentéséhez és az __alapismeretekhez__való visszatéréshez.

    ![HDInsight Apache Kafka-fürt típusa](./media/apache-kafka-get-started/apache-kafka-cluster-type.png)

5. Az __Alapvető beállítások__ képernyőn adja meg vagy válassza ki a következő információkat:

    | Beállítás | Érték |
    | --- | --- |
    | Fürt bejelentkezési felhasználóneve | A fürtön futtatott webszolgáltatások vagy REST API-k eléréséhez használt bejelentkezési név. Tartsa meg az alapértelmezett értéket (admin). |
    | Fürt bejelentkezési jelszava | A fürtön futtatott webszolgáltatások vagy REST API-k eléréséhez használt jelszó. |
    | Secure Shell- (SSH-) felhasználónév | A fürt SSH-kapcsolaton keresztüli elérésekor használt bejelentkezési adatok. Alapértelmezés szerint a jelszó megegyezik a fürt bejelentkezési jelszavával. |
    | Erőforráscsoport | Az erőforráscsoport, amelyben a fürt létre lesz hozva. |
    | Hely | Az Azure-régió, amelyben a fürt létre lesz hozva. |

    Minden egyes Azure-régió (hely) _tartalék tartományokat_ biztosít. A tartalék tartomány az alapul szolgáló hardver logikai csoportosítása egy Azure-adatközpontban. Mindegyik tartalék tartomány közös áramforrással és hálózati kapcsolóval rendelkezik. A HDInsight-fürtön belül a csomópontokat implementáló virtuális gépek és felügyelt lemezek ezek között a tartalék tartományok között vannak elosztva. Ez az architektúra csökkenti a fizikai hardverhibák lehetséges hatását.

    Az adatok magas rendelkezésre állásának biztosításához válasszon egy olyan régiót (helyet), amely __három tartalék tartományt__ tartalmaz. Az adott régióban található tartalék tartományok számáról további információkat a [Linux rendszerű virtuális gépek rendelkezésre állása](../../virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set) dokumentumban talál.

   ![A fürt alapalapjainak Azure Portal létrehozása](./media/apache-kafka-get-started/hdinsight-basic-configuration-2.png)

    Kattintson a __tovább__ gombra az alapszintű konfiguráció befejezéséhez.

6. Ebben a rövid útmutatóban hagyja meg az alapértelmezett biztonsági beállításokat. Az Enterprise Security Package csomaggal kapcsolatos további információért lásd: [HDInsight-fürt konfigurálása az Enterprise Security Package használatára az Azure Active Directory Domain Services használatával](../domain-joined/apache-domain-joined-configure-using-azure-adds.md). A saját kulcs Apache Kafka-lemeztitkosításhoz való használatával kapcsolatos információért lásd: [Saját kulcs használata az Apache Kafka on Azure HDInsightban](apache-kafka-byok.md)

   Ha szeretné egy virtuális hálózathoz csatlakoztatni a fürtöt, válasszon egy virtuális hálózatot a **Virtuális hálózat** legördülő listából.

   ![Fürt hozzáadása egy virtuális hálózathoz](./media/apache-kafka-get-started/kafka-security-config.png)

7. A **Tárolás** panelen válasszon ki vagy hozzon létre egy Storage-fiókot. A jelen dokumentumban leírt lépésekben a többi mező alapértelmezett értékét ne módosítsa. Kattintson a __Tovább__ gombra a tárolókonfiguráció mentéséhez. További információ a Data Lake Storage Gen2 használatáról: gyors [útmutató: Fürtök beállítása a HDInsight](../../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)-ben.

   ![A tárfiók HDInsight-beállításainak konfigurálása](./media/apache-kafka-get-started/storage-configuration.png)

8. Az __Alkalmazások (opcionális)__ területen kattintson a __Tovább__ gombra az alapértelmezett beállítások használatával való folytatáshoz.

9. A __Fürtméret__ lapon kattintson a __Tovább__ gombra az alapértelmezett beállítások használatával való folytatáshoz.

    Az Apache Kafka on HDInsight platform rendelkezésre állásának biztosításához a __feldolgozó csomópontok száma__ bejegyzés értékének legalább 3-nak kell lennie. Az alapértelmezett érték a 4.

    A **lemezek száma feldolgozó csomópontonként** bejegyzés az Apache Kafka on HDInsight skálázhatóságát konfigurálja. Az Apache Kafka on HDInsight a fürt virtuális gépeinek helyi lemezén tárolja az adatokat. Mivel az Apache Kafka nagy ki- és bemenő adatforgalmat kezel, az [Azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md) szolgáltatás gondoskodik a magas átviteli sebességről és csomópontonként több tárhelyről. A felügyelt lemez típusa __Standard__ (HDD) vagy __Prémium__ (SSD) lehet. A lemez típusa a feldolgozó csomópontok (Apache Kafka-közvetítők) által használt virtuálisgép-mérettől függ. A DS és GS sorozatbeli virtuális gépek automatikusan prémium lemezeket használnak. Minden más virtuálisgép-típus standard lemezeket használ.

   ![Az Apache Kafka-fürt méretének beállítása](./media/apache-kafka-get-started/apace-kafka-cluster-size.png)

10. A __Speciális beállítások__ lapon kattintson a __Tovább__ gombra az alapértelmezett beállítások használatával való folytatáshoz.

11. Az **Összegzés** lapon tekintse át a fürt konfigurációját. A __Szerkesztés__ hivatkozásai használatával módosítsa a hibás beállításokat. Végül válassza a **Létrehozás** elemet a fürt létrehozásához.

    ![a Kafka-fürt konfigurációjának összegzése](./media/apache-kafka-get-started/kafka-configuration-summary.png)

    A fürt létrehozása 20 percig is eltarthat.

## <a name="connect-to-the-cluster"></a>Csatlakozás a fürthöz

1. A Apache Kafka-fürt elsődleges fő csomópontjára való kapcsolódáshoz használja a következő parancsot. Cserélje le az `sshuser` elemet az SSH-felhasználónévre. Cserélje `mykafka` le az helyére az Apache-Kafkacluster nevét.

    ```bash
    ssh sshuser@mykafka-ssh.azurehdinsight.net
    ```

2. Amikor első alkalommal csatlakozik a fürthöz, az SSH-ügyfél olyan figyelmeztetést jeleníthet meg, amely szerint a gazdaszámítógép nem hitelesíthető. Amikor a rendszer kéri, írja be a __yes__ (igen) szót, majd nyomja majd nyomja le az __Enter__ billentyűt, hogy a gazdaszámítógépet felvegye az SSH-ügyfél megbízható kiszolgálókat tartalmazó listájába.

3. Ha a rendszer kéri, adja meg az SSH-felhasználó jelszavát.

    Miután csatlakozott, az alábbi szöveghez hasonló információkat lát:
    
    ```output
    Authorized uses only. All activity may be monitored and reported.
    Welcome to Ubuntu 16.04.4 LTS (GNU/Linux 4.13.0-1011-azure x86_64)
    
     * Documentation:  https://help.ubuntu.com
     * Management:     https://landscape.canonical.com
     * Support:        https://ubuntu.com/advantage
    
      Get cloud support with Ubuntu Advantage Cloud Guest:
        https://www.ubuntu.com/business/services/cloud
    
    83 packages can be updated.
    37 updates are security updates.


    Welcome to Apache Kafka on HDInsight.
    
    Last login: Thu Mar 29 13:25:27 2018 from 108.252.109.241
    ```

## <a id="getkafkainfo"></a>Az Apache Zookeeper és a Broker gazdagép adatainak beszerzése

A Kafka használatakor ismernie kell az *Apache Zookeeper* és a *Broker* gazdagépeit. Az Apache Kafka API és a Kafkában elérhető számos segédprogram használja ezeket a gazdagépeket.

Ebben a szakaszban a gazdagépre vonatkozó információkat a fürt Apache Ambari-REST API szerezheti be.

1. Telepítse a [jQ](https://stedolan.github.io/jq/)parancssori JSON-processzort. Ez a segédprogram a JSON-dokumentumok elemzésére szolgál, és hasznos a gazdagép adatainak elemzéséhez. Az Open SSH-kapcsolatban adja meg a következő parancsot a `jq`telepítéséhez:
   
    ```bash
    sudo apt -y install jq
    ```

2. Környezeti változók beállítása. Cserélje `PASSWORD` le `CLUSTERNAME` a és a nevet a fürt bejelentkezési jelszavára és a fürt nevére, majd írja be a parancsot:

    ```bash
    export password='PASSWORD'
    export clusterNameA='CLUSTERNAME'
    ```

3. Helyesen kibontott fürt neve. A fürt nevének tényleges burkolata különbözhet attól függően, hogy a fürt hogyan lett létrehozva. Ez a parancs beolvassa a tényleges burkolatot, tárolja egy változóban, majd megjeleníti a megfelelő tokozási nevet és a korábban megadott nevet. Írja be a következő parancsot:

    ```bash
    export clusterName=$(curl -u admin:$password -sS -G "https://$clusterNameA.azurehdinsight.net/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')
    echo $clusterName, $clusterNameA
    ```

4. A Zookeeper gazdagép-információkkal rendelkező környezeti változók beállításához használja az alábbi parancsot. A parancs lekéri az összes Zookeeper-gazdagépet, majd csak az első két bejegyzést adja vissza. Ez azért van, mert hasznos lehet a redundancia, ha az egyik gazdagép esetleg nem érhető el.

    ```bash
    export KAFKAZKHOSTS=`curl -sS -u admin:$password -G http://headnodehost:8080/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

    A parancs közvetlenül a fürt átjárócsomópontján kérdezi le az Ambari szolgáltatást. A Ambari a nyilvános címe `https://$CLUSTERNAME.azurehdinsight.net:80/`alapján is elérheti. Egyes hálózati beállítások megakadályozhatják a nyilvános cím elérését. Ilyen például a hálózati biztonsági csoportok (NSG-k) használata a HDInsighthoz való hozzáférés korlátozására a virtuális hálózatokon.

5. A környezeti változók helyes beállításának ellenőrzését az alábbi paranccsal végezheti el:

    ```bash
    echo $KAFKAZKHOSTS
    ```

    Ez a parancs az alábbi szöveghez hasonló információt ad vissza:

    `zk0-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk2-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181`

6. A környezeti változók Apache Kafka-közvetítőgazdagépek adataival való beállításához használja az alábbi parancsot:

    ```bash
    export KAFKABROKERS=`curl -sS -u admin:$password -G http://headnodehost:8080/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    ```

7. A környezeti változók helyes beállításának ellenőrzését az alábbi paranccsal végezheti el:

    ```bash   
    echo $KAFKABROKERS
    ```

    Ez a parancs az alábbi szöveghez hasonló információt ad vissza:
   
    `wn1-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092,wn0-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092`

## <a name="manage-apache-kafka-topics"></a>Apache Kafka-témakörök kezelése

A Kafka *témakörökben* tárolja az adatstreameket. A `kafka-topics.sh` segédprogrammal kezelheti a témaköröket.

* **Egy témakör létrehozásához** használja az alábbi parancsot az SSH-kapcsolatban:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS
    ```

    Ez a parancs a `$KAFKAZKHOSTS`-ban tárolt gazdagépadatok használatával kapcsolódik a Zookeeperhez, majd létrehoz egy **test** nevű Apache Kafka-témakört. 

    * A témakörben tárolt adatok nyolc partícióban vannak elosztva.

    * Mindegyik partíció három feldolgozó csomópontra van replikálva a fürtben.

        Ha olyan Azure-régióban hozta létre a fürtöt, amely három tartalék tartományt biztosít, használjon 3-as replikációs tényezőt. Ellenkező esetben használjon 4-es replikációs tényezőt.
        
        A három tartalék tartományt tartalmazó régiókban a 3-as replikációs tényező lehetővé teszi, hogy a replikákat el lehessen osztani a tartalék tartományok között. A két tartalék tartományt tartalmazó régiókban a négyes replikációs tényező egyenlően osztja el a replikákat a tartományok között.
        
        Az adott régióban található tartalék tartományok számáról további információkat a [Linux rendszerű virtuális gépek rendelkezésre állása](../../virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set) dokumentumban talál.

        Az Apache Kafka nem észleli a tartalék Azure-tartományokat. Témakörök számára történő partícióreplikák létrehozásakor lehetséges, hogy a Kafka nem a magas rendelkezésre állásnak megfelelően osztja ki a replikákat.

        A magas rendelkezésre állás biztosítása érdekében használja a [Apache Kafka Partition rebalance eszközt](https://github.com/hdinsight/hdinsight-kafka-tools). Ezt az eszközt egy SSH-kapcsolatból kell futtatni az Apache Kafka-fürt főcsomópontjához.

        Az Apache Kafka-adatok lehető legmagasabb rendelkezésre állása érdekében egyensúlyozza újra a témaköre partícióreplikáit a következő esetekben:

        * Új témakör vagy partíció létrehozásakor

        * Fürt vertikális felskálázásakor

* **A témakörök listázásához** használja az alábbi parancsot:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
    ```

    Ez a parancs listázza az Apache Kafka-fürtön elérhető témaköröket.

* **Egy témakör törléséhez** használja az alábbi parancsot:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --delete --topic topicname --zookeeper $KAFKAZKHOSTS
    ```

    Ez a parancs törli a `topicname` nevű témakört.

    > [!WARNING]  
    > Ha törli a korábban létrehozott `test` témakört, akkor újra létre kell hoznia. A dokumentum későbbi lépései használni fogják.

A `kafka-topics.sh` segédprogrammal elérhető parancsokkal kapcsolatos további információért használja a következő parancsot:

```bash
/usr/hdp/current/kafka-broker/bin/kafka-topics.sh
```

## <a name="produce-and-consume-records"></a>Rekordok létrehozása és felhasználása

A Kafka témakörökben tárolja a *rekordokat*. A rekordokat *előállítók* hozzák létre, és *fogyasztók* használják fel. A létrehozók és a feldolgozók a *Kafka-közvetítő* szolgáltatással kommunikálnak. A HDInsight-fürt mindegyik feldolgozó csomópontja egy Apache Kafka-közvetítőgazdagép.

Kövesse az alábbi lépéseket a rekordoknak a korábban létrehozott test témakörben való tárolására, majd a beolvasásukra egy fogyasztó használatával:

1. Ha rekordokat szeretne írni a témakörbe, használja az SSH-kapcsolat `kafka-console-producer.sh` segédprogramját:
   
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic test
    ```
   
    A parancs kiadása után egy üres sor jelenik meg.

2. Írjon be egy szöveges üzenetet az üres sorba, majd nyomja le az Enter billentyűt. Írjon be így még néhány szöveges üzenetet, majd a **Ctrl + C** billentyűparancs használatával térjen vissza a szokásos parancssorhoz. A rendszer minden sort külön rekordként küld el az Apache Kafka-témakörbe.

3. Ha rekordokat szeretne olvasni a témakörből, használja az SSH-kapcsolat `kafka-console-consumer.sh` segédprogramját:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic test --from-beginning
    ```

    A parancs lekéri a rekordokat a témakörből, majd megjeleníti őket. A `--from-beginning` használata arra utasítja a fogyasztót, hogy a stream elejétől kezdje a műveletet, így az összes rekord lekérése megtörténik.

    Ha a Kafka régebbi verzióját használja, cserélje le a `--bootstrap-server $KAFKABROKERS` előtagot a következőre: `--zookeeper $KAFKAZKHOSTS`.

4. Használja a __Ctrl + C__ billentyűparancsot a fogyasztó leállításához.

Szoftveresen is létrehozhat előállítókat és fogyasztókat. Az API használatára példaként tekintse meg a [Apache Kafka producer és fogyasztói API HDInsight-](apache-kafka-producer-consumer-api.md) dokumentummal című témakört.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A jelen rövid útmutató által létrehozott erőforrások törléséhez törölheti az erőforráscsoportot. Az erőforráscsoport törlésekor a kapcsolódó HDInsight-fürt, valamint az esetlegesen az erőforráscsoporthoz társított egyéb erőforrások is törlődnek.

Az erőforráscsoport eltávolítása az Azure Portallal:

1. Az Azure Portalon bontsa ki a bal oldalon a szolgáltatásmenüt, és válassza az __Erőforráscsoportok__ lehetőséget az erőforráscsoportok listájának megjelenítéséhez.
2. Keresse meg a törölni kívánt erőforráscsoportot, és kattintson a jobb gombbal a lista jobb oldalán lévő __Továbbiak__ gombra (...).
3. Válassza az __Erőforráscsoport törlése__ elemet, és erősítse meg a választását.

> [!WARNING]  
> A HDInsight-fürt számlázása a fürt létrehozásakor kezdődik és a fürt törlésekor fejeződik be. A számlázás percalapú, ezért mindig érdemes törölni a fürtöt, ha az már nincs használatban.
> 
> Az Apache Kafka on HDInsight-fürt törlése a Kafkában tárolt összes adatot is törli.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Apache Spark használata a Apache Kafka](../hdinsight-apache-kafka-spark-structured-streaming.md)
