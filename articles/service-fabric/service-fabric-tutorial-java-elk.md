---
title: Alkalmazások monitorozása a Service Fabricben az ELK-val az Azure-ban | Microsoft Docs
description: Ez az oktatóanyag azt mutatja be, hogyan állíthatja be az ELK-t és monitorozhatja Service Fabric-alkalmazásait.
services: service-fabric
documentationcenter: java
author: suhuruli
manager: msfussell
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/26/2018
ms.author: suhuruli
ms.custom: mvc
ms.openlocfilehash: 689207339db0250d42fc64c33f43c42c18317d41
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "61388727"
---
# <a name="tutorial-monitor-your-service-fabric-applications-using-elk"></a>Oktatóanyag: Az elk-val a Service Fabric-alkalmazások figyelése

Ez az oktatóanyag egy sorozat negyedik része. Azt mutatja be, hogyan használhatja az ELK-t (Elasticsearch, Logstash és Kibana) az Azure-ban futó Service Fabric-alkalmazások monitorozására.

A sorozat negyedik részében az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * ELK-kiszolgáló beállítása az Azure-ban
> * A Logstash konfigurálása, hogy naplókat fogadjon az Event Hubsról
> * Platform- és alkalmazásnaplók vizualizációja a Kibanában

Ebben az oktatóanyag-sorozatban az alábbiakkal ismerkedhet meg:
> [!div class="checklist"]
> * [Java Service Fabric Reliable Services-alkalmazás létrehozása](service-fabric-tutorial-create-java-app.md)
> * [Az alkalmazás üzembe helyezése és hibakeresése egy helyi fürtön](service-fabric-tutorial-debug-log-local-cluster.md)
> * [Alkalmazás üzembe helyezése egy Azure-fürtön](service-fabric-tutorial-java-deploy-azure.md)
> * Monitorozás és diagnosztika beállítása az alkalmazáshoz
> * [CI/CD beállítása](service-fabric-tutorial-java-jenkins.md)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdése előtt:

* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Állítsa be úgy az alkalmazást, hogy a [második részben](service-fabric-tutorial-debug-log-local-cluster.md) megadott helyen hozzon létre naplókat.
* Teljesítse a [harmadik részt](service-fabric-tutorial-java-deploy-azure.md), hogy legyen egy működő Service Fabric-fürtje, amely naplókat küld az Event Hubsnak.
* Szükség van arra az Event Hubs-szabályzatra, amely figyelési engedéllyel rendelkezik, valamint a hozzá tartozó, a harmadik részből származó elsődleges kulcsra.

## <a name="download-the-voting-sample-application"></a>A mintául szolgáló szavazóalkalmazás letöltése

Ha nem hozta létre a mintául szolgáló szavazóalkalmazást [az oktatóanyag-sorozat első részében](service-fabric-tutorial-create-java-app.md), akkor le is töltheti. Egy parancssori ablakban futtassa a következő parancsot a mintaalkalmazás-adattár helyi számítógépre történő klónozásához.

```bash
git clone https://github.com/Azure-Samples/service-fabric-java-quickstart
```

## <a name="create-an-elk-server-in-azure"></a>ELK-kiszolgáló létrehozása az Azure-ban

Az oktatóanyag lépéseit egy előre konfigurált ELK-környezetben is végrehajthatja. Ha már rendelkezik ilyennel, átugorhatja **A Logstash beállítása** szakaszt. Amennyiben nem rendelkezik ilyen környezettel, a következő módon hozhatja létre az Azure-ban.

1. Hozza létre a [Bitnami](https://ms.portal.azure.com/#create/bitnami.elk4-6) által hitelesített ELK-t az Azure-ban. Az oktatóanyag elvégzéséhez a létrehozott kiszolgálónak semmilyen konkrét specifikációnak nem kell megfelelnie.

2. Keresse meg az erőforrását az Azure Portalon, és lépjen be **Támogatás + Hibaelhárítás** szakasz **Rendszerindítási diagnosztika** lapjára. Ezután kattintson a **Soros napló** fülre.

    ![Rendszerindítási diagnosztika](./media/service-fabric-tutorial-java-elk/bootdiagnostics.png)
3. Keresse meg a Kibana-példány hozzáféréséhez szükséges jelszót a naplókban. Ez a következő kódrészletre hasonlít:

    ```bash
    [   25.932766] bitnami[1496]: #########################################################################
    [   25.948656] bitnami[1496]: #                                                                       #
    [   25.962448] bitnami[1496]: #        Setting Bitnami application password to '[PASSWORD]'           #
    [   25.978137] bitnami[1496]: #        (the default application username is 'user')                   #
    [   26.004770] bitnami[1496]: #                                                                       #
    [   26.029413] bitnami[1496]: #########################################################################
    ```

4. Az Azure Portalon, a kiszolgáló Áttekintés oldalán kattintson a csatlakozási gombra.

    ![Virtuálisgép-kapcsolat](./media/service-fabric-tutorial-java-elk/vmconnection.png)

5. Csatlakozzon SSH-kapcsolattal az ELK-rendszerképet üzemeltető kiszolgálóra a következő paranccsal.

    ```bash
    ssh [USERNAME]@[CONNECTION-IP-OF-SERVER]

    Example: ssh testaccount@104.40.63.157
    ```

## <a name="set-up-elk"></a>Az ELK beállítása

1. Első lépésként töltse be az ELK-környezetet.

    ```bash
    sudo /opt/bitnami/use_elk
    ```

2. Ha egy meglévő környezetet használ, futtassa a következő parancsot a Logstash leállításához.

    ```bash
    sudo /opt/bitnami/ctlscript.sh stop logstash
    ```

3. Futtassa a következő parancsot az Event Hubs Logstash beépülő moduljának telepítéséhez.

    ```bash
    logstash-plugin install logstash-input-azureeventhub
    ```

4. Hozzon létre vagy módosítsa a meglévő Logstash konfigurációs fájlt a következő tartalommal: A fájlt hoz létre, hogy rendelkezik-e létrehozandó ```/opt/bitnami/logstash/conf/access-log.conf``` ELK Bitnami-rendszerkép használata az Azure-ban.

    ```json
    input
    {
        azureeventhub
        {
            key => "[RECEIVER-POLICY-KEY-FOR-EVENT-HUB]"
            username => "[RECEIVER-POLICY-NAME]"
            namespace => "[EVENTHUB-NAMESPACENAME]"
            eventhub => "[EVENTHUB-NAME]"
            partitions => 4
        }
    }

    output {
         elasticsearch {
             hosts => [ "127.0.0.1:9200" ]
         }
     }
    ```

5. A konfiguráció ellenőrzéséhez futtassa a következő parancsot:

    ```bash
    /opt/bitnami/logstash/bin/logstash -f /opt/bitnami/logstash/conf/ --config.test_and_exit
    ```

6. Indítsa el a Logstash szolgáltatást.

    ```bash
    sudo /opt/bitnami/ctlscript.sh start logstash
    ```

7. Győződjön meg arról, hogy az Elasticsearch fogad adatokat.

    ```bash
    curl 'localhost:9200/_cat/indices?v'
    ```

8. A Kibana irányítópultját a következő hozzáférési **http:\//SERVER-IP** és a Kibana felhasználónevét és jelszavát adja meg. Amennyiben az Azure-ban található ELK-rendszerképet használta, az alapértelmezett felhasználónév „user”, a jelszó pedig az, amit a **Rendszerindítási diagnosztika** futtatásakor kapott.

    ![Kibana](./media/service-fabric-tutorial-java-elk/kibana.png)

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * ELK-kiszolgáló üzembe helyezése az Azure-ban
> * A kiszolgáló konfigurálása, hogy az diagnosztikai adatokat kapjon a Service Fabric-fürttől

Folytassa a következő oktatóanyaggal:
> [!div class="nextstepaction"]
> [CI/CD beállítása a Jenkins használatával](service-fabric-tutorial-java-jenkins.md)
