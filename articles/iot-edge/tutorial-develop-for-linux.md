---
title: Oktatóanyag – Modul fejlesztése Linux-eszközökhöz Azure IoT Edge
description: Ez az oktatóanyag végigvezeti a fejlesztői gép és a felhőerőforrások beállításán, hogy a IoT Edge linuxos eszközök Linux-tárolóit használva fejleszthetheket.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/30/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 10742ec277f5742067c432a2823cbb7592e6a752
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874692"
---
# <a name="tutorial-develop-iot-edge-modules-with-linux-containers"></a>Oktatóanyag: IoT Edge modulok fejlesztése Linux-tárolók használatával

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

A Visual Studio Code használatával kódokat fejleszthet és helyezhet üzembe az IoT Edge.

A rövid útmutatóban létrehozott egy IoT Edge, és üzembe helyezett egy modult a Azure Marketplace. Ez az oktatóanyag végigvezeti a saját kód fejlesztésén és üzembe helyezésén egy IoT Edge eszközön. Ez a cikk hasznos előfeltétel a többi oktatóanyaghoz, amelyek részletesebben ismertetik az egyes programozási nyelveket vagy Azure-szolgáltatásokat.

Ez az oktatóanyag egy C#-modul Linux-eszközön való **üzembe helyezésének példáját használja.** Ezt a példát azért választottuk, mert ez a leggyakoribb fejlesztői forgatókönyv a IoT Edge megoldásokhoz. Ez az oktatóanyag akkor is hasznos, ha egy másik nyelvet szeretne használni vagy üzembe helyezni egy Azure-szolgáltatást. Ez az oktatóanyag akkor is hasznos, ha többet szeretne megtudni a fejlesztési eszközökről és fogalmakról. A fejlesztési folyamat bemutatásának befejezése után válassza ki a kívánt nyelvet vagy Azure-szolgáltatást a részletek részletes bemutatásához.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * A fejlesztői gép beállítása.
> * Az IoT Edge Code-hoz Visual Studio eszközök használatával hozzon létre egy új projektet.
> * Készítse el a projektet tárolóként, és tárolja egy Azure-beli tároló-beállításjegyzékben.
> * A kód üzembe helyezése egy IoT Edge eszközön.

## <a name="prerequisites"></a>Előfeltételek

Egy fejlesztői gép:

* A fejlesztési beállításoktól függően használhatja a saját számítógépét vagy virtuális gépét.
  * Győződjön meg arról, hogy a fejlesztői gép támogatja a beágyazott virtualizálást. Ez a képesség egy tárolómotor futtatásához szükséges, amelyet a következő szakaszban telepíthet.
* A tárolómotort futtatni képes legtöbb operációs rendszer használható linuxos IoT Edge modulok fejlesztéséhez. Ez az oktatóanyag egy Windows rendszerű számítógépet használ, de kiemeli az ismert különbségeket macOS vagy Linux rendszeren.
* Telepítse a [Gitet,](https://git-scm.com/)hogy lekérhetők a modulsablon-csomagok az oktatóanyag későbbi részen.  
* [C# bővítmény a Visual Studio Code-hoz (szolgáltató: OmniSharp) ](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).
* [.NET Core 2.1 SDK](https://dotnet.microsoft.com/download/dotnet/2.1).

Egy Azure IoT Edge-eszköz:

* Javasoljuk, hogy ne IoT Edge a fejlesztői gépen, hanem használjon egy külön eszközt. Ez a különbség a fejlesztői gép és a IoT Edge között pontosabban tükrözi a valódi üzembe helyezési forgatókönyvet, és segít a különböző fogalmak egyértelműbb megkülönböztetésében.
* Ha nem érhető el második eszköz, a rövid útmutató segítségével hozzon létre egy IoT Edge-eszközt az Azure-ban egy [Linux rendszerű virtuális géppel.](quickstart-linux.md)

Felhőerőforrások:

* Ingyenes vagy standard szintű [IoT](../iot-hub/iot-hub-create-through-portal.md) Hub az Azure-ban.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="key-concepts"></a>Fő fogalmak

Ez az oktatóanyag egy új modul IoT Edge be. Egy *IoT Edge modul*( vagy  röviden csak modul) egy végrehajtható kódot tartalmazó tároló. Üzembe helyezhet egy vagy több modult egy IoT Edge eszközön. A modulok olyan konkrét feladatokat hajt végre, mint az érzékelőkből származó adatok fogadása, az adatok tisztítása és elemzése, vagy üzenetek küldése egy IoT Hubra. További információ: [A modulokkal kapcsolatos Azure IoT Edge.](iot-edge-modules.md)

A IoT Edge fejlesztésekor fontos megérteni a különbséget a fejlesztői gép és a célként IoT Edge eszköz között, ahol a modult végül üzembe fogja helyezni. A modul kódját tároló tárolónak egyeznie kell a céleszköz operációs *rendszerével*(OS). A leggyakoribb forgatókönyv például az, amikor egy modult fejleszt egy Windows rendszerű számítógépen, amely egy linuxos eszközt céloz meg IoT Edge. Ebben az esetben a tároló operációs rendszere Linux. Az oktatóanyag során tartsa szem előtt a  fejlesztői gép operációs rendszer és a tároló operációs *rendszer közötti különbséget.*

>[!TIP]
>Ha Windows rendszeren [linuxos IoT Edge](iot-edge-for-linux-on-windows.md)használ, akkor  a forgatókönyvben a céleszköz a Linux rendszerű virtuális gép, nem pedig a Windows-gazdagép.

Ez az oktatóanyag a Linux-IoT Edge futtató eszközöket célozza meg. Használhatja az előnyben részesített operációs rendszert, ha a fejlesztői gépen Linux-tárolók futnak. Javasoljuk, hogy a linuxos Visual Studio kód használatával fejlessze a fejlesztést, így ez az oktatóanyag is ezt fogja használni. Használhatja a Visual Studio is, bár a két eszköz támogatása eltérő.

Az alábbi táblázat a **Code** és Visual Studio linuxos tárolók támogatott fejlesztési forgatókönyvét Visual Studio.

|   | Visual Studio Code | Visual Studio 2017/2019 |
| - | ------------------ | ------------------ |
| **Linux-eszközarchitektúra** | Linux AMD64 <br> Linux ARM32 | Linux AMD64 <br> Linux ARM32 |
| **Azure-szolgáltatások** | Azure Functions <br> Azure Stream Analytics <br> Azure Machine Learning |   |
| **Nyelvek** | C# <br> C# <br> Java <br> Node.js <br> Python | C# <br> C# |
| **További információ** | [Azure IoT Edge kód Visual Studio](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) | [Azure IoT Edge Tools 2017 Visual Studio re vonatkozó Visual Studio](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) <br> [Azure IoT Edge Tools 2019 Visual Studio re vonatkozó Visual Studio](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) |

>[!NOTE]
>A Linux ARM64-eszközök támogatása nyilvános előzetes [verzióban érhető el.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) További információ: [ARM64-modulok fejlesztése és IoT Edge a Visual Studio (előzetes verzió) modulban.](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview)

Ez az oktatóanyag a Kód Visual Studio be. Ha inkább a Visual Studio szeretné használni, tekintse meg a Use [Visual Studio 2019 to](how-to-visual-studio-develop-module.md)develop and debug modules for Azure IoT Edge ( Modulok fejlesztése és hibakeresése a 2019-es Azure IoT Edge).
## <a name="install-container-engine"></a>Tárolómotor telepítése

IoT Edge modulok tárolókként vannak csomagolva, ezért a fejlesztéshez és a kezelésükhez egy tárolómotorra van szükség a fejlesztői gépen. A Docker Desktopot a funkciók támogatása és népszerűsége miatt javasoljuk a fejlesztéshez. A Windows rendszeren elérhető Docker Desktop lehetővé teszi a Linux-tárolók és a Windows-tárolók közötti váltást, így egyszerűen fejleszthet modulokat különböző típusú IoT Edge eszközökhöz.

Használja a Docker dokumentációját a telepítéshez a fejlesztői gépen:

* [A Windows rendszerhez készült Docker asztali verziójának telepítése](https://docs.docker.com/docker-for-windows/install/)

  * A Windowshoz való Docker Desktop telepítésekor a rendszer megkérdezi, hogy Linux- vagy Windows-tárolókat szeretne-e használni. Ez a döntés bármikor módosítható egy egyszerű kapcsolóval. Ebben az oktatóanyagban Linux-tárolókat használunk, mert a moduljaink Linux-eszközöket céloznak meg. További információ: [Switch between Windows and Linux containers (Váltás Windows- és Linux-tárolók között).](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers)

* [A Mac docker desktop telepítése](https://docs.docker.com/docker-for-mac/install/)

* A [Docker CE-ről](https://docs.docker.com/install/) több Linux-platformon való telepítéssel kapcsolatos információkért olvassa el a tudnivalókat.
  * A Linuxos Windows-alrendszer (WSL) telepítéséhez telepítse a Windowshoz való Docker Desktopot.

## <a name="set-up-vs-code-and-tools"></a>A VS Code és az eszközök beállítása

Az IoT Code-hoz Visual Studio IoT-bővítményekkel fejleszthet IoT Edge modulokat. Ezek a bővítmények projektsablonokat biztosítanak, automatizálják az üzembe helyezési jegyzék létrehozását, és lehetővé teszik az eszközök IoT Edge kezelését. Ebben a szakaszban telepítenie kell a Visual Studio Code-et és az IoT-bővítményt, majd be kell állítania az Azure-fiókját a IoT Hub-erőforrások a Code-ban Visual Studio kezelésére.

1. Telepítse [Visual Studio Code-kódot](https://code.visualstudio.com/) a fejlesztői gépen.

2. A telepítés befejezése után válassza a **Bővítmények**  >  **megtekintése lehetőséget.**

3. Keressen rá **a Azure IoT Tools** kifejezésre, amely valójában az IoT Hub- és IoT-eszközökkel való interakciót segítő bővítmények gyűjteménye, valamint a IoT Edge fejlesztése.

4. Válassza a **Telepítés** gombot. Az egyes mellékelt bővítmények egyenként telepíthetők.

5. Ha a bővítmények telepítése kész, nyissa meg a parancskatajánt a View Command Palette **(Parancspaletta**  >  **megtekintése) lehetőség kiválasztásával.**

6. A parancskata ban keresse meg és válassza az **Azure: Bejelentkezés lehetőséget.** Az utasításokat követve jelentkezzen be Azure-fiókjába.

7. A parancskatapalóban keresse meg és válassza ki a **Azure IoT Hub: Válassza a IoT Hub** lehetőséget. Kövesse az utasításokat az Azure-előfizetés és az IoT Hub kiválasztásához.

8. Nyissa meg a Visual Studio Code Explorer szakaszát a bal oldali tevékenységsáv ikonjára kattintva, vagy a **Nézetkezelő**  >  **kiválasztásával.**

9. Az Explorer szakasz alján bontsa ki az összecsukott Azure IoT Hub **/ Eszközök menüt.** A parancskatapaletta segítségével meg kell IoT Edge IoT Hubhoz társított eszközöknek és eszközöknek.

   ![Eszközök megtekintése az IoT Hubban](./media/tutorial-develop-for-linux/view-iot-hub-devices.png)

[!INCLUDE [iot-edge-create-container-registry](../../includes/iot-edge-create-container-registry.md)]

## <a name="create-a-new-module-project"></a>Új modulprojekt létrehozása

A Azure IoT Tools bővítmény projektsablonokat biztosít a Code IoT Edge támogatott Visual Studio nyelvekhez. Ezek a sablonok tartalmaznak minden fájlt és kódot, amely egy munkamodul üzembe helyezéséhez szükséges a IoT Edge teszteléséhez, vagy kiindulópontot biztosít a sablon saját üzleti logikával való testreszabásához.

Ebben az oktatóanyagban a C# modulsablont használjuk, mert ez a leggyakrabban használt sablon.

### <a name="create-a-project-template"></a>Projektsablon létrehozása

A Visual Studio Code parancskataában keresse meg és válassza a Azure IoT Edge: Új IoT Edge **megoldás lehetőséget.** Kövesse az utasításokat, és használja a következő értékeket a megoldás létrehozásához:

   | Mező | Érték |
   | ----- | ----- |
   | Mappa kiválasztása | Válassza ki azt a helyet a fejlesztői gépen, ahol a VS Code létre fogja hozni a megoldásfájlokat. |
   | Provide a solution name (Megoldásnév megadása) | Adjon egy leíró nevet a megoldásnak, vagy fogadja el az **alapértelmezett EdgeSolution nevet.** |
   | Select module template (Modulsablon kiválasztása) | Válassza a **C# modult.** |
   | Provide a module name (Modulnév megadása) | Fogadja el az alapértelmezett **SampleModule modult.** |
   | Provide Docker image repository for the module (Docker-rendszerkép adattárának megadása a modulhoz) | Egy rendszerképadattár a tárolóregisztrációs adatbázis nevét és a tárolórendszerkép nevét tartalmazza. A rendszer előre kitölti a tároló rendszerképét az előző lépésben megadott név alapján. Cserélje **le a localhost:5000** értéket az Azure Container Registry bejelentkezési kiszolgálójának értékére.  A bejelentkezési kiszolgáló értékét a tároló-beállításjegyzék Áttekintés lapján olvashatja be a Azure Portal. <br><br> A végső képtár a következőre hasonlít: \<registry name\> .azurecr.io/samplemodule. |

   ![Docker-rendszerkép adattárának megadása](./media/tutorial-develop-for-linux/image-repository.png)

Miután az új megoldás betöltődik a Visual Studio Code ablakban, ismerkedjen meg az újonnan létrehozott fájlokkal:

* A **.vscode** mappa tartalmaz egylaunch.jsnevű **fájlt,** amely a modulok hibakereséséhez használatos.
* A **modules mappa** a megoldás minden moduljának egy-egy mappáját tartalmazza. Jelenleg ez csak **SampleModule** lehet, vagy bármilyen név, amit a modulnak adott. A SampleModule mappa tartalmazza a program fő kódját, a modul metaadatait és számos Docker-fájlt.
* Az **.env fájl** tárolja a hitelesítő adatokat a tároló-beállításjegyzékben. Ezek a hitelesítő adatok meg vannak osztva a IoT Edge eszközzel, hogy hozzáférnek a tároló rendszerképei lekért adataihoz.
* A **deployment.debug.template.jsfájlon és a** **fájlondeployment.template.js** fájlban található fájlok olyan sablonok, amelyek segítségével létrehozhat egy üzembe helyezési jegyzékfájlt. Az *üzembe helyezési* jegyzékfájl egy fájl, amely meghatározza, hogy pontosan mely modulokat szeretné üzembe helyezni az eszközön, hogyan kell konfigurálni őket, és hogyan kommunikálhatnak egymással és a felhővel. A sablonfájlok bizonyos értékekhez mutatót használnak. Ha a sablont valódi üzembe helyezési jegyzékké alakítja át, a mutatókat más megoldásfájlokból származó értékek váltják fel. Keresse meg a két általános helyőrzőt az üzembe helyezési sablonban:

  * A beállításjegyzék hitelesítő adatai szakaszban a rendszer automatikusan kitölti a címet a megoldás létrehozásakor megadott információkból. A felhasználónév és a jelszó azonban az .env fájlban tárolt változókra hivatkozik. Ez a konfiguráció biztonsági okokból van, mivel a git figyelmen kívül hagyja az .env fájlt, de az üzembe helyezési sablon nem.
  * A SampleModule szakaszban a tároló rendszerképe nem lesz kitöltve annak ellenére, hogy a megoldás létrehozásakor a rendszerkép-adattárat biztosította. Ez a helyőrző a **SampleModulemodule.jsfájlban** található fájlra mutat. Ha erre a fájlra ás, láthatja, hogy az image mező tartalmazza az adattárat, de egy címkeértéket is, amely a tároló verziójából és platformjérérből áll. A verziókat a fejlesztési ciklus részeként manuálisan is iterálhatja, a tárolóplatformot pedig egy kapcsolóval választhatja ki, amit a szakasz későbbi részében mutatunk be.

### <a name="provide-your-registry-credentials-to-the-iot-edge-agent"></a>Adja meg a regisztrációs adatbázis hitelesítő adatait a IoT Edge ügynöknek

A környezeti fájl tárolja a tárolóregisztrációs adatbázis hitelesítő adatait, és megosztja őket az IoT-Edge futtatókörnyezettel. A runtime-nak szüksége van ezekre a hitelesítő adatokra, hogy lekérte a tároló rendszerképét a IoT Edge eszközre.

>[!NOTE]
>Ha nem cserélte le a **localhost:5000** értéket az Azure Container Registry bejelentkezési kiszolgálójának értékére, [**akkor a**](#create-a-project-template) Projektsablon létrehozása lépésben az üzembe helyezési jegyzék **.env** fájlja és registryCredentials szakasza hiányzik. 

A IoT Edge a bővítmény megpróbálja lekért hitelesítő adatokat lekért tárolójegyzékből az Azure-ból, és feltölti őket a környezeti fájlba. Ellenőrizze, hogy szerepel-e már a hitelesítő adatai között. Ha nem, adja hozzá őket most:

1. Nyissa meg **az .env** fájlt a modulmegoldásban.
2. Adja meg  **az** Azure Container Registryből másolt felhasználónév- és jelszóértékeket.
3. Mentse az .env fájl módosításait.

### <a name="select-your-target-architecture"></a>A célarchitektúra kiválasztása

A Visual Studio Code jelenleg C#-modulokat fejleszthet Linux AMD64- és ARM32v7-eszközökhöz. Ki kell választania, hogy melyik architektúrát célozza meg az egyes megoldások, mivel ez befolyásolja a tároló felépítését és futását. Az alapértelmezett beállítás a Linux AMD64.

1. Nyissa meg a parancskataccsal, és keressen rá a **Azure IoT Edge: Set Default Target Platform for Edge Solution**(Alapértelmezett célplatform beállítása az Edge-megoldáshoz) kifejezésre, vagy válassza az ablak alján található oldalsáv parancsikonját.

   ![Architektúra ikon kiválasztása az oldalsávon](./media/tutorial-develop-for-linux/select-architecture.png)

2. A parancskatatúrában válassza ki a célarchitektúrát a lehetőségek listájából. Ebben az oktatóanyagban ubuntus virtuális gépet fogunk használni IoT Edge eszközként, ezért megtartjuk az alapértelmezett **amd64-et.**

### <a name="review-the-sample-code"></a>A mintakód áttekintése

A létrehozott megoldássablon mintakódot tartalmaz egy IoT Edge modulhoz. Ez a mintamodul egyszerűen fogadja az üzeneteket, majd továbbítja azokat. A folyamatfunkció egy fontos fogalmat mutat be a IoT Edge, vagyis azt, hogyan kommunikálnak egymással a modulok.

Minden modul több bemeneti  *és* kimeneti üzenetsort is deklarálhat a kódban. Az IoT Edge-központ egy modul kimenetének üzeneteit egy vagy több modul bemenetének adja át. A bemenetek és kimenetek deklaratív kódjának nyelve eltérő, de a koncepció minden modulban ugyanaz. További információ a modulok közötti útválasztásról: [Útvonalak deklarálása.](module-composition.md#declare-routes)

A projektsablonhoz tartozó C#-mintakód a .NET-hez készült IoT Hub [ModuleClient](/dotnet/api/microsoft.azure.devices.client.moduleclient) osztályát használja.

1. Nyissa meg a **Modules/SampleModule/** mappában található **Program.cs** fájlt.

2. A program.cs fájlban keresse meg a **SetInputMessageHandlerAsync metódust.**

3. A [SetInputMessageHandlerAsync](/dotnet/api/microsoft.azure.devices.client.moduleclient.setinputmessagehandlerasync) metódus beállít egy bemeneti üzenetsort a bejövő üzenetek fogadásához. Tekintse át ezt a metódust, és nézze meg, hogyan inicializálja az input1 nevű **bemeneti üzenetsort.**

   ![Keresse meg a bemeneti nevet a SetInputMessageCallback konstruktorban](./media/tutorial-develop-for-linux/declare-input-queue.png)

4. Ezután keresse meg a **SendEventAsync metódust.**

5. A [SendEventAsync](/dotnet/api/microsoft.azure.devices.client.moduleclient.sendeventasync) metódus feldolgozza a fogadott üzeneteket, és beállítja a kimeneti üzenetsort, hogy továbbküldje azokat. Tekintse át ezt a metódust, és ellenőrizze, hogy inicializálja-e az output1 nevű **kimeneti üzenetsort.**

   ![Keresse meg a kimeneti nevet a SendEventToOutputAsync fájlban](./media/tutorial-develop-for-linux/declare-output-queue.png)

6. Nyissa meg **deployment.template.jsfájlt.**

7. Keresse **meg** a kívánt $edgeAgent modules tulajdonságát.

   Itt két modulnak kell lennie. Az egyik a **SimulatedTemperatureSensor** modul, amely alapértelmezés szerint megtalálható az összes sablonban, hogy szimulált hőmérsékleti adatokat biztosítson a modulok teszteléséhez. A másik a megoldás részeként létrehozott **SampleModule** modul.

8. A fájl alján keresse meg a kívánt tulajdonságokat a **$edgeHub** modulhoz.

   A központi modul egyik IoT Edge az üzeneteknek az üzembe helyezés összes modulja közötti útválasztása. Tekintse át az **útvonalak tulajdonságban található értékeket.** Az egyik útvonal, a **SampleModuleToIoTHub** helyettesítő karaktert (* _) használ a SampleModule modul kimeneti üzenetsoraiból érkező üzenetek *\** jelzésére. Ezek az üzenetek a _$upstream*-be kerülnek, amely egy fenntartott név, amely a IoT Hub. A másik útvonal, a **sensorToSampleModule** a SimulatedTemperatureSensor modulból érkező üzeneteket a SampleModule kódban inicializált *input1* bemeneti üzenetsorhoz viszi.

   ![Tekintse át a deployment.template.jsútvonalait](./media/tutorial-develop-for-linux/deployment-routes.png)

## <a name="build-and-push-your-solution"></a>A megoldás összeállítása és leküldése

Áttekintte a modul kódját és az üzembe helyezési sablont, hogy megértse az üzembe helyezés néhány alapvető fogalmát. Most már készen áll a SampleModule tároló rendszerképének építésére és a tárolójegyzékbe való leküldésére. A Visual Studio Code-hoz használható IoT-eszközbővítmény segítségével ez a lépés az üzembe helyezési jegyzékfájlt is létrehozza a sablonfájlban lévő információk és a megoldásfájlokból származó modulinformációk alapján.

### <a name="sign-in-to-docker"></a>Bejelentkezés a Dockerbe

Adja meg a tároló-beállításjegyzék hitelesítő adatait a Dockernek, hogy le tudja adni a tároló rendszerképét a tárolójegyzékben való tároláshoz.

1. Nyissa meg a Visual Studio Code integrált terminálját a **View** Terminal (Terminál  >  **megtekintése) lehetőség kiválasztásával.**

2. Jelentkezzen be a Dockerbe a regisztrációs adatbázis létrehozása után mentett Azure Container Registry hitelesítő adatokkal.

   ```cmd/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Előfordulhat, hogy egy biztonsági figyelmeztetés jelenik meg, amely a használatát `--password-stdin` javasolja. Bár ez az ajánlott eljárás éles környezetben is ajánlott, ez az oktatóanyag nem terjed ki rá. További információért tekintse meg a [docker bejelentkezési referenciáját.](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin)
   
3. Bejelentkezés az Azure Container Registrybe

   ```cmd/sh
   az acr login -n <ACR registry name>
   ```

### <a name="build-and-push"></a>Build és leküldés

Visual Studio Code most már hozzáfér a tároló-beállításjegyzékhez, így ideje a megoldás kódját tároló-rendszerképté kapcsolni.

1. A Visual Studio Code Explorerben kattintson a  jobb gombbal a fájlbandeployment.template.jselemre, és válassza a **Build and Push IoT Edge Solution (Megoldás létrehozása és leküldése) lehetőséget.**

   ![Modulok összeállítása IoT Edge leküldése](./media/tutorial-develop-for-linux/build-and-push-modules.png)

   A build és a push parancs három műveletet kezd el. Először létrehoz egy **config** nevű új mappát a megoldásban, amely tartalmazza a teljes üzembe helyezési jegyzékfájlt, amely a telepítési sablonban és más megoldásfájlokban lévő információkból épül fel. Másodszor fut, hogy a tárolórendszerképet a célarchitektúra megfelelő `docker build` dockerfile-ja alapján készítse el. Ezután a rendszer `docker push` lekulpeli a rendszerkép-adattárat a tároló-beállításjegyzékbe.

   Ez a folyamat az első alkalommal több percig is eltarthat, de a parancsok következő futtatásakor gyorsabb lesz.

2. Nyissa meg **deployment.amd64.jsfájlt** az újonnan létrehozott config mappában. A fájlnév a célarchitektúrát tükrözi, így más lesz, ha másik architektúrát választ.

3. Figyelje meg, hogy a helyőrzőkben lévő két paraméter most már a megfelelő értékekkel van feltöltve. A **registryCredentials** szakasz a regisztrációs adatbázis .env fájlból lekért felhasználónevét és jelszavát használja. A **SampleModule a** fájlban található fájlban található fájlból származó teljes rendszerképadattárat és -verziót, valamint architektúracímkét module.jsrendelkezik.

4. Nyissa meg **module.jsSampleModule** mappában található fájlban található fájlt.

5. Módosítsa a modul rendszerképének verziószámát. (A verzió, nem pedig $schema-verzió.) Például növelje a javítás verziószámát **0.0.2-re** úgy, mintha egy kisebb javítást adtunk volna a modul kódban.

   >[!TIP]
   >A modulverziók lehetővé teszik a verziók szabályozását, és lehetővé teszik a módosítások tesztelését az eszközök egy kis készletén, mielőtt frissítéseket telepítenének az éles környezetben. Ha nem növeli meg a modulverziót a felépítés és lekésés előtt, felülírja az adattárat a tároló-beállításjegyzékben.

6. Mentse a fájlban module.jsmódosításokat.

7. Kattintson ismét a jobb **gombbal adeployment.template.js,** majd válassza ismét a Build and Push IoT Edge Solution (Megoldás létrehozása **és leküldése) lehetőséget.**

8. Nyissa meg **deployment.amd64.jsfájlt.** Figyelje meg, hogy a build és a push parancs újrafuttatáskor nem jött létre új fájl. Inkább ugyanazt a fájlt frissítettük, hogy tükrözze a módosításokat. A SampleModule rendszerkép most a tároló 0.0.2-es verziójára mutat.

9. A build és a leküldéses parancs további ellenőrzéséhez lépjen a Azure Portal, [és](https://portal.azure.com) keresse meg a tároló-beállításjegyzéket.

10. A tárolójegyzékben válassza az Adattárak, majd **a** **samplemodule lehetőséget.** Ellenőrizze, hogy a rendszerkép mindkét verziója lekért-e a beállításjegyzékbe.

    ![Mindkét rendszerképverzió megtekintése a tároló-beállításjegyzékben](./media/tutorial-develop-for-linux/view-repository-versions.png)

<!--Alternative steps: Use VS Code Docker tools to view ACR images with tags-->

### <a name="troubleshoot"></a>Hibaelhárítás

Ha hibákba ütközik a modul rendszerképének kiépítése és lekért lekullása során, annak gyakran a docker-konfigurációval kell kapcsolatosnak megtennie a fejlesztési gépen. A konfiguráció áttekintését az alábbi ellenőrzésekkel használhatja:

* A parancsot a tároló-beállításjegyzékből másolt hitelesítő adatokkal `docker login` futtatta? Ezek a hitelesítő adatok eltérnek az Azure-ba való bejelentkezéshez használt hitelesítő adatoktól.
* Helyes a tároló-adattár? A tároló-beállításjegyzék neve és a modul neve helyes? Nyissa meg **module.jsa** SampleModule mappában található fájlban található fájlt az ellenőrzéshez. Az adattár értékének a következőnek kell lennie: **\<registry name\> .azurecr.io/samplemodule.**
* Ha a modulban a **SampleModule** névtől eltérő nevet használt, akkor ez a név konzisztens a megoldásban?
* A gépe ugyanolyan típusú tárolókat futtat, mint amit ön is? Ez az oktatóanyag Linux IoT Edge-eszközökre szól, ezért az Visual Studio Code-nak **amd64-nek** vagy **arm32v7-nek** kell lennie az oldalsávon, és a Docker Desktopnak Linux-tárolókat kell futnia.  

## <a name="deploy-modules-to-device"></a>Modulok üzembe helyezése az eszközön

Ellenőrizte, hogy a beépített tároló rendszerképeket a tárolójegyzék tárolja, ezért itt az ideje, hogy üzembe helyezd őket egy eszközön. Ellenőrizze, hogy a IoT Edge eszköz működik-e.

1. A Visual Studio Code Explorer Azure IoT Hub **szakaszban** bontsa ki  az Eszközök listában az IoT-eszközök listáját.

2. Kattintson a jobb gombbal arra IoT Edge eszközre, amelybe telepíteni szeretné, majd válassza a Központi telepítés **létrehozása egyetlen eszközhöz lehetőséget.**

   ![Üzemelő példány létrehozása egyetlen eszközhöz](./media/tutorial-develop-for-linux/create-deployment.png)

3. A fájlkezelőben lépjen a **config** mappába, majd válassza ki **deployment.amd64.jsfájlt.**

   Ne használja a deployment.template.jsfájlban, amely nem tartalmaz tárolójegyzékbeli hitelesítő adatokat vagy modul rendszerkép-értékeit. Ha Linux ARM32-eszközt céloz meg, az üzembe helyezési jegyzék neve deployment.arm32v7.jsbe.

4. Az eszköz alatt **bontsa** ki a Modulok listában az üzembe helyezett és futó modulok listáját. Kattintson a frissítés gombra. Az eszközön az új SimulatedTemperatureSensor és SampleModule modulnak kell futnia.

   A modulok elindulás eltarthat néhány percig. A IoT Edge-modulnak meg kell kapnia az új üzembe helyezési jegyzékfájlt, le kell húznia a modul rendszerképét a tároló-modulból, majd el kell kezdenie az új modulokat.

   ![A saját eszközén futó IoT Edge megtekintése](./media/tutorial-develop-for-linux/view-running-modules.png)

## <a name="view-messages-from-device"></a>Eszközről származó üzenetek megtekintése

A SampleModule kód fogadja az üzeneteket a bemeneti üzenetsorán keresztül, és továbbítja azokat a kimeneti üzenetsorán keresztül. Az üzembe helyezési jegyzék útvonalakat deklarált, amelyek a SimulatedTemperatureSensor modulból üzeneteket továbbítottak a SampleModule modulból a IoT Hub. Az Azure IoT Tools for Visual Studio Code lehetővé teszi, hogy az egyes eszközökről érkező IoT Hub üzeneteket lássa.

1. A Visual Studio Code Explorerben kattintson a jobb gombbal a figyelni kívánt IoT Edge-eszközre, majd válassza a **Start Monitoring Built-in Event Endpoint**(Beépített eseményvégpont figyelése) lehetőséget.

2. Tekintse meg a kódban a Visual Studio az IoT Hubra érkező üzeneteket.

   ![Bejövő eszközről felhőbe küldött üzenetek megtekintése](./media/tutorial-develop-for-linux/view-d2c-messages.png)

## <a name="view-changes-on-device"></a>Módosítások megtekintése az eszközön

Ha szeretné látni, hogy mi történik magában az eszközön, az ebben a szakaszban található parancsokkal megvizsgálhatja az eszközön futó IoT Edge futtatókörnyezetet és modulokat.

Az ebben a szakaszban található parancsok a saját IoT Edge, és nem a fejlesztői gépre. Ha virtuális gépet használ a IoT Edge eszközéhez, csatlakozzon hozzá most. Az Azure-ban lépjen a virtuális gép áttekintő oldalára, és válassza a **Csatlakozás** lehetőséget a Secure Shell-kapcsolat eléréséhez.

* Az összes megtekintése az eszközön üzembe helyezett modulokat, és ellenőrizze azok állapotát:

   ```bash
   iotedge list
   ```

   Négy modulnak kell lennie: a két IoT Edge modul, a SimulatedTemperatureSensor és a SampleModule. Mind a négynek futónak kell lennie a listán.

* Vizsgálja meg egy adott modul naplóit:

   ```bash
   iotedge logs <module name>
   ```

   IoT Edge modulok megkülönböztetik a kis- és nagybetűket.

   A SimulatedTemperatureSensor és a SampleModule naplóknak meg kell mutatniuk az üzenetek feldolgozását. Az edgeAgent modul felelős a többi modul indításáért, így a naplói az üzembehely helyezési jegyzék megvalósításáról tartalmaznak információkat. Ha valamelyik modul nem szerepel a listában, vagy nem fut, az edgeAgent naplók valószínűleg hibákat fognak tartalmazni. Az edgeHub-modul felelős a modulok és az IoT Hub. Ha a modulok futnak, de az üzenetek nem érkeznek meg az IoT Hubra, az edgeHub-naplók valószínűleg hibát fognak tartalmazni.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy a következő ajánlott cikkel folytatja, megtarthatja és újból felhasználhatja a létrehozott erőforrásokat és konfigurációkat. Azt is megteheti, hogy ugyanezt az IoT Edge-eszközt használja teszteszközként.

Ellenkező esetben a díjak elkerülése érdekében törölheti a cikkben használt helyi konfigurációkat és Azure-erőforrásokat.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban beállította a Visual Studio Code-et a fejlesztői gépen, és üzembe IoT Edge első modulját. Most, hogy már ismeri az alapfogalmakat, próbáljon funkciókat hozzáadni egy modulhoz, hogy elemezni tudja az áthaladó adatokat. Válassza ki a kívánt nyelvet:

> [!div class="nextstepaction"]
> [C](tutorial-c-module.md) 
>  [C#](tutorial-csharp-module.md) 
>  [Java](tutorial-java-module.md) 
>  [Node.js](tutorial-node-module.md) 
>  [Python](tutorial-python-module.md)
