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
ms.openlocfilehash: 3a70c11fd4f2a0549370933c300f431a03ffcf1d
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061298"
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