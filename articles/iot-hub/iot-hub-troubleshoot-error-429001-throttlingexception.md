---
title: Az Azure IoT Hub hibáinak elhárítása 429001 ThrottlingException
description: Ismerje meg, hogyan javíthatja a 429001-es hibát a ThrottlingException
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: d2f12a6982886eeaa375151c5b8a73acc573aab9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92545361"
---
# <a name="429001-throttlingexception"></a>429001 ThrottlingException

Ez a cikk a **429001 ThrottlingException** -hibák okait és megoldásait ismerteti.

## <a name="symptoms"></a>Hibajelenségek

A IoT Hub-kérések sikertelenek lesznek a 429001-es **ThrottlingException** hiba esetén.

## <a name="cause"></a>Ok

Túllépte a kért művelet IoT Hub [szabályozási korlátait](./iot-hub-devguide-quotas-throttling.md) .

## <a name="solution"></a>Megoldás

Győződjön meg arról, hogy a sávszélesség-szabályozási korlátot a *telemetria üzenet küldési kísérletek* metrikájának a fent megadott korlátokkal való összehasonlításával éri el. Azt is megtekintheti, hogy a *sávszélesség-szabályozás hány* mérőszámot tartalmaz. További információ ezekről a metrikákkal kapcsolatban: [Device telemetria mérőszámok](monitor-iot-hub-reference.md#device-telemetry-metrics). További információ arról, hogyan használhatók a metrikák az IoT hub figyeléséhez: [IoT hub figyelése](monitor-iot-hub.md).

A IoT Hub a 429 ThrottlingException csak akkor adja vissza, ha a határérték túl hosszú ideig megsértette a korlátot. Erre azért van szükség, hogy az üzenetek ne legyenek elvetve, ha az IoT hub burst forgalmat kap. Eközben az IoT Hub a művelet sávszélesség-szabályozási értékén dolgozza fel az üzeneteket, ami lassú lehet, ha túl nagy a forgalom a várólistán. További tudnivalókért lásd [az IoT Hub adatforgalom-alakításával kapcsolatos részt](./iot-hub-devguide-quotas-throttling.md#traffic-shaping).

## <a name="next-steps"></a>Következő lépések

Ha kvóta-vagy sávszélesség-korlátozást használ, vegye fontolóra [a IoT hub skálázását](./iot-hub-scaling.md) .