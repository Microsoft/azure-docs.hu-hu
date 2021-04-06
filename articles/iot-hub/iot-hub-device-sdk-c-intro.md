---
title: A C Azure IoT eszközoldali SDK-je | Microsoft Docs
description: Ismerkedjen meg a C Azure IoT eszközoldali SDK-val, és Ismerje meg, hogyan hozhat létre IoT-hubhoz kommunikáló eszközöket.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: robinsh
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: 17b31e365e311b97e322828927827f40733313a6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97588828"
---
# <a name="azure-iot-device-sdk-for-c"></a>C-hez készült Azure IoT eszközoldali SDK

Az **Azure IoT ESZKÖZOLDALI SDK** olyan kódtárak összessége, amelyek leegyszerűsítik az üzenetek küldésének és fogadásának folyamatát az **Azure IoT hub** szolgáltatásból. Az SDK különböző variációi vannak, amelyek mindegyike egy adott platformra vonatkozik, de ez a cikk a **C Azure IoT Device SDK**-t ismerteti.

> [!NOTE]
> A beágyazott C SDK a saját hálózat használata (BYON) megközelítését támogató korlátozott eszközök alternatívája. A IoT-fejlesztők szabadon hozhatják létre a MQTT-ügyfelet, a TLS-t és a választott szoftvercsatornát az eszköz megoldásához. [További információ a beágyazott C SDK-ról](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot).

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

A C Azure IoT eszközoldali SDK-t ANSI C (C99) nyelven írták, hogy maximalizálja a hordozhatóságot. Ez a funkció lehetővé teszi, hogy a kódtárak több platformon és eszközön is működjenek, különösen ha a lemez és a memória minimálisra csökkentése prioritást jelent.

Az SDK tesztelésének számos platformja van (a részletekért tekintse meg az [Azure Certified for IoT-eszköz katalógusát](https://catalog.azureiotsolutions.com/) ). Bár ez a cikk a Windows platformon futó mintakód bemutatóit tartalmazza, a cikkben ismertetett kód megegyezik a támogatott platformok tartományán.

A következő videó áttekintést nyújt a C-hez készült Azure IoT SDK-ról:

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-IoT-C-SDK-insights/Player]

Ez a cikk bemutatja a C-hez készült Azure IoT Device SDK architektúráját. Azt mutatja be, hogyan lehet inicializálni az eszköz könyvtárát, az adatok küldését IoT Hub, és üzeneteket fogadni. A cikkben szereplő információknak elegendőnek kell lenniük az SDK használatának megkezdéséhez, de a könyvtárakkal kapcsolatos további információkra mutató hivatkozásokat is biztosít.

## <a name="sdk-architecture"></a>SDK-architektúra

A C GitHub-tárházhoz [**tartozó Azure IoT ESZKÖZOLDALI SDK**](https://github.com/Azure/azure-iot-sdk-c) -t megtalálja, és megtekintheti az API részleteit a [c API-referenciában](/azure/iot-hub/iot-c-sdk-ref/).

A kódtárak legújabb verziója az adattár **fő** ágában található:

  ![A tárház fő ágának képernyőképe](./media/iot-hub-device-sdk-c-intro/RepoMasterBranch.png)

* Az SDK alapvető implementációja a **iothub \_ Client** mappában található, amely a legalacsonyabb API-réteg megvalósítását tartalmazza az SDK-ban: a **iothubclientről** könyvtár. A **iothubclientről** -függvénytár olyan API-kat tartalmaz, amelyek a nyers üzenetküldést implementálják az üzenetek küldéséhez IoT hub és az üzenetek fogadásához IoT hubról. Ennek a könyvtárnak a használatakor Ön felelős az üzenetek szerializálásának végrehajtásáért, de a IoT Hubkel folytatott kommunikáció egyéb adatai is kezelhetők.

* A **szerializáló** mappa olyan segítő függvényeket és mintákat tartalmaz, amelyek bemutatják, hogyan szerializálhatja az adatokat, mielőtt elküldené az Azure-IoT hub az ügyféloldali kódtár használatával. A szerializáló használata nem kötelező, és kényelmi szolgáltatásként van megadva. A **szerializáló** könyvtár használatához meg kell határoznia azt a modellt, amely megadja a IoT hub küldendő adatok és a tőle érkező üzenetek fogadását. A modell meghatározása után az SDK egy API-felületet biztosít, amely lehetővé teszi az eszközről a felhőbe és a felhőből az eszközre irányuló üzenetek egyszerű használatát anélkül, hogy a szerializálás részleteit kellene aggódnia. A könyvtár más nyílt forráskódú könyvtáraktól függ, amelyek olyan protokollok használatával implementálják a továbbítást, mint például a MQTT és a AMQP.

* A **iothubclientről** könyvtár más nyílt forráskódú könyvtáraktól függ:

  * Az [Azure c megosztott segédprogram](https://github.com/Azure/azure-c-shared-utility) könyvtára, amely általános funkciókat biztosít a több Azure-hoz kapcsolódó C SDK-hoz tartozó alapvető feladatokhoz (például a karakterláncok, a lista-manipuláció és az IO).

  * Az [Azure uAMQP](https://github.com/Azure/azure-uamqp-c) könyvtár, amely az erőforrás által korlátozott eszközökre optimalizált AMQP ügyféloldali implementációja.

  * Az [Azure uMQTT](https://github.com/Azure/azure-umqtt-c) könyvtára, amely egy általános célú függvénytár, amely az MQTT protokollt implementálja, és az erőforrás által korlátozott eszközökre van optimalizálva.

A kódtárak használata könnyebben értelmezhető, mint például a kód megvizsgálása. Az alábbi szakaszokban megismerheti az SDK-ban található példákat. Ebben az útmutatóban jól érzik az SDK építészeti rétegeinek különböző funkcióit és az API-k működésének bevezetését.

## <a name="before-you-run-the-samples"></a>A minták futtatása előtt

Mielőtt futtatja a mintákat a C-hez készült Azure IoT Device SDK-ban, [létre kell hoznia a IoT hub szolgáltatás egy példányát](iot-hub-create-through-portal.md) az Azure-előfizetésében. Ezután hajtsa végre a következő feladatokat:

* A fejlesztőkörnyezet előkészítése
* Eszköz hitelesítő adatainak beszerzése.

### <a name="prepare-your-development-environment"></a>A fejlesztőkörnyezet előkészítése

A csomagok a közös platformokhoz (például a NuGet for Windows apt_get vagy a Debian és az Ubuntu rendszerhez) vannak megadva, és a minták ezeket a csomagokat használják, ha vannak ilyenek. Bizonyos esetekben le kell fordítania az SDK-t a vagy az eszközön. Ha le kell fordítania az SDK-t, tekintse meg a [fejlesztési környezet előkészítése](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) a GitHub-tárházban című témakört.

A minta alkalmazás kódjának beszerzéséhez töltse le az SDK egy példányát a GitHubról. Szerezze be a forrás másolatát a [GitHub-adattár](https://github.com/Azure/azure-iot-sdk-c) **fő** ágában.


### <a name="obtain-the-device-credentials"></a>Az eszköz hitelesítő adatainak beszerzése

Most, hogy már rendelkezik a minta forráskódtal, a következő lépés az eszköz hitelesítő adatainak beolvasása. Ahhoz, hogy egy eszköz hozzáférhessen egy IoT hubhoz, előbb fel kell vennie az eszközt a IoT Hub Identity registrybe. Az eszköz hozzáadásakor megkapja az eszköz hitelesítő adatait, amelyeknek szüksége van ahhoz, hogy az eszköz csatlakozni tudjon az IoT hubhoz. A következő szakaszban tárgyalt példák a hitelesítő adatokat egy **eszköz-összekapcsolási karakterlánc** formájában számítják fel.

Számos nyílt forráskódú eszköz segíti az IoT hub kezelését.

* Egy [Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer)nevű Windows-alkalmazás.

* Egy platformfüggetlen Visual Studio Code-bővítmény az [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)néven.

* A többplatformos Python CLI [a IoT bővítmény az Azure CLI-hez](https://github.com/Azure/azure-iot-cli-extension).

Ez az oktatóanyag a grafikus *eszköz Explorer* eszközt használja. Ha a VS Code-ban fejleszt, a vs Code-hoz készült *Azure IoT-eszközöket* is használhatja. Használhatja az *Azure cli 2,0* eszközhöz készült IoT-bővítményt is, ha egy CLI-eszközt szeretne használni.

Az Eszközkezelő eszköz az Azure IoT Service librarys használatával különféle funkciókat hajt végre IoT Hubon, beleértve az eszközök hozzáadását is. Ha az Eszközkezelő eszköz használatával ad hozzá egy eszközt, az eszközhöz egy kapcsolódási karakterlánc fog megjelenni. Erre a kapcsolódási sztringre szüksége lesz a minta alkalmazások futtatásához.

Ha nem ismeri az Eszközkezelő eszközt, az alábbi eljárás azt ismerteti, hogyan lehet eszköz hozzáadására és eszköz-kapcsolódási karakterlánc beszerzésére használni.

1. Az Eszközkezelő eszköz telepítéséhez tekintse meg [a Device Explorer IoT hub eszközökhöz való használatát](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/)ismertető témakört.

1. A program futtatásakor ezt a felületet látja:

   ![Device Explorer Twin képernyőkép](./media/iot-hub-device-sdk-c-intro/DeviceExplorerTwinConfigTab.png)

1. Adja meg az **IoT hub a kapcsolatok karakterláncát** az első mezőben, majd kattintson a **frissítés** gombra. Ez a lépés úgy konfigurálja az eszközt, hogy az képes legyen kommunikálni IoT Hubokkal. 

A **kapcsolati karakterlánc** a **IoT hub Service**  >  **Settings**  >  **Shared Access Policy**  >  **iothubowner** alatt található.

1. Ha a IoT Hub kapcsolódási karakterlánc konfigurálva van, kattintson a **kezelés** fülre:

   ![Device Explorer Twin/Management képernyőkép](./media/iot-hub-device-sdk-c-intro/DeviceExplorerTwinManagementTab.png)

Ezen a lapon kezelheti az IoT hub-ban regisztrált eszközöket.

1. Hozzon létre egy eszközt a **Létrehozás** gombra kattintva. Megjelenik egy párbeszédpanel előre megadott kulcsokkal (elsődleges és másodlagos). Adja meg az **eszköz azonosítóját** , majd kattintson a **Létrehozás** gombra.

   ![Eszköz létrehozása képernyőfelvétel](./media/iot-hub-device-sdk-c-intro/CreateDevice.png)

1. Az eszköz létrehozásakor az eszközök felsorolja az összes regisztrált eszközzel, beleértve az imént létrehozott eszközt. Ha a jobb gombbal az új eszközre kattint, a következő menü jelenik meg:

   ![Device Explorer kattintson a jobb gombbal az eredmény elemre.](./media/iot-hub-device-sdk-c-intro/DeviceExplorerTwinManagementTab_RightClick.png)

1. Ha a **kiválasztott eszközhöz a kapcsolódási karakterlánc másolása** lehetőséget választja, a rendszer az eszköz kapcsolódási karakterláncát másolja a vágólapra. Őrizze meg az eszköz-kapcsolatok karakterláncának másolatát. A következő szakaszokban ismertetett minta alkalmazások futtatásakor szüksége lesz rá.

A fenti lépések elvégzése után már készen áll egy kód futtatására. A legtöbb minta állandó a fő forrásfájl tetején, amely lehetővé teszi a kapcsolati karakterlánc megadását. Például a **iothub_client \_ minták \_ iothub_convenience_sample** alkalmazás megfelelő sora a következőképpen jelenik meg.

```c
static const char* connectionString = "[device connection string]";
```

## <a name="use-the-iothubclient-library"></a>A Iothubclientről-könyvtár használata

A **iothub \_ Client** mappában az [Azure-IOT-SDK-c](https://github.com/azure/azure-iot-sdk-c) tárházban található egy **minta** nevű mappa, amely egy **iothub- \_ ügyfél \_ minta \_ mqtt** nevű alkalmazást tartalmaz.

A **iothub_client \_ minta \_ iothub_convenience_sample** alkalmazás Windows-verziója a következő Visual Studio-megoldást tartalmazza:

  ![Visual Studio Megoldáskezelő](./media/iot-hub-device-sdk-c-intro/iothub-client-sample-mqtt.png)

> [!NOTE]
> Ha a Visual Studio kéri a projekt legutóbbi verzióra való átcélzását, fogadja el a kérést.

Ez a megoldás egyetlen projektet tartalmaz. Ebben a megoldásban négy NuGet-csomag van telepítve:

* Microsoft. Azure. C. SharedUtility
* Microsoft. Azure. IoTHub. MqttTransport
* Microsoft. Azure. IoTHub. Iothubclientről
* Microsoft. Azure. umqtt

Ha az SDK-val dolgozik, mindig szüksége lesz a **Microsoft. Azure. C. SharedUtility** csomagra. Ez a példa a MQTT protokollt használja, ezért a **Microsoft. Azure. umqtt** és a **Microsoft. Azure. IoTHub. MqttTransport** csomagokat kell megadnia (a AMQP és a https-vel egyenértékű csomagok vannak). Mivel a minta a **iothubclientről** könyvtárat használja, a megoldásban szerepelnie kell a **Microsoft. Azure. IoTHub. iothubclientről** csomagnak is.

A minta alkalmazás megvalósítását a **iothub_client \_ samples \_ iothub_convenience_sample** forrásfájlban találja.

A következő lépésekkel megtudhatja, hogy mi szükséges a **iothubclientről** -könyvtár használatához.

### <a name="initialize-the-library"></a>A könyvtár inicializálása

> [!NOTE]
> A kódtárak használatának megkezdése előtt előfordulhat, hogy valamilyen platform-specifikus inicializálást kell végrehajtania. Ha például Linux rendszeren szeretné használni a AMQP-t, inicializálnia kell az OpenSSL könyvtárat. A [GitHub-tárházban](https://github.com/Azure/azure-iot-sdk-c) lévő minták meghívja a segédprogram-funkció **platformjának \_ inicializálását** , amikor az ügyfél elindul, és meghívja a **platform \_ deinit** függvényt a kilépés előtt. Ezek a függvények a platform. h fejléc fájljában vannak deklarálva. Vizsgálja meg ezen függvények definícióit a [tárházban](https://github.com/Azure/azure-iot-sdk-c) a cél platformon annak megállapításához, hogy szükség van-e a platform-specifikus inicializálási kódokra az ügyfélen.

A kódtárak használatának megkezdéséhez először le kell foglalni egy IoT Hub-ügyfél leíróját:

```c
if ((iotHubClientHandle = 
  IoTHubClient_LL_CreateFromConnectionString(connectionString, MQTT_Protocol)) == NULL)
{
    (void)printf("ERROR: iotHubClientHandle is NULL!\r\n");
}
else
{
    ...
```

Átadja az Eszközkezelő eszköztől a függvénynek beszerzett eszköz-kapcsolódási karakterlánc másolatát. A használni kívánt kommunikációs protokollt is kijelöli. Ez a példa MQTT használ, de a AMQP és a HTTPS is lehetőség.

Ha érvényes **IOTHUB-ügyfél- \_ \_ leíróval** rendelkezik, megkezdheti az API-k meghívását, hogy üzeneteket küldjön és fogadjon IoT hub.

### <a name="send-messages"></a>Üzenetek küldése

A minta alkalmazás egy hurkot állít be, amely üzeneteket küld az IoT hubhoz. A következő kódrészlet:

- Létrehoz egy üzenetet.
- Tulajdonság hozzáadására az üzenethez.
- Üzenet küldése.

Először hozzon létre egy üzenetet:

```c
size_t iterator = 0;
do
{
    if (iterator < MESSAGE_COUNT)
    {
        sprintf_s(msgText, sizeof(msgText), "{\"deviceId\":\"myFirstDevice\",\"windSpeed\":%.2f}", avgWindSpeed + (rand() % 4 + 2));
        if ((messages[iterator].messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText))) == NULL)
        {
            (void)printf("ERROR: iotHubMessageHandle is NULL!\r\n");
        }
        else
        {
            messages[iterator].messageTrackingId = iterator;
            MAP_HANDLE propMap = IoTHubMessage_Properties(messages[iterator].messageHandle);
            (void)sprintf_s(propText, sizeof(propText), "PropMsg_%zu", iterator);
            if (Map_AddOrUpdate(propMap, "PropName", propText) != MAP_OK)
            {
                (void)printf("ERROR: Map_AddOrUpdate Failed!\r\n");
            }

            if (IoTHubClient_LL_SendEventAsync(iotHubClientHandle, messages[iterator].messageHandle, SendConfirmationCallback, &messages[iterator]) != IOTHUB_CLIENT_OK)
            {
                (void)printf("ERROR: IoTHubClient_LL_SendEventAsync..........FAILED!\r\n");
            }
            else
            {
                (void)printf("IoTHubClient_LL_SendEventAsync accepted message [%d] for transmission to IoT Hub.\r\n", (int)iterator);
            }
        }
    }
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(1);

    iterator++;
} while (g_continueRunning);
```

Minden alkalommal, amikor üzenetet küld, meg kell adnia egy, az adat elküldésekor meghívott visszahívás-függvényre mutató hivatkozást. Ebben a példában a visszahívási függvény neve **SendConfirmationCallback**. A következő kódrészlet ezt a visszahívási függvényt jeleníti meg:

```c
static void SendConfirmationCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback)
{
    EVENT_INSTANCE* eventInstance = (EVENT_INSTANCE*)userContextCallback;
    (void)printf("Confirmation[%d] received for message tracking id = %zu with result = %s\r\n", callbackCounter, eventInstance->messageTrackingId, MU_ENUM_TO_STRING(IOTHUB_CLIENT_CONFIRMATION_RESULT, result));
    /* Some device specific action code goes here... */
    callbackCounter++;
    IoTHubMessage_Destroy(eventInstance->messageHandle);
}
```

Figyelje meg, hogy a **IoTHubMessage \_ Destroy** függvény hívása az üzenettel együtt történik. Ez a függvény felszabadítja az üzenet létrehozásakor lefoglalt erőforrásokat.

### <a name="receive-messages"></a>Üzenetek fogadása

Az üzenet fogadása aszinkron művelet. Először regisztrálja a visszahívást, hogy meghívja azt, amikor az eszköz üzenetet kap:

```c
if (IoTHubClient_LL_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext) != IOTHUB_CLIENT_OK)
{
    (void)printf("ERROR: IoTHubClient_LL_SetMessageCallback..........FAILED!\r\n");
}
else
{
    (void)printf("IoTHubClient_LL_SetMessageCallback...successful.\r\n");
    ...
```

Az utolsó paraméter egy érvénytelen mutató, amit szeretne. A mintában ez a mutató egy egész számra mutat, de a mutató egy összetettebb adatstruktúrára mutat. Ez a paraméter lehetővé teszi, hogy a visszahívási függvény megosztott állapotban működjön a függvény hívója által.

Amikor az eszköz üzenetet kap, a rendszer meghívja a regisztrált visszahívási függvényt. Ez a visszahívási függvény a következőt kéri le:

* Az üzenet AZONOSÍTÓjának és korrelációs azonosítójának azonosítója.
* Az üzenet tartalma.
* Az üzenet bármely egyéni tulajdonsága.

```c
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    int* counter = (int*)userContextCallback;
    const char* buffer;
    size_t size;
    MAP_HANDLE mapProperties;
    const char* messageId;
    const char* correlationId;

    // Message properties
    if ((messageId = IoTHubMessage_GetMessageId(message)) == NULL)
    {
        messageId = "<null>";
    }

    if ((correlationId = IoTHubMessage_GetCorrelationId(message)) == NULL)
    {
        correlationId = "<null>";
    }

    // Message content
    if (IoTHubMessage_GetByteArray(message, (const unsigned char**)&buffer, &size) != IOTHUB_MESSAGE_OK)
    {
        (void)printf("unable to retrieve the message data\r\n");
    }
    else
    {
        (void)printf("Received Message [%d]\r\n Message ID: %s\r\n Correlation ID: %s\r\n Data: <<<%.*s>>> & Size=%d\r\n", *counter, messageId, correlationId, (int)size, buffer, (int)size);
        // If we receive the work 'quit' then we stop running
        if (size == (strlen("quit") * sizeof(char)) && memcmp(buffer, "quit", size) == 0)
        {
            g_continueRunning = false;
        }
    }

    // Retrieve properties from the message
    mapProperties = IoTHubMessage_Properties(message);
    if (mapProperties != NULL)
    {
        const char*const* keys;
        const char*const* values;
        size_t propertyCount = 0;
        if (Map_GetInternals(mapProperties, &keys, &values, &propertyCount) == MAP_OK)
        {
            if (propertyCount > 0)
            {
                size_t index;

                printf(" Message Properties:\r\n");
                for (index = 0; index < propertyCount; index++)
                {
                    (void)printf("\tKey: %s Value: %s\r\n", keys[index], values[index]);
                }
                (void)printf("\r\n");
            }
        }
    }

    /* Some device specific action code goes here... */
    (*counter)++;
    return IOTHUBMESSAGE_ACCEPTED;
}
```

Az üzenet beolvasásához használja a **IoTHubMessage \_ GetByteArray** függvényt, amely ebben a példában egy karakterlánc.

### <a name="uninitialize-the-library"></a>A könyvtár inicializálásának megszüntetése

Ha végzett az események küldésével és az üzenetek fogadásával, a IoT-könyvtár inicializálását is elvégezheti. Ehhez adja ki a következő függvény hívását:

```c
IoTHubClient_LL_Destroy(iotHubClientHandle);
```

Ez a hívás felszabadítja a **iothubclientről \_ CreateFromConnectionString** függvény által korábban lefoglalt erőforrásokat.

Amint láthatja, egyszerűen küldhet és fogadhat üzeneteket a **iothubclientről** -könyvtárral. A könyvtár kezeli a IoT Hubokkal folytatott kommunikáció részleteit, beleértve a fejlesztő szemszögéből (a fejlesztő szempontjából ez egy egyszerű konfigurációs lehetőség).

A **iothubclientről** -függvénytár emellett precízen szabályozza az eszköz által a IoT hubba küldött adatokat. Bizonyos esetekben ez a vezérlési szint előnyt jelent, de a többinél olyan implementációs adatról van szó, amelyről nem kíván foglalkozni. Ebben az esetben érdemes lehet a **szerializáló** függvénytárat használni, amelyet a következő szakaszban ismertetünk.

## <a name="use-the-serializer-library"></a>A szerializáló kódtár használata

A **szerializáló** könyvtára elméletileg a **iothubclientről** könyvtár tetején található az SDK-ban. A **iothubclientről** könyvtárat használja az IoT hub-vel való kommunikációhoz, de olyan modellezési képességeket biztosít, amelyek megszüntetik a fejlesztőtől az üzenetek szerializálásával járó terheket. Egy példa szemlélteti, hogyan működik ez a könyvtár.

Az [Azure-IOT-SDK-c tárház](https://github.com/Azure/azure-iot-sdk-c) **szerializáló** mappájában egy **minta** mappa található, amely egy **simplesample \_ mqtt** nevű alkalmazást tartalmaz. A minta Windows-verziója a következő Visual Studio-megoldást tartalmazza:

  ![Visual Studio-megoldás a mqtt-minta számára](./media/iot-hub-device-sdk-c-intro/simplesample_mqtt.png)

> [!NOTE]
> Ha a Visual Studio kéri a projekt legutóbbi verzióra való átcélzását, fogadja el a kérést.

Akárcsak az előző mintában, ez több NuGet-csomagot is tartalmaz:

* Microsoft. Azure. C. SharedUtility
* Microsoft. Azure. IoTHub. MqttTransport
* Microsoft. Azure. IoTHub. Iothubclientről
* Microsoft. Azure. IoTHub. szerializáló
* Microsoft. Azure. umqtt

Az előző mintában a csomagok többsége látható, de a **Microsoft. Azure. IoTHub. szerializáló** új. Ez a csomag a **szerializáló** függvénytár használata esetén szükséges.

A minta alkalmazás megvalósítását a **iothub_client \_ samples \_ iothub_convenience_sample** fájlban találja.

A következő szakasz végigvezeti a minta főbb részein.

### <a name="initialize-the-library"></a>A könyvtár inicializálása

A **szerializáló** függvénytár használatának megkezdéséhez hívja meg az inicializálási API-kat:

```c
if (serializer_init(NULL) != SERIALIZER_OK)
{
    (void)printf("Failed on serializer_init\r\n");
}
else
{
    IOTHUB_CLIENT_LL_HANDLE iotHubClientHandle = IoTHubClient_LL_CreateFromConnectionString(connectionString, MQTT_Protocol);
    srand((unsigned int)time(NULL));
    int avgWindSpeed = 10;

    if (iotHubClientHandle == NULL)
    {
        (void)printf("Failed on IoTHubClient_LL_Create\r\n");
    }
    else
    {
        ContosoAnemometer* myWeather = CREATE_MODEL_INSTANCE(WeatherStation, ContosoAnemometer);
        if (myWeather == NULL)
        {
            (void)printf("Failed on CREATE_MODEL_INSTANCE\r\n");
        }
        else
        {
...
```

A **szerializáló \_ init** függvény hívása egy egyszeri hívás, amely inicializálja az alapul szolgáló könyvtárat. Ezután hívja meg az **iothubclientről \_ ll \_ CreateFromConnectionString** függvényt, amely megegyezik az **iothubclientről** -mintával megegyező API-val. Ez a hívás beállítja az eszköz kapcsolati karakterláncát (ez a hívás a használni kívánt protokollt is kiválasztja). Ez a példa az MQTT-t használja átvitelként, de a AMQP vagy a HTTPS protokollt is használhatja.

Végül hívja meg a **\_ modell létrehozása \_ példány** -függvényt. A **WeatherStation** a modell névtere, a **ContosoAnemometer** pedig a modell neve. A modell példányának létrehozása után megkezdheti az üzenetek küldését és fogadását. Fontos azonban megérteni, hogy mi a modell.

### <a name="define-the-model"></a>A modell meghatározása

A **szerializáló** függvénytár egyik modellje határozza meg, hogy az eszköz mely üzeneteket küldhet IoT hubnak és az üzeneteknek, amelyeket a modellezési nyelvben *, az* általa fogadott üzenetekben lehet elküldeni. A modelleket C-makrók használatával definiálhatja a **iothub_client \_ samples \_ iothub_convenience_sample** Sample alkalmazásban:

```c
BEGIN_NAMESPACE(WeatherStation);

DECLARE_MODEL(ContosoAnemometer,
WITH_DATA(ascii_char_ptr, DeviceId),
WITH_DATA(int, WindSpeed),
WITH_ACTION(TurnFanOn),
WITH_ACTION(TurnFanOff),
WITH_ACTION(SetAirResistance, int, Position)
);

END_NAMESPACE(WeatherStation);
```

A névtér és a **végpontok \_** **megkezdése \_** makrók a modell névterét is elvégzik argumentumként. A rendszer azt várta, hogy ezek a makrók a modell vagy modellek definícióját, valamint a modellek által használt adatstruktúrákat határozzák meg.

Ebben a példában a **ContosoAnemometer** nevű egyetlen modell létezik. Ez a modell két adatot határoz meg, amelyeket az eszköz el tud küldeni a IoT Hub: **DeviceID** és a **Szélsebesség**. Emellett három olyan műveletet (üzenetet) is meghatároz, amelyet az eszköz fogadni tud: **TurnFanOn**, **TurnFanOff** és **SetAirResistance**. Minden adatelem rendelkezik egy típussal, és mindegyik művelethez tartozik egy név (és opcionálisan egy paraméter is).

A modellben definiált adatműveletek és műveletek olyan API-felületet határoznak meg, amely segítségével üzeneteket küldhetnek a IoT Hubba, és válaszolhat az eszköznek küldött üzenetekre. A modell használatát egy példán keresztül érdemes értelmezni.

### <a name="send-messages"></a>Üzenetek küldése

A modell meghatározza a IoT Hub elküldhető adattípusokat. Ebben a példában ez azt jelenti, hogy a két adatelem egyike van definiálva a **WITH_DATA** makró használatával. A **DeviceID** és a **Szélsebesség** értékek IoT hubhoz való küldéséhez több lépés szükséges. Első lépésként adja meg az elküldeni kívánt adatbevitelt:

```c
myWeather->DeviceId = "myFirstDevice";
myWeather->WindSpeed = avgWindSpeed + (rand() % 4 + 2);
```

A korábban megadott modell lehetővé teszi az értékek megadását egy **struct**-tag tagjainak beállításával. Ezután szerializálja az elküldeni kívánt üzenetet:

```c
unsigned char* destination;
size_t destinationSize;
if (SERIALIZE(&destination, &destinationSize, myWeather->DeviceId, myWeather->WindSpeed) != CODEFIRST_OK)
{
    (void)printf("Failed to serialize\r\n");
}
else
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
    free(destination);
}
```

Ez a kód szerializálja az eszközről a felhőbe irányuló puffert (erre a **célra** hivatkozik). A kód ezután meghívja a **üzenetküldés** függvényt, hogy elküldje az üzenetet a IoT hubnak:

```c
static void sendMessage(IOTHUB_CLIENT_LL_HANDLE iotHubClientHandle, const unsigned char* buffer, size_t size)
{
    static unsigned int messageTrackingId;
    IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromByteArray(buffer, size);
    if (messageHandle == NULL)
    {
        printf("unable to create a new IoTHubMessage\r\n");
    }
    else
    {
        if (IoTHubClient_LL_SendEventAsync(iotHubClientHandle, messageHandle, sendCallback, (void*)(uintptr_t)messageTrackingId) != IOTHUB_CLIENT_OK)
        {
            printf("failed to hand over the message to IoTHubClient");
        }
        else
        {
            printf("IoTHubClient accepted the message for delivery\r\n");
        }
        IoTHubMessage_Destroy(messageHandle);
    }
    messageTrackingId++;
}
```

Az **iothubclientről \_ ll \_ SendEventAsync** második – utolsó paramétere az adat sikeres elküldésekor meghívott visszahívási függvényre mutató hivatkozás. Itt látható a visszahívási függvény a mintában:

```c
void sendCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback)
{
    unsigned int messageTrackingId = (unsigned int)(uintptr_t)userContextCallback;

    (void)printf("Message Id: %u Received.\r\n", messageTrackingId);

    (void)printf("Result Call Back Called! Result is: %s \r\n", MU_ENUM_TO_STRING(IOTHUB_CLIENT_CONFIRMATION_RESULT, result));
}
```

A második paraméter a felhasználói környezet mutatója; Ugyanez a mutató lett átadva az **iothubclientről \_ ll \_ SendEventAsync**. Ebben az esetben a környezet egy egyszerű számláló, de bármit is szeretne.

Ennyi az eszközről a felhőbe irányuló üzenetek küldése. Az egyetlen dolog, ami az üzenetek fogadására marad.

### <a name="receive-messages"></a>Üzenetek fogadása

Az üzenet fogadása hasonlóan működik, mint az üzenetek **iothubclientről** -tárban való működésének módja. Először regisztrálnia kell egy üzenet-visszahívási függvényt:

```c
if (IoTHubClient_LL_SetMessageCallback(iotHubClientHandle, 
  IoTHubMessage, myWeather) != IOTHUB_CLIENT_OK)
{
    printf("unable to IoTHubClient_SetMessageCallback\r\n");
}
else
{
...
```

Ezután írja be az üzenet fogadásakor meghívott visszahívási függvényt:

```c
static IOTHUBMESSAGE_DISPOSITION_RESULT IoTHubMessage(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    IOTHUBMESSAGE_DISPOSITION_RESULT result;
    const unsigned char* buffer;
    size_t size;
    if (IoTHubMessage_GetByteArray(message, &buffer, &size) != IOTHUB_MESSAGE_OK)
    {
        printf("unable to IoTHubMessage_GetByteArray\r\n");
        result = IOTHUBMESSAGE_ABANDONED;
    }
    else
    {
        /*buffer is not zero terminated*/
        char* temp = malloc(size + 1);
        if (temp == NULL)
        {
            printf("failed to malloc\r\n");
            result = IOTHUBMESSAGE_ABANDONED;
        }
        else
        {
            (void)memcpy(temp, buffer, size);
            temp[size] = '\0';
            EXECUTE_COMMAND_RESULT executeCommandResult = EXECUTE_COMMAND(userContextCallback, temp);
            result =
                (executeCommandResult == EXECUTE_COMMAND_ERROR) ? IOTHUBMESSAGE_ABANDONED :
                (executeCommandResult == EXECUTE_COMMAND_SUCCESS) ? IOTHUBMESSAGE_ACCEPTED :
                IOTHUBMESSAGE_REJECTED;
            free(temp);
        }
    }
    return result;
}
```

Ez a kód nem szabványos – ez minden megoldás esetében azonos. Ez a függvény fogadja az üzenetet, és gondoskodik arról, hogyan irányítja át a megfelelő függvénynek a végrehajtás meghívásával **\_ paranccsal**. Az ebben a pontban meghívott függvény a modell műveleteinek meghatározásából függ.

Amikor megad egy műveletet a modellben, meg kell valósítania egy olyan függvényt, amely akkor lesz meghívva, amikor az eszköz megkapja a megfelelő üzenetet. Ha például a modell meghatározza ezt a műveletet:

```c
WITH_ACTION(SetAirResistance, int, Position)
```

Függvény definiálása ezzel az aláírással:

```c
EXECUTE_COMMAND_RESULT SetAirResistance(ContosoAnemometer* device, int Position)
{
    (void)device;
    (void)printf("Setting Air Resistance Position to %d.\r\n", Position);
    return EXECUTE_COMMAND_SUCCESS;
}
```

Figyelje meg, hogy a függvény neve megegyezik a modellben szereplő művelet nevével, és hogy a függvény paraméterei megegyeznek a művelethez megadott paraméterekkel. Az első paraméter mindig kötelező, és tartalmaz egy mutatót a modell példányára.

Amikor az eszköz kap egy üzenetet, amely megfelel ennek az aláírásnak, a rendszer a megfelelő függvényt hívja meg. Ezért attól függetlenül, hogy a **IoTHubMessage**-ből bele kell foglalni a szabványos kódot, az üzenetek fogadása csak a modellben definiált műveletek egyszerű függvényének definiálására vonatkozik.

### <a name="uninitialize-the-library"></a>A könyvtár inicializálásának megszüntetése

Ha végzett az adatok küldésével és az üzenetek fogadásával, a IoT-könyvtár inicializálását a következőket teheti:

```c
...
        DESTROY_MODEL_INSTANCE(myWeather);
    }
    IoTHubClient_LL_Destroy(iotHubClientHandle);
}
serializer_deinit();
```

Mindhárom függvény a korábban leírt három inicializálási függvényhez igazodik. Az API-k meghívásával biztosíthatja, hogy a korábban lefoglalt erőforrások ingyenesek legyenek.

## <a name="next-steps"></a>Következő lépések

Ez a cikk a **C-hez készült Azure IoT Device SDK** kódtárainak használatának alapjait tárgyalja. Ez elegendő információt biztosít ahhoz, hogy megértsük az SDK-t, az architektúráját és a Windows-minták használatának megkezdését. A következő cikk a [iothubclientről könyvtárával kapcsolatos további tudnivalókkal](iot-hub-device-sdk-c-iothubclient.md)folytatja az SDK leírását.

Ha többet szeretne megtudni a IoT Hub fejlesztéséről, tekintse meg az [Azure IoT SDK](iot-hub-devguide-sdks.md)-kat.

A IoT Hub képességeinek további megismeréséhez lásd:

* [Mesterséges intelligencia telepítése peremeszközökön az Azure IoT Edge szolgáltatással](../iot-edge/quickstart-linux.md)
