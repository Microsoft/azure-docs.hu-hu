---
title: Az Azure IoT Hub hibáinak elhárítása 404103 DeviceNotOnline
description: Ismerje meg, hogyan javíthatja a 404103-es hibát a DeviceNotOnline
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: e648428f924cfc33421c8591c41f7ac85b05a033
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "76960814"
---
# <a name="404103-devicenotonline"></a>404103 DeviceNotOnline

Ez a cikk a **404103 DeviceNotOnline** -hibák okait és megoldásait ismerteti.

## <a name="symptoms"></a>Hibajelenségek

Az eszköz közvetlen metódusa meghiúsul, ha az eszköz online állapotban van, és a 404103-es **DeviceNotOnline** hiba történik. 

## <a name="cause"></a>Ok

Ha tudja, hogy az eszköz online állapotban van, és továbbra is hibaüzenetet kap, valószínű, hogy a közvetlen metódus visszahívása nincs regisztrálva az eszközön.

## <a name="solution"></a>Megoldás

Az eszköz megfelelő konfigurálásához a közvetlen metódus-visszahívások esetében lásd: [közvetlen metódus kezelése eszközön](iot-hub-devguide-direct-methods.md#handle-a-direct-method-on-a-device).