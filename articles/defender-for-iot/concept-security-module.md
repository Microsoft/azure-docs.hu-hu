---
title: Biztonsági modul és eszköz ikrek
description: Ismerje meg az ikrek biztonsági moduljának fogalmát, valamint azt, hogy azok hogyan használhatók a Defenderben a IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/24/2019
ms.author: mlottner
ms.openlocfilehash: feb84a1261e37600c68d8e372162033d4021f564
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/15/2021
ms.locfileid: "100522920"
---
# <a name="security-module"></a>Biztonsági modul

Ez a cikk azt ismerteti, hogyan használja a Defender for IoT az eszköz-ikreket és modulokat.

## <a name="device-twins"></a>Eszköz ikrek

Az Azure-ban beépített IoT-megoldások esetében az eszközök az ikrek kulcsszerepet játszanak az eszközkezelés és a folyamatok automatizálása terén is.

A IoT Defender teljes körű integrációt biztosít a meglévő IoT-eszközkezelés platformmal, lehetővé téve az eszköz biztonsági állapotának kezelését, valamint a meglévő eszközök vezérlési képességeinek kihasználását. Az integráció a IoT Hub Twin mechanizmus használatával érhető el.

Tudjon meg többet az Azure IoT Hub [eszközön található ikrek](../iot-hub/iot-hub-devguide-device-twins.md) koncepciójának megismeréséről.

## <a name="security-module-twins"></a>Biztonsági modul ikrek

A IoT Defender a szolgáltatás minden eszközéhez külön biztonsági modult tart fenn.
A biztonsági modul Twin tartalmazza az eszköz biztonságával kapcsolatos összes információt a megoldás minden egyes eszközén.
Az eszköz biztonsági tulajdonságait egy külön biztonsági modulban, a biztonságosabb kommunikációhoz, valamint a kevesebb erőforrást igénylő frissítések és karbantartás engedélyezéséhez kell megőrizni.

Lásd: a [biztonsági modul létrehozása](quickstart-create-security-twin.md) és a [biztonsági ügynökök konfigurálása](how-to-agent-configuration.md) , amelyekből megtudhatja, hogyan hozhatja létre, testreszabhatja és konfigurálhatja a Twin-et. Tekintse meg az ikrek [modul ismertetése](../iot-hub/iot-hub-devguide-module-twins.md) című témakört, amely részletesebben ismerteti a IoT hub-ben az ikrek koncepcióját.

## <a name="next-steps"></a>Következő lépések

- [Defender for IoT – áttekintés](overview.md)
- [Biztonsági ügynökök üzembe helyezése](how-to-deploy-agent.md)
- [Biztonsági ügynök hitelesítési módszerei](concept-security-agent-authentication-methods.md)