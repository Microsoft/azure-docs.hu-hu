---
title: Az Azure IoT Hub-kapcsolat figyelése és hibakeresése
description: Megtudhatja, hogyan figyelheti és elháríthatja az Azure-IoT Hub eszköz-kapcsolattal kapcsolatos gyakori hibákat
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: jlian
ms.custom:
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
- 'Role: Technical Support'
ms.openlocfilehash: b194812ef68820a0c310d0bac3b055360c5b5e4a
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2020
ms.locfileid: "92538425"
---
# <a name="monitor-diagnose-and-troubleshoot-disconnects-with-azure-iot-hub"></a>Az Azure IoT Hub-vel való kapcsolat figyelése, diagnosztizálása és hibakeresése

A IoT-eszközök kapcsolódási problémái nehézkesek lehetnek a hibaelhárításhoz, mert számos lehetséges meghibásodási pont áll rendelkezésre. Az alkalmazás logikája, a fizikai hálózatok, a protokollok, a hardverek, a IoT Hub és más felhőalapú szolgáltatások okozhatnak problémákat. Kritikus fontosságú a probléma forrásának észlelése és felderítése. Egy IoT megoldás azonban több ezer eszközzel is rendelkezhet, ezért nem célszerű manuálisan megnézni az egyes eszközöket. Ezen problémák nagy léptékű észlelése, diagnosztizálása és hibaelhárítása érdekében használja a figyelési képességek IoT Hub a Azure Monitoron keresztül. Ezek a képességek arra korlátozódnak, hogy milyen IoT Hub figyelhetők meg, ezért javasoljuk, hogy kövesse az eszközökhöz és más Azure-szolgáltatásokhoz kapcsolódó ajánlott eljárások monitorozását.

## <a name="get-alerts-and-error-logs"></a>Riasztások és hibanapló beolvasása

A Azure Monitor használatával riasztásokat kaphat, és naplókat írhat az eszközök leválasztásakor.

### <a name="turn-on-logs"></a>Naplók bekapcsolása

Az eszköz csatlakozási eseményeinek és hibáinak naplózásához hozzon létre egy diagnosztikai beállítást [IoT hub kapcsolatok erőforrás-naplóihoz](monitor-iot-hub-reference.md#connections). Azt javasoljuk, hogy a lehető leghamarabb hozza létre ezt a beállítást, mert ezek a naplók alapértelmezés szerint nem kerülnek gyűjtésre, és anélkül, hogy az eszköz leválasztásához nem fog tudni adatokat elhárítani.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Keresse meg az IoT hubot.

1. Válassza a **diagnosztikai beállítások** lehetőséget.

1. Válassza a **diagnosztikai beállítás hozzáadása** lehetőséget.

1. Válassza a **kapcsolatok** naplók lehetőséget.

1. A könnyebb elemzéshez válassza a **küldés log Analyticsra** ( [lásd: díjszabás](https://azure.microsoft.com/pricing/details/log-analytics/)). A [kapcsolódási hibák elhárítása](#resolve-connectivity-errors)című témakörben talál példát.

   ![Ajánlott beállítások](./media/iot-hub-troubleshoot-connectivity/diagnostic-settings-recommendation.png)

További információért lásd: [IoT hub figyelése](monitor-iot-hub.md).

### <a name="set-up-alerts-for-device-disconnect-at-scale"></a>Riasztások beállítása az eszköz leválasztásához a skálán

Ha riasztásokat szeretne kapni az eszközök leválasztásakor, konfigurálja a riasztásokat a **csatlakoztatott eszközök (előzetes verzió)** metrikában.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Keresse meg az IoT hubot.

3. Válassza a **riasztások** lehetőséget.

4. Válassza az **Új riasztási szabály** lehetőséget.

5. Válassza a **feltétel hozzáadása** , majd a csatlakoztatott eszközök (előzetes verzió) lehetőséget.

6. A küszöbértékek és a riasztások beállítása a következő utasításokat követve.

További információ: [Mik a riasztások a Microsoft Azure?](../azure-monitor/platform/alerts-overview.md).

#### <a name="detecting-individual-device-disconnects"></a>Az egyes eszközök leválasztásának észlelése

Az eszközön keresztüli leválasztások *észleléséhez* , például ha tudnia kell, hogy egy gyár offline állapotba került, [konfigurálja az eszköz leválasztási eseményeit Event Grid](iot-hub-event-grid.md).

## <a name="resolve-connectivity-errors"></a>Csatlakozási hibák elhárítása

Amikor bekapcsolja a naplókat és a riasztásokat a csatlakoztatott eszközökhöz, riasztást kap, ha hiba történik. Ez a szakasz azt ismerteti, hogyan lehet gyakori problémákat keresni a riasztások érkezésekor. Az alábbi lépések azt feltételezik, hogy már létrehozott egy diagnosztikai beállítást, amely IoT Hub kapcsolati naplókat küld egy Log Analytics munkaterületre.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Keresse meg az IoT hubot.

1. Válassza a **naplók** lehetőséget.

1. Ha el szeretné különíteni a IoT Hub csatlakozási naplófájljait, adja meg a következő lekérdezést, majd válassza a **Futtatás** lehetőséget:

    ```kusto
    AzureDiagnostics
    | where ( ResourceType == "IOTHUBS" and Category == "Connections" and Level == "Error")
    ```

1. Ha vannak találatok, keresse meg `OperationName` a `ResultType` (hibakód) és a `ResultDescription` (hibaüzenet), hogy további részleteket kapjon a hibáról.

   ![Hibanapló – példa](./media/iot-hub-troubleshoot-connectivity/diag-logs.png)

1. Kövesse a leggyakoribb hibák elhárítási útmutatóit:

    - **[404104 DeviceConnectionClosedRemotely](iot-hub-troubleshoot-error-404104-deviceconnectionclosedremotely.md)**
    - **[401003 IoTHubUnauthorized](iot-hub-troubleshoot-error-401003-iothubunauthorized.md)**
    - **[409002 LinkCreationConflict](iot-hub-troubleshoot-error-409002-linkcreationconflict.md)**
    - **[500001 ServerError](iot-hub-troubleshoot-error-500xxx-internal-errors.md)**
    - **[500008 GenericTimeout](iot-hub-troubleshoot-error-500xxx-internal-errors.md)**

## <a name="i-tried-the-steps-but-they-didnt-work"></a>Megpróbáltam a lépéseket, de nem működnek

Ha az előző lépések nem segítettek, próbálkozzon a következő lépésekkel:

* Ha rendelkezik hozzáféréssel a problémás eszközökhöz fizikailag vagy távolról (például SSH), kövesse az [eszközre vonatkozó hibaelhárítási útmutatót](https://github.com/Azure/azure-iot-sdk-node/wiki/Troubleshooting-Guide-Devices) a hibaelhárítás folytatásához.

* Ellenőrizze, hogy az eszközök **engedélyezve** vannak-e a Azure Portal > az IoT hub > IoT eszközöket.

* Ha az eszköz MQTT protokollt használ, ellenőrizze, hogy a 8883-es port nyitva van-e. További információ: [csatlakozás IoT hubhoz (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

* Segítség kérése a Microsoft Q&az Azure IoT Hub, a [stack overflow](https://stackoverflow.com/questions/tagged/azure-iot-hub)vagy az [Azure-támogatás](https://azure.microsoft.com/support/options/) [kérdéseit tartalmazó oldalon](/answers/topics/azure-iot-hub.html).

Ha segítségre van szüksége mindenki számára, az alábbi visszajelzések szakaszban hagyja meg a megjegyzéseit, ha az útmutató nem segít Önnek.

## <a name="next-steps"></a>Következő lépések

* Az átmeneti problémák megoldásával kapcsolatos további tudnivalókért lásd: az [átmeneti hibák kezelésének](/azure/architecture/best-practices/transient-faults)ismertetése.

* Ha többet szeretne megtudni az Azure IoT SDK-ról és az újrapróbálkozások kezeléséről, olvassa el a [kapcsolat és a megbízható üzenetkezelés kezelése az azure IoT hub Device SDK](iot-hub-reliability-features-in-sdks.md#connection-and-retry)-k használatával című témakört