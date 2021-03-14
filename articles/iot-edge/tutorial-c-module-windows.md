---
title: Oktatóanyag – C-modulok fejlesztése a Windowshoz Azure IoT Edge használatával
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre IoT Edge modulokat C kóddal, és hogyan telepítheti azokat Windows-eszközökre, amelyek IoT Edge futtatnak.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 05/28/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 8f019c8f3c560fdfdc0c8e5992389c253c9b0d74
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/14/2021
ms.locfileid: "103463374"
---
# <a name="tutorial-develop-c-iot-edge-modules-using-windows-containers"></a>Oktatóanyag: C IoT Edge-modulok fejlesztése Windows-tárolók használatával

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Ebből a cikkből megtudhatja, hogyan használhatja a Visual studiót a C kód fejlesztéséhez, és hogyan helyezheti üzembe azt egy Azure IoT Edge rendszert futtató Windows-eszközön.

>[!NOTE]
>IoT Edge 1,1 LTS az utolsó kiadási csatorna, amely támogatni fogja a Windows-tárolókat. A 1,2-es verziótól kezdődően a Windows-tárolók nem támogatottak. A Windows rendszerű eszközökön [IoT Edge Linux rendszeren](iot-edge-for-linux-on-windows.md) való használatának vagy áthelyezésének megfontolnia, hogy Windows-eszközökön fusson IoT Edge.

Azure IoT Edge-modulok használatával olyan programkódot helyezhet üzembe, amely közvetlenül a IoT Edge-eszközökön valósítja meg az üzleti logikát. Ez az oktatóanyag végigvezeti az érzékelőktől kapott adatokat szűrő IoT Edge-modul létrehozásának és üzembe helyezésének lépésein.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * A Visual Studio használatával hozzon létre egy IoT Edge modult, amely a C SDK-ra épül.
> * A Visual Studio és a Docker használatával hozzon létre egy Docker-rendszerképet, és tegye közzé azt a beállításjegyzékben.
> * A modul üzembe helyezése az IoT Edge-eszközön.
> * A létrejött adatok megtekintése.

Az ebben az oktatóanyagban létrehozott IoT Edge-modul szűri az eszköze által létrehozott hőmérsékletadatokat. A modul csak akkor küldi az üzeneteket, ha a hőmérséklet meghaladja a megadott küszöbértéket. Ez a fajta peremhálózati elemzés a felhőbe küldött és ott tárolt adatok mennyiségének csökkentésére használható.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag bemutatja, hogyan fejleszthet a C modulban a Visual Studio 2019 segítségével, majd hogyan telepítheti azt egy Windows-eszközre. Ha Linux-tárolókat használó modulokat fejleszt, ugorjon a [C IoT Edge-modulok fejlesztése Linux-tárolók használatával](tutorial-csharp-module.md) című lépésre.

A C modulok Windows-tárolókkal való fejlesztésének és üzembe helyezésének lehetőségeiről a következő táblázatban talál további információt:

| C | Visual &nbsp; Studio &nbsp; Code | Visual Studio 2017 &nbsp; és &nbsp; 2019 |
| -- | ------------------ | :------------------: |
| Windows AMD64 |  | ![C modulok fejlesztése a WinAMD64 a Visual Studióban](./media/tutorial-c-module/green-check.png) |

Mielőtt elkezdené ezt az oktatóanyagot, állítsa be a fejlesztési környezetet a [IoT Edge modulok fejlesztése a Windows-tárolók használatával](tutorial-develop-for-windows.md) című oktatóanyag utasításait követve. A befejezése után a környezet a következő előfeltételeket fogja tartalmazni:

* Egy ingyenes vagy standard szintű [IoT hub](../iot-hub/iot-hub-create-through-portal.md) az Azure-ban.
* [Azure IoT Edge rendszert futtató Windows-eszköz](how-to-install-iot-edge-windows-on-windows.md).
* Egy tároló-beállításjegyzék, például [Azure Container Registry](../container-registry/index.yml).
* A [Visual Studio 2019](/visualstudio/install/install-visual-studio), amely a [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) bővítménnyel van konfigurálva.
* A [Docker Desktop](https://docs.docker.com/docker-for-windows/install/), amely Windows-tárolók futtatására van konfigurálva.

Az alábbi parancsok futtatásával telepítse az Azure IoT C SDK for Windows x64-et a vcpkg-en keresztül:

   ```powershell
   git clone https://github.com/Microsoft/vcpkg
   cd vcpkg
   .\bootstrap-vcpkg.bat
   .\vcpkg install azure-iot-sdk-c:x64-windows
   .\vcpkg --triplet x64-windows integrate install
   ```

> [!TIP]
> Ha a Visual Studio 2017-es verzióját (15,7-es vagy újabb verziót) használja, töltse le és telepítse Azure IoT Edge Tools for Visual Studio 2017 alkalmazást a Visual Studio [Marketplace](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools)-ről.

## <a name="create-a-module-project"></a>Modul-projekt létrehozása

Ebben a szakaszban egy IoT Edge modul-projektet hoz létre, amely a C SDK-ra épül a Visual Studio és a Azure IoT Edge Tools bővítmény használatával. A sablon létrehozása után új kódot kell hozzáadnia, hogy a modul kiszűrje az üzeneteket a jelentett tulajdonságaik alapján.

### <a name="create-a-new-project"></a>Új projekt létrehozása

Létrehozhat egy C-megoldást, amelyet a saját kódjával testreszabhat.

1. Nyissa meg a Visual Studio 2019 alkalmazást, majd válassza az **új projekt létrehozása** lehetőséget.

1. A **create a New Project (új projekt létrehozása** ) panelen keresse meg **IoT Edge** , majd az eredmények listájában válassza ki a **Azure IoT Edge (Windows amd64)** projektet.

   ![Képernyőkép a IoT Edge "új projekt létrehozása" panelről.](./media/tutorial-c-module-windows/new-project.png)

1. Kattintson a **Tovább** gombra.

    Megnyílik az **új projekt konfigurálása** panel.

   ![Képernyőkép az "új projekt konfigurálása" panelről.](./media/tutorial-c-module-windows/configure-project.png)

1. Az **új projekt konfigurálása** panelen nevezze át a projektet és a megoldást valami más leíróra, például **CTutorialApp**. 

1. A projekt létrehozásához válassza a **Létrehozás** lehetőséget.

   Megnyílik a **modul hozzáadása** panel.

   ![Képernyőkép a projekt konfigurálásához szükséges "modul hozzáadása" panelről.](./media/tutorial-c-module-windows/add-application-and-module.png)

1. Az **új projekt konfigurálása** lapon tegye a következőket:

   a. A bal oldali panelen válassza ki a **C modul** sablonját.  
   b. A **modul neve** mezőbe írja be a **CModule** nevet.  
   c. Az **adattár URL-címe** mezőben cserélje le a **localhost: 5000** értéket az Azure Container Registry **bejelentkezési kiszolgáló** értékére, a következő formátumban: `<registry name>.azurecr.io/cmodule`

    > [!NOTE]
    > Egy rendszerképadattár a tárolóregisztrációs adatbázis nevét és a tárolórendszerkép nevét tartalmazza. A tároló képe előre fel van töltve a modul Project-Name értékével.  A bejelentkezési kiszolgálót a tároló beállításjegyzékének áttekintés lapjáról kérheti le a Azure Portal.

1. A projekt létrehozásához válassza a **Hozzáadás** lehetőséget.

### <a name="add-your-registry-credentials"></a>A regisztrációs adatbázis hitelesítő adatainak hozzáadása

Az üzembe helyezési jegyzék megosztja a tároló beállításjegyzékének hitelesítő adatait a IoT Edge futtatókörnyezettel. A futtatókörnyezetnek szüksége van ezekre a hitelesítő adatokra a privát rendszerképek letöltéséhez az IoT Edge-eszközre. Használja az Azure Container Registry **hozzáférési kulcsok** szakaszának hitelesítő adatait.

1. A Visual Studio Megoldáskezelő nyissa *meg adeployment.template.js* fájlt.

1. Az $edgeAgent kívánt tulajdonságok között keresse meg a **registryCredentials** tulajdonságot. A tulajdonsághoz tartozó beállításjegyzékbeli címnek a projekt létrehozásakor megadott információval kell kitöltenie. A Felhasználónév és a jelszó mezőben a változók neveinek kell szerepelniük. Például:

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "$CONTAINER_REGISTRY_USERNAME_<registry name>",
       "password": "$CONTAINER_REGISTRY_PASSWORD_<registry name>",
       "address": "<registry name>.azurecr.io"
     }
   }
   ```

1. Nyissa meg a környezeti (ENV) fájlt a modul-megoldásban. Alapértelmezés szerint a fájl el van rejtve Megoldáskezelőban, ezért előfordulhat, hogy az **összes fájl megjelenítése** gombra kell kattintania a megjelenítéséhez. Az ENV-fájlnak ugyanazt a Felhasználónév és jelszó változót kell tartalmaznia, amelyet a fájl *deployment.template.jsban* látott.

1. Adja hozzá a **Felhasználónév** és a **jelszó** értékét az Azure Container registryből.

1. Mentse a módosításokat az ENV-fájlba.

### <a name="update-the-module-with-custom-code"></a>A modul módosítása egyéni kóddal

Az alapértelmezett modul kódja üzeneteket fogad egy bemeneti várólistában, és egy kimeneti várólistán keresztül továbbítja azokat. Adjunk hozzá még egy kódot, hogy a modul feldolgozza az üzeneteket a peremen, mielőtt továbbítaná azokat az IoT hubhoz. Frissítse a modult úgy, hogy az minden üzenetben elemezze a hőmérsékleti adatokat, és csak akkor küldje el az üzenetet az IoT hub számára, ha a hőmérséklet meghaladja az adott küszöbértéket.

1. Ebben a forgatókönyvben az érzékelőről származó adatok JSON formátumban szerepelnek. A JSON formátumú üzenetek szűréséhez importáljon egy C-hez készült JSON-kódtárat. Ez az oktatóanyag Parson-kódtárat használ.

   a. Töltse le a [plébános GitHub-tárházát](https://github.com/kgabis/parson).  
   b. Másolja a *plébános. c* és a *plébános. h* fájlokat a CModule projektbe.  
   c. A Visual Studióban nyissa meg a *CMakeLists.txt* fájlt a CModule Project mappából.  
   d. A fájl tetején importálja a Parson-fájlokat **my_parson** nevű kódtárként.

      ```txt
      add_library(my_parson
          parson.c
          parson.h
      )
      ```  
   e. Adja hozzá a `my_parson` könyvtárat a *CMakeLists.txt* fájl "target_link_libraries" szakaszában található könyvtárak listájához.  
   f. Mentse a *CMakeLists.txt* fájlt.  
   : Válassza a **CModule**  >  **Main. c** elemet. A belefoglalási utasítások listájának alján vegyen fel egy új utasítást, amely tartalmazza a `parson.h` JSON-támogatást:

      ```c
      #include "parson.h"
      ```

1. A *Main. c* fájlban adjon hozzá egy nevű globális változót `temperatureThreshold` a változó mellett `messagesReceivedByInput1Queue` . Ez a változó úgy állítja be az értéket, hogy a mért hőmérsékletnek meg kell haladnia az IoT hub számára küldendő adatmennyiséget.

    ```c
    static double temperatureThreshold = 25;
    ```

1. Keresse meg a `CreateMessageInstance` függvényt a *Main. c*-ben. Cserélje le a belső *IF-Else* utasítást a következő kódra, amely néhány sornyi funkciót is felvesz:

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

   Az *Else* utasításban szereplő új sorok új tulajdonságot adhatnak hozzá az üzenethez, amely riasztásként felcímkézi az üzenetet. Ez a kód minden üzenetet felcímkéz riasztásként, mert olyan funkciókat adunk hozzá, amelyek csak akkor küldenek üzeneteket az IoT hubhoz, ha magas hőmérsékletet jelentenek.

1. Keresse meg a `InputQueue1Callback` függvényt, és cserélje le a teljes függvényt a következő kódra. Ez a függvény valósítja meg a tényleges üzenetszűrőt. Üzenet érkezésekor ellenőrzi, hogy a jelentett hőmérséklet meghaladja-e a küszöbértéket. Ha a hőmérséklet meghaladja a küszöbértéket, a függvény a kimeneti várólistán keresztül továbbítja az üzenetet. Ha nem lépi túl a küszöbértéket, a függvény figyelmen kívül hagyja az üzenetet.

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

        // Check whether the message reports temperatures that exceed the threshold
        JSON_Value *root_value = json_parse_string(messageBody);
        JSON_Object *root_object = json_value_get_object(root_value);
        double temperature;

        // If temperature exceeds the threshold, send to output1
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
        // If message does not exceed the threshold, do not forward
        else
        {
            printf("Not sending message (%zu) to the next stage in pipeline.\r\n", messagesReceivedByInput1Queue);
            result = IOTHUBMESSAGE_ACCEPTED;
        }

        messagesReceivedByInput1Queue++;
        return result;
    }
    ```

1. Adjon hozzá egy `moduleTwinCallback` függvényt. Ez a metódus a kívánt tulajdonságok frissítéseit fogadja a modul ikerdokumentumától, és ennek megfelelően frissíti a **temperatureThreshold** változót. Minden modulhoz külön modul tartozik, amely lehetővé teszi, hogy a modulon belül futtatott kódot közvetlenül a felhőből konfigurálja.

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

1. Keresse meg a `SetupCallbacksForModule` függvényt. Cserélje le a függvényt a következő kódra, amely egy *másik-if* utasítást vesz fel annak vizsgálatához, hogy a Twin modul frissítve lett-e.

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

1. Mentse a *main.c* fájlt.

1. Nyissa meg a *deployment.template.js* fájlt.

1. Adja hozzá a Twin **CModule** modult a telepítési jegyzékhez. Szúrja be a következő JSON-tartalmat a `moduleContent` szakasz alján, az `$edgeHub` modul ikerdokumentuma után:

   ```json
   "CModule": {
       "properties.desired":{
           "TemperatureThreshold":25
       }
   }
   ```

   ![Képernyőfelvétel: az üzembe helyezési sablonhoz hozzáadandó modul.](./media/tutorial-c-module-windows/module-twin.png)

1. Mentse a *deployment.template.json* fájlt.

## <a name="build-and-push-your-module"></a>Modul létrehozása és leküldése

Az előző szakaszban létrehozott egy IoT Edge megoldást, és hozzáadta a kódot a **CModule** , hogy kiszűrje azokat az üzeneteket, amelyekben a jelentett gépi hőmérséklet az elfogadható küszöbérték alá esik. Most létre kell hoznia a megoldást tárolórendszerképként, és le kell küldenie a tárolóregisztrációs adatbázisba.

### <a name="sign-in-to-docker"></a>Bejelentkezés a Docker-be

Adja meg a tároló beállításjegyzékbeli hitelesítő adatait a Docker számára a fejlesztői gépen, így a tároló rendszerképét leküldheti a beállításjegyzékben.

1. Nyisson meg egy PowerShell-t vagy egy parancssorablakot.

1. Jelentkezzen be a Docker-be a beállításjegyzék létrehozása után mentett Azure Container Registry hitelesítő adatokkal.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Előfordulhat, hogy olyan biztonsági figyelmeztetést kap, amely a használatát javasolja `--password-stdin` . Bár ezt ajánlott eljárásként javasoljuk a termelési forgatókönyvek esetében, az oktatóanyag hatókörén kívül esik. További információkért lásd a [Docker bejelentkezési referenciáját](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin).

### <a name="build-and-push"></a>Létrehozás és leküldés

A fejlesztői gép most már rendelkezik hozzáféréssel a tároló beállításjegyzékéhez, és a IoT Edge-eszközök is elérhetők lesznek. Itt az ideje, hogy bekapcsolja a projekt kódját egy tároló képébe.

1. A Visual Studio Megoldáskezelőban kattintson a jobb gombbal a létrehozni kívánt projekt nevére. Az alapértelmezett név a **AzureIotEdgeApp1**. Ebben az oktatóanyagban a **CTutorialApp** nevet választottuk, mivel Windows-modult készít, a bővítménynek **Windows. Amd64**-nek kell lennie.

1. Válassza ki **az IoT Edge-modulok kiépítése és leküldése** lehetőséget.

   A build és a push parancs három műveletet indít el:
   * Először létrehoz egy új mappát a *konfiguráció* nevű megoldásban, amely tartalmazza a teljes telepítési jegyzéket. A szolgáltatás a központi telepítési sablonban és más megoldás fájljaiban található információk alapján készült. 
   * Másodszor, futtatja `docker build` a tároló rendszerképének összeállítását a cél architektúrájának megfelelő Docker alapján. 
   * Végül futtatja, `docker push` hogy leküldi a rendszerkép-tárházat a tároló-beállításjegyzékbe.

   Ez a folyamat az első alkalommal több percet is igénybe vehet, de a parancsok legközelebb történő futtatása gyorsabb lesz.

## <a name="deploy-modules-to-the-device"></a>Modulok üzembe helyezése az eszközön

A Visual Studio Cloud Explorer és a Azure IoT Edge Tools bővítmény használatával telepítse a modul-projektet a IoT Edge eszközre. Már előkészített egy üzembe helyezési jegyzéket a forgatókönyvhöz, a *konfigurációs* mappában található fájl *deployment.windows-amd64.js* . Most csak ki kell választania az üzemelő példányt fogadó eszközt.

Ellenőrizze, hogy a IoT Edge eszköz működik-e.

1. A Visual Studio Cloud Explorerben bontsa ki az erőforrásokat a IoT-eszközök listájának megtekintéséhez.

1. Kattintson a jobb gombbal annak a IoT Edge eszköznek a nevére, amelyről szeretné fogadni a központi telepítést.

1. Válassza a **központi telepítés létrehozása** lehetőséget.

1. A Visual Studio fájlkezelőben válassza ki a *deployment.windows-amd64.js* fájlt a megoldás *konfigurációs* mappájába.

1. A Cloud Explorer frissítésével megtekintheti az eszköz alatt felsorolt telepített modulokat.

## <a name="view-generated-data"></a>A létrejött adatok megtekintése

Miután alkalmazza az üzembe helyezési jegyzéket a IoT Edge eszközre, az eszközön futó IoT Edge-futtatókörnyezet gyűjti az új központi telepítési adatokat, és elindítja a futtatását. Az eszközön futó, de az üzembe helyezési jegyzékben nem szereplő modulok leállnak. Az eszközről hiányzó modulok elindulnak.

A IoT Edge Tools bővítmény használatával megtekintheti az IoT hub-ra beérkező üzeneteket.

1. A Visual Studio Cloud Explorerben válassza ki a IoT Edge eszköz nevét.

1. A **műveletek** listában válassza a **figyelés beépített esemény végpontjának elindítása** lehetőséget.

1. Megtekintheti az IoT hub-ra érkező üzeneteket. Eltarthat egy ideig, amíg az üzenetek megérkeznek, mivel a IoT Edge eszköznek meg kell kapnia az új központi telepítést, és el kell indítania az összes modult. A CModule-kódnak kell megvárnia, amíg a gép hőmérséklete 25 fokkal nem éri el az üzenetek küldését. A kód az üzenet típusú **riasztást** is hozzáadja az adott hőmérsékleti küszöbértéket elérő üzenetekhez.

   ![A kimeneti ablak képernyőképe, amely az IoT hub-on érkező üzeneteket jeleníti meg.](./media/tutorial-c-module-windows/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>A modul két különálló szerkesztése

A CModule modult a Twin értékre használta, hogy a hőmérsékleti küszöbértéket 25 fokos értékre állítsa be. A Twin modul használatával módosíthatja a funkciót anélkül, hogy frissítenie kellene a modul kódját.

1. A Visual Studióban nyissa meg a *deployment.windows-amd64.js* fájlt. 

   Ne *nyissa* meg a *Deployment. template* fájlt. Ha nem látja az üzembe helyezési jegyzéket a Megoldáskezelő található *konfigurációs* fájlban, válassza a megoldáskezelő eszköztár **összes fájl megjelenítése** ikonját.

1. Keresse meg a CModule Twin értéket, és módosítsa a **temperatureThreshold** paraméter értékét egy olyan új hőmérsékletre, amely a legutóbbi jelentett hőmérsékletnél 5 – 10 fok.

1. Mentse a *deployment.windows-amd64.js* fájlt.

1. Kövesse a központi telepítési útmutatót, és alkalmazza a frissített üzembe helyezési jegyzéket az eszközre.

1. A bejövő eszközről a felhőbe irányuló üzenetek figyelése. Az új hőmérsékleti küszöb eléréséig az üzenetek le kell állnia.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy folytatja a következő javasolt cikket, megtarthatja és újra felhasználhatja az oktatóanyagban létrehozott erőforrásokat és konfigurációkat. Azt is megteheti, hogy ugyanezt az IoT Edge-eszközt használja teszteszközként.

Ellenkező esetben a felmerülő költségek elkerülése érdekében törölheti a helyi konfigurációkat és az itt használt Azure-erőforrásokat.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban IoT Edge-modult hozott létre olyan kóddal, amely szűri az IoT Edge-eszköz által létrehozott nyers adatokat.

Annak megismeréséhez, hogy a Azure IoT Edge hogyan segíthet az Azure Cloud Services üzembe helyezésében az adatfeldolgozásban és az adatelemzésben, folytassa a következő oktatóanyagokkal.

> [!div class="nextstepaction"]
> [Azure functions](tutorial-deploy-function.md) 
>  [Azure stream Analytics](tutorial-deploy-stream-analytics.md) 
>  [Azure Machine learning](tutorial-deploy-machine-learning.md) 
>  [Custom Vision szolgáltatás](tutorial-deploy-custom-vision.md)
