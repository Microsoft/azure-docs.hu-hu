---
title: A Defender IoT-ügynökön alapuló megoldás beléptetése
description: Ismerje meg, hogyan hozhatja be és engedélyezheti a Defender for IoT biztonsági szolgáltatást az Azure IoT Hubban.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/20/2021
ms.author: shhazam
ms.openlocfilehash: 127e439a7740cb97cbe126071aaaa5245cd85782
ms.sourcegitcommit: 4784fbba18bab59b203734b6e3a4d62d1dadf031
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2021
ms.locfileid: "99809133"
---
# <a name="onboard-to-defender-for-iot-agent-based-solution"></a>A Defender IoT-ügynökön alapuló megoldás beléptetése

Ez a cikk azt ismerteti, hogyan engedélyezheti a Defender for IoT szolgáltatást a meglévő IoT Hub. Ha jelenleg nem rendelkezik IoT Hubval, a kezdéshez tekintse meg [a IoT hub létrehozása a Azure Portal használatával](../iot-hub/iot-hub-create-through-portal.md) című témakört.

A IoT biztonságát IoT Hub a Defender IoT szolgáltatásán keresztül kezelheti. A IoT Hub található felügyeleti portál lehetővé teszi a következőket: 

- IoT Hub biztonság kezelése.

- Egy IoT-eszköz biztonságának alapszintű felügyelete az ügynök telepítése nélkül a IoT Hub telemetria alapján. 

- Speciális felügyelet a Micro-ügynökön alapuló IoT-eszközök biztonsága szempontjából.

> [!NOTE]
> A IoT Defender jelenleg csak a standard szintű IoT hubokat támogatja.

## <a name="onboard-to-defender-for-iot-in-iot-hub"></a>IoT Hub-beli IoT beléptetése a Defenderbe

Az új IoT-hubok esetében a Defender for IoT alapértelmezés szerint be van **kapcsolva** . A IoT Hub-létrehozási folyamat során ellenőrizheti, hogy az IoT Defender be van-e **kapcsolva** .

Annak ellenőrzése, hogy a váltógomb be van **-e állítva:**

1. Lépjen az Azure Portalra.

1. Válassza ki **IoT hub** az Azure-szolgáltatások listájából.

1. Válassza a **Létrehozás** lehetőséget.

    :::image type="content" source="media/quickstart-onboard-iot-hub/create-iot-hub.png" alt-text="Válassza a létrehozás gombot a felső eszköztáron." lightbox="media/quickstart-onboard-iot-hub/create-iot-hub-expanded.png":::

1. Válassza a **felügyelet** fület, és ellenőrizze, hogy a **Defender for IoT** váltógomb be értékre van-e **állítva.**

    :::image type="content" source="media/quickstart-onboard-iot-hub/management-tab.png" alt-text="Győződjön meg arról, hogy a Defender for IoT váltógomb be értékre van állítva.":::

## <a name="onboard-defender-for-iot-to-an-existing-iot-hub"></a>A Defender IoT meglévő IoT Hub

A szolgáltatás elindításához a következő lépéseket követve figyelheti az eszköz Identitáskezelés-felügyeletét, az eszközt a felhőbe és a felhőben az eszköz kommunikációs mintáit: 

1. Navigáljon IoT Hub. 

1. Válassza a **Biztonság áttekintése**   menüt. 

1. Kattintson a IoT-megoldás biztonságossá tétele lehetőségre, és fejezze be a bevezetési űrlapot. 


## <a name="next-steps"></a>Következő lépések

Folytassa a következő cikkel a megoldás konfigurálásához...

> [!div class="nextstepaction"]
> [Defender IOT Micro Agent-modul létrehozása twin (előzetes verzió)](quickstart-create-micro-agent-module-twin.md)
