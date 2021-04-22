---
title: A fejlesztői környezet beállítása macOS rendszeren
description: Telepítse a futtatókörnyezetet, az SDK-t és az eszközöket, majd hozzon létre egy helyi fejlesztési fürtöt. A beállítás befejezése után készen áll az alkalmazások macOS-en való építésére.
ms.topic: conceptual
ms.date: 10/16/2020
ms.custom: devx-track-js
ms.openlocfilehash: 6fe551f8371322af8d955b5233e6d9d05741f3d9
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107868122"
---
# <a name="set-up-your-development-environment-on-mac-os-x"></a>A fejlesztési környezet beállítása Mac OS X-en
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md)
> * [Linux](service-fabric-get-started-linux.md)
> * [Mac OS X](service-fabric-get-started-mac.md)

A Mac OS X használatával Linux-fürtökön futó Azure Service Fabric-alkalmazásokat hozhat létre. Ez a cikk bemutatja, hogyan állíthatja be Mac gépét a fejlesztéshez.

## <a name="prerequisites"></a>Előfeltételek
Az Azure Service Fabric nem fut natív módon a Mac OS X-en. A helyi Service Fabric-fürt futtatásához egy előre konfigurált Docker-tároló rendszerképet biztosítunk. A kezdés előtt a következőkre lesz szüksége:

* Legalább 4 GB RAM.
* A [Docker](https://www.docker.com/) legújabb verziója.

>[!TIP]
>
>A Docker Mac gépen való telepítéséhez kövesse a [Docker dokumentációjában](https://docs.docker.com/docker-for-mac/install/#what-to-know-before-you-install) ismertetett lépéseket. A telepítés után [ellenőrizze meg a telepítést](https://docs.docker.com/docker-for-mac/#check-versions-of-docker-engine-compose-and-machine).
>

## <a name="create-a-local-container-and-set-up-service-fabric"></a>Helyi tároló létrehozása és a Service Fabric beállítása
Egy helyi Docker-tároló beállításához és egy Service Fabric-fürt rajta való futtatásához hajtsa végre a következő lépéseket:

1. Frissítse a Docker-démon konfigurációját a gazdagépen a következő beállításokkal, és indítsa újra a Docker-démont: 

    ```json
    {
        "ipv6": true,
        "fixed-cidr-v6": "fd00::/64"
    }
    ```
    Ezeket a beállításokat közvetlenül a Docker telepítési útvonalán található daemon.json fájlban frissítheti. A démon konfigurációs beállításait közvetlenül módosíthatja a Dockerben. Válassza a **Docker ikonját**, majd válassza a **Preferences (Beállítások)** > **Daemon (Démon)** > **Advanced (Speciális)** lehetőséget.
    
    >[!NOTE]
    >
    >Ajánlott közvetlenül a Dockerben módosítani a démont, mert a daemon.jshelye gépenként eltérő lehet. Például: ~/Library/Containers/com.docker.docker/Data/database/com.docker.driver.amd64-linux/etc/docker/daemon.json.
    >

    >[!TIP]
    >Javasoljuk, hogy növelje a Docker számára lefoglalt erőforrásokat nagy méretű alkalmazások tesztelése esetén. Ehhez válassza a **Docker ikont**, majd a **Speciális** lehetőséget a magok és a memória számának módosításához.

2. Indítsa el a fürtöt.<br/>
    <b>Ubuntu 18.04 LTS:</b>
    ```bash
    docker run --name sftestcluster -d -v /var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mcr.microsoft.com/service-fabric/onebox:u18
    ```

    <b>Ubuntu 16.04 LTS:</b>
    ```bash
    docker run --name sftestcluster -d -v /var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mcr.microsoft.com/service-fabric/onebox:u16
    ```

    >[!TIP]
    > Alapértelmezés szerint ez a Service Fabric legújabb verziójával rendelkező rendszerképet kéri le. Az egyes változatokért látogasson el a Service Fabric [Onebox oldalára](https://hub.docker.com/_/microsoft-service-fabric-onebox) a Docker Hub.



3. Nem kötelező: Készítse el a kiterjesztett Service Fabric rendszerképet.

    Hozzon létre egy nevű fájlt egy új könyvtárban a `Dockerfile` testreszabott rendszerkép létrehozásához:

    >[!NOTE]
    >A fenti rendszerképet egy Docker-fájl használatával úgy is adaptálhatja, hogy további programokat vagy függőségeket adjon a tárolóhoz.
    >A `RUN apt-get install nodejs -y` részlet hozzáadása például lehetővé teszi a `nodejs`-alkalmazások vendég végrehajtható fájlokként való támogatását.
    ```Dockerfile
    FROM mcr.microsoft.com/service-fabric/onebox:u18
    RUN apt-get install nodejs -y
    EXPOSE 19080 19000 80 443
    WORKDIR /home/ClusterDeployer
    CMD ["./ClusterDeployer.sh"]
    ```
    
    >[!TIP]
    > Alapértelmezés szerint ez a Service Fabric legújabb verziójával rendelkező rendszerképet kéri le. Adott változatokért látogasson el a [Docker Hub](https://hub.docker.com/r/microsoft/service-fabric-onebox/) oldalára.

    Az újrahasználható rendszerképének a fájlból való felépítéséhez nyisson meg egy terminált, és a közvetlenül a `Dockerfile` `cd` fájlban található `Dockerfile` fájlhoz, majd futtassa a következőt:

    ```bash 
    docker build -t mysfcluster .
    ```
    
    >[!NOTE]
    >Ez a művelet eltarthat egy ideig, de csak egyszer kell végrehajtani.

    Most már gyorsan elindíthatja a példányok helyi Service Fabric amikor szüksége van rá a következő futtatásával:

    ```bash 
    docker run --name sftestcluster -d -v /var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mysfcluster
    ```

    >[!TIP]
    >Ha megad egy nevet a tárolópéldányhoz, átláthatóbb módon kezelheti. 
    >
    >Ha az alkalmazás bizonyos portokon figyel, a portokat további `-p` címkékkel kell meghatározni. Ha például az alkalmazás a 8080-as porton figyel, adja hozzá az alábbi `-p` címkét:
    >
    >`docker run -itd -p 19000:19000 -p 19080:19080 -p 8080:8080 --name sfonebox mcr.microsoft.com/service-fabric/onebox:u18`
    >

4. A fürt elindul. Ha fut, a következő paranccsal megtekintheti a naplókat, vagy az irányítópultra ugorva megtekintheti a fürtök állapotát: `http://localhost:19080`

    ```bash 
    docker logs sftestcluster
    ```


5. A tárolót a következő paranccsal állíthatja le és tisztíthatja meg. A következő lépésben azonban ezt a tárolót fogjuk használni.

    ```bash 
    docker rm -f sftestcluster
    ```

### <a name="known-limitations"></a>Ismert korlátozások 
 
 A Mac gép tárolójában futó helyi fürt ismert korlátozásai a következők: 
 
 * A DNS-szolgáltatás nem fut, és jelenleg nem támogatott a tárolóban. [Probléma #132](https://github.com/Microsoft/service-fabric/issues/132)
 * A tárolóalapú alkalmazások futtatásához az SF Linux-gazdagépen való futtatására van szükség. A beágyazott tárolóalkalmazások jelenleg nem támogatottak.

## <a name="set-up-the-service-fabric-cli-sfctl-on-your-mac"></a>A Service Fabric parancssori felület (sfctl) beállítása Mac gépen

A Service Fabric parancssori felület (`sfctl`) Mac gépre való telepítéséhez kövesse a [Service Fabric parancssori felület](service-fabric-cli.md#cli-mac) utasításait.
A parancssori felület parancsai támogatják a Service Fabric-entitásokkal, többek között fürtökkel, alkalmazásokkal és szolgáltatásokkal folytatott interakciók elvégzését.

1. Ha csatlakozni szeretne a fürthöz az alkalmazások üzembe helyezése előtt, futtassa az alábbi parancsot. 

```bash
sfctl cluster select --endpoint http://localhost:19080
```

## <a name="create-your-application-on-your-mac-by-using-yeoman"></a>Alkalmazás létrehozása Mac gépen a Yeoman használatával

A Service Fabric olyan szerkezetkialakító eszközöket biztosít, amelyekkel Service Fabric-alkalmazásokat hozhat létre a terminálról a Yeoman sablongenerátor használatával. Az alábbi lépések végrehajtásával ellenőrizze, hogy a Service Fabric Yeoman sablongenerátor működik-e a gépen:

1. A Node.jsk és Node Package Manager (NPM) szolgáltatásoknak telepítve kell lennie a Mac gépen. A szoftver a [HomeBrew](https://brew.sh/) használatával, az alábbi módon telepíthető:

    ```bash
    brew install node
    node -v
    npm -v
    ```
2. A [Yeoman](https://yeoman.io/) sablongenerátor telepítése a gépre az NPM-ből:

    ```bash
    npm install -g yo
    ```
3. Telepítse a használni kívánt Yeoman generátort az első lépéseket ismertető [dokumentációban](service-fabric-get-started-linux.md#set-up-yeoman-generators-for-containers-and-guest-executables) leírt lépések végrehajtásával. Service Fabric-alkalmazások Yeomannal való létrehozásához kövesse az alábbi lépéseket:

    ```bash
    npm install -g generator-azuresfjava       # for Service Fabric Java Applications
    npm install -g generator-azuresfguest      # for Service Fabric Guest executables
    npm install -g generator-azuresfcontainer  # for Service Fabric Container Applications
    ```
4. A generátorok telepítése után hozzon létre futtatható vendégalkalmazásokat vagy tárolószolgáltatásokat a `yo azuresfguest` vagy a `yo azuresfcontainer` futtatásával.

5. Service Fabric Java-alkalmazások Mac gépen való létrehozásához a JDK 1.8-as verziójának és a Gradle-nek telepítve kell lennie a gépen. A szoftver a [HomeBrew](https://brew.sh/) használatával, az alábbi módon telepíthető: 

    ```bash
    brew update
    brew cask install java
    brew install gradle
    ```

    > [!IMPORTANT]
    > Előfordulhat, hogy a jelenlegi `brew cask install java` verziói a JDK egy újabb verzióját telepítik.
    > Mindenképpen telepítse a JDK 8-at.

## <a name="deploy-your-application-on-your-mac-from-the-terminal"></a>Alkalmazás telepítése Mac gépen a terminálból

Miután létrehozta és kiépítette a Service Fabric-alkalmazását, a [Service Fabric parancssori felület](service-fabric-cli.md#cli-mac) segítségével helyezheti üzembe:

1. Csatlakozzon a Mac gépén lévő tárolópéldányon belül futó Service Fabric-fürthöz:

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```

2. Futtassa a telepítési szkriptet a projektkönyvtárból:

    ```bash
    cd MyProject
    bash install.sh
    ```

## <a name="set-up-net-core-31-development"></a>.NET Core 3.1-fejlesztés beállítása

Telepítse a [Machez készült .NET Core 3.1 SDK-t](https://dotnet.microsoft.com/download?initial-os=macos) a C# és [Service Fabric létrehozásához.](service-fabric-create-your-first-linux-application-with-csharp.md) A .NET Core-Service Fabric alkalmazások csomagjai a NuGet.org.

## <a name="install-the-service-fabric-plug-in-for-eclipse-on-your-mac"></a>Az Eclipse-hez készült Service Fabric beépülő modul telepítése Mac gépen

Az Azure Service Fabric biztosítja az Eclipse Neonhoz (vagy újabb kiadáshoz) készült beépülő modult a Java IDE környezethez. A beépülő modul leegyszerűsíti a Java-szolgáltatások létrehozásának, felépítésének és üzembe helyezésének folyamatát. Ha telepíteni szeretné az Eclipse-hez készült Service Fabric beépülő modul legújabb verzióját, vagy frissíteni szeretne a legújabb verzióra, kövesse [ezeket a lépéseket](service-fabric-get-started-eclipse.md#install-or-update-the-service-fabric-plug-in-in-eclipse). Az [Eclipse-hez készült Service Fabric dokumentációjában](service-fabric-get-started-eclipse.md) ismertetett további lépések szintén érvényesek: alkalmazás létrehozása, szolgáltatás hozzáadása egy alkalmazáshoz, alkalmazás eltávolítása stb.

Az utolsó lépés a tároló példányosítása egy a gazdagéppel megosztott elérési út használatával. Ahhoz, hogy a beépülő modul működjön a Mac gép Docker-tárolójával, ilyen típusú példányosításra van szükség. Például:

```bash
docker run -itd -p 19080:19080 -v /Users/sayantan/work/workspaces/mySFWorkspace:/tmp/mySFWorkspace --name sfonebox mcr.microsoft.com/service-fabric/onebox:latest
```

Az attribútumok a következők:
* `/Users/sayantan/work/workspaces/mySFWorkspace` a Mac munkaterületének teljes elérési útja.
* `/tmp/mySFWorkspace` az útvonal abban a tárolóban, ahová a munkaterület le lesz képezve.

>[!NOTE]
> 
>Ha más nevet/elérési útvonalat használ a munkaterülethez, frissítse az értékeket a `docker run` parancsban.
> 
>Ha a tárolót más, az `sfonebox` névtől eltérő néven indítja, frissítse a név értéket a Service Fabric-aktor Java alkalmazásában lévő testclient.sh fájlban.
>

## <a name="next-steps"></a>Következő lépések
<!-- Links -->
* [Az első Service Fabric Java-alkalmazás létrehozása és üzembe helyezése Linux rendszeren Yeoman használatával](service-fabric-create-your-first-linux-application-with-java.md)
* [Az első Service Fabric Java-alkalmazás létrehozása és üzembe helyezése Linux rendszeren az Eclipse-hez készült Service Fabric beépülő modul használatával](service-fabric-get-started-eclipse.md)
* [Service Fabric-fürt létrehozása az Azure Portalon](service-fabric-cluster-creation-via-portal.md)
* [Service Fabric-fürt létrehozása az Azure Resource Managerrel](service-fabric-cluster-creation-via-arm.md)
* [A Service Fabric-alkalmazásmodell megismerése](service-fabric-application-model.md)
* [A Service Fabric parancssori felület használata az alkalmazások kezeléséhez](service-fabric-application-lifecycle-sfctl.md)
* [Linux fejlesztőkörnyezet előkészítése Windowson](service-fabric-local-linux-cluster-windows.md)

<!-- Images -->
[cluster-setup-script]: ./media/service-fabric-get-started-mac/cluster-setup-mac.png
[sfx-mac]: ./media/service-fabric-get-started-mac/sfx-mac.png
[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-mac/sf-eclipse-plugin-install.png
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship
