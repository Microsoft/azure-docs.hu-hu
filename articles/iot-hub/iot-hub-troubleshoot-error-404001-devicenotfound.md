---
title: Az Azure IoT Hub hibáinak elhárítása 404001 DeviceNotFound
description: Ismerje meg, hogyan javíthatja a 404001-es hibát a DeviceNotFound
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 09f3c00dadc6e95aae01fb8082fb746e155d4688
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061281"
---
# <a name="404001-devicenotfound"></a>404001 DeviceNotFound

Ez a cikk a **404001 DeviceNotFound** -hibák okait és megoldásait ismerteti.

## <a name="symptoms"></a>Hibajelenségek

A felhőből az eszközre irányuló (C2D) kommunikáció során a művelet meghiúsul, például a C2D-üzenet, a Twin Update vagy a Direct metódus, a 404001-as **DeviceNotFound** hiba miatt.

## <a name="cause"></a>Ok

A művelet nem sikerült, mert IoT Hub nem található az eszköz. Az eszköz nincs regisztrálva vagy letiltva.

## <a name="solution"></a>Megoldás

Regisztrálja a használt eszköz AZONOSÍTÓját, és próbálkozzon újra.