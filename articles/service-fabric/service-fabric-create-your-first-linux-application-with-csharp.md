---
title: 'Az első Azure Service Fabric-alkalmazás létrehozása Linuxon C használatával #'
description: Megtudhatja, hogyan hozhat létre és helyezhet üzembe egy Service Fabric alkalmazást a C# és a .NET Core 2,0 használatával.
ms.topic: conceptual
ms.date: 04/11/2018
ms.openlocfilehash: 556d8dee4efd492fa98755f1ffd1cdc1c9887856
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "82193649"
---
# <a name="create-your-first-azure-service-fabric-application"></a>Az első Azure Service Fabric-alkalmazás létrehozása
> [!div class="op_single_selector"]
> * [Java – Linux (előzetes verzió)](service-fabric-create-your-first-linux-application-with-java.md)
> * [C# – Linux (előzetes verzió)](service-fabric-create-your-first-linux-application-with-csharp.md)
>
>

A Service Fabric SDK-kat biztosít Linux-szolgáltatások létrehozásához a .NET és a Java használatával egyaránt. A jelen oktatóanyagban áttekintjük, hogyan hozhat létre alkalmazásokat a Linux rendszerre, valamint szolgáltatásokat a C# használatával .NET Core 2.0 alatt.

## <a name="prerequisites"></a>Előfeltételek
Mielőtt elkezdené, győződjön meg arról, hogy [beállította a Linux-fejlesztőkörnyezetet](service-fabric-get-started-linux.md). Amennyiben a Mac OS X rendszert használja, [beállíthat egy beépített Linux-környezetet egy virtuális gépen a Vagrant használatával](service-fabric-get-started-mac.md).

A [Service FABRIC CLI](service-fabric-cli.md) -t is telepíteni kívánja

### <a name="install-and-set-up-the-generators-for-c"></a>A C# generátorainak telepítése és beállítása
A Service Fabric olyan szerkezetkialakító eszközöket biztosít, amelyek segítségével Service Fabric-alkalmazásokat hozhat létre a terminálból Yeoman-sablongenerátorok használatával. Hajtsa végre ezeket a lépéseket a Service Fabric Yeoman sablongenerátorok beállításához C#-hoz:

1. A node.js és az NPM telepítése a gépre

   ```bash
   curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.0/install.sh | bash 
   nvm install node 
   ```
2. A [Yeoman](https://yeoman.io/) sablongenerátor telepítése a gépre az NPM-ből

   ```bash
   npm install -g yo
   ```
3. A Service Fabric Yeoman C#-alkalmazásgenerátor telepítése az NPM-ből

   ```bash
   npm install -g generator-azuresfcsharp
   ```

## <a name="create-the-application"></a>Az alkalmazás létrehozása
A Service Fabric-alkalmazás egy vagy több szolgáltatást tartalmazhat, melyek mindegyike adott szerepkörrel rendelkezik az alkalmazás funkcióinak biztosításához. A C#-hoz készült Service Fabric [Yeoman](https://yeoman.io/) generátor, amelyet az utolsó lépésben telepített, megkönnyíti az első szolgáltatás létrehozását, és a továbbiak hozzáadását a későbbiekben. Hozzunk létre egy egyetlen szolgáltatással rendelkező alkalmazást a Yeoman használatával.

1. Írja be a terminálba az alábbi parancsot a keret létrehozásához: `yo azuresfcsharp`
2. Adjon nevet az alkalmazásnak.
3. Válassza ki az első szolgáltatása típusát, majd nevezze el. A jelen oktatóanyag esetén egy Reliable aktorszolgáltatást választunk.

   ![C# nyelvhez készült Service Fabric Yeoman-generátor][sf-yeoman]

> [!NOTE]
> További tudnivalók a beállításokról: [Service Fabric programming model overview](service-fabric-choose-framework.md) (A Service Fabric programozási modell áttekintése).
>
>

## <a name="build-the-application"></a>Az alkalmazás létrehozása
A Service Fabric Yeoman-sablonok tartalmaznak egy felépítési szkriptet, amelyet felhasználhat az alkalmazás terminálból történő létrehozásához (miután megnyitotta az alkalmazás mappáját).

  ```sh
 cd myapp
 ./build.sh
  ```

## <a name="deploy-the-application"></a>Az alkalmazás üzembe helyezése

Az alkalmazást a létrehozása után telepítheti a helyi fürtben.

1. Csatlakozzon a helyi Service Fabric-fürthöz.

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```

2. Futtassa a sablonban megadott telepítési szkriptet az alkalmazáscsomagnak a fürt lemezképtárolójába való másolásához, regisztrálja az alkalmazás típusát, és hozza létre az alkalmazás egy példányát.

    ```bash
    ./install.sh
    ```

A kész alkalmazás a többi Service Fabric-alkalmazással azonos módon telepíthető. Részletesebb útmutatást talál a [Service Fabric-alkalmazás kezelése a Service Fabric parancssori felülettel](service-fabric-application-lifecycle-sfctl.md) című dokumentációban.

Ezen parancsok paraméterezése megtalálható az alkalmazáscsomagon belül, a generált jegyzékfájlokban.

Az alkalmazás telepítése után nyisson meg egy böngészőt, és keresse fel a [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)t a `http://localhost:19080/Explorer` URL-címen. Bontsa ki az **Alkalmazások** csomópontot, és figyelje meg, hogy most már megjelenik benne egy bejegyzés az alkalmazás típusához, és egy másik a típus első példányához.

> [!IMPORTANT]
> Ha az alkalmazást egy biztonságos Linux-fürtön szeretné üzembe helyezni az Azure-ban, konfigurálnia kell egy tanúsítványt, hogy érvényesítse az alkalmazást a Service Fabric futtatókörnyezettel. Ezzel lehetővé teszi, hogy a Reliable Services szolgáltatásai kommunikáljanak a mögöttes Service Fabric Runtime API-kkal. További információ: [Reliable Services alkalmazás konfigurálása Linux-fürtökön való futtatásra](./service-fabric-configure-certificates-linux.md#configure-a-reliable-services-app-to-run-on-linux-clusters).  
>

## <a name="start-the-test-client-and-perform-a-failover"></a>Tesztügyfél elindítása és feladatátvétel végrehajtása
Az aktorprojektek önmagukban nem csinálnak semmit. Egy másik szolgáltatást vagy alkalmazást igényelnek, amely üzeneteket küld a számukra. Az aktorsablon egy egyszerű tesztszkriptet tartalmaz, amelyet az aktorszolgáltatással való kommunikációra használhat.

1. Futtassa a szkriptet a figyelési segédprogram használatával az aktorszolgáltatás kimenetének megtekintéséhez.

   MAC OS X esetén a myactorsvcTestClient mappát a tárolón belül ugyanarra a helyre kell másolnia a következő további parancsok futtatásával.
    
    ```bash
    docker cp  [first-four-digits-of-container-ID]:/home
    docker exec -it [first-four-digits-of-container-ID] /bin/bash
    cd /home
    ```
    
    ```bash
    cd myactorsvcTestClient
    watch -n 1 ./testclient.sh
    ```
2. Keresse meg az aktorszolgáltatás elsődleges replikáját futtató csomópontot a Service Fabric Explorerben. Az alábbi képernyőképen ez a 3. csomópont.

    ![Az elsődleges replika megkeresése a Service Fabric Explorerben][sfx-primary]
3. Kattintson az előző lépésben megtalált csomópontra, majd válassza a **Inaktiválás (újraindítás)** elemet a Műveletek menüből. Ezzel a művelettel újraindítja a helyi fürt egy csomópontját, és feladatátvételt kényszerít ki egy másik csomóponton futó másodlagos replikára. A művelet végrehajtása közben figyelje meg a tesztügyfél kimenetét, amelyből láthatja, hogy a számláló a feladatátvétel ellenére továbbra is növekszik.

## <a name="adding-more-services-to-an-existing-application"></a>További szolgáltatások hozzáadása meglévő alkalmazáshoz

Ha egy másik szolgáltatást szeretne hozzáadni a `yo` használatával már létrehozott alkalmazáshoz, hajtsa végre az alábbi lépéseket:
1. Lépjen a meglevő alkalmazás gyökérkönyvtárába.  Például `cd ~/YeomanSamples/MyApplication`, ha a `MyApplication` a Yeoman által létrehozott alkalmazás.
2. Az `yo azuresfcsharp:AddService` parancs futtatása

## <a name="next-steps"></a>Következő lépések

* [Service Fabric-fürtök használata a Service Fabric parancssori felületén](service-fabric-cli.md)
* A [Service Fabric támogatási lehetőségeinek](service-fabric-support.md) ismertetése
* [A Service Fabric parancssori felület használatának első lépései](service-fabric-cli.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-create-your-first-linux-application-with-csharp/yeoman-csharp.png
[sfx-primary]: ./media/service-fabric-create-your-first-linux-application-with-csharp/sfx-primary.png
