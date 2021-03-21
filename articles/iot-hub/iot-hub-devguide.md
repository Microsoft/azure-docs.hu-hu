---
title: Az Azure IoT Hub fejlesztői útmutatója | Microsoft Docs
description: Az Azure IoT Hub fejlesztői útmutatója a végpontok, a biztonság, az identitás-nyilvántartó, az eszközkezelés, a közvetlen metódusok, az eszköz-ikrek, a fájlfeltöltés, a feladatok, a IoT Hub lekérdezési nyelv és az üzenetküldés témaköreit tartalmazza.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.custom: mqtt
ms.openlocfilehash: 1fcf560ba1fef137a28ab1872635cc2182cd07e1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103009198"
---
# <a name="azure-iot-hub-developer-guide"></a>Azure IoT Hub – fejlesztői útmutató

Az Azure IoT Hub egy teljes körűen felügyelt szolgáltatás, amely lehetővé teszi a megbízható és biztonságos kétirányú kommunikációt több millió eszköz és egy megoldás hátterében.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Az Azure IoT Hub a következőket biztosítja:

* Biztonságos kommunikáció az eszközönkénti biztonsági hitelesítő adatok és hozzáférés-vezérlés használatával.

* Több eszközről a felhőbe és a felhőből az eszközre irányuló Hyper-Scale kommunikációs lehetőségek.

* Eszközönkénti állapotinformációkat és meta-adatokat lekérdezhető tároló.

* A legnépszerűbb nyelvekhez és platformokhoz egyszerűen csatlakoztathatók az eszközök könyvtárai.

Ez a IoT Hub fejlesztői útmutató a következő cikkeket tartalmazza:

* [Az eszközről a felhőbe irányuló kommunikációs útmutató](iot-hub-devguide-d2c-guidance.md) segítséget nyújt az eszközről a felhőbe irányuló üzenetek, az eszköz Twin által jelentett tulajdonságok és a fájlfeltöltés közötti választáshoz.

* [A felhőből az eszközre irányuló kommunikációs útmutató](iot-hub-devguide-c2d-guidance.md) segítségével választhatja ki a közvetlen metódusok, az eszköz Twin kívánt tulajdonságai és a felhőből az eszközre irányuló üzenetek lehetőséget.

* Az [eszközről a felhőbe és a felhőből az eszközre irányuló üzenetküldés IoT hub](iot-hub-devguide-messaging.md) ismerteti az üzenetkezelési funkciókat (az eszközről a felhőbe és a felhőből az eszközre), amelyet a IoT hub tesz elérhetővé.

  * [Eszközről a felhőbe irányuló üzenetek küldése IoT hubra](iot-hub-devguide-messages-d2c.md).

  * [Az eszközről a felhőbe irányuló üzenetek olvasása a beépített végpontról](iot-hub-devguide-messages-read-builtin.md).

  * [Az eszközről a felhőbe irányuló üzenetekhez egyéni végpontokat és útválasztási szabályokat használjon](iot-hub-devguide-messages-read-custom.md).

  * [A felhőből az eszközre irányuló üzenetek küldése IoT hubról](iot-hub-devguide-messages-c2d.md).

  * [IoT hub üzenetek létrehozása és olvasása](iot-hub-devguide-messages-construct.md).

* [Fájlok feltöltése egy eszközről](iot-hub-devguide-file-upload.md) leírja, hogyan tölthet fel fájlokat egy eszközről. A cikk olyan témakörökkel kapcsolatos információkat is tartalmaz, mint például a feltöltési folyamat által küldött értesítések.

* [Az eszközök identitásának kezelése IoT hub ismerteti, hogy az](iot-hub-devguide-identity-registry.md) egyes IoT hub-beli identitás-beállításjegyzék mely információkat tárolja. A cikk azt is leírja, hogyan érheti el és módosíthatja azt.

* A [IoT hubhoz való hozzáférés szabályozása](iot-hub-devguide-security.md) : az eszközök és a Felhőbeli összetevők IoT hub funkcióinak elérését biztosító biztonsági modellt ismerteti. A cikk a tokenek és az X. 509 tanúsítványok használatáról, valamint a megadható engedélyek részleteiről tartalmaz információkat.

* Az eszközök [és a konfigurációk szinkronizálásához használja](iot-hub-devguide-device-twins.md) az eszközök *iker* fogalmát. A cikk az ikrek által elérhető funkciókat is ismerteti, például az eszközök és az eszközök kettős szinkronizálását. A cikk a Twin eszközön tárolt adatokról tartalmaz információkat.

* [Közvetlen metódus meghívása egy eszközön](iot-hub-devguide-direct-methods.md) a közvetlen metódus életciklusát írja le. A cikk azt ismerteti, hogyan indíthatók metódusok egy eszközön a háttérbeli alkalmazásból, és hogyan kezelhető a közvetlen módszer az eszközön.

* [Több eszközön is ütemezhet feladatokat](iot-hub-devguide-jobs.md) , hogy miként ütemezhet több eszközön a feladatokat. A cikk azt ismerteti, hogyan lehet feladatokat ellátni a közvetlen metódusok végrehajtásával, egy eszköz a Twin eszközzel történő frissítésével. Azt is leírja, hogyan lehet lekérdezni a feladatok állapotát.

* [Hivatkozás – válassza ki a kommunikációs protokollt](iot-hub-devguide-protocols.md) , írja le azokat a kommunikációs protokollokat, amelyeket a IoT hub támogat az eszközök közötti kommunikációhoz, és felsorolja a megnyitni kívánt portokat.

* A [hivatkozás-IoT hub végpontok](iot-hub-devguide-endpoints.md) ismertetik a különböző végpontokat, amelyeket az egyes IoT hub a futtatókörnyezet és a felügyeleti műveletek számára tesz elérhetővé. A cikk azt is leírja, hogyan hozhatók létre további végpontok az IoT hub-ban, és hogyan használhatók a helyszíni átjárók a IoT Hub-végpontok nem szabványos helyzetekben való kapcsolódásának engedélyezéséhez.

* [Hivatkozás – IoT hub lekérdezési nyelv az ikrek, a feladatok és az üzenet-útválasztás számára](iot-hub-devguide-query-language.md) azt írja le, hogy IoT hub lekérdezési nyelv, amely lehetővé teszi információk lekérését az adott központról az adott eszközről az ikrekről

* [Referenciák – a kvóták és a szabályozások](iot-hub-devguide-quotas-throttling.md) összegzik a IoT hub szolgáltatásban beállított kvótákat és a kvóták túllépése esetén bekövetkezett szabályozást.

* [Referenciák – a díjszabás](iot-hub-devguide-pricing.md) általános információkat tartalmaz a különböző SKU-ról és a IoT hub díjszabásáról, valamint arról, hogy a különböző IoT hub funkciók hogyan mérhetők a IoT hub által küldött üzenetekként.

* Az [eszköz-és szolgáltatási SDK-](iot-hub-devguide-sdks.md) k az Azure IoT SDK-kat listázza az IoT hub-t használó eszköz-és szolgáltatás-alkalmazások fejlesztéséhez. A cikk az online API-dokumentációra mutató hivatkozásokat tartalmaz.

* A [Reference-IOT hub MQTT támogatása](iot-hub-mqtt-support.md) részletes információkat nyújt arról, hogy a IoT hub hogyan támogatja a MQTT protokollt. A cikk az Azure IoT SDK-k beépített MQTT protokolljának támogatását ismerteti, és információt nyújt az MQTT protokoll közvetlen használatáról.
