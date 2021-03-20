---
title: A C-Iothubclientről készült Azure IoT-eszköz SDK-je | Microsoft Docs
description: A Iothubclientről könyvtár használata a C Azure IoT Device SDK-ban az IoT hub használatával kommunikáló eszközök létrehozásához.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 08/29/2017
ms.author: robinsh
ms.custom: amqp
ms.openlocfilehash: f33521dd9110d7ba6ee84650345b38c8c6a4950b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92149138"
---
# <a name="azure-iot-device-sdk-for-c--more-about-iothubclient"></a>A C-hez készült Azure IoT eszközoldali SDK – további információ a Iothubclientről

A [c-hez készült Azure IoT ESZKÖZOLDALI SDK](iot-hub-device-sdk-c-intro.md) a c-hez készült **Azure IoT Device SDK** bevezetését bemutató sorozat első cikke. Ez a cikk azt ismerteti, hogy az SDK-ban két építészeti réteg található. Az alapszintű a **iothubclientről** -könyvtár, amely közvetlenül kezeli a IoT Hubával folytatott kommunikációt. Létezik még a **szerializáló** könyvtára is, amely a szerializálási szolgáltatások biztosítására épül. Ebben a cikkben további részleteket biztosítunk a **iothubclientről** könyvtáráról.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Az előző cikk azt ismerteti, hogyan használható a **iothubclientről** -függvénytár az események küldésére IoT hub és üzenetek fogadására. Ez a cikk azt mutatja be, hogy *miként lehet pontosabban kezelni az* adatküldési és-fogadási műveleteket, és bemutatjuk az **alacsonyabb szintű API-kat**. Azt is bemutatjuk, hogyan lehet tulajdonságokat csatolni az eseményekhez (és az üzenetekből beolvasni őket) a **iothubclientről** -könyvtár tulajdonságok kezelési funkciói segítségével. Végezetül további magyarázatot adunk a IoT Hubtól kapott üzenetek kezelésének különböző módjairól.

A cikk több különböző témakörre következtet, beleértve az eszköz hitelesítő adatait, valamint azt, hogy miként lehet módosítani a **iothubclientről** viselkedését a konfigurációs beállításokkal.

Ezeket a témákat a **iothubclientről** SDK-minták használatával mutatjuk be. Ha követni szeretné a lépéseket, tekintse meg a **iothub \_ ügyfél- \_ minta \_ http** -és **iothub ügyfél- \_ \_ példaként szolgáló \_ Amqp** -alkalmazásokat, amelyek szerepelnek a C Azure IoT eszközoldali SDK-ban. az alábbi szakaszokban ismertetett összes Leírás a példákban látható.

A C GitHub-tárházhoz [**tartozó Azure IoT ESZKÖZOLDALI SDK**](https://github.com/Azure/azure-iot-sdk-c) -t megtalálja, és megtekintheti az API részleteit a [c API-referenciában](/azure/iot-hub/iot-c-sdk-ref/).

## <a name="the-lower-level-apis"></a>Az alsó szintű API-k

Az előző cikk a **iothubclientről** alapszintű műveletét ismertette a **iothub \_ Client \_ Sample \_ amqp** alkalmazás kontextusában. Ez például azt ismerteti, hogyan lehet inicializálni a könyvtárat a kód használatával.

```C
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

Azt is ismerteti, hogyan lehet eseményeket küldeni a függvény hívásával.

```C
IoTHubClient_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message);
```

A cikk azt is ismerteti, hogyan fogadhat üzeneteket egy visszahívási függvény regisztrálásával.

```C
int receiveContext = 0;
IoTHubClient_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext);
```

A cikk azt is megmutatta, hogyan szabadíthat fel erőforrásokat kód, például az alábbiak alapján.

```C
IoTHubClient_Destroy(iotHubClientHandle);
```

A következő API-k mindegyike rendelkezik kiegészítő funkciókkal:

* Iothubclientről \_ ll \_ CreateFromConnectionString
* Iothubclientről \_ ll \_ SendEventAsync
* Iothubclientről \_ ll \_ SetMessageCallback
* Iothubclientről \_ ll \_ Destroy

A függvények mindegyike **tartalmazza az** API-nevet. A név **ll** részét képező egyéb függvények paraméterei azonosak a nem ll-társaikkal. A függvények viselkedése azonban egy fontos módon eltér.

A **iothubclientről \_ CreateFromConnectionString** meghívásakor a mögöttes kódtárak egy új szálat hoznak létre, amely a háttérben fut. Ez a szál eseményeket küld, és a IoT Hub üzeneteket fogad. Az **ll** API-k használatakor nem jön létre ilyen szál. A háttérbeli szál létrehozása a fejlesztő kényelme. Nem kell aggódnia az események explicit módon történő elküldésével és az üzenetek fogadásával kapcsolatban IoT Hub – ez automatikusan megtörténik a háttérben. Ezzel szemben az **ll** API-k explicit módon szabályozzák a kommunikációt IoT Hubekkel, ha szüksége van rá.

Ha szeretné jobban megismerni ezt a koncepciót, tekintsük át a következő példát:

A **iothubclientről \_ SendEventAsync** meghívásakor valójában egy pufferbe helyezi az eseményt. A **iothubclientről- \_ CreateFromConnectionString** hívásakor létrehozott háttér-szál folyamatosan figyeli ezt a puffert, és a benne található összes olyan adatokat elküldi, amelyeket a IoT hub tartalmaz. Ez a háttérben történik, amikor a fő szál más munkát is végrehajt.

Hasonlóképpen, amikor a **iothubclientről \_ SetMessageCallback** használatával regisztrál egy visszahívási függvényt az üzenetekhez, arra utasítja az SDK-t, hogy a háttér-szál meghívja a visszahívási funkciót, ha üzenet érkezik, a fő száltól függetlenül.

Az **ll** API-k nem hoznak létre háttérbeli szálat. Ehelyett új API-t kell meghívni az adatok explicit módon történő küldéséhez és fogadásához IoT Hubból. Ezt a következő példában mutatjuk be.

Az SDK-ban található **iothub- \_ ügyfél- \_ minta \_ http** -alkalmazás az alsó szintű API-kat mutatja be. Ebben a példában az eseményeket a következő kóddal küldi IoT Hubra:

```C
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Message_%d_From_IoTHubClient_LL_Over_HTTP", i);
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText));

IoTHubClient_LL_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message)
```

Az első három sor hozza létre az üzenetet, az utolsó sor pedig elküldi az eseményt. A korábban említettek szerint azonban az esemény elküldése azt jelenti, hogy az adatok egyszerűen pufferbe kerülnek. A hálózatban semmi nincs továbbítva a **iothubclientről \_ ll \_ SendEventAsync** hívásakor. Ahhoz, hogy ténylegesen bekerüljön az IoT Hubba az adatforgalom, meg kell hívnia az **iothubclientről \_ ll \_ DoWork**, ahogy az alábbi példában is látható:

```C
while (1)
{
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(100);
}
```

Ez a kód (a **iothub \_ ügyfél- \_ minta \_ http** -alkalmazásból) ismételten meghívja a **iothubclientről \_ ll \_ DoWork**-t. Minden alkalommal, amikor a **iothubclientről \_ ll \_ DoWork** meghívja, néhány eseményt küld a pufferből a IoT hubba, és lekéri az eszközre küldött, várólistára helyezett üzenetet. Az utóbbi esetben azt jelenti, hogy ha egy visszahívási függvényt regisztráltunk az üzenetekhez, akkor a visszahívás meghívására kerül sor (feltéve, hogy az üzenetek várólistára kerülnek). Ezt a visszahívási függvényt a következő kóddal regisztráltuk:

```C
IoTHubClient_LL_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext)
```

Ennek az az oka, hogy az **iothubclientről \_ ll \_ DoWork** gyakran hívási ciklusban van, hogy minden alkalommal meghívásra kerül, a rendszer elküld *néhány* pufferelt eseményt IoT hub és lekéri *a következő* üzenetet az eszközre. Minden hívás nem garantált, hogy az összes pufferelt eseményt elküldjék, vagy az összes várólistára helyezett üzenetet lekéri. Ha a pufferben lévő összes eseményt el szeretné küldeni, majd más feldolgozással folytatja a műveletet, akkor a hurok a következő kóddal helyettesíthető:

```C
IOTHUB_CLIENT_STATUS status;

while ((IoTHubClient_LL_GetSendStatus(iotHubClientHandle, &status) == IOTHUB_CLIENT_OK) && (status == IOTHUB_CLIENT_SEND_STATUS_BUSY))
{
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(100);
}
```

Ez a kód meghívja az **iothubclientről \_ ll \_ DoWork** , amíg a pufferben lévő összes eseményt el nem küldték IoT hubba. Vegye figyelembe, hogy ez nem jelenti azt is, hogy az összes várólistán lévő üzenet érkezett. Ennek az az oka, hogy az összes üzenet ellenőrzése nem determinisztikus művelet. Mi történik, ha az üzenetek összes üzenetét lekéri, de ezt követően a rendszer azonnal elküldje egy másikat az eszközre? A programozott időkorláttal hatékonyabban kezelheti az adott megoldást. Az üzenet visszahívási funkciója például minden egyes híváskor visszaállíthatja az időzítőt. Ezután megírhatja a logikát a feldolgozás folytatásához, ha például nem érkezett üzenet az utolsó *X* másodpercben.

Ha végzett az események ingressing, és üzeneteket fogad, ügyeljen arra, hogy a megfelelő függvényt hívja meg az erőforrások törléséhez.

```C
IoTHubClient_LL_Destroy(iotHubClientHandle);
```

Alapvetően csak egy API-készletet küldhet és fogadhat az olyan háttérbeli szálral és más API-kkal, amelyek a háttérben futó szál nélkül ugyanazt a dolgot használják. Számos fejlesztő előnyben részesíti a nem LL API-kat, de az alacsonyabb szintű API-k akkor hasznosak, ha a fejlesztő explicit módon szeretné vezérelni a hálózati átvitelt. Például egyes eszközök időben gyűjtenek adatokat, és csak meghatározott időközönként beáramlanak az eseményekre (például egy órával vagy naponta egyszer). Az alsó szintű API-k lehetővé teszi, hogy explicit módon vezérelje az adatok küldését és fogadását IoT Hubról. Mások egyszerűen az alacsonyabb szintű API-k által biztosított egyszerűséget részesítik előnyben. Minden, ami a fő szálon történik, nem pedig a háttérben végzett munka.

Bármelyik modellt választja, ügyeljen arra, hogy a használt API-k konzisztensek legyenek. Ha a **iothubclientről \_ ll \_ CreateFromConnectionString** meghívásával indítja el, ügyeljen arra, hogy csak a megfelelő alsóbb szintű API-kat használja a követési munkához:

* Iothubclientről \_ ll \_ SendEventAsync
* Iothubclientről \_ ll \_ SetMessageCallback
* Iothubclientről \_ ll \_ Destroy
* Iothubclientről \_ ll \_ DoWork

Az ellenkezője igaz is. Ha elindítja a **iothubclientről \_ CreateFromConnectionString**, a nem ll API-kat használja bármilyen további feldolgozáshoz.

A C Azure IoT eszközoldali SDK-ban tekintse meg a **iothub \_ ügyfél- \_ minta \_ http** -alkalmazást az alsó szintű API-k teljes példáját. A **iothub \_ ügyféloldali \_ minta \_ AMQP** alkalmazás a nem ll API-k teljes példájának megfelelő hivatkozásokra hivatkozhat.

## <a name="property-handling"></a>Tulajdonság-kezelési

Ha az adatok elküldését ismertetjük, az üzenet törzsére utalunk. Vegyük például a következő kódot:

```C
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Hello World");
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText));
IoTHubClient_LL_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message)
```

Ez a példa egy üzenetet küld, amely a "Hello World" szöveggel IoT Hub. A IoT Hub azonban lehetővé teszi az egyes üzenetekhez tartozó tulajdonságok csatolását is. A tulajdonságok név/érték párok, amelyek csatolhatók az üzenethez. Például módosíthatjuk az előző kódot, hogy egy tulajdonságot csatoljon az üzenethez:

```C
MAP_HANDLE propMap = IoTHubMessage_Properties(message.messageHandle);
sprintf_s(propText, sizeof(propText), "%d", i);
Map_AddOrUpdate(propMap, "SequenceNumber", propText);
```

Kezdjük a **IoTHubMessage \_ tulajdonságainak** meghívásával és az üzenet leírójának átadásával. A mi visszakapott funkció egy **Térkép- \_ leíró** hivatkozás, amely lehetővé teszi, hogy megkezdje a tulajdonságok hozzáadását. Az utóbbit a **map \_ AddOrUpdate** meghívásával lehet elérni, amely egy Térkép \_ fogópontra, a tulajdonság nevére és a tulajdonság értékére hivatkozik. Ezzel az API-val tetszőleges számú tulajdonságot adhat hozzá.

Ha az esemény beolvasása **Event Hubsból** történik, a fogadó képes enumerálni a tulajdonságokat, és beolvasni a megfelelő értékeket. Például a .NET-ben ez a [EventData objektum Tulajdonságok gyűjteményének](/dotnet/api/microsoft.servicebus.messaging.eventdata)elérésével valósítható meg.

Az előző példában a tulajdonságokat csatoljuk egy IoT Hubnak küldött eseményhez. A tulajdonságok a IoT Hubból fogadott üzenetekhez is csatolhatók. Ha egy üzenetből szeretnénk beolvasni a tulajdonságokat, használhatunk kódot, például a következőt az üzenet visszahívási függvényében:

```C
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    . . .

    // Retrieve properties from the message
    MAP_HANDLE mapProperties = IoTHubMessage_Properties(message);
    if (mapProperties != NULL)
    {
        const char*const* keys;
        const char*const* values;
        size_t propertyCount = 0;
        if (Map_GetInternals(mapProperties, &keys, &values, &propertyCount) == MAP_OK)
        {
            if (propertyCount > 0)
            {
                printf("Message Properties:\r\n");
                for (size_t index = 0; index < propertyCount; index++)
                {
                    printf("\tKey: %s Value: %s\r\n", keys[index], values[index]);
                }
                printf("\r\n");
            }
        }
    }

    . . .
}
```

A **IoTHubMessage \_ Tulajdonságok** hívása a **Térkép \_ leírójának** hivatkozását adja vissza. Ezután továbbítjuk ezt a hivatkozást a **Térkép \_ GetInternals** , hogy a név/érték párok tömbje (valamint a tulajdonságok száma) alapján szerezzen be egy hivatkozást. Ezen a ponton egyszerű a tulajdonságok enumerálása, hogy megkapjuk a kívánt értékeket.

Az alkalmazásban nem kell tulajdonságokat használnia. Ha azonban az eseményeken be kell állítania őket, vagy az üzenetekből kell lekérnie őket, a **iothubclientről** -könyvtár megkönnyíti a konfigurálást.

## <a name="message-handling"></a>Üzenetkezelés

Amint azt korábban már említettük, amikor az üzenetek érkeznek IoT Hub a **iothubclientről** -könyvtár válaszol egy regisztrált visszahívás-függvény meghívásával. A függvény visszatérési paramétere további magyarázatot érdemel. Íme egy részlet a visszahívási függvényről a **iothub \_ ügyféloldali \_ \_ http** -minta alkalmazásban:

```C
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    . . .
    return IOTHUBMESSAGE_ACCEPTED;
}
```

Vegye figyelembe, hogy a visszatérési típus **IOTHUBMESSAGE- \_ törlési \_ eredmény** , és ebben a konkrét esetben a **IOTHUBMESSAGE \_ elfogadjuk**. Más értékek is visszatérhetnek a függvényből, amelyek megváltoztatják, hogy a **iothubclientről** -könyvtár hogyan működik az üzenet visszahívásával. Itt láthatja a lehetőségeket.

* **IOTHUBMESSAGE \_ ELFOGADva** – az üzenet feldolgozása sikeresen megtörtént. A **iothubclientről** -könyvtár nem hívja meg újra a visszahívási függvényt ugyanazzal az üzenettel.

* **IOTHUBMESSAGE \_ ELUTASÍTva** – az üzenet nem lett feldolgozva, és a későbbiekben nem kívánja elvégezni. A **iothubclientről** -függvénytár nem hívhatja újra a visszahívási függvényt ugyanazzal az üzenettel.

* **IOTHUBMESSAGE \_ Elhagyva** – az üzenet nem lett sikeresen feldolgozva, de a **iothubclientről** -függvénytárnak újra meg kell hívnia a visszahívási függvényt ugyanazzal az üzenettel.

Az első két visszatérési kód esetében a **iothubclientről** -könyvtár üzenetet küld IoT hub jelzi, hogy az üzenetet törölni kell az eszköz-várólistából, és nem kell újból elküldeni. A hálózati hatás ugyanaz (az üzenet törlődik az eszköz-várólistából), de az üzenet elfogadása vagy elutasítása továbbra is meg van rögzítve.  A különbségtétel rögzítése akkor hasznos, ha az üzenet elküldi a visszajelzést, és megállapítja, hogy az eszköz elfogadta vagy elutasította-e az adott üzenetet.

Az utolsó esetben a rendszer a IoT Hub is elküld egy üzenetet, de azt jelzi, hogy az üzenetet újra kell kézbesíteni. Ha hibát tapasztal, a rendszer általában kihagy egy üzenetet, de megpróbálja újból feldolgozni az üzenetet. Ezzel szemben az üzenet elutasítása akkor megfelelő, ha helyreállíthatatlan hibába ütközik (vagy ha úgy dönt, hogy nem szeretné feldolgozni az üzenetet).

Minden esetben vegye figyelembe a különböző visszatérési kódokat, hogy kiváltsa a kívánt viselkedést a **iothubclientről** -könyvtárból.

## <a name="alternate-device-credentials"></a>Alternatív eszköz hitelesítő adatai

Amint azt korábban említettük, a **iothubclientről** -függvénytár használatakor az első teendő a **IOTHUB- \_ ügyfél \_ leírójának** beszerzése egy hívással, például a következőkkel:

```C
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

A **iothubclientről \_ CreateFromConnectionString** argumentumai az eszköz kapcsolati karakterlánca, valamint egy paraméter, amely a IoT hub használatával folytatott kommunikációhoz használt protokollt jelzi. Az eszköz-kapcsolatok karakterláncának formátuma a következőképpen jelenik meg:

```C
HostName=IOTHUBNAME.IOTHUBSUFFIX;DeviceId=DEVICEID;SharedAccessKey=SHAREDACCESSKEY
```

Ebben a karakterláncban négy információ található: IoT Hub név, IoT Hub utótag, eszköz azonosítója és megosztott elérési kulcs. Egy IoT hub teljes tartománynevét (FQDN) szerezheti be, amikor létrehozza az IoT hub-példányt a Azure Portalban – ezzel megadja az IoT hub nevét (a teljes tartománynév első részét) és a IoT hub utótagját (a teljes TARTOMÁNYNEVEt). Az eszköz AZONOSÍTÓját és a közös elérési kulcsot akkor kapja meg, amikor regisztrálja az eszközt a IoT Hub (az [előző cikkben](iot-hub-device-sdk-c-intro.md)leírtak szerint).

**Iothubclientről \_ A CreateFromConnectionString** egy módszert biztosít a könyvtár inicializálására. Ha szeretné, létrehozhat egy új **IOTHUB- \_ ügyfelet \_** ezen egyéni paraméterekkel, az eszköz csatlakoztatási karakterlánca helyett. Ez a következő kóddal érhető el:

```C
IOTHUB_CLIENT_CONFIG iotHubClientConfig;
iotHubClientConfig.iotHubName = "";
iotHubClientConfig.deviceId = "";
iotHubClientConfig.deviceKey = "";
iotHubClientConfig.iotHubSuffix = "";
iotHubClientConfig.protocol = HTTP_Protocol;
IOTHUB_CLIENT_HANDLE iotHubClientHandle = IoTHubClient_LL_Create(&iotHubClientConfig);
```

Ez ugyanazokat a feladatokat hajtja végre, mint a **iothubclientről \_ CreateFromConnectionString**.

Nyilvánvalónak tűnhet, hogy az inicializálási módszer helyett inkább a **iothubclientről \_ CreateFromConnectionString** -t szeretné használni. Ne feledje azonban, hogy amikor regisztrál egy eszközt a IoT Hub a kapott eszköz AZONOSÍTÓját és az eszköz kulcsát (nem a kapcsolódási karakterláncot). A [korábbi cikkben](iot-hub-device-sdk-c-intro.md) bemutatott *Device Explorer* SDK-eszköz az **Azure IoT Service SDK** -ban található könyvtárakkal hozza létre az eszköz-kapcsolódási karakterláncot az eszköz azonosítója, az eszköz kulcsa és az IoT hub állomásnév alapján. Így az **iothubclientről \_ ll \_ létrehozása** is előnyösebb lehet, mivel elmenti a kapcsolatok karakterláncának létrehozásának lépését. Tetszőleges módszert használhat.

## <a name="configuration-options"></a>Beállítási lehetőségek

Eddig minden, ami a **iothubclientről** -könyvtár működésének módjáról szól, az alapértelmezett viselkedését tükrözi. Van azonban néhány olyan lehetőség, amely a könyvtár működésének megváltoztatására van beállítva. Ez az **iothubclientről \_ ll \_ SetOption** API használatával valósítható meg. Vegye fontolóra ezt a példát:

```C
unsigned int timeout = 30000;
IoTHubClient_LL_SetOption(iotHubClientHandle, "timeout", &timeout);
```

A leggyakrabban használt lehetőségek:

* **SetBatching** (bool) – Ha az **értéke igaz**, a rendszer a IoT hub elküldett adatkötegeket küldi el. Ha **hamis**, akkor az üzenetek küldése külön történik. Az alapértelmezett érték a **false**. A AMQP/AMQP-WS, valamint a D2C-üzenetekhez tartozó Rendszertulajdonságok hozzáadását is támogatja a rendszer.

* **Időtúllépés** (előjel nélküli int) – Ez az érték ezredmásodpercben jelenik meg. Ha HTTPS-kérést küld, vagy a válasz fogadása hosszabb időt vesz igénybe, akkor a kapcsolat túllépi az időkorlátot.

A kötegelt beállítás fontos. Alapértelmezés szerint a könyvtár ingresses az eseményeket (egyetlen esemény, amit a **iothubclientről \_ ll \_ SendEventAsync**-nek továbbít). Ha a kötegelt beállítás értéke **true (igaz**), a tár annyi eseményt gyűjt, amennyit a pufferből tud (az IoT hub által elfogadásra kerülő üzenetek maximális méretére).  Az Event batch egyetlen HTTPS-hívásban IoT Hub érkezik (az egyes események egy JSON-tömbbe vannak csomagolva). A kötegelt feldolgozás engedélyezése általában nagy teljesítménybeli nyereséget eredményez, mivel csökkenti a hálózati ciklikus utakat. Emellett jelentősen csökkenti a sávszélességet, mivel a HTTPS-fejlécek egyetlen készletét küldi el egy eseményvezérelt kötegtel, és nem az egyes eseményekhez tartozó fejléceket. Ha nem rendelkezik konkrét indoklással, akkor általában engedélyezni szeretné a kötegelt feldolgozást.

## <a name="next-steps"></a>Következő lépések

Ez a cikk részletesen ismerteti a **C Azure IoT Device SDK**-ban található **iothubclientről** -függvénytár viselkedését. Ezekkel az információkkal tisztában kell lennie a **iothubclientről** -könyvtár képességeivel. A sorozat második cikke a [C-szerializáló Azure IoT Device SDK-](iot-hub-device-sdk-c-serializer.md)je, amely a **szerializálási** függvénytárhoz hasonló részleteket tartalmaz.

Ha többet szeretne megtudni a IoT Hub fejlesztéséről, tekintse meg az [Azure IoT SDK](iot-hub-devguide-sdks.md)-kat.

A IoT Hub képességeinek további megismeréséhez tekintse meg a [mesterséges intelligenciát használó eszközökre való telepítését Azure IoT Edge](../iot-edge/quickstart-linux.md)használatával című témakört.