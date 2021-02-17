---
title: Az Azure IoT Hub-kapcsolat figyelése és hibakeresése
description: Megtudhatja, hogyan figyelheti és elháríthatja az Azure-IoT Hub eszköz-kapcsolattal kapcsolatos gyakori hibákat
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/06/2020
ms.author: jlian
ms.custom:
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
- 'Role: Technical Support'
- fasttrack-edit
- iot
ms.openlocfilehash: 8bd20e3c7207c75e87a2132fca89906885de2676
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100579251"
---
# <a name="monitor-diagnose-and-troubleshoot-disconnects-with-azure-iot-hub"></a>Az Azure IoT Hub-vel való kapcsolat figyelése, diagnosztizálása és hibakeresése

A IoT-eszközök kapcsolódási problémái nehézkesek lehetnek a hibaelhárításhoz, mert számos lehetséges meghibásodási pont áll rendelkezésre. Az alkalmazás logikája, a fizikai hálózatok, a protokollok, a hardverek, a IoT Hub és más felhőalapú szolgáltatások okozhatnak problémákat. Kritikus fontosságú a probléma forrásának észlelése és felderítése. Egy IoT megoldás azonban több ezer eszközzel is rendelkezhet, ezért nem célszerű manuálisan megnézni az egyes eszközöket. A IoT Hub két Azure-szolgáltatással integrálható:

* **Azure monitor** Ezen problémák nagy léptékű észlelése, diagnosztizálása és hibaelhárítása érdekében használja a figyelési képességek IoT Hub a Azure Monitoron keresztül. Ez magában foglalja a riasztások beállítását az értesítések és műveletek elindításához, amikor a kapcsolat megszakad, és konfigurálja azokat a naplókat, amelyeket felhasználhat a leválasztást okozó feltételek felderítésére.

* **Azure Event Grid** A kritikus infrastruktúrához és az eszközönkénti leválasztáshoz használja a Azure Event Grid az eszköz csatlakoztatására és a IoT Hub által kibocsátott események leválasztására.

Ezek a képességek mindkét esetben a IoT Hub megfigyelésére korlátozódnak, ezért javasoljuk, hogy kövesse az eszközökhöz és más Azure-szolgáltatásokhoz kapcsolódó ajánlott eljárások nyomon követését is.

## <a name="event-grid-vs-azure-monitor"></a>Event Grid vs Azure Monitor

A Event Grid egy kis késleltetésű, eszközönkénti figyelési megoldást biztosít, amellyel nyomon követheti az eszközök kapcsolatait a kritikus fontosságú eszközök és infrastruktúra számára. A Azure Monitor metrikát, *csatlakoztatott eszközöket* biztosít, amelyek segítségével figyelheti a IoT hubhoz csatlakoztatott eszközök számát, és riasztást indíthat, ha a szám a statikus küszöbérték alá esik.

Vegye figyelembe a következőket, amikor eldönti, hogy Event Grid vagy Azure Monitor kíván-e használni egy adott forgatókönyvhöz:

* Riasztási késés: IoT Hub a kapcsolatok eseményei sokkal gyorsabban érkeznek Event Gridon keresztül. Ez Event Grid jobb választás olyan forgatókönyvek esetén, ahol a gyors értesítés kívánatos.

* Eszközönkénti értesítések: Event Grid lehetővé teszi a csatlakozások nyomon követését és az egyes eszközök kapcsolatának leválasztását. Ez Event Grid jobb választás olyan helyzetekben, amikor a kritikus fontosságú eszközök kapcsolatainak figyelésére van szükség.

* Könnyű beállítás: Azure Monitor metrikus riasztások olyan egyszerű telepítési élményt nyújtanak, amely nem igényli más szolgáltatásokkal való integrációt, hogy e-mailben, SMS-ben, hangon és más értesítéseken keresztül továbbítsa az értesítéseket.  A Event Grid használatával integrálni kell más Azure-szolgáltatásokkal az értesítések kézbesítéséhez. Mindkét szolgáltatás integrálható más szolgáltatásokkal az összetettebb műveletek elindításához.

Az alacsony késésű, eszközönkénti képességek miatt éles környezetekben javasoljuk, hogy Event Grid használatával figyelje a kapcsolatokat. Természetesen a választás nem kizárólagos, Azure Monitor metrikai riasztásokat és Event Grid is használhat. Függetlenül attól, hogy az Ön által választott leválasztást választja-e, valószínűleg Azure Monitor erőforrás-naplókat fog használni az eszköz váratlan leválasztásának okainak megoldásához. A következő részek részletesebben ismertetik ezeket a lehetőségeket.

## <a name="event-grid-monitor-device-connect-and-disconnect-events"></a>Event Grid: az eszközök csatlakoztatásának figyelése és az események leválasztása

Ha figyelni szeretné az eszköz csatlakoztatását, és leválasztja az eseményeket az éles környezetben, javasoljuk, hogy a riasztások elindításához és az eszköz kapcsolati állapotának figyeléséhez az Event Grid [ **DeviceConnected** és **DeviceDisconnected** eseményeit](iot-hub-event-grid.md#event-types) is A Event Grid sokkal alacsonyabb az esemény késése, mint Azure Monitor, és a csatlakoztatott eszközök teljes száma helyett a figyelést eszközönkénti alapon végezheti el. Ezek a tényezők Event Grid a kritikus fontosságú eszközök és infrastruktúra figyelésére szolgáló előnyben részesített módszert.

Ha Event Grid használatával figyeli vagy elindítja a riasztásokat az eszköz leválasztásakor, ügyeljen arra, hogy az Azure IoT SDK-kat használó eszközökön a SAS-jogkivonat megújítása miatt kiszűrje az időszakos leválasztásokat. További információ: MQTT- [eszköz leválasztási viselkedése az Azure IoT SDK](#mqtt-device-disconnect-behavior-with-azure-iot-sdks)-k használatával.

A következő témakörökből megtudhatja, hogyan figyelheti meg az eszközök kapcsolódási eseményeit Event Grid használatával:

* A Event Grid és a IoT Hub használatának áttekintését lásd: [IoT hub eseményekre való reagálás Event Grid](iot-hub-event-grid.md). Fordítson különös figyelmet a [csatlakoztatott eszközökre és az eszközről leválasztott események szakaszra vonatkozó korlátozásokra](iot-hub-event-grid.md#limitations-for-device-connected-and-device-disconnected-events) .

* Az eszköz-csatlakoztatási események megrendelésével kapcsolatos oktatóanyagért lásd: [eszközök csatlakoztatási eseményeinek megrendelése az Azure IoT Hub Azure Cosmos db használatával](iot-hub-how-to-order-connection-state-events.md).

* Az e-mail-értesítések küldésével kapcsolatos oktatóanyagért lásd: [e-mail-értesítések küldése az Azure IoT hub eseményekről az Event Grid dokumentációjának Event Grid és Logic Apps használatával](../event-grid/publish-iot-hub-events-to-logic-apps.md) .

## <a name="azure-monitor-route-connection-events-to-logs"></a>Azure Monitor: kapcsolódási események továbbítása a naplókhoz

Az IoT hub a műveletek számos kategóriájára vonatkozóan folyamatosan bocsát ki erőforrás-naplókat. A naplózási adatok összegyűjtéséhez létre kell hoznia egy diagnosztikai beállítást, amely azt a célhelyre irányítja, ahol elemezni vagy archiválni lehet. Az egyik ilyen cél Azure Monitor naplók egy Log Analytics-munkaterületen ([lásd a díjszabást](https://azure.microsoft.com/pricing/details/log-analytics/)), ahol az Kusto-lekérdezések használatával elemezheti az adatforrásokat.

A IoT Hub [erőforrás-naplók kapcsolatok kategóriája](monitor-iot-hub-reference.md#connections) az eszközök kapcsolataival kapcsolatos műveleteket és hibákat bocsát ki. A következő képernyőfelvétel egy diagnosztikai beállítást mutat be, amely a naplókat Log Analytics munkaterületre irányítja:

:::image type="content" source="media/iot-hub-troubleshoot-connectivity/create-diagnostic-setting.png" alt-text="A kapcsolati naplók Log Analytics munkaterületre való küldéséhez javasolt beállítás.":::

Azt javasoljuk, hogy a lehető leghamarabb hozzon létre egy diagnosztikai beállítást az IoT hub létrehozása után, mert bár a IoT Hub mindig az erőforrás-naplókat bocsátja ki, a Azure Monitor addig nem gyűjti őket, amíg nem továbbítja őket a célhelyre.

Ha többet szeretne megtudni az útválasztási naplókról a célhelyre, tekintse meg a [gyűjtemény és az Útválasztás](monitor-iot-hub.md#collection-and-routing)című témakört. A diagnosztikai beállítások létrehozásával kapcsolatos részletes utasításokért tekintse meg a [metrikák és naplók használata oktatóanyagot](tutorial-use-metrics-and-diags.md).

## <a name="azure-monitor-set-up-metric-alerts-for-device-disconnect-at-scale"></a>Azure Monitor: metrikai riasztások beállítása az eszköz leválasztásához méretezéskor

A riasztásokat a IoT Hub által kibocsátott platform-mérőszámok alapján állíthatja be. A metrikai riasztások segítségével az érintett személyeket értesítheti, hogy a rendszer feltételt észlelt, és aktiválja azokat a műveleteket, amelyek automatikusan reagálnak erre az állapotra.

A [*csatlakoztatott eszközök (előzetes verzió)*](monitor-iot-hub-reference.md#device-metrics) mérőszáma jelzi, hogy hány eszköz csatlakozik a IoT hubhoz. Riasztásokat hozhat létre, amelyek akkor aktiválódnak, ha ez a metrika a küszöbérték alá csökken:

:::image type="content" source="media/iot-hub-troubleshoot-connectivity/configure-alert-logic.png" alt-text="Riasztási logikai beállítások a csatlakoztatott eszközök mérőszámához.":::

Metrikus riasztási szabályok segítségével figyelheti az eszköz leválasztási rendellenességeit a skálán. Azaz, ha jelentős számú eszköz váratlanul leválasztja a kapcsolatot. Ha a rendszer ilyen eseményt észlel, megtekintheti a naplókat, hogy segítsen a probléma megoldásában. Az eszközön belüli leválasztások figyelése és a kritikus fontosságú eszközök leválasztása; azonban a Event Gridt kell használnia. A Event Grid az Azure-metrikák valós idejű élményét is biztosítja.

Ha többet szeretne megtudni a IoT Hubrel kapcsolatos riasztásokról, tekintse meg a [figyelő IoT hub riasztások](monitor-iot-hub.md#alerts)című témakört. A riasztások IoT Hubban való létrehozásának lépéseiért tekintse meg a [metrikák és naplók használata oktatóanyagot](tutorial-use-metrics-and-diags.md). A riasztások részletesebb áttekintését lásd: a [Microsoft Azure riasztások áttekintése](../azure-monitor/alerts/alerts-overview.md) a Azure monitor dokumentációjában.

## <a name="azure-monitor-use-logs-to-resolve-connectivity-errors"></a>Azure Monitor: kapcsolódási hibák elhárítása naplók használatával

Ha az eszköz leválasztását felismeri, legyen szó Azure Monitor metrikai riasztásokról vagy a Event Grid, akkor a naplók segítségével elháríthatja az okát. Ez a szakasz azt ismerteti, hogyan lehet megkeresni a gyakori problémákat Azure Monitor naplókban. Az alábbi lépések azt feltételezik, hogy már létrehozott egy [diagnosztikai beállítást](#azure-monitor-route-connection-events-to-logs) , amely IoT hub kapcsolati naplókat küld egy log Analytics munkaterületre.

Miután létrehozott egy diagnosztikai beállítást IoT Hub erőforrás-naplók átirányításához Azure Monitor naplókra, az alábbi lépéseket követve megtekintheti a naplókat Azure Portal.

1. Navigáljon a IoT hubhoz [Azure Portal](https://portal.azure.com).

1. Az IoT hub bal oldali paneljének **figyelés** területén válassza a **naplók** lehetőséget.

1. Ha el szeretné különíteni a IoT Hub csatlakozási naplófájljait, adja meg a következő lekérdezést a lekérdezés-szerkesztőben, majd válassza a **Futtatás** lehetőséget:

    ```kusto
    AzureDiagnostics
    | where ( ResourceType == "IOTHUBS" and Category == "Connections" and Level == "Error")
    ```

1. Ha vannak találatok, keresse meg `OperationName` a `ResultType` (hibakód) és a `ResultDescription` (hibaüzenet), hogy további részleteket kapjon a hibáról.

   ![Hibanapló – példa](./media/iot-hub-troubleshoot-connectivity/diag-logs.png)

A hiba azonosítása után kövesse a probléma megoldási útmutatóit a leggyakoribb hibákkal kapcsolatos segítségért:

* [400027 ConnectionForcefullyClosedOnNewConnection](iot-hub-troubleshoot-error-400027-connectionforcefullyclosedonnewconnection.md)

* [404104 DeviceConnectionClosedRemotely](iot-hub-troubleshoot-error-404104-deviceconnectionclosedremotely.md)

* [401003 IoTHubUnauthorized](iot-hub-troubleshoot-error-401003-iothubunauthorized.md)

* [409002 LinkCreationConflict](iot-hub-troubleshoot-error-409002-linkcreationconflict.md)

* [500001 ServerError](iot-hub-troubleshoot-error-500xxx-internal-errors.md)

* [500008 GenericTimeout](iot-hub-troubleshoot-error-500xxx-internal-errors.md)

## <a name="mqtt-device-disconnect-behavior-with-azure-iot-sdks"></a>MQTT-eszköz leválasztása az Azure IoT SDK-val

Az Azure IoT-eszközök SDK-k leválasztása IoT Hubról, majd újracsatlakozás, amikor megújítják az SAS-jogkivonatokat a MQTT (és a MQTT over WebSockets) protokollon keresztül. A naplókban ez a tájékoztató eszköz leválasztása, valamint a kapcsolódási események időnként a hibákkal együtt történő összekapcsolását mutatja.

Alapértelmezés szerint a jogkivonat élettartama 60 perc az összes SDK esetében; néhány SDK-ban azonban a fejlesztők módosíthatják azt. Az alábbi táblázat összefoglalja a jogkivonat élettartamát, a jogkivonat-megújítást és az egyes SDK-k jogkivonat-megújítási viselkedését:

| SDK | Jogkivonat élettartama | Jogkivonat megújítása | Megújítási viselkedés |
|-----|----------|---------------------|---------|
| .NET | 60 perc, konfigurálható | az élettartam 85%-a, konfigurálható | Az SDK csatlakoztatja és leválasztja a token élettartamát, valamint egy 10 perces türelmi időszakot. A naplókban létrehozott tájékoztató események és hibák. |
| Java | 60 perc, konfigurálható | az élettartam 85%-a nem konfigurálható | Az SDK csatlakoztatja és leválasztja a token élettartamát, valamint egy 10 perces türelmi időszakot. A naplókban létrehozott tájékoztató események és hibák. |
| Node.js | 60 perc, konfigurálható | konfigurálható | Az SDK csatlakoztatja és leválasztja a jogkivonat-megújítást. Csak tájékoztató események jönnek létre a naplókban. |
| Python | 60 perc, nem konfigurálható | -- | Az SDK csatlakoztatja és leválasztja a jogkivonat élettartamát. |

A következő Képernyőképek a jogkivonat-megújítási viselkedést mutatják be a különböző SDK-k Azure Monitor naplófájljaiban. A jogkivonat élettartama és megújítási küszöbértéke módosult az alapértelmezett értékektől a feljegyzett értéktől számítva.

* A .NET-eszköz SDK egy 1200 másodperces (20 perces) jogkivonat élettartama és megújítása az élettartam 90%-ában történik. a kapcsolat bontása 30 percenként történik:

    :::image type="content" source="media/iot-hub-troubleshoot-connectivity/net-mqtt.png" alt-text="Hiba történt a token megújításakor a MQTT Azure Monitor-naplókban a .NET SDK-val.":::

* Java SDK a 300 másodperces (5 perces) jogkivonat élettartama és az alapértelmezett 85%-os élettartam megújítása esetén. A leválasztás 15 percenként történik:

    :::image type="content" source="media/iot-hub-troubleshoot-connectivity/java-mqtt.png" alt-text="Hiba történt a token megújításakor a MQTT Azure Monitor-naplókban Java SDK-val.":::

* A Node SDK egy 300 másodperces (5 perces) jogkivonat élettartama és token-megújítása 3 percen belül megtörténik. A kapcsolat bontása a jogkivonat megújításakor történik. Továbbá nincsenek hibák, csak a tájékoztató kapcsolódási/leválasztási események vannak kibocsátva:

    :::image type="content" source="media/iot-hub-troubleshoot-connectivity/node-mqtt.png" alt-text="Hiba történt a token megújításakor a MQTT Azure Monitor-naplókban a Node SDK-val.":::

Az eredmények gyűjtésére az alábbi lekérdezés szolgál. A lekérdezés kibontja az SDK nevét és verzióját a tulajdonság táskából; További információ: [az SDK verziója IoT hub naplókban](monitor-iot-hub.md#sdk-version-in-iot-hub-logs).

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
| where Category == "Connections"
| extend parsed_json = parse_json(properties_s)
| extend SDKVersion = tostring(parsed_json.sdkVersion) , DeviceId = tostring(parsed_json.deviceId) , Protocol =  tostring(parsed_json.protocol)
| distinct TimeGenerated, OperationName, Level, ResultType, ResultDescription, DeviceId, Protocol, SDKVersion

```

A IoT-megoldások fejlesztője vagy kezelője számára fontos tisztában lennie azzal, hogy a kapcsolódási/leválasztási eseményeket és a naplókban a kapcsolódó hibákat értelmezi. Ha módosítani szeretné a jogkivonat élettartamát vagy megújítási viselkedését az eszközökön, ellenőrizze, hogy az eszköz megvalósítja-e az eszköz kettős beállítását vagy egy eszköz módszerét, amely ezt lehetővé teszi.

Ha az Event hub használatával figyeli az eszközök kapcsolatait, győződjön meg arról, hogy az SAS-jogkivonat megújítása miatt kiszűri az időszakos leválasztásokat. például azáltal, hogy nem indít el műveleteket a leválasztások alapján, feltéve, hogy a leválasztási eseményt egy adott időtartományon belül egy csatlakozási esemény követi.

> [!NOTE]
> A IoT Hub eszközön csak egy aktív MQTT-kapcsolatok támogatottak. Az azonos eszköz AZONOSÍTÓjának nevében az új MQTT-kapcsolatok IoT Hub a meglévő kapcsolatok eldobásához.
>
> 400027 a ConnectionForcefullyClosedOnNewConnection bekerül IoT Hub naplókba

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