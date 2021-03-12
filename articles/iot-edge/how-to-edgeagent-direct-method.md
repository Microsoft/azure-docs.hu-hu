---
title: Beépített edgeAgent közvetlen metódusok – Azure IoT Edge
description: IoT Edge üzemelő példány figyelése és kezelése beépített közvetlen módszerekkel az IoT Edge Agent Runtime modulban
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/02/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 52cd7817594c5c2a1d4e3a4ca9c56891df594cd6
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2021
ms.locfileid: "103201111"
---
# <a name="communicate-with-edgeagent-using-built-in-direct-methods"></a>Kommunikáció a edgeAgent a beépített közvetlen módszerek használatával

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

IoT Edge központi telepítések figyelése és kezelése az IoT Edge Agent modulban foglalt közvetlen módszerek használatával. A közvetlen metódusok implementálva vannak az eszközön, majd a felhőből is meghívhatók. A IoT Edge ügynök olyan közvetlen metódusokat tartalmaz, amelyek segítségével távolról figyelheti és kezelheti IoT Edge eszközeit.

További információ a közvetlen módszerekről, a használatáról és a saját modulok megvalósításáról: [közvetlen módszerek megismerése és meghívása a IoT hubból](../iot-hub/iot-hub-devguide-direct-methods.md).

Ezeknek a közvetlen metódusoknak a neve a kis-és nagybetűket nem megkülönböztető módon kezeli.

## <a name="ping"></a>Ping

A **pingelési** módszer hasznos annak ellenőrzéséhez, hogy IoT Edge fut-e az eszközön, vagy hogy az eszköz rendelkezik-e nyitott internetkapcsolattal IoT hub. Ezzel a közvetlen módszerrel pingelheti a IoT Edge-ügynököt, és lekérheti annak állapotát. A sikeres pingelés üres adattartalmat ad vissza, és **"status": 200**.

Például:

```azurecli
az iot hub invoke-module-method --method-name 'ping' -n <hub name> -d <device name> -m '$edgeAgent'
```

A Azure Portalban hívja meg a metódust a metódus nevével `ping` és egy üres JSON-adattartalommal `{}` .

![Közvetlen "ping" metódus meghívása Azure Portal](./media/how-to-edgeagent-direct-method/ping-direct-method.png)

## <a name="restart-module"></a>Modul újraindítása

A **RestartModule** metódus lehetővé teszi a IoT Edge eszközön futó modulok távoli felügyeletét. Ha egy modul hibás állapotot vagy más nem kifogástalan viselkedést jelent, akkor a IoT Edge-ügynök kiváltható az újraindításhoz. Egy sikeres újraindítási parancs üres adattartalmat ad vissza, és **"status": 200**.

A RestartModule metódus a IoT Edge 1.0.9 és újabb verziókban érhető el. 

A RestartModule Direct metódust bármely IoT Edge eszközön futó modulon használhatja, beleértve a edgeAgent modult is. Ha azonban ezt a közvetlen módszert használja a edgeAgent leállítására, nem fog sikeres eredményt kapni, mert a rendszer megszakította a kapcsolódást a modul újraindításakor.

Például:

```azurecli
az iot hub invoke-module-method --method-name 'RestartModule' -n <hub name> -d <device name> -m '$edgeAgent' --method-payload \
'
    {
        "schemaVersion": "1.0",
        "id": "<module name>"
    }
'
```

A Azure Portal hívja meg a metódust a metódus nevével `RestartModule` és a következő JSON-adattartalommal:

```json
{
    "schemaVersion": "1.0",
    "id": "<module name>"
}
```

![Közvetlen "RestartModule" metódus hívása Azure Portal](./media/how-to-edgeagent-direct-method/restartmodule-direct-method.png)

## <a name="diagnostic-direct-methods"></a>Közvetlen diagnosztikai módszerek

* [GetModuleLogs](how-to-retrieve-iot-edge-logs.md#retrieve-module-logs): a közvetlen metódus válaszában beágyazott modul-naplók beolvasása.
* [UploadModuleLogs](how-to-retrieve-iot-edge-logs.md#upload-module-logs): beolvassa a modul naplófájljait, és feltölti őket az Azure Blob Storageba.
* [UploadSupportBundle](how-to-retrieve-iot-edge-logs.md#upload-support-bundle-diagnostics): a modul naplófájljainak beolvasása támogatási csomag használatával és zip-fájl feltöltése az Azure Blob Storageba.
* [GetTaskStatus](how-to-retrieve-iot-edge-logs.md#get-upload-request-status): a feltöltési naplók vagy a támogatási csomagra vonatkozó kérelem állapotának beadása.

Ezek a diagnosztikai közvetlen metódusok a 1.0.10 kiadásban érhetők el.

## <a name="next-steps"></a>Következő lépések

[A IoT Edge-ügynök és az IoT Edge hub-modulok ikrek tulajdonságai](module-edgeagent-edgehub.md)
