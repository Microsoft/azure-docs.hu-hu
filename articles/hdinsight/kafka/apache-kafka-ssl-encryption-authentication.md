---
title: TLS-titkosítás Apache Kafka & hitelesítés – Azure HDInsight
description: Állítsa be a TLS-titkosítást a Kafka-ügyfelek és a Kafka-brókerek közötti kommunikációhoz, valamint a Kafka-közvetítők között. Az ügyfelek SSL-hitelesítésének beállítása.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 05/01/2019
ms.openlocfilehash: d061832022b983e4d5fd55e72c1d4789b82f6633
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104863224"
---
# <a name="set-up-tls-encryption-and-authentication-for-apache-kafka-in-azure-hdinsight"></a>TLS-titkosítás és-hitelesítés beállítása Apache Kafkahoz az Azure HDInsight

Ez a cikk bemutatja, hogyan állíthat be Transport Layer Security (TLS) titkosítást, korábbi nevén SSL (SSL) titkosítást Apache Kafka ügyfelek és Apache Kafka-ügynökök között. Azt is bemutatja, hogyan állíthatja be az ügyfelek hitelesítését (más néven kétirányú TLS-ként).

> [!Important]
> Két ügyfél használhatja a Kafka-alkalmazásokhoz: egy Java-ügyfelet és egy konzol-ügyfelet. Csak a Java `ProducerConsumer.java` -ügyfél használhatja a TLS-t mind a gyártó, mind a fogyasztó számára. A konzol termelői ügyfele `console-producer.sh` nem működik a TLS-vel.

> [!Note]
> Az 1,1-es verzióval rendelkező HDInsight Kafka-konzol gyártója nem támogatja az SSL használatát.

## <a name="apache-kafka-broker-setup"></a>Apache Kafka Broker beállítása

A Kafka TLS Broker telepítője négy HDInsight-fürtöt fog használni a következő módon:

* átjárócsomóponthoz 0 – hitelesítésszolgáltató (CA)
* feldolgozói csomópont: 0, 1 és 2 – közvetítők

> [!Note] 
> Ez az útmutató önaláírt tanúsítványokat fog használni, de a legbiztonságosabb megoldás a megbízható hitelesítésszolgáltatók által kiadott tanúsítványok használata.

A közvetítő telepítési folyamatának összefoglalása a következő:

1. A következő lépéseket a három feldolgozó csomóponton kell megismételni:

    1. Állítson elő egy tanúsítványt.
    1. Hozzon létre egy tanúsítvány-aláírási kérelmet.
    1. Küldje el a tanúsítvány-aláírási kérelmet a hitelesítésszolgáltatónak (CA).
    1. Jelentkezzen be a HITELESÍTÉSSZOLGÁLTATÓba, és írja alá a kérelmet.
    1. SZOLGÁLTATÁSKAPCSOLÓDÁSI pont az aláírt tanúsítvány visszahívása a munkavégző csomópontra.
    1. SCP a HITELESÍTÉSSZOLGÁLTATÓ nyilvános tanúsítványa a munkavégző csomóponthoz.

1. Az összes tanúsítvány után helyezze a tanúsítványokat a tanúsítvány-tárolóba.
1. Nyissa meg a Ambari, és módosítsa a konfigurációkat.

A következő részletes utasításokat követve hajthatja végre a Broker telepítését:

> [!Important]
> A következő kódrészletekben a wnX a három munkavégző csomópont egyikének rövidítése, és a vagy a megfelelő módon kell `wn0` helyettesíteni `wn1` `wn2` . `WorkerNode0_Name` és a `HeadNode0_Name` megfelelő gépek nevével kell helyettesíteni.

1. Hajtsa végre a kezdeti beállítást a 0. fő csomóponton, amely a HDInsight kitölti a hitelesítésszolgáltató (CA) szerepkörét.

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl
    ```

1. Hajtsa végre ugyanezt a kezdeti beállítást minden egyes brókernél (feldolgozói csomópontok: 0, 1 és 2).

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl
    ```

1. Mindegyik munkavégző csomóponton hajtsa végre a következő lépéseket az alábbi kódrészlet használatával.
    1. Hozzon létre egy tárolót, és töltse fel egy új privát tanúsítvánnyal.
    1. Hozzon létre egy tanúsítvány-aláírási kérelmet.
    1. SZOLGÁLTATÁSKAPCSOLÓDÁSI pont a tanúsítvány-aláírási kérelem a CA számára (headnode0)

    ```bash
    keytool -genkey -keystore kafka.server.keystore.jks -validity 365 -storepass "MyServerPassword123" -keypass "MyServerPassword123" -dname "CN=FQDN_WORKER_NODE" -storetype pkcs12
    keytool -keystore kafka.server.keystore.jks -certreq -file cert-file -storepass "MyServerPassword123" -keypass "MyServerPassword123"
    scp cert-file sshuser@HeadNode0_Name:~/ssl/wnX-cert-sign-request
    ```

1. A hitelesítésszolgáltatói számítógépen futtassa a következő parancsot a CA-CERT és a CA-Key fájlok létrehozásához:

    ```bash
    openssl req -new -newkey rsa:4096 -days 365 -x509 -subj "/CN=Kafka-Security-CA" -keyout ca-key -out ca-cert -nodes
    ```

1. Váltson a HITELESÍTÉSSZOLGÁLTATÓI gépre, és írja alá az összes fogadott tanúsítvány-aláírási kérelmet:

    ```bash
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn0-cert-sign-request -out wn0-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn1-cert-sign-request -out wn1-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn2-cert-sign-request -out wn2-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    ```

1. Küldje vissza az aláírt tanúsítványokat a munkavégző csomópontoknak a HITELESÍTÉSSZOLGÁLTATÓTÓL (headnode0).

    ```bash
    scp wn0-cert-signed sshuser@WorkerNode0_Name:~/ssl/cert-signed
    scp wn1-cert-signed sshuser@WorkerNode1_Name:~/ssl/cert-signed
    scp wn2-cert-signed sshuser@WorkerNode2_Name:~/ssl/cert-signed
    ```

1. Küldje el a HITELESÍTÉSSZOLGÁLTATÓ nyilvános tanúsítványát az egyes munkavégző csomópontoknak.

    ```bash
    scp ca-cert sshuser@WorkerNode0_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode1_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode2_Name:~/ssl/ca-cert
    ```

1. Minden munkavégző csomóponton adja hozzá a CAs nyilvános tanúsítványt a truststore és a tárolóhoz. Ezután adja hozzá a munkavégző csomópont saját aláírt tanúsítványát a tárolóhoz.

    ```bash
    keytool -keystore kafka.server.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -import -file cert-signed -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt

    ```

## <a name="update-kafka-configuration-to-use-tls-and-restart-brokers"></a>A Kafka konfigurációjának frissítése a TLS-és újraindítási közvetítők használatára

Most már beállította az egyes Kafka-átvitelszervezőket egy truststore, és importálta a megfelelő tanúsítványokat. Ezután módosítsa a kapcsolódó Kafka-konfiguráció tulajdonságait a Ambari használatával, majd indítsa újra a Kafka-közvetítőket.

A konfiguráció módosításának befejezéséhez hajtsa végre a következő lépéseket:

1. Jelentkezzen be a Azure Portalba, és válassza ki az Azure HDInsight Apache Kafka-fürtöt.
1. Nyissa meg a Ambari felhasználói felületét a **Ambari** elemre kattintva a **fürt irányítópultok** területén.
1. A **Kafka-közvetítő** alatt állítsa be a **figyelők** tulajdonságot a következőre `PLAINTEXT://localhost:9092,SSL://localhost:9093`
1. A **speciális Kafka-Broker** területen állítsa be a **Security. Inter. Broker. Protocol** tulajdonságot a következőre: `SSL`

    :::image type="content" source="./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari.png" alt-text="A Kafka SSL konfigurációs tulajdonságainak szerkesztése a Ambari-ben" border="true":::

1. Az **Egyéni Kafka-Broker** területen állítsa be az **SSL. Client. auth** tulajdonságot a következőre: `required` . Ez a lépés csak akkor szükséges, ha a hitelesítést és a titkosítást állítja be.

    :::image type="content" source="./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari2.png" alt-text="A Kafka SSL konfigurációs tulajdonságainak szerkesztése a Ambari-ben" border="true":::

1. A HDI 3,6-es verziójában nyissa meg a Ambari felhasználói felületét, és adja hozzá a következő konfigurációkat a **speciális Kafka-env** és a **Kafka-env sablon** tulajdonsághoz.

    ```bash
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092,SSL://$IP_ADDRESS:9093" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.keystore.location=/home/sshuser/ssl/kafka.server.keystore.jks" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.keystore.password=MyServerPassword123" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.key.password=MyServerPassword123" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.truststore.location=/home/sshuser/ssl/kafka.server.truststore.jks" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.truststore.password=MyServerPassword123" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

1. Itt látható a képernyőkép, amely a Ambari konfigurációs felhasználói felületet mutatja be ezekkel a módosításokkal.

    A HDI 3,6-es verziójában:

    :::image type="content" source="./media/apache-kafka-ssl-encryption-authentication/editing-configuration-kafka-env.png" alt-text="Kafka-env template tulajdonság szerkesztése a Ambari-ben" border="true":::

    A HDI 4,0-es verziójában:

     :::image type="content" source="./media/apache-kafka-ssl-encryption-authentication/editing-configuration-kafka-env-four.png" alt-text="Kafka-env template tulajdonság szerkesztése a Ambari Four-ban" border="true":::

1. Indítsa újra az összes Kafka-közvetítőt.

## <a name="client-setup-without-authentication"></a>Ügyfél beállítása (hitelesítés nélkül)

Ha nincs szüksége a hitelesítésre, a csak a TLS-titkosítás beállításához szükséges lépések összegzése a következő:

1. Jelentkezzen be a HITELESÍTÉSSZOLGÁLTATÓba (aktív fő csomópont).
1. Másolja a HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt az ügyfélszámítógépre a HITELESÍTÉSSZOLGÁLTATÓI gépről (wn0).
1. Jelentkezzen be az ügyfélszámítógépre (hn1), és navigáljon a `~/ssl` mappához.
1. Importálja a HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt a truststore.
1. Importálja a HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt a rendszertárolóba.

Ezeket a lépéseket a következő kódrészletekben részletezjük.

1. Jelentkezzen be a HITELESÍTÉSSZOLGÁLTATÓI csomópontba.

    ```bash
    ssh sshuser@HeadNode0_Name
    cd ssl
    ```

1. A CA-CERT másolása az ügyfélszámítógépre

    ```bash
    scp ca-cert sshuser@HeadNode1_Name:~/ssl/ca-cert
    ```

1. Jelentkezzen be az ügyfélszámítógépre (készenléti fej csomópontja).

    ```bash
    ssh sshuser@HeadNode1_Name
    cd ssl
    ```

1. Importálja a HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt a truststore.

    ```bash
    keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    ```

1. Importálja a HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt a tárolóba.
    
    ```bash
    keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    ```

1. Hozza létre a fájlt az `client-ssl-auth.properties` ügyfélgépen (hn1). A következő sorokkal kell rendelkeznie:

    ```config
    security.protocol=SSL
    ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
    ssl.truststore.password=MyClientPassword123
    ```

1. Indítsa el a felügyeleti ügyfelet a producer és a fogyasztói lehetőségek közül annak ellenőrzéséhez, hogy mindkét gyártó és a fogyasztó dolgozik-e a 9093-es porton. Tekintse meg az alábbi [ellenőrzési](apache-kafka-ssl-encryption-authentication.md#verification) szakaszt a telepítő konzolos gyártó/fogyasztó használatával történő ellenőrzéséhez szükséges lépésekért.

## <a name="client-setup-with-authentication"></a>Ügyfél beállítása (hitelesítéssel)

> [!Note]
> A következő lépések csak akkor szükségesek, ha a TLS-titkosítást **és** a hitelesítést is beállítja. Ha csak a titkosítást állítja be, akkor az [ügyfél beállítása hitelesítés nélkül](apache-kafka-ssl-encryption-authentication.md#client-setup-without-authentication)című témakörben talál további információt.

A következő négy lépés összefoglalja az ügyfél telepítésének befejezéséhez szükséges feladatokat:

1. Jelentkezzen be az ügyfélszámítógépre (készenléti fej csomópontja).
1. Hozzon létre egy Java-tárolót, és szerezzen be egy aláírt tanúsítványt a közvetítő számára. Ezután másolja a tanúsítványt arra a virtuális gépre, amelyen a HITELESÍTÉSSZOLGÁLTATÓ fut.
1. Váltson át a HITELESÍTÉSSZOLGÁLTATÓI gépre (az aktív fő csomópontra) az ügyféltanúsítvány aláírásához.
1. Nyissa meg az ügyfélszámítógépet (készenléti fej csomópont), és navigáljon a `~/ssl` mappához. Másolja az aláírt tanúsítványt az ügyfélszámítógépre.

Az egyes lépések részleteit alább találja.

1. Jelentkezzen be az ügyfélszámítógépre (készenléti fej csomópontja).

    ```bash
    ssh sshuser@HeadNode1_Name
    ```

1. Távolítsa el a meglévő SSL-könyvtárat.

    ```bash
    rm -R ~/ssl
    mkdir ssl
    cd ssl
    ```

1. Hozzon létre egy Java-tárolót, és hozzon létre egy tanúsítvány-aláírási kérelmet. 

    ```bash
    keytool -genkey -keystore kafka.client.keystore.jks -validity 365 -storepass "MyClientPassword123" -keypass "MyClientPassword123" -dname "CN=HEADNODE1_FQDN" -storetype pkcs12
    
    keytool -keystore kafka.client.keystore.jks -certreq -file client-cert-sign-request -storepass "MyClientPassword123" -keypass "MyClientPassword123"
    ```

1. A tanúsítvány-aláírási kérelem másolása a HITELESÍTÉSSZOLGÁLTATÓTÓL

    ```bash
    scp client-cert-sign-request sshuser@HeadNode0_Name:~/ssl/client-cert-sign-request
    ```

1. Váltson a HITELESÍTÉSSZOLGÁLTATÓI gépre (az aktív fő csomópontra), és írja alá az ügyféltanúsítványt.

    ```bash
    ssh sshuser@HeadNode0_Name
    cd ssl
    openssl x509 -req -CA ca-cert -CAkey ca-key -in ~/ssl/client-cert-sign-request -out ~/ssl/client-cert-signed -days 365 -CAcreateserial -passin pass:MyClientPassword123
    ```

1. Az aláírt ügyfél tanúsítványának másolása a CA-ból (aktív fő csomópont) az ügyfélszámítógépre.

    ```bash
    scp client-cert-signed sshuser@HeadNode1_Name:~/ssl/client-signed-cert
    ```

1. A CA-CERT másolása az ügyfélszámítógépre

    ```bash
    scp ca-cert sshuser@HeadNode1_Name:~/ssl/ca-cert
    ```

    1. Jelentkezzen be az ügyfélszámítógépre (készenléti fej csomópont), és navigáljon az SSL-címtárhoz.

    ```bash
    ssh sshuser@HeadNode1_Name
    cd ssl
    ```

1. Hozzon létre az ügyfél-áruházat aláírt tanúsítványokkal, és importálja a hitelesítésszolgáltatói tanúsítványt a (z) truststore az ügyfélszámítógépen (hn1).

    ```bash
    keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    
    keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    
    keytool -keystore kafka.client.keystore.jks -import -file client-cert-signed -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    ```

1. Hozzon létre egy fájlt `client-ssl-auth.properties` az ügyfélgépen (hn1). A következő sorokkal kell rendelkeznie:

    ```bash
    security.protocol=SSL
    ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
    ssl.truststore.password=MyClientPassword123
    ssl.keystore.location=/home/sshuser/ssl/kafka.client.keystore.jks
    ssl.keystore.password=MyClientPassword123
    ssl.key.password=MyClientPassword123
    ```

## <a name="verification"></a>Ellenőrzés

Futtassa ezeket a lépéseket az ügyfélszámítógépen.

> [!Note]
> Ha a HDInsight 4,0 és a Kafka 2,1 telepítve van, akkor a konzol gyártójának/felhasználóinak segítségével ellenőrizheti a telepítést. Ha nem, futtassa a Kafka gyártóját a 9092-as porton, és küldjön üzeneteket a témakörbe, majd használja a Kafka-fogyasztót a 9093-es porton, amely a TLS protokollt használja.

### <a name="kafka-21-or-above"></a>Kafka 2,1 vagy újabb

1. Hozzon létre egy témakört, ha már nem létezik.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --zookeeper <ZOOKEEPER_NODE>:2181 --create --topic topic1 --partitions 2 --replication-factor 2
    ```

1. Indítsa el a konzol gyártóját, és adja meg az elérési utat a `client-ssl-auth.properties` gyártó konfigurációs fájljához.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list <FQDN_WORKER_NODE>:9093 --topic topic1 --producer.config ~/ssl/client-ssl-auth.properties
    ```

1. Nyisson meg egy másik SSH-kapcsolódást az ügyfélgépen, és indítsa el a konzol fogyasztóját, és adja meg az elérési utat a `client-ssl-auth.properties` fogyasztó konfigurációs fájljához.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server <FQDN_WORKER_NODE>:9093 --topic topic1 --consumer.config ~/ssl/client-ssl-auth.properties --from-beginning
    ```

### <a name="kafka-11"></a>Kafka 1,1

1. Hozzon létre egy témakört, ha már nem létezik.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --zookeeper <ZOOKEEPER_NODE_0>:2181 --create --topic topic1 --partitions 2 --replication-factor 2
    ```

1. Indítsa el a konzol gyártóját, és adja meg az ügyfél-SSL-auth. properties elérési útját a gyártó konfigurációs fájljának.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list <FQDN_WORKER_NODE>:9092 --topic topic1 
    ```

1. Nyisson meg egy másik SSH-kapcsolódást az ügyfélgépen, és indítsa el a konzol fogyasztóját, és adja meg az elérési utat a `client-ssl-auth.properties` fogyasztó konfigurációs fájljához.

    ```bash
    $ /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server <FQDN_WORKER_NODE>:9093 --topic topic1 --consumer.config ~/ssl/client-ssl-auth.properties --from-beginning
    ```

## <a name="next-steps"></a>Következő lépések

* [Mi az a HDInsight-alapú Apache Kafka?](apache-kafka-introduction.md)
