---
title: 'Rövid útmutató: Defender IoT-alapú megoldáshoz'
description: Ebből a rövid útmutatóból megtudhatja, hogyan lehet bevezetni és engedélyezni a Defender for IoT Security szolgáltatást az Azure IoT Hubban.
ms.topic: quickstart
ms.date: 1/20/2021
ms.openlocfilehash: 2704f8989ab39825cef052ca5556a2e6461efe75
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2021
ms.locfileid: "106384493"
---
# <a name="quickstart-onboard-defender-for-iot-to-an-agent-based-solution"></a>Rövid útmutató: Defender IoT-alapú megoldáshoz

Ez a cikk azt ismerteti, hogyan engedélyezheti a Defender for IoT szolgáltatást a meglévő IoT Hub. Ha jelenleg nem rendelkezik IoT Hubval, a kezdéshez tekintse meg [a IoT hub létrehozása a Azure Portal használatával](../iot-hub/iot-hub-create-through-portal.md) című témakört.

A IoT biztonságát IoT Hub a Defender IoT szolgáltatásán keresztül kezelheti. A IoT Hub található felügyeleti portál lehetővé teszi a következőket: 

- IoT Hub biztonság kezelése.

- Egy IoT-eszköz biztonságának alapszintű felügyelete az ügynök telepítése nélkül a IoT Hub telemetria alapján. 

- Speciális felügyelet a Micro-ügynökön alapuló IoT-eszközök biztonsága szempontjából.

> [!NOTE]
> A IoT Defender jelenleg csak a standard szintű IoT hubokat támogatja.

## <a name="prerequisites"></a>Előfeltételek

Nincsenek

## <a name="onboard-defender-for-iot-to-an-iot-hub"></a>Defender IoT IoT Hub

Az új IoT-hubok esetében a Defender for IoT alapértelmezés szerint be van **kapcsolva** . A IoT Hub-létrehozási folyamat során ellenőrizheti, hogy az IoT Defender be van-e **kapcsolva** .

Annak ellenőrzése, hogy a váltógomb be van **-e állítva:**

1. Lépjen az Azure Portalra.

1. Válassza ki **IoT hub** az Azure-szolgáltatások listájából.

1. Válassza a **Létrehozás** lehetőséget.

    :::image type="content" source="media/quickstart-onboard-iot-hub/create-iot-hub.png" alt-text="Válassza a létrehozás gombot a felső eszköztáron." lightbox="media/quickstart-onboard-iot-hub/create-iot-hub-expanded.png":::

1. Válassza a **felügyelet** fület, és ellenőrizze, hogy a **Defender for IoT** váltógomb be értékre van-e **állítva.**

    :::image type="content" source="media/quickstart-onboard-iot-hub/management-tab.png" alt-text="Győződjön meg arról, hogy a Defender for IoT váltógomb be értékre van állítva.":::

## <a name="onboard-defender-for-iot-to-an-existing-iot-hub"></a>A Defender IoT meglévő IoT Hub

A Defender a IoT egy meglévő IoT Hubba is bevezethető, ahol nyomon követheti az eszközök identitás-kezelését, az eszköz és a felhő közötti kommunikációt, valamint a felhőben az eszköz kommunikációs mintáit.

A Defender beléptetése a IoT meglévő IoT Hubba:

1. Navigáljon a IoT Hub. 

1. Válassza ki a bekészíteni kívánt IoT Hub.

1. Válassza a **Biztonság** szakaszban található bármelyik lehetőséget.

1. Kattintson a **IoT-megoldás biztonságossá tétele** lehetőségre   , és fejezze be a bevezetési űrlapot. 

    :::image type="content" source="media/quickstart-onboard-iot-hub/secure-your-iot-solution.png" alt-text="A megoldás biztonságossá tételéhez kattintson a IoT-megoldás biztonságossá tétele gombra.":::

A **IoT-megoldás biztonságossá tétele** gomb csak akkor jelenik meg, ha a IoT hub még nincs előkészítve, vagy ha bevezetéskor a Defender for IoT kapcsolót **kikapcsolta**.

:::image type="content" source="media/quickstart-onboard-iot-hub/toggle-is-off.png" alt-text="Ha a váltógomb be van kapcsolva a bevezetéskor.":::

## <a name="next-steps"></a>Következő lépések

Folytassa a következő cikkel a megoldás konfigurálásához...

> [!div class="nextstepaction"]
> [Defender IOT Micro Agent-modul létrehozása twin (előzetes verzió)](quickstart-create-micro-agent-module-twin.md)
