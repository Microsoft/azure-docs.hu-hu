---
title: Az Azure IoT Hub hibáinak elhárítása 504101 GatewayTimeout
description: Ismerje meg, hogyan javíthatja a 504101-es hibát a GatewayTimeout
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.custom: amqp
ms.openlocfilehash: 5d5962c43a8a3cd97b31ad3fa50bce774e2626cb
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106060941"
---
# <a name="504101-gatewaytimeout"></a>504101 GatewayTimeout

Ez a cikk a **504101 GatewayTimeout** -hibák okait és megoldásait ismerteti.

## <a name="symptoms"></a>Hibajelenségek

Amikor egy közvetlen metódust próbál meg IoT Hubról egy eszközre meghívni, a kérelem a **504101 GatewayTimeout** hibával meghiúsul.

## <a name="cause"></a>Ok

### <a name="cause-1"></a>1\. ok

IoT Hub hibát észlelt, és nem tudta megerősíteni, hogy a közvetlen metódus befejeződött-e az időtúllépés előtt.

### <a name="cause-2"></a>2\. ok

Ha az Azure IoT C# SDK (<1.19.0) egy korábbi verzióját használja, akkor az eszköz és a IoT Hub közötti AMQP-kapcsolat egy hiba miatt csendesan elhagyható.

## <a name="solution"></a>Megoldás

### <a name="solution-1"></a>1\. megoldás

Próbálkozzon újra.

### <a name="solution-2"></a>2\. megoldás

Frissítsen az Azure IOT C# SDK legújabb verziójára.