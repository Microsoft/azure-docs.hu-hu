---
title: A fejlesztői környezet beállítása Linuxon
description: Telepítse a futtatókörnyezetet és az SDK-t, majd hozzon létre egy helyi fejlesztési fürtöt Linuxon. A beállítás befejezése után készen áll az alkalmazások létrehozására.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: 346230c0363bf58926cc46cb8bac2de61b81d68b
ms.sourcegitcommit: f98ab5af0fa17a9bba575286c588af36ff075615
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2020
ms.locfileid: "85361972"
---
# <a name="prepare-your-development-environment-on-linux"></a>A fejlesztőkörnyezet előkészítése Linuxon
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md)
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
>
>  

Az [Azure Service Fabric-alkalmazásoknak](service-fabric-application-model.md) a linuxos fejlesztői gépen való üzembe helyezéséhez és futtatásához telepítse a futtatókörnyezetet és az általános SDK-t. A Javához és a .NET Core-fejlesztéshez készült opcionális SDK-kat is telepítheti. 

A cikkben ismertetett lépések azt feltételezik, hogy natív telepítést végez Linuxon, vagy a Service Fabric-tároló beépített rendszerképét használja (`mcr.microsoft.com/service-fabric/onebox:latest`).

A Service Fabric-futtatókörnyezet és az SDK telepítése nem támogatott a Linuxra készült Windows alrendszeren. A felhőben máshol vagy a helyszínen üzemeltetett Service Fabric-entitásokat kezelheti az Azure Service Fabric parancssori felületével (CLI), mely támogatott. A parancssori felület telepítési módját a [Service Fabric parancssori felület telepítését](./service-fabric-cli.md) ismertető témakörben találja.


## <a name="prerequisites"></a>Előfeltételek

A fejlesztéshez a következő operációsrendszer-verziók támogatottak.

* Ubuntu 16,04 ( `Xenial Xerus` ), 18,04 ( `Bionic Beaver` )

    Győződjön meg róla, hogy az `apt-transport-https` csomag telepítve van.
         
    ```bash
    sudo apt-get install apt-transport-https
    ```
* Red Hat Enterprise Linux 7.4 (Service Fabric előzetes verzió támogatása)


## <a name="installation-methods"></a>Telepítési módok

### <a name="script-installation-ubuntu"></a>Telepítés szkripttel (Ubuntu)

Az egyszerű használat érdekében megadunk egy szkriptet a Service Fabric-futtatókörnyezetnek és a Service Fabric általános SDK-nak a **sfctl** CLI felülettel együttes telepítéséhez. Futtassa a manuális telepítési lépéseket a következő szakaszban. Láthatja a telepített elemeket és az azokhoz tartozó licenceket. A szkript a futtatáskor azt feltételezi, hogy Ön átolvasta és elfogadja a telepített szoftverek licencfeltételeit.

A szkript sikeres futtatását követően folytathatja a [Helyi fürt beállítása](#set-up-a-local-cluster) lépéssel.

```bash
sudo curl -s https://raw.githubusercontent.com/Azure/service-fabric-scripts-and-templates/master/scripts/SetupServiceFabric/SetupServiceFabric.sh | sudo bash
```

### <a name="manual-installation"></a>Manuális telepítés
A Service Fabric-futtatókörnyezet és az általános SDK manuális telepítéséhez kövesse a jelen útmutató hátralévő részét.

## <a name="update-your-apt-sources-or-yum-repositories"></a>APT-források vagy Yum-adattárak frissítése
Az SDK és a kapcsolódó futtatókörnyezet-csomag apt-get parancssori eszköz használatával történő telepítéséhez először frissítenie kell az Advanced Packaging Tool (APT) forrásait.

### <a name="ubuntu"></a>Ubuntu

1. Nyisson meg egy terminált.

2. Adja hozzá a `dotnet` tárházat a források listájához a disztribúciónak megfelelően.

    ```bash
    wget -q https://packages.microsoft.com/config/ubuntu/$(lsb_release -rs)/packages-microsoft-prod.deb
    sudo dpkg -i packages-microsoft-prod.deb
    ```

3. Adja hozzá az új Gnu Privacy Guard- (GnuPG- vagy GPG-) kulcsot az APT-kulcstárhoz.

    ```bash
    sudo curl -fsSL https://packages.microsoft.com/keys/msopentech.asc | sudo apt-key add -
    ```

4. Adja hozzá a hivatalos Docker GPG-kulcsot az APT-kulcstárhoz.

    ```bash
    sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    ```

5. Adja hozzá az MS Open Tech GPG kulcsot az APT kulcstartóhoz.

    ```bash
    sudo curl -fsSL https://packages.microsoft.com/keys/msopentech.asc | apt-key add -
    ```

6. Állítsa be a Docker-tárházat.

    ```bash
    sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
    ```

7. Adja hozzá az Azul JDK-kulcsot az APT-kulcstartóhoz, és állítsa be az adattárát.

    ```bash
    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 0xB1998361219BD9C9
    sudo apt-add-repository "deb http://repos.azul.com/azure-only/zulu/apt stable main"
    ```

8. Frissítse a csomaglistákat az újonnan hozzáadott adattárak szerint.

    ```bash
    sudo apt-get update
    ```


### <a name="red-hat-enterprise-linux-74-service-fabric-preview-support"></a>Red Hat Enterprise Linux 7.4 (Service Fabric előzetes verzió támogatása)

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

6. Telepítse a .NET SDK-t.

    ```bash
    yum install rh-dotnet20 -y
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

### <a name="red-hat-enterprise-linux-74-service-fabric-preview-support"></a>Red Hat Enterprise Linux 7.4 (Service Fabric előzetes verzió támogatása)

```bash
sudo yum install servicefabricsdkcommon
```

Az SDK-telepítéssel együtt érkező Service Fabric-futtatókörnyezet az alábbi táblázatban szereplő csomagokat tartalmazza. 

 | | DotNetCore | Java | Python | NodeJS | 
--- | --- | --- | --- |---
Ubuntu | 2.0.0 | AzulJDK 1,8 | Implicit módon az npm-ből | legújabb |
RHEL | - | OpenJDK 1.8 | Implicit módon az npm-ből | legújabb |

## <a name="set-up-a-local-cluster"></a>Helyi fürt beállítása
Ha a telepítés befejeződött, indítson egy helyi fürtöt.

1. Futtassa a fürttelepítési szkriptet.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```

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

## <a name="set-up-net-core-20-development"></a>.NET Core 2.0 fejlesztői környezet beállítása

Telepítse az [Ubuntu rendszerre készült .NET Core 2.0 SDK-t](https://www.microsoft.com/net/core#linuxubuntu)[a C# Service Fabric-alkalmazások létrehozásának](service-fabric-create-your-first-linux-application-with-csharp.md) első lépéseként. A .NET Core 2.0 Service Fabric-alkalmazások csomagjai a NuGet.org webhelyen érhetők el, jelenleg előzetes verzióban.

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

1. Az Eclipse-ben győződjön meg arról, hogy telepítve van az Eclipse Neon vagy egy újabb verzió, és a Buildship 2.2.1-es vagy újabb verziója. A telepített összetevők verziójának megtekintéséhez válassza az **Help**  >  **Eclipse**-  >  **telepítés részleteinek**súgóját. A Buildship frissítéséhez kövesse az [Eclipse Buildship: Eclipse Plug-ins for Gradle][buildship-update] (Eclipse Buildship: Eclipse beépülő modulok a Gradle-hez) című témakör utasításait.

2. A Service Fabric beépülő modul telepítéséhez válassza a **Súgó**  >  **új szoftver telepítése**lehetőséget.

3. A **Work with (munka** ) mezőben adja meg a **https: \/ /dl.microsoft.com/Eclipse**értéket.

4. Válassza a **Hozzáadás** elemet.

    ![Az Available Software (Elérhető szoftver) lap][sf-eclipse-plugin]

5. Válassza a **ServiceFabric** beépülő modult, majd a **Next** (Tovább) gombot.

6. Hajtsa végre a telepítés lépéseit. Ezután fogadja el a végfelhasználói licencszerződést.

Ha a Service Fabric Eclipse beépülő modul már telepítve van, győződjön meg arról, hogy a legújabb verzióval rendelkezik. Jelölje be az **Help**  >  **Eclipse**-  >  **telepítés részleteinek**súgóját. Ezután keressen rá Service Fabric a telepített beépülő modulok listájában. Válassza a **frissítés** lehetőséget, ha újabb verzió érhető el.

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

## <a name="next-steps"></a>További lépések

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
