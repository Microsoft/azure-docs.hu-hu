---
title: Az Azure IoT Hub Operations monitoring migrálása IoT Hub erőforrás-naplókba Azure Monitorban | Microsoft Docs
description: Az Azure IoT Hub frissítése az IoT hub-beli műveletek állapotának valós idejű figyelésére az Operations monitoring helyett Azure Monitor használatával.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: kgremban
ms.openlocfilehash: 48b646881b12047b28490999a96326f6076af2c1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100591846"
---
# <a name="migrate-your-iot-hub-from-operations-monitoring-to-azure-monitor-resource-logs"></a>IoT Hub migrálása az Operations monitoring szolgáltatásból Azure Monitor erőforrás-naplókba

Azok az ügyfelek, akik a [műveletek figyelésével](iot-hub-operations-monitoring.md) követik a műveletek állapotát, IoT hub áttelepíthetik ezt a munkafolyamatot [Azure monitor erőforrás-naplókra](../azure-monitor/essentials/platform-logs-overview.md), a Azure monitor egy funkciójának használatára. Az erőforrás-naplók számos Azure-szolgáltatás erőforrás-szintű diagnosztikai információit biztosítják.

**A IoT hub Operations monitoring funkciója elavult**, és el lett távolítva a portálról. Ez a cikk azokat a lépéseket ismerteti, amelyekkel áthelyezheti a munkaterheléseket az operatív figyelésből Azure Monitor erőforrás-naplókba. Az elavult idővonalról további információt az [Azure IoT-megoldások monitorozása Azure monitor és Azure Resource Health](https://azure.microsoft.com/blog/monitor-your-azure-iot-solutions-with-azure-monitor-and-azure-resource-health/)című témakörben talál.

## <a name="update-iot-hub"></a>IoT Hub frissítése

A Azure Portal IoT Hub frissítéséhez először hozzon létre egy diagnosztikai beállítást, majd kapcsolja ki a műveletek figyelését.  

### <a name="create-a--diagnostic-setting"></a>Diagnosztikai beállítás létrehozása

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és navigáljon az IoT hubhoz.

1. A bal oldali ablaktábla **figyelés** területén válassza a **diagnosztikai beállítások** elemet. Ezután válassza a **diagnosztikai beállítás hozzáadása** lehetőséget.

   :::image type="content" source="media/iot-hub-migrate-to-diagnostics-settings/open-diagnostic-settings.png" alt-text="Képernyőfelvétel: a figyelés szakasz diagnosztikai beállításainak kiemelése.":::

1. A **diagnosztikai beállítások** panelen adja meg a diagnosztikai beállítások nevét.

1. A **Kategória részletei** területen válassza ki a figyelni kívánt műveletek kategóriáit. További információ a IoT Hub által elérhető műveletek kategóriáról: [erőforrás-naplók](monitor-iot-hub-reference.md#resource-logs).

1. A **célhely részletei** területen válassza ki, hová szeretné elküldeni a naplókat. Kiválaszthatja a következő célhelyek tetszőleges kombinációját:

   * Archiválás tárfiókba
   * Streamelés eseményközpontba
   * Küldés Azure Monitor naplókba egy Log Analytics-munkaterületen keresztül

   A következő képernyőfelvétel egy diagnosztikai beállítást mutat be, amely a kapcsolatok és az eszközök telemetria-kategóriákban lévő műveleteket egy Log Analytics munkaterületre irányítja:

   :::image type="content" source="media/iot-hub-migrate-to-diagnostics-settings/add-diagnostic-setting.png" alt-text="A befejezett diagnosztikai beállításokat bemutató képernyőkép.":::

1. A beállítások mentéséhez válassza a **Mentés** lehetőséget.

Az új beállítások körülbelül 10 percen belül lépnek érvénybe. Ezt követően a naplók a konfigurált célhelyen jelennek meg. A diagnosztika konfigurálásával kapcsolatos további információkért lásd: [adatok gyűjtése és felhasználása az Azure-erőforrásokból](../azure-monitor/essentials/platform-logs-overview.md).

A diagnosztikai beállítások létrehozásával, beleértve a PowerShell-lel és az Azure CLI-vel kapcsolatos részletes információkat a Azure Monitor dokumentációjának [diagnosztikai beállítások](../azure-monitor/essentials/diagnostic-settings.md) című részében találja.

### <a name="turn-off-operations-monitoring"></a>Műveletek figyelésének kikapcsolása

> [!NOTE]
> 2019. március 11-én az Operations monitoring szolgáltatás el lett távolítva a IoT Hub Azure Portal felületéről. Az alábbi lépések már nem érvényesek. Az áttelepítéshez győződjön meg arról, hogy a megfelelő kategóriák átirányítva a célhelyre egy Azure Monitor diagnosztikai beállítással.

Ha teszteli az új diagnosztikai beállításokat a munkafolyamatban, kikapcsolhatja az Operations monitoring funkciót. 

1. A IoT Hub menüben válassza az **Operations monitoring** elemet.

2. Az egyes figyelési kategóriák területen válassza a **nincs** lehetőséget.

3. Mentse az Operations monitoring módosításait.

## <a name="update-applications-that-use-operations-monitoring"></a>Operations monitoringot használó alkalmazások frissítése

A műveletek figyeléséhez és az erőforrás-naplókhoz tartozó sémák némileg eltérőek. Fontos, hogy frissítse azokat az alkalmazásokat, amelyek az Operations monitoring szolgáltatást használják ma az erőforrás-naplók által használt sémához való leképezéshez.

Emellett IoT Hub erőforrás-naplók öt új kategóriát biztosítanak a nyomon követéshez. Miután frissítette az alkalmazásokat a meglévő sémához, adja hozzá az új kategóriákat is:

* A felhőből az eszközre irányuló kettős műveletek
* Az eszközről a felhőbe irányuló kettős művelet
* Dupla lekérdezés
* Feladatműveletek
* Közvetlen metódusok

Az adott séma-struktúrákkal kapcsolatban lásd: [erőforrás-naplók](monitor-iot-hub-reference.md#resource-logs).

## <a name="monitoring-device-connect-and-disconnect-events-with-low-latency"></a>Az eszközök csatlakoztatásának figyelése és az események leválasztása alacsony késéssel

Ha figyelni szeretné az eszköz csatlakoztatását, és leválasztja az eseményeket az éles üzemben, javasoljuk, hogy a riasztások beszerzéséhez és az eszköz kapcsolati állapotának figyeléséhez az eszközön [ **leválasztott** Event Grid eseményt](iot-hub-event-grid.md#event-types) . Ebből az [oktatóanyagból](iot-hub-how-to-order-connection-state-events.md) megtudhatja, hogyan integrálhatja a csatlakoztatott eszközök és az eszközök kapcsolatait a IoT-megoldás IoT hub.

## <a name="next-steps"></a>Következő lépések

[Az IoT Hub monitorozása](monitor-iot-hub.md)