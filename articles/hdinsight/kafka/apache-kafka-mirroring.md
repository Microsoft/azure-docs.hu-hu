---
title: Mirror Apache Kafka témakörök – Azure HDInsight
description: Megtudhatja, hogyan használhatja a Apache Kafka tükrözési funkcióját a HDInsight-fürtön található Kafka replikájának fenntartásához a témakörök tükrözésével egy másodlagos fürtre.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/29/2019
ms.openlocfilehash: c2fce6d4ee95a56cc087d50184fcd69ac113620f
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98940838"
---
# <a name="use-mirrormaker-to-replicate-apache-kafka-topics-with-kafka-on-hdinsight"></a>A MirrorMaker használata Apache Kafka-témakörök replikálására a Kafka on HDInsight esetében

Ismerje meg, hogyan replikálhatja a témaköröket egy másodlagos fürtre a Apache Kafka tükrözési funkciójával. A tükrözést folyamatos folyamatként lehet futtatni, vagy időszakosan használhatja az adatok egyik fürtről a másikra való áttelepítésének módját.

> [!NOTE]
> Ez a cikk az *engedélyezési* feltételekre mutató hivatkozásokat tartalmaz, amelyek egy kifejezés, amelyet a Microsoft már nem használ. Ha a rendszer eltávolítja a kifejezést a szoftverből, azt a cikkből távolítjuk el.

Ebben a példában a tükrözés két HDInsight-fürt közötti replikálásra szolgál. Mindkét fürt különböző virtuális hálózatokban található, különböző adatközpontokban.

> [!WARNING]  
> A tükrözés nem tekinthető úgy, hogy a hibatűrést megvalósító eszköz legyen. A témakörben lévő elemek eltolása eltérő az elsődleges és a másodlagos fürtök között, így az ügyfelek nem használhatják a kettőt.
>
> Ha aggódik a hibatűréssel kapcsolatban, állítsa be a fürtön belüli témakörök replikálását. További információ: Ismerkedés [a Apache Kafka a HDInsight](apache-kafka-get-started.md).

## <a name="how-apache-kafka-mirroring-works"></a>A Apache Kafka tükrözés működése

A tükrözés úgy működik, hogy a [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) eszközt használja (Apache Kafka), hogy felhasználja az elsődleges fürtön található témakörökből származó rekordokat, majd helyi másolatot hozzon létre a másodlagos fürtön. A MirrorMaker egy (vagy több), az elsődleges fürtből beolvasott *fogyasztót* , valamint egy olyan *gyártót* használ, amely a helyi (másodlagos) fürtöt írja.

A leghasznosabb tükrözési beállítás a vész-helyreállításhoz a Kafka-fürtöket használja különböző Azure-régiókban. Ennek eléréséhez azok a virtuális hálózatok, amelyeken a fürtök találhatók, össze vannak egyesítve.

A következő ábra a tükrözési folyamatot mutatja be, valamint azt, hogy a kommunikáció hogyan folyik a fürtök között:

![A tükrözési folyamat ábrája](./media/apache-kafka-mirroring/kafka-mirroring-vnets2.png)

Az elsődleges és a másodlagos fürtök eltérőek lehetnek a csomópontok és a partíciók számában, és a témakörökben lévő eltolások eltérőek is. A tükrözéssel megőrzi a particionáláshoz használt kulcs értékét, így a rögzítési sorrendet a kulcs alapján kell megőrizni.

### <a name="mirroring-across-network-boundaries"></a>A hálózati határokon átívelő tükrözés

Ha a Kafka-fürtök különböző hálózatokban való tükrözése szükséges, a következő szempontokat kell figyelembe vennie:

* **Átjárók**: a hálózatoknak képesnek kell lenniük a TCP/IP szintjén való kommunikációra.

* **Kiszolgáló kezelése**: dönthet úgy, hogy a fürt CSOMÓPONTJAIT IP-címük vagy teljes tartományneveik alapján kezeli.

    * **IP-címek**: Ha a Kafka-fürtöket IP-címek reklámozására konfigurálja, a tükrözési beállítás a közvetítő csomópontok és a Zookeeper-csomópontok IP-címeivel is folytatható.
    
    * **Tartománynevek**: Ha nem konfigurálja a Kafka-fürtöket az IP-címek reklámozásához, a fürtöknek teljes tartománynevek (FQDN) használatával kell tudniuk csatlakozni egymáshoz. Ehhez minden olyan hálózaton meg kell adni egy DNS-kiszolgálót, amely a kérelmek más hálózatokra való továbbítására van konfigurálva. Azure-Virtual Network létrehozásakor a hálózattal megadott automatikus DNS használata helyett meg kell adnia egy egyéni DNS-kiszolgálót és a kiszolgáló IP-címét. A Virtual Network létrehozása után létre kell hoznia egy, az adott IP-címet használó Azure-beli virtuális gépet, majd telepítenie és konfigurálnia kell a DNS-szoftvert.

    > [!WARNING]  
    > Hozza létre és konfigurálja az egyéni DNS-kiszolgálót, mielőtt telepítené a HDInsight a Virtual Networkba. Nincs szükség további konfigurációra ahhoz, hogy a HDInsight használni lehessen a Virtual Network konfigurált DNS-kiszolgálót.

A két Azure-beli virtuális hálózat összekapcsolásával kapcsolatos további információkért lásd: [VNet-VNet kapcsolat konfigurálása](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).

## <a name="mirroring-architecture"></a>Tükrözési architektúra

Ez az architektúra két fürtöt tartalmaz különböző erőforráscsoportok és virtuális hálózatok esetében: **elsődleges** és **másodlagos**.

### <a name="creation-steps"></a>Létrehozás lépései

1. Hozzon létre két új erőforráscsoportot:

    |Erőforráscsoport | Hely |
    |---|---|
    | Kafka – elsődleges – RG | Az USA középső régiója |
    | Kafka – másodlagos – RG | USA északi középső régiója |

1. Hozzon létre egy új virtuális hálózatot, a Kafka **-Primary-vnet** -t a **Kafka-Primary-RG**-ban. Hagyja meg az alapértelmezett beállításokat.
1. Hozzon létre egy új Virtual Network **Kafka-másodlagos-vnet** a **Kafka-másodlagos-RG**-ben, az alapértelmezett beállításokkal is.

1. Hozzon létre két új Kafka-fürtöt:

    | Fürt neve | Erőforráscsoport | Virtual Network | Tárfiók |
    |---|---|---|---|
    | Kafka – elsődleges – fürt | Kafka – elsődleges – RG | Kafka – elsődleges – vnet | kafkaprimarystorage |
    | Kafka – másodlagos – fürt | Kafka – másodlagos – RG | Kafka – másodlagos – vnet | kafkasecondarystorage |

1. Hozzon létre virtuális hálózati társításokat. Ez a lépés két társítást hoz létre: egyet a **Kafka-Primary-vnet** és a **Kafka-másodlagos-vnet** , valamint a Kafka- **másodlagos-vnet** és a **Kafka-Primary-vnet** között.
    1. Válassza ki a **Kafka-Primary-vnet** virtuális hálózatot.
    1. Válassza **a** **Beállítások** területen a társítások lehetőséget.
    1. Válassza a **Hozzáadás** elemet.
    1. A társ-kezelés **hozzáadása** képernyőn adja meg a részleteket az alábbi képernyőképen látható módon.

        ![HDInsight Kafka vnet-társítás hozzáadása](./media/apache-kafka-mirroring/hdi-add-vnet-peering.png)

### <a name="configure-ip-advertising"></a>IP-hirdetés konfigurálása

Konfigurálja az IP-hirdetést úgy, hogy az ügyfél a tartománynevek helyett a Broker IP-címeivel kapcsolódjon.

1. Nyissa meg az elsődleges fürt Ambari-irányítópultját: `https://PRIMARYCLUSTERNAME.azurehdinsight.net` .
1. Válassza a **szolgáltatások**  >  **Kafka** lehetőséget. CliSelectck a **konfigurációk** lapon.
1. Adja hozzá a következő konfigurációs sorokat az alsó **Kafka-env sablon** szakaszhoz. Kattintson a **Mentés** gombra.

    ```
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

1. Írjon be egy megjegyzést a **konfiguráció mentése** képernyőn, majd kattintson a **Mentés** gombra.
1. Ha a rendszer konfigurációs figyelmeztetést kér, kattintson a **Folytatás** gombra.
1. Kattintson az **OK gombra** a **konfiguráció módosításainak mentése** elemre.
1.   >  Az újraindítás **szükséges** értesítésnél válassza az **összes érintett újraindítás újraindítása** elemet. Válassza **az összes újraindításának megerősítése** lehetőséget.

    ![Az Apache Ambari újraindítása minden érintett](./media/apache-kafka-mirroring/ambari-restart-notification.png)

### <a name="configure-kafka-to-listen-on-all-network-interfaces"></a>A Kafka beállítása az összes hálózati adapter figyelésére.
    
1. Maradjon a **konfigurációk** lapon a Kafka- **szolgáltatások** területen  >  . A **Kafka-átvitelszervező** szakaszban állítsa be a **figyelők** tulajdonságot a következőre: `PLAINTEXT://0.0.0.0:9092` .
1. Kattintson a **Mentés** gombra.
1. Válassza az **Újraindítás** lehetőséget, és **erősítse meg az összes újraindítását**.

### <a name="record-broker-ip-addresses-and-zookeeper-addresses-for-primary-cluster"></a>A Broker IP-címeinek és Zookeeper címeinek rögzítése az elsődleges fürthöz.

1. Válassza a **gazdagépek** lehetőséget a Ambari irányítópultján.
1. Jegyezze fel a közvetítők és a Zookeeperek IP-címeit. A közvetítő csomópontok az állomásnév első két betűje szerint **lefelé** , a Zookeeper-csomópontok pedig az **ZK** első két betűjét jelölik.

    ![Apache Ambari-nézet csomópontjának IP-címei](./media/apache-kafka-mirroring/view-node-ip-addresses2.png)

1. Ismételje meg az előző három lépést a második, **Kafka-másodlagos fürthöz**: konfigurálja az IP-hirdetéseket, állítson be figyelőket, és jegyezze fel a közvetítő és a Zookeeper IP-címét.

## <a name="create-topics"></a>Témakörök létrehozása

1. Kapcsolódjon az **elsődleges** fürthöz az SSH használatával:

    ```bash
    ssh sshuser@PRIMARYCLUSTER-ssh.azurehdinsight.net
    ```

    Cserélje le a **sshuser** -t a fürt létrehozásakor használt SSH-felhasználónévre. Cserélje le a **PRIMARYCLUSTER** a fürt létrehozásakor használt alapnévre.

    További információk: [Az SSH használata HDInsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. Az alábbi paranccsal hozzon létre egy változót az Apache Zookeeper-gazdagépek használatával az elsődleges fürthöz. A hasonló sztringeket `ZOOKEEPER_IP_ADDRESS1` a korábban rögzített aktuális IP-címekkel, például a és a kifejezéssel kell helyettesíteni `10.23.0.11` `10.23.0.7` . Ha FQDN-feloldást használ egyéni DNS-kiszolgálóval, kövesse az [alábbi lépéseket](apache-kafka-get-started.md#getkafkainfo) a közvetítő és a Zookeeper nevének beszerzéséhez:

    ```bash
    # get the zookeeper hosts for the primary cluster
    export PRIMARY_ZKHOSTS='ZOOKEEPER_IP_ADDRESS1:2181, ZOOKEEPER_IP_ADDRESS2:2181, ZOOKEEPER_IP_ADDRESS3:2181'
    ```

1. Egy nevű témakör létrehozásához `testtopic` használja a következő parancsot:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $PRIMARY_ZKHOSTS
    ```

1. A következő parancs használatával ellenőrizheti, hogy létrejött-e a témakör:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $PRIMARY_ZKHOSTS
    ```

    A válasz tartalmaz `testtopic` .

1. A következő paranccsal tekintheti meg az adott ( **elsődleges**) fürt Zookeeper-gazdagépének adatait:

    ```bash
    echo $PRIMARY_ZKHOSTS
    ```

    Ez az alábbi szöveghez hasonló adatokat ad vissza:

    `10.23.0.11:2181,10.23.0.7:2181,10.23.0.9:2181`

    Mentse ezt az információt. Ez a következő szakaszban használatos.

## <a name="configure-mirroring"></a>Tükrözés konfigurálása

1. Kapcsolódás a **másodlagos** fürthöz egy másik SSH-munkamenet használatával:

    ```bash
    ssh sshuser@SECONDARYCLUSTER-ssh.azurehdinsight.net
    ```

    Cserélje le a **sshuser** -t a fürt létrehozásakor használt SSH-felhasználónévre. Cserélje le a **SECONDARYCLUSTER** nevet a fürt létrehozásakor használt névre.

    További információk: [Az SSH használata HDInsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. A `consumer.properties` rendszer egy fájlt használ az **elsődleges** fürttel való kommunikáció konfigurálására. A fájl létrehozásához használja a következő parancsot:

    ```bash
    nano consumer.properties
    ```

    Használja a következő szöveget a fájl tartalmának `consumer.properties` :

    ```yaml
    zookeeper.connect=PRIMARY_ZKHOSTS
    group.id=mirrorgroup
    ```

    Cserélje le a **PRIMARY_ZKHOSTSt** az **elsődleges** fürt Zookeeper IP-címeire.

    Ez a fájl az elsődleges Kafka-fürtről való olvasáskor használandó fogyasztói adatokat ismerteti. További információ a fogyasztói konfigurációról: [fogyasztói konfigurációk](https://kafka.apache.org/documentation#consumerconfigs) a Kafka.Apache.org címen.

    A fájl mentéséhez használja a **CTRL + X billentyűkombinációt** **, majd** **írja be** a következőt:.

1. Mielőtt konfigurálja a másodlagos fürttel kommunikáló gyártót, állítson be egy változót a **másodlagos** fürt Broker IP-címeihez. A változó létrehozásához használja a következő parancsokat:

    ```bash
    export SECONDARY_BROKERHOSTS='BROKER_IP_ADDRESS1:9092,BROKER_IP_ADDRESS2:9092,BROKER_IP_ADDRESS2:9092'
    ```

    A parancsnak `echo $SECONDARY_BROKERHOSTS` az alábbi szöveghez hasonló adatokat kell visszaadnia:

    `10.23.0.14:9092,10.23.0.4:9092,10.23.0.12:9092`

1. `producer.properties`A rendszer egy fájlt használ a **másodlagos** fürt kommunikációjához. A fájl létrehozásához használja a következő parancsot:

    ```bash
    nano producer.properties
    ```

    Használja a következő szöveget a fájl tartalmának `producer.properties` :

    ```yaml
    bootstrap.servers=SECONDARY_BROKERHOSTS
    compression.type=none
    ```

    Cserélje le a **SECONDARY_BROKERHOSTSt** az előző lépésben használt Broker IP-címekre.

    További információ a gyártói konfigurációról: a kafka.apache.org- [beli gyártói konfigurációk](https://kafka.apache.org/documentation#producerconfigs) .

1. A következő parancsokkal hozhat létre környezeti változót a másodlagos fürthöz tartozó Zookeeper-gazdagépek IP-címeivel:

    ```bash
    # get the zookeeper hosts for the secondary cluster
    export SECONDARY_ZKHOSTS='ZOOKEEPER_IP_ADDRESS1:2181,ZOOKEEPER_IP_ADDRESS2:2181,ZOOKEEPER_IP_ADDRESS3:2181'
    ```

1. A Kafka on HDInsight alapértelmezett konfigurációja nem teszi lehetővé a témakörök automatikus létrehozását. A tükrözési folyamat megkezdése előtt a következő lehetőségek egyikét kell használnia:

    * **Témakörök létrehozása a másodlagos fürtön**: Ez a beállítás azt is lehetővé teszi, hogy beállítsa a partíciók számát és a replikálási tényezőt.

        A következő paranccsal hozhat létre témákat az idő előtt:

        ```bash
        /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $SECONDARY_ZKHOSTS
        ```

        A helyére írja `testtopic` be a létrehozandó témakör nevét.

    * **A fürt konfigurálása automatikus témakör-létrehozáshoz**: Ez a beállítás lehetővé teszi, hogy a MirrorMaker automatikusan hozzon létre témákat, azonban az elsődleges témakörtől eltérő számú partícióval vagy replikációs tényezővel is létrehozhatók.

        Ha úgy szeretné beállítani a másodlagos fürtöt, hogy automatikusan hozzon létre témákat, hajtsa végre a következő lépéseket:

        1. Nyissa meg a Ambari irányítópultot a másodlagos fürthöz: `https://SECONDARYCLUSTERNAME.azurehdinsight.net` .
        1. Kattintson a **szolgáltatások**  >  **Kafka** lehetőségre. Kattintson a **konfigurációk** fülre.
        1. A __szűrő__ mezőbe írja be a értékét `auto.create` . Ezzel kiszűri a tulajdonságok listáját, és megjeleníti a `auto.create.topics.enable` beállítást.
        1. Módosítsa az értéket True értékre `auto.create.topics.enable` , majd válassza a __Mentés__ lehetőséget. Vegyen fel egy megjegyzést, majd válassza a __Mentés__ újra lehetőséget.
        1. Válassza ki a __Kafka__ szolgáltatást, válassza az __Újraindítás__ lehetőséget, majd kattintson az __összes érintett újraindítása__ elemre. Ha a rendszer kéri, válassza __az összes újraindításának megerősítése__ lehetőséget.

        ![a Kafka automatikus létrehozási témaköreinek engedélyezése](./media/apache-kafka-mirroring/kafka-enable-auto-create-topics.png)

## <a name="start-mirrormaker"></a>MirrorMaker elindítása

1. A **másodlagos** fürthöz tartozó SSH-kapcsolatban használja a következő parancsot a MirrorMaker folyamat elindításához:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-run-class.sh kafka.tools.MirrorMaker --consumer.config consumer.properties --producer.config producer.properties --whitelist testtopic --num.streams 4
    ```

    Az ebben a példában használt paraméterek a következők:

    |Paraméter |Leírás |
    |---|---|
    |--consumer.config|A fogyasztói tulajdonságokat tartalmazó fájlt adja meg. Ezek a tulajdonságok az *elsődleges* Kafka-fürtből beolvasott fogyasztó létrehozására szolgálnak.|
    |--producer.config|A gyártói tulajdonságokat tartalmazó fájlt adja meg. Ezek a tulajdonságok egy olyan gyártó létrehozásához használatosak, amely a *másodlagos* Kafka-fürtbe ír.|
    |--engedélyezési lista|Azon témakörök listája, amelyeket a MirrorMaker az elsődleges fürtről a másodlagosra replikál.|
    |--NUM. streamek|A létrehozandó felhasználói szálak száma.|

    A másodlagos csomóponton lévő fogyasztó most már üzenetek fogadására vár.

2. Az **elsődleges** fürthöz tartozó SSH-kapcsolatban használja az alábbi parancsot egy gyártó elindításához és az üzenetek küldéséhez a témakörbe:

    ```bash
    export PRIMARY_BROKERHOSTS=BROKER_IP_ADDRESS1:9092,BROKER_IP_ADDRESS2:9092,BROKER_IP_ADDRESS2:9092
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $SOURCE_BROKERHOSTS --topic testtopic
    ```

     Ha egy kurzort tartalmazó üres sorra érkezik, írjon be néhány szöveges üzenetet. Az üzenetek elküldése az **elsődleges** fürtön található témakörbe történik. Ha elkészült, használja a **CTRL + C** billentyűkombinációt a termelői folyamat befejezéséhez.

3. A **másodlagos** fürthöz tartozó SSH-kapcsolatban használja a **CTRL + C** billentyűkombinációt a MirrorMaker folyamat befejezéséhez. A folyamat befejezése több másodpercig is eltarthat. Az alábbi paranccsal ellenőrizheti, hogy az üzenetek replikálódtak-e a másodlagosra:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $SECONDARY_ZKHOSTS --topic testtopic --from-beginning
    ```

    A témakörök listája mostantól tartalmazza `testtopic` , amely akkor jön létre, amikor a MirrorMaster az elsődleges fürtről a másodlagosra tükrözi a témakört. A témakörből beolvasott üzenetek megegyeznek az elsődleges fürtön megadott adatokkal.

## <a name="delete-the-cluster"></a>A fürt törlése

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

A jelen dokumentum lépései különböző Azure-erőforráscsoportok által létrehozott fürtöket hoztak létre. Az összes létrehozott erőforrás törléséhez törölheti a két létrehozott erőforráscsoportot: **Kafka-Primary-RG** és **Kafka-secondary_rg**. Az erőforráscsoportok törlésével eltávolíthatja a dokumentum alapján létrehozott összes erőforrást, beleértve a fürtöket, a virtuális hálózatokat és a Storage-fiókokat.

## <a name="next-steps"></a>Következő lépések

Ebből a dokumentumból megtudhatta, hogyan használhatja a [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) -t egy [Apache Kafka](https://kafka.apache.org/) -fürt replikájának létrehozásához. Az alábbi hivatkozásokat követve megismerheti a Kafka használatának egyéb módjait:

* [Apache Kafka MirrorMaker dokumentációját](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) a cwiki.Apache.org címen.
* [A Kafka Mirror Maker ajánlott eljárásai](https://community.cloudera.com/t5/Community-Articles/Kafka-Mirror-Maker-Best-Practices/ta-p/249269)
* [Ismerkedés a HDInsight Apache Kafkaával](apache-kafka-get-started.md)
* [Apache Spark használata a HDInsight Apache Kafka használatával](../hdinsight-apache-spark-with-kafka.md)
* [Kapcsolódás Apache Kafka Azure-beli Virtual Network](apache-kafka-connect-vpn-gateway.md)
