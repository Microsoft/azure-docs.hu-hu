---
title: A Azure IoT Hub közötti üzenetkezelés | Microsoft Docs
description: Ez a fejlesztői útmutató bemutatja, hogyan használhatja a felhőből az eszközök közötti üzenetküldést az IoT Hubbal. Információkat tartalmaz az üzenetek életciklusról és konfigurációs beállításokról.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/15/2018
ms.custom: mqtt, devx-track-azurecli
ms.openlocfilehash: 7bb3ca2b31eaef5c0639f30e0f2a329a37dfe7e0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107761780"
---
# <a name="send-cloud-to-device-messages-from-an-iot-hub"></a>Üzenetek küldése a felhőből az eszközre egy IoT Hubról

Ha egy egyértelmű értesítéseket küld egy eszközalkalmazásnak a megoldás háttéralkalmazásból, küldjön a felhőből az eszközre üzeneteket az IoT Hubról az eszközre. A felhőből az eszközökre való kommunikáció egyéb, a felhőből az Azure IoT Hub által támogatott lehetőségekről a felhőből az eszközre való [kommunikáció útmutatója nyújt további útmutatást.](iot-hub-devguide-c2d-guidance.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

A felhőből az eszközre irányuló üzeneteket az */messages/devicebound* szolgáltatásvégponton keresztül küldheti el. Az eszközök ezután egy eszközspecifikus végponton *(/devices/{deviceId}/messages/devicebound)* keresztül kapják meg az üzeneteket.

Ha minden felhőből egy eszközre irányuló üzenetet egyetlen eszközre  céloz, az IoT Hub a tulajdonságot a következőre állítja: */devices/{deviceId}/messages/devicebound*.

Az eszközsorok akár 50, felhőből az eszközre küldött üzenetet is tartalmaznak. Ha több üzenetet próbál küldeni ugyanabba az eszközre, az hibát jelez.

## <a name="the-cloud-to-device-message-life-cycle"></a>A felhőből az eszközre küldött üzenetek életciklusa

Az Üzenetek legalább egyszer történő kézbesítésének garantálása érdekében az IoT Hub eszközönkénti üzenetsorok között is megmarad a felhőből az eszközre küldött üzenetekben. Ahhoz, hogy az IoT Hub eltávolítsa az üzeneteket az üzenetsorból, az eszközöknek explicit módon meg kell nyugtázni a *befejezést.* Ez a megközelítés rugalmasságot biztosít a csatlakozási és eszközhibák esetén.

Az életciklus-állapot diagramja az alábbi ábrán látható:

![A felhőből az eszközre küldött üzenetek életciklusa](./media/iot-hub-devguide-messages-c2d/lifecycle.png)

Amikor az IoT Hub szolgáltatás üzenetet küld egy eszköznek, az üzenetállapotot a *Következőre állítja: Enqueued*. Amikor egy eszköz üzenetet *szeretne kapni,* az IoT *Hub* a Láthatatlan állapotra való beállításával *zárolja az üzenetet.* Ez az állapot lehetővé teszi, hogy az eszközön más szálak is elkezdenek más üzeneteket fogadni. Amikor egy eszközszál befejezi egy üzenet feldolgozását, az  üzenet kitöltésével értesíti az IoT Hubot. Az IoT Hub ezután Befejezve állapotba állítja *az állapotot.*

Az eszközök a következőre is képesek:

* *Az* üzenet elutasítása, amelynek hatására az IoT Hub a Nem küldött *betűvel írt állapotba állítsa* azt. Az üzenetsor-kezelés telemetria-átviteli (MQTT) protokollal összekapcsolt eszközök nem utasítják el a felhőből az eszközre küldött üzeneteket.

* *Hagyja* fel az üzenetet, ami miatt az IoT Hub visszatesz egy üzenetet az üzenetsorba, és az állapot *a Sorba állítva lesz.* Az MQTT protokollon keresztül csatlakozó eszközök nem hagyhatják le a felhőből az eszközre küldött üzeneteket.

Egy szál nem tudja feldolgozni az üzenetet az IoT Hub értesítése nélkül. Ebben az esetben az üzenetek  automatikusan visszatérnek  a Láthatatlan állapotról a Besorolt  állapotba a *láthatósági* időkorlát (vagy a zárolás időkorlátja) után. Ennek az időkorreklének értéke egy perc, és nem módosítható.

Az **IoT** Hub maximális kézbesítésszám tulajdonsága határozza meg, hogy egy üzenet legfeljebb hányszor válthat át a *Enqueued* és *a Invisible* (Láthatatlan) államok között. Ennyi áttűnés után az IoT Hub az üzenet állapotát *"Dead lettered" (Nem küldött) állapotra állítja.* Hasonlóképpen, az IoT Hub az üzenetek állapotát a lejárati ideje utáni, "Dead *lettered"* (Nem küldött üzenet) állapotra állítja. További információ: [Time to live (Az idő az életben).](#message-expiration-time-to-live)

A [How to send cloud-to-device messages with IoT Hub (Felhőből](iot-hub-csharp-csharp-c2d.md) az eszközre küldött üzenetek küldése a IoT Hub-val) cikk bemutatja, hogyan küldhet a felhőből az eszközre üzeneteket a felhőből, és hogyan fogadhatja azokat egy eszközön.

Az eszközök általában egy felhőből egy eszközre vonatkozó üzenetet küldnek, ha az üzenet elvesztése nincs hatással az alkalmazás logikára. Ez lehet például akkor, ha az eszköz helyben tárolta az üzenet tartalmát, vagy sikeresen végrehajtott egy műveletet. Az üzenet átmeneti információkat is hordozhat, amelyek elvesztése nem befolyásolta volna az alkalmazás működését. A hosszan futó feladatokhoz néha a következő műveleteket lehet elvégezni:

* Miután az eszköz megőrzötte a feladat leírását a helyi tárolóban, töltse ki a felhőből az eszközre küldött üzenetet.

* Értesítse a megoldás háttérszolgáltatását egy vagy több, eszközről a felhőbe küldött üzenetben a feladat előrehaladásának különböző szakaszaiban.

## <a name="message-expiration-time-to-live"></a>Üzenetek lejárata (az idő az élőben)

Minden felhőből egy eszközre küldött üzenet lejárati idővel rendelkezik. Ezt az időt a következők valamelyike adja meg:

* A **szolgáltatás ExpiryTimeUtc** tulajdonsága
* Az IoT Hub az  IoT Hub-tulajdonságként megadott alapértelmezett időtartam használatával

Lásd: A [felhőből az eszközre vonatkozó konfigurációs beállítások.](#cloud-to-device-configuration-options)

Az üzenetek lejáratának és az üzenetek leválasztott eszközökre való küldésének elkerülése érdekében gyakori módszer a rövid idő beállítása az élő *értékekhez.* Ez a megközelítés ugyanazt az eredményt éri el, mint az eszköz kapcsolati állapotának fenntartása, de hatékonyabb. Amikor nyugtázást kér az üzenethez, az IoT Hub értesíti, hogy mely eszközök:

* Képes üzeneteket fogadni.
* Nincsenek online vagy sikertelenek.

## <a name="message-feedback"></a>Üzenet-visszajelzés

Amikor egy felhőből egy eszközre küldött üzenetet küld, a szolgáltatás kérheti az üzenetenkénti visszajelzés kézbesítését az üzenet végleges állapotáról. Ezt az **iothub-ack** alkalmazástulajdonságnak a következő négy érték egyikére küldött, felhőből az eszközre küldött üzenetben kell megtennie:

| Ack tulajdonság értéke | Működés |
| ------------ | -------- |
| Nincs     | Az IoT Hub nem hoz létre visszajelzési üzenetet (alapértelmezett viselkedés). |
| pozitív | Ha a felhőből az eszközre küldött üzenet *Befejezve* állapotba jut, az IoT Hub létrehoz egy visszajelzési üzenetet. |
| negatív | Ha a felhőből az eszközre küldött üzenet a "Nem *küldve"* állapotot éri el, az IoT Hub létrehoz egy visszajelzési üzenetet. |
| Teljes     | Az IoT Hub mindkét esetben létrehoz egy visszajelzési üzenetet. |

Ha **az Ack** értéke *megtelt,* és nem kap visszajelzési üzenetet, az azt jelenti, hogy a visszajelzési üzenet lejárt. A szolgáltatás nem tudja, mi történt az eredeti üzenettel. A gyakorlatban a szolgáltatásnak biztosítania kell, hogy feldolgozhatja a visszajelzést, mielőtt lejárna. A maximális lejárati idő két nap, ami időt hagy a szolgáltatás újrafuttatására hiba esetén.

A [Végpontok részben](iot-hub-devguide-endpoints.md)leírtak szerint az IoT Hub egy szolgáltatás felé néző végponton *(/messages/servicebound/feedback)* keresztül küld visszajelzést üzenetként. A visszajelzések fogadásának szemantikája ugyanaz, mint a felhőből az eszközre küldött üzenetekben. Amikor csak lehetséges, az üzenet-visszajelzéseket a rendszer egyetlen üzenetbe kötegi, a következő formátumban:

| Tulajdonság     | Leírás |
| ------------ | ----------- |
| EnqueuedTime | Egy időbélyeg, amely azt jelzi, hogy a visszajelzési üzenetet mikor kapta meg a központ |
| UserId (Felhasználóazonosító)       | `{iot hub name}` |
| ContentType (Tartalomtípus)  | `application/vnd.microsoft.iothub.feedback.json` |

A rendszer akkor küld visszajelzést, ha a köteg eléri a 64 üzenetet, vagy 15 másodpercen belül az utolsó küldéstől (amelyik előbb érkezik). 

A törzs rekordok JSON-szerializált tömbje, amelyek mind a következő tulajdonságokkal rendelkeznek:

| Tulajdonság           | Leírás |
| ------------------ | ----------- |
| EnqueuedTimeUtc    | Egy időbélyeg, amely azt jelzi, hogy mikor történt az üzenet kimenete (például a központ megkapta a visszajelzési üzenetet, vagy az eredeti üzenet lejárt) |
| OriginalMessageId (EredetimessageId)  | Annak *a felhőből* az eszközre küldött üzenetnek az Üzenetazonosítója, amelyhez a visszajelzési információ kapcsolódik |
| StatusCode (Állapotkód)         | Egy kötelező sztring, amely az IoT Hub által létrehozott visszajelzési üzenetekben használatos: <br/> *Siker* <br/> *Lejárt* <br/> *DeliveryCountExceeded* <br/> *Elutasítva* <br/> *Kiürítve* |
| Description        | Sztringértékek a *StatusCode-hez* |
| DeviceId           | A *felhőből* az eszközre küldött üzenet céleszközének DeviceId-e, amelyhez ez a visszajelzés kapcsolódik |
| DeviceGenerationId | Annak *a felhő–eszköz* üzenetnek a DeviceGenerationId-e, amelyre ez a visszajelzés vonatkozik |

Ahhoz, hogy a felhőből egy eszközre küldött üzenet össze tudja korrelálni a visszajelzését az eredeti üzenettel, a szolgáltatásnak meg kell adnia egy *MessageId (Üzenetazonosító) értéket.*

A visszajelzési üzenet törzse az alábbi kódban látható:

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

**Függőben lévő visszajelzés a törölt eszközökről**

Egy eszköz törlésekor a függőben lévő visszajelzések is törlődnek. Az eszköz visszajelzései kötegben vannak elküldve. Ha egy eszközt törölnek a keskeny (gyakran 1 másodpercnél rövidebb) ablakban, amikor az eszköz megerősíti az üzenet beérkeztét, és a következő visszajelzési köteg elkészülte között, a visszajelzés nem történik meg.

Ezt a viselkedést úgy is felhasználhatja, ha az eszköz törlése előtt várakozik egy ideig, amíg a függőben lévő visszajelzés megérkezik. A kapcsolódó üzenetekkel kapcsolatos visszajelzéseket az eszköz törlésekor el kell veszni.

## <a name="cloud-to-device-configuration-options"></a>A felhőből az eszközre vonatkozó konfigurációs lehetőségek

Minden IoT Hub a következő konfigurációs lehetőségeket teszi elérhetővé a felhőből az eszközre való üzenetküldéshez:

| Tulajdonság                  | Leírás | Tartomány és alapértelmezett |
| ------------------------- | ----------- | ----------------- |
| defaultTtlAsIso8601       | Alapértelmezett TTL a felhőből az eszközre küldött üzenetekhez | ISO_8601 legfeljebb 2 nap (legalább 1 perc) időköz lehet; alapértelmezett: 1 óra |
| maxDeliveryCount          | A felhőből az eszközre történő kézbesítés maximális száma az eszközönkénti üzenetsorok esetén | 1 és 100 között; alapértelmezett: 10 |
| feedback.ttlAsIso8601     | Szolgáltatáshoz kötött visszajelzési üzenetek megőrzése | ISO_8601 időköz legfeljebb 2 nap (legalább 1 perc); alapértelmezett: 1 óra |
| feedback.maxDeliveryCount | A visszajelzési üzenetsor maximális kézbesítési száma | 1 és 100 között; alapértelmezett: 10 |
| feedback.lockDurationAsIso8601 | A visszajelzési üzenetsor maximális kézbesítési száma | ISO_8601 időköz 5 és 300 másodperc között (legalább 5 másodperc); alapértelmezett érték: 60 másodperc. |

A konfigurációs beállításokat a következő módokon állíthatja be:

* **Azure Portal:** **Az** IoT Hub Beállítások csomópontja alatt válassza a Beépített végpontok lehetőséget, és **bontsa** ki a Cloud to device messaging (Felhő az eszköz **üzenetkezelése) csomópontot.** (A **feedback.maxDeliveryCount** és **a feedback.lockDurationAsIso8601** tulajdonságok beállítása jelenleg nem támogatott a Azure Portal.)

    ![A felhőből az eszközre való üzenetküldés konfigurációs beállításainak megadása a portálon](./media/iot-hub-devguide-messages-c2d/c2d-configuration-portal.png)

* **Azure CLI:** Használja [az az iot hub update parancsot:](/cli/azure/iot/hub#az_iot_hub_update)

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

A felhőből az eszközre küldött üzenetek fogadására használható ADATTK-okkal kapcsolatos információkért lásd: [Azure IoT SDK-k.](iot-hub-devguide-sdks.md)

A felhőből az eszközre küldött üzenetek fogadásának kipróbálásért tekintse meg a Felhőből az eszközre küldése [oktatóanyagot.](iot-hub-csharp-csharp-c2d.md)
