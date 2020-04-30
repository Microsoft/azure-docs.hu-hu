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
ms.openlocfilehash: 373acc30ed652a7f540e840dfad5eeeda65ca179
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81759551"
---
# <a name="504101-gatewaytimeout"></a>504101 GatewayTimeout

Ez a cikk a **504101 GatewayTimeout** -hibák okait és megoldásait ismerteti.

## <a name="symptoms"></a>Probléma

Amikor egy közvetlen metódust próbál meg IoT Hubról egy eszközre meghívni, a kérelem a **504101 GatewayTimeout**hibával meghiúsul.

## <a name="cause"></a>Ok

### <a name="cause-1"></a>1. ok

IoT Hub hibát észlelt, és nem tudta megerősíteni, hogy a közvetlen metódus befejeződött-e az időtúllépés előtt.

### <a name="cause-2"></a>2. ok

Ha az Azure IoT C# SDK (<1.19.0) egy korábbi verzióját használja, akkor az eszköz és a IoT Hub közötti AMQP-kapcsolat egy hiba miatt csendesan elhagyható.

## <a name="solution"></a>Megoldás

### <a name="solution-1"></a>1. megoldás

Próbálkozzon újra.

### <a name="solution-2"></a>2. megoldás

Frissítsen az Azure IOT C# SDK legújabb verziójára.