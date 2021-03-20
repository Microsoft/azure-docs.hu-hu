---
title: Az Azure IoT Hub hibáinak elhárítása 403006 DeviceMaximumActiveFileUploadLimitExceeded
description: Ismerje meg, hogyan javíthatja a 403006-es hibát a DeviceMaximumActiveFileUploadLimitExceeded
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 1e3c05e4cc3ccf34573b55d3729aded16e26d66e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "76960840"
---
# <a name="403006-devicemaximumactivefileuploadlimitexceeded"></a>403006 DeviceMaximumActiveFileUploadLimitExceeded

Ez a cikk a **403006 DeviceMaximumActiveFileUploadLimitExceeded** -hibák okait és megoldásait ismerteti.

## <a name="symptoms"></a>Hibajelenségek

A fájlfeltöltés kérelme meghiúsul a **403006** hibakódnál, és egy üzenet, amely szerint az "aktív fájlfeltöltés-kérelmek száma nem haladhatja meg a 10" üzenetet.

## <a name="cause"></a>Ok

Minden eszköz-ügyfél [10 egyidejű fájl feltöltésére](./iot-hub-devguide-quotas-throttling.md#other-limits)van korlátozva. 

Ha az eszköz nem értesíti IoT Hub a fájlfeltöltés befejezésekor, könnyedén meghaladhatja a korlátot. Ezt a problémát általában egy megbízhatatlan eszköz-hálózat okozza.

## <a name="solution"></a>Megoldás

Győződjön meg arról, hogy az eszköz azonnal [értesíti IoT hub fájlfeltöltés befejezéséről](./iot-hub-devguide-file-upload.md#notify-iot-hub-of-a-completed-file-upload). Ezután próbálja meg [csökkenteni az SAS-JOGKIVONAT TTL-fájl feltöltési konfigurációját](iot-hub-configure-file-upload.md).

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a fájlok feltöltéséről, tekintse meg a [fájlok feltöltése a IoT hub](./iot-hub-devguide-file-upload.md) [használatával és IoT hub fájlfeltöltés konfigurálása a Azure Portal segítségével](./iot-hub-configure-file-upload.md)című témakört.