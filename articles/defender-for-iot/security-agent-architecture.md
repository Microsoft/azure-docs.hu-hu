---
title: 'Gyors útmutató: biztonsági ügynökök áttekintése'
description: Ebből a rövid útmutatóból megtudhatja, hogyan megismerheti a biztonsági ügynök architektúráját az Azure Defender for IoT szolgáltatásban használt ügynökökkel kapcsolatban.
ms.topic: quickstart
ms.date: 4/4/2021
ms.openlocfilehash: 0937cccb0335f4eee16ca3590babe9574320b89f
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2021
ms.locfileid: "106384979"
---
# <a name="quickstart-security-agent-reference-architecture"></a>Gyors útmutató: biztonsági ügynök hivatkozási architektúrája

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

## <a name="prerequisites"></a>Előfeltételek

- Nincsenek

## <a name="agent-supported-platforms"></a>Ügynök által támogatott platformok

A IoT Defender különböző telepítő ügynököket kínál a 32 bites és a 64 bites Windowshoz, és ugyanezt a 32 bites és a 64 bites Linux rendszerhez. Győződjön meg arról, hogy rendelkezik a megfelelő ügynök-telepítővel az egyes eszközökhöz az alábbi táblázat szerint:

| Architektúra | Linux | Windows | Részletek |
|--|--|--|--|
| 32 bit | C# | C# |  |
| 64 bit | C# vagy C | C# | Azt javasoljuk, hogy a C ügynököt olyan eszközökön használja, amelyeken korlátozottabb vagy minimális erőforrású eszköz van. |


## <a name="next-steps"></a>Következő lépések

Ebben a cikkben a IoT Defender-IoT-Micro-Agent architektúrával és a rendelkezésre álló telepítők magas szintű áttekintését olvashatja.
A Defender IoT-telepítéssel való megismerésének folytatásához 

> [!div class="nextstepaction"]
> [biztonsági ügynök hitelesítési módszerei](concept-security-agent-authentication-methods.md)
