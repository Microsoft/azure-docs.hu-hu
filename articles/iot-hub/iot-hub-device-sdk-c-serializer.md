---
title: A C-szerializáló Azure IoT Device SDK-je | Microsoft Docs
description: A C Azure IoT Device SDK-ban található szerializáló függvénytárának használata az IoT hub használatával kommunikáló eszköz-alkalmazások létrehozásához.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 09/06/2016
ms.author: robinsh
ms.custom: amqp
ms.openlocfilehash: f52d1d1c5f264550076688d5e25e110de230eff4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92152238"
---
# <a name="azure-iot-device-sdk-for-c--more-about-serializer"></a>A C-hez készült Azure IoT Device SDK – további információ a szerializáló szolgáltatásról

A sorozat első cikke bemutatta az [Azure IoT Device SDK C-hez való bevezetését](iot-hub-device-sdk-c-intro.md). A következő cikk részletesebb leírást [tartalmaz a C--iothubclientről készült Azure IoT ESZKÖZOLDALI SDK-](iot-hub-device-sdk-c-iothubclient.md)ról. Ez a cikk az SDK lefedettségét fejezi ki úgy, hogy részletesebb leírást biztosít a fennmaradó összetevőről: a **szerializáló** könyvtáráról.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

A bevezető cikk azt ismerteti, hogyan használható a **szerializáló** függvénytár az események küldéséhez és az üzenetek fogadásához a IoT hubból. Ebben a cikkben részletesebben ismertetjük, hogy miként modellezhető az adatai a **szerializáló** makro nyelvével. A cikk több részletet is tartalmaz arról, hogy a könyvtár hogyan szerializálja az üzeneteket (és bizonyos esetekben hogyan vezérelheti a szerializálási viselkedést). Leírunk néhány olyan paramétert is, amelyet módosíthat, hogy meghatározza a létrehozott modellek méretét.

Végül a cikk a korábbi cikkek, például az üzenetek és a tulajdonságok kezelésére szolgáló témaköröket is felkeresi. Amint kiderül, ezek a funkciók ugyanúgy működnek, mint a **szerializálási** függvénytárat a **iothubclientről** -kódtár használatával.

A cikkben leírt összes művelet a **szerializáló** SDK-mintákon alapul. Ha követni szeretné a lépéseket, tekintse meg a **simplesample \_ amqp** és a **simplesample \_ http** -alkalmazásait a C Azure IoT Device SDK-ban.

A C GitHub-tárházhoz [**tartozó Azure IoT ESZKÖZOLDALI SDK**](https://github.com/Azure/azure-iot-sdk-c) -t megtalálja, és megtekintheti az API részleteit a [c API-referenciában](/azure/iot-hub/iot-c-sdk-ref/).

## <a name="the-modeling-language"></a>A modellezés nyelve

Ebben a sorozatban az [Azure IoT ESZKÖZOLDALI SDK for c](iot-hub-device-sdk-c-intro.md) cikk az **Azure IoT Device SDK c** modellezési nyelvre lett bevezetve a **simplesample \_ amqp** alkalmazásban bemutatott példán keresztül:

```C
BEGIN_NAMESPACE(WeatherStation);

DECLARE_MODEL(ContosoAnemometer,
WITH_DATA(ascii_char_ptr, DeviceId),
WITH_DATA(double, WindSpeed),
WITH_ACTION(TurnFanOn),
WITH_ACTION(TurnFanOff),
WITH_ACTION(SetAirResistance, int, Position)
);

END_NAMESPACE(WeatherStation);
```

Amint láthatja, a modellezési nyelv C-makrón alapul. Mindig Kezdje a definíciót a **BEGIN \_ névtérrel** , és mindig fejezze be a **végponti \_ névteret**. Gyakori a vállalat névterének elnevezése, vagy például a projekt, amelyen dolgozik.

A névtéren belül a modell definíciói. Ebben az esetben a Szélmérők egyetlen modellje van. Ismét a modell neve bármi lehet, de általában a modell neve az eszköz vagy az adattípusa, amelyet IoT Hub szeretne cserélni.  

A modellek tartalmazzák a IoT Hubba beérkező események (az *adatok*), valamint a IoT Hubtól kapott üzenetek (a *műveletek*) definícióját. Ahogy a példában látható, az események típusa és neve; a műveleteknek névvel és választható paraméterekkel kell rendelkezniük (mindegyik típussal).

Ez a minta nem mutatja be az SDK által támogatott további adattípusokat. A következőt fogjuk kimutatni.

> [!NOTE]
> A IoT Hub az eszköz által az *eseményként* küldött adatokat jelöli, míg a modellezési nyelv az *adatokat* ( **WITH_DATA** használatával meghatározott módon) jelöli. Hasonlóképpen, IoT Hub az eszközökre *üzenetként* küldött adatként hivatkozik, míg a modellezési nyelv a *műveletekre* hivatkozik (a **WITH_ACTION** használatával meghatározva). Vegye figyelembe, hogy ezek a kifejezések a jelen cikkben is felcserélhetők.
> 
> 

## <a name="supported-data-types"></a>Támogatott adattípusok

A következő adattípusok támogatottak a **szerializáló** függvénytárral létrehozott modellekben:

| Típus | Description |
| --- | --- |
| double |dupla pontosságú lebegőpontos szám |
| int |32 bites egész szám |
| float |egyszeres pontosságú lebegőpontos szám |
| hosszú |hosszú egész szám |
| int8 \_ t |8 bites egész szám |
| Int16 \_ t |16 bites egész szám |
| \_t t |32 bites egész szám |
| Int64 \_ t |64 bites egész szám |
| logikai |boolean |
| ASCII \_ karakteres \_ PTR |ASCII-karakterlánc |
| EDM \_ dátum és \_ idő \_ eltolása |dátum és idő eltolása |
| EDM \_ GUID |GUID |
| EDM \_ bináris fájl |binary |
| STRUCT DEKLARÁLása \_ |Összetett adattípus |

Kezdjük a legutóbbi adattípussal. A **Declare \_ STRUCT** segítségével összetett adattípusokat határozhat meg, amelyek a többi primitív típus csoportjai. Ezek a csoportok lehetővé teszik, hogy Definiáljon egy modellt, amely a következőképpen néz ki:

```C
DECLARE_STRUCT(TestType,
double, aDouble,
int, aInt,
float, aFloat,
long, aLong,
int8_t, aInt8,
uint8_t, auInt8,
int16_t, aInt16,
int32_t, aInt32,
int64_t, aInt64,
bool, aBool,
ascii_char_ptr, aAsciiCharPtr,
EDM_DATE_TIME_OFFSET, aDateTimeOffset,
EDM_GUID, aGuid,
EDM_BINARY, aBinary
);

DECLARE_MODEL(TestModel,
WITH_DATA(TestType, Test)
);
```

A modell egyetlen **TestType** típusú adateseményt tartalmaz. A **TestType** egy összetett típus, amely több tagot tartalmaz, amelyek együttesen mutatják be a **szerializáló** modellezési nyelv által támogatott primitív típusokat.

Ehhez hasonlóan a kód is írható, amely az alábbi módon megjelenő IoT Hub küldi el az adatküldést:

```C
TestModel* testModel = CREATE_MODEL_INSTANCE(MyThermostat, TestModel);

testModel->Test.aDouble = 1.1;
testModel->Test.aInt = 2;
testModel->Test.aFloat = 3.0f;
testModel->Test.aLong = 4;
testModel->Test.aInt8 = 5;
testModel->Test.auInt8 = 6;
testModel->Test.aInt16 = 7;
testModel->Test.aInt32 = 8;
testModel->Test.aInt64 = 9;
testModel->Test.aBool = true;
testModel->Test.aAsciiCharPtr = "ascii string 1";

time_t now;
time(&now);
testModel->Test.aDateTimeOffset = GetDateTimeOffset(now);

EDM_GUID guid = { { 0x00, 0x01, 0x02, 0x03, 0x04, 0x05, 0x06, 0x07, 0x08, 0x09, 0x0A, 0x0B, 0x0C, 0x0D, 0x0E, 0x0F } };
testModel->Test.aGuid = guid;

unsigned char binaryArray[3] = { 0x01, 0x02, 0x03 };
EDM_BINARY binaryData = { sizeof(binaryArray), &binaryArray };
testModel->Test.aBinary = binaryData;

SendAsync(iotHubClientHandle, (const void*)&(testModel->Test));
```

Alapvetően egy értéket rendelünk a **tesztelési** struktúra minden tagjához, majd meghívja a **SendAsync** -t, hogy elküldje az adat- **ellenőrzési** eseményt a felhőbe. A **SendAsync** egy segítő függvény, amely egyetlen adat-eseményt küld IoT hubba:

```C
void SendAsync(IOTHUB_CLIENT_LL_HANDLE iotHubClientHandle, const void *dataEvent)
{
    unsigned char* destination;
    size_t destinationSize;
    if (SERIALIZE(&destination, &destinationSize, *(const unsigned char*)dataEvent) ==
    {
        // null terminate the string
        char* destinationAsString = (char*)malloc(destinationSize + 1);
        if (destinationAsString != NULL)
        {
            memcpy(destinationAsString, destination, destinationSize);
            destinationAsString[destinationSize] = '\0';
            IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromString(destinationAsString);
            if (messageHandle != NULL)
            {
                IoTHubClient_SendEventAsync(iotHubClientHandle, messageHandle, sendCallback, (void*)0);

                IoTHubMessage_Destroy(messageHandle);
            }
            free(destinationAsString);
        }
        free(destination);
    }
}
```

Ez a függvény szerializálja a megadott adat-eseményt, és elküldi IoT Hub **iothubclientről \_ SendEventAsync** használatával. Ez ugyanaz a kód, amelyet a korábbi cikkek is tárgyalnak (a **SendAsync** egy kényelmes függvénybe ágyazza be a logikát).

Az előző kódban használt egy másik segítő függvény **GetDateTimeOffset**. Ez a függvény a megadott időt a **EDM \_ dátum és \_ idő \_ eltolása** típusú értékre alakítja át:

```C
EDM_DATE_TIME_OFFSET GetDateTimeOffset(time_t time)
{
    struct tm newTime;
    gmtime_s(&newTime, &time);
    EDM_DATE_TIME_OFFSET dateTimeOffset;
    dateTimeOffset.dateTime = newTime;
    dateTimeOffset.fractionalSecond = 0;
    dateTimeOffset.hasFractionalSecond = 0;
    dateTimeOffset.hasTimeZone = 0;
    dateTimeOffset.timeZoneHour = 0;
    dateTimeOffset.timeZoneMinute = 0;
    return dateTimeOffset;
}
```

Ha ezt a kódot futtatja, a rendszer a következő üzenetet küldi a IoT Hubnak:

```C
{"aDouble":1.100000000000000, "aInt":2, "aFloat":3.000000, "aLong":4, "aInt8":5, "auInt8":6, "aInt16":7, "aInt32":8, "aInt64":9, "aBool":true, "aAsciiCharPtr":"ascii string 1", "aDateTimeOffset":"2015-09-14T21:18:21Z", "aGuid":"00010203-0405-0607-0809-0A0B0C0D0E0F", "aBinary":"AQID"}
```

Vegye figyelembe, hogy a szerializálás a JSON-ban van, amely a **szerializáló** könyvtár által generált formátum. Azt is vegye figyelembe, hogy a szerializált JSON-objektum minden tagja megfelel a modellben definiált **TestType** tagjainak. Az értékek pontosan egyeznek a kódban használt értékekkel is. Vegye figyelembe azonban, hogy a bináris adatfájl Base64 kódolású: a "AQID" a (z) {0x01, protokollszáma 0x02, 0x03} Base64 kódolása.

Ez a példa a **szerializáló** függvénytár használatának előnyeit mutatja be. lehetővé teszi, hogy JSON-t küldjön a felhőbe anélkül, hogy explicit módon kellene foglalkoznia az alkalmazás szerializálásával. Mindössze annyit kell aggódnia, hogy a modellben szereplő adatesemények értékeit beállítja, majd egyszerű API-kat hív meg, hogy ezeket az eseményeket a felhőbe küldje.

Ezen információk segítségével olyan modelleket adhat meg, amelyek tartalmazzák a támogatott adattípusok körét, beleértve a komplex típusokat is (a komplex típusokat más összetett típusokon belül is lehet megadni). A fenti példa által generált szerializált JSON azonban egy fontos pontot hoz létre. Az adatküldés a **szerializáló** könyvtárából pontosan meghatározza, hogy a *JSON hogyan legyen* kialakítva. Erre a pontra a következőt fogjuk lefedni.

## <a name="more-about-serialization"></a>További információ a szerializálásról

Az előző szakasz a **szerializáló** könyvtára által generált kimenetre mutat példát. Ebben a szakaszban bemutatjuk, hogyan szerializálja a függvénytár az adatszerializálást, és hogyan vezérelheti a viselkedést a szerializálási API-k használatával.

A szerializálással kapcsolatos megbeszélések elvégzéséhez egy termosztáton alapuló új modellel fogunk dolgozni. Először is lássunk némi hátteret arra a forgatókönyvre, amellyel foglalkozni próbálunk.

Olyan termosztátot szeretnénk modellezni, amely a hőmérsékletet és a páratartalmat méri. Az egyes adatmennyiségeket a rendszer másként fogja elküldeni IoT Hub. Alapértelmezés szerint a termosztát 2 percenként egyszer ingresses egy hőmérsékleti eseményt; a páratartalom-eseményt 15 percenként ingressed. Ha bármelyik esemény ingressed van, tartalmaznia kell egy időbélyeget, amely azt mutatja, hogy a megfelelő hőmérséklet vagy páratartalom milyen időpontig volt mérhető.

Ebben a forgatókönyvben két különböző módon mutatjuk be az adatokat, és leírjuk, hogy a modellezés milyen hatással van a szerializált kimenetre.

### <a name="model-1"></a>1. modell

Itt látható az előző forgatókönyvet támogató modell első verziója:

```C
BEGIN_NAMESPACE(Contoso);

DECLARE_STRUCT(TemperatureEvent,
int, Temperature,
EDM_DATE_TIME_OFFSET, Time);

DECLARE_STRUCT(HumidityEvent,
int, Humidity,
EDM_DATE_TIME_OFFSET, Time);

DECLARE_MODEL(Thermostat,
WITH_DATA(TemperatureEvent, Temperature),
WITH_DATA(HumidityEvent, Humidity)
);

END_NAMESPACE(Contoso);
```

Vegye figyelembe, hogy a modell két adateseményt tartalmaz: a **hőmérsékletet** és a **páratartalmat**. Az előző példáktól eltérően az egyes események típusa a **Declare \_ STRUCT** használatával meghatározott struktúra. A **TemperatureEvent** tartalmaz egy hőmérséklet-mérést és egy időbélyeget; A **HumidityEvent** tartalmaz egy páratartalom-mérést és egy időbélyeget. Ez a modell természetes módszert kínál a fent ismertetett forgatókönyvhöz tartozó információk modellezésére. Amikor egy eseményt küld a felhőbe, a rendszer elküld egy hőmérsékletet/időbélyeget, vagy egy páratartalom/timestamp párt.

Egy hőmérséklet-eseményt a felhőhöz a következő kód használatával küldhetünk:

```C
time_t now;
time(&now);
thermostat->Temperature.Temperature = 75;
thermostat->Temperature.Time = GetDateTimeOffset(now);

unsigned char* destination;
size_t destinationSize;
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

A kódban a hőmérséklet és a páratartalom rögzített értékeit fogjuk használni, de képzeljük el, hogy ezeket az értékeket ténylegesen lekéri a termosztát megfelelő érzékelők mintavételezésével.

A fenti kód a korábban bevezetett **GetDateTimeOffset** segítőt használja. A később törlésre kerülő okok miatt ez a kód explicit módon elválasztja az esemény szerializálásának és küldésének feladatát. Az előző kód szerializálja a hőmérsékleti eseményt egy pufferbe. Ezután a **üzenetküldés** egy segítő függvény (tartalmazza a **simplesample \_ amqp**), amely elküldi az eseményt az IoT hubnak:

```C
static void sendMessage(IOTHUB_CLIENT_HANDLE iotHubClientHandle, const unsigned char* buffer, size_t size)
{
    static unsigned int messageTrackingId;
    IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromByteArray(buffer, size);
    if (messageHandle != NULL)
    {
        IoTHubClient_SendEventAsync(iotHubClientHandle, messageHandle, sendCallback, (void*)(uintptr_t)messageTrackingId);

        IoTHubMessage_Destroy(messageHandle);
    }
    free((void*)buffer);
}
```

Ez a kód az előző szakaszban ismertetett **SendAsync** segítő részhalmaza, így itt nem kerül át újra.

Ha az előző kódot futtatjuk a hőmérsékleti esemény elküldéséhez, az esemény ezen szerializált formáját IoT Hub küldi a rendszer:

```C
{"Temperature":75, "Time":"2015-09-17T18:45:56Z"}
```

**TemperatureEvent** típusú hőmérsékletet küldünk **, és a** struct **hőmérséklet** -és időtagot tartalmaz. Ez közvetlenül a szerializált adatként jelenik meg.

Hasonlóképpen a következő kóddal is küldhetünk páratartalom-eseményt:

```C
thermostat->Humidity.Humidity = 45;
thermostat->Humidity.Time = GetDateTimeOffset(now);
if (SERIALIZE(&destination, &destinationSize, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

A IoT Hub eljuttatott szerializált űrlap a következőképpen jelenik meg:

```C
{"Humidity":45, "Time":"2015-09-17T18:45:56Z"}
```

Ez a várt érték.

Ennek a modellnek a segítségével elképzelheti, hogy a további események hogyan vehetők fel egyszerűen. A **Declare \_ STRUCT** használatával több struktúrát határozhat meg, és a modellben a megfelelő eseményt is felhasználhatja az **\_ adataival**.

Most módosítsuk a modellt úgy, hogy az ugyanazokat az adatszerkezetet tartalmazza, de egy másik struktúrával.

### <a name="model-2"></a>2. modell

Tekintse át ezt az alternatív modellt a fentiek közül:

```C
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

Ebben az esetben megtettük a bejelentési **\_ struktúra** makróit, és egyszerűen definiáljuk a forgatókönyvben szereplő adatelemeket a modellezési nyelvből származó egyszerű típusok használatával.

Ebben az esetben hagyja figyelmen kívül az esemény **időpontját** . Ettől eltekintve a következő kód kerül be a bejövő **hőmérsékletre**:

```C
time_t now;
time(&now);
thermostat->Temperature = 75;

unsigned char* destination;
size_t destinationSize;
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Ez a kód a következő szerializált eseményt küldi a IoT Hubnak:

```C
{"Temperature":75}
```

A páratartalom esemény küldésére szolgáló kód a következőképpen jelenik meg:

```C
thermostat->Humidity = 45;
if (SERIALIZE(&destination, &destinationSize, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Ez a kód a következő IoT Hub küldi el:

```C
{"Humidity":45}
```

Eddig még nincsenek meglepetések. Most nézzük meg, hogyan használjuk a SZERIALIZÁLÁSi makrót.

A **szerializáló** makró argumentumként több adateseményt is igénybe vehet. Ez lehetővé teszi, hogy a **hőmérséklet** -és **páratartalom** -eseményt összegyűjtse, és egyetlen hívással küldje azokat IoT hubba:

```C
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Előfordulhat, hogy a kód eredménye az, hogy két adatesemény érkezik a IoT Hubba:

[{"Hőmérséklet": 75}, {"páratartalom": 45}]

Más szóval várható, hogy ez a kód a **hőmérséklet** és a **páratartalom** külön történő küldésével egyezik meg. Ez csak egy kényelmi, hogy mindkét eseményt **szerializálja** ugyanabban a hívásban. Azonban ez nem így van. Ehelyett a fenti kód elküldi ezt az egyetlen adateseményt a IoT Hubnak:

{"Hőmérséklet": 75, "páratartalom": 45}

Ez furcsának tűnhet, mert a modell a **hőmérsékletet** és a **páratartalmat** két *külön* eseményként határozza meg:

```C
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

Még ennél is többet nem modellezünk ezek az események, ahol a **hőmérséklet** és a **páratartalom** ugyanabban a struktúrában van:

```C
DECLARE_STRUCT(TemperatureAndHumidityEvent,
int, Temperature,
int, Humidity,
);

DECLARE_MODEL(Thermostat,
WITH_DATA(TemperatureAndHumidityEvent, TemperatureAndHumidity),
);
```

Ha ezt a modellt használtuk, könnyebb lenne megérteni, hogy a **hőmérséklet** és a **páratartalom** hogyan legyen elküldve ugyanabban a szerializált üzenetben. Azonban előfordulhat, hogy nem egyértelmű, hogy miért működik úgy, hogy mindkét adateseményt a 2. modell használatával **szerializálja** .

Ez a viselkedés könnyebben érthető, ha ismeri a **szerializáló** függvénytárának feltételezéseit. Ennek érdekében térjünk vissza a modellbe:

```C
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

Gondoljon erre a modellre az objektumorientált kifejezésekben. Ebben az esetben egy fizikai eszközt (termosztátot) modellezünk, és az eszköz olyan attribútumokat tartalmaz, mint például a **hőmérséklet** és a **páratartalom**.

A modell teljes állapotát a következő kóddal küldheti el:

```C
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature, thermostat->Humidity, thermostat->Time) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

A hőmérséklet, a páratartalom és az idő értékének megadásával feltételezhető, hogy az ehhez hasonló eseményt IoT Hub:

```C
{"Temperature":75, "Humidity":45, "Time":"2015-09-17T18:45:56Z"}
```

Időnként előfordulhat, hogy csak a modell *bizonyos* tulajdonságait szeretné elküldeni a felhőbe (Ez különösen akkor igaz, ha a modell nagy számú adateseményt tartalmaz). Az adatesemények csak egy részhalmazát lehet elküldeni, például a korábbi példában:

```C
{"Temperature":75, "Time":"2015-09-17T18:45:56Z"}
```

Ez pontosan ugyanazt a szerializált eseményt hozza létre, mintha egy **hőmérséklet** **-és** időtaggal **TemperatureEvent** meg, ugyanúgy, mint az 1. modellel. Ebben az esetben képesek vagyunk pontosan ugyanazt a szerializált eseményt egy másik modell (2. modell) használatával kiállítani, mert a **szerializálást** más módon hívjuk.

A lényeg az, hogy ha több adateseményt továbbít a **szerializáláshoz,** azt feltételezi, hogy minden esemény egy JSON-objektum egyik tulajdonsága.

A legjobb megoldás attól függ, hogy mit gondol a modellre. Ha az "események" kifejezést a felhőbe küldi, és minden esemény a tulajdonságok meghatározott készletét tartalmazza, akkor az első megközelítés sok értelmet jelent. Ebben az esetben a **Declare \_ STRUCT** használatával határozhatja meg az egyes események szerkezetét, majd belefoglalhatja őket a modellbe **a \_ with adat** makróval. Ezután elküldheti az egyes eseményeket, ahogy a fenti első példában. Ebben a megközelítésben csak egyetlen adateseményt kell átadnia a **szerializáló** számára.

Ha úgy gondolja, hogy a modell egy objektumorientált módon van kialakítva, akkor a második megközelítés is megfelel Önnek. Ebben az esetben az **\_ adatokat** használó elemek az objektum "tulajdonságok". Az események azon részhalmazát adja át, amely a kívánt módon **szerializálható** , attól függően, hogy mekkora az "objektum" állapota a felhőbe való küldéshez.

Az Nether megközelítés helyes vagy helytelen. Vegye figyelembe, hogyan működik a **szerializáló** függvénytár, és válassza ki az igényeinek leginkább megfelelő modellezési megközelítést.

## <a name="message-handling"></a>Üzenetkezelés

Eddig ez a cikk csak az események küldését tárgyalja IoT Hubre, és nem kezelte az üzeneteket. Ennek az az oka, hogy az üzenetek fogadásával kapcsolatos tudnivalók széles körben szerepelnek a [C Azure IoT ESZKÖZOLDALI SDK](iot-hub-device-sdk-c-intro.md)-val foglalkozó cikkében. Az üzenet visszahívási függvényének regisztrálásával visszahívhatja az üzeneteket az üzenetek feldolgozásához:

```C
IoTHubClient_SetMessageCallback(iotHubClientHandle, IoTHubMessage, myWeather)
```

Ezután írja be az üzenet fogadásakor meghívott visszahívási függvényt:

```C
static IOTHUBMESSAGE_DISPOSITION_RESULT IoTHubMessage(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    IOTHUBMESSAGE_DISPOSITION_RESULT result;
    const unsigned char* buffer;
    size_t size;
    if (IoTHubMessage_GetByteArray(message, &buffer, &size) != IOTHUB_MESSAGE_OK)
    {
        printf("unable to IoTHubMessage_GetByteArray\r\n");
        result = EXECUTE_COMMAND_ERROR;
    }
    else
    {
        /*buffer is not zero terminated*/
        char* temp = malloc(size + 1);
        if (temp == NULL)
        {
            printf("failed to malloc\r\n");
            result = EXECUTE_COMMAND_ERROR;
        }
        else
        {
            memcpy(temp, buffer, size);
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

A **IoTHubMessage** ezen implementációja meghívja az adott függvényt a modellben lévő egyes műveletekhez. Ha például a modell meghatározza ezt a műveletet:

```C
WITH_ACTION(SetAirResistance, int, Position)
```

Meg kell adnia egy függvényt az alábbi aláírással:

```C
EXECUTE_COMMAND_RESULT SetAirResistance(ContosoAnemometer* device, int Position)
{
    (void)device;
    (void)printf("Setting Air Resistance Position to %d.\r\n", Position);
    return EXECUTE_COMMAND_SUCCESS;
}
```

Ekkor a rendszer meghívja a **SetAirResistance** , amikor az üzenetet elküldi az eszköznek.

Amit még nem ismertetett, az üzenet szerializált verziója így néz ki. Más szóval, ha **SetAirResistance** -üzenetet szeretne küldeni az eszközre, mit néz ki?

Ha üzenetet küld az eszköznek, ezt az Azure IoT Service SDK-n keresztül teheti meg. Továbbra is tudnia kell, hogy milyen karakterláncot kell elküldeni egy adott művelet meghívásához. Az üzenetek küldésének általános formátuma a következőképpen jelenik meg:

```C
{"Name" : "", "Parameters" : "" }
```

A következő két tulajdonsággal rendelkező szerializált JSON-objektumot küldi: a **Name** a művelet (üzenet) neve, a **Paraméterek** pedig tartalmazzák a művelet paramétereit.

A **SetAirResistance** meghívásához például elküldheti ezt az üzenetet egy eszköznek:

```C
{"Name" : "SetAirResistance", "Parameters" : { "Position" : 5 }}
```

A művelet nevének pontosan egyeznie kell a modellben meghatározott művelettel. A paraméterek neveinek is egyezniük kell. Vegye figyelembe a kis-és nagybetűk megkülönböztetését is. A **név** és a **Paraméterek** mindig nagybetűs. Ügyeljen arra, hogy megfeleljen a modellben szereplő művelet nevének és paramétereinek. Ebben a példában a művelet neve "SetAirResistance", és nem "SetAirResistance".

A két másik művelet **TurnFanOn** és **TurnFanOff** is meghívható, ha ezeket az üzeneteket egy eszközre küldi:

```C
{"Name" : "TurnFanOn", "Parameters" : {}}
{"Name" : "TurnFanOff", "Parameters" : {}}
```

Ez a szakasz azt ismerteti, hogy az események küldése és üzenetek fogadása a **szerializáló** kódtár használatával milyen információkra van szüksége. Mielőtt továbblépne, Lássunk néhány olyan paramétert, amelyet konfigurálhat a modell méretének szabályozására.

## <a name="macro-configuration"></a>Makró konfigurálása

Ha a **szerializáló** függvénytárat használja, az SDK fontos része, hogy tisztában legyen az Azure-c-Shared-Utility Library-ben.

Ha már klónozott az Azure-IOT-SDK-c tárházat a GitHubról, és kiadta a `git submodule update --init` parancsot, akkor itt találja ezt a megosztott segédprogram-tárat:

```C
.\\c-utility
```

Ha nem klónozotta a könyvtárat, [itt](https://github.com/Azure/azure-c-shared-utility)találhatja meg.

A megosztott segédprogramok könyvtárában a következő mappa jelenik meg:

```C
azure-c-shared-utility\\macro\_utils\_h\_generator.
```

Ez a mappa egy Visual Studio-megoldást tartalmaz, amelynek neve **Macro \_ utils \_ h \_ Generator. SLN**:

  ![Képernyőkép a Visual Studio-megoldásról maco_utils_h_generator](media/iot-hub-device-sdk-c-serializer/01-macro_utils_h_generator.png)

Az ebben a megoldásban szereplő program létrehozza a **\_ utils. h** nevű fájlt. Az SDK-ban szerepel egy alapértelmezett makró \_ utils. h fájl. Ez a megoldás lehetővé teszi bizonyos paraméterek módosítását, majd a naplófájlok alapján hozza létre újra a fejlécet.

A két fontos paraméter a következő: **nArithmetic** és **nMacroParameters**, amelyek a makró utils.tt található két sorban vannak meghatározva \_ :

```C
<#int nArithmetic=1024;#>
<#int nMacroParameters=124;/*127 parameters in one macro deﬁnition in C99 in chapter 5.2.4.1 Translation limits*/#>
```

Ezek az értékek az SDK-ban található alapértelmezett paraméterek. Minden paraméter a következő jelentéssel rendelkezik:

* nMacroParameters – azt határozza meg, hogy hány paramétert használhat az egyik DEKLARÁLt \_ modellben.
* nArithmetic – a modellben engedélyezett tagok teljes számát szabályozza.

Ennek a paraméternek az az oka, hogy ezek a paraméterek a modell méretének szabályozására szolgálnak. Vegyük például a modell definícióját:

```C
DECLARE_MODEL(MyModel,
WITH_DATA(int, MyData)
);
```

Ahogy korábban említettük, a **deklarált \_ modell** csak egy C-makró. A modell és a **with \_** adatutasítás neve (még egy másik makró) a **deklarált \_ modell** paraméterei. a **nMacroParameters** határozza meg, hogy hány paraméter szerepelhet a **deklarált \_ modellben**. Gyakorlatilag ez határozza meg, hogy hány adateseményre és műveletre vonatkozó deklarációt használhat. Így az alapértelmezett 124-as korláttal az azt jelenti, hogy meghatározhat egy modellt az 60 műveletekkel és az adateseményekkel együtt. Ha megpróbálja túllépni ezt a korlátot, a következőhöz hasonló fordítási hibákat fog kapni:

  ![Képernyőkép a makró-paraméterek fordítási hibáiról](media/iot-hub-device-sdk-c-serializer/02-nMacroParametersCompilerErrors.png)

A **nArithmetic** paraméterrel többet tudhat meg a makrók nyelvének belső működéséről, mint az alkalmazás.  Meghatározza a modellben található tagok teljes számát, beleértve a **DECLARE_STRUCT** makrókat is. Ha elindítja a fordítóprogram hibáit, például a következőt, próbálja meg növelni a **nArithmetic**:

   ![Képernyőkép az aritmetikai fordítóprogram hibáiról](media/iot-hub-device-sdk-c-serializer/03-nArithmeticCompilerErrors.png)

Ha módosítani kívánja ezeket a paramétereket, módosítsa a makró \_ utils.tt-fájljának értékeit, fordítsa újra a makrót a \_ utils \_ h \_ Generator. SLN megoldással, és futtassa a lefordított programot. Ha így tesz, a \_ rendszer létrehoz és elhelyez egy új, a. \\ h nevű makró-fájlt. common \\ Inc könyvtár.

Ahhoz, hogy használni lehessen a Macro \_ utils. h új verzióját, távolítsa el a **szerializáló** NuGet-csomagot a megoldásból, és a helyére foglalja bele a **szerializáló** Visual Studio-projektet. Ez lehetővé teszi, hogy a kód a szerializáló könyvtár forráskódján legyen lefordítva. Ez magában foglalja a frissített makrót ( \_ utils. h). Ha ezt szeretné tenni a simplesample- **\_ amqp**, először távolítsa el a NuGet-csomagot a szerializáló könyvtárból a megoldásból:

   ![Képernyőkép a szerializáló könyvtár NuGet-csomagjának eltávolításáról](media/iot-hub-device-sdk-c-serializer/04-serializer-github-package.png)

Ezt követően adja hozzá a projektet a Visual Studio-megoldáshoz:

> .\\ c \\ szerializáló \\ Build \\ Windows \\ szerializáló. vcxproj
> 
> 

Ha elkészült, a megoldásnak így kell kinéznie:

   ![Képernyőkép a simplesample_amqp Visual Studio megoldásról](media/iot-hub-device-sdk-c-serializer/05-serializer-project.png)

Most, hogy lefordítja a megoldást, a frissített Macro \_ . h a bináris fájl részét képezi.

Vegye figyelembe, hogy ezek az értékek elég magasak lehetnek, és túllépik a fordítóprogram korlátait. Erre a pontra a **nMacroParameters** az a fő paraméter, amelynek az érintettnek kell lennie. A C99 specifikációja azt adja meg, hogy egy makró-definícióban legalább 127 paraméter megengedett. A Microsoft fordító pontosan követi a specifikációt (és 127), így az alapértelmezettnél nem fogja tudni megnövekedni a **nMacroParameters** . Más fordítóprogramok is engedélyezhetik ezt (például a GNU-fordító magasabb korlátot támogat).

Eddig mindent megtettünk, amit tudnia kell, hogyan írhat kódot a **szerializáló** könyvtárral. Mielőtt megkezdené, olvassa el újra a korábbi cikkekből származó témaköröket, amelyeket érdemes tudnia.

## <a name="the-lower-level-apis"></a>Az alsó szintű API-k
Az a minta alkalmazás, amelyre ez a cikk koncentrált, **simplesample \_ amqp**. Ez a példa a magasabb szintű (a nem **ll**) API-kat használja az események küldéséhez és az üzenetek fogadásához. Ha ezeket az API-kat használja, a háttérben futó szál fut, amely az események küldését és az üzenetek fogadását is gondoskodik. Azonban az alsó szintű (LL) API-k használatával megkerülheti ezt a háttérbeli szálat, és explicit módon szabályozhatja, hogy mikor küldjön eseményeket vagy fogad üzeneteket a felhőből.

Az [előző cikkben](iot-hub-device-sdk-c-iothubclient.md)leírtaknak megfelelően vannak olyan függvények, amelyek a magasabb szintű API-kat alkotják:

* Iothubclientről \_ CreateFromConnectionString
* Iothubclientről \_ SendEventAsync
* Iothubclientről \_ SetMessageCallback
* Iothubclientről- \_ megsemmisítés

Ezeket az API-kat a **simplesample \_ amqp** mutatja be.

Az alacsonyabb szintű API-k is hasonló módon vannak elfoglalva.

* Iothubclientről \_ ll \_ CreateFromConnectionString
* Iothubclientről \_ ll \_ SendEventAsync
* Iothubclientről \_ ll \_ SetMessageCallback
* Iothubclientről \_ ll \_ Destroy

Vegye figyelembe, hogy az alsó szintű API-k pontosan ugyanúgy működnek, mint az előző cikkekben. Az API-k első készletét akkor használhatja, ha azt szeretné, hogy a háttérben futó szál kezelje az eseményeket és fogadja az üzeneteket. Az API-k második készletét kell használnia, ha a IoT Hubtól érkező adatok küldésekor és fogadásakor explicit módon szeretné szabályozni a vezérlést. Az API-k egy csoportja ugyanúgy működik együtt a **szerializáló** könyvtárral.

Az alsóbb szintű API-k **szerializálási** könyvtárral való használatának példáját a **simplesample \_ http** -alkalmazásban találhatja meg.

## <a name="additional-topics"></a>További témakörök
Néhány további téma is érdemes megemlíteni a tulajdonságok kezelését, alternatív eszköz hitelesítő adatokkal és konfigurációs lehetőségekkel. Ezeket az összes témakört egy [korábbi cikkben](iot-hub-device-sdk-c-iothubclient.md)tárgyaljuk. A lényeg az, hogy ezeknek a szolgáltatásoknak az összes funkciója ugyanúgy működik, mint a **szerializáló** könyvtára, ahogyan az a **iothubclientről** -könyvtárral együtt történik. Ha például a modellből egy eseményhez szeretne tulajdonságokat csatolni, a **IoTHubMessage \_ tulajdonságokat** és a **Térkép** \_ **AddorUpdate** kell használnia, ugyanúgy, mint a korábban leírt módon:

```C
MAP_HANDLE propMap = IoTHubMessage_Properties(message.messageHandle);
sprintf_s(propText, sizeof(propText), "%d", i);
Map_AddOrUpdate(propMap, "SequenceNumber", propText);
```

Azt jelzi, hogy az esemény a **szerializáló** könyvtárból lett létrehozva, vagy manuálisan lett létrehozva a **iothubclientről** -kódtár használatával.

Az alternatív eszköz hitelesítő adatai esetében az **iothubclientről \_ ll \_ create-hoz** hasonlóan működik, mint a **iothubclientről \_ CreateFromConnectionString** a **IOTHUB- \_ ügyfél- \_ leíró** kiosztásához.

Végül, ha a **szerializáló** függvénytárat használja, a konfigurációs beállításokat az **iothubclientről \_ ll \_ SetOption** is megadhatja ugyanúgy, ahogy a **iothubclientről** -könyvtár használatakor.

A **szerializáló** függvénytár egyedi funkciója az inicializálási API-k. A könyvtár használatának megkezdése előtt hívnia kell a **szerializáló \_ init**:

```C
serializer_init(NULL);
```

Ezt csak a **iothubclientről \_ CreateFromConnectionString** meghívása előtt végezheti el.

Hasonlóképpen, amikor elkészült a könyvtárral, az utolsó hívás a **szerializáló \_ deinit**:

```C
serializer_deinit();
```

Ellenkező esetben a fent felsorolt többi funkció ugyanúgy működik a **szerializáló** könyvtárában, mint a **iothubclientről** -könyvtárban. További információt ezekről a témakörökről a sorozat [előző cikkében](iot-hub-device-sdk-c-iothubclient.md) talál.

## <a name="next-steps"></a>Következő lépések

Ez a cikk részletesen ismerteti a **C-hez készült Azure IoT Device SDK**-ban található **szerializáló** függvénytár egyedi szempontjait. A megadott információkkal tisztában kell lennie azzal, hogyan használhatók modellek az események küldéséhez és az üzenetek fogadásához IoT Hubról.

Ez a három részből álló sorozatot is megköti, hogy miként fejleszthet alkalmazásokat a **C-hez készült Azure IoT Device SDK**-val. Ennek elég információnak kell lennie ahhoz, hogy ne csak az Ön számára legyen elérhető, hanem alaposan meg kell ismernie az API-k működését. További információ: az SDK-ban nem tárgyalt néhány példa. Ellenkező esetben az [Azure IOT SDK dokumentációja](https://github.com/Azure/azure-iot-sdk-c) egy jó erőforrás a további információkhoz.

Ha többet szeretne megtudni a IoT Hub fejlesztéséről, tekintse meg az [Azure IoT SDK](iot-hub-devguide-sdks.md)-kat.

A IoT Hub képességeinek további megismeréséhez tekintse meg a [mesterséges intelligenciát használó eszközökre való telepítését Azure IoT Edge](../iot-edge/quickstart-linux.md)használatával című témakört.