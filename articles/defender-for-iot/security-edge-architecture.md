---
title: Defender a IoT biztonsági moduljának IoT Edge
description: Ismerje meg az Azure Defender IoT biztonsági moduljának architektúráját és képességeit a IoT Edge.
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
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: 3e653983b39e8eb4ac13ad840ca53e4ab6d1cfc7
ms.sourcegitcommit: 2501fe97400e16f4008449abd1dd6e000973a174
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2021
ms.locfileid: "99820619"
---
# <a name="azure-defender-for-iot-edge-security-module"></a>Azure Defender IoT Edge biztonsági modulhoz

A [Azure IoT Edge](../iot-edge/index.yml) hatékony képességeket biztosít az üzleti munkafolyamatok kezeléséhez és az Edge-ben való végrehajtásához.
A IoT-környezetekben IoT Edge játszik, különösen vonzó a kártékony szereplők számára.

A Defender for IoT biztonsági modul átfogó biztonsági megoldást nyújt a IoT Edge-eszközökhöz.
A Defender for IoT modul összegyűjti, összesíti és elemzi a nyers biztonsági adatokat az operációs rendszertől és a tároló rendszertől a gyakorlatban használható biztonsági javaslatokkal és riasztásokkal.

A IoT-eszközök IoT biztonsági ügynökökhöz hasonlóan a Defender for IoT Edge modul nagyméretűen testreszabható a saját modulján keresztül.
További információért lásd: [az ügynök konfigurálása](how-to-agent-configuration.md) .

A IoT Edge IoT biztonsági moduljának védelmezője a következő funkciókat kínálja:

- Az alapul szolgáló operációs rendszer (Linux) és a IoT Edge tároló rendszerek nyers biztonsági eseményeit gyűjti.

  A rendelkezésre álló biztonsági adatgyűjtők megismeréséhez tekintse meg a [Defender for IoT-ügynök konfigurációját](how-to-agent-configuration.md) .

- IoT Edge üzembe helyezési jegyzékek elemzése.

- Az [IoT Edge hub](../iot-edge/iot-edge-runtime.md#iot-edge-hub)-on keresztül küldött üzenetekben összesíti a nyers biztonsági eseményeket.

- Távolítsa el a konfigurációt a Twin biztonsági modul használatával.

  További információért lásd: [Defender konfigurálása a IoT-ügynökhöz](how-to-agent-configuration.md) .

A IoT biztonsági moduljának IoT Edge a rendszerjogosultságú módban fut IoT Edge alatt.
A Kiemelt üzemmód szükséges ahhoz, hogy a modul figyelje az operációs rendszert és az egyéb IoT Edge modulokat.

## <a name="module-supported-platforms"></a>Modul által támogatott platformok

A IoT biztonsági moduljának IoT Edge jelenleg csak Linux rendszeren érhető el.

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta a Defender architektúráját és képességeit a IoT Edge IoT biztonsági moduljában.

A Defender IoT-telepítéssel való használatának folytatásához használja a következő cikkeket:

- [IoT Edge biztonsági moduljának](how-to-deploy-edge.md) telepítése
- Útmutató [a biztonsági modul konfigurálásához](how-to-agent-configuration.md)
- Tekintse át a IoT [Defender Defender for IoT horizontot](resources-manage-proprietary-protocols.md)
- Ismerje meg, hogyan [engedélyezheti a Defender for IoT szolgáltatást a IoT hub](quickstart-onboard-iot-hub.md)
- További információ a szolgáltatásról a [Defender for IoT – gyakori kérdések](resources-frequently-asked-questions.md)