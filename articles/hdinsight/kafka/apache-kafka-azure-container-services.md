---
title: Az Azure Kubernetes szolgáltatás használata a Kafka on HDInsight
description: Ismerje meg, hogyan használható a Kafka a HDInsight az Azure Kubernetes szolgáltatásban (ak) üzemeltetett tárolók rendszerképein.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/04/2019
ms.openlocfilehash: d807b591229644984f6658cdacd0bf447759f292
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98933028"
---
# <a name="use-azure-kubernetes-service-with-apache-kafka-on-hdinsight"></a>Az Azure Kubernetes szolgáltatás használata a HDInsight-mel való Apache Kafka

Ismerje meg, hogyan használhatja az Azure Kubernetes szolgáltatást (ak) a HDInsight-fürtön lévő [Apache Kafka](https://kafka.apache.org/) használatával. A jelen dokumentumban ismertetett lépések egy, az AK-ban üzemeltetett Node.js alkalmazást használnak a Kafka-kapcsolat ellenőrzéséhez. Ez az alkalmazás a [Kafka-Node](https://www.npmjs.com/package/kafka-node) csomagot használja a Kafka-vel való kommunikációhoz. A [socket.IO](https://socket.io/) használja az eseményvezérelt üzenetküldéshez a böngésző-ügyfél és az AK-ban üzemeltetett háttér között.

Az [Apache Kafka](https://kafka.apache.org) egy nyílt forráskódú elosztott streamelési platform streamadatfolyamatok és -alkalmazások létrehozásához. Az Azure Kubernetes szolgáltatás kezeli az üzemeltetett Kubernetes-környezetet, és gyorsan és egyszerűen üzembe helyezi a tároló alkalmazásokat. Az Azure Virtual Network használatával összekapcsolhatók a két szolgáltatás.

> [!NOTE]  
> Ennek a dokumentumnak a témája az Azure Kubernetes Service és a HDInsight Kafka-kommunikációjának engedélyezéséhez szükséges lépések. Maga a példa egy alapszintű Kafka-ügyfél, amely igazolja, hogy a konfiguráció működik.

## <a name="prerequisites"></a>Előfeltételek

* [Azure CLI](/cli/azure/install-azure-cli)
* Azure-előfizetés

Ez a dokumentum azt feltételezi, hogy már ismeri a következő Azure-szolgáltatások létrehozását és használatát:

* Kafka on HDInsight
* Azure Kubernetes Service
* Azure virtuális hálózatok

Ez a dokumentum azt is feltételezi, hogy elvégezte az [Azure Kubernetes szolgáltatás oktatóanyagát](../../aks/tutorial-kubernetes-prepare-app.md). Ez a cikk egy tároló szolgáltatást hoz létre, létrehoz egy Kubernetes-fürtöt, egy tároló-beállításjegyzéket, és konfigurálja a `kubectl` segédprogramot.

## <a name="architecture"></a>Architektúra

### <a name="network-topology"></a>Hálózati topológia

Mind a HDInsight, mind az AK egy Azure-Virtual Network használ a számítási erőforrások tárolójának. A HDInsight és az AK közötti kommunikáció engedélyezéséhez engedélyeznie kell a hálózatok közötti kommunikációt. A jelen dokumentum lépései a Virtual Network a hálózatokra való kapcsolódást használják. Más kapcsolatok, például a VPN is működnek. A társítással kapcsolatos további információkért tekintse meg a [virtuális hálózat](../../virtual-network/virtual-network-peering-overview.md) társítása című dokumentumot.

A következő ábra a dokumentumban használt hálózati topológiát szemlélteti:

![HDInsight egy virtuális hálózatban (ak) egy másikban, a társítás használatával](./media/apache-kafka-azure-container-services/kafka-aks-architecture.png)

> [!IMPORTANT]  
> A névfeloldás nincs engedélyezve a megosztott hálózatok között, ezért az IP-címzést használja a rendszer. Alapértelmezés szerint a Kafka on HDInsight úgy van konfigurálva, hogy az IP-címek helyett a gazdagépek nevét állítsa vissza, amikor az ügyfelek csatlakoznak. A jelen dokumentumban ismertetett lépések a Kafka-t úgy módosítják, hogy helyette az IP-hirdetéseket használják.

## <a name="create-an-azure-kubernetes-service-aks"></a>Azure Kubernetes szolgáltatás létrehozása (ak)

Ha még nem rendelkezik AK-fürttel, az alábbi dokumentumok egyikével megtudhatja, hogyan hozhat létre egyet:

* [Azure Kubernetes Service (ak) fürt üzembe helyezése – portál](../../aks/kubernetes-walkthrough-portal.md)
* [Azure Kubernetes Service (ak) fürt üzembe helyezése – parancssori felület](../../aks/kubernetes-walkthrough.md)

> [!IMPORTANT]  
> Az AK egy virtuális hálózatot hoz létre a telepítés során egy **további** erőforráscsoporthoz. A további erőforráscsoport a **MC_resourceGroup_AKSclusterName_location** elnevezési konvencióját követi.  
> Ez a hálózat a következő szakaszban a HDInsight számára létrehozotthoz van összefoglalva.

## <a name="configure-virtual-network-peering"></a>Virtuális hálózati társak konfigurálása

### <a name="identify-preliminary-information"></a>Előzetes információk azonosítása

1. A [Azure Portal](https://portal.azure.com)keresse meg azt a további **erőforráscsoportot** , amely az AK-fürt virtuális hálózatát tartalmazza.

2. Az erőforráscsoport területen válassza ki a __virtuális hálózati__ erőforrást. Jegyezze fel a nevet későbbi felhasználásra.

3. A **Beállítások** területen válassza a __Címtartomány__ elemet. Jegyezze fel a felsorolt címterület méretét.

### <a name="create-virtual-network"></a>Virtuális hálózat létrehozása

1. Virtuális hálózat HDInsight való létrehozásához navigáljon a __+ erőforrás létrehozása__  >  __hálózatkezelés__  >  __virtuális hálózat__ elemre.

1. Hozza létre a hálózatot a következő irányelvek alapján bizonyos tulajdonságok esetében:

    |Tulajdonság | Érték |
    |---|---|
    |Címtér|Olyan címtartományt kell használnia, amely nem fedi át az AK-fürt hálózata által használt területet.|
    |Hely|Használja ugyanazt a __helyet__ az AK-fürthöz használt virtuális hálózathoz.|

1. Várjon, amíg a virtuális hálózat létrejött, mielőtt továbblép a következő lépésre.

### <a name="configure-peering"></a>Társviszony-létesítés konfigurálása

1. A HDInsight hálózat és az AK-fürt közötti társítás konfigurálásához __válassza ki a__ virtuális hálózatot, majd válassza a társítások lehetőséget.

1. Válassza a __+ Hozzáadás__ lehetőséget, és használja az alábbi értékeket az űrlap feltöltéséhez:

    |Tulajdonság |Érték |
    |---|---|
    |A végpont neve a \<this VN> távoli virtuális hálózatba|Adjon meg egy egyedi nevet ehhez a társ-konfigurációhoz.|
    |Virtuális hálózat|Válassza ki a virtuális hálózatot az **AK-fürthöz**.|
    |\<AKS VN>A társítás neve\<this VN>|Adjon meg egy egyedi nevet.|

    Hagyja meg az összes többi mezőt az alapértelmezett értéknél, majd kattintson az __OK__ gombra a társítás konfigurálásához.

## <a name="create-apache-kafka-cluster-on-hdinsight"></a>Apache Kafka-fürt létrehozása a HDInsight-ben

A Kafka HDInsight-fürtön való létrehozásakor csatlakoztatnia kell a korábban létrehozott virtuális hálózatot a HDInsight-hez. A Kafka-fürtök létrehozásával kapcsolatos további információkért tekintse meg az [Apache Kafka-fürt létrehozása](apache-kafka-get-started.md) című dokumentumot.

## <a name="configure-apache-kafka-ip-advertising"></a>Apache Kafka IP-hirdetés konfigurálása

A következő lépésekkel konfigurálhatja a Kafka-t, hogy a tartománynevek helyett IP-címeket Hirdessen:

1. Egy böngészőben nyissa meg a következőt: `https://CLUSTERNAME.azurehdinsight.net`. Cserélje le a CLUSTERNAME nevet a Kafka on HDInsight-fürt nevére.

    Ha a rendszer kéri, használja a fürt HTTPS-felhasználónevét és-jelszavát. Megjelenik a fürt Ambari webes felhasználói felülete.

2. A Kafka-adatok megtekintéséhez válassza a __Kafka__ lehetőséget a bal oldali listából.

    ![Szolgáltatások listája a Kafka kiemelésével](./media/apache-kafka-azure-container-services/select-kafka-service.png)

3. A Kafka-konfiguráció megtekintéséhez válassza a __konfigurációk__ lehetőséget a felső középső listából.

    ![Apache Ambari Services-konfiguráció](./media/apache-kafka-azure-container-services/select-kafka-config1.png)

4. A __Kafka-env__ konfiguráció megkereséséhez írja be a `kafka-env` jobb felső sarokban található __szűrő__ mezőt.

    ![Kafka-konfiguráció, Kafka-env](./media/apache-kafka-azure-container-services/search-for-kafka-env.png)

5. Ha a Kafka-t IP-címekre szeretné reklámozni, adja hozzá a következő szöveget a __Kafka-env-template__ mező aljához:

    ```bash
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

6. A Kafka által figyelt felület konfigurálásához írja be a `listeners` jobb felső sarokban található __szűrő__ mezőt.

7. Ha úgy szeretné beállítani a Kafka-t, hogy az összes hálózati adaptert figyelje, módosítsa a __figyelők__ mező értékét a következőre: `PLAINTEXT://0.0.0.0:9092` .

8. A konfigurációs módosítások mentéséhez használja a Save ( __Mentés__ ) gombot. Adjon meg egy szöveges üzenetet, amely leírja a módosításokat. A módosítások mentése után válassza __az OK gombot__ .

    ![Apache Ambari-mentési konfiguráció](./media/apache-kafka-azure-container-services/save-configuration-button.png)

9. Ha meg szeretné akadályozni a Kafka újraindításakor fellépő hibákat, használja a __szolgáltatási műveletek__ gombot, és válassza a __karbantartási mód bekapcsolása__ lehetőséget. A művelet befejezéséhez kattintson az OK gombra.

    ![Szolgáltatási műveletek, a karbantartás bekapcsolásával](./media/apache-kafka-azure-container-services/turn-on-maintenance-mode.png)

10. A Kafka újraindításához használja az __Újraindítás__ gombot, és válassza az __összes érintett újraindítása__ lehetőséget. Erősítse meg az újraindítást, majd használja az __OK__ gombot a művelet befejeződése után.

    ![Újraindítás gomb az összes érintett Kiemelt újraindítással](./media/apache-kafka-azure-container-services/restart-required-button.png)

11. A karbantartási mód letiltásához használja a __szolgáltatási műveletek__ gombot, és válassza a __karbantartási mód kikapcsolása__ lehetőséget. A művelet befejezéséhez kattintson **az OK gombra** .

## <a name="test-the-configuration"></a>A konfiguráció tesztelése

Ezen a ponton a Kafka és az Azure Kubernetes szolgáltatás kommunikál a Kiemelt virtuális hálózatokon keresztül. A kapcsolódás teszteléséhez kövesse az alábbi lépéseket:

1. Hozzon létre egy Kafka-témakört, amelyet a teszt alkalmazás használ. A Kafka-témakörök létrehozásával kapcsolatos információkért tekintse meg a [Apache Kafka-fürt létrehozása](apache-kafka-get-started.md) című dokumentumot.

2. Töltse le a példa alkalmazást a alkalmazásból [https://github.com/Blackmist/Kafka-AKS-Test](https://github.com/Blackmist/Kafka-AKS-Test) .

3. Szerkessze a `index.js` fájlt, és módosítsa a következő sorokat:

    * `var topic = 'mytopic'`: Cserélje le az `mytopic` alkalmazást az alkalmazás által használt Kafka-témakör nevére.
    * `var brokerHost = '176.16.0.13:9092`: Cserélje le a `176.16.0.13` t a fürt egyik Broker-gazdagépének belső IP-címére.

        A fürtben található Broker-gazdagépek (workernodes) belső IP-címének megkereséséhez tekintse meg az [Apache Ambari REST API](../hdinsight-hadoop-manage-ambari-rest-api.md#get-the-internal-ip-address-of-cluster-nodes) dokumentumot. Válassza ki az egyik olyan bejegyzés IP-címét, amelyben a tartománynév kezdődik `wn` .

4. A címtár egyik parancssorában `src` telepítse a függőségeket, és a Docker használatával hozzon létre egy rendszerképet az üzembe helyezéshez:

    ```bash
    docker build -t kafka-aks-test .
    ```

    > [!NOTE]  
    > Az alkalmazás által igényelt csomagokat a rendszer a tárházba ellenőrzi, így nem kell a segédprogramot használnia a `npm` telepítéshez.

5. Jelentkezzen be a Azure Container Registryba (ACR), és keresse meg a lekéréséhez nevét:

    ```azurecli
    az acr login --name <acrName>
    az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
    ```

    > [!NOTE]  
    > Ha nem ismeri a Azure Container Registry nevét, vagy nem tudja, hogy az Azure CLI használatával működjön együtt az Azure Kubernetes szolgáltatással, tekintse meg az [AK-oktatóanyagokat](../../aks/tutorial-kubernetes-prepare-app.md).

6. Címkézze fel a helyi `kafka-aks-test` rendszerképet az ACR lekéréséhez. Adja hozzá a `:v1` végéhez a rendszerkép verziószámát is:

    ```bash
    docker tag kafka-aks-test <acrLoginServer>/kafka-aks-test:v1
    ```

7. Küldje le a rendszerképet a beállításjegyzékbe:

    ```bash
    docker push <acrLoginServer>/kafka-aks-test:v1
    ```

    A művelet végrehajtása több percet is igénybe vehet.

8. Szerkessze a Kubernetes jegyzékfájlját ( `kafka-aks-test.yaml` ), és cserélje le a `microsoft` 4. lépésben beolvasott ACR-lekéréséhez.

9. Az alábbi paranccsal telepítheti az Alkalmazásbeállítások a jegyzékből:

    ```bash
    kubectl create -f kafka-aks-test.yaml
    ```

10. Az alkalmazás figyeléséhez használja a következő parancsot `EXTERNAL-IP` :

    ```bash
    kubectl get service kafka-aks-test --watch
    ```

    A külső IP-cím hozzárendelése után a __CTRL + C__ billentyűkombinációval lépjen ki a figyelésből

11. Nyisson meg egy webböngészőt, és adja meg a szolgáltatás külső IP-címét. A következő képhez hasonló oldal érkezik:

    ![Apache Kafka weblap képe](./media/apache-kafka-azure-container-services/test-web-page-image1.png)

12. Írja be a szöveget a mezőbe, majd kattintson a __Küldés__ gombra. Az adatküldés a Kafka szolgáltatásba történik. Az alkalmazásban a Kafka-fogyasztó beolvassa az üzenetet, és hozzáadja azt a Kafka-szakasz __üzeneteihez__ .

    > [!WARNING]  
    > Az üzenetek több példányban is megjelenhetnek. Ez a probléma általában akkor fordul elő, ha a böngészőt a csatlakozás után frissíti, vagy több böngészőbeli kapcsolatot nyit meg az alkalmazáshoz.

## <a name="next-steps"></a>Következő lépések

A HDInsighton futó Apache Kafka használatának megismeréséhez tekintse meg a következő hivatkozásokat:

* [Ismerkedés a HDInsight Apache Kafkaával](apache-kafka-get-started.md)

* [Apache Kafka replikájának létrehozása a MirrorMaker használatával a HDInsight](apache-kafka-mirroring.md)

* [Apache Storm használata a HDInsight Apache Kafka használatával](../hdinsight-apache-storm-with-kafka.md)

* [Apache Spark használata a HDInsight Apache Kafka használatával](../hdinsight-apache-spark-with-kafka.md)

* [Kapcsolódás Apache Kafka Azure-beli Virtual Network](apache-kafka-connect-vpn-gateway.md)
