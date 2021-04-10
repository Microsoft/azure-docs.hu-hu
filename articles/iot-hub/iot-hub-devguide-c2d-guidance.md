---
title: Azure IoT Hub a felhőből az eszközre lehetőségek | Microsoft Docs
description: Fejlesztői útmutató – útmutató a közvetlen módszerek használatához, az eszköz Twin kívánt tulajdonságainak vagy a felhőből az eszközre irányuló kommunikációhoz.
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
ms.openlocfilehash: ad4f5dcd137a9be6dfc764385802792026c0297d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101093011"
---
# <a name="cloud-to-device-communications-guidance"></a>A felhőből az eszközre irányuló kommunikációs útmutató

A IoT Hub három lehetőséget biztosít az eszközök alkalmazásai számára a háttérbeli alkalmazások működésének biztosítására:

* A [közvetlen metódusok](iot-hub-devguide-direct-methods.md) az eredmény azonnali megerősítését igénylő kommunikációhoz valók. A közvetlen metódusokat gyakran használják az eszközök interaktív vezérlésére, például ventilátor bekapcsolására.

* Az eszköz kívánt állapotba helyezéséhez szükséges, a hosszú ideig futó parancsok számára a [Twin tulajdonság](iot-hub-devguide-device-twins.md) . Állítsa be például a telemetria küldési időközét 30 percre.

* A [felhőből az eszközre](iot-hub-devguide-messages-c2d.md) irányuló üzenetek az eszköz alkalmazására vonatkozó egyirányú értesítésekhez.

Annak megismeréséhez, hogy az [Azure IoT Plug and Play](../iot-pnp/overview-iot-plug-and-play.md) ezeket a beállításokat használja a IoT Plug and Play eszközök vezérléséhez, lásd: [IoT Plug and Play Service fejlesztői útmutató](../iot-pnp/concepts-developer-guide-service.md).

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Itt látható a felhőből az eszközre irányuló különböző kommunikációs lehetőségek részletes összevetése.

| Kategóriák | Közvetlen metódusok | A Twin kívánt tulajdonságai | Felhőből az eszközre irányuló üzenetek |
| ---------- | -------------- | ------------------------- | ------------------------ |
| Eset | Azonnali megerősítést igénylő parancsok, például ventilátor bekapcsolása. | Hosszú ideig futó parancsok, amelyek célja, hogy az eszköz egy bizonyos kívánt állapotba kerüljön. Állítsa be például a telemetria küldési időközét 30 percre. | Egyirányú értesítések az eszköz alkalmazásához. |
| Adatfolyam | Kétirányú. Az eszköz azonnal válaszolhat a metódusra. A megoldás háttérbe kerül a kérelem kontextusa. | Egyirányú. Az alkalmazás értesítést kap a tulajdonság változásáról. | Egyirányú. Az eszköz megkapja az üzenetet
| Tartósság | A leválasztott eszközök nincsenek kapcsolatba. A megoldás háttér-végpontja értesítést kap arról, hogy az eszköz nincs csatlakoztatva. | A tulajdonság értékei megmaradnak az eszköz Twin-ben. Az eszköz a következő újracsatlakoztatáskor fogja olvasni. A tulajdonságértékek a [IoT hub lekérdezési nyelvvel](iot-hub-devguide-query-language.md)olvashatók be. | Az üzenetek IoT Hub akár 48 óráig is megmaradnak. |
| Targets | Egy eszköz, amely **deviceId**-t vagy több eszközt használ a [feladatok](iot-hub-devguide-jobs.md)használatával. | Egy eszköz, amely **deviceId**-t vagy több eszközt használ a [feladatok](iot-hub-devguide-jobs.md)használatával. | Egyetlen eszköz az **deviceId** alapján. |
| Méret | A közvetlen metódusok maximális hasznos mérete 128 KB. | A kívánt tulajdonságok maximális mérete 32 KB. | Akár 64 KB-os üzenet. |
| Gyakoriság | Magas. További információ: [IoT hub korlátok](iot-hub-devguide-quotas-throttling.md). | Közepes. További információ: [IoT hub korlátok](iot-hub-devguide-quotas-throttling.md). | Alacsony. További információ: [IoT hub korlátok](iot-hub-devguide-quotas-throttling.md). |
| Protokoll | A MQTT vagy a AMQP használatával érhető el. | A MQTT vagy a AMQP használatával érhető el. | Minden protokollon elérhető. Az eszköznek a HTTPS használatakor kell lekérdezni. |

Ismerje meg, hogyan használhatók a közvetlen metódusok, a kívánt tulajdonságok és a felhőből az eszközre irányuló üzenetek a következő oktatóanyagokban:

* [Közvetlen metódusok használata](quickstart-control-device-node.md)
* [A kívánt tulajdonságok használata az eszközök konfigurálásához](tutorial-device-twins.md) 
* [Üzenetküldés a felhőből az eszközökre](iot-hub-node-node-c2d.md)
