---
title: Modulok fejlesztése és hibakeresése a Visual Studióban – Azure IoT Edge
description: A Visual Studióval és az Azure IoT eszközökkel fejlesztheti a C vagy C# IoT Edge modult, és leküldheti a IoT Hubból egy IoT-eszközre, amelyet a telepítési jegyzék konfigurál.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 3/27/2020
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: 6e5b5c021eb6a83de9ecfb31757855065b70c290
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103196931"
---
# <a name="use-visual-studio-2019-to-develop-and-debug-modules-for-azure-iot-edge"></a>Azure IoT Edge-modulok fejlesztése és hibakeresése a Visual Studio 2019 használatával

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Az üzleti logikát bekapcsolhatja Azure IoT Edge moduljaiba. Ebből a cikkből megtudhatja, hogyan használhatja a Visual Studio 2019-et a modulok fejlesztésére és hibakeresésére szolgáló fő eszközként.

A Visual studióhoz készült Azure IoT Edge Tools a következő előnyöket nyújtja:

- Azure IoT Edge megoldások és modulok létrehozása, szerkesztése, összeállítása, futtatása és hibakeresése a helyi fejlesztési számítógépen.
- Azure IoT Edge megoldás üzembe helyezése Azure IoT Edge eszközön az Azure IoT Hub használatával.
- Az Azure IoT-modulokat C vagy C# nyelven, a Visual Studio-fejlesztés összes előnyének kihasználásával.
- Azure IoT Edge eszközök és modulok kezelése felhasználói felületen.

Ez a cikk bemutatja, hogyan használhatja a Visual Studio 2019 Azure IoT Edge-eszközeit a IoT Edge modulok fejlesztéséhez. Azt is megtudhatja, hogyan helyezheti üzembe a projektet a Azure IoT Edge eszközön. A Visual Studio 2019 jelenleg a C és C# nyelven írt modulok támogatását biztosítja. A támogatott eszközök architektúrái a következők: Windows x64 és Linux x64 vagy ARM32. További információ a támogatott operációs rendszerekről, nyelvekről és architektúráról: [nyelvi és architektúra-támogatás](module-development.md#language-and-architecture-support).
  
## <a name="prerequisites"></a>Előfeltételek

Ez a cikk azt feltételezi, hogy egy Windows rendszert futtató számítógépet vagy virtuális gépet használ fejlesztői számítógépként. Windows rendszerű számítógépeken Windows-és Linux-modulokat is fejleszthet. Windows-modulok fejlesztéséhez használjon 1809/Build 17763 vagy újabb verziót futtató Windows-számítógépet. Linux-modulok fejlesztéséhez használjon olyan Windows-számítógépet, amely megfelel a [Docker Desktop követelményeinek](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install).

Mivel ez a cikk a Visual Studio 2019-et használja fő fejlesztői eszközként, telepítse a Visual studiót. Győződjön meg róla, hogy a Visual Studio 2019-es verziójában az **Azure-fejlesztés** és az **asztali fejlesztés C++** számítási feladatait tartalmazza. A [Visual Studio 2019](/visualstudio/install/modify-visual-studio?view=vs-2019&preserve-view=true) a szükséges számítási feladatok hozzáadásához is módosítható.

Miután a Visual Studio 2019 elkészült, a következő eszközökre és összetevőkre is szüksége lesz:

- Töltse le és telepítse [Azure IoT Edge eszközöket](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) a Visual Studio Marketplace-ből, és hozzon létre egy IoT Edge projektet a visual Studio 2019-ben.

> [!TIP]
> Ha a Visual Studio 2017-at használja, töltse le és telepítse [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) for vs 2017 alkalmazást a Visual Studio piactérről

- Töltse le és telepítse a [Docker Community Editiont](https://docs.docker.com/install/) a fejlesztői gépen a modul lemezképének elkészítéséhez és futtatásához. A Docker CE-t úgy kell beállítania, hogy Linux-tároló módban vagy Windows-tároló módban fusson.

- Állítsa be a helyi fejlesztési környezetet a IoT Edge megoldás hibakereséséhez, futtatásához és teszteléséhez az [Azure IoT EdgeHub dev Tool](https://pypi.org/project/iotedgehubdev/)telepítésével. Telepítse a [Pythont (2.7/3.6 +) és a pip](https://www.python.org/) -et, majd telepítse a **iotedgehubdev** csomagot a következő parancs futtatásával a terminálon. Győződjön meg arról, hogy az Azure IoT EdgeHub dev Tool verziója nagyobb, mint a 0.3.0.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```

- A tárház klónozásával telepítse a Vcpkg Library Managert, majd telepítse a Windowshoz készült **Azure-IOT-SDK-c csomagot** .

  ```cmd
  git clone https://github.com/Microsoft/vcpkg
  cd vcpkg
  bootstrap-vcpkg.bat
  ```

  ```cmd
  vcpkg.exe install azure-iot-sdk-c:x64-windows
  vcpkg.exe --triplet x64-windows integrate install
  ```

- [Azure Container Registry](../container-registry/index.yml) vagy [Docker hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags).

  > [!TIP]
  > A Felhőbeli beállításjegyzék helyett használhat egy helyi Docker-beállításjegyzéket prototípus-és tesztelési célokra.

- A modul eszközön való teszteléséhez egy aktív IoT hub szükséges legalább egy IoT Edge eszközzel. Ha a számítógépet IoT Edge eszközként szeretné használni, kövesse a [Linux](quickstart-linux.md) vagy [Windows rendszerhez](quickstart.md)készült gyors útmutató lépéseit. Ha IoT Edge démont futtat a fejlesztői gépen, előfordulhat, hogy le kell állítania a EdgeHub és a EdgeAgent, mielőtt elkezdi a fejlesztést a Visual Studióban.

### <a name="check-your-tools-version"></a>Az eszközök verziójának keresése

1. A **bővítmények** menüben válassza a **Bővítmények kezelése** lehetőséget. Bontsa ki a **telepített > eszközöket** , és megtalálhatja a Visual Studio és **a Cloud Explorer** **Azure IoT Edge eszközeit** .

1. Jegyezze fel a telepített verziót. Ezt a verziót a Visual Studio Marketplace legújabb verziójának használatával hasonlíthatja össze ([Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS2019), [Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools))

1. Ha a verzió régebbi, mint a Visual Studio Marketplace-en elérhető verziója, frissítse az eszközeit a Visual Studióban, ahogy az a következő szakaszban is látható.

### <a name="update-your-tools"></a>Az eszközök frissítése

1. A **Bővítmények kezelése** ablakban bontsa ki a **frissítések > a Visual Studio Marketplace** elemet, válassza a **Azure IoT Edge Tools** vagy **a Cloud Explorer for Visual Studio** lehetőséget, majd válassza a **frissítés** lehetőséget.

1. Az eszközök frissítésének letöltése után a Visual Studio bezárásával aktiválja az eszközök frissítését a VSIX telepítő használatával.

1. A telepítőben kattintson az **OK gombra** a kezdéshez, majd **módosítsa** az eszközök frissítéséhez.

1. A frissítés befejezése után kattintson a **Bezárás gombra** , és indítsa újra a Visual studiót.

### <a name="create-an-azure-iot-edge-project"></a>Azure IoT Edge projekt létrehozása

A Visual Studióban Azure IoT Edge Project sablon egy olyan projektet hoz létre, amely az Azure IoT Hub Azure IoT Edge eszközeire telepíthető. Először hozzon létre egy Azure IoT Edge megoldást, majd hozza létre a megoldás első modulját. Minden IoT Edge megoldás több modult is tartalmazhat.

> [!TIP]
> A Visual Studio által létrehozott IoT Edge projekt szerkezete nem ugyanaz, mint a Visual Studio Code-ban.

1. A Visual Studio új projekt párbeszédpanelen keresse meg és válassza ki **Azure IoT Edge** projektet, majd kattintson a **tovább** gombra. A Project Configuration (projekt konfigurációja) ablakban adja meg a projekt nevét, és adja meg a helyet, majd válassza a **Létrehozás** lehetőséget. Az alapértelmezett projekt neve **AzureIoTEdgeApp1**.

   ![Új projekt létrehozása](./media/how-to-visual-studio-develop-csharp-module/create-new.png)

1. A **IoT Edge alkalmazás és modul hozzáadása** ablakban válassza a **C# modul** vagy a **C modul** lehetőséget, majd adja meg a modul nevét és a modul rendszerkép-tárházát. A Visual Studio automatikusan feltölti a modul nevét a **localhost: 5000/<a modul \> nevével**. Cserélje le a saját beállításjegyzék-adataira. Ha a teszteléshez helyi Docker-beállításjegyzéket használ, a **localhost** rendben van. Ha Azure Container Registry használ, használja a bejelentkezési kiszolgálót a beállításjegyzék beállításai közül. A bejelentkezési kiszolgáló a következőhöz hasonló: **_\<registry name\>_ . azurecr.IO**. Csak cserélje le a **localhost: 5000** részét a karakterláncra, hogy a végeredmény a következőhöz hasonlítson: **\<*registry name*\> . azurecr.IO/ _\<your module name\>_**. Az alapértelmezett modul neve **IotEdgeModule1**

   ![Alkalmazás és modul hozzáadása](./media/how-to-visual-studio-develop-csharp-module/add-application-and-module.png)

1. Kattintson **az OK** gombra, ha a Azure IoT Edge-megoldást C# vagy C protokollt használó modullal szeretné létrehozni.

Most már rendelkezik egy **AzureIoTEdgeApp1. Linux. amd64** projekttel vagy egy **AzureIoTEdgeApp1. Windows. Amd64** projekttel, valamint egy **IotEdgeModule1** -projekttel a megoldásban. Minden **AzureIoTEdgeApp1** -projekt tartalmaz egy `deployment.template.json` fájlt, amely meghatározza a IoT Edge megoldásához felépíteni és telepíteni kívánt modulokat, valamint meghatározza a modulok közötti útvonalakat is. Az alapértelmezett megoldás egy **SimulatedTemperatureSensor** modult és egy **IotEdgeModule1** modult tartalmaz. A **SimulatedTemperatureSensor** modul szimulált értékeket hoz létre a **IotEdgeModule1** modulba, míg az **IotEdgeModule1** modul alapértelmezett kódja a közvetlenül az Azure IoT hubba érkező üzeneteket fogad.

A szimulált hőmérséklet-érzékelő működésének megtekintéséhez tekintse meg a [SimulatedTemperatureSensor. csproj forráskódot](https://github.com/Azure/iotedge/tree/master/edge-modules/SimulatedTemperatureSensor).

A **IotEdgeModule1** -projekt egy .net Core 2,1 Console-alkalmazás, ha C#-modul. A Windows-tárolóval vagy a Linux-tárolóval futó IoT Edge eszközhöz szükséges Docker-fájlokat tartalmazza. A `module.json` fájl a modul metaadatait írja le. Az Azure IoT Device SDK-t függőségként tároló tényleges modul-kód a `Program.cs` vagy a `main.c` fájlban található.

## <a name="develop-your-module"></a>A modul fejlesztése

A megoldás alapértelmezett moduljának kódja a **IotEdgeModule1**  >  **program. cs** (C#) vagy a **Main. c** (c) esetében található. A rendszer úgy állítja be a modult és a `deployment.template.json` fájlt, hogy felépítse a megoldást, leküldi a tároló-beállításjegyzékbe, és üzembe helyezi azt egy eszközön, hogy bármilyen kód megérintése nélkül el tudja indítani a tesztelést. A modul úgy lett felépítve, hogy bejegyezze a forrás (ebben az esetben az adatokat szimuláló **SimulatedTemperatureSensor** modul) adatait, és átadja az Azure IoT hubnak.

Ha készen áll a modul sablonjának testre szabására a saját kódjával, az [Azure IoT hub SDK](../iot-hub/iot-hub-devguide-sdks.md) -k segítségével olyan modulokat építhet ki, amelyek a IoT-megoldások, például a biztonság, az eszközkezelés és a megbízhatóság terén szükségesek.

## <a name="initialize-iotedgehubdev-with-iot-edge-device-connection-string"></a>Iotedgehubdev inicializálása IoT Edge eszköz-csatlakoztatási karakterlánccal

1. Másolja a Visual Studio Cloud Explorerben a IoT Edge eszközhöz tartozó kapcsolatok karakterláncát az **elsődleges kapcsolatok sztringből** . Ügyeljen arra, hogy ne másolja a nem peremhálózati eszközhöz tartozó kapcsolódási karakterláncot, mert egy IoT Edge eszköz ikonja eltér a nem peremhálózati eszköz ikonjának.

   ![Edge-eszköz csatlakoztatási karakterláncának másolása](./media/how-to-visual-studio-develop-csharp-module/copy-edge-conn-string.png)

1. A **Tools (eszközök** ) menüben válassza a **Azure IoT Edge Tools**  >  **Setup IoT Edge Simulator** elemet, illessze be a kapcsolatok karakterláncát, és kattintson **az OK** gombra.

   ![Az Edge-kapcsolatok karakterláncának megnyitására szolgáló ablak megnyitása](./media/how-to-visual-studio-develop-csharp-module/set-edge-conn-string.png)

1. Adja meg a kapcsolatok karakterláncát az első lépésnél, majd kattintson **az OK gombra**.

> [!NOTE]
> Ezeket a lépéseket csak egyszer kell követnie a fejlesztői számítógépen, mivel a rendszer automatikusan alkalmazza az eredményeket az összes további Azure IoT Edge megoldásra. Ez az eljárás akkor követhető újra, ha másik kapcsolódási sztringre kell váltania.

## <a name="build-and-debug-single-module"></a>Egyetlen modul létrehozása és hibakeresése

Az egyes modulokat általában tesztelni és hibakeresést végezni, mielőtt egy teljes megoldáson belül futtatja, több modullal.

1. A **megoldáskezelő** kattintson a jobb gombbal a **IotEdgeModule1** elemre, és válassza a **beállítás indítási projektként** lehetőséget a helyi menüben.

   ![Indítási projekt beállítása](./media/how-to-visual-studio-develop-csharp-module/module-start-up-project.png)

1. Nyomja le az **F5** billentyűt, vagy kattintson az alábbi gombra a modul futtatásához; &ndash;az első alkalommal 10 20 másodpercig is eltarthat.

   ![Modul futtatása](./media/how-to-visual-studio-develop-csharp-module/run-module.png)

1. Ha a modul inicializálása sikeresen megtörtént, megjelenik egy .NET Core Console-alkalmazás.

   ![Modul fut](./media/how-to-visual-studio-develop-csharp-module/single-module-run.png)

1. Ha C# nyelven fejleszt, állítson be egy töréspontot a `PipeMessage()` **program. cs**; függvényben, ha C-t használ, állítson be egy töréspontot a `InputQueue1Callback()` **Main. C**-ben lévő függvényben. Ezt követően a következő parancs futtatásával tesztelheti azt egy **git bash** -vagy **WSL bash** -rendszerhéjban. (A parancsot nem futtathatja `curl` powershellből vagy parancssorból.)

    ```bash
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   ![Egyetlen modul hibakeresése](./media/how-to-visual-studio-develop-csharp-module/debug-single-module.png)

    A töréspontot aktiválnia kell. A Visual Studio **Locals** ablakban is megtekintheti a változókat.

   > [!TIP]
   > A [Poster](https://www.getpostman.com/) vagy más API-eszközöket is használhatja az üzenetek küldéséhez a helyett `curl` .

1. Nyomja le a **CTRL + F5** billentyűkombinációt, vagy kattintson a Leállítás gombra a hibakeresés leállításához.

## <a name="build-and-debug-iot-edge-solution-with-multiple-modules"></a>IoT Edge-megoldás létrehozása és hibakeresése több modullal

Miután befejezte egyetlen modul fejlesztését, érdemes lehet egy teljes megoldást futtatni és hibakeresést végezni több modullal.

1. A **Megoldáskezelőban** adjon hozzá egy második modult a megoldáshoz, kattintson a jobb gombbal a **AzureIoTEdgeApp1** elemre, és válassza az   >  **új IoT Edge modul** hozzáadása lehetőséget. A második modul alapértelmezett neve **IotEdgeModule2** , és egy másik pipe-modulként fog működni.

1. Nyissa meg a fájlt `deployment.template.json` , és látni fogja, hogy a **IotEdgeModule2** hozzá lett adva a **modulok** szakaszhoz. Cserélje le az **útvonalak** szakaszt az alábbira. Ha testre szabta a modulok nevét, ügyeljen rá, hogy a neveket a megfelelő értékre frissítse.

    ```json
        "routes": {
          "IotEdgeModule1ToIoTHub": "FROM /messages/modules/IotEdgeModule1/outputs/* INTO $upstream",
          "sensorToIotEdgeModule1": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IotEdgeModule1/inputs/input1\")",
          "IotEdgeModule2ToIoTHub": "FROM /messages/modules/IotEdgeModule2/outputs/* INTO $upstream",
          "sensorToIotEdgeModule2": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IotEdgeModule2/inputs/input1\")"
        },
    ```

1. Kattintson a jobb gombbal a **AzureIoTEdgeApp1** elemre, és válassza a **beállítás indítási projektként** lehetőséget a helyi menüben.

1. Hozza létre a töréspontokat, majd nyomja le az **F5** billentyűt egyszerre több modul futtatásához és hibakereséséhez. Látnia kell több .NET Core Console-alkalmazást, amely az egyes ablakokat egy másik modult jelképezi.

   ![Több modul hibakeresése](./media/how-to-visual-studio-develop-csharp-module/debug-multiple-modules.png)

1. A hibakeresés leállításához nyomja le a **CTRL + F5** billentyűkombinációt, vagy kattintson a Leállítás gombra.

## <a name="build-and-push-images"></a>Lemezképek készítése és leküldése

1. Győződjön meg arról, hogy a **AzureIoTEdgeApp1** az indítási projekt. Válassza a **hibakeresés** vagy a **kiadás** lehetőséget a modul lemezképei számára felépített konfigurációként.

    > [!NOTE]
    > A **hibakeresés** kiválasztásakor a Visual Studio `Dockerfile.(amd64|windows-amd64).debug` Docker-lemezképek készítésére használja. Ez magában foglalja a .NET Core parancssori hibakereső VSDBG a tároló rendszerképében a létrehozás során. Az éles használatra kész IoT Edge modulok esetében javasoljuk, hogy a **kiadási** konfigurációt használja, amely `Dockerfile.(amd64|windows-amd64)` VSDBG nélkül használatos.

1. Ha olyan privát beállításjegyzéket használ, mint például az Azure Container Registry (ACR), a következő Docker-parancs használatával jelentkezzen be.  A felhasználónevet és a jelszót a beállításjegyzék **hozzáférési kulcsok** lapjáról kérheti le a Azure Portal. Ha helyi beállításjegyzéket használ, [helyi beállításjegyzéket is futtathat](https://docs.docker.com/registry/deploying/#run-a-local-registry).

    ```cmd
    docker login -u <ACR username> -p <ACR password> <ACR login server>
    ```

1. Ha olyan privát beállításjegyzéket használ, mint például a Azure Container Registry, a beállításjegyzék bejelentkezési adatait fel kell vennie a fájlban található futtatókörnyezeti beállításokba `deployment.template.json` . Cserélje le a helyőrzőket a tényleges ACR-rendszergazdai felhasználónevére, jelszavára és beállításjegyzék-nevére.

    ```json
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": "",
            "registryCredentials": {
              "registry1": {
                "username": "<username>",
                "password": "<password>",
                "address": "<registry name>.azurecr.io"
              }
            }
          }
    ```

1. A **megoldáskezelő** kattintson a jobb gombbal a **AzureIoTEdgeApp1** elemre, és válassza a **IoT Edge modulok létrehozása és leküldése** lehetőséget, hogy minden modulhoz létrehozza és leküldje a Docker-rendszerképet.

## <a name="deploy-the-solution"></a>A megoldás üzembe helyezése

Az IoT Edge-eszköz beállításához használt rövid útmutatóban egy modult helyezett üzembe az Azure Portal segítségével. A Visual studióhoz készült Cloud Explorer használatával a modulokat is üzembe helyezheti. Már rendelkezik egy, a forgatókönyvhöz előkészített üzembe helyezési jegyzékfájlval, a `deployment.json` fájlnak és az összes szükséges műveletnek ki kell választania egy eszközt az üzemelő példány fogadásához.

1. Nyissa meg a **Cloud Explorert** a   >  **Cloud Explorer** megtekintése elemre kattintva. Győződjön meg arról, hogy bejelentkezett a Visual Studio 2019-ba.

1. A **Cloud Explorerben** bontsa ki az előfizetését, keresse meg az Azure-IoT hub és a telepíteni kívánt Azure IoT Edge eszközt.

1. Kattintson a jobb gombbal a IoT Edge eszközre, és hozzon létre egy központi telepítést. Navigáljon a platformhoz konfigurált üzembe helyezési jegyzékfájlhoz a Visual Studio-megoldás **konfigurációs** mappájában, például: `deployment.arm32v7.json` .

1. Kattintson a refresh (frissítés) gombra, és tekintse meg az új modulokat, amelyek a **SimulatedTemperatureSensor** modullal és a **$edgeAgent** és **$edgeHubval** együtt futnak.

## <a name="view-generated-data"></a>A létrejött adatok megtekintése

1. Egy adott IoT-Edge eszköz D2C-üzenetének figyeléséhez válassza ki azt az IoT hub-ban a **Cloud Explorerben** , majd a **művelet** ablakban kattintson a **beépített esemény végpontjának figyelése** elemre.

1. A figyelési információk leállításához jelölje be a **beépített esemény végpontjának figyelése** a **műveleti** ablakban jelölőnégyzetet.

## <a name="next-steps"></a>Következő lépések

Egyéni modulok fejlesztése a IoT Edge-eszközökhöz, az [Azure IoT hub SDK-k megismerése és használata](../iot-hub/iot-hub-devguide-sdks.md).