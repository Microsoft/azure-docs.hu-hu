---
title: Az Azure IoT Hub fájl feltöltésének ismertetése | Microsoft Docs
description: Fejlesztői útmutató – a IoT Hub fájlfeltöltés funkciójával kezelheti a fájlokat az eszközről egy Azure Storage blob-tárolóba.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/07/2018
ms.custom:
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: 3286b464051b8fea88d2797d4f82b20fe432b4b8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "90019529"
---
# <a name="upload-files-with-iot-hub"></a>Fájlok feltöltése az IoT Hubbal

A [IoT hub endpoints (végpontok](iot-hub-devguide-endpoints.md) ) című cikkben leírtaknak megfelelően egy eszköz elindíthatja a fájl feltöltését egy, az eszközre irányuló végponton (**/Devices/{deviceId}/Files**) keresztül küldött értesítés elküldésével. Ha egy eszköz értesítést küld IoT Hub arról, hogy a feltöltés befejeződött, IoT Hub küld egy fájlfeltöltés-értesítési üzenetet a **/messages/servicebound/filenotifications** szolgáltatás felé irányuló végponton keresztül.

Ahelyett, hogy az üzeneteket a IoT hubon keresztül kellene közvetíteni, IoT hub helyette egy társított Azure Storage-fiókhoz tartozó kézbesítő viselkedik. Egy eszköz olyan IoT Hub tároló jogkivonatot kér, amely az eszköz által feltölteni kívánt fájlra vonatkozik. Az eszköz az SAS URI használatával tölti fel a fájlt a tárolóba, és amikor a feltöltés befejeződött, az eszköz elküld egy értesítést a befejezésről IoT Hub. IoT Hub ellenőrzi, hogy befejeződött-e a fájlfeltöltés, majd hozzáadja a fájl feltöltése értesítési üzenetet a szolgáltatáshoz kapcsolódó fájl értesítési végpontjának.

Mielőtt feltölt egy fájlt IoT Hub egy eszközről, konfigurálnia kell a hubot [egy Azure Storage-fiók társításával](iot-hub-devguide-file-upload.md#associate-an-azure-storage-account-with-iot-hub) .

Az eszköz ezután [inicializálhat egy feltöltést](iot-hub-devguide-file-upload.md#initialize-a-file-upload) , majd [értesítheti az IoT hubot](iot-hub-devguide-file-upload.md#notify-iot-hub-of-a-completed-file-upload) a feltöltés befejeződése után. Ha egy eszköz értesíti IoT Hub arról, hogy a feltöltés befejeződött, a szolgáltatás [értesítési üzenetet](iot-hub-devguide-file-upload.md#file-upload-notifications)is létrehozhat.

[!INCLUDE [iot-hub-include-x509-ca-signed-file-upload-support-note](../../includes/iot-hub-include-x509-ca-signed-file-upload-support-note.md)]

### <a name="when-to-use"></a>A következő esetekben használja

A fájlfeltöltés használatával küldhet médiafájlokat és nagyméretű telemetria-kötegeket, amelyeket időnként csatlakoztatott eszközök vagy tömörítettek a sávszélesség megtakarítása érdekében.

Ha kétségei vannak a jelentett tulajdonságok, az eszközről a felhőbe irányuló üzenetek vagy a fájlfeltöltés között, tekintse meg az [eszközről a felhőbe irányuló kommunikációs útmutatót](iot-hub-devguide-d2c-guidance.md) .

## <a name="associate-an-azure-storage-account-with-iot-hub"></a>Azure Storage-fiók hozzárendelése IoT Hub

A fájlfeltöltés funkció használatához először össze kell kapcsolni egy Azure Storage-fiókot a IoT Hub. Ezt a feladatot a Azure Portalon vagy programozott módon, a [IoT hub erőforrás-szolgáltató REST API](/rest/api/iothub/iothubresource)-kon keresztül hajthatja végre. Miután hozzárendelt egy Azure Storage-fiókot a IoT Hubhoz, a szolgáltatás egy SAS URI-t ad vissza egy eszközre, amikor az eszköz elindít egy fájlfeltöltés-kérelmet.

A [fájlok feltöltése az eszközről a felhőbe a IoT hub](iot-hub-csharp-csharp-file-upload.md) útmutatók a feltöltési folyamat teljes áttekintését tartalmazzák. Ezek a útmutatók azt mutatják be, hogyan lehet a Azure Portal használatával társítani egy IoT hub-beli Storage-fiókot.

> [!NOTE]
> Az [Azure IoT SDK](iot-hub-devguide-sdks.md) -k automatikusan kezelik az SAS URI beolvasását, a fájl feltöltését és a befejezett feltöltés IoT hub értesítését.

## <a name="initialize-a-file-upload"></a>Fájl feltöltésének inicializálása
IoT Hub egy végponttal rendelkezik, amely a fájlok feltöltésére szolgáló SAS URI-t igényel a tárolóhoz. A fájlfeltöltés folyamatának elindításához az eszköz POST-kérést küld `{iot hub}.azure-devices.net/devices/{deviceId}/files` a következő JSON-törzsnek:

```json
{
    "blobName": "{name of the file for which a SAS URI will be generated}"
}
```

IoT Hub a következő adatok visszaadása, amelyeket az eszköz a fájl feltöltéséhez használ:

```json
{
    "correlationId": "somecorrelationid",
    "hostName": "yourstorageaccount.blob.core.windows.net",
    "containerName": "testcontainer",
    "blobName": "test-device1/image.jpg",
    "sasToken": "1234asdfSAStoken"
}
```

### <a name="deprecated-initialize-a-file-upload-with-a-get"></a>Elavult: fájlfeltöltés inicializálása GET-vel

> [!NOTE]
> Ez a szakasz az elavult funkciókat ismerteti, amelyekkel egy SAS URI-t fogadhat IoT Hubból. Használja a korábban ismertetett POST metódust.

IoT Hub két REST-végponttal támogatja a fájlok feltöltését, egyet az SAS URI-t a tároláshoz, a másikat pedig egy befejezett feltöltés IoT hub értesítéséhez. Az eszköz elindítja a fájlfeltöltés folyamatát úgy, hogy elküld egy GET parancsot a IoT hubhoz a következő címen: `{iot hub}.azure-devices.net/devices/{deviceId}/files/{filename}` . Az IoT hub a következőket adja vissza:

* A feltölteni kívánt fájlhoz tartozó SAS URI.

* A feltöltés befejeződése után használandó korrelációs azonosító.

## <a name="notify-iot-hub-of-a-completed-file-upload"></a>Egy befejezett fájlfeltöltés IoT Hub értesítése

Az eszköz feltölti a fájlt a tárolóba az Azure Storage SDK-k használatával. Ha a feltöltés befejeződött, az eszköz POST-kérést küld `{iot hub}.azure-devices.net/devices/{deviceId}/files/notifications` a következő JSON-törzsnek:

```json
{
    "correlationId": "{correlation ID received from the initial request}",
    "isSuccess": bool,
    "statusCode": XXX,
    "statusDescription": "Description of status"
}
```

A értéke `isSuccess` egy logikai érték, amely jelzi, hogy a fájl feltöltése sikeres volt-e. A a fájl a `statusCode` tárterületre való feltöltésének állapota, a pedig a következőnek felel meg: `statusDescription` `statusCode` .

## <a name="reference-topics"></a>Hivatkozási témakörök:

A következő témakörök további információkat nyújtanak a fájlok eszközökről történő feltöltéséről.

## <a name="file-upload-notifications"></a>Fájlfeltöltés értesítései

Ha egy eszköz értesítést küld IoT Hub arról, hogy a feltöltés befejeződött, IoT Hub létrehoz egy értesítési üzenetet. Ez az üzenet tartalmazza a fájl nevét és tárolási helyét.

Ahogy azt a [végpontok](iot-hub-devguide-endpoints.md)ismertetik, IoT hub a fájlfeltöltés-értesítéseket a szolgáltatással szemben álló végponton (**/messages/servicebound/fileuploadnotifications**) keresztül küldi el üzenetként. A fájlfeltöltés-értesítések fogadása megegyeznek a felhőből az eszközre irányuló üzenetek esetében, és ugyanaz az [üzenet](iot-hub-devguide-messages-c2d.md#the-cloud-to-device-message-life-cycle)életciklusa. A fájlfeltöltés értesítési végpontból beolvasott minden üzenet egy JSON-rekord, amely a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Leírás |
| --- | --- |
| EnqueuedTimeUtc |Az értesítés létrejöttét jelző időbélyeg. |
| DeviceId |A fájlt feltöltött eszköz **DeviceID** . |
| BlobUri |A feltöltött fájl URI-ja. |
| BlobName |A feltöltött fájl neve. |
| LastUpdatedTime |A fájl utolsó frissítésének időbélyegzője. |
| BlobSizeInBytes |A feltöltött fájl mérete. |

**Példa**. Ez a példa egy fájlfeltöltés értesítési üzenet törzsét jeleníti meg.

```json
{
    "deviceId":"mydevice",
    "blobUri":"https://{storage account}.blob.core.windows.net/{container name}/mydevice/myfile.jpg",
    "blobName":"mydevice/myfile.jpg",
    "lastUpdatedTime":"2016-06-01T21:22:41+00:00",
    "blobSizeInBytes":1234,
    "enqueuedTimeUtc":"2016-06-01T21:22:43.7996883Z"
}
```

## <a name="file-upload-notification-configuration-options"></a>Fájlfeltöltés-értesítés konfigurációs beállításai

Minden IoT hub a következő konfigurációs beállításokkal rendelkezik a fájlfeltöltés értesítéseihez:

| Tulajdonság | Leírás | Tartomány és alapértelmezett |
| --- | --- | --- |
| **enableFileUploadNotifications** |Azt szabályozza, hogy a fájlfeltöltés-értesítések a fájl értesítéseinek végpontján vannak-e írva. |Bool. Alapértelmezett: true. |
| **fileNotifications.ttlAsIso8601** |A fájlfeltöltés értesítéseinek alapértelmezett ÉLETTARTAMa. |ISO_8601 intervallum legfeljebb 48H (legalább 1 perc). Alapértelmezett: 1 óra. |
| **fileNotifications.lockDuration** |A fájlfeltöltés-értesítések várólistájának zárolási időtartama. |5 – 300 másodperc (legalább 5 másodperc). Alapértelmezett: 60 másodperc. |
| **fileNotifications.maxDeliveryCount** |A fájlfeltöltés értesítési várólistájának maximális kézbesítési száma. |1 – 100. Alapértelmezett: 100. |

Ezeket a tulajdonságokat az IoT hub Azure Portal, az Azure CLI vagy a PowerShell használatával állíthatja be. Ennek megismeréséhez tekintse meg a [file upload beállítása](iot-hub-configure-file-upload.md)című témakört.

## <a name="additional-reference-material"></a>További referenciaanyagok

A IoT Hub Fejlesztői útmutatóban található további témakörök a következők:

* [IoT hub végpontok](iot-hub-devguide-endpoints.md) ismertetik a különböző IoT hub-végpontokat a futásidejű és a felügyeleti műveletekhez.

* A [szabályozás és a kvóták](iot-hub-devguide-quotas-throttling.md) a IoT hub szolgáltatásra vonatkozó kvótákat és szabályozási viselkedéseket ismertetik.

* Az [Azure IoT-eszközök és-szolgáltatások SDK](iot-hub-devguide-sdks.md) -k felsorolja azokat a különböző nyelvi SDK-kat, amelyek a IoT hub használatával kommunikáló eszköz-és szolgáltatás-alkalmazások fejlesztéséhez használhatók.

* [IoT hub lekérdezési nyelv](iot-hub-devguide-query-language.md) leírja a lekérdezési nyelvet, amellyel információkat kérhet le az eszközökről, és feladatairól IoT hub.

* [IOT hub MQTT-támogatás](iot-hub-mqtt-support.md) további információkat nyújt a MQTT protokoll IoT hub támogatásáról.

## <a name="next-steps"></a>Következő lépések

Most, hogy megtanulta, hogyan tölthet fel fájlokat az eszközökről IoT Hub használatával, az alábbi IoT Hub fejlesztői útmutató témaköreiben talál további információt:

* [Az eszközök identitásának kezelése IoT Hubban](iot-hub-devguide-identity-registry.md)

* [IoT Hub-hozzáférés szabályozása](iot-hub-devguide-security.md)

* [Az állapot és a konfigurációk szinkronizálása az eszközök ikrek használatával](iot-hub-devguide-device-twins.md)

* [Közvetlen metódus meghívása egy eszközön](iot-hub-devguide-direct-methods.md)

* [Feladatok ütemezése több eszközön](iot-hub-devguide-jobs.md)

A cikkben ismertetett fogalmak némelyikének kipróbálásához tekintse meg a következő IoT Hub oktatóanyagot:

* [Fájlok feltöltése eszközökről a felhőbe IoT Hub](iot-hub-csharp-csharp-file-upload.md)