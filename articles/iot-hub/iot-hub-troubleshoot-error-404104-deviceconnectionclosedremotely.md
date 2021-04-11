---
title: Az Azure IoT Hub hibáinak elhárítása 404104 DeviceConnectionClosedRemotely
description: Ismerje meg, hogyan javíthatja a 404104-es hibát a DeviceConnectionClosedRemotely
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.custom: mqtt
ms.openlocfilehash: f1acd7b309a31dfe437f1f82cb9bbf73f4a5cca5
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061077"
---
# <a name="404104-deviceconnectionclosedremotely"></a>404104 DeviceConnectionClosedRemotely

Ez a cikk a **404104 DeviceConnectionClosedRemotely** -hibák okait és megoldásait ismerteti.

## <a name="symptoms"></a>Hibajelenségek

### <a name="symptom-1"></a>1. tünet

Az eszközök rendszeres időközönként (65 percenként) leválasztják a leválasztást, és a **404104 DeviceConnectionClosedRemotely** IoT hub erőforrás-naplókban láthatja. Időnként a **401003 IoTHubUnauthorized** és a sikeres eszköz-kapcsolati esemény is látható, amely kevesebb, mint egy perc múlva.

### <a name="symptom-2"></a>2. tünet

Az eszközök véletlenszerűen bontja a kapcsolatot, és a IoT Hub erőforrás-naplókban a **404104 DeviceConnectionClosedRemotely** látható.

### <a name="symptom-3"></a>3. tünet

Számos eszköz bontja a kapcsolatot egyszerre, a [csatlakoztatott eszközök (connectedDeviceCount) metrikájában](monitor-iot-hub-reference.md)egy dip látható, és a szokásosnál több **404104 DeviceConnectionClosedRemotely** és [500xxx belső Azure monitor hiba](iot-hub-troubleshoot-error-500xxx-internal-errors.md) történik.

## <a name="causes"></a>Okok

### <a name="cause-1"></a>1\. ok

A IoT Hub lejártához való [csatlakozáshoz használt sas-jogkivonat](iot-hub-devguide-security.md#security-tokens) , amely IoT hub az eszköz leválasztását okozza. A kapcsolat újra létrejön, amikor az eszköz frissíti a tokent. Például [az SAS-jogkivonat minden órában lejár, alapértelmezés szerint a C SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-authentication)-hoz, ami rendszeres leválasztást eredményezhet.

További információ: [401003 IoTHubUnauthorized ok](iot-hub-troubleshoot-error-401003-iothubunauthorized.md#cause-1).

### <a name="cause-2"></a>2\. ok

Néhány lehetőség a következőkre terjed ki:

- Az eszköz a [MQTT](iot-hub-mqtt-support.md#default-keep-alive-timeout)hosszabb ideig megszakadt a mögöttes hálózati kapcsolaton, ami távoli üresjárati időkorlátot eredményez. Az MQTT Keep-Alive beállítás nem lehet más eszközönként.

- Az eszköz TCP/IP szintű alaphelyzetbe állítást küldött, de nem adott meg alkalmazás-szintet `MQTT DISCONNECT` . Alapvetően az eszköz hirtelen lezárta az alapul szolgáló szoftvercsatorna-kapcsolatát. Időnként ezt a problémát az Azure IoT SDK régebbi verzióiban felmerülő hibák okozzák.

- Az eszköz oldalsó alkalmazása összeomlott.

### <a name="cause-3"></a>3. ok

Előfordulhat, hogy a IoT Hub átmeneti problémákba ütközik. Lásd: [IoT hub belső kiszolgálóhiba oka](iot-hub-troubleshoot-error-500xxx-internal-errors.md#cause).

## <a name="solutions"></a>Megoldások

### <a name="solution-1"></a>1\. megoldás

Lásd: [401003 IoTHubUnauthorized-megoldás 1](iot-hub-troubleshoot-error-401003-iothubunauthorized.md#solution-1)

### <a name="solution-2"></a>2\. megoldás

- Ellenőrizze, hogy az eszköz rendelkezik-e megfelelő kapcsolattal IoT Hub a [kapcsolat tesztelésével](tutorial-connectivity.md). Ha a hálózat megbízhatatlan vagy időszakos, nem javasoljuk, hogy növelje a Keep-Alive értéket, mivel az észlelést eredményezhet (Azure Monitor riasztásokon keresztül, például). 

- Használja a [IoT SDK](iot-hub-devguide-sdks.md)-k legújabb verzióit.

### <a name="solution-3"></a>3\. megoldás

Tekintse [meg IoT hub belső kiszolgálói hibák megoldásait](iot-hub-troubleshoot-error-500xxx-internal-errors.md#solution).

## <a name="next-steps"></a>Következő lépések

Azt javasoljuk, hogy az Azure IoT-eszközök SDK-k használatával megbízhatóan kezelje a kapcsolatokat. További információ: [kapcsolat kezelése és megbízható üzenetkezelés az Azure IoT hub Device SDK](iot-hub-reliability-features-in-sdks.md) -k használatával