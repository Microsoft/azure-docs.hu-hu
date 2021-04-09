---
title: Defender-IoT-Micro-Agent és Device Twins
description: Ismerje meg a Defender-IoT-Micro-Agent ikrek fogalmát, valamint azt, hogyan használják őket a Defender for IoT.
ms.topic: conceptual
ms.date: 07/24/2019
ms.openlocfilehash: 1ed6faf03d168ed7a2a2f07733cb7e238d234915
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104779171"
---
# <a name="defender-iot-micro-agent"></a>Defender-IoT-Micro-Agent

Ez a cikk azt ismerteti, hogyan használja a Defender for IoT az eszköz-ikreket és modulokat.

## <a name="device-twins"></a>Eszköz ikrek

Az Azure-ban beépített IoT-megoldások esetében az eszközök az ikrek kulcsszerepet játszanak az eszközkezelés és a folyamatok automatizálása terén is.

A IoT Defender teljes körű integrációt biztosít a meglévő IoT-eszközkezelés platformmal, lehetővé téve az eszköz biztonsági állapotának kezelését, valamint a meglévő eszközök vezérlési képességeinek kihasználását. Az integráció a IoT Hub Twin mechanizmus használatával érhető el.

Tudjon meg többet az Azure IoT Hub [eszközön található ikrek](../iot-hub/iot-hub-devguide-device-twins.md) koncepciójának megismeréséről.

## <a name="defender-iot-micro-agent-twins"></a>Defender-IoT-Micro-Agent ikrek

A IoT Defender a szolgáltatás minden eszközén fenntart egy Defender-IoT-Micro-Agent-ügynököt.
A Defender-IoT-Micro-Agent Twin tartalmazza az eszköz biztonságával kapcsolatos összes információt a megoldásban.
Az eszköz biztonsági tulajdonságait egy dedikált Defender-IoT-Micro-Agent Twin biztosítja a biztonságos kommunikációhoz, és lehetővé teszi a kevesebb erőforrást igénylő frissítések és karbantartás engedélyezését.

Lásd: [Defender létrehozása – IoT-Micro-Agent Twin](quickstart-create-security-twin.md) és a [biztonsági ügynökök konfigurálása](how-to-agent-configuration.md) a Twin-elemek létrehozásának, testreszabásának és konfigurálásának megismeréséhez. Tekintse meg az ikrek [modul ismertetése](../iot-hub/iot-hub-devguide-module-twins.md) című témakört, amely részletesebben ismerteti a IoT hub-ben az ikrek koncepcióját.

## <a name="see-also"></a>Lásd még

- [Defender for IoT – áttekintés](overview.md)
- [Biztonsági ügynökök üzembe helyezése](how-to-deploy-agent.md)
- [Biztonsági ügynök hitelesítési módszerei](concept-security-agent-authentication-methods.md)