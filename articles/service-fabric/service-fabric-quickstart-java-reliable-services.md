---
title: 'Gyors útmutató: Java-alkalmazás létrehozása az Azure-ban Service Fabric'
description: Ez a rövid útmutató azt ismerteti, hogyan lehet Java-alkalmazást létrehozni az Azure-ban a Service Fabric Reliable Services mintaalkalmazásának használatával.
ms.topic: quickstart
ms.date: 01/29/2019
ms.custom: mvc, devcenter, seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: 2f1e5f8f73f74d4b427e574b9e6a75aaf84a4211
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91529850"
---
# <a name="quickstart--deploy-a-java-app-to-azure-service-fabric-on-linux"></a>Gyors útmutató: Java-alkalmazás üzembe helyezése az Azure Service Fabric Linuxon

Ebben a rövid útmutatóban egy Java-alkalmazást helyez üzembe az Azure Service Fabric az Eclipse IDE használatával egy linuxos fejlesztői gépen. Az útmutató elvégzése után rendelkezni fog egy Java webes kezelőfelületes szavazóalkalmazással, amely egy a fürtben található állapotalapú háttérszolgáltatásba menti a szavazati adatokat.

Az Azure Service Fabric egy elosztott rendszerplatform, amely mikroszolgáltatások és tárolók üzembe helyezésére és kezelésére szolgál.

## <a name="prerequisites"></a>Előfeltételek

- [Java-környezet](./service-fabric-get-started-linux.md#set-up-java-development) és [Yeoman](./service-fabric-get-started-linux.md#set-up-yeoman-generators-for-containers-and-guest-executables)
- [Eclipse neon (4.6) +](https://www.eclipse.org/downloads/packages/) és [Eclipse beépülő modul a Service Fabric](./service-fabric-get-started-linux.md#install-the-eclipse-plug-in-optional)
- [Service Fabric SDK és parancssori felület (CLI)](./service-fabric-get-started-linux.md#installation-methods)
- [Git](https://git-scm.com/downloads)

## <a name="download-the-sample"></a>A minta letöltése

Egy parancssori ablakban futtassa a következő parancsot a mintaalkalmazás-adattár helyi számítógépre történő klónozásához.

```bash
git clone https://github.com/Azure-Samples/service-fabric-java-quickstart.git
```

## <a name="run-the-application-locally"></a>Az alkalmazás helyi futtatása

1. A helyi fürt elindításához futtassa a következő parancsot:

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```
    A helyi fürt elindítása eltarthat egy ideig. A fürt üzembe állásának ellenőrzéséhez nyissa meg a Service Fabric Explorert a `http://localhost:19080` címen. Ha öt kifogástalan csomópontot lát, a fürt megfelelően üzemel.

    ![Az Azure Service Fabric Explorer kifogástalan állapotú csomópontokat jelenít meg](./media/service-fabric-quickstart-java/service-fabric-explorer-healthy-nodes.png)

2. Nyissa meg az Eclipse környezetet.
3. Válassza a **fájl**  >  **importálása**  >  **Gradle**  >  **meglévő Gradle-projekt** lehetőséget, és kövesse a varázslót.
4. Válassza ki a **könyvtárat** , és válassza ki a githubról klónozott **Service-Fabric-Java-Gyorsindítás** mappában található **szavazási** könyvtárat. Válassza a **Befejezés** gombot.

    ![Gradle-projekt importálása az Eclipse-be](./media/service-fabric-quickstart-java/eclipse-import-gradle-project.png)

5. Ezzel létrehozta a `Voting` projektet az Eclipse-hez készült Package Explorerben.
6. Kattintson a jobb gombbal a projektre, és válassza az **alkalmazás közzététele** a **Service Fabric** legördülő menüben. Válassza a **célprofilnak publishprofiles/Local.js** elemet a cél profil területen, majd válassza a **Közzététel** lehetőséget.

    ![Azure Service Fabric helyi JSON közzététele](./media/service-fabric-quickstart-java/service-fabric-publish-local-json.png)

7. Nyissa meg a kedvenc webböngészőjét, és nyissa meg az alkalmazást a `http://localhost:8080` címen.

    ![Azure Service Fabric helyi gazdagép](./media/service-fabric-quickstart-java/service-fabric-local-host.png)

Most hozzáadhat szavazási lehetőségeket az alkalmazáshoz, és megkezdheti a szavazatok gyűjtését. Az alkalmazás különálló adatbázis nélkül futtatja és tárolja az összes adatot a Service Fabric-fürtben.

![Azure Service Fabric szavazó minta](./media/service-fabric-quickstart-java/service-fabric-voting-sample.png)

## <a name="scale-applications-and-services-in-a-cluster"></a>Alkalmazások és szolgáltatások méretezése a fürtökben

A szolgáltatások skálázhatók egy adott fürtben, hogy kövessék a szolgáltatások terhelésének változásait. A szolgáltatások méretezése a fürtben futó példányok számának módosításával történik. A szolgáltatások méretezésének számos módja van. Használhat például parancsfájlokat vagy parancsokat Service Fabric parancssori felületről ( `sfctl` ). A következő lépések során a Service Fabric Explorert használjuk.

Service Fabric Explorer az összes Service Fabric-fürtön fut, és a böngészőből érhető el, ha a fürt HTTP-kezelési portjára (19080) keres rá. Például: `http://localhost:19080`.

A webes előtér-szolgáltatás skálázásához tegye a következőket:

1. Nyissa meg Service Fabric Explorer a fürtben. Például: `https://localhost:19080`.
2. Válassza a **háló:/szavazó/VotingWeb** csomópont melletti három pontot (**..**.) a TreeView vezérlőben, és válassza a **szolgáltatás méretezése** lehetőséget.

    ![Szolgáltatás méretezése az Azure Service Fabricban](./media/service-fabric-quickstart-java/service-fabric-scale-service.png)

    Most már méretezheti a webes előtér-szolgáltatás példányainak számát.

3. Módosítsa a számot **2** értékre, és válassza a **szolgáltatás méretezése** lehetőséget.
4. Válassza ki a **háló:/szavazás/VotingWeb** csomópontot a fanézetben, és bontsa ki a partíciós csomópontot (amelyet egy GUID képvisel).

    ![Skálázható szolgáltatás az Azure-ban Service Fabric](./media/service-fabric-quickstart-java/service-fabric-explorer-service-scaled.png)

    Ekkor láthatja, hogy a szolgáltatás két példányban jelenik meg, a fanézetben pedig megtekintheti, hogy a példányok melyik csomópontokon futnak.

Ezzel az egyszerű felügyeleti eljárással megduplázta az előtér-szolgáltatás számára a felhasználói terhelések feldolgozásához rendelkezésre álló erőforrások mennyiségét. Fontos tudni, hogy nincs szükség több példányra ahhoz, hogy a szolgáltatás megbízhatóan fusson. Ha egy szolgáltatás meghibásodik, a Service Fabric gondoskodik róla, hogy egy új szolgáltatáspéldány elinduljon a fürtben.

## <a name="next-steps"></a>Következő lépések

Ennek a rövid útmutatónak a segítségével megtanulta a következőket:

* Az Eclipse használata a Service Fabricben üzemeltetett Java-alkalmazásokhoz
* Java-alkalmazások központi telepítése a helyi fürtre
* Az alkalmazás horizontális felskálázása több csomópontra

Ha bővebb információra van szüksége a Java-alkalmazások Service Fabricben való használatával kapcsolatban, lépjen tovább a Java-alkalmazásokról szóló oktatóanyagra.

> [!div class="nextstepaction"]
> [Java-alkalmazás üzembe helyezése](./service-fabric-tutorial-create-java-app.md)
