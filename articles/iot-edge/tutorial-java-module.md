---
title: Oktatóanyag – egyéni Java-modulok oktatóanyaga Azure IoT Edge használatával
description: Az oktatóanyag bemutatja, hogyan hozhat létre IoT Edge-modult Java-kóddal, és hogyan helyezheti üzembe azt peremhálózati eszközökön.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/30/2020
ms.topic: tutorial
ms.service: iot-edge
ms.custom:
- mvc
- mqtt
- devx-track-java
ms.openlocfilehash: 3f24f38db7704557894d866b789890763f9e1316
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103463255"
---
# <a name="tutorial-develop-a-java-iot-edge-module-using-linux-containers"></a>Oktatóanyag: Java-IoT Edge modul fejlesztése Linux-tárolók használatával

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Az Azure IoT Edge-modulokkal olyan kódot helyezhet üzembe, amely közvetlenül az IoT Edge-eszközökön implementálja az üzleti logikát. Ez az oktatóanyag végigvezeti az érzékelőktől kapott adatokat szűrő IoT Edge-modul létrehozásának és üzembe helyezésének lépésein. A gyors üzembe helyezési útmutatóban az Azure IoT Edge központi telepítése szimulált IoT Edge eszközét fogja használni. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * IoT Edge Java-modul létrehozása a Visual Studio Code-dal az Azure IoT Edge Maven-sabloncsomag és az Azure IoT Java eszközoldali SDK alapján.
> * Docker-rendszerkép létrehozása és közzététele a regisztrációs adatbázisba a Visual Studio Code és a Docker használatával.
> * A modul üzembe helyezése az IoT Edge-eszközön.
> * A létrejött adatok megtekintése.

Az ebben az oktatóanyagban létrehozott IoT Edge-modul szűri az eszköze által létrehozott hőmérsékletadatokat. Csak akkor küld felfelé irányuló üzeneteket, amikor a hőmérséklet egy megadott küszöbérték felett van. Ez a fajta peremhálózati elemzés a felhőbe küldött és ott tárolt adatok mennyiségének csökkentésére használható.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag bemutatja, hogyan fejleszthet **Java** -modult a **Visual Studio Code** használatával, és hogyan helyezheti üzembe azt egy IoT Edge eszközön. A IoT Edge nem támogatja a Windows-tárolóként létrehozott Java-modulokat.

A következő táblázat segítségével megismerheti a Java-modulok fejlesztésének és üzembe helyezésének lehetőségeit:

| Java | Visual Studio Code | Visual Studio 2017/2019 |
| - | ------------------ | ------------------ |
| **Linux AMD64** | ![A VS Code használata Java-modulokhoz a Linux AMD64-ben](./media/tutorial-c-module/green-check.png) |  |
| **Linux ARM32** | ![A VS Code használata Java-modulokhoz Linux ARM32](./media/tutorial-c-module/green-check.png) |  |

Az oktatóanyag megkezdése előtt el kellett volna végeznie az előző oktatóanyagot a fejlesztői környezet létrehozásához a Linux-tárolók fejlesztéséhez: [IoT Edge modulok létrehozása Linux-eszközökhöz](tutorial-develop-for-linux.md). Ezeknek az oktatóanyagoknak a végrehajtásával a következő előfeltételek szükségesek:

* Egy ingyenes vagy standard szintű [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) az Azure-ban.
* Azure IoT Edge rendszert futtató eszköz. A rövid útmutatók segítségével [Linux-eszközt](quickstart-linux.md) vagy [Windows-eszközt](quickstart.md)állíthat be.
* Egy tároló-beállításjegyzék, például [Azure Container Registry](../container-registry/index.yml).
* A [Visual Studio Code](https://code.visualstudio.com/) az [Azure IoT-eszközökkel](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)van konfigurálva.
* A [Docker CE](https://docs.docker.com/install/) Linux-tárolók futtatására van konfigurálva.

IoT Edge modul javában történő fejlesztéséhez telepítse a következő további előfeltételeket a fejlesztői gépre:

* A Visual Studio Code-hoz készült [Java-bővítménycsomag](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack).
* [Java SE Development Kit 11](/azure/developer/java/fundamentals/java-jdk-long-term-support), és [állítsa be a `JAVA_HOME` környezeti változót](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) , hogy a JDK-telepítésre mutasson.
* [Maven](https://maven.apache.org/)

   >[!TIP]
   >A Java és a Maven telepítési folyamata környezeti változókat ad hozzá a rendszerhez. A telepítés befejezése után indítsa újra a Visual Studio Code Terminal, a PowerShell vagy a parancssori példányok bármelyikét. Ez a lépés biztosítja, hogy ezek a segédprogramok fel tudják ismerni a Java-és Maven-parancsokat.

## <a name="create-a-module-project"></a>Modul-projekt létrehozása

Az alábbi lépések egy IoT Edge modul-projektet hoznak létre, amely a Azure IoT Edge Maven-sablon csomagján és az Azure IoT Java-eszköz SDK-on alapul. A projektet a Visual Studio Code és az Azure IoT Tools használatával hozza létre.

### <a name="create-a-new-project"></a>Új projekt létrehozása

Létrehozhat egy Java-megoldást, amelyet a saját kódjával testreszabhat.

1. A Visual Studio Code-ban kattintson  >  a **parancs paletta** megtekintése elemre a vs Code parancs paletta megnyitásához.

2. A parancskatalógusban írja be és futtassa az **Azure IoT Edge: New IoT Edge solution** parancsot. Kövesse a parancskatalógusban található utasításokat a megoldás létrehozásához.

   | Mező | Érték |
   | ----- | ----- |
   | Mappa kiválasztása | Válassza ki azt a helyet a fejlesztői gépen, ahol a VS Code létre fogja hozni a megoldásfájlokat. |
   | Provide a solution name (Megoldásnév megadása) | Adjon meg egy leíró nevet a megoldáshoz, vagy fogadja el az alapértelmezett **EdgeSolution**. |
   | Select module template (Modulsablon kiválasztása) | Válasszon **Java-modult**. |
   | Provide a module name (Modulnév megadása) | Nevezze el a modul **JavaModule**. |
   | Provide Docker image repository for the module (Docker-rendszerkép adattárának megadása a modulhoz) | Egy rendszerképadattár a tárolóregisztrációs adatbázis nevét és a tárolórendszerkép nevét tartalmazza. A tároló képe előre fel van töltve az utolsó lépésben megadott névvel. Cserélje le a **localhost: 5000** értéket a **bejelentkezési kiszolgáló** értékére az Azure Container registryben. A bejelentkezési kiszolgálót a tároló beállításjegyzékének áttekintés lapjáról kérheti le a Azure Portal. <br><br>A rendszerkép utolsó tárháza a következőhöz hasonlít: \<registry name\> . azurecr.IO/javamodule. |
   | Adja meg a groupId értékét | Adja meg a csoport AZONOSÍTÓjának értékét, vagy fogadja el az alapértelmezett **com. edgemodule**. |

   ![Docker-rendszerkép adattárának megadása](./media/tutorial-java-module/repository.png)

Ha első alkalommal hoz létre Java-modult, a Maven-csomagok letöltése több percet is igénybe vehet. Ha a megoldás elkészült, a VS Code ablak betölti a IoT Edge megoldás munkaterületét. A megoldás munkaterület öt legfelső szintű összetevőt tartalmaz:

* A **modulok** mappa tartalmazza a modulhoz tartozó Java-kódot és a Docker-fájlokat, hogy a modult tároló képként lehessen felépíteni.
* Az **\. env** -fájl tárolja a tároló beállításjegyzékbeli hitelesítő adatait.
* A **deployment.template.json** fájl az IoT Edge-futtatókörnyezet által a modulok eszközön való üzembe helyezéséhez használt információkat tartalmazza.
* A fájlok **deployment.debug.template.js** a modulok hibakeresési verzióját tárolják.
* Ebben az oktatóanyagban nem módosítja a **\. vscode** mappát vagy a **\. gitignore** -fájlt.

Ha nem adott meg tárolóregisztrációs adatbázist a megoldás létrehozásakor, de elfogadta az alapértelmezett localhost:5000 értéket, akkor nem lesz \.env fájlja.

### <a name="add-your-registry-credentials"></a>A regisztrációs adatbázis hitelesítő adatainak hozzáadása

A környezeti fájl tárolja a tárolóregisztrációs adatbázis hitelesítő adatait, és megosztja őket az IoT-Edge futtatókörnyezettel. A futtatókörnyezetnek szüksége van ezekre a hitelesítő adatokra a privát rendszerképek letöltéséhez az IoT Edge-eszközre.

A IoT Edge bővítmény megpróbálja lekérni a tároló beállításjegyzékbeli hitelesítő adatait az Azure-ból, és feltölti azokat a környezeti fájlban. Ellenőrizze, hogy a hitelesítő adatok már szerepelnek-e. Ha nem, adja hozzá őket most:

1. A VS Code Explorerben nyissa meg a .env fájlt.
2. Adja meg az Azure Container Registryből kimásolt **felhasználónevet** és **jelszót** a megfelelő mezőkben.
3. Mentse el ezt a fájlt.

### <a name="select-your-target-architecture"></a>Válassza ki a cél architektúrát

A Visual Studio Code jelenleg a Linux AMD64 és Linux ARM32v7-eszközökhöz használható Java-modulokat fejleszt. Ki kell választania, hogy melyik architektúrát célozza meg az egyes megoldásokkal, mivel a tárolót az egyes architektúrák típusainál eltérően építették és futtatják. Az alapértelmezett érték a Linux AMD64.

1. Nyissa meg a parancssort, és keressen rá **Azure IoT Edge: állítsa be az alapértelmezett cél platformot az Edge megoldáshoz**, vagy válassza a parancsikon ikont az ablak alján található oldalsó sávban.

2. A parancs palettáján válassza ki a cél architektúrát a lehetőségek listájából. Ebben az oktatóanyagban egy Ubuntu rendszerű virtuális gépet használunk IoT Edge eszközként, így megtarthatja az alapértelmezett **amd64**-t.

### <a name="update-the-module-with-custom-code"></a>A modul módosítása egyéni kóddal

1. A vs Code Explorerben nyissa meg a **modulok**  >  **JavaModule**  >  **src**  >  **Main**  >  **Java**  >  **com**  >  **edgemodule**  >  **app. Java**.

2. Adja hozzá az alábbi kódot a fájl elejéhez új hivatkozott osztályok importálásához.

    ```java
    import java.io.StringReader;
    import java.util.concurrent.atomic.AtomicLong;
    import java.util.HashMap;
    import java.util.Map;

    import javax.json.Json;
    import javax.json.JsonObject;
    import javax.json.JsonReader;

    import com.microsoft.azure.sdk.iot.device.DeviceTwin.Pair;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.Property;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.TwinPropertyCallBack;
    ```

3. Vegye fel a következő definíciót az **App** osztályba. Ez a változó hőmérsékleti küszöbértéket állít be. A mért gépi hőmérséklet nem lesz jelenteni IoT Hubnak, amíg az érték át nem kerül.

    ```java
    private static final String TEMP_THRESHOLD = "TemperatureThreshold";
    private static AtomicLong tempThreshold = new AtomicLong(25);
    ```

4. A **MessageCallbackMqtt** végrehajtási metódusát cserélje a következő kódra. Ezt a metódust akkor hívja meg a rendszer, amikor a modul MQTT-üzenetet kap az IoT Edge-központtól. Kiszűri a modul ikerdokumentumán keresztül beállított hőmérsékleti határérték alatti hőmérsékleteket jelentő üzeneteket.

    ```java
    protected static class MessageCallbackMqtt implements MessageCallback {
        private int counter = 0;
        @Override
        public IotHubMessageResult execute(Message msg, Object context) {
            this.counter += 1;

            String msgString = new String(msg.getBytes(), Message.DEFAULT_IOTHUB_MESSAGE_CHARSET);
            System.out.println(
                   String.format("Received message %d: %s",
                            this.counter, msgString));
            if (context instanceof ModuleClient) {
                try (JsonReader jsonReader = Json.createReader(new StringReader(msgString))) {
                    final JsonObject msgObject = jsonReader.readObject();
                    double temperature = msgObject.getJsonObject("machine").getJsonNumber("temperature").doubleValue();
                    long threshold = App.tempThreshold.get();
                    if (temperature >= threshold) {
                        ModuleClient client = (ModuleClient) context;
                        System.out.println(
                            String.format("Temperature above threshold %d. Sending message: %s",
                            threshold, msgString));
                        client.sendEventAsync(msg, eventCallback, msg, App.OUTPUT_NAME);
                    }
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
            return IotHubMessageResult.COMPLETE;
        }
    }
    ```

5. Adja hozzá az alábbi két statikus belső osztályt az **App** osztályhoz. Ezek az osztályok frissítik a tempThreshold változót, ha a modul a kívánt tulajdonságot módosítja. Minden modul rendelkezik saját ikerdokumentummal, amelyekkel közvetlenül a felhőből konfigurálhatja a modulban futó kódot.

    ```java
    protected static class DeviceTwinStatusCallBack implements IotHubEventCallback {
        @Override
        public void execute(IotHubStatusCode status, Object context) {
            System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
    }

    protected static class OnProperty implements TwinPropertyCallBack {
        @Override
        public void TwinPropertyCallBack(Property property, Object context) {
            if (!property.getIsReported()) {
                if (property.getKey().equals(App.TEMP_THRESHOLD)) {
                    try {
                        long threshold = Math.round((double) property.getValue());
                        App.tempThreshold.set(threshold);
                    } catch (Exception e) {
                        System.out.println("Faile to set TemperatureThread with exception");
                        e.printStackTrace();
                    }
                }
            }
        }
    }
    ```

6. Adja hozzá a következő sorokat a **main** metódushoz a **client.open()** után a modul ikerdokumentum-frissítéseire való feliratkozáshoz.

    ```java
    client.startTwin(new DeviceTwinStatusCallBack(), null, new OnProperty(), null);
    Map<Property, Pair<TwinPropertyCallBack, Object>> onDesiredPropertyChange = new HashMap<Property, Pair<TwinPropertyCallBack, Object>>() {
        {
            put(new Property(App.TEMP_THRESHOLD, null), new Pair<TwinPropertyCallBack, Object>(new OnProperty(), null));
        }
    };
    client.subscribeToTwinDesiredProperties(onDesiredPropertyChange);
    client.getTwin();
    ```

7. Mentse az app. Java fájlt.

8. A VS Code Explorerben nyissa meg a IoT Edge megoldás munkaterületen található fájl **deployment.template.js** .

9. Adja hozzá a **JavaModule** modul ikerdokumentumát az üzembehelyezési jegyzékhez. Szúrja be a következő JSON-tartalmat a **moduleContent** szakasz alján, az **$edgeHub** modul ikerdokumentuma után:

   ```json
     "JavaModule": {
         "properties.desired":{
             "TemperatureThreshold":25
         }
     }
   ```

   ![Modul Twin hozzáadása a központi telepítési sablonhoz](./media/tutorial-java-module/module-twin.png)

10. Mentse a deployment.template.json fájlt.

## <a name="build-and-push-your-module"></a>Modul létrehozása és leküldése

Az előző szakaszban létrehozott egy IoT Edge megoldást, és hozzáadta a kódot a **JavaModule** , hogy kiszűrje azokat az üzeneteket, amelyekben a jelentett gép hőmérséklete az elfogadható határérték alá esik. Most hozza létre a megoldást tároló képként, és küldje el a tároló-beállításjegyzékbe.

1. Nyissa meg a vs Code integrált terminált a terminál **megtekintése** lehetőség kiválasztásával  >  .

2. Jelentkezzen be a Docker-be a következő parancs beírásával a terminálon. Jelentkezzen be a felhasználónévvel, a jelszóval és a bejelentkezési kiszolgálóval az Azure Container registryből. Ezeket az értékeket a beállításjegyzék **hozzáférési kulcsok** részéből kérheti le a Azure Portal.

   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Biztonsági figyelmeztetés jelenhet meg, amely a használatát javasolja `--password-stdin` . Habár az ajánlott eljárás az éles környezetekben javasolt, az oktatóanyag hatókörén kívül esik. További információkért lásd a [Docker bejelentkezési](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) referenciáját.

3. A VS Code Explorerben kattintson a jobb gombbal a **deployment.template.js** fájlra, és válassza a **IoT Edge megoldás létrehozása és leküldése** lehetőséget.

   A build és a push parancs három műveletet indít el. Először létrehoz egy új mappát a **konfigurációban** , amely tartalmazza a teljes telepítési jegyzékfájlt, amely a központi telepítési sablonban és más megoldási fájlokban található információkból épül fel. Másodszor, futtatja `docker build` a tároló rendszerképét a célként megadott architektúra megfelelő Docker alapján. Ezután futtatja, `docker push` hogy leküldi a rendszerkép-tárházat a tároló-beállításjegyzékbe.

   Ez a folyamat az első alkalommal több percet is igénybe vehet, de gyorsabb a parancsok következő futtatásakor.

## <a name="deploy-modules-to-device"></a>Modulok üzembe helyezése az eszközön

A Visual Studio Code Explorer és az Azure IoT Tools bővítmény használatával telepítse a modul-projektet a IoT Edge eszközre. Már rendelkezik egy, a forgatókönyvhöz előkészített üzembe helyezési jegyzékfájlval, a konfigurációs mappában található fájl **deployment.amd64.js** . Most csak ki kell választania az üzemelő példányt fogadó eszközt.

Ellenőrizze, hogy a IoT Edge eszköz működik-e.

1. A Visual Studio Code Explorer **Azure IoT hub** szakasza alatt bontsa ki az **eszközök** elemet a IoT-eszközök listájának megtekintéséhez.

2. Kattintson a jobb gombbal az IoT Edge-eszköz nevére, majd válassza a **Create Deployment for Single Device** (Üzembe helyezés létrehozása egyetlen eszközhöz) parancsot.

3. Válassza ki a **konfigurációs** mappában található fájl **deployment.amd64.jsét** , majd kattintson az **Edge központi telepítési jegyzék kiválasztása** elemre. Ne használja a deployment.template.json fájlt.

4. Az eszköz alatt bontsa ki a **modulok** elemet a telepített és futó modulok listájának megtekintéséhez. Kattintson a frissítés gombra. Meg kell jelennie az új **JavaModule** , amelyen a **SimulatedTemperatureSensor** modul, valamint a **$edgeAgent** és a **$edgeHub** fut.  

    A modulok elindításához néhány percet is igénybe vehet. Az IoT Edge futtatókörnyezetnek meg kell kapnia az új üzembe helyezési jegyzékfájlt, le kell kérnie a modul lemezképeit a tároló futtatókörnyezetből, majd el kell indítania az új modulokat.

## <a name="view-the-generated-data"></a>A létrejött adatok megtekintése

Miután alkalmazta az üzembehelyezési jegyzéket az IoT Edge-eszközén, az eszköz IoT Edge-futtatókörnyezet összegyűjti az új környezettel kapcsolatos információkat, és megkezdi a végrehajtást. Az eszközön minden olyan futó modul leáll, amely nem szerepel az üzembehelyezési jegyzékben. Az eszközről hiányzó modulok elindulnak.

1. A Visual Studio Code Explorerben kattintson a jobb gombbal a IoT Edge eszköz nevére, és válassza a **figyelés beépített esemény végpontjának indítása** lehetőséget.

2. Megtekintheti a IoT Hub érkező üzeneteket. Eltarthat egy ideig, amíg az üzenetek megérkeznek. A IoT Edge eszköznek meg kell kapnia az új központi telepítést, és el kell indítania az összes modult. Ezután megvárja a JavaModule-kód módosításait, amíg a gép hőmérséklete 25 fokkal nem éri el az üzenetek küldését. Az üzenet típusú **riasztást** is hozzáadja az adott hőmérsékleti küszöbértéket elérő üzenetekhez.

## <a name="edit-the-module-twin"></a>A modul két különálló szerkesztése

A JavaModule modult az üzembe helyezési jegyzékben használta, hogy 25 fokos hőmérsékleti küszöbértéket állítson be. A Twin modul használatával módosíthatja a funkciót anélkül, hogy frissítenie kellene a modul kódját.

1. A Visual Studio Code-ban bontsa ki a IoT Edge eszköz alatti részleteket a futó modulok megtekintéséhez.

2. Kattintson a jobb gombbal a **JavaModule** elemre, és válassza a **modul Twin szerkesztése** lehetőséget.

3. A kívánt tulajdonságok között keresse meg a **TemperatureThreshold** . Módosítsa az értékét egy új 5 fokos hőmérsékletre, a legutóbbi jelentett hőmérsékletnél pedig 10 fokkal magasabbra.

4. Mentse a modul különálló fájlját.

5. Kattintson a jobb gombbal a modul dupla szerkesztési paneljén bárhová, és válassza a **modul Twin frissítése** elemet.

6. A bejövő eszközről a felhőbe irányuló üzenetek figyelése. Ekkor az új hőmérsékleti küszöb eléréséig az üzenetek leállnak.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy a következő ajánlott cikkel folytatja, megtarthatja és újból felhasználhatja a létrehozott erőforrásokat és konfigurációkat. Azt is megteheti, hogy ugyanezt az IoT Edge-eszközt használja teszteszközként.

Ellenkező esetben a díjak elkerülése érdekében törölheti a jelen cikkben létrehozott helyi konfigurációkat és Azure-erőforrásokat.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban létrehozott egy IoT Edge modult, amely a IoT Edge-eszköz által generált nyers adatait szűri.

Folytassa a következő oktatóanyagokkal, amelyből megtudhatja, hogyan helyezheti üzembe az Azure Cloud Servicest az Azure IoT Edge a peremhálózat adatfeldolgozásához és elemzéséhez.

> [!div class="nextstepaction"]
> [Függvények](tutorial-deploy-function.md) 
>  [Stream Analytics](tutorial-deploy-stream-analytics.md) 
>  [Machine learning](tutorial-deploy-machine-learning.md) 
>  [Custom Vision Service](tutorial-deploy-custom-vision.md)
