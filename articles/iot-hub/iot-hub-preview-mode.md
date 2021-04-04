---
title: Az előnézeti mód bekapcsolása az Azure IoT Hub
description: Megtudhatja, hogyan kapcsolhatja be az előnézeti módot a IoT Hubhoz, és néhány figyelmeztetést
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 11/24/2020
ms.author: jlian
ms.openlocfilehash: 864870c4392b12477c321c86afd9da848120490c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96621705"
---
# <a name="turn-on-preview-mode-for-iot-hub-to-try-select-new-features"></a>Az előnézeti mód bekapcsolása IoT Hub az új funkciók kiválasztásához

<!-- 
- We are working hard to bring you new features
- Some of these features require a brand new iot hub with preview mode on
- some features may not work at all or have unexpected behavior
- "Normal preview features" do NOT require preview mode 
- Support opt-in at creation time only
- Customer cannot opt back out post creation
- If customer wants to evaluate, they must use new hub dedicated for the preview
- Banners, documentations and all materials indicate preview quality: no GA guarantee at all
-->

Az új funkciók nyilvános előzetes verzióban érhetők el a IoT Hubhoz, beleértve a következőket:

- 5. MQTT
- ECC-kiszolgáló tanúsítványa
- TLS-töredék hosszának egyeztetése
- X509 HITELESÍTÉSSZOLGÁLTATÓI lánc támogatása HTTPS/websockethez

Ezek a szolgáltatások a IoT Hub protokoll és a hitelesítési rétegek fejlesztése, így csak az **új** IoT hub esetében érhetők el. Még *nem* állnak rendelkezésre a meglévő IoT hubok számára. A funkciók előzetes megtekintéséhez az IoT hub előnézeti módban kell létrehozni.

## <a name="turn-on-preview-mode-for-a-new-iot-hub"></a>Új IoT hub előnézeti mód bekapcsolása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Az Azure kezdőlapján kattintson a **+ erőforrás létrehozása** gombra, majd írja be a *IoT hub* **kifejezést a keresés a piactéren** mezőbe.

1. Válassza a **IoT hub** elemet a keresési eredmények közül, majd válassza a **Létrehozás** lehetőséget.

1. Az **alapvető beállítások** lapon végezze el a mezőket a [szokásos](iot-hub-create-through-portal.md) módon, a **régió** kivételével. Válasszon a következő régiók közül:
    
    - Az USA középső régiója
    - Nyugat-Európa
    - Délkelet-Ázsia

1. Válassza a **felügyelet** fület, majd bontsa ki a **Speciális beállítások** szakaszt.

1. Az **előnézet mód** mellett válassza **a** be lehetőséget. Olvassa el figyelmesen a figyelmeztetés szövegét.

    :::image type="content" source="media/iot-hub-preview-mode/turn-on-preview-mode-at-create.png" alt-text="Az új IoT hub létrehozásakor az előnézeti mód lehetőség kiválasztásának helyét bemutató kép":::

1. Válassza a **Next (tovább): felülvizsgálat + létrehozás**, majd a **Létrehozás** lehetőséget.

A létrehozást követően az előnézeti módban lévő IoT Hub mindig ezt a szalagcímet jeleníti meg, így a IoT hub csak előzetes verzióként használható: 

:::image type="content" source="media/iot-hub-preview-mode/banner.png" alt-text="Az előnézeti mód IoT hub szalagcímét ábrázoló kép":::

## <a name="using-an-iot-hub-in-preview-mode"></a>IoT hub használata előnézeti módban

Az előnézeti módban *ne* használjon IoT hubot éles környezetben. Az előnézeti mód *csak* a lap tetején felsorolt szolgáltatások kiválasztására szolgál. Az előnézeti mód IoT Hub egyéb korlátozásai

- Bizonyos meglévő IoT Hub szolgáltatások, például az IP-szűrők, a magánhálózati kapcsolatok, a felügyelt identitások, az eszköz-adatfolyamok és a feladatátvétel nem várt módon működhetnek, vagy egyáltalán nem.
- Az IoT hub előnézeti módban nem módosítható és nem frissíthető normál IoT-hubhoz.
- Nem tudjuk garantálni, hogy a normál [IOT hub SLA](https://azure.microsoft.com/support/legal/sla/iot-hub/v1_2/) – nem használható éles környezetben.

> [!TIP]
> Az [eszköz-adatfolyamok](iot-hub-device-streams-overview.md) és az [elosztott nyomkövetés](iot-hub-distributed-tracing.md)esetében nem szükséges az előnézet mód használata. A régebbi előzetes verziójú funkciók használatához a szokásos módon végezze el a dokumentációt. 

## <a name="next-steps"></a>Következő lépések

- A MQTT 5 támogatásának megtekintéséhez lásd: [IOT hub MQTT 5 támogatás áttekintése (előzetes verzió)](iot-hub-mqtt-5.md)
- Az ECC-kiszolgáló tanúsítványának megtekintéséhez lásd: [elliptikus görbe titkosítási (ECC) kiszolgáló TLS-tanúsítványa (előzetes verzió)](iot-hub-tls-support.md#elliptic-curve-cryptography-ecc-server-tls-certificate-preview)
- A TLS-töredékek méretének egyeztetéséhez lásd: a [TLS-töredék hosszának egyeztetése (előzetes verzió)](iot-hub-tls-support.md#tls-maximum-fragment-length-negotiation-preview)