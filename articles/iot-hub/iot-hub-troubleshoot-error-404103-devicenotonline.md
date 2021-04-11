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
ms.openlocfilehash: ed4341c1c8df588bf735bdc9c531c2165514d610
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061264"
---
# <a name="404103-devicenotonline"></a>404103 DeviceNotOnline

Ez a cikk a **404103 DeviceNotOnline** -hibák okait és megoldásait ismerteti.

## <a name="symptoms"></a>Hibajelenségek

Az eszköz közvetlen metódusa meghiúsul, ha az eszköz online állapotban van, és a 404103-es **DeviceNotOnline** hiba történik. 

## <a name="cause"></a>Ok

Ha tudja, hogy az eszköz online állapotban van, és továbbra is hibaüzenetet kap, valószínű, hogy a közvetlen metódus visszahívása nincs regisztrálva az eszközön.

## <a name="solution"></a>Megoldás

Az eszköz megfelelő konfigurálásához a közvetlen metódus-visszahívások esetében lásd: [közvetlen metódus kezelése eszközön](iot-hub-devguide-direct-methods.md#handle-a-direct-method-on-a-device).