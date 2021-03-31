---
title: 'Gyors útmutató: Spring boot-alkalmazás létrehozása az Azure Service Fabric'
description: Ez a rövid útmutató azt ismerteti, hogyan lehet Spring Boot-alkalmazást telepíteni az Azure Service Fabricre egy Spring Boot-mintaalkalmazás használatával.
ms.topic: quickstart
ms.date: 01/29/2019
ms.custom: mvc, devcenter, seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: 84ce5920af95113801f468e3149421f3b9bd8901
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91530000"
---
# <a name="quickstart-deploy-a-java-spring-boot-app-on-azure-service-fabric"></a>Gyors útmutató: Java Spring boot-alkalmazás üzembe helyezése az Azure-ban Service Fabric

Ebben a rövid útmutatóban egy Java Spring boot-alkalmazást helyez üzembe az Azure Service Fabricba a Linux vagy MacOS rendszerű, ismerős parancssori eszközök használatával. Az Azure Service Fabric egy elosztott rendszerplatform, amely mikroszolgáltatások és tárolók üzembe helyezésére és kezelésére szolgál. 

## <a name="prerequisites"></a>Előfeltételek

#### <a name="linux"></a>[Linux](#tab/linux)

- [Java-környezet](./service-fabric-get-started-linux.md#set-up-java-development) és [Yeoman](./service-fabric-get-started-linux.md#set-up-yeoman-generators-for-containers-and-guest-executables)
- [Service Fabric SDK & Service Fabric parancssori felület (CLI)](./service-fabric-get-started-linux.md#installation-methods)
- [Git](https://git-scm.com/downloads)

#### <a name="macos"></a>[MacOS](#tab/macos)

- [Java-környezet és Yeoman](./service-fabric-get-started-mac.md#create-your-application-on-your-mac-by-using-yeoman)
- [Service Fabric SDK & Service Fabric parancssori felület (CLI)](./service-fabric-cli.md#cli-mac)
- [Git](https://git-scm.com/downloads)

--- 

## <a name="download-the-sample"></a>A minta letöltése

Egy terminál ablakban futtassa a következő parancsot a Spring boot [első lépések](https://github.com/spring-guides/gs-spring-boot) minta alkalmazás helyi gépre történő klónozásához.

```bash
git clone https://github.com/spring-guides/gs-spring-boot.git
```

## <a name="build-the-spring-boot-application"></a>A Spring Boot-alkalmazás létrehozása 
A *GS-Spring-boot/Complete* könyvtáron belül futtassa az alábbi parancsot az alkalmazás létrehozásához. 

```bash
./gradlew build
``` 

## <a name="package-the-spring-boot-application"></a>A Spring Boot-alkalmazás becsomagolása 
1. A klónban a *GS-Spring-boot* könyvtárban futtassa a `yo azuresfguest` parancsot. 

1. Az egyes sorokban adja meg a következő adatokat.

    ![Spring boot Yeoman bejegyzései](./media/service-fabric-quickstart-java-spring-boot/yeoman-entries-spring-boot.png)

1. A *SpringServiceFabric/SpringServiceFabric/SpringGettingStartedPkg/Code* mappában hozzon létre egy *entryPoint.sh* nevű fájlt. Adja hozzá a következő kódot a *entryPoint.sh* fájlhoz. 

    ```bash
    #!/bin/bash
    BASEDIR=$(dirname $0)
    cd $BASEDIR
    java -jar *spring-boot*.jar
    ```

1. Adja hozzá a **végpontok** erőforrását a *GS-Spring-boot/SpringServiceFabric/SpringServiceFabric/SpringGettingStartedPkg/ServiceManifest.xml* fájlban

    ```xml 
        <Resources>
          <Endpoints>
            <Endpoint Name="WebEndpoint" Protocol="http" Port="8080" />
          </Endpoints>
       </Resources>
    ```

    A *ServiceManifest.xml* fájlnak most így kell kinéznie: 

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <ServiceManifest Name="SpringGettingStartedPkg" Version="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" >

       <ServiceTypes>
          <StatelessServiceType ServiceTypeName="SpringGettingStartedType" UseImplicitHost="true">
       </StatelessServiceType>
       </ServiceTypes>

       <CodePackage Name="code" Version="1.0.0">
          <EntryPoint>
             <ExeHost>
                <Program>entryPoint.sh</Program>
                <Arguments></Arguments>
                <WorkingFolder>CodePackage</WorkingFolder>
             </ExeHost>
          </EntryPoint>
       </CodePackage>
        <Resources>
          <Endpoints>
            <Endpoint Name="WebEndpoint" Protocol="http" Port="8080" />
          </Endpoints>
       </Resources>
     </ServiceManifest>
    ```

Mostanra más létrehozta Spring Boot mintájához tartozó Service Fabric-alkalmazást, amelyet a Service Fabricre telepíthet.

## <a name="run-the-application-locally"></a>Az alkalmazás helyi futtatása

1. A helyi fürt Ubuntu rendszerű gépeken való elindításához futtassa a következő parancsot:

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```

    Mac rendszerű gép esetében indítsa el a helyi fürtöt a Docker-rendszerképből (ehhez az [előfeltételek](./service-fabric-get-started-mac.md#create-a-local-container-and-set-up-service-fabric) alapján be kell állítania a helyi fürtöt Mac rendszerhez). 

    ```bash
    docker run --name sftestcluster -d -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 -p 8080:8080 mysfcluster
    ```

    A helyi fürt elindítása eltarthat egy ideig. A fürt üzembe állásának ellenőrzéséhez nyissa meg a Service Fabric Explorert a `http://localhost:19080` címen. Ha öt kifogástalan csomópontot lát, a fürt megfelelően üzemel. 
    
    ![Service Fabric Explorer a kifogástalan csomópontokat jeleníti meg](./media/service-fabric-quickstart-java-spring-boot/service-fabric-explorer-healthy-nodes.png)

1. Nyissa meg a *GS-Spring-boot/SpringServiceFabric* mappát.
1. A helyi fürthöz való csatlakozáshoz futtassa a következő parancsot.

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```
1. Futtassa a *install.sh* szkriptet.

    ```bash
    ./install.sh
    ```

1. Nyissa meg a kedvenc webböngészőjét, és nyissa meg az alkalmazást a `http://localhost:8080` címen.

    ![Spring boot Service Fabric minta](./media/service-fabric-quickstart-java-spring-boot/spring-boot-service-fabric-sample.png)

Most már elérhető a Service Fabric-fürtre telepített Spring Boot-alkalmazás.

További információ: Spring boot [első lépések](https://spring.io/guides/gs/spring-boot/) minta a Spring webhelyén.

## <a name="scale-applications-and-services-in-a-cluster"></a>Alkalmazások és szolgáltatások méretezése a fürtökben

A szolgáltatások skálázhatók egy adott fürtben, hogy kövessék a szolgáltatások terhelésének változásait. A szolgáltatások méretezése a fürtben futó példányok számának módosításával történik. A szolgáltatásokat többféleképpen is skálázhatja, használhat például szkripteket vagy a Service Fabric parancssori felület (sfctl) parancsait. A következő lépések során a Service Fabric Explorert használjuk.

A Service Fabric Explorer az összes Service Fabric-fürtben fut. Az eléréséhez egy böngészőben navigáljon az adott fürt HTTP-kezelési portjára (19080), például: `http://localhost:19080`.

A webes előtér-szolgáltatás skálázásához tegye a következőket:

1. Nyissa meg a Service Fabric Explorert a fürtben – például: `http://localhost:19080`.
1. Válassza a fanézetben a **Fabric:/SpringServiceFabric/SpringGettingStarted** csomópont melletti három pontot (**..**.), és válassza a **méretezési szolgáltatás** elemet.

    ![Service Fabric Explorer méretezési szolgáltatás mintája](./media/service-fabric-quickstart-java-spring-boot/service-fabric-explorer-scale-sample.png)

    Most már méretezheti a szolgáltatás példányainak számát.

1. Módosítsa a számot **3** értékre, majd válassza a **méretezési szolgáltatás** elemet.

    A szolgáltatás parancssorral történő skálázása a következőképpen is végrehajtható.

    ```bash
    # Connect to your local cluster
    sfctl cluster select --endpoint https://<ConnectionIPOrURL>:19080 --pem <path_to_certificate> --no-verify

    # Run Bash command to scale instance count for your service
    sfctl service update --service-id 'SpringServiceFabric~SpringGettingStarted' --instance-count 3 --stateless 
    ``` 

1. Válassza ki a **Fabric:/SpringServiceFabric/SpringGettingStarted** csomópontot a fanézetben, és bontsa ki a partíciós csomópontot (egy GUID jelöli).

    ![Service Fabric Explorer skálázási szolgáltatás kész](./media/service-fabric-quickstart-java-spring-boot/service-fabric-explorer-partition-node.png)

    A szolgáltatás három példányban jelenik meg, a fanézetben pedig látható, hogy a példányok melyik csomópontokon futnak.

Ezzel az egyszerű felügyeleti eljárással megduplázta az előtér-szolgáltatás számára a felhasználói terhelések feldolgozásához rendelkezésre álló erőforrások mennyiségét. Fontos tudni, hogy nincs szükség több példányra ahhoz, hogy a szolgáltatás megbízhatóan fusson. Ha egy szolgáltatás meghibásodik, a Service Fabric gondoskodik róla, hogy egy új szolgáltatáspéldány elinduljon a fürtben.

## <a name="fail-over-services-in-a-cluster"></a>Feladatátvételi szolgáltatások a fürtben

A szolgáltatás feladatátvételének bemutatásához a Service Fabric Explorerben szimuláljuk egy csomópont újraindítását. Győződjön meg arról, hogy a szolgáltatásnak kizárólag egy példánya fut.

1. Nyissa meg a Service Fabric Explorert a fürtben – például: `http://localhost:19080`.
1. Válassza a szolgáltatás példányát futtató csomópont melletti három pontot (**..**.), majd indítsa újra a csomópontot.

    ![Service Fabric Explorer csomópont újraindítása](./media/service-fabric-quickstart-java-spring-boot/service=fabric-explorer-restart=node.png)
1. A szolgáltatás példánya egy új csomópontra kerül át anélkül, hogy az alkalmazás leállna.

    ![Service Fabric Explorer a csomópont újraindítása sikerült](./media/service-fabric-quickstart-java-spring-boot/service-fabric-explorer-service-moved.png)

## <a name="next-steps"></a>Következő lépések

Ennek a rövid útmutatónak a segítségével megtanulta a következőket:

* Spring Boot-alkalmazás telepítése Service Fabricre
* Az alkalmazás központi telepítése a helyi fürtre
* Az alkalmazás horizontális felskálázása több csomópontra
* Feladatátvétel elvégzése a szolgáltatáson a rendelkezésre állás korlátozása nélkül

Ha bővebb információra van szüksége a Java-alkalmazások Service Fabricben való használatával kapcsolatban, lépjen tovább a Java-alkalmazásokról szóló oktatóanyagra.

> [!div class="nextstepaction"]
> [Java-alkalmazás üzembe helyezése](./service-fabric-tutorial-create-java-app.md)
