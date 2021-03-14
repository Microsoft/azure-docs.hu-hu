---
title: 'Oktatóanyag: szimulált eszköz adatMachine Learningának előállítása Azure IoT Edge'
description: Oktatóanyag – szimulált telemetria létrehozó virtuális eszközök létrehozása, amelyek később a Machine learning-modellek betanítására használhatók.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 1/20/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: d5fdd762834d351119116c5e4854dd4233671c29
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/14/2021
ms.locfileid: "103463129"
---
# <a name="tutorial-generate-simulated-device-data"></a>Oktatóanyag: szimulált eszköz-adatértékek előállítása

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Ebben a cikkben a Machine learning-betanítási adatok segítségével szimuláljuk a telemetria-t az Azure IoT Hubba küldő eszközt. Ahogy az a bevezetésben is szerepel, ez az oktatóanyag a [Turbofan motor-degradációs szimulációs adatkészletet](https://c3.nasa.gov/dashlink/resources/139/) használja, hogy szimulálja az adatok egy csoportból történő betanítását és tesztelését.

A kísérleti forgatókönyvben tudjuk, hogy:

* Az adatgyűjtés több többváltozós idősorozatból áll.
* Az egyes adatkészletek betanítási és tesztelési részhalmazokra vannak osztva.
* Minden idősorozat egy másik motorból származik.
* Az egyes motorok a kezdeti kopás és a gyártási variáció különböző mértékével kezdődnek.

Ebben az oktatóanyagban egy egyetlen adatkészlet (FD003) betanítási adatkészletét használjuk.

A valóságban minden motor egy független IoT-eszköz lenne. Ha nem áll rendelkezésre elérhető internetkapcsolattal rendelkező turbofan-motor, akkor ezekhez az eszközökhöz beépített szoftver áll rendelkezésére.

A szimulátor egy C#-program, amely a IoT Hub API-k használatával programozott módon regisztrálja a virtuális eszközöket a IoT Hub. Ezután elolvasjuk az egyes eszközök adatait a NASA által megadott adatkészletből, és egy szimulált IoT-eszköz használatával elküldhetik az IoT hub-ra. Az oktatóanyag ezen részének minden kódja megtalálható az adattár DeviceHarness könyvtárában.

A DeviceHarness projekt egy C# nyelven írt .NET Core-projekt, amely négy osztályból áll:

* **Program:** A felhasználói bevitel és a teljes koordináció kezeléséért felelős végrehajtási pont.
* **TrainingFileManager:** A kiválasztott adatfájl olvasásához és elemzéséhez felelős.
* **CycleData:** Egy, az üzenet formátumára konvertált fájl egyetlen sorát jelöli.
* **TurbofanDevice:** Olyan IoT-eszköz létrehozásáért felelős, amely egyetlen eszközhöz (idősorozat) tartozik, és az adatban továbbítja a IoT Hub.

A cikkben leírt feladatok elvégzése körülbelül 20 percet vesz igénybe.

Ennek a lépésnek a működésével egyenértékűnek kell lennie az eszközök fejlesztői és a felhőalapú fejlesztők által.

Az oktatóanyag ezen szakaszában a következőket sajátíthatja el:

> [!div class="checklist"]
>
> * Külső projekt beépítése a fejlesztési környezetbe.
> * Szimulált IoT-eszközök létrehozásához használja a minta DeviceHarness-projektet.
> * Megtekintheti a IoT Hub generált adatait.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk egy sorozat részét képezi a Azure Machine Learning IoT Edge-on való használatáról szóló oktatóanyaghoz. A sorozat minden cikke az előző cikkben található munkára épül. Ha ezt a cikket közvetlenül megérkezett, tekintse meg az [első cikket](tutorial-machine-learning-edge-01-intro.md) a sorozatban.

## <a name="configure-visual-studio-code-and-build-deviceharness-project"></a>A Visual Studio Code konfigurálása és a DeviceHarness-projekt létrehozása

1. Nyisson meg egy távoli asztali munkamenetet a fejlesztői virtuális géphez.

1. A Visual Studio Code-ban nyissa meg a `C:\source\IoTEdgeAndMlSample\DeviceHarness` mappát.

1. Mivel első alkalommal használja a bővítményeket ezen a gépen, egyes bővítmények frissítik és telepítik a függőségeiket. A rendszer kérheti a bővítmény frissítését. Ha igen, válassza az **Újratöltés ablak** lehetőséget.

   Ha a kimeneti ablakban OmniSharp hibák jelennek meg, el kell távolítania a C#-bővítményt.

1. A rendszer kérni fogja a szükséges eszközök hozzáadását a DeviceHarness. Válassza az **Igen** lehetőséget a hozzáadásához.

   * Az értesítés néhány másodpercig is eltarthat.
   * Ha kihagyta ezt az értesítést, ellenőrizze a jobb alsó sarokban található harang ikont.

   ![VS Code bővítmény előugró ablak](media/tutorial-machine-learning-edge-03-generate-data/add-required-assets.png)

1. A csomag függőségeinek visszaállításához válassza a **visszaállítás** lehetőséget.

   ![VS Code Restore prompt](media/tutorial-machine-learning-edge-03-generate-data/restore-package-dependencies.png)

   Ha nem kapja meg ezeket az értesítéseket, akkor nyissa meg a Visual Studio Code-ot, törölje a bin és az obj könyvtárakat a alkalmazásban `C:\source\IoTEdgeAndMlSample\DeviceHarness` , nyissa meg a Visual Studio Code-ot, majd nyissa meg újra

1. Ellenőrizze, hogy a környezet megfelelően van-e beállítva a Build, a **CTRL**  +  **SHIFT**  +  **B** vagy a **Terminal**  >  **Run Build feladat** aktiválásával.

1. A rendszer kéri, hogy válassza ki a futtatandó felépítési feladatot. Válassza a **Létrehozás** lehetőséget.

1. A Build lefut, és sikert jelző üzenetet küld.

   ![Sikeres kimeneti üzenet létrehozása](media/tutorial-machine-learning-edge-03-generate-data/build-success.png)

1. Ezt kiépítheti az alapértelmezett Build feladattal, ha kiválasztja a **terminál**  >  **konfigurálása alapértelmezett Build feladatát.** .. lehetőséget, majd kiválaszthatja a **Build** elemet a parancssorból.

## <a name="connect-to-iot-hub-and-run-deviceharness"></a>Kapcsolódás IoT Hub és DeviceHarness futtatása

Most, hogy létrehoztuk a projektet, kapcsolódjon az IoT hub-hoz a kapcsolati sztring eléréséhez, és figyelje az adatgenerálás folyamatát.

### <a name="sign-in-to-azure-in-visual-studio-code"></a>Bejelentkezés az Azure-ba a Visual Studio Code-ban

1. Jelentkezzen be az Azure-előfizetésbe a Visual Studio Code-ban a parancs paletta megnyitásával `Ctrl + Shift + P` vagy a parancs-paletta **megtekintésével**  >  .

1. Keresse meg az **Azure: Sign in** parancsot.

   Megnyílik egy böngészőablak, amely kéri a hitelesítő adatok megadását. Amikor a rendszer átirányítja a sikert jelző oldalra, lezárhatja a böngészőt.

### <a name="connect-to-your-iot-hub-and-retrieve-hub-connection-string"></a>Az IoT hub csatlakoztatása és a hub kapcsolati karakterláncának beolvasása

1. A Visual Studio Code Explorer alsó részén kattintson az **Azure IoT hub** -keretre a kibontásához.

1. A kibontott keretben kattintson a **IoT hub kiválasztása** elemre.

1. Ha a rendszer kéri, válassza ki az Azure-előfizetését, majd az IoT hubot.

1. További műveletekért kattintson az **Azure IoT hub** jobb oldalán lévő **..** . elemre. Válassza a **másolás IoT hub a kapcsolatok karakterlánca** lehetőséget.

   ![IoT Hub-kapcsolatok karakterláncának másolása](media/tutorial-machine-learning-edge-03-generate-data/copy-hub-connection-string.png)

### <a name="run-the-deviceharness-project"></a>A DeviceHarness projekt futtatása

1.   >  A Visual Studio Code Terminal megnyitásához válassza a **terminál** megtekintése lehetőséget.

   Ha nem jelenik meg a kérdés, nyomja le az ENTER billentyűt.

1. A terminálban írja be a következőt: `dotnet run`.

1. Amikor a rendszer kéri a IoT Hub kapcsolódási karakterláncot, illessze be az előző szakaszban másolt kapcsolódási karakterláncot.

1. Az **Azure IoT hub eszközök** keretében kattintson a frissítés gombra.

   ![IoT Hub eszközök listájának frissítése](media/tutorial-machine-learning-edge-03-generate-data/refresh-hub-device-list.png)

1. Vegye figyelembe, hogy az eszközök bekerülnek a IoT Hubba, és az eszközök zöld színnel jelennek meg, jelezve, hogy az eszközön keresztül történik az adatküldés. Miután az eszközök üzeneteket küldenek az IoT hubhoz, a kapcsolat megszakadt és kék színnel jelenik meg.

1. Megtekintheti az adott hubhoz küldött üzeneteket, ha a jobb gombbal rákattint valamelyik eszközre, és kiválasztja a **figyelés beépített esemény-végpontjának elindítása** lehetőséget. Az üzenetek a Visual Studio Code kimenet paneljén jelennek meg.

1. A figyelés leállításához kattintson az **Azure IoT hub** kimenet ablaktáblára, és válassza a **beépített esemény-végpont figyelésének leállítása** lehetőséget.

1. Hagyja, hogy az alkalmazás végrehajtsa a befejezést, ami néhány percet vesz igénybe.

## <a name="check-iot-hub-for-activity"></a>Tevékenység IoT Hubának keresése

A DeviceHarness által elküldett adatai a IoT hubhoz kerültek, ahol ellenőrizheti a Azure Portal.

1. Nyissa meg a [Azure Portalt](https://portal.azure.com/) , és navigáljon az oktatóanyaghoz létrehozott IoT hub-hoz.

1. A bal oldali ablaktábla **figyelés** területén válassza a **metrikák** lehetőséget.

1. A diagram definíciója lapon kattintson a **metrika** legördülő listára, görgessen le a listából, és válassza az **Útválasztás: tárolóba szállított adatok** elemet. A diagramnak meg kell jelennie annak a csúcsnak, amikor az adattovábbítás a Storage szolgáltatásba megtörténik.

   ![A diagram a tárolóhelyre történő adattároláskor megjeleníti a tüske](media/tutorial-machine-learning-edge-03-generate-data/iot-hub-usage.png)

## <a name="validate-data-in-azure-storage"></a>Az Azure Storage-ban tárolt adatellenőrzés

Az IoT hub számára elküldett adatait a rendszer átirányítja az előző cikkben létrehozott tárolóba. Nézzük meg a Storage-fiókban lévő adatgyűjtést.

1. Az Azure Portalon lépjen a tárfiókra.

1. A Storage-fiók navigátorában válassza a **Storage Explorer (előzetes verzió)** lehetőséget.

1. A Storage Explorerben válassza a **blob-tárolók** lehetőséget `devicedata` .

1. A tartalom ablaktáblán kattintson az IoT hub neveként a mappára, amelyet év, hónap, nap és óra követ. Ekkor több, a perceket jelképező mappa jelenik meg, amikor az adatgyűjtés megtörténik.

   ![Mappák megtekintése a blob Storage-ban](media/tutorial-machine-learning-edge-03-generate-data/confirm-data-storage-results.png)

1. Kattintson az egyik mappára a partícióhoz tartozó **00** és **01** adatfájlok kereséséhez.

1. A fájlok [Avro](https://avro.apache.org/) formátumban vannak írva. Az egyik fájlra duplán kattintva nyisson meg egy másik böngésző fület, és részben jelenítse meg az adathalmazt. Ha a program kéri, hogy nyissa meg a fájlt egy programban, válassza ki a VS Code-ot, és helyesen fog megjelenni.

1. Az adatolvasásra és az adatelemzésre jelenleg nincs szükség. ezt a következő cikkben fogjuk elvégezni.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ez az oktatóanyag egy olyan készlet része, amelyben az egyes cikkek az előzőekben végzett munkára épülnek. Várjon, amíg az összes erőforrást el nem végezte, amíg el nem végzi a végleges oktatóanyagot.

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben egy .NET Core-projekttel hoztunk létre virtuális IoT-eszközöket, és a rajtuk keresztül továbbítjuk azokat az IoT hub és egy Azure Storage-tárolóba. Ez a projekt olyan valós helyzetet szimulál, amelyben a fizikai IoT-eszközök az adatküldés egy IoT Hubba és egy egy kurátori tárolóba kerülnek. Ezek az információk az érzékelők, az üzemeltetési beállítások, a meghibásodási jelek és a módok, valamint így tovább. Ha elegendő adatokat gyűjtöttek össze, azt használjuk a modell betanítására, amely megjósolja az eszköz hátralévő hasznos élettartamát (RUL). Ezt a Machine learninget a következő cikkben mutatjuk be.

Folytassa a következő cikkel, amellyel betaníthat egy gépi tanulási modellt az adatelemzéssel.

> [!div class="nextstepaction"]
> [Azure Machine Learning-modell betanítása és üzembe helyezése](tutorial-machine-learning-edge-04-train-model.md)
