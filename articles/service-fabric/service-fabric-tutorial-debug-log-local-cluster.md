---
title: Java-alkalmazás hibakeresése helyi Service Fabric fürtön
description: Ez az oktatóanyag azt ismerteti, hogyan végezhet hibakeresést és kérhet le naplókat egy helyi fürtön futó Service Fabric Java-alkalmazásból.
ms.topic: tutorial
ms.date: 02/26/2018
ms.custom: mvc, devx-track-java
ms.openlocfilehash: 801702d43bae6d925fa4f7fbc37cf44bf585fa6d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91531990"
---
# <a name="tutorial-debug-a-java-application-deployed-on-a-local-service-fabric-cluster"></a>Oktatóanyag: Helyi Service Fabric-fürtön üzembe helyezett Java-alkalmazás hibakeresése

Ez az oktatóanyag egy sorozat második része. Megtudhatja, hogyan csatolhat egy távoli hibakeresőt az Eclipse használatával a Service Fabric-alkalmazáshoz. Azzal is megismerkedhet, hogyan irányíthat át naplókat a futó alkalmazásokból egy olyan helyre, amelyhez a fejlesztő kényelmesen hozzáfér.

Ebben az oktatóanyag-sorozatban az alábbiakkal ismerkedhet meg:
> [!div class="checklist"]
> * [Service Fabric Reliable Services Java-alkalmazás létrehozása](service-fabric-tutorial-create-java-app.md)
> * Az alkalmazás üzembe helyezése és hibakeresése egy helyi fürtön
> * [Alkalmazás üzembe helyezése egy Azure-fürtön](service-fabric-tutorial-java-deploy-azure.md)
> * [Figyelés és diagnosztika beállítása az alkalmazáshoz](service-fabric-tutorial-java-elk.md)
> * [CI/CD beállítása](service-fabric-tutorial-java-jenkins.md)


A sorozat második részében az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Hibakeresés Java-alkalmazásokban az Eclipse használatával
> * Naplók átirányítása egy konfigurálható helyre


## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdése előtt:

* Állítsa be a fejlesztési környezetet [Mac](service-fabric-get-started-mac.md) vagy [Linux](service-fabric-get-started-linux.md) rendszerhez. Az utasításokat követve telepítse a következőket: Eclipse beépülő modul, Gradle, Service Fabric SDK és Service Fabric parancssori felület (sfctl).

## <a name="download-the-voting-sample-application"></a>A mintául szolgáló szavazóalkalmazás letöltése

Ha nem a [jelen oktatóanyag-sorozat első részében](service-fabric-tutorial-create-java-app.md)a szavazási minta alkalmazást használta, letöltheti azt. Egy parancssori ablakban futtassa a következő parancsot a mintaalkalmazás-adattár helyi számítógépre történő klónozásához.

```bash
git clone https://github.com/Azure-Samples/service-fabric-java-quickstart
```

Az alkalmazás [létrehozása és üzembe helyezése](service-fabric-tutorial-create-java-app.md#deploy-application-to-local-cluster) a helyi fejlesztési fürtön.

## <a name="debug-java-application-using-eclipse"></a>Hibakeresés Java-alkalmazásokban az Eclipse használatával

1. Nyissa meg az Eclipse IDE-t a számítógépen, majd kattintson a **File (Fájl) -> Import... (Importálás...)** elemre.

2. Az előugró ablakban válassza a **General (Általános) -> Existing Projects into Workspace (Meglévő projekteket a munkaterületre)** lehetőséget, majd kattintson a Next (Tovább) gombra.

3. Az Import Projects (Projektek importálása) ablakban válassza a **Select root directory** (Gyökérkönyvtár kiválasztása) lehetőséget, majd válassza a **Voting** könyvtárat. Ha követte az első oktatóanyag-sorozatot, a **Voting** könyvtár az **Eclipse-workspace** könyvtárban található.

4. Frissítse azon szolgáltatás entryPoint.sh fájlját, amelyben hibakeresést szeretne végezni, hogy távoli hibakeresési paraméterekkel indítsa el a Java-folyamatot. Ehhez az oktatóanyaghoz az állapot nélküli előtér használatos: *szavazás/VotingApplication/VotingWebPkg/Code/BelépésiPont. sh*. Ebben a példában a 8001-es port hibakeresésre van beállítva.

    ```bash
    java -Xdebug -Xrunjdwp:transport=dt_socket,address=8001,server=y,suspend=n -Djava.library.path=$LD_LIBRARY_PATH -jar VotingWeb.jar
    ```

5. Az alkalmazásjegyzék frissítéséhez állítsa egyre azon szolgáltatás példányszámát vagy replikaszámát, amelyen hibakeresést végez. Ezzel a beállítással elkerülheti, hogy ütközések alakuljanak ki a hibakereséshez használt portnál. Állapotmentes szolgáltatások esetén például állítsa be az ``InstanceCount="1"`` értéket, állapotalapú szolgáltatások esetén pedig állítsa be a cél és a minimális replikakészlet méretét 1-re a következőképpen: ``TargetReplicaSetSize="1" MinReplicaSetSize="1"``.

6. Az Eclipse IDE-ben válassza a **Run -> Debug Configurations -> Remote Java Application** (Futtatás -> Konfigurációk hibakeresése -> Távoli Java-alkalmazás) elemet, kattintson a **New** (Új) gombra, állítsa be a tulajdonságokat az alábbiak szerint, és kattintson az **Apply** (Alkalmaz) gombra.

    ```
    Name: Voting
    Project: Voting
    Connection Type: Standard
    Host: localhost
    Port: 8001
    ```

7. Tegyen egy töréspontot a *Voting/VotingWeb/src/statelessservice/HttpCommunicationListener.java* fájl 109. sorába.

8. A Package Explorerben kattintson a jobb gombbal a **Voting** (Szavazás) projektre, és kattintson a **Service Fabric -> Publish Application...** (Alkalmazás közzététele) elemre.

9. A **Publish Application** (Alkalmazás közzététele) ablakban válassza ki a **Local.json** elemet a legördülő listából, és kattintson a **Publish** (Közzététel) elemre.

10. Az Eclipse IDE-ben válassza a **Run -> Debug Configurations -> Remote Java Application** (Futtatás -> Konfigurációk hibakeresése -> Távoli Java-alkalmazás) elemet, kattintson a létrehozott **Voting** (Szavazás) konfigurációra, és kattintson a **Debug** (Hibakeresés) gombra.

11. Nyissa meg a webböngészőt, és lépjen a következő **helyre: 8080**. Ekkor a rendszer automatikusan megnyomja a töréspontot, és az Eclipse megadja a **hibakeresési perspektívát**.

Most ugyanezeket a lépéseket alkalmazhatja az összes Service Fabric alkalmazás hibakereséséhez az Eclipse-ben.

## <a name="redirect-application-logs-to-custom-location"></a>Alkalmazásnaplók átirányítása egyéni helyre

Az alábbi lépések bemutatják, hogyan irányíthatja át az alkalmazásnaplókat az alapértelmezett */var/log/syslog* helyről egy egyéni helyre.

1. A Service Fabric Linux-fürtökön futó alkalmazások jelenleg csak egyetlen naplófájl felszedését támogatják. Ha egy alkalmazást úgy szeretne beállítani, hogy a naplók mindig a */tmp/mysfapp0.0.log* legyenek, hozzon létre egy naplózás. properties nevű fájlt a következő helyen: *szavazó/VotingApplication/VotingWebPkg/Code/fakitermelés. properties* , és adja hozzá a következő tartalmat.

    ```
    handlers = java.util.logging.FileHandler

    java.util.logging.FileHandler.level = ALL
    java.util.logging.FileHandler.formatter = java.util.logging.SimpleFormatter

    # This value specifies your custom location.
    # You will have to ensure this path has read and write access by the process running the SF Application
    java.util.logging.FileHandler.pattern = /tmp/mysfapp0.0.log
    ```

2. Adja meg a következő paramétert a *Voting/VotingApplication/VotingWebPkg/Code/entryPoint.sh* helyen a Java futtatási parancsához:

    ```bash
    -Djava.util.logging.config.file=logging.properties
    ```

    Az alábbi példa egy minta-végrehajtást mutat be a hibakeresővel, amely az előző szakaszban leírt végrehajtáshoz hasonló.

    ```bash
    java -Xdebug -Xrunjdwp:transport=dt_socket,address=8001,server=y,suspend=n -Djava.library.path=$LD_LIBRARY_PATH -Djava.util.logging.config.file=logging.properties -jar VotingWeb.jar
    ```

Megismerkedett vele, hogyan végezhet hibakeresést, és hogyan érheti el az alkalmazásnaplókat a Service Fabric Java-alkalmazások fejlesztésekor.

## <a name="next-steps"></a>Következő lépések

Az oktatóanyag jelen részében megismerkedhetett a következőkkel:

> [!div class="checklist"]
> * Hibakeresés Java-alkalmazásokban az Eclipse használatával
> * Naplók átirányítása egy konfigurálható helyre

Folytassa a következő oktatóanyaggal:
> [!div class="nextstepaction"]
> [Az alkalmazás üzembe helyezése az Azure-ban](service-fabric-tutorial-java-deploy-azure.md)