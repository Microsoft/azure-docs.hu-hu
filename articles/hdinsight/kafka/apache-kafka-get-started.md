---
title: 'Gyors útmutató: Apache Kafka beállítása a HDInsight Azure Portal használatával'
description: Ebben a rövid útmutatóban megismerheti, hogyan hozhat létre Apache Kafka-fürtöt az Azure HDInsightban az Azure Portal használatával. A Kafka-témakörökről, -előfizetőkről és -fogyasztókról is olvashat.
ms.service: hdinsight
ms.topic: quickstart
ms.custom: mvc
ms.date: 04/29/2020
ms.openlocfilehash: 102bb1a7bf1bd3cb799d52ce13045e01a2a34297
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102505256"
---
# <a name="quickstart-create-apache-kafka-cluster-in-azure-hdinsight-using-azure-portal"></a>Rövid útmutató: Apache Kafka-fürt létrehozása az Azure HDInsight Azure Portal használatával

A [Apache Kafka](./apache-kafka-introduction.md) egy nyílt forráskódú, elosztott streaming platform. Sokszor használják üzenetközvetítőként, mivel a közzétételi-feliratkozási üzenetsorokhoz hasonló funkciókat kínál.

Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre Apache Kafka-fürtöt a Azure Portal használatával. Azt is megtudhatja, hogyan küldhet és fogadhat üzeneteket a mellékelt segédprogramokkal az Apache Kafka segítségével. Az elérhető konfigurációk részletes magyarázatát lásd: [fürtök beállítása a HDInsight-ben](../hdinsight-hadoop-provision-linux-clusters.md). A portál fürtön való létrehozásával kapcsolatos további információkért lásd: [fürtök létrehozása a portálon](../hdinsight-hadoop-create-linux-clusters-portal.md).

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

Az Apache Kafka API csak az ugyanazon virtuális hálózaton belüli erőforrások számára érhető el. Ebben a rövid útmutatóban közvetlenül az SSH használatával fér hozzá a fürthöz. Ha más szolgáltatásokat, hálózatokat vagy virtuális gépeket szeretne csatlakoztatni az Apache Kafkához, először létre kell hoznia egy virtuális hálózatot, majd létre kell hoznia a hálózaton belüli erőforrásokat. További információt a [Csatlakozás az Apache Kafkához virtuális hálózattal](apache-kafka-connect-vpn-gateway.md) című dokumentumban találhat. A virtuális hálózatok HDInsight történő megtervezésével kapcsolatos általánosabb információkért lásd: [virtuális hálózat tervezése az Azure HDInsight](../hdinsight-plan-virtual-network-deployment.md).

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Előfeltételek

Egy SSH-ügyfél. További információért lásd: [Csatlakozás a HDInsighthoz (Apache Hadoop) SSH-val](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-an-apache-kafka-cluster"></a>Apache Kafka-fürt létrehozása

Apache Kafka-fürt HDInsight való létrehozásához kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. A felső menüben válassza az **+ erőforrás létrehozása** lehetőséget.

    ![Erőforrás-HDInsight Azure Portal létrehozása](./media/apache-kafka-get-started/azure-portal-create-resource.png)

1. Válassza az **Analytics**  >  **Azure HDInsight** lehetőséget a **HDInsight-fürt létrehozása** lap megjelenítéséhez.

1. **Az alapok** lapon adja meg a következő információkat:

    |Tulajdonság  |Leírás  |
    |---------|---------|
    |Előfizetés    |  A legördülő listában válassza ki a fürthöz használt Azure-előfizetést. |
    |Erőforráscsoport     | Hozzon létre egy erőforráscsoportot, vagy válasszon ki egy már meglévőt.  Az erőforráscsoport az Azure összetevőit tartalmazó tároló.  Ebben az esetben az erőforráscsoport a HDInsight-fürtöt és a függő Azure Storage-fiókot tartalmazza. |
    |Fürt neve   | Adjon meg egy globálisan egyedi nevet. A név legfeljebb 59 karaktert tartalmazhat, beleértve a betűket, számokat és kötőjeleket. A név első és utolsó karaktere nem lehet kötőjel. |
    |Region    | A legördülő listából válassza ki azt a régiót, ahol a fürtöt létrehozták.  A jobb teljesítmény érdekében válasszon régiót közelebbről. |
    |Fürt típusa| A lista megnyitásához válassza a **fürt típusának kiválasztása** lehetőséget. A listából válassza a **Kafka** lehetőséget a fürt típusaként.|
    |Verzió|A fürt típusának alapértelmezett verziója lesz megadva. Ha más verziót szeretne megadni, válasszon a legördülő listából.|
    |A fürt bejelentkezési felhasználóneve és jelszava    | Az alapértelmezett bejelentkezési név a **rendszergazda**. A jelszónak legalább 10 karakterből kell állnia, és tartalmaznia kell legalább egy számot, egy nagybetűs és egy kisbetűs betűt, egy nem alfanumerikus karaktert (kivéve a következő karaktereket: "" " \) . Győződjön meg róla, hogy **ne adjon meg** gyakori jelszót, mint például a következő: Pass@word1.|
    |Secure Shell- (SSH-) felhasználónév | Az alapértelmezett felhasználónév az **sshuser**.  SSH-felhasználónévként más nevet is megadhat. |
    |Fürt bejelentkezési jelszavának használata SSH-hoz| Jelölje be ezt a jelölőnégyzetet, ha ugyanazt a jelszót szeretné használni az SSH-felhasználó számára, mint a fürt bejelentkezési felhasználójának.|

   ![A fürt alapalapjainak Azure Portal létrehozása](./media/apache-kafka-get-started/azure-portal-cluster-basics.png)

    Minden egyes Azure-régió (hely) _tartalék tartományokat_ biztosít. A tartalék tartomány az alapul szolgáló hardver logikai csoportosítása egy Azure-adatközpontban. Mindegyik tartalék tartomány közös áramforrással és hálózati kapcsolóval rendelkezik. A HDInsight-fürtön belül a csomópontokat implementáló virtuális gépek és felügyelt lemezek ezek között a tartalék tartományok között vannak elosztva. Ez az architektúra csökkenti a fizikai hardverhibák lehetséges hatását.

    Az adatok magas rendelkezésre állásának biztosításához válasszon egy olyan régiót (helyet), amely __három tartalék tartományt__ tartalmaz. Az adott régióban található tartalék tartományok számáról további információkat a [Linux rendszerű virtuális gépek rendelkezésre állása](../../virtual-machines/availability.md) dokumentumban talál.

    Kattintson a **következő: storage >>** fülre a tárolási beállítások megadásához.

1. A **Storage (tárolás** ) lapon adja meg a következő értékeket:

    |Tulajdonság  |Leírás  |
    |---------|---------|
    |Elsődleges tároló típusa|Használja az alapértelmezett értéket az **Azure Storage**-ban.|
    |Kiválasztási módszer|Használja az alapértelmezett értéket a **listából**.|
    |Az elsődleges tárfiók|A legördülő listából válasszon ki egy meglévő Storage-fiókot, vagy válassza az **új létrehozása** lehetőséget. Új fiók létrehozásakor a névnek 3 – 24 karakter hosszúnak kell lennie, és csak számokat és kisbetűket tartalmazhat.|
    |Tároló|Használja az automatikusan feltöltött értéket.|

    ![HDInsight Linux – első lépések – a fürt tárolási értékeinek megadása](./media/apache-kafka-get-started/azure-portal-cluster-storage.png "Tárolási értékek megadása HDInsight-fürt létrehozásához")

    Válassza a **Biztonság + hálózatkezelés** lapot.

1. Ebben a rövid útmutatóban hagyja meg az alapértelmezett biztonsági beállításokat. Az Enterprise Security Package csomaggal kapcsolatos további információért lásd: [HDInsight-fürt konfigurálása az Enterprise Security Package használatára az Azure Active Directory Domain Services használatával](../domain-joined/apache-domain-joined-configure-using-azure-adds.md). Ha szeretné megtudni, hogyan használhatja a saját kulcsát Apache Kafka lemezes titkosításhoz, látogasson el az [ügyfél által felügyelt kulcs lemezes titkosítására](../disk-encryption.md)

   Ha szeretné egy virtuális hálózathoz csatlakoztatni a fürtöt, válasszon egy virtuális hálózatot a **Virtuális hálózat** legördülő listából.

   ![Fürt hozzáadása egy virtuális hálózathoz](./media/apache-kafka-get-started/azure-portal-cluster-security-networking-kafka-vnet.png)

    Válassza a **konfiguráció + díjszabás** lapot.

1. A HDInsight Apache Kafka rendelkezésre állásának biztosításához a **munkavégző csomóponthoz** tartozó __csomópontok számát__ 3 vagy nagyobb értékre kell állítani. Az alapértelmezett érték a 4.

    A **szabványos lemezek/feldolgozó csomópontok** bejegyzései a Apache Kafka méretezhetőségét konfigurálja a HDInsight. Az Apache Kafka on HDInsight a fürt virtuális gépeinek helyi lemezén tárolja az adatokat. Mivel az Apache Kafka nagy ki- és bemenő adatforgalmat kezel, az [Azure Managed Disks](../../virtual-machines/managed-disks-overview.md) szolgáltatás gondoskodik a magas átviteli sebességről és csomópontonként több tárhelyről. A felügyelt lemez típusa __Standard__ (HDD) vagy __Prémium__ (SSD) lehet. A lemez típusa a feldolgozó csomópontok (Apache Kafka-közvetítők) által használt virtuálisgép-mérettől függ. A DS és GS sorozatbeli virtuális gépek automatikusan prémium lemezeket használnak. Minden más virtuálisgép-típus standard lemezeket használ.

   ![Az Apache Kafka-fürt méretének beállítása](./media/apache-kafka-get-started/azure-portal-cluster-configuration-pricing-kafka.png)

    Válassza a **felülvizsgálat + létrehozás** lapot.

1. Tekintse át a fürt konfigurációját. Módosítsa a helytelen beállításokat. Végül válassza a **Létrehozás** elemet a fürt létrehozásához.

    ![a Kafka-fürt konfigurációjának összegzése](./media/apache-kafka-get-started/azure-portal-cluster-review-create-kafka.png)

    A fürt létrehozása 20 percig is eltarthat.

## <a name="connect-to-the-cluster"></a>Csatlakozás a fürthöz

1. A fürthöz való kapcsolódáshoz használja az [SSH-parancsot](../hdinsight-hadoop-linux-use-ssh-unix.md) . Szerkessze az alábbi parancsot az CLUSTERNAME helyére a fürt nevével, majd írja be a következő parancsot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Ha a rendszer kéri, adja meg az SSH-felhasználó jelszavát.

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

## <a name="get-the-apache-zookeeper-and-broker-host-information"></a><a id="getkafkainfo"></a>Az Apache Zookeeper és a Broker gazdagép adatainak beszerzése

A Kafka használatakor ismernie kell az *Apache Zookeeper* és a *Broker* gazdagépeit. Az Apache Kafka API és a Kafkában elérhető számos segédprogram használja ezeket a gazdagépeket.

Ebben a szakaszban a gazdagépre vonatkozó információkat a fürt Apache Ambari-REST API szerezheti be.

1. Telepítse a [jQ](https://stedolan.github.io/jq/)parancssori JSON-processzort. Ez a segédprogram a JSON-dokumentumok elemzésére szolgál, és hasznos a gazdagép adatainak elemzéséhez. Az Open SSH-kapcsolatban adja meg a következő parancsot a telepítéséhez `jq` :

    ```bash
    sudo apt -y install jq
    ```

1. Jelszó-változó beállítása. Cserélje le a `PASSWORD` nevet a fürt bejelentkezési jelszavára, majd írja be a parancsot:

    ```bash
    export password='PASSWORD'
    ```

1. Bontsa ki a megfelelő tokozású fürt nevét. A fürt nevének tényleges burkolata különbözhet attól függően, hogy a fürt hogyan lett létrehozva. Ez a parancs beolvassa a tényleges burkolatot, majd egy változóban tárolja. Írja be a következő parancsot:

    ```bash
    export clusterName=$(curl -u admin:$password -sS -G "http://headnodehost:8080/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')
    ```

    > [!Note]  
    > Ha ezt a folyamatot a fürtön kívülről hajtja végre, a fürt nevének tárolására eltérő eljárás szükséges. A fürt nevének lekérése kisbetűvel a Azure Portalból. Ezután helyettesítse be a fürt nevét `<clustername>` a következő parancsban, és hajtsa végre: `export clusterName='<clustername>'` .


1. A Zookeeper gazdagép-információkkal rendelkező környezeti változók beállításához használja az alábbi parancsot. A parancs lekéri az összes Zookeeper-gazdagépet, majd csak az első két bejegyzést adja vissza. Ez azért van, mert hasznos lehet a redundancia, ha az egyik gazdagép esetleg nem érhető el.

    ```bash
    export KAFKAZKHOSTS=$(curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2);
    ```

    > [!Note]  
    > Ehhez a parancshoz Ambari-hozzáférés szükséges. Ha a fürt egy NSG mögött található, futtassa ezt a parancsot egy olyan gépről, amely hozzáférhet a Ambari.

1. A környezeti változók helyes beállításának ellenőrzését az alábbi paranccsal végezheti el:

    ```bash
    echo $KAFKAZKHOSTS
    ```

    Ez a parancs az alábbi szöveghez hasonló információt ad vissza:

    `zk0-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk2-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181`

1. A környezeti változók Apache Kafka-közvetítőgazdagépek adataival való beállításához használja az alábbi parancsot:

    ```bash
    export KAFKABROKERS=$(curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2);
    ```

    > [!Note]  
    > Ehhez a parancshoz Ambari-hozzáférés szükséges. Ha a fürt egy NSG mögött található, futtassa ezt a parancsot egy olyan gépről, amely hozzáférhet a Ambari.

1. A környezeti változók helyes beállításának ellenőrzését az alábbi paranccsal végezheti el:

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

        * Ha olyan Azure-régióban hozta létre a fürtöt, amely három tartalék tartományt biztosít, használjon 3-as replikációs tényezőt. Ellenkező esetben használjon 4-es replikációs tényezőt.
        
        * A három tartalék tartományt tartalmazó régiókban a 3-as replikációs tényező lehetővé teszi, hogy a replikákat el lehessen osztani a tartalék tartományok között. A két tartalék tartományt tartalmazó régiókban a négyes replikációs tényező egyenlően osztja el a replikákat a tartományok között.
        
        * Az adott régióban található tartalék tartományok számáról további információkat a [Linux rendszerű virtuális gépek rendelkezésre állása](../../virtual-machines/availability.md) dokumentumban talál.

        * Az Apache Kafka nem észleli a tartalék Azure-tartományokat. Témakörök számára történő partícióreplikák létrehozásakor lehetséges, hogy a Kafka nem a magas rendelkezésre állásnak megfelelően osztja ki a replikákat.

        * A magas rendelkezésre állás biztosítása érdekében használja a [Apache Kafka Partition rebalance eszközt](https://github.com/hdinsight/hdinsight-kafka-tools). Ezt az eszközt egy SSH-kapcsolatból kell futtatni az Apache Kafka-fürt főcsomópontjához.

        * Az Apache Kafka-adatok lehető legmagasabb rendelkezésre állása érdekében egyensúlyozza újra a témaköre partícióreplikáit a következő esetekben:

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

A Kafka a témakörökben tárolja a *rekordokat* . A rekordokat *előállítók* hozzák létre, és *fogyasztók* használják fel. A létrehozók és a feldolgozók a *Kafka-közvetítő* szolgáltatással kommunikálnak. A HDInsight-fürt mindegyik feldolgozó csomópontja egy Apache Kafka-közvetítőgazdagép.

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
> A HDInsight Apache Kafka-fürt törlése a Kafka-ben tárolt összes adathalmazt törli.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Apache Spark használata a Apache Kafka](../hdinsight-apache-kafka-spark-structured-streaming.md)