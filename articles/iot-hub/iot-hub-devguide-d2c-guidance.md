---
title: Az Azure IoT Hub eszközről a felhőbe irányuló lehetőségek | Microsoft Docs
description: Fejlesztői útmutató – útmutató arról, hogy mikor kell használni az eszközről a felhőbe irányuló üzeneteket, a jelentett tulajdonságokat vagy a fájlfeltöltés a felhőből az eszközre történő kommunikációhoz.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: dd4f4ad7f84ebc1f5e254843e7afa4aa0f3f224f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87319133"
---
# <a name="device-to-cloud-communications-guidance"></a>Az eszközről a felhőbe irányuló kommunikációs útmutató

Amikor adatokat küld az eszköz alkalmazásból a megoldás háttérbe, IoT Hub három lehetőséget tesz elérhetővé:

* Az idősorozat-telemetria és-riasztások [eszközről a felhőbe](iot-hub-devguide-messages-d2c.md) irányuló üzenetei.

* A [Device Twin által jelentett tulajdonságok jelentik](iot-hub-devguide-device-twins.md) az eszköz állapotával kapcsolatos információkat, például az elérhető képességeket, a feltételeket vagy a hosszan futó munkafolyamatok állapotát. Ilyenek például a konfiguráció és a szoftverfrissítések.

* A fájlok feltöltése a médiafájlok és a nagyméretű telemetria-kötegek számára, amelyeket időnként csatlakoztatott eszközök vagy tömörítettek a sávszélesség megtakarítása érdekében töltöttek [fel](iot-hub-devguide-file-upload.md) .

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Itt látható az eszközről a felhőbe irányuló különböző kommunikációs lehetőségek részletes összevetése.

| Szempont | Az eszközről a felhőbe irányuló üzenetek | A Device Twin szolgáltatás jelentett tulajdonságai | Fájlfeltöltés |
| ---- | ------- | ---------- | ---- |
| Eset | Telemetria idősorozat és riasztások. Például 256 – KB-os érzékelő adatkötegek 5 percenként elküldve. | Elérhető képességek és kikötések. Például az eszköz aktuális kapcsolati módja, például a mobil vagy a WiFi. Hosszan futó munkafolyamatok, például a konfiguráció és a szoftverfrissítések szinkronizálása. | Médiafájlok. Nagyméretű (általában tömörített) telemetria kötegek. |
| Tárolás és lekérés | Ideiglenesen tárolta IoT Hub, legfeljebb 7 nap. Csak szekvenciális olvasás. | Az eszköz Twin IoT Hub tárolja. Beolvasható a [IoT hub lekérdezési nyelv](iot-hub-devguide-query-language.md)használatával. | A felhasználó által megadott Azure Storage-fiókban tárolva. |
| Méret | Akár 256 KB-os üzenet. | A jelentett tulajdonságok maximális mérete 32 KB. | Az Azure Blob Storage által támogatott maximális fájlméret. |
| Gyakoriság | Magas. További információ: [IoT hub korlátok](iot-hub-devguide-quotas-throttling.md). | Közepes. További információ: [IoT hub korlátok](iot-hub-devguide-quotas-throttling.md). | Alacsony. További információ: [IoT hub korlátok](iot-hub-devguide-quotas-throttling.md). |
| Protokoll | Minden protokollon elérhető. | A MQTT vagy a AMQP használatával érhető el. | Bármely protokoll használata esetén elérhető, de HTTPS-t igényel az eszközön. |

Előfordulhat, hogy egy alkalmazásnak telemetria idősorozatként vagy riasztásként is el kell küldenie az adatokat, és elérhetővé kell tennie azt az eszköz Twin szolgáltatásában. Ebben az esetben az alábbi lehetőségek közül választhat:

* Az eszköz az eszközről a felhőbe irányuló üzenetet küld, és jelentést készít a tulajdonságok változásáról.
* A megoldás háttérbe állítása az eszköz Twin címkéi között tárolja az üzenetet, amikor megkapja az üzenetet.

Mivel az eszközről a felhőbe irányuló üzenetek sokkal nagyobb teljesítményt tesznek lehetővé, mint az eszközök kettős frissítései, időnként érdemes elkerülni, hogy az eszközről a felhőbe irányuló összes üzenethez ne kelljen a Twin-et frissíteni.
