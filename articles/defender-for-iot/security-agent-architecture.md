---
title: Biztonsági ügynökök áttekintése
description: A biztonsági ügynök architektúrájának ismertetése az Azure Defender for IoT szolgáltatásban használt ügynökökkel kapcsolatban.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2021
ms.author: shhazam
ms.openlocfilehash: 929b8de8a6b70519bb44e41ba5cfede9ec12f110
ms.sourcegitcommit: 4784fbba18bab59b203734b6e3a4d62d1dadf031
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2021
ms.locfileid: "99808861"
---
# <a name="security-agent-reference-architecture"></a>Biztonsági ügynök hivatkozási architektúrája

A IoT készült Azure Defender olyan biztonsági ügynökök számára biztosít hivatkozási architektúrát, amelyek IoT Hubon keresztül naplózzák, feldolgozzák, összesítik és elküldik a biztonsági adatokat.

A biztonsági ügynökök úgy vannak kialakítva, hogy korlátozott IoT-környezetben működjenek, és az általuk használt erőforrásokhoz képest igen testreszabhatók.

A biztonsági ügynökök a következő szolgáltatásokat támogatják:

- Hitelesítés meglévő eszköz-identitással vagy dedikált modul-identitással. További információ: [biztonsági ügynök hitelesítési módszerei](concept-security-agent-authentication-methods.md).

- Nyers biztonsági események gyűjtése a mögöttes operációs rendszerből (Linux, Windows). További információ a rendelkezésre álló biztonsági adatgyűjtőről: [Defender for IoT Agent Configuration](how-to-agent-configuration.md).

- A nyers biztonsági események összesítése IoT Hubon keresztül küldött üzenetekben.

- Konfigurálja távolról a **azureiotsecurity** modul Twin használatával. További információ: [Defender konfigurálása a IoT-ügynökhöz](how-to-agent-configuration.md).

A IoT biztonsági ügynökök védelmezője nyílt forráskódú projektként lett kifejlesztve, és elérhető a GitHubról:

- [Defender IoT C-alapú ügynökhöz](https://github.com/Azure/Azure-IoT-Security-Agent-C)
- [Defender IoT C#-alapú ügynökhöz](https://github.com/Azure/Azure-IoT-Security-Agent-CS)

## <a name="agent-supported-platforms"></a>Ügynök által támogatott platformok

A IoT Defender különböző telepítő ügynököket kínál a 32 bites és a 64 bites Windowshoz, és ugyanezt a 32 bites és a 64 bites Linux rendszerhez. Győződjön meg arról, hogy rendelkezik a megfelelő ügynök-telepítővel az egyes eszközökhöz az alábbi táblázat szerint:

| Architektúra | Linux | Windows | Részletek |
|--|--|--|--|
| 32 bit | C# | C# |  |
| 64 bit | C# vagy C | C# | Azt javasoljuk, hogy a C ügynököt olyan eszközökön használja, amelyeken korlátozottabb vagy minimális erőforrású eszköz van. |


## <a name="next-steps"></a>Következő lépések

Ebben a cikkben a IoT biztonsági modul architektúrájának és a rendelkezésre álló telepítőknek a magas szintű áttekintése található.

A Defender IoT-telepítéssel való használatának folytatásához használja a következő cikkeket:

- A [biztonsági ügynök hitelesítési módszereinek](concept-security-agent-authentication-methods.md) megismerése
- [Biztonsági ügynök](how-to-deploy-agent.md) kiválasztása és központi telepítése
- Tekintse át a Defender IoT [rendszerkövetelményeit](quickstart-system-prerequisites.md)
- Ismerje meg, hogyan [engedélyezheti a Defender for IoT szolgáltatást a IoT hub](quickstart-onboard-iot-hub.md)
- További információ a szolgáltatásról a [Defender for IoT – gyakori kérdések](resources-frequently-asked-questions.md)
