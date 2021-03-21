---
title: Az Azure IoT Hub a felhőből az eszközre irányuló üzenetkezelés ismertetése | Microsoft Docs
description: Ez a fejlesztői útmutató ismerteti, hogyan használható a felhőből az eszközre irányuló üzenetkezelés az IoT hub használatával. Az üzenet életciklusával és a konfigurációs beállításokkal kapcsolatos információkat tartalmaz.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/15/2018
ms.custom: mqtt, devx-track-azurecli
ms.openlocfilehash: 154b496a6c14d307c09ddcd1b42bf4ba568cb315
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104607891"
---
# <a name="send-cloud-to-device-messages-from-an-iot-hub"></a>Felhőből az eszközre irányuló üzenetek küldése IoT-hubhoz

Ha egyirányú értesítéseket szeretne küldeni egy eszközre a megoldás háttérből, küldjön a felhőből az eszközre irányuló üzeneteket a IoT hub-ról az eszközre. Az Azure IoT Hub által támogatott egyéb felhőből eszközre vonatkozó beállításokkal kapcsolatos további információkért lásd: a [felhőből az eszközre irányuló kommunikációs útmutató](iot-hub-devguide-c2d-guidance.md).

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

A felhőből az eszközre irányuló üzeneteket a szolgáltatással szemközti végponton ( */messages/devicebound*) keresztül küldi el. Az eszközök ezután egy adott eszközre vonatkozó végponton, */Devices/{deviceId}/messages/devicebound* keresztül kapják meg az üzeneteket.

Ha egyetlen eszközön szeretné megcélozni az egyes felhőből az eszközre irányuló üzeneteket, az IoT hub a **to** tulajdonságot */Devices/{deviceId}/messages/devicebound* értékre állítja.

Minden eszköz-várólista rendelkezik, legfeljebb 50 felhőből eszközre küldött üzenetekkel. Ha több üzenetet szeretne küldeni ugyanarra az eszközre, a rendszer hibát jelez.

## <a name="the-cloud-to-device-message-life-cycle"></a>A felhőből az eszközre irányuló üzenetek életciklusa

A legalább egyszeri kézbesítés biztosításához az IoT hub a felhőből az eszközre irányuló üzeneteket tart fenn eszközönkénti várólistán. Ahhoz, hogy az IoT-központ eltávolítsa az üzeneteket a várólistából, az eszközöknek explicit módon el kell ismerniük a *befejezést*. Ez a megközelítés biztosítja a rugalmasságot a kapcsolat és az eszközök meghibásodása ellen.

Az életciklus állapotának diagramja az alábbi ábrán látható:

![A felhőből az eszközre irányuló üzenetek életciklusa](./media/iot-hub-devguide-messages-c2d/lifecycle.png)

Amikor az IoT hub szolgáltatás üzenetet küld az eszköznek, a szolgáltatás *várólistán lévő* állítja be az üzenet állapotát. Ha egy eszköz üzenetet szeretne *kapni* , az IoT hub *zárolja* az üzenetet úgy, hogy az állapotot *láthatatlanra* állítja. Ez az állapot lehetővé teszi, hogy az eszköz más szálai is megkezdsék a többi üzenet fogadását. Ha egy eszköz szála befejezi az üzenet feldolgozását, az üzenet *elvégzésével* értesíti az IoT hubot. Az IoT hub ezután a *befejezett* állapotot állítja be.

Az eszköz A következőket is elvégezheti:

* *Utasítsa* el az üzenetet, ami azt eredményezi, hogy az IoT hub a *Kézbesítetlen betűjelű* állapotba állítja azt. A Message Queuing telemetria Transport (MQTT) protokollon keresztül csatlakozó eszközök nem tagadhatják meg a felhőből az eszközre irányuló üzeneteket.

* Az üzenet *elhagyása* , amelynek hatására az IoT hub visszahelyezi az üzenetet a várólistába, és az állapot *várólistán lévő* értékre van állítva. A MQTT protokollon keresztül csatlakozó eszközök nem tudják kihagyni a felhőből az eszközre irányuló üzeneteket.

Egy szál nem tud feldolgozni egy üzenetet az IoT hub értesítése nélkül. Ebben az esetben az üzenetek automatikusan a *láthatatlan* állapotból térnek vissza a *várólistán lévő* állapotba egy *láthatósági* időkorlát (vagy *zárolási* időkorlát) után. Az időtúllépés értéke egy perc, és nem módosítható.

Az IoT hub **Max Delivery Count** tulajdonsága határozza meg, hogy az üzenet hány alkalommal tud áttérni a *várólistán lévő* és a *láthatatlan* állapot között. Ennyi átmenet után az IoT hub az üzenet állapotát a *Kézbesítetlen levélre* állítja. Hasonlóképpen, az IoT hub egy üzenet állapotát a lejárati idő *lejárta után* kézbesíti. További információ: [time to Live](#message-expiration-time-to-live).

A [felhőből az eszközre irányuló üzenetek küldésének módja IoT hub](iot-hub-csharp-csharp-c2d.md) cikkből megtudhatja, hogyan küldhet a felhőből az eszközre irányuló üzeneteket a felhőből, és hogyan fogadhatja őket az eszközön.

Az eszköz általában a felhőből az eszközre irányuló üzenetet hajt végre, ha az üzenet elvesztése nem befolyásolja az alkalmazás logikáját. Ilyen lehet például, ha az eszköz helyileg megtartotta az üzenet tartalmát, vagy sikeresen végrehajtotta a műveletet. Az üzenet átmeneti adatokat is tartalmazhat, amelyek elvesztése nem befolyásolja az alkalmazás funkcióit. A hosszan futó feladatok esetében időnként a következőket teheti:

* Fejezze be a felhőből az eszközre irányuló üzenetet, miután az eszköz megtartotta a feladat leírását a helyi tárolóban.

* Értesítse a megoldás hátterét egy vagy több, az eszközről a felhőbe irányuló üzenettel a feladat előrehaladásának különböző szakaszaiban.

## <a name="message-expiration-time-to-live"></a>Üzenet lejárata (élettartam)

Minden felhőből eszközre küldött üzenet lejárati idővel rendelkezik. Ezt az időt a következők valamelyike állítja be:

* A szolgáltatás **ExpiryTimeUtc** tulajdonsága
* Az IoT hub az IoT hub-tulajdonságként megadott alapértelmezett *idő* használatával

Lásd: [a felhőből az eszközre vonatkozó konfigurációs beállítások](#cloud-to-device-configuration-options).

Az üzenetek lejárati idejének kihasználása, valamint az üzenetek leválasztott eszközökre való küldésének általános módja, ha a rövid *élettartamot az élő* értékek értékre állítja. Ez a megközelítés ugyanazt az eredményt éri el, mint az eszköz csatlakoztatási állapotának fenntartása, de hatékonyabb. Ha üzenet-visszaigazolást kér, az IoT hub értesíti a következő eszközökről:

* Képes üzeneteket fogadni.
* Nem online vagy sikertelen volt.

## <a name="message-feedback"></a>Üzenet visszajelzése

A felhőből az eszközre küldött üzenetek küldésekor a szolgáltatás az üzenet végleges állapotára vonatkozó üzenet-visszajelzések kézbesítését is kérheti. Ezt úgy teheti meg, hogy a **iothub-ACK** Application tulajdonságot a következő négy érték egyikére küldi a felhőből az eszközre:

| ACK tulajdonság értéke | Működés |
| ------------ | -------- |
| Nincs     | Az IoT hub nem állít elő visszajelzési üzenetet (az alapértelmezett viselkedés). |
| pozitív | Ha a felhőből az eszközre irányuló üzenet eléri a *befejezett* állapotot, a IoT hub visszajelzési üzenetet hoz létre. |
| negatív | Ha a felhőből az eszközre irányuló üzenet eléri a *Kézbesítetlen* állapotot, a IoT hub visszajelzési üzenetet hoz létre. |
| teljes     | Az IoT hub mindkét esetben létrehoz egy visszajelzési üzenetet. |

Ha a **ACK** érték *megtelt*, és nem kap visszajelzést, az azt jelenti, hogy a visszajelzési üzenet lejárt. A szolgáltatás nem tudja, mi történt az eredeti üzenettel. A gyakorlatban a szolgáltatásnak biztosítania kell, hogy a lejárata előtt fel tudja dolgozni a visszajelzést. A maximális lejárati idő két nap, így a szolgáltatás sikertelenül fog futni, ha hiba történik.

Ahogy azt a [végpontok](iot-hub-devguide-endpoints.md)ismertetik, a IoT hub visszajelzést küld a szolgáltatás felé irányuló végponton, a */messages/servicebound/feedback*, az üzenetekként. A visszajelzések fogadásának szemantikai megegyeznek a felhőből az eszközre irányuló üzenetekkel. Ha lehetséges, az üzenetek visszajelzése egyetlen üzenetbe van batch, a következő formátumban:

| Tulajdonság     | Leírás |
| ------------ | ----------- |
| EnqueuedTime | Időbélyeg, amely azt jelzi, hogy a központ mikor fogadta el a visszajelzési üzenetet |
| UserId (Felhasználóazonosító)       | `{iot hub name}` |
| ContentType  | `application/vnd.microsoft.iothub.feedback.json` |

A rendszer akkor küldi el a visszajelzést, ha a köteg eléri a 64 üzenetet, vagy az utolsó elküldötttől számított 15 másodpercben, attól függően, hogy melyik következik be először. 

A törzs a rekordok JSON-szerializált tömbje, amelyek mindegyike a következő tulajdonságokkal rendelkezik:

| Tulajdonság           | Leírás |
| ------------------ | ----------- |
| EnqueuedTimeUtc    | Egy időbélyeg, amely azt jelzi, hogy mikor történt az üzenet eredménye (például a hub megkapta a visszajelzési üzenetet, vagy lejárt az eredeti üzenet) |
| OriginalMessageId  | A *MessageID* üzenet, amelyre ez a visszajelzési információ vonatkozik |
| StatusCode         | Egy kötelező karakterlánc, amelyet az IoT hub által generált visszajelzési üzenetekben használ: <br/> *Siker* <br/> *Lejárt* <br/> *DeliveryCountExceeded* <br/> *Elutasítva* <br/> *Törlődnek* |
| Description        | *Statuscode* karakterlánc-értékei |
| DeviceId           | A felhőből az eszközre irányuló üzenet megcélzott eszközének *DeviceID* eleme, amelyre ez a visszajelzés vonatkozik |
| DeviceGenerationId | A felhőből az eszközre irányuló üzenet *DeviceGenerationId* , amelyre ez a visszajelzés vonatkozik |

Ahhoz, hogy a felhőből az eszközre irányuló üzenet összekapcsolja az eredeti üzenettel kapcsolatos visszajelzéseit, a szolgáltatásnak meg kell adnia egy *MessageID*.

A visszajelzési üzenet törzse a következő kódban látható:

```json
[
  {
    "OriginalMessageId": "0987654321",
    "EnqueuedTimeUtc": "2015-07-28T16:24:48.789Z",
    "StatusCode": 0,
    "Description": "Success",
    "DeviceId": "123",
    "DeviceGenerationId": "abcdefghijklmnopqrstuvwxyz"
  },
  {
    ...
  },
  ...
]
```

**Törlésre váró eszközök visszajelzése**

Ha töröl egy eszközt, a függőben lévő visszajelzések is törlődnek. Az eszköz visszajelzéseit kötegekben küldi el a rendszer. Ha egy eszköz törölve lett a keskeny ablakban (általában 1 másodpercnél kevesebb), amikor az eszköz megerősíti az üzenet fogadását és a következő visszajelzési köteg előkészítését, a visszajelzés nem fog történni.

Ezt a viselkedést úgy érheti el, ha az eszköz törlése előtt egy időszakot vár a függőben lévő visszajelzések megérkezésére. A kapcsolódó üzenetek visszajelzését az eszköz törlése után el kell fogadniuk.

## <a name="cloud-to-device-configuration-options"></a>A felhőből az eszközre történő konfigurációs beállítások

Minden IoT hub a következő konfigurációs beállításokat teszi elérhetővé a felhőből az eszközre irányuló üzenetküldéshez:

| Tulajdonság                  | Leírás | Tartomány és alapértelmezett |
| ------------------------- | ----------- | ----------------- |
| defaultTtlAsIso8601       | A felhőből az eszközre irányuló üzenetek alapértelmezett ÉLETTARTAMa | ISO_8601 intervallum legfeljebb 2 nap (legalább 1 perc); alapértelmezett: 1 óra |
| maxDeliveryCount          | A felhőből az eszközre irányuló várólisták maximális kézbesítési száma | 1 – 100; alapértelmezett: 10 |
| visszajelzés. ttlAsIso8601     | Szolgáltatáshoz kötött visszajelzési üzenetek megőrzése | ISO_8601 intervallum legfeljebb 2 nap (legalább 1 perc); alapértelmezett: 1 óra |
| visszajelzés. maxDeliveryCount | A visszajelzési várólista maximális kézbesítési száma | 1 – 100; alapértelmezett: 10 |
| visszajelzés. lockDurationAsIso8601 | A visszajelzési várólista maximális kézbesítési száma | ISO_8601 5 és 300 másodperc közötti intervallum (legalább 5 másodperc); alapértelmezett: 60 másodperc. |

A konfigurációs beállításokat a következő módszerek egyikével állíthatja be:

* **Azure Portal**: az IoT hub **Beállítások** területén válassza a **beépített végpontok** lehetőséget, és bontsa ki **a felhőt az eszköz üzenetkezeléséhez**. (A **feedback. maxDeliveryCount** és a **feedback. lockDurationAsIso8601** tulajdonságok beállítása jelenleg nem támogatott a Azure Portalban.)

    ![A felhőből az eszközre irányuló üzenetküldés konfigurációs beállításainak megadása a portálon](./media/iot-hub-devguide-messages-c2d/c2d-configuration-portal.png)

* **Azure CLI**: használja az az [IOT hub Update](/cli/azure/iot/hub#az-iot-hub-update) parancsot:

    ```azurecli
    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.defaultTtlAsIso8601=PT1H0M0S

    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.maxDeliveryCount=10

    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.feedback.ttlAsIso8601=PT1H0M0S

    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.feedback.maxDeliveryCount=10

    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.feedback.lockDurationAsIso8601=PT0H1M0S
    ```

## <a name="next-steps"></a>Következő lépések

További információ a felhőből az eszközre irányuló üzenetek fogadására használható SDK-k használatáról: [Azure IoT SDK](iot-hub-devguide-sdks.md)-k.

A felhőből az eszközre irányuló üzenetek fogadásának kipróbálásához tekintse meg a [felhőből az eszközre történő küldéssel](iot-hub-csharp-csharp-c2d.md) kapcsolatos oktatóanyagot.
