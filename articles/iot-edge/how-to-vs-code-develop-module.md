---
title: Modulok fejlesztése és hibakeresése Azure IoT Edge | Microsoft Docs
description: A Visual Studio Code használatával modulokat fejleszthet, fejleszthet és hibakeresést Azure IoT Edge C#, Python, Node.js, Java vagy C használatával
services: iot-edge
keywords: ''
author: kgremban
ms.author: kgremban
ms.date: 08/07/2019
ms.topic: conceptual
ms.service: iot-edge
ms.custom: devx-track-js
ms.openlocfilehash: 7cc5061911ffc2cbc91dfa8c0d2fd1a6e59ff1a1
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107875664"
---
# <a name="use-visual-studio-code-to-develop-and-debug-modules-for-azure-iot-edge"></a>A Visual Studio Code használata modulok fejlesztéséhez és hibakereséséhez Azure IoT Edge

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Az üzleti logikát modulokat is Azure IoT Edge. Ez a cikk bemutatja, hogyan használhatja a Visual Studio Code-et fő eszközként a modulok fejlesztéséhez és hibakereséséhez.

A C#, Node.js vagy Java nyelven írt modulok hibakeresésének két módja van a Visual Studio Code-ban: csatolhat egy folyamatot egy modultárolóban, vagy elindíthatja a modul kódját hibakeresési módban. A Pythonban vagy C-ben írt modulok hibakereséséhez csak Linux amd64-tárolókban található folyamathoz csatolhat.

Ha nem ismeri a Visual Studio Code hibakeresési képességeit, olvassa el a [hibakeresést.](https://code.visualstudio.com/Docs/editor/debugging)

Ez a cikk több nyelven, több architektúrához elérhető modulok fejlesztésére és hibakeresésére vonatkozó utasításokat tartalmaz. A Visual Studio Code jelenleg C#, C, Python, Node.js és Java nyelven írt modulokhoz nyújt támogatást. A támogatott eszközarchitektúrák az X64 és az ARM32. További információ a támogatott operációs rendszerekről, nyelvekről és architektúrákról: [Nyelvi és architektúratámogatás.](module-development.md#language-and-architecture-support)

>[!NOTE]
>A Linux ARM64-eszközök fejlesztésének és hibakeresésének támogatása nyilvános előzetes [verzióban érhető el.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) További információ: [ARM64-modulok fejlesztése és hibakeresése IoT Edge a Visual Studio (előzetes verzió) modulban.](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview)

## <a name="prerequisites"></a>Előfeltételek

Fejlesztési gépként Windows, macOS vagy Linux rendszert futtató számítógépet vagy virtuális gépet is használhat. Windows rendszerű számítógépeken Windows- vagy Linux-modulokat fejleszthet. Windows-modulok fejlesztéséhez használja az 1809/build 17763-as vagy újabb verzióját futtató Windows-számítógépet. Linux-modulok fejlesztéséhez olyan Windows-számítógépet használjon, amely megfelel a [Docker Desktop követelményeinek.](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install)

Először telepítse Visual Studio Code-kódot, majd adja hozzá a következő bővítményeket: [](https://code.visualstudio.com/)

- [Azure IoT-eszközök](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
- [Docker-bővítmény](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
- Visual Studio a fejlesztés nyelvére jellemző bővítmény(nek):
  - C#, beleértve a Azure Functions: [C#-bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
  - Python: [Python-bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  - Java: [Java-bővítménycsomag Visual Studio Code-hoz](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack)
  - C: [C/C++ bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools)

A modul fejlesztéséhez további nyelvspecifikus eszközöket is telepítenie kell:

- C#, beleértve a Azure Functions: [.NET Core 2.1 SDK](https://dotnet.microsoft.com/download/dotnet/2.1)

- Python: [Python](https://www.python.org/downloads/) és [Pip](https://pip.pypa.io/en/stable/installing/#installation) a Python-csomagok telepítéséhez (általában a Python-telepítés része).

- Node.js: [Node.js. ](https://nodejs.org) Emellett telepítenie kell a [Yeomant](https://www.npmjs.com/package/yo) és a Azure IoT Edge Node.js [Modulgenerátort is.](https://www.npmjs.com/package/generator-azure-iot-edge-module)

- Java: [Java SE Development Kit 10](/azure/developer/java/fundamentals/java-jdk-long-term-support) és [Maven](https://maven.apache.org/). Úgy kell beállítania [a környezeti `JAVA_HOME` változót,](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) hogy a JDK-telepítésre mutasson.

A modul rendszerképének felépítéséhez és üzembe helyezéséhez a Dockerre lesz szüksége a modul rendszerképének és a modul rendszerképét tároló tároló regisztrációs adatbázisnak a felépítéséhez:

- [Docker Community Edition a](https://docs.docker.com/install/) fejlesztői gépen.

- [Azure Container Registry](../container-registry/index.yml) vagy [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)

    > [!TIP]
    > A helyi Docker-beállításjegyzéket prototípus- és tesztelési célokra használhatja felhőbeli beállításjegyzék helyett.

Hacsak nem C-ben fejleszt modult, szüksége lesz a Python-alapú [Azure IoT EdgeHub fejlesztői](https://pypi.org/project/iotedgehubdev/) eszközre is, hogy beállítsa a helyi fejlesztési környezetet a megoldás hibakereséséhez, futtatásához és IoT Edge teszteléséhez. Ha még nem tette meg, telepítse a [Pythont (2.7/3.6/3.7)](https://www.python.org/) és a Pipet, majd telepítse az **iotedgehubdev** parancsot a parancs terminálon való futtatásával.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```
   
> [!NOTE]
> Az iotedgehubdev jelenleg olyan docker-py kódtárat használ, amely nem kompatibilis a Python 3.8-as verzióval.
>
> Ha több Pythonnal is van, beleértve az előre telepített Python 2.7-et (például Ubuntu vagy macOS rendszeren), győződjön meg arról, hogy a megfelelőt használja, vagy az `pip` `pip3` **iotedgehubdev** telepítéséhez

A modul eszközön való teszteléséhez szüksége lesz egy aktív IoT Hubra, amely legalább egy IoT Edge rendelkezik. Ha számítógépét linuxos vagy IoT Edge eszközként használni, kövesse a [Linux](quickstart-linux.md) vagy Windows rendszerről történő rövid útmutató [lépéseit.](quickstart.md) Ha egy démont IoT Edge a fejlesztői gépen, előfordulhat, hogy le kell állítania az EdgeHubot és az EdgeAgentet, mielőtt továbblép a következő lépésre.

## <a name="create-a-new-solution-template"></a>Új megoldássablon létrehozása

A következő lépések azt mutatják be, hogyan hozhat létre egy IoT Edge-modult a kívánt fejlesztési nyelven (beleértve a C#-ban írt Azure Functions-modult) a Visual Studio Code és a Azure IoT Tools. Először létrehoz egy megoldást, majd létrehozza a megoldás első modulját. Minden megoldás több modult is tartalmazhat.

1. Válassza **a View** Command Palette  >  **(Parancskatapaletta megtekintése) lehetőséget.**

1. A parancskataján belül írja be és futtassa a **következő parancsot: Azure IoT Edge: New IoT Edge Solution**.

   ![Új IoT Edge futtatása](./media/how-to-develop-csharp-module/new-solution.png)

1. Keresse meg azt a mappát, ahol létre szeretné hozni az új megoldást, majd válassza a **Mappa kiválasztása lehetőséget.**

1. Adja meg a megoldás nevét.

1. Válasszon ki egy modulsablont, hogy a kívánt fejlesztési nyelv legyen a megoldás első modulja.

1. Adja meg a modul nevét. Válasszon egy egyedi nevet a tárolójegyzékben.

1. Adja meg a modul rendszerkép-adattárának nevét. Visual Studio Code automatikusan kitölti a modul nevét **a localhost:5000/<a \> modulnévvel.** Cserélje le a saját beállításjegyzék-adataira. Ha helyi Docker-beállításjegyzéket használ a teszteléshez, akkor **a localhost** megfelelő. Ha a Azure Container Registry, használja a regisztrációs adatbázis beállításaiból származó bejelentkezési kiszolgálót. A bejelentkezési kiszolgáló így néz ki: **_\<registry name\>_ .azurecr.io.** Csak a **sztring localhost:5000** részét cserélje le, hogy a végeredmény **\<*registry name*\> .azurecr.io/. _\<your module name\>_**

   ![Docker-rendszerkép adattárának megadása](./media/how-to-develop-csharp-module/repository.png)

Visual Studio Code átveszi a megadott információkat, létrehoz egy IoT Edge megoldást, majd betölti egy új ablakban.

A megoldás négy elemet tartalmaz:

- A **.vscode mappa** hibakeresési konfigurációkat tartalmaz.

- A **modules mappa** minden modulhoz tartalmaz almappákat.  Az egyes modulok mappájában található egy **fájl(** module.js, amely a modulok építését és üzembe helyezését szabályozza.  Ezt a fájlt módosítani kell, hogy a modul üzembe helyezési tárolójegyzékét localhostról távoli beállításjegyzékre módosítsuk. Ezen a ponton csak egy modulja van.  A parancskatapaletta további elemekkel is bővülhet a Azure IoT Edge: Add IoT Edge Module (Modul **hozzáadása) paranccsal.**

- Az **.env fájl** a környezeti változókat sorolja fel. Ha Azure Container Registry az Ön regisztrációs adatbázisa, akkor a Azure Container Registry felhasználónevével és jelszavával fog használni.

  > [!NOTE]
  > A környezeti fájl csak akkor jön létre, ha a modulhoz képtárat ad meg. Ha elfogadta a localhost alapértelmezett értékét a helyi teszteléshez és hibakereséshez, akkor nem kell környezeti változókat deklarálni.

- A **deployment.template.js** az új modult a **SimulatedTemperatureSensor** mintamodullal együtt listázza, amely a teszteléshez használható adatokat szimulálja. Az üzembe helyezési jegyzékekkel kapcsolatos további információkért lásd: Az üzembe helyezési jegyzékek használata modulok üzembe helyezésére és [útvonalak létrehozására.](module-composition.md)

A szimulált hőmérséklet modul működését a [SimulatedTemperatureSensor.csproj forráskódban láthatja.](https://github.com/Azure/iotedge/tree/master/edge-modules/SimulatedTemperatureSensor)

## <a name="add-additional-modules"></a>További modulok hozzáadása

Ha további modulokat szeretne hozzáadni a megoldáshoz, futtassa a **Azure IoT Edge: Add IoT Edge Module** (Modul hozzáadása) parancsot a parancskatapaletta használatával. A jobb gombbal a **modules** mappára vagy a fájlra is kattinthat a Visual Studio Code Explorer nézetben, majd válassza az Add IoT Edge Module (Modul `deployment.template.json` **hozzáadása) lehetőséget.**

## <a name="develop-your-module"></a>A modul fejlesztése

A megoldás alapértelmezett modulkódja a következő helyen található:

- Azure-függvény (C#): **> *&lt; &gt; modul neve a* modul  >  *&lt; neve &gt;*.cs**
- C#: **modulok > a modul *&lt; nevét &gt;* > Program.cs fájlban**
- Python: **a > *&lt; modulnevét &gt; > main.py***
- Node.js: modulok **> *&lt; a modul nevét &gt; > app.js***
- Java: **modulok> *&lt; &gt;* a modul nevét > src > fő > java > com > edgemodulemodules > App.java**
- C: **modulok > a modul *&lt; nevét &gt;* > main.c fájlban**

A modul és deployment.template.jsa fájlban beállítás azért van beállítva, hogy felépítse a megoldást, leküldse a tároló-beállításjegyzékbe, és üzembe helyeződve elindítsa a tesztelést a kód érintése nélkül. A modul úgy van felépítve, hogy egyszerűen bemenetet vegyen egy forrásból (ebben az esetben a SimulatedTemperatureSensor modulból, amely adatokat szimulál), és átküldje azt a IoT Hub.

Ha készen áll a sablon saját kóddal való testreszabására, az [Azure IoT Hub-szoftverekkel](../iot-hub/iot-hub-devguide-sdks.md) olyan modulokat építhet, amelyek az IoT-megoldások, például a biztonság, az eszközkezelés és a megbízhatóság kulcsfontosságú igényeit elégék ki.

## <a name="debug-a-module-without-a-container-c-nodejs-java"></a>Modul hibakeresése tároló nélkül (C#, Node.js, Java)

Ha C#, Node.js vagy Java nyelven fejleszt, a modulnak egy **ModuleClient** objektumot kell használnia az alapértelmezett modulkódban, hogy elindítsa, futtassa és irányítsa az üzeneteket. A bemeneti csatorna alapértelmezett **bemenetét1** is használhatja a művelethez, amikor a modul üzeneteket fogad.

### <a name="set-up-iot-edge-simulator-for-iot-edge-solution"></a>A szimulátor IoT Edge beállítása IoT Edge megoldáshoz

A fejlesztői gépen elindíthat egy IoT Edge-szimulátort az IoT Edge biztonsági démon telepítése helyett, így futtathatja IoT Edge megoldását.

1. A bal oldali Eszközkezelőben kattintson a jobb gombbal az IoT Edge-eszközazonosítóra, majd válassza a **IoT Edge-szimulátor** beállítása lehetőséget a szimulátor eszközkapcsolati sztring használatával való indításához.
1. A folyamat részleteit az integrált terminálon IoT Edge láthatja, hogy a szimulátor sikeresen be lett állítva.

### <a name="set-up-iot-edge-simulator-for-single-module-app"></a>Egyszerű IoT Edge beállítása egymodulos alkalmazáshoz

A szimulátor beállításához és futtatásához futtassa a **Azure IoT Edge: Start IoT Edge Hub Simulator for Single Module** (Központszimulátor IoT Edge modulhoz) parancsot a Visual Studio Code parancskatajánján. Amikor a rendszer kéri, használja az alapértelmezett modulkód **input1** értékét (vagy a kód egyenértékű értékét) az alkalmazás bemeneti neveként. A parancs aktiválja az **iotedgehubdev** parancssori felületét, majd elindítja az IoT Edge szimulátort és egy tesztelési segédmodul-tárolót. Az alábbi kimeneteket az integrált terminálon láthatja, ha a szimulátor sikeresen elindult egymodulos módban. Egy parancs is `curl` megjelenik, amely az üzenetek elküldését segíti. Erre később még szüksége lesz.

   ![Egyszerű IoT Edge beállítása egymodulos alkalmazáshoz](media/how-to-develop-csharp-module/start-simulator-for-single-module.png)

   Az Visual Studio Code Docker Explorer nézetében megtekintheti a modul futási állapotát.

   ![Szimulátormodul állapota](media/how-to-develop-csharp-module/simulator-status.png)

   Az **edgeHubDev** tároló a helyi tároló IoT Edge része. Futhat a fejlesztői gépen a IoT Edge biztonsági démon nélkül, és környezeti beállításokat biztosít a natív modulalkalmazáshoz vagy modultárolókhoz. A **bemeneti tároló** REST API-kat is elérhetővé teszi az üzenetek hídként való használatával a modul cél bemeneti csatornájára.

### <a name="debug-module-in-launch-mode"></a>Hibakeresési modul indítási módban

1. Készítse elő a környezetet a fejlesztési nyelv követelményeinek megfelelő hibakeresésre, állítson be egy töréspontot a modulban, és válassza ki a használni kívánt hibakeresési konfigurációt:
   - **C#**
     - A Visual Studio Code integrált ***&lt; &gt;*** termináljában módosítsa a könyvtárat a modulnév mappára, majd futtassa a következő parancsot a .NET Core-alkalmazás felépítéséhez.

       ```cmd
       dotnet build
       ```

     - Nyissa meg a `Program.cs` fájlt, és adjon hozzá egy töréspontot.

     - Navigáljon a Visual Studio Code Debug (Kód hibakeresése) nézethez a **View (Nézet) > Debug (Hibakeresés) lehetőség kiválasztásával.** Válassza ki a hibakeresési **_&lt; konfigurációt, &gt; amely_** a modul neve Helyi hibakeresés (.NET Core) a legördülő menüből.

        > [!NOTE]
        > Ha a .NET Core nem konzisztens a programban található program elérési úttal, manuálisan kell frissítenie a program elérési útját, hogy megegyezzon a `TargetFramework` `launch.json` `launch.json` .csproj fájlban található elérési úttal, hogy az Visual Studio Code sikeresen elindítsa a `TargetFramework` programot.

   - **Node.js**
     - A Visual Studio Code integrált terminálján módosítsa ***&lt; &gt;*** a könyvtárat a modulnév mappára, majd futtassa a következő parancsot a Node-csomagok telepítéséhez

       ```cmd
       npm install
       ```

     - Nyissa meg a `app.js` fájlt, és adjon hozzá egy töréspontot.

     - Navigáljon a Visual Studio Code hibakeresési nézethez a Hibakeresési nézet **> kiválasztásával.** Válassza ki a hibakeresési **_&lt; konfigurációt &gt;_** a modul neve Helyi hibakeresés (Node.js) a legördülő menüből.
   - **Java**
     - Nyissa meg a `App.java` fájlt, és adjon hozzá egy töréspontot.

     - Navigáljon a Visual Studio Code hibakeresési nézethez a Hibakeresési nézet **> kiválasztásával.** Válassza ki a hibakeresési **_&lt; konfigurációt a &gt;_** modul neve Helyi hibakeresés (Java) a legördülő menüből.

1. A **hibakeresési munkamenethez kattintson** a Start Debugging (Hibakeresési indítás) elemre, vagy nyomja le az **F5** billentyűt.

1. A Visual Studio Code integrált terminálon futtassa **a** következő parancsot, hogy egy Hello World üzenetet küldjön a modulnak. Ez a parancs jelenik meg az előző lépésekben a szimulátor IoT Edge beállításakor.

    ```bash
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   > [!NOTE]
   > Ha Windowst használ, győződjön meg arról, hogy a Visual Studio Code integrált terminálja a **Git Bash** vagy a **WSL Bash.** A parancsot nem `curl` futtathatja PowerShellből vagy parancssorból.
   > [!TIP]
   > A PostMan vagy más [API-eszközök](https://www.getpostman.com/) használatával is küldhet üzeneteket a `curl` helyett.

1. A Visual Studio Code Debug (Kód hibakeresése) nézetben a változók a bal oldali panelen fognak látszatra.

1. A hibakeresési munkamenet leállításhoz válassza a Leállítás gombot, vagy nyomja le a **Shift + F5** billentyűkombinációt, majd futtassa a **Azure IoT Edge: Stop IoT Edge Simulator** parancsot a parancskataján belül a szimulátor leállításhoz és a tisztításhoz.

## <a name="debug-in-attach-mode-with-iot-edge-simulator-c-nodejs-java-azure-functions"></a>Hibakeresés csatolási módban IoT Edge szimulátorral (C#, Node.js, Java, Azure Functions)

Az alapértelmezett megoldás két modult tartalmaz, az egyik egy szimulált hőmérséklet-érzékelő modul, a másik pedig a pipe modul. A szimulált hőmérséklet-érzékelő üzeneteket küld a folyamatmodulnak, majd az üzeneteket a rendszer a IoT Hub. A létrehozott modulmappában számos Docker-fájl található a különböző tárolótípusokhoz. A modul teszteléshez való buildeléséhez használja a **.debug** kiterjesztésű fájlok bármelyikét.

A csatolási módban a hibakeresés jelenleg csak a következőképpen támogatott:

- A C#-modulok, beleértve a Azure Functions modulokat is, támogatják a linuxos amd64-tárolókban való hibakeresést
- Node.js modulok támogatják a hibakeresést Linux amd64- és arm32v7-tárolókban és Windows amd64-tárolókban
- A Java-modulok támogatják a hibakeresést Linux amd64 és arm32v7 tárolókban

> [!TIP]
> Az alapértelmezett platformra vonatkozó beállítások között a IoT Edge kód állapotsávján található elemre Visual Studio válthat.

### <a name="set-up-iot-edge-simulator-for-iot-edge-solution"></a>Az IoT Edge szimulátor beállítása IoT Edge megoldáshoz

A fejlesztői gépen elindíthat egy IoT Edge-szimulátort az IoT Edge biztonsági démon telepítése helyett, hogy futtatni tudja IoT Edge megoldását.

1. A bal oldali Eszközkezelőben kattintson a jobb gombbal az IoT Edge-eszközazonosítójára, majd válassza a Setup IoT Edge Simulator (Szimulátor beállítása) **lehetőséget,** hogy elindítsa a szimulátort az eszköz kapcsolati sztringjével.

1. A folyamat részleteit az integrált terminálon IoT Edge láthatja, hogy a szimulátor sikeresen be lett állítva.

### <a name="build-and-run-container-for-debugging-and-debug-in-attach-mode"></a>Tároló összeállítása és futtatása csatolási módban való hibakereséshez és hibakereséshez

1. Nyissa meg a modulfájlt ( `Program.cs` , , vagy ), és adjon hozzá egy `app.js` `App.java` `<your module name>.cs` töréspontot.

1. A Visual Studio Code Explorer nézetben kattintson a jobb gombbal a megoldás fájlra, majd válassza a Megoldás létrehozása `deployment.debug.template.json` **és IoT Edge a Szimulátorban** lehetőséget. Az összes modultároló naplóját ugyanabban az ablakban figyelheti. A tároló állapotát a Docker-nézetben is megtekintheti.

   ![Változók figyelte](media/how-to-vs-code-develop-module/view-log.png)

1. Lépjen a Visual Studio Code Hibakeresés nézetére, és válassza ki a modul hibakeresési konfigurációs fájlját. A hibakeresési beállítás nevének hasonlónak kell lennie ***&lt; a modul nevéhez: &gt;* Távoli hibakeresés**

1. Válassza **a Start Debugging (Hibakeresés kezdete) lehetőséget,** vagy nyomja le **az F5 billentyűt.** Válassza ki a csatolás folyamatát.

1. A Visual Studio hibakeresési nézetben a változók a bal oldali panelen fognak látszatra.

1. A hibakeresési munkamenet leállításhoz először válassza a Leállítás gombot, vagy nyomja le a **Shift + F5** billentyűkombinációt, majd válassza a **Azure IoT Edge: Stop IoT Edge Simulator** lehetőséget a parancskatajánján.

> [!NOTE]
> Az előző példa bemutatja, hogyan lehet hibakeresést végezni IoT Edge modulokon a tárolókon. Elérhetővé tette a portokat a modul `createOptions` tárolóbeállításában. A modulok hibakeresésének befejezése után javasoljuk, hogy távolítsa el ezeket a portokat az éles használatra kész IoT Edge érdekében.
>
> A C#-ban írt modulok, például a Azure Functions esetében ez a példa a hibakeresési verzióján alapul, amely a .NET Core parancssori hibakeresőt (VSDBG) tartalmazza a tároló rendszerképében annak `Dockerfile.amd64.debug` kiépítésekor. A C#-modulok hibakeresése után javasoljuk, hogy a Dockerfile-t VSDBG nélkül, közvetlenül használja az éles használatra kész IoT Edge modulokhoz.

## <a name="debug-a-module-with-the-iot-edge-runtime"></a>Modul hibakeresése a IoT Edge használatával

Az egyes modulmappákban számos Docker-fájl található a különböző tárolótípusokhoz. A modul teszteléshez való buildeléséhez használja a **.debug** kiterjesztésű fájlok bármelyikét.

Ha ezzel a módszerrel hibakeresést futtat, a modulok a modul futtatókörnyezetén IoT Edge futnak. Az IoT Edge-eszköz és a Visual Studio Code ugyanazon a gépen, vagy általában a Visual Studio Code a fejlesztői gépen található, az IoT Edge-futtatókörnyezet és a modulok pedig egy másik fizikai gépen futnak. Az Visual Studio code-ból való hibakereséshez a következőt kell végeznie:

- Állítsa be IoT Edge eszközét, állítsa össze IoT Edge modul(okat) a **.debug** Dockerfile használatával, majd telepítse az IoT Edge eszközre.
- Tegye elérhetővé a modul IP-címét és portját, hogy a hibakereső csatolható legyen.
- Frissítse a(Visual Studio, hogy a kód csatolja a folyamatot a távoli `launch.json` gépen található tárolóban. Ez a fájl a munkaterület mappájában található, és minden alkalommal frissül, amikor új modult ad hozzá, amely támogatja `.vscode` a hibakeresést.

### <a name="build-and-deploy-your-module-to-the-iot-edge-device"></a>A modul összeállítása és üzembe helyezése a IoT Edge eszközön

1. A Visual Studio nyissa meg a modul rendszerképének hibakeresési verzióját tartalmazó fájlt a `deployment.debug.template.json` megfelelő értékekkel. `createOptions`

1. Ha Pythonban fejleszt modult, a folytatás előtt kövesse az alábbi lépéseket:
   - Nyissa meg a `main.py` fájlt, és adja hozzá ezt a kódot az importálási szakasz után:

      ```python
      import ptvsd
      ptvsd.enable_attach(('0.0.0.0',  5678))
      ```

   - Adja hozzá a következő egysoros kódot a hibakeresést végezni kívánt visszahíváshoz:

      ```python
      ptvsd.break_into_debugger()
      ```

     Ha például hibakeresést szeretne végezni a függvényen, szúrja be ezt a kódsort `receive_message_listener` az alábbi módon:

      ```python
      def receive_message_listener(client):
          ptvsd.break_into_debugger()
          global RECEIVED_MESSAGES
          while True:
              message = client.receive_message_on_input("input1")   # blocking call
              RECEIVED_MESSAGES += 1
              print("Message received on input1")
              print( "    Data: <<{}>>".format(message.data) )
              print( "    Properties: {}".format(message.custom_properties))
              print( "    Total calls received: {}".format(RECEIVED_MESSAGES))
              print("Forwarding message to output1")
              client.send_message_to_output(message, "output1")
              print("Message successfully forwarded")
      ```

1. A Visual Studio Code parancskatajánján:
   1. Futtassa a Azure IoT Edge: Build and Push IoT Edge solution (Build **és leküldéses IoT Edge megoldás).**

   1. Válassza ki `deployment.debug.template.json` a megoldás fájlját.

1. A **Azure IoT Hub Code** Explorer nézet Visual Studio Eszközök szakaszában:
   1. Kattintson a jobb gombbal egy IoT Edge eszközazonosítóra, majd válassza a **Központi telepítés létrehozása egyetlen eszközhöz lehetőséget.**

      > [!TIP]
      > Annak ellenőrzéséhez, hogy a kiválasztott eszköz egy IoT Edge-eszköz-e, jelölje ki a modulok listájának kibontásához, és ellenőrizze a $edgeHub **és** a **$edgeAgent.** Minden IoT Edge tartalmazza ezt a két modult.

   1. Lépjen a megoldás **konfigurációs** mappájába, válassza ki a fájlt, majd válassza a Select Edge Deployment Manifest (Edge üzembe `deployment.debug.amd64.json` **helyezési jegyzék kiválasztása) lehetőséget.**

Az integrált terminálon láthatja, hogy az üzembe helyezés sikeresen létrejött egy üzembe helyezési azonosítóval.

A tároló állapotát az parancs terminálon való futtatásával `docker ps` ellenőrizheti. Ha a Visual Studio Code és IoT Edge futtatókörnyezet ugyanazon a gépen fut, az állapotot a Visual Studio Code Docker nézetben is ellenőrizheti.

### <a name="expose-the-ip-and-port-of-the-module-for-the-debugger"></a>Tegye elérhetővé a modul IP-címét és portját a hibakereső számára

Ezt a szakaszt kihagyhatja, ha a modulok ugyanazon a gépen futnak, mint a Visual Studio Code, mivel a localhost használatával csatlakoztatja a tárolót, és már rendelkezik a megfelelő portbeállításokkal a **.debug** Dockerfile-ban, a modul tárolóbeállításában és `createOptions` `launch.json` fájljában. Ha a modulok és Visual Studio Code külön gépeken fut, kövesse a fejlesztési nyelvhez szükséges lépéseket.

- **C# a Azure Functions**

  [Konfigurálja az SSH-csatornát](https://github.com/OmniSharp/omnisharp-vscode/wiki/Attaching-to-remote-processes) a fejlesztői gépen, IoT Edge az eszközt, majd szerkessze `launch.json` a csatolni kívánt fájlt.

- **Node.js**

  - Győződjön meg arról, hogy a hibakeresésre használható gép modulja fut, és készen áll a hibakeresők csatlakoztatásra, és hogy a 9229-es port kívülről elérhető. Ezt úgy ellenőrizheti, ha `http://<target-machine-IP>:9229/json` megnyitja a következőt a hibakereső gépen: . Ennek az URL-címnek a hibakereséshez szükséges Node.js információkat kell mutatnia.
  
  - A fejlesztői gépen nyissa meg a Visual Studio Code-et, majd szerkessze úgy, hogy a modul neve Távoli hibakeresés `launch.json` ***&lt; &gt;* (Node.js)** profil (vagy **_&lt; &gt;_** a modul neve Távoli hibakeresés (windowsos tárolóban Node.js) profilja, ha a modul Windows-tárolóként fut) a hibakeresés alatt áll gép IP-címe.

- **Java**

  - A következő futtatásával építsen ki egy SSH-alagutat a hibakeresésre a `ssh -f <username>@<target-machine> -L 5005:127.0.0.1:5005 -N` géphez: .
  
  - A fejlesztői gépen nyissa meg a Visual Studio ***&lt; &gt;*** Code-et, és szerkessze a Modul neve Távoli hibakeresés (Java) profilt a fájlban, hogy csatolni tudja a `launch.json` célgépet. A Java a Visual Studio Code használatával való szerkesztésével és hibakeresésével kapcsolatos további információkért lásd a hibakereső `launch.json` [konfigurálásával kapcsolatos szakaszt.](https://code.visualstudio.com/docs/java/java-debugging#_configuration)

- **Python**

  - Győződjön meg arról, hogy a hibakeresésre használható gépen az 5678-as port nyitva van és elérhető.

  - A korábban beszúrt kódban módosítsa a `ptvsd.enable_attach(('0.0.0.0', 5678))` `main.py` **0.0.0.0-s** kódot a hibakeresésre megjelölt gép IP-címére. Build, push és deploy your IoT Edge module again.

  - A fejlesztői gépen nyissa meg a Visual Studio Code-et, majd szerkessze úgy, hogy a Modul neve `launch.json` Távoli hibakeresési `host` ***&lt; &gt;* (Python)** profil a helyett a célgép IP-címét `localhost` használja.

### <a name="debug-your-module"></a>A modul hibakeresése

1. A Visual Studio Code Debug (Kód hibakeresése) nézetben válassza ki a modul hibakeresési konfigurációs fájlját. A hibakeresési beállítás nevének hasonlónak kell lennie ***&lt; a modul nevéhez: &gt;* Távoli hibakeresés**

1. Nyissa meg a fejlesztési nyelv modulfájlját, és adjon hozzá egy töréspontot:

   - **Azure-függvény (C#):** Adja hozzá a töréspontot a `<your module name>.cs` fájlhoz.
   - **C#**: Adja hozzá a töréspontot a `Program.cs` fájlhoz.
   - **Node.js:** Adja hozzá a töréspontot a `app.js` fájlhoz.
   - **Java:** Adja hozzá a töréspontot a `App.java` fájlhoz.
   - **Python:** Adja hozzá a töréspontot a fájlhoz abban a visszahívási `main.py` metódusban, amelyben a sort `ptvsd.break_into_debugger()` hozzáadta.
   - **C:** Adja hozzá a töréspontot a `main.c` fájlhoz.

1. Válassza **a Hibakeresés elindítani vagy** az **F5 lehetőséget.** Válassza ki a csatolás folyamatát.

1. A Visual Studio Code Debug (Kód hibakeresése) nézetben a változók a bal oldali panelen fognak látszatra.

> [!NOTE]
> Az előző példa bemutatja, hogyan lehet hibakeresést végezni IoT Edge modulokon a tárolókon. Elérhetővé tette a portokat a modul `createOptions` tárolóbeállításában. A modulok hibakeresésének befejezése után javasoljuk, hogy távolítsa el ezeket a portokat az éles használatra kész IoT Edge érdekében.

## <a name="build-and-debug-a-module-remotely"></a>Modul távoli összeállítása és hibakeresése

A Docker- és a Moby-motorban az SSH-kapcsolatok támogatásához nemrégiben végrehajtott módosítások, valamint a Azure IoT Tools új beállítása, amely lehetővé teszi a környezeti beállítások injektálését az Visual Studio Code parancskatajánába és Azure IoT Edge termináljaiba, most már távoli eszközökön is felépíthet és hibakeresést futtathat.

További információt és részletes utasításokat ebben az [IoT](https://devblogs.microsoft.com/iotdev/easily-build-and-debug-iot-edge-modules-on-your-remote-device-with-azure-iot-edge-for-vs-code-1-9-0/) Developer-blogbejegyzésben talál.

## <a name="next-steps"></a>Következő lépések

Miután felépítette a modult, megtudhatja, hogyan helyezhet üzembe Azure IoT Edge [modulokat a Visual Studio kódból.](how-to-deploy-modules-vscode.md)

A modulokat a saját IoT Edge fejlesztéséhez [a Azure IoT Hub és használatát.](../iot-hub/iot-hub-devguide-sdks.md)