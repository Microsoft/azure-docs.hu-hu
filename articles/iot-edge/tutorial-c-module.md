---
title: Útmutató a C modul fejlesztéséhez Linux rendszeren – Azure IoT Edge | Microsoft Docs
description: Ez az oktatóanyag bemutatja, hogyan hozhat létre egy IoT Edge modult C kóddal, és hogyan helyezheti üzembe egy Linux rendszerű eszközön IoT Edge
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/30/2020
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 3778361d066543325672f37cf163e58329f04abe
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2021
ms.locfileid: "106221568"
---
# <a name="tutorial-develop-a-c-iot-edge-module-using-linux-containers"></a>Oktatóanyag: C IoT Edge modul fejlesztése Linux-tárolók használatával

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

A Visual Studio Code segítségével alakítsa ki a C kódot, és telepítse azt egy Azure IoT Edge rendszert futtató eszközre.

Az IoT Edge-modulokkal olyan kódot helyezhet üzembe, amely közvetlenül az IoT Edge-eszközökön implementálja az üzleti logikát. Ez az oktatóanyag végigvezeti az érzékelőktől kapott adatokat szűrő IoT Edge-modul létrehozásának és üzembe helyezésének lépésein. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * C-kódban írt IoT Edge-modul létrehozása a Visual Studio Code-dal
> * Docker-rendszerkép létrehozása és közzététele a tárolóregisztrációs adatbázisban a Visual Studio Code és a Docker használatával
> * A modul üzembe helyezése az IoT Edge-eszközön
> * A létrejött adatok megtekintése

Az ebben az oktatóanyagban létrehozott IoT Edge-modul szűri az eszköze által létrehozott hőmérsékletadatokat. Csak akkor küld felfelé irányuló üzeneteket, amikor a hőmérséklet egy megadott küszöbérték felett van. Ez a fajta peremhálózati elemzés a felhőbe küldött és ott tárolt adatok mennyiségének csökkentésére használható.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag bemutatja, hogyan fejleszthet egy modult a **C** -ben a **Visual Studio Code** használatával, és hogyan helyezheti üzembe azt egy IoT Edge eszközön. Ha Windows-tárolókat használó modulokat fejleszt, ugorjon [a C IoT Edge modul fejlesztése Windows-tárolók használatával](tutorial-c-module-windows.md) című lépésre.

A következő táblázat segítségével megismerheti a C modulok Linux-tárolók használatával történő fejlesztésének és üzembe helyezésének lehetőségeit:

| C | Visual Studio Code | Visual Studio |
| - | ------------------ | ------------- |
| **Linux AMD64** | ![A VS Code használata C-modulokhoz Linux AMD64 rendszeren](./media/tutorial-c-module/green-check.png) | ![A VS használata C-modulokhoz Linux AMD64 rendszeren](./media/tutorial-c-module/green-check.png) |
| **Linux ARM32** | ![A VS Code használata C-modulokhoz Linux ARM32](./media/tutorial-c-module/green-check.png) | ![A VS C modulok használata Linux ARM32](./media/tutorial-c-module/green-check.png) |

Az oktatóanyag megkezdése előtt el kellett volna végeznie az előző oktatóanyagot a fejlesztői környezet létrehozásához a Linux-tárolók fejlesztéséhez: [IoT Edge modulok létrehozása Linux-tárolók használatával](tutorial-develop-for-linux.md). Az oktatóanyag elvégzésével a következő előfeltételek szükségesek:

* Egy ingyenes vagy standard szintű [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) az Azure-ban.
* A Linux-tárolókkal Azure IoT Edge rendszert futtató eszköz. A rövid útmutatók segítségével [Linux-eszközt](quickstart-linux.md) vagy [Windows-eszközt](quickstart.md)állíthat be.
* Egy tároló-beállításjegyzék, például [Azure Container Registry](../container-registry/index.yml).
* A [Visual Studio Code](https://code.visualstudio.com/) az [Azure IoT-eszközökkel](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)van konfigurálva.
* A [Docker CE](https://docs.docker.com/install/) Linux-tárolók futtatására van konfigurálva.

A C IoT Edge moduljának fejlesztéséhez telepítse a következő további előfeltételeket a fejlesztői gépre:

* [C/C++-bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools) a Visual Studio Code-hoz.

Az Azure IoT C SDK telepítése nem szükséges ehhez az oktatóanyaghoz, de hasznos funkciókat biztosít, mint például az IntelliSense és a program-definíciók olvasása. Telepítési információk: [Azure IoT C SDK-k és kódtárak](https://github.com/Azure/azure-iot-sdk-c).

## <a name="create-a-module-project"></a>Modul-projekt létrehozása

A következő lépések egy IoT Edge modul-projektet hoznak létre a C számára a Visual Studio Code és az Azure IoT Tools bővítmény használatával. Miután létrehozta a projekt sablonját, vegyen fel új kódot, hogy a modul kiszűrje az üzeneteket a jelentett tulajdonságok alapján.

### <a name="create-a-new-project"></a>Új projekt létrehozása

Létrehozhat egy C-megoldást, amelyet a saját kódjával testreszabhat.

1.   >  A vs Code parancs paletta megnyitásához kattintson a **parancs paletta** megtekintése elemre.

2. A parancskatalógusban írja be és futtassa az **Azure: Sign in** (Azure: bejelentkezés) parancsot, és az utasításokat követve jelentkezzen be Azure-fiókjába. Ha már be van jelentkezve, ezt a lépést kihagyhatja.

3. A parancskatalógusban írja be és futtassa az **Azure IoT Edge: New IoT Edge solution** (Azure IoT Edge: új IoT Edge-megoldás) parancsot. Kövesse a parancskatalógusban található utasításokat a megoldás létrehozásához.

   | Mező | Érték |
   | ----- | ----- |
   | Mappa kiválasztása | Válassza ki azt a helyet a fejlesztői gépen, ahol a VS Code létre fogja hozni a megoldásfájlokat. |
   | Provide a solution name (Megoldásnév megadása) | Adjon meg egy leíró nevet a megoldáshoz, vagy fogadja el az alapértelmezett **EdgeSolution**. |
   | Select module template (Modulsablon kiválasztása) | Válassza a **C modul** lehetőséget. |
   | Provide a module name (Modulnév megadása) | A modulnak adja a **CModule** nevet. |
   | Provide Docker image repository for the module (Docker-rendszerkép adattárának megadása a modulhoz) | Egy rendszerképadattár a tárolóregisztrációs adatbázis nevét és a tárolórendszerkép nevét tartalmazza. A tároló képe előre fel van töltve az utolsó lépésben megadott névvel. Cserélje le a **localhost: 5000** értéket a **bejelentkezési kiszolgáló** értékére az Azure Container registryben. A bejelentkezési kiszolgálót a tároló beállításjegyzékének áttekintés lapjáról kérheti le a Azure Portal. <br><br> A rendszerkép utolsó tárháza a következőhöz hasonlít: \<registry name\> . azurecr.IO/cmodule. |

   ![Docker-rendszerkép adattárának megadása](./media/tutorial-c-module/repository.png)

### <a name="add-your-registry-credentials"></a>A regisztrációs adatbázis hitelesítő adatainak hozzáadása

A környezeti fájl tárolja a tárolóregisztrációs adatbázis hitelesítő adatait, és megosztja őket az IoT-Edge futtatókörnyezettel. A futtatókörnyezetnek szüksége van ezekre a hitelesítő adatokra a privát rendszerképek letöltéséhez az IoT Edge-eszközre.

A IoT Edge bővítmény megpróbálja lekérni a tároló beállításjegyzékbeli hitelesítő adatait az Azure-ból, és feltölti azokat a környezeti fájlban. Ellenőrizze, hogy a hitelesítő adatok már szerepelnek-e. Ha nem, adja hozzá őket most:

1. A VS Code Explorerben nyissa meg a .env fájlt.
2. Adja meg az Azure Container Registryből kimásolt **felhasználónevet** és **jelszót** a megfelelő mezőkben.
3. Mentse el ezt a fájlt.

### <a name="select-your-target-architecture"></a>Válassza ki a cél architektúrát

A Visual Studio Code jelenleg Linux AMD64 és Linux rendszerű ARM32v7-eszközökhöz is fejleszt C modulokat. Ki kell választania, hogy melyik architektúrát célozza meg az egyes megoldásokkal, mivel a tárolót az egyes architektúrák típusainál eltérően építették és futtatják. Az alapértelmezett érték a Linux AMD64.

1. Nyissa meg a parancssort, és keressen rá **Azure IoT Edge: állítsa be az alapértelmezett cél platformot az Edge megoldáshoz**, vagy válassza a parancsikon ikont az ablak alján található oldalsó sávban.

2. A parancs palettáján válassza ki a cél architektúrát a lehetőségek listájából. Ebben az oktatóanyagban egy Ubuntu rendszerű virtuális gépet használunk IoT Edge eszközként, így megtarthatja az alapértelmezett **amd64**-t.

### <a name="update-the-module-with-custom-code"></a>A modul módosítása egyéni kóddal

Az alapértelmezett modul kódja üzeneteket fogad egy bemeneti várólistán, és egy kimeneti várólistán keresztül továbbítja azokat. Vegyünk fel néhány további kódot, hogy a modul feldolgozza az üzeneteket a peremen, mielőtt továbbítaná őket a IoT Hubba. Frissítse a modult úgy, hogy az minden üzenetben elemezze a hőmérsékleti adatokat, és csak akkor küldje el az üzenetet IoT Hub, ha a hőmérséklet meghaladja az adott küszöbértéket.

1. Ebben a forgatókönyvben az érzékelőről származó adatok JSON formátumban szerepelnek. A JSON formátumú üzenetek szűréséhez importáljon egy C-hez készült JSON-kódtárat. Ez az oktatóanyag Parson-kódtárat használ.

   1. Töltse le a [plébános GitHub-tárházát](https://github.com/kgabis/parson). Másolja a **parson.c** és a **parson.h** fájlokat a **CModule** mappába.

   2. Nyissa meg a **modulok**  >  **CModule**  >  **CMakeLists.txt**. A fájl tetején importálja a Parson-fájlokat **my_parson** nevű kódtárként.

      ```txt
      add_library(my_parson
          parson.c
          parson.h
      )
      ```

   3. Adja hozzá a `my_parson` kódtárak listáját a CMakeLists.txt **target_link_libraries** függvényében.

   4. Mentse a **CMakeLists.txt** fájlt.

   5. Nyissa meg a **modules**  >  **CModule**  >  **Main. c**. A belefoglalási utasítások listájának alján adjon hozzá egy újat a JSON- `parson.h` támogatáshoz:

      ```c
      #include "parson.h"
      ```

1. A **main.c** fájlban adjon hozzá egy `temperatureThreshold` nevű globális változót az include szakasz után. Ez a változó azt az értéket állítja be, amelyet a mért hőmérsékletnek túl kell lépnie ahhoz, hogy a rendszer elküldje az adatokat az IoT Hubnak.

    ```c
    static double temperatureThreshold = 25;
    ```

1. Keresse meg a `CreateMessageInstance` függvényt a Main. c-ben. Cserélje le a belső if-Else utasítást a következő kódra, amely néhány sornyi funkciót is felvesz:

   ```c
       if ((messageInstance->messageHandle = IoTHubMessage_Clone(message)) == NULL)
       {
           free(messageInstance);
           messageInstance = NULL;
       }
       else
       {
           messageInstance->messageTrackingId = messagesReceivedByInput1Queue;
           MAP_HANDLE propMap = IoTHubMessage_Properties(messageInstance->messageHandle);
           if (Map_AddOrUpdate(propMap, "MessageType", "Alert") != MAP_OK)
           {
              printf("ERROR: Map_AddOrUpdate Failed!\r\n");
           }
       }
   ```

   Az else utasításban szereplő új sorok új tulajdonságot adhatnak hozzá az üzenethez, amely riasztásként felcímkézi az üzenetet. Ez a kód minden olyan üzenetet felcímkéz, amely riasztásként jelenik meg, mert olyan funkciókat adunk hozzá, amelyek csak akkor küldenek üzeneteket IoT Hub, ha magas hőmérsékletet jelentenek.

1. Cserélje le a teljes `InputQueue1Callback` függvényt az alábbi kódra. Ez a függvény valósítja meg a tényleges üzenetszűrőt. Üzenet érkezésekor ellenőrzi, hogy a jelentett hőmérséklet meghaladja-e a küszöbértéket. Ha igen, akkor a kimeneti várólistán keresztül továbbítja az üzenetet. Ha nem, akkor figyelmen kívül hagyja az üzenetet.

    ```c
    static unsigned char *bytearray_to_str(const unsigned char *buffer, size_t len)
    {
        unsigned char *ret = (unsigned char *)malloc(len + 1);
        memcpy(ret, buffer, len);
        ret[len] = '\0';
        return ret;
    }

    static IOTHUBMESSAGE_DISPOSITION_RESULT InputQueue1Callback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
    {
        IOTHUBMESSAGE_DISPOSITION_RESULT result;
        IOTHUB_CLIENT_RESULT clientResult;
        IOTHUB_MODULE_CLIENT_LL_HANDLE iotHubModuleClientHandle = (IOTHUB_MODULE_CLIENT_LL_HANDLE)userContextCallback;

        unsigned const char* messageBody;
        size_t contentSize;

        if (IoTHubMessage_GetByteArray(message, &messageBody, &contentSize) == IOTHUB_MESSAGE_OK)
        {
            messageBody = bytearray_to_str(messageBody, contentSize);
        } else
        {
            messageBody = "<null>";
        }

        printf("Received Message [%zu]\r\n Data: [%s]\r\n",
                messagesReceivedByInput1Queue, messageBody);

        // Check if the message reports temperatures higher than the threshold
        JSON_Value *root_value = json_parse_string(messageBody);
        JSON_Object *root_object = json_value_get_object(root_value);
        double temperature;
        if (json_object_dotget_value(root_object, "machine.temperature") != NULL && (temperature = json_object_dotget_number(root_object, "machine.temperature")) > temperatureThreshold)
        {
            printf("Machine temperature %f exceeds threshold %f\r\n", temperature, temperatureThreshold);
            // This message should be sent to next stop in the pipeline, namely "output1".  What happens at "outpu1" is determined
            // by the configuration of the Edge routing table setup.
            MESSAGE_INSTANCE *messageInstance = CreateMessageInstance(message);
            if (NULL == messageInstance)
            {
                result = IOTHUBMESSAGE_ABANDONED;
            }
            else
            {
                printf("Sending message (%zu) to the next stage in pipeline\n", messagesReceivedByInput1Queue);

                clientResult = IoTHubModuleClient_LL_SendEventToOutputAsync(iotHubModuleClientHandle, messageInstance->messageHandle, "output1", SendConfirmationCallback, (void *)messageInstance);
                if (clientResult != IOTHUB_CLIENT_OK)
                {
                    IoTHubMessage_Destroy(messageInstance->messageHandle);
                    free(messageInstance);
                    printf("IoTHubModuleClient_LL_SendEventToOutputAsync failed on sending msg#=%zu, err=%d\n", messagesReceivedByInput1Queue, clientResult);
                    result = IOTHUBMESSAGE_ABANDONED;
                }
                else
                {
                    result = IOTHUBMESSAGE_ACCEPTED;
                }
            }
        }
        else
        {
            printf("Not sending message (%zu) to the next stage in pipeline.\r\n", messagesReceivedByInput1Queue);
            result = IOTHUBMESSAGE_ACCEPTED;
        }

        messagesReceivedByInput1Queue++;
        return result;
    }
    ```

1. Adjon hozzá egy `moduleTwinCallback` függvényt. Ez a metódus a kívánt tulajdonságok frissítéseit fogadja a modul ikerdokumentumától, és ennek megfelelően frissíti a **temperatureThreshold** változót. Minden modul rendelkezik saját ikerdokumentummal, amelyekkel közvetlenül a felhőből konfigurálhatja a modulban futó kódot.

    ```c
    static void moduleTwinCallback(DEVICE_TWIN_UPDATE_STATE update_state, const unsigned char* payLoad, size_t size, void* userContextCallback)
    {
        printf("\r\nTwin callback called with (state=%s, size=%zu):\r\n%s\r\n",
            MU_ENUM_TO_STRING(DEVICE_TWIN_UPDATE_STATE, update_state), size, payLoad);
        JSON_Value *root_value = json_parse_string(payLoad);
        JSON_Object *root_object = json_value_get_object(root_value);
        if (json_object_dotget_value(root_object, "desired.TemperatureThreshold") != NULL) {
            temperatureThreshold = json_object_dotget_number(root_object, "desired.TemperatureThreshold");
        }
        if (json_object_get_value(root_object, "TemperatureThreshold") != NULL) {
            temperatureThreshold = json_object_get_number(root_object, "TemperatureThreshold");
        }
    }
    ```

1. Keresse meg a `SetupCallbacksForModule` függvényt. Cserélje le a függvényt a következő kódra, amely egy **Else if** utasítást tartalmaz, amely azt vizsgálja, hogy a Twin modul frissítve lett-e.

   ```c
   static int SetupCallbacksForModule(IOTHUB_MODULE_CLIENT_LL_HANDLE iotHubModuleClientHandle)
   {
       int ret;

       if (IoTHubModuleClient_LL_SetInputMessageCallback(iotHubModuleClientHandle, "input1", InputQueue1Callback, (void*)iotHubModuleClientHandle) != IOTHUB_CLIENT_OK)
       {
           printf("ERROR: IoTHubModuleClient_LL_SetInputMessageCallback(\"input1\")..........FAILED!\r\n");
           ret = MU_FAILURE;
       }
       else if (IoTHubModuleClient_LL_SetModuleTwinCallback(iotHubModuleClientHandle, moduleTwinCallback, (void*)iotHubModuleClientHandle) != IOTHUB_CLIENT_OK)
       {
           printf("ERROR: IoTHubModuleClient_LL_SetModuleTwinCallback(default)..........FAILED!\r\n");
           ret = MU_FAILURE;
       }
       else
       {
           ret = 0;
       }

       return ret;
   }
   ```

1. Mentse a main.c fájlt.

1. A VS Code Explorerben nyissa meg a IoT Edge megoldás munkaterületen található fájl **deployment.template.js** .

1. Adja hozzá a CModule ikermodult az üzembehelyezési jegyzékhez. Szúrja be a következő JSON-tartalmat a `moduleContent` szakasz alján, az `$edgeHub` modul ikerdokumentuma után:

   ```json
   "CModule": {
       "properties.desired":{
           "TemperatureThreshold":25
       }
   }
   ```

   ![A CModule ikermodul hozzáadása az üzembehelyezési sablonhoz](./media/tutorial-c-module/module-twin.png)

1. Mentse a **deployment.template.json** fájlt.

## <a name="build-and-push-your-module"></a>Modul létrehozása és leküldése

Az előző szakaszban létrehozott egy IoT Edge megoldást, és hozzáadta a kódot a CModule, amely kiszűri azokat az üzeneteket, amelyekben a jelentett gépi hőmérséklet az elfogadható határértékeken belül van. Most létre kell hoznia a megoldást tárolórendszerképként, és le kell küldenie a tárolóregisztrációs adatbázisba.

1. Nyissa meg a vs Code terminált a terminál **megtekintése** lehetőség kiválasztásával  >  .

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

4. Az eszköz alatt bontsa ki a **modulok** elemet a telepített és futó modulok listájának megtekintéséhez. Kattintson a frissítés gombra. Meg kell jelennie az új **CModule** , amelyen a **SimulatedTemperatureSensor** modul, valamint a **$edgeAgent** és a **$edgeHub** fut.

    A modulok elindításához néhány percet is igénybe vehet. Az IoT Edge futtatókörnyezetnek meg kell kapnia az új üzembe helyezési jegyzékfájlt, le kell kérnie a modul lemezképeit a tároló futtatókörnyezetből, majd el kell indítania az új modulokat.

## <a name="view-generated-data"></a>A létrejött adatok megtekintése

Miután alkalmazta az üzembehelyezési jegyzéket az IoT Edge-eszközén, az eszköz IoT Edge-futtatókörnyezet összegyűjti az új környezettel kapcsolatos információkat, és megkezdi a végrehajtást. Az eszközön minden olyan futó modul leáll, amely nem szerepel az üzembehelyezési jegyzékben. Az eszközről hiányzó modulok elindulnak.

1. A Visual Studio Code Explorerben kattintson a jobb gombbal a IoT Edge eszköz nevére, és válassza a **figyelés beépített esemény végpontjának indítása** lehetőséget.

2. Megtekintheti a IoT Hub érkező üzeneteket. Eltarthat egy ideig, amíg az üzenetek megérkeznek, mivel a IoT Edge eszköznek meg kell kapnia az új központi telepítést, és el kell indítania az összes modult. Ezután megvárja a CModule-kód módosításait, amíg a gép hőmérséklete 25 fokkal nem éri el az üzenetek küldését. Az üzenet típusú **riasztást** is hozzáadja az adott hőmérsékleti küszöbértéket elérő üzenetekhez.

   ![IoT Hubra érkező üzenetek megtekintése](./media/tutorial-c-module/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>A modul két különálló szerkesztése

A CModule modult az üzembe helyezési jegyzékben használta, hogy 25 fokos hőmérsékleti küszöbértéket állítson be. A Twin modul használatával módosíthatja a funkciót anélkül, hogy frissítenie kellene a modul kódját.

1. A Visual Studio Code-ban bontsa ki a IoT Edge eszköz alatti részleteket a futó modulok megtekintéséhez.

2. Kattintson a jobb gombbal a **CModule** elemre, és válassza a **modul Twin szerkesztése** lehetőséget.

3. A kívánt tulajdonságok között keresse meg a **TemperatureThreshold** . Módosítsa az értékét egy új 5 fokos hőmérsékletre, a legutóbbi jelentett hőmérsékletnél pedig 10 fokkal magasabbra.

4. Mentse a modul különálló fájlját.

5. Kattintson a jobb gombbal a modul dupla szerkesztési paneljén bárhová, és válassza a **modul Twin frissítése** elemet.

6. A bejövő eszközről a felhőbe irányuló üzenetek figyelése. Ekkor az új hőmérsékleti küszöb eléréséig az üzenetek leállnak.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy a következő ajánlott cikkel folytatja, megtarthatja és újból felhasználhatja a létrehozott erőforrásokat és konfigurációkat. Azt is megteheti, hogy ugyanezt az IoT Edge-eszközt használja teszteszközként.

Ellenkező esetben törölheti a cikkben használt helyi konfigurációkat és az Azure-erőforrásokat a díjak elkerüléséhez.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban olyan kódot tartalmazó IoT Edge-modult hozott létre, amely szűri az IoT Edge-eszköz által létrehozott nyers adatokat.

Folytassa a következő oktatóanyagokkal, amelyből megtudhatja, hogyan hozhatja Azure IoT Edge az Azure Cloud Services üzembe helyezését az adathordozón lévő adatfeldolgozás és-elemzés során.

> [!div class="nextstepaction"]
> [Függvények](tutorial-deploy-function.md) 
>  [Stream Analytics](tutorial-deploy-stream-analytics.md) 
>  [Machine learning](tutorial-deploy-machine-learning.md) 
>  [Custom Vision Service](tutorial-deploy-custom-vision.md)