---
title: A fejlesztői környezet beállítása Linuxon
description: Telepítse a futtatókörnyezetet és az SDK-t, majd hozzon létre egy helyi fejlesztési fürtöt Linuxon. A beállítás befejezése után készen áll az alkalmazások létrehozására.
ms.topic: conceptual
ms.date: 10/16/2020
ms.custom: devx-track-js
ms.openlocfilehash: 14b8a278605a908b4182c724831b2e42de54a753
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93086890"
---
# <a name="prepare-your-development-environment-on-linux"></a>A fejlesztőkörnyezet előkészítése Linuxon
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md)
> * [Linux](service-fabric-get-started-linux.md)
> * [Mac OS X](service-fabric-get-started-mac.md)

Az [Azure Service Fabric-alkalmazásoknak](service-fabric-application-model.md) a linuxos fejlesztői gépen való üzembe helyezéséhez és futtatásához telepítse a futtatókörnyezetet és az általános SDK-t. A Javához és a .NET Core-fejlesztéshez készült opcionális SDK-kat is telepítheti. 

A cikkben ismertetett lépések azt feltételezik, hogy natív módon telepíti a Linux rendszert, vagy használja a [Service Fabric beépített-tároló rendszerképet](https://hub.docker.com/_/microsoft-service-fabric-onebox), például: `mcr.microsoft.com/service-fabric/onebox:u18` .

A felhőben vagy a helyszínen üzemeltetett Service Fabric entitásokat az Azure Service Fabric parancssori felületével (CLI) kezelheti. A parancssori felület telepítési módját a [Service Fabric parancssori felület telepítését](./service-fabric-cli.md) ismertető témakörben találja.


## <a name="prerequisites"></a>Előfeltételek

A fejlesztéshez a következő operációsrendszer-verziók támogatottak.

* Ubuntu 16,04 ( `Xenial Xerus` ), 18,04 ( `Bionic Beaver` )

    Győződjön meg róla, hogy az `apt-transport-https` csomag telepítve van.
         
    ```bash
    sudo apt-get install apt-transport-https
    ```
* Red Hat Enterprise Linux 7.4 (Service Fabric előzetes verzió támogatása)


## <a name="installation-methods"></a>Telepítési módok

<!-- markdownlint-disable MD025 -->
<!-- markdownlint-disable MD024 -->

# <a name="ubuntu"></a>[Ubuntu](#tab/sdksetupubuntu)

## <a name="update-your-apt-sources"></a>Frissítse az APT-forrásait
Az SDK és a kapcsolódó futtatókörnyezet-csomag apt-get parancssori eszköz használatával történő telepítéséhez először frissítenie kell az Advanced Packaging Tool (APT) forrásait.

## <a name="script-installation"></a>Telepítés szkripttel

A kényelmes használat érdekében parancsfájlt biztosít a Service Fabric futtatókörnyezet és a Service Fabric Common SDK, valamint a [ **sfctl** CLI](service-fabric-cli.md)telepítésére. A szkript a futtatáskor azt feltételezi, hogy Ön átolvasta és elfogadja a telepített szoftverek licencfeltételeit. Azt is megteheti, hogy futtatja a [manuális telepítési](#manual-installation) lépéseket a következő szakaszban, amely a társított licenceket és a telepítés alatt álló összetevőket is bemutatja.

A szkript sikeres futtatását követően folytathatja a [Helyi fürt beállítása](#set-up-a-local-cluster) lépéssel.

```bash
sudo curl -s https://raw.githubusercontent.com/Azure/service-fabric-scripts-and-templates/master/scripts/SetupServiceFabric/SetupServiceFabric.sh | sudo bash
```

## <a name="manual-installation"></a>Manuális telepítés
A Service Fabric-futtatókörnyezet és az általános SDK manuális telepítéséhez kövesse a jelen útmutató hátralévő részét.

1. Nyisson meg egy terminált.

2. Adja hozzá a `dotnet` tárházat a források listájához a disztribúciónak megfelelően.

    ```bash
    wget -q https://packages.microsoft.com/config/ubuntu/$(lsb_release -rs)/packages-microsoft-prod.deb
    sudo dpkg -i packages-microsoft-prod.deb
    ```

3. Adja hozzá az új MS Open Tech GNU Privacy Guard (GnuPG vagy GPG) kulcsot az APT kulcstartóhoz.

    ```bash
    sudo curl -fsSL https://packages.microsoft.com/keys/msopentech.asc | sudo apt-key add -
    ```

4. Adja hozzá a hivatalos Docker GPG-kulcsot az APT-kulcstárhoz.

    ```bash
    sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    ```

5. Állítsa be a Docker-tárházat.

    ```bash
    sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
    ```

6. Adja hozzá az Azul JDK-kulcsot az APT-kulcstartóhoz, és állítsa be az adattárát.

    ```bash
    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 0xB1998361219BD9C9
    sudo apt-add-repository "deb http://repos.azul.com/azure-only/zulu/apt stable main"
    ```

7. Frissítse a csomaglistákat az újonnan hozzáadott adattárak szerint.

    ```bash
    sudo apt-get update
    ```

## <a name="install-and-set-up-the-service-fabric-sdk-for-a-local-cluster"></a>A Service Fabric SDK telepítése és beállítása helyi fürthöz

A források frissítése után telepítheti az SDK-t. Telepítse a Service Fabric SDK-csomagot, erősítse meg a telepítést, és fogadja el a licencszerződést.

### <a name="ubuntu"></a>Ubuntu

```bash
sudo apt-get install servicefabricsdkcommon
```

> [!TIP]
>   A következő parancsok automatizálják a Service Fabric-csomagok licenceinek elfogadását:
>   ```bash
>   echo "servicefabric servicefabric/accepted-eula-ga select true" | sudo debconf-set-selections
>   echo "servicefabricsdkcommon servicefabricsdkcommon/accepted-eula-ga select true" | sudo debconf-set-selections
>   ```

# <a name="red-hat-enterprise-linux-74"></a>[Red Hat Enterprise Linux 7.4](#tab/sdksetuprhel74)

## <a name="update-your-yum-repositories"></a>A yum-adattárak frissítése
Az SDK és a kapcsolódó futásidejű csomag a yum parancssori eszköz használatával történő telepítéséhez először frissítenie kell a csomag forrásait.

## <a name="manual-installation-rhel"></a>Manuális telepítés (RHEL)
A Service Fabric-futtatókörnyezet és az általános SDK manuális telepítéséhez kövesse a jelen útmutató hátralévő részét.

1. Nyisson meg egy terminált.
2. Töltse le és telepítse az Extra Packages for Enterprise Linux (EPEL) programot.

    ```bash
    wget https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
    sudo yum install epel-release-latest-7.noarch.rpm
    ```
3. Adja hozzá az EfficiOS RHEL7 csomagtárat a rendszerhez.

    ```bash
    sudo wget -P /etc/yum.repos.d/ https://packages.efficios.com/repo.files/EfficiOS-RHEL7-x86-64.repo
    ```

4. Importálja az EfficiOS csomag aláírókulcsát a helyi GPG-kulcstárba.

    ```bash
    sudo rpmkeys --import https://packages.efficios.com/rhel/repo.key
    ```

5. Adja hozzá a Microsoft RHEL-adattárat a rendszerhez.

    ```bash
    curl https://packages.microsoft.com/config/rhel/7.4/prod.repo > ./microsoft-prod.repo
    sudo cp ./microsoft-prod.repo /etc/yum.repos.d/
    ```

## <a name="install-and-set-up-the-service-fabric-sdk-for-a-local-cluster-rhel"></a>A Service Fabric SDK telepítése és beállítása helyi fürthöz (RHEL)

A források frissítése után telepítheti az SDK-t. Telepítse a Service Fabric SDK-csomagot, erősítse meg a telepítést, és fogadja el a licencszerződést.

```bash
sudo yum install servicefabricsdkcommon
```

---

## <a name="included-packages"></a>Belefoglalt csomagok
Az SDK-telepítéssel együtt érkező Service Fabric-futtatókörnyezet az alábbi táblázatban szereplő csomagokat tartalmazza. 

 | | DotNetCore | Java | Python | NodeJS | 
--- | --- | --- | --- |---
**Ubuntu** | 2.0.7 | AzulJDK 1,8 | Implicit módon az npm-ből | legújabb |
**RHEL** | - | OpenJDK 1.8 | Implicit módon az npm-ből | legújabb |

## <a name="set-up-a-local-cluster"></a>Helyi fürt beállítása
1. Indítsa el a helyi Service Fabric-fürtöt a fejlesztéshez.

# <a name="container-based-local-cluster"></a>[Tároló-alapú helyi fürt](#tab/localclusteroneboxcontainer)

Tároló-alapú [Service Fabric beépített](https://hub.docker.com/_/microsoft-service-fabric-onebox) -fürt elindítása.

1. Telepítse a Mobyt a Docker-tárolók üzembe helyezéséhez.
    ```bash
    sudo apt-get install moby-engine moby-cli -y
    ```
2. Frissítse a Docker-démon konfigurációját a gazdagépen a következő beállításokkal, majd indítsa újra a Docker-démont. Részletek: [IPv6-támogatás engedélyezése](https://docs.docker.com/config/daemon/ipv6/)

    ```json
    {
        "ipv6": true,
        "fixed-cidr-v6": "fd00::/64"
    }
    ```

3. Indítsa el a fürtöt.<br/>
    <b>Ubuntu 18,04 LTS:</b>
    ```bash
    docker run --name sftestcluster -d -v /var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mcr.microsoft.com/service-fabric/onebox:u18
    ```

    <b>Ubuntu 16,04 LTS:</b>
    ```bash
    docker run --name sftestcluster -d -v /var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mcr.microsoft.com/service-fabric/onebox:u16
    ```

    >[!TIP]
    > Alapértelmezés szerint ez a Service Fabric legújabb verziójával rendelkező rendszerképet kéri le. Adott változatokért látogasson el a [Docker Hub](https://hub.docker.com/r/microsoft/service-fabric-onebox/) oldalára.

# <a name="local-cluster"></a>[Helyi fürt](#tab/localcluster)

Miután telepítette az SDK-t a fenti lépések használatával, indítson el egy helyi fürtöt.

1. Futtassa a fürttelepítési szkriptet.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```

---

2. Nyisson meg egy webböngészőt, és lépjen a **Service Fabric Explorer** () webhelyre `http://localhost:19080/Explorer` . A fürt indításakor megjelenik a Service Fabric Explorer irányítópultja. Eltarthat néhány percig, amíg a rendszer teljesen beállítja a fürtöt. Ha a böngésző nem tudja megnyitni az URL-címet, vagy a Service Fabric Explorer azt mutatja, hogy a rendszer nem áll készen, várjon néhány percet, és próbálkozzon újra.

    ![Service Fabric Explorer Linuxon][sfx-linux]

    Most telepítheti az előzetesen összeállított Service Fabric-alkalmazáscsomagokat vagy a vendégtárolókon vagy vendég futtatható fájlokon alapuló új alkalmazáscsomagokat. Ha új szolgáltatásokat szeretne létrehozni a Java vagy a .NET Core SDK-k használatával, kövesse az alábbi szakaszokban megadott opcionális beállítási lépéseket.


> [!NOTE]
> Az önálló fürtök Linuxon nem támogatottak.


> [!TIP]
> Ha van kéznél egy SSD lemez, az igazán kiváló teljesítmény érdekében érdemes egy SSD-könyvtárútvonalat továbbítani a `--clusterdataroot` és a devclustersetup.sh használatával.

## <a name="set-up-the-service-fabric-cli"></a>A Service Fabric parancssori felület beállítása

A [Service Fabric parancssori felület](service-fabric-cli.md) a Service Fabric-entitásokkal, többek között fürtökkel és alkalmazásokkal folytatott interakcióra szolgáló parancsokat is tartalmaz. A CLI telepítéséhez kövesse a [Service Fabric parancssori felület](service-fabric-cli.md) című cikk utasításait.


## <a name="set-up-yeoman-generators-for-containers-and-guest-executables"></a>Yeoman-generátorok beállítása a tárolókhoz és vendégalkalmazásokhoz
A Service Fabric olyan szerkezetkialakító eszközöket biztosít, amelyek segítségével Service Fabric-alkalmazásokat hozhat létre a terminálból Yeoman-sablongenerátorok használatával. Hajtsa végre ezeket a lépéseket a Service Fabric Yeoman-sablongenerátorok beállításához:

1. Telepítse a Node.js és az npm eszközt a gépre.

    ```bash
    sudo add-apt-repository "deb https://deb.nodesource.com/node_8.x $(lsb_release -s -c) main"
    sudo apt-get update
    sudo apt-get install nodejs
    ```
2. Telepítse a gépre a [Yeoman](https://yeoman.io/) sablongenerátort az npm-ből.

    ```bash
    sudo npm install -g yo
    ```
3. Telepítse a Service Fabric Yeo tárológenerátort és futtatható vendégalkalmazás-generátort az npm-ből.

    ```bash
    sudo npm install -g generator-azuresfcontainer  # for Service Fabric container application
    sudo npm install -g generator-azuresfguest      # for Service Fabric guest executable application
    ```

A generátorok telepítése után hozzon létre futtatható vendégalkalmazásokat vagy tárolószolgáltatásokat a `yo azuresfguest` vagy a `yo azuresfcontainer` futtatásával.

## <a name="set-up-net-core-31-development"></a>A .NET Core 3,1-fejlesztés beállítása

Telepítse a [.net Core 3,1 SDK for Ubuntu](https://www.microsoft.com/net/core#linuxubuntu) alkalmazást a [C# Service Fabric-alkalmazások létrehozásának](service-fabric-create-your-first-linux-application-with-csharp.md)megkezdéséhez. A .NET Core Service Fabric alkalmazások csomagjai a NuGet.org futnak.

## <a name="set-up-java-development"></a>Java fejlesztői környezet beállítása

Ha a Java-t használó Service Fabric-szolgáltatásokat szeretne létrehozni, telepítse a Gradle-t a Build-feladatok futtatásához. Futtassa az alábbi parancsot a Gradle telepítéséhez. A Service Fabric Java-kódtárakat a Mavenből kéri le a rendszer.


* Ubuntu

    ```bash
    curl -s https://get.sdkman.io | bash
    sdk install gradle 5.1
    ```

* Red Hat Enterprise Linux 7.4 (Service Fabric előzetes verzió támogatása)

  ```bash
  sudo yum install java-1.8.0-openjdk-devel
  curl -s https://get.sdkman.io | bash
  sdk install gradle
  ```

A Service Fabric Yeo-generátort is telepítenie kell végrehajtható Java-fájlokhoz. Győződjön meg róla, hogy [telepítette a Yeomant](#set-up-yeoman-generators-for-containers-and-guest-executables), majd futtassa az alábbi parancsot:

  ```bash
  npm install -g generator-azuresfjava
  ```
 
## <a name="install-the-eclipse-plug-in-optional"></a>Az Eclipse beépülő modul telepítése (nem kötelező)

A Service Fabric Eclipse beépülő modulját a Java-fejlesztőknek vagy a Java EE-fejlesztőknek készült Eclipse IDE-ből telepítheti. Az Eclipse segítségével új Service Fabric futtatható vendégalkalmazásokat és tárolóalkalmazásokat, valamint Service Fabric Java-alkalmazásokat hozhat létre.

> [!IMPORTANT]
> A Service Fabric beépülő modulhoz Eclipse Neon vagy újabb verzió szükséges. Az ezt a megjegyzést követő útmutatások segítségével ellenőrizheti az Eclipse verzióját. Ha az Eclipse egy korábbi verziója van telepítve, az [Eclipse webhelyéről](https://www.eclipse.org) tölthet le újabb verziót. Javasoljuk, hogy ne telepítse az új verziót az Eclipse meglévő telepítésére (azt felülírva). Ehelyett a meglévő verziót távolítsa el a telepítő futtatása előtt, vagy telepítse másik könyvtárba az újabb verziót.
> 
> Ubuntu rendszeren ajánlott közvetlenül az Eclipse webhelyéről elvégezni a telepítést csomagtelepítő helyett (`apt` vagy `apt-get`). Így biztosan az Eclipse legfrissebb verzióját fogja beszerezni. Telepítheti a Java-fejlesztőknek vagy a Java EE-fejlesztőknek készült Eclipse IDE-t.

1. Az Eclipse-ben győződjön meg arról, hogy telepítve van az Eclipse Neon vagy egy újabb verzió, és a Buildship 2.2.1-es vagy újabb verziója. A telepített összetevők verziójának megtekintéséhez válassza az   >  **Eclipse**-  >  **telepítés részleteinek** súgóját. A Buildship frissítéséhez kövesse az [Eclipse Buildship: Eclipse Plug-ins for Gradle][buildship-update] (Eclipse Buildship: Eclipse beépülő modulok a Gradle-hez) című témakör utasításait.

2. A Service Fabric beépülő modul telepítéséhez válassza a **Súgó**  >  **új szoftver telepítése** lehetőséget.

3. A **Work with (munka** ) mezőben adja meg a **https: \/ /dl.microsoft.com/Eclipse** értéket.

4. Válassza a **Hozzáadás** lehetőséget.

    ![Az Available Software (Elérhető szoftver) lap][sf-eclipse-plugin]

5. Válassza a **ServiceFabric** beépülő modult, majd a **Next** (Tovább) gombot.

6. Hajtsa végre a telepítés lépéseit. Ezután fogadja el a végfelhasználói licencszerződést.

Ha a Service Fabric Eclipse beépülő modul már telepítve van, győződjön meg arról, hogy a legújabb verzióval rendelkezik. Jelölje be az   >  **Eclipse**-  >  **telepítés részleteinek** súgóját. Ezután keressen rá Service Fabric a telepített beépülő modulok listájában. Válassza a **frissítés** lehetőséget, ha újabb verzió érhető el.

További információ: [Service Fabric beépülő modul az Eclipse-alapú Java-alkalmazásfejlesztéshez](service-fabric-get-started-eclipse.md).

## <a name="update-the-sdk-and-runtime"></a>Az SDK és a futtatókörnyezet frissítése

Az SDK és a futtatókörnyezet legújabb verziójára történő frissítéshez futtassa a következő parancsokat.

```bash
sudo apt-get update
sudo apt-get install servicefabric servicefabricsdkcommon
```
A Java SDK bináris fájljainak a Mavenből való frissítéséhez frissítenie kell a megfelelő bináris fájl verzióadatait a ``build.gradle`` fájlban, hogy azok a legfrissebb verzióra mutassanak. A verzió frissítésének pontos helyét bármelyik ``build.gradle`` fájlból megtudhatja a [Service Fabric első lépéseit bemutató példákból](https://github.com/Azure-Samples/service-fabric-java-getting-started).

> [!NOTE]
> A csomagok frissítése miatt előfordulhat, hogy a helyi fejlesztési fürt leáll. Frissítés után a jelen cikk utasításait követve indítsa újra a helyi fürtöt.

## <a name="remove-the-sdk"></a>Az SDK eltávolítása
A Service Fabric SDK-k eltávolításához futtassa a következő parancsokat.

* Ubuntu

    ```bash
    sudo apt-get remove servicefabric servicefabicsdkcommon
    npm uninstall -g generator-azuresfcontainer
    npm uninstall -g generator-azuresfguest
    sudo apt-get install -f
    ```


* Red Hat Enterprise Linux 7.4 (Service Fabric előzetes verzió támogatása)

    ```bash
    sudo yum remove servicefabric servicefabicsdkcommon
    npm uninstall -g generator-azuresfcontainer
    npm uninstall -g generator-azuresfguest
    ```

## <a name="next-steps"></a>Következő lépések

* [Az első Service Fabric Java-alkalmazás létrehozása és üzembe helyezése Linux rendszeren Yeoman használatával](service-fabric-create-your-first-linux-application-with-java.md)
* [Az első Service Fabric Java-alkalmazás létrehozása és üzembe helyezése Linux rendszeren az Eclipse Service Fabric beépülő modul használatával](service-fabric-get-started-eclipse.md)
* [Az első CSharp-alkalmazás létrehozása Linuxon](service-fabric-create-your-first-linux-application-with-csharp.md)
* [A fejlesztőkörnyezet előkészítése OSX-en](service-fabric-get-started-mac.md)
* [Linux fejlesztőkörnyezet előkészítése Windowson](service-fabric-local-linux-cluster-windows.md)
* [Alkalmazások kezelése a Service Fabric parancssori felületével](service-fabric-application-lifecycle-sfctl.md)
* [Service Fabric – Különbségek Windows és Linux rendszeren](service-fabric-linux-windows-differences.md)
* [A Service Fabric parancssori felület használatának első lépései](service-fabric-cli.md)

<!-- Links -->

[azure-xplat-cli-github]: https://github.com/Azure/azure-xplat-cli
[install-node]: https://nodejs.org/en/download/package-manager/#installing-node-js-via-package-manager
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

<!--Images -->

[sf-eclipse-plugin]: ./media/service-fabric-get-started-linux/service-fabric-eclipse-plugin.png
[sfx-linux]: ./media/service-fabric-get-started-linux/sfx-linux.png
