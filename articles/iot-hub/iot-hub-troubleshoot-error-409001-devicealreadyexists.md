---
title: Az Azure IoT Hub hibáinak elhárítása 409001 DeviceAlreadyExists
description: Ismerje meg, hogyan javíthatja a 409001-es hibát a DeviceAlreadyExists
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: b075519fff2c7c328778d770ef68e9751922807b
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061128"
---
# <a name="409001-devicealreadyexists"></a>409001 DeviceAlreadyExists

Ez a cikk a **409001 DeviceAlreadyExists** -hibák okait és megoldásait ismerteti.

## <a name="symptoms"></a>Hibajelenségek

Ha IoT Hub-ben próbál meg regisztrálni egy eszközt, a kérelem meghiúsul a 409001-es **DeviceAlreadyExists**.

## <a name="cause"></a>Ok

Már van egy eszköz ugyanazzal az AZONOSÍTÓval az IoT hub-ban. 

## <a name="solution"></a>Megoldás

Használjon másik eszköz-azonosítót, és próbálkozzon újra.