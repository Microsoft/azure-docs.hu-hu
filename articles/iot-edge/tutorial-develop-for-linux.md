---
title: Oktatóanyag – modul fejlesztése linuxos eszközökhöz Azure IoT Edge használatával
description: Ez az oktatóanyag végigvezeti a fejlesztői gép és a felhő erőforrásainak beállításán, hogy IoT Edge modulokat fejlesszen a Linux-eszközök Linux-tárolók használatával
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/30/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: fea8f52ebf40ba8195de134098693f90315bb384
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/14/2021
ms.locfileid: "103461419"
---
# <a name="tutorial-develop-iot-edge-modules-with-linux-containers"></a>Oktatóanyag: IoT Edge-modulok fejlesztése Linux-tárolókkal

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

A Visual Studio Code használatával a IoT Edge rendszert futtató eszközökhöz programkódot fejleszthet és helyezhet üzembe.

A rövid útmutatóban létrehozott egy IoT Edge eszközt, és üzembe helyezett egy modult az Azure piactéren. Ez az oktatóanyag végigvezeti a saját programkódjának a IoT Edge eszközön való fejlesztésén és üzembe helyezésén. Ez a cikk hasznos előfeltételként szolgál a többi oktatóanyaghoz, amely részletesen ismerteti az egyes programozási nyelveket és az Azure-szolgáltatásokat.

Ez az oktatóanyag a **C#-modulok Linux rendszerű eszközre való** üzembe helyezésének példáját használja. Ez a példa azért lett kiválasztva, mert ez a leggyakoribb fejlesztői forgatókönyv IoT Edge megoldásokhoz. Az oktatóanyag még akkor is hasznos, ha más nyelvet használ, vagy egy Azure-szolgáltatást helyez üzembe. Ez az oktatóanyag továbbra is hasznos a fejlesztői eszközök és fogalmak megismeréséhez. Fejezze be ezt a bevezetést a fejlesztési folyamatba, majd válassza ki az előnyben részesített nyelvet vagy az Azure-szolgáltatást.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Állítsa be a fejlesztői gépet.
> * Új projekt létrehozásához használja a Visual Studio Code IoT Edge eszközeit.
> * Készítse el a projektet tárolóként, és tárolja egy Azure Container registryben.
> * A kód üzembe helyezése egy IoT Edge eszközön.

## <a name="prerequisites"></a>Előfeltételek

Egy fejlesztői gép:

* A fejlesztési beállításoktól függően saját számítógépet vagy virtuális gépet is használhat.
  * Győződjön meg arról, hogy a fejlesztői számítógép támogatja a beágyazott virtualizálás szolgáltatást. Ez a funkció a következő szakaszban telepítendő tároló-motor futtatásához szükséges.
* A tároló motort futtató legtöbb operációs rendszer használható IoT Edge modulok Linux-eszközökhöz való fejlesztéséhez. Ez az oktatóanyag egy Windows rendszerű számítógépet használ, de a macOS vagy Linux rendszeren ismert eltéréseket mutat.
* Telepítse a [git](https://git-scm.com/)-t, hogy lekérje a modul sablonjának csomagjait az oktatóanyag későbbi részében.  
* [C# bővítmény a Visual Studio Code-hoz (szolgáltató: OmniSharp) ](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download).

Egy Azure IoT Edge-eszköz:

* Javasoljuk, hogy ne futtasson IoT Edge a fejlesztői gépen, hanem használjon külön eszközt. A fejlesztési gép és a IoT Edge eszköz közötti különbségtétel pontosabban tükrözi a valódi üzembe helyezési forgatókönyvet, és segít a különböző fogalmak közvetlen megtartásában.
* Ha nem érhető el második eszköz, a gyors üzembe helyezési cikk használatával hozzon létre egy IoT Edge-eszközt az Azure-ban egy Linux rendszerű [virtuális géppel](quickstart-linux.md).

Felhőerőforrások:

* Egy ingyenes vagy standard szintű [IoT hub](../iot-hub/iot-hub-create-through-portal.md) az Azure-ban.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="key-concepts"></a>Fő fogalmak

Ez az oktatóanyag végigvezeti egy IoT Edge modul fejlesztésén. Egy *IoT Edge modul*, vagy néha csak a *modul* rövid, végrehajtható kóddal rendelkező tároló. IoT Edge eszközön egy vagy több modult is telepíthet. A modulok olyan konkrét feladatokat látnak el, mint például az érzékelőkből származó adatok beolvasása, az adatok tisztítása és elemzése, illetve üzenetek küldése egy IoT hubhoz. További információ: Azure IoT Edge- [modulok megismerése](iot-edge-modules.md).

IoT Edge modulok fejlesztésekor fontos megérteni a fejlesztési gép és a cél IoT Edge eszköz közötti különbséget, ahol a modul végül üzembe lesz helyezve. A modul kódjának tárolására felépített tárolónak meg kell egyeznie a *céleszköz* operációs rendszerével (os). Például a leggyakoribb forgatókönyv egy olyan modul létrehozása a Windows rendszerű számítógépen, amely a IoT Edge rendszert futtató linuxos eszközt célozza meg. Ebben az esetben a tároló operációs rendszer Linux lenne. Ebben az oktatóanyagban ne feledje, hogy a *fejlesztői gép operációs rendszere* és a *tároló operációs rendszer* közötti különbség.

>[!TIP]
>Ha Windows rendszeren használja a Linux rendszerhez készült [IoT Edge](iot-edge-for-linux-on-windows.md), akkor a forgatókönyvben a *célként megadott eszköz* a Linux rendszerű virtuális gép, nem pedig a Windows-gazdagép.

Ez az oktatóanyag a Linux-tárolókkal IoT Edget futtató eszközöket célozza meg. Használhatja az előnyben részesített operációs rendszert, ha a fejlesztői számítógép Linux-tárolókat futtat. Javasoljuk, hogy a Visual Studio Code használatával fejlesszen a Linux-tárolókat, hogy ezt az oktatóanyagot fogjuk használni. Használhatja a Visual studiót is, de a két eszköz között eltérések vannak.

A következő táblázat a **Linux-tárolók** támogatott fejlesztési forgatókönyveit sorolja fel a Visual Studio Code és a Visual Studio alkalmazásban.

|   | Visual Studio Code | Visual Studio 2017/2019 |
| - | ------------------ | ------------------ |
| **Linux-eszközök architektúrája** | Linux AMD64 <br> Linux ARM32 | Linux AMD64 <br> Linux ARM32 |
| **Azure-szolgáltatások** | Azure Functions <br> Azure Stream Analytics <br> Azure Machine Learning |   |
| **Nyelvek** | C# <br> C# <br> Java <br> Node.js <br> Python | C# <br> C# |
| **További információ** | [Azure IoT Edge a Visual Studio Code-hoz](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) | [Azure IoT Edge Tools for Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) <br> [Azure IoT Edge Tools for Visual Studio 2019](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) |

>[!NOTE]
>A Linux ARM64-eszközök támogatása [nyilvános előzetes](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)verzióban érhető el. További információ: [ARM64 IoT Edge-modulok fejlesztése és hibakeresése a Visual Studio Code-ban (előzetes verzió)](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview).

Ez az oktatóanyag a Visual Studio Code-hoz készült fejlesztési lépéseket ismerteti. Ha inkább a Visual studiót szeretné használni, tekintse meg a [Visual studio 2019 használati utasításait a modulok fejlesztéséhez és hibakereséséhez a Azure IoT Edgehoz](how-to-visual-studio-develop-module.md).
## <a name="install-container-engine"></a>Tároló-motor telepítése

IoT Edge modulok tárolóként vannak csomagolva, ezért a fejlesztői gépen szükség van egy tároló motorra a létrehozásához és kezeléséhez. Javasoljuk, hogy a Docker Desktopot a funkciójának támogatása és népszerűsége alapján fejlessze. A Windowsban a Docker Desktop lehetővé teszi a Linux-tárolók és a Windows-tárolók közötti váltást, így könnyedén fejleszthet különböző típusú IoT Edge eszközökhöz tartozó modulokat.

Telepítse a Docker dokumentációját a fejlesztői gépre való telepítéshez:

* [A Windows rendszerhez készült Docker asztali verziójának telepítése](https://docs.docker.com/docker-for-windows/install/)

  * A Windows rendszerhez készült Docker Desktop telepítésekor megkérdezi, hogy Linux-vagy Windows-tárolókat kíván-e használni. A döntés bármikor módosítható egy egyszerű kapcsoló használatával. Ebben az oktatóanyagban Linux-tárolókat használunk, mert a Moduljaink linuxos eszközöket céloznak. További információ: váltás a [Windows és a Linux rendszerű tárolók között](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).

* [A Docker Desktop for Mac telepítése](https://docs.docker.com/docker-for-mac/install/)

* A több linuxos platformon történő telepítési információkért olvassa el a [Docker CE](https://docs.docker.com/install/) -t ismertető témakört.
  * A Linux rendszerhez készült Windows alrendszer (WSL) esetében telepítse a Windowshoz készült Docker Desktopot.

## <a name="set-up-vs-code-and-tools"></a>A VS Code és az eszközök beállítása

A Visual Studio Code-hoz készült IoT Extensions használatával IoT Edge modulokat fejleszthet. Ezek a bővítmények a Project sablonjait biztosítják, automatizálják az üzembe helyezési jegyzék létrehozását, és lehetővé teszik IoT Edge-eszközök figyelését és kezelését. Ebben a szakaszban telepíti a Visual Studio Code-ot és a IoT-bővítményt, majd beállítja az Azure-fiókját, hogy a Visual Studio Code-ból IoT Hub erőforrásokat kezeljen.

1. Telepítse a [Visual Studio Code](https://code.visualstudio.com/) -ot a fejlesztői gépére.

2. A telepítés befejezése után válassza a   >  **bővítmények** megtekintése lehetőséget.

3. Keressen olyan **Azure IoT-eszközöket**, amelyek tulajdonképpen olyan bővítmények gyűjteményei, amelyek segítséget nyújtanak a IoT hub és a IoT eszközök használatához, valamint IoT Edge modulok fejlesztéséhez.

4. Válassza a **Telepítés** gombot. Az egyes mellékelt bővítmények egyenként települnek.

5. A bővítmények telepítésének befejezése után nyissa meg a parancssort a  >  **parancs-paletta** megtekintése lehetőség kiválasztásával.

6. A parancs palettáján keresse meg és válassza ki az **Azure: bejelentkezés** lehetőséget. Az utasításokat követve jelentkezzen be Azure-fiókjába.

7. A parancssorban keresse meg és válassza ki az **Azure IoT hub: válassza a IoT hub lehetőséget**. Kövesse az utasításokat az Azure-előfizetés és az IoT hub kiválasztásához.

8. Nyissa meg a Visual Studio Code Explorer szakaszát úgy, hogy kiválasztja a bal oldali tevékenység sávján található ikont, vagy az Intéző **megtekintése** lehetőségre kattint  >  .

9. Az Explorer szakasz alján bontsa ki az összecsukott **Azure IoT hub/eszközök** menüt. A IoT hubhoz kiválasztott eszközöket és IoT Edge eszközöket a parancssorban kell megtekinteni.

   ![Az IoT hub eszközeinek megtekintése](./media/tutorial-develop-for-linux/view-iot-hub-devices.png)

[!INCLUDE [iot-edge-create-container-registry](../../includes/iot-edge-create-container-registry.md)]

## <a name="create-a-new-module-project"></a>Új modul-projekt létrehozása

Az Azure IoT Tools bővítmény a Visual Studio Code-ban az összes támogatott IoT Edge modul nyelvéhez biztosít Project sablonokat. Ezekben a sablonokban minden olyan fájl és kód szerepel, amelyre a IoT Edge teszteléséhez egy munkamodult kell telepítenie, vagy egy kiindulási pontot kell adnia a sablon saját üzleti logikával való testreszabásához.

Ebben az oktatóanyagban a C# modul sablonját használjuk, mivel ez a leggyakrabban használt sablon.

### <a name="create-a-project-template"></a>Projekt sablonjának létrehozása

A Visual Studio Code parancs palettáján keresse meg és válassza ki a **Azure IoT Edge: új IoT Edge megoldás** elemet. Kövesse az utasításokat, és használja a következő értékeket a megoldás létrehozásához:

   | Mező | Érték |
   | ----- | ----- |
   | Mappa kiválasztása | Válassza ki azt a helyet a fejlesztői gépen, ahol a VS Code létre fogja hozni a megoldásfájlokat. |
   | Provide a solution name (Megoldásnév megadása) | Adjon meg egy leíró nevet a megoldáshoz, vagy fogadja el az alapértelmezett **EdgeSolution**. |
   | Select module template (Modulsablon kiválasztása) | Válassza a **C# modul** lehetőséget. |
   | Provide a module name (Modulnév megadása) | Fogadja el az alapértelmezett **SampleModule**. |
   | Provide Docker image repository for the module (Docker-rendszerkép adattárának megadása a modulhoz) | Egy rendszerképadattár a tárolóregisztrációs adatbázis nevét és a tárolórendszerkép nevét tartalmazza. A tároló képe előre fel van töltve az utolsó lépésben megadott névvel. Cserélje le a **localhost: 5000** értéket a **bejelentkezési kiszolgáló** értékére az Azure Container registryben. A bejelentkezési kiszolgáló értékét a Azure Portal tároló-beállításjegyzékének áttekintés lapjáról kérheti le. <br><br> A rendszerkép utolsó tárháza a következőhöz hasonlít: \<registry name\> . azurecr.IO/samplemodule. |

   ![Docker-rendszerkép adattárának megadása](./media/tutorial-develop-for-linux/image-repository.png)

Miután az új megoldás betöltődik a Visual Studio Code ablakba, szánjon egy kis időt a létrehozott fájlok megismerésére:

* A **. vscode** mappa egy **launch.js** nevű fájlt tartalmaz, amely hibakeresési modulokhoz használható.
* A **modulok** mappa a megoldás minden moduljának mappáját tartalmazza. Most, hogy csak **SampleModule**, vagy bármilyen nevet adtak a modulnak. A SampleModule mappa tartalmazza a fő programkódot, a modul metaadatait és számos Docker-fájlt.
* A **. env** fájl tárolja a tároló beállításjegyzékének hitelesítő adatait. Ezeket a hitelesítő adatokat a rendszer megosztja a IoT Edge eszközzel, hogy hozzáférhessen a tároló lemezképének lekéréséhez.
* A fájl-és **deployment.template.js** **deployment.debug.template.js** a telepítési jegyzékfájl létrehozásához segítséget nyújtó sablonok. Az *üzembe helyezési jegyzék* egy olyan fájl, amely pontosan meghatározza, hogy mely modulokat kívánja telepíteni az eszközön, hogyan legyenek konfigurálva, és hogyan kommunikálnak egymással és a felhővel. A sablonfájlok egyes értékekhez mutatókat használnak. Ha a sablont egy valódi üzembe helyezési jegyzékbe alakítja át, a mutatók a más megoldási fájlokból származó értékekkel lesznek lecserélve. Keresse meg a két közös helyőrzőt a központi telepítési sablonban:

  * A beállításjegyzékbeli hitelesítő adatok szakaszban a rendszer a megoldás létrehozásakor megadott információk alapján tölti ki a címeket. A Felhasználónév és a jelszó azonban hivatkozik a. env fájlban tárolt változókra. Ez a konfiguráció biztonsági megoldás, mert az. env fájl a git figyelmen kívül lett hagyva, de a telepítési sablon nem.
  * A SampleModule szakaszban a tároló rendszerképe nincs kitöltve annak ellenére, hogy a megoldás létrehozásakor megadta a rendszerkép-tárházat. Ez a helyőrző a SampleModule mappában található fájl **module.jsára** mutat. Ha ezt a fájlt nyitja meg, látni fogja, hogy a képmező tartalmazza a tárházat, de a címke értékét is, amely a tároló verziójából és platformból tevődik fel. A verziót manuálisan is megismételheti a fejlesztési ciklus részeként, és kiválaszthatja a tároló platformot egy olyan kapcsoló használatával, amelyet a jelen szakasz későbbi részében bevezetünk.

### <a name="provide-your-registry-credentials-to-the-iot-edge-agent"></a>Adja meg a beállításjegyzékbeli hitelesítő adatait a IoT Edge-ügynöknek

A környezeti fájl tárolja a tárolóregisztrációs adatbázis hitelesítő adatait, és megosztja őket az IoT-Edge futtatókörnyezettel. A futtatókörnyezetnek szüksége van ezekre a hitelesítő adatokra, hogy a tároló lemezképeit a IoT Edge eszközre húzza.

>[!NOTE]
>Ha nem cserélte le a **localhost: 5000** értéket az Azure Container Registry bejelentkezési kiszolgáló értékével, akkor a [**projekt sablonjának létrehozása**](#create-a-project-template) lépésben a **. env** fájl és az üzembe helyezési jegyzék registryCredentials szakasza hiányzik. 

A IoT Edge bővítmény megpróbálja lekérni a tároló beállításjegyzékbeli hitelesítő adatait az Azure-ból, és feltölti azokat a környezeti fájlban. Ellenőrizze, hogy a hitelesítő adatok már szerepelnek-e. Ha nem, adja hozzá őket most:

1. Nyissa meg a **. env** fájlt a modul-megoldásban.
2. Adja meg az Azure Container registryből másolt **felhasználónevet** és a **jelszó** értékeit.
3. Mentse a módosításokat a. env fájlba.

### <a name="select-your-target-architecture"></a>Válassza ki a cél architektúrát

A Visual Studio Code jelenleg a Linux AMD64-és ARM32v7-eszközökhöz C#-modulokat fejleszthet. Ki kell választania, hogy melyik architektúrát célozza meg az egyes megoldásokkal, mivel ez befolyásolja a tároló kiépítésének és futtatásának módját. Az alapértelmezett érték a Linux AMD64.

1. Nyissa meg a parancssort, és keressen rá **Azure IoT Edge: állítsa be az alapértelmezett cél platformot az Edge megoldáshoz**, vagy válassza a parancsikon ikont az ablak alján található oldalsó sávban.

   ![Architektúra ikon kiválasztása az oldalsó sávban](./media/tutorial-develop-for-linux/select-architecture.png)

2. A parancs palettáján válassza ki a cél architektúrát a lehetőségek listájából. Ebben az oktatóanyagban egy Ubuntu rendszerű virtuális gépet használunk IoT Edge eszközként, így megtarthatja az alapértelmezett **amd64**-t.

### <a name="review-the-sample-code"></a>A mintakód áttekintése

A létrehozott megoldási sablon egy IoT Edge modulhoz tartozó mintakód-kódot tartalmaz. Ez a mintakód egyszerűen fogad üzeneteket, majd továbbítja azokat a következőre:. A folyamat funkciója a IoT Edge fontos koncepcióját mutatja be, ami azt szemlélteti, hogyan kommunikálnak a modulok egymással.

Minden modul több *bemeneti* és *kimeneti* várólistával is rendelkezhet a kódban. Az eszközön futó IoT Edge hub az egyik modul kimenetében lévő üzeneteket egy vagy több modul bemenetére irányítja. A bemenetek és kimenetek deklarálása adott kód a nyelvek között változik, de a koncepció ugyanaz, mint az összes modulban. További információ a modulok közötti útválasztásról: [útvonalak deklarálása](module-composition.md#declare-routes).

A Project sablonhoz tartozó C#-kód a [ModuleClient osztályt](/dotnet/api/microsoft.azure.devices.client.moduleclient) használja a .net-hez készült IoT hub SDK-ból.

1. Nyissa meg a **program.cs** fájlt, amely a **moduls/SampleModule/** mappában található.

2. A program.cs-ben keresse meg a **SetInputMessageHandlerAsync** metódust.

3. A [SetInputMessageHandlerAsync](/dotnet/api/microsoft.azure.devices.client.moduleclient.setinputmessagehandlerasync) metódus beállít egy bemeneti várólistát a bejövő üzenetek fogadásához. Tekintse át ezt a metódust, és nézze meg, hogyan inicializál egy **input1** nevű bemeneti várólistát.

   ![A bemeneti név megkeresése a SetInputMessageCallback konstruktorban](./media/tutorial-develop-for-linux/declare-input-queue.png)

4. Ezután keresse meg a **SendEventAsync** metódust.

5. A [SendEventAsync](/dotnet/api/microsoft.azure.devices.client.moduleclient.sendeventasync) metódus feldolgozza a fogadott üzeneteket, és beállítja a kimeneti várólistát, hogy átadja őket. Tekintse át ezt a metódust, és tekintse meg, hogy inicializál egy **output1** nevű kimeneti várólistát.

   ![A kimeneti név megkeresése a SendEventToOutputAsync](./media/tutorial-develop-for-linux/declare-output-queue.png)

6. Nyissa meg a **deployment.template.js** fájlt.

7. Keresse meg a $edgeAgent kívánt tulajdonságok **modulok** tulajdonságát.

   Itt két modulnak kell szerepelnie. Az egyik a **SimulatedTemperatureSensor** modul, amely alapértelmezés szerint az összes sablonban szerepel, hogy szimulált hőmérsékleti értékeket biztosítson, amelyeket a modulok tesztelésére használhat. A másik a megoldás részeként létrehozott **SampleModule** -modul.

8. A fájl alján keresse meg a **$edgeHub** modul kívánt tulajdonságait.

   Az IoT Edge hub modul egyik funkciója az üzenetek továbbítása a központi telepítés összes modulja között. Tekintse át az **útvonalak** tulajdonságban található értékeket. Az egyik útvonal, a **SampleModuleToIoTHub**, helyettesítő karaktert (* *\** _) használ a SampleModule modulban található kimeneti várólistákból érkező üzenetek jelzésére. Ezek az üzenetek a _ $ upstream * értékre kerülnek, amely a IoT Hubt jelző fenntartott név. A másik útvonal, a **sensorToSampleModule**, a SimulatedTemperatureSensor-modultól érkező üzeneteket fogad, és átirányítja azokat a *input1* bemeneti várólistába, amelyet a SampleModule-kódban inicializált.

   ![Útvonalak áttekintése deployment.template.json](./media/tutorial-develop-for-linux/deployment-routes.png)

## <a name="build-and-push-your-solution"></a>Megoldás létrehozása és leküldése

Áttekintette a modul kódját és a telepítési sablont, hogy megismerje a kulcsfontosságú telepítési fogalmakat. Most már készen áll arra, hogy létrehozza a SampleModule-tároló rendszerképét, és leküldi a tároló-beállításjegyzékbe. A Visual Studio Code-hoz készült IoT Tools bővítménnyel ez a lépés az üzembe helyezési jegyzékfájlt is létrehozza a sablonfájl információi és a modul információi alapján a megoldás fájljaiban.

### <a name="sign-in-to-docker"></a>Bejelentkezés a Docker-be

Adja meg a tároló beállításjegyzékbeli hitelesítő adatait a Docker számára, hogy a tároló rendszerképét leküldheti a beállításjegyzékben.

1. Nyissa meg a Visual Studio Code integrált terminált a terminál **megtekintése** lehetőség kiválasztásával  >  .

2. Jelentkezzen be a Docker-be a beállításjegyzék létrehozása után mentett Azure Container Registry hitelesítő adatokkal.

   ```cmd/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Biztonsági figyelmeztetés jelenhet meg, amely a használatát javasolja `--password-stdin` . Habár az ajánlott eljárás az éles környezetekben javasolt, az oktatóanyag hatókörén kívül esik. További információkért lásd a [Docker bejelentkezési](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) referenciáját.
   
3. Bejelentkezés az Azure Container Registrybe

   ```cmd/sh
   az acr login -n <ACR registry name>
   ```

### <a name="build-and-push"></a>Létrehozás és leküldés

A Visual Studio Code mostantól hozzáfér a tároló-beállításjegyzékhez, ezért itt az ideje, hogy a megoldás kódját egy tároló-rendszerképbe kapcsolja.

1. A Visual Studio Code Explorerben kattintson a jobb gombbal a **deployment.template.js** fájlra, majd válassza a **IoT Edge megoldás létrehozása és leküldése** lehetőséget.

   ![IoT Edge-modulok létrehozása és leküldése](./media/tutorial-develop-for-linux/build-and-push-modules.png)

   A build és a push parancs három műveletet indít el. Először létrehoz egy új mappát a **konfigurációban** , amely tartalmazza a teljes telepítési jegyzékfájlt, kiépítve a telepítési sablonban és más megoldási fájlokban található információkat. Másodszor, futtatja `docker build` a tároló rendszerképét a célként megadott architektúra megfelelő Docker alapján. Ezután futtatja, `docker push` hogy leküldi a rendszerkép-tárházat a tároló-beállításjegyzékbe.

   Ez a folyamat az első alkalommal több percet is igénybe vehet, de gyorsabb a parancsok következő futtatásakor.

2. Nyissa meg a **deployment.amd64.js** fájlt az újonnan létrehozott konfigurációs mappában. A fájlnév a célként megadott architektúrát tükrözi, így más architektúrát is választhat.

3. Figyelje meg, hogy a helyőrzőket tartalmazó két paraméter a megfelelő értékekkel van kitöltve. A **registryCredentials** szakasz a beállításjegyzék felhasználónevét és jelszavát a. env fájlból húzta le. A **SampleModule** rendelkezik a teljes rendszerkép-tárházmal, amelynek neve, verziója és architektúra címkéje a fájl module.js.

4. Nyissa meg a **module.js** fájlt a SampleModule mappában.

5. Módosítsa a modul rendszerképének verziószámát. (A verzió, nem a $schema verziója.) Megnövelheti például a javítás verziószámát a **0.0.2** , mintha egy kis javítást hajtottunk végre a modul kódjában.

   >[!TIP]
   >A modul verziói lehetővé teszik a verziókövetés használatát, és lehetővé teszik, hogy a frissítések éles környezetben való telepítése előtt tesztelje a módosításokat az eszközök egy kis készletén. Ha nem növeli a modul verzióját a kiépítése és a kitolása előtt, írja felül a tárházat a tároló beállításjegyzékében.

6. Mentse a módosításokat a module.jsfájlba.

7. Kattintson a jobb gombbal a **deployment.template.js** fájlra, majd ismét válassza a **IoT Edge megoldás létrehozása és leküldése** lehetőséget.

8. Nyissa meg ismét a **deployment.amd64.js** fájlt. Figyelje meg, hogy a build és a leküldéses parancs ismételt futtatásakor nem jött létre új fájl. Ehelyett ugyanez a fájl frissült, hogy tükrözze a módosításokat. A SampleModule-rendszerkép most a tároló 0.0.2-verziójára mutat.

9. A build és a leküldéses parancs végrehajtásának további ellenőrzéséhez lépjen a [Azure Portal](https://portal.azure.com) , és keresse meg a tároló beállításjegyzékét.

10. A tároló beállításjegyzékében válassza a **tárak** , majd a **samplemodule** lehetőséget. Ellenőrizze, hogy a rendszerkép mindkét verzióját leküldte-e a beállításjegyzékbe.

    ![A tároló beállításjegyzékének mindkét rendszerkép-verziójának megtekintése](./media/tutorial-develop-for-linux/view-repository-versions.png)

<!--Alternative steps: Use VS Code Docker tools to view ACR images with tags-->

### <a name="troubleshoot"></a>Hibaelhárítás

Ha hibát tapasztal a modul rendszerképének létrehozásakor és leküldésekor, gyakran a Docker-konfigurációval kell végrehajtania a fejlesztői gépen. A konfiguráció áttekintéséhez használja a következő ellenőrzéseket:

* Futtatta a `docker login` parancsot a tároló-beállításjegyzékből másolt hitelesítő adatok használatával? Ezek a hitelesítő adatok eltérnek az Azure-ba való bejelentkezéshez használttól.
* Helyes a tároló-adattár? A tároló beállításjegyzékének megfelelő neve és a megfelelő modul neve? A SampleModule mappában Nyissa meg a **module.js** fájlt, és keresse meg a következőt:. A tárház értékének a következőhöz hasonlóan kell kinéznie: **\<registry name\> . azurecr.IO/samplemodule**.
* Ha a modul **SampleModule** eltérő nevet használt, az adott név konzisztens a megoldás során?
* A gép ugyanazon típusú tárolókat futtatja, amelyeket Ön épít? Ez az oktatóanyag a linuxos IoT Edge eszközökhöz készült, így a Visual Studio Code-nak az **amd64** vagy a **arm32v7** kell megjelennie az oldalsó sávban, és a Docker desktopnak Linux-tárolókat kell futtatnia.  

## <a name="deploy-modules-to-device"></a>Modulok üzembe helyezése az eszközön

Ellenőrizte, hogy a rendszer a tároló beállításjegyzékében tárolja a beépített tároló lemezképeit, ezért itt az ideje, hogy egy eszközre telepítse őket. Ellenőrizze, hogy a IoT Edge eszköz működik-e.

1. A Visual Studio Code Explorer **Azure IoT hub** szakasza alatt bontsa ki az **eszközök** elemet a IoT-eszközök listájának megtekintéséhez.

2. Kattintson a jobb gombbal arra a IoT Edge eszközre, amelyet központilag telepíteni kíván, majd válassza a **központi telepítés létrehozása egyetlen eszközhöz** lehetőséget.

   ![Üzemelő példány létrehozása egyetlen eszközhöz](./media/tutorial-develop-for-linux/create-deployment.png)

3. A Fájlkezelőben navigáljon a **konfigurációs** mappához, majd válassza ki a **deployment.amd64.js** fájlt.

   Ne használja a deployment.template.jsfájlt, amely nem rendelkezik a tároló beállításjegyzékbeli hitelesítő adataival vagy a modul képének értékeivel. Ha Linux ARM32-eszközt céloz meg, a telepítési jegyzékfájl neve deployment.arm32v7.json lesz.

4. Az eszköz alatt bontsa ki a **modulok** elemet a telepített és futó modulok listájának megtekintéséhez. Kattintson a frissítés gombra. Ekkor meg kell jelennie az eszközön futó új SimulatedTemperatureSensor-és SampleModule-moduloknak.

   A modulok elindításához néhány percet is igénybe vehet. Az IoT Edge futtatókörnyezetnek meg kell kapnia az új üzembe helyezési jegyzékfájlt, le kell kérnie a modul lemezképeit a tároló futtatókörnyezetből, majd el kell indítania az új modulokat.

   ![A IoT Edge eszközön futó modulok megtekintése](./media/tutorial-develop-for-linux/view-running-modules.png)

## <a name="view-messages-from-device"></a>Üzenetek megtekintése az eszközről

A SampleModule kód üzeneteket fogad a bemeneti várólistán keresztül, és továbbítja azokat a kimeneti várólistán keresztül. Az üzembe helyezési jegyzék olyan útvonalakat jelentett be, amelyek az üzeneteket a SimulatedTemperatureSensor SampleModule továbbították, majd továbbítják az üzeneteket a SampleModule-ből a IoT Hubba. A Visual Studio Code-hoz készült Azure IoT Tools lehetővé teszi, hogy az egyes eszközökről érkező üzeneteket megtekintse IoT Hub.

1. A Visual Studio Code Explorerben kattintson a jobb gombbal a figyelni kívánt IoT Edge eszközre, majd válassza a **figyelés beépített esemény végpontjának elindítása** lehetőséget.

2. Tekintse meg a Visual Studio Code kimenet ablakát, és tekintse meg az IoT-hubhoz érkező üzeneteket.

   ![A bejövő eszköz Felhőbeli üzeneteinek megtekintése](./media/tutorial-develop-for-linux/view-d2c-messages.png)

## <a name="view-changes-on-device"></a>Az eszköz változásainak megtekintése

Ha szeretné megtekinteni, hogy mi történik az eszközön, használja az ebben a szakaszban található parancsokat az eszközön futó IoT Edge futtatókörnyezet és modulok vizsgálatához.

Az ebben a szakaszban található parancsok a IoT Edge eszközre, nem pedig a fejlesztői gépre vonatkoznak. Ha virtuális gépet használ a IoT Edge eszközhöz, kapcsolódjon hozzá most. Az Azure-ban nyissa meg a virtuális gép áttekintés lapját, és válassza a **Kapcsolódás** lehetőséget a biztonságos rendszerhéj-kapcsolat eléréséhez.

* Tekintse meg az eszközre telepített összes modult, és tekintse meg az állapotukat:

   ```bash
   iotedge list
   ```

   Négy modulnak kell megjelennie: a két IoT Edge futásidejű modul, a SimulatedTemperatureSensor és a SampleModule. Mind a négynek szerepelnie kell futtatásként.

* Egy adott modul naplófájljainak vizsgálata:

   ```bash
   iotedge logs <module name>
   ```

   A IoT Edge modulok megkülönböztetik a kis-és nagybetűket.

   A SimulatedTemperatureSensor és a SampleModule naplóknak meg kell jeleníteniük a feldolgozás alatt lévő üzeneteket. A edgeAgent modul felelős a többi modul indításához, így a naplók az üzembe helyezési jegyzék megvalósításával kapcsolatos információkat fognak tartalmazni. Ha valamelyik modul nem jelenik meg vagy nem fut, a edgeAgent-naplók valószínűleg a hibákkal fognak rendelkezni. A edgeHub modul felelős a modulok és a IoT Hub közötti kommunikációért. Ha a modulok működése folyamatban van, de az üzenetek nem érkeznek meg az IoT hub-ba, akkor a edgeHub-naplók valószínűleg a hibákkal fognak rendelkezni.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy a következő ajánlott cikkel folytatja, megtarthatja és újból felhasználhatja a létrehozott erőforrásokat és konfigurációkat. Azt is megteheti, hogy ugyanezt az IoT Edge-eszközt használja teszteszközként.

Ellenkező esetben törölheti a cikkben használt helyi konfigurációkat és az Azure-erőforrásokat a díjak elkerüléséhez.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban beállíthatja a Visual Studio Code-ot a fejlesztői gépen, és üzembe helyezheti az első IoT Edge modulját. Most, hogy már ismeri az alapfogalmakat, próbáljon hozzá funkciókat hozzáadni egy modulhoz, hogy az áthaladó adatok elemezhetők legyenek. Válassza ki a kívánt nyelvet:

> [!div class="nextstepaction"]
> [C](tutorial-c-module.md) 
>  [C#](tutorial-csharp-module.md) 
>  [Java](tutorial-java-module.md) 
>  [Node.js](tutorial-node-module.md) 
>  [Python](tutorial-python-module.md)
