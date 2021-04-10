---
title: Működővé tenni ML-szolgáltatások a HDInsight-ben – Azure
description: Ismerje meg, hogyan működővé tenni az adatmodellt, hogy előrejelzéseket készítsen az Azure HDInsight ML-szolgáltatásaival.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 06/27/2018
ms.openlocfilehash: 7d597c4dbb81562050e9523c61d47d0020fc9059
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104869468"
---
# <a name="operationalize-ml-services-cluster-on-azure-hdinsight"></a>Működővé tenni ML Services-fürt az Azure HDInsight

Miután a HDInsight-ben használta a ML Services-fürtöt az adatmodellezés befejezéséhez, működővé tenni a modellt, hogy előkészítse az előrejelzéseket. Ez a cikk útmutatást nyújt a feladat végrehajtásához.

## <a name="prerequisites"></a>Előfeltételek

* Egy ML Services-fürt a HDInsight-on. Lásd: [Apache Hadoop-fürtök létrehozása a Azure Portal használatával](../hdinsight-hadoop-create-linux-clusters-portal.md) , majd a **ml-szolgáltatások** kiválasztása a **fürt típusához**.

* Secure Shell- (SSH-) ügyfél: Egy SSH-ügyféllel távolról csatlakozhat a HDInsight-fürthöz, és közvetlenül a fürtön futtathat parancsokat. További információ: az [SSH használata a HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="operationalize-ml-services-cluster-with-one-box-configuration"></a>Működővé tenni ML Services-fürt egy beépített konfigurációval

> [!NOTE]  
> Az alábbi lépések a R Server 9,0 és a ML Server 9,1-es verzióra vonatkoznak. ML Server 9,3 esetében tekintse meg a [operacionalizálási-konfiguráció felügyeletét a felügyeleti eszköz használatával](/machine-learning-server/operationalize/configure-admin-cli-launch)című témakört.

1. Jelentkezzen be SSH-n keresztül az élcsomópontba.

    ```bash
    ssh USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net
    ```

    Az SSH és az Azure HDInsight használatával kapcsolatos utasításokért lásd: az [SSH használata a HDInsight.](../hdinsight-hadoop-linux-use-ssh-unix.md)

1. Módosítsa a megfelelő verziójú könyvtárat és a sudo NET DLL-t: 

    - Microsoft ML Server 9,1 esetén:

        ```bash
        cd /usr/lib64/microsoft-r/rserver/o16n/9.1.0
        sudo dotnet Microsoft.RServer.Utils.AdminUtil/Microsoft.RServer.Utils.AdminUtil.dll
        ```

    - Microsoft R Server 9.0 esetén:

        ```bash
        cd /usr/lib64/microsoft-deployr/9.0.1
        sudo dotnet Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll
        ```

1. A választható lehetőségek közül választhat. Válassza az első lehetőséget, ahogyan az a következő képernyőképen is látható, a **operacionalizálási ml Server konfigurálásához**.

    :::image type="content" source="./media/r-server-operationalize/admin-util-one-box-1.png" alt-text="R Server felügyeleti segédprogram kiválasztása" border="true":::

1. Ezzel a lehetőséggel megadhatja, hogyan szeretné működővé tenni ML Server. A megjelenő beállítások közül válassza ki az elsőt a következő beírásával **:.**

    :::image type="content" source="./media/r-server-operationalize/admin-util-one-box-2.png" alt-text="R Server felügyeleti segédprogram működővé tenni" border="true":::

1. Ha a rendszer kéri, írja be és írja be újra a helyi rendszergazda felhasználó jelszavát.

1. A művelet sikerességét sugalló kimenetnek kell megjelennie. A menüben egy másik lehetőséget is meg kell adnia. Válassza az E lehetőséget a főmenüre való visszatéréshez.

    :::image type="content" source="./media/r-server-operationalize/admin-util-one-box-3.png" alt-text="Az R Server felügyeleti segédprogramjának sikeressége" border="true":::

1. A diagnosztikai ellenőrzéseket a következőképpen végezheti el diagnosztikai tesztek futtatásával:

    a. A főmenüben válassza a **6** lehetőséget a diagnosztikai tesztek futtatásához.

    :::image type="content" source="./media/r-server-operationalize/hdinsight-diagnostic1.png" alt-text="R Server felügyeleti segédprogram diagnosztika" border="true":::

    b. A diagnosztikai tesztek menüből válassza ki **a** következőt:. Ha a rendszer kéri, adja meg a helyi rendszergazda felhasználóhoz megadott jelszót.

    :::image type="content" source="./media/r-server-operationalize/hdinsight-diagnostic2.png" alt-text="R Server felügyeleti segédprogram tesztelése" border="true":::

    c. Győződjön meg arról, hogy a kimenetben látható, hogy a teljes állapot pass.

    :::image type="content" source="./media/r-server-operationalize/hdinsight-diagnostic3.png" alt-text="R Server felügyeleti segédprogram pass" border="true":::

    d. A megjelenő menüben adja meg az **E** gombot a főmenühöz való visszatéréshez, majd írja be a **8-as** értéket a felügyeleti segédprogramból való kilépéshez.

### <a name="long-delays-when-consuming-web-service-on-apache-spark"></a>Hosszú késések a webszolgáltatás Apache Spark

Ha hosszú késleltetést tapasztal, amikor egy Apache Spark számítási környezetben mrsdeploy függvényekkel létrehozott webszolgáltatást próbál felhasználni, előfordulhat, hogy hozzá kell adnia néhány hiányzó mappát. A Spark-alkalmazás egy *rserve2* nevű felhasználóhoz tartozik, ha egy mrsdeploy függvényeket használó webszolgáltatásból hívja meg. Megkerülő megoldás a problémára:

```r
# Create these required folders for user 'rserve2' in local and hdfs:

hadoop fs -mkdir /user/RevoShare/rserve2
hadoop fs -chmod 777 /user/RevoShare/rserve2

mkdir /var/RevoShare/rserve2
chmod 777 /var/RevoShare/rserve2


# Next, create a new Spark compute context:

rxSparkConnect(reset = TRUE)
```

Az operacionalizálás konfigurációja ezzel befejeződött. Most már használhatja a `mrsdeploy` csomagot a rclientben, hogy csatlakozhasson a operacionalizálási a peremhálózati csomóponton, és megkezdje az olyan funkciók használatát, mint a [távoli végrehajtás](/machine-learning-server/r/how-to-execute-code-remotely) és a [webes szolgáltatások](/machine-learning-server/operationalize/concept-what-are-web-services). Attól függően, hogy fürt virtuális hálózaton van-e beállítva, szükség lehet porttovábbító bújtatás kialakítására SSH-bejelentkezésen keresztül. Az alábbi szakaszok ismertetik, hogyan állíthatja be ezt az alagutat.

### <a name="ml-services-cluster-on-virtual-network"></a>ML Services-fürt a virtuális hálózaton

Bizonyosodjon meg róla, hogy engedélyezett a forgalom az 12800-as porton az élcsomópont felé. Így az élcsomópont használatával kapcsolódhat az operacionalizálási szolgáltatáshoz.

```r
library(mrsdeploy)

remoteLogin(
    deployr_endpoint = "http://[your-cluster-name]-ed-ssh.azurehdinsight.net:12800",
    username = "admin",
    password = "xxxxxxx"
)
```

Ha a `remoteLogin()` metódus nem tud kapcsolódni az élcsomóponthoz, de SSH-n be tud jelentkezni az élcsomópontba, győződjön meg róla, hogy a szabály, amely engedélyezi a forgalmat az 12800-as porton, megfelelően van-e beállítva. Ha a probléma továbbra is jelentkezik, egy másik megoldás segítségével is beállíthat porttovábbító alagutat az SSH-n keresztül. Útmutatásért tekintse meg a következő szakaszt:

### <a name="ml-services-cluster-not-set-up-on-virtual-network"></a>A ML Services-fürt nincs beállítva a virtuális hálózaton

Ha a fürt nem a virtuális hálózaton van beállítva vagy problémás a kapcsolódás a virtuális hálózaton keresztül, akkor használhatja az SSH porttovábbító alagutat:

```bash
ssh -L localhost:12800:localhost:12800 USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net
```

Ha az SSH-munkamenet aktív, a rendszer a helyi gép 12800-as portján keresztül továbbítja a forgalmat az 12800-es portra az SSH-munkamenetben. A `127.0.0.1:12800` címet használja a `remoteLogin()` metódusban. Ez a csomópont operacionalizálási keresztül jelentkezik be a peremhálózat csomópontjaira.

```r
library(mrsdeploy)

remoteLogin(
    deployr_endpoint = "http://127.0.0.1:12800",
    username = "admin",
    password = "xxxxxxx"
)
```

## <a name="scale-operationalized-compute-nodes-on-hdinsight-worker-nodes"></a>Működő számítási csomópontok méretezése a HDInsight Worker csomópontjain

A számítási csomópontok méretezéséhez először szerelje le a munkavégző csomópontokat, majd konfigurálja a számítási csomópontokat a leszerelt feldolgozó csomópontokon.

### <a name="step-1-decommission-the-worker-nodes"></a>1. lépés: a munkavégző csomópontok leszerelése

A ML-szolgáltatások fürtjét nem [Apache HADOOP fonal](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html)felügyeli. Ha a feldolgozó csomópontokat nem szereli le, a fonal erőforrás-kezelője nem a várt módon működik, mert nem ismeri a kiszolgáló által végrehajtott erőforrásokat. Ennek a helyzetnek az elkerülésére javasoljuk a feldolgozó csomópontok leszerelését a számítási csomópontok horizontális felskálázása előtt.

A munkavégző csomópontok leszereléséhez kövesse az alábbi lépéseket:

1. Jelentkezzen be a fürt Ambari-konzolján, és kattintson a **gazdagépek** fülre.

1. Válassza ki a munkavégző csomópontokat (leszerelni kívánt).

1. Kattintson a **műveletek**  >  **kiválasztott gazdagépek**  >  **gazdagépek**  >  **karbantartási mód bekapcsolása** elemre. A következő képen például a wn3 és a wn4 pontokat választottuk ki leszerelésre.  

   :::image type="content" source="./media/r-server-operationalize/get-started-operationalization.png" alt-text="Az Apache Ambari bekapcsolja a karbantartási módot" border="true":::  

* Válassza a **műveletek**  >  **kiválasztott gazdagépek**  >  **adatcsomópontok** > kattintson a **leszerelés** elemre.
* Válassza a **műveletek**  >  **kiválasztott gazdagépek**  >  **csomópontkezelők** > kattintson a **leszerelés** elemre.
* Válassza a **műveletek**  >  **kiválasztott gazdagépek**  >  **adatcsomópontok** > kattintson a **Leállítás** gombra.
* Válassza a **műveletek**  >  **kiválasztott gazdagépek**  >  **csomópontkezelők** > kattintson a **Leállítás** gombra.
* Válassza   >  a **kijelölt gazdagépek** gazdagépek  >   > kattintson **az összes összetevő leállítása** elemre.
* Szüntesse meg a feldolgozó csomópontok kijelölését, és jelölje ki az átjárócsomópontokat.
* Válassza   >  a **kijelölt gazdagépek** > "**gazdagépek**  >  **újraindítása az összes összetevőt**.

### <a name="step-2-configure-compute-nodes-on-each-decommissioned-worker-nodes"></a>2. lépés: a számítási csomópontok konfigurálása az összes leszerelt feldolgozó csomópont (ok) számára

1. Jelentkezzen be SSH-n keresztül minden egyes leszerelt feldolgozó csomópontba.

1. Futtassa a felügyeleti segédprogramot a megfelelő DLL-fájl használatával a ML Services-fürthöz. ML Server 9,1 esetében futtassa a következőt:

    ```bash
    dotnet /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll
    ```

1. Adja meg az **1** értéket, és válassza **a operacionalizálási ml Server konfigurálása** lehetőséget.

1. Adja meg a **C** értéket a lehetőség kiválasztásához `C. Compute node` . Ez konfigurálja a számítási csomópontot a feldolgozó csomóponton.

1. Lépjen ki az admin segédprogramból.

### <a name="step-3-add-compute-nodes-details-on-web-node"></a>3. lépés: számítási csomópontok részleteinek hozzáadása a webes csomóponton

Miután az összes leszerelt feldolgozó csomópont konfigurálva van a számítási csomópont futtatására, térjen vissza a peremhálózati csomópontra, és adja hozzá a leszerelt feldolgozó csomópontok IP-címeit a ML Server webcsomópontjának konfigurációjában:

1. Jelentkezzen be SSH-n keresztül az élcsomópontba.

1. Futtassa az `vi /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Server.WebAPI/appsettings.json` parancsot.

1. Keresse meg az "URI-k" szakaszt, és adja hozzá a feldolgozói csomópont IP-címét és portjának adatait.

    ```json
    "Uris": {
        "Description": "Update 'Values' section to point to your backend machines. Using HTTPS is highly recommended",
        "Values": [
            "http://localhost:12805", "http://[worker-node1-ip]:12805", &quot;http://[workder-node2-ip]:12805"
        ]
    }
    ```

## <a name="next-steps"></a>Következő lépések

* [Az ML-szolgáltatások fürtjének kezelése a HDInsighton](r-server-hdinsight-manage.md)
* [Számítási környezeti beállítások az ML-szolgáltatások HDInsighton belüli fürtjében](r-server-compute-contexts.md)
* [Azure Storage-lehetőségek az ML-szolgáltatások HDInsighton belüli fürtjéhez](r-server-storage.md)