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
ms.openlocfilehash: 93ab2ecc8e820c461a7c79082ac1d50c24f0ba8b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "76960788"
---
# <a name="409001-devicealreadyexists"></a>409001 DeviceAlreadyExists

Ez a cikk a **409001 DeviceAlreadyExists** -hibák okait és megoldásait ismerteti.

## <a name="symptoms"></a>Hibajelenségek

Ha IoT Hub-ben próbál meg regisztrálni egy eszközt, a kérelem meghiúsul a 409001-es **DeviceAlreadyExists**.

## <a name="cause"></a>Ok

Már van egy eszköz ugyanazzal az AZONOSÍTÓval az IoT hub-ban. 

## <a name="solution"></a>Megoldás

Használjon másik eszköz-azonosítót, és próbálkozzon újra.