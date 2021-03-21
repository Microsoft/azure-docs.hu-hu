---
title: Ismerje meg, hogyan futnak a modulok logikája az eszközökön – Azure IoT Edge | Microsoft Docs
description: Azure IoT Edge modulok a logikai tároló egységei, amelyek távolról üzembe helyezhetők és felügyelhetők, így az üzleti logikát IoT Edge eszközökön is futtathatja
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/21/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: devx-track-csharp
ms.openlocfilehash: a9b1ffb2dbcbd6e81856277f4b672cf876cc75f1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103492370"
---
# <a name="understand-azure-iot-edge-modules"></a>Az Azure IoT Edge-modulok ismertetése

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Azure IoT Edge lehetővé teszi az üzleti logikák üzembe helyezését és kezelését *modulok* formájában. A Azure IoT Edge modulok a IoT Edge által kezelt számítási egységek legkisebb egységei, és tartalmazhatnak Azure-szolgáltatásokat (például Azure Stream Analytics) vagy a saját megoldásra vonatkozó kódokat. A modulok fejlesztésének, üzembe helyezésének és karbantartásának megismeréséhez vegye fontolóra a modul négy fogalmi elemét:

* A **modul képe** egy olyan csomag, amely tartalmazza a modult meghatározó szoftvert.
* A **Module-példányok** az adott számítási egység, amely a modul rendszerképét futtatja egy IoT Edge eszközön. A modul példányát a IoT Edge futtatókörnyezet indítja el.
* A **modul identitása** a IoT hub tárolt adatok (beleértve a biztonsági hitelesítő adatokat is), amelyek az egyes modulok példányaihoz vannak társítva.
* A **Twin modul** egy IOT hub tárolt JSON-dokumentum, amely a modul példányaira vonatkozó állapotinformációkat tartalmaz, beleértve a metaadatokat, a konfigurációkat és a feltételeket.

## <a name="module-images-and-instances"></a>Modul lemezképei és példányai

IoT Edge modul lemezképei olyan alkalmazásokat tartalmaznak, amelyek kihasználják a IoT Edge futtatókörnyezet felügyeleti, biztonsági és kommunikációs funkcióit. Saját modul-lemezképeket fejleszthet, vagy exportálhat egyet egy támogatott Azure-szolgáltatásból, például Azure Stream Analyticsból.
A lemezképek a felhőben találhatók, és különböző megoldásokban frissíthetők, módosíthatók és üzembe helyezhetők. Például egy gépi tanulást használó modul, amely azt jelzi, hogy a termelési vonal kimenete különálló képként létezik, mint egy olyan modul, amely a számítógép jövőképét használja a drone vezérléséhez.

Minden alkalommal, amikor egy modul lemezképét központilag telepíti egy eszközre, és a IoT Edge futtatókörnyezet indította el, létrejön a modul egy új példánya. A világ különböző részein két eszköz használhatja ugyanazt a modul-rendszerképet. Azonban minden eszköz saját modul-példánnyal rendelkezik, ha a modul elindult az eszközön.

![Diagram – modul-rendszerképek a felhőben, modul-példányok az eszközökön](./media/iot-edge-modules/image_instance.png)

A implementációban a modulok lemezképei tároló lemezképként vannak tárolva, és a modul példányai az eszközökön található tárolók.

<!--
As use cases for Azure IoT Edge grow, new types of module images and instances will be created. For example, resource constrained devices cannot run containers so may require module images that exist as dynamic link libraries and instances that are executables. 
-->

## <a name="module-identities"></a>Modul identitásai

Ha a IoT Edge futtatókörnyezet létrehoz egy új modul-példányt, a rendszer egy megfelelő modul-identitást kap. A modul identitása IoT Hub van tárolva, és a rendszer az adott modul-példány helyi és felhőalapú kommunikációjának címzési és biztonsági hatókörét használja.

A modul-példányhoz társított identitás azon eszköz identitásán múlik, amelyen a példány fut, valamint az adott modulhoz megadott nevet a megoldásban. Ha például egy `insight` Azure stream Analyticst használó modult hív meg, és egy nevű eszközön telepíti azt `Hannover01` , akkor a IoT Edge futtatókörnyezet létrehoz egy megfelelő modul-identitást `/devices/Hannover01/modules/insight` .

Egyértelmű, hogy olyan helyzetekben, amikor egy modul lemezképét többször kell telepíteni ugyanazon az eszközön, több alkalommal is telepítheti ugyanazt a rendszerképet különböző nevekkel.

![Diagram – a modul identitásai egyediek az eszközökön és az eszközök között](./media/iot-edge-modules/identity.png)

## <a name="module-twins"></a>Ikrek modul

Minden modul-példányhoz tartozik egy külön modul is, amely a modul példányának konfigurálására használható. A példány és a Twin a modul identitásán keresztül kapcsolódnak egymáshoz.

A Twin modul egy JSON-dokumentum, amely a modul információit és a konfigurációs tulajdonságokat tárolja. Ez a koncepció párhuzamosan az [eszköz kettős](../iot-hub/iot-hub-devguide-device-twins.md) fogalmát IoT hub. A Twin modul szerkezete megegyezik az eszköz Twin szolgáltatásával. A mindkét típusú ikrekkel való interakcióhoz használt API-k szintén azonosak. Az egyetlen különbség a kettő között az ügyfél-SDK létrehozásához használt identitás.

```csharp
// Create a ModuleClient object. This ModuleClient will act on behalf of a
// module since it is created with a module’s connection string instead
// of a device connection string.
ModuleClient client = new ModuleClient.CreateFromEnvironmentAsync(settings);
await client.OpenAsync();

// Get the module twin
Twin twin = await client.GetTwinAsync();
```

## <a name="offline-capabilities"></a>Offline képességek

Azure IoT Edge modulok legalább egyszer tudnak működni az IoT Hubekkel való szinkronizálás után. IoT Edge eszközök az offline képességet más IoT-eszközökre is kiterjeszthetik. További információ: [IoT Edge eszközök, modulok és alárendelt eszközök kibővített offline képességeinek ismertetése](offline-capabilities.md).

## <a name="next-steps"></a>Következő lépések

* [A IoT Edge modulok fejlesztéséhez szükséges követelmények és eszközök ismertetése](module-development.md)
* [A Azure IoT Edge futtatókörnyezet és az architektúrájának ismertetése](iot-edge-runtime.md)
