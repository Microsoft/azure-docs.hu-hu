---
title: Defender a IoT Defender számára – IoT-Micro-Agent IoT Edge
description: Ismerje meg az Azure Defender IoT Defender-IoT-Micro-Agent architektúráját és képességeit a IoT Edge.
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
ms.openlocfilehash: 4b7377f74e24660945555c112fed4d77d2c5ddcc
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2021
ms.locfileid: "103495946"
---
# <a name="azure-defender-for-iot-edge-defender-iot-micro-agent"></a>Azure Defender a IoT Edge Defender számára – IoT-Micro-Agent

A [Azure IoT Edge](../iot-edge/index.yml) hatékony képességeket biztosít az üzleti munkafolyamatok kezeléséhez és az Edge-ben való végrehajtásához.
A IoT-környezetekben IoT Edge játszik, különösen vonzó a kártékony szereplők számára.

Defender for IoT Defender – a IoT-Micro-Agent átfogó biztonsági megoldást nyújt a IoT Edge-eszközökhöz.
A Defender for IoT modul összegyűjti, összesíti és elemzi a nyers biztonsági adatokat az operációs rendszertől és a tároló rendszertől a gyakorlatban használható biztonsági javaslatokkal és riasztásokkal.

A IoT-eszközök IoT biztonsági ügynökökhöz hasonlóan a Defender for IoT Edge modul nagyméretűen testreszabható a saját modulján keresztül.
További információért lásd: [az ügynök konfigurálása](how-to-agent-configuration.md) .

Defender for IoT Defender – IoT-Micro-Agent IoT Edge a következő funkciókat kínálja:

- Az alapul szolgáló operációs rendszer (Linux) és a IoT Edge tároló rendszerek nyers biztonsági eseményeit gyűjti.

  A rendelkezésre álló biztonsági adatgyűjtők megismeréséhez tekintse meg a [Defender for IoT-ügynök konfigurációját](how-to-agent-configuration.md) .

- IoT Edge üzembe helyezési jegyzékek elemzése.

- Az [IoT Edge hub](../iot-edge/iot-edge-runtime.md#iot-edge-hub)-on keresztül küldött üzenetekben összesíti a nyers biztonsági eseményeket.

- Távolítsa el a konfigurációt a Defender-IoT-Micro-Agent Twin használatával.

  További információért lásd: [Defender konfigurálása a IoT-ügynökhöz](how-to-agent-configuration.md) .

Defender for IoT Defender-IoT-Micro-Agent IoT Edge a IoT Edge alatt a privilegizált módban fut.
A Kiemelt üzemmód szükséges ahhoz, hogy a modul figyelje az operációs rendszert és az egyéb IoT Edge modulokat.

## <a name="module-supported-platforms"></a>Modul által támogatott platformok

A IoT Defender-IoT-Micro-Agent for IoT Edge jelenleg csak Linux rendszeren érhető el.

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta a Defender architektúráját és képességeit a IoT Defender-IoT-Micro-Agent számára a IoT Edge.

A Defender IoT-telepítéssel való használatának folytatásához használja a következő cikkeket:

- [Defender-IoT-Micro-Agent](how-to-deploy-edge.md) üzembe helyezése IoT Edge
- Ismerje meg, hogyan [konfigurálhatja a Defender-IoT-Micro-agentet](how-to-agent-configuration.md)
- Tekintse át a IoT [Defender Defender for IoT horizontot](resources-manage-proprietary-protocols.md)
- Ismerje meg, hogyan [engedélyezheti a Defender for IoT szolgáltatást a IoT hub](quickstart-onboard-iot-hub.md)
- További információ a szolgáltatásról a [Defender for IoT – gyakori kérdések](resources-frequently-asked-questions.md)