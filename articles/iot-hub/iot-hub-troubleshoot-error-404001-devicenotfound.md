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
ms.openlocfilehash: 15aa21c2ec2c11bb251f7208fd22c92ceb859d6d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "76960827"
---
# <a name="404001-devicenotfound"></a>404001 DeviceNotFound

Ez a cikk a **404001 DeviceNotFound** -hibák okait és megoldásait ismerteti.

## <a name="symptoms"></a>Probléma

A felhőből az eszközre irányuló (C2D) kommunikáció során a művelet meghiúsul, például a C2D-üzenet, a Twin Update vagy a Direct metódus, a 404001-as **DeviceNotFound**hiba miatt.

## <a name="cause"></a>Ok

A művelet nem sikerült, mert IoT Hub nem található az eszköz. Az eszköz nincs regisztrálva vagy letiltva.

## <a name="solution"></a>Megoldás

Regisztrálja a használt eszköz AZONOSÍTÓját, és próbálkozzon újra.