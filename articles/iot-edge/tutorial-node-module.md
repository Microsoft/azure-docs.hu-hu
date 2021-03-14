---
title: 'Oktatóanyag: Node.js modul fejlesztése Linux rendszeren – Azure IoT Edge | Microsoft Docs'
description: Az oktatóanyag bemutatja, hogyan hozhat létre IoT Edge-modult Node.js-kóddal, és hogyan helyezheti üzembe azt peremhálózati eszközökön
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/30/2020
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, devx-track-python, devx-track-js
ms.openlocfilehash: a8a6d09819aaa65645da3d4d697866609c226723
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/14/2021
ms.locfileid: "103461147"
---
# <a name="tutorial-develop-and-deploy-a-nodejs-iot-edge-module-using-linux-containers"></a>Oktatóanyag: Node.js IoT Edge modul fejlesztése és üzembe helyezése Linux-tárolók használatával

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

A Visual Studio Code segítségével fejlesztheti Node.js kódot, és telepítheti azt egy Azure IoT Edge rendszert futtató eszközre.

Az IoT Edge-modulokkal olyan kódot helyezhet üzembe, amely közvetlenül az IoT Edge-eszközökön implementálja az üzleti logikát. Ez az oktatóanyag végigvezeti az érzékelőktől kapott adatokat szűrő IoT Edge-modul létrehozásának és üzembe helyezésének lépésein. A rövid útmutatókban létrehozott IoT Edge eszközt fogja használni. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * IoT Edge Node.js-modul létrehozása a Visual Studio Code-dal
> * Docker-rendszerkép létrehozása és közzététele a beállításjegyzékbe a Visual Studio Code és a Docker használatával
> * A modul üzembe helyezése az IoT Edge-eszközön
> * A létrejött adatok megtekintése

Az ebben az oktatóanyagban létrehozott IoT Edge-modul szűri az eszköze által létrehozott hőmérsékletadatokat. Csak akkor küld felfelé irányuló üzeneteket, amikor a hőmérséklet egy megadott küszöbérték felett van. Ez a fajta peremhálózati elemzés a felhőbe küldött és ott tárolt adatok mennyiségének csökkentésére használható.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag bemutatja, hogyan fejleszthet egy modult **Node.js** a **Visual Studio Code** használatával, és hogyan helyezheti üzembe azt egy IoT Edge eszközön.

A IoT Edge nem támogatja a Windows-tárolókat használó Node.js modulokat.

A következő táblázat segítségével megismerheti a Node.js modulok fejlesztésének és üzembe helyezésének lehetőségeit:

| Node.js | Visual Studio Code | Visual Studio 2017/2019 |
| - | ------------------ | ------------------ |
| **Linux AMD64** | ![A VS Code használata a Linux AMD64 Node.js moduljaihoz](./media/tutorial-c-module/green-check.png) |  |
| **Linux ARM32** | ![A VS Code használata a Linux ARM32 Node.js moduljaihoz](./media/tutorial-c-module/green-check.png) |  |

Az oktatóanyag megkezdése előtt el kellett volna végeznie az előző oktatóanyagot a fejlesztői környezet létrehozásához a Linux-tárolók fejlesztéséhez: [IoT Edge modulok létrehozása Linux-tárolók használatával](tutorial-develop-for-linux.md). Az oktatóanyag elvégzésével a következő előfeltételek szükségesek:

* Egy ingyenes vagy standard szintű [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) az Azure-ban.
* Azure IoT Edge rendszert futtató eszköz. A rövid útmutatók segítségével [Linux-eszközt](quickstart-linux.md) vagy [Windows-eszközt](quickstart.md)állíthat be.
* Egy tároló-beállításjegyzék, például [Azure Container Registry](../container-registry/index.yml).
* A [Visual Studio Code](https://code.visualstudio.com/) az [Azure IoT-eszközökkel](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)van konfigurálva.
* A [Docker CE](https://docs.docker.com/install/) Linux-tárolók futtatására van konfigurálva.

Node.js IoT Edge moduljának fejlesztéséhez telepítse a következő további előfeltételeket a fejlesztői gépre:

* [Node.js és NPM](https://nodejs.org). Az npm csomag a Node.js részeként érhető el, ami azt jelenti, hogy a Node.js letöltésével az npm is automatikusan települ a számítógépre.

## <a name="create-a-module-project"></a>Modul-projekt létrehozása

A következő lépések bemutatják, hogyan hozhat létre IoT Edge Node.js modult a Visual Studio Code és az Azure IoT Tools használatával.

### <a name="create-a-new-project"></a>Új projekt létrehozása

Az **npm** használatával létrehozhat egy Node.js-megoldást, amelyre majd építkezhet.

1. A Visual Studio Code-ban válassza  >  az **integrált terminál** megtekintése lehetőséget a vs Code integrált terminál megnyitásához.

2. Az integrált terminálon írja be a következő parancsot a **yeoman** és a Node.js Azure IoT Edge-modul generátorának telepítéséhez:

    ```cmd/sh
    npm install -g yo generator-azure-iot-edge-module
    ```

3.   >  A vs Code parancs paletta megnyitásához kattintson a **parancs paletta** megtekintése elemre.

4. A parancskatalógusban írja be és futtassa az **Azure: Sign in** (Azure: bejelentkezés) parancsot, és az utasításokat követve jelentkezzen be Azure-fiókjába. Ha már be van jelentkezve, ezt a lépést kihagyhatja.

5. A parancskatalógusban írja be és futtassa az **Azure IoT Edge: New IoT Edge solution** (Azure IoT Edge: új IoT Edge-megoldás) parancsot. Kövesse a parancskatalógusban található utasításokat a megoldás létrehozásához.

   | Mező | Érték |
   | ----- | ----- |
   | Mappa kiválasztása | Válassza ki azt a helyet a fejlesztői gépen, ahol a VS Code létre fogja hozni a megoldásfájlokat. |
   | Provide a solution name (Megoldásnév megadása) | Adjon meg egy leíró nevet a megoldáshoz, vagy fogadja el az alapértelmezett **EdgeSolution**. |
   | Select module template (Modulsablon kiválasztása) | Válassza **Node.js modult**. |
   | Provide a module name (Modulnév megadása) | A modulnak adja a **NodeModule** nevet. |
   | Provide Docker image repository for the module (Docker-rendszerkép adattárának megadása a modulhoz) | Egy rendszerképadattár a tárolóregisztrációs adatbázis nevét és a tárolórendszerkép nevét tartalmazza. A tároló képe előre fel van töltve az utolsó lépésben megadott névvel. Cserélje le a **localhost: 5000** értéket a **bejelentkezési kiszolgáló** értékére az Azure Container registryben. A bejelentkezési kiszolgálót a tároló beállításjegyzékének áttekintés lapjáról kérheti le a Azure Portal. <br><br>A rendszerkép utolsó tárháza a következőhöz hasonlít: \<registry name\> . azurecr.IO/nodemodule. |

   ![Docker-rendszerkép adattárának megadása](./media/tutorial-node-module/repository.png)

### <a name="add-your-registry-credentials"></a>A regisztrációs adatbázis hitelesítő adatainak hozzáadása

A környezeti fájl tárolja a tárolóadattár hitelesítő adatait, és megosztja azokat az IoT-Edge futtatókörnyezettel. A futtatókörnyezetnek szüksége van ezekre a hitelesítő adatokra a privát rendszerképek letöltéséhez az IoT Edge-eszközre.

A IoT Edge bővítmény megpróbálja lekérni a tároló beállításjegyzékbeli hitelesítő adatait az Azure-ból, és feltölti azokat a környezeti fájlban. Ellenőrizze, hogy a hitelesítő adatok már szerepelnek-e. Ha nem, adja hozzá őket most:

1. A VS Code Explorerben nyissa meg a **. env** fájlt.
2. Adja meg az Azure Container Registryből kimásolt **felhasználónevet** és **jelszót** a megfelelő mezőkben.
3. Mentse el ezt a fájlt.

### <a name="select-your-target-architecture"></a>Válassza ki a cél architektúrát

A Visual Studio Code jelenleg a Linux AMD64 és Linux ARM32v7-eszközökön Node.js modulokat fejleszthet. Ki kell választania, hogy melyik architektúrát célozza meg az egyes megoldásokkal, mivel a tárolót az egyes architektúrák típusainál eltérően építették és futtatják. Az alapértelmezett érték a Linux AMD64.

1. Nyissa meg a parancssort, és keressen rá **Azure IoT Edge: állítsa be az alapértelmezett cél platformot az Edge megoldáshoz**, vagy válassza a parancsikon ikont az ablak alján található oldalsó sávban.

2. A parancs palettáján válassza ki a cél architektúrát a lehetőségek listájából. Ebben az oktatóanyagban egy Ubuntu rendszerű virtuális gépet használunk IoT Edge eszközként, így megtarthatja az alapértelmezett **amd64**-t.

### <a name="update-the-module-with-custom-code"></a>A modul módosítása egyéni kóddal

Minden sablonhoz tartozik egy mintakód, amely a **SimulatedTemperatureSensor** modul szimulált érzékelő adatait veszi át, és átirányítja IoT hub. Ebben a szakaszban kódot adunk hozzá, amely elemzi az üzeneteket azok elküldése előtt.

1. A vs Code Explorerben nyissa meg a **modulok**  >  **NodeModule**  >  **app.js**.

2. Adjon hozzá egy hőmérsékleti határértéket tároló változót a szükséges csomópontmodulok alá. A hőmérsékleti határérték azt az értéket állítja be, amelyet a mért hőmérsékletnek túl kell lépnie ahhoz, hogy a rendszer elküldje az adatokat az IoT Hubnak.

    ```javascript
    var temperatureThreshold = 25;
    ```

3. Cserélje le a teljes `PipeMessage` függvényt a `FilterMessage` függvényre.

    ```javascript
    // This function filters out messages that report temperatures below the temperature threshold.
    // It also adds the MessageType property to the message with the value set to Alert.
    function filterMessage(client, inputName, msg) {
        client.complete(msg, printResultFor('Receiving message'));
        if (inputName === 'input1') {
            var message = msg.getBytes().toString('utf8');
            var messageBody = JSON.parse(message);
            if (messageBody && messageBody.machine && messageBody.machine.temperature && messageBody.machine.temperature > temperatureThreshold) {
                console.log(`Machine temperature ${messageBody.machine.temperature} exceeds threshold ${temperatureThreshold}`);
                var outputMsg = new Message(message);
                outputMsg.properties.add('MessageType', 'Alert');
                client.sendOutputEvent('output1', outputMsg, printResultFor('Sending received message'));
            }
        }
    }

    ```

4. Cserélje le a `pipeMessage` függvénynevet a `filterMessage` névre a `client.on()` függvényben.

    ```javascript
    client.on('inputMessage', function (inputName, msg) {
        filterMessage(client, inputName, msg);
        });
    ```

5. Másolja a következő kódrészletet a `client.open()` függvény visszahívási részébe a `client.on()` után az `else` utasításon belülre. A rendszer ezt a függvényt akkor hívja meg, amikor a kívánt tulajdonságok frissülnek.

    ```javascript
    client.getTwin(function (err, twin) {
        if (err) {
            console.error('Error getting twin: ' + err.message);
        } else {
            twin.on('properties.desired', function(delta) {
                if (delta.TemperatureThreshold) {
                    temperatureThreshold = delta.TemperatureThreshold;
                }
            });
        }
    });
    ```

6. Mentse az app.js fájlt.

7. A VS Code Explorerben nyissa meg a IoT Edge megoldás munkaterületen található fájl **deployment.template.js** .

8. Adja hozzá a NodeModule ikermodult az üzembehelyezési jegyzékhez. Szúrja be a következő JSON-tartalmat a `moduleContent` szakasz alján, az `$edgeHub` modul ikerdokumentuma után:

   ```json
     "NodeModule": {
         "properties.desired":{
             "TemperatureThreshold":25
         }
     }
   ```

   ![Modul Twin hozzáadása a központi telepítési sablonhoz](./media/tutorial-node-module/module-twin.png)

9. Mentse a deployment.template.json fájlt.

## <a name="build-and-push-your-module"></a>Modul létrehozása és leküldése

Az előző szakaszban létrehozott egy IoT Edge megoldást, és hozzáadta a kódot a NodeModule, amely kiszűri azokat az üzeneteket, amelyekben a jelentett gépi hőmérséklet az elfogadható határértékeken belül van. Most létre kell hoznia a megoldást tárolórendszerképként, és le kell küldenie a tárolóregisztrációs adatbázisba.

1. Nyissa meg a vs Code integrált terminált a terminál **megtekintése** lehetőség kiválasztásával  >  .

2. Jelentkezzen be a Docker-be a következő parancs beírásával a terminálon. Jelentkezzen be a felhasználónévvel, a jelszóval és a bejelentkezési kiszolgálóval az Azure Container registryből. Ezeket az értékeket a beállításjegyzék **hozzáférési kulcsok** részéből kérheti le a Azure Portal.

   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Biztonsági figyelmeztetés jelenhet meg, amely a használatát javasolja `--password-stdin` . Habár az ajánlott eljárás az éles környezetekben javasolt, az oktatóanyag hatókörén kívül esik. További információkért lásd a [Docker bejelentkezési](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) referenciáját.

3. A VS Code Explorerben kattintson a jobb gombbal a **deployment.template.js** fájlra, és válassza a **IoT Edge megoldás létrehozása és leküldése** lehetőséget.

   A build és a push parancs három műveletet indít el. Először létrehoz egy új mappát a **konfigurációban** , amely tartalmazza a teljes telepítési jegyzékfájlt, kiépítve a telepítési sablonban és más megoldási fájlokban található információkat. Másodszor, futtatja `docker build` a tároló rendszerképét a célként megadott architektúra megfelelő Docker alapján. Ezután futtatja, `docker push` hogy leküldi a rendszerkép-tárházat a tároló-beállításjegyzékbe.

   Ez a folyamat az első alkalommal több percet is igénybe vehet, de gyorsabb a parancsok következő futtatásakor.

## <a name="deploy-modules-to-device"></a>Modulok üzembe helyezése az eszközön

A Visual Studio Code Explorer és az Azure IoT Tools bővítmény használatával telepítse a modul-projektet a IoT Edge eszközre. Már rendelkezik egy, a forgatókönyvhöz előkészített üzembe helyezési jegyzékfájlval, a konfigurációs mappában található fájl **deployment.amd64.js** . Most csak ki kell választania az üzemelő példányt fogadó eszközt.

Ellenőrizze, hogy a IoT Edge eszköz működik-e.

1. A Visual Studio Code Explorer **Azure IoT hub** szakasza alatt bontsa ki az **eszközök** elemet a IoT-eszközök listájának megtekintéséhez.

2. Kattintson a jobb gombbal az IoT Edge-eszköz nevére, majd válassza a **Create Deployment for Single Device** (Üzembe helyezés létrehozása egyetlen eszközhöz) parancsot.

3. Válassza ki a **konfigurációs** mappában található fájl **deployment.amd64.jsét** , majd kattintson az **Edge központi telepítési jegyzék kiválasztása** elemre. Ne használja a deployment.template.json fájlt.

4. Az eszköz alatt bontsa ki a **modulok** elemet a telepített és futó modulok listájának megtekintéséhez. Kattintson a frissítés gombra. Meg kell jelennie az új **NodeModule** , amelyen a **SimulatedTemperatureSensor** modul, valamint a **$edgeAgent** és a **$edgeHub** fut.

    A modulok elindításához néhány percet is igénybe vehet. Az IoT Edge futtatókörnyezetnek meg kell kapnia az új üzembe helyezési jegyzékfájlt, le kell kérnie a modul lemezképeit a tároló futtatókörnyezetből, majd el kell indítania az új modulokat.

## <a name="view-the-generated-data"></a>A létrejött adatok megtekintése

Miután alkalmazta az üzembehelyezési jegyzéket az IoT Edge-eszközén, az eszköz IoT Edge-futtatókörnyezet összegyűjti az új környezettel kapcsolatos információkat, és megkezdi a végrehajtást. Az eszközön minden olyan futó modul leáll, amely nem szerepel az üzembehelyezési jegyzékben. Az eszközről hiányzó modulok elindulnak.

Az IoT Edge-eszköz állapotát a Visual Studio Code Explorer **Azure IoT Hub-eszközök** szakaszában tekintheti meg. Bontsa ki az eszköz részleteit, és megjelenik a telepített és a futó modulok listája.

1. A Visual Studio Code Explorerben kattintson a jobb gombbal a IoT Edge eszköz nevére, és válassza a **figyelés beépített esemény végpontjának indítása** lehetőséget.

2. Megtekintheti a IoT Hub érkező üzeneteket. Eltarthat egy ideig, amíg az üzenetek megérkeznek. A IoT Edge eszköznek meg kell kapnia az új központi telepítést, és el kell indítania az összes modult. Ezután megvárja a NodeModule-kód módosításait, amíg a gép hőmérséklete 25 fokkal nem éri el az üzenetek küldését. Az üzenet típusú **riasztást** is hozzáadja az adott hőmérsékleti küszöbértéket elérő üzenetekhez.

## <a name="edit-the-module-twin"></a>A modul két különálló szerkesztése

A NodeModule modult az üzembe helyezési jegyzékben használta, hogy 25 fokos hőmérsékleti küszöbértéket állítson be. A Twin modul használatával módosíthatja a funkciót anélkül, hogy frissítenie kellene a modul kódját.

1. A Visual Studio Code-ban bontsa ki a IoT Edge eszköz alatti részleteket a futó modulok megtekintéséhez.

2. Kattintson a jobb gombbal a **NodeModule** elemre, és válassza a **modul Twin szerkesztése** lehetőséget.

3. A kívánt tulajdonságok között keresse meg a **TemperatureThreshold** . Módosítsa az értékét egy új 5 fokos hőmérsékletre, a legutóbbi jelentett hőmérsékletnél pedig 10 fokkal magasabbra.

4. Mentse a modul különálló fájlját.

5. Kattintson a jobb gombbal a modul dupla szerkesztési paneljén bárhová, és válassza a **modul Twin frissítése** elemet.

6. A bejövő eszközről a felhőbe irányuló üzenetek figyelése. Ekkor az új hőmérsékleti küszöb eléréséig az üzenetek leállnak.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy a következő ajánlott cikkel folytatja, megtarthatja és újból felhasználhatja a létrehozott erőforrásokat és konfigurációkat. Azt is megteheti, hogy ugyanezt az IoT Edge-eszközt használja teszteszközként.

Ellenkező esetben a díjak elkerülése érdekében törölheti a jelen cikkben létrehozott helyi konfigurációkat és Azure-erőforrásokat.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban olyan kódot tartalmazó IoT Edge-modult hozott létre, amely szűri az IoT Edge-eszköz által létrehozott nyers adatokat.

Folytassa a következő oktatóanyagokkal, amelyből megtudhatja, hogyan hozhatja Azure IoT Edge az Azure Cloud Services üzembe helyezését az adathordozón lévő adatfeldolgozás és-elemzés során.

> [!div class="nextstepaction"]
> [Függvények](tutorial-deploy-function.md) 
>  [Stream Analytics](tutorial-deploy-stream-analytics.md) 
>  [Machine learning](tutorial-deploy-machine-learning.md) 
>  [Custom Vision Service](tutorial-deploy-custom-vision.md)