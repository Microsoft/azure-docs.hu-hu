---
title: Oktatóanyag – modul fejlesztése Windows-eszközökhöz Azure IoT Edge használatával
description: Ez az oktatóanyag végigvezeti a fejlesztői gép és a felhő erőforrásainak beállításán, hogy IoT Edge modulokat fejlesszen a Windows-eszközökön Windows-tárolók használatával
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/30/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: b5ff515f5a6d25285009f6579570aa3afa5a711f
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/14/2021
ms.locfileid: "103463357"
---
# <a name="tutorial-develop-iot-edge-modules-using-windows-containers"></a>Oktatóanyag: IoT Edge-modulok fejlesztése Windows-tárolók használatával

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

A Visual Studióval hozhat létre és helyezhet üzembe programkódot a IoT Edge rendszert futtató Windows-eszközökön.

>[!NOTE]
>IoT Edge 1,1 LTS az utolsó kiadási csatorna, amely támogatni fogja a Windows-tárolókat. A 1,2-es verziótól kezdődően a Windows-tárolók nem támogatottak. A Windows rendszerű eszközökön [IoT Edge Linux rendszeren](iot-edge-for-linux-on-windows.md) való használatának vagy áthelyezésének megfontolnia, hogy Windows-eszközökön fusson IoT Edge.

Ez az oktatóanyag végigvezeti a saját programkódjának IoT Edge eszközön való fejlesztéséhez és üzembe helyezéséhez szükséges lépéseken. Ez az oktatóanyag hasznos előfeltételt nyújt a többi oktatóanyaghoz, amely részletesen ismerteti az egyes programozási nyelveket és az Azure-szolgáltatásokat.

Ez az oktatóanyag egy **C#-modul Windows-eszközre** történő központi telepítésének példáját használja. Ez a példa azért lett kiválasztva, mert ez a leggyakoribb fejlesztési forgatókönyv. Ha más nyelven fejleszti a fejlesztést, vagy az Azure-szolgáltatások modulként való üzembe helyezését tervezi, akkor ez az oktatóanyag továbbra is hasznos lehet a fejlesztői eszközök megismeréséhez. A fejlesztési fogalmak megismerése után kiválaszthatja az Ön által választott nyelvet vagy Azure-szolgáltatást, és megismerheti a részleteket.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Állítsa be a fejlesztői gépet.
> * Hozzon létre egy új projektet a Visual studióhoz készült IoT Edge Tools használatával.
> * Készítse el a projektet tárolóként, és tárolja egy Azure Container registryben.
> * A kód üzembe helyezése egy IoT Edge eszközön.

## <a name="prerequisites"></a>Előfeltételek

Egy fejlesztői gép:

* Windows 10 1809-es vagy újabb frissítéssel.
* A fejlesztési beállításoktól függően saját számítógépet vagy virtuális gépet is használhat.
  * Győződjön meg arról, hogy a fejlesztői számítógép támogatja a beágyazott virtualizálás szolgáltatást. Ez a funkció a következő szakaszban telepítendő tároló-motor futtatásához szükséges.
* Telepítse a [git](https://git-scm.com/)-t.

Egy Azure IoT Edge eszköz a Windows rendszerben:

* [Azure IoT Edge telepítése és kezelése Windows-tárolókkal](how-to-install-iot-edge-windows-on-windows.md).
* Javasoljuk, hogy ne futtasson IoT Edge a fejlesztői gépen, hanem ha lehetséges, használjon külön eszközt. A fejlesztési gép és a IoT Edge eszköz közötti különbségtétel pontosabban tükrözi a valódi üzembe helyezési forgatókönyvet, és segít a különböző fogalmak közvetlen megtartásában.

Felhőerőforrások:

* Egy ingyenes vagy standard szintű [IoT hub](../iot-hub/iot-hub-create-through-portal.md) az Azure-ban.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="key-concepts"></a>Fő fogalmak

Ez az oktatóanyag végigvezeti egy IoT Edge modul fejlesztésén. Egy *IoT Edge modul*, vagy néha csak a *modul* rövid, egy olyan tároló, amely végrehajtható kódot tartalmaz. IoT Edge eszközön egy vagy több modult is telepíthet. A modulok olyan konkrét feladatokat látnak el, mint például az érzékelőkből származó adatok beolvasása, az adatelemzési vagy adattisztítási műveletek végrehajtása, illetve üzenetek küldése egy IoT hubhoz. További információ: Azure IoT Edge- [modulok megismerése](iot-edge-modules.md).

IoT Edge modulok fejlesztésekor fontos megérteni a fejlesztési gép és a cél IoT Edge eszköz közötti különbséget, ahol a modul végül üzembe lesz helyezve. A modul kódjának tárolására felépített tárolónak meg kell egyeznie a *céleszköz* operációs rendszerével (os). A Windows-tárolók fejlesztéséhez ez a koncepció egyszerűbb, mivel a Windows-tárolók csak Windows operációs rendszereken futnak. Előfordulhat például, hogy a Windows fejlesztői gépe segítségével modulokat készít a linuxos IoT Edge eszközökhöz. Ebben az esetben meg kell győződnie arról, hogy a fejlesztői számítógép Linux-tárolókat futtatott. Ebben az oktatóanyagban ne feledje, hogy a *fejlesztői gép operációs rendszere* és a *tároló operációs rendszer* közötti különbség.

Ez az oktatóanyag a IoT Edge rendszert futtató Windows-eszközöket célozza meg. A Windows IoT Edge-eszközök Windows-tárolókat használnak. Javasoljuk, hogy a Visual Studio használatával fejlesszen a Windows-eszközökön, hogy ez az oktatóanyag milyen módon fog használatba venni. Használhatja a Visual Studio Code-ot is, de a két eszköz között eltérések vannak.

A következő táblázat a **Windows-tárolók** támogatott fejlesztési forgatókönyveit sorolja fel a Visual Studio Code és a Visual Studio alkalmazásban.

|   | Visual Studio Code | Visual Studio 2017/2019 |
| - | ------------------ | ------------------ |
| **Azure-szolgáltatások** | Azure Functions <br> Azure Stream Analytics |   |
| **Nyelvek** | C# (a hibakeresés nem támogatott) | C# <br> C# |
| **További információ** | [Azure IoT Edge a Visual Studio Code-hoz](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) | [Azure IoT Edge Tools for Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools)<br>[Azure IoT Edge Tools for Visual Studio 2019](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) |

## <a name="install-container-engine"></a>Tároló-motor telepítése

IoT Edge modulok tárolóként vannak csomagolva, ezért a tárolók létrehozásához és kezeléséhez szüksége lesz egy tároló motorra a fejlesztői gépen. Azt javasoljuk, hogy a Docker Desktopot a számos funkciójának és a népszerű tároló-motornak a használatával fejlessze. A Windows rendszerű számítógépeken a Docker Desktop segítségével válthat a Linux-tárolók és a Windows-tárolók között, így egyszerűen fejleszthet különböző típusú IoT Edge eszközökhöz tartozó modulokat.

Telepítse a Docker dokumentációját a fejlesztői gépre való telepítéshez:

* [A Windows rendszerhez készült Docker asztali verziójának telepítése](https://docs.docker.com/docker-for-windows/install/)

  * A Windows rendszerhez készült Docker Desktop telepítésekor megkérdezi, hogy Linux-vagy Windows-tárolókat kíván-e használni. Ebben az oktatóanyagban használja a **Windows-tárolókat**. További információ: váltás a [Windows és a Linux rendszerű tárolók között](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).

## <a name="set-up-visual-studio-and-tools"></a>A Visual Studio és az eszközök beállítása

A Visual studióhoz készült IoT-bővítmények segítenek IoT Edge modulok fejlesztésében. Ezek a bővítmények a Project sablonjait biztosítják, automatizálják az üzembe helyezési jegyzék létrehozását, és lehetővé teszik IoT Edge-eszközök figyelését és kezelését. Ebben a szakaszban a Visual studiót és a IoT Edge-bővítményt telepíti, majd beállíthatja az Azure-fiókját, hogy az IoT Hub erőforrásokat a Visual studióból kezelhesse.

Ez az oktatóanyag a Visual Studio 2019 fejlesztési lépéseit tanítja. Ha a Visual Studio 2017-es verzióját (15,7-es vagy újabb verzió) használja, a lépések hasonlóak. Ha inkább a Visual Studio Code-ot szeretné használni, tekintse meg a [Visual Studio Code használata a Azure IoT Edge moduljainak fejlesztéséhez és hibakereséséhez](how-to-vs-code-develop-module.md)című témakör útmutatását.

1. Készítse elő a Visual Studio 2019-et a fejlesztői gépen.

   * Ha még nem rendelkezik a Visual Studióval a fejlesztői gépen, [telepítse a Visual studio 2019](/visualstudio/install/install-visual-studio) -et a következő munkaterhelésekkel:

      * Azure-fejlesztés
      * Asztali fejlesztés C++ nyelven
      * .NET Core platformfüggetlen fejlesztés

   * Ha már rendelkezik a Visual Studio 2019-rel a fejlesztői gépen, kövesse a [Visual Studio módosítása](/visualstudio/install/modify-visual-studio) a szükséges számítási feladatok hozzáadásához című témakör lépéseit.

2. Töltse le és telepítse a Visual Studio 2019 [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) bővítményét.

   Ha a Visual Studio 2017-es verzióját (15,7-es vagy újabb verziót) használja, töltse le és telepítse a [Visual studio 2017 Azure IoT Edge eszközeit](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools).

3. A telepítések befejezése után nyissa meg a Visual Studio 2019 alkalmazást, és válassza a **Folytatás kód nélkül** lehetőséget.

4. Válassza a  >  **Cloud Explorer** megtekintése lehetőséget.

5. Válassza a profil ikont a Cloud Explorerben, és jelentkezzen be az Azure-fiókjába, ha még nincs bejelentkezve.

6. Miután bejelentkezett, megjelenik az Azure-előfizetések listája. Bontsa ki az IoT hub-előfizetést.

7. Az előfizetés alatt bontsa ki a **IoT hubok** csomópontot, majd az IoT hubot. Ekkor meg kell jelennie a IoT-eszközök listájának, és az Intéző használatával felügyelheti őket.

   ![Hozzáférés IoT Hub erőforrásokhoz a Cloud Explorerben](./media/tutorial-develop-for-windows/cloud-explorer-view-hub.png)

[!INCLUDE [iot-edge-create-container-registry](../../includes/iot-edge-create-container-registry.md)]

## <a name="create-a-new-module-project"></a>Új modul-projekt létrehozása

A Azure IoT Edge Tools bővítmény a Visual Studióban az összes támogatott IoT Edge modul nyelvéhez biztosít Project sablonokat. Ezekben a sablonokban minden olyan fájl és kód szerepel, amelyre a IoT Edge teszteléséhez egy munkamodult kell telepítenie, vagy egy kiindulási pontot kell adnia a sablon saját üzleti logikával való testreszabásához.

1. Válassza a **fájl**  >  **új**  >  **projekt... lehetőséget.**

2. Az új projekt ablakban keresse meg **IoT Edge** , és válassza ki a **Azure IoT Edge (Windows amd64)** projektet. Kattintson a **Tovább** gombra.

   ![Új Azure IoT Edge projekt létrehozása](./media/tutorial-develop-for-windows/new-project.png)

3. Az új projekt konfigurálása ablakban nevezze át a projektet és a megoldást valami olyan leíróra, mint a **CSharpTutorialApp**. A projekt létrehozásához kattintson a **Létrehozás** gombra.

   ![Új Azure IoT Edge projekt konfigurálása](./media/tutorial-develop-for-windows/configure-project.png)

4. A modul hozzáadása ablakban konfigurálja a projektet a következő értékekkel:

   | Mező | Érték |
   | ----- | ----- |
   | Visual Studio-sablon | Válassza a **C# modul** lehetőséget. |
   | Modulnév | Fogadja el az alapértelmezett **IotEdgeModule1**. |
   | Tárház URL-címe | Egy rendszerképadattár a tárolóregisztrációs adatbázis nevét és a tárolórendszerkép nevét tartalmazza. A tároló képe előre fel van töltve a modul projekt neve értékből. Cserélje le a **localhost: 5000** értéket a **bejelentkezési kiszolgáló** értékére az Azure Container registryben. A bejelentkezési kiszolgáló értékét a Azure Portal tároló-beállításjegyzékének áttekintés lapjáról kérheti le. <br><br> A rendszerkép utolsó tárháza a következőhöz hasonlít: \<registry name\> . azurecr.IO/iotedgemodule1. |

      ![A projekt konfigurálása a céleszköz, a modul típusa és a tároló-beállításjegyzék számára](./media/tutorial-develop-for-windows/add-module-to-solution.png)

5. A modul létrehozásához válassza a **Hozzáadás** lehetőséget.

Ha az új projekt betöltődik a Visual Studio ablakába, szánjon egy kis időt a létrehozott fájlok megismerésére:

* Egy **CSharpTutorialApp** nevű IoT Edge-projekt.
  * A **modulok** mappa mutatókat tartalmaz a projektben szereplő modulokhoz. Ebben az esetben csak IotEdgeModule1 kell lennie.
  * A Hidden **. env** fájl tárolja a tároló beállításjegyzékének hitelesítő adatait. Ezeket a hitelesítő adatokat a rendszer megosztja a IoT Edge eszközzel, hogy hozzáférhessen a tároló lemezképének lekéréséhez.
  * A fájl **deployment.template.js** egy olyan sablon, amely segítséget nyújt a központi telepítési jegyzék létrehozásához. Az *üzembe helyezési jegyzék* egy olyan fájl, amely pontosan meghatározza, hogy mely modulokat kívánja telepíteni az eszközön, hogyan legyenek konfigurálva, és hogyan kommunikálnak egymással és a felhővel.
    > [!TIP]
    > A beállításjegyzékbeli hitelesítő adatok szakaszban a rendszer a megoldás létrehozásakor megadott információk alapján tölti ki a címeket. A Felhasználónév és a jelszó hivatkozási változói azonban a. env fájlban tárolódnak. Ez a biztonság érdekében, mivel az. env fájlt a git figyelmen kívül hagyja, de a telepítési sablon nem.
* Egy IoT Edge **IotEdgeModule1** nevű modul-projekt.
  * A **program.cs** fájl tartalmazza a Project sablonhoz tartozó alapértelmezett C# modul kódját. Az alapértelmezett modul beírja a forrásból a bemenetet, és átadja a IoT Hubnak.
  * A fájlban lévő **module.js** a modul részletes adatait tartalmazza, beleértve a teljes rendszerkép tárházát, a képváltozatot, valamint azt, hogy melyik Docker kell használni az egyes támogatott platformokhoz.

### <a name="provide-your-registry-credentials-to-the-iot-edge-agent"></a>Adja meg a beállításjegyzékbeli hitelesítő adatait a IoT Edge-ügynöknek

A IoT Edge futtatókörnyezetnek szüksége van a beállításjegyzékbeli hitelesítő adataira, hogy a tároló lemezképeit a IoT Edge eszközre húzza. A IoT Edge bővítmény megpróbálja lekérni a tároló beállításjegyzék-információit az Azure-ból, és feltölti azt a telepítési sablonban.

1. Nyissa meg a **deployment.template.js** fájlt a modul-megoldásban.

1. Keresse meg a **registryCredentials** tulajdonságot a $edgeAgent kívánt tulajdonságban. A szolgáltatásnak a projekt létrehozásakor megadott információk alapján kell kitöltenie a beállításjegyzékbeli címeket, majd a Felhasználónév és a jelszó mezőben a változók nevét kell tartalmaznia. Például:

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "$CONTAINER_REGISTRY_USERNAME_<registry name>",
       "password": "$CONTAINER_REGISTRY_PASSWORD_<registry name>",
       "address": "<registry name>.azurecr.io"
     }
   }
   ```

1. Nyissa meg a **. env** fájlt a modul-megoldásban. (Alapértelmezés szerint rejtett a Megoldáskezelőban, ezért előfordulhat, hogy az **összes fájl megjelenítése** gombra kell kattintania a megjelenítéséhez.)

1. Adja meg az Azure Container registryből másolt **felhasználónevet** és a **jelszó** értékeit.

1. Mentse a módosításokat a. env fájlba.

### <a name="review-the-sample-code"></a>A mintakód áttekintése

A létrehozott megoldási sablon egy IoT Edge modulhoz tartozó mintakód-kódot tartalmaz. Ez a mintakód egyszerűen fogad üzeneteket, majd továbbítja azokat a következőre:. A folyamat funkciója a IoT Edge fontos koncepcióját mutatja be, ami azt szemlélteti, hogyan kommunikálnak a modulok egymással.

Minden modul több *bemeneti* és *kimeneti* várólistával is rendelkezhet a kódban. Az eszközön futó IoT Edge hub az egyik modul kimenetében lévő üzeneteket egy vagy több modul bemenetére irányítja. A bemenetek és kimenetek deklarálása adott kód a nyelvek között változik, de a koncepció ugyanaz, mint az összes modulban. További információ a modulok közötti útválasztásról: [útvonalak deklarálása](module-composition.md#declare-routes).

A Project sablonhoz tartozó C#-kód a [ModuleClient osztályt](/dotnet/api/microsoft.azure.devices.client.moduleclient) használja a .net-hez készült IoT hub SDK-ból.

1. A **program.cs** fájlban keresse meg a **SetInputMessageHandlerAsync** metódust.

2. A [SetInputMessageHandlerAsync](/dotnet/api/microsoft.azure.devices.client.moduleclient.setinputmessagehandlerasync) metódus beállít egy bemeneti várólistát a bejövő üzenetek fogadásához. Tekintse át ezt a metódust, és nézze meg, hogyan inicializál egy **input1** nevű bemeneti várólistát.

   ![A bemeneti név megkeresése a SetInputMessageHandlserAsync konstruktorban](./media/tutorial-develop-for-windows/declare-input-queue.png)

3. Ezután keresse meg a **SendEventAsync** metódust.

4. A [SendEventAsync](/dotnet/api/microsoft.azure.devices.client.moduleclient.sendeventasync) metódus feldolgozza a fogadott üzeneteket, és beállítja a kimeneti várólistát, hogy átadja őket. Tekintse át ezt a metódust, és tekintse meg, hogy inicializál egy **output1** nevű kimeneti várólistát.

   ![A kimeneti név megkeresése a SendEventAsync konstruktorban](./media/tutorial-develop-for-windows/declare-output-queue.png)

5. Nyissa meg a **deployment.template.js** fájlt.

6. Keresse meg a $edgeAgent kívánt tulajdonságok **modulok** tulajdonságát.

   Itt két modulnak kell szerepelnie. Az egyik a **SimulatedTemperatureSensor** modul, amely alapértelmezés szerint az összes sablonban szerepel, hogy szimulált hőmérsékleti értékeket biztosítson, amelyeket a modulok tesztelésére használhat. A másik a projekt részeként létrehozott **IotEdgeModule1** -modul.

   Ez a modul-tulajdonság azt deklarálja, hogy mely modulok szerepeljenek az eszközön vagy az eszközökön való üzembe helyezésben.

7. Keresse meg a $edgeHub kívánt tulajdonságok **Routes (útvonalak** ) tulajdonságát.

   Az IoT Edge hub modul egyik funkciója az üzenetek továbbítása a központi telepítés összes modulja között. Tekintse át az útvonalak tulajdonságban található értékeket. Az egyik útvonal, a **IotEdgeModule1ToIoTHub**, helyettesítő karaktert (* *\** _) használ, hogy tartalmazza a IotEdgeModule1 modul bármely kimeneti sorból érkező üzenetet. Ezek az üzenetek a _ $ upstream * értékre kerülnek, amely a IoT Hubt jelző fenntartott név. A másik útvonal, a **sensorToIotEdgeModule1**, a SimulatedTemperatureSensor-modultól érkező üzeneteket fogad, és átirányítja azokat a IotEdgeModule1 modul *input1* bemeneti sorába.

   ![Útvonalak áttekintése deployment.template.json](./media/tutorial-develop-for-windows/deployment-routes.png)

## <a name="build-and-push-your-solution"></a>Megoldás létrehozása és leküldése

Áttekintette a modul kódját és a telepítési sablont, hogy megismerje a kulcsfontosságú telepítési fogalmakat. Most már készen áll arra, hogy létrehozza a IotEdgeModule1-tároló rendszerképét, és leküldi a tároló-beállításjegyzékbe. A Visual studióhoz készült IoT Tools bővítménnyel ez a lépés az üzembe helyezési jegyzékfájlt is létrehozza a sablonfájl információi és a modul információi alapján a megoldás fájljaiban.

### <a name="sign-in-to-docker"></a>Bejelentkezés a Docker-be

Adja meg a tároló beállításjegyzékbeli hitelesítő adatait a Docker számára a fejlesztői gépen, így a tároló rendszerképét leküldheti a beállításjegyzékben.

1. Nyisson meg egy PowerShell-vagy egy parancssort.

2. Jelentkezzen be a Docker-be a beállításjegyzék létrehozása után mentett Azure Container Registry hitelesítő adatokkal.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Biztonsági figyelmeztetés jelenhet meg, amely a használatát javasolja `--password-stdin` . Habár az ajánlott eljárás az éles környezetekben javasolt, az oktatóanyag hatókörén kívül esik. További információkért lásd a [Docker bejelentkezési](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) referenciáját.

### <a name="build-and-push"></a>Létrehozás és leküldés

A fejlesztői gép most már rendelkezik hozzáféréssel a tároló beállításjegyzékéhez, és a IoT Edge-eszközök is elérhetők lesznek. Itt az ideje, hogy bekapcsolja a projekt kódját egy tároló képébe.

1. Kattintson a jobb gombbal a **CSharpTutorialApp** projekt mappájára, és válassza a **IoT Edge modulok kiépítése és leküldése** lehetőséget.

   ![IoT Edge-modulok létrehozása és leküldése](./media/tutorial-develop-for-windows/build-and-push-modules.png)

   A build és a push parancs három műveletet indít el. Először létrehoz egy új mappát a **konfigurációban** , amely tartalmazza a teljes telepítési jegyzékfájlt, kiépítve a telepítési sablonban és más megoldási fájlokban található információkat. Másodszor, futtatja `docker build` a tároló rendszerképét a célként megadott architektúra megfelelő Docker alapján. Ezután futtatja, `docker push` hogy leküldi a rendszerkép-tárházat a tároló-beállításjegyzékbe.

   Ez a folyamat az első alkalommal több percet is igénybe vehet, de gyorsabb a parancsok következő futtatásakor.

2. Nyissa meg a **deployment.windows-amd64.js** fájlt az újonnan létrehozott konfigurációs mappában. (Előfordulhat, hogy a konfigurációs mappa nem jelenik meg a Megoldáskezelő a Visual Studióban. Ha ez a helyzet, jelölje be az **összes fájl megjelenítése** ikont a megoldáskezelő tálcán.)

3. Keresse meg a IotEdgeModule1 szakasz **rendszerkép** paraméterét. Figyelje meg, hogy a rendszerkép tartalmazza a teljes képtárat a fájl module.jsa név, a verzió és az architektúra címkével.

4. Nyissa meg a **module.js** fájlt a IotEdgeModule1 mappában.

5. Módosítsa a modul rendszerképének verziószámát. (A verzió, nem a $schema verziója.) Megnövelheti például a javítás verziószámát a **0.0.2** , mintha egy kis javítást hajtottunk végre a modul kódjában.

   >[!TIP]
   >A modul verziói lehetővé teszik a verziókövetés használatát, és lehetővé teszik, hogy a frissítések éles környezetben való telepítése előtt tesztelje a módosításokat az eszközök egy kis készletén. Ha nem növeli a modul verzióját a kiépítése és a kitolása előtt, írja felül a tárházat a tároló beállításjegyzékében.

6. Mentse a módosításokat a module.jsfájlba.

7. Kattintson ismét a **CSharpTutorialApp** projekt mappájára, majd válassza újra a **IoT Edge-modulok kiépítése és leküldése** lehetőséget.

8. Nyissa meg ismét a **deployment.windows-amd64.js** fájlt. Figyelje meg, hogy a build és a leküldéses parancs ismételt futtatásakor nem jött létre új fájl. Ehelyett ugyanez a fájl frissült, hogy tükrözze a módosításokat. A IotEdgeModule1-rendszerkép most a tároló 0.0.2-verziójára mutat. Az üzembe helyezési jegyzékben szereplő változás arról tájékoztatja a IoT Edge eszközt, hogy egy modul új verziója van a lekéréshez.

9. A build és a leküldéses parancs végrehajtásának további ellenőrzéséhez lépjen a [Azure Portal](https://portal.azure.com) , és keresse meg a tároló beállításjegyzékét.

10. A tároló beállításjegyzékében válassza a **tárak** , majd a **iotedgemodule1** lehetőséget. Ellenőrizze, hogy a rendszerkép mindkét verzióját leküldte-e a beállításjegyzékbe.

    ![A tároló beállításjegyzékének mindkét rendszerkép-verziójának megtekintése](./media/tutorial-develop-for-windows/view-repository-versions.png)

### <a name="troubleshoot"></a>Hibaelhárítás

Ha hibát tapasztal a modul rendszerképének létrehozásakor és leküldésekor, gyakran a Docker-konfigurációval kell végrehajtania a fejlesztői gépen. A konfiguráció áttekintéséhez használja a következő ellenőrzéseket:

* Futtatta a `docker login` parancsot a tároló-beállításjegyzékből másolt hitelesítő adatok használatával? Ezek a hitelesítő adatok eltérnek az Azure-ba való bejelentkezéshez használttól.
* Helyes a tároló-adattár? A tároló beállításjegyzékének megfelelő neve és a megfelelő modul neve? A IotEdgeModule1 mappában Nyissa meg a **module.js** fájlt, és keresse meg a következőt:. A tárház értékének a következőhöz hasonlóan kell kinéznie: **\<registry name\> . azurecr.IO/iotedgemodule1**.
* Ha a modul **IotEdgeModule1** eltérő nevet használt, az adott név konzisztens a megoldás során?
* A gép ugyanazon típusú tárolókat futtatja, amelyeket Ön épít? Ez az oktatóanyag a Windows IoT Edge eszközökhöz készült, ezért a Visual Studio-fájloknak Windows **-amd64** kiterjesztéssel kell rendelkezniük, és a Docker desktopnak Windows-tárolókat kell futtatnia.

## <a name="deploy-modules-to-device"></a>Modulok üzembe helyezése az eszközön

Ellenőrizte, hogy a rendszer a tároló beállításjegyzékében tárolja a beépített tároló lemezképeit, ezért itt az ideje, hogy egy eszközre telepítse őket. Ellenőrizze, hogy a IoT Edge eszköz működik-e.

1. Nyissa meg a Cloud Explorer alkalmazást a Visual Studióban, és bontsa ki az IoT hub részleteit.

2. Válassza ki a telepíteni kívánt eszköz nevét. A **műveletek** listában válassza a **központi telepítés létrehozása** elemet.

   ![Üzemelő példány létrehozása egyetlen eszközhöz](./media/tutorial-develop-for-windows/create-deployment.png)

3. A Fájlkezelőben navigáljon a projekt konfigurációs mappájához, és válassza ki a **deployment.windows-amd64.js** fájlt. Ez a fájl gyakran a következő helyen található: `C:\Users\<username>\source\repos\CSharpTutorialApp\CSharpTutorialApp\config\deployment.windows-amd64.json`

   Ne használja a deployment.template.jsa fájlon, amely nem rendelkezik a modul teljes rendszerképének értékével.

4. Bontsa ki a IoT Edge eszközének részleteit a Cloud Explorerben, és tekintse meg az eszközön található modulokat.

5. Az eszköz állapotának frissítéséhez használja a **refresh (frissítés** ) gombot, és ellenőrizze, hogy a SimulatedTemperatureSensor és a IotEdgeModule1 modulok telepítve vannak-e az eszközön.

   ![A IoT Edge eszközön futó modulok megtekintése](./media/tutorial-develop-for-windows/view-running-modules.png)

## <a name="view-messages-from-device"></a>Üzenetek megtekintése az eszközről

A IotEdgeModule1 kód üzeneteket fogad a bemeneti várólistán keresztül, és továbbítja azokat a kimeneti várólistán keresztül. Az üzembe helyezési jegyzék olyan útvonalakat jelentett be, amelyek üzeneteket továbbítanak a SimulatedTemperatureSensor a IotEdgeModule1-be, majd továbbítottak üzeneteket a IotEdgeModule1-ből a IoT Hubba. A Visual studióhoz készült Azure IoT Edge Tools lehetővé teszi, hogy az egyes eszközökről érkező IoT Hub megtekintse az üzeneteket.

1. A Visual Studio Cloud Explorerben válassza ki annak a IoT Edge eszköznek a nevét, amelyet központilag telepített.

2. A **műveletek** menüben válassza a **figyelés beépített esemény végpontjának elindítása** lehetőséget.

3. Tekintse meg a Visual Studio **output (kimenet** ) szakaszát, és tekintse meg az IoT hub-ra érkező üzeneteket.

   Eltarthat néhány percig, amíg mindkét modul el nem indul. Az IoT Edge futtatókörnyezetnek meg kell kapnia az új üzembe helyezési jegyzékfájlt, le kell kérnie a modul lemezképeit a tároló futtatókörnyezetből, majd el kell indítania az új modulokat.

   ![A bejövő eszköz Felhőbeli üzeneteinek megtekintése](./media/tutorial-develop-for-windows/view-d2c-messages.png)

## <a name="view-changes-on-device"></a>Az eszköz változásainak megtekintése

Ha szeretné megtekinteni, hogy mi történik az eszközön, használja az ebben a szakaszban található parancsokat az eszközön futó IoT Edge futtatókörnyezet és modulok vizsgálatához.

Az ebben a szakaszban található parancsok a IoT Edge eszközre, nem pedig a fejlesztői gépre vonatkoznak. Ha virtuális gépet használ a IoT Edge eszközhöz, kapcsolódjon hozzá most. Az Azure-ban nyissa meg a virtuális gép áttekintés lapját, és válassza a **Kapcsolódás** lehetőséget a távoli asztali kapcsolat eléréséhez. Az eszközön nyisson meg egy parancsot vagy egy PowerShell-ablakot a `iotedge` parancsok futtatásához.

* Tekintse meg az eszközre telepített összes modult, és tekintse meg az állapotukat:

   ```cmd
   iotedge list
   ```

   Négy modulnak kell megjelennie: a két IoT Edge futásidejű modul, a SimulatedTemperatureSensor és a IotEdgeModule1. Mind a négynek szerepelnie kell futtatásként.

* Egy adott modul naplófájljainak vizsgálata:

   ```cmd
   iotedge logs <module name>
   ```

   A IoT Edge modulok megkülönböztetik a kis-és nagybetűket.

   A SimulatedTemperatureSensor és a IotEdgeModule1 naplóknak meg kell jeleníteniük a feldolgozás alatt lévő üzeneteket. A edgeAgent modul felelős a többi modul indításához, így a naplók az üzembe helyezési jegyzék megvalósításával kapcsolatos információkat fognak tartalmazni. Ha valamelyik modul nem jelenik meg vagy nem fut, a edgeAgent-naplók valószínűleg a hibákkal fognak rendelkezni. A edgeHub modul felelős a modulok és a IoT Hub közötti kommunikációért. Ha a modulok működése folyamatban van, de az üzenetek nem érkeznek meg az IoT hub-ba, akkor a edgeHub-naplók valószínűleg a hibákkal fognak rendelkezni.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy a következő ajánlott cikkel folytatja, megtarthatja és újból felhasználhatja a létrehozott erőforrásokat és konfigurációkat. Azt is megteheti, hogy ugyanezt az IoT Edge-eszközt használja teszteszközként.

Ellenkező esetben törölheti a cikkben használt helyi konfigurációkat és az Azure-erőforrásokat a díjak elkerüléséhez.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban beállíthatja a Visual Studio 2019-et a fejlesztői gépen, és üzembe helyezheti az első IoT Edge modulját. Most, hogy már ismeri az alapfogalmakat, próbáljon hozzá funkciókat hozzáadni egy modulhoz, hogy az áthaladó adatok elemezhetők legyenek. Válassza ki a kívánt nyelvet:

> [!div class="nextstepaction"]
> [C](tutorial-c-module-windows.md) 
>  [C#](tutorial-csharp-module-windows.md)