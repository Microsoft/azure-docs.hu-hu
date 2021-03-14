---
title: Oktatóanyag – Azure Machine Learning üzembe helyezése egy eszközön a Azure IoT Edge használatával
description: Ebben az oktatóanyagban létrehoz egy Azure Machine Learning modellt, majd üzembe helyezi modulként egy peremhálózati eszközön
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/29/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 292c82624103fc2eae46d8aecb5e85b2181e7938
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/14/2021
ms.locfileid: "103463051"
---
# <a name="tutorial-deploy-azure-machine-learning-as-an-iot-edge-module-preview"></a>Oktatóanyag: Az Azure Machine Learning üzembe helyezése IoT Edge-modulként (előzetes verzió)

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

A Azure Notebooks használatával fejlesztheti a Machine learning modult, és üzembe helyezheti azt egy Azure IoT Edge rendszert futtató linuxos eszközön.
Az IoT Edge-modulokkal olyan kódot helyezhet üzembe, amely közvetlenül az IoT Edge-eszközökön implementálja az üzleti logikát. Ez az oktatóanyag végigvezeti egy olyan Azure Machine Learning-modul üzembe helyezésén, amely előrejelzi az eszközök meghibásodását szimulált géphőmérsékleti adatok alapján. A IoT Edge Azure Machine Learningával kapcsolatos további információkért tekintse meg a [Azure Machine learning dokumentációját](../machine-learning/how-to-deploy-and-where.md).

>[!NOTE]
>Az Azure Machine Learning moduljai nyilvános előzetes verzióban érhetők el az Azure IoT Edge szolgáltatásban.

Az ebben az oktatóanyagban létrehozott Azure Machine Learning-modul kiolvassa az eszköz által előállított környezeti adatokat, illetve rendellenesként vagy nem rendellenesként jelöli meg az üzeneteket.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Hozzon létre egy Azure Machine Learning modult.
> * Egy modul tárolójának leküldése egy Azure Container registrybe.
> * Helyezzen üzembe egy Azure Machine Learning modult a IoT Edge eszközön.
> * A létrejött adatok megtekintése.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Egy Azure IoT Edge-eszköz:

* A rövid útmutatók segítségével [Linux-eszközt](quickstart-linux.md) vagy [Windows-eszközt](quickstart.md)állíthat be.
* A Azure Machine Learning modul nem támogatja a Windows-tárolókat.
* Az Azure Machine Learning modul nem támogatja az ARM-processzorokat.

Felhőerőforrások:

* Egy ingyenes vagy standard szintű [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) az Azure-ban.
* Egy Azure Machine Learning-munkaterület. Kövesse a Azure Portal használatának első lépései című témakör útmutatását, és Ismerje meg, hogyan használhatja [a Azure Machine learning](../machine-learning/tutorial-1st-experiment-sdk-setup.md) .
  * Jegyezze fel a munkaterület nevét, az erőforráscsoportot és az előfizetés AZONOSÍTÓját. Ezek az értékek a Azure Portal munkaterület áttekintésében érhetők el. Ezeket az értékeket az oktatóanyag későbbi részében fogja használni Azure Notebooks-fájlnak a munkaterület-erőforrásokhoz való összekapcsolásához.

## <a name="create-and-deploy-azure-machine-learning-module"></a>Azure Machine Learning modul létrehozása és üzembe helyezése

Ebben a szakaszban a betanított gépi tanulási modell fájljait és egy Azure Machine Learning tárolóba alakítja át. A Docker-rendszerképhez szükséges összetevők megtalálhatók az [Azure IoT Edge GitHub-adattár AI-eszközkészletében](https://github.com/Azure/ai-toolkit-iot-edge/tree/master/IoT%20Edge%20anomaly%20detection%20tutorial). Az alábbi lépéseket követve feltöltheti a tárházat Microsoft Azure Notebooksba a tároló létrehozásához, és leküldheti a Azure Container Registryba.

1. Navigáljon a Azure Notebooks projektjeihez. A [Azure Portal](https://portal.azure.com) Azure Machine learning munkaterületéről, illetve az Azure-fiókkal való bejelentkezéshez is bejelentkezhet a [Microsoft Azure Notebooksba](https://notebooks.azure.com/home/projects) .

2. Válassza a GitHub-tárház **feltöltése** lehetőséget.

3. Adja meg a következő GitHub-tárház nevét: `Azure/ai-toolkit-iot-edge` . Ha meg szeretné őrizni a saját projektet, törölje a **nyilvános** mezőt. Válassza az **Importálás** lehetőséget.

4. Az importálás befejezése után navigáljon az új **AI-Toolkit-IOT-Edge** projekthez, és nyissa meg a **IoT Edge anomália észlelése oktatóanyag** mappát.

5. Ellenőrizze, hogy a projekt fut-e. Ha nem, válassza **a Futtatás ingyenes számításkor** lehetőséget.

   ![Futtatás ingyenes számítási feladatokban](./media/tutorial-deploy-machine-learning/run-on-free-compute.png)

6. Nyissa meg a **aml_config/config.js** fájlt.

7. Szerkessze a konfigurációs fájlt, hogy tartalmazza az Azure-előfizetés AZONOSÍTÓjának, az előfizetéshez tartozó erőforráscsoport, valamint a Azure Machine Learning munkaterület nevét. Ezek az értékek az Azure-beli munkaterület **Áttekintés** szakaszában olvashatók be.

8. Mentse a konfigurációs fájlt.

9. Nyissa meg a **00-anomália-Detection-tutorial. ipynb** fájlt.

10. Ha a rendszer kéri, válassza ki a **Python 3,6** kernelt, majd válassza a **kernel beállítása** lehetőséget.

11. Szerkessze a jegyzetfüzet első celláját a megjegyzések részben leírtak szerint. Használja ugyanazt az erőforráscsoportot, az előfizetés AZONOSÍTÓját és a munkaterület nevét, amelyet a konfigurációs fájlhoz adott hozzá.

12. Futtassa a cellákat a jegyzetfüzetben, és válassza ki őket, és válassza a **Futtatás** vagy a lenyomva lehetőséget `Shift + Enter` .

    >[!TIP]
    >Az anomália-észlelési oktatóanyagban található jegyzetfüzetek némelyike nem kötelező, mert olyan erőforrásokat hoz létre, amelyeket egyes felhasználók még nem használhatnak, mint például egy IoT Hub. Ha az első cellába helyezi a meglévő erőforrásadatokat, hibaüzeneteket fog kapni, ha olyan cellákat futtat, amelyek új erőforrásokat hoznak létre, mert az Azure nem hoz létre duplikált erőforrásokat. Ez rendben van, így figyelmen kívül hagyhatja a hibákat, vagy kihagyhatja a választható szakaszt.

A jegyzetfüzet összes lépésének elvégzésével elvégezte az anomáliák észlelésének modelljét, amely Docker-tárolói rendszerképként lett létrehozva, és leküldte a rendszerképet Azure Container Registry. Ezután tesztelte a modellt, és végül üzembe helyezte a IoT Edge eszközön.

## <a name="view-container-repository"></a>Tároló adattárának megtekintése

Győződjön meg arról, hogy a tároló rendszerképének létrehozása és tárolása a gépi tanulási környezethez társított Azure Container registryben történt. Az előző szakaszban használt jegyzetfüzet automatikusan átadja a tároló rendszerképét és a beállításjegyzék hitelesítő adatait a IoT Edge eszköz számára, de tudnia kell, hogy hol tárolja őket, így később is megtalálhatja az adatokat.

1. A [Azure Portal](https://portal.azure.com)navigáljon a Machine learning szolgáltatás munkaterületére.

2. Az **Áttekintés** szakasz a munkaterület részleteit, valamint a hozzájuk tartozó erőforrásokat sorolja fel. Válassza ki a **beállításazonosító** értékét, amelynek a munkaterület nevét, majd a véletlenszerűen megadott számokat kell megadnia.

3. A tároló-beállításjegyzék **szolgáltatások** területén válassza a **tárolók** lehetőséget. Meg kell jelennie egy **tempanomalydetection** nevű tárháznak, amelyet a korábbi szakaszban futtatott jegyzetfüzetből hoztak létre.

4. Válassza a **tempanomalydetection** lehetőséget. Látnia kell, hogy az adattárnak van egy címkéje: **1**.

   Most, hogy már ismeri a beállításjegyzék nevét, a tárház nevét és a címkét, ismeri a tároló teljes rendszerképének elérési útját. A képelérési utak a következőképpen néznek ki: **\<registry_name\> . azurecr.IO/tempanomalydetection:1**. A lemezkép elérési útjával üzembe helyezheti a tárolót IoT Edge-eszközökön.

5. A tároló beállításjegyzékének **Beállítások** területén válassza a **hozzáférési kulcsok** elemet. Meg kell jelennie a hozzáférési hitelesítő adatok számának, beleértve a **bejelentkezési kiszolgálót** , valamint a **felhasználónevet** és a **jelszót** a rendszergazda felhasználó számára.

   Ezeket a hitelesítő adatokat belefoglalhatja az üzembe helyezési jegyzékbe, az IoT Edge-eszköznek így hozzáférése lesz a regisztrációs adatbázis tárolólemezképeihez.

Most már tudja, hol tárolja a Machine Learning tároló képét. A következő szakasz végigvezeti a IoT Edge eszközön modulként futó tároló megtekintésének lépésein.

## <a name="view-the-generated-data"></a>A létrejött adatok megtekintése

Megtekintheti az egyes IoT Edge-modulok által létrehozott üzeneteket, és megtekintheti az IoT-központjába küldött üzeneteket.

### <a name="view-data-on-your-iot-edge-device"></a>Adatok megtekintése az IoT Edge-eszközön

IoT Edge-eszközén megtekintheti az egyes modulok által küldött üzeneteket.

Előfordulhat, hogy `sudo` a parancsok futtatásához emelt szintű engedélyeket kell használnia `iotedge` . A kijelentkezés és a visszajelentkezés az eszközre automatikusan frissíti az engedélyeket.

1. Tekintse meg az összes modult az IoT Edge-eszközön.

   ```cmd/sh
   iotedge list
   ```

2. Tekintse meg az egy adott eszközről küldött üzeneteket. Használja az előző parancs kimenetéből származó modulnevet.

   ```cmd/sh
   iotedge logs <module_name> -f
   ```

### <a name="view-data-arriving-at-your-iot-hub"></a>Az IoT-központba érkező adatok megtekintése

Megtekintheti az IoT hub által a [Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)-hoz készült Azure IoT hub-bővítmény által fogadott, az eszközről a felhőbe irányuló üzeneteket.

A következő lépések azt mutatják be, hogyan állítható be a Visual Studio Code az IoT-központba érkező, eszközről a felhőbe küldött üzenetek monitorozására.

1. A Visual Studio Code Explorer **Azure IoT hub** szakasza alatt bontsa ki az **eszközök** elemet a IoT-eszközök listájának megtekintéséhez.

2. Kattintson a jobb gombbal a IoT Edge eszköz nevére, és válassza a **figyelés beépített esemény végpontjának elindítása** lehetőséget.

3. Figyelje meg a tempSensortól érkező üzeneteket öt másodpercenként. Az üzenettörzs tartalmaz egy **anomaly** nevű tulajdonságot, amelyet a machinelearningmodule igaz vagy hamis értékkel ad meg. Az **AzureMLResponse** tulajdonság az „OK” értéket tartalmazza, ha a modell sikeresen lefutott.

   ![Azure Machine Learning válasz az üzenet törzsében](./media/tutorial-deploy-machine-learning/ml-output.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy a következő ajánlott cikkel folytatja, megtarthatja és újból felhasználhatja a létrehozott erőforrásokat és konfigurációkat. Azt is megteheti, hogy ugyanezt az IoT Edge-eszközt használja teszteszközként.

Ellenkező esetben a díjak elkerülése érdekében törölheti a jelen cikkben létrehozott helyi konfigurációkat és Azure-erőforrásokat.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban üzembe helyezett egy, az Azure Machine Learning által működtetett IoT Edge-modult. Továbbléphet bármely másik oktatóanyagra, és megtudhatja, milyen más módokon alakíthatja jelentőséggel bíró üzleti információkká ezeket az adatokat a peremhálózaton az Azure IoT Edge segítségével.

> [!div class="nextstepaction"]
> [Képek osztályozása a Custom Vision szolgáltatás használatával](tutorial-deploy-custom-vision.md)
