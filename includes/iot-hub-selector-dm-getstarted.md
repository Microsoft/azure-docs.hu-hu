---
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 239ea2eb4d5bc8d326d5ca503a18b149252dc1be
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "69558753"
---
> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/iot-hub-node-node-device-management-get-started.md)
> * [.NET](../articles/iot-hub/iot-hub-csharp-csharp-device-management-get-started.md)
> * [Java](../articles/iot-hub/iot-hub-java-java-device-management-getstarted.md)
> * [Python](../articles/iot-hub/iot-hub-python-python-device-management-get-started.md)

A háttérbeli alkalmazások az Azure IoT Hub primitíveket, például az eszközök [kettős][lnk-devtwin] és [közvetlen metódusait][lnk-c2dmethod]használhatják az eszközökön található eszközkezelés távoli indításához és figyeléséhez. Ebből az oktatóanyagból megtudhatja, hogyan működhet együtt a háttérbeli alkalmazások és az eszközök egy távoli eszköz újraindításának elindításához és figyeléséhez IoT Hub használatával.

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]

A felhőben található háttérbeli alkalmazásból közvetlen metódust használhat az Eszközkezelő műveletek (például az újraindítás, a gyári beállítások visszaállítása és a belső vezérlőprogram frissítése) elindításához. Az eszköz feladata:

* A IoT Hub által elküldett metódus-kérelem feldolgozása.

* A megfelelő eszközre vonatkozó művelet kezdeményezése az eszközön.

* Az állapot frissítéseinek biztosítása a *jelentett tulajdonságokkal* IoT hub.

A felhőben található háttérbeli alkalmazás használatával az eszközökre vonatkozó kettős lekérdezések futtatásával jelentést készíthet az Eszközkezelő műveleteinek folyamatáról.

[lnk-devtwin]: ../articles/iot-hub/iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
