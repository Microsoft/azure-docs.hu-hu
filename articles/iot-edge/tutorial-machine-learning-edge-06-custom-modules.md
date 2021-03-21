---
title: 'Oktatóanyag: egyéni modulok létrehozása és telepítése – Machine Learning on Azure IoT Edge'
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre és helyezhet üzembe olyan IoT Edge modulokat, amelyek a gépi tanulási modell segítségével dolgozzák fel az adatok a Leaf-eszközökről, majd az elemzések elküldésével IoT Hub.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 6/30/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: devx-track-csharp
ms.openlocfilehash: 5c096b26f31bdf92bb5ab91c8dad7876f228ff14
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103462796"
---
# <a name="tutorial-create-and-deploy-custom-iot-edge-modules"></a>Oktatóanyag: egyéni IoT Edge-modulok létrehozása és üzembe helyezése

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Ebben a cikkben három IoT Edge-modult hozunk létre, amelyek üzeneteket fogadnak a Leaf IoT-eszközökről, az adatok futtatását a gépi tanulási modellen keresztül, majd az elemzések továbbításával IoT Hub.

IoT Edge hub megkönnyíti a modul kommunikációját. Az IoT Edge hub használata a Message Broker számára egymástól független modulokat tart fenn. A moduloknak csak azokat a bemeneteket kell megadniuk, amelyeken üzeneteket fogadnak, valamint azokat a kimeneteket, amelyekhez üzeneteket írnak.

Azt szeretnénk, hogy a IoT Edge eszköz négy dolgot hajtson végre nekünk:

* Adatok fogadása a Leaf-eszközökről.
* Tippelje meg a hátralévő hasznos élettartamot (RUL) az információkat küldő eszköz számára.
* Üzenet küldése az eszköz RUL IoT Hub. Ez a függvény úgy módosítható, hogy csak akkor küldjön adatküldést, ha a RUL egy megadott szint alá csökken.
* Mentse a levélben tárolt eszközöket a IoT Edge eszköz helyi fájljába. Ezt az adatfájlt rendszeresen feltölti IoT Hub a Machine learning-modell képzésének pontosításához. A fájlok feltöltése a folyamatos üzenetküldés helyett költséghatékony.

Ezeknek a feladatoknak a végrehajtásához három egyéni modult használunk:

* **RUL osztályozó:** A turboFanRulClassifier modul, amelyet a [Betanításban hoztunk létre, és üzembe helyezünk egy Azure Machine learning modellt](tutorial-machine-learning-edge-04-train-model.md) egy standard Machine learning modul, amely egy "amlInput" nevű bemenetet és egy "amlOutput" nevű kimenetet tesz elérhetővé. A "amlInput" azt várja, hogy a bemenet pontosan ugyanúgy nézzen ki, mint az ACI-alapú webszolgáltatásnak továbbított bemenet. Hasonlóképpen, a "amlOutput" ugyanazokat az adatokkal adja vissza, mint a webszolgáltatás.

* **Avro-író:** Ez a modul fogadja az üzeneteket a "avroModuleInput" bemeneten, és Avro formátumban megőrzi az üzenetet a lemezre a későbbi feltöltéshez IoT Hub.

* **Útválasztó modul:** Az útválasztó modul üzeneteket fogad az alsóbb rétegbeli eszközökről, majd formázza és elküldi az üzeneteket az osztályozó számára. A modul ezután fogadja az üzeneteket az osztályozó és továbbítja az üzenetet a Avro-író modulnak. Végül a modul csak a RUL-előrejelzést küldi el a IoT Hub.

  * Bemenetek
    * **deviceInput**: üzenetek fogadása a Leaf-eszközökről
    * **rulInput:** üzenetek fogadása a "amlOutput"

  * Kimenetek
    * **besorolás:** üzenetek küldése "amlInput"
    * **writeAvro:** üzenetek küldése a "avroModuleInput" értékre
    * **toIotHub:** üzeneteket küld $upstream, amelyek továbbítják az üzeneteket a csatlakoztatott IoT hub

Az alábbi ábrán a modulok, a bemenetek, a kimenetek és a IoT Edge hub útvonalak láthatók a teljes megoldáshoz:

![IoT Edge három modul architektúrájának diagramja](media/tutorial-machine-learning-edge-06-custom-modules/modules-diagram.png)

A cikkben ismertetett lépéseket általában egy felhőalapú fejlesztő hajtja végre.

Az oktatóanyag ezen szakaszában a következőket sajátíthatja el:

> [!div class="checklist"]
>
> * Hozzon létre egy IoT Edge modult az egyéni kódból.
> * Docker-rendszerkép létrehozása az egyéni modulból.
> * Konfigurálja újra IoT Hub útválasztást az egyéni modulok támogatásához.
> * Egyéni modulok készítése, közzététele és üzembe helyezése.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk egy sorozat részét képezi a Azure Machine Learning IoT Edge-on való használatáról szóló oktatóanyaghoz. A sorozat minden cikke az előző cikkben található munkára épül. Ha ezt a cikket közvetlenül megérkezett, tekintse meg az [első cikket](tutorial-machine-learning-edge-01-intro.md) a sorozatban.

## <a name="create-a-new-iot-edge-solution"></a>Új IoT Edge-megoldás létrehozása

Két Azure Notebooks második végrehajtása során létrehoztunk és közzétettünk egy RUL modellt tartalmazó tároló-rendszerképet. Azure Machine Learning a lemezkép létrehozási folyamatának részeként csomagolta a modellt úgy, hogy a lemezkép Azure IoT Edge modulként legyen telepítve.

Ebben a lépésben létrehozunk egy Azure IoT Edge megoldást az "Azure Machine Learning" modul használatával, és a modult a Azure Notebooks használatával közzétett rendszerképre irányítjuk.

1. Nyisson meg egy távoli asztali munkamenetet a fejlesztői virtuális géphez.

1. Nyissa meg a **C mappát: \\ forrás \\ IoTEdgeAndMlSample** a Visual Studio Code-ban.

1. Kattintson a jobb gombbal az Explorer panelre (az üres területre), és válassza az **új IoT Edge megoldást**.

    ![Új IoT Edge megoldás létrehozása](media/tutorial-machine-learning-edge-06-custom-modules/new-edge-solution-command.png)

1. Fogadja el az alapértelmezett megoldás nevét **EdgeSolution**.

1. Válassza a **Azure Machine learning** lehetőséget a modul sablonként.

1. Nevezze el a modul **turbofanRulClassifier**.

1. Válassza ki a Machine learning-munkaterületet. Ez a munkaterület az oktatóanyagban létrehozott **turboFanDemo** munkaterület, amely [egy Azure Machine learning modell betanítását és üzembe helyezését ismerteti.](tutorial-machine-learning-edge-04-train-model.md)

1. Válassza ki az Azure-jegyzetfüzet futtatásakor létrehozott rendszerképet.

1. Tekintse meg a megoldást, és figyelje meg a létrehozott fájlokat:

   * **deployment.template.jsa** következőn: Ez a fájl tartalmazza a megoldás egyes moduljainak definícióját. Ebben a fájlban három szakaszt kell figyelnie:

     * **Beállításjegyzékbeli hitelesítő adatok:** Meghatározza a megoldásban használt egyéni tároló-nyilvántartások készletét. Jelenleg a Machine learning-munkaterületről kell tartalmaznia a beállításjegyzéket, amely a Azure Machine Learning rendszerképét tárolja. Tetszőleges számú tároló-bejegyzést használhat, de az egyszerűség kedvéért minden modul esetében ezt a beállításjegyzéket fogjuk használni.

       ```json
       "registryCredentials": {
         "<your registry>": {
           "username": "$CONTAINER_REGISTRY_USERNAME_<your registry>",
           "password": "$CONTAINER_REGISTRY_PASSWORD_<your registry>",
           "address": "<your registry>.azurecr.io"
         }
       }
       ```

     * **Modulok:** Ez a szakasz a felhasználó által definiált, a megoldáshoz tartozó modulok készletét tartalmazza. A turbofanRulClassifier modul definíciója a tároló beállításjegyzékében lévő képre mutat. Ahogy további modulokat adunk a megoldáshoz, megjelennek ebben a szakaszban.

       ```json
        "modules": {
          "turbofanRulClassifier": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "turbofandemo2cd74296.azurecr.io/edgemlsample:1",
              "createOptions": {}
            }
          }
        }
       ```

     * **Útvonalak:** ebben az oktatóanyagban egy kicsit is dolgozunk az útvonalakon. Az útvonalak határozzák meg, hogy a modulok hogyan kommunikáljanak egymással. A sablon által definiált meglévő útvonal nem egyezik a szükséges útválasztással. Törölje az `turbofanRulClassifierToIoTHub` útvonalat.

       ```json
        "$edgeHub": {
          "properties.desired": {
            "schemaVersion": "1.0",
            "routes": {
              "turbofanRulClassifierToIoTHub": "FROM /messages/modules/turbofanRulClassifier/outputs/* INTO $upstream"
            },
            "storeAndForwardConfiguration": {
              "timeToLiveSecs": 7200
            }
          }
        }
       ```

   * **deployment.debug.template.json:** ez a fájl a deployment.template.jshibakeresési verziója. Ezt a fájlt általában szinkronizálni kell a fájl deployment.template.jstartalmával, de erre nincs szükség ehhez az oktatóanyaghoz.

   * **. env:** ebben a fájlban kell megadnia a beállításjegyzék eléréséhez szükséges felhasználónevet és jelszót.

      ```env
      CONTAINER_REGISTRY_USERNAME_<your registry name>=<ACR username>
      CONTAINER_REGISTRY_PASSWORD_<your registry name>=<ACR password>
      ```

1. Kattintson a jobb gombbal a deployment.template.jsfájlra a Visual Studio Code Explorerben, és válassza a **IoT Edge megoldás létrehozása** lehetőséget.

1. Figyelje meg, hogy a parancs létrehoz egy deployment.amd64.jsfájlt tartalmazó konfigurációs mappát. Ez a fájl a megoldás konkrét telepítési sablonja.

## <a name="add-router-module"></a>Útválasztó modul hozzáadása

Ezután hozzáadjuk az útválasztó modult a megoldáshoz. Az útválasztó modul a megoldás számos feladatát kezeli:

* **Üzenetek fogadása a Leaf-eszközökről:** mivel az üzenetek a IoT Edge eszközre érkeznek az alárendelt eszközökről, az útválasztó modul megkapja az üzenetet, és megkezdi az üzenet útválasztásának előkészítését.
* **Üzenetek küldése az RUL osztályozó modulba:** ha egy alsóbb rétegbeli eszközről új üzenet érkezik, az útválasztó modul átalakítja az üzenetet arra a formátumra, amelyet a RUL osztályozó vár. Az útválasztó elküldi az üzenetet az RUL osztályozó RUL-előrejelzésnek. Miután az osztályozó elvégezte az előrejelzést, visszaküldi az üzenetet az útválasztó modulnak.
* **RUL üzenetek küldése a IoT hubnak:** ha az útválasztó üzeneteket fogad az osztályozó, akkor átalakítja az üzenetet, hogy csak a lényeges információkat, az eszköz azonosítóját és a RUL tartalmazza, és a rövidített üzenetet a IoT hubhoz küldje. A további finomítások, amelyeket itt nem készítettünk, csak akkor küldenek üzeneteket a IoT Hubnak, ha a RUL előrejelzése küszöbérték alá esik (például ha a RUL kevesebb, mint 100 ciklus). A szűrés ily módon csökkentené az üzenetek mennyiségét, és csökkenti az IoT hub költségeit.
* **Üzenet küldése a Avro-író modulnak:** az alárendelt eszköz által küldött összes adatok megőrzése érdekében az útválasztó modul elküldi az osztályozó által fogadott teljes üzenetet az Avro-író modulnak, amely megtartja és feltölti az adatokat IoT hub fájlfeltöltés használatával.

Az útválasztó modul a megoldás fontos részét képezi, amely biztosítja, hogy az üzenetek a megfelelő sorrendben legyenek feldolgozva.

### <a name="create-the-module-and-copy-files"></a>A modul létrehozása és fájlok másolása

1. Kattintson a jobb gombbal a Visual Studio Code modulok mappájára, majd válassza a **IoT Edge modul hozzáadása** lehetőséget.

1. Válassza a **C# modult** a modul sablonhoz.

1. Nevezze el a modul **turbofanRouter**.

1. Ha a rendszer kéri a Docker-rendszerkép tárházát, használja a Machine learning-munkaterületen a beállításjegyzéket (a beállításjegyzéket a *deployment.template.js* fájljának registryCredentials csomópontjában találja). Ez az érték a beállításjegyzék teljesen minősített címe, például **\<your registry\> . azurecr.IO/turbofanrouter**.

    > [!NOTE]
    > Ebben a cikkben a Azure Machine Learning munkaterület által létrehozott Azure Container Registry használjuk. Ez csupán kényelmi megoldás. Létrehoztunk egy új tároló-beállításjegyzéket, és ott közzétettük a modulokat.

1. A terminálon egy parancssori rendszerhéj használatával másolja a fájlokat a mintából a megoldásba.

    ```cmd
    copy c:\source\IoTEdgeAndMlSample\EdgeModules\modules\turbofanRouter\*.cs c:\source\IoTEdgeAndMlSample\EdgeSolution\modules\turbofanRouter\
    ```

1. Fogadja el a program. cs fájljának felülírására vonatkozó kérést.

### <a name="build-router-module"></a>Útválasztó modul létrehozása

1. A Visual Studio Code-ban válassza a **terminál**  >  **alapértelmezett felépítési feladat beállítása** lehetőséget.

1. Válassza **a létrehozás tasks.jsfájlból sablonból** lehetőséget.

1. Válassza a **.net Core** lehetőséget.

1. Cserélje le a tasks.jstartalmát a következő kódra.

    ```json
    {
      "version": "2.0.0",
      "tasks": [
        {
          "label": "build",
          "command": "dotnet",
          "type": "shell",
          "group": {
            "kind": "build",
            "isDefault": true
          },
          "args": [
            "build",
            "${workspaceFolder}/modules/turbofanRouter"
          ],
          "presentation": {
            "reveal": "always"
          },
          "problemMatcher": "$msCompile"
        }
      ]
    }
    ```

1. tasks.jsmentése és lezárása.

1. Futtasson Build `Ctrl + Shift + B` -t vagy **terminál**-  >  **futtatási felépítési feladatot**.

### <a name="set-up-module-routes"></a>Modulok útvonalának beállítása

A fentiekben leírtaknak megfelelően a IoT Edge Runtime a fájl *deployment.template.js* konfigurált útvonalakat használja a lazán kapcsolt modulok közötti kommunikáció kezelésére. Ebben a szakaszban bemutatjuk, hogyan állíthatja be az útvonalakat a turbofanRouter modulhoz. Először a bemeneti útvonalakat fogjuk kijelölni, majd a kimeneteket áthelyezni.

#### <a name="inputs"></a>Bevitelek

1. A program. cs init () metódusában két visszahívást regisztrálunk a modulhoz:

   ```csharp
   await ioTHubModuleClient.SetInputMessageHandlerAsync(EndpointNames.FromLeafDevice, LeafDeviceInputMessageHandler, ioTHubModuleClient);
   await ioTHubModuleClient.SetInputMessageHandlerAsync(EndpointNames.FromClassifier, ClassifierCallbackMessageHandler, ioTHubModuleClient);
   ```

2. Az első visszahívás figyeli az **deviceInput** -fogadónak küldött üzeneteket. A fenti ábrán láthatjuk, hogy bármely levélből származó üzeneteket át szeretnénk irányítani erre a bemenetre. A fájl *deployment.template.jsban* adjon hozzá egy útvonalat, amely megadja, hogy a peremhálózati központ átirányítsa a IoT Edge eszköz által fogadott üzeneteket, amelyeket az IoT Edge modul nem küldött el a turbofanRouter modul "deviceInput" nevű bemenetén:

   ```json
   "leafMessagesToRouter": "FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/deviceInput\")"
   ```

3. Ezután adjon hozzá egy útvonalat az üzenetekhez a rulClassifier modulból a turbofanRouter modulba:

   ```json
   "classifierToRouter": "FROM /messages/modules/turbofanRulClassifier/outputs/amloutput INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/rulInput\")"
   ```

#### <a name="outputs"></a>Kimenetek

Adjon hozzá négy további útvonalat a $edgeHub Route paraméterhez az útválasztó modul kimenetének kezeléséhez.

1. A program. cs definiálja a SendMessageToClassifier () metódust, amely a modul ügyfelét használja a RUL osztályozó üzenet küldéséhez az útvonal használatával:

   ```json
   "routerToClassifier": "FROM /messages/modules/turbofanRouter/outputs/classOutput INTO BrokeredEndpoint(\"/modules/turbofanRulClassifier/inputs/amlInput\")"
   ```

2. A SendRulMessageToIotHub () a modul-ügyféllel küldi el a IoT Hub az eszközre vonatkozó RUL-adatelérési útvonalat az útvonalon keresztül:

   ```json
   "routerToIoTHub": "FROM /messages/modules/turboFanRouter/outputs/hubOutput INTO $upstream"
   ```

3. A SendMessageToAvroWriter () a modul ügyfelével küldi el az üzenetet a avroFileWriter modulba felvett RUL-adattal.

   ```json
   "routerToAvro": "FROM /messages/modules/turbofanRouter/outputs/avroOutput INTO BrokeredEndpoint(\"/modules/avroFileWriter/inputs/avroModuleInput\")"
   ```

4. A HandleBadMessage () nem küldi el a sikertelen üzeneteket a IoT Hub, ahol később átirányíthatja őket.

   ```json
   "deadLetter": "FROM /messages/modules/turboFanRouter/outputs/deadMessages INTO $upstream"
   ```

A "$edgeHub" csomóponttal együtt vett összes útvonalnak a következő JSON-hez hasonlóan kell kinéznie:

```json
"$edgeHub": {
  "properties.desired": {
    "schemaVersion": "1.0",
    "routes": {
      "leafMessagesToRouter": "FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/deviceInput\")",
      "classifierToRouter": "FROM /messages/modules/turbofanRulClassifier/outputs/amlOutput INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/rulInput\")",
      "routerToClassifier": "FROM /messages/modules/turbofanRouter/outputs/classOutput INTO BrokeredEndpoint(\"/modules/turbofanRulClassifier/inputs/amlInput\")",
      "routerToIoTHub": "FROM /messages/modules/turboFanRouter/outputs/hubOutput INTO $upstream",
      "routerToAvro": "FROM /messages/modules/turbofanRouter/outputs/avroOutput INTO BrokeredEndpoint(\"/modules/avroFileWriter/inputs/avroModuleInput\")",
      "deadLetter": "FROM /messages/modules/turboFanRouter/outputs/deadMessages INTO $upstream"
    },
    "storeAndForwardConfiguration": {
      "timeToLiveSecs": 7200
    }
  }
}
```

  > [!NOTE]
  > A turbofanRouter modul hozzáadása a következő további útvonalat hozta létre: `turbofanRouterToIoTHub": "FROM /messages/modules/turbofanRouter/outputs/* INTO $upstream` . Távolítsa el ezt az útvonalat, csak a fent felsorolt útvonalakat hagyja a fájl deployment.template.js.

## <a name="add-avro-writer-module"></a>Avro-író modul hozzáadása

A Avro-író modul két feladattal rendelkezik a megoldásban, az üzenetek tárolásához és a fájlok feltöltéséhez.

* **Üzenetek tárolása**: Ha a Avro-író modul üzenetet kap, a Avro formátumban írja az üzenetet a helyi fájlrendszerbe. Egy kötési csatlakoztatást használunk, amely egy könyvtárat (ebben az esetben/Data/avrofiles) egy, a modul tárolójában lévő elérési útra csatlakoztat. Ez a csatlakoztatás lehetővé teszi, hogy a modul a helyi elérési útra írjon (/avrofiles), és ezek a fájlok közvetlenül elérhetők legyenek a IoT Edge eszközről.

* **Fájlok feltöltése**: a Avro-író modul az Azure IoT hub file upload szolgáltatást használja a fájlok Azure Storage-fiókba való feltöltéséhez. A fájl sikeres feltöltése után a modul törli a fájlt a lemezről.

### <a name="create-module-and-copy-files"></a>Modul létrehozása és fájlok másolása

1. A Visual Studio Code-ban válassza a  >  **parancs paletta** megtekintése lehetőséget, majd keresse meg a **Python: Select tolmács** elemet.

1. Válassza ki a telepített Python 3,7-es vagy újabb verzióját.

1. Kattintson a jobb gombbal a Visual Studio Code modulok mappájára, majd válassza a **IoT Edge modul hozzáadása** lehetőséget.

1. Válassza a **Python Module** (Python-modul) lehetőséget.

1. Adja meg a modul nevét `avroFileWriter` .

1. Ha a rendszer kéri a Docker-rendszerkép tárházát, használja ugyanazt a beállításjegyzéket, amelyet az útválasztó modul hozzáadásakor használt.

1. Fájlok másolása a minta modulból a megoldásba.

   ```cmd
   copy C:\source\IoTEdgeAndMlSample\EdgeModules\modules\avroFileWriter\*.py C:\source\IoTEdgeAndMlSample\EdgeSolution\modules\avroFileWriter\
   ```

1. Fogadja el a main.py felülírását.

1. Figyelje meg, hogy a filemanager.py és a schema.py hozzá lettek adva a megoldáshoz, és a main.py frissült.

> [!NOTE]
> Amikor megnyit egy Python-fájlt, a rendszer kérni fogja, hogy telepítse a pylint-t. Az oktatóanyag elvégzéséhez nem kell telepítenie a kitöltést.

### <a name="bind-mount-for-data-files"></a>Kötési csatlakoztatás adatfájlokhoz

Ahogy azt korábban említettük, az író modul a kötési csatlakoztatások jelenlétén alapul, hogy Avro-fájlokat írjon az eszköz fájlrendszeréhez.

#### <a name="add-directory-to-device"></a>Könyvtár hozzáadása az eszközhöz

1. Ha nem fut, indítsa el a Azure Portal IoT Edge eszköz virtuális gépén. Csatlakozzon az SSH használatával. A kapcsolathoz a Azure Portal található virtuális gép áttekintés lapjáról másolható DNS-név szükséges.

   ```cmd
   ssh -l <user>@<vm name>.<region>.cloudapp.azure.com
   ```

1. A bejelentkezés után hozza létre azt a könyvtárat, amely a mentett levél üzeneteit tárolja.

   ```bash
   sudo mkdir -p /data/avrofiles
   ```

1. Frissítse a könyvtár engedélyeit, hogy az a tároló által írható legyen.

   ```bash
   sudo chmod ugo+rw /data/avrofiles
   ```

1. Ellenőrizze, hogy a címtárban már van-e írási (w) engedély a felhasználóhoz, a csoporthoz és a tulajdonoshoz.

   ```bash
   ls -la /data
   ```

   ![Avrofiles tartozó címtár-engedélyek](media/tutorial-machine-learning-edge-06-custom-modules/avrofiles-directory-permissions.png)

#### <a name="add-directory-to-the-module"></a>Könyvtár hozzáadása a modulhoz

Ha a könyvtárat hozzá szeretné adni a modul tárolóhoz, akkor a avroFileWriter modulhoz társított Dockerfiles módosítjuk. A modulhoz három Dockerfiles van társítva: Docker. amd64, Docker. amd64. debug és Docker. arm32v7. Ezeket a fájlokat szinkronban kell tartani, ha egy arm32-eszközön szeretne hibakeresést végezni vagy üzembe helyezni. Ehhez a cikkhez csak a Docker. amd64-re koncentrálhat.

1. A fejlesztői virtuális gépen nyissa meg a **C:\source\IoTEdgeAndMlSample\EdgeSolution\modules\avoFileWriter\Dockerfile.amd64** fájlt.

1. Módosítsa a fájlt úgy, hogy az a következő példához hasonlóan néz ki:

   ```dockerfile
   FROM ubuntu:xenial

   WORKDIR /app

   RUN apt-get update && apt-get install -y --no-install-recommends libcurl4-openssl-dev
   python3-pip libboost-python1.58-dev libpython3-dev && rm -rf /var/lib/apt/lists/*

   RUN pip3 install --upgrade pip
   COPY requirements.txt ./
   RUN pip install -r requirements.txt

   COPY . .

   RUN useradd -ms /bin/bash moduleuser
   RUN mkdir /avrofiles && chown moduleuser /avrofiles
   USER moduleuser

   CMD [ "python3", "-u", "./main.py" ]
   ```

   A `mkdir` és `chown` parancsok arra utasítja a Docker-felépítési folyamatot, hogy hozzon létre egy/avrofiles nevű legfelső szintű könyvtárat a rendszerképben, majd a moduleuser az adott könyvtár tulajdonosaként. Fontos, hogy ezek a parancsok bekerülnek a modul felhasználójának a paranccsal való hozzáadása után, `useradd` és mielőtt a környezet átvált a moduleuser (felhasználói moduleuser).

1. Ha szükséges, végezze el a megfelelő módosításokat a Docker. amd64. debug és a Docker. arm32v7.

#### <a name="add-bind-configuration-to-the-avrofilewriter"></a>Kötési konfiguráció hozzáadása a avroFileWriter

A kötés létrehozásának utolsó lépése a kötési információval rendelkező (és deployment.debug.template.js) fájlok deployment.template.jsfrissítése.

1. deployment.template.jsmegnyitása a következőn:.

2. Módosítsa a avroFileWriter moduljának definícióját úgy, hogy hozzáadja azt a `Binds` paramétert, amely a Container Directory/avrofiles a peremhálózati eszköz helyi könyvtárába mutat. A modul definíciójának meg kell egyeznie a következő példával:

   ```json
   "avroFileWriter": {
     "version": "1.0",
     "type": "docker",
     "status": "running",
     "restartPolicy": "always",
     "settings": {
       "image": "${MODULES.avroFileWriter}",
       "createOptions": {
         "HostConfig": {
           "Binds": [
             "/data/avrofiles:/avrofiles"
           ]
         }
       }
     }
   }
   ```

### <a name="bind-mount-for-access-to-configyaml"></a>Kötés csatlakoztatása a config. YAML eléréséhez

Az író modulhoz még egy kötést kell hozzáadni. Ez a kötés lehetővé teszi, hogy a modul hozzáférhessen a IoT Edge eszközön található/etc/iotedge/config.YAML fájl kapcsolati karakterláncának olvasásához. A Iothubclientről létrehozásához szükség van a kapcsolódási karakterláncra, hogy meghívjuk a \_ blob \_ aszinkron feltöltési módszerét, hogy fájlokat töltsenek fel az IoT hub-ba. A kötés hozzáadásának lépései hasonlóak az előző szakaszhoz.

#### <a name="update-directory-permission"></a>Könyvtár frissítése engedély

1. Csatlakozzon az IoT Edge-eszközhöz az SSH használatával.

   ```bash
   ssh -l <user>@IoTEdge-<extension>.<region>.cloudapp.azure.com
   ```

1. Olvasási engedély hozzáadása a config. YAML fájlhoz.

   ```bash
   sudo chmod +r /etc/iotedge/config.yaml
   ```

1. Ellenőrizze, hogy az engedélyek helyesen vannak-e beállítva.

   ```bash
   ls -la /etc/iotedge/
   ```

1. Győződjön meg arról, hogy a config. YAML engedélyei a következők: **r--r**--r--.

#### <a name="add-directory-to-module"></a>Könyvtár hozzáadása a modulhoz

1. A fejlesztői gépen nyissa meg a **Docker. amd64** fájlt.

1. Adjon hozzá további készletet `mkdir` és `chown` parancsokat a fájlhoz, így a következőképpen néz ki:

   ```dockerfile
   FROM ubuntu:xenial

   WORKDIR /app

   RUN apt-get update && apt-get install -y --no-install-recommends libcurl4-openssl-dev
   python3-pip libboost-python1.58-dev libpython3-dev && rm -rf /var/lib/apt/lists/\*

   RUN pip3 install --upgrade pip
   COPY requirements.txt ./
   RUN pip install -r requirements.txt

   COPY . .

   RUN useradd -ms /bin/bash moduleuser
   RUN mkdir /avrofiles && chown moduleuser /avrofiles
   RUN mkdir -p /app/iotconfig && chown moduleuser /app/iotconfig

   USER moduleuser

   CMD "python3", "-u", "./main.py"]
   ```

1. Végezze el a megfelelő módosításokat a Docker. amd64. debug és a Docker. arm32v7.

#### <a name="update-the-module-configuration"></a>A modul konfigurációjának frissítése

1. Nyissa meg a **deployment.template.js** fájlt.

1. Módosítsa a avroFileWriter modul definícióját úgy, hogy hozzáad egy második sort a `Binds` paraméterhez, amely a Container Directoryt (/App/iotconfig) az eszköz helyi könyvtárára (/etc/iotedge) mutat.

   ```json
   "avroFileWriter": {
     "version": "1.0",
     "type": "docker",
     "status": "running",
     "restartPolicy": "always",
     "settings": {
       "image": "${MODULES.avroFileWriter}",
       "createOptions": {
         "HostConfig": {
           "Binds": [
             "/data/avrofiles:/avrofiles",
             "/etc/iotedge:/app/iotconfig"
           ]
         }
       }
     }
   }
   ```

1. Végezze el a megfelelő módosításokat deployment.debug.template.jsa következőn:.

## <a name="install-dependencies"></a>Függőségek telepítése

Az író modul két Python-kódtár, fastavro és PyYAML függőségét veszi igénybe. A függőségeket a fejlesztői gépre kell telepíteni, és arra kell utasítani a Docker-Build folyamatát, hogy azok a modul rendszerképében legyenek telepítve.

### <a name="pyyaml"></a>PyYAML

1. A fejlesztői gépen nyissa meg a `C:\source\IoTEdgeAndMlSample\EdgeSolution\modules\avoFileWriter\requirements.txt` fájlt, és adja hozzá a "PyYAML" kifejezést a fájl új sorához.

   ```txt
   azure-iothub-device-client~=1.4.3
   pyyaml
   ```

1. Nyissa meg a **Docker. amd64** fájlt, és adjon hozzá egy `pip install` parancsot a setuptools frissítéséhez.

   ```dockerfile
   FROM ubuntu:xenial

   WORKDIR /app

   RUN apt-get update && \
       apt-get install -y --no-install-recommends libcurl4-openssl-dev python3-pip libboost-python1.58-dev libpython3-dev && \
       rm -rf /var/lib/apt/lists/\*

   RUN pip3 install --upgrade pip
   RUN pip install -U pip setuptools
   COPY requirements.txt ./
   RUN pip install -r requirements.txt

   COPY . .

   RUN useradd -ms /bin/bash moduleuser
   RUN mkdir /avrofiles && chown moduleuser /avrofiles
   RUN mkdir -p /app/iotconfig && chown moduleuser /app/iotconfig
   USER moduleuser

   CMD [ "python3", "-u", "./main.py" ]
   ```

1. A parancssorban telepítse a PyYAML a fejlesztői gépre.

   ```cmd
   pip install pyyaml
   ```

### <a name="fastavro"></a>Fastavro

1. A requirements.txt a PyYAML után adja hozzá a fastavro-t.

   ```txt
   azure-iothub-device-client~=1.4.3
   pyyaml
   fastavro
   ```

1. Telepítse a fastavro a fejlesztői gépre.

   ```cmd
   pip install fastavro
   ```

## <a name="reconfigure-iot-hub"></a>IoT Hub újrakonfigurálása

A IoT Edge eszköz és modulok a rendszerbe való bevezetésével megváltoztak az Elvárásaink arról, hogy milyen információkat küld a központ és milyen célra. Az új realitások kezeléséhez újra kell konfigurálnia az útválasztást a központban.

> [!NOTE]
> A modulok üzembe helyezése előtt konfigurálja újra az elosztót, mert a hub egyes beállításai, különösen a fájlok feltöltése megfelelően be kell állítani a avroFileWriter modul megfelelő futtatását.

### <a name="set-up-route-for-rul-messages-in-iot-hub"></a>RUL-üzenetek útvonalának beállítása IoT Hub

A router és az osztályozó a helyén arra számítunk, hogy a rendszer csak az eszköz AZONOSÍTÓját és az eszköz RUL előrejelzését tartalmazó normál üzeneteket fogad. Azt szeretnénk, hogy a RUL a saját tárolóhelyére irányítsák, ahol az eszközök állapotát figyelemmel kísérheti, jelentéseket és riasztásokat hozhat létre, szükség szerint. Ugyanakkor azt szeretnénk, hogy az eszközön lévő összes olyan eszközt, amelyet még nem csatoltak a IoT Edge eszközhöz, továbbra is közvetlenül egy levélben küldi el a rendszer, hogy továbbra is az aktuális tárolási helyre irányítsa.

#### <a name="create-a-rul-message-route"></a>RUL-üzenet útvonalának létrehozása

1. A Azure Portal navigáljon a IoT Hub.

1. A bal oldali ablaktábla **üzenetkezelés** területén válassza az **üzenet-útválasztás** lehetőséget.

1. Az **útvonalak** lapon válassza a **Hozzáadás** lehetőséget.

1. Nevezze el az útvonal **RulMessageRoute**.

1. Válassza a végpont **hozzáadása** lehetőséget a **végpont** -választótól jobbra, majd válassza a **Storage** lehetőséget.

1. A **tárolási végpont hozzáadása** lapon nevezze el a végpont **ruldata**.

1. Válassza **a tároló** kiválasztása lehetőséget.

1. A **Storage-fiókok** oldalon keresse meg az ebben az oktatóanyagban használt Storage-fiókot, amelynek neve **iotedgeandml \<unique suffix\>**, például a következő:.

1. Válassza ki a **ruldata** tárolót, és kattintson a **kiválasztás** elemre.

1. A Storage-végpont **hozzáadása** lapon kattintson a **Létrehozás** elemre a tárolási végpont létrehozásához.

1. Az **útvonal hozzáadása lapon adja** vissza az **útválasztási lekérdezést** a `true` következő lekérdezéssel:

    ```sql
    IS_DEFINED($body.PredictedRul) AND NOT IS_DEFINED($body.OperationalSetting1)
    ```

1. Bontsa ki a **teszt** szakaszt, majd az **üzenet törzse** szakaszt. Cserélje le az üzenet törzsét a várt üzenetekre:

    ```json
    {
      "ConnectionDeviceId": "aaLeafDevice_1",
      "CorrelationId": "b27e97bb-06c5-4553-a064-e9ad59c0fdd3",
      "PredictedRul": 132.62721409309165,
      "CycleTime": 64.0
    }
    ```

1. Válassza a **teszt útvonal** lehetőséget. Ha a teszt sikeres, "az üzenet megfelelt a lekérdezésnek" jelenik meg.

1. Kattintson a **Mentés** gombra.

#### <a name="update-turbofandevicedatatostorage-route"></a>TurbofanDeviceDataToStorage-útvonal frissítése

Nem szeretnénk átirányítani az új előrejelzési adatgyűjtést a régi tárolási helyükre, ezért az útvonal frissítésével megakadályozhatja.

1. Az IoT Hub **üzenet-útválasztás** lapon válassza az **útvonalak** fület.

1. Válassza ki a **turbofanDeviceDataToStorage**, vagy bármilyen nevet, amelyet a kezdeti eszköz adatútvonala számára adott meg.

1. Az útválasztási lekérdezés frissítése a következőre

   ```sql
   IS_DEFINED($body.OperationalSetting1)
   ```

1. Bontsa ki a **teszt** szakaszt, majd az **üzenet törzse** szakaszt. Cserélje le az üzenetet a várt üzenetek példáján:

   ```json
   {
     "Sensor13": 2387.96,
     "OperationalSetting1": -0.0008,
     "Sensor6": 21.61,
     "Sensor11": 47.2,
     "Sensor9": 9061.45,
     "Sensor4": 1397.86,
     "Sensor14": 8140.39,
     "Sensor18": 2388.0,
     "Sensor12": 522.87,
     "Sensor2": 642.42,
     "Sensor17": 391.0,
     "OperationalSetting3": 100.0,
     "Sensor1": 518.67,
     "OperationalSetting2": 0.0002,
     "Sensor20": 39.03,
     "DeviceId": 19.0,
     "Sensor5": 14.62,
     "PredictedRul": 212.00132402791962,
     "Sensor8": 2388.01,
     "Sensor16": 0.03,
     "CycleTime": 42.0,
     "Sensor21": 23.3188,
     "Sensor15": 8.3773,
     "Sensor3": 1580.09,
     "Sensor10": 1.3,
     "Sensor7": 554.57,
     "Sensor19": 100.0
   }
   ```

1. Válassza a **teszt útvonal** lehetőséget. Ha a teszt sikeres, "az üzenet megfelelt a lekérdezésnek" jelenik meg.

1. Kattintson a **Mentés** gombra.

### <a name="configure-file-upload"></a>Fájlfeltöltés konfigurálása

Konfigurálja a IoT Hub fájlfeltöltés funkciót, hogy a fájl-író modul feltöltse a fájlokat a tárolóba.

1. A IoT Hub bal oldali panel menüjének **üzenetkezelés** területén válassza a **fájlfeltöltés** lehetőséget.

1. Válassza az **Azure Storage-tároló** elemet.

1. Válassza ki a Storage-fiókját a listából.

1. Válassza ki a **azureml-blobtárhely-** vel kezdődő tárolót GUID azonosítóval, majd kattintson a **kiválasztás** gombra.

1. Kattintson a **Mentés** gombra. A portál értesíti a Mentés befejeződéséről.

> [!Note]
> Nincs bekapcsolva a feltöltési értesítés ebben az oktatóanyagban, de a fájlfeltöltés-értesítések kezelésével kapcsolatos részletekért tekintse meg a [fájl feltöltésére vonatkozó értesítés fogadása](../iot-hub/iot-hub-java-java-file-upload.md#receive-a-file-upload-notification) című témakört.

## <a name="build-publish-and-deploy-modules"></a>Modulok készítése, közzététele és üzembe helyezése

Most, hogy elvégezte a konfigurációs módosításokat, készen állunk a rendszerképek létrehozására és az Azure Container registrybe való közzétételre. A létrehozási folyamat a fájl deployment.template.jssegítségével határozza meg, hogy mely modulokat kell felépíteni. Az egyes modulokhoz tartozó beállítások (beleértve a verziót is) a Module mappában található fájl module.jstalálhatók. A létrehozási folyamat először egy Docker-buildet futtat a fájl module.jsjában található aktuális konfigurációnak megfelelő Dockerfiles. Ezt követően közzéteszi a rendszerképet a beállításjegyzékben a fájl module.jsján egy olyan verziószámmal, amely megegyezik a fájlban található module.js. Végezetül létrehoz egy konfiguráció-specifikus központi telepítési jegyzéket (például deployment.amd64.js), amelyet az IoT Edge eszközre fogunk telepíteni. A IoT Edge eszköz beolvassa az adatokat a telepítési jegyzékből, és az utasítások alapján letölti a modulokat, konfigurálja az útvonalakat, és beállítja a kívánt tulajdonságokat. Ennek a telepítési módszernek két mellékhatása van:

* **Központi telepítési késés:** mivel a IoT Edge futtatókörnyezetnek fel kell ismernie a kívánt tulajdonságok módosítását az újrakonfigurálás megkezdése előtt, a modulok üzembe helyezése után némi időt vehet igénybe, amíg a futtatókörnyezet fel nem veszi őket, és nem indítja el a IoT Edge eszköz frissítését.

* **Modulok verziói:** ha egy modul tárolójának új verzióját közzéteszi a tároló-beállításjegyzékben az előző modullal megegyező verziójú címkék használatával, a futtatókörnyezet nem fogja letölteni a modul új verzióját. Összehasonlítja a helyi rendszerkép verziószámát és a kívánt rendszerképet az üzembe helyezési jegyzékből. Ha ezek a verziók egyeznek, a futtatókörnyezet nem hajt végre műveletet. Ezért fontos, hogy minden alkalommal növelje a modul verzióját, amikor új módosításokat kíván üzembe helyezni. Növelje a verziót úgy, hogy módosítja a **verzió** tulajdonságot **a (** z) module.jsfájljában a módosítani kívánt modulhoz. Ezután hozza létre és tegye közzé a modult.

    ```json
    {
      "$schema-version": "0.0.1",
      "description": "",
      "image": {
        "repository": "<your registry>.azurecr.io/avrofilewriter",
        "tag": {
          "version": "0.0.1",
          "platforms": {
            "amd64": "./Dockerfile.amd64",
            "amd64.debug": "./Dockerfile.amd64.debug",
            "arm32v7": "./Dockerfile.arm32v7"
          }
        },
        "buildOptions": []
      },
      "language": "python"
    }
    ```

### <a name="build-and-publish"></a>Létrehozás és közzététel

1. A fejlesztői virtuális gépen indítsa el a Docker-t, ha az nem fut.

1. A Visual Studio Code-ban indítson el egy új terminált egy parancssorba, és jelentkezzen be az Azure Container registrybe (ACR).

  A felhasználónevet, a jelszót és a bejelentkezési kiszolgáló értékét a Azure Portalban találja. A tároló-beállításjegyzék nevének formátuma a következő: "turbofandemo \<unique id\> ". A bal oldali ablaktábla **Beállítások** területén a **hozzáférési kulcsok** elemre kattintva megtekintheti azokat.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

1. A Visual Studio Code-ban kattintson a jobb gombbal a deployment.template.jselemre, majd válassza a **IoT Edge megoldás létrehozása és leküldése** lehetőséget.

### <a name="view-modules-in-the-registry"></a>A beállításjegyzék moduljainak megtekintése

A létrehozás sikeres befejezését követően a Azure Portal segítségével áttekintheti a közzétett modulokat.

1. Nyissa meg az oktatóanyaghoz tartozó Azure Container Registry. A tároló-beállításjegyzék nevének formátuma a következő: "turbofandemo \<unique id\> ". 

1. A bal oldali ablaktábla **szolgáltatások** területén válassza a **tárolók** lehetőséget.

1. Vegye figyelembe, hogy mindkét létrehozott, **avrofilewriter** és **turbofanrouter**-modul adattárakként jelenik meg.

1. Válassza a **turbofanrouter** lehetőséget, és vegye figyelembe, hogy egy 0.0.1-amd64 címkével rendelkező képet adott közzé.

   ![A turbofanrouter első címkézett verziójának megtekintése](media/tutorial-machine-learning-edge-06-custom-modules/tagged-image-turbofanrouter-repo.png)

### <a name="deploy-modules-to-iot-edge-device"></a>Modulok üzembe helyezése IoT Edge eszközön

Beépítettük és konfiguráltuk a megoldásokat a megoldásban, most a modulokat a IoT Edge eszközre fogjuk telepíteni.

1. A Visual Studio Code-ban kattintson a jobb gombbal a **deployment.amd64.js** fájlra a konfigurációs mappában.

1. Válassza **a központi telepítés létrehozása egyetlen eszközhöz** lehetőséget.

1. Válassza ki a IoT Edge eszközt, **aaTurboFanEdgeDevice**.

1. Frissítse az Azure IoT Hub-eszközök panelt a Visual Studio Code Explorerben. Látnia kell, hogy a három új modul telepítve van, de még nem fut.

1. Néhány perc elteltével újra frissül, és a futó modulok megjelennek.

   ![Futó modulok megtekintése a Visual Studio Code-ban](media/tutorial-machine-learning-edge-06-custom-modules/view-running-modules-list.png)

> [!NOTE]
> Több percet is igénybe vehet, amíg a modulok elindulnak, és egy állandó futású állapotba rendezhetők. Ebben az időszakban a modulok indítását és leállását láthatja, amikor megpróbálnak kapcsolatot létesíteni az IoT Edge hub modullal.

## <a name="diagnosing-failures"></a>Hibák diagnosztizálása

Ebben a szakaszban néhány olyan technikát is megosztunk, amely megértette, hogy mi a hiba egy modul vagy modul esetében. A Visual Studio Code-ban lévő állapotból gyakran nem lehet hibát kimutatni.

### <a name="identify-failed-modules"></a>Sikertelen modulok azonosítása

* **Visual Studio code:** Tekintse meg az Azure IoT Hub-eszközök panelt. Ha a legtöbb modul fut állapotban van, de az egyik le van állítva, meg kell vizsgálnia, hogy a leállított modul továbbra is fennáll. Ha az összes modul hosszabb ideig leállított állapotban van, akkor a meghibásodást is jelezheti.

* **Azure Portal:** Ha megkeresi az IoT hub-t a portálon, majd megkeresi az eszköz részletei lapot (IoT Edge alatt az eszköz részletezése), akkor előfordulhat, hogy egy modul hibát jelzett, vagy soha nem jelentett semmit az IoT hubhoz.

### <a name="diagnosing-from-the-device"></a>Diagnosztizálás az eszközről

Ha bejelentkezik a IoT Edge eszközre (az esetünkben a linuxos virtuális gépre), a modulok állapotával kapcsolatban jó információkhoz juthat. Az általunk használt fő mechanizmus a Docker-parancs, amely lehetővé teszi, hogy megvizsgáljuk az eszközön található tárolókat és képeket.

1. Jelentkezzen be IoT Edge eszközére:

   ```bash
   ssh -l <user>@IoTEdge-<extension>.<region>.cloudapp.azure.com
   ```

1. Az összes futó tároló listázása. Azt várjuk, hogy minden modulhoz egy tárolót lássunk, amelynek a neve megfelel a modulnak. Emellett a parancs felsorolja a tároló pontos képét is, beleértve a verziót is, így Ön is megtekintheti a vártat. A rendszerképeket úgy is listázhatja, hogy a parancsban az "image" tulajdonságot a "Container" értékre cseréli.

   ```bash
   sudo docker container ls
   ```

1. A tároló naplóinak beolvasása. Ez a parancs kimenete a tárolóban található StdErr és StdOut-ra is vonatkozik. Ez a parancs olyan tárolók esetében működik, amelyek valamilyen okból elkezdődtek, majd elhaltak. Azt is hasznos megérteni, hogy mi történt a edgeAgent vagy a edgeHub-tárolóval.

   ```bash
   sudo docker container logs <container id>
   ```

1. Egy tároló vizsgálata. Ez a parancs rengeteg információt nyújt a rendszerképről. Az Adatszűrés a keresett elemtől függően szűrhető. Ha például meg szeretné tekinteni, hogy a avroFileWriter kötései helyesek-e, használja a következő parancsot:

   ```bash
   sudo docker container inspect -f "{{ json .Mounts }}" avroFileWriter | python -m json.tool
   ```

1. Kapcsolódás egy futó tárolóhoz. Ez a parancs akkor lehet hasznos, ha a tárolót a futtatása közben szeretné megvizsgálni:

   ```bash
   sudo docker exec -it avroFileWriter bash
   ```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ez az oktatóanyag egy olyan készlet része, amelyben az egyes cikkek az előzőekben végzett munkára épülnek. Várjon, amíg az összes erőforrást el nem végezte, amíg el nem végzi a végleges oktatóanyagot.

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben egy IoT Edge megoldást hoztunk létre a Visual Studio Code-ban három modullal: egy osztályozó, egy útválasztó és egy fájl írója/feltöltője. Az útvonalakat úgy állítottuk be, hogy a modulok kommunikáljanak egymással a peremhálózati eszközön. Módosítottuk a peremhálózati eszköz konfigurációját, és frissítettük a Dockerfiles, hogy telepítse a függőségeket, és hozzáadja a kötési csatlakoztatásokat a modulok tárolóhoz. 

A következő lépésben frissítettük a IoT Hub konfigurációját, hogy az üzeneteket a típus és a fájlfeltöltés kezelésére használjuk. Minden esetben a modulokat a IoT Edge eszközre telepítettük, és gondoskodtak arról, hogy a modulok megfelelően futnak.

Folytassa a következő cikkel az adatok küldésének megkezdéséhez és a megoldás működés közbeni megtekintéséhez.

> [!div class="nextstepaction"]
> [Az adatküldés transzparens átjárón keresztül](tutorial-machine-learning-edge-07-send-data-to-hub.md)