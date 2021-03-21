---
title: Az Azure Service Fabric Linux-fürt beállítása Windows rendszeren
description: Ez a cikk bemutatja, hogyan állíthat be Service Fabric Linux rendszerű fürtöket Windows-fejlesztői gépeken. Ez a megközelítés a platformok közötti fejlesztéshez hasznos.
ms.topic: conceptual
ms.date: 10/16/2020
ms.openlocfilehash: 7b25a84e76773baea9f17430df1b7ba13aa661aa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93087077"
---
# <a name="set-up-a-linux-service-fabric-cluster-on-your-windows-developer-machine"></a>Linux Service Fabric-fürt beállítása a Windows fejlesztői gépen

Ez a dokumentum ismerteti, hogyan állíthat be helyi Linux Service Fabric-fürtöt egy Windows rendszerű fejlesztői gépen. A helyi Linux-fürtök beállítása hasznos lehet a Linux-fürtöket megcélzó alkalmazások gyors tesztelésére, de a Windows rendszerű gépeken való fejlesztésre.

## <a name="prerequisites"></a>Előfeltételek
A Linux-alapú Service Fabric-fürtök nem futnak Windows rendszeren, de a többplatformos prototípusok engedélyezéséhez a Linux Service Fabric egy box-beli Docker-tárolót biztosítunk, amely a Windows Docker használatával telepíthető.

A kezdés előtt a következőkre lesz szüksége:

* Legalább 4 GB RAM
* A [Windows rendszerhez készült Docker](https://store.docker.com/editions/community/docker-ce-desktop-windows) legújabb verziója
* A Docker-nek Linux-tároló módban kell futnia

>[!TIP]
> A Docker Windows rendszerű gépen való telepítéséhez kövesse a [Docker dokumentációjának](https://store.docker.com/editions/community/docker-ce-desktop-windows/plans/docker-ce-desktop-windows-tier?tab=instructions)lépéseit. A telepítés után [ellenőrizze meg a telepítést](https://docs.docker.com/docker-for-windows/#check-versions-of-docker-engine-compose-and-machine).
>

## <a name="create-a-local-container-and-setup-service-fabric"></a>Helyi tároló létrehozása és a Service Fabric beállítása
Egy helyi Docker-tároló beállításához és egy Service Fabric-fürt futtatásához futtassa a következő lépéseket:


1. Frissítse a Docker-démon konfigurációját a gazdagépen a következő értékekkel, és indítsa újra a Docker-démont: 

    ```json
    {
      "ipv6": true,
      "fixed-cidr-v6": "2001:db8:1::/64"
    }
    ```
    A frissítés javasolt módja, ha a következőre lép: 

    * Docker-ikon > beállítások > Docker-motor
    * Adja hozzá a fent felsorolt új mezőket
    * Alkalmazás & újraindítása – a módosítások életbe léptetéséhez indítsa újra a Docker-démont.

2. Indítsa el a fürtöt a PowerShell használatával.<br/>
    <b>Ubuntu 18,04 LTS:</b>
    ```powershell
    docker run --name sftestcluster -d -v /var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mcr.microsoft.com/service-fabric/onebox:u18
    ```

    <b>Ubuntu 16,04 LTS:</b>
    ```powershell
    docker run --name sftestcluster -d -v /var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mcr.microsoft.com/service-fabric/onebox:u16
    ```

    >[!TIP]
    > Alapértelmezés szerint ez a Service Fabric legújabb verziójával rendelkező rendszerképet kéri le. Adott változatok esetében tekintse meg a Docker hub [Service Fabric beépített](https://hub.docker.com/_/microsoft-service-fabric-onebox) lapját.



3. Nem kötelező: a bővített Service Fabric rendszerkép kiépítése.

    Egy új könyvtárban hozzon létre egy nevű fájlt a `Dockerfile` testreszabott rendszerkép létrehozásához:

    >[!NOTE]
    >A fenti képen a Docker további programokat vagy függőségeket adhat hozzá a tárolóhoz.
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

    Az újrahasznosítható rendszerkép létrehozásához `Dockerfile` nyissa meg a terminált, és `cd` a közvetlenül a következő `Dockerfile` parancsot:

    ```powershell 
    docker build -t mysfcluster .
    ```
    
    >[!NOTE]
    >Ez a művelet eltarthat egy ideig, de csak egyszer kell végrehajtani.

    Most gyorsan elindíthatja Service Fabric helyi példányát, amikor a futtatásához szükség van rá:

    ```powershell 
    docker run --name sftestcluster -d -v /var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mysfcluster
    ```

    >[!TIP]
    >Ha megad egy nevet a tárolópéldányhoz, átláthatóbb módon kezelheti. 
    >
    >Ha az alkalmazás bizonyos portokon figyel, a portokat további `-p` címkékkel kell meghatározni. Ha például az alkalmazás a 8080-as porton figyel, adja hozzá az alábbi `-p` címkét:
    >
    >`docker run -itd -p 19000:19000 -p 19080:19080 -p 8080:8080 --name sfonebox mcr.microsoft.com/service-fabric/onebox:u18`
    >


4. A fürt elindulása eltart egy rövid ideig. A naplók megtekintéséhez vagy az irányítópultra való ugráshoz és a fürt állapotának megtekintéséhez használja az alábbi parancsot `http://localhost:19080`:

    ```powershell 
    docker logs sftestcluster
    ```

5. Miután a fürt üzembe helyezése sikeres volt a 4. lépésben megfigyelt módon, ``http://localhost:19080`` a Windows rendszerű gépről megkeresheti a Service Fabric Explorer irányítópultot. Ezen a ponton a Windows Developer Machine eszközeivel csatlakozhat ehhez a fürthöz, és telepítheti a Linux Service Fabric-fürtökre irányuló alkalmazásokat. 

    > [!NOTE]
    > Az Eclipse beépülő modul jelenleg nem támogatott a Windows rendszeren. 

6. Ha elkészült, állítsa le és törölje a tárolót a következő paranccsal:

    ```powershell 
    docker rm -f sftestcluster
    ```

### <a name="known-limitations"></a>Ismert korlátozások 
 
 A Mac gép tárolójában futó helyi fürt ismert korlátozásai a következők: 
 
 * A DNS-szolgáltatás nem fut, és jelenleg nem támogatott a tárolón belül. [Probléma #132](https://github.com/Microsoft/service-fabric/issues/132)
 * A Container-alapú alkalmazások futtatásához az SF futtatására van szükség egy Linux-gazdagépen. A beágyazott tároló alkalmazások jelenleg nem támogatottak.

## <a name="next-steps"></a>Következő lépések
* [Az első Service Fabric Java-alkalmazás létrehozása és üzembe helyezése Linux rendszeren Yeoman használatával](service-fabric-create-your-first-linux-application-with-java.md)
* Ismerkedés az [Eclipse](./service-fabric-get-started-eclipse.md) -szel
* Tekintse meg a [Java-mintákat](https://github.com/Azure-Samples/service-fabric-java-getting-started)
* A [Service Fabric támogatási lehetőségeinek](service-fabric-support.md) ismertetése


<!-- Image references -->

[publishdialog]: ./media/service-fabric-manage-multiple-environment-app-configuration/publish-dialog-choose-app-config.png
[app-parameters-solution-explorer]:./media/service-fabric-manage-multiple-environment-app-configuration/app-parameters-in-solution-explorer.png
